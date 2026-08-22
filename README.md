WEEKLY RISALA REPORT - SINGLE LINK + OFFICE DASHBOARD

FILES
1. index.html  -> GitHub/Vercel frontend. Replace your current index.html.
2. Code.gs     -> Google Apps Script. Replace the old Code.gs completely.

GOOGLE SHEETS
- User login tab name: User Id
- Report tab name: Responses
- Existing tabs PincodeMaster and Settings remain unchanged.

IMPORTANT DEPLOYMENT
1. Open the Google Apps Script project connected to your report spreadsheet.
2. Replace ALL Code.gs content with the new Code.gs.
3. Deploy > Manage deployments.
4. Edit the existing Web app deployment and choose New version.
5. Execute as: Me.
6. Who has access: Anyone.
7. Deploy.
8. Keep the same Web App URL if possible. If Google gives a new URL, update scriptURL in index.html.
9. Replace index.html in GitHub and wait for Vercel deployment.
10. Browser hard refresh: Ctrl+F5.

USER ID TAB COLUMNS
A User_ID
B Password
C Name
D State
E Region
F District
G Department
H Role
I Status

ROLES
Admin = all reports
State = matching State
Region = matching Region + State when State is provided
District = matching District + Region + State when provided
Department = matching Department

RESPONSES
The new Code.gs reads Responses headers dynamically and also uses the existing doPost column order as fallback:
Timestamp, Chain Type, User Name, Contact Number, Nigran Level, Zimmedar Level, Department List, Risala Report, Pincode, District, Division, State, Region

DASHBOARD
- Reports are loaded from Responses after login.
- Filters: Search, State, Region, District.
- Summary cards include total entries and report quantity.
- Graphs: State, Region, Department, District.
- Refresh Reports button is included.
- Dashboard also shows source row count and visible row count.
