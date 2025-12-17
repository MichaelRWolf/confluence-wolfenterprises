# Hierarchy Reconstruction Guide

⚠️ **WARNING: This approach was NOT selected for the migration.**

We chose to **preserve** hierarchy as reference metadata instead of reconstructing it. See [hierarchy-reference.md](hierarchy-reference.md) for the selected approach.

**Why reconstruction was rejected:**

- High maintenance cost (requires ongoing discipline and tooling)
- Fragile (breaks when files are moved or renamed)
- Low value (modern search and AI make hierarchy less important)
- Creates technical debt (dual structures to maintain)

This document is preserved for reference only, in case reconstruction becomes necessary in the future.

Rebuilding Confluence Structure From Flat HTML Export  
**Supports Parallel “Flat” and “Hierarchical” Views in Google Drive**

Confluence Cloud HTML export is always **flat**.  
Hierarchy exists only inside **`index.html`**, not in folders.

This guide explains how to:

1. Reconstruct the original Confluence hierarchy, **mechanically**.  
2. Upload that reconstructed hierarchy to Drive.  
3. Keep **both** flat and hierarchical versions in parallel:  
   - `Living (Flat)`  
   - `Living (Hierarchical)`  
4. Enable **cross-links** between versions.

---

## 1. What This Produces

After reconstruction and upload, Drive will look like this:

```text
Confluence-Living/
  Living (Flat)/              ← All pages, shortcuts or originals
  Living (Hierarchical)/      ← Reconstructed Confluence tree
```

Each contains **the same documents**, just organized differently:

- **Flat:** search-first, AI-friendly, everything in one place  
- **Hierarchical:** human-browseable, legacy Confluence feel  

There is **no duplication of content** unless you explicitly choose it.  
Drive shortcuts can eliminate duplication completely.

---

## 2. Where Reconstruction Happens

Hierarchy reconstruction should be done **on the HTML files before upload**, because:

- The hierarchy is encoded in `index.html`
- HTML filenames map cleanly to pages
- Google Docs move/rename operations are slower and API-dependent

You upload **two different folder structures**:

### A. “Flat” import

Direct upload of the unmodified Confluence export:

```text
Confluence-Living/Living (Flat)/
    <all pages as Google Docs>
    attachments/
    images/
```

### B. “Hierarchical” import

Upload a **reconstructed folder tree**:

```text
Confluence-Living/Living (Hierarchical)/
    Housing/
        Living-in-real-apartments.html
        Housing-Opportunities.html
    RV/
        RV-Break-in-2024.html
        Leaving-Myrtle-Beach-2022.html
    Taxes/
        ...
```

Google Drive will convert the `.html` pages to Docs automatically inside the folder tree.

---

## 3. How the Reconstruction Works (Overview)

Inputs:

- `index.html` sidebar tree (required)
- exported page HTML files (to extract readable titles)

Outputs:

- `hierarchy-tree.json`
- `rename-map.csv`
- `reconstructed/` folder tree *(for upload as “Living (Hierarchical)”)*

Steps:

1. Parse `index.html` for tree structure  
2. Map titles → filenames  
3. Create folder structure  
4. Move+rename HTML files  
5. Upload to Drive

---

## 4. Creating Both Views in Drive

### Step 1 — Upload flat export (no changes)

On the VM:

```text
Confluence-Living/Living (Flat)/
    <upload unzipped HTML folder here>
```

Drive converts `.html` → Google Docs.

Everything stays **flat**.

---

### Step 2 — Reconstruct hierarchy (on VM)

Produce:

```text
reconstructed/
  RV/
     Leaving-Myrtle-Beach.html
     RV-Break-in.html
  Housing/
     Living-In-Real-Apartments.html
     Housing-Opportunities.html
```

### Step 3 — Upload reconstructed version

Upload:

```text
Confluence-Living/Living (Hierarchical)/
    RV/
    Housing/
    ...
```

Drive will again convert `.html` → Docs, but now placed in the correct folders.

---

## 5. Linking Between Flat & Hierarchical Versions

Because **Google Docs are objects in Drive**, you can do **one of two linking strategies**:

---

## Strategy A — Use Drive Shortcuts (Recommended)

No duplication of files.  
Each view is just a different arrangement of pointers.

### Implementing A

1. Upload the **Hierarchical** version (converted to Google Docs)
2. Upload the **Flat** version using **shortcuts**:
   - Search for all Google Docs inside `Living (Hierarchical)`
   - Select all → Right-click → **Add shortcut to Drive**
   - Choose: `Living (Flat)`

Result:

- **Hierarchical** holds the *real files*
- **Flat** holds *shortcuts* to the same files

### Benefits

- Single source of truth  
- Both layouts always stay in sync  
- No duplicate storage  
- Links and permissions are unified  

---

## Strategy B — Duplicate the Docs (Not recommended)

Drive allows duplication, but:

- Uses more storage  
- Creates two independent docs  
- They drift  
- Links do not stay synchronized  

Only use this if you explicitly want “archival snapshots."

---

## 6. Links Between Views

If using **shortcuts** (Strategy A):

### Linking from “Flat” → “Hierarchical”

Shortcuts already point directly to real files inside `Living (Hierarchical)`.  
Clicking a shortcut opens the canonical location automatically.

### Linking from “Hierarchical” → “Flat”

You can insert links in several ways:

- Right-click the file → “Copy link” → paste link into a Doc  
- Insert → Link → paste Drive link  
- Doc-to-Doc links remain stable even if you rename folders

Because both views point to the **same file objects**, links never break.

---

## 7. Which Version Should Hold the “Real” Files?

### Choose one

1. **Hierarchy as real content**  
   Flat is shortcuts  
   (recommended if migrating from Confluence mindset)

2. **Flat as real content**  
   Hierarchy is shortcuts  
   (recommended if AI-first and structure is secondary)

### Default Recommendation

**Hierarchy = real  
Flat = shortcuts**

Because:

- Confluence was hierarchy-first  
- Drive search works equally well across both  
- AI tools don’t care which folder holds the actual files

---

## 8. Summary of Parallel Views

You will end up with:

```text
Confluence-Living/
  Living (Hierarchical)/   ← REAL FILES
      RV/
      Housing/
      ...
  Living (Flat)/           ← SHORTCUT VIEW
      <shortcuts to all docs>
```

This arrangement is:

- fully consistent  
- low maintenance  
- reversible  
- intuitive for humans  
- AI-friendly  

---

## 9. Next Steps

If you provide:

- `index.html`
- list of exported `.html` files

I can generate:

- the hierarchy tree  
- rename map  
- folder layout  
- fully reconstructed structure folder  
- instructions or script to create shortcuts for the flat view  
