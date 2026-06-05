# Cross-Reference Completeness Checklist

Use this checklist to verify consistency across all PRD deliverable files.

## Report Format

```markdown
# {Project} — Completeness Verification Report

| Item | Detail |
|------|--------|
| Date | {date} |
| Files Verified | {list} |
| Total Issues | {count} |
| Critical | {count} |
| Major | {count} |
| Minor | {count} |
```

## Issue Entry Format

```markdown
### Issue #{N}: {Title}

| Attribute | Value |
|-----------|-------|
| Severity | Critical / Major / Minor |
| Dimension | Entity / Field / Relationship / Coverage |
| Affected Files | file1, file2 |
| Description | What is wrong |
| Recommended Fix | How to fix it |
```

---

## Dimension 1: Entity Consistency

### Checks

- [ ] **PRD → ER:** Every entity described in PRD has a matching entity in ER diagram
- [ ] **PRD → DDL:** Every entity described in PRD has a matching table in DDL
- [ ] **ER → DDL:** Every entity in ER diagram has a matching table in DDL
- [ ] **DDL → ER:** Every table in DDL has a matching entity in ER diagram
- [ ] **Entity count comments:** Header counts in ER and DDL match actual counts
- [ ] **Entity naming:** Entity/table names are identical across all three files

### How to verify

1. Extract entity names from PRD (field definition tables)
2. Extract entity names from ER diagram (entity blocks)
3. Extract table names from DDL (CREATE TABLE statements)
4. Compare all three sets — report any discrepancies

---

## Dimension 2: Field Consistency

### Checks

- [ ] **PRD → DDL:** Every field in PRD field definition tables exists in DDL
- [ ] **DDL → PRD:** Every DDL column is documented in PRD
- [ ] **ER → DDL:** ER diagram fields match DDL columns
- [ ] **Field types:** Data types match between PRD and DDL
- [ ] **Constraints:** NOT NULL, UNIQUE, DEFAULT values consistent
- [ ] **Naming:** Field/column names identical (no snake_case vs camelCase mismatches)

### How to verify

1. For each entity, compare field lists between PRD table, ER entity, and DDL CREATE TABLE
2. Check type mapping (e.g., PRD "string" → DDL VARCHAR)
3. Verify constraint alignment

---

## Dimension 3: Relationship Consistency

### Checks

- [ ] **ER relationships → DDL foreign keys:** Every ER relationship has a FK in DDL
- [ ] **DDL foreign keys → ER relationships:** Every FK has a relationship in ER
- [ ] **Cardinality:** ER cardinality matches PRD-described relationships
- [ ] **No orphans:** Every entity participates in at least one relationship
- [ ] **Parent-child consistency:** For 1:N relationships, child has parent_id FK

### How to verify

1. List all ER relationships and their cardinality
2. List all DDL foreign key columns
3. Cross-reference: each relationship should have a corresponding FK
4. Check for orphan entities

---

## Dimension 4: API & Test Coverage

### Checks

- [ ] **PRD modules → API:** Every PRD functional module has API endpoints
- [ ] **API → Tests:** Every API endpoint has at least one acceptance test
- [ ] **Test categories:** All required test categories are present
- [ ] **Test IDs:** Sequential with no gaps within each category
- [ ] **Test count:** Header count matches actual test cases
- [ ] **State machines:** All state transitions (valid + invalid) have tests

### How to verify

1. List all PRD functional modules
2. For each module, verify corresponding API section exists
3. For each API endpoint, verify at least one test case references it
4. Count tests per category and verify ID sequences

---

## Severity Classification

| Severity | Definition | Examples |
|----------|-----------|---------|
| **Critical** | Data model broken; will cause runtime failures | Missing entity in DDL, field type mismatch, broken FK |
| **Major** | Significant gap; affects completeness | Missing API endpoints, incomplete test coverage, missing fields |
| **Minor** | Documentation issue; no functional impact | Comment count mismatch, formatting inconsistency, missing description |

## Post-Fix Re-Verification

After fixing issues, re-run checks on affected dimensions only:

```markdown
## Re-Verification Results

| Check | Before | After |
|-------|--------|-------|
| Entity count consistency | FAIL (18 vs 19) | PASS |
| Field: adjustment_entry | MISSING | PRESENT |
| ... | ... | ... |
```

---

## Dimension 5: State Machine Coverage（新增维度）

> 适用条件：文档集包含 State_Machine.md 时执行。

### Checks

- [ ] **状态机 → API：** 状态机中每个状态转换（包括异常转换）对应至少一个 API 端点
- [ ] **API → 状态机：** 每个触发状态变更的 API 端点在状态机中有对应转换
- [ ] **状态机 → 测试：** 每个状态转换（有效 + 无效）有至少一个测试用例覆盖
- [ ] **状态规范一致：** 所有状态名与 domain/payment-states.md 完全一致（无自创状态）
- [ ] **不可逆标注：** 所有不可逆转换在状态机和测试用例中均标注 ⚠️
- [ ] **Webhook 覆盖：** 每个状态转换对应的 Webhook 事件在 domain/payment-states.md §6 中存在

### How to verify

1. 提取状态机中所有转换（格式：StateA → StateB via event）
2. 对每个转换，检查 API 文档中是否有触发该转换的端点
3. 对每个转换，检查测试用例中是否有正向（合法转换）+ 负向（非法转换）用例
4. 提取所有状态名，与 domain/payment-states.md 中的标准状态对比

### Issue 报告格式

```markdown
### Issue #N: 状态转换 {StateA → StateB} 无对应测试用例

| 属性 | 值 |
|------|---|
| 严重度 | Major |
| 维度 | State Machine Coverage |
| 影响文件 | State_Machine.md, Acceptance_Tests.md |
| 描述 | Subscription.Active → Past_Due 转换存在状态机，但 Acceptance_Tests.md 中无对应负向测试用例 |
| 修复建议 | 在 Acceptance_Tests.md 中新增：STF-XXX: 验证扣款失败触发 Past_Due 状态转换 |
```
