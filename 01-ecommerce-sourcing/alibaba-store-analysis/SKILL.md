---
name: alibaba-store-analysis
description: Alibaba.com (Alibaba International Station) weekly business report data analysis tool. Automatically retrieves store weekly report data via browser sessions and performs structured analysis.
---

# Alibaba International Station Weekly Business Report Analysis SKILL

## Overview

Access Alibaba International Station via browser, automatically retrieve and analyze store weekly business report data, and output structured business diagnostics.

## Simplified Flow

```
Step 1: Check Login Status
  │  Visit i.alibaba.com, detect if redirected to login page
  │  ├─ Not logged in → Guide user to log in, wait for completion
  │  └─ Logged in ↓
  │
Step 2: Retrieve Business Data
  │  Call diagnoseData.json API
  │  ↓
Step 3: Data Validation (Silent)
  │  Validate data / encryptedReportId / values.receipt
  │  ├─ Validation failed → Output fallback message, terminate
  │  └─ Validation passed ↓
  │
Step 4: Structured Display
  │  Output all at once: Data Overview → Diagnostic Summary → Merchant Tasks → Report Link
  │  ↓
Step 5: Retrieve Full Report Data
  │  Call queryWeekReportAllData.json API
  │  Display "Full data analysis complete", wait for user questions before providing on-demand analysis
```

---

## Environment & Prerequisites

| Item | Description |
| --- | --- |
| Browser Tool | Use `browser` tool; use user-specified profile if provided, otherwise default to `profile=openclaw` |
| Target Domain | `https://i.alibaba.com/` |
| Login Status | User must be logged in; guide to complete login if not |

---

## Core Workflow

### Step 1: Check Login Status

Visit `https://i.alibaba.com/`:

- Redirected to `https://login.alibaba.com/newlogin/icbuLogin.htm?...` → Not logged in, prompt user to complete login and notify when done
- No redirect → Logged in, proceed with workflow

### Step 2: Retrieve Business Data

Execute in browser console:

```javascript
(async () => {
  const url = 'https://crm.alibaba.com/crmlogin/aisales/dingwukong/diagnoseData.json';
  const r = await fetch(url, {
    method: 'POST',
    credentials: 'include'
  });
  if (!r.ok) throw new Error(`HTTP error: ${r.status}`);
  return await r.json();
})()
```

### Step 3: Data Validation (Silent — do not display to user)

Validate in the following order; terminate with fallback message if any check fails:

1. **API response is empty `{}` or `null`** → **CRITICAL: This is a definitive signal that no data is available for this account in the weekly report system. Terminate IMMEDIATELY. Do NOT attempt to navigate to `crm.alibaba.com` or troubleshoot via UI/Search, as these systems may have account mismatches or session delays that cannot be fixed via automation.**
2. `encryptedReportId` field does not exist or is empty → Terminate
3. `values.receipt` field does not exist or is empty → Terminate (this field is used as a request parameter in Step 5; if it has a value, record it for later use)

Fallback message:

> Sorry, no business data is available for the current account in the Alibaba Weekly Report system. Analysis cannot be performed at this time.
> 
> **Expert Tip:** If you can see data in your backend, please provide a screenshot or list your key metrics (Exposure, Clicks, Inquiries, etc.), and I will provide a manual diagnostic and optimization plan for you.

Error tolerance:

- `weekDiagnose` missing → Note "This period's data does not include business diagnostics", continue outputting other data, do not terminate
- Both `diagnoseTitle` and `diagnoseSummary` are empty → Skip this diagnostic item, continue processing others

### Step 4: Structured Data Display

From the `values.weekDiagnose` array, filter for the data object where `scope = "近7天"` (Last 7 Days), combine with `values.diagnoseSummary`, and output in the following four sections.

#### Section 1: Store Data Overview

Extract core metrics from the `indicatorList` array and display as a Markdown table.

Field mapping:

| Field | Meaning |
| --- | --- |
| `name` | Metric name |
| `value` | This week's data |
| `cycleCRC` | Week-over-week change |
| `valueVsAvg` | vs. Industry average |

Example output:

> Here is your store's data for the last 7 days:

| Metric | This Week | WoW Change | vs. Industry Avg |
| --- | --- | --- | --- |
| Clicks | 6.0 | +0.0% | -96.4% |
| Inquiries | 10.0 | +0.0% | -65.5% |
| Premium Product Ratio | 0.0% | - | - |

> When `cycleCRC` or `valueVsAvg` is missing, display as `-`

#### Section 2: Store Diagnostic Summary

Directly display the content of `values.diagnoseSummary` field.

Example output:

> Your store's average daily search exposure over the last 7 days is only 100, far below the industry average of 1048.6 — you are in catch-up mode. Notably, this metric declined 41.35% compared to last week and needs urgent attention. Recommended immediate actions: publish 5 potential winning products and build 10 premium/trending products — this can boost overall store exposure by 40%-50% within 30 days!

#### Section 3: Merchant Tasks

Iterate through the `maTaskList` array, extracting `title` and `desc` (HTML tags must be cleaned).

Example output:

Recommended key tasks:

- Task 1: Publish 5 potential winning products
  - Estimated impact: Completing this task could boost overall store exposure by 40%-50%
- Task 2: Build 10 premium/trending products
  - Estimated impact: Completing this task could boost overall store exposure by 20%-30%

#### Section 4: Weekly Report Link

Construct the link using `encryptedReportId`:

```
https://crm.alibaba.com/crmagent/crm-grow/luyou/report-render.html?id={encryptedReportId}&dateScope=week&isDownload=false
```

Example output:

> View full weekly business report: https://crm.alibaba.com/crmagent/crm-grow/luyou/report-render.html?id={encryptedReportId}&dateScope=week&isDownload=false

#### Display Requirements

- Output all four sections to the user at once after data collection is complete
- Do not save content to a markdown file
- After returning content, proceed to Step 5

### Step 5: Retrieve Full Merchant Report Data

After Step 3 data validation passes, use `values.receipt` from Step 2 response to call the report detail API for the complete merchant report data.

Execute in browser console:

```javascript
(async () => {
  const receipt = '<values.receipt obtained from Step 2 response>';
  const url = 'https://crm.alibaba.com/crmlogin/aisales/dingwukong/queryWeekReportAllData.json';
  const r = await fetch(url, {
    method: 'POST',
    credentials: 'include',
    headers: { 'Content-Type': 'application/x-www-form-urlencoded' },
    body: `receipt=${receipt}`
  });
  if (!r.ok) throw new Error(`HTTP error: ${r.status}`);
  return await r.json();
})()
```

#### Data Validation (Silent — do not display to user)

- API response is empty / `null` / `data` is empty object → Display "Sorry, unable to retrieve full report data at this time. Please try again later." and terminate
- Partial module data missing → Skip that module, continue processing others, do not terminate

#### Data Understanding & Storage

The API response is organized by `reportPageCode` into modules. See `reference/weekly_report_data_explanation.md` for module codes and field descriptions.

Module overview:

| Module Code | Description |
| --- | --- |
| STORE_DATA_OVERVIEW | Store data overview (search exposure, clicks, inquiries, conversion rate, etc.) |
| STORE_INFRASTRUCTURE_SITUATION | Store infrastructure status (product quantity, reply rate, etc.) |
| STORE_DIAGNOSIS | Store diagnostics (star rating, capability scores and optimization suggestions) |
| OPPORTUNITY_STAR_LEVEL_DATA_OVERVIEW | Inquiry star rating data overview |
| TRADE_STAR_LEVEL_DATA_OVERVIEW | Transaction star rating data overview |
| ACTION_SUGGESTION | Action suggestions (daily inquiry & transaction trends, task list) |
| PRODUCT_DATA_OVERVIEW | Product data overview (product tiers, category Top 5) |
| EXPOSURE_TOP10_PRODUCT_DATA | Top 10 products by exposure |
| CATEGORY_EXPANSION_SUGGESTION | Category expansion suggestions |
| BUYER_DISTRIBUTION_DATA | Buyer distribution data (by exposure/visits/inquiries) |
| P4P_PLAN_OPTIMIZ_SUGGESTION | P4P (Pay-for-Performance) campaign optimization suggestions |
| P4P_SEARCH_WORD_OPTIMIZ_SUGGESTION | P4P search keyword optimization suggestions |
| FLOW_SOURCE_CHANNEL_ANALYSIS | Traffic source channel analysis |
| STORE_DETAIL_12_MONTHS | Store performance details for the last 12 months |
| STORE_COMMUNICATION_CONVERSION_OVERVIEW | Communication conversion data overview |
| STORE_ACCOUNT_DATA_OVERVIEW | Store sub-account data overview |
| EMPLOYEE_MANAGEMENT | Employee management |

> For complete field mappings and definitions, see: `reference/weekly_report_data_explanation.md`

#### Post-retrieval Behavior

After retrieving the full report data, only display:

> Full data analysis complete. Feel free to ask me any questions about your store data!

Wait for the user to ask specific questions before providing targeted answers based on the complete data. Do not proactively display full data content; keep the data in context for on-demand analysis.

#### Data Application Examples

| User Question | Corresponding Data Module |
| --- | --- |
| What are my store traffic sources? | `FLOW_SOURCE_CHANNEL_ANALYSIS` |
| How is P4P performing? | `P4P_DATA_OVERVIEW_1` / `P4P_DATA_OVERVIEW_2` + `P4P_PLAN_OPTIMIZ_SUGGESTION` |
| How are my products performing? | `PRODUCT_DATA_OVERVIEW` + `EXPOSURE_TOP10_PRODUCT_DATA` |
| Where are my buyers from? | `BUYER_DISTRIBUTION_DATA` |
| What's my star rating status? | `STORE_DIAGNOSIS` + `OPPORTUNITY_STAR_LEVEL_DATA_OVERVIEW` + `TRADE_STAR_LEVEL_DATA_OVERVIEW` |

When answering, combine field descriptions from `reference/weekly_report_data_explanation.md` to translate raw data into business-friendly language with analysis and recommendations. Use Markdown table format for data display, maintaining a consistent style with Step 4.

---

## Exception Handling

| Exception Scenario | Detection Signal | Resolution |
| --- | --- | --- |
| Not logged in (redirect) | URL redirects to `login.alibaba.com` | Prompt "Login not detected, please complete login", poll and wait |
| Not logged in (API error) | Abnormal API response or error code | Guide user to open login page, retry every 10 seconds |
| System busy | Page contains "System busy" message | Prompt user to try again later |
| Weekly report load timeout | Polling exceeds 30 iterations (5 minutes) | Stop polling, prompt "Weekly report retrieval timed out, please try again later" |
| Empty data / missing fields | Empty response or missing `encryptedReportId` | Output fallback message, terminate workflow |
| `weekDiagnose` missing | Field does not exist or is empty | Note "Business diagnostics not included", continue outputting other data |
| Diagnostic title/summary both empty | Both `diagnoseTitle` and `diagnoseSummary` have no value | Skip this diagnostic item, continue processing |

---

## Operational Standards

1. **Privacy & Security**: Only retrieve business data explicitly requested by the user; do not proactively scrape or disclose sensitive store configurations
2. **Content Format**: All analysis results output in Markdown format; preserve Alibaba International Station related links
3. **Request Frequency**: Avoid high-frequency repeated requests to the same API in a short period to prevent triggering security controls
