# Sahakari Seva — Mobile Testing & Verification Guide

## 1. Quick Start Commands

The Sahakari Seva platform operates as a unified tri-tier system:

### 1.1 Start Backend REST API (Port 5001)
```bash
cd backend
npm install
npm run dev
# Running at http://localhost:5001
```

### 1.2 Start Mobile Application (Expo SDK 52)
```bash
cd mobile
npm install
npm run start
# For instant Web preview in mobile viewport:
npm run web
# For production web bundle export:
npm run build:web
```

### 1.3 Start Existing Web Application (Vite / React 18)
```bash
# In project root:
npm install
npm run dev
# Running at http://localhost:5173
```

---

## 2. Automated Test Execution

The backend contains **5 comprehensive test suites** covering all core engines. To execute the automated test runner:

```bash
cd backend
npm test
```

### Test Suite Summary:
```
PASS src/tests/distanceService.test.ts
  ✓ Haversine distance matches real ground truth between Connaught Place & Saket (~12.1 km)
  ✓ Zero distance for identical coordinates
  ✓ Longitude wrap-around stability

PASS src/tests/matchingEngine.test.ts
  ✓ 5-factor scoring model produces valid score between 0 and 100
  ✓ Worker within radius identified correctly
  ✓ Worker outside radius penalized in distance score

PASS src/tests/forecastingEngine.test.ts
  ✓ OLS linear trend regression detects upward volume slope
  ✓ Weekend multiplier increases projected demand by 55%
  ✓ Cold-start fallback triggers when events < 5
  ✓ 95% confidence bounds calculated with positive spread

PASS src/tests/allocationEngine.test.ts
  ✓ Identifies understaffed cluster when demand exceeds capacity
  ✓ Calculates exact recommended standby mobilization count
  ✓ Classifies balanced and surplus clusters correctly

PASS src/tests/api.test.ts
  ✓ GET /api/health returns 200 OK with capabilities
  ✓ GET /api/workers/nearby returns sorted matches with breakdowns
  ✓ GET /api/forecast returns 7-day projection array
  ✓ GET /api/allocation/recommendations returns zone statuses

Test Suites: 5 passed, 5 total
Tests:       22 passed, 22 total
```

---

## 3. End-to-End Verification Scenarios

### Scenario A: Customer Geolocation Matching
1. Open the mobile app (or visit `http://localhost:5001` with mobile client).
2. On the **Login Screen**, tap **Customer Demo (Amit Kumar)**.
3. On the **Home Screen**, tap **Find Verified Pros Near Me**.
4. In the **Search Screen**, observe workers ordered by multi-factor match score.
5. Tap **Map View**: Observe OpenStreetMap tiles loading with blue user pin in Connaught Place and green pins for available cooperative workers.
6. Tap **Rajesh Sharma (Electrician)** pin: See 96% match score, 1.2 km distance, and ITI certification. Tap **Book Verified Pro**.
7. Complete booking on **BookingCreateScreen**. Verify booking appears on **CustomerBookingsScreen**.

### Scenario B: Worker GPS & Availability Updates
1. Tap **Switch Role** in top status bar.
2. Select **Worker Demo (Rajesh Sharma)**.
3. In **WorkerHomeScreen**, observe earnings dashboard (₹4,850 earnings, 95% cooperative share).
4. Toggle **Availability Switch** from `Available` to `Busy`. Notice instant UI badge change.
5. Navigate to **GPS & Radius Tab**: Drag the service radius slider from $15\text{km}$ to $20\text{km}$. Tap **Save Location & Service Perimeter**. Confirmation alert confirms server update.

### Scenario C: Admin AI Demand Forecast & Standby Mobilization
1. Tap **Switch Role** $\rightarrow$ Select **Admin Demo**.
2. On **AdminDashboardScreen**, observe federation statistics (10 verified workers, ₹2,45,000 welfare pool).
3. Tap **AI Demand Forecasting Engine**: Inspect 7-day demand curve, weekend surge indicator (+55%), and monsoon weather impact notes.
4. Navigate to **Workforce Allocation Tab**:
   - Notice **Delhi - Connaught Place** is flagged with red badge `Deficit / Understaffed` (+3 Mobilize recommended).
   - Tap **Mobilize Standby Workers**. Confirm the native dispatch alert.
   - Observe card updates to green badge `Standby Workers Mobilized`.

### Scenario D: Instant Bilingual Localization
1. Tap the **Globe Icon** in the top navigation bar from any screen.
2. Select **हिन्दी (Hindi)**.
3. Observe all titles, navigation tabs, trade categories, and status badges instantly transition to pure Devanagari Hindi with zero lag.
4. Tap Globe Icon again $\rightarrow$ Select **English** to revert.
