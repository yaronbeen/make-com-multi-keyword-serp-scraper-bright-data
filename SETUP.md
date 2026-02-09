# Setup Guide

## 1. Import the Blueprint

1. Download `blueprint.json` from this repository
2. Open [Make.com](https://www.make.com/) and go to your dashboard
3. Click **Create a new scenario**
4. Click the **...** menu (bottom of the editor) and select **Import Blueprint**
5. Upload the `blueprint.json` file

## 2. Set Up Bright Data MCP Connection

### What is Bright Data MCP?

Bright Data MCP is a free AI-powered web scraping tool that connects directly to Make.com via the Model Context Protocol (MCP). It handles proxies, browser rendering, CAPTCHA solving, and anti-bot bypass automatically.

### Free Tier

- **5,000 free credits** on signup
- **No credit card required**
- Credits cover SERP scraping, webpage scraping, and batch operations

### Setup Steps

1. Sign up at [brightdata.com](https://brightdata.com) (free account)
2. Navigate to **MCP Integrations** in your dashboard
3. Copy your **MCP token**
4. In Make.com, click the **MCP Client** module in the scenario
5. Click **Add** next to Connection
6. Configure:
   - **Connection type**: SSE (Server-Sent Events)
   - **Server URL**: `mcp.brightdata.com`
   - **Authentication**: Token (paste your token)
7. Click **Save**

### Available MCP Tools

| Tool                  | Description                                      |
| --------------------- | ------------------------------------------------ |
| `search_engine`       | Scrape SERP results from Google, Bing, or Yandex |
| `scrape_as_markdown`  | Scrape a webpage and return content as Markdown  |
| `search_engine_batch` | Run multiple search queries simultaneously       |
| `scrape_batch`        | Scrape multiple URLs simultaneously              |

## 3. Set Up Google Sheets Connection

1. Click the **Google Sheets** module in the scenario
2. Click **Add** next to Connection
3. Sign in with your Google account and grant permissions
4. Create a new Google Sheet:
   - Import the provided `google-sheets-template.csv` file, **OR**
   - Create a blank sheet with headers: `Query`, `Engine`, `Rank`, `Title`, `URL`, `Domain`, `Source from`, `Scraped_at`
5. Update the **Spreadsheet ID** in the module to point to your new sheet
6. Ensure the sheet name is **Sheet1** (or update the module to match your sheet name)

> **How to find your Spreadsheet ID**: Open your Google Sheet in a browser. The URL looks like `https://docs.google.com/spreadsheets/d/YOUR_SPREADSHEET_ID/edit`. Copy the long string between `/d/` and `/edit`.

## 4. Configure Input Variables

Click the first module (**Input Parameters**) and update:

| Variable      | What to Set                                                           |
| ------------- | --------------------------------------------------------------------- |
| `keyword`     | Your JSON array of keywords, e.g. `["seo tools", "keyword research"]` |
| `domain`      | Search engine: `google.com`, `bing.com`, or `yandex.com`              |
| `num_results` | How many results per keyword (1-10 recommended)                       |
| `country`     | Target country for localized results                                  |

## 5. Test the Scenario

1. Click **Run once** in the Make.com editor
2. Check each module for green checkmarks
3. Open your Google Sheet to verify results appeared
4. Check the execution log for any errors

## 6. Troubleshooting

| Issue                      | Solution                                                                                 |
| -------------------------- | ---------------------------------------------------------------------------------------- |
| MCP connection fails       | Verify your Bright Data token is correct and hasn't expired                              |
| Empty results              | Try different keywords or check if the search engine domain is correct                   |
| CAPTCHA errors logged      | This is normal for some queries. Bright Data handles most CAPTCHAs. Retry usually works. |
| Invalid JSON errors        | The MCP response format may vary. Check the raw response in the error handler output.    |
| Can't find error details   | Click the error handler module (pink) after a failed run to see captured error variables |
| Google Sheets not updating | Verify the spreadsheet ID and sheet name match your configuration                        |
| Rate limiting              | Add longer delays between keywords or reduce the keyword count per run                   |
