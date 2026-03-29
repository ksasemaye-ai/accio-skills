---
name: amz-hot-keywords
description: "Amazon hot keyword scraper - scrapes ABA (Amazon Brand Analytics) weekly search term rankings from AMZ123. Extracts keyword, current week rank, last week rank, and trend (up/down/flat). Use this skill whenever the user asks to check Amazon hot search keywords, search term rankings, keyword trends, keyword popularity, or mentions AMZ123. Trigger phrases include: 'hot keywords', 'search term rank', 'keyword trend', 'Amazon keyword', 'ABA data', 'search volume rank', 'search popularity', 'trending keywords on Amazon', as well as Chinese equivalents like '热搜词', '搜索热度', '关键词排名', '关键词趋势'."
---

# Amazon Hot Keywords Scraper

Scrape ABA weekly search term rankings from AMZ123 for any keyword. Outputs structured CSV data with search term, current week rank, last week rank, and trend direction.

## How It Works

The skill uses a Selenium-based scraper (`scripts/amz_scraper.py`) to:
1. Open AMZ123's US top keywords page in headless Chrome
2. Search for the user-specified keyword
3. Extract up to 200 related search terms with their weekly rankings
4. Calculate trend (up/down/flat) by comparing current vs. last week rank
5. Save results as a timestamped CSV file

## Usage

Run the scraper script with the target keyword:

```bash
python3 <SKILL_DIR>/scripts/amz_scraper.py --keyword "dog bed"
```

### Parameters

| Parameter | Required | Description | Example |
|-----------|----------|-------------|---------|
| `--keyword` | Yes | Search keyword | `--keyword "yoga mat"` |
| `--max-results` | No | Max results to scrape (default: 200) | `--max-results 100` |
| `--output-dir` | No | Output directory for CSV (default: current dir) | `--output-dir ./data` |

### Output

The script produces a CSV file named `amz123_hotwords_<keyword>_<timestamp>.csv` with columns:

| Column | Description |
|--------|-------------|
| search_term | The keyword/search term |
| current_rank | This week's ranking position |
| last_rank | Last week's ranking position |
| trend | Calculated direction: up / down / flat / new |

Trend logic: rank number decreasing = rising popularity ("up"), rank number increasing = falling popularity ("down"), same = "flat", no previous rank = "new".

### Prerequisites

- Python 3.9+
- Chrome browser installed
- Python packages: `selenium`, `pandas` (install via `pip3 install selenium pandas`)

ChromeDriver is auto-managed by Selenium 4.6+, so no manual driver installation needed.

## Troubleshooting

If the scraper returns empty results:
1. AMZ123 may have updated their page structure. Check the debug HTML file saved alongside the CSV for clues.
2. The CSS selectors in `scripts/amz_scraper.py` (see the `SELECTORS` dict) may need updating to match new class names.
3. Try running with `--headless false` to visually inspect what the page looks like.

## Data Source

[AMZ123 US Top Keywords](https://www.amz123.com/usatopkeywords) - sourced from Amazon Brand Analytics (ABA) weekly reports covering ~250,000 search terms.
