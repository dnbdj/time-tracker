# Time Tracker — APK download site

Static GitHub Pages site. No build step. Drop your APK in and enable Pages.

## 1. Add your APK (versioned — keeps every version downloadable)

Copy the built APK with its version number, and refresh the `time-tracker.apk`
stable link that the Download button points at:

```powershell
Copy-Item C:\dev\time-tracker-android\app\build\outputs\apk\debug\app-debug.apk `
  C:\dev\apk-site\apk\time-tracker-1.2.0.apk -Force
Copy-Item C:\dev\apk-site\apk\time-tracker-1.2.0.apk `
  C:\dev\apk-site\apk\time-tracker.apk -Force
```

Keep each file under 100 MB (GitHub file limit).

## 2. Update the version

In `index.html`, bump:

```js
const APP_VERSION = "v1.0.0";
```

## 3. Push as its own repo and enable Pages

```powershell
cd C:\dev\apk-site
git init
git add .
git commit -m "Time Tracker download page"
git branch -M main
git remote add origin https://github.com/<you>/time-tracker-downloads.git
git push -u origin main
```

Then on GitHub: **Settings → Pages → Source: GitHub Actions**.
The included workflow (`.github/workflows/pages.yml`) deploys on every push to `main`.
Your URL will be `https://<you>.github.io/time-tracker-downloads/`.

## 4. Test locally

Just open `index.html` in a browser, or:

```powershell
npx serve C:\dev\apk-site
```

## Note: Releases vs Pages

GitHub Pages is fine for a small APK, but the recommended Android distribution is
**GitHub Releases** (upload the APK as a release asset, link to it from this page).
If your APK grows past ~50 MB, switch to that and change the Download button URL.
