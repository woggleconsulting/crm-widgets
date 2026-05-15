# Woggle Function Exporter - Zoho CRM Widget

A CRM widget that exports all Deluge (and NodeJS) function source code from any Zoho CRM org as a structured JSON file. Built for system audits, backups, and health checks.

## Setup: GitHub Pages Hosting (Recommended)

This approach lets you maintain one copy of the widget and reuse it across every client org. Updates to the repo automatically propagate to all orgs.

### 1. Create the GitHub repo

1. Create a new repo (e.g. `woggle-crm-widgets`) -- public or private both work, but public is simpler for GitHub Pages
2. Push the following files to the `main` branch:

```
woggle-crm-widgets/
  function-exporter/
    index.html        <-- rename widget.html to index.html
    plugin-manifest.json
```

3. Go to **Settings > Pages** in the repo
4. Under **Source**, select **Deploy from a branch**
5. Set the branch to `main` and folder to `/ (root)`
6. Click **Save**
7. GitHub will publish your site at: `https://<your-username>.github.io/woggle-crm-widgets/`

### 2. Install the widget in a client org

1. Log into the client's Zoho CRM as admin
2. Go to **Setup > Developer Hub > Widgets**
3. Click **+ Create New Widget**
4. Enter the name: `Function Exporter`
5. Set **Hosting** to **External**
6. For **Base URL**, enter: `https://<your-username>.github.io/woggle-crm-widgets/function-exporter`
7. For **Index Page**, enter: `/index.html`
8. Under **Widget Location**, choose where you want it accessible:
   - **Web Tab** -- adds it to the left nav menu (good for admin tools)
   - **Button** on any module -- puts it behind a custom button click
   - **Settings Widget** -- keeps it in the admin area
9. Click **Save**

### 3. Repeat step 2 for each additional client org

That's it. Same Base URL every time. The widget authenticates against whichever org it's running in via the Zoho JS SDK.

## Usage

1. Open the widget from wherever you installed it
2. It auto-loads all functions in the org
3. Use the search bar and category filter to narrow down if needed
4. Check/uncheck individual functions or use Select All
5. Click **Fetch Code** to pull source code for all selected functions
6. Click **Export JSON** to download the backup file

The exported file is named `functions_<orgname>_<date>.json` automatically.

## Updating the Widget

Since every org points at your GitHub Pages URL, just push changes to `main` and they go live everywhere. No re-uploading zip files per org.

Note: GitHub Pages caches aggressively. If you push an update and don't see it reflected, do a hard refresh (Ctrl+Shift+R) in the browser, or wait a few minutes for the CDN to clear.

## Export Format

```json
{
  "export_info": {
    "tool": "Woggle Function Exporter",
    "version": "1.0.0",
    "exported_at": "2026-05-15T...",
    "org_name": "Client Org Name",
    "org_id": "...",
    "total_functions": 42,
    "functions_with_code": 40,
    "functions_without_code": 2
  },
  "functions": [
    {
      "id": "...",
      "display_name": "UpdateDealStage",
      "api_name": "updatedealstage",
      "category": "workflow",
      "language": "deluge",
      "description": "...",
      "created_by": "Jed Thomas",
      "created_time": "...",
      "modified_by": "Jed Thomas",
      "modified_time": "...",
      "code": "// full function source code here",
      "code_fetched": true
    }
  ]
}
```

## Troubleshooting

**"Failed to load functions" error:**
- Confirm you're logged in as an admin or have Developer Hub access
- Some older orgs may use a different API structure; the widget tries
  both v2 and v2.2 endpoints automatically

**Functions show but code fetch fails:**
- This is expected for some system/extension functions that don't
  expose source code via the API
- Check the log panel at the bottom for specific error messages

**Widget shows a blank iframe or "refused to connect":**
- Make sure the GitHub Pages site is published and accessible
- Check that the Base URL in the widget config doesn't have a trailing slash
- If using a private repo, GitHub Pages won't serve the files publicly;
  switch to a public repo or use a different static host

**Widget doesn't render inside CRM:**
- Zoho's CSP allows external widget hosting, but the URL must be HTTPS
  (GitHub Pages is HTTPS by default, so this should be fine)
- Make sure the Index Page path starts with a forward slash: `/index.html`

## Notes

- Requires admin permissions in the CRM org to read function settings
- Uses relative URLs through the Zoho JS SDK, so it works on any
  data center (US, EU, IN, AU, etc.) without configuration
- Functions are fetched sequentially with a small delay to avoid
  rate limiting
- Extension-bundled functions sometimes don't expose their source code
  via the API; these will show as "Error" status
