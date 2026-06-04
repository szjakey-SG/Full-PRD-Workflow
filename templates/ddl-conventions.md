# DDL Conventions

## File Header

```sql
-- =============================================
-- {Product Name} — Database DDL
-- Version: {version}
-- Tables: {count}
-- Generated: {date}
-- =============================================
```

**Important:** The table count in the header MUST match the actual `CREATE TABLE` count. Always recount after edits.

## Table Definition Pattern

```sql
CREATE TABLE {entity_name} (
    id              BIGINT          NOT NULL AUTO_INCREMENT  COMMENT 'Primary key',
    -- business fields here
    status          VARCHAR(32)     NOT NULL DEFAULT 'active' COMMENT 'Enum: active, frozen, closed',
    version         INT             NOT NULL DEFAULT 0       COMMENT 'Optimistic lock version',
    created_at      TIMESTAMP       NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT 'Creation time',
    updated_at      TIMESTAMP       NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT 'Last update time',
    PRIMARY KEY (id),
    UNIQUE KEY uk_{unique_field} ({unique_field}),
    KEY idx_{index_field} ({index_field})
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci
  COMMENT='{Human-readable table description}';
```

## Naming Conventions

| Element | Convention | Example |
|---------|-----------|---------|
| Table name | snake_case, singular | `ledger_account` |
| Column name | snake_case | `merchant_id` |
| Primary key | `id` | `id BIGINT NOT NULL` |
| Foreign key | `{entity}_id` | `account_id` |
| Unique key | `uk_{field}` | `uk_source_event` |
| Index | `idx_{field}` | `idx_merchant_id` |
| Composite index | `idx_{field1}_{field2}` | `idx_currency_status` |
| Boolean field | `is_{action}` | `is_reversed` (TINYINT(1)) |
| Timestamp field | `{action}_at` | `posted_at`, `created_at` |
| Amount field | `{type}_amount` | `total_amount` |
| Enum field | Describe values in COMMENT | `COMMENT 'Enum: pending, posted, archived'` |

## Required Columns

Every table must include:
- `id` BIGINT — Primary key with AUTO_INCREMENT
- `created_at` TIMESTAMP — Creation timestamp
- `updated_at` TIMESTAMP — Last update timestamp with ON UPDATE

## Column Ordering Convention

1. `id` (primary key)
2. Business identifier fields (e.g., `merchant_id`, `account_id`)
3. Core business fields
4. Status and control fields
5. Amount and currency fields (grouped together)
6. Reference and audit fields (e.g., `source_system`, `source_event_id`)
7. `version` (optimistic lock)
8. `created_at`, `updated_at` (timestamps)

## Index Strategy

- Primary key: automatic via `PRIMARY KEY (id)`
- Unique constraints: for idempotency keys and business unique identifiers
- Foreign key indexes: for all columns referenced in JOINs
- Query indexes: for frequently filtered/sorted columns
- Composite indexes: left-to-right based on query patterns

## Verification Checklist

- [ ] Header table count matches actual CREATE TABLE count
- [ ] Every table has `id`, `created_at`, `updated_at`
- [ ] All ENUM fields have value lists in COMMENT
- [ ] Foreign key columns have matching indexes
- [ ] Column names match ER diagram field names exactly
- [ ] No reserved words used as column names
- [ ] Decimal precision appropriate for amount fields (e.g., DECIMAL(20,8))
