# Architecture

## Flow Diagram

```
[1] Input Parameters
 |
 v
[30] Keyword Iterator (loops through keyword array)
 |
 v
[6] SERP Fetch (Bright Data MCP) -----> onerror:
 |                                        [16] Sleep 3s
 |                                         |
 |                                        [17] Retry SERP Fetch
 |                                         |
 |                                        [18] Sleep 3s (Final)
 |                                         |
 |                                        [19] Fallback Error Output
 v
[12] Parse JSON Results -----> onerror:
 |                              [23] Invalid JSON Error Handler
 v
[21] Result Decision Router
 |          |           |
 v          v           v
Route 1   Route 2     Route 3
(results) (empty)     (CAPTCHA)
 |          |           |
 v          v           v
[31]      [26]        [29]
Results   Empty       CAPTCHA/Blocked
Iterator  Results     Error Handler
 |        Handler
 v
[25] Normalize Output Schema
 |
 v
[15] Persist to Google Sheets
```

## Module Inventory

| ID  | Type                    | Name                          | Purpose                                                     |
| --- | ----------------------- | ----------------------------- | ----------------------------------------------------------- |
| 1   | `util:SetVariables`     | Input Parameters              | Define keyword array, domain, num_results, country          |
| 30  | `builtin:BasicFeeder`   | Keyword Iterator              | Iterate through keyword array one by one                    |
| 6   | `mcp-client:DoAnAction` | SERP Fetch (Bright Data)      | Call Bright Data MCP to fetch organic SERP results          |
| 16  | `util:FunctionSleep`    | Retry Delay (3s)              | Wait 3 seconds before retry (error handler)                 |
| 17  | `mcp-client:DoAnAction` | Retry SERP Fetch              | Second attempt at fetching SERP results (error handler)     |
| 18  | `util:FunctionSleep`    | Retry Delay (Final)           | Wait 3 seconds after retry failure (error handler)          |
| 19  | `util:SetVariables`     | Fallback Error Output         | Capture error details when all retries fail (error handler) |
| 12  | `json:ParseJSON`        | Parse JSON Results            | Convert MCP string response to structured JSON              |
| 23  | `util:SetVariables`     | Invalid JSON Error Handler    | Capture error when JSON parsing fails (error handler)       |
| 21  | `builtin:BasicRouter`   | Result Decision Router        | Route based on result status (3 routes)                     |
| 31  | `builtin:BasicFeeder`   | Results Iterator              | Iterate through parsed SERP results                         |
| 25  | `util:SetVariables`     | Normalize Output Schema       | Standardize output fields for Google Sheets                 |
| 15  | `google-sheets:addRow`  | Persist to Google Sheets      | Save each result row to the spreadsheet                     |
| 26  | `util:SetVariables`     | Empty Results Error Handler   | Log when no organic results found                           |
| 29  | `util:SetVariables`     | CAPTCHA/Blocked Error Handler | Log when CAPTCHA or blocking detected                       |

## Data Flow

1. **Input**: keyword array, domain, num_results, country set as execution-scoped variables
2. **Iteration**: Keywords are fed one at a time to the MCP client
3. **Scraping**: Bright Data MCP searches the specified engine and returns JSON with query + results array
4. **Parsing**: Raw MCP string converted to structured JSON (query, results[{rank, title, url, domain}])
5. **Routing**: 3-way decision based on result count and content patterns
6. **Normalization**: Each result mapped to consistent schema with metadata (source, timestamp)
7. **Storage**: Each row written to Google Sheets: Query(A), Engine(B), Rank(C), Title(D), URL(E), Domain(F), Source from(G), Scraped_at(H)

## Error Handling Strategy

### MCP Retry Chain

Module [6] fails -> [16] Sleep 3s -> [17] Retry MCP call -> if fails -> [18] Sleep 3s -> [19] Capture error type, message, source, and timestamp as fallback output.

### JSON Parse Error

Module [12] fails -> [23] Captures status=error, error_type=invalid_json, error_message, raw MCP response, source, and timestamp.

### Empty Results

Router Route 2 -> [26] Captures status, error_type=empty_results, message="No organic results found", query, engine, source, timestamp.

### CAPTCHA/Blocking Detection

Router Route 3 -> [29] Detects patterns via filter conditions (OR logic):

- Title contains "captcha"
- Title contains "unusual traffic"
- Title contains "verify you are human"
- Title contains "access denied"
- Title equals "blocked"

## Router Logic

| Route | Filter Condition               | Destination                                    |
| ----- | ------------------------------ | ---------------------------------------------- |
| 1     | `length(results) > 0`          | Results Iterator -> Normalize -> Google Sheets |
| 2     | `length(results) == 0`         | Empty Results Error Handler                    |
| 3     | Title matches CAPTCHA patterns | CAPTCHA/Blocked Error Handler                  |
