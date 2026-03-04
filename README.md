# EY Contact Navigator — Railway Deployment

## Quick Deploy

### Option A: GitHub → Railway (recommended)
1. Push this folder to a **GitHub repo**
2. Go to [railway.app](https://railway.app) → **New Project** → **Deploy from GitHub**
3. Select the repo → Railway auto-detects Python + builds
4. Set environment variable: `SECRET_KEY` = any random string (e.g. `openssl rand -hex 32`)
5. Railway assigns a public URL → done

### Option B: Railway CLI
```bash
npm install -g @railway/cli
railway login
cd navigator_v11
railway init
railway up
```

## Environment Variables

| Variable | Required | Default | Description |
|----------|----------|---------|-------------|
| `SECRET_KEY` | **Yes** (prod) | dev key | Flask session encryption key |
| `PORT` | Auto | 5000 | Railway sets this automatically |

## Default Login Credentials

| Username | Password | Role | Access |
|----------|----------|------|--------|
| `admin` | `admin123` | Admin | Full access (upload, config, all features) |
| `supervisor` | `super123` | Supervisor | Toggle initiatives, overrides, recalculate |
| `analyst` | `analyst123` | Analyst | Read-only (view all pages, export) |

> ⚠️ Change these passwords after first deploy via the SQLite DB.

## Architecture

```
app.py                    ← Flask app (gunicorn serves this)
engines/                  ← 12 calculation engines
  data_loader.py          ← ETL from Excel files
  waterfall.py            ← Core financial projection
  pools.py                ← Pool-based netting
  gross.py                ← Per-initiative impact
  diagnostic.py           ← Performance diagnostics
  risk.py                 ← 3-axis risk scoring
  workforce.py            ← Workforce transition planning
  channel_strategy.py     ← Channel optimization
  intent_profile.py       ← Intent enrichment
  maturity.py             ← Maturity assessment
  readiness.py            ← Readiness scoring
  recommendations.py      ← Industry-specific recommendations
infrastructure/           ← Auth, DB, file management
templates/                ← Frontend (single-page app)
data/                     ← Excel data files (bundled)
```

## Key Files for Railway

| File | Purpose |
|------|---------|
| `Procfile` | `web: gunicorn app:app --bind 0.0.0.0:$PORT --workers 2 --timeout 120` |
| `railway.toml` | Build/deploy config, health check at `/api/health` |
| `requirements.txt` | Python dependencies (flask, gunicorn, openpyxl, fpdf2) |
| `.python-version` | Python 3.12 |

## Notes

- **Ephemeral filesystem**: Railway's filesystem resets on each deploy. The SQLite DB and uploaded files won't persist across deploys. The bundled Excel data files in `data/` are part of the repo and always available.
- **Health check**: `/api/health` returns `{"status":"ok"}` without requiring auth.
- **Workers**: 2 gunicorn workers with 120s timeout (ETL can take a few seconds on first load).
