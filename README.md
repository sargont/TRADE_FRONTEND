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
- **Home page** (`index.html`) introduces the platform and links to all sections.
- **Login page** (`login.html`) allows simple entry before accessing the dashboard.
- **Dashboard** (`dashboard.html`) generates trade signals and lets users commit or reject them. Committing a trade now asks for confirmation then posts it to `/api/direv`.
- **History page** (`history.html`) lists closed trades stored in the browser.
- Committed trades appear under **Open Trades** with options to mark them as *Won* or *Lost*.

