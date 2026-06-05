# Payment PRD Workflow Skill v2.0.0

面向支付与 SaaS 产研团队的 AI 工程交付 Skill，覆盖从项目立项到上线打包的全生命周期。

---

## 快速开始

将本 Skill 安装到 Claude Cowork 后，直接输入以下指令触发：

```
/生成PRD          → 生成工程可执行 PRD
/生成状态机       → 生成业务对象状态机（含 Mermaid 图）
/拆分研发任务     → 生成 Epic/Story/Task 三级任务清单
/生成上线Checklist → 生成含 Go/No-Go 判断的上线检查表
/需求澄清         → 从模糊需求输出结构化 Scope Summary
/检查需求完整性   → 对已有文档进行 5 维交叉校验
```

或者描述项目背景，让 Skill 自动从 Phase 0 开始全流程执行。

---

## 适用场景

- **领域**：订阅计费（Subscription Billing）、账务分录（Ledger）、支付网关、风控、结算
- **团队角色**：业务负责人、产品经理、架构师、后端/前端研发、QA、DevOps
- **项目阶段**：立项 → 需求澄清 → PRD → 系统设计 → 开发排期 → 测试 → 上线 → 复盘

---

## 工作流

```
Phase 0  安全检查 + 领域识别      [每次调用，自动执行]
Phase 1  需求收集 + Scope 确认    [⏸ 等待人工确认]
Phase 2  7 文件并行/串行生成      [⏸ 等待人工确认]
Phase 3  5 维交叉校验             [⏸ 等待人工确认]
Phase 4  问题修复
Phase 5  架构图 + 业务指南 HTML
Phase 6  质量门禁评分 + 打包交付  [⏸ 等待人工确认]
```

**完整交付物（12 文件）：**

| 文件 | 说明 |
|------|------|
| `{Project}_PRD.md` | 工程可执行 PRD，含状态机、字段表、验收标准 |
| `{Project}_ER.mermaid` | 实体关系图 |
| `{Project}_API_Design.md` | 完整 API 设计，含 Request/Response Schema |
| `{Project}_Acceptance_Tests.md` | 验收测试用例集 |
| `{Project}_DDL.sql` | 数据库建表脚本 |
| `{Project}_State_Machine.md` | 业务对象状态机文档 |
| `{Project}_Sprint_Tasks.md` | 研发任务拆分（Epic/Story/Task）|
| `{Project}_Technical_Diagrams.html` | 交互式架构图（5 种 Mermaid）|
| `{Project}_Business_Guide.html` | 面向非技术干系人的业务指南 |
| `{Project}_Completeness_Report.md` | 5 维交叉校验报告 |
| `{Project}_Release_Checklist.md` | 上线 Checklist（含 Go/No-Go）|
| `{Project}_Quality_Report.md` | LLM 自动评分报告 |

---

## 文件结构

```
payment-prd-skill/
├── SKILL.md                           ← 核心执行文件（~400行，每次全量加载）
├── domain/
│   ├── payment-states.md              ← 支付领域标准状态定义（唯一来源）
│   └── security-rules.md             ← 数据安全边界规则
├── templates/
│   ├── prd-template.md
│   ├── er-conventions.md
│   ├── api-design-template.md
│   ├── test-case-template.md
│   ├── ddl-conventions.md
│   ├── state-machine-template.md      ← v2.0 新增
│   ├── sprint-tasks-template.md       ← v2.0 新增
│   ├── release-checklist-template.md  ← v2.0 新增
│   └── completeness-checklist.md
└── eval/
    ├── quality-rubric.md              ← LLM 可执行评分 Rubric
    └── golden-cases/
        └── README.md                  ← 回归验证框架说明
```

> **Context 效率设计**：SKILL.md 每次全量加载（~400行），其余文件按 Phase 按需 Read，不预加载。

---

## 核心能力

### 安全前置检查
每次调用前自动扫描输入，命中以下规则时立即拦截：
- **FORBIDDEN_INPUT**：真实卡号、API Key、生产数据库连接串等敏感数据 → 拒绝处理，要求脱敏
- **HIGH_RISK_INTERCEPT**：生产数据库变更脚本、真实资金操作方案 → 提示高风险，等待人工确认

### 支付领域知识内置
`domain/payment-states.md` 定义了 Subscription / Invoice / Payment Attempt / Dunning 的标准状态枚举、合法转换规则和标准 Webhook 事件列表。所有生成文档中的状态名必须与此文件一致，不得自创。

### LLM 自动质量评分
`eval/quality-rubric.md` 提供 5 维可执行评分标准，AI 在 Phase 6 自动打分：

| 维度 | 权重 |
|------|------|
| 业务完整性 | 25% |
| 工程可执行性 | 25% |
| 系统一致性 | 20% |
| 风险控制覆盖 | 15% |
| 文档规范性 | 15% |

总分 < 70 → BLOCKED，不得打包；70-79 → 必须修复；≥ 80 → 可交付。

### 5 维交叉校验
自动检查 PRD / ER / DDL / API / 测试用例之间的一致性（实体/字段/关系/API覆盖/状态机覆盖），生成带严重度分级的 Completeness Report。

---

## 数据安全

**允许输入：** 脱敏后的业务流程、示例数据（标注 example/test）、非生产错误信息

**禁止输入：** 真实卡号/CVV、API Key、Webhook Secret、生产数据库连接串、含PII的生产日志

参见 `domain/security-rules.md` 完整规则。

---

## 质量门禁

AI 输出默认为 **Draft**，进入正式评审前必须满足：

```
总分 ≥ 80 分
Completeness Report 无 Critical 问题
所有 Human Checkpoint 已人工确认
```

---

## 团队角色与责任

| 角色 | 常用指令 | Review 责任 |
|------|---------|------------|
| 产品经理 | `/需求澄清` `/生成PRD` `/检查需求完整性` | 业务规则、状态机、验收标准 |
| 架构师 | `/生成系统设计` `/生成ER图` `/生成API清单` | 架构、数据模型、幂等、安全 |
| 后端研发 | `/生成API清单` `/拆分研发任务` | 接口实现、异常处理 |
| QA | `/生成测试用例` `/检查需求完整性` | 覆盖率、回归范围 |
| DevOps | `/生成上线Checklist` | 发布步骤、回滚方案、监控 |
| 风控/财务 | `/需求澄清` `/检查需求完整性` | 资金规则、结算口径 |

---

## Skill 生命周期

**更新触发条件：**
- 项目复盘后 → 更新领域规则和 Prompt
- 每季度 → 运行 `eval/golden-cases/` 回归，评估质量漂移
- 重大故障后 → 专项修订对应 Phase 的 Guardrails
- 模型版本升级时 → 更新 `validated_models`，重新运行 eval

**暂停使用标准：**
- 连续 3 次输出评分 < 70
- 输出被发现包含编造的业务规则
- 被用于处理未脱敏的生产数据

---

## 版本历史

| 版本 | 日期 | 主要变更 |
|------|------|---------|
| v2.0.0 | 2026-06-05 | 整合 Oceanpayment Skill v1.0；新增 Phase 0 安全检查；扩展为 7 文件交付；XML 结构化 Prompt；LLM 质量门禁；Agentic 并行执行；domain/ + eval/ 目录 |
| v1.0.0 | 2026-06-01 | 初始版本（full-prd-workflow）|

---

## 验证模型

| 模型 | 状态 |
|------|------|
| claude-sonnet-4-6 | ✅ 已验证 |
| claude-opus-4-8 | ✅ 已验证 |
