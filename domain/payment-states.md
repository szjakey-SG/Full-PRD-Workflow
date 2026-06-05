# 支付领域标准状态定义

> 版本：2.0.0 | 维护：架构负责人
> 所有生成文档中的支付相关状态枚举必须以本文件为唯一来源，不得自创状态名。

---

## 1. Subscription 订阅状态

```
状态机（标准）：

Draft → Trialing → Active → Past_Due → Non_renewing → Canceled
                 ↓                  ↓
              Paused             Expired

完整状态列表：
```

| 状态 | 说明 | 终态 | 可恢复 |
|------|------|------|--------|
| Draft | 已创建未激活，尚未开始计费 | 否 | — |
| Trialing | 试用期中 | 否 | — |
| Active | 正常计费中 | 否 | — |
| Past_Due | 最近一次扣款失败，Dunning 进行中 | 否 | 是（扣款成功后→Active）|
| Paused | 商户手动暂停，停止计费 | 否 | 是（恢复后→Active）|
| Non_renewing | 订阅到期不续费，当前周期仍然有效 | 否 | 否 |
| Canceled | 订阅已取消，停止计费 ⚠️ 不可逆 | 是 | 否 |
| Expired | 订阅自然到期（trial/non_renewing 后）⚠️ 不可逆 | 是 | 否 |

**合法转换：**
- Draft → Trialing（免费试用开始）
- Draft → Active（直接激活，无试用）
- Trialing → Active（试用期满，首次扣款成功）
- Trialing → Past_Due（试用期满，首次扣款失败）
- Active → Past_Due（周期扣款失败）
- Active → Paused（商户暂停）
- Active → Non_renewing（商户取消续费，当前周期保持）
- Past_Due → Active（Dunning 扣款成功）
- Past_Due → Canceled（Dunning 耗尽）
- Paused → Active（商户恢复）
- Non_renewing → Expired（当前周期结束）
- Any → Canceled（强制取消）⚠️ 高风险

---

## 2. Invoice 账单状态

| 状态 | 说明 | 终态 |
|------|------|------|
| Draft | 草稿，尚未向商户呈现 | 否 |
| Open | 已生成，等待支付 | 否 |
| Paid | 支付成功 ⚠️ 不可逆 | 是 |
| Past_Due | 到期未支付（Dunning 触发后）| 否 |
| Void | 作废（替换/错误账单）⚠️ 不可逆 | 是 |
| Refunded | 已全额退款 ⚠️ 不可逆 | 是 |
| Partially_Refunded | 已部分退款 | 否 |
| Failed | 支付失败且不再重试 ⚠️ 不可逆 | 是 |

**合法转换：**
- Draft → Open（账单正式下发）
- Open → Paid（支付成功）
- Open → Past_Due（到期未支付）
- Open → Void（作废）
- Past_Due → Paid（补款成功）
- Past_Due → Failed（放弃追款）
- Paid → Refunded（全额退款）
- Paid → Partially_Refunded（部分退款）

---

## 3. Payment Attempt 支付尝试状态

| 状态 | 说明 | 终态 |
|------|------|------|
| Created | 支付请求已创建 | 否 |
| Processing | 正在处理（已发送至支付通道）| 否 |
| Succeeded | 支付成功 ⚠️ 不可逆 | 是 |
| Failed | 支付失败（可重试）| 是 |
| Canceled | 已取消 ⚠️ 不可逆 | 是 |
| Timeout | 超时（未收到通道回调）| 是 |
| Requires_Action | 需要用户额外操作（3DS等）| 否 |

**合法转换：**
- Created → Processing（发送至通道）
- Processing → Succeeded（通道成功回调）
- Processing → Failed（通道失败回调）
- Processing → Timeout（超时无回调）
- Processing → Requires_Action（需要3DS验证）
- Requires_Action → Processing（用户完成验证）
- Requires_Action → Failed（验证超时/拒绝）
- Created → Canceled（主动取消）

---

## 4. Dunning 追款状态

| 状态 | 说明 |
|------|------|
| Active | 追款进行中 |
| Succeeded | 追款成功（某次重试付款成功）|
| Exhausted | 追款耗尽（所有重试均失败）|
| Canceled | 追款取消（商户主动取消）|

**标准 Dunning 策略（可按项目配置）：**
- 失败后 Day 1：立即重试
- Day 3：第二次重试
- Day 7：第三次重试（发送 Payment Link）
- Day 14：最终重试，失败后标记 Exhausted，Subscription → Canceled

---

## 5. 标准业务对象命名规范

| 对象 | 数据库表名 | 英文单数 |
|------|-----------|---------|
| 订阅计划 | subscription_plan | SubscriptionPlan |
| 客户 | customer | Customer |
| 订阅 | subscription | Subscription |
| 账单 | invoice | Invoice |
| 账单明细 | invoice_line_item | InvoiceLineItem |
| 支付尝试 | payment_attempt | PaymentAttempt |
| 支付方式 | payment_method | PaymentMethod |
| 追款记录 | dunning_record | DunningRecord |
| 退款 | refund | Refund |
| 优惠券 | coupon | Coupon |
| 折扣 | discount | Discount |
| Webhook事件 | webhook_event | WebhookEvent |

---

## 6. 标准事件列表（Webhook Events）

```
subscription.created          subscription.activated
subscription.trial_started    subscription.trial_ended
subscription.past_due         subscription.paused
subscription.resumed          subscription.non_renewing
subscription.canceled         subscription.expired

invoice.created               invoice.paid
invoice.past_due              invoice.voided
invoice.refunded

payment.created               payment.succeeded
payment.failed                payment.timeout
payment.requires_action

dunning.started               dunning.attempt_failed
dunning.succeeded             dunning.exhausted

payment_link.sent             payment_link.paid
```
