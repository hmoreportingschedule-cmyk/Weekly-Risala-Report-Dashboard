WEEKLY RISALA REPORT - FINAL 5 POINT UPDATE

Executable files:
1. Code.gs -> Google Apps Script project
2. index.html -> Vercel project

The third old TXT backend file should NOT be pasted into Apps Script; it is an older duplicate backend.

Updates included:
1. Excel download creates Row Data + Dashboard sheets.
2. Web Dashboard graph order: Region | State, then Division | Department.
3. Dashboard clock: Date: DD/MM/YYYY, Time : HH:MM:SS (India time), immediately left of Logout.
4. Row Data table shows 15 rows per page with Previous / page numbers / Next.
5. "Employee Dashboard" line removed.

Deployment:
- Replace entire Code.gs in the Apps Script project.
- Deploy > Manage deployments > Edit > New version > Web app > Execute as Me > Who has access: Anyone.
- Replace the Vercel index.html with the supplied index.html.
- Vercel deploy.
- Browser: Ctrl+F5.
