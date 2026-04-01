# Scenario: Critical Stockout

**Inventory Item:** SKU-1001 — Heavy Duty Stretch Wrap
**Agent Triggered:** CriticalStockAgent
**Expected Sheet Change:** `status` updated to `CRITICAL`, `last_checked` updated, `notes` updated

---

## Inventory State

| Field | Value |
|---|---|
| sku | SKU-1001 |
| product_name | Heavy Duty Stretch Wrap |
| category | Packaging |
| current_stock | 0 |
| reorder_point | 100 |
| max_stock | 500 |
| supplier_name | PacSupply Co. |
| unit_cost | $12.50 |
| status | CRITICAL *(set by agent)* |

---

## What the Analysis Agent Sees

The Analysis Agent receives this inventory row and evaluates:

- `current_stock = 0` → qualifies as CRITICAL (stock fully depleted)
- `stock_status = CRITICAL`
- `recommended_order_qty = 500` (max_stock − current_stock)
- `confidence = 0.99`
- `urgency_note = "Stock fully depleted — immediate reorder required"`

---

## What CriticalStockAgent Does

1. Calls **Get Inventory Row (Critical)** — reads the full row for SKU-1001
2. Calls **Update Inventory Row** — writes the following changes:

| Column | New Value |
|---|---|
| `status` | CRITICAL |
| `last_checked` | *(current run timestamp)* |
| `notes` | Stock fully depleted — immediate reorder required |

---

## What CriticalStockAgent Does NOT Change

- `row_number`
- `sku`
- `product_name`
- `category`
- `current_stock` *(the agent does not fabricate stock — only humans update this)*
- `reorder_point`
- `max_stock`
- `supplier_name`
- `unit_cost`

---

## Verification

After running the workflow, open your Inventory sheet and confirm:
- [ ] SKU-1001 `status` is now `CRITICAL`
- [ ] SKU-1001 `last_checked` shows today's run timestamp
- [ ] SKU-1001 `notes` contains a short explanation of the stockout
- [ ] All other columns for SKU-1001 are unchanged
- [ ] No new row was written to the Purchase Orders sheet for this item *(CriticalStockAgent only flags; it does not create POs)*
