# Multi-Keyword SERP Scraper with Bright Data MCP

[![Make.com](https://img.shields.io/badge/Make.com-6D00CC?style=flat&logo=make&logoColor=white)](https://www.make.com/)
[![Bright Data MCP](https://img.shields.io/badge/Bright%20Data-MCP-00D4AA?style=flat)](https://brightdata.com/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

A Make.com automation that scrapes organic search engine results for multiple keywords using Bright Data MCP, with built-in retry logic, CAPTCHA detection, and Google Sheets output.

## What It Does

- Scrapes organic SERP results from Google, Bing, or Yandex for multiple keywords in a single run
- Extracts rank, title, URL, and domain for each result
- Handles retries automatically when scraping fails (3-second delay + retry)
- Detects CAPTCHA/blocking and routes to error handling
- Saves normalized results to Google Sheets with full metadata

## How It Works

```
Input Keywords --> Iterate Each Keyword --> Bright Data MCP SERP Fetch
    --> Parse JSON --> Router:
        |-- Has Results --> Iterate --> Normalize --> Google Sheets
        |-- Empty Results --> Log Warning
        +-- CAPTCHA Detected --> Log Error
```

The scenario uses a keyword array as input. For each keyword, it calls the Bright Data MCP `search_engine` tool to fetch organic results. Results are parsed, validated through a 3-way router, and saved to Google Sheets. Error handling includes automatic retries with delays and fallback error logging.

## Input Variables

| Variable      | Default                                                        | Description                           |
| ------------- | -------------------------------------------------------------- | ------------------------------------- |
| `keyword`     | `["best running shoes", "nike air zoom", "adidas ultraboost"]` | JSON array of search keywords         |
| `domain`      | `google.com`                                                   | Search engine domain                  |
| `num_results` | `5`                                                            | Number of organic results per keyword |
| `country`     | `United States`                                                | Target country for search results     |

## Output Schema

| Column     | Type     | Description                             |
| ---------- | -------- | --------------------------------------- |
| Query      | text     | The search keyword used                 |
| Engine     | text     | Search engine domain (e.g., google.com) |
| Rank       | number   | Position in organic results             |
| Title      | text     | Result page title                       |
| URL        | text     | Result page URL                         |
| Domain     | text     | Result domain name                      |
| Source     | text     | Data source identifier (brightdata_mcp) |
| Scraped At | datetime | Timestamp of when data was collected    |

## Prerequisites

- [Make.com](https://www.make.com/) account (free or paid)
- [Bright Data](https://brightdata.com/) account with MCP access (free tier: 5,000 credits, no credit card required)
- Google account (for Google Sheets output)

## Quick Start

1. **Import** the `blueprint.json` into Make.com ([IMPORT-GUIDE.md](IMPORT-GUIDE.md))
2. **Connect** Bright Data MCP ([SETUP.md](SETUP.md))
3. **Create** a Google Sheet using the [CSV template](google-sheets-template.csv)
4. **Configure** your keywords, domain, and country
5. **Run** the scenario

## Error Handling

- **Retry logic**: If the initial MCP call fails, waits 3s and retries. If retry fails, waits 3s more then logs fallback error with error type, message, and timestamp.
- **Invalid JSON**: If MCP response can't be parsed, captures error with raw response for debugging.
- **Empty results**: When no organic results found, logs structured warning with query details.
- **CAPTCHA/blocking**: Detects patterns like "captcha", "unusual traffic", "verify you are human", "access denied", "blocked" and logs them.

## Make It Your Own

This blueprint is a **flexible building block**, not a rigid template. Every part of it -- the prompts, the data structure, the output destination, and the logic -- is designed to be adjusted. Use it as-is for SERP scraping, or reshape it into something completely different.

### Adjust the MCP Prompt

The MCP prompt is where the magic happens. It tells Bright Data what to scrape and how to structure the response. You can rewrite it to extract completely different data:

- **Extract different fields**: Add `snippet`, `date_published`, `image_url`, or any other data visible on the page
- **Change the output format**: Request nested JSON, markdown tables, or a flat list
- **Add instructions**: Tell the MCP to "ignore sponsored results", "only return results from .edu domains", or "translate titles to English"
- **Scrape different pages**: The MCP isn't limited to search engines. Point it at any webpage and describe what you want extracted

### Swap the Data Source

The input doesn't have to be keywords for Google. You can replace the input module with:

- **A webhook** that receives keywords from another system
- **A Google Sheets feeder** that reads URLs or keywords from a spreadsheet
- **An RSS feed** that triggers scraping when new content is published
- **A scheduler** that runs different keywords on different days

### Change the Output

Google Sheets is just one option. Replace the output module with:

- **Airtable** for a richer database with views and filters
- **Notion** for team-accessible dashboards
- **A webhook** to send data to your own API or database
- **Slack/Email** for real-time notifications when specific results appear
- **CSV/JSON file** via Google Drive for bulk data processing

### Extend the Logic

The router, aggregator, and error handling modules are all building blocks you can rearrange:

- **Add filters**: Only save results from specific domains, or with certain keywords in the title
- **Add scoring**: Use a second MCP call to evaluate or rank results before saving
- **Chain scenarios**: Output from this scenario can trigger another Make.com scenario
- **Add deduplication**: Compare new results against existing Sheets data to avoid duplicates
- **Schedule runs**: Set the scenario to run hourly/daily for continuous monitoring

### Example: Turn This Into a Brand Mention Tracker

1. Change keywords to your brand name and competitors
2. Adjust the MCP prompt to extract: `title`, `url`, `snippet`, `sentiment`
3. Add a filter to only keep results mentioning your brand
4. Replace Google Sheets with Slack to get instant alerts

The blueprint handles the hard parts (MCP connection, retries, CAPTCHA detection, JSON parsing, error handling). You just change what goes in and what comes out.

## Documentation

- [SETUP.md](SETUP.md) -- Step-by-step setup guide
- [ARCHITECTURE.md](ARCHITECTURE.md) -- Technical deep-dive
- [IMPORT-GUIDE.md](IMPORT-GUIDE.md) -- How to import and customize

## License

MIT
