# Hierarchy Preservation (Not Reconstruction)

An Engineering Proposal for Confluence → Google Docs Migration

## Executive Summary

**Goal:** Preserve the *knowledge* of hierarchy without rebuilding or maintaining it.

We explicitly choose **not** to reconstruct Confluence’s page hierarchy in Google Drive folders.  
Instead, we preserve hierarchy as **reference metadata** that can be consulted by humans or tools later, if and only if the value justifies the cost.

This document explains:

- why hierarchy mattered historically
- why flat + search + AI is the right default now
- why reconstruction is expensive and fragile
- what “hierarchy preservation” means in practice
- how to leave enough structure to recreate hierarchy later if needed

This is a *middle way*:  
respecting past structure without letting it control the future.

---

## Background: Why Hierarchy Was Good (Then)

When Confluence was adopted (~20–25 years ago):

- Search was weak and literal
- Context lived in navigation, not language
- Wikis were explored by browsing, not querying
- Humans relied on:
  - left-hand trees
  - parent/child containment
  - visual location as memory

In that world:

- hierarchy *was* the index
- page location conveyed meaning
- “where something lived” mattered as much as “what it said”

Confluence optimized for this reality.

---

## What Changed

Today:

- Full-text search is excellent
- AI retrieval is semantic, not positional
- Context can be reconstructed from language
- Users ask questions instead of browsing trees

Modern tools (Notion, Roam, Obsidian, AI copilots) all converge on this truth:

> **Smaller units + flat storage + strong retrieval outperform deep trees.**

Hierarchy is no longer the primary access path.
It is, at best, secondary metadata.

---

## Why Flat Was Easier to Migrate

### Confluence Cloud Reality

Confluence Cloud exports are **always flat**:

- All pages exported to one directory
- Hierarchy encoded only inside `index.html`
- No filesystem-level parent/child relationships

This means:

- Flat is not a choice — it is the only native output
- Any hierarchy requires post-processing

### Google Drive Reality

Google Drive:

- Converts HTML → Google Docs easily
- Preserves folders *if they exist*
- Does **not** infer hierarchy from HTML

Therefore:

- Flat imports are fast, reliable, and mechanical
- Hierarchical imports require parsing, mapping, and restructuring

Flat aligns with both systems’ strengths.

---

## Why Reconstruction Is High Cost / Low Return

Reconstructing hierarchy requires:

- Parsing `index.html`
- Mapping page IDs to filenames
- Renaming files
- Creating folders
- Moving content
- Deciding “canonical” vs “shortcut” views
- Explaining to humans which structure is real
- Maintaining consistency over time

Maintaining *both* flat and hierarchical views requires:

- discipline
- tooling
- repeated human attention

Humans are bad at double bookkeeping.
Computers are good at it — but only if we keep paying them.

The value does not justify the complexity *now*.

---

## Proposed Middle Way: Preserve, Don’t Rebuild

### What “Preserve Hierarchy” Means

- We **do not** create folder trees in Drive
- We **do not** move Docs after import
- We **do not** maintain dual structures

Instead, we preserve hierarchy as **documentation**:

- one Markdown file per Confluence space
- living alongside the flat Google Docs
- readable by humans
- parsable by tools
- never required for daily work

---

## The Preservation Artifact: `<SPACE>-hierarchy-reference.md`

Example files:

- `LIV-hierarchy-reference.md`
- `ALIGN-hierarchy-reference.md` ([ALIGN](https://wolfenterprises.atlassian.net/wiki/spaces/ALIGN) space - BLOOM)
- `BLOOM-hierarchy-reference.md`

**Filename rules:**

- Filenames may preserve spaces and punctuation (e.g., `Michael Personal Space-hierarchy-reference.md`)
- The `<SPACE>` portion should match the Confluence export folder name for that space

These files contain:

1. The original Confluence hierarchy as an outline
2. A mapping between:
   - original page titles
   - exported HTML filenames
3. Minimal guidance for future manual reconstruction

They act as:

- a memory
- a map
- an acknowledgment of past structure

---

## Example ([ALIGN](https://wolfenterprises.atlassian.net/wiki/spaces/ALIGN) Space - BLOOM)

```md
# ALIGN (BLOOM) – Original Confluence Hierarchy (Preserved)

*See [HOWTO_CREATE_hierarchy-reference.md](HOWTO_CREATE_hierarchy-reference.md) for format specification.*

## Hierarchy Outline

- [BLOOM](https://wolfenterprises.atlassian.net/wiki/spaces/ALIGN/pages/326533328)
  - [BLOOM: BIG Picture: Branding, Offerings, etc](https://wolfenterprises.atlassian.net/wiki/spaces/ALIGN/pages/614498331)
    - [Wendy's Visions for Ministry Impact & Money](https://wolfenterprises.atlassian.net/wiki/spaces/ALIGN/pages/2531852289)
  - [Tools](https://wolfenterprises.atlassian.net/wiki/spaces/ALIGN/pages/663683116)
    - [MemberVault, Cartra, HubSpot, Podia.... Patreon](https://wolfenterprises.atlassian.net/wiki/spaces/ALIGN/pages/951091215)
```

### Mapping Table

| Confluence Title                                | Confluence Link                                               | HTML Export                   |
|-------------------------------------------------|---------------------------------------------------------------|-------------------------------|
| Wendy's Visions for Ministry Impact & Money     | [Page ID][align-2531852289], [Page URL][align-2531852289-url] | [HTML][align-2531852289-html] |
| MemberVault, Cartra, HubSpot, Podia.... Patreon | [Page ID][align-951091215], [Page URL][align-951091215-url]   | [HTML][align-951091215-html]  |

[align-2531852289]: https://wolfenterprises.atlassian.net/wiki/spaces/ALIGN/pages/2531852289
[align-2531852289-url]: https://wolfenterprises.atlassian.net/wiki/spaces/ALIGN/pages/2531852289/Wendy%27s+Visions+for+Ministry+Impact+%26+Money
[align-2531852289-html]: ALIGN/2531852289.html
[align-951091215]: https://wolfenterprises.atlassian.net/wiki/spaces/ALIGN/pages/951091215
[align-951091215-url]: https://wolfenterprises.atlassian.net/wiki/spaces/ALIGN/pages/951091215/MemberVault%2C+Cartra%2C+HubSpot%2C+Podia....+Patreon
[align-951091215-html]: ALIGN/MemberVault%2C-Cartra%2C-HubSpot%2C-Podia....-Patreon_951091215.html

**Why titles might differ:**

- HTML export filenames are derived from titles but are not identical
- URL encoding: spaces and special characters become URL-encoded in filenames and in Confluence title slugs
- Confluence titles can change over time; older exports may not match current titles
- Some exports are numeric-only (e.g., `2531852289.html`) while others embed a title slug (e.g., `Soul-Leadership_196610.html`)

This is enough information to:

- find anything
- explain what changed
- recreate hierarchy later if desired

---

## Implementation Notes

**Page ID extraction:**

- For number-only filenames (e.g., `2531852289.html`): the page ID is the number before `.html`
- For encoded filenames (e.g., `MemberVault%2C-Cartra_951091215.html`): the page ID is the number after the final underscore before `.html`

**Page URL encoding:**

- Page titles in URLs must be URL-encoded (spaces become `+`, special characters percent-encoded)
- Use the actual Confluence page title, not the HTML filename
- Prefer the `/wiki/spaces/<SPACE_KEY>/pages/<PAGE_ID>` link form; avoid `pages/viewpage.action?pageId=<PAGE_ID>` (it may not resolve correctly in Confluence Cloud)

**Hierarchy outline:**

- Include all pages that appear in the Confluence space hierarchy
- Use indentation (2 spaces per level) to show parent/child relationships
- Link each page title to its Confluence page using the page ID URL format (works without the title slug; Confluence will often redirect to add it):
  - `https://<site>.atlassian.net/wiki/spaces/<SPACE_KEY>/pages/<PAGE_ID>`
  - `https://<site>.atlassian.net/wiki/spaces/<SPACE_KEY>/pages/<PAGE_ID>/<Title+Slug>` (optional)

**Mapping table:**

- Include all pages from the space, not just those shown in the hierarchy outline
- Order rows to match the hierarchy outline first, then append orphan pages sorted by Confluence Title
- Include `index.html` as a special first row (so it stays clickable), even though it is not a Confluence page:
  - Confluence Title: `Space index`
  - Confluence Link: `https://<site>.atlassian.net/wiki/spaces/<SPACE_KEY>`
  - HTML Export: `<SPACE_FOLDER>/index.html`

**Space name format:**

- Use format: `SPACE_KEY (Display Name)` where Display Name is the space's display name from Confluence
- If space key and display name are identical, use: `SPACE_KEY`
- For simplicity, you may use the Confluence export folder name as `SPACE_KEY` (personal spaces may not resolve perfectly, and that's acceptable if link accuracy there is not important)

---

## Example Applicability ([ALIGN](https://wolfenterprises.atlassian.net/wiki/spaces/ALIGN) (BLOOM) / Other Spaces)

The same pattern applies cleanly to other spaces:

- [ALIGN](https://wolfenterprises.atlassian.net/wiki/spaces/ALIGN) (BLOOM): product, people, experiments, notes
- Other spaces: principles, working agreements, reference material

Each space gets:

- one preserved hierarchy document
- zero enforced structure in Drive

---

## Why This Is the Right Compromise

- Honors historical structure without rebuilding it
- Avoids fragile scripts and ongoing maintenance
- Keeps migration mechanical and low-risk
- Aligns with AI-first and search-first workflows
- Leaves future options open
- Reduces emotional loss without creating technical debt

Hierarchy is preserved as *knowledge*, not *infrastructure*.

---

## For Tools and Agents (Cursor, etc.)

This document is intentionally structured so that an agent can:

- parse hierarchy outlines
- read mapping tables
- generate `<SPACE>-hierarchy-reference.md` files automatically
- optionally reconstruct folders in the future

No assumptions are baked in.

---

## Final Position

We accept that:

- hierarchy was valuable
- flat is now superior
- reconstruction is optional
- preservation is sufficient

This migration optimizes for **today**, without burning bridges to **yesterday**.
