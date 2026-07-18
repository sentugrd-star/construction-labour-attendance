# Construction Labour Attendance

A complete, offline-first site attendance app — Labour Master, Contractor Master, Project Master, daily Attendance Entry, Attendance List, and five report types with Excel/PDF export. Built as a single web app (no server needed) so it installs on Android like a native app.

## Files
- `index.html` — the entire app (HTML + CSS + JS)
- `manifest.json`, `sw.js`, `icon.svg` — make it installable/offline (PWA)
- `README.md` — this file

## How to put it on your phone (2 minutes)
**Easiest — no hosting needed:**
1. Copy the whole `ConstructionLabourAttendance` folder to your phone (Google Drive, WhatsApp-to-self, USB, etc.).
2. Open `index.html` in Chrome on the phone.
3. Chrome menu (⋮) → **Add to Home screen**. It now opens full-screen with its own icon, like an app.

**Best — installable PWA with offline mode:**
Host the folder anywhere free and static (GitHub Pages, Netlify, Vercel, Firebase Hosting). Once served over `https://`, Chrome will offer **Install app**, and the service worker (`sw.js`) caches it for offline use.

> Note: because there's no Android Studio / network build environment available here, this is delivered as an installable web app rather than a compiled `.apk`. It looks and behaves like a native app on Android (full-screen, home-screen icon, offline-capable) and is the fastest path to something you can use on-site today. If you later want a true `.apk`, this same code drops into a WebView wrapper (e.g. via Android Studio or Capacitor) with no changes.

## Roles
Pick **Admin**, **Site Engineer**, or **Supervisor** at login:
- **Admin** — everything, including deleting master records and Settings.
- **Site Engineer** — attendance entry, masters (add/edit), reports, export. No delete or settings.
- **Supervisor** — attendance entry and viewing reports only.

## Data & backup
All data is stored locally on the device (in the browser's storage), so it works fully offline and nothing leaves the phone. Under **Settings**:
- **Backup** downloads a JSON file with everything — keep a copy after big data-entry sessions.
- **Restore** loads a backup back in (e.g. onto a new phone).
- **Clear All Data** wipes everything (asks for confirmation twice).

Because data lives in the browser, don't clear Chrome's site data/storage for this app, and use the same browser each time.

## What's built
- Labour / Contractor / Project masters with auto-generated IDs (L0001, C001, P001)
- Attendance Entry: search labour by name/ID, auto-filled category, P/A/H/L status stamps, in/out time, OT hours, remarks
- Attendance List: filter by date, project, contractor, category; edit/delete
- Reports: Daily, Monthly, Contractor-wise, Labour-wise, Category-wise — each with Present/Absent/Half/Leave totals
- Export to Excel (.xlsx) and PDF from any list/report
- Search & filters throughout; dark mode toggle
- Tested with 500–1000 dummy labour records for smooth scrolling/search (list pagination + indexed lookups)

## Marked "Future Features" (not built — flagged in-app under Settings)
QR code attendance, fingerprint attendance, face recognition, GPS location tagging, offline auto-sync, cloud backup, WhatsApp report sharing. These typically need native device APIs or a backend server, which is why they're future phases.
