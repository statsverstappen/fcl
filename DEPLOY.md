# Case Logger — separate, personal deployment

This app is deliberately **separate from the resident transplant-site** so nothing about it
(URL, files, service-worker manifest) appears in the shared repo or shared PWA.

The hosted page contains **no patient data**. Cases live only in your device's browser
storage; data moves between devices only via the app's Backup/Restore JSON files.

## Setup (one time)

1. Create a **new, private** GitHub repository. Use a non-obvious name (e.g. `cl-app`),
   since the GitHub Pages URL itself is technically reachable by anyone who knows it.
2. Upload the contents of this folder to the repo root (`index.html` at top level).
3. Settings → Pages → Deploy from branch → `main` / root.
4. On your iPhone: open `https://<username>.github.io/<repo>/` in Safari →
   Share → **Add to Home Screen** ("Case Log", kidney icon). Works offline after first load.
5. First launch: AirDrop `caselog-seed.json` (or a `caselog-backup-*.json`) to the phone,
   then All Cases → **Restore JSON**.

## Rules

- **Never commit a seed or backup JSON to any repo** — those files contain UNOS IDs/MRNs.
- Treat the phone as the primary copy. Export a backup regularly (header shows "⚠︎ backup"
  after 14 days without one); save backups to a private location (Files/iCloud), not the repo.
- To update the app itself: edit `index.html`, bump `case-log-v1` → `v2` in `sw.js`, re-upload.
  Your data is untouched by app updates (it lives in browser storage, not in the files).

## Even stricter options (if ever wanted)

- Cloudflare Pages + Access (free) puts a real login in front of the URL.
- The app works from any static host; nothing is specific to GitHub Pages.
