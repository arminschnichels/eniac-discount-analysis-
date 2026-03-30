# Data Quality Notes

## Issues Identified and Resolved

### 1. Corrupted Prices in Orderlines (unit_price)
- **Problem:** 36,169 rows (12.3%) had values like "1.137.99" with multiple decimal points
- **Decision:** - Corrupted price values in orderlines identified and removed — entire affected orders excluded to prevent misleading revenue calculations

### 2. Corrupted Prices in Products (price)
- **Problem:** 431 rows (2.23%) had prices with multiple decimal points
- **Decision:** Flagged as corrupted, excluded from price comparison analysis
- **Impact:** Minimal — less than 2.5% of product catalog

### 3. Missing Values in Orders (total_paid)
- **Problem:** 5 rows had null total_paid values
- **Decision:** Dropped rows — 0.002% of data, negligible impact

### 4. Duplicate Products
- **Problem:** 8,746 duplicate rows in products table
- **Decision:** Dropped duplicates, retaining first occurrence

### 5. Zero-Price Orders
- **Problem:** 524 completed orders had unit_sale_price = 0
- **Decision:** Excluded from discount erosion calculations
- **Rationale:** Division by zero prevents margin % calculation; these represent promotional/sample items with no revenue
- **Impact:** 0.35% of completed orders excluded, €0 revenue impact

### 6. Zero Total_Paid on Completed Orders
- **Problem:** 16 completed orders show total_paid = 0.00
- **Likely cause:** Missing payment data or fully comped orders
- **Decision:** Retained in order count analysis, excluded from revenue calculations

## Methodology Decisions

### Order Filtering
Only orders with state = "Completed" were included in discount and revenue analysis.
Shopping Basket, Pending, Place Order, and Cancelled states were excluded.

### Discount Definition
An order is classified as "discounted" if ANY orderline in that order has unit_price < products.price.
An order is classified as "full price" only if ALL orderlines are at full price.

### Margin % Calculation
Calculated as SUM(margin) / SUM(revenue), NOT as AVG(margin_percentage).
Averaging percentages incorrectly weights small and large orders equally.

### Holiday Period Definition
November 2017 – January 2018 defined as the holiday period.
Baseline comparison uses February–October 2017 + February 2018 to avoid circular comparison.
