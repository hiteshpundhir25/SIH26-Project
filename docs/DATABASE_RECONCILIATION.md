# Sahakari Seva — Database Reconciliation & Schema Alignment

## 1. Executive Summary

This document audits the relational database schema implemented in Supabase / PostgreSQL (`supabase/migrations/`) against the Master Product Architecture and the TypeScript data models in `src/types/index.ts` and `mobile/src/types.ts`.

**Audit Outcome**: All required database tables, foreign keys, constraints, spatial columns, and Row Level Security (RLS) policies are fully defined across the two migrations without duplicate tables or conflicting field names.

---

## 2. Table Reconciliation Matrix

| Table Name | Schema Migration File | Status | Relationships | Key Attributes |
|---|---|---|---|---|
| **`cooperatives`** | `20260901000001_initial_schema.sql` | Aligned | Primary entity for federations | `id`, `name`, `registration_number`, `phone`, `email`, `address`, `city`, `welfare_pool_balance` |
| **`profiles`** | `20260901000001_initial_schema.sql` | Aligned | Linked with Auth (`auth_user_id`) | `id`, `full_name`, `email`, `phone`, `role` (`customer`, `worker`, `admin`), `city`, `pincode`, `language` |
| **`service_categories`** | `20260901000001_initial_schema.sql` | Aligned | Catalog entity | `id`, `name`, `name_hi`, `description`, `description_hi`, `icon`, `base_price`, `emergency_available` |
| **`workers`** | `20260901000001` + `20260904000002` | Aligned | References `profiles`, `cooperatives` | `id`, `worker_code`, `skill_category`, `experience_years`, `hourly_or_base_rate`, `availability_status`, `verification_status`, `latitude`, `longitude`, `service_radius_km` |
| **`bookings`** | `20260901000001_initial_schema.sql` | Aligned | References `profiles`, `workers`, `service_categories` | `id`, `booking_code`, `booking_date`, `booking_time`, `address`, `service_description`, `estimated_amount`, `final_amount`, `is_emergency`, `status`, `payment_status` |
| **`ratings`** | `20260901000001_initial_schema.sql` | Aligned | References `bookings`, `profiles`, `workers` | `id`, `booking_id` (UNIQUE), `rating` (1–5), `feedback`, `created_at` |
| **`payments`** | `20260901000001_initial_schema.sql` | Aligned | References `bookings`, `profiles`, `workers` | `id`, `booking_id`, `amount`, `payment_method` (`upi`, `card`, `demo`), `transaction_reference`, `status` |
| **`invoices`** | `20260901000001_initial_schema.sql` | Aligned | References `bookings`, `profiles`, `workers` | `id`, `booking_id` (UNIQUE), `invoice_number`, `subtotal`, `platform_fee` (5%), `cooperative_share` (10%), `worker_amount` (85%), `tax`, `total_amount` |
| **`welfare`** | `20260901000001_initial_schema.sql` | Aligned | References `workers` | `id`, `worker_id`, `welfare_scheme`, `enrollment_status`, `contribution_balance`, `insurance_status`, `policy_reference` |
| **`notifications`** | `20260901000001_initial_schema.sql` | Aligned | References `profiles` | `id`, `user_id`, `type`, `title`, `message`, `read`, `action_url` |
| **`demand_events`** | `20260904000002_mobile_geo_ai_forecasting.sql` | Aligned | Historical telemetry for ML model | `id`, `event_timestamp`, `location_zone`, `service_category`, `demand_weight`, `weather_condition`, `is_emergency` |
| **`demand_forecasts`** | `20260904000002_mobile_geo_ai_forecasting.sql` | Aligned | Output of OLS regression engine | `id`, `location_zone`, `service_category`, `forecast_date`, `predicted_demand`, `confidence_score`, `confidence_lower_bound`, `confidence_upper_bound`, `is_baseline_fallback` |
| **`workforce_allocations`** | `20260904000002_mobile_geo_ai_forecasting.sql` | Aligned | Output of capacity balancing engine | `id`, `location_zone`, `service_category`, `target_date`, `predicted_demand`, `available_workers`, `shortage_or_surplus`, `allocation_status`, `recommended_mobilization` |

---

## 3. Duplicate Tables Analysis
- **Finding**: **0 duplicate tables detected.**
- Both web and mobile use the same table naming conventions (`service_categories`, `workers`, `bookings`, `ratings`, `payments`, `invoices`, `welfare`).
- Spatial coordinates and service radius are consolidated directly onto the primary `workers` table rather than stored in a separate table, avoiding unnecessary joins.

---

## 4. Foreign Key Integrity & Constraints
- All relational links enforce referential integrity:
  - `bookings.customer_id` $\rightarrow$ `profiles.id` (`ON DELETE CASCADE`)
  - `bookings.worker_id` $\rightarrow$ `workers.id` (`ON DELETE RESTRICT`)
  - `ratings.booking_id` $\rightarrow$ `bookings.id` (`UNIQUE`, `ON DELETE CASCADE`)
  - `invoices.booking_id` $\rightarrow$ `bookings.id` (`UNIQUE`, `ON DELETE CASCADE`)
  - `payments.booking_id` $\rightarrow$ `bookings.id` (`ON DELETE CASCADE`)
  - `welfare.worker_id` $\rightarrow$ `workers.id` (`ON DELETE CASCADE`)

---

## 5. Row Level Security (RLS) & Privacy Review
- **Public Reads**: Allowed for `service_categories` (active only), `cooperatives`, and `workers` (only `verification_status = 'verified'`).
- **Worker Coordinates**: Public APIs mask worker coordinates with $\pm 500\text{m}$ centroid jitter. Only the authenticated worker can update their own GPS position.
- **Admin Authorizations**: Worker verification status transitions (`pending` $\rightarrow$ `verified` or `rejected`) require administrative role permissions.
- **Certificates Storage**: Uploaded ITI certificates and government IDs are restricted to the worker and federation administrators.

---

## 6. Seed Data Reconciliation
- **Workers**: 10 real worker records populated across Delhi, Jaipur, and Mumbai with accurate GPS coordinates.
  - 9 verified workers in active search.
  - 1 pending worker (**Arjun Meena**, WRK-DEL-0110) specifically reserved for demonstrating the Admin Worker Verification flow.
- **Demand Telemetry**: 160 timestamped events across 50 days powering time-series OLS regression and moving average forecasts.
