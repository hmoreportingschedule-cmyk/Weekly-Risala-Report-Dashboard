WEEKLY RISALA REPORT - SINGLE LINK OFFICE LOGIN SETUP

1) GitHub/Vercel
- Replace your current index.html with the included index.html.
- Keep the same Vercel project and same URL.
- Do NOT create a separate employee-login.html page. Office Login is built into index.html.

2) Google Apps Script
- Replace Code.gs with the included Code.gs.
- Deploy > Manage deployments > Edit > New version > Web app.
- Execute as: Me
- Who has access: Anyone
- Copy the deployed /exec URL and confirm it matches the API URL already present in index.html.

3) Google Sheet
Create/keep a tab named EXACTLY: User Id
Headers in row 1:
User_ID | Password | Name | State | Region | District | Department | Role | Status

Example:
EMP001 | 12345 | Employee 1 | Maharashtra | Mumbai | Raigad | Dept 1 | District | Active

The login is NOT hard-coded. Every login reads the current User Id sheet. If you change User_ID, Password, Status, State, Region, District, Department or Role in the sheet, the next login uses the changed values automatically.

4) Roles
Admin/All = all reports
State = same State
Region = same State + Region
District = same State + Region + District
Department = same Department

5) Public page
- Public submission stays on the same home page.
- There is NO top Submit Report button.
- Only the Submit Report button inside the form remains at the bottom.
- Office Login appears at the top right.

Security note: this version compares the Password value from the Google Sheet directly because that is the requested setup. Do not share the sheet publicly. For stronger production security, passwords can later be moved to hashed authentication.
