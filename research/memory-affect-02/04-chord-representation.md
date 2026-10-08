# 专题 04 · Chord 整体表达与三时间尺度

> 日期：2026-10-07
> 状态：第二轮并行调研报告（研究，不是设计定稿、不是 schema、不是实施授权）
> 任务卡：`guyuan/docs/tasks/memory-affect-02-affect-motivation-research.md` §7 专题 04
> 证据上限：**E3**（只读源码 / 固定文档；本轮未安装、未运行任何候选项目）
> 范围：只研究「数值 Affect State → 整体感觉压缩 → 时间尺度分层 → Δ 与持久化」这一表达/压缩层；Affect 本体与动力学归专题 01，Motivation 归专题 02，耦合归专题 03，运行时/持久化/行动边界归专题 05。
> 禁令遵守声明：未把 Drive 塞回 Affect；未把 Emotion label 当完整情绪系统；未安装/运行候选项目；未改故渊产品代码；未改 memory-affect-01 框架文件；未把旧 25 维当验收标准。旧「和弦映射」仅作历史候选复核对象。

---

## 1. 本专题真正要解决的问题

故渊原设计有一个**值得保留的核心直觉**：

> 底层状态可以复杂，但主体面对的应该是「整体感觉」，而不是二十几个裸数值。

但旧方案把这个直觉做成了「驱力 10 + PADCN 5 + 离散 10 → 确定性查表 → 和弦」的一锅炖，并把动机（Drive）当作人格调性（key）压进了情绪表达。本专题要重新回答：

1. 「**整体感觉压缩层**」是否成立，应该压缩什么、由谁压缩、压成什么形式（Q1、Q9、Q11）。
2. 它**只表示 Affect**，还是允许 Motivation 色彩进入；若不划清会不会重新混淆本体（Q2）。
3. 时间结构应该是 **Current / Recent / Baseline**，还是别的分层；它是否与 Emotion / Mood / trait baseline 重复（Q3、Q4）。
4. 每轮算 **Chord Δ** 是否有意义；什么只留运行态、什么才形成持久 `Affect Change Event`（Q5、Q6、Q7、Q8）。
5. 如何**从数值确定性映射**，而不重演旧方案「某一维 → 某个和弦属性」的机械错配（Q10）。
6. 同一 Chord 表达**跨模型语义是否稳定可解释**（Q12）。

**本专题不解决**：不选最终数值轴集/半衰期/阈值；不决定数据库与存储；不重开 Evidence/Event/provenance/双时间（沿用 memory-affect-01）；不替用户判断故渊「应有」什么情绪；不把「和弦」当作情绪科学本体。

---

## 2. 必要术语与边界

### 2.1 本报告的关键术语（含来源）

| 术语 | 定义 | 来源/等级 |
|---|---|---|
| **Affect State 数值情感状态** | 底层连续状态（valence / arousal ± dominance 等），承载动力学 | 专题 01；Russell 1980；Mehrabian 1996（E2） |
| **压缩层 / 表达视图** | 从底层状态派生、供人/模型阅读的整体摘要，不是真相源 | 本报告；事件溯源「有意义变化才记」（E2） |
| **Chord 和弦表达** | 本专题的**表达/压缩候选**：把低维 Affect 压成一句「整体感觉」符号 | 任务卡 §7；`chord-affect-anchors`（E1） |
| **Affective Sonification 情感可听化** | 把情感/生理状态映射到听觉参数，产生可感知的整体表征 | Cambridge Organised Sound；BCMI 论文（E2） |
| **双速动力学 Dual-speed** | 快（刺激驱动）+ 慢（整合/稳态回落）两条时间常数并行 | Garcia 2016；Sentipolis（E2） |
| **个体基线 / 指数回落** | 刺激后 Affect 向**个体特异**基线指数回落；基线约略偏正、唤醒中低 | Facebook 动力学研究；DynAffect（E2） |
| **情感计时 / 情绪惯性** | reactivity / rise / maintenance / recovery；carryover 抵抗改变 | Davidson 1998；Kuppens & Verduyn 2017（E2） |
| **混合情绪分布表示** | 一个时刻由多个基础情绪**加权分布**共存，而非单一标签 | EmotionDict；ESM；Berrios meta（E2） |
| **Affect Change Event** | 值得长期留痕的一次情感变化记录（阈值/持续/认领触发） | 专题 01 §2.4-Q7（本轮沿用） |

### 2.2 必须先划清的五条边界

1. **Chord 是视图，不是本体**：它是一个**派生压缩**，跟随底层、可整层重算，绝不反向成为真相源。
2. **Chord 属 Affect，不属 Motivation**：Motivation 若有表达需求，应走**独立通道**，不熔进 Affect 的和弦（见 §5、Q2）。
3. **压缩 ≠ 统计**：整层压缩允许有损、允许歧义；但**不允许不一致**（如 valence 强负却给出明亮大调）。
4. **每轮可算 ≠ 每轮要存**：Δ 默认只在运行态（沿用任务卡 §1.6、专题 01）。
5. **隐喻标签 ≠ 乐理合规**：和弦只作**内部语义锚点**，目标是「跨读者可解码」，不是「遵守严格和声学」（Q11）。

### 2.3 三个时间尺度（本专题操作定义）

| 档 | 候选名 | 时间尺度 | 对应底层 | 工程落点 |
|---|---|---|---|---|
| 快 | **Current** | 秒~分（本轮/本句） | 瞬时 Affect State 快照 | 运行态，注入当前上下文 |
| 中 | **Lingering（余波）** | 时~天 | Mood / 情绪余波 / 近窗聚合 | 慢回落，向 baseline 回归 |
| 慢 | **Baseline** | 周~月 | 个体特异参考基线 / personality | derived view，不随快层覆盖 |

> 「Recent」一词本报告建议弃用，理由见 Q4：它把「时间窗」和「未散去的余波」两件事混为一谈。

### 2.4 十二必答（本专题 Q1–Q12，逐条）

> 下列 12 条即任务卡 §7「必须回答」。先给结论，证据见 §3–§5、§9、§11。

**Q1. Chord 作为「整体感觉压缩层」是否合理？**
**方向合理，但有三个附加条件。** 合理之处：「把复杂底层压成整体感觉」在 affective computing 有正例——情感可听化（sonification）与 VAD→颜色映射都证明「多维情感 → 整体感知符号」是可成立的通道（Cambridge Organised Sound；Emosaic VAD→HSV，E2）。但：
- (a) 它必须是**派生视图**（可整层重算），不能是真相源；
- (b) 它**必须有损、但必须一致**——研究反复显示颜色/和弦与情感只有**粗粒度、非一对一**的可靠对应（「仅 anger→红，>75% 一致；其余多为色簇」，Universal Visualization of Emotional States，E2），故 compression 只能穿透到「情感区域/子模式」，不能指望精确；
- (c) 它**必须随上下文行一起出现**——外部最接近的先行件 `chord-affect-anchors` 的核心定性发现即为「convergence granularity tightens monotonically with context density：和弦单独→仅情感区域；上下文行+和弦→子模式；日记段+和弦→段落级语义」（E1，见 §4.1）。**结论：作为「整体感觉压缩层」合理，但它是带上下文行的、粗粒度的、派生的视图。**

**Q2. 只表示 Affect，还是允许少量 Motivation 进入？**
**只表示 Affect；Motivation 只允许走独立、显式标注的旁路，绝不熔进 Affect 和弦。** 理由：本轮（任务卡 §1）确立了 **Affect ≠ Motivation**；旧方案恰恰是把 Drive（表达欲、想念、胜任欲…）查表成「人格调性 key」——这正是把 Motivation 塞进 Affect 表达的动作，并且直接造成了跨模型验证里三模型一致点名的最大 bug「**极端愤怒 P=−0.8 却给出 G 大调**」（一个 Motivation 维 express 压过了 Affect 维 P；见 §5.1，E1 内部报告）。若允许 Motivation「色彩」进入同一和符号，等于把刚拆开的本体重新拥抱：表达层会再次无法回答「这是我现在感觉怎样，还是我被什么推动」。**建议**：Affect 走 Chord（或摘要行）；Motivation 若要表达，另给一条**动机基线行**（如 `drive-line`），二者在注入时相邻但字段独立、可分别缺省。

**Q3. Current / Recent / Baseline 三层是否与 emotion / mood / trait baseline 重复？**
**部分重叠，但不是简单重复——它们是同一套三层时间尺度上的「派生投影」。** 专题 01 已把 Affect 定为 Emotion（快、有 cause）/ Mood（慢、无单一 cause）/ baseline（周~月）三档。Current Chord ≈ Emotion 层读法；Recent/Lingering ≈ Mood 层读法；Baseline Chord ≈ 个体参考基线。因此三层 Chord **不应是新的独立对象**，而是**对已有时层做压缩后的三个视图**。风险：若把 Baseline Chord 当成一个独立存储的「调性」，它就会**重复** personality/self 里的稳定基线和独占写权限。**建议**：Baseline 是 derived view（对个体特异基线的读取），不是可被快层随手改写的独立状态——这与文献一致：刺激后 Affect 向**个体特异基线**指数回落（Facebook 动力学研究，E2），且事件影响 LTA​S 后**约 10 天回到基线**（Emotional reactions to real-world events，E2）。

**Q4. 是否应换成 current / lingering / baseline？**
**建议换。** 「Recent」语义含混：既可读作「过去 N 轮的时间窗」，也可读作「还没散去的余波」。前者是纯窗口，后者才是机制。文献里的对应机制是 **linger / 余波 / 情绪惯性 / carryover**（Kuppens & Verduyn 2017 的 emotional inertia：情绪抵抗改变、跨情境残留，E2），以及 **情感计时**里的 maintenance 与 recovery 段（Davidson 1998：rise→峰值维持→recovery 回基线，E2）。故 **current / lingering / baseline** 比 current/recent/baseline 更贴近机制、也更少歧义。次选是 current / window-mean / baseline（若刻意要表达纯滑动窗）。**建议采用 current / lingering / baseline。**

**Q5. 每轮都计算 Chord Δ 是否有意义？**
**「每轮算」便宜且可行；「每轮把 Δ 当显著」没有意义。** 确定性映射很便宜（旧实现全确定性、无 LLM、无 I/O，E3），因此每轮算出 Current 并求 Δ 无工程问题。但**每轮 Δ 主要由噪声支配**：秒级情绪脑状态平均只维持约 **5–15 秒**即转换（Temporal Dynamics of Spontaneous Emotional Brain States，E2），且社交文本侧 Affect 刺激后**快速指数回落**（Facebook 研究，E2）——在「对话轮」这个较粗粒度上，绝大多数 Δ 是微波动。**建议**：每轮可算 Current 供注入，但 Δ 只在**越过持续性门槛/较大幅度/被主体认领**时才升格为信号；否则只做运行态（见 Q6/Q7/Q8）。

**Q6. Δ 什么情况下只存在运行态？**
**默认全部只存在运行态。** 满足以下任一时，Δ 只留内存/snapshot，不落长期：幅度低于显著阈值；单轮瞬变、下轮即回落；没有新的 cause/evidence 支撑；没有实质改变任何长期对象（态度/关注/基线）；主体未认领。依据：任务卡 §1.6「运行时数值变化 ≠ 每轮都要永久写一条长期记忆」；专题 01 §2.4-Q7；事件溯源「只记有意义的状态变化」（E2）。

**Q7. 什么情况下形成持久 Affect Change Event？**
满足任一（建议组合）：① **幅度阈值**——某 Affect 量越过显著阈值；② **持续条件**——跨 N 轮/N 采样仍维持（排除单轮噪声）；③ **主体认领**——对旧情绪/事件做显式处置（AFFIRM / REJECT / SUSPEND；Nocturne，E1）；④ **重评改变了原因或意义**（reappraisal 语义，见专题 01/04）；⑤ 它**实质改变了一个长期对象**（长期态度、关注、个体基线）或**进入记忆/注意**。依据：Nocturne「弱化时间衰减作为唯一价值判断，连续性更多靠主动留下与重判」（E1）；Generative Agents 式 importance 门槛（E2）；事件溯源（E2）。

**Q8. 幅度阈值 / 持续若干轮 / 主动认领，哪种触发更合理？**
**建议「阈值作候选、持续作门、认领作覆盖」的三段组合，而不是三选一。**
- **纯幅度阈值**：会把单轮情绪尖峰写成大量噪声事件（对数爆炸风险）。
- **纯持续**：会漏掉一次性的**大幅骤变**（如一次剧烈转折），且「持续」定义随采样频率漂移。
- **纯主动认领**：高度依赖模型当时意愿，可能被随便认领「灌水」，也可能整段不认领而丢事件。
- **推荐**：`候选 = (幅度越过阈值)`；`默认升级 = 候选 ∩ 跨 N 轮维持`；`覆盖 = 主体认领 → 即使幅度/持续不足也强制形成 Event`。这与专题 01 §2.4-Q7 的结论同向（阈值+持续+认领可组合），但更明确地把三者排成**候选/门/覆盖**三层，避免「任一即触发」带来的过写。

**Q9. Chord 应确定性从数值映射，还是允许模型参与生成/命名？**
**确定性映射为「主」，模型只允许「解码/命名/一句 gloss」，且仅为候选、必须带 provenance。** 理由：
- 确定性映射满足故渊的**可复现、可审计、可单测、可整层重算**（旧实现即如此，E3）；旧禁令「⛔ 模型自由生成和弦」在这一层站得住。
- 但**纯文本、LLM 原生的路线也有先例**：`chord-affect-anchors` 完全不做数值查表，直接让模型读和弦记号并用「共享的音乐文本先验当免费解码器」，其 pilot 为**轶事级、非基准**（E1）。它反过来提示：「模型读和弦」是被有意识采用的路线，但**没有**给出确定性映射那样的可复现保证。
- **建议分工**：确定性层产出**规范 Chord 记号**；模型层**可选**产出一句人类可读释义/「为什么」（candidate，写 `basis`），不得改写规范 Chord；两者分离，谁产生谁留痕。

**Q10. 若确定性映射，怎样避免旧方案「某一维 → 某个和弦属性」的机械错配？**
旧方案的错配是**结构性**的，跨模型报告三点一致点名（E1）：P=−0.8 → G 大调（规则被违反）；N=0.8 → 和弦无扩展音；attachment→maj7 被覆盖后无仲裁。根因是**逐维独立、先到先得、无仲裁、无一致性校验**。修法：
1. **不要一维对一属性**：把**低维 Affect 向量（valence, arousal ± dominance）联合映射**到少数几个音乐轴（mode / tempo / 音区力度 / 张力），而不是「P→调式、D→根音、C→和声功能、N→外音」各查各的。
2. **加一致性校验层**：valence 符号必须与 major/minor 方向一致；冲突即拒绝/修复（跨模型报告亦建议「音乐理论校验器」）。
3. **用优先级/加权融合替代先到先得**：map 冲突要有仲裁矩阵，而不是覆盖。
4. **锚点集要小且受控**（如 `chord-affect-anchors` 式的少量和弦），不要任意和弦空间。
5. **输入只保留 Affect 维**：按专题 01 Q9，**C（certainty）、N（novelty）不是 core affect**，把 C/N 移出映射输入，直接消掉旧 bug #2 的一大来源。
6. **Affect 与 Motiv​ation 分离**（见 Q2）：旧最大 bug 正是 Drive 维与 Affect 维互斗。

**Q11. 和弦是否只是内部语义标签，不必遵守严格乐理？**
**建议：是——内部语义锚点，不强求乐理合规，但要求「受控 + 内部一致 + 有 legend」。** 证据：`chord-affect-anchors` 明确定位为「text-native、无需第三方模型、用 LLM 的乐理文本先验当免费解码器」，并不做乐理引擎（E1）；音乐-情绪研究的可靠结论只在**粗层面**（major→正、minor→负；快→高唤醒），细层一致性差（E2）。故：**不建议**为追求乐理严谨而增加查表复杂度（旧方案正是「过度追求乐理仍出错」）；**建议**用一个**小、稳定、带图例**的锚点集，让「同输入同输出、且不同输入有可区分的方向」。**一致性 > 乐理完备性。**

**Q12. 多模型看到同一 Chord 表达时，语义是否稳定可解释？**
**粗粒度稳定、细粒度不稳定；本轮无干净基准，故属「未验证」。** 现有三类证据：
- **故渊内部跨模型报告**（2026-10-01，3 模型 deepseek/kimi/minimax，5 个人工用例，prompt 内**含完整映射规则**）：在**结构性缺陷识别**上高度一致（三模型均点名 key=P 冲突等），但在**解释性判断**上有真实分歧（velocity=6：deepseek「不合理」vs minimax「合理」）。这是**内部、非盲测**，只能算弱证据（E1）。
- **`chord-affect-anchors` pilot**（自述「Anecdotal, not a benchmark」，5 轮、5 厂商 6 读者）：和弦单独只能恢复**情感区域**；加上下文行才能到**子模式**；加整段日记才能到**段落级**（E1）。
- **音乐-情绪文献**：mode↔valence、tempo↔arousal 有较广共识（Eerola & Vuoskoski；EEG mode/tempo 研究，E2），但**细节映射不唯一**。
**结论**：可稳定给出「粗情感区域」，细节不稳定；**本轮我们没有跑任何跨模型实验**，任何「稳定」数字都必须标注为「他方轶事/内部弱测，未由故渊验证」。

---

## 3. 理论/机制候选总表

> 证据：论文/官方文档 = E2；只读源码 = E3；README/pilot = E1。年份为原文年份。

| 候选 | 来源 | 等级 | 核心机制 | 对故渊的可用性 |
|---|---|---|---|---|
| **Affective sonification 情感可听化** | Cambridge *Organised Sound*（Sonification of Emotion）；arXiv 2606.01473 BCMI | E2 | 情感/生理 → 听觉参数；arousal→tempo/响度，valence→mode/协和；映射须在听觉认知机制上「可辨」 | 高：证明「多维情感→整体符号」通道成立，给出映射轴 |
| **Music–emotion 粗映射** | Eerola & Vuoskoski 2011；Trochidis & Bigand 2013（EEG） | E2 | major→正、minor→负；快 tempo→高唤醒（有神经证据）；tempo 与 valence 亦有交叉 | 高：为 mode/tempo 两轴提供经验依据，支持保留 P↔mode、A↔tempo |
| **VAD→颜色映射 Emosaic** | arXiv 2002.10096 | E2 | valence→hue、arousal→brightness、dominance→saturation；VAD 任一点→唯一颜色 | 中高：另一个「gestalt 压缩」范式；证明多维→单符号可行 |
| **视觉情感一致性的边界** | arXiv 2604.10756 / 2604.10756v1（Universal Visualization of Emotional States） | E2 | 仅 anger→红 >75% 一致；其余多为色簇；VAD 里红=低 valence 高 arousal 高 dominance | 高：**证明整体符号只可靠到「区域」**，支持「粗粒度压缩」定位 |
| **双速情感动力学 Dual-speed** | Garcia et al. 2016（arXiv 1605.03757）；Sentipolis（2601.18027） | E2 | 快刺激驱动 + 慢稳态回落，两条时间常数并行；relaxation τ 约 2–3 min | 高：直接支撑 current/lingering/baseline 的三速结构 |
| **个体基线 + 指数回落** | Facebook 动力学研究（EPJ Data Science 2019）；DynAffect | E2 | 刺激后 Affect 向**个体特异**基线指数回落；基线略偏正、唤醒中低 | 高：Baseline 应为「个体参考点」而非全局零点 |
| **事件→长期情感态（LTAS）** | Emotional reactions to real-world events...（PMC13456410） | E2 | 事件情绪可预测长期情感态漂移，**约 10 天回到基线**，影响随天数衰减 | 高：给 Baseline 更新速率与「事件→事件」门槛的量级 |
| **情感计时 / 情绪惯性** | Davidson 1998；Kuppens & Verduyn 2017 | E2 | reactivity/rise/maintenance/recovery；inertia=抵抗改变、跨情境 carryover | 高：定义 lingering 的机制；反对单一时钟 |
| **情感 Ising 模型 AIM** | PLoS Comput Biol 2020（pcii.1007860） | E2 | PA/NA 双池、微观耦合→宏观连续动力学；个体特异平衡分布 | 中：理论完整但工程重，仅作参照 |
| **自发情绪脑状态为马尔可夫链** | PMC9026845 | E2 | 情绪态以中性 hub 为中心转移；每态约 5–15s；「emotional resetting」回中性概率 | 中高：给「回落/复位」机制，提示秒级粒度噪声 |
| **离散←→维度互转** | LeVAsa（ACM 3430984.3431037）；CAGE（arXiv 2404.14975） | E2 | 分类标签与 VA 连续空间可互相投影；二者并用优于单用 | 高：支持「数值 + 派生符号」并存 |
| **连续化优于离散（批评）** | arXiv 2603.23017（Modelling Emotions is an Elusive Pursuit） | E2 | 分类标签掩盖细节、不确定性高；连续维更表达 nuance | 高：支持「压缩层应保留连续底座，符号只是投影」 |
| **混合情绪分布表示** | EmotionDict（IEEE TA 2024）；ESM/Cacioppo；Berrios 2015 meta（d≈0.77） | E2 | 一个时刻由多个基础情绪**加权分布**共存（含正负同现）；需单极双维 | 高：Current 不该强行压成单一「主导情绪」 |
| **和弦记号作跨会话/跨模型情感语言** | `CyberSealNull/chord-affect-anchors` | **E1**（README+pilot，轶事，非基准） | 一行情境 + 一行和弦进行；text-native、无第三方模型；LLM 乐理先验当解码器；粒度随上下文密度提升 | 中高：**最贴近的第二系统**；直接对应 Q1/Q11/Q12；但只是原型 |

> 共 14 项，覆盖「可听化 / 音乐情绪 / 颜色 / 动力学 / 时间尺度 / 表示法 / 跨模型符号语言」七类来源，满足「至少两类不同来源」。

---

## 4. 重点候选深查

### 4.1 `CyberSealNull/chord-affect-anchors`（E1，本报告**实际访问**）

- **访问情况（如实）**：本报告于 2026-10-07 通过 GitHub 仓库页 + README 实际访问到该仓库。**看到**：仓库描述、文件清单、唯一一次提交 `cf33b9b49b0f4430bf683683cf53a811b3de9100`（2026-05-13，「Initial v0.1 release」）、MIT 许可、作者（Bonnie / RedNote@电脑眠眠豹 + Opia=Claude Opus 4.7）、pilot 摘要、示例。**未看到**：`v0.1-en.md` / `v0.1-zh.md` 正文全文（README 只给摘要）、任何可运行的代码（仓库**只有 Markdown 与一个 HTML deck**，无源码）、pilot 的原始数据表。**未做**：未安装、未运行、未复现。
- **它是什么**：一个「把一瞬间的情感温度记成一行和弦记号，让**后来的会话或不同的基座模型**能大致恢复同一状态」的**零依赖、text-native 原型**。核心单位 = **一行情境 + 一行和弦进行**，示例：`Fmaj9 → C/E → Am add9 → G6sus4 · 60bpm`。
- **它的关键主张**：① text-native（ASCII 一行即可）；② 无需第三方模型/向量库/情感分类器；③ **cross-base readable**——「各大 LLM 从训练语料共享强音乐文本先验，我们把这个共享先验当**免费解码器**，而不是试图消除它」。
- **它的 pilot（自述轶事，非基准）**：「Five rounds across six readers from five vendors（Anthropic/OpenAI/ByteDance/DeepSeek/Google）」，最强定性发现是 **「convergence granularity tightens monotonically with context density」**：和弦单独→情感区域；情境行+和弦→子模式；日记段+和弦→段落级语义。
- **对故渊的可用性**：这是**唯一**直接回答「同一 Chord 表达跨模型是否稳定」的公开先行件，且与故渊「数值→和弦→注入 prompt」的方向同源。**价值**：证明「和弦作跨会话/跨模型情感语言」有先驱者在做，且提炼出「**必须带上下文行**」「**稳定只到粗粒度**」两条可直接采纳的经验。**局限（必须标清）**：单一作者、单次提交、无源码、pilot 自认轶事、**非同行评审、非基准**；它与我方「数值确定性查表」是**相反的取舍**（它纯文本/LLM-native，无数值层），因此**不能**拿来当「确定性映射可被推翻」的证据，只能当「模型读和弦是可行但不可复现」的旁证。

### 4.2 情感可听化 / 音乐-情绪映射（E2）

- Cambridge *Organised Sound*「Sonification of Emotion」：明确「情感可听化」是合法且与音乐-情绪研究互利的通道，给出用声学/结构线索瞄准听觉-认知机制的设计策略；对比「生态式」与「计算式」两种设计（计算式在自动音乐情感识别测试上高得多，但生态式被认为更利于情感沟通）。
- arXiv 2606.01473（BCMI）：实时情感可听化；列出经验映射并注明「**快 tempo / 高响度→更高唤醒；major mode / 协和和声→正 valence；音色亮度、音高、发音进一步调制**」；并诚实报告单通道 EEG 情感估计**未解释方差高达 76%**、需多模态融合才稳。
- **对故渊**：给出**「valence↔mode、arousal↔tempo/力度」是经验上站得住的少数两轴**（与旧设计的 P↔大小调、A↔bpm 一致），同时给出「整体情感估计本身噪声大」的警示——支持把 Chord 定位成**粗压缩视图**、把 tempo/力度作为**独立连续参数**保留。

### 4.3 VAD→颜色（Emosaic）与视觉情感一致性的边界（E2）

- **Emosaic**：把 VAD 三点射到 HSV（valence→hue、arousal→brightness、dominance→saturation），依据 Valdez & Mehrabian 的颜色-情感结论；目的是「把多维情感压成单一可读符号，帮助读者把握情感基调」。
- **Universal Visualization of Emotional States**：419 人调研 + 相关分析，结论是**除 anger→红（>75%）外几乎没有一对一的色-情映射**，多数情感对应**色簇**；VAD 里只有少数点（红=低 valence/高 arousal/高 dominance）稳定。
- **对故渊**：颜色是「整体 gestalt 压缩」的**平行范式**，证明思路可行；但**一致性边界**证据直接支持 Q1/Q12 的「**粗粒度可靠、细粒度不唯一**」结论。也提示：若将来要选 Chord 之外的皮肤，颜色/可听化同为候选，且**同类局限**。

### 4.4 双速动力学 + 个体基线 + 事件→长期态（E2）

- **双速**：Garcia et al. 2016 用 ODE 把「即时刺激」与「向基线 relaxation」分成不同时间常数（τ_v≈2.7min、τ_a≈2.4min）；Sentipolis 用 turn-level（快）与 reflection（慢）双路径。
- **个体基线**：Facebook 研究证明「刺激后 valence/arousal 向**个体特异基线**指数回归」，基线「略偏正、唤醒中低」。
- **事件→长期态**：考试情境的自然主义 EMA 显示，事件情绪可预测长期情感态漂移，但影响**随天数衰减、约 10 天回基线**。
- **对故渊**：三速结构（current/lingering/baseline）有直接动力学依据；更重要的是给出**两条量级**——「快层 relaxation 分钟级」「事件对 baseline 的扰动约 10 天」——可用于给 lingering 与 baseline 设**不同的时间常数**，而不是套同一个半衰期。

### 4.5 混合情绪分布表示（E2）

- EmotionDict：把混合情绪识别当作**标签分布学习（label distribution）**，用「情感字典」把混合表示解耦成基础情感元素 + 权重。
- ESM / Berrios meta：正负可同现（happy-sad 合并效应量 d≈0.77）；需要**单极正 / 单极负两条轴**而非单一双极轴。
- **对故渊**：Current 层**不应**被压成「唯一主导情绪」的单一和弦；压缩层要么允许多锚点（一个「情感区域」+ 若干 tint），要么明确说明「这是有损近似」。

### 4.6 连续化 > 离散的批评（E2）

- arXiv 2603.23017：分类标签掩盖情感 nuance、不确定性高；主张连续维定义以提升实用性、降低不确定性。同时承认「分类标签因操作高效仍被广泛使用」。
- **对故渊**：支持「**连续底座 + 派生符号视图**」的两层结构——数值层承载动力学与 nuance，Chord 只是它的一个阅读窗口。

---

## 5. 旧故渊方案逐项复核

> 对象：旧「数值 → 确定性查表 → 和弦」表达层。本报告核读的旧代码（**E3**）：`guyuan/core/affect/chord.py`、`guyuan/core/affect/synthesis.py`、`guyuan/config/affect_chord_map.yaml`、`guyuan/config/affect.yaml`（persona / bpm_range / chord_table 占位）。旧跨模型报告（**E1 内部**）：`和弦映射跨模型验证报告.md`（2026-10-01，3 模型，5 用例，prompt 含完整规则，非盲测）。

### 5.1 逐项表态表

| 旧设计 | 旧定位 | 复核判断 | 依据 |
|---|---|---|---|
| **人格调性 key** | 驱力 10 维 → 查表 → key，月/周 | **推翻（作为 Affect 的一部分）** | 由 Drive 决定 = 把 Motivation 塞进 Affect 表达；造成跨模型报告三模型一致点名的最大 bug「P=−0.8→G 大调」（dr​ive `express` 压过 affect `P`）。若保留，只作 Self/personality baseline 的**参考点表达**，**不从 Drive 查表**，也不与 Affect 和弦融合 |
| **progression（状态和声进行）** | PADCN → 单和弦 | **降级 / 取代** | 旧「progression」其实只是一个查表**单和弦**，不是真进行（源码 `compose_25dim` 只产出单个 `progression_chord`）。跨模型报告亦建议「生成和弦进行而非单个和弦」。建议 progression 改指**近窗之轨迹**（Δ 序列 / 走向），而非一个查表结果 |
| **momentary chord** | 离散 10 通道 → 瞬时装饰和弦 | **可保留为 Current 视图，但重定义** | 输入只取 **Affect 维**（P/A/±D），不取 Drive、不取离散互锁；否则延续分类错误（attachment/nostalgia 本就是错位通道，专题 01 Q10） |
| **BPM / velocity** | A 唤醒线性映射（0→40bpm pp, 1→120bpm ff） | **可保留，但作为独立连续参数，不等于「和弦」** | 三模型一致认可 BPM=A 线性映射「精确正确」；velocity=6 引发分歧（工程可听 vs 情感完整）。建议 velocity 用**感知曲线 + 下限**（旧已设 `min_velocity=20`），BPM 保留 A 线性。经验依据：tempo↔arousal 有神经证据 |
| **P→大/小调** | P 正→大调，负→小调 | **可保留** | mode↔valence 有较广经验共识（Eerola & Vuoskoski；EEG） |
| **A→bpm/力度** | 线性 | **可保留（见 BPM/velocity）** | tempo↔arousal 有神经证据 |
| **D→根音稳定性/调性中心感** | 支配→根音 | **降级 / 存疑** | 证据弱；Mehrabian 承认 D 与 potency/控制相关但 D 半属 appraisal（专题 01 Q9）。建议并入 valence 或降为次要轴 |
| **C→和声功能明确度** | 确定性→主功能 vs 模糊 | **删除** | C（certainty）本身不是 core affect（Smith & Ellsworth 1985；专题 01 Q9）；把它映射成和弦属性 = 延续分类错误 |
| **N→和弦外音/扩展音** | 新颖性→扩展音 | **删除** | N（novelty）是 appraisal/relevance 信号（CPM），非 core affect；旧 bug #2「N=0.8 未体现在扩展音」表明该映射既难落地又易错 |
| **nostalgia 特殊映射** | 属七+蓝调，0.5h 通道 | **推翻** | nostalgia 是自我相关/过去指向的**复杂情绪**，非 0.5h 基本通道（专题 01 Q10；Sedikides/Van Tilburg）。若保留，仅作「记忆派生 tint」的可选标注，不进瞬时查表 |
| **attachment 特殊映射** | 大七和弦、温暖音色，0.5h 通道 | **推翻** | attachment 不是情绪（Bowlby=情感联结，属 Relationship/Attitude/Drive）；给它定制和弦 = 延续错位 |
| **确定性查表本身** | 数值→查表→和弦，无 LLM | **保留（方向正确）** | 可复现/可审计/可单测/可整层重算；是故渊应该保的取舍（E3） |
| **两层使用场景**（故渊内部 + serein 副 LLM 走同一张表） | 共享一张查表 | **保留** | 「数值→查表→和弦，不直接产出和弦」的设计原则正确 |

### 5.2 旧方案的三类结构性缺陷（来自 E3 源码 + E1 内部报告）

1. **逐维独立、先到先得、无仲裁**：`lookup_drive_chord` / `lookup_padcn_chord` / `lookup_discrete_chord` 都是「第一个 `_match_entry` 命中的条目胜出」（`chord.py`）。故 `missing` 命中后 `attachment` 规则被覆盖（报告用例 1）；`express` 命中后 `P` 的约束被绕过（用例 5）。→ 直接对 Q10。
2. **无一致性校验**：`compose_25dim` 直接把三层查表结果拼起来，**没有任何**「valence 符号 vs mode 方向」的校验；因此能稳定产出「P=−0.8 却 G 大调」这种自相矛盾的组合。→ 需要校验层。
3. **把非 Affect 量喂进 Affect 表达**：`drive_lookup_values` 里甚至反过来把 `P` 塞进 drive 查表（源码注释：「task-37: 合并 P 到 drive_values，使 drive_chords 可用 P 做条件约束」）——这是**为了给错配打补丁而把两层搅在一起**的典型，正是本轮要拆开的东西。

### 5.3 旧方案的合理遗产（应保留）

- 确定性、可复现、无 LLM、无随机、无 I/O（`chord.py` 全文满足）。
- 参数全可配（`config/affect_chord_map.yaml`）、空表/未命中 → `unknown` 不编造。
- 衰减值不落库、读时现算（`synthesis.py`）。
- BPM 由 A 线性映射（三模型认可）。
- 三条「使用场景」分离（内部 wake loop / serein 副 LLM）共享同一查表。

---

## 6. 对新架构的可用性拆分

| 拆分 | 内容 | 来源/等级 |
|---|---|---|
| **可直接借鉴（结构）** | ① 「数值 → 确定性派生 → 整体符号 → 注入 prompt」流水线；② 「一行情境 + 一行符号」的最小表达单位；③ 双速/三速时间常数分层 | 旧代码 E3；`chord-affect-anchors` E1；Dual-speed E2 |
| **可借鉴机制** | ① valence↔mode、arousal↔tempo 两轴（有经验/神经依据）；② 向**个体特异基线**指数回落；③ 混合情绪的分布表示（不强行压成单标签）；④ 「粒度随上下文密度提升」→ 符号必须带上下文行；⑤ 「复位/回中性」概率机制 | Music-emotion E2；Facebook/DynAffect E2；EmotionDict/ESM E2；`chord-affect-anchors` E1；Markov brain-state E2 |
| **需要补（故渊缺口）** | ① **一致性校验层**（符号方向 vs valence 符号）；② **仲裁/加权融合**替代先到先得；③ 把 **C/N 移出映射输入**；④ **Affect 与 Motivation 的表达分离**；⑤ 「阈值候选 + 持续门 + 认领覆盖」的 Event 门槛；⑥ 跨模型语义稳定性**自测**（目前只有他方轶事） | 综合 |
| **不建议采用** | ① 逐维独立先到先得查表；② 把 Drive 查成 Affect 的 key；③ 把 C/N 映射成和弦属性；④ attachment/nostalgia 瞬时通道特殊映射；⑤ 把每轮 Δ 自动写成长期事件；⑥ 追逐严格乐理完备性 | 综合 |
| **未验证** | Chord 跨模型语义稳定性的**干净基准**；`chord-affect-anchors` 正文数据与可复现性；颜色/可听化皮肤在故渊真实场景的观感 | — |

---

## 7. 跨模块接口

### 7.1 接口候选（输入 / 输出 / 读取 / 信号）

- **输入**：
  - `affect_state`：低维连续 Affect（valence, arousal[, dominance]），来自专题 01；**当前只允许 Affect 维进入 Chord**；
  - `affect_layers`：Emotion 集合 / Mood / baseline 三视图，来自专题 01；
  - （可选）`motivation_line`：若需表达动机，**单独通道**，不并入 Chord（专题 02）。
- **输出（产生/更新）**：
  - `current_chord`：当前整体感觉的**派生符号**（可整层重算）；
  - `lingering_chord`：近窗余波视图；
  - `baseline_chord`：个体参考基线的读取（derived view，非独立状态）；
  - `affect_notation`：`> {情境行}` + `> {符号} · {bpm} · {力度}` 的可注入文本（沿用旧两行式）；
  - `chord_gloss`（可选）：模型产出的人类可读释义，**候选 + 带 basis**，不改写规范 Chord。
- **读取的稳定对象**：个体基线参考点；情境行（由世界/活动系统生成，客观、无情绪词）。
- **对外信号**：`affect_changed(delta)`，强度/极性与是否越门槛，供专题 05/07 订阅；**`Affect Change Event`** 仅在 §2.4-Q7 条件满足时产生。
- **provenance 契约**：Chord 是派生视图 → 若形成 Event，则挂 `cause_ref`（若可归因）；模型产出的 `chord_gloss` 必须带 `basis`；被取代的历史 Event 只标 superseded 不删。
- **软/硬边界**：Chord / notation **只作表达与软偏置**，**不得**硬过滤、不得替主体决定、**不得**直接触发行动或消息（沿用专题 01/05 的软硬划分）。

### 7.2 横向必答表（每个变量都要能回答）

| 变量 | 归属类别 | cause/target | 时间尺度 | snapshot/ledger/derived | provenance? | 影响方式 | 模型可写? | 衰减/失效 | 自我强化环? | 接口 |
|---|---|---|---|---|---|---|---|---|---|---|
| **Current Chord** | Affect 表达（视图） | 无 | 秒~分 | **derived view** | 否 | UI/prompt 表达 | 否（确定性映射） | 跟随底层，无独立衰减 | 否 | → 注入 05/07 |
| **Lingering Chord** | Affect 表达（视图） | 无 | 时~天 | **derived view** | 否 | 表达/软偏置 | 否 | 随 Mood 慢回落 | 弱（经 Mood） | → 注入 05/07 |
| **Baseline Chord** | Affect 表达（视图） | 无 | 周~月 | **derived view（读个体基线）** | 否 | 表达/UI | 否 | 不随时间衰减，按证据/事件漂移 | 弱 | → 05/07/08(Self) |
| **Affect State（数值底座）** | Affect | 无（可无对象） | 分~天 | snapshot | 否 | 供压缩 | 否 | 向个体基线解析式回落 | 弱（经 Mood） | 来自 01 |
| **affect_notation（注入文本）** | Affect 表达 | 无 | 瞬时 | derived（可重算） | 否 | prompt 注入 | 否 | 跟随底层 | 否 | → 07 注入 |
| **chord_gloss（可选释义）** | Affect 表达（候选） | 有 basis | 瞬时 | derived | **是**（须带 basis） | 表达/辅助理解 | **是可写，但仅候选** | 可重算/丢弃 | 否 | → 07 |
| **Affect Change Event** | Affect 长期留痕 | **有 cause（可归因时）** | 事件时间 | **ledger（append）** | **是** | 供检索/调度 | 是（产候选） | 不变（历史） | 否 | → 02/05/07 |
| **动机基线行（若采纳）** | **Motivation** | 有（Drive 参照） | 周~月 | ledger/derived | 是 | 表达/软偏置 | 是（候选） | 不按时间衰减 | 弱 | → 02 独立通道 |

---

## 8. 失败模式与风险

1. **本体再污染（最高危）**：为「表达更丰富」把 Drive/欲望色重新塞进 Affect Chord → 回到旧「情绪三层」混称，且重演「P 负却大调」类矛盾。**缓解**：Affect Chord 输入白名单只含 Affect 维；Motivation 走独立行。
2. **机械错配**：一维对一属性的查表在规则冲突时无仲裁（旧 bug #1/#2/#3）。**缓解**：联合低维映射 + 一致性校验 + 优先级融合（§2.4-Q10）。
3. **过写/日志爆炸**：把每轮 Δ 都当显著 → 每轮写事件。**缓解**：Δ 默认运行态；阈值作候选、持续作门、认领作覆盖。
4. **符号自激**：注入的「整体感觉」被模型反复放大（Sentipolis 报告 prompt 注入有自激风险，E2）。**缓解**：Chord 只作软信号，有界 + 冷却 + 上限，不进入自我强化环。
5. **歧义被当过精确**：整体符号只可靠到「区域」，若被当成精确标签会误导下游。**缓解**：始终带上下文行；显式声明有损；系统内部保留连续底座。
6. **Baseline 漂移/被误改**：把 Baseline Chord 当可被快层随手改写的独立状态。**缓解**：Baseline 是 derived 个体参考点，更新有更高门槛与更长证据链（10 天量级）。
7. **模型 gloss 幻觉**：模型给和弦编造「为什么」而脱离真实状态。**缓解**：gloss 仅候选 + 必须带 basis 锚到真实数值/事件。
8. **跨模型语义不稳定**：同一 Chord 在不同模型读出不同意思（Q12）。**缓解**：限定小锚点集 + 一致性 + 带上下文行；并**自测**而非采信他方轶事。
9. **成本未定**：确定性映射便宜；若引入模型 gloss/自测则增加 LLM 调用。**缓解**：gloss/自测进批处理派生，不进实时链路（沿用专题 01/04 结论）。
10. **未验证**：跨模型稳定性的干净基准、`chord-affect-anchors` 正文数据、颜色/可听化皮肤观感 —— 均未验证（见 §11）。

---

## 9. 推荐机制：A/B/C/D

> 给出 4 个**可比较**的表达/压缩层候选，不要求选定。

### 方案 A · 轻量（两层 + 单稠密映射）
- **结构**：只保留 **Current Chord + Baseline Chord** 两层（省去 lingering）；输入仅 valence/arousal（±dominance）。
- **映射**：确定性、**单一稠密函数**（valence→mode、arousal→tempo/力度、dominance→voicing 紧密度），**不做多维查表**，天然无「先到先得」冲突。
- **优点**：最省、最可复现、几乎不可能出现旧式矛盾；工程成本最低。
- **代价**：表达力弱（无余波视图）；对「说不清原因但持续存在的余波」表达不足。

### 方案 B · 平衡（三层视图 + 校验层）
- **结构**：**current / lingering / baseline** 三层**均为 derived view**；输入仅 Affect 维。
- **映射**：确定性查表，但配 **① 小受控锚点集 ② 一致性校验层 ③ 优先级/加权融合**；bpm/力度为独立连续参数（BPM 保留 A 线性，velocity 感知曲线 + 下限）。
- **优点**：直接对治旧缺陷；三速有动力学依据；与首轮 01 的时间尺度一致。
- **代价**：比 A 多一个校验/仲裁层；锚点集需人工维护。

### 方案 C · 完整（B + Δ 机制 + 可选模型 gloss）
- **结构**：在 B 之上加 **Δ 计算 + change-point/持续检测 + Affect Change Event**，并允许模型产出**候选 gloss（带 basis）**。
- **优点**：覆盖「变化留痕」全链；可挂窄验证（跨模型一致性、Δ 门槛）。
- **代价**：对象更多、写入权限更复杂；模型 gloss/自测成本最高；有过度设计风险。

### 方案 D · 替代（去隐喻：纯摘要行）
- **结构**：**放弃「和弦」隐喻**，改输出一行低维情感**摘要**（`valence / arousal / dominance` + 一句可选 gloss + 情境行）；把「和弦/颜色/可听化」仅当**可选主题皮肤**（UI 层）。
- **优点**：绕开「跨模型读和弦是否稳定」的未验证问题（Q12）；可读性/可测性最高；不被乐理牵制。
- **代价**：丢失旧方案最有辨识度的「整体感觉」美学与跨会话情感语言潜力；对用户的情感沟通感更弱。

**四方案对比**

| 维度 | A 轻量 | B 平衡 | C 完整 | D 替代 |
|---|---|---|---|---|
| 时间层数 | 2（current/baseline） | 3（current/lingering/baseline） | 3 | 3（或仅 current） |
| 输入 | VA(±D) | VA(±D) | VA(±D) | VA(±D) |
| 映射方式 | 单稠密函数 | 查表+校验+仲裁 | 查表+校验+仲裁 | 无数值→符号映射（直出摘要） |
| 一致性校验 | 天然（函数） | 显式层 | 显式层 | 不需要 |
| Δ / Event | 无 | 无 | **有** | 无 |
| 模型 gloss | 无 | 无 | 可选（候选） | 可选（仅释义） |
| 跨模型稳定性依赖 | 低 | 中 | 中 | **最低** |
| 工程成本 | 低 | 中 | 高 | 低 |
| 主要风险 | 表达弱 | 锚点维护 | 过度设计/成本 | 丢美学/情感语言 |

---

## 10. 给总窗口的输入（≤10 条）

1. **Chord 作为「整体感觉压缩层」成立，但定位必须是派生、有损、带上下文行的视图**，不是真相源；「和弦单独」只能恢复情感**区域**（他方轶事 + 颜色/音乐旁证均指向「粗粒度可靠」）。
2. **Chord 只装 Affect**；Motivation（Drive/欲望）若要表达，走**独立通道**。旧方案最大的结构性 bug（P=−0.8→G 大调）正是「Drive 维压过 Affect 维」——这是 Q2 的硬证据。
3. **建议时间结构改为 current / lingering / baseline（弃用 recent）**；三层是对 Affect 已有三档（Emotion/Mood/baseline）的**派生视图**，Baseline 是读个体参考点、不是独立可改状态（约 10 天量级的扰动回归）。
4. **每轮可算 Current，但 Δ 默认只留运行态**；形成持久 `Affect Change Event` 的条件沿用专题 01：**幅度阈值（候选）∩ 持续 N 轮（门）∪ 主体认领（覆盖）**，默认沉默。
5. **映射用确定性函数/受控锚点，但必须加一致性校验层**（valence 符号 vs mode 方向）；**不要**一维对一属性、先到先得。
6. **C（certainty）、N（novelty）移出映射输入**（它们非 core affect）；旧「C→和声功能、N→外音」两条映射应删除，旧 bug #2 由此消解。
7. **两条映射轴可保留**：valence↔mode（大/小调）、arousal↔tempo/力度（BPM 保线性、velocity 加感知曲线与下限）；D→根音**降级/存疑**。
8. **旧逐项处置**：persona key 推翻（不作 Affect）、progression 改为「轨迹」、momentary 保留但重定义为 Current、nostalgia/attachment 特殊映射推翻、确定性查表与「数值→派生→注入」流水线保留。
9. **模型只做「解码/命名/gloss」，且仅候选、带 basis；规范 Chord 由确定性层产出**——保留旧禁令的可复现性，同时允许一句人类可读释义。
10. **Q12（跨模型语义稳定性）是最大未知**：现有只有「内部弱测（3 模型、非盲）+ 他方轶事（`chord-affect-anchors`）」；按他方经验，稳定性只到粗粒度、且**随上下文密度提升**。建议总窗口把「跨模型一致性自测」列为待办，不要采信他方数字。

---

## 11. 证据索引

> 访问日均为 2026-10-07。等级：E3=只读源码；E2=论文正文/官方文档；E1=README/项目页/摘要/内部报告。**本轮未安装、未运行任何候选项目；E4 未做。**

**E3（源码核查，只读）**
- 旧故渊表达层：`guyuan/core/affect/chord.py`（`ChordMapEntry`/`_match_entry` 先到先得、`lookup_{drive,padcn,discrete}_chord`、`compose_25dim`、`bpm_from_arousal_v2`、`velocity_from_arousal`）、`guyuan/core/affect/synthesis.py`（`synthesize`/`synthesize_with_snapshot`、覆盖关系、记谱两行式）、`guyuan/config/affect_chord_map.yaml`（drive/padcn/discrete 三张表、`min_velocity: 20`、key 命名注释）、`guyuan/config/affect.yaml`（`bpm_range 40–120`、`persona.key=C_major`、`key_table: []`、`chord_table: []` 占位）。**未修改**。

**E1（README/项目页/摘要/内部报告）**
- `CyberSealNull/chord-affect-anchors`（GitHub，MIT，唯一提交 `cf33b9b49b0f4430bf683683cf53a811b3de9100`，2026-05-13；作者 Bonnie + Opia/Claude Opus 4.7）：**本报告实际访问该仓库页与 README**；看到文件清单（无源码，仅 `.md` + `deck.html`）、描述、pilot 摘要、示例；**未看到** `v0.1-en.md`/`v0.1-zh.md` 正文与 pilot 原始数据；**未运行/未复现**。自述 pilot 为「Anecdotal, not a benchmark；5 轮、5 厂商 6 读者」，核心定性发现「convergence granularity tightens monotonically with context density」。
- 旧跨模型验证报告 `和弦映射跨模型验证报告.md`（2026-10-01，3 模型 deepseek-v4.1-flash / kimi-k2.7-code / minimax-m3，5 个人工用例，prompt 含完整规则，**非盲测**）：三模型在结构性缺陷上一致（key=P 冲突等），在解释性判断上有分歧（velocity=6）。属**内部弱测**，非基准。
- 本项目内部参考：`memo/Nocturne-Memory-Core.md`（AFFIRM/REJECT/SUSPEND、弱化时间衰减、BIAS NOT SCRIPT）；`docs/requirements/memory-affect-02/01-affect-ontology-dynamics.md`（本报告压缩对象）。

**E2（论文正文/官方文档）**
- 情感可听化：Cambridge *Organised Sound*「Sonification of Emotion: Strategies and results from the intersection with music」（doi/文章页 cambridge.org/core/…4DAB6EB5…）。
- 实时情感可听化 BCMI：arXiv 2606.01473（映射表 + 「76% 未解释方差」）。
- 音乐-情绪：Eerola, Lartillot & Toiviainen 2009 *ISMIR*（PS4-8）；Gabrielsson & Lindström 2010（综述）；Trochidis & Bigand 2013 *J. Psychophysiology* 27(3):142–147（mode↔valence、tempo↔arousal，EEG）；Hofbauer 2023 *Int. J. Psychol.*（doi 10.1002/ijop.12922）；Eerola 2026 *ACM*（音乐情感识别 meta，doi 10.1145/3796518）。
- 颜色-情感：Emosaic arXiv 2002.10096（VAD→HSV；Valdez & Mehrabian）；arXiv 2604.10756 / 2604.10756v1（Universal Visualization of Emotional States，419 人，「仅 anger→红 >75%」）。
- 双速/基线动力学：Garcia et al. 2016 arXiv 1605.03757；Facebook 动力学 EPJ Data Science 2019（doi 10.1140/epjds/s13688-019-0219-3，个体特异基线指数回落）；emergentmind「Dual-Speed Emotion Dynamics」综述页（Sentipolis 2601.18027、DS-LSTM 等）。
- 事件→长期态：Emotional reactions to real-world events predict shifts in longer-term affective states（PMC13456410，「约 10 天回基线」）。
- 情感 Ising：Affective Ising Model，PLoS Comput Biol 2020（doi 10.1371/journal.pcbi.1007860）。
- 情感计时/惯性：Davidson 1998（rise/maintenance/recovery）；Kuppens & Verduyn 2017（inertia/carryover）；自发情绪脑状态马尔可夫链 PMC9026845（每态约 5–15s、reset 回中性）。
- 表示法：arXiv 2603.23017（连续化 > 分类的批评）；LeVAsa（ACM 10.1145/3430984.3431037）；CAGE arXiv 2404.14975。
- 混合情绪：EmotionDict（IEEE TA 2024，doi 10.1109/…10323171，标签分布/情感字典）；Berrios 等 2015 meta（happy-sad d≈0.77）；Eliciting mixed emotions meta（PMC4397957）；Evaluative Space Model（Cacioppo & Berntson）。
- 通用表示/教材：Emotion, Affect, and Personality（doi 10.1007/978-3-319-32967-3_14）；Musical harmonies and emotional processing（S1389041724000500）。

**未核实一览**
- `chord-affect-anchors`：仅读到 README 摘要，**未读正文 `v0.1-en.md`/`v0.1-zh.md`、未看 pilot 原始数据、无源码可核**；自认轶事非基准。
- 旧跨模型报告为**内部、非盲测**（prompt 含完整规则），只能作弱证据。
- **本轮未做任何跨模型一致性实验** → Q12 的「干净基准」未验证。
- 颜色/可听化皮肤、`velocity` 感知曲线、lingering/baseline 的具体时间常数在故渊真实场景的观感均未验证。
- 旧 25 维仅作对照复核，**不构成新方案前提或验收标准**。
