# AlgaPSA ↔ Acronis Cyber Protect Cloud — Customer Sync (Middleware)

Fast, configurable middleware to **sync customers/tenants** from **Acronis Cyber Protect Cloud** into **AlgaPSA**.
Built with **FastAPI** + **APScheduler** for manual and scheduled runs. Works great on Replit, Docker, or bare Python.

> ✅ Out-of-the-box: pulls Acronis **customer tenants** and **upserts** matching **companies** in AlgaPSA.
> 🔧 Fully configurable field mappings in `src/mapping.yaml` (no code changes needed).
> 🧪 Includes a dry-run mode and idempotent conflict-free upserts using a stable external key.

---

## Quick Start (Local / Replit)

1) **Clone or import**
```bash
git clone https://github.com/your-org/alga-acronis-sync.git
cd alga-acronis-sync
```

On **Replit**: Import from GitHub, or upload the provided `.zip` and extract.

2) **Create `.env`** (copy from example)
```bash
cp .env.example .env
# fill in values
```

3) **Run**
```bash
python -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
uvicorn src.app:app --host 0.0.0.0 --port 8000 --reload
```

Open http://localhost:8000/docs for interactive API.

---

## Environment Variables

Create and fill `.env` (see `.env.example`).

### Acronis (Partner tenant API client)
- `ACRONIS_BASE_URL` — e.g. `https://us2-cloud.acronis.com/api/2` (your DC URL + `/api/2`)
- `ACRONIS_AUTH_URL` — e.g. `https://us2-cloud.acronis.com/api/2/idp/token`
- `ACRONIS_CLIENT_ID` — API client ID (Management Portal → Settings → API Clients)
- `ACRONIS_CLIENT_SECRET` — API client secret
- `ACRONIS_TENANT_ID` — Your partner tenant UUID (scope for listing sub-tenants)

### AlgaPSA
- `ALGA_BASE_URL` — base API url (e.g. `https://your-alga.example.com/api`)
- `ALGA_API_TOKEN` — Bearer token
- `ALGA_COMPANIES_ENDPOINT` — relative path to companies collection (default `/companies`)
- `ALGA_COMPANY_SEARCH_ENDPOINT` — relative path to search (default `/companies/search`)
- `ALGA_COMPANY_KEY_FIELD` — field to use as idempotent external key (default `externalId`)

### App
- `SYNC_CRON` — optional CRON string for scheduler, e.g. `0 */1 * * *` (hourly). Leave empty to disable.
- `DRY_RUN` — `true` to only print what would change.
- `LOG_LEVEL` — `INFO` (default), `DEBUG`, etc.

---

## What gets synced?

**Source:** Acronis *customer tenants* (`kind=customer`) listed under your partner tenant.

**Target:** AlgaPSA *companies* (configurable endpoints). Upsert rule:
- Find by `ALGA_COMPANY_KEY_FIELD` (defaults to `externalId`). If absent, tries name match.
- Create if missing; update if found.

**Default field mapping** (override in `src/mapping.yaml`):
- `name` ← Acronis tenant `name`
- `externalId` ← Acronis tenant `id`
- `notes` ← Acronis tenant `kind` and id for traceability

---

## API Endpoints

- `POST /sync` — runs a one-time sync now (optional `dry_run=true` query)
- `GET /health` — simple health probe
- `GET /preview` — shows transformed payloads without writing

If `SYNC_CRON` is set, the scheduler runs background syncs on the given cadence.

---

## Run with Docker

```bash
docker build -t alga-acronis-sync:latest .
docker run --env-file .env -p 8000:8000 alga-acronis-sync:latest
```

---

## Notes & References

- Acronis API: Account Management (tenants) and token flow. See developer docs.
- AlgaPSA: uses its published OpenAPI. Endpoints are configurable via env if your route prefixes differ.

---

## License

MIT for this middleware sample (see `LICENSE`). Follow your platform licenses accordingly.



## Docker (first-run setup wizard)

```bash
docker build -t alga-acronis-sync:latest .
docker run -p 8000:8000 \  -e DATA_DIR=/data \  -v $(pwd)/data:/data \  --name alga-acronis-sync alga-acronis-sync:latest
```

Then open `http://localhost:8000/` — you'll be redirected to **/setup**. Enter your Acronis/Alga settings and create the **admin** login.
Settings are persisted to `/data` (mounted volume). After setup, you'll be taken to the **/admin** dashboard.


---

## CI/CD — Build & Push to GHCR

This repo includes a GitHub Actions workflow that builds and pushes the Docker image to **GitHub Container Registry (GHCR)** on pushes to `main` and tags.

**Setup**:
1) Ensure your repo is public or that consumers have permission to pull from GHCR.
2) The workflow uses `${{ secrets.GITHUB_TOKEN }}` with `packages: write` permission by default (granted automatically).

**Image name**:
- `ghcr.io/<owner>/<repo>:latest`
- Also tags: the Git `sha`, and any semver tag (e.g. `v1.0.0`).

**Server install using prebuilt image** (no on-server build):
```bash
# One-liner using the installer; swap in your repo & GHCR coordinates
sudo bash -c "$(curl -fsSL https://raw.githubusercontent.com/<owner>/<repo>/main/scripts/install.sh)" \  --repo https://github.com/<owner>/<repo>.git \  --image-repo ghcr.io/<owner>/<repo> \  --port 8000
```
