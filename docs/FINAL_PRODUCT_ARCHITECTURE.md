# Sahakari Seva — Final Product Architecture & Layered System Design

## 1. Architectural Principles

Sahakari Seva is engineered with strict separation of concerns, ensuring that business logic, mathematical engines, and database persistence never leak into ad-hoc UI components:

```
+-------------------------------------------------------------------------+
|                              MOBILE UI LAYER                            |
|  Screens: Customer (Home, Search, Map, Detail, Book, Track, Invoice)    |
|           Worker (Earnings, Jobs, Location, Welfare, Profile)           |
|           Admin (Dashboard, Verification, Forecast, Allocation)         |
+-------------------------------------------------------------------------+
                                     ↓
+-------------------------------------------------------------------------+
|                             NAVIGATION LAYER                            |
|  RootNavigator: Auth Session Guard → Multi-Role Bottom Tabs + Stacks    |
|  CustomerStackNavigator | WorkerTabNavigator | AdminTabNavigator       |
+-------------------------------------------------------------------------+
                                     ↓
+-------------------------------------------------------------------------+
|                       REUSABLE COMPONENTS & THEME                       |
|  Design System Tokens: Colors, Typography, Spacing, Radii, Shadows      |
|  UI Atoms: Button, Card, Badge, RatingStars, EmptyState, Input, Modal   |
|  Specialized: MobileMapView (OSM Leaflet), LanguageModal, Header        |
+-------------------------------------------------------------------------+
                                     ↓
+-------------------------------------------------------------------------+
|                        APPLICATION SERVICES LAYER                       |
|  - ApiClient: Timeout management, dynamic LAN host resolution, caching   |
|  - MobileLocationService: Foreground GPS lifecycle, fallback centroids  |
|  - i18n Localization Engine: Sub-50ms reactive switching (EN / HI)     |
+-------------------------------------------------------------------------+
                                     ↓
+-------------------------------------------------------------------------+
|                       BACKEND REST ENGINE (PORT 5001)                   |
|  - Haversine Distance Engine (R = 6371.0 km)                            |
|  - 5-Factor Transparent Matching Algorithm (40/20/20/10/10)             |
|  - Time-Series OLS Trend Regression + EMA + Weekend Surge (+55%)        |
|  - Workforce Allocation Capacity Balancer (3 jobs/worker/day)           |
|  - Ratings, Invoices, Payments, Welfare & Notifications Handlers        |
+-------------------------------------------------------------------------+
                                     ↓
+-------------------------------------------------------------------------+
|                        DATABASE & PERSISTENCE LAYER                     |
|  PostgreSQL / Supabase (13 Relational Tables with RLS & Spatial Indexes) |
|  - profiles, cooperatives, workers, service_categories, bookings        |
|  - ratings, payments, invoices, welfare, notifications                  |
|  - demand_events, demand_forecasts, workforce_allocations               |
+-------------------------------------------------------------------------+
```

---

## 2. Component Layer Responsibilities

### 2.1 Mobile UI Layer (`mobile/src/screens/`)
- **Customer Subsystem**:
  - `HomeScreen`: Categorized services, emergency quick-action, nearby recommendations.
  - `WorkerSearchScreen`: Multi-criteria filtering (trade, rating, emergency, radius).
  - `WorkerMapScreen`: Fullscreen interactive map with worker pins and callout bottom sheet.
  - `WorkerDetailScreen`: Worker portfolio, ITI credentials, skills pills, customer reviews.
  - `BookingCreateScreen`: Date/time scheduling, address input, 85/10/5 price breakdown.
  - `BookingDetailScreen`: Real-time job lifecycle tracking, calling worker, emergency notes, payment trigger, invoice viewer, rating modal.
  - `InvoiceScreen`: Digital cooperative invoice receipt with tax, fees, and fair split allocation.
  - `CustomerBookingsScreen`: Customer booking history list with status indicators.
- **Worker Subsystem**:
  - `WorkerHomeScreen`: Real-time earnings card (85% wage vs 10% welfare pool), availability switch (`Available` / `Busy`).
  - `WorkerJobsScreen`: Lifecycle execution action bar: Accept $\rightarrow$ Start Work $\rightarrow$ Complete Work.
  - `WorkerLocationScreen`: GPS coordinate resolution, address resolution, interactive radius slider.
  - `WorkerWelfareScreen`: Social security scheme tracker (Ayushman Bharat, PMSBY, PMJJBY, pension corpus share).
  - `WorkerProfileScreen`: Trade skill checklist, bio editor, ITI certificate upload simulator with file validation.
- **Admin Subsystem**:
  - `AdminDashboardScreen`: Federation overview, welfare pool balance, verified worker roster.
  - `AdminVerificationScreen`: Review pending workers (Arjun Meena), inspect identity and ITI credentials, 1-tap Approve/Reject.
  - `AdminForecastScreen`: 7-day projected demand curves, peak hours, and seasonal surges.
  - `AdminAllocationScreen`: Cluster supply-demand balancing, deficit discovery, 1-tap standby worker mobilization.

---

### 2.2 Reusable Design System Layer (`mobile/src/theme/` & `mobile/src/components/ui/`)
- **Centralized Tokens**:
  - `colors`: Cooperative forest green (`#15803d`), saffron alert (`#b45309`), deep slate (`#0f172a`), surface off-white (`#f8fafc`).
  - `spacing`: Strict 4px scale (`xs: 4`, `sm: 8`, `md: 12`, `lg: 16`, `xl: 20`, `xxl: 24`).
  - `radii`: Rounded corners (`sm: 6`, `md: 10`, `lg: 14`, `xl: 18`, `full: 9999`).
  - `typography`: Standardized mobile font scale (11px caption, 13px body, 15px title, 18px heading, 24px hero).
- **Atoms**:
  - `Button`: Primary, secondary, outline, danger, ghost variants with minimum $48\text{px}$ touch targets.
  - `Card`: Surface container with border and subtle elevation.
  - `Badge`: Status tags (`success`, `danger`, `warning`, `info`, `coop`).
  - `RatingStars`: Accessible 1–5 star renderer with fractional ratings.

---

### 2.3 Application Services Layer (`mobile/src/services/`)
- **`ApiClient`**:
  - Automatically resolves developer machine LAN IP via `Constants.expoConfig?.hostUri` when running in Expo Go on mobile phones.
  - Implements an automated 4-second request abort timeout.
  - Encapsulates rich offline fallback data for categories, nearby workers, bookings, forecasts, allocations, and stats so network blips never crash the UI.
- **`MobileLocationService`**:
  - Interfaces with native `expo-location` for foreground GPS permissions.
  - Provides pre-configured fallback coordinates for Delhi (CP, Saket, Karol Bagh, Mayur Vihar), Jaipur, and Mumbai.
- **`i18n` Engine**:
  - Manages translation catalogs for English (`en.json`) and Hindi (`hi.json`).
  - Supports instant language switching with zero app reloads and session persistence.

---

### 2.4 Backend Microservice Layer (`backend/`)
- **Geolocation Matching**: Spherical Haversine calculation ($R = 6371.0\text{ km}$) with dual-radius constraints.
- **Privacy Masking**: Exact worker home coordinates are jittered by $\pm 500\text{m}$ before client transmission.
- **AI Forecasting**: Ordinary Least Squares (OLS) trend analysis, Exponential Moving Averages ($\alpha = 0.35$), weekend multipliers ($+55\%$), and 95% confidence bounds.
- **Workforce Allocation**: Analyzes capacity deficit ($\Delta = 3 \times W_{\text{active}} - \text{Demand}$) and calculates standby worker mobilization requirements.
- **Lifecycle Endpoints**: Handles booking state machine, payment processing, invoice generation, worker verification, and welfare updates.

---

### 2.5 Database & Storage Layer (`supabase/migrations/`)
- Relational schema enforcing foreign keys, spatial indices, automatic `updated_at` triggers, and Row-Level Security (RLS).
