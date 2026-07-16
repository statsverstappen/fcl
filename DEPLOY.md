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

## Option B (recommended): Cloudflare Pages + Access — real login, free

Everything above about GitHub Pages works, but Cloudflare adds an actual email login
in front of the URL at no cost. No credit card; the Zero Trust free plan covers up to 50 users.

### Deploy (one time, ~15 min)

1. Create a free account at dash.cloudflare.com.
2. **Workers & Pages → Create → Pages → Upload assets** (Direct Upload — no git needed).
   Project name = the URL, so pick something non-obvious (e.g. `cl-app` → `cl-app.pages.dev`).
   Drag the contents of this folder in. Site is live immediately.
   (Alternative: connect a *private* GitHub repo — free on Cloudflare, unlike GitHub Pages.)
3. First visit to **Zero Trust** in the dashboard: pick any team name, choose the **Free** plan.

### Put the login in front of it

4. In your Pages project: **Settings → Enable access policy**. This alone only protects
   preview URLs — continue:
5. Select **Manage** on that policy → under **Access → Applications**, select the app →
   **Configure** → under **Public hostname**, delete the wildcard `*` from the Subdomain
   field (rename the application if it complains) → **Save**. Now `cl-app.pages.dev`
   itself is behind Access.
6. Go back to Pages **Settings → General → Enable access policy** again so preview URLs
   are re-covered (you should end with two Access applications).
7. In the Access application's policy, confirm the rule is **Allow → Emails →
   jonathanmitchell5@me.com** (it defaults to your login email).
8. In the application settings, set **Session Duration** to the maximum (1 month) so the
   phone app doesn't ask you to log in constantly.

### Phone

9. Open `https://cl-app.pages.dev` in Safari → enter your email → type the 6-digit code
   it emails you → Share → **Add to Home Screen** → restore your backup JSON.
10. Roughly monthly, the app will show the login screen again — same email + code.
    Offline use keeps working regardless (the app is cached on-device).

### Updating the app later

Re-upload via **Deployments → Create deployment** (drag the folder again) after bumping
`case-log-v1` → `v2` in `sw.js`. Your case data is never touched by app updates.

## Onboarding other fellows

The app is inherently multi-user-safe: each person's cases live only in their own
device's browser storage. Nothing is shared between users and nothing touches a server.

1. Add their email to the Access policy: **Zero Trust → Access → Applications →
   (your app) → Policies → include → Emails** — add each fellow's email
   (or switch the rule to "Emails ending in @<your-institution>.edu" to cover everyone).
   The free plan covers 50 users.
2. Send them the URL. They log in with their email + one-time code, Add to Home Screen.
3. They bootstrap their history with **All Cases → Import portal CSV**: in the TAC portal
   they export their surgical log (an .xlsx), open it in Excel, Save As → CSV, and import
   that file. Rows arrive marked "entered", duplicates are skipped, and re-importing later
   only adds new rows. From then on they log new cases in the app.

Each fellow should make their own JSON backups. There is no shared dashboard — if the
program ever wants one, that would need a small server component (by design, it doesn't exist).

## Notes on what Access does and doesn't change

- Access gates who can *load the page*. Your case data still lives only in the device's
  browser storage and never passes through Cloudflare or any repo.
- The app works from any static host; nothing here is specific to GitHub or Cloudflare.
