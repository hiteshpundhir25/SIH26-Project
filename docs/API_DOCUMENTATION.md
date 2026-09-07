# Sahakari Seva — Backend REST API Specification

## 1. Overview & Architecture

The **Sahakari Seva API Layer** is an Express.js and TypeScript microservice providing real-time geolocation matching, booking lifecycle management, AI demand forecasting, and workforce supply-demand balancing.

- **Base URL**: `http://localhost:5001/api`
- **Protocol**: HTTP/1.1 REST JSON
- **Port**: `5001`
- **CORS**: Configured to support web (`localhost:5173`) and mobile Expo environments (`localhost:8081`, `exp://*`).
- **Dependencies**: Express 5, TypeScript 5, CORS, `ts-node`.

---

## 2. Endpoints Reference

### 2.1 System Health
#### `GET /api/health`
Returns the operational health and active capabilities of the backend engine.
- **Status**: `200 OK`
- **Response**:
```json
{
  "status": "healthy",
  "app": "Sahakari Seva Backend API",
  "timestamp": "2026-09-04T13:03:17.606Z",
  "capabilities": [
    "OpenStreetMap Haversine Geolocation Matching",
    "Bilingual English/Hindi Trade Catalog",
    "AI Time-Series Demand Forecasting",
    "Workforce Capacity Allocation Engine"
  ]
}
```

---

### 2.2 Geolocation Matching & Workers

#### `GET /api/workers/nearby`
Matches verified cooperative workers near a customer's coordinates using Haversine calculation and 5-factor scoring.
- **Query Parameters**:
  - `latitude` (number, required): Customer's latitude (e.g. `28.6315`).
  - `longitude` (number, required): Customer's longitude (e.g. `77.2167`).
  - `radius` (number, optional, default: `15`): Search radius in kilometers.
  - `service` (string, optional): Trade filter (e.g. `Electrical`, `Plumbing`).
  - `emergency` (boolean, optional): Set to `true` to require immediate availability.
- **Status**: `200 OK`
- **Response Sample**:
```json
{
  "success": true,
  "count": 4,
  "data": [
    {
      "id": "w0000000-0000-0000-0000-000000000001",
      "name": "Rajesh Sharma",
      "service": "Electrical",
      "skills": ["House Wiring", "MCB Tripping Fix", "Fan Installation"],
      "latitude": 28.6315,
      "longitude": 77.2167,
      "distance_km": 0.0,
      "within_radius": true,
      "rating": 4.9,
      "total_jobs": 142,
      "hourly_rate": 249,
      "availability": "available",
      "verification": "verified",
      "matchScore": 96,
      "breakdown": {
        "distanceScore": 40,
        "availabilityScore": 20,
        "ratingScore": 19,
        "reliabilityScore": 8,
        "serviceMatchScore": 10
      },
      "approximate_location": {
        "area": "Central & South Delhi",
        "city": "New Delhi",
        "pincode": "110001",
        "latitude": 28.6335,
        "longitude": 77.2187
      }
    }
  ]
}
```

#### `GET /api/workers`
Returns all verified cooperative workers.
- **Status**: `200 OK`

#### `GET /api/workers/:id`
Returns full profile details for a specific worker by ID.
- **Status**: `200 OK` (or `404 Not Found`)

#### `PATCH /api/workers/:id/location`
Updates a worker's live GPS coordinates and service radius.
- **Request Body**:
```json
{
  "latitude": 28.6320,
  "longitude": 77.2190,
  "service_radius_km": 15
}
```
- **Status**: `200 OK`

#### `PATCH /api/workers/:id/availability`
Toggles worker operational availability status.
- **Request Body**:
```json
{
  "status": "available" // or "busy", "offline"
}
```
- **Status**: `200 OK`

---

### 2.3 Trade Categories
#### `GET /api/services`
Returns all trade categories with English and Hindi titles and descriptions.
- **Status**: `200 OK`
- **Response Sample**:
```json
{
  "success": true,
  "data": [
    {
      "id": "s0000000-0000-0000-0000-000000000001",
      "name": "Electrical",
      "name_hi": "विद्युत सेवाएं (इलेक्ट्रीशियन)",
      "description": "Wiring repair, short-circuits, MCB fixes, switches, fans, lighting.",
      "description_hi": "वायरिंग मरम्मत, शॉर्ट-सर्किट, एमसीबी ट्रिपिंग, स्विच और लाइट फिटिंग।",
      "icon": "Zap",
      "base_price": 249.00,
      "emergency_available": true,
      "active": true
    }
  ]
}
```

---

### 2.4 Bookings Lifecycle

#### `GET /api/bookings`
Returns bookings filtered by `customer_id` or `worker_id`.
- **Status**: `200 OK`

#### `POST /api/bookings`
Creates a new cooperative service booking.
- **Request Body**:
```json
{
  "customer_id": "cust-demo",
  "worker_id": "w0000000-0000-0000-0000-000000000001",
  "service_category_id": "s0000000-0000-0000-0000-000000000001",
  "booking_date": "2026-09-05",
  "booking_time": "14:00",
  "address": "Flat 402, Connaught Place",
  "city": "New Delhi",
  "state": "Delhi",
  "pincode": "110001",
  "service_description": "Ceiling fan sparking and switchboard loose.",
  "is_emergency": false,
  "estimated_amount": 349
}
```
- **Status**: `201 Created`

#### `PATCH /api/bookings/:id/status`
Updates the booking lifecycle state (`pending` $\rightarrow$ `accepted` $\rightarrow$ `in_progress` $\rightarrow$ `completed` or `cancelled`).
- **Request Body**:
```json
{
  "status": "in_progress"
}
```
- **Status**: `200 OK`

---

### 2.5 AI Demand Forecasting & Workforce Allocation

#### `GET /api/forecast`
Generates time-series demand projections using ordinary least squares (OLS) linear regression and day-of-week cadence over 160+ historical events.
- **Status**: `200 OK`
- **Response Structure**:
  - `zone_forecasts`: Array of zone demand records with `predicted_demand`, `confidence_score` (0.0 to 1.0), `confidence_lower_bound`, `confidence_upper_bound`, and `is_baseline_fallback`.
  - `weekly_demand_curve`: Array of daily forecasts for the next 7 days.
  - `total_historical_events`: Total number of training events analyzed (e.g. `160`).

#### `GET /api/allocation/recommendations`
Evaluates predicted demand vs active workers per zone and categorizes clusters into:
- `understaffed`: Deficit exists $\rightarrow$ `recommended_mobilization > 0`.
- `balanced`: Supply meets demand within healthy tolerance.
- `overstaffed`: Surplus exists $\rightarrow$ potential donors for standby rebalancing.
- **Status**: `200 OK`

---

### 2.6 Federation Statistics
#### `GET /api/stats/admin`
Returns aggregated federation metrics: verified worker count, active bookings, completed jobs, and total welfare corpus balance.
- **Status**: `200 OK`
