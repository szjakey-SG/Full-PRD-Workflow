# Payment PRD Workflow Skill v2.1.0

面向支付与 SaaS 产研团队的 AI 工程交付 Skill，覆盖从项目立项到上线打包的全生命周期。
v2.1 在 v2.0 基础上增加了完整的 **JIT Planning 工程能力**，支持按需拉取、自适应文档集、渐进式质量反馈。

---

## 快速开始

将本 Skill 安装到 Claude Cowork 后，直接输入以下指令触发：

```
/生成PRD              → 生成工程可执行 PRD（自动进入完整流程）
/生成状态机           → 生成业务对象状态机（含 Mermaid stateDiagram-v2）
/生成API清单          → 生成完整 API 设计文档
/生成测试用例         → 生成覆盖支付异常的验收测试集
/拆分研发任务         → 生成 Epic/Story/Task 三级任务清单
/生成上线Checklist    → 生成含 Go/No-Go 判断的上线检查表
/需求澄清             → 从模糊需求输出结构化 Scope Summary
/检查需求完整性       → 对已有文档进行 5 维交叉校验
/JIT生成PRD           → 以 JIT 模式启动（按需文档集 + 渐进式生成）
/快速需求             → JIT 轻量模式，仅生成 PRD + API（适合 MVP）
```

或者直接描述项目背景，Skill 自动从 Phase 0 开始全流程执行。

---

## 两种执行模式

### 标准模式（适合完整项目交付）

```
Phase 0  安全检查 + 领域识别          [自动执行]
Phase 1  需求收集 + Scope 确认        [⏸ Checkpoint 1]
Phase 2  7 文件并行/串行生成           [⏸ Checkpoint 2]
Phase 3  5 维交叉校验                  [⏸ Checkpoint 3]
Phase 4  问题修复
Phase 5  架构图 + 业务指南 HTML
Phase 6  质量门禁评分 + 打包交付       [⏸ Checkpoint 4]
```

**交付物（12 文件）：**

| 文件 | 说明 |
|------|------|
| `{Project}_PRD.md` | 工程可执行 PRD，含状态机、字段表、验收标准 |
| `{Project}_ER.mermaid` | 实体关系图 |
| `{Project}_API_Design.md` | 完整 API 设计，含 Request/Response Schema |
| `{Project}_Acceptance_Tests.md` | 验收测试用例集 |
| `{Project}_DDL.sql` | 数据库建表脚本 |
| `{Project}_State_Machine.md` | 业务对象状态机文档 |
| `{Project}_Sprint_Tasks.md` | 研发任务拆分（Epic/Story/Task）|
| `{Project}_Technical_Diagrams.html` | 交互式架构图 |
| `{Project}_Business_Guide.html` | 面向非技术干系人的业务指南 |
| `{Project}_Completeness_Report.md` | 5 维交叉校验报告 |
| `{Project}_Release_Checklist.md` | 上线 Checklist（含 Go/No-Go）|
| `{Project}_Quality_Report.md` | LLM 自动评分报告 |

---

### JIT 模式（适合 MVP、快速迭代、单模块扩展）

触发关键词：`/JIT生成PRD`、`/快速需求`、`只需要`、`先生成`、`MVP` 等。

**JIT 模式的核心差异：**

```
Phase 0  安全检查                      [自动执行]
Phase 1  需求 + DOCUMENT_INTENT        [⏸ 确认意向]
         ↓ 生成 PRD（第一个文档）
         PRD_REVIEW → DOCUMENT_PLAN 最终版 + CHECKPOINT_PLAN
         [⏸ 确认最终计划]
Phase 2  FOR each doc IN DOCUMENT_PLAN:
           MICRO-PLAN（按 CONTEXT_PACKAGE 注入上下文）
           → READ TEMPLATE_MAP[doc]（仅当前文档需要的模板）
           → GENERATE
           → 即时质量检查
           → CHECKPOINT 卡片（含可交付集合说明）
           ↓ 用户可在任意完整文档边界停止
Phase 3  增量校验摘要（O(n) 轻量化）
```

**JIT 模式特性：**

| 特性 | 说明 |
|------|------|
| 自适应文档集 | 基于 PRD 实际内容决定是否生成 ER/DDL/State_Machine |
| 按需模板加载 | 每个文档生成前才加载对应模板，跳过的文档不占 context |
| 动态批次推导 | 从 DOCUMENT_PLAN 拓扑排序推导并行批次，非硬编码 |
| 自适应检查点 | 文档数 ≤3 → 1个；4-5 → 2个；6-7 → 3个；≥8 → 4个 |
| 渐进式质量分 | 每文档生成后即时评分，50% 进度可见质量趋势 |
| 部分交付支持 | 每个 Checkpoint 标注当前可交付集合，可随时停止 |

**部分交付规则（JIT 模式下，任意边界可停止）：**

```
仅 PRD               → ✅ 可用于需求评审、立项决策
PRD + API_Design     → ✅ 可用于前后端接口对齐、联调启动
PRD + State_Machine  → ✅ 可用于业务流程评审、前端状态管理
PRD + ER + DDL       → ✅ 可用于数据库设计评审
PRD + Test_Cases     → ✅ 可用于 QA 测试计划制定
ER 有但 DDL 无       → ⚠️ 数据模型不完整，建议同步生成 DDL
```

---

## 文件结构

```
payment-prd-skill/
├── SKILL.md                           ← 核心执行文件（~590行，每次全量加载）
├── AUTOMATION.md                      ← 自动化运行策略（周巡检/变更响应/质量回归）
├── README.md                          ← 本文件
├── domain/
│   ├── payment-states.md              ← 支付领域标准状态定义（唯一来源）
│   └── security-rules.md             ← 数据安全边界规则
├── templates/                         ← 按需加载，非全量
│   ├── prd-template.md
│   ├── er-conventions.md
│   ├── api-design-template.md
│   ├── test-case-template.md
│   ├── ddl-conventions.md
│   ├── state-machine-template.md
│   ├── sprint-tasks-template.md
│   ├── release-checklist-template.md
│   └── completeness-checklist.md
└── eval/
    ├── quality-rubric.md              ← LLM 可执行评分 Rubric（5维，需引用原文才能打分）
    └── golden-cases/
        └── README.md                  ← 回归验证框架说明
```

> **Context 效率设计：** SKILL.md 每次全量加载（~590行）。其余文件仅在对应文档生成前按需 Read，
> JIT 模式下跳过的文档其模板不会被加载。

---

## 核心能力

### 安全前置检查

每次调用前自动扫描输入：

| 规则 | 触发内容 | 响应 |
|------|---------|------|
| FORBIDDEN_INPUT | 真实卡号、API Key、生产数据库连接串、含PII的生产日志 | ⛔ 拒绝处理，要求脱敏后重新输入 |
| HIGH_RISK_INTERCEPT | 生产DB变更脚本、真实资金操作方案、不可逆状态变更 | ⚠️ 提示高风险，等待人工确认后继续 |
| ALLOWED_INPUT | 脱敏流程描述、示例数据、非生产错误信息 | 正常处理，无需提示 |

### 支付领域知识内置

`domain/payment-states.md` 是所有生成文档的状态名唯一来源：

- **Subscription** 8 个标准状态（Draft / Trialing / Active / Past_Due / Paused / Non_renewing / Canceled / Expired）
- **Invoice** 8 个状态 + 合法转换规则
- **Payment Attempt** 7 个状态（Created / Processing / Succeeded / Failed / Canceled / Timeout / Requires_Action）
- **Dunning** 标准重试策略（Day 1/3/7/14）
- **30+ 标准 Webhook 事件**（`subscription.created`, `payment.succeeded` 等）

所有输出中的状态名必须与此文件完全一致，不得自创。

### LLM 自动质量评分

`eval/quality-rubric.md` 提供 5 维可执行评分，**AI 必须先引用原文才能打分**（防止无据打分）：

| 维度 | 权重 | 关键检查 |
|------|------|---------|
| 业务完整性 | 25% | P0 模块是否有业务规则；验收标准是否具体 |
| 工程可执行性 | 25% | API 端点数 ≥ 模块数×2；字段类型是否完整 |
| 系统一致性 | 20% | PRD/ER/DDL/API 实体字段是否对齐 |
| 风险控制覆盖 | 15% | 金额精度、幂等性、Dunning 策略是否覆盖 |
| 文档规范性 | 15% | 是否使用 Mermaid/Markdown Table/JSON 代码块 |

**质量门禁：**
- 总分 ≥ 80 → APPROVED，可交付
- 总分 70-79 → APPROVED WITH NOTES，需修复后交付
- 总分 < 70 → BLOCKED，不得打包

### 5 维交叉校验

自动检查 5 个维度的跨文档一致性：

1. **实体一致性** — PRD ↔ ER ↔ DDL 实体名对齐
2. **字段一致性** — 字段名/类型/约束跨文件一致
3. **关系一致性** — ER 关系 ↔ DDL 外键对应
4. **API 与测试覆盖** — 每个模块有 API，每个 API 有正向+负向测试
5. **状态机覆盖** — 每个状态转换在 API 和测试用例中有对应覆盖

---

## 自动化运行策略

`AUTOMATION.md` 定义了 4 条自动化策略，无需每次手动触发：

| 策略 | 触发条件 | 模型推荐 | 说明 |
|------|---------|---------|------|
| **变更响应** | 检测到 PRD/ER 文件修改 | claude-haiku-4-5 | 即时定向一致性检查，只扫描受影响维度 |
| **周期巡检** | 每周一 09:00 | claude-sonnet-4-6 | 对所有在研项目执行 5 维完整性巡检 |
| **质量回归** | SKILL.md 版本号变更 | claude-sonnet-4-6 | 对 Golden Cases 跑回归，偏差 >10 分告警 |
| **日报摘要** | 每个工作日 18:00 | claude-haiku-4-5 | 汇总当天调用次数、平均分、拦截次数 |

> 变更响应策略最符合 JIT 精神，建议优先建设。所有自动化任务只输出"异常项"，正常情况静默完成。

---

## 数据安全

**允许输入：** 脱敏后的业务流程、示例数据（标注 example/test）、非生产错误信息、公开技术文档引用

**禁止输入：** 真实卡号/CVV、API Key、Webhook Secret、生产数据库连接串、含PII的生产日志、未脱敏交易流水

完整脱敏规范见 `domain/security-rules.md`（示例：卡号 → `411111****9012`，手机号 → `186****5678`）。

---

## 团队角色与常用指令

| 角色 | 常用指令 | Review 责任 |
|------|---------|------------|
| 产品经理 | `/需求澄清` `/生成PRD` `/JIT生成PRD` | 业务规则、状态机、验收标准 |
| 架构师 | `/生成API清单` `/生成ER图` `/生成状态机` | 架构设计、数据模型、幂等、安全 |
| 后端研发 | `/生成API清单` `/拆分研发任务` | 接口实现、异常处理 |
| QA | `/生成测试用例` `/检查需求完整性` | 覆盖率、回归范围 |
| DevOps | `/生成上线Checklist` | 发布步骤、回滚方案、监控告警 |
| 风控/财务 | `/需求澄清` `/检查需求完整性` | 资金规则、结算口径 |

---

## 质量与生命周期

**质量门禁通过条件（三项必须同时满足）：**

```
1. LLM 质量评分 ≥ 80 分
2. Completeness Report 无 Critical 问题
3. 所有 Human Checkpoint 已人工确认
```

**Skill 更新触发条件：**

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
| v2.1.0 | 2026-06-09 | JIT Planning 全面落地：TEMPLATE_MAP 按文档加载、CONTEXT_PACKAGE 最小上下文包、DOCUMENT_INTENT 两阶段确认、PARTIAL_DELIVERY_RULES 部分交付、DEPENDENCY_RULES 动态批次推导、CHECKPOINT_RULES 自适应检查点。新增 AUTOMATION.md 自动化运行策略。JIT 评分 11/35 → 29/35（83%）。|
| v2.0.0 | 2026-06-05 | 整合 Oceanpayment Skill v1.0 + full-prd-workflow v1.0。新增 Phase 0 安全检查；扩展为 7 文件交付；XML 结构化 Prompt；LLM-graded 质量门禁；Agentic 并行执行；Human Checkpoint；domain/ eval/ 目录。|
| v1.0.0 | 2026-06-01 | 初始版本（full-prd-workflow）|

---

## 验证模型

| 模型 | 状态 | 说明 |
|------|------|------|
| claude-sonnet-4-6 | ✅ 已验证 | 推荐用于完整项目和 JIT 标准模式 |
| claude-opus-4-8 | ✅ 已验证 | 复杂支付系统推荐 |
| claude-haiku-4-5 | ✅ 推荐 | 自动化任务（变更响应、日报）推荐，成本低 |
