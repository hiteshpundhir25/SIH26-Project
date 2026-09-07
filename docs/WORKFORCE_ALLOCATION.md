# Sahakari Seva — Workforce Allocation & Supply-Demand Balancing Engine

## 1. Cooperative Workforce Mission

Unlike corporate gig aggregators that exploit driver/worker oversupply to suppress wages, **Sahakari Seva** optimizes workforce distribution to maximize **fair living earnings** and **service availability**. Every booking follows the same visible allocation used by the payment engine: **85%** direct worker pay, **10%** to the shared Federation Welfare & Insurance Corpus, and **5%** for cooperative operations.

---

## 2. Supply-Demand Evaluation Model

The Workforce Allocation Engine (`backend/src/services/allocationEngine.ts`) bridges the output of the AI Demand Forecasting Engine with the active pool of verified cooperative workers.

### 2.1 Daily Worker Capacity Heuristic
Each verified full-time cooperative trade professional has a standard service capacity of:
$$C_{\text{worker}} = 3\text{ service jobs / day}$$

Available capacity in zone $z$ for service $s$:
$$\text{Supply Capacity}(z, s) = W_{\text{active}}(z, s) \times C_{\text{worker}}$$

---

## 3. Zone Classification Logic

For each zone and trade cluster, the net shortage or surplus is calculated:
$$\Delta = \text{Supply Capacity}(z, s) - \text{Predicted Demand}(z, s)$$

| Classification | Condition | Priority | System Action |
|---|---|---|---|
| **`understaffed`** | $\Delta < -2$ | `urgent` / `high` | Recommend immediate mobilization of standby workers from neighboring surplus zones. |
| **`balanced`** | $-2 \le \Delta \le 3$ | `normal` | Maintain standard cooperative dispatch; healthy wait times expected. |
| **`overstaffed`** | $\Delta > 3$ | `low` | Flag zone as potential donor cluster for cross-zone dispatch. |

---

## 4. Recommended Mobilization Formula

When a zone experiences a demand deficit ($\Delta < 0$):

$$\text{Recommended Mobilization} = \left\lceil \frac{|\Delta|}{C_{\text{worker}}} \right\rceil$$

### Example Scenario:
In **Delhi - Connaught Place / Central**:
- Predicted Electrical Demand: $14\text{ jobs}$
- Active Verified Electricians: $2\text{ workers}$ ($2 \times 3 = 6\text{ jobs capacity}$)
- Deficit $\Delta = 6 - 14 = -8\text{ jobs}$
- **Recommended Mobilization**: $\lceil 8 / 3 \rceil = \mathbf{3\text{ Standby Workers}}$.

---

## 5. Standby Mobilization Workflow

```mermaid
sequenceDiagram
    participant Admin as Federation Admin (Mobile/Web)
    participant Engine as Allocation Engine (Port 5001)
    participant Standby as Cooperative Standby Pool
    
    Admin->>Engine: GET /api/allocation/recommendations
    Engine-->>Admin: Returns 5 zone statuses (2 understaffed, 2 balanced, 1 surplus)
    Admin->>Admin: Reviews Connaught Place (+3 Mobilization needed)
    Admin->>Engine: Trigger "Mobilize Standby Workers"
    Engine->>Standby: Dispatches SMS / Push notifications to off-duty members in neighboring Karol Bagh
    Engine-->>Admin: Mobilization Confirmed; Deficit Resolved
```

---

## 6. Protection Against Algorithmic Coercion

Sahakari Seva implements cooperative governance safeguards:
1. **Voluntary Acceptance**: Mobilization calls are incentives with standard hourly tariffs, never punitive.
2. **Zero Algorithmic Demotions**: Workers who decline standby mobilization incur zero score penalties.
3. **Transparent Criteria**: Every recommendation is accompanied by `recommendation_notes` explaining why the surge was forecasted.
