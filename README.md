FINAL SETUP - WEEKLY RISALA REPORT

Google Spreadsheet ID:
1eJs7TlbCbs4QcX6qdeZJhsuybcgzPr30JGb9BPtzvxI

Required tabs:
- User Id
- Responses
- Setting
- PincodeMaster

Responses actual columns:
G = Department
H = Risala Report
J = District Name
K = Division Name
L = State Name
M = Region Name

IMPORTANT FIX:
The previous Code.gs was missing the valAt_() function. That caused the dashboard request to fail while login still worked. This final Code.gs includes valAt_() and explicit actual header mapping.

DEPLOY:
1. Open Apps Script connected to the spreadsheet/project.
2. Replace ALL existing Code.gs with the Code.gs in this package.
3. Save.
4. Deploy > Manage deployments > Edit > New version > Deploy.
5. Web app: Execute as Me, Who has access: Anyone.
6. Keep the same /exec URL in index.html.
7. Replace GitHub index.html with this package's index.html.
8. Redeploy Vercel and hard refresh with Ctrl+F5.

Do not rename User Id or Responses.
