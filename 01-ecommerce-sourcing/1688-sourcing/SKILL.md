---
name: 1688-sourcing
description: |
  1688 image-based product matching - Find similar products on 1688 via image search. Use cases: Finding 1688 suppliers for Amazon product sourcing, reverse image search for suppliers, batch search for similar products.
  Input supports image URL lists or local image files. Results are automatically saved as CSV/JSON files under the project path.
---

# 1688 Image-Based Product Matching v6.0 (Local Path Edition)

**Core Principle: Each run must extract all 19 fields completely, and results are saved to the local project path**

## Strictly Prohibited (NEVER DO)

- ❌ Do not proceed without verifying field completeness (HARD-GATE)
- ❌ Do not skip browser launch and use cached data directly
- ❌ Do not extract data before page has fully loaded (must wait_for 5000ms)
- ❌ Do not use dynamic class selectors — must use nth-of-type fixed selectors
- ❌ **Do not proceed without 1688 product image links** — must extract src attribute from `<img>` tags
- ❌ **Do not silently ignore empty product links** — use search link fallback instead
- ❌ Do not start execution without confirming the save path
- ❌ Do not hide field-missing errors — must explicitly inform the user which fields are missing
- ❌ **Do not rely on any external spreadsheet tools (e.g. DingTalk tables)**

## Required Fields Checklist (19 fields)

| # | Field Name | Criticality |
|---|------------|-------------|
| 1-4 | Original image link, **1688 product image link**, Product link, Product name | ⭐⭐⭐ |
| 5-11 | Price, Last 90 days sales, Last 14 days sales, Factory years, Repeat purchase rate, Overall service score, Customer service response rate | ⭐⭐ |
| 12-19 | MOQ, Delivery fulfillment rate, 48h pickup rate, First listing date, Review count, Source factory, Supplier name, Shipping origin | ⭐⭐⭐ |

**Product Link Construction Rule**: Use the product name to generate a 1688 on-site search link, see [references/product-link-solution.md](./references/product-link-solution.md)

## Intent Decision Tree

```
User mentions "find similar" / "1688 sourcing" / "reverse image search":
├─ Provides image URL list directly → URL mode: batch process provided image links
└─ Provides local image files → Local file mode: upload/process local images

User mentions "save results":
└─ Default save to project root directory `1688-sourcing-results.csv` or `1688-sourcing-results.json`
```

## Core Workflow

```bash
# Step 1: Confirm input source (HARD-GATE)
URL mode: use URL list provided by user directly
Local image mode: read local image paths

# Step 2: Construct 1688 search URL
https://air.1688.com/app/1688-lp/landing-page/comparison-table.html?bizType=browser&currency=CNY&customerId=dingtalk&outImageAddress=<URL-encoded image address>

# Step 3: Open browser and extract data
use_browser(action=navigate, url=<search_url>)
use_browser(action=wait_for, timeMs=5000)
use_browser(action=evaluate, fn=<JavaScript extraction script>)

# Step 4: Field completeness validation (HARD-GATE)
Verify all 19 fields are present; stop and report if any field is missing

# Step 5: Save results locally
Write file: write(path="1688-sourcing-results.csv", content=<CSV formatted data>)
Or: write(path="1688-sourcing-results.json", content=<JSON formatted data>)

# Step 6: Return file path (HARD-GATE)
Confirm file has been generated and inform the user of the complete file path
```

## Context Passing Rules

| Operation | Extract from return | Used for |
|-----------|-------------------|----------|
| Input read | Product image link/local path | Construct search URL |
| Browser open | targetId | Subsequent evaluate/screenshot operations |
| JS extraction | products array (19 fields) | Save to local file |
| File write | File path | Report completion status to user |

## HARD-GATE Validation Mechanism

**Gate 1: Input Source Confirmation** - Input mode confirmed, necessary image links/paths obtained

**Gate 2: Field Completeness** - Verify all 19 required fields are present, product image link cannot be empty

**Gate 3: Pre-save Confirmation** - Target path confirmed, all record fields validated, data format meets requirements

**Gate 4: Delivery Confirmation** - Must inform user of the complete file path, e.g. `/workspace/1688-sourcing-results.csv`

## Data Format Specification

### ✅ CSV Format (Recommended)
```csv
Original Image Link,1688 Product Image Link,Product Link,Product Name,Price,Last 90 Days Sales,...
https://m.media-amazon.com/images/I/xxx.jpg,https://cbu01.alicdn.com/O1CN01xxx.jpg,https://s.1688.com/...,Waterproof Oxford Pet Mat,¥14.90,123,...
```

### ✅ JSON Format
```json
[
  {
    "original_image_link": "https://m.media-amazon.com/images/I/xxx.jpg",
    "product_image_link_1688": "https://cbu01.alicdn.com/O1CN01xxx.jpg",
    "product_link": "https://s.1688.com/selloffer/offer_search.htm?keywords=...",
    "product_name": "Waterproof Oxford Pet Mat",
    "price": "¥14.90",
    "moq": "1"
  }
]
```

## JavaScript Extraction Script

Full script at [references/dom-selectors.md](./references/dom-selectors.md), key points:
- Use `tr.ant-table-row` to select data rows
- Use `td:nth-of-type(1) img` to extract product image links
- Product link left empty temporarily, search link constructed later

## Error Handling

Error logs saved to `memory/1688-error.json`. Retry strategy and detailed error handling at [references/error-handling.md](./references/error-handling.md)

| Error Scenario | Resolution |
|----------------|------------|
| Browser cannot open | Stop immediately, check network/proxy, retry up to 2 times |
| No search results on 1688 page | Inform "No similar products found for this image on 1688", skip |
| Field missing | HARD-GATE failed, log to memory/1688-error.json, pause |
| Page structure changed | Stop, prompt that skill selectors need updating |
| File write failed | Check disk space/permissions, inform user of error |

## Detailed References (read as needed)

- [references/dom-selectors.md](./references/dom-selectors.md) — DOM selector details
- [references/field-mapping.md](./references/field-mapping.md) — 19-field mapping rules
- [references/error-handling.md](./references/error-handling.md) — Error codes and debugging
- [references/product-link-solution.md](./references/product-link-solution.md) — Product link solution
- [references/fixed-script.py](./references/fixed-script.py) — Fixed Python script (local save version)

## Version History

**v6.0** (Current) - Local path edition: Removed DingTalk dependency, data saved to project path CSV/JSON  
**v5.2** - Product link optimization: Added search link fallback, fixed Python script, enhanced field validation  
**v5.1** - Error handling enhancement: Added error log format + retry strategy  
**v5.0** - dingtalk-neulink standard: ≤150 lines, NEVER DO, decision tree, HARD-GATE
