WEEKLY RISALA REPORT - MULTI ROLE UPDATE

Existing working system preserved.

USER ID SHEET
Sheet name: User Id
Columns:
User_ID | Password | Name | State | Region | District | Country | Department | Role | Status

ROLE VALUES
Country
State
Region
District
Department
Chain
Division

MULTIPLE ROLE VALUES
Country,Department
State,Department
Region,Department
District,Department
Country,State

Multiple roles are AND conditions.
Example:
Role = Country,Department
Country = India
Department = HMO
=> user sees only India + HMO reports.

IMPORTANT RESPONSE COUNTRY
The dashboard reads Country from Responses if a Country column exists.
If Responses has no Country column, existing Indian reports are treated as Country = India.

DEPLOYMENT
1. Apps Script: replace Code.gs completely.
2. Save.
3. Deploy > Manage deployments > Edit > New version > Web app.
4. Execute as Me, access Anyone.
5. Keep the same /exec URL in index.html (already set to the user's current URL).
6. GitHub: replace index.html completely.
7. Vercel redeploy.
8. Ctrl+F5 and login again.

DASHBOARD
- No raw employee row data.
- No charts.
- Dashboard uses digit cards.
- Filters: Search, Region, State, District, Chain, Department.
- Pivot-style summary appears after a filter/search is selected.
- Summary includes totals and Grand Total.
