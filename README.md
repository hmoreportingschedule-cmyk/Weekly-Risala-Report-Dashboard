WEEKLY RISALA REPORT - DATA FIX

WHY THE DASHBOARD WAS SHOWING 0:
1. The Apps Script was using getActiveSpreadsheet(). A deployed web app should not depend on an active spreadsheet.
2. The Responses sheet headers are "District Name", "Division Name", "State Name", "Region Name", and "Country". The old code only detected exact District/State/Region names.
3. The old role logic did not support Country or multiple roles correctly.

THIS FIX:
- Uses the exact Spreadsheet ID directly with openById().
- Reads Responses reliably.
- Detects current headers and old headers.
- Supports Country, State, Region, District, Department, Division and multiple roles such as Country,Department.
- Uses India as fallback Country for old rows with blank Country.
- Keeps the User Id sheet structure.
- Keeps public report submission.

IMPORTANT DEPLOY STEPS:
1. Open the Google Apps Script project connected to your current dashboard.
2. Open Code.gs.
3. Delete the entire old Code.gs.
4. Paste the entire Code_FIXED_FINAL.gs contents.
5. Save.
6. Deploy > Manage deployments > Edit (pencil) > New version > Deploy.
7. Keep access as Anyone.
8. Use the SAME /exec URL in your Vercel index.html.
9. Logout from dashboard, clear old session by closing/reopening or use Logout, then login again.
10. Hard refresh Vercel: Ctrl+F5.

DO NOT rename:
- Responses
- User Id
- Setting
- PincodeMaster

User Id headers:
User_ID | Password | Name | State | Region | District | Country | Department | Role | Status

Example:
Employee 4: Country=India, Role=Country, Status=Active
Employee 3: State=Gujarat, Role=State, Status=Active
Employee 2: Region=Delhi, Role=Region, Status=Active
Employee 1: District=Raigad, Role=District, Status=Active

After deployment, dashboard should show Responses rows and values.
