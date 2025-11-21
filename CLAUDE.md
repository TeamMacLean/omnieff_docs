# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This is a **documentation repository** containing documents for a pipeline called omnieff. The purpose is to take the documentation and turn it into
a website for users.


### When Editing These Documents
- **Match the existing style**: Direct, concise, example-driven
- **Update don't proliferate**: Enhance existing docs rather than creating new ones
- **Maintain the philosophy**: User controls decisions, LLM implements
- **Use concrete examples**: "Good" vs "Bad" request/response patterns
- **Keep checklists and quick references**: Users want actionable guidance

## Response Style for This Repository

When helping users work with these materials:
- **Be direct and concise** (matches the guide's philosophy)
- **Quote relevant sections** from the guides
- **Provide concrete examples** from the documents
- **Don't over-explain** - these users understand LLM workflows
- **Respect the boundary**: Help them use the materials, don't make their development decisions

## Quarto Website

This repository is published as a Quarto website at: **https://teammacLean.github.io/omnieff_docs/**

### File Structure

**Quarto files (.qmd)** - Rendered to website:
- `index.qmd` - Landing page (converted from README.md)
- `01-PFAM_DOMAINS.qmd` - information on PFAM domains used in the pipeline
- `02-SCORING.qmd` - how scoring is done
- `about.qmd` - About page

**Original Markdown files (.md)** - Preserved in repo as examples, excluded from rendering:
- `README.md`, `01-*.md`, `02-*.md`, etc.
- `CLAUDE.md` - This file (guidance for Claude Code)

**Configuration:**
- `_quarto.yml` - Website config with nested navigation and .md exclusions
- `styles.css` - Custom styling
- `.github/workflows/publish.yml` - Auto-deployment to GitHub Pages

### Build Commands

**Local preview:**
```bash
quarto preview
```

**Build site:**
```bash
quarto render
```
Output: `_site/` directory (gitignored)

**Check Quarto version:**
```bash
quarto --version
```

### GitHub Pages Deployment

**Automatic deployment:**
- Triggered on push to `main` branch
- GitHub Actions workflow builds and deploys
- Check status: https://github.com/TeamMacLean/omnieff_docs/actions

**Setup requirements:**
1. Repository Settings → Pages → Source: **GitHub Actions** (not branch)
2. Workflow file: `.github/workflows/publish.yml`
3. Permissions already configured in workflow (pages: write, id-token: write)

### Common Issues and Solutions

**Issue: "Repository not found" when pushing**
- Solution: Create repo on GitHub first at https://github.com/TeamMacLean/omnieff_docs

**Issue: Deployment fails with 404 "Ensure GitHub Pages has been enabled"**
- Solution: Go to repo Settings → Pages, set Source to "GitHub Actions"
- Then re-run the failed workflow from Actions tab

**Issue: Quarto renders both .md and .qmd files (duplicate content)**
- Solution: Already handled in `_quarto.yml` with render exclusions:
  ```yaml
  project:
    render:
      - "*.qmd"
      - "!*.md"
  ```

**Issue: Need to add new page to website**
- Create `newpage.qmd` with YAML frontmatter
- Add to `_quarto.yml` navbar
- Test with `quarto preview` before committing

### Editing Workflow

**For content updates:**
1. Edit the `.qmd` files (NOT the `.md` files)
2. Test locally: `quarto preview`
3. Commit and push - deployment is automatic
4. Original `.md` files remain as examples, don't delete them

**For navigation/styling changes:**
- Edit `_quarto.yml` for menu structure
- Edit `styles.css` for custom styling
- Test locally before pushing