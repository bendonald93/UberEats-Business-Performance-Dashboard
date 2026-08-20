# 🛵 UberEats Interactive Business & Performance Dashboard

An end-to-end Power BI analytics solution analyzing 5,000 UberEats orders to deliver actionable insights on revenue drivers, delivery efficiency, and order completion performance.

## 📊 Executive Overview

![Dashboard Preview](screenshots/overview.png)

### Key Metrics Tracked
* **Total Revenue & Completed Revenue**: Financial breakdown across order statuses.
* **Order Completion Rate**: Ratio of completed vs. refunded/cancelled orders.
* **Fulfillment Efficiency**: Average delivery time correlated with customer ratings.
* **Category & Regional Performance**: Highest performing cities and food categories.

---

## 🛠️ Data Model & DAX Architecture

This dashboard utilizes a **Star Schema** centered on the `ubereats_data` fact table linked to a custom `Dim_Date` dimension table.

### Primary DAX Measures
```dax
// Total Revenue
Total Revenue = SUM ( 'ubereats_data'[order_value] )

// Completion Rate
Completion Rate = 
DIVIDE ( 
    CALCULATE ( COUNTROWS('ubereats_data'), 'ubereats_data'[order_status] = "Completed" ), 
    COUNTROWS('ubereats_data'), 
    0 
)

// Average Delivery Time
Avg Delivery Time (mins) = AVERAGE ( 'ubereats_data'[delivery_time_mins] )
