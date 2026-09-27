ecommerce.bronze.category_name_translation# Olist E-Commerce Dataset — Data Dictionary

## 1. Dataset Overview

### Source
Olist Brazilian E-Commerce Dataset

### Purpose
This dataset is used as the source data for the E-Commerce Analytics
Lakehouse project.

### Source Tables

| Table | Description |
|---|---|
| customers | Customer information |
| sellers | Seller information |
| orders | Order information and order lifecycle |
| order_items | Items purchased in each order |
| products | Product information |
| payments | Payment information |
| reviews | Customer review information |
| category_translation | Product category translations |

---

# 2. Orders

## Table Overview

**Purpose:**  
Contains information about customer orders and their lifecycle.

**Row Count:**  
99,441

**Column Count:**  
8

## Grain

One row represents one customer order.

## Business Key

`order_id`

## Foreign Keys

`customer_id`

References:

`customers.customer_id`

## Referential Integrity

| Relationship | Unmatched Records | Status |
|---|---:|---|
| orders.customer_id → customers.customer_id | 0 | Passed |

## Schema

| Column | Data Type | Description |
|---|---|---|
| order_id | string | Unique order identifier |
| customer_id | string | Customer identifier |
| order_status | string | Order status |
| order_purchase_timestamp | string | Order purchase timestamp |
| order_approved_at | string | Order approval timestamp |
| order_delivered_carrier_date | string | Date order was handed to carrier |
| order_delivered_customer_date | string | Date order was delivered to customer |
| order_estimated_delivery_date | string | Estimated delivery date |

## Null Analysis

| Column | Null Count |
|---|---:|
| order_id | 0 |
| customer_id | 0 |
| order_status | 0 |
| order_purchase_timestamp | 0 |
| order_approved_at | 160 |
| order_delivered_carrier_date | 1,783 |
| order_delivered_customer_date | 2,965 |
| order_estimated_delivery_date | 0 |

## Duplicate Analysis

**Duplicate `order_id` records:** 0

**Conclusion:**  
`order_id` uniquely identifies each record in the orders table.

## Data Quality Observations

- `order_id` contains no NULL values.
- `customer_id` contains no NULL values.
- Delivery-related timestamp columns contain NULL values.
- NULL delivery timestamps require business-context analysis before deciding how they should be handled.

---

# Customers

## Table Overview

**Purpose:** Contains customer information for the e-commerce dataset.

**Row Count:** 99,441  
**Column Count:** 5

## Grain

One row represents **one customer record**.

## Business Key

`customer_id`

`customer_id` uniquely identifies each record in the customers table.

## Alternate Key

`customer_unique_id`

`customer_unique_id` is also unique in the source dataset and can be used as an alternate identifier for the customer.

## Foreign Keys

**None identified.**

The `customers` table does not contain a foreign key to another table.

> **Note:** `customer_id` is referenced by the `orders` table, but it is not a foreign key in the `customers` table.

## Date/Timestamp Columns

**None.**

## Schema

| Column | Data Type | Description |
|---|---|---|
| `customer_id` | string | Unique identifier for the customer record |
| `customer_unique_id` | string | Unique identifier for the customer |
| `customer_zip_code_prefix` | string | Customer ZIP code prefix |
| `customer_city` | string | Customer city |
| `customer_state` | string | Customer state |

## Null Analysis

| Column | Null Count |
|---|---:|
| `customer_id` | 0 |
| `customer_unique_id` | 0 |
| `customer_zip_code_prefix` | 0 |
| `customer_city` | 0 |
| `customer_state` | 0 |

**Conclusion:** No NULL values were found in any column.

## Duplicate Analysis

### `customer_id`

**Total rows:** 99,441  
**Distinct `customer_id` values:** 99,441  
**Duplicate records:** 0

**Conclusion:** `customer_id` uniquely identifies every record in the customers table.

### `customer_unique_id`

**Total rows:** 99,441  
**Distinct `customer_unique_id` values:** 99,441  
**Duplicate records:** 0

**Conclusion:** `customer_unique_id` is also unique in the source dataset.

## Data Quality Observations

- No NULL values were found in any column.
- No duplicate `customer_id` values were found.
- No duplicate `customer_unique_id` values were found.
- `customer_id` is used as the business key.
- `customer_unique_id` is recorded as an alternate key.
- No foreign keys were identified in this table.
- No date/timestamp columns are present.
- Data types are currently represented as strings in the raw dataset.

---

# Sellers

## Table Overview

**Purpose:** Contains information about sellers participating in the e-commerce platform.

**Row Count:** 3,095  
**Column Count:** 4

## Grain

One row represents **one seller**.

## Business Key

`seller_id`

`seller_id` uniquely identifies each seller record in the sellers table.

## Alternate Key

**None identified.**

## Foreign Keys

**None identified.**

The `sellers` table does not contain a foreign key to another table.

> **Note:** `seller_id` is referenced by the `order_items` table, but it is not a foreign key in the `sellers` table.

## Date/Timestamp Columns

**None.**

## Schema

| Column | Data Type | Description |
|---|---|---|
| `seller_id` | string | Unique identifier for the seller |
| `seller_zip_code_prefix` | string | Seller ZIP code prefix |
| `seller_city` | string | Seller city |
| `seller_state` | string | Seller state |

## Null Analysis

| Column | Null Count |
|---|---:|
| `seller_id` | 0 |
| `seller_zip_code_prefix` | 0 |
| `seller_city` | 0 |
| `seller_state` | 0 |

**Conclusion:** No NULL values were found in any column.

## Duplicate Analysis

### `seller_id`

**Total rows:** 3,095  
**Distinct `seller_id` values:** 3,095  
**Duplicate records:** 0

**Conclusion:** `seller_id` uniquely identifies every record in the sellers table.

## Data Quality Observations

- No NULL values were found in any column.
- No duplicate `seller_id` values were found.
- `seller_id` is used as the business key.
- No alternate key was identified.
- No foreign keys were identified in this table.
- `seller_id` is referenced by the `order_items` table.
- No date/timestamp columns are present.
- Data types are currently represented as strings in the raw dataset.
---

# Order Items

## Table Overview

**Purpose:** Contains information about individual items purchased within customer orders.

**Row Count:** 112,650  
**Column Count:** 7

## Grain

One row represents **one product item within an order**.

## Business Key

**Composite Business Key:**

`order_id + order_item_id + product_id`

The combination of `order_id`, `order_item_id`, and `product_id` uniquely identifies an order-item record.

## Foreign Keys

| Column | References |
|---|---|
| `order_id` | `orders.order_id` |
| `product_id` | `products.product_id` |
| `seller_id` | `sellers.seller_id` |

## Date/Timestamp Columns

`shipping_limit_date`

This column represents the date/time limit by which the seller should ship the item.

## Schema

| Column | Data Type | Description |
|---|---|---|
| `order_id` | string | Identifier of the order |
| `order_item_id` | string | Item sequence/identifier within an order |
| `product_id` | string | Identifier of the purchased product |
| `seller_id` | string | Identifier of the seller fulfilling the item |
| `shipping_limit_date` | string | Date/time limit for shipping the item |
| `price` | string | Price of the item |
| `freight_value` | string | Freight/shipping cost associated with the item |

## Null Analysis

| Column | Null Count |
|---|---:|
| `order_id` | 0 |
| `order_item_id` | 0 |
| `product_id` | 0 |
| `seller_id` | 0 |
| `shipping_limit_date` | 0 |
| `price` | 0 |
| `freight_value` | 0 |

**Conclusion:** No NULL values were found in any column.

## Duplicate Analysis

**Total rows:** 112,650  
**Distinct rows:** 112,650  
**Duplicate rows:** 0

**Conclusion:** No duplicate records were found in the `order_items` table.

## Data Quality Observations

- No NULL values were found in any column.
- No duplicate records were found.
- The combination of `order_id`, `order_item_id`, and `product_id` is used as the composite business key.
- `order_id` references the `orders` table.
- `product_id` references the `products` table.
- `seller_id` references the `sellers` table.
- `shipping_limit_date` is a date/timestamp column and is currently stored as a string in the raw dataset.
- `price` and `freight_value` are currently stored as strings and will require appropriate numeric data types during the Silver-layer transformation.
---

# Products

## Table Overview

**Purpose:** Contains information about the products available on the e-commerce platform.

**Row Count:** 32,951  
**Column Count:** 9

## Grain

One row represents **one product**.

## Business Key

`product_id`

`product_id` uniquely identifies each record in the products table.

## Alternate Key

**None identified.**

## Foreign Keys

**None identified.**

The `products` table does not contain a foreign key to another table.

## Relationships

### Category Translation

`products.product_category_name → category_translation.product_category_name`

The `product_category_name` column was investigated for its relationship with the category translation table.

**Observation:**

- Some `product_category_name` values are NULL.
- Some non-NULL category values do not have a corresponding entry in the category translation table.
- The relationship requires further investigation during the data quality phase.

> **Note:** NULL category values and unmatched category values are treated as separate data-quality observations.

## Date/Timestamp Columns

**None.**

## Schema

| Column | Data Type | Description |
|---|---|---|
| `product_id` | string | Unique identifier for the product |
| `product_category_name` | string | Product category name |
| `product_name_lenght` | string | Length of the product name |
| `product_description_lenght` | string | Length of the product description |
| `product_photos_qty` | string | Number of product photos |
| `product_weight_g` | string | Product weight in grams |
| `product_length_cm` | string | Product length in centimeters |
| `product_height_cm` | string | Product height in centimeters |
| `product_width_cm` | string | Product width in centimeters |

## Null Analysis

| Column | Null Count |
|---|---:|
| `product_id` | 0 |
| `product_category_name` | 610 |
| `product_name_lenght` | 610 |
| `product_description_lenght` | 610 |
| `product_photos_qty` | 610 |
| `product_weight_g` | 2 |
| `product_length_cm` | 2 |
| `product_height_cm` | 2 |
| `product_width_cm` | 2 |

**Conclusion:** NULL values are present in several product attributes.

## Duplicate Analysis

### `product_id`

**Total rows:** 32,951  
**Distinct `product_id` values:** 32,951  
**Duplicate `product_id` records:** 0

**Conclusion:** `product_id` uniquely identifies every record in the products table.

## Data Quality Observations

- No NULL values were found in `product_id`.
- No duplicate `product_id` values were found.
- `product_id` is used as the business key.
- `product_category_name` contains **610 NULL values**.
- `product_name_lenght` contains **610 NULL values**.
- `product_description_lenght` contains **610 NULL values**.
- `product_photos_qty` contains **610 NULL values**.
- `product_weight_g` contains **2 NULL values**.
- `product_length_cm` contains **2 NULL values**.
- `product_height_cm` contains **2 NULL values**.
- `product_width_cm` contains **2 NULL values**.
- `product_category_name` contains both NULL and non-NULL values.
- The relationship between `product_category_name` and the category translation table requires further investigation.
- NULL category values and unmatched category values are separate data-quality observations.
- No date/timestamp columns are present.
- Data types are currently represented as strings in the raw dataset.
- Product measurements and quantity fields will require appropriate numeric data types during Silver-layer transformation.
---

# Order Payments

## Table Overview

**Purpose:** Contains information about payments associated with customer orders.

**Row Count:** 103,886  
**Column Count:** 5

## Grain

One row represents **one payment associated with an order**.

## Business Key

**Composite Business Key:**

`order_id + payment_sequential`

The combination of `order_id` and `payment_sequential` uniquely identifies each payment record.

## Foreign Keys

| Column | References |
|---|---|
| `order_id` | `orders.order_id` |

## Date/Timestamp Columns

**None.**

## Schema

| Column | Data Type | Description |
|---|---|---|
| `order_id` | string | Identifier of the order associated with the payment |
| `payment_sequential` | string | Sequential number identifying the payment within an order |
| `payment_type` | string | Type of payment used for the order |
| `payment_installments` | string | Number of installments used for the payment |
| `payment_value` | string | Value of the payment |

## Null Analysis

| Column | Null Count |
|---|---:|
| `order_id` | 0 |
| `payment_sequential` | 0 |
| `payment_type` | 0 |
| `payment_installments` | 0 |
| `payment_value` | 0 |

**Conclusion:** No NULL values were found in any column.

## Duplicate Analysis

**Total rows:** 103,886  
**Distinct rows:** 103,886  
**Duplicate rows:** 0

**Conclusion:** No duplicate records were found in the `order_payments` table.

## Data Quality Observations

- No NULL values were found in any column.
- No duplicate records were found.
- The combination of `order_id` and `payment_sequential` is used as the composite business key.
- `order_id` references `orders.order_id`.
- No date/timestamp columns are present.
- `payment_installments` and `payment_value` are currently represented as strings in the raw dataset and will require appropriate numeric data types during Silver-layer transformation.

---
# Order Reviews

## Table Overview

**Purpose:** Contains customer review information associated with orders.

**Row Count:** 104,162  
**Column Count:** 7

## Grain

**To be determined**

## Business Key

**No reliable business key identified yet.**

`review_id` was investigated as a potential identifier, but it contains 1 NULL value. Further investigation is required before finalizing the business key.

## Foreign Keys

`order_id`

References: `orders.order_id`

## Referential Integrity

### Relationship

`order_reviews.order_id → orders.order_id`

**Unmatched records:** 4,938

**Status:** Referential integrity issue identified.

The relationship was investigated, but unmatched records were identified. These records will be investigated further during the data quality phase.

> **Note:** NULL `order_id` values are treated separately from unmatched `order_id` values.

## Date/Timestamp Columns

| Column | Data Type |
|---|---|
| `review_creation_date` | string |
| `review_answer_timestamp` | string |

> **Note:** These columns are currently stored as strings in the raw dataset and will be converted to appropriate timestamp types during Silver-layer transformation.

## Schema

| Column | Data Type | Description |
|---|---|---|
| `review_id` | string | Identifier associated with the review |
| `order_id` | string | Identifier of the associated order |
| `review_score` | string | Customer rating score |
| `review_comment_title` | string | Title of the customer review |
| `review_comment_message` | string | Customer review message |
| `review_creation_date` | string | Date/time when the review was created |
| `review_answer_timestamp` | string | Date/time when the review was answered |

## Null Analysis

| Column | Null Count |
|---|---:|
| `review_id` | 1 |
| `order_id` | 2,236 |
| `review_score` | 2,380 |
| `review_comment_title` | 92,157 |
| `review_comment_message` | 63,079 |
| `review_creation_date` | 8,764 |
| `review_answer_timestamp` | 8,785 |

**Conclusion:** NULL values are present in multiple columns, particularly in the review comment fields.

## Duplicate Analysis

**Total rows:** 104,162  
**Distinct rows:** 104,077  
**Duplicate rows:** 85

**Conclusion:** 85 duplicate records were identified when comparing all columns.

## Data Quality Observations

- `review_id` contains 1 NULL value.
- `order_id` contains 2,236 NULL values.
- `review_score` contains 2,380 NULL values.
- `review_comment_title` contains 92,157 NULL values.
- `review_comment_message` contains 63,079 NULL values.
- `review_creation_date` contains 8,764 NULL values.
- `review_answer_timestamp` contains 8,785 NULL values.
- 85 duplicate records were identified across all columns.
- No reliable business key has been finalized yet.
- `order_id` is a potential foreign-key relationship to `orders.order_id`.
- 4,938 unmatched `order_id` records were identified during the referential-integrity check.
- NULL `order_id` values and unmatched `order_id` values are separate data-quality observations.
- `review_comment_title` and `review_comment_message` contain a high number of NULL values, which may be valid when customers do not provide written comments.
- `review_creation_date` and `review_answer_timestamp` are timestamp-related columns.
- All columns are currently represented as strings in the raw dataset.
- Appropriate data types will be applied during Silver-layer transformation.
---

# Product Category Name Translation

## Table Overview

**Purpose:** Contains English translations of the product category names.

**Row Count:** 71  
**Column Count:** 2

## Grain

One row represents **one product category translation**.

## Business Key

`product_category_name`

`product_category_name` uniquely identifies each category translation record.

## Foreign Keys

**None identified.**

This table acts as the referenced table for the `products.product_category_name` relationship.

## Relationships

### Product Category

`products.product_category_name → product_category_name_translation.product_category_name`

The `product_category_name` column in the products table references the `product_category_name` column in this table.

## Date/Timestamp Columns

**None.**

## Schema

| Column | Data Type | Description |
|---|---|---|
| `product_category_name` | string | Product category name in the source language |
| `product_category_name_english` | string | English translation of the product category name |

## Null Analysis

| Column | Null Count |
|---|---:|
| `product_category_name` | 0 |
| `product_category_name_english` | 0 |

**Conclusion:** No NULL values were found in any column.

## Duplicate Analysis

**Total rows:** 71  
**Distinct rows:** 71  
**Duplicate rows:** 0

**Conclusion:** No duplicate records were found in the `product_category_name_translation` table.

## Data Quality Observations

- No NULL values were found in any column.
- No duplicate records were found.
- `product_category_name` is used as the business key.
- `product_category_name` uniquely identifies each translation record.
- No foreign keys were identified in this table.
- `products.product_category_name` references `product_category_name_translation.product_category_name`.
- No date/timestamp columns are present.
- Data types are currently represented as strings in the raw dataset.
---
# 10. Geolocation

## Table Overview

**Purpose:** Contains geographic information including ZIP code prefixes, latitude, longitude, city, and state.

**Row Count:** 1,000,163  
**Column Count:** 5

## Grain

**One row represents a geographic observation associated with a ZIP code prefix.**

The geolocation dataset contains multiple records associated with ZIP code prefixes, so the exact grain requires further investigation.

## Business Key

**No business key identified.**

`geolocation_zip_code_prefix` is not treated as the business key because a ZIP code prefix may have multiple geographic records.

## Foreign Keys

**None identified.**

No direct foreign-key relationship was identified during the initial investigation.

## Relationships

**No direct relationships identified.**

The geolocation table may be used later as a geographic enrichment dataset for customer and seller information using ZIP code prefixes.

Potential enrichment relationships:

- `customers.customer_zip_code_prefix`
- `sellers.seller_zip_code_prefix`
- `geolocation.geolocation_zip_code_prefix`

These relationships will be evaluated during the data modeling and data-quality phases.

## Date/Timestamp Columns

**None.**

## Schema

| Column | Data Type | Description |
|---|---|---|
| `geolocation_zip_code_prefix` | string | ZIP code prefix associated with the geographic location |
| `geolocation_lat` | string | Latitude coordinate |
| `geolocation_lng` | string | Longitude coordinate |
| `geolocation_city` | string | City associated with the geographic location |
| `geolocation_state` | string | State associated with the geographic location |

## Null Analysis

| Column | Null Count |
|---|---:|
| `geolocation_zip_code_prefix` | 0 |
| `geolocation_lat` | 0 |
| `geolocation_lng` | 0 |
| `geolocation_city` | 0 |
| `geolocation_state` | 0 |

**Conclusion:** No NULL values were found in any column.

## Duplicate Analysis

**Total rows:** 1,000,163

Duplicate analysis for the geolocation dataset requires consideration of the dataset's grain and geographic attributes before determining whether repeated records represent actual duplicates.

## Data Quality Observations

- No NULL values were found in any column.
- No business key was identified during the initial investigation.
- No direct foreign-key relationships were identified.
- `geolocation_zip_code_prefix` may occur across multiple records.
- Latitude and longitude are currently represented as strings in the raw dataset.
- Latitude and longitude will require appropriate numeric data types during Silver-layer transformation.
- The geolocation dataset can be used as a geographic enrichment source for customer and seller data.
- The exact grain and appropriate key require further investigation.