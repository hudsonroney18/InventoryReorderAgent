# Test Scenarios

This folder contains three test scenarios that together cover every agent path in the workflow. Each scenario describes a simulated state of the inventory sheet that will trigger a specific agent.

## The Three Paths

| File | Agent Triggered | Operation | What Happens |
|------|----------------|-----------|--------------|
| `critical_stockout.md` | CriticalStockAgent | **Flag Critical** | A SKU with zero stock is identified. The agent updates its `status` to `CRITICAL` and writes a note in the inventory sheet. |
| `reorder_needed.md` | ReorderAgent | **Create PO + Update Status** | A SKU at or below reorder point is identified. The agent appends a new row to the Purchase Orders sheet and updates the inventory row to `REORDER_PLACED`. |
| `healthy_stock.md` | *(no agent)* | **Pass-through** | A SKU with healthy stock is classified as `OK`. No sheet is modified. This tests that the workflow correctly ignores items that need no action. |

## How to Use These

The seed data in `data/inventory.csv` already includes items in all three states. When you import the CSV and run the workflow:

1. **SKU-1001** and **SKU-1008** (stock = 0) will route to `CriticalStockAgent`
2. **SKU-1004** (stock = 8, reorder point = 75, less than 10% of threshold) will also route to `CriticalStockAgent`
3. **SKU-1002**, **SKU-1005**, and **SKU-1007** (stock below reorder point) will route to `ReorderAgent`
4. **SKU-1003** and **SKU-1006** (stock above reorder point) will pass through as `OK` — no action taken

Read each scenario file below to understand the expected behavior before you run the workflow.

> For full end-to-end testing, import the seed data, run the workflow once, then check both sheets for the expected changes.
