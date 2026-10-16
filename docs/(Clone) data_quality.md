## Orders

### Timestamp Standardization
The following columns were converted from string to timestamp:
- order_purchase_timestamp
- order_approved_at
- order_delivered_carrier_date
- order_delivered_customer_date
- order_estimated_delivery_date

### NULL Analysis

The following NULLs were identified in the Bronze data:

| Column | NULL Count |
|---|---:|
| order_approved_at | 160 |
| order_delivered_carrier_date | 1,783 |
| order_delivered_customer_date | 2,965 |

### Investigation Findings

- 8 orders have `order_status = delivered` and NULL `order_delivered_customer_date`.
- These NULL values were preserved because the actual delivery timestamp cannot be reliably reconstructed.
- 14 delivered orders have NULL `order_approved_at`.
- These values were also preserved because the actual approval timestamp cannot be reconstructed.
- 1,359 delivered orders have `order_approved_at` later than `order_delivered_carrier_date`. The source data contains this timestamp ordering pattern, so the rule requires further investigation before treating it as invalid.
- 23 orders have `order_delivered_carrier_date` later than `order_delivered_customer_date`. These records were identified as an invalid delivery sequence, but the original timestamps were preserved.
- 6 canceled orders have a populated `order_delivered_customer_date`. These records were flagged because the order status is inconsistent with the presence of a customer delivery date.
- 7,827 orders have `order_delivered_customer_date` later than `order_estimated_delivery_date`. These were not treated as data-quality errors because they represent orders delivered later than the estimated delivery date.

### Data Quality Status

A `data_quality_status` column was created to capture the identified data-quality issues.

| `data_quality_status` | Count |
|---|---:|
| VALID | 98,045 |
| INVALID_DELIVERY_SEQUENCE | 1,382 |
| CANCELED_WITH_DELIVERY_DATE | 6 |
| MISSING_EXPECTED_DELIVERY_DATE | 8 |

The original timestamp values were preserved, and no timestamps were artificially reconstructed or replaced.

---
## Customers

### Data Type Validation

The `customers` table was inspected for appropriate data types.

- `customer_id` remains `string` because it is an identifier.
- `customer_unique_id` remains `string` because it is an identifier.
- `customer_zip_code_prefix` remains `string` because it represents a ZIP code prefix rather than a numeric measure.
- `customer_city` remains `string`.
- `customer_state` remains `string`.

The `customer_zip_code_prefix` column was checked for unexpected non-numeric values. No unexpected non-numeric values were found.

### NULL Analysis

No NULL values were found in any column of the Customers table.

| Column | NULL Count |
|---|---:|
| customer_id | 0 |
| customer_unique_id | 0 |
| customer_zip_code_prefix | 0 |
| customer_city | 0 |
| customer_state | 0 |

### Duplicate Analysis

No duplicate values were found for either customer identifier.

| Column | Duplicate Count |
|---|---:|
| customer_id | 0 |
| customer_unique_id | 0 |

### Value Standardization

The customer attributes were checked for common formatting issues.

- No leading or trailing whitespace was found in `customer_city`.
- No leading or trailing whitespace was found in `customer_state`.
- `customer_city` values are consistently represented in lowercase.
- No mixed `sao paulo` / `São Paulo` representation was identified.
- `customer_state` contains the expected 26 distinct state codes.
- No unexpected capitalization or formatting issues were identified.

### Silver Transformation Decision

No data-cleaning transformation was required for the Customers table.

The original values were preserved because the data passed the NULL, duplicate, formatting, and value validation checks.

`customer_zip_code_prefix` was intentionally retained as a string because it represents a ZIP code prefix and is not a numeric measure.

---
## Sellers

### Data Type Validation

The `sellers` table was inspected for appropriate data types.

- `seller_id` remains `string` because it is an identifier.
- `seller_zip_code_prefix` remains `string` because it represents a ZIP code prefix rather than a numeric measure.
- `seller_city` remains `string`.
- `seller_state` remains `string`.

The `seller_zip_code_prefix` column was checked for unexpected non-numeric values and unusual formatting. No issues were identified.

### NULL Analysis

No NULL values were found in any column of the Sellers table.

| Column | NULL Count |
|---|---:|
| seller_id | 0 |
| seller_zip_code_prefix | 0 |
| seller_city | 0 |
| seller_state | 0 |

### Duplicate Analysis

No duplicate `seller_id` values were found.

| Column | Duplicate Count |
|---|---:|
| seller_id | 0 |

### Value Standardization

The seller attributes were checked for common formatting issues.

- No leading or trailing whitespace was found in `seller_city`.
- No leading or trailing whitespace was found in `seller_state`.
- `seller_city` values are consistently represented in lowercase.
- `seller_state` values contain consistent state codes.
- No unexpected formatting issues were identified in `seller_zip_code_prefix`.

### Silver Transformation Decision

No data-cleaning transformation was required for the Sellers table.

The original values were preserved because the data passed the NULL, duplicate, formatting, and value validation checks.

`seller_zip_code_prefix` was intentionally retained as a string because it represents a ZIP code prefix and is not a numeric measure.ecommerce.bronze.order_items

---
## Order Items

### Data Type Validation

The `order_items` table was inspected for appropriate data types.

- `order_id` remains `string` because it is an identifier.
- `order_item_id` remains `string` because it identifies the item sequence within an order.
- `product_id` remains `string` because it is an identifier.
- `seller_id` remains `string` because it is an identifier.
- `shipping_limit_date` was converted from `string` to `timestamp`.
- `price` was converted from `string` to `double`.
- `freight_value` was converted from `string` to `double`.

Both `price` and `freight_value` were verified to contain values with up to two decimal places.

### NULL Analysis

No NULL values were found in any column of the Order Items table.

| Column | NULL Count |
|---|---:|
| order_id | 0 |
| order_item_id | 0 |
| product_id | 0 |
| seller_id | 0 |
| shipping_limit_date | 0 |
| price | 0 |
| freight_value | 0 |

### Duplicate Analysis

No duplicate records were found in the Order Items table.

| Metric | Count |
|---|---:|
| Total Rows | 112,650 |
| Distinct Rows | 112,650 |
| Duplicate Rows | 0 |

### Referential Integrity

The foreign-key relationships were validated during dataset exploration.

| Column | Referenced Table | Result |
|---|---|---|
| order_id | orders.order_id | Valid |
| product_id | products.product_id | Valid |
| seller_id | sellers.seller_id | Valid |

No unmatched foreign-key values were identified.

### Business Rule Validation

The monetary columns were checked for negative values.

- No negative `price` values were found.
- No negative `freight_value` values were found.

Therefore, no monetary data-quality issue was identified.

### Silver Transformation Decision

The following transformations were applied to the Order Items table:

- `shipping_limit_date` → `timestamp`
- `price` → `double`
- `freight_value` → `double`

The identifier columns were preserved as strings.

No NULL handling, duplicate removal, or data-quality flagging was required because the source data passed the identified validation checks.

---


## Products

### Data Type Validation

The `products` table was inspected for appropriate data types.

- `product_id` remains `string` because it is an identifier.
- `product_category_name` remains `string` because it represents a product category.
- `product_name_lenght` was converted from `string` to `int`.
- `product_description_lenght` was converted from `string` to `int`.
- `product_photos_qty` was converted from `string` to `int`.
- `product_weight_g` was converted from `string` to `int`.
- `product_length_cm` was converted from `string` to `int`.
- `product_height_cm` was converted from `string` to `int`.
- `product_width_cm` was converted from `string` to `int`.

### NULL Analysis

NULL values were identified in two groups of product records.

#### Product Attribute NULLs

The following four columns contain NULL values for the same 610 products:

| Column | NULL Count |
|---|---:|
| product_category_name | 610 |
| product_name_lenght | 610 |
| product_description_lenght | 610 |
| product_photos_qty | 610 |

These NULL values were preserved because the missing product information cannot be reliably reconstructed from the source data.

#### Physical Measurement NULLs

The following four columns contain NULL values for the same 2 products:

| Column | NULL Count |
|---|---:|
| product_weight_g | 2 |
| product_length_cm | 2 |
| product_height_cm | 2 |
| product_width_cm | 2 |

These NULL values were also preserved.

NULL values were not replaced with zero because NULL indicates unavailable information, whereas zero represents an actual measurement or quantity of zero.

### Duplicate Analysis

No duplicate `product_id` values were found.

| Metric | Count |
|---|---:|
| Total Rows | 32,951 |
| Distinct `product_id` | 32,951 |
| Duplicate `product_id` | 0 |

### Business Rule Validation

The numeric product attributes were checked for negative values.

No negative values were found in:

- `product_name_lenght`
- `product_description_lenght`
- `product_photos_qty`
- `product_weight_g`
- `product_length_cm`
- `product_height_cm`
- `product_width_cm`

### Category Translation Relationship

The relationship between `products.product_category_name` and `category_name_translation.product_category_name` was investigated.

- 610 products have a NULL `product_category_name`.
- Two non-NULL product categories do not have corresponding entries in the translation table:
  - `pc_gamer`
  - `portateis_cozinha_e_preparadores_de_alimentos`
- These unmatched categories were not treated as invalid product records.
- The original `product_category_name` values will be preserved.
- No English translation will be artificially assigned.

The `category_name_translation.product_category_name` column was also checked for duplicates. No duplicate category keys were found.

### Silver Transformation Decision

The Products table will be cleaned and standardized without artificially filling missing values.

The category translation will remain as a separate Silver table rather than being joined into `silver.products`.

The category translation relationship will be used later during Gold-layer modeling and analytics.

The original product category values and valid NULL values are preserved.

---
## Product Category Name Translation

### Data Type Validation

The `product_category_name_translation` table was inspected for appropriate data types.

- `product_category_name` remains `string`.
- `product_category_name_english` remains `string`.

Both columns represent category names and do not require numeric or date/timestamp conversions.

### NULL Analysis

No NULL values were found in either column.

| Column | NULL Count |
|---|---:|
| product_category_name | 0 |
| product_category_name_english | 0 |

### Duplicate Analysis

No duplicate `product_category_name` values were found.

| Metric | Count |
|---|---:|
| Total Rows | 71 |
| Distinct Rows | 71 |
| Duplicate Rows | 0 |

`product_category_name` uniquely identifies each translation record.

### Value Standardization

The category names were checked for common formatting issues.

- No leading or trailing whitespace was found in `product_category_name`.
- No leading or trailing whitespace was found in `product_category_name_english`.
- Both columns are consistently represented in lowercase.
- No unexpected capitalization issues were identified.

### Relationship Validation

The `product_category_name` column was validated as the reference key for the relationship:

`products.product_category_name → product_category_name_translation.product_category_name`

No duplicate category keys were found in the translation table.

Some non-NULL product categories in the Products table do not have corresponding translation records. These categories were preserved in the Products table and were not artificially translated.

### Silver Transformation Decision

No data-cleaning transformation was required for the Product Category Name Translation table.

The original category names and English translations were preserved.

The table will be stored as a separate Silver table and joined with product data during Gold-layer modeling when English category names are required.

---
## Order Payments

### Data Type Validation

The `order_payments` table was inspected for appropriate data types.

- `order_id` remains `string` because it is an identifier.
- `payment_sequential` was converted from `string` to `int`.
- `payment_type` remains `string`.
- `payment_installments` was converted from `string` to `int`.
- `payment_value` was converted from `string` to `double`.

`payment_value` was verified to contain values with two decimal places.

### NULL Analysis

No NULL values were found in any column of the Order Payments table.

| Column | NULL Count |
|---|---:|
| order_id | 0 |
| payment_sequential | 0 |
| payment_type | 0 |
| payment_installments | 0 |
| payment_value | 0 |

### Duplicate Analysis

No duplicate records were found in the Order Payments table.

| Metric | Count |
|---|---:|
| Total Rows | 103,886 |
| Distinct Rows | 103,886 |
| Duplicate Rows | 0 |

### Business Key Validation

The composite business key:

`order_id + payment_sequential`

was validated for uniqueness.

| Metric | Count |
|---|---:|
| Total Rows | 103,886 |
| Distinct Composite Keys | 103,886 |

The combination of `order_id` and `payment_sequential` uniquely identifies each payment record.

### Referential Integrity

The `order_id` foreign-key relationship was validated during dataset exploration.

`order_payments.order_id → orders.order_id`

No unmatched `order_id` values were identified.

### Business Rule Validation

The numeric payment columns were checked for negative values.

- No negative `payment_sequential` values were found.
- No negative `payment_installments` values were found.
- No negative `payment_value` values were found.

### Payment Type Validation

The following payment types were identified:

- `credit_card`
- `boleto`
- `voucher`
- `debit_card`
- `not_defined`

The values were preserved as provided by the source dataset. `not_defined` was treated as a valid source value rather than being converted to NULL.

### Silver Transformation Decision

The following transformations were applied:

- `payment_sequential` → `int`
- `payment_installments` → `int`
- `payment_value` → `double`

Identifier and categorical columns were preserved as strings.

No NULL handling, duplicate removal, or additional data-quality flagging was required.

---

## Data Quality Observations — Order Reviews

### 1. Dataset Overview

- Total source records: 104,162
- Total columns: 7
- Exact duplicate records: 85
- Records remaining after removing exact duplicates: 104,077
- Intended grain: One row per review record.
- Business key: `review_id` is the intended identifier, but its reliability is affected by missing values and malformed records.

### 2. Missing Values

| Column | Null Count | Observation |
|---|---:|---|
| `review_id` | 1 | Missing review identifier |
| `order_id` | 2,236 | Missing order reference |
| `review_score` | 2,380 | Missing or potentially malformed review score |
| `review_comment_title` | 92,157 | Optional review title; NULL values are acceptable |
| `review_comment_message` | 63,079 | Optional review message; NULL values are acceptable |
| `review_creation_date` | 8,764 | Missing review creation timestamp |
| `review_answer_timestamp` | 8,785 | Missing review response timestamp |

**Observations:**
- Missing review titles and messages are not necessarily data quality issues because customers can submit reviews without written comments.
- Missing identifiers and scores require monitoring.
- Missing timestamps are preserved rather than replaced with artificial values.

### 3. Duplicate Records

- The source contains 85 exact duplicate rows.
- Exact duplicates are removed using `dropDuplicates()`.
- Deduplication is performed across all columns because a reliable business key has not been established.
- Records are not deduplicated using `review_id` alone.

### 4. Identifier Validation

- Expected identifier format: 32-character hexadecimal strings.
- One record has a missing `review_id`.
- Validation identified 2,702 records with non-null `order_id` values that do not match the expected identifier format.
- A sample of these records contains timestamps and product-description text in identifier and score columns, indicating apparent column misalignment.

**Decision:**
- Preserve malformed records and flag them using `data_quality_status`.
- Do not treat malformed identifiers as valid business keys.
- Investigate the original CSV parsing and ingestion configuration in a future remediation step.

### 5. Referential Integrity

- `order_id` is expected to reference `orders.order_id`.
- The latest validation identified 2,702 records with non-null `order_id` values that failed the expected identifier-format check.
- These records require separate treatment from records with genuinely missing `order_id` values.
- Referential integrity should be validated again after resolving the apparent column misalignment.

### 6. Review Score Validation

- Expected values: Integers from 1 to 5.
- The source contains 2,380 NULL review scores.
- Some records contain timestamp strings in `review_score`, indicating apparent column misalignment.
- `try_cast` is used to prevent malformed values from causing transformation failures.
- Missing or malformed scores are flagged rather than replaced with arbitrary values.

### 7. Timestamp Standardization

The following columns are converted to timestamp data types:

- `review_creation_date`
- `review_answer_timestamp`

NULL values are preserved. Records containing malformed timestamp values may require further investigation.

### 8. Silver Layer Decisions

| Data Quality Issue | Silver Layer Decision |
|---|---|
| Exact duplicate rows | Remove exact duplicates |
| Missing review identifiers | Preserve and flag |
| Missing order identifiers | Preserve and flag |
| Malformed identifiers | Preserve and flag |
| Missing or malformed review scores | Preserve as NULL after safe casting and flag |
| Optional review comments | Preserve NULL values |
| Missing timestamps | Preserve NULL values |
| Incorrect column alignment | Document for further investigation |

### 9. Known Limitation

The malformed identifier and score values suggest a possible CSV parsing or column-alignment issue. The current Silver transformation prevents casting failures and flags suspicious records, but it does not repair the underlying source data.

The affected records should be investigated before using them for review-level analytics.

### 10. Final Decision

The `order_reviews` Silver table standardizes data types, removes exact duplicates, preserves NULL values, and flags identified data quality issues.

The table is retained for downstream processing, with the known source-data limitation documented.

---

## 10. Geolocation

### Overview
- **Source Table:** `ecommerce.bronze.geolocation`
- **Target Table:** `ecommerce.silver.geolocation`
- **Original Row Count:** 1,000,163
- **Row Count After Deduplication:** 738,332
- **Exact Duplicate Rows Removed:** 261,831
- **Number of Columns:** 5 in the original Bronze table
- **Grain:** One row represents a geographic observation associated with a ZIP code prefix. Multiple observations can exist for the same prefix.
- **Business Key:** No unique business key identified.

### 1. Null Value Validation

All five columns were checked for NULL values in the Bronze table.

| Column | NULL Count | Action |
|---|---:|---|
| geolocation_zip_code_prefix | 0 | No action required |
| geolocation_lat | 0 | Validate numeric conversion |
| geolocation_lng | 0 | Validate numeric conversion |
| geolocation_city | 0 | No action required |
| geolocation_state | 0 | No action required |

### 2. Duplicate Validation

Exact duplicate rows were identified and removed.

| Metric | Count |
|---|---:|
| Original rows | 1,000,163 |
| Rows after deduplication | 738,332 |
| Exact duplicate rows removed | 261,831 |

**Cleaning Decision:**
- Removed completely identical rows.
- Preserved distinct geographic observations, including observations sharing the same ZIP code prefix.
- Did not treat ZIP code prefix as a unique identifier.

### 3. Data Type Validation

The Bronze table stores all columns as strings.

The following data types are required in the Silver layer:

| Column | Target Data Type |
|---|---|
| geolocation_zip_code_prefix | STRING |
| geolocation_lat | DOUBLE |
| geolocation_lng | DOUBLE |
| geolocation_city | STRING |
| geolocation_state | STRING |

The ZIP code prefix remains a string to preserve leading zeros.

### 4. Coordinate Validation

Latitude and longitude values were checked for numeric convertibility and valid geographic ranges.

| Validation | Expected Range |
|---|---|
| Latitude | -90 to 90 |
| Longitude | -180 to 180 |

The validation results after exact duplicate removal were:

| Metric | Count |
|---|---:|
| Rows validated | 738,332 |
| Rows with VALID coordinate status | 738,332 |
| Rows with invalid or missing coordinate status | 0 |

**Cleaning Decision:**
- Retained records with valid coordinates.
- Used safe numeric conversion to identify unconvertible values.
- Flagged missing, unconvertible, or out-of-range coordinates for investigation rather than automatically deleting records.

**Note:** These results establish that the remaining rows passed the implemented coordinate checks. They do not establish that each coordinate accurately corresponds to its stated ZIP code prefix or city.

### 5. Business Key and Referential Integrity

- No unique business key was identified.
- ZIP code prefixes can appear in multiple records.
- No direct foreign key was identified.
- Geolocation data can enrich customer and seller records using their respective ZIP code prefix columns.

Potential enrichment columns:
- `customers.customer_zip_code_prefix`
- `sellers.seller_zip_code_prefix`
- `geolocation.geolocation_zip_code_prefix`

### 6. Cleaning Decisions

1. Removed 261,831 exact duplicate rows.
2. Converted latitude and longitude to DOUBLE.
3. Preserved ZIP code prefixes as STRING.
4. Retained city and state as STRING.
5. Added `coordinate_quality_status` to identify coordinate quality issues.
6. Preserved distinct geographic observations sharing the same ZIP code prefix.
7. Prepared the cleaned data for storage in `ecommerce.silver.geolocation`.

### 7. Post-Transformation Validation

The following checks should be completed after saving the Silver table:

- Confirm the final row count is 738,332.
- Verify the expected schema and data types.
- Confirm no exact duplicate rows remain.
- Verify coordinate quality status counts.
- Confirm latitude and longitude fall within their valid ranges.
- Ensure repeated ZIP code prefixes are preserved.

### Conclusion

The original geolocation dataset contained 1,000,163 rows. Exact duplicate removal reduced the dataset to 738,332 rows, eliminating 261,831 duplicate records.

All 738,332 remaining rows passed the implemented coordinate validation. The cleaned dataset retains ZIP code prefixes as strings and geographic coordinates as numeric values, making it suitable for downstream customer and seller geographic enrichment.

The Silver table should be validated after saving to confirm the final schema, row count, and data quality results.
