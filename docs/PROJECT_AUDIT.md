# Sahakari Seva — Comprehensive Project Audit

**Audit Date**: September 2026  
**Audited Systems**: 
1. React 18 + Vite 6 Web Application (`/src`)
2. React Native + Expo SDK 52 Mobile Application (`/mobile`)
3. Express 5 + TypeScript REST Backend (`/backend`)
4. PostgreSQL / Supabase Schema & Migrations (`/supabase`)

---

## 1. Executive Summary

Sahakari Seva (सहकारी सेवा) is an open-source, worker-owned cooperative platform providing household and urban trade services across Indian metropolitan areas (Delhi, Jaipur, Mumbai). 

The platform enforces a transformative economic model:
- **85%** direct earnings to the certified skilled worker.
- **10%** contribution to the worker-owned Cooperative Welfare, Social Security & Pension Corpus (covering Ayushman Bharat, PMJJBY, PMSBY).
- **5%** transparent platform operational fee.

This audit evaluates the codebase across both the original web portal and the newly developed mobile application, identifying architectural strengths, missing features, duplicate logic, and migration pathways.

---

## 2. Mobile Application Audit (`/mobile`)

### 2.1 Package Configuration & Tooling
- **Framework**: React Native `0.76.9` with Expo SDK `52.0.37`.
- **Language**: TypeScript 5.3.3 in strict mode with `skipLibCheck: true`.
- **Navigation**: `@react-navigation/bottom-tabs` (v7.2.0), `@react-navigation/native-stack` (v7.2.0), `@react-navigation/native` (v7.0.14).
- **Icons & UI**: `lucide-react-native` (v0.477.0), `react-native-safe-area-context` (v4.12.0), `react-native-screens` (~4.4.0), `react-native-svg` (15.8.0).
- **Mapping**: OpenStreetMap raster tiles rendered via Leaflet.js in `react-native-webview` (v13.12.5). Zero Google Maps / Mapbox dependencies.
- **Localization**: `i18next` (v24.2.2) and `react-i18next` (v15.4.1) with full English and Hindi catalogs.
- **Location**: `expo-location` (~18.0.7) with foreground permissions and coordinate acquisition.

### 2.2 Navigation Architecture
- **RootNavigator**:
  - `LoginScreen`: Multi-role selector (Customer, Worker, Admin) with 1-click evaluation profiles.
  - `CustomerStackNavigator`: Nested bottom tabs (`Home`, `Search`, `Map`, `Bookings`) + modal stacks (`WorkerDetail`, `BookingCreate`).
  - `WorkerTabNavigator`: Bottom tabs (`WorkerHome`, `WorkerJobs`, `WorkerLocation`).
  - `AdminTabNavigator`: Bottom tabs (`AdminDashboard`, `Forecast`, `Allocation`).

### 2.3 Working Workflows & Capabilities in Mobile
1. Role selection and demo profile switching.
2. 8-Trade service catalog display with bilingual Hindi titles.
3. OpenStreetMap fullscreen interactive map with worker pins and service radius rings.
4. Worker search with category filtering, minimum rating, and emergency priority.
5. Booking creation with date/time pickers and 85/10/5 fair split price breakdown.
6. Worker job lifecycle: Accept booking $\rightarrow$ Start work $\rightarrow$ Mark completed.
7. Worker GPS location detection and service radius slider.
8. Admin AI demand forecasting 7-day projection curves and weekend surge metrics.
9. Admin workforce allocation supply-demand deficit discovery and 1-tap standby worker mobilization.

### 2.4 Mobile Gaps Identified
- Missing dedicated **`BookingDetailScreen`** for live status tracking, calling the worker, emergency alerts, and post-completion triggers.
- Missing **Demo Payment & Invoice generation screen** (`InvoiceScreen`).
- Missing **1–5 Star Rating & Review submission modal** (`RatingModal`).
- Missing **Admin Worker Verification & KYC Review screen** (`AdminVerificationScreen`) to approve/reject pending workers like Arjun Meena.
- Missing **Worker Welfare & Social Security details screen** (`WorkerWelfareScreen`).
- Missing **Worker Profile & ITI Certification upload simulator** (`WorkerProfileScreen`).
- Missing **In-App Notifications modal** (`NotificationsModal`).

---

## 3. Web Application Feature Inventory (`/src`)

The existing web application is built on React 18, Vite 6, Tailwind CSS, and a local/Supabase reactive service (`src/services/dataService.ts`).

### 3.1 Web Pages & Capabilities
- **Customer Pages**:
  - `WorkerSearchPage.tsx`: Includes Map / Grid toggle, radius filter, GPS anchor presets.
  - `WorkerDetailPage.tsx`: Displays worker portfolio, ITI credentials, cooperative membership.
  - `BookingCreatePage.tsx`: Fair split calculator, emergency toggle, address entry.
  - `BookingDetailPage.tsx`: Detailed tracking, cancellation, payment trigger, review trigger.
  - `InvoicePage.tsx`: Clean GST invoice receipt with 85/10/5 split breakdown.
  - `CustomerDashboard.tsx`: Active bookings, quick re-book.
- **Worker Pages**:
  - `WorkerDashboard.tsx`: Daily earnings, net payouts, welfare share.
  - `WorkerJobsPage.tsx`: Filter by status (`all`, `active`, `completed`).
  - `WorkerEarningsWelfarePage.tsx`: Social security scheme enrollment (Ayushman Bharat, PMSBY, PMJJBY, pension pool).
  - `WorkerProfilePage.tsx`: Certification upload form with file validation, skills list, KYC badge.
- **Admin Pages**:
  - `WorkerVerificationPage.tsx`: Pending workers list, document preview, 1-tap Approve/Reject.
  - `AdminWorkersPage.tsx`: Roster of all verified and pending workers.
  - `AdminBookingsPage.tsx`: Global booking log.
  - `AdminServicesPage.tsx`: Trade category management.
  - `AdminWelfarePage.tsx`: Federation welfare corpus distribution and balance.
  - `DemandForecastingPage.tsx`: 7-day curve and live workforce allocation.

---

## 4. Backend Service Layer Audit (`/backend`)

- **Server**: Express 5 on port `5001`.
- **Engines**:
  - `distanceService.ts`: Spherical Haversine calculation ($R = 6371\text{ km}$).
  - `matchingEngine.ts`: 5-factor scoring model ($40\%$ distance, $20\%$ availability, $20\%$ rating, $10\%$ reliability, $10\%$ trade match).
  - `forecastingEngine.ts`: OLS linear trend regression, EMA baseline, $+55\%$ weekend surge, 95% confidence intervals, cold-start fallback.
  - `allocationEngine.ts`: Compares predicted demand against active workers $\times 3\text{ jobs/day}$, categorizing clusters into `understaffed`, `balanced`, `overstaffed`.
- **Data Store**: Reactive in-memory store initialized with 10 real worker coordinates and 160+ historical events spanning 50 days.
- **Test Coverage**: 5 test suites with 22/22 unit and integration tests passing.
- **Needed Extensions**: Routes for ratings (`/api/ratings`), payments & invoices (`/api/payments`, `/api/invoices`), welfare (`/api/welfare`), notifications (`/api/notifications`), and worker verification (`PATCH /api/workers/:id/verify`).

---

## 5. Database Schema Audit (`/supabase`)

- **Migration 1** (`20260901000001_initial_schema.sql`):
  1. `cooperatives`
  2. `profiles`
  3. `service_categories`
  4. `workers`
  5. `bookings`
  6. `ratings`
  7. `payments`
  8. `invoices`
  9. `welfare`
  10. `notifications`
- **Migration 2** (`20260904000002_mobile_geo_ai_forecasting.sql`):
  11. `demand_events`
  12. `demand_forecasts`
  13. `workforce_allocations`
  - Added spatial columns to `workers`: `latitude`, `longitude`, `location_updated_at`, `location_accuracy`, `service_radius_km`.
- **Schema Verdict**: 100% reconciled and aligned with product requirements. No schema conflicts or missing tables.
