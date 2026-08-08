# Grassroots FC — App Store Connect prep

Draft materials for the App Store Connect listing. Everything here is a
starting point for you to edit — nothing has been submitted anywhere.

## 1. App icon

- `app-icon-1024.png` in this folder — 1024×1024, no alpha, no rounded
  corners (Apple applies the mask). Use this for the "App Store" icon slot
  in App Store Connect.
- The same source (`assets/logo.png`) was already run through
  `@capacitor/assets` and is wired into the Xcode project at
  `ios/App/App/Assets.xcassets/AppIcon.appiconset/` — nothing more to do
  for the in-app icon.
- This is a placeholder crest (gold shield + ball on pitch-green). Swap
  `assets/logo.png` for real branding whenever you're ready and re-run:
  ```
  npx @capacitor/assets generate --ios --iconBackgroundColor '#0F2E1D' \
    --iconBackgroundColorDark '#0F2E1D' --splashBackgroundColor '#0F2E1D' \
    --splashBackgroundColorDark '#0F2E1D'
  npx cap sync ios
  ```

## 2. Screenshots

Apple requires at least one screenshot set sized for the largest iPhone
display you support; App Store Connect will scale it down for other
display sizes if you don't upload every size separately. As of this
writing the required set is:

| Display | Resolution (px) | Devices |
|---|---|---|
| 6.9" / 6.7" iPhone | 1290 × 2796 or 1284 × 2778 | iPhone 15/16 Pro Max, 14/15 Plus |
| 6.5" iPhone (optional, legacy) | 1242 × 2688 | iPhone 11 Pro Max, XS Max |
| iPad Pro 13" (if you support iPad) | 2048 × 2732 | iPad Pro 12.9"/13" |

**Double-check exact required sizes in App Store Connect at submission
time** — Apple adjusts this list as new devices ship, and this table may
be stale by the time you submit.

`screenshots/` in this folder contains six draft captures at 1284×2778
(6.5"/6.7" class), taken from the live game running headless:

1. `01-dashboard.png` — home/dashboard screen
2. `02-stadium.png` — Stadium tab
3. `03-campus.png` — Campus tab
4. `04-squad.png` — Squad screen
5. `05-match.png` — live match in progress
6. `06-fulltime.png` — full-time result

**Known limitation to fix before these are marketing-ready:** the game's
layout is a centered, fixed-width column, so at the full 1284×2778 iPhone
canvas there's a lot of empty dark space below the content (visible in
every capture above). These are functionally accurate — this is what the
app actually looks like today on a tall phone screen — but you'll likely
want either (a) a responsive layout pass so screens fill the display, or
(b) to treat these as functional references and have a designer composite
nicer marketing screenshots (device frame + captions) before launch. Either
is a bigger job than this pass covers.

## 3. Draft description

**App name:** Grassroots FC

**Subtitle** (30 chars max): `Build a club from the bottom`

**Promotional text** (170 chars max, editable without review):
> Take a non-league side from Division Two to the top. Run transfers,
> tactics, and a growing training campus — all offline, all in your pocket.

**Description:**

> Grassroots FC is a football club management game about building
> something from nothing.
>
> Start in the lower leagues with a threadbare squad and a stadium that's
> barely more than a fence around a pitch. Win matches, sign and develop
> players, and reinvest every pound into your club — a training pitch that
> speeds up development, a youth academy that graduates its own talent, a
> medical center that keeps your squad on the pitch, and a stadium that
> grows from open terracing to a full modern bowl over 17 distinct tiers.
>
> — Manage transfers, tactics, and a starting XI every matchday
> — Watch matches play out with a live commentary ticker
> — Grow an independent campus of upgradeable facilities — training,
>   academy, medical, retail, parking, and more — each with its own
>   payoff
> — Take your stadium through a full physical rebuild, tier by tier
> — Play entirely offline — your save lives on your device, nothing is
>   sent anywhere
>
> No ads, no in-app purchases, no account required. Just building a club.

**Keywords** (100 chars max, comma-separated, no spaces needed but counted):
`football,soccer,club,manager,management,sim,career,stadium,youth academy,tactics`

**Category:** Games → Sports (secondary: Simulation)

**Age rating:** No objectionable content — should qualify for 4+.

**Support URL / Marketing URL / Privacy Policy URL:** placeholders — you'll
need to supply real URLs before submission (App Store Connect requires a
privacy policy URL even for apps that collect nothing).

## 4. App Privacy ("privacy nutrition label") checklist

Based on what's actually in the code today:

- Save data (`GameStorage`) is written to on-device storage only —
  `@capacitor/filesystem` in the native app, `localStorage` in a plain
  browser. There is no server, no API call, no analytics SDK, and no
  network request anywhere in `www/index.html`.
- Local notifications (`@capacitor/local-notifications`) are scheduled
  and read entirely on-device; nothing about them leaves the device.
- There is no account creation, no login, no ad SDK, no crash reporter,
  no third-party analytics.

Given that, the App Store Connect "App Privacy" questionnaire should be
answerable as:

- **Data Collection: "Data Not Collected."** You aren't collecting any
  data from this app, on Apple's terms — nothing is transmitted off the
  device by the app itself.
- If Apple's own tooling (App Store Connect, TestFlight crash logs, Xcode
  Organizer) collects anything on your behalf as the developer, that's
  Apple's own collection, not something you declare for this app's data
  types.
- If you later add anything that calls out to a server — an analytics
  SDK, a remote leaderboard, crash reporting — revisit this before your
  next submission; the questionnaire needs to change the moment any
  network call is added.

This is a draft based on reading the current source, not legal advice —
worth a final read-through against the actual shipped build before you
submit, especially if anything changes between now and your release.
