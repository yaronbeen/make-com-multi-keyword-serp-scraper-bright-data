# Import Guide

## Method 1: Import via Make.com UI

1. Download `blueprint.json` from this repository
2. Log in to [Make.com](https://www.make.com/)
3. Click **Scenarios** in the left sidebar
4. Click **Create a new scenario**
5. In the scenario editor, click the **...** menu at the bottom
6. Select **Import Blueprint**
7. Choose the downloaded `blueprint.json` file
8. The scenario will be created with all modules pre-configured

## Method 2: Clone and Import

```bash
git clone https://github.com/yaronbeen/make-com-multi-keyword-serp-scraper-bright-data.git
cd make-com-multi-keyword-serp-scraper-bright-data
```

Then follow Method 1 using the `blueprint.json` from the cloned repo.

## Post-Import Configuration

### Required Connections

| Connection      | Module                  | How to Set Up                                                |
| --------------- | ----------------------- | ------------------------------------------------------------ |
| Bright Data MCP | MCP Client modules (x2) | See [SETUP.md](SETUP.md#2-set-up-bright-data-mcp-connection) |
| Google Sheets   | Google Sheets module    | See [SETUP.md](SETUP.md#3-set-up-google-sheets-connection)   |

### Required Variables

Update the **Input Parameters** module (first module):

| Variable      | Required | Notes                                 |
| ------------- | -------- | ------------------------------------- |
| `keyword`     | Yes      | Must be a valid JSON array of strings |
| `domain`      | No       | Defaults to google.com                |
| `num_results` | No       | Defaults to 5                         |
| `country`     | No       | Defaults to United States             |

### Google Sheets Setup

Create your output spreadsheet using one of these methods:

- Import `google-sheets-template.csv` into Google Sheets
- Create a blank sheet with the headers from the template

Then update the spreadsheet ID in the Google Sheets module.

## Customization Tips

- **Add more keywords**: Expand the keyword JSON array
- **Change search engine**: Update the `domain` variable (google.com, bing.com, yandex.com)
- **Increase results**: Change `num_results` (higher values use more MCP credits)
- **Add scheduling**: Set up a Make.com schedule trigger to run automatically
- **Custom output**: Modify the Normalize Output Schema module to add/remove fields
- **Different storage**: Replace the Google Sheets module with Airtable, database, or webhook

## Version History

| Version | Date       | Changes         |
| ------- | ---------- | --------------- |
| 1.0.0   | 2026-02-09 | Initial release |
