# 专题 05 · Runtime / Persistence / Action Boundary

> 日期：2026-10-07
> 状态：第二轮并行调研报告（研究，不是设计定稿、不是 schema、不是实施授权）
> 任务卡：`guyuan/docs/tasks/memory-affect-02-affect-motivation-research.md` §8 专题 05
> 证据上限：**E3**（只读源码/固定文档；本轮未安装、未运行任何候选项目，未发任何模型请求）
> 范围：只回答「Affect 与 Motivation 分开之后，运行时长什么样：哪些每轮算、哪些落盘、什么值得长期保存、Motivation 怎样接 Intention / Wake / Task 而不越权」。情绪生成本体归 01，Drive/Desire 本体归 02，双向耦合归 03，Chord 表达归 04，召回工程归 07。本报告不选最终存储/schema。
> 禁令遵守声明：未把 Drive 塞回 Affect；未把 Intention 当 Task；未让 Motivation 绕过 Cognition/Permission 直接执行；未让每轮 Δ 自动变长期记忆；未安装/运行候选项目；未改故渊产品代码；未改 memory-affect-01 框架文件；未把旧 25 维当验收标准。
> 成本口径：本报告所有「每轮计算 / 存储 / LLM 调用」均为**工程推算**，标 `basis: world_prior`，非实测（本轮无安装运行）。所有外部论文数字为**论文声称**，未复现。

---

## 1. 本专题真正要解决的问题

01、02 把本体分开了：**Affect 回答「我现在感觉怎样」，Motivation 回答「我被什么推动」**。但分开之后，运行时立刻暴露四个此前被「一锅情绪三层」掩盖的工程问题：

1. **每轮到底算什么、存什么**。旧方案把「数值可算」默认成「每轮都要持久」。真实约束是：数值几乎全是**可重算的派生量**，只有**有意义的变化**才值得落盘。若每轮把 25 维×每轮写库，日志会爆炸；若什么都不存，又丢掉「为什么变了」的可追溯性。
2. **不活动时怎么演化**。故渊红线：衰减必须用**解析式 `f(t)`**，⛔ 禁止 tick 累加。这决定了「长时间不活动」不能靠每个 tick 往上加——必须闭式现算。本专题要把这条红线推广到 mood 回弹、drive 回落、聚合全部数值层。
3. **什么才升级为长期对象**。情绪 Δ、Desire、Intention 都可能只是运行态；什么时候形成 **Affect Change Event / Concern / Intention / Task**，需要明确的**触发门槛**与**权威写者**。
4. **Motivation 怎样接行动而不越权**。Drive/Desire 只能提供 bias；Intention 必须经认知判断 + 主体认领；Task 必须经独立登记。旧方案里「驱力超阈值直接驱动行为」这条路径是**必须删除的越权**。

**本专题不解决**：不选数据库、不定 schema、不定模型路由、不盘点 Serein、不安装/运行候选。输出是**机制候选 + 接口契约 + 可套用的运行方案 + 成本**。

---

## 2. 必要术语与边界

### 2.1 本报告的关键术语（含来源）

| 术语 | 定义 | 关键来源 |
|---|---|---|
| **Runtime State 运行态** | 进程内、每轮可算、可丢弃并从权威点重建的状态（内存 snapshot） | Event Sourcing（Fowler 2005，E2） |
| **Ledger 台账** | append-only、只增不改、记录**有意义变化**序列的持久结构（≠ 每轮数值） | Event Sourcing（E2）；Nocturne（E1） |
| **Derived View 派生视图** | 读时从权威点 + 输入现算的值，不落库 | CQRS / read model（Fowler，E2） |
| **Analytic Decay 解析式衰减** | 用闭式 `x(t)=x₀·0.5^((t−t₀)/HL)` 现算，任一时刻可由 `(x₀,t₀,参数)` 直接求出；⛔ 不做逐 tick 累加 | 故渊红线；FAtiMA（E3）；emmem（E3） |
| **EWMA 指数平滑** | 一阶低通 `sₜ=α·xₜ+(1−α)·sₜ₋₁`，用于平滑抖动；二阶加动量产生**惯性/hysteresis** | arXiv 2601.16087（E2） |
| **Hysteresis 迟滞 / 双阈值** | 用**上/下双阈值 + 最短驻留**避免在单一阈值附近反复抖动（flapping） | 工程范式（E1/E0）；2604.27872（E2） |
| **Change-Point 变点** | 序列分布发生结构性变化的时刻；在线检测（BOCPD）用于「何时真的变了」 | Adams & MacKay 2007，arXiv 0710.3742（E2） |
| **Salience Threshold 显著性门槛** | 只有累积显著度/重要性越过门槛才触发写入或反思 | Generative Agents，arXiv 2304.03442（E2） |
| **Affect Change Event** | 满足「阈值 / 持续 / 认领」触发条件、值得长期留痕的**一次情绪变化** | 专题 01 §2.4-Q7；本报告 §2.4-Q5 |
| **Intention 承诺** | 经认知判断 + 主体认领形成的行动承诺，会约束后续推理，需 reconsideration 才撤 | BDI / Jason（专题 02 E3） |
| **Wake Gate 唤醒闸门** | 决定「是否值得主动重新考虑」的确定性/轻量前置过滤，默认沉默 | 本报告 §4.6；00c §11（E2） |

### 2.2 必须先划清的六条边界

1. **数值可算 ≠ 要落盘**：core affect、mood、drive 值、chord 都是 derived；落盘的是**事件**与**权威对象变化**。
2. **Ledger ≠ 每轮快照**：ledger 是稀疏的「有意义变化」序列；per-round 数值属于运行态。
3. **Snapshot ≠ Ledger**：snapshot 是低频覆盖式当前值（用于重启恢复/可读）；ledger 是只增历史。
4. **衰减只能解析式**：任何时间演化用 `f(t)` 现算；不活动 = 无 tick = 无累积；⛔ 禁止 drive/mood meter 逐 tick 上涨。
5. **模型只产候选**：appraisal / 情绪命名 / Desire / Intention 一律「候选」，高权威对象写入门槛更高。
6. **从内部状态到对外行动必须过闸**：Drive/Desire/情绪强度**不得**直接生成 Task 或对外动作（与 00c §11、02 §7 护栏一致）。

### 2.3 四层运行节奏（综合 00c §5 与 GraphRAG/07）

| 层 | 时机 | 允许做的事 | 允许的事否落盘 | 典型 LLM? |
|---|---|---|---|---|
| **实时** | 每轮对话内 | 算 derived 值、阈值/持续检测、拼上下文 | 只落**极轻**：事件候选、必要状态位 | 否（appraisal 不进实时链） |
| **短延迟** | 内容滑出 active context 后 | 初步 Event / 分类候选 | 落事件候选、desire/intention 状态迁移 | 可选小模型 |
| **后台巩固** | 定时（cron/心跳，分钟~小时级） | 归线、聚合、态度/concern 更新 | 落长期对象新版本（失效旧版不删） | 是（appraisal 批处理） |
| **批处理重算** | 夜间/按需 | 索引、聚类、摘要、视图重算 | 幂等重写派生（内容哈希） | 可选 |

### 2.4 21 个必答问题逐条回答

> 下列 21 条即任务卡 §8「必须回答」。先给结论，机制证据见 §3/§4，写入契约见 §6.2，运行方案见 §9。

#### Affect

**Q1. 哪些每轮计算？**
全部 **derived，纯确定性，不落库**：
- Core Affect 当前值 `(v,a)`：由上次权威点 `(x₀,t₀)` 经解析式 `f(t)` 现算；
- Mood 当前值：同法（慢时间常数）；
- 情绪片段强度：逐类型半衰期 `f(t)` 现算（无 off-by-tick）；
- Current Chord：由数值**确定性查表**映射（归 04）；
- 阈值/持续/hysteresis 检测位。
每轮计算量 = O(维数) 次浮点（含指数）。即使 25–100 维，单轮 CPU **< 1 ms**（推算，`basis: world_prior`）。

**Q2. 哪些只保存当前 snapshot？**
Core Affect、Mood、Action Tendency、Current Chord、Emotion portfolio（内存态）。它们可由 `(x₀,t₀,参数)` + 输入流重算，故默认只在内存；仅落**一个低频 last_state 行**用于进程重启恢复（覆盖式，不是历史）。Emotion portfolio 若要「可查当时为什么这样」，则由其 `cause_ref + appraisal_basis` 事件承载，而非存每轮向量。

**Q3. 是否要保留 Affect ledger？**
**要，但必须是稀疏的事件台账**：ledger 记 **Affect Change Event**（满足 Q5 触发条件的变化），**不记每轮 Δ**。依据：Event Sourcing 只记「有意义的状态变化」（E2）；Nocturne「主动留下」而非睡前总结（E1）；专题 01 §2.4-Q7。它是「历史可追溯 + 可回放」的唯一低成本载体。

**Q4. 每轮 Δ 是否落盘？**
**否。** 默认只在内存/运行态（对应任务卡 §1 前提 6）。仅当该轮 Δ 使**累计**变化满足 Q5 的触发条件时，才把这次变化**提升为一条 append-only event**。

**Q5. 什么变化才形成长期事件？**
三选一（可组合），**默认沉默**：
- (a) **幅度阈值**：某情绪/核心情感累计偏移越过显著阈值；
- (b) **持续条件**：跨 N 轮/时间窗仍维持（排除单轮噪声）；
- (c) **主体认领**：AFFIRM / REJECT / SUSPEND 或重评改变原因/意义；
- 附加 (d) 它**实质改变了一个长期对象**（态度/关注/记忆写入），或 (e) 触发线变点（BOCPD 判定结构变化）。
依据：Generative Agents 用 importance 阈值触发反思（E2）；专题 01 §2.4-Q7；本报告 §4.3/§4.4。

**Q6. Current / Recent / Baseline 各自多久更新？**
- **Current**：每轮（内存，不落盘）；
- **Recent**：滚动窗口聚合（最近 K 轮/时间窗），每轮读时现算或事件补齐；
- **Baseline**：慢，低频（天/周级后台批处理），只有**长期认领的证据**才推动它。
三者是**同一底层数值流的不同时间常数派生视图**，不是三个独立持久状态。Baseline 与 Self Model 的 Stable 层对齐（00c §8）。

#### Motivation

**Q7. Drive 是 snapshot、ledger，还是两者都有？**
**主体是 derived view（读时现算）**：`Drive = f(deficit, incentive, 环境, 记忆, 价值)`，由慢参数 + 当前对象算出，不落「每轮 drive 值」。可选**稀疏 ledger 记权威偏移事件**（哪次事件改变了哪个 drive 的 offset/baseline），用于可审计——即 **state = derived；history = ledger(events only)**。坚决不落「每 tick drive 数值」。

**Q8. Desire 是否需要持久化？**
**需要，但持久的是生命周期状态机**：`candidate → active →(satisfied | abandoned | suspended→Concern | promoted→Intention)`，带 `target_ref + basis + provenance`；**不持久每轮强度数值**（强度 derived）。只有**可解析到对象**的 Desire 才登记；无对象欲望不登记，降级为 Drive/Mood 背景偏置（与 02 §2.2 答 6 一致）。

**Q9. Desire 离开 active context 后什么时候消失，什么时候形成 Concern？**
四条规则：
- 被满足/显式放弃 → **关闭**（保留历史，不删）；
- 因「当前上下文不再提及」而降活 → 进 **dormant**（降低可见度，不删）；
- 若它对应「世界仍未解决」的开放状态，且超过 T（时间/轮数）仍无解决证据 → **升级为 Concern**（Concern 不要求存在 want，只管「这事还挂着」，00b/00c 定义）；
- 长期 dormant 且无对象/证据再出现 → **归档（archival）**，非删除。
关键边界：Desire 有 want；**Concern 是 open-loop，无必需的 want**（02 §2.2 答 8）。

**Q10. Intention 由谁写？**
**Cognitive Deliberation 层 + 主体认领**写。Motivation 只能产 `intention_candidate`；模型只能产候选；**Drive/Desire 不得直接写 Intention**。对应 BDI 的 `selectOption → selectIntention` 在 agent 循环内完成（Jason E3，专题 02 §4.5）。

**Q11. Intention 怎样取消 / 悬置 / 冲突解决？**
状态机 `running / waiting / suspended / satisfied / dropped`（借 Jason `Intention.State` 语义，E3）。取消需 **reconsideration 触发条件**（不能每轮 reconsider，避免 thrash）；悬置 = `suspended` 保留复活可能；冲突用「**上下文加权 + hysteresis + 显式互斥检测**」，最终**交 Cognition 裁决**（不设 Maslow 硬层次，02 §2.2 答 5）。

**Q12. Drive / Desire 是否可以成为 Wake candidate？**
**可以作软信号/候选参与度，但不能直接生成 Wake 行为或 Task。** 规则：Drive/Desire 提高「值得重新考虑」的分数；Wake 闸门由 **Concern / Commitment / Task / Trigger + 外部状态 + 权限**决定；**默认沉默**（00c §11）。强 Drive **不得**直接生成 Task 或对外行动。

**Q13. 哪一步必须经过认知判断或主体认领？**
- Desire → Intention：必须经 **Cognitive Deliberation**；
- Intention → Task：必须经 **独立登记步骤**（可要求 Permission）；
- 任何「内部状态 → 对外行动」的越级一律禁止；
- 高权威长期对象（态度 / 自我 / 原则）写入必须**主体认领**。

**Q14. 哪一步才允许建立 Task？**
只在 **Intention（已认领承诺）→ 独立登记步骤** 之后。**Drive/Desire/情绪强度/「驱力超阈值」都不允许直接建 Task**（旧方案该路径必须删除，见 §5）。

#### 工程

**Q15. 哪些更新可纯确定性？**
解析式衰减值 `f(t)`；Core Affect / Mood 数值演化；情绪强度聚合（log-sum-exp，FAtiMA E3）；阈值/持续检测（EWMA / CUSUM / BOCPD 类可确定性算）；temporal aggregation（窗口均值/EWMA）；和弦映射（确定性查表）；去重/幂等（内容哈希，mem0 E3）。**零 LLM**。

**Q16. 哪些需要模型 appraisal / interpretation？**
Event→appraisal 变量（desirability/expectedness/coping/norm）；情绪类型命名；Desire 候选；Intention 候选；语义判断（事件是否触及某 concern）。→ 全部**只产候选**，进**批处理派生**，**不进实时链路**（沿用 04/05/07 结论）。

**Q17. 哪些状态需要 provenance？**
一切派生/解释性对象：Emotion（`cause_ref + appraisal_basis`）、Affect Change Event（cause）、Attitude（证据累积）、Desire（basis/target）、Intention（cause + 认领）、Concern（开放事件）。纯计算派生量（core affect 值 / drive 值 / chord）**不需** provenance（可重算）；但一旦它成为**权威快照**，必须带 `source/author/basis`（沿用事件三字段红线）。

**Q18. 哪些状态需要 valid_at / supersession？**
长期对象：Attitude、Interpretation、Self Model、Concern/Commitment、Intention、Desire 状态迁移。用 `valid_at / invalid_at / superseded_by`，**不删**（Graphiti 双时钟，07 E2）。运行态数值**不需**（可重算）。Append-only 事件**不需要** supersession（天然不可变）。

**Q19. 如何避免每轮状态都写数据库导致日志爆炸？**
五条组合：(a) 数值状态 derived、读时现算，不落库；(b) 只把**事件**落 append-only；(c) 触发门槛 = 阈值/持续/认领，默认沉默；(d) 低频 snapshot（覆盖式）替代每轮行；(e) 内容哈希幂等防重复写；(f) 分层运行（实时只落极轻）。
**量化对比**（推算，`basis: world_prior`）：朴素每轮落 N=25 维 × 50 轮/对话 ≈ 1250 行 ≈ 0.5–1 MB/对话；事件化后 = 每对话 3–15 条事件 × ~300–800 B ≈ 2–12 KB/对话 → **约 50–100× 缩减**。

**Q20. 如何避免长时间不活动时 tick 累加？**
**根本手段是解析式 `f(t)`**：任何「从上次权威点 `(x₀,t₀)` 到当前 `t`」的演化都用**闭式公式**（衰减 / 回弹 / 聚合），完全不依赖中间 tick。因此**无活动 = 无 tick = 无累积误差**；下次唤醒一次性算出现在值。⛔ 禁止任何「每 tick 写入/累加」的 drive meter。身体变量（疲惫/唤醒）若需 accrual，也用闭式（线性/指数闭式）而非逐 tick。

**Q21. 解析式衰减 / 回弹适用于哪些对象，不适用于哪些？**
- **适用**（本质是「对稳态的偏离」）：Core Affect 瞬时偏移、Emotion 强度 spike、Mood 向 baseline 回弹、Action Tendency、Body 状态（疲惫/唤醒）闭式回归。
- **不适用**（证据/认领驱动，不能只按时间衰减）：Attitude/Attachment、Appraisal/basis、Stable Self/Value、Concern（靠显式关闭或长期无证据归档）、Desire 的「存在」（靠满足/放弃，而非时钟）。
- 附加：情绪**终止**也应含「重评 / 解决 / let_go」，不只时钟（Frijda/Verduyn，专题 01 §2.4-Q6）；半衰期须**逐类型可配**。

### 2.5 横向必答（每个变量逐项）——见 §7.2 表

---

## 3. 理论/机制候选总表

> 证据：论文正文/官方文档 = E2；只读源码 = E3；工程范式/README = E1；检索摘要 = E0。年份为原文年份。

| # | 机制候选 | 来源 | 证据 | 核心机制 | 对故渊 runtime 的可用性 |
|---|---|---|---|---|---|
| 1 | **Event Sourcing / append-only 日志** | Fowler 2005（martinfowler.com/eaaDev/EventSourcing.html） | E2 | 只记「有意义的状态变化」；当前态 = 从事件回放/投影派生；事件按过去时命名 | 高：Affect Change Event / Desire 状态迁移 / Intention 生命周期的落盘范式；**只记变化不记每轮值** |
| 2 | **CQRS / read model 分离** | Fowler（martinfowler.com/bliki/CQRS.html） | E2 | 写模型（命令）与读模型（查询投影）分离；读模型可重建 | 高：数值 derived view = read model；ledger = write model |
| 3 | **双时钟 / 边失效（valid_at/invalid_at）** | Graphiti / Zep（专题 07 §4.1，E2/E3） | E2 | 矛盾只 invalidate 不物理删；支持 as-of 查询 | 高：Attitude / Interpretation / Concern 版本链；= 故渊既有 supersession 原则 |
| 4 | **解析式指数衰减** | FAtiMA `DecayEmotion`（E3）；emmem `decay.py`（E3） | E3 | `I=λI₀·exp(...)` 闭式；不 tick | 高：直接复用算法（非代码）；红线一致 |
| 5 | **一阶指数平滑 EWMA** | arXiv 2601.16087（2026-01） | E2 | `sₜ=αxₜ+(1−α)sₜ₋₁`；给状态「持久性」防突变 | 高：mood/聚合逐轮平滑；防抖动 |
| 6 | **二阶动量 / Affective Hysteresis** | arXiv 2601.16087 | E2 | 加动量项产生**惯性**；hysteresis = 上升/回落轨迹间面积，随动量增大 | 中高：表达「情绪缓解路径不同于恶化路径」；代价是响应变慢（有 tradeoff） |
| 7 | **双阈值迟滞 / dead zone** | 工程范式（E1/E0）；arXiv 2604.27872（E2） | E1/E2 | 上/下双阈值 + 最短驻留，防阈附近 flapping | 高：事件触发门槛、Intention 冲突裁决；**开销近零** |
| 8 | **在线变点检测 BOCPD** | Adams & MacKay 2007, arXiv 0710.3742 | E2 | 在线后验「run length」，判序列分布结构变化 | 中：判「是否真的变了」的客观门槛；在线 O(t) 需剪枝，注释：成本需实测 |
| 9 | **Salience/importance 阈值门** | Generative Agents, arXiv 2304.03442 | E2 | importance 累积越阈触发 reflection；recency = 指数衰减（factor 0.995） | 高：写入/反思的触发门槛样板；本报告 §4.4 |
| 10 | **Temporal aggregation（窗口/EWMA 聚合）** | 2604.27872；2601.16087；Generative Agents | E2 | 连续信号→慢聚合（Recent/Baseline） | 高：Current/Recent/Baseline 三层派生 |
| 11 | **BDI 意图生命周期 + reconsideration** | Rao & Georgeff 1995；Kinny & Georgeff 1991（bold/cautious/single-minded）；Jason（专题 02 E3） | E2/E3 | Intention 有 `running/waiting/suspended`；reconsideration 策略控制何时重考虑 | 高：Intention→Task 的越级护栏范本；**不自动产生外部效应** |
| 12 | **轻量唤醒触发器（trigger head）** | arXiv 2605.30152（TGL，2026）；Microsoft Research | E2 | 用极轻模型/确定性信号判「是否唤醒」，LLM 只在触发后调 | 高：Wake Gate 的工程范式；**避免空转烧 LLM** |
| 13 | **心跳式调度（heartbeat scheduler）** | arXiv 2604.14178（2026） | E2 | 周期心跳触发认知模块（Planner/Critic/Recaller）；周期外静默 | 中：Wake/cron 参考；**须配确定性 gating 否则烧钱**（见 §8） |
| 14 | **Snapshot + 有界回放** | Event Sourcing（E2） | E2 | 定期 snapshot 限制回放长度 | 高：last_state 行 + 事件日志，重启恢复 |
| 15 | **内容哈希幂等去重** | mem0 8 阶段（专题 07 §4.2，E3） | E3 | `md5(text)` 去重；单函数多入口幂等 | 高：单 VPS 幂等、防重复写 |
| 16 | **分层运行（实时/后台/批处理）** | GraphRAG update_*（07 E3）；00c §5（E2） | E2/E3 | 实时/收口/夜间/批处理分工 | 高：成本控在「不频繁调 LLM」 |

> 共 16 项，覆盖「事件溯源 / 状态动力学 / 变点 / 门槛 / BDI / 唤醒 / 工程运行」七类来源，满足「至少两类不同来源」。代码级关键结论见 §4（部分引自 01/02/07 的 E3 核查）。

---

## 4. 重点候选深查

### 4.1 Event Sourcing / CQRS（E2，落盘范式地基）
- **机制**：状态不是「一行可覆盖」的记录，而是「事件序列 + 投影」。Fowler 定义核心为「Capture all changes to an application state as a sequence of events」；当前态 = 回放/投影派生；命名必须过去时（`RefundToolReturned`，不是 `IssueRefund`）。**Command（可能被拒）≠ Event（既成事实）**。
- **对故渊**：正好解决 Q3/Q4/Q19——**只记有意义的变化**（Affect Change Event、Desire 状态迁移、Intention 生命周期、态度新版本），每轮数值做 read model（derived）。External Query 需把响应记进流（LLM 调用即 Fowler 的 External Query）——故渊的 appraisal（模型调用）其结果必须**落成事件**，回放时不再调模型。
- **工程教训（E1/E2）**：Overeem 等 2021（arXiv:2104.01146）研究 19 个事件溯源系统归纳 5 种 schema 演化策略；其中 in-place / copy-and-transform **会改写日志**。故渊口径：**domain 层 append-only，schema 迁移作为受管例外并留变更记录**（与 AGENTS.md「原始证据不静默改写」一致）。
- **不吻合**：完整 ES 的「全量回放」对陪伴型 agent 过重；故渊取「事件 + 低频 snapshot」的有界形态即可。

### 4.2 解析式衰减 + 指数平滑（E3/E2，动力学基座）
- **解析式衰减**（E3）：FAtiMA `DecayEmotion` 用 `λ=ln(HalfLifeConst)/T_half; I=I₀·exp(λ·Decay·Δt)`；emmem `effective_decay=base·(1−0.5·arousal)·(1/(1+0.1·count))`。二者都**闭式现算、不落衰减值**（01/05 已核）。
- **一阶/二阶动力学**（E2，arXiv 2601.16087，2026-01）：论文引入 agent 级连续 VAD 状态，用**无记忆估计器**抽瞬时信号，再用**指数平滑（一阶）或动量（二阶）**积分，注入生成但不改模型参数。结论：stateless 无连贯轨迹；state persistence 带来延迟响应与可靠恢复；**二阶动量引入 affective inertia 与 hysteresis，随动量增大，稳定 vs 响应存在 tradeoff**（原文摘要）。
- **对故渊**：一阶平滑用于「防单轮抖动」；二阶动量**按需可选**（表达「缓解比恶化慢」的迟滞），但**必须可关**，且注入 prompt 的强度要有界（Sentipolis 警示：emotion-aware 会轻微降低规范遵守，01 §4.9）。二阶状态需要两个权威点 `(x, ẋ, t)` 才能闭式现算——**仍是解析式，不违反红线**。

### 4.3 变点检测 BOCPD（E2，客观的「真的变了」门槛）
- **机制**（Adams & MacKay 2007, arXiv 0710.3742）：在线维护「run length」后验 `p(rₜ|x₁:ₜ)`；每来一个点，后验或「增长（无变点）」或「归零（有变点）」；框架把「变点算法」与「数据模型」解耦。
- **对故渊**：给 Q5 提供一个**非拍脑袋**的触发判据——「累计变化是否构成结构性变化」。可确定性算（配合剪枝），零 LLM。**成本提示**：在线 BOCPD 维护 run-length 分布，朴素实现 O(t) 内存/步，长序列必须**剪枝（pruning）+ 只对少数关键维跑**（推算，`basis: world_prior`）。
- **不吻合**：BOCPD 原假设 i.i.d. 段；情绪/mood 是有趋势慢变，需先做去趋势或用可容趋势的变体——**属故渊需补**。

### 4.4 显著性门槛 / 双阈值迟滞（E2/E1，事件触发）
- **Generative Agents**（E2，arXiv 2304.03442）：retrieval 用 `recency(指数衰减 factor 0.995) + relevance + importance`；**reflection 在 importance 累积越阈（150）时触发**——工程上「写入门槛」的直接样板。
- **双阈值迟滞**（E1/E2）：单一阈值会让状态在阈值附近 per-frame 抖动；工程标准解法是**上界进入、下界退出 + 最短驻留 + 输入低通**（EWMA）。arXiv 2604.27872 也区分 stateless / 一阶 / 二阶（迟滞）三种「关注轨迹」动力学。
- **对故渊**：事件触发用「**阈值 + 持续窗 + 迟滞**」组合，避免噪声写入（与 Q5、§7 冲突裁决一致）。开销近零。

### 4.5 BDI 意图生命周期与 reconsideration（E3，越权护栏范本）
- **源码核查**（引自专题 02 §4.5，**本报告未复核**）：`jason-lang/jason`，main @ commit `2c2d7e1c1ea3bd5cef9712d551632ba367109741`；`Intention` 是 `IntendedMeans` 栈，`State{running, waiting, suspended, undefined}`，有 `setSuspended/getSuspendedReason`；`Agent.selectIntention` 默认轮转（不饿死）；`Option = Plan + Unifier + Event`。
- **理论**（E2）：Rao & Georgeff 1995 区分 commitment 语义（blindly / single-minded / open-minded）；Kinny & Georgeff 的 **bold / cautious / single-minded** reconsideration 策略——决定「多久暂停重考虑意图」。
- **对故渊**：**Intention 有完整生命周期与 reconsideration，却从不自动产生外部效应**——这是「Intention ≠ Task」最硬的一手证据（Q10/Q11/Q14）。故渊应保留「Intention→Task 的独立登记步骤」，并用 reconsideration 策略控制抖动（配合 §4.4 的双阈值）。

### 4.6 唤醒闸门 / 轻量触发器（E2，Wake 的工程范式）
- **TGL 触发器**（E2，arXiv 2605.30152 / Microsoft Research）：核心论点——「always-on 的触发若本身是大模型，会成为最贵组件」（论文举例某 demo 每 15 秒尝试一次，每小时 240 次全 LLM 成本）。改用**小模型/图上的节点分类**做 trigger head，LLM **只在触发后**调；论文报告 11.13 ms/event（GPU）与 13.99 ms（笔记本，约 220 MiB BF16）。**数字为论文声称，未复现**。
- **心跳调度**（E2，arXiv 2604.14178）：周期心跳 + 可学习调度器选择认知模块（Planner/Critic/Recaller/Dreamer），空闲期做反思/巩固。
- **对故渊**：**Wake 默认沉默 + 确定性 gate 先筛 + LLM 只在触发后调**。禁止「心跳每跳都调 LLM」。这是把唤醒成本从「每 15 分钟一次调用」压到「真触发才调用」的关键（Q12、§9 成本）。

### 4.7 旧故渊 runtime 线索（E3，仅作对照）
- `core/decay.py::exponential_decay(x0, half_life, dt) = x0*0.5^(dt/half_life)`（E3，task-14 记载）；`core/affect/layers.py` 三层数值演化；`core/affect/chord.py` 确定性查表；`core/clock.py` 单一时钟；wake loop 唤醒时「三层演化→合成→查表→注入 `<guyuan_now>`」；serein 侧异步副 LLM 产 PADCN→查表。详见 §5。

---

## 5. 旧故渊方案逐项复核（runtime 视角）

> 对象：旧「25 维三层」的**运行/持久化机制**（本体分类错误已在 01/02 复核，此处只查 runtime）。核对材料为 task-14 记载的接口与红线（E3/项目内部），**未重新读源码**。

| 旧机制 | 复核判断 | 理由 |
|---|---|---|
| **解析式衰减 `f(t)=x0*0.5^(dt/half_life)`** | ✅ **保留**（红线） | 正确、可重算、不 tick；是全报告的地基 |
| **单一时钟 `core/clock.py`** | ✅ **保留** | 红线：core 之外不得有第二处 `datetime.now()` |
| **数值层读时现算、衰减值不落库** | ✅ **保留** | 与 ES/derived 一致；旧方案的优点 |
| **unknown 不填默认值** | ✅ **保留** | 与 provenance 一致 |
| **离散通道并发激活 + 互锁（suppress/amplify）** | 🟡 **保留为表达/标签层** | 可作标签冲突规则，**不作生成本体**（会掩盖「为何产生」） |
| **PADCN 稳态回弹** | 🟡 **保留但收敛** | 方向对；收敛到 V/A（±D）；C/N 移出（01 Q9） |
| **和弦确定性查表** | ✅ **保留**（归 04） | 表达层，确定性，零 LLM |
| **meaning_weight 乘数（note×2.5…let_go×0.5）** | 🟡 **保留但归位** | 本质是「有效半衰期 = base × 乘数」；应并入 **Affect Change Event / 记忆显著性**，**不是 core affect 参数** |
| **驱力层「期望值 + 当前值 + 时间衰减 + 饱足期」** | 🟡 **部分保留** | 期望值可作 baseline/need；**反对自由自然增长**（02 答 3）；饱足期→保留为 satiation 不应期（refractory） |
| **「驱力超阈值直接驱动行为，独立于情绪」** | ❌ **删除** | **越权路径**，与 00c §11 / 02 §7 护栏冲突；Drive 只能 bias |
| **wake loop：唤醒时三层演化→查表→注入** | 🟡 **结构保留，语义收窄** | 唤醒时用 `f(t)` **现算**（不 tick）；注入为**软信号**；不得由强 Drive 越级生成 Task/对外行动 |
| **serein 异步副 LLM 产 PADCN** | ✅ **保留但归位** | 属 appraisal 批处理，**只产候选**，不进实时链 |
| **「每轮持久化」**（旧方案未见此证据） | — | 旧方案数值层读时现算，**未暴露每轮落库**；本轮明确不引入 |

**旧 runtime 三处缺口（需新补）**：
1. **无 Affect ledger / 事件触发门槛**：旧方案没有「什么变化才长期留痕」的显式规则；
2. **无 Desire 状态机 / Concern 升级路径**：Desire→Concern 缺权威迁移规则；
3. **无 Intention→Task 登记闸门**：旧方案只有「驱力超阈值驱动行为」，缺认知认领与登记（须换成 BDI 式护栏）。

---

## 6. 对新架构的可用性拆分

### 6.1 归类

| 归类 | 内容 | 来源/等级 |
|---|---|---|
| **可直接复用（算法，非代码）** | ① 解析式半衰期衰减式；② 一阶 EWMA 平滑；③ log-sum-exp 情绪聚合；④ RRF/哈希幂等 | E3（01/05/07）/E2 |
| **可直接复用（结构）** | ① Event + projection 的 append-only；② 双时钟 `valid_at/invalid_at`；③ BDI 意图状态机语义；④ snapshot + 有界回放 | E2/E3 |
| **可借鉴机制** | ① 显著性阈值触发写入；② 双阈值迟滞防抖；③ 轻量 trigger head + 触发后才调 LLM；④ 分层运行（实时/后台/批处理）；⑤ 二阶动量迟滞（可选） | E2/E1 |
| **需要补（故渊缺口）** | ① 事件触发门槛（阈值+持续+认领）的**具体参数化**；② Desire 状态机与 Concern 升级规则；③ Intention→Task 的登记闸门；④ Wake Gate 的确定性打分函数；⑤ 心理 need 的 setpoint/量纲 | 综合 |
| **不建议采用** | ① 每轮数值落库 / 每 tick drive meter；② 无门控 LLM 自动 UPDATE/DELETE；③ 「命中即强化」；④ 心跳每跳调 LLM；⑤ 完整 ES 全量回放；⑥ 完整 EFE 每轮规划 | 综合 |

### 6.2 状态写入契约表（每类状态：定义层 / 谁写 / 何时写 / 存什么字段 / 何时删或失效）

> 这是本专题的核心可交付物。三类落点：**derived**（不落）、**snapshot**（低频覆盖）、**ledger/event**（append-only）。字段为候选，非最终 schema。

| 状态对象 | 定义层 | 谁写（权威） | 何时写 | 存什么字段（候选） | 何时删/失效 |
|---|---|---|---|---|---|
| **Core Affect (V/A,±D)** | Affect 运行态 | 确定性层 | 每轮**算**，不写库；仅 low-freq last_state | `v,a[,d], updated_at`（last_state 行） | 覆盖式更新；无需 supersession |
| **Mood** | Affect 运行态 | 确定性层 | 每轮算；慢聚合 | `mood_v,mood_a, updated_at` | 归零阈值；覆盖 |
| **Emotion 片段（portfolio）** | Affect 运行态 | 确定性层 + appraisal 候选 | 事件进来时创建；每轮算强度 | `type,intensity,valence,cause_ref,target,appraisal_basis,decay_params`（内存） | 强度<阈 → 移除；被取代留 superseded |
| **Affect Change Event** | Affect 长期留痕 | 确定性触发（Q5 三条件） | **仅触发时** | `id,ts,kind,subject,delta,threshold_hit,cause_ref,source,author,basis` | **不删**（不可变） |
| **Affect Annotation** | Memory 派生 | 编码期（冻结） | 事件写入时一次性 | `event_ref,core_affect_snapshot,appraisal_ref` | 不删、不改 |
| **Chord (Current/Recent/Baseline)** | Affect 表达视图 | 确定性映射 | Current 每轮；Recent/Baseline 低频 | `chord_symbol,level,ts`（快照可选落） | 派生；可重算 |
| **Drive state** | Motivation | 确定性层（读时现算） | **不写每轮值** | `drive_id,intensity,deficit,incentive_ref,basis,valid_at`（derived，读时算） | 派生；不落 |
| **Drive offset ledger（可选）** | Motivation 台账 | 事件触发（Q7） | 权威偏移时 | `drive_id,from,to,basis,event_ref,ts,source,author` | 不删 |
| **Desire** | Motivation | Desire 管理器（确定性 + 候选） | 候选生成 / 状态迁移时 | `desire_id,drive_ref,target_ref,intensity(derived),state,evidence,provenance` | 关闭/放弃 → 失效但留历史；转 Concern |
| **Action Tendency** | Affect×Motivation 接口 | 确定性层（情绪触发） | 每轮算 | `direction,intensity,source_emotion_ref`（内存） | 随情绪回落 |
| **Intention** | Cognition（承诺） | Deliberation + 主体认领 | 认领时；reconsider 时 | `intention_id,cause,target,state(running/waiting/suspended/satisfied/dropped),basis,provenance` | reconsider / 达成；留版本 |
| **Concern** | Continuity/Memory | 升级规则（Desire→Concern / 开放事件） | 超 T 未解决时 | `concern_id,open_event_ref,status,since,valid_at,superseded_by` | 显式关闭 / 长期无证据归档 |
| **Task** | 执行层 | **独立登记步骤**（可要求 Permission） | 登记时 | `task_id,intention_ref,state,owner,permission,basis` | 完成/取消；留历史 |
| **Wake candidate** | 调度 | 确定性 gate + 触发 | 心跳/gate 越阈时 | `signal_ref,score,reason`（运行态）；触发后落 wake event | 触发后即消费；不堆积 |

---

## 7. 跨模块接口

### 7.1 接口候选（输入 / 输出 / 读取 / 信号）

- **Affect 运行时对外读**：`core_affect`（V/A,±D）、`mood`、`emotion_portfolio`（带 cause/basis）、`current_chord`、`action_tendency`。默认**软信号**。
- **Affect 长期对象**：`affect_change_event[]`（append-only）、`affect_annotation[]`（冻结）、`attitude[object]`（独立慢对象，版本链）。
- **Motivation 对外读**：`drive_state`（derived）、`desire[]`（状态机 + target）、`action_tendency`；`intention_candidate`（**仅候选**，交 Cognition）。
- **Motivation 长期对象**：`drive_offset_ledger[]`（可选）、`desire` 状态迁移历史。
- **Cognition 侧写**：`intention`（认领后）、`task`（登记后）。
- **输入侧**：`event`（source/author/basis + 视角）、`appraisal_context`（goal/standard/attitude）、`affect_state`（当前）、`world/self/concern`、`values/principles`（闸门）。
- **对外信号（订阅用）**：`affect_changed(subject?, delta, basis)`、`motivation_shift(drive_id?, delta, basis)`、`wake_trigger(reason)`（仅在 gate 越阈时）。
- **provenance 契约**：一切派生/解释对象带 `source/author/basis`；长期对象带 `valid_at/invalid_at/superseded_by`；事件 `source/author/basis` 三字段必有。
- **软/硬边界**：mood/attitude/core affect/drive/desire/action tendency **只作偏置与软排序**；**不得硬过滤或替主体决策**；情绪/动机**不自动改事项状态、不发消息、不建 Task**。

### 7.2 横向必答表

| 变量 | 类别 | cause/target | 时间尺度 | snapshot/ledger/derived | provenance? | 影响方式 | 模型可写? | 衰减/失效 | 自我强化? | 与 Self/Concern/Task/Wake 接口 |
|---|---|---|---|---|---|---|---|---|---|---|
| **Core Affect (V/A)** | Affect | 无 | 分~天 | snapshot | 否 | bias/软信号 | 否（确定性） | 解析式回 baseline | 弱 | 注入上下文；不接 Task |
| **Mood** | Affect | 无单一 cause | 时~天 | snapshot/derived | 否 | 软偏置（≤0.3） | 否 | 慢解析式回 baseline | 是（与情绪双向，需有界） | 注入；软信号 |
| **Emotion 片段** | Affect | **有 cause_ref,target** | 秒~分（可数日） | 运行态 + 事件化 | **是** | bias + 行动倾向输入 | 是（候选） | 逐类型半衰期 + 重评/解决 | 是（→mood），需有界 | → 03/05 |
| **Affect Change Event** | Affect 留痕 | 有 cause | 事件时间 | **ledger（append）** | **是** | 供检索/调度 | 是（候选） | 不变（历史） | 否 | → 02/05/07 |
| **Affect Annotation** | Memory 派生 | 有（锚 event） | 冻结 | 不可变 | **是** | 只读 | 否 | 不衰减 | 否 | 05/07 |
| **Chord** | Affect 视图 | 无 | 三级 | derived | 否 | UI/prompt 表达 | 否 | 跟随底层 | 否 | 归 04 |
| **Drive** | Motivation | cause=deficit/incentive | 分~时 | **derived** | 是（来源事件） | bias-only | 候选 | 解析式回落 | 中高（需闸门） | 输入 Desire；软信号 Wake |
| **Drive offset ledger** | Motivation 台账 | 有 cause | 事件时间 | ledger | **是** | 审计 | 否 | 不删 | 否 | → Concern/审计 |
| **Desire** | Motivation | **target 必需**, cause=drive×对象 | 分~时 | **ledger（状态机）** | **是** | bias-only | 候选 | 满足/放弃/搁置→失效 | 中 | → Concern / → Cognition→Intention |
| **Action Tendency** | Affect×Motivation | cause=emotion/appraisal | 秒~分 | snapshot | 弱 | bias-only | 候选 | 随情绪回落 | 中 | Affect→行动选择 |
| **Intention** | Cognition（承诺） | cause=deliberation+认领 | 承诺期 | ledger（状态机） | **是** | 约束后续推理 | **否（需认领）** | reconsider / 达成 | 中 | → Task（经登记/Permission） |
| **Concern** | Continuity/Memory | cause=开放事件 | 天~周 | ledger | **是** | 影响 Wake/注意 | 候选 | 显式关闭/消退归档 | 低 | ↔ Wake/Task |
| **Task** | 执行层 | cause=登记 | 任务期 | ledger（状态机） | **是** | 执行（唯一可执行） | 否 | 完成/取消 | 低 | ← Intention |
| **Wake candidate** | 调度 | cause=gate 越阈 | 瞬时 | 运行态（触发后事件化） | 是（触发事件） | **只触发考虑，不执行** | 否（确定性 gate） | 消费即失效 | 低 | ↔ Wake |

---

## 8. 失败模式与风险

1. **日志爆炸（核心工程风险）**：每轮落 25 维×每轮 → 一天几十万行。缓解：derived + 事件化 + 触发门槛（Q19，约 50–100× 缩减）。
2. **tick 累加 / 时钟漂移**：任何「每 tick 加一点」的 drive/mood meter 都会在长不活动期累积成虚假紧迫。缓解：**解析式 `f(t)` 现算**（Q20），禁止 tick 累加。
3. **越权自动执行（最严重语义风险）**：Drive/Desire/强情绪绕过认知直接建 Task/发消息。缓解：BDI 式分层 + 登记步骤 + 主体认领；**删除旧「驱力超阈值驱动行为」**。
4. **触发过灵敏**：阈值太低 → 噪声写入、日志又涨。缓解：阈值 + 持续窗 + 双阈值迟滞（§4.4）。
5. **触发过钝**：阈值太高 → 丢重要变化。缓解：变点检测（BOCPD）+ 主体认领通道兜底；阈值可配可审。
6. **自我强化反馈环**：情绪→mood→偏置同类情绪→更强 mood；或召回/显著性自评复利（Echo Gap，05 §7）。缓解：有界标量 + 半衰期 + 冷却 + 上限 + **误差独立的外部信号**（召回不回流抬升 importance）。
7. **模型幻觉 appraisal/desire**：LLM 编造「为何生气」「我想要 X」。缓解：只产候选；`appraisal_basis` 锚到被评价事件原文；写入门槛。
8. **Reconsideration thrash**：意图在阈值附近反复重考虑。缓解：bold/cautious 策略 + 最短驻留 + 双阈值。
9. **Wake 空转烧钱**：心跳每跳都调 LLM。缓解：确定性 gate 先筛 + 触发后才调（§4.6）；量化见 §9。
10. **状态不可重算/漂移**：mood/attitude 若不可重算，调参即漂移。缓解：解析式、读时现算、可整层重算。
11. **版本链未接导致历史丢失**：覆盖式 summary 改写历史。缓解：双时钟 `valid_at/invalid_at`，只失效不删。
12. **单 VPS 成本超**：图数据库、torch/igraph、无门控 LLM 自动改写是成本红线（07 §8 D）。缓解：SQLite/单文件 append + 确定性优先。

---

## 9. 推荐机制：A / B / C / D

> 给出 4 个**可比较**的 runtime 机制候选，不要求选定。§9.5 再给三套可直接套用的整体运行方案。

### 方案 A · Derived-first + 事件稀疏（保守派，推荐基线）
- **数值层全 derived**：core affect / mood / drive 读时现算，不落库；仅 `last_state` 低频恢复行。
- **落盘只落事件**：Affect Change Event（阈值+持续+认领触发）、Desire/Intention 状态迁移。
- **无 Wake 自发**：仅外部事件/触发条件推动；默认沉默。
- **获得**：成本最低、可重算、无日志爆炸、无越权路径。**牺牲**：主动性弱（无「无来由想动」）。
- **证据**：Event Sourcing + FAtiMA 解析式（E2/E3）。

### 方案 B · Drive/Desire Ledger + 软偏置（Nocturne 式，历史丰富派）
- **在 A 上叠加稀疏 Driver/Desire ledger**：按事件写入权威偏移，带 `basis/event_ref`，可追溯「为什么变了多少」。
- **Wake 有确定性 gate**：Concern/Task/Trigger 打分，Drive/Desire 只贡献软分。
- **获得**：可审计、支持长期演变叙事。**牺牲**：需入账规则防膨胀；易把 trait 与 state 混回一个 ledger。
- **证据**：Nocturne Drive Ledger（E1，项目参考）+ homeostatic RL（E2）。

### 方案 C · 完整动力学 + 变点门槛 + 迟滞（理论最全派）
- **在 B 上加**：二阶动量迟滞（可选关）、BOCPD 变点门槛、显式 regulation（reappraisal/let_go）、双时钟版本链、评测闭环（影子模式）。
- **获得**：覆盖现象最全、触发判据最客观。**牺牲**：VPS/工程成本最高，有过设计风险；二阶响应变慢。
- **证据**：2601.16087（E2）+ BOCPD（E2）+ Graphiti 双时钟（E2）。

### 方案 D · 符号优先 / 无连续数值（BDI-only）
- **不设连续 Drive 数值**：Drive 以「goal-blocked」符号表达；Desire=option，Intention=承诺栈。
- **获得**：结构清晰、越级护栏天然、无浮点调参。**牺牲**：难表达强度/紧迫度与分级偏置；「冲劲」表达弱。
- **证据**：Jason（E3）+ Rao & Georgeff（E2）。

**四方案对比**

| 维度 | A conservative | B Ledger | C 完整 | D BDI-only |
|---|---|---|---|---|
| 每轮 CPU | 极低 | 极低 | 低（BOCPD 需剪枝） | 极低 |
| 存储增长 | 最低 | 中 | 高（版本链） | 中 |
| 可审计 | 中 | 高 | 很高 | 高 |
| 主动性表达 | 低 | 中 | 中高 | 低 |
| 失控风险 | 极低 | 低 | 中 | 低 |
| 与故渊现状契合 | 高 | 高（近 Nocturne） | 低 | 中 |

### 9.5 三套可直接套用的运行方案（轻量 / 平衡 / 完整）与 VPS 成本

> 成本均为**工程推算**（`basis: world_prior`，非实测）。假设：单台 VPS、无 GPU、对话轮数按典型 30–80 轮/次估算。**CPU 不是瓶颈，LLM 调用才是**。

#### L1 · 轻量（≈ 方案 A）
- **Affect**：V/A 二维 + 情绪集合（内存）+ mood 标量；触发仅「阈值」。
- **Motivation**：drive 现算（derived）；desire 状态机落 ledger；intention 需认领；无 Wake 自发。
- **落盘**：仅事件（affect change + desire/intention 迁移）。无每轮行。
- **每轮计算**：O(几十) 浮点 ≈ **< 1 ms CPU**；零实时 LLM。
- **存储**：~3–15 事件/对话 × ~300–800 B ≈ **2–12 KB/对话**。
- **LLM 调用**：appraisal 批处理 **1 次/对话**（或复用已有 DP 分析）。
- **VPS**：**2 vCPU / 2 GB** 足够；SQLite 或单文件 append；无额外常驻服务。
- **增量成本**：≈ 每对话 1 次小模型调用。

#### L2 · 平衡（≈ 方案 B）
- **Affect**：V/A(±D) + 情绪 portfolio + mood 向量 + 独立 appraisal 变量层；触发「阈值 + 持续」。
- **Motivation**：drive derived + drive offset ledger + desire 状态机 + Concern 升级规则；Intention 认领 + reconsideration。
- **Wake**：cron/心跳（如 15 min）+ **确定性 gate**；LLM 仅触发后调；默认沉默。
- **每轮计算**：O(百) 浮点 + EWMA/迟滞检测 ≈ **几 ms CPU**。
- **存储**：事件 + 低频 snapshot（每 N 轮/分钟一条，覆盖式）+ attitude/concern ledger；事件十几~几十条/对话。
- **LLM 调用**：appraisal 1 次/对话 + **偶尔** wake 触发（多数心跳不触发）。
- **VPS**：**2–4 vCPU / 4 GB**；SQLite + cron；成本以 LLM 调用为主。
- **增量成本**：LLM ≈ 1–2 次/对话（appraisal + 偶发 wake）。

#### L3 · 完整（≈ 方案 C）
- **在 L2 上加**：BOCPD 变点门槛（剪枝、只对关键维）、可选二阶动量迟滞、显式 regulation、双时钟版本链、影子模式评测。
- **每轮计算**：仍可控，但 BOCPD 在线维护需**剪枝 + 限维**（否则 O(t) 内存）；**成本需实测**。
- **存储**：+ 版本链（attitude/interpretation）+ regulation ledger，**相对 L2 约翻倍**。
- **LLM 调用**：与 L2 相近；若加轻量触发模型，需额外内存（如论文级 ~220 MiB BF16，**未在故渊 VPS 验证**）。
- **VPS**：**4 vCPU / 8 GB** 更稳；有过设计风险。
- **增量成本**：最高；建议只在窄验证证明收益后再启用。

**三方案一句话取舍**：**L1** 先保证「不爆炸、不越权、可重算」；**L2** 若需要「可审计的动机演变 + 偶发主动」；**L3** 仅在窄验证证明变点/迟滞真的改善体验后再上。

---

## 10. 给总窗口的输入（≤10 条）

1. **落盘原则**：数值层（core affect / mood / drive / chord）一律 **derived（读时现算）**；**只有事件与权威对象变化落盘**。这是避免日志爆炸的根。
2. **衰减只能解析式 `f(t)`**，且应推广到 mood 回弹、drive 回落、聚合全部数值层——**不活动 = 无 tick = 无累积**；⛔ 任何每 tick 累加都违规（Q20）。
3. **每轮 Δ 默认不落盘**；形成长期 **Affect Change Event** 的触发 = **阈值 / 持续 / 主体认领**（可加变点）三选一，默认沉默。
4. **Affect 需要 ledger，但是稀疏事件台账**（不是每轮快照）；Current/Recent/Baseline 是**同流不同时间常数的派生视图**，不是三个持久态。
5. **Drive 主体是 derived + 可选稀疏 offset ledger**；**Desire 持久的是状态机**（带 target/basis），**不持久每轮强度**；无对象欲望不登记。
6. **Desire→Concern 的边界**：Desire 有 want；Concern 是 open-loop，超 T 未解决才从 Desire 升级；关闭靠显式或长期无证据归档。
7. **越权护栏（硬）**：Intention 由 **Deliberation + 主体认领**写；Task 由**独立登记步骤**产生；**Drive/Desire/情绪强度不得直接建 Task 或对外行动**——旧「驱力超阈值驱动行为」必须删除。
8. **Wake 默认沉默 + 确定性 gate 先筛 + LLM 只在触发后调**；Drive/Desire 只能提高「值得考虑」的软分，不得越级。
9. **写入契约已给（§6.2）**：每类状态的「定义层/谁写/何时写/字段/失效」可直接进入下一轮组合；高频写只用 snapshot，历史用 append-only。
10. **成本结论**：单 VPS 上 CPU 不是瓶颈；**LLM 调用才是**。三方案：L1（2vCPU/2GB，~1 次 LLM/对话）/ L2（2–4vCPU/4GB，1–2 次）/ L3（4vCPU/8GB，最高）——数字为推算，需窄验证。

---

## 11. 证据索引

> 访问日期均为 2026-10-07。等级：E3=只读源码；E2=论文正文/官方文档；E1=README/项目页/项目内部；E0=检索摘要。

**E3（源码核查，只读；本报告未重新安装/运行）**
- `jason-lang/jason`，main @ commit `2c2d7e1c1ea3bd5cef9712d551632ba367109741`；路径 `jason-interpreter/src/main/java/jason/asSemantics/{Agent.java, Intention.java, Option.java, TransitionSystem.java}`（`Intention.State{running,waiting,suspended}`、`SelectIntention` 轮转、`Option=Plan+Unifier+Event`）。**引自专题 02 §4.5，本报告未复核**。
- `GAIPS-INESC-ID/FAtiMA-Toolkit`（master HEAD 只读）`Assets/EmotionalAppraisal/{ActiveEmotion.cs, Mood.cs}`：指数半衰期 `DecayEmotion`、log-sum-exp `ReforceEmotion`。**引自专题 01 §4.5**。
- `getzep/graphiti` 0.30.2 @`aa5bb27`（2026-10-06，Apache-2.0）：边 `valid_at/invalid_at/expired_at`、`rrf`、`label_propagation`。**引自专题 07 §4.1**。
- `mem0ai/mem0` 2.2.1 @`c93420c`（2026-10-05，Apache-2.0）：8 阶段写入 + MD5 幂等。**引自专题 07 §4.2**。
- 旧故渊 runtime 线索：`core/decay.py::exponential_decay`、`core/affect/{layers,chord,synthesis,meaning_weight}.py`、`core/clock.py`、`config/affect.yaml`（task-14 记载，项目内部；**本报告未重新读源码**）。

**E2（论文正文 / 官方文档）**
- Fowler, M. *Event Sourcing*（2005-12-12）https://martinfowler.com/eaaDev/EventSourcing.html ；*CQRS* https://martinfowler.com/bliki/CQRS.html 。
- Adams, R. P., & MacKay, D. J. C. (2007). *Bayesian Online Changepoint Detection*. arXiv:0710.3742. https://arxiv.org/abs/0710.3742
- *Controlling Long-Horizon Behavior in Language Model Agents with Explicit State Dynamics*. arXiv:2601.16087（2026-01-22）https://arxiv.org/abs/2601.16087 （VAD + 一阶 EWMA / 二阶动量 + hysteresis）。
- *Clinical Concern Trajectories in LLM Agents*. arXiv:2604.27872（stateless / first-order / second-order hysteretic dynamics）。
- Park, J. S., et al. (2023). *Generative Agents: Interactive Simulacra of Human Behavior*. arXiv:2304.03442（recency 指数衰减 factor 0.995、importance 阈值 150 触发 reflection）。
- Rao, A. S., & Georgeff, M. P. (1995). *BDI Agents: From Theory to Practice*（commitment 语义）；Kinny & Georgeff, *Intention reconsideration in complex environments*（bold/cautious/single-minded）https://dl.acm.org/doi/pdf/10.1145/336595.337377 ；Wooldridge 等 *Principles of Intention Reconsideration*。
- *Do Proactive Agents Really Need an LLM to Decide When to Wake and What to Anchor?* arXiv:2605.30152 / Microsoft Research（TGL trigger head；11.13 ms/event、~220 MiB 为**论文声称**）。
- *Simulating Human Cognition: Heartbeat-Driven Autonomous Thinking Activity Scheduling*. arXiv:2604.14178（心跳调度）。
- Overeem, M., et al. (2021). *A study of event sourcing schema evolution*. JSS 178, arXiv:2104.01146（append-only 的 schema 迁移战术，E2）。
- 专题 01 §3/§11（OCC / Scherer CPM / Russell / FAtiMA / Sentipolis / Frijda-Verduyn）与专题 02 §3/§11（SDT / homeostatic RL / BDI / Loewenstein / pymdp）中的 E2 条目，本报告沿用其结论。

**E1（项目页 / 项目内部参考）**
- `memo/Nocturne-Memory-Core.md`（Drive Ledger、AFFIRM/REJECT/SUSPEND、BIAS NOT SCRIPT、弱化时间衰减；项目内部）。
- `docs/requirements/memory-affect-01/00c-...-2026-10-07.md`（§5 分层运行、§6 Concern/Task 分离、§11 Wake 软信号；项目内部）。
- `docs/requirements/memory-affect-02/01-affect-ontology-dynamics.md`、`02-motivation-drive-desire.md`（本轮本体报告）。

**E0（仅作线索，未采用为结论）**
- 双阈值迟滞/dead zone 的工程博客范式（bugnet.io、industrialmonitordirect 等检索摘要）。

**未核实一览**
- **本报告未安装/运行任何候选项目，未发任何模型请求**；所有「每轮计算 / 存储 / LLM 调用 / VPS 规格」均为**工程推算**（`basis: world_prior`），非实测。
- arXiv 2605.30152 的 11.13 ms / ~220 MiB、arXiv 2601.16087 的 hysteresis 结论、Generative Agents 的 0.995 / 150 阈值均为**论文声称**，未复现。
- Jason / FAtiMA / Graphiti / mem0 的源码结论**引自专题 01/02/07**，本报告**未复核**；Jason 的 commit 亦来自 02。
- 旧故渊 runtime 线索来自 task-14 记载，**本报告未重新读 `guyuan/core/affect/*` 源码**。
- BOCPD 在故渊场景的在线成本（剪枝/限维）**未实测**；二阶动量对 prompt 注入的实际观感**未验证**；触发门槛（阈值/持续窗）的具体参数**未标定**。
- Overeem 2021、Kinny & Georgeff 原文**未直读全文**（部分经摘要/二手转述）。
