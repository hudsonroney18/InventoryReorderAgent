# Scenario: Reorder Needed

**Inventory Item:** SKU-1002 — Corrugated Shipping Boxes (12x12x12)
**Agent Triggered:** ReorderAgent
**Expected Sheet Changes:** New PO row appended to Purchase Orders sheet; inventory row `status` updated to `REORDER_PLACED`

---

## Inventory State

| Field | Value |
|---|---|
| sku | SKU-1002 |
| product_name | Corrugated Shipping Boxes (12x12x12) |
| category | Packaging |
| current_stock | 35 |
| reorder_point | 150 |
| max_stock | 600 |
| supplier_name | BoxWorld Inc. |
| unit_cost | $3.75 |
| status | REORDER *(identified by Analysis Agent)* |

---

## What the Analysis Agent Sees

The Analysis Agent receives this row and evaluates:

- `current_stock = 35`, `reorder_point = 150` → stock is below reorder point but above zero
- `stock_status = REORDER`
- `recommended_order_qty = 565` (max_stock − current_stock = 600 − 35)
- `confidence = 0.97`
- `urgency_note = "Stock 77% below reorder point — standard reorder cycle"`

---

## What ReorderAgent Does

ReorderAgent executes three steps:

### Step 1 — Read Inventory Row
Calls **Get Inventory Row (Reorder)** to fetch the full row for SKU-1002, confirming `supplier_name`, `unit_cost`, and `max_stock`.

### Step 2 — Create Purchase Order
Calls **Append to Purchase Orders** to write one new row to the Purchase Orders sheet:

| Column | Value |
|---|---|
| `po_number` | PO-SKU1002-04012026 |
| `created_at` | *(current run timestamp)* |
| `sku` | SKU-1002 |
| `product_name` | Corrugated Shipping Boxes (12x12x12) |
| `supplier_name` | BoxWorld Inc. |
| `order_qty` | 565 |
| `unit_cost` | 3.75 |
| `total_cost` | 2118.75 |
| `status` | PENDING |
| `expected_delivery` | *(7 days from today)* |
| `notes` | Stock 77% below reorder point — standard reorder cycle |

### Step 3 — Update Inventory Row
Calls **Update Inventory Status** to update only these columns on the SKU-1002 inventory row:

| Column | New Value |
|---|---|
| `status` | REORDER_PLACED |
| `last_checked` | *(current run timestamp)* |
| `notes` | PO PO-SKU1002-04012026 created — pending fulfillment |

---

## What ReorderAgent Does NOT Change in Inventory

- `row_number`, `sku`, `product_name`, `category`
- `current_stock` *(updated only when stock is physically received)*
- `reorder_point`, `max_stock`, `supplier_name`, `unit_cost`

---

## Verification

After running the workflow:
- [ ] A new row appears in the **Purchase Orders** sheet for SKU-1002
- [ ] The PO row has correct `order_qty`, `total_cost`, and `status = PENDING`
- [ ] The `expected_delivery` is approximately 7 days from today
- [ ] The SKU-1002 inventory row shows `status = REORDER_PLACED`
- [ ] SKU-1002 `last_checked` shows today's run timestamp
- [ ] All other inventory columns for SKU-1002 are unchanged
