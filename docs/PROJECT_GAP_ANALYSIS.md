# Sahakari Seva — Requirements Gap Analysis (Web vs Mobile)

## A. Already Working in Mobile
1. **Multi-Role Authentication & Demo Session Switcher**:
   - `LoginScreen.tsx` with role toggle (Customer, Worker, Admin) and 1-tap evaluation accounts (Amit Kumar, Rajesh Sharma, Federation Officer).
2. **8-Trade Service Catalog**:
   - Bilingual trade categories on Customer `HomeScreen.tsx` with icons and base pricing.
3. **OpenStreetMap Leaflet Interactive Mapping (Zero Paid Map APIs)**:
   - `MobileMapView.tsx` rendering OSM raster tiles, custom worker pins, distance indicators, and service radius rings.
4. **Worker Proximity Discovery & Search**:
   - `WorkerSearchScreen.tsx` with category filter, radius slider (5–25km), minimum rating filter, and emergency toggle.
5. **Worker Profile Inspection**:
   - `WorkerDetailScreen.tsx` showing ITI certificates, skills, bio, and hourly tariffs.
6. **Booking Creation with Fair Split Calculation**:
   - `BookingCreateScreen.tsx` modeling 85% worker earnings, 10% welfare contribution, and 5% platform operations fee.
7. **Worker Job Lifecycle Actions**:
   - `WorkerJobsScreen.tsx` with Accept $\rightarrow$ Start Work $\rightarrow$ Complete Work optimistic state transitions.
8. **Worker GPS Detection & Service Radius Configuration**:
   - `WorkerLocationScreen.tsx` with native GPS permission handling and draggable radius slider.
9. **Admin AI Demand Forecasting Engine**:
   - `AdminForecastScreen.tsx` with 7-day projected demand curves, weekend surges (+55%), and monsoon impact alerts.
10. **Admin Workforce Allocation Engine**:
    - `AdminAllocationScreen.tsx` discovering zone deficits and triggering 1-tap standby worker mobilizations.
11. **Instant Bilingual Localization (English & Hindi)**:
    - `i18next` integration supporting sub-50ms language toggle without application restart.

---

## B. Working in Website but Missing from Mobile
1. **Booking Detail & Live Job Tracking (`BookingDetailPage.tsx` $\rightarrow$ Mobile)**:
   - In the web app, customers can view a live status stepper, contact the worker, view emergency dispatch notes, initiate demo payment upon completion, view the invoice, and submit ratings. Currently in mobile, tapping a booking in `CustomerBookingsScreen.tsx` does not navigate to a dedicated detail screen.
2. **Demo Payment & Invoice Generation (`InvoicePage.tsx` $\rightarrow$ Mobile)**:
   - In the web app, after a job is completed, customers execute a simulated payment with instant invoice receipt generation displaying the 85/10/5 breakdown.
3. **1–5 Star Rating & Review Submission (`RatingModal` $\rightarrow$ Mobile)**:
   - In the web app, customers rate completed bookings (1–5 stars) with feedback comments, which immediately updates the worker's average rating and total jobs count.
4. **Admin Worker Verification & KYC Review (`WorkerVerificationPage.tsx` $\rightarrow$ Mobile)**:
   - In the web app, administrators can inspect pending worker profiles (e.g., Arjun Meena), review government ITI certificates, and approve/reject them with a single click.
5. **Worker Welfare & Social Security Breakdown (`WorkerEarningsWelfarePage.tsx` $\rightarrow$ Mobile)**:
   - In the web app, workers can inspect their enrollment in national schemes (Ayushman Bharat, PMSBY, PMJJBY, pension contribution corpus share).
6. **Worker Profile & Certification Upload Simulator (`WorkerProfilePage.tsx` $\rightarrow$ Mobile)**:
   - In the web app, workers can manage trade skills, update bio, and upload trade certificates with file validation.
7. **In-App Notifications Viewer (`NotificationsModal` $\rightarrow$ Mobile)**:
   - In the web app, reactive notifications keep users informed of booking status changes, verification approvals, and emergency alerts.

---

## C. Present in Both but Inconsistent
1. **Navigation Target Aliases**:
   - Web uses URL paths (`/customer/workers`, `/worker/jobs`). Mobile uses tab names (`Search`, `WorkerLocation`). Handled by adding alias routes in `RootNavigator.tsx`.
2. **Pricing Structure Representation**:
   - Mobile and Web both model 85/10/5, but mobile previously lacked an invoice receipt screen to visualize the fee allocation for customers after payment.

---

## D. Present but Broken
- *Previously Discovered & Fixed*:
  - Missing PNG assets in `mobile/assets/` (`icon.png`, `splash.png`, etc.) causing Metro bundling failure — **Fixed**.
  - Route name mismatch (`Services` vs `Search`, `Availability` vs `WorkerLocation`) — **Fixed with alias screens**.
  - Hardcoded `localhost:5001` in mobile — **Fixed with dynamic LAN IP resolution via `Constants.expoConfig?.hostUri`**.

---

## E. Present but Badly Implemented
1. **Ad-hoc Styling & Colors in Mobile Screens**:
   - Colors were individually hardcoded as string literals (`#15803d`, `#64748b`, `#0f172a`) in separate stylesheets rather than imported from a centralized design system.
   - *Remedy*: Create `mobile/src/theme/index.ts` with color tokens, typography, spacing, border radii, and reusable UI components.

---

## F. Required by Architecture but Missing from Both
- Real government UIDAI / DigiLocker API integration (properly and transparently simulated as demo verification according to Constraint 7).

---

## G. Duplicate / Unnecessary Functionality
- Desktop-specific multi-column marketing layouts on the web landing page that do not belong inside a native mobile bottom-tab experience.

---

## H. Features that Should be Removed
- Any hardcoded mock data that bypasses the unified `dataStore` / `ApiClient` layer.

---

## I. Features that Should be Redesigned for Mobile
1. **Worker Verification Interface**:
   - Rather than a wide desktop table, redesign as a touch-friendly mobile review card with large "Approve" / "Reject" touch targets and an in-sheet certificate previewer.
2. **Invoice Receipt**:
   - Rather than a full-width A4 printable layout, redesign as a receipt slip card optimized for smartphone screens with 85/10/5 fair split badges.
3. **Rating Submission**:
   - Implement as a sleek modal or bottom sheet with large touchable gold stars.

---

## J. Features that Should Remain Web/Admin-Only
- Advanced batch CSV export of historical federation tax filings and multi-page printable legal bylaws.
