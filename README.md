# Baseline Logbook

Personal health tracker: daily check-in, self-run fitness / cognitive / mood tests, blood work,
five pillar scores (Longevity, Strength, Cognition, Happiness, Fortitude) and a weekly review.

This repo holds **only the app code**. It contains no health data, so it can be public
(GitHub Pages is free on public repos). All data lives in a separate **private** repo.

## One-time setup (≈10 min)

1. **Private data repo** — GitHub → New repository → name `health-data` → **Private** →
   tick "Add a README" → Create. The app refuses to write to a public repo.
2. **App repo** — New repository → `health-logbook` → Public → upload every file in this
   folder (`index.html`, `sw.js`, `manifest.webmanifest`, the three `icon-*.png`, this README).
   Then Settings → Pages → Source: *Deploy from a branch* → `main` / `(root)` → Save.
   Your link: https://shashishekhawat.github.io/health-logbook/
3. **Token** — GitHub → Settings → Developer settings → Personal access tokens →
   **Fine-grained tokens** → Generate new token:
   - Repository access: **Only select repositories → `health-data`**
   - Permissions → Repository → **Contents: Read and write** (Metadata: read is added automatically)
   - Expiration: up to 1 year. Put a calendar reminder to renew it.
4. **iPhone** — open the link in Safari → Share → **Add to Home Screen**. Open it from the
   home screen → Review → *Sync to GitHub* → username, `health-data`, `main`, paste token → Connect.
   (The home-screen app and Safari keep separate storage: enter the token in the one you use.)
5. **PCs** — open the same link in your browser and connect the same way.

## Privacy model

- Data is written only to your private repo, through GitHub's API, over HTTPS.
- The token stays in that device's browser storage. It is never in either repo.
- The page loads nothing from third parties (no fonts, no analytics, no CDNs). Its
  Content-Security-Policy only allows network calls to `api.github.com`.
- Anyone who opens the public link sees an empty app with no data.
- Lost a device? GitHub → Settings → Developer settings → revoke that token. Make a new one for the other devices.

## Offline

Entries are saved on the device first and pushed to GitHub when you are online
("N changes waiting" in the header). The app itself also opens offline after the first visit.

## Data format (private repo)

```
data/profile.json        { "schema": 1, "updated": ISO, "profile": {name, birthYear, height, sex, trainPlan, goals} }
data/days/2026.json      { "schema": 1, "year": "2026", "updated": ISO,
                           "days": { "2026-09-27": { "date", "c": {daily check-in}, "m": {metricKey: number} } } }
```

- `c` (check-in): sleep, sleepq, mood, energy, stress, train, cardio, deep, med, steps,
  hard, sun, protein, screens, alcohol, note.
- `m` (measurements): keys match the catalog `M` in `index.html` (e.g. `waist`, `rhr`,
  `cooper`, `squat`, `apob`, `who5`). Derived values (BMI, VO₂max, HOMA-IR…) are computed, never stored.
- Every save is a commit, so git history is a complete audit trail and undo.

## Upgrading safely

- App changes only touch this repo. The data repo is never rewritten by an upgrade.
- New metrics = new keys in `M`; old days simply lack them.
- If the format ever has to change, bump `SCHEMA` and add a step in `migrateFile()`.
  Old files are upgraded when read and written back in the new format on the next save;
  the old version stays in git history.
- Before any upgrade: Review → Export JSON (second backup).
