# My Expenses — PWA setup guide

A personal expense tracker that installs like an app on your phone and laptop, syncs data across all your devices in real-time, and works offline.

## What you get

- Home-screen icon on phone (Android + iOS) and laptop (Chrome/Edge on Windows, Safari on Mac)
- Real-time sync between devices — add on phone, see it on laptop instantly
- Works offline, auto-syncs when back online
- Excel (.xlsx) and CSV export
- Monthly breakdown, category breakdown, savings calculation
- 100% free forever

## Part 1 — Host the app on GitHub Pages (10 minutes)

### Step 1.1: Create a GitHub account
If you don't have one, go to [github.com](https://github.com) and sign up. It's free.

### Step 1.2: Create a new repository
1. Click the **+** icon top-right → **New repository**
2. Name it `expenses` (or anything you like)
3. Set it to **Public**
4. Check **Add a README file**
5. Click **Create repository**

### Step 1.3: Upload the app files
1. In your new repo, click **Add file** → **Upload files**
2. Drag and drop ALL 5 files from this package:
   - `index.html`
   - `manifest.json`
   - `sw.js`
   - `icon-192.png`
   - `icon-512.png`
3. Scroll down, click **Commit changes**

### Step 1.4: Enable GitHub Pages
1. In your repo, go to **Settings** (top menu)
2. In the left sidebar, click **Pages**
3. Under "Source", select **Deploy from a branch**
4. Branch: **main**, folder: **/ (root)**
5. Click **Save**
6. Wait ~2 minutes, then refresh. You'll see a URL like:
   `https://YOUR_USERNAME.github.io/expenses/`

**Your app is now live!** Open that URL on your phone and laptop.

## Part 2 — Enable cloud sync with Firebase (10 minutes)

Skip this part if you're okay with data being stored separately on each device. With Firebase, your phone and laptop share the same data in real-time.

### Step 2.1: Create a Firebase project
1. Go to [console.firebase.google.com](https://console.firebase.google.com)
2. Sign in with your Google account
3. Click **Add project**
4. Project name: `my-expenses` (or whatever)
5. Disable Google Analytics (not needed), click **Create project**
6. Wait ~30 seconds, click **Continue**

### Step 2.2: Create a Firestore database
1. In the left sidebar, click **Build → Firestore Database**
2. Click **Create database**
3. Choose location closest to you (e.g. `asia-southeast1` for Cambodia)
4. Start in **test mode** for now, click **Create**

### Step 2.3: Set security rules
1. Click the **Rules** tab at the top of Firestore
2. Replace the rules with this (open access — fine for personal use since nobody has your project ID):

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /{document=**} {
      allow read, write: if true;
    }
  }
}
```

3. Click **Publish**

> **Security note**: Your data is protected by obscurity — anyone would need your exact Firebase config (project ID + API key) to access it. For a personal tracker, this is fine. If you want stronger security, I can help you add email/password auth later.

### Step 2.4: Get your Firebase config
1. Click the gear icon top-left → **Project settings**
2. Scroll down to **Your apps**
3. Click the `</>` (web) icon
4. Nickname: `Expenses web`, **do NOT** check Firebase Hosting
5. Click **Register app**
6. You'll see a config object like:

```javascript
const firebaseConfig = {
  apiKey: "AIzaSy...",
  authDomain: "my-expenses-xxxxx.firebaseapp.com",
  projectId: "my-expenses-xxxxx",
  storageBucket: "...",
  messagingSenderId: "...",
  appId: "..."
};
```

7. Copy everything inside the curly braces `{ ... }` and convert it to valid JSON (add quotes around keys). Example:

```json
{
  "apiKey": "AIzaSy...",
  "authDomain": "my-expenses-xxxxx.firebaseapp.com",
  "projectId": "my-expenses-xxxxx",
  "storageBucket": "...",
  "messagingSenderId": "...",
  "appId": "..."
}
```

### Step 2.5: Paste it into the app
1. Open your app URL on any device
2. Click the **Cloud sync** button
3. Paste the JSON config
4. Click **Save & connect**
5. The status dot top-right turns green — you're synced!

Repeat Step 2.5 on each device (phone + laptop). Use the exact same config.

## Part 3 — Install as an app

### On Android (Chrome):
1. Open your app URL
2. Tap the three-dot menu → **Install app** or **Add to Home screen**
3. Done — icon appears on home screen, launches fullscreen

### On iPhone (Safari):
1. Open your app URL in Safari
2. Tap the Share button (square with arrow)
3. Scroll down → **Add to Home Screen**
4. Done

### On laptop (Chrome/Edge):
1. Open your app URL
2. Look for the install icon in the address bar (usually a small + or monitor icon)
3. Click → **Install**
4. Appears in your Start menu / Applications

## Usage

- **Add an entry**: tap the blue **+** button
- **Delete**: tap the × on the right of any entry
- **Export to Excel**: tap **Export Excel** — downloads a .xlsx with 2 sheets (entries + monthly summary)
- **View monthly/category breakdowns**: scroll down on the main screen

## Updating the app

Edit files on GitHub → push changes → GitHub Pages redeploys automatically within ~1 minute. Your installed apps update themselves next time you open them.

## Troubleshooting

- **Sync dot stays gray**: no Firebase config saved yet, or you skipped it
- **Sync dot red**: config invalid, or Firestore rules too strict — check Step 2.3
- **Can't install on iPhone**: must use Safari, not Chrome
- **Data missing on new device**: log into the Cloud sync panel and paste the same Firebase config
