# Sahakari Seva — Feature Parity Matrix (Website vs Mobile)

**Generated**: September 5, 2026  
**Auditor**: Lead Full-Stack & Mobile Engineer  
**Objective**: Comprehensive feature parity audit mapping all capabilities from the original React Web Application (`src/`) against the React Native Mobile Application (`mobile/`), identifying gaps, migration status, and technical implementation details.

---

## 1. Feature Parity Matrix

| Feature Area | Sub-Feature / Capability | Web App (`src/`) Status | Mobile App (`mobile/`) Target Status | Implementation Details & Parity Notes |
|---|---|---|---|---|
| **Authentication & Identity** | Multi-Role Authentication | Supported (Customer, Worker, Admin) | **Full Parity** | `LoginScreen.tsx` with one-tap demo account selector and role-based routing. |
| | Demo Role Switcher | Available via top-right pill | **Full Parity** | Persistent switcher in `Header.tsx` and `LoginScreen.tsx` for seamless multi-role testing. |
| | Session State & Persistence | React Context (`AuthContext.tsx`) | **Full Parity** | `AuthContext.tsx` with mobile AsyncStorage / in-memory store. |
| **Customer Journey** | Service Category Discovery | Icon grid on Landing & Services page | **Full Parity** | Interactive grid with category icons & counts on `HomeScreen.tsx`. |
| | Geo-Proximity Worker Search | Radius search & list | **Enhanced** | Haversine distance calculation, radius filter (1–25 km), sorting by distance/rating/price. |
| | Live OpenStreetMap Worker Discovery | Leaflet web map | **Enhanced** | Native `react-native-webview` Leaflet map with user pin, worker markers, and tap-to-view sheet. |
| | Worker Profile & Verification View | Detail modal / page | **Full Parity** | `WorkerDetailScreen.tsx` with verified badges, hourly rate, reviews, and cooperative contribution. |
| | Booking Creation Flow | Booking form with service selection | **Enhanced** | `BookingCreateScreen.tsx` with live 85/10/5 fair split transparent calculation. |
| | Customer Booking Management | Booking list with status badges | **Full Parity** | `CustomerBookingsScreen.tsx` with tabbed filter (Active, Completed, Cancelled). |
| | Booking Life-Cycle Tracking | `BookingDetailPage.tsx` with stepper | **Full Parity (Migrated)** | `BookingDetailScreen.tsx` with step-by-step progress, worker phone dialer, and quick actions. |
| | Demo Payment Processing | Simulated checkout | **Full Parity (Migrated)** | Direct "Pay Now (Demo)" action on `BookingDetailScreen.tsx` updating booking state to `completed`. |
| | Transparent Cooperative Invoice | `InvoicePage.tsx` with 85/10/5 breakdown | **Full Parity (Migrated)** | `InvoiceScreen.tsx` displaying worker payout (85%), welfare pool (10%), and platform fee (5%). |
| | Worker Rating & Review System | Star rating component & submit | **Full Parity (Migrated)** | `RatingModal.tsx` supporting 1–5 stars, quality tags ("Prompt", "Skilled"), and comments. |
| **Worker Cockpit** | On-Duty / Off-Duty Toggle | Toggle button | **Full Parity** | One-tap switch on `WorkerHomeScreen.tsx` updating live availability in store. |
| | Job Management Life-Cycle | Accept $\rightarrow$ Start $\rightarrow$ Complete buttons | **Full Parity** | `WorkerJobsScreen.tsx` with real-time state mutation and alert feedback. |
| | Live GPS Location Broadcasting | Browser Geolocation API | **Enhanced** | `WorkerLocationScreen.tsx` using `expo-location` with high-accuracy GPS and simulator. |
| | Welfare Passbook & Social Security | `WorkerEarningsWelfarePage.tsx` | **Full Parity (Migrated)** | `WorkerWelfareScreen.tsx` showing Ayushman Bharat, PMSBY, PMJJBY, and pension accrual. |
| | Digital Worker Profile & Skills | `WorkerProfilePage.tsx` | **Full Parity (Migrated)** | `WorkerProfileScreen.tsx` showcasing ITI certification, years of experience, trade, and cooperative badge. |
| **Admin Operations** | Cooperative Dashboard | GMV, Job count, Worker metrics | **Full Parity** | `AdminDashboardScreen.tsx` displaying live KPI cards, welfare reserve, and active jobs. |
| | Worker KYC & Verification Queue | `WorkerVerificationPage.tsx` | **Full Parity (Migrated)** | `AdminVerificationScreen.tsx` inspecting pending workers (e.g. Arjun Meena) with 1-tap Approve/Reject. |
| | AI Demand Forecasting | OLS + EMA historical regression | **Enhanced** | `AdminForecastScreen.tsx` with 7-day projection, seasonal trends, and model accuracy indicators ($R^2$, MAE). |
| | Workforce Reallocation Optimizer | Deficit analysis & rebalance | **Enhanced** | `AdminAllocationScreen.tsx` with ward-level demand-supply delta and dispatch suggestions. |
| **Global Capabilities** | Multilingual (English & Hindi) | i18next `en.json`, `hi.json` | **Full Parity** | `LanguageContext.tsx` with instant language switcher button in header and all screens translated. |
| | Notification System | Toast and notification dropdown | **Full Parity (Migrated)** | `NotificationsModal.tsx` accessible via header bell showing booking, payment, and welfare alerts. |
| | Offline Resiliency | Limited | **Enhanced** | `ApiClient.ts` with local fallback datasets ensuring complete app usability even offline. |

---

## 2. Parity Verification Checklist

- [x] Every customer action (Search, View Worker, Book, Pay, View Invoice, Rate) functions end-to-end without crashing.
- [x] Every worker action (Toggle Duty, Accept Job, Start Job, Complete Job, View Welfare, View Profile) operates with full state consistency.
- [x] Every admin action (View KPIs, Verify Worker, Review AI Forecast, Reallocate Workforce) mutates backend data store cleanly.
- [x] Zero external paid APIs: Geolocation uses `expo-location` and OpenStreetMap tiles; AI forecasting runs in-process OLS regression.
- [x] Cooperative economics (85% worker, 10% welfare, 5% platform) strictly calculated and visually presented across all receipt surfaces.
