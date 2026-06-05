# Golden Cases — 回归验证用例集

> 用途：每次 SKILL.md 或 Prompt 更新后，对 golden cases 执行回归测试，确认输出质量未下降。
> 维护：产研 PMO + QA 负责人

---

## 目录结构

```
golden-cases/
├── README.md                     ← 本文件
├── case_001_subscription_mvp/    ← 订阅计费 MVP（基础场景）
│   ├── input.md                  ← 结构化用户输入
│   ├── expected_scope.md         ← 期望的 Scope Summary
│   ├── expected_score.json       ← 期望的 Quality Gate 得分
│   └── eval_notes.md             ← 评估重点说明
├── case_002_payment_dunning/     ← Dunning 追款流程（状态机复杂场景）
├── case_003_ledger_entries/      ← Ledger 账务分录（高风险场景）
└── case_004_security_intercept/  ← 安全拦截验证（Guardrail 场景）
```

---

## 回归测试流程

```
1. 选取 golden case
2. 将 input.md 内容作为用户输入，触发 Skill
3. 对 AI 输出执行 eval/quality-rubric.md 评分
4. 将实际得分与 expected_score.json 对比
5. 差异 > 10 分 → 触发人工 Review，判断是 Skill 退化还是评分基准需更新
6. 记录回归结果到 regression_log.md
```

---

## expected_score.json 格式

```json
{
  "case_id": "case_001",
  "case_name": "Subscription Billing MVP",
  "last_validated": "2026-06-05",
  "validated_model": "claude-sonnet-4-6",
  "expected_scores": {
    "business_completeness": 4,
    "engineering_executability": 4,
    "system_consistency": 5,
    "risk_coverage": 4,
    "documentation_standards": 4
  },
  "expected_total": 85,
  "tolerance": 10,
  "notes": "订阅MVP场景，业务完整性期望4分（Dunning细节可能标待确认）"
}
```

---

## Case 设计指南

每个 case 应覆盖不同的挑战场景：

| Case 类型 | 挑战点 | 期望验证项 |
|-----------|--------|-----------|
| 基础场景 | 完整需求输入 | 7文件正确生成，一致性高 |
| 模糊需求场景 | 输入不完整 | Skill 正确触发 Phase 1 澄清，不直接生成 |
| 高风险场景 | 输入含资金/账务 | Guardrail 触发，正确提示风险 |
| 安全拦截场景 | 输入含敏感数据 | 正确拒绝，给出脱敏指引 |
| 状态机复杂场景 | 多对象交叉状态 | 状态机与 payment-states.md 一致 |

---

## 回归日志格式（regression_log.md）

```markdown
## 回归记录 — {date}

| Case | 期望总分 | 实际总分 | 差异 | 模型 | 结论 |
|------|---------|---------|------|------|------|
| case_001 | 85 | 87 | +2 | claude-sonnet-4-6 | PASS |
| case_002 | 82 | 71 | -11 | claude-sonnet-4-6 | REVIEW → 状态机模板需更新 |
```
