# Acronis ↔ AlgaPSA Integration — Replit App (No Docker)

This bundle runs on Replit with full **Setup Wizard** and **Admin Dashboard**.

## Run on Replit
1) Create a new Repl from this ZIP (or import from GitHub).
2) Click **Run**. Replit installs `requirements.txt` and starts the server.
3) Open the webview → you’ll be redirected to **/setup**:
   - Enter Acronis (US5) + AlgaPSA credentials
   - Create the admin login
   - You’ll land on **/admin** to preview and run syncs

Endpoints: `/health`, `/preview`, `/sync`, `/admin`, `/admin/settings`, `/docs`.

## Defaults (US5)
- `ACRONIS_BASE_URL=https://us5-cloud.acronis.com/api/2`
- `ACRONIS_AUTH_URL=https://us5-cloud.acronis.com/api/2/idp/token`