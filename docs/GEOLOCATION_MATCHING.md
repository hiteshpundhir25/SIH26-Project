# Sahakari Seva — Geolocation Matching Architecture & Algorithms

## 1. Pure Open-Source Map Engine

Sahakari Seva complies strictly with **Constraint 2**:
> **ZERO GOOGLE MAPS API OR MAPBOX DEPENDENCIES.**

All spatial rendering, tile caching, and coordinate projections are executed using **OpenStreetMap (OSM)** raster tiles rendered via **Leaflet.js (v1.9.4)** inside lightweight native WebViews and sandboxed iframes.

---

## 2. Haversine Distance Formula

To determine the great-circle distance between a customer coordinate $(\phi_1, \lambda_1)$ and a worker coordinate $(\phi_2, \lambda_2)$ without external routing APIs, the system utilizes the spherical Haversine formula:

$$d = 2 R \arcsin \left( \sqrt{ \sin^2\left(\frac{\Delta \phi}{2}\right) + \cos(\phi_1) \cos(\phi_2) \sin^2\left(\frac{\Delta \lambda}{2}\right) } \right)$$

Where:
- $R = 6371.0\text{ km}$ (mean radius of the Earth).
- $\Delta \phi = \phi_2 - \phi_1$ (difference in latitude in radians).
- $\Delta \lambda = \lambda_2 - \lambda_1$ (difference in longitude in radians).

### Implementation Code (`backend/src/services/distanceService.ts`):
```typescript
export function calculateHaversineDistance(
  lat1: number,
  lon1: number,
  lat2: number,
  lon2: number
): number {
  const R = 6371.0;
  const toRad = (degrees: number) => (degrees * Math.PI) / 180;

  const dLat = toRad(lat2 - lat1);
  const dLon = toRad(lon2 - lon1);

  const a =
    Math.sin(dLat / 2) * Math.sin(dLat / 2) +
    Math.cos(toRad(lat1)) *
      Math.cos(toRad(lat2)) *
      Math.sin(dLon / 2) *
      Math.sin(dLon / 2);

  const c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1 - a));
  return Math.round(R * c * 10) / 10; // Rounded to 1 decimal place (100m precision)
}
```

---

## 3. Dual-Radius Filtering Logic

A worker is marked `within_radius = true` if and only if both conditions are met:
1. The distance between customer and worker is less than or equal to the customer's search perimeter:
   $$d \le r_{\text{customer}}$$
2. The distance is within the worker's operational service perimeter:
   $$d \le r_{\text{worker\_service\_radius}}$$

Workers outside the radius can still be viewed in general search, but are penalized in the distance score factor.

---

## 4. Multi-Factor Transparent Scoring Algorithm (100 Points)

Unlike opaque proprietary gig platforms, Sahakari Seva calculates matching rank via a mathematically transparent 5-factor model:

| Factor | Weight | Formula / Rules |
|---|---|---|
| **Distance Score** | **40%** | $\max\left(0, \left(1 - \frac{d}{r_{\text{max}}}\right) \times 40\right)$ |
| **Availability Score** | **20%** | `available` = 20 pts, `busy` = 8 pts, `offline` = 0 pts |
| **Rating Score** | **20%** | $\frac{\text{rating}}{5.0} \times 20$ pts |
| **Reliability Score** | **10%** | $\min\left(10, \frac{\text{total\_jobs}}{10}\right)$ pts |
| **Service Match** | **10%** | Exact trade match = 10 pts; related trade = 5 pts |

$$\text{MatchScore} = S_{\text{dist}} + S_{\text{avail}} + S_{\text{rating}} + S_{\text{rel}} + S_{\text{service}}$$

### Example Match Calculation:
For Rajesh Sharma (Electrician):
- Distance: $1.2\text{ km}$ ($15\text{ km}$ radius) $\rightarrow \frac{15 - 1.2}{15} \times 40 = 36.8\text{ pts}$
- Availability: `available` $\rightarrow 20.0\text{ pts}$
- Rating: $4.9 / 5.0 \rightarrow 19.6\text{ pts}$
- Reliability: 142 jobs completed $\rightarrow 10.0\text{ pts}$
- Service Match: Exact match for Electrical $\rightarrow 10.0\text{ pts}$
- **Total Match Score**: $36.8 + 20.0 + 19.6 + 10.0 + 10.0 = \mathbf{96.4\%}$ (Displayed as `96%`).

---

## 5. Worker Privacy Protection & Coordinate Masking

To prevent stalking and protect cooperative worker safety at their homes, worker coordinates exposed to public search clients are masked:

```typescript
export function maskWorkerCoordinates(lat: number, lon: number) {
  // Deterministic 200m-500m centroid jitter
  const jitterLat = 0.002 * (Math.sin(lat * 100) > 0 ? 1 : -1);
  const jitterLon = 0.002 * (Math.cos(lon * 100) > 0 ? 1 : -1);
  return {
    latitude: Math.round((lat + jitterLat) * 10000) / 10000,
    longitude: Math.round((lon + jitterLon) * 10000) / 10000
  };
}
```
The exact coordinate is retained on the secure server solely for initial Haversine distance ranking.

---

## 6. Location Permission Lifecycle & Fallback Anchors

When a user opens the mobile application:
1. `LocationService` requests standard foreground GPS permissions (`expo-location`).
2. If granted, hardware GPS latitude and longitude are acquired with high accuracy.
3. If permission is denied or device GPS is unavailable, the application immediately transitions to **Manual Fallback Mode** and provides prominent 1-tap anchors for major city centers:
   - **Connaught Place, Central Delhi** (`28.6315, 77.2167`)
   - **Saket, South Delhi** (`28.5244, 77.2173`)
   - **Karol Bagh, West Delhi** (`28.6517, 77.1906`)
   - **Mayur Vihar, East Delhi** (`28.6083, 77.2965`)
   - **Vaishali Nagar, Jaipur** (`26.9075, 75.7483`)
   - **Bandra West, Mumbai** (`19.0596, 72.8295`)
