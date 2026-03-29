---
name: 1688-product-research
description: |
  1688 product sourcing search and data collection assistant. Supports keyword-based product search, collecting data such as price, sales volume, and store ratings, then saving results to a JSON file under the project path.
  Triggered when the user requests 1688 product selection, supplier sourcing, product search, product data collection, or batch sourcing.
---

# 1688 Product Selection Assistant

Search for suppliers on 1688, collect data, and save to the local project path.

## Constraints

- **Every search must be re-executed** — Regardless of whether there is historical data in the workspace, each user search request must re-execute the search script to get the latest data
- **Do not construct search URLs manually** — Must use cli.py to perform searches
- **Do not reuse historical data or check existing workspace files** — When a user initiates a search request, the search script must be re-executed regardless of whether the same keyword was searched before
- When the user says "find products / select products / search products / best-selling XX", default to `--sort sale` to sort by sales volume
- Data must be saved to the `data/1688/` directory under the project path

## Core Workflow

### Step 1: Prepare Environment

**1.1 Launch Browser**

Call `use_browser(action="navigate", url="https://www.1688.com")` to open the 1688 homepage.
Get the returned `wsUrl` address, e.g. ws://127.0.0.1:9222/xxx, then parse HOST and PORT from the address, e.g. HOST=127.0.0.1, PORT=9222

### Step 2: Execute Search Script

```bash
KEYWORD="keyword"
# Ensure output directory exists
mkdir -p "${PROJECT_ROOT}/data/1688"
OUTPUT_FILE="${PROJECT_ROOT}/data/1688/${KEYWORD}_$(date +%Y%m%d_%H%M%S).json"

# Use the actual HOST and PORT parsed from the WebSocket URL
python3 scripts/cli.py --host "$HOST" --port "$PORT" search -k "$KEYWORD" --sort sale -l 20 -o "$OUTPUT_FILE"
```

Parameters: `-k` keyword, `--sort` sorting (sale/price_asc/price_desc), `-l` count, `--price-start/--price-end` price range

### Step 3: Verify and Display Data

**3.1 Confirm file has been generated**
Confirm that `$OUTPUT_FILE` exists and contains valid data.

**3.2 Display result summary**
Read the JSON file and extract the top-ranked product information (title, price, sales volume, store name) to display to the user.

### Step 4: Deliver Results

Inform the user of the saved data path and ask if they need to convert the data to other formats (e.g. CSV) or perform further analysis.

Example:
```
✅ Data source: 1688 platform sorted by sales volume
✅ Collected: 20 {keyword} products
✅ Saved to: project/data/1688/{filename}.json
```

### Step 5: Close Browser

Close the browser after task completion:

```
use_browser(action="close")
```

## Error Handling

| Issue | Solution |
|-------|----------|
| Chrome not started | Launch browser via `use_browser` skill and get the CDP WebSocket address |
| Chrome connection failed | Check that HOST and PORT parsed from the WebSocket address are correct |
| Search returns empty | Try different keywords; confirm Chrome is running properly |
| Search interrupted/error | Close browser and restart; temporary profile requires no manual data cleanup |
