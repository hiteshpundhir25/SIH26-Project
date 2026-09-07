# Project Architecture & System Design — Sahakari Seva

**Status**: Completed & Production-Ready  
**Architecture**: Unified Tri-Tier System (Mobile App + REST Backend + Web App)  
**Date**: September 2026  

---

## 1. Executive Summary

**Sahakari Seva** (सहकारी सेवा) is an open-source, worker-owned digital cooperative platform empowering skilled gig professionals (electricians, plumbers, carpenters, cleaners, appliance technicians) across Indian urban centers (Delhi, Jaipur, Mumbai). 

The platform enforces a transformative economic model:
- **85%** of the tariff goes directly to the cooperative worker.
- **10%** contributes to the worker-owned **Federation Social Security, Welfare & Emergency Corpus**.
- **5%** supports transparent cooperative operations; there are no hidden deductions or algorithmic penalties.

---

## 2. Tri-Tier System Architecture

```
                                  +--------------------------------------------------+
                                  |            POSTGRESQL / SUPABASE DB              |
                                  | - workers (lat, lng, radius)                     |
                                  | - demand_events (160+ historical events)         |
                                  | - demand_forecasts (OLS regression & confidence) |
                                  | - workforce_allocations (supply/demand zones)    |
                                  +--------------------------------------------------+
                                                           ^
                                                           |
                                                           v
                                  +--------------------------------------------------+
                                  |           EXPRESS + TYPESCRIPT REST API          |
                                  |                   (PORT 5001)                    |
                                  | - Haversine Distance Engine (R=6371km)           |
                                  | - 5-Factor Matching & Privacy Masking            |
                                  | - Time-Series OLS AI Forecasting Engine          |
                                  | - Supply-Demand Balancing & Mobilization         |
                                  +--------------------------------------------------+
                                            ^                              ^
                                            |                              |
                                            v                              v
+---------------------------------------------------+    +---------------------------------------------------+
|         MOBILE APP (REACT NATIVE / EXPO 52)       |    |           WEB APP (VITE 6 / REACT 18)             |
|                    (/mobile)                      |    |                     (/src)                        |
| - Expo SDK 52 + React Navigation 7                |    | - React 18 + Vite 6 + Tailwind CSS                |
| - Bottom Tabs: Customer, Worker, Admin            |    | - OpenStreetMap Leaflet Map / Grid Switcher       |
| - OpenStreetMap Leaflet Touch MapView             |    | - Geolocation Anchor & GPS Filter                 |
| - GPS Location Permission & Fallback Anchors      |    | - Interactive 7-Day Demand Curves                 |
| - Complete English + Hindi (i18next) Localization |    | - Live Dynamic Workforce Allocation Tab           |
| - 1-Click Role Switching & Standby Mobilization   |    | - Fair-Wage Commission & Welfare Corpus Trackers  |
+---------------------------------------------------+    +---------------------------------------------------+
```

---

## 3. Component Deep Dive

### 3.1 Backend REST API (`/backend`)
- **Runtime**: Node.js + Express 5 + TypeScript 5
- **Port**: `5001`
- **Modules**:
  - `distanceService.ts`: Pure spherical Haversine distance formula ($R = 6371\text{ km}$).
  - `matchingEngine.ts`: 5-factor scoring model ($40\%$ distance, $20\%$ availability, $20\%$ rating, $10\%$ reliability, $10\%$ service match) with privacy centroid masking.
  - `forecastingEngine.ts`: OLS linear trend regression, EMA baseline, $+55\%$ weekend multipliers, and 95% confidence bounds.
  - `allocationEngine.ts`: Evaluates worker supply capacity vs predicted jobs, categorizes zones into `understaffed`, `balanced`, and `overstaffed`, and outputs mobilization plans.
  - `dataStore.ts`: In-memory reactive store with 10 real worker coordinates and 160+ historical events.
- **Automated Tests**: 5 test suites (22/22 unit and integration tests passing).

### 3.2 Mobile Application (`/mobile`)
- **Runtime**: Expo SDK 52 + React Native 0.76.7
- **Navigation**: Multi-role Bottom Tab Navigators + Native Stack Navigation
  - **Customer**: Home, Search, OpenStreetMap View, Bookings, Worker Profile, Create Booking.
  - **Worker**: Home / Earnings Dashboard, Job Lifecycle Manager, GPS & Service Radius.
  - **Admin**: Federation Overview, AI Demand Forecast, Dynamic Workforce Allocation.
- **Mapping**: Pure OpenStreetMap Leaflet rendering via `react-native-webview` (Zero Google Maps/Mapbox APIs).
- **Localization**: Complete English + Hindi (देवनागरी) bilingual catalog.
- **Touch & Accessibility**: Every interactive element satisfies $\ge 48\text{px}$ touch targets.

### 3.3 Web Application (`/src`)
- **Runtime**: Vite 6 + React 18 + Tailwind CSS + Lucide Icons + Recharts
- **Enhancements**:
  - `WebOsmMap.tsx`: OpenStreetMap Leaflet container with radius rings and worker pins.
  - `WorkerSearchPage.tsx`: Grid / Map view mode switcher, GPS geolocation detection, radius filters, and Haversine distance matching.
  - `DemandForecastingPage.tsx`: Live connection to `/api/allocation/recommendations`, zone status filtering, and 1-tap standby mobilization.

---

## 4. Technical Documentation Directory

| Document | Scope |
|---|---|
| [`MOBILE_ARCHITECTURE.md`](file:///Users/hitesh_pundhir25/Desktop/SIH%20Project/MOBILE_ARCHITECTURE.md) | Mobile app structure, navigation hierarchy, touch standards, and resilience. |
| [`API_DOCUMENTATION.md`](file:///Users/hitesh_pundhir25/Desktop/SIH%20Project/API_DOCUMENTATION.md) | Comprehensive REST API endpoints, schemas, and responses. |
| [`DATABASE_CHANGES.md`](file:///Users/hitesh_pundhir25/Desktop/SIH%20Project/DATABASE_CHANGES.md) | SQL migration, schema definitions, indices, and RLS policies. |
| [`GEOLOCATION_MATCHING.md`](file:///Users/hitesh_pundhir25/Desktop/SIH%20Project/GEOLOCATION_MATCHING.md) | Haversine formula, 5-factor matching algorithm, and privacy masking. |
| [`INTERNATIONALIZATION.md`](file:///Users/hitesh_pundhir25/Desktop/SIH%20Project/INTERNATIONALIZATION.md) | Bilingual English/Hindi localization architecture and trade dictionaries. |
| [`ML_FORECASTING.md`](file:///Users/hitesh_pundhir25/Desktop/SIH%20Project/ML_FORECASTING.md) | OLS regression, time-series moving averages, and 95% confidence intervals. |
| [`WORKFORCE_ALLOCATION.md`](file:///Users/hitesh_pundhir25/Desktop/SIH%20Project/WORKFORCE_ALLOCATION.md) | Capacity balancing, deficit discovery, and standby mobilization workflows. |
| [`MOBILE_TESTING.md`](file:///Users/hitesh_pundhir25/Desktop/SIH%20Project/MOBILE_TESTING.md) | Verification guide, test suite output, and user flows. |
| [`README.md`](file:///Users/hitesh_pundhir25/Desktop/SIH%20Project/README.md) | Project quickstart, overview, and feature matrix. |
