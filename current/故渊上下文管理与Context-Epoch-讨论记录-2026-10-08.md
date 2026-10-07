# 故渊上下文管理与 Context Epoch · 讨论记录

> 日期：2026-10-08（Asia/Shanghai）  
> Status: **CANDIDATE / DISCUSSION RECORD**  
> 性质：记录 2026-10-08 关于滚动上下文、缓存复用、Context Projection、Active Continuity 与 Context Epoch 的讨论。  
> 本文不是 schema、实现卡或 provider 选型；未选择 Pi / OpenAI / Codex OAuth 路径，也未验证具体缓存命中率、费用或配额折算。

---

## 1. 触发问题

用户提出：

> 如果故渊使用滚动窗口，模型上下文是否会长期处于接近满额状态？  
> 如果如此，上下文缓存是否会非常重要？  
> 若未来用 Pi 等后端并接入 GPT Plus / Codex 类订阅路径，缓存是否还能命中？

这暴露出一个比“窗口多长”更基础的问题：

> **故渊需要单独设计 Context Management / Context Projection，而不能把 Active Continuity 简化成“每轮把最老消息删掉一点”。**

---

## 2. 当前最重要的候选判断

### 2.1 不推荐逐轮左删的永久满窗

以下形态不建议作为默认方向：

```text
轮 1: A B C D E + F
轮 2:   B C D E F + G
轮 3:     C D E F G + H
```

原因不仅是信息连续性，还包括缓存复用与上下文可解释性：

- 每轮都改变靠前部分；
- 前缀稳定性差；
- 压缩/删减行为持续发生；
- 状态投影、工具结果、历史消息容易互相挤占；
- 很难区分“内部状态改变”和“模型可见上下文需要改变”。

这不是说任何 provider 都一定按完全相同的 prefix-cache 规则工作，而是：

> **稳定前缀 + 后部追加**是一种更健壮、对多数前缀缓存机制更友好的组织方式。

具体 provider 的缓存算法、TTL、计费/配额行为以后必须按实际版本复核。

---

## 3. 候选总体形态：Epoch-based bounded context

当前更值得继续讨论的方向：

> **不是永远左删的 rolling window，而是有界 Context Epoch。**

候选结构：

```text
① Stable Prefix
────────────────
核心身份 / system policy
稳定 tool definitions
少量必须常驻的长期约束

② Current Projection
────────────────
当前 Self / Affect / Motivation
当前 Concern / Activity
必要 Person / World projection
当前 authority / freshness 摘要

③ Context Epoch
────────────────
本 epoch 的原始对话
tool calls
agent activity
持续 append

④ Ephemeral Tail
────────────────
临时大块 tool result
刚读取的资料
短期检索结果
允许若干轮后退出
```

同一 epoch 中尽量：

```text
Stable + Projection + A B C
Stable + Projection + A B C D
Stable + Projection + A B C D E
```

而不是每轮从左侧切掉一个旧片段。

---

## 4. Context Epoch 的边界

### 4.1 不追求 100% 填满

上下文需要为以下内容留 headroom：

- 模型输出；
- reasoning / hidden compute 需要的输入预算；
- tool result 突增；
- Passive Recall / Retrieval 临时注入；
- 外部任务回流；
- 错误恢复与补充证据。

因此 Context Epoch 更适合使用：

```text
normal growth
   ↓
high watermark
   ↓
compaction / hydration
   ↓
new epoch at lower watermark
```

讨论中举过 70–80% / 40–55% 作为直觉例子，**不是当前参数决定**。

具体 high / low watermark 必须按：
- 模型上下文上限；
- 最大输出需求；
- 工具负载；
- retrieval 临时预算；
- 真实 cache / latency / quality 测试

再标定。

---

## 5. Active Continuity 与 Context Projection 分工

当前 00c 已有：

```text
Global Event Stream
        ↓
Active Continuity
        ↓
Consolidation
        ↓
Long-term Cognition
```

本轮新增候选理解：

> **Active Continuity 不应直接等于“发给 LLM 的字符串”。**

中间需要一个 **Context Projection** 职责：

```text
Global Event Stream
        ↓
Active Continuity
        ↓
Context Projection
        ↓
Context Epoch
        ↓
LLM / Agent Host
```

Context Projection 的职责是：

- 从完整近期状态中选择当前模型真正需要的部分；
- 尽量保持 prompt-visible 内容稳定；
- 保留 as-of / freshness / revision；
- 控制 token 预算；
- 避免内部细小波动每轮重写 prompt；
- 给 Retrieval / Passive Recall 留临时插槽；
- 在 epoch 重建时生成 hydration 输入。

它是**消费投影**，不是新的事实源。

---

## 6. 三种 revision 必须分开

这是本轮最值得保留的候选概念之一：

### State Revision

内部真实状态发生变化。

例如：
- Affect 数值变化；
- Mood 派生值变化；
- Drive / Concern 状态变化；
- Active Continuity 收到新事件。

### Projection Revision

变化已经达到“模型需要看到不同语义”的程度，因此 prompt-visible projection 更新。

例如：
- “轻微疲惫 0.41 → 0.43”可能不需要；
- “稳定 → 明显疲惫，开始影响活动选择”可能需要。

### Context Epoch

当前上下文结构需要整体重建。

例如：
- 达到 high watermark；
- compaction 完成；
- 任务上下文发生大型切换；
- 旧 epoch 已不适合继续 append。

因此：

```text
State Revision
≠ Projection Revision
≠ Context Epoch
```

不能用同一个 revision counter 代替三者。

---

## 7. Low-churn / Semantic Projection

Affect/Motivation 第二轮已经出现一个可复用启发：

> 内部状态可以连续变化，但 prompt-visible state 不必跟着每个小数点跳动。

候选方式：

- coarse / bucketed projection；
- 语义阈值；
- hysteresis；
- 最短驻留；
- 重要 lifecycle change 强制 revision；
- authority / permission / task state 变化不应因缓存优化被延迟。

目标不是“为了 cache 少更新”，而是：

> **只在模型看到不同内容真正有意义时更新投影。**

正确性优先于 cache。

不能为了保持稳定前缀：
- 隐藏真实 Task/Permission 变化；
- 延迟危险状态；
- 保留已经失效的事实；
- 让 stale projection 冒充 current。

---

## 8. Epoch compaction / hydration 候选流程

```text
Current Epoch
   ↓ high watermark

保存：
- Global Event / Evidence 已独立落盘
- consumer cursor / computed-through
- current task/activity state
- 必要 raw tail

生成：
- Active Continuity projection
- bridge / checkpoint summary
- unresolved references
- retrieval handles

   ↓

New Context Epoch

Stable Prefix
+ current semantic projection
+ bridge/checkpoint
+ selected raw tail
+ later append-only turns
```

关键边界：

- compaction 不删除 Evidence；
- summary / bridge 不是事实源；
- hydration projection 必须有 as-of / cursor；
- epoch 死亡不是“事件结束”；
- 换 epoch 不自动关闭 Concern / Task / Intention；
- compaction 失败不应导致历史丢失。

---

## 9. Tool / Retrieval / Passive Recall 的上下文寿命

### Tool result

不是所有工具回执都应永久常驻 prompt。

候选分层：
- 当前操作必须读：直接留 tail；
- 后续需要引用但内容过大：保留 handle / extracted fact / evidence ref；
- 仅临时中间结果：若已被权威结果替代，可退出 prompt；
- 原始结果是否长期保存由 Evidence / task artifact 规则决定，不由 prompt 生命周期决定。

### Retrieval / Passive Recall

最好作为：
- 临时插入；
- 带来源和用途；
- 可在若干轮后退出；
- “被注入”与“模型实际使用”分开；
- 不因为缓存或召回频率反向强化 importance。

---

## 10. 与缓存优化的关系

Context Management 的第一目标仍然是：

1. 正确；
2. 连续；
3. 可恢复；
4. token / latency / cost 可控。

缓存复用是重要工程约束，但不是事实层设计原则。

当前候选优化：

- Stable Prefix 尽量稳定；
- 同 epoch 内 append 优先于反复重写历史；
- Current Projection 使用 semantic revision，减少无意义 churn；
- 工具定义和稳定 policy 避免每轮动态重排；
- 大型 compaction 尽量发生在明确 epoch boundary；
- 新 epoch 后重新建立稳定增长区。

### Provider-specific OPEN

未来若使用：
- Pi；
- OpenAI API；
- OpenAI Codex OAuth / ChatGPT subscription；
- 其他 provider；

必须分别复核：
- cache key / prefix 规则；
- session id 语义；
- compaction 是否破坏缓存；
- cache TTL；
- cached input 是否影响费用或订阅配额；
- 工具定义变化对缓存的影响；
- API 与订阅路径是否使用同样计量逻辑。

**本讨论不把任何 provider 当前缓存行为提升为故渊架构事实。**

---

## 11. 与 Work / Codex 项目工作流的类比

项目自身目前也采用：

```text
stable docs
+ active task
+ checkpoint
+ compaction
+ next epoch
```

这与故渊候选 Context Management 结构相似，但二者不是同一系统。

项目工作流的 checkpoint 是协作纪律；
故渊 Context Epoch 是运行架构候选。

不要因为两者形式相似就自动复用同一 schema。

---

## 12. 当前候选信息流

```text
                 Global Event Stream
                         │
                         ▼
                  Active Continuity
                         │
                         ▼
                 Context Projection
          ┌──────────────┼──────────────┐
          ▼              ▼              ▼
    Stable Prefix   Semantic State   Retrieval Slots
                         │
                         ▼
                   Context Epoch
                 append-only growth
                         │
             high watermark reached
                         ▼
              Compaction / Hydration
                         │
                         ▼
                  Next Context Epoch
```

并行地：

```text
Evidence Archive
Memory / Self / Affect / Motivation / Task
```

继续作为真实状态来源。

Context Projection 只消费它们，不取代它们。

---

## 13. 这轮明确不决定

以下仍是 OPEN：

- exact context budget；
- high / low watermark；
- raw tail 长度；
- projection semantic threshold；
- projection hysteresis；
- compaction 何时使用模型；
- bridge summary 格式；
- 不同任务是否共享一个 epoch；
- 主对话 / 本人任务 / 子代理是否各有独立 context consumer；
- 工具大结果保留几轮；
- cache-aware prefix 的具体 serializer；
- Pi / OpenAI / Codex OAuth 的最终 provider cache 机制；
- 是否需要显式 cache instrumentation；
- cache miss / hit 是否进入运行观测指标。

---

## 14. 对现有架构的候选影响

### Continuity

补出一层：
> Active Continuity → Context Projection → Context Epoch

### Injection

Injection 需要区分：
- stable projection；
- semantic state projection；
- ephemeral recall；
- temporary tool evidence。

### Affect / Motivation

继续支持：
> internal state revision ≠ prompt-visible projection revision

### Runtime / Host

Host / Harness 未来需要负责：
- consumer cursor；
- context budget；
- epoch boundary；
- compaction / hydration；
- projection freshness；
- provider-specific cache telemetry（若可用）。

### Memory

Memory 不因 Context Epoch 切换而创建/关闭对象；
Evidence Archive 与 context lifecycle 分离。

---

## 15. 下一步建议

先把本记录作为 Context / Harness 设计输入。

等 framework-07 与宿主组合讨论完成后，再决定是否将 Context Projection / Context Epoch 正式提升为 current architecture，并为选定 host 核对：

- 哪些能力框架已有；
- 哪些需要故渊补；
- cache-aware serializer 是否值得做；
- compaction / session restore 与 provider cache 如何兼容。

在此之前不要进入具体 schema 或定死 watermark 参数。
