# Job Tracker — Real Estate PM roles

Duplicate of [BRNTNG/job-tracker](https://brntng.github.io/job-tracker/) with the
exact same parameters, filters and automation — but for **real estate project
management roles only** (not BA/PM).

Live site (once pushed and GitHub Pages is enabled):
https://brntng.github.io/job-tracker-real-estate/

## What it tracks

- **Roles (real estate PM only):** real estate project manager / coordinator,
  associate real estate project manager, real estate development project
  manager, development project manager / coordinator, land development
  coordinator / project manager, real estate development coordinator / analyst,
  construction project coordinator, development coordinator, real estate analyst
- **Where:** Toronto / GTA for location searches (explicit GTA municipality
  filter) + remote Canada for the remote queries
- **Level:** junior / entry + associate only. LinkedIn experience levels 2–3,
  interns excluded, and Senior / Director / VP / Principal / Lead / Architect
  titles filtered out (`project manager`, `development manager` and
  `real estate project manager` are protected from the generic manager filter)
- **Sources:** LinkedIn (guest), federal Job Bank, We Work Remotely, Dice —
  same fetch functions as the original
- **Queries:** 18 LinkedIn (14 Toronto + 4 remote Canada), 6 Job Bank, 4 Dice
  — see `config.json` (the fallback `EMBEDDED_CONFIG` in `app.py` matches it exactly)
- **Categories:** Real Estate Development, Real Estate Project Management,
  Construction Project Management, Project Management, Real Estate Analysis

## How it runs

- `app.py` fetches, dedupes by normalized title + company, stores in SQLite
  (`jobs.db`), prunes listings older than 90 days, renders `dashboard.html`
  and, with `JT_BUILD_PAGE=1`, builds the compact published `index.html`
  (max 200 listings, ~95k chars).
- GitHub Actions (`.github/workflows/update-jobs.yml`) runs twice a day on the
  Toronto AM/PM slot gate — cron `0 4,5,16,17 * * *` UTC, gated by
  `last_slot.txt` — and commits `index.html`, `dashboard.html`, `jobs.db`
  and `last_slot.txt` back to the repo.
- Dashboard localStorage keys are `jt_re_bookmarks` / `jt_re_applied` /
  `jt_re_notinterested`, so Saved / Applied / Not Interested marks do not
  collide with the original tracker on the same `brntng.github.io` origin.

## Run locally

```bash
JT_NO_PUBLISH=1 JT_BUILD_PAGE=1 python3 app.py   # fetch + store + render + build index.html
python3 app.py --render                          # only re-render dashboard.html
python3 -c "import app; open('index.html','w').write(app.build_page())"  # rebuild index.html from dashboard.html
```

## Setup for a new GitHub repo

1. Create `BRNTNG/job-tracker-real-estate`, push this directory as its root.
2. Enable GitHub Pages from the `main` branch / root.
3. The workflow needs only `contents: write` (already declared) — no secrets.
