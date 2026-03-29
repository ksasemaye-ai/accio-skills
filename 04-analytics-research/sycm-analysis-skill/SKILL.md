---
name: sycm-analysis-skill
description: Taobao Sycm (Business Advisor) data analysis tool. Uses browser sessions to call Sycm and retrieve store weekly report data.
---

# Taobao Sycm (Business Advisor) Data Analysis SKILL

## 1. Tool Overview

This tool calls Sycm internal APIs through browser sessions, supporting:

* **Weekly Business Report Retrieval**: Deep Markdown report extraction for store periodic performance.

## 2. Environment & Prerequisites

* **Browser Environment**: Must use the `browser` tool, set `profile=openclaw`.
* **Target Domain**:
  * **Production Environment**: `sycm.taobao.com` (primarily used for AI queries).


* **Login Status**: User must be logged in.
If not logged in, redirect to: login.taobao.com for login. Use the `Exec` tool for polling to verify login status.


## 3. Core Feature Flow
### Weekly Business Report Retrieval

1. **Check Login Status**:
Visit `sycm.taobao.com`. If the response does not auto-redirect to `https://sycm.taobao.com/custom/login.htm`, the user is logged in. If it auto-redirects, call `Exec` to poll and check login status — do not terminate the conversation.


2. **Send Request**:
Call the API to send a "view weekly report" command:
  **Code Template**:
   ```javascript
   async () => {
     const query = encodeURIComponent("查看周报");
     const url = `https://sycm.taobao.com/ucc/next/message/send.json?text=${query}`;
     const r = await fetch(url);
     return await r.json();
   }
   ```
*Extract `conversationCode` and `sendTime` from the response.*

3. **Async Polling**:
Send a request every **5 seconds** to the endpoint below until `data.content` is non-empty, with a maximum wait of 5 minutes.
`https://sycm.taobao.com/ucc/next/message/getReportResult.json?conversationCode={conversationCode}&sendTime={sendTime}`
Must use multiple separate calls, checking API status once per call. Do not use the browser's `evaluate` tool as it has a timeout limit.

3. **Report Display**:
3.1 Directly return the complete Markdown content from `data.content`, including core conclusions, visitor analysis, competitor trends, etc.

## 4. Exception Handling & Login Verification Mechanism

| Exception Scenario | Detection Signal | Solution |
| --- | --- | --- |
| **Not Logged In (Redirect)** | Page redirects to `login.taobao.com` | Prompt user: "Login not detected, please complete QR code login". |
| **Not Logged In (API Error)** | Abnormal API response | Guide user to open the login page, auto-retry every 5 seconds to check status. |
| **System Busy** | Page content contains "Too many visitors, queuing" | Prompt user to try again later. |
| **Weekly Report Generation Timeout** | Polling exceeds 100 iterations (5 minutes) with no result | Stop polling, prompt "Weekly report generation timed out, please try again later". |


## 5. Operational Standards

1. **Privacy & Security**: Do not proactively scrape or disclose sensitive store configuration beyond what the user explicitly requests.
2. **Content Presentation**:
   * All report content must maintain original Markdown format to ensure charts and links are functional.
   * Qianniu links (`qianniu.taobao.com/...`) in reports should be preserved for user clickability.


3. **Rate Limiting**: Avoid high-frequency repeated requests to the same API in a short period to prevent triggering security controls.
