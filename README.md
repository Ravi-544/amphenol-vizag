# Amphenol Attendance Portal

A geofenced attendance and task-management portal for Amphenol Exa Thermometrics, available both as a website and as a native Android app.

## What this is

The core application is a single-page app (`www/index.html`) covering:

- Secure login (manager / employee roles)
- Home dashboard with live metrics
- Task management board
- Location-based (geofenced) attendance check-in using device GPS
- User/credential creation
- Employee shift tasks
- Monthly calendar view

All application state (users, tasks, attendance logs, plant location) is stored in the browser's `localStorage` — there is no backend server for this app.

## Key technologies

- Plain HTML/CSS/JS, styled with Tailwind CSS (via CDN)
- Browser Geolocation API for GPS-based attendance
- PWA support (`www/manifest.json`, `www/service-worker.js`) so the site is installable and works offline
- [Capacitor](https://capacitorjs.com) (`android/` folder) to wrap the same web app into a native Android project
- `@capacitor/geolocation` plugin so GPS access works reliably inside the native Android WebView

## Running the website locally

Just open `www/index.html` in a browser, or serve the folder with any static file server:

```bash
npx serve www
```

When deployed to Netlify, the `www` folder is published as-is (see `netlify.toml`).

## Building the Android app (.apk)

This repository already contains a generated native Android project (`android/`) wired to the same web app. Turning it into an installable `.apk` requires Android build tooling that isn't available in this build environment (no Android SDK / Gradle / Android Studio here), so the final compile step has to happen on your machine:

1. Install [Android Studio](https://developer.android.com/studio) (includes the Android SDK and Gradle).
2. Install dependencies once: `npm install`.
3. Whenever you change files in `www/`, run `npm run android:sync` to copy them into the native project.
4. Open the project: `npm run android:open` (or open the `android/` folder directly in Android Studio).
5. In Android Studio, use **Build > Build Bundle(s) / APK(s) > Build APK(s)** to produce a debug `.apk`, or **Build > Generate Signed Bundle / APK** for a signed release build.
6. Install the resulting APK on an Android device/emulator, or publish it to the Play Store.

The app requests fine/coarse location permission on first use (required for the geofenced attendance feature).

## Project structure

```
www/                the actual web app (source of truth for all UI/logic)
android/            generated native Android (Capacitor) project — do not hand-edit generated files
capacitor.config.json   Capacitor configuration (app id, name, web dir)
netlify.toml         tells Netlify to publish the www folder
```
