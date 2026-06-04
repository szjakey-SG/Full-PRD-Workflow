# Acceptance Test Case Template

## Document Header

```markdown
# {Product Name} — Acceptance Test Cases

| Item | Detail |
|------|--------|
| Version | v1.0 |
| Total Cases | {count} |
| Categories | {count} |
| Date | {date} |
```

## Test Case Format

Each test case follows this structure:

```markdown
### {PREFIX}-{NNN}: {Test Title}

| Attribute | Value |
|-----------|-------|
| ID | {PREFIX}-{NNN} |
| Category | {Category Name} |
| Priority | P0 (Critical) / P1 (High) / P2 (Medium) / P3 (Low) |
| Precondition | {What must be true before testing} |

**Steps:**
1. Step 1 description
2. Step 2 description
3. Step 3 description

**Expected Result:**
- Assertion 1
- Assertion 2
```

## Category Organization

Organize tests into categories with unique prefixes:

| Prefix | Category | Description |
|--------|----------|-------------|
| {PRE}- | Positive Flow | Happy path, normal operations |
| NEG- | Negative Flow | Invalid inputs, missing fields |
| STF- | State Machine | State transitions, invalid transitions |
| CON- | Concurrency | Race conditions, parallel operations |
| EDG- | Edge Cases | Boundary values, precision, rounding |
| SEC- | Security | Auth bypass, permission violations |
| INT- | Integration | Cross-module workflows |

## Required Test Categories

Every PRD should have tests covering at minimum:

1. **Positive paths** — Each API endpoint's success case
2. **Negative/boundary** — Invalid inputs, missing required fields, overflow
3. **State machine transitions** — Valid and invalid state transitions
4. **Concurrency** — Simultaneous operations on same resource
5. **Precision** — Decimal precision, rounding rules, currency handling
6. **Cross-module** — Workflows that span multiple modules

## Test Data Appendix

Include a dedicated section at the end:

```markdown
## Appendix: Test Data

### Sample Accounts
| ID | Type | Currency | Status |
|----|------|----------|--------|

### Sample Events
| Event Type | Product | Merchant Type | Currency |
|------------|---------|---------------|----------|

### Sample Rules
| Rule ID | Event Type | Debit Account | Credit Account |
|---------|------------|---------------|----------------|
```

## Coverage Guidelines

- Minimum 10 test cases per functional module
- Every API endpoint should have at least 1 positive + 1 negative test
- Every state machine transition should be tested (valid + invalid)
- Test IDs within a category must be sequential with no gaps
- Total test count should be documented in header and verified
