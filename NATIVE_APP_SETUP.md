# Grassroots FC — native iOS build (manual steps)

This repo can now be built as a native iOS app via Capacitor. Everything
that can be done without macOS/Xcode has been done here in the sandbox;
the remaining steps need a Mac.

## What's already in the repo

- `www/index.html` — the game itself, unchanged apart from the storage
  and native-integration work described below. This is the single
  source of truth; there is no separate build step.
- `package.json` / `package-lock.json` — Capacitor CLI + plugins
  (`@capacitor/filesystem`, `@capacitor/local-notifications`,
  `@capacitor/haptics`, `@capacitor/status-bar`, `@capacitor/splash-screen`,
  `@capacitor/app`).
- `capacitor.config.json` — app id, name, colors, plugin config.
- `ios/App/` — the generated Xcode project (`App.xcodeproj` /
  `App.xcworkspace`), already synced with the current `www/` build and
  the generated app icon/splash.
- `assets/logo.png` — the 1024×1024 source icon (placeholder crest).
- `app-store-assets/` — draft screenshots, description, and privacy
  checklist for App Store Connect.

## What changed under the hood (for context)

- The save system no longer uses `window.storage.*` (a Claude.ai-only
  API). It now goes through a `GameStorage` wrapper
  (`www/index.html`, search for `const GameStorage`) that uses
  `@capacitor/filesystem` on-device in the native app, and falls back to
  `localStorage` when running in a plain browser (so nothing about local
  testing changed). The save key changed from `snowfc-save-v1` to
  `grassrootsfc-save-v1` — old saves under the previous game's name
  won't carry over, which is expected since this is a rename.
- Native touches: haptics on key actions (kickoff, goals, full-time,
  stadium/campus upgrades), status bar + safe-area handling for the
  notch/home indicator, and two local notification triggers —
  "lineup needs attention" and "pending transfer offers" — scheduled a
  few hours after the app is backgrounded and cancelled when it's
  reopened, so they never pile up or go stale.

## Steps to run on your Mac

1. **Install CocoaPods and Xcode** if you don't already have them
   (Xcode from the App Store; CocoaPods via `sudo gem install cocoapods`
   or `brew install cocoapods`).
2. **Clone this branch and install JS deps:**
   ```
   npm install
   ```
3. **Finish the iOS native dependency install** (this was skipped in the
   sandbox because CocoaPods isn't available there):
   ```
   cd ios/App
   pod install
   cd ../..
   ```
4. **Open the workspace (not the .xcodeproj):**
   ```
   npx cap open ios
   ```
   or open `ios/App/App.xcworkspace` directly in Xcode.
5. **Set your own bundle identifier.** The project currently uses the
   placeholder `com.grassrootsfc.app` (set in `capacitor.config.json` and
   the Xcode project). Change it to something under a domain/team you
   actually control before you archive/submit — in Xcode: select the
   `App` target → **Signing & Capabilities** → update the Bundle
   Identifier, and update `capacitor.config.json`'s `appId` to match, then
   run `npx cap sync ios` again.
6. **Pick your Team** under Signing & Capabilities (requires your Apple
   Developer account, which you're handling separately).
7. **Build and run** on a Simulator first, then on a real device once
   that's working.
8. **After any change to `www/index.html`**, re-sync before rebuilding
   in Xcode:
   ```
   npx cap sync ios
   ```

## Before you submit

- Swap `assets/logo.png` for real branding if you want something other
  than the placeholder crest, then re-run:
  ```
  npx @capacitor/assets generate --ios --iconBackgroundColor '#0F2E1D' \
    --iconBackgroundColorDark '#0F2E1D' --splashBackgroundColor '#0F2E1D' \
    --splashBackgroundColorDark '#0F2E1D'
  npx cap sync ios
  ```
- Read `app-store-assets/APP_STORE_CONNECT.md` — draft description,
  keywords, and privacy-questionnaire answers, plus a note on the
  screenshots' current limitation (a lot of empty space on tall iPhone
  screens, since the game's layout is a fixed-width centered column).
- You'll need a privacy policy URL and support URL for App Store Connect
  even though the app collects no data — those aren't generated here.
