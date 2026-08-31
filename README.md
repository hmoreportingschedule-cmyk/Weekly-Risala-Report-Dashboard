WEEKLY RISALA REPORT — COUNTRY ROLE FIX

Problem fixed:
The current Responses sheet has no Country column (A:M ends at Region). The previous code never put country into each response row, so a user with Role=Country received 0 visible rows.

Fix:
- If Responses has a Country column, it is used.
- If Responses has no Country column, existing Indian reports are treated as Country=India.
- User Id Country=India + Role=Country will now see the reports.
- State/Region/District/Department/Chain/Division roles remain supported.
- Multiple roles remain supported, e.g. Country,Department.

Deploy:
1. Replace Code.gs in Apps Script.
2. Save.
3. Deploy > Manage deployments > Edit > New version > Deploy.
4. Replace index.html on GitHub only if needed; the current index is included for completeness.
5. Redeploy Vercel and Ctrl+F5.
