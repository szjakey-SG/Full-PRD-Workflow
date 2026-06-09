# 自动化运行策略

> 版本：1.0.0 | 适用 Skill：payment-prd-workflow v2.1+

---

## 一、自动化触发场景

### 场景分类

| 触发类型 | 场景 | 触发条件 | 推荐频率 |
|---------|------|---------|---------|
| **定时扫描** | PRD 完整性周期巡检 | 时间驱动 | 每周一 09:00 |
| **变更响应** | 需求变更后一致性检查 | 文档变更驱动 | 变更后即时 |
| **质量回归** | Prompt 升级后 Golden Case 回归 | Skill 版本变更 | 每次 SKILL.md 更新 |
| **发布前检查** | 上线前 Checklist 自动生成 | 发布 Tag 创建 | 每次发布前 |
| **每日摘要** | 团队 AI 使用质量日报 | 每天定时 | 每天 18:00 |

---

## 二、策略 A：PRD 完整性周期巡检（每周）

**目标**：确保在研项目的 PRD 文档集持续符合完整性标准，及时发现字段漂移、状态机不一致等问题。

**触发时间**：每周一 09:00

**执行 Prompt（自动化任务内容）**：
```
你是 payment-prd-workflow Skill 的自动化巡检 agent。

执行以下周期性完整性巡检：

1. 从项目文档目录读取最新版本的以下文件（如果存在）：
   - *_PRD.md
   - *_ER.mermaid
   - *_DDL.sql
   - *_API_Design.md
   - *_State_Machine.md

2. 加载 templates/completeness-checklist.md，执行 5 维交叉校验。

3. 对照 domain/payment-states.md，检查所有状态枚举是否符合标准。

4. 输出巡检报告，格式：
   ## 周期巡检报告 — {date}
   | 项目 | Critical | Major | Minor | 评分趋势 |
   上周：{score} → 本周：{score}（{+/-N}）
   
   [如有问题] 具体问题列表 + 修复建议
   [如无问题] ✅ 文档集健康，无需处理

5. 如发现 Critical 问题，在报告末尾附加：
   "⚠️ 发现 Critical 问题，建议在本周 Sprint 中优先修复。"
```

**输出**：Markdown 巡检报告，保存为 `reports/weekly_check_{date}.md`

---

## 三、策略 B：需求变更响应检查（即时）

**目标**：当 PRD 或设计文档发生变更时，立即检查变更是否导致跨文档不一致。

**触发条件**：检测到 `*_PRD.md` 或 `*_ER.mermaid` 文件修改时间变化

**执行 Prompt**：
```
你是 payment-prd-workflow Skill 的变更影响分析 agent。

检测到以下文件发生变更：{changed_files}

执行变更影响分析：

1. 读取变更文件，识别变更内容（新增字段/删除字段/状态变更/模块增删）。

2. 对变更内容执行定向一致性检查（只检查受影响维度，不全量扫描）：
   - 字段变更 → 检查 ER + DDL + API 中对应字段
   - 状态变更 → 检查 State_Machine + 测试用例中对应状态
   - 模块新增 → 检查 API 和测试用例中是否有对应覆盖

3. 输出变更影响报告：
   ## 变更影响分析 — {changed_files} — {timestamp}
   变更摘要：{1-2句描述}
   影响范围：{受影响的文件列表}
   
   | 影响项 | 严重度 | 受影响文件 | 需要的同步操作 |
   
   建议处理方式：立即同步 / 下个 Sprint 处理 / 无需处理

4. 如影响严重度为 Critical，输出：
   "🚨 变更导致数据模型不一致，建议在合并前修复。"
```

---

## 四、策略 C：Skill 质量回归（版本驱动）

**目标**：每次 SKILL.md 更新后，自动对 Golden Cases 执行回归测试，确认输出质量未下降。

**触发条件**：`SKILL.md` 的 `version` 字段发生变更

**执行 Prompt**：
```
你是 payment-prd-workflow Skill 的质量回归 agent。

Skill 版本已从 {old_version} 更新至 {new_version}。

执行以下回归验证：

1. 读取 eval/golden-cases/ 目录下所有 case 的 input.md 和 expected_score.json。

2. 对每个 case，模拟用户输入并生成对应的核心交付物摘要（不需要生成完整文档，
   只需生成 PRD 的前两个功能模块 + 对应状态机作为质量样本）。

3. 加载 eval/quality-rubric.md，对生成的样本执行评分。

4. 与 expected_score.json 中的期望分对比，计算偏差。

5. 输出回归报告：
   ## Skill 质量回归报告 — v{old} → v{new}
   | Case | 期望分 | 实际分 | 偏差 | 判定 |
   
   整体结论：
   - PASS：所有 case 偏差 <= 10 分
   - WARN：1-2 个 case 偏差 > 10 分，建议人工复查
   - FAIL：3+ 个 case 偏差 > 10 分，建议回滚 Prompt 变更

6. 将结果追加到 eval/golden-cases/regression_log.md。
```

---

## 五、策略 D：每日 AI 使用质量日报（定时）

**目标**：汇总当天团队对 Skill 的使用情况和输出质量，供团队负责人每日 Review。

**触发时间**：每天 18:00

**执行 Prompt**：
```
你是 payment-prd-workflow Skill 的日报生成 agent。

生成今日（{date}）AI 使用质量日报：

1. 统计今日 Skill 调用情况（从 quality_self_report 日志中读取）：
   - 调用次数
   - 各指令使用频率（/生成PRD / /生成状态机 / ...）
   - 平均 Quality Score
   - 触发安全拦截次数
   - BLOCKED（评分<70）次数

2. 输出日报：
   ## AI 使用质量日报 — {date}
   
   今日概览：调用 {N} 次 | 平均分 {X}/100 | 拦截 {N} 次
   
   质量分布：
   APPROVED(>=90): {N}次 | APPROVED WITH NOTES(80-89): {N}次
   NEEDS_REVISION(70-79): {N}次 | BLOCKED(<70): {N}次
   
   待关注项：{Quality Score最低的输出摘要，如有}
   
   建议：{基于数据的1条运营建议}

3. 如当日 BLOCKED 次数 >= 3，在日报末尾加：
   "⚠️ 今日 BLOCKED 率偏高，建议 Skill Owner 检查对应 Prompt。"
```

---

## 六、调度配置参考

### Cron 表达式

| 策略 | Cron | 说明 |
|------|------|------|
| 策略 A（周巡检）| `0 9 * * 1` | 每周一 09:00 |
| 策略 D（日报）| `0 18 * * 1-5` | 工作日 18:00 |

### 策略优先级与资源消耗

| 策略 | 优先级 | Token 消耗 | 推荐模型 |
|------|--------|-----------|---------|
| B（变更响应）| P0 | 低（定向检查）| claude-haiku-4-5 |
| A（周巡检）| P1 | 中（5维扫描）| claude-sonnet-4-6 |
| C（质量回归）| P2 | 中（样本生成+评分）| claude-sonnet-4-6 |
| D（日报）| P3 | 低（日志汇总）| claude-haiku-4-5 |

---

## 七、JIT 自动化运行原则

1. **按需触发优于定时触发**：策略 B（变更响应）比策略 A（周巡检）更符合 JIT 精神，应优先建设。
2. **最小化输出**：自动化任务只输出"异常"和"需要人工介入的项"，正常情况静默完成。
3. **不替代人工决策**：自动化任务只做检查和报告，不自动修复文档（修复需人工确认）。
4. **短路保护**：任何自动化任务执行时间 > 5 分钟，应输出中间进度并暂停等待确认。
5. **失败降级**：自动化任务失败时，输出失败原因并通知 Skill Owner，不静默忽略。
