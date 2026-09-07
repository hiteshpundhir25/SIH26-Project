# Sahakari Seva (सहकारी सेवा / সহকারী সেবা / சகாரி சேவா / సహకారి సేవ / सहकारी सेवा / સહકારી સેવા / ಸಹಕಾರಿ ಸೇವಾ)

> **India's First Worker-Owned Cooperative Platform for Urban & Household Gig Services** — a **mobile-first application** (Expo SDK 57 / React Native) that runs on **Expo Go** for Android & iOS.

> **📘 Full project context first:** read [`COMPLETE_PROJECT_CONTEXT.md`](COMPLETE_PROJECT_CONTEXT.md) for the complete picture of the whole product (architecture, API, database, ML, mobile, design system, QA) as one continuous document. This `README.md` is intentionally kept **separate** as a short quick-start overview — the two files are never merged. Original guides stay untouched in [`docs/`](docs/).

---

## 🌟 Key Highlights

- **📱 True Mobile Application** — Expo SDK 57 / React Native app (single project in this folder, no nested copies) with role-based bottom tabs (Customer, Worker, Admin), touch-first UI, native safe areas, GPS matching, and offline demo fallback so *every* screen works even without the backend.
- **🗣 8-Language Simultaneous Localization** — English + **7 Indian languages** (हिन्दी Hindi, বাংলা Bengali, தமிழ் Tamil, తెలుగు Telugu, मराठी Marathi, ગુજરાતી Gujarati, ಕನ್ನಡ Kannada). Switching languages swaps **the entire app at once** — tabs, screens, alerts, invoices, admin dashboards — behind a smooth branded cross-fade with **zero glitches**. Your choice persists across restarts.
- **✨ Unique Classy Animations Everywhere** — reusable animation system (`FadeInView`, `ScalePressable`, `AnimatedNumber`, `PulseView`): staggered entrance sequences on every screen, springy haptic press feedback, count-up earnings/KPI numbers, gentle pulsing emergency banner, and cross-fade screen transitions.
- **🤖 AI Demand Forecasting & Workforce Allocation** — Ensemble time-series model (weekend surge ×1.55, OLS trend regression, 95% confidence bounds) plus real-time supply–demand balancing with 1-tap standby worker mobilization.
- **📍 Zero Paid Map APIs** — 100% open-source OpenStreetMap + Haversine geo-matching. Zero Google Maps / Mapbox fees.
- **💰 Fair-Wage Cooperative Economics** — 85% direct to the worker, 10% to the social-security & welfare corpus, 5% cooperative operations — transparent on every booking and invoice.
- **🛡 Cooperative Governance** — worker KYC verification queue, ITI/NSDC certification checks, welfare passbook with Ayushman Bharat + PMSBY integration.

---

## 🚀 Quick Start (Expo Go — Android)

### 1. Install Expo Go on your Android phone
Search **"Expo Go"** in the Google Play Store and install it.

### 2. Install dependencies
```bash
npm install
```

### 3. Start the app (run this from the project root — the only Expo project is here)
```bash
npm start
```

### 4. Connect
Put your phone and computer on the **same Wi-Fi**, open **Expo Go**, tap **"Scan QR code"**, and scan the QR shown in the terminal. The app bundles and opens in seconds. No Android Studio, no APK, no build required.

> **Note:** Expo Go supports the latest Expo SDK only — this project pins **Expo SDK 57**, which is the current version. If a QR/LAN connection fails, start with `npx expo start --tunnel` or set `EXPO_PUBLIC_API_URL=http://<computer-ip>:5001` to reach a live backend.

### 5. Optional — Live Backend API (port 5001)
The backend is a separate repository. When it is running, point the app at it with `EXPO_PUBLIC_API_URL=http://<computer-ip>:5001` before `npm start`. Without a backend the app automatically falls back to built-in demo data — every feature still works.

---

## 🌐 Language Switching (7 Indian Languages + English)

| Language | Native | Script |
|---|---|---|
| English | English | Latin |
| Hindi | हिन्दी | Devanagari |
| Bengali | বাংলা | Bengali |
| Tamil | தமிழ் | Tamil |
| Telugu | తెలుగు | Telugu |
| Marathi | मराठी | Devanagari |
| Gujarati | ગુજરાતી | Gujarati |
| Kannada | ಕನ್ನಡ | Kannada |

Tap the **🌐 chip** in any header to open the language picker. The switch is
orchestrated by `LanguageSwitchProvider` (`src/animations/`) — a
branded overlay cross-fades in (140 ms), i18next swaps all ~300 UI strings in a
single render, then the overlay fades out (280 ms). **No text jumps, no
partial translation, ever.** The selected language is saved with
AsyncStorage and restored on launch.

---

## 📱 Mobile App Screens & Flows

| Role | Screens & Capabilities |
|---|---|
| **Customer** | **Home**: animated category grid, pulsing emergency trigger, nearby highlights, 85/10/5 fair-split banner.<br>**Search**: trade chips (localized), rating & emergency filters, Haversine ranking.<br>**Map**: fullscreen OpenStreetMap with radius perimeter and worker pins.<br>**Detail**: worker background, verified ITI certificates, reviews, cooperative affiliation.<br>**Booking**: transparent wage-split breakdown, emergency dispatch toggle, instant confirmation.<br>**Invoice**: official cooperative tax receipt with 85/10/5 distribution & share. |
| **Worker** | **Dashboard**: count-up direct earnings, welfare corpus, live availability toggle.<br>**Jobs**: active task lifecycle (Accept → Start → Complete) with emergency badges.<br>**Welfare**: social-security passbook (Ayushman Bharat, PMSBY, cooperative pension).<br>**Credentials**: cooperative digital ID card, trade profile editor, certification upload.<br>**GPS & Radius**: live GPS sync, address resolution, service radius picker, privacy notice. |
| **Admin** | **Federation**: KPI grid with animated counters & welfare corpus.<br>**Verify KYC**: pending/verified queue with approve/reject workflows.<br>**AI Forecast**: 7-day demand curve, weekend surge (+55%), confidence bounds, cold-start fallback.<br>**Allocation**: live supply-demand clusters with 1-tap standby mobilization. |

---

## 🧱 Architecture

```
.                 → Expo SDK 57 React Native app — the one and only project (runs on Expo Go)
├─ App.tsx        → root component (SafeAreaProvider + Theme + Language + Navigation)
├─ app.json       → Expo app config (name, icons, permissions, plugins)
├─ src/animations/ → FadeInView, ScalePressable, AnimatedNumber, PulseView,
│                    LanguageSwitchProvider (glitch-free whole-app switching)
├─ src/i18n/      → 8 complete locales (en, hi, bn, ta, te, mr, gu, kn)
├─ src/screens/   → auth / customer / worker / admin role flows
├─ src/components/→ ui, common (Header, modals, WorkerCard), map
├─ src/services/  → ApiClient (auto LAN host detection + offline fallback), GPS
├─ src/theme/     → design tokens (colors, spacing, typography, radii, shadows)
└─ package.json   → scripts (`npm start`, `npm run typecheck`, ...) and dependencies
```

---

## 📚 Checks & Troubleshooting

| Command | Purpose |
|---|---|
| `npm start` | Start the Expo dev server (scan the QR with Expo Go) |
| `npm run web` | Start the app in a browser |
| `npm run typecheck` | TypeScript check (0 errors expected) |
| `npm run doctor` | `expo-doctor` health check (all checks should pass) |

**If Expo Go shows a project/route error:** make sure you started the server from **this folder** (`npm start`) and that no other Expo server is squatting on port 8081 — if one is, start with `npx expo start --port 8082` or stop the other server. This folder intentionally contains exactly **one** Expo project so the dev-server address is never ambiguous.

---

## 📚 Documentation

The complete documentation set lives in [`docs/`](docs/) — all files are preserved as-is. For a single-file overview of the whole product, see [`COMPLETE_PROJECT_CONTEXT.md`](COMPLETE_PROJECT_CONTEXT.md).

| Guide | File |
|---|---|
| **Full project context (all docs merged)** | [`COMPLETE_PROJECT_CONTEXT.md`](COMPLETE_PROJECT_CONTEXT.md) |
| **Run on Android via Expo Go** (QR, Tunnel mode, backend hookup) | [`docs/EXPO_GO_SETUP.md`](docs/EXPO_GO_SETUP.md) |
| **Product architecture (end-to-end)** | [`docs/PROJECT_ARCHITECTURE.md`](docs/PROJECT_ARCHITECTURE.md) · [`docs/FINAL_PRODUCT_ARCHITECTURE.md`](docs/FINAL_PRODUCT_ARCHITECTURE.md) |
| **Mobile app architecture** (navigation, screens, offline resilience) | [`docs/MOBILE_ARCHITECTURE.md`](docs/MOBILE_ARCHITECTURE.md) |
| **REST API reference** | [`docs/API_DOCUMENTATION.md`](docs/API_DOCUMENTATION.md) |
| **Database schema changes & reconciliation** | [`docs/DATABASE_CHANGES.md`](docs/DATABASE_CHANGES.md) · [`docs/DATABASE_RECONCILIATION.md`](docs/DATABASE_RECONCILIATION.md) |
| **AI demand forecasting** (OLS, EMA, surge) | [`docs/ML_FORECASTING.md`](docs/ML_FORECASTING.md) + [`AI_Demand_Forecasting_Engine_Brief.pdf`](docs/AI_Demand_Forecasting_Engine_Brief.pdf) |
| **Geolocation matching** (Haversine, privacy) | [`docs/GEOLOCATION_MATCHING.md`](docs/GEOLOCATION_MATCHING.md) |
| **Workforce allocation & mobilization** | [`docs/WORKFORCE_ALLOCATION.md`](docs/WORKFORCE_ALLOCATION.md) |
| **Localization architecture** (8 languages) | [`docs/INTERNATIONALIZATION.md`](docs/INTERNATIONALIZATION.md) |
| **Theme & design system** | [`docs/THEME_DESIGN_SYSTEM.md`](docs/THEME_DESIGN_SYSTEM.md) |
| **Testing & QA** (plan, report, screen audit) | [`docs/MOBILE_TESTING.md`](docs/MOBILE_TESTING.md) · [`docs/MOBILE_TEST_REPORT.md`](docs/MOBILE_TEST_REPORT.md) · [`docs/SCREEN_AUDIT.md`](docs/SCREEN_AUDIT.md) |
| **Feature parity & project audits** | [`docs/FEATURE_PARITY.md`](docs/FEATURE_PARITY.md) · [`docs/PROJECT_AUDIT.md`](docs/PROJECT_AUDIT.md) · [`docs/PROJECT_GAP_ANALYSIS.md`](docs/PROJECT_GAP_ANALYSIS.md) |

---

## ⚖️ License & Cooperative Ethics

Sahakari Seva is developed under the MIT License for open-source cooperative public goods. Dedicated to the dignity of informal gig labor and digital sovereignty for worker cooperatives.
