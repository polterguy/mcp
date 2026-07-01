---
name: analytics-dashboard-workflow
when_to_use: Building an interactive analytics dashboard that visualizes metrics from a database or API as charts on a webpage.
---

# Create an analytics dashboard

A guided process for building interactive analytics dashboards that visualize data from
databases or APIs.

## Steps

1. Ask the user which data source to use (database or API).
   - A user's own CSV file can be uploaded in SQL Studio.
   - You must create an API to feed the dashboard with data.
2. Retrieve the schema or data structure (`get-database-schema`) to understand the available
   metrics.
3. Let the user choose chart types (bar, line, pie, table, etc.).
4. Generate HTML using Chart.js, D3.js, Three.js, or Mermaid for visualization.
5. Combine HTML, CSS and JS into a responsive dashboard layout.
6. Save the dashboard inside `/etc/www/UNIQUE_EXPLAINABLE_NAME` as a SPA webpage (`create-file`).
   Choose a relevant, explainable folder name for the app.

## Rules for generating KPI endpoints

When using `generate-hyperlambda` to create KPI endpoints (or endpoints in general):

1. Do NOT create one single endpoint that returns all data. Instead create several smaller,
   simpler HTTP endpoints, each returning one or two concepts at most. Save each with a
   `filename`; a saved endpoint is automatically exposed as an MCP tool.

## Outcome

A reusable analytics dashboard that displays key metrics.

## Authentication for the GUI

If the user wants a GUI restricted to authorized users only, the main GUI must be invisible
until the user has logged in. Provide a login button/form, and show a logout button once
authenticated. Hide the login button/logic/form after login. Store the token in `localStorage`.

The frontend authentication flow is:

1. Hide main app and show login UI.
2. User logs in.
3. Hide login UI and show app UI.
4. User logs out.
5. Hide app UI and show login UI.
