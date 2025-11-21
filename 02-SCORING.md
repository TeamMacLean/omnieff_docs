# Protein Ranking Score Calculation

This document describes how protein ranking scores are calculated in our effector-like prediction pipeline.

## Overview

The ranking system assigns a custom score to each protein based on multiple features from various bioinformatics tools. The score is designed to identify proteins most likely to be effectors based on their secretion potential, effector prediction scores, and domain characteristics.

## Score Components

### 1. Secretion Status (Weight: +2.0)

A protein is considered "secreted" if it meets ALL of the following criteria:

  - Has at least one positive signal peptide prediction from any of:
     - SignalP3 HMM (score > 0.5)
     - SignalP3 Neural Network (score > 0.5) 
     - Phobius (has signal peptide)
     - SignalP4 (score > 0.5)
     - SignalP5 (score > 0.5)
     - SignalP6 (score > 0.5)
  - AND has no transmembrane domains (both single and multiple TM counts = 0)

### 2. Signal Peptide Predictions

  - **SignalP3 HMM score**: Raw score × 0.0001
  - **SignalP3 Neural Network score**: Raw score × 0.0001
  - **SignalP4 score**: Raw score × 0.003
  - **SignalP5 score**: Raw score × 0.003
  - **SignalP6 score**: Raw score × 0.003
  - **Phobius signal peptide**: Boolean × 0.0001

### 3. Transmembrane Domain Penalties

  - **Multiple transmembrane domains**: -1.0 per domain
  - **Single transmembrane domain**: -0.7 per domain

### 4. Effector Predictions

  - **EffectorP1 probability**: 2×(probability - 0.5) × 0.5
  - **EffectorP2 probability**: 2×(probability - 0.5) × 2.5
  - **EffectorP3 apoplastic (fungal)**: Boolean × 0.5
  - **EffectorP3 cytoplasmic (fungal)**: Boolean × 0.5
  - **EffectorP3 noneffector (fungal)**: Boolean × -2.5

### 5. Deep Learning Predictions

  - **DeepRedEff (fungi)**: 2×(probability - 0.5) × 0.1
  - **DeepRedEff (oomycete)**: 2×(probability - 0.5) × 0.0 (no contribution)

### 6. Domain Analysis

  - **Selected PFAM domain match**: Boolean × 2.0
     - **IMPORTANT**: This is one of the highest-weighted features in the scoring system
     - A match to any of the curated virulence-related PFAM domains adds +2.0 to the score
     - This feature is critical for identifying proteins with known effector domains

## Boolean Multiplication in Scoring

Boolean features in this scoring system work as follows:

  - **True values** contribute their full weight to the score
  - **False values** contribute 0 to the score
  - **Missing values** (RESULTS_MISSING, NA) are ignored

**Examples:**

  - `has_selected_pfam_match = True` → adds +2.0 to score
  - `has_selected_pfam_match = False` → adds +0.0 to score  
  - `effectorp3_noneffector_fungal = True` → adds -2.5 to score (penalty)
  - `effectorp3_noneffector_fungal = False` → adds +0.0 to score

This effectively means: `score += weight if feature_is_true else 0`

## Score Calculation Formula

```
Total Score = 
  + (2.0 if secreted) 
  + Σ(signal_peptide_scores × weights)
  + Σ(transmembrane_penalties)
  + Σ(effector_probabilities × 2×(prob-0.5) × weights)
  + Σ(boolean_features × weights)
```

## Interpretation

  - **Higher positive scores** indicate proteins more likely to be effectors
  - **Lower or negative scores** indicate proteins less likely to be effectors
  - **Missing data** (RESULTS_MISSING, NA) is ignored and doesn't contribute to the score
  - The ranking is based on these scores in descending order

## Implementation Notes

  - All probability-based scores use the transformation `2×(x-0.5)` to center around 0 and scale appropriately
  - Boolean features contribute their full weight when True, 0 when False
  - The system is designed to be robust to missing tool outputs
  - Scores are computed using the `score_proteins.py` script which reads the parsed tool outputs CSV 