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

## Login & Admin approval
The app now uses **Firebase Authentication** with an approval workflow:
- Anyone can **Sign Up** (name, email, password, requested role). The **very first person ever to sign up is auto-approved as Admin**.
- Everyone after that lands on a "Waiting for Admin Approval" screen until an Admin approves them.
- Admin approves/rejects people from **More → User Approvals**, and can change anyone's role or deactivate them later from the same screen.

## Data & backup
All Labour, Contractor, Project and Attendance data now lives in **Firebase Firestore** (a free cloud database), not just on one phone. That means:
- Every approved user, on any phone, sees the same live data — mark attendance on one phone, it appears on another instantly.
- An internet connection is needed to sync; Firestore caches recently-seen data for brief offline viewing, but new entries need connectivity to save.

Under **Settings**:
- **Backup** downloads a JSON file with everything — keep a copy from time to time.
- **Restore** replaces the cloud data for everyone from a backup file (asks for confirmation).
- **Clear All Data** wipes everything for everyone (asks for confirmation twice) — use with care since this now affects every device.

### Important: tighten Firestore security rules
The Firestore database was created in **test mode**, which allows open read/write for 30 days and then locks everyone out. Before that happens (or sooner, for real security), go to **Firebase Console → Firestore Database → Rules** and replace the default rules with something like:
```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /users/{userId} {
      allow read: if request.auth != null;
      allow create: if request.auth != null && request.auth.uid == userId;
      allow update, delete: if request.auth != null &&
        get(/databases/$(database)/documents/users/$(request.auth.uid)).data.role == 'Admin';
    }
    match /{collection}/{docId} {
      allow read, write: if request.auth != null &&
        get(/databases/$(database)/documents/users/$(request.auth.uid)).data.status == 'approved';
    }
  }
}
```
This restricts all data access to signed-in, Admin-approved users only. Publish the rules after pasting them in.

## What's built
- Labour / Contractor / Project masters with auto-generated IDs (L0001, C001, P001)
- Attendance Entry: search labour by name/ID, auto-filled category, P/A/H/L status stamps, in/out time, OT hours, remarks
- Attendance List: filter by date, project, contractor, category; edit/delete
- Reports: Daily, Monthly, Contractor-wise, Labour-wise, Category-wise — each with Present/Absent/Half/Leave totals
- Export to Excel (.xlsx) and PDF from any list/report
- Search & filters throughout; dark mode toggle
- Tested with 500–1000 dummy labour records for smooth scrolling/search (list pagination + indexed lookups)

## Face Attendance (new)
- **Enroll a face**: Labour Master → edit any labour → under "Face ID" tap **Capture** → allow camera → position the face in frame → **Capture Now**. This stores a face signature (not a photo) against that labour record.
- **Auto-mark attendance**: Dashboard/More → **Face Attendance** → pick Date/Project/Contractor → **Start Camera & Scan**. Point the camera at each worker's face; recognized workers get marked **Present** automatically (once per day). Unrecognized faces are just skipped — no data is stored for them.
- Runs entirely in the browser (no data leaves the phone except the final attendance record); needs decent lighting and one face at a time works best. Accuracy in tough site conditions (dust, helmets, poor light) won't match a real biometric device — treat it as a fast convenience tool, and Attendance Entry/List remain available for corrections.

## Play Store (optional, next step)
This is a PWA, so it can be wrapped into an installable Android package without rewriting anything:
1. Go to **pwabuilder.com**, enter your live link (`https://sentugrd-star.github.io/construction-labour-attendance/`).
2. Generate the Android package (.aab), download it.
3. Create a Google Play Console developer account (one-time fee), create an app listing, upload the .aab, fill in screenshots/description, submit for review.


