# Personal Dashboard — Web App Deployment Guide

Your dashboard is now a installable **Progressive Web App (PWA)**: a normal
website that can also be "installed" to a phone's home screen or a
desktop dock, runs full-screen with no browser chrome, works offline after
the first load, and has its own icon. This guide covers deploying it on
GitHub Pages (same approach as your Net Worth Planner) and installing it
on your devices.

## What's in this package

```
dashboard-web/
├── index.html              # The app itself
├── manifest.webmanifest    # PWA metadata (name, icons, colors)
├── service-worker.js       # Offline caching
├── tailwind.css            # Compiled styles (no external CDN dependency)
└── icons/                  # App icon in all required sizes
    ├── icon-16.png ... icon-512.png
    ├── icon-maskable-192.png / icon-maskable-512.png   (Android adaptive icon)
    ├── apple-touch-icon.png                            (iOS home screen)
    └── favicon-16.png / favicon-32.png
```

All paths inside these files are **relative** (`./icons/...`, not
`/icons/...`), so this works whether it's hosted at the root of a domain
or in a GitHub Pages subfolder like `username.github.io/dashboard/`.

---

## Deploy to GitHub Pages

### 1. Create the repository

1. Go to [github.com/new](https://github.com/new)
2. Name it something like `personal-dashboard`
3. Keep it **Public** (required for free GitHub Pages) or Private if you
   have GitHub Pro/Team (Pages works on private repos there too)
4. Create the repo

### 2. Upload the files

**Easiest way (browser, no git needed):**
1. Open your new repo → click **"Add file" → "Upload files"**
2. Drag in `index.html`, `manifest.webmanifest`, `service-worker.js`,
   `tailwind.css`, and the whole `icons/` folder
3. Commit directly to `main`

**Or with git, from your Mac terminal:**
```bash
cd dashboard-web
git init
git add .
git commit -m "Initial dashboard deploy"
git branch -M main
git remote add origin https://github.com/YOUR-USERNAME/personal-dashboard.git
git push -u origin main
```

### 3. Enable GitHub Pages

1. In the repo, go to **Settings → Pages**
2. Under "Build and deployment" → Source: **Deploy from a branch**
3. Branch: **main**, folder: **/ (root)** → Save
4. Wait ~1 minute, then GitHub shows your live URL:
   ```
   https://YOUR-USERNAME.github.io/personal-dashboard/
   ```

That's it — the app is live, served over HTTPS (required for installability
and the service worker to work).

### 4. Updating it later

Any time you want to change the app, just edit `index.html` (or the other
files) and push/upload again — GitHub Pages redeploys automatically within
a minute. If you change `index.html`, bump the version number in
`service-worker.js`:
```js
const CACHE_NAME = 'personal-dashboard-v2';  // was v1
```
This makes the service worker discard its old cache and fetch the new
version. Without this bump, installed/offline copies may keep showing the
old version until the cache naturally expires.

---

## Install it on your devices

Once it's live at your GitHub Pages URL:

### iPhone / iPad (Safari)
1. Open the URL in **Safari** (must be Safari, not Chrome — iOS only
   allows Add to Home Screen from Safari)
2. Tap the **Share** icon (square with an arrow) in the toolbar
3. Scroll down, tap **"Add to Home Screen"**
4. Tap **Add** — the icon appears on your home screen and opens full-screen,
   no address bar
- The app also shows a banner with this same instruction the first time you
  visit in Safari.

### Android (Chrome)
1. Open the URL in Chrome
2. You'll see an **"Install"** banner at the top (or tap the **⋮** menu →
   **"Install app"** / **"Add to Home screen"**)
3. Confirm — it installs like a native app, with its own icon and app-switcher entry

### Desktop (Mac/Windows — Chrome or Edge)
1. Open the URL
2. Click the **install icon** in the address bar (a monitor-with-arrow icon),
   or the in-app "Install" banner
3. It opens as its own app window, and can be pinned to the Dock/Taskbar

### Desktop (Safari)
Safari on macOS doesn't support installable web apps the same way; use
Chrome or Edge for the installed-app experience, or just keep it as a
regular browser tab/bookmark.

---

## How the offline support works

- The **service worker** caches the app shell (HTML, CSS, icons) the first
  time you visit.
- After that, the app opens instantly and works with **no internet
  connection** — it's a local dashboard that happens to be reachable at a
  URL.
- Your task data itself lives in the browser's local storage on each
  device (same as before) — install on multiple devices and use CSV
  export/import or the Google Drive sync option to keep them in sync.

## Verifying installability (optional)

If you want to double check everything is set up correctly:
1. Open the deployed URL in Chrome
2. Open DevTools (Cmd+Option+I) → **Application** tab → **Manifest** —
   confirms the icons and metadata are loading correctly
3. Or run a Lighthouse audit (DevTools → Lighthouse → check "Progressive
   Web App") for an installability score

## Custom domain (optional)

If you'd rather use something like `dashboard.yourdomain.com`:
1. Add a `CNAME` file to the repo root containing just that domain name
2. Add a DNS CNAME record pointing that subdomain at
   `YOUR-USERNAME.github.io`
3. Set it in **Settings → Pages → Custom domain**

Not necessary — the default `github.io` URL works perfectly fine for
personal use.
