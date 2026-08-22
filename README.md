WEEKLY RISALA REPORT - FINAL DASHBOARD FIX

Google Spreadsheet ID:
1eJs7TlbCbs4QcX6qdeZJhsuybcgzPr30JGb9BPtzvxI

Sheets:
- User Id
- Responses
- PincodeMaster
- Setting / Settings (existing project setup)

IMPORTANT:
1. Replace the entire Code.gs in Apps Script with this Code.gs.
2. Save.
3. Deploy > Manage deployments > Edit > New version > Deploy.
4. Web app access must be Anyone.
5. Replace GitHub index.html with this index.html.
6. Redeploy Vercel / wait for deployment, then Ctrl+F5.
7. Login again.

Why this fixes the current problem:
- Web App now uses SpreadsheetApp.openById() instead of getActiveSpreadsheet().
- Added the missing valAt_() function used by the dashboard.
- Responses mapping is detected from headers with the existing fallbacks:
  Department = G
  Risala Report = H
  District Name = J
  Division Name = K
  State Name = L
  Region Name = M
- Location matching accepts labels such as Delhi and Delhi Region.
- Added ?action=testResponses diagnostics.

TEST:
Open the Apps Script Web App URL with:
?action=testResponses

Expected:
{"status":"Success","responsesRows":...}
