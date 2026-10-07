# memory-affect-01：入口指引

> 本目录是故渊“长期认知连续性层”第一轮系统调研与架构收敛区。  
> **从这里开始读。**
>
> 当前目标不是立刻定 schema 或写代码，而是先把：
> Continuity / Memory / Cognition / Affect / Motivation / Self Model / Concern / Commitment / Task / Retrieval / Wake
> 的职责、权威和连接关系理清。

---

# 1. 当前一句话定位

故渊现在要做的已经不只是“记忆库”，而是一层：

> **Long-term Cognitive Continuity Layer / 长期认知连续性层**

它负责让近期发生的事情持续流动，让历史可追溯，让理解、情绪、自我认知与未完成事项能够演变，并让这些过去在合适的时候重新参与当前活动。

当前核心流向：

```text
Global Event Stream
        ↓
Active Continuity
        ↓
Evidence / Event
        ↓
Consolidation
        ↓
Memory / Narrative / Concern / Affect / Self Model
        ↓
Retrieval / Wake / Injection
        ↓
当前活动
        ↓
新的 Global Event
```

---

# 2. 最推荐的阅读顺序

## 如果只想快速知道“我们现在决定到哪了”

按这个顺序：

1. **[00c-长期认知系统-整体架构候选框架-2026-10-07.md](./00c-长期认知系统-整体架构候选框架-2026-10-07.md)**
   - 当前最重要的总框架；
   - 看系统整体怎么运转；
   - 看 Continuity / Memory / Self / State / Action 怎样连接。

2. **[00b-记忆情绪系统-研究参考地图-2026-10-07.md](./00b-记忆情绪系统-研究参考地图-2026-10-07.md)**
   - 看每个区域参考了什么；
   - 哪些已经基本接受；
   - 哪些还要细化；
   - 哪些只是候选。

3. **[00d-Light-Balanced-Full运行复杂度方案-2026-10-07.md](./00d-Light-Balanced-Full运行复杂度方案-2026-10-07.md)**
   - 共享本体与 authority 的三套运行安排；
   - 尚未选择，Balanced 仅为比较建议；
   - 看成本、失败/恢复、本人任务/子代理与升级条件。

4. **[00a-四专题正文复核-阶段性对象边界-2026-10-07.md](./00a-四专题正文复核-阶段性对象边界-2026-10-07.md)**
   - 看为什么会得到现在这套对象边界；
   - 适合追溯设计演变和争议。

Context / Harness 侧新增候选讨论见 [故渊上下文管理与 Context Epoch](../故渊上下文管理与Context-Epoch-讨论记录-2026-10-08.md)：目前仅为 CANDIDATE，重点是 Active Continuity → Context Projection → Context Epoch、低 churn semantic projection、compaction/hydration 与 provider cache 的待核边界。

工具 / MCP / 主动性侧新增候选讨论见 [Tool / Skill / MCP / Capability 与主动性循环](../Tool-Skill-MCP-Capability与主动性循环-讨论记录-2026-10-08.md)：重点是 Capability Projection、Tool Slim/Observation Projection、体验型 vs 委派型行为、Opportunity → Desire → Intention 边界，以及低成本后台 watcher / worker 只产机会候选而不替主体行动。

当前两项决定查 [ADR-001](../decisions/ADR-001-self-ownership-and-affect-projection-2026-10-07.md)；七组影响与推理查 [第二阶段](../memory-affect-02-第二阶段-内部状态表示与运行契约裁决-2026-10-07.md)；续接查 [checkpoint](../../handoffs/memory-affect-长期认知连续性-Checkpoint-2026-10-07.md)，跨模块 OPEN 查 [集中入口](../OPEN-QUESTIONS.md)。旧阶段/研究解释“为什么”，00c 描述 current。

---

## 如果想追研究依据

先读：

- **[00-横向汇总-摘要.md](./00-横向汇总-摘要.md)**

然后按专题进入：

1. [01-memory-ontology.md](./01-memory-ontology.md)
2. [02-event-evolution.md](./02-event-evolution.md)
3. [03-open-loops.md](./03-open-loops.md)
4. [04-affect-appraisal.md](./04-affect-appraisal.md)
5. [05-memory-affect-coupling.md](./05-memory-affect-coupling.md)
6. [06-world-person-model.md](./06-world-person-model.md)
7. [07-consolidation-retrieval-runtime.md](./07-consolidation-retrieval-runtime.md)

`_snapshots/` 保存调研时的一手源码/资料摘录，平时不用从这里开始读。

---

# 3. 每份总览文件分别负责什么

## 00 · 横向汇总

**角色：研究摘要入口**

回答：
- 七专题共同收敛出了什么；
- 哪些机制被多份材料共同支持；
- 哪些冲突还没裁。

它是研究压缩，不是最终设计。

---

## 00a · 阶段性对象边界

**角色：设计推理与边界演变记录**

回答：
- Evidence / Event / Fact / Interpretation 怎么分；
- Narrative Line / Concern / Commitment / Task / Trigger 为什么拆开；
- Emotion / Mood / Attitude / Preference 怎么重新划界；
- 哪些早期结论被正文复核后修正。

适合回看“为什么这么设计”。

---

## 00b · 研究参考地图

**角色：参考索引 + 状态地图**

回答：
- 每个节点有哪些理论、项目、代码可参考；
- 当前状态是：
  - ✅ 已基本接受
  - 🟡 需要细化
  - 🔴 冲突待裁
  - ⚪ 纯参考候选
- 哪些区域下一步需要详细讨论。

以后新增论文、项目、机制，优先挂回这张地图。

---

## 00c · 整体架构候选框架

**角色：当前总架构主文件**

回答：
- 故渊整体系统现在长什么样；
- 信息怎样流动；
- Continuity、Memory、Affect、Self、Action、Retrieval、Wake 怎么连接；
- 哪些是长期结构，哪些是运行时状态；
- 三份新参考：
  - Global-Context-Sync
  - Latent-memory
  - Nocturne-Memory-Core
  分别补在哪一层。

以后讨论整体架构，以 00c 为主。

---

## 00d · 三套运行复杂度候选

**角色：共享本体/authority 的运行安排对比**。描述处理时机、本人任务/子代理、外部条件检测、恢复/失败、接线成本和升级依据；不选择具体框架、数据库、参数或部署。每档都保留共同契约，尚未由用户选择。

---

# 4. 当前已经比较稳定的原则

下面这些目前不需要反复重开大讨论，除非出现新证据：

- Evidence 原始证据只增不改；
- Event 记录“发生了什么”，不等于当前解释；
- Fact / Interpretation / Appraisal 等派生必须保留 provenance；
- 历史取代使用 supersession / invalidation，不静默覆盖；
- valid time 与 record / transaction time 分开；
- Narrative Line 是可重算叙事组织，不是任务状态机；
- 一事件可属于多条线；
- “未完成”不等于“已经规划下一步”；
- Concern / Commitment / Task / Trigger 不应混成一个万能 open-loop；
- Affect Annotation（当时感受）与当前 Emotion / Mood 分开；
- Retrieval 不能因为“被召回”就自动强化 importance；
- 确定性约束可以硬门，主观/体验信号主要软排序；
- 模型优先产候选，不直接改写高权威对象；
- 窗口不是系统级边界，连续性不应依赖“换窗总结”。
- 命名 Affect 为本体，V/A 为版本化 Base Projection + 慢变 Subject Calibration 派生；Mood 是派生慢层；Echo 与持久 Affect Change Event 分开；
- Self Ownership 由主体决定、Host 验证签发 receipt，普通叙述不取得认领权；
- capability 与历史 receipt 分开，域内证明不互换；消费不等于当前 Permission；
- 曲线拐点 anchor + read-time derived，游标/as-of/投影与校准版本/freshness/未结算差异可见；历史感受不被当前 Calibration 改写。

---

# 5. 当前最需要继续细化的区域

优先级大致如下：

## A. Concern / Commitment / Task / Trigger

要裁清：
- 什么只是“仍挂着”；
- 什么算明确约定；
- 什么才成为执行任务；
- 什么只是未来重新检查的条件；
- 它们怎样升级，但不能静默越级。

---

## B. Interpretation / Preference / Attitude / Person / Self Model

要裁清：
- “怎么理解”
- “偏好什么”
- “长期怎么看某对象”
- “怎么看自己”
- “人物/世界模型怎么组织这些内容”

之间的边界。

---

## C. Affect：Appraisal → Emotion → Mood → Chord

第二阶段的表示/时间/持久原则已吸收，不重开广搜。剩余：命名维度与参数、D 观测、discrete/interlocks、文本锚/Recent 聚合/Baseline Chord、Calibration 历史分类与长期证据门槛；见 00b §6/10。

---

## C2. Motivation：Drive → Desire → Intention

Need 目标/时间原则、对象化 Desire、认领与独立 Task 登记、生成边/结算边已吸收。剩余：生命周期具体编码、任务/承诺达成验真、合法参数/不应期与预算；旧迁移表不授权修改旧模块。

---

## D. Continuity → Consolidation

要裁清：
- Active Continuity 保留多久；
- 什么内容只是短期流动；
- 什么时间点开始整理；
- 什么立刻形成长期结构；
- 什么永远只当近期上下文。

---

## E. 全系统权威 / 时间 / provenance 契约

已确认 Subject-owned decision + Host-issued authority receipt 与版本化投影；横向 Authority / Capability / Receipt 组织仍 CANDIDATE。需继续细化跨进程重验、到期/撤销/重放、共同确认、Commitment 和 Permission 消费位置。以下是概念契约，具体字段/schema 未定：
- source
- author
- basis
- event_time
- record_time
- valid_at
- invalid_at
- superseded_by
- confidence
- writer / reviewer / authority

---

## F. 消费契约

要给每类对象标出：
- 常驻；
- Passive Recall；
- keyword trigger；
- 按需检索；
- 软排序；
- 硬门；
- Wake candidate；
- UI only；
- 默认沉默。

---

# 6. 新增参考放哪里

新增理论 / 项目 / 实现时，不要直接改 00c 让总框架越长越胖。

推荐流程：

1. 先判断它补的是哪一块；
2. 写入或补充对应专题；
3. 更新 **00b 研究参考地图**；
4. 如果它改变了宏观结构，再更新 **00c 整体架构**；
5. 如果它改变了对象边界，再补 **00a** 的演变记录。

这样“参考”和“当前设计”不会重新混在一起。

---

# 7. 当前外部参考的角色

## 早期两份核心参考

- `memo/记忆管线架构.md`
  - 机制参考库；
  - 工程经验；
  - 整理 / 召回 / 影子 / 评测 / 多视图。

- `memo/Nameless-Coastline-记忆系统.md`
  - 产品交互；
  - 门 / 排名 / 席位；
  - Gazing Back；
  - Shadow；
  - 轻量召回。

## Continuity / Self Continuity 新参考

- `memo/Global-Context-Sync.md`
  - Continuity；
  - 全局事件流；
  - 窗口只是 consumer。

- `oliscatt/Latent-memory`
  - Passive Recall；
  - 宿主 hook；
  - 临时注入；
  - Retrieval / Injection 工程。

- `memo/Nocturne-Memory-Core.md`
  - Self Continuity；
  - 当前重新认领过去；
  - AFFIRM / REJECT / SUSPEND；
  - Fluid / Evolving / Stable Self 的参考。

---

# 8. 进入实现前还缺什么

当前还**不建议直接定最终数据库表**。

至少要先完成：

1. 高耦合对象契约；
2. 状态迁移规则；
3. 权威写入规则；
4. Continuity / Consolidation 时序；
5. Retrieval / Injection / Wake 消费规则；
6. Self Model 与 Affect 的边界。

本轮已经形成三套共享本体/authority 的运行复杂度候选（00d），尚未选择。用户选择/修正并裁决相关 OPEN 后，后续另定范围才进入：

- schema；
- API；
- 存储；
- 索引；
- Serein 复用 / 迁移；
- Harness 接线；
- 窄验证。

---

# 9. 给新 Agent / 新窗口的最短指引

如果你是第一次进入这个目录：

> **先读 00c，再读 00b。**
>
> 需要理解“为什么”时看 00a。
>
> 需要核研究证据时看 00 和 01–07。
>
> Work / Codex / 普通聊天窗口都要遵守项目级 [窗口上下文持久化与 Checkpoint 约定](../../handoffs/窗口上下文持久化与Checkpoint约定.md)：聊天上下文只视为缓存，改变 current 的结论必须落文件；上下文压缩后先重读 checkpoint/current 再继续。
>
> 不要把旧参考方案自动当成新系统前提。
>
> 不要把“用户同意记录”理解成“设计已经定稿”。
>
> 不要在高耦合对象契约尚未讨论清楚前直接写最终 schema 或实现。

---

# 10. 当前阶段名称

> **两项用户决定已核对，Authority / Capability / Receipt 最小职责与七组影响已复核，阶段 checkpoint 已保存，成熟结论已吸收 00b/00c；Light / Balanced / Full 三套运行复杂度候选已形成、未选择。本轮停止在设计层，待用户选择/修正与裁决 OPEN；最终 schema/API/implementation 尚未开始。**

运行流程只是纸面设计，新后端组合/接线未定。**宿主原则已有用户决定**：故渊可不承担 wake loop，主运行与适用的调度机制可交框架；旧模拟定时唤醒退出新核心默认要求，详见 [ADR-002](../decisions/ADR-002-host-and-wake-ownership-2026-10-07.md)。此前已有 framework-04/05 比较与离线证据，PydanticAI Core + 按需 Harness 为当时优先建议，LangGraph/Pi 保留候选；从这些入口针对新需求续接，不从零广搜，不强制先选三档。具体组合未拍板，未授权实现。

framework-06 的 Hermes 研究与另获用户授权的 Linux 退出/会话窄实验已回档；[主窗口局部复核（2026-10-08）](../../reports/framework-06/主窗口复核-2026-10-08.md)核账并收紧并发、调度/沙箱归属、统计/恢复条件及 Windows 版本建议。组合仍 CANDIDATE，未改变 current 本体/authority，也未销账旧完整 C6；P1/P2追加核账与组合讨论入口见复核§6（108进程/84会话；Node版本建议仍CANDIDATE）。framework-06任务状态见[原卡](../../../guyuan/docs/tasks/framework-06-host-and-scheduling-review.md)。2026-10-08按用户要求编制[framework-07组合与配置可修改性任务卡](../../../guyuan/docs/tasks/framework-07-composition-and-configurability-review.md)，加入[日后改设定／配置的关注点](../运行设定与配置可修改性-讨论记录-2026-10-08.md)，Hermes研究现已交付、主窗口局部复核完成，见[复核记录](../../reports/framework-07/主窗口复核-2026-10-08.md)。调度配套与配置加载分层，authority不因同栈自动满足；没有选择组合或配置生效策略，不自动进入实验或实现。
