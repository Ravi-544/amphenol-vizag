# AGENTS.md

## Architecture

This project ships one application in two shells:

- **Web**: `www/index.html` is the entire app — markup, Tailwind (CDN) styling, and vanilla JS in inline `<script>` tags, all in one file. It's a client-only SPA-style app with tab switching handled by `switchTab()`, no router, no build step. State (users, tasks, attendance logs, plant GPS coordinates) lives in `localStorage` under keys suffixed `Geo500`.
- **Android**: `android/` is a Capacitor-generated native project pointing at `www/` as its web asset directory (`capacitor.config.json`, `webDir: "www"`). It is regenerated/kept in sync via `npx cap sync android` — do not hand-edit files under `android/app/src/main/assets/public` (they're overwritten by sync) or other Capacitor-managed files; only `android/app/src/main/AndroidManifest.xml`-level native config (permissions, app metadata) should be edited directly, then re-verified after a `cap sync`.

## Key directories

- `www/` — source of truth for all UI and logic. Edit here first.
- `android/` — native Android project. Regenerate web assets with `npm run android:sync` after any `www/` change.
- `capacitor.config.json` — app id (`com.amphenol.exathermometrics.attendance`), app name, web dir.
- `netlify.toml` — publishes `www/` as the site's build output.

## Conventions / non-obvious decisions

- No backend, no database: this app was built as a fully client-side tool (per the original single-file HTML it was converted from), so all persistence is `localStorage`. If real multi-device sync, shared task boards, or durable attendance records are needed, that requires introducing a real backend (e.g. Netlify Functions + Netlify Database) and is a deliberate scope decision, not an oversight.
- Geolocation permissions (`ACCESS_FINE_LOCATION`, `ACCESS_COARSE_LOCATION`) were added manually to `AndroidManifest.xml` because Capacitor's plugin installer does not inject manifest permissions automatically — re-check this file after any `cap add`/`cap sync` regeneration.
- There is no CI/local tooling here that can compile the `.apk` (no Android SDK/Gradle in this environment) — the `android/` project is meant to be opened in Android Studio on a developer machine to produce the actual installable artifact.
- The web build (`www/`) also carries a `manifest.json` + `service-worker.js` so the same code works as an installable, offline-capable PWA independent of the native Android wrapper.
