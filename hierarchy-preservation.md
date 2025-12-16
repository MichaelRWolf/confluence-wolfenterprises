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

## The Preservation Artifact: `<SPACE>-hierarchy.md`

Example files:

- `LIV-hierarchy.md`
- `BLOOM-hierarchy.md`
- `ALIGN-hierarchy.md`

These files contain:

1. The original Confluence hierarchy as an outline
2. A mapping between:
   - original page titles
   - exported HTML filenames
   - resulting Google Doc titles
3. Minimal guidance for future manual reconstruction

They act as:

- a memory
- a map
- an acknowledgment of past structure

---

## Example (LIV Space)

```md
# LIV – Original Confluence Hierarchy (Preserved)

## Hierarchy Outline

- Living
  - Housing
    - Living in real apartments (spring 2022)
    - Housing Opportunities
  - RV
    - RV – 2021-08-01
    - RV Break-in – 2024-06-29
  - Health
    - Health Insurance
    - Health Solutions – BSA
```

### Mapping Table

| Confluence Title                        | HTML Export     | Google Doc Title                        |
|-----------------------------------------|-----------------|-----------------------------------------|
| RV Break-in – 2024-06-29                | 2453504001.html | RV Break-in – 2024-06-29                |
| Living in real apartments (spring 2022) | 2166391009.html | Living in real apartments (spring 2022) |

This is enough information to:

- find anything
- explain what changed
- recreate hierarchy later if desired

---

## Example Applicability (BLOOM / ALIGN)

The same pattern applies cleanly to other spaces:

- BLOOM: product, people, experiments, notes
- ALIGN: principles, working agreements, reference material

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
- generate `<SPACE>-hierarchy.md` files automatically
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
