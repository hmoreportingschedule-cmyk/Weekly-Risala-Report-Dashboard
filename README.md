WEEKLY RISALA REPORT - CASCADING FILTER UPDATE

This package keeps the working dashboard/report logic and adds dependent dashboard filters:

Search (smaller) -> Region -> State -> Division -> District

Behavior:
1. Region selected: State/Division/District options are limited to that Region.
2. State selected: Division/District options are limited to that State inside the selected Region.
3. Division selected: District options are limited to that Division inside the selected Region/State.
4. Changing a higher-level filter resets the lower filters.
5. Search continues to work together with all filters.
6. Existing server-side role access remains in Code.gs.

Files:
- Code.gs
- index.html

IMPORTANT:
- Replace the Apps Script Code.gs with this Code.gs.
- Replace the HTML file content with this index.html.
- Deploy a NEW VERSION of the Apps Script Web App (or Manage deployments -> Edit -> New version).
- Use the updated /exec URL in the Vercel/frontend if the deployment URL changed.
