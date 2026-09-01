WEEKLY RISALA REPORT - CASCADING FILTERS + EXCEL DOWNLOAD

Updated:
1. Search + Region + State + Division + District cascading filters.
2. Dashboard report data and charts remain access-controlled by logged-in user.
3. Added "Download Excel" button.
4. Excel export contains ONLY the rows currently visible after the user's access scope, filters and search.
5. Download includes Reports sheet and Summary sheet with active filters.

INSTALL:
- Replace Code.gs in Apps Script.
- Replace index.html in Apps Script.
- Save.
- Deploy > Manage deployments > Edit > New version > Deploy.
- Refresh dashboard with Ctrl+F5.

NOTE:
Excel generation uses SheetJS from the official CDN in index.html, so the browser needs internet access when loading the dashboard.
