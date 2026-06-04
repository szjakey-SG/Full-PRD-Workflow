# Full PRD Workflow

**[English](#english)** | **[中文](#中文)**

---

## English

### Overview

Full PRD Workflow is an AI-agent skill that implements a structured, 6-phase methodology for producing professional-grade Product Requirements Document (PRD) deliverable suites. It is designed to be used within [QoderWork](https://qoder.com) or any compatible AI agent environment.

Rather than generating a single monolithic document, this skill orchestrates the creation of a complete deliverable set — PRD document, ER diagram, API design, acceptance tests, database DDL, architecture diagrams, and a business-facing guide — with built-in cross-reference verification to ensure consistency across all artifacts.

### Features

- **End-to-End PRD Delivery** — From requirements scoping to final packaging, the entire workflow is automated across 6 phases.
- **5 Core Deliverables** — PRD main document, ER diagram (Mermaid), API design document, acceptance test cases, and database DDL.
- **Cross-Reference Verification** — A multi-dimension consistency check across entities, fields, relationships, and API/test coverage.
- **Issue Remediation** — Automated fix workflow prioritized by severity (Critical → Major → Minor).
- **Architecture & Business Outputs** — Interactive technical diagrams (HTML + Mermaid) and a stakeholder-friendly business guide with UI mockups.
- **Parallel Execution** — Leverages subagents to generate independent documents concurrently for faster delivery.

### Workflow Phases

| Phase | Description |
|-------|-------------|
| **1. Requirements Scoping** | Gather product info, define scope boundaries, confirm with stakeholders |
| **2. Core Document Generation** | Produce 5 deliverable files (PRD, ER, API, Tests, DDL) in parallel |
| **3. Cross-Reference Verification** | Run 4-dimension consistency checks and generate a completeness report |
| **4. Issue Remediation** | Fix identified issues by severity, with parallel subagent streams |
| **5. Architecture & Business Guide** | Create interactive diagrams and a business-facing HTML guide |
| **6. Integration & Packaging** | Merge diagrams into PRD, package all files into a ZIP archive |

### Project Structure

```
full-prd-workflow/
├── SKILL.md                          # Skill definition and workflow instructions
├── README.md                         # This file
└── templates/
    ├── prd-template.md               # PRD main document template
    ├── api-design-template.md        # API design document template
    ├── er-conventions.md             # ER diagram naming and syntax conventions
    ├── test-case-template.md         # Acceptance test case template
    ├── ddl-conventions.md            # Database DDL naming and structure conventions
    └── completeness-checklist.md     # Cross-reference verification checklist
```

### Deliverable Output

A complete delivery package includes:

```
{Project}_Deliverables_{version}.zip
├── {Project}_PRD.md                  # Product Requirements Document
├── {Project}_ER.mermaid              # Entity Relationship Diagram
├── {Project}_API_Design.md           # API Endpoint Specifications
├── {Project}_Acceptance_Tests.md     # Test Cases with full coverage
├── {Project}_DDL.sql                 # Database Schema Definition
├── {Project}_Technical_Diagrams.html # Interactive Architecture Diagrams
├── {Project}_Business_Guide.html     # Stakeholder Guide with UI Mockups
└── {Project}_Completeness_Report.md  # Cross-Reference Verification Report
```

### Installation

This skill is designed for use with QoderWork. To install:

1. Place the `full-prd-workflow/` folder into your skills directory (`~/.qoderwork/skills/`).
2. Restart or refresh your QoderWork session.
3. The skill will be available automatically — invoke it by asking to "write a PRD" or "generate product requirements."

### Usage

Simply describe your product or feature to the AI agent. For example:

> "Write a complete PRD for a subscription billing platform that supports recurring payments, plan management, and invoice generation."

The skill will guide the agent through requirements scoping, document generation, verification, and final packaging.

### Verification Dimensions

The built-in cross-reference check covers four dimensions:

- **Entity Consistency** — PRD entities, ER diagram entities, and DDL tables must align perfectly.
- **Field Consistency** — Field names, types, and constraints match across PRD, ER, and DDL.
- **Relationship Consistency** — ER relationships correspond to DDL foreign keys; no orphan entities.
- **API & Test Coverage** — Every PRD module has API endpoints, and every endpoint has acceptance tests.

### License

MIT License. See [LICENSE](LICENSE) for details.

---

## 中文

### 概述

Full PRD Workflow 是一个 AI Agent 技能（Skill），实现了一套结构化的六阶段方法论，用于生成专业级的产品需求文档（PRD）交付物套件。该技能可在 [QoderWork](https://qoder.com) 或任何兼容的 AI Agent 环境中使用。

与生成单一的文档不同，该技能协调生成一套完整的交付物——PRD 主文档、ER 图、API 设计、验收测试、数据库 DDL、架构图和面向业务的指南——并内置交叉引用验证，确保所有产出物之间的一致性。

### 核心特性

- **端到端 PRD 交付** — 从需求范围确认到最终打包，整个工作流通过 6 个阶段自动完成。
- **5 大核心交付物** — PRD 主文档、ER 图（Mermaid）、API 设计文档、验收测试用例和数据库 DDL。
- **交叉引用验证** — 对实体、字段、关系和 API/测试覆盖率进行多维度一致性检查。
- **问题自动修复** — 按严重程度（Critical → Major → Minor）优先修复已识别的问题。
- **架构图与业务指南** — 生成交互式技术架构图（HTML + Mermaid）和面向利益相关方的业务指南（含 UI 原型）。
- **并行执行** — 利用子代理同时生成独立文档，提升交付效率。

### 工作流阶段

| 阶段 | 说明 |
|------|------|
| **1. 需求范围确认** | 收集产品信息，界定范围边界，与利益相关方确认 |
| **2. 核心文档生成** | 并行生成 5 个交付文件（PRD、ER、API、测试、DDL） |
| **3. 交叉引用验证** | 执行 4 个维度的一致性检查，生成完整性报告 |
| **4. 问题修复** | 按严重程度修复问题，支持子代理并行处理 |
| **5. 架构图与业务指南** | 创建交互式技术架构图和面向业务的 HTML 指南 |
| **6. 整合与打包** | 将架构图合并到 PRD 中，将所有文件打包为 ZIP 归档 |

### 项目结构

```
full-prd-workflow/
├── SKILL.md                          # 技能定义与工作流指令
├── README.md                         # 本文件
└── templates/
    ├── prd-template.md               # PRD 主文档模板
    ├── api-design-template.md        # API 设计文档模板
    ├── er-conventions.md             # ER 图命名与语法规范
    ├── test-case-template.md         # 验收测试用例模板
    ├── ddl-conventions.md            # 数据库 DDL 命名与结构规范
    └── completeness-checklist.md     # 交叉引用验证清单
```

### 交付产出

一个完整的交付包包含：

```
{项目名}_Deliverables_{版本号}.zip
├── {项目名}_PRD.md                   # 产品需求文档
├── {项目名}_ER.mermaid               # 实体关系图
├── {项目名}_API_Design.md            # API 接口规范
├── {项目名}_Acceptance_Tests.md      # 全覆盖测试用例
├── {项目名}_DDL.sql                  # 数据库表结构定义
├── {项目名}_Technical_Diagrams.html  # 交互式架构图
├── {项目名}_Business_Guide.html      # 利益相关方指南（含 UI 原型）
└── {项目名}_Completeness_Report.md   # 交叉引用验证报告
```

### 安装方式

该技能适用于 QoderWork 平台，安装步骤如下：

1. 将 `full-prd-workflow/` 文件夹放入技能目录（`~/.qoderwork/skills/`）。
2. 重启或刷新 QoderWork 会话。
3. 技能将自动可用——通过要求"写一个 PRD"或"生成产品需求文档"即可调用。

### 使用方式

只需向 AI Agent 描述你的产品或功能需求，例如：

> "为一个支持周期性支付、套餐管理和账单生成的订阅计费平台编写完整的 PRD。"

技能将引导 Agent 完成需求确认、文档生成、验证和最终打包的全流程。

### 验证维度

内置的交叉引用检查覆盖四个维度：

- **实体一致性** — PRD 实体、ER 图实体和 DDL 表必须完全对齐。
- **字段一致性** — 字段名、类型和约束在 PRD、ER 和 DDL 之间保持一致。
- **关系一致性** — ER 关系对应 DDL 外键，无孤立实体。
- **API 与测试覆盖率** — 每个 PRD 模块都有 API 端点，每个端点都有验收测试。

### 许可证

MIT License. 详见 [LICENSE](LICENSE)。
