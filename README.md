# Vitalico

**Personal Health Intelligence Platform** — a multi-user nutrition and health tracker built around a community food database of 900+ Indian and international foods.

Live app: https://vitalico.higgsfield.app

## Features

- **Serving-first food logging** — log "2 Rotis", "1 Scoop Protein", "1 Glass Milk"; every serving converts to grams/ml internally and nutrition is always calculated from normalized per-100 g values
- **Community food database** — 925+ foods with diet tagging (veg / egg / non-veg) and per-user diet filtering; user additions are universal, edits are personal copy-on-write overrides
- **Universal Add Food form** — one schema, six food types (Homemade, Packaged, Restaurant, Generic, Recipe, Supplement) that show only the relevant fields; unlimited serving definitions per food
- **OCR nutrition label scanner** — photograph a label and the per-100 g values fill in automatically (self-hosted Tesseract, runs in the browser)
- **Daily dashboard** — calorie fuel gauge, macro bars, quick logging, habit toggles, BMI / TDEE / deficit stats
- **Weight, habits, measurements, analytics** — weigh-in trends with goal projection, habit tracker, body-tape measurements with Navy body-fat estimate, weekly reports
- **Multi-user with Google Sign-In** — day-1 onboarding builds a personal plan (Mifflin-St Jeor); every account's data is isolated
- **Admin console** — platform stats, user registry, per-user log inspection, universal food database editing with impact-checked deletes
- **Light + dark themes**, PWA-ready, and a signed Android TWA wrapper

## Stack

| Layer | Technology |
|---|---|
| Framework | React 19 + TanStack Start (SSR) |
| Runtime | Cloudflare Workers |
| Database | Cloudflare D1 (SQLite) — migrations in `app/migrations/` |
| Storage | Cloudflare R2 (label photos) |
| Auth | Google OAuth + session cookies (phone OTP built, behind a flag) |
| OCR | Tesseract.js, self-hosted under `/assets/tesseract` |
| Android | Trusted Web Activity (Bubblewrap), signed APK/AAB in `android/` |

## Repository layout

```
app/
  migrations/          # D1 schema + seed + evolution (0001–0009)
  src/routes/          # file-based routes (dashboard, log, foods, admin, …)
  src/lib/api/         # server functions (auth, data, admin, servings)
  src/lib/             # auth/session core, label scanner, bindings
  src/components/ff/   # design-system components (shell, food panel, charts)
  public/assets/       # hero art, icons, tesseract runtime
android/               # TWA project snapshot, signed AAB, keystore (see warning)
```

## Architecture notes

- **Nutrition engine**: values stored per 100 g / 100 ml only; `food_servings` rows are pure conversion definitions (name → weight). Log entries store resolved grams plus which serving and count, so history survives serving edits.
- **Copy-on-write overrides**: universal food rows are never mutated by user edits — a per-user `food_overrides` row wins for that user only. Admin edits mutate the universal row.
- **Theme**: `data-ff-theme="dark"` attribute on `<html>`, set pre-paint from `localStorage`, with per-theme assets swapped by CSS.

## ⚠️ Security

- `android/vitalico-release.keystore` is the **release signing key**. Keep this repository **private**, or strip the `android/` folder before publishing publicly. The keystore password is not in the repo (stored as a deployment secret).
- Runtime secrets (Google OAuth client secret, keystore password) are injected as Worker secrets — never committed.

## License

Private project. All rights reserved.
