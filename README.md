WEEKLY RISALA REPORT - FINAL REPORT CONNECTION FIX

IMPORTANT FIX:
Code.gs now connects directly to Spreadsheet ID:
1eJs7TlbCbs4QcX6qdeZJhsuybcgzPr30JGb9BPtzvxI

This fixes the dashboard showing:
Responses rows: 0
Visible entries: 0
Report quantity: 0
when the Responses sheet actually contains data.

Steps:
1. Google Apps Script: replace the entire Code.gs with this Code.gs.
2. Save.
3. Deploy > Manage deployments > Edit > New version > Deploy.
4. Keep access as Anyone.
5. Keep the same /exec URL if possible.
6. Open Vercel dashboard and press Ctrl+F5.

Do NOT change User Id or Responses sheet names.
Country role: User Id Country=India, Role=Country.
