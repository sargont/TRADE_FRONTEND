# TRADE_FRONTEND
Frontend for a forex and commodity trading information portal.

When the backend API is unavailable, the signal form falls back to random test
data so developers can continue working on the UI.

The dashboard includes a React + TailwindCSS modal for purchasing usage
credits. Payments are mocked via `/api/purchase` and the user's balance is
updated on success.

Every signal request consumes one credit per pair. The form displays the cost
before generating and will prompt you to top up if your balance is too low.

## New Features
- **Login page** (`login.html`) allows simple entry before accessing the dashboard.
- Each generated trade can be committed or rejected. Multiple trades may be selected and committed or rejected in bulk.
- Committed trades appear under **Open Trades** with options to mark them as *Won* or *Lost* and to request a re-assessment via `/api/update-trades`.
- Closed trades are moved to the **History** panel for reference.

