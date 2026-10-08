# 专题 01 · Affect 本体与动力学

> 日期：2026-10-07
> 状态：第二轮并行调研报告（研究，不是设计定稿、不是 schema、不是实施授权）
> 任务卡：`guyuan/docs/tasks/memory-affect-02-affect-motivation-research.md` §4 专题 01
> 证据上限：**E3**（只读源码/固定文档；本轮未安装、未运行任何候选项目）
> 范围：只研究「事件→情绪→心境」自身的本体分层、状态更新、衰减、积累与表达物理；Affect × Motivation 耦合归专题 03，Chord 表达归专题 04，运行时/持久化/行动边界归专题 05。
> 禁令遵守声明：未把 Drive 塞回 Affect；未把 Emotion label 当完整情绪系统；未安装/运行候选项目；未改故渊产品代码；未改 memory-affect-01 框架文件；未把旧 25 维当验收标准。旧 25 维仅作历史候选复核对象。

---

## 1. 本专题真正要解决的问题

故渊要的不是「给记忆贴一个情绪标签」，而是一层**有因果、可累积、可回落、可被当前主体重新处置**的内部感觉状态。它必须回答四件事：

1. **本体**：Emotion / Mood / Core Affect / Appraisal / Attitude 各自是什么，谁派生谁，谁只是谁的视图（Q3、Q8）。
2. **生成**：一次真实 Event 进来，Affect 侧**最少需要哪些输入**才能产生一个「为什么」可解释的情绪（Q1、Q2、Q9）。
3. **表示**：多种情绪并存、数值与标签并存，怎么在**不强行选唯一标签**的前提下表示（Q4、Q5）。
4. **动力学**：哪些量按时间解析式回落，哪些**不能**简单按时间衰减；每轮变化何时只是运行态、何时才值得形成长期 Affect Change Event（Q6、Q7）。

真正暴露的基础问题（任务卡开头）：旧方案把「驱力 10 + PADCN 5 + 离散 10」混成一锅「情绪三层」。本专题只处理其中的**纯 Affect 部分**，并把不属于 Affect 的东西点出来。

**本专题不解决**：不选数值轴集/阈值/存储；不决定是否落库；不重开 Evidence/Event/provenance/双时间（沿用 memory-affect-01）；不替用户判断故渊「应有」什么情绪；不把用户情绪识别当主体情绪。

---

## 2. 必要术语与边界

### 2.1 本报告的关键术语（含来源）

| 术语 | 定义 | 关键来源 |
|---|---|---|
| **Core Affect 核心情感** | 一种神经生理状态，「感觉好/坏、清醒/困倦」的底色；可自由漂浮、可无对象 | Russell 2003 *Psych. Review*（E2） |
| **Emotion 情绪** | 有界的情绪**片段**：core affect 被归因到某个对象/原因，叠加概念化与其他非情绪成分 | Russell 2003；OCC 1988（E2） |
| **Mood 心境** | 更弥散、更持久、**通常无单一先行原因**的背景情感状态 | FAtiMA 文档；WASABI；Beedie 等 2005（E2） |
| **Appraisal 评价** | 「事件对我的目标/标准/偏好意味着什么」的结构化判断；必须能回答「为什么」 | OCC；Scherer CPM；Smith & Ellsworth 1985（E2） |
| **Appraisal 变量** | 评价的结构化输出（goal relevance、desirability、expectedness、certainty、coping、norm 等） | OCC；Scherer CPM；Smith & Ellsworth 1985（E2） |
| **情感控制论 / EPA** | 用文化情感值（评价-权势-活跃）描述身份/行为/情绪，事件造成 deflection 生成情绪 | ACT（Heise 1979）；EmoACT（E2） |
| **Affect Annotation 情绪标注** | 编码/发生**当时**冻结在 Evidence/Event 上的情绪快照 | 沿用 memory-affect-01（✅ 已接受） |
| **Attitude 态度** | 对**具体对象**（人/话题/作品）的长期评价倾向；object-specific | OCC「taste/attitude」分支；ACT；DAM-LLM（E2） |
| **Affect Change Event 情绪变化事件** | 值得长期留痕的一次情绪变化记录（阈值/持续/认领触发） | 本报告 §9；Nocturne（E1） |

### 2.2 必须先划清的六条边界

1. **Core Affect ≠ Emotion ≠ Mood**：前两者是「状态 vs 片段」，Mood 是「无对象的慢状态」。三者不是简单的一条派生链（详见 Q3）。
2. **Appraisal ≠ Affect state**：评价是**输入/中间产物**（属 Cognition 与 Affect 之间），不是感觉本身。
3. **数值可计算 ≠ 属 Affect**：这是本轮**最需要警惕**的一条。C（certainty）、N（novelty）能算成数，但它们是评价/认知信号（Q9）。
4. **Emotion label ≠ 情绪系统**：离散标签只是表示层的一种视图，不是本体。
5. **运行时 Δ ≠ 长期记忆**：每轮数值变化默认只属运行态。
6. **强烈情绪 ≠ 越权改 Task/Commitment**：Affect 对行动默认只能提供 bias 或软信号。

### 2.3 三个时间尺度（操作定义，综合 ALMA / FAtiMA / WASABI）

| 档 | 名称 | 时间尺度 | 是否有对象/原因 | 工程落点 |
|---|---|---|---|---|
| 快 | 情绪片段 Emotion | 秒~分~时（可数日，见 Q6） | **有** cause_ref + target | ActiveEmotion 列表（FAtiMA） |
| 中 | 心境 Mood | 时~天 | **无单一对象** | 标量或向量 + 向 baseline 回落 |
| 慢 | 态度/人格 Attitude/baseline | 周~月 | **object-specific 或 global** | 独立对象，不随快层直接覆盖 |

来源：ALMA（Gebhard 2005，short/medium/long term affect）；FAtiMA（情绪 half-life ≈15 tick、mood ≈60）；WASABI（core affect PAD 向中性回落）。以上均为 **E2**；FAtiMA 源码为 **E3**（本报告 §4.5）。

### 2.4 十个必答问题逐条回答（本专题 Q1–Q10）

> 下列 10 条即任务卡 §4「必须回答」。先给结论，证据见 §4、§11。

**Q1. Event → Appraisal → Emotion 这条链需要哪些最小输入？**
最小输入＝三类，缺一不可：
- (a) **可评价的事件表示**：主体/动作/对象 + 时间 + 视角（perspective）。OCC 三分支即「事件(后果)/主体(行动)/客体(属性)」；FAtiMA 的 `event + target + conditions`；EMA 的 causal interpretation（信念/愿望/意图/计划/概率）——**E2，且 FAtiMA 结构为 E3**。
- (b) **被事件触及的关切参照**：goal（对事件）/ standard·norm（对行动）/ taste·attitude（对客体）。OCC「value system」= goals/standards/tastes——**E2**。
- (c) **一个小的评价变量向量**：至少 `desirability`（目标）与 `expectedness/likelihood`；对他人事件加 `desirability-for-others`；对行动加 `praiseworthiness`；对客体加 `like`。CPM 把它顺序化为 relevance→implication→coping→normative——**E2**。
- 最小集结论：**(事件 + 视角) × (goal/standard/attitude 参照) × (desirability + expectedness)** 即可生成第一档情绪；coping/norm 属后阶段、可缺失。EMA 已证明**无需自然语言**，用因果图即可算 desirability/likelihood/causal attribution/controllability（E2）。

**Q2. Emotion 是否必须有 cause_ref？Mood 是否允许无单一 cause？**
是、是。
- Emotion **必须**有 cause_ref：OCC 情绪都是「对事件/行动/客体的反应」，FAtiMA `ActiveEmotion` 结构 `<type,valence,intensity,cause,target>`，`CauseId` 指向自传记忆事件 id（**E3**）；ALMA 明确「emotion bound to a specific event/action/object which is the cause」。
- Mood **不要求**单一 cause：FAtiMA `Mood` 是一个 `[-10,10]` 标量，无 cause 字段（**E3**）；WASABI 称 mood 是「diffuse valenced state，experiencer 说不出明确原因」；文献普遍以「object-directedness / cause attribution」区分 emotion 与 mood——**E2**。
- Core affect 允许**无归属**（free-floating = mood）——Russell 2003（E2）。

**Q3. Emotion、Mood、Core Affect 三者是互相派生，还是不同视图？**
是**部分重叠的不同视图**，不是一条严格派生链。
- Russell 2003（E2）：core affect 是底层神经生理状态；**被归因到某原因 → 开始一个情绪片段**；**未被归因、自由漂浮 → mood**。即 Emotion = core affect + 归因 + 概念化 + 其他非情绪成分；Mood = 未被归因的 core affect 的较长时间读法。
- 所以三者是同一底层的**三种读取方式**：Core Affect=底状态视图；Emotion=有界片段视图（带 cause/action tendency）；Mood=去归因慢聚合视图。
- 但存在**双向弱耦合**（非派生）：情绪抬高/压低 mood（FAtiMA `mood += valence·Intensity·factor`，E3）；mood 反过来偏置后续同类情绪的强度（FAtiMA `potential += valence·mood·factor`，E3）。因此推荐把 Core Affect 当**持续数值底座**，Emotion 当**归因片段**，Mood 当**慢聚合**（可为状态或 derived view，见 §9）。

**Q4. 多种 Emotion 同时存在时怎样表示，而不是强制选唯一标签？**
用**激活情绪集合（portfolio）**：每个成员各自带 `type / intensity / valence / cause_ref / target / appraisal_basis`，允许多条并存、甚至价性相反。
- 混合情绪（mixed emotions）是被反复验证的真实体验：meta 分析 63 项实验、效应量 d≈0.77，独立于维度/离散模型，是「稳健、可测量、非伪」的（Berrios, Totterdell & Kellett 2015，E2）。结构上需要**正负价各自单极**（Evaluative Space Model）而非单一双极轴（Cacioppo & Berntson 1994，E2）。
- 表示层可再加一个**派生聚合视图**（如 Plutchik 邻相「dyad」命名：Joy+Trust=Love）用于表达，但**不能反推本体**——Plutchik 1980（E2）。
- 反例警示：FAtiMA 对**同一 cause** 的情绪是**替换**（reappraisal），会丢掉「既高兴又难过」——这正是故渊要自补的地方（§4.5，E3）。

**Q5. 数值状态与离散情绪类型是否需要同时存在？**
需要，二者分工不同、同时存在已是工程共识。
- 维度侧（valence–arousal / PAD）负责**连续动力学**（更新、衰减、聚合）；离散侧负责**可解释标签**（注入 prompt、UI、命名）。
- 工程证据：GAMYGDALA 提供 OCC（离散）→PAD（连续）转译；WASABI 用 PAD 连续演化、**随后才**分类为离散情绪并驱动表情（E2）；Sentipolis 用连续 PAD 作持久状态、再用 KNN 映到 Plutchik 风格标签注入 prompt（E2）。
- 结论：**持续数值状态 + 离散标签视图**并存；离散标签是视图，不是底层。

**Q6. 哪些变量应该解析式回落/衰减，哪些不能简单按时间衰减？**
- **应解析式衰减**：核心情感的**瞬时偏移**、情绪强度 spike、唤醒类量。依据：FAtiMA 用 `I = I₀·exp(ln(HalfLifeConst)/T_half·Decay·Δt)` 逐类型独立衰减（**E3**）；WASABI 让 PAD 向中性回落；Sentipolis 用 half-life 衰减（E2）。这类量本质是「对稳态的偏离」，指数回落是合理近似。
- **不能只按时间衰减**：
  1. **Attitude/Attachment**：对象相关、证据驱动，应随新证据更新而非被动随时间消失（ACT/DAM-LLM；Nocturne 明确「弱化时间衰减作为唯一价值判断」，E1）。
  2. **Appraisal/basis**：是证据链，不可衰减。
  3. **Stable Self / 价值**：不可随时间衰减。
  4. **Mood**：虽向 baseline 回落，但**不是纯特征衰减**——它被情绪推高、又反过来偏置情绪，且「情绪结束」常因**重评/解决**而非时钟（Frijda 1991、Verduyn 2011/2015，E2）。
- **重要限定证据**：情绪**时长高度可变**，50% 的被回忆情绪片段 >1 小时；不同情绪时长不同（悲伤 > 内疚 > 愤怒 > 羞耻 > 厌恶/恐惧）；**强度与时长仅弱-中相关**（Frijda, Mesquita, Sonnemans & van Goozen 1991；Verduyn 等 2013，E2）。故「单一全局半衰期」是**工程简化**，必须配**重评/解决/主动放下**作为真正的终止机制，并把半衰期做成**逐类型可配**。

**Q7. 「情绪变化」什么时候只是运行态，什么时候值得形成长期 Affect Change Event？**
- **默认（运行态）**：每轮算出的数值 Δ 只留在内存/snapshot，不落长期。
- **值得形成长期事件**（满足任一，且建议可组合）：
  1. **幅度阈值**：某情绪/核心情感强度越过显著阈值；
  2. **持续条件**：跨越 N 轮/N 次采样仍维持（排除单轮噪声）；
  3. **主体认领**：当前主体对某旧情绪/事件做了显式处置（AFFIRM/REJECT/SUSPEND；Nocturne，E1）；
  4. **重评改变了原因/意义**（FAtiMA reappraisal 语义，E3）；
  5. **它实质改变了一个长期对象**（如态度、关注）或**进入记忆/注意**。
- 依据：Nocturne「时间衰减不再是唯一价值判断，连续性更多靠主动留下与重判」（E1）；Generative Agents 用 importance 阈值触发 reflection/写入（E2），可类比为「写入门槛」；Event Sourcing 原则只记**有意义的状态变化**（E2）。
- 建议：**阈值 + 持续 + 认领**三选一即可触发，默认沉默。

**Q8. Attitude 应不应该继续留在 Affect 模块，还是只把 Affect 当它的输入？**
倾向：**Attitude 不留在 Affect 运行时状态里；它应是独立的长期对象，Affect 只作它的输入/证据**。
- 依据：
  - OCC 的 **attitude 是「taste/like」——对客体的评价，属 value system（appraisal 的输入）**，不是情绪状态本身；OCC 里 Love/Hate 是 object-directed「attraction 情绪」，短时、非长期态度（E2）。
  - ACT 把长期关系情感放在**独立的文化情感值**里（E2）；DAM-LLM 以「对象·方面」为粒度**累积**态度（E2）；Nocturne/00b 把 Attitude 归为长期对象模型（E1）。
  - FAtiMA **没有**对具体人的长期 attitude 对象，只有短时 Love/Hate 情绪——是它印证该缺口（E3）。
- 结论：保留 **OCC 意义的「attitude 作为 appraisal 输入」**；但**故渊「对某对象的长期态度」应移出 Affect 运行时**，成为慢变、object-specific、带 provenance 的独立对象，由多次 appraisal/情绪累积形成（去哪一层见 §9 的 A/B 方案对比）。

**Q9. 旧 PADCN 里 C（certainty）、N（novelty）是否真的属于 Affect state，还是 appraisal/cognition 信号？**
**不属于 core affect；它们是 appraisal/cognition 信号。** 这是旧方案的一个明确分类错误。
- Mehrabian 的 PAD 只有 **Pleasure / Arousal / Dominance 三个近正交轴**（Mehrabian 1995/1996，E2）；**没有 C、N**。
- 在评价理论里：**Novelty 是 Scherer CPM 的 relevance 类评价**（stimulus evaluation check）；**Certainty/Expectedness 是 implication 类评价**——Smith & Ellsworth 1985 恢复出的六个正交评价维度就明确包含 **certainty**（E2）。
- Russell 1978 早已指出：超出 pleasure/arousal 的维度描述的是**「关于情绪前因/后果的信念」，而非情绪本身**（E2）。
- 补充：连 **Dominance（D）也偏 appraisal**——WASABI 的 D 由认知层推断、不参与衰减式情感冲量（E2）。故旧 PADCN 的 P/A/D/C/N 里，**仅 P、A 是较纯的 core affect**，D 半属评价，**C、N 基本属评价/认知**。
- 处置建议：C、N 移入 Appraisal 层作输入；core affect 收敛到 valence–arousal（±dominance，需谨慎）。

**Q10. 旧离散通道里 nostalgia / attachment 等是否分类正确？**
**不正确，至少有 2/10 明显错位。**
- 旧 10 通道 = Plutchik 8 基本情绪（joy, trust, fear, surprise, sadness, disgust, anger, anticipation）+ **nostalgia + attachment**（E2，Plutchik 1980）。
- **nostalgia（怀旧）**：是一个**真实情绪**，但属**自我相关、过去指向、mostly positive / bittersweet 的复杂情绪**（Sedikides 等 2015；Van Tilburg 等 2018：正价、趋近、低唤醒），**不是 30 分钟半衰期的基本通道**。它更像「记忆 × 自我 × 情感」的派生情绪/复合情感，半衰期错配（旧配置 0.5h）。
- **attachment（依恋）**：**不是情绪**。Bowlby 定义为**情感联结**（tendency to seek proximity），属 **Relationship / Attitude / Drive** 范畴（E2）。它绝不该作为 0.5h 半衰期的瞬时离散通道。
- 次要存疑：**trust、anticipation** 是否属「基本情绪」学界有争议（trust 常被并入 disgust 对立轴；anticipation 更像预期/行动倾向）——标为**待验证**。
- 结论：旧 10 离散通道里，**attachment 明确错位**、**nostalgia 层级错位**；trust/anticipation 存疑（证见 §5、§11）。

### 2.5 横向必答（每个变量都要能回答）——见 §7 表

---

## 3. 理论/机制候选总表

> 证据：理论论文/官方文档 = E2；只读源码 = E3；README/项目页 = E1。年份为原文年份。

| 候选 | 来源 | 证据等级 | 核心机制 | 对故渊的可用性 |
|---|---|---|---|---|
| **OCC 模型** | Ortony, Clore & Collins 1988（2nd ed. 2022） | E2 | 事件/主体/客体三分支；对 goals/standards/tastes 评价；22 型离散；local 强度 + 4 全局变量 | 高：情绪**生成最小契约**与「为何产生」的骨架 |
| **Scherer CPM** | Scherer 2001/2009；Scherer & Moors 2019 | E2 | 序贯 SEC 4 类：relevance→implication→coping→normative；后类递归修正前类 | 高：**评价输入清单** + 快/慢分层天然 |
| **Smith & Ellsworth 1985** | JPSP 48(4):813 | E2 | 6 个正交评价维（含 pleasantness、certainty、control…） | 高：证明 certainty 属**评价**非 core affect |
| **Russell Core Affect / Circumplex** | Russell 1980；Russell 2003 | E2 | 二维双极 valence×arousal；emotion=归因的 core affect；mood=未归因 | 高：**情绪±心境**区分的理论底座 |
| **PAD** | Mehrabian 1995/1996 | E2 | Pleasure-Arousal-Dominance，3 近正交轴 | 中：连续表示格式；**证明无 C、N** |
| **Plutchik Wheel** | Plutchik 1980 | E2 | 8 基本情绪、3 强度、4 对反、dyad 混合 | 中：标签/混合表示的参照；**不能作本体** |
| **ALMA** | Gebhard 2005 | E2 | emotion 短/mood 中/personality 长；pull-push；向人格位回归；24 情绪/8 mood/5 personality | 高：**三层骨架**（原实现已停产） |
| **FAtiMA Toolkit** | GAIPS-INESC-ID/FAtiMA-Toolkit | **E3**（本报告核 master 源码）/E2 论文 | OCC 可运行实现；`ActiveEmotion` 带 CauseId/AppraisalVariables/Mood 单标量；指数半衰期；log-sum-exp 聚合 | 高：结构可照搬（C#） |
| **EMA** | Marsella & Gratch 2004/2009 | E2 | 因果图（信念/愿望/意图/计划）→评价变量；coping 反向作用；快评价/慢推断 | 高：**appraisal 变量与 coping 的工程范式** |
| **WASABI** | Becker-Asano 2008；Becker-Asano & Wachsmuth 2009 | E2 | primary=core affect PAD 连续演化向中性回落；secondary=认知；mood-congruent 唤起 | 中高：**维度→离散的分类顺序** |
| **ACT / EmoACT** | Heise 1979；arxiv 2504.12125 | E2（理论）/E1–E2（实现） | EPA 空间；impression−identity deflection 生成情绪；长期可改关系身份 | 中：关系态度落点；需文化词典 |
| **Sentipolis** | arXiv 2601.18027 / ACL Findings 2026 | E2 | 持续 PAD + 双速更新（fast 逐轮/slow 反思）+ 情绪-记忆耦合 + half-life 衰减 + KNN→Plutchik 标签 | 高：**最贴合的 LLM 情绪态化设计**；代码是否开源未核 |
| **情绪动力学（时长）** | Frijda et al. 1991；Verduyn et al. 2013/2015 | E2 | 情绪时长高度可变；强度/时长弱相关；时长受评价/反刍驱动 | 高：**修正「单一指数半衰期」的过度简化** |
| **混合情绪** | Berrios, Totterdell & Kellett 2015（meta）；Larsen 等 | E2 | 正负同现稳健（d≈0.77）；需单极双维结构 | 高：**多情绪并存的证据** |
| **情绪调节** | Gross 1998/2015（process model） | E2 | 前因聚焦（情境/注意/认知改变=reappraisal）与反应聚焦（suppression） | 中高：**reappraisal / let_go 的机制依据** |
| **情感作为信息** | Schwarz & Clore 1983 | E2 | mood 被用作判断信息；归因正确可消除偏差 | 中：mood 作软信号的边界 |
| **评价粒度** | Barrett 2004 等（emotional granularity） | E2 | 个体区分同类价性情绪的能力不同 | 中：多情绪标签分层依据 |
| **nostalgia** | Sedikides 等 2015；Van Tilburg 等 2018 | E2 | 自我相关、过去指向、mostly positive bittersweet、低唤醒 | 高：**复核旧通道分类错误** |
| **attachment** | Bowlby 1969 | E2 | 情感联结/寻求接近倾向，属关系构念 | 高：**证明其非情绪** |

> 共 18 项，覆盖「评价理论 / 维度模型 / 工程实现 / 近年 LLM / 动力学 / 分类复核」六类来源，满足「至少两类不同来源」的要求。

---

## 4. 重点候选深查

### 4.1 OCC（E2，最小可实现契约）
- 情绪 = 对**事件（后果）**/**主体（行动）**/**客体（属性）**的价性反应，分别以 **goals / standards / tastes(attitudes)** 评价；**22 型**由 successive differentiation 分出；强度 = 分支 local 变量（desirability/praiseworthiness/appealingness）× 4 全局变量（sense of reality/proximity/unexpectedness/arousal）。
- **对故渊**：三入口恰好对应「目标/约定标准/人物偏好」——与 01/03/06 接口天然吻合。**局限**：OCC 是「情绪空间地图」非时间线，**不规定衰减/累积**（各实现自定）；其 attitude=taste（评价输入），**≠ 对一个人的长期关系态度**。

### 4.2 Scherer CPM（E2，最完整的输入清单）
- 情绪由**序贯 SEC** 触发：**Relevance**（novelty / goal relevance / intrinsic pleasantness）→ **Implication**（cause·agency / expectedness / outcome probability / goal conduciveness / urgency）→ **Coping potential**（control / power / adjustment）→ **Normative significance**（internal·external standards）。后类依赖并**递归修正**前类；normative **最后**评估（需最多信息）。结果分化出感受/生理/表达/行动倾向四分量。
- **对故渊**：给出「事件→情绪需要哪些输入」的完整清单，正答 Q1；relevance 先跑、normative 最后，**天然支持快/慢/分层**。
- **局限**：原为人类心理学，无标准开源实现；落成可计算接口是**我们需补**的。

### 4.3 Smith & Ellsworth 1985（E2，certainty 的归属证据）
- 从 15 种情绪中**恢复出 6 个正交评价维度**：pleasantness、anticipated effort、**certainty**、attentional activity、self–other responsibility/control、situational control；15 情绪按这些维度的评价模式被判别分析以 >40% 正确预测。
- **对故渊**：直接证明 **certainty 是 cognitive appraisal 维度，不是 core affect 轴**——是 Q9 的关键一手证据。

### 4.4 Russell Core Affect / Circumplex（E2，情绪×心境的理论底座）
- 1980：情感状态落在**二维双极**空间（pleasure–displeasure × arousal–sleepiness），情绪词环绕圆周，非独立维。
- 2003：**core affect** 是底层神经生理状态；**归因到某原因 → 情绪片段开始**；**自由漂浮（未归因）→ mood**。
- **对故渊**：给出 Emotion / Mood / Core Affect 的**统一底层 + 不同读取**关系（Q3），并提供 valence–arousal 作持久底座。

### 4.5 FAtiMA Toolkit（E3，本报告核源码，最完整可照搬的结构）
- **仓库**：`github.com/GAIPS-INESC-ID/FAtiMA-Toolkit`，C#，Apache 2.0。本报告核读 **master 分支**原始文件 `Assets/EmotionalAppraisal/ActiveEmotion.cs`、`Mood.cs`（只读，未安装/未运行）。注：memory-affect-01 §04 曾核到 commit `56b7cbd…`（2024-05-31）；本报告读的是 master HEAD，**未重新比对那一 commit**。
- **ActiveEmotion 字段（源码已核）**：`CauseId`（自传记忆事件 id）、`Target`、`EventName`、`EmotionType`、`Valence`、`AppraisalVariables`（产生它的评价变量名）、`InfluenceMood`、`Decay`（0–10 截断）、`Threshold`、`Intensity`、`Potential=Intensity+Threshold`。**自带原因与依据**，直接答「为何产生」。
- **衰减（源码已核）**：`DecayEmotion`：`lambda = ln(HalfLifeDecayConstant)/EmotionalHalfLifeDecayTime; I = I₀·exp(lambda·Decay·Δt)`；`IsRelevant = Intensity>0.1`；同类不同事件用 `ReforceEmotion = log(exp(Potential)+exp(potential))`（log-sum-exp）叠强度。
- **Mood（源码已核）**：`MoodValue ∈ [-10,10]` 单标量；`SetMoodValue` 在 `|value| < MinimumMoodValueForInfluencingEmotions` 时归零；`UpdateMood`：`mood += valence·(emotion.Intensity·EmotionInfluenceOnMoodFactor)`；`DecayMood`：`I = I₀·exp(ln(HalfLifeConst)/MoodHalfLifeDecayTime·Δt)`，跌到阈值即归零。**mood 无 cause 字段** → 直接支撑 Q2。
- **吻合**：情绪带 cause+依据、快/慢分离、解析式衰减、可重算——全中故渊「带依据、可追溯」。
- **不吻合**：mood 单标量（无 P/D/关系维）；**无对特定人的长期态度对象**；**同一 cause 的情绪是替换，矛盾情绪无结构化处理**；C# 与故渊 Python 栈不同。
- **未核实**：仅静态只读，未运行、未验证衰减观感与跨 commit 差异。

### 4.6 ALMA（E2，三层骨架；原实现已停产）
- emotion=short / mood=medium / personality=long；mood 有 **pull（向人格位回归）** 与 **push（被情绪推动）**；向 baseline 回落。
- **对故渊**：三时间尺度骨架可直接借；**局限**：原实现不可获取性未核。

### 4.7 WASABI（E2，维度→分类的顺序证据）
- primary 情绪 = **core affect 在 PAD 空间的连续演化**，向中性**缓慢回落**；**随后才分类**为离散情绪并驱动表情；secondary 情绪走认知层、**mood-congruent** 唤起。mood = 「diffuse valenced state，说不出明确原因」，时长 generally 长于 emotion。
- **对故渊**：给出「连续先演化、离散后分类」的正确顺序（Q5）；mood 弥散无因（Q2）。

### 4.8 EMA（E2，appraisal/coping 工程范式）
- 用**因果图**（信念/愿望/意图/计划/概率）算评价变量：`Perspective / Desirability / Likelihood / Causal attribution / Temporal status / Controllability / Changeability`；coping 反作用于信念/愿望/意图（action、planning、positive reinterpretation、acceptance、denial…）。
- **对故渊**：证明**不靠自然语言**就能算评价变量；coping 模块可作为将来「reinstate/reappraise/let_go」的理论映射。**局限**：依赖显式因果计划表示，故渊事件流未必有。

### 4.9 Sentipolis（E2，最贴近的 LLM 情绪态化设计）
- 每 agent 持**连续 PAD 作持久状态**；**双速更新**（fast 逐轮对话 / slow 在 reflection 时整合检索历史）；**情绪-记忆表示级耦合**（记忆连同 PAD 派生标签存储）；PAD→KNN（人类 PAD 锚点）→ Plutchik 风格标签→生成情绪段落注入 prompt；half-life 衰减。
- 论文自陈**失败/代价**：believability 增益**依赖模型容量**（小模型可能下降）；**emotion-awareness 会轻微降低对社交规范的遵守**——「情绪驱动 vs 规则遵守」的真实张力。此点对故渊「情绪不得越过认知/权限」是重要警示。
- **未核实**：代码/许可是否开源；跨模型一致性为**论文声称**，未由故渊验证。

### 4.10 情绪动力学：时长与衰减（E2，修正过度简化）
- Frijda, Mesquita, Sonnemans & van Goozen 1991：被回忆的情绪片段 **50% 时长 >1 小时**；68.64% 报告 1–3 小时至一周以上；多数人对「情绪很短」的估计偏低。
- Verduyn 等：**时长高度可变**（分钟级到 >1 天），不同情绪不同（悲伤最长，其次内疚/愤怒/羞耻，厌恶/恐惧更短）；**强度与时长仅弱-中相关**。
- Verduyn 等 2015：时长由三类因素决定——(a) 引发事件（时长、评价）、(b) 情绪本身（强度、反刍）、(c) 个体/情境调节。
- **对故渊**：**单一全局指数半衰期是工程简化**；终止更应含「事件解决/重评/反刍停下」；半衰期须**逐类型可配**（FAtiMA 已这样做，E3）。

### 4.11 混合情绪、调节、分类复核
- **混合情绪**（Berrios 等 2015 meta，E2）：正负同现稳健可测；需要**单极正、单极负两条轴**才能表示（Evaluative Space Model）。→ 支持 Q4 的「集合表示」。
- **情绪调节**（Gross process model，E2）：五族策略 = 情境选择/情境修正/注意部署/认知改变（含 reappraisal）/反应调整（suppression）。对故渊**最可落地 = reappraisal（重评同事件→替换/降权情绪）+ 显式 let_go**；**不建议 suppression 式「装作没情绪」**（与 provenance 冲突）。
- **nostalgia**（Sedikides 2015；Van Tilburg 2018，E2）：自我相关、过去指向、**mostly positive bittersweet**、低唤醒→**复杂情绪/记忆派生**，非基本通道。
- **attachment**（Bowlby 1969，E2）：「情感联结 / 寻求接近倾向」→ **Relationship/Attitude/Drive**，非情绪。

---

## 5. 旧故渊方案逐项复核

> 对象：旧「25 维三层」= 驱力 10 + PADCN 5 + 离散 10。
> 本报告核读的旧代码（**E3**）：`guyuan/core/affect/schema.py`（DriveId/PADCNAxisId/DiscreteChannelId 枚举 + DriveAxis/PADCNAxis/DiscreteChannel 结构）、`guyuan/config/affect.yaml`（10 驱力/5 PADCN/10 离散 + interlocks + meaning_weight + bpm_range + persona）。注：驱力层 10 维的复核主体归专题 02；此处只做**归属判断**，不与专题 02 抢结论。

### 5.1 分层结构复核

| 旧层 | 旧定位 | 复核判断 |
|---|---|---|
| 驱力 10 维 | 人格调性（key），月/周 | **移出 Affect** → Motivation（专题 02）。旧注释「驱力超阈值直接驱动行为，独立于情绪」本身已承认它更像动机层。 |
| PADCN 5 维 | 状态和声进行，天/周 | **部分移出**：P、A 可留作 core affect；**D 半属 appraisal**；**C、N 应移入 Appraisal/Cognition**（Q9）。 |
| 离散 10 维 | 瞬时音+bpm+力度，分/秒 | **保留为标签视图**，但不是本体；**nostalgia/attachment 分类错误**（Q10）。 |

### 5.2 PADCN 逐维复核（E3 源码 + E2 理论）

| 旧维 | 旧值域/半衰期 | 复核归属 | 依据 |
|---|---|---|---|
| P 愉悦 | [-1,1]，36h 回弹 | **Core Affect（可留）** | Mehrabian；Russell |
| A 唤醒 | [-1,1]，36h 回弹 | **Core Affect（可留）** | Mehrabian；Russell |
| D 支配 | [-1,1]，36h 回弹 | **半属 appraisal/coping** | WASABI 的 D 由认知层推断（E2）；Mehrabian 承认 D 与 potency/控制相关 |
| C 确定性 | [-1,1]，36h 回弹 | **移入 Appraisal（明确错位）** | Smith & Ellsworth 1985「certainty」是评价维；Mehrabian PAD 无 C |
| N 新颖性 | [-1,1]，36h 回弹 | **移入 Appraisal（明确错位）** | CPM「novelty」是 relevance SEC；Russell 1978：额外维是「关于前因/后果的信念」 |

### 5.3 离散 10 通道逐维复核

| 旧通道 | 分类 | 复核判断 |
|---|---|---|
| joy, trust, fear, surprise, sadness, disgust, anger, anticipation | Plutchik 8 基本情绪 | 可留为**标签层**；trust/anticipation 是否属「基本」存疑（E2） |
| **nostalgia 怀旧** | 列为 0.5h 离散通道 | **层级错位**：真实但为自我相关/过去指向/mostly positive 的**复杂情绪**，非基本通道（Sedikides 2015） |
| **attachment 依恋** | 列为 0.5h 离散通道 | **明确错位**：Bowlby 定义为**情感联结**，属 Relationship/Attitude/Drive，**不是情绪** |

### 5.4 机制层面的复核

1. **离散通道互锁（suppress/amplify）**：`config/affect.yaml` 的 `interlocks`（fear suppress joy 等）。作为**表达层/标签层的冲突规则**可保留候选，但**不能当情绪生成本体**（会掩盖「为何产生」）。
2. **稳态回弹**（PADCN 向 baseline 0.0 指数回归）：方向正确，可留，但**不能推广到 attitude/attachment**（Q6）。
3. **meaning_weight 乘数**（note×2.5/reflection×4/mention×1.8/none×1/let_go×0.5，源码已核）：本质是**有效半衰期 = base × 乘数**——即「意义/认领改变衰减速率」。**可取**，但它作用在**事件的情绪意义**上，应并入「Affect Change Event / 记忆显著性」，**不是 core affect 参数**。
4. **和弦确定性查表**：属表达层（归专题 04），不进 Affect 生成本体。
5. **「驱力超阈值直接驱动行为」**：与故渊护栏（Drive ≠ 行动；不得越权）**冲突**，应删除该越权路径（归专题 02/05）。
6. **数值参数可配 + 衰减不落库、读时现算**：**保留**（FAtiMA 亦如此，E3）。
7. **unknown 不填默认值**：保留（与 provenance 一致）。

---

## 6. 对新架构的可用性拆分

| 拆分 | 内容 | 来源/等级 |
|---|---|---|
| **可直接复用（算法，非代码）** | ① 逐类型指数半衰期衰减式；② log-sum-exp 情绪聚合式；③ ACT 的 EPA 情绪公式 | FAtiMA E3；ACT E2 |
| **可直接复用（结构）** | `ActiveEmotion` 记录：`type/valence/intensity/cause_ref/target/appraisal_basis/decay/threshold` | FAtiMA E3 |
| **可借鉴机制** | ① OCC 三分支评价入口；② CPM 序贯 SEC 作输入清单；③ 快情绪/慢 mood 双层 + 双向弱耦合（系数 ≤0.3、向 baseline 回落）；④ WASABI 的「连续演化→后分类」顺序；⑤ ALMA 的 pull-push mood；⑥ EMA 的评价变量与 coping 映射；⑦ Sentipolis 双速 + 情绪-记忆耦合；⑧ Gross 的 reappraisal 作终止机制 | E2/E3 |
| **需要补（故渊缺口）** | ① 把 CPM/OCC 落成**显式 appraisal 变量 schema**；② **多情绪并存**（同对象多情绪多依据，FAtiMA 只替换）；③ 对具体人的**长期 attitude 独立对象**；④ 情绪**真正的终止条件**（重评/解决/放下，而非只有时钟）；⑤ 情绪→记忆检索耦合（归专题 05） | 综合 |
| **不建议采用** | ① 纯维度生成（无原因、不可解释）；② 离散互锁当**生成**层；③ 单一全局半衰期覆盖所有变量；④ 把 certainty/novelty 当 core affect 轴；⑤ attachment/nostalgia 当瞬时情绪通道 | 综合 |

---

## 7. 跨模块接口

### 7.1 接口候选（输入 / 输出 / 读取 / 信号）

- **输入**：
  - `event`：主体/动作/对象/时间 + 视角（来自 01/02）；
  - `appraisal_context`：相关 goal、standard/约定、对目标对象当前 attitude（来自 03/06）；
  - `prior_affect`：现情绪快照 + mood + 对相关对象 attitude。
- **输出（产生/更新）**：
  - `emotion`：`{type, intensity, valence, target, cause_ref, appraisal_basis, decay 参数}`；
  - `mood`：标量或向量（**无 cause_ref**）；
  - `core_affect`：`{valence, arousal[, dominance]}` 持久底座；
  - `attitude[object]`：慢变，独立对象（若采纳 §9-A 则位在 Affect，否则位在人物/话题模型）。
- **读取的稳定对象**：不可变事件 id（作 cause_ref）；对象稳定引用（人/话题，来自 06）。
- **对外信号**：`affect_changed(object?, delta)`，强度/极性供 05、07 订阅；**`affect change event`** 仅在满足 §2.4-Q7 条件时产生。
- **provenance 契约**：每条情绪留 `cause_ref + appraisal_basis`；被取代的历史情绪**只标 superseded 不删**，且被取代者不参与后续软加权。
- **软/硬边界**：mood / attitude / core affect **只作强度偏置与召回软权**，不得硬过滤或替用户决策；情绪不自动改事项状态或发消息；mood/attitude 更新只产候选。

### 7.2 横向必答表（每个变量都要能回答）

| 变量 | 归属类别 | cause/target | 时间尺度 | snapshot/ledger/derived | provenance? | 影响方式 | 模型可写? | 衰减/失效 | 自我强化环? | 接口 |
|---|---|---|---|---|---|---|---|---|---|---|
| **Core Affect (V/A,±D)** | Affect | 无（可无对象） | 分钟~天 | snapshot | 否 | bias/软信号 | 否（确定性） | 向 baseline 解析式回落 | 弱（经 mood） | 注入 05/07 |
| **Emotion 片段** | Affect | **有 cause_ref + 可 target** | 秒~分（可数日） | ledger（多条） | **是**（cause+basis） | bias + 行动倾向输入 | 是（评价候选） | 逐类型半衰期 + 重评/解决 | 是（→mood→偏置情绪），需有界 | → 05/03 |
| **Mood** | Affect | **无单一 cause** | 时~天 | snapshot 或 derived | 否 | 软偏置（≤0.3） | 否 | 慢解析式回 baseline（可为零基归零） | 是（与情绪双向），需有界 | 注入 05/07 |
| **Appraisal 变量** | Cognition（Affect 输入） | target=事件 | 瞬时 | ledger（可重算） | **是**（锚 event） | 只作生成输入 | 是（产候选） | **不衰减**（证据链） | 否 | → Affect |
| **Attitude** | 长期对象（非 Affect 运行时） | **有 target（对象）** | 周~月 | ledger/derived view | **是**（证据累积） | bias | 是（候选） | **不按时间衰减**，按证据更新 | 弱 | → appraisal 输入 |
| **Chord** | Affect 表达层（视图） | 无 | 三级 | derived view | 否 | UI/prompt 表达 | 否（确定性映射） | 跟随底层 | 否 | 归 04 |
| **Affect Annotation** | Memory 派生 | 有（锚 event） | **冻结** | 不可变快照 | **是** | 只读 | 否（写后不改） | 不衰减 | 否 | 05/07 |
| **Affect Change Event** | Affect 长期留痕 | 有 cause | 事件时间 | ledger（append） | **是** | 供检索/调度 | 是（产候选） | 不变（历史） | 否 | → 02/05/07 |
| **nostalgia** | 复合情绪 / 记忆派生 | 有（记忆+自我） | 中 | derived | 是 | bias/表达 | 是 | 非 0.5h | 弱 | → 01/06 |
| **attachment** | **Relationship/Attitude/Drive** | 有（对象） | 周~月 | ledger | 是 | bias | 是 | 不按时间衰减 | 弱 | → 06/02 |

---

## 8. 失败模式与风险

1. **自激放大（核心风险）**：情绪→mood→偏置后续同类情绪→更强 mood 的正反馈。FAtiMA 用系数 ≤0.3 + 基线回落缓解；Sentipolis 的 prompt 注入更险。**缓解**：有界标量 + 半衰期 + 冷却 + 上限。
2. **原因漂移/幻觉**：LLM 评价可能编造「为何生气」。**缓解**：`appraisal_basis` 必须锚到被评价事件原文，评价只产候选（保留 provenance）。
3. **矛盾情绪被误合并**：FAtiMA 同 cause 直接替换会丢「既高兴又难过」。**缓解**：允许同对象多情绪并存并定义聚合/呈现。
4. **过度简化衰减**：把「情绪结束」当作纯时钟。文献（Frijda/Verduyn）显示时长高度可变、受重评/反刍驱动。**缓解**：逐类型半衰期 + 重评/解决/let_go 作为真正终止。
5. **态度漂移**：对一个人的态度被单次强情绪改写会长期不一致。**缓解**：独立慢时间常数 + 证据累积 + 历史版本可溯（不删）。
6. **分类污染**：把 certainty/novelty 当情感轴、把 attachment/nostalgia 当瞬时通道，会让「不是情绪的东西」进入衰减动力学。**缓解**：按 §5 分类复核归位。
7. **model-write 失控**：模型自由改写高权威情绪对象。**缓解**：模型优先产候选；高权威对象（长期态度、核心身份）写入门槛更高。
8. **状态漂移/不可重算**：mood/attitude 若不可重算，调参即漂移。**缓解**：解析式衰减、读时现算、不落库衰减值、可整层重算（FAtiMA 与旧故渊共同印证，E3）。
9. **成本未定**：appraisal 需模型、向量/衰减可确定性算。**缓解**：appraisal/标注进批处理派生，不进实时链路（沿用 04/05 结论）。
10. **未验证**：LLM 评价稳定性与成本；衰减参数在「真实活动 + 长跨度」下的观感；最小可用 appraisal 变量集；Sentipolis 代码可获取性。

---

## 9. 推荐机制：A/B/C/D

> 给出 ≥2 个（此处 4 个）**可比较**的 Affect 本体+动力学候选方案，不要求选定。

### 方案 A · 轻量（二维 core affect + 情绪片段 + 标量 mood）
- **本体**：core affect = valence/arousal 二维持久底座；Emotion = 带 `cause_ref/target/appraisal_basis` 的激活情绪**集合**；Mood = 单标量（±），无 cause；**Attitude 移出** Affect（存人物/话题模型）。
- **动力学**：core affect 与情绪强度解析式回落；mood 被情绪推动并慢回 baseline；每轮 Δ 只运行态，满足 §2.4-Q7 条件才成 event。
- **优点**：工程最省；与 FAtiMA 结构最接近（E3 可照搬）；无 C/N 污染。
- **代价**：mood 单标量信息量低；无法表达 P/D 分离；多情绪聚合需自补。

### 方案 B · 平衡（PAD 基底 + 情绪 portfolio + mood 向量 + appraisal 变量层）
- **本体**：core affect = PAD 三维（D 谨慎、视为半评价）；Emotion = portfolio（多因并存、可相反价）；Mood = 慢向量/或慢标量+方向；**Appraisal 变量层独立**（CPM 序贯 SEC）；**Attitude = 独立慢对象**（由多次 appraisal 累积，不删历史）。
- **动力学**：core affect/情绪逐类型半衰期；mood 双向弱耦合（≤0.3）；态度按证据更新不按时间衰减；reappraisal/let_go 作终止。
- **优点**：覆盖现象最全（混合情绪、关系态度、重评）；接口清晰。
- **代价**：对象更多、写入权限更复杂；D 的归属需仲裁。

### 方案 C · 完整（B + 显式 regulation + 认知/评价分离 + 评测闭环）
- **本体**：在 B 之上加 **regulation 模块**（reappraisal / attention / let_go）与 **Cognition 边界**（C/N 明确落评价侧），并预置「Affect Change Event / Affect ledgers」。
- **动力学**：同上 + 冷却/上限/去自激规则。
- **优点**：最贴近理论与故渊长期目标；可挂评测（e.g., 用情绪时长/混合情绪/态度一致性做窄验证）。
- **代价**：VPS/工程成本最高；有过度设计风险。

### 方案 D · 替代（core affect 单层 + 离散仅作视图，Attitude 完全外置）
- **本体**：只保留 valence–arousal 连续底座 + 派生离散标签；不设独立 mood 状态（mood=core affect 的慢窗平均 derived view）；Attitude 完全在人物模型。
- **优点**：对象最少，符合「别把可算的数字当本体」。
- **代价**：丢失 mood 的「独立被推动」证据；对「说不清原因的持续低落」表达力弱。

**四方案对比**

| 维度 | A 轻量 | B 平衡 | C 完整 | D 替代 |
|---|---|---|---|---|
| core affect 维数 | 2 | 3（PAD） | 3 | 2 |
| 情绪表示 | 集合 | portfolio | portfolio | 集合 |
| Mood | 标量 | 向量/慢 | 向量/慢 | derived（无状态） |
| Attitude 位置 | 外置 | 外置独立对象 | 外置独立对象 | 完全外置 |
| appraisal 变量层 | 内联字段 | 独立层 | 独立层+Cognition 边界 | 内联 |
| regulation | 无 | 隐式（reappraisal） | 显式模块 | 无 |
| 工程成本 | 低 | 中 | 高 | 低 |
| 主要风险 | 表达弱 | 复杂度 | 过度设计 | mood 证据缺失 |

---

## 10. 给总窗口的输入（≤10 条）

1. **情绪生成用 OCC 三分支 + CPM 序贯 SEC**：OCC 定「评什么」，CPM 定「按什么顺序、要哪些输入」。
2. **Emotion 必须有 `cause_ref + appraisal_basis`；Mood 必须允许无单一 cause**（FAtiMA 结构 E3 已证可做到）。
3. **快/慢分层是硬需求**：Emotion(分~时)/Mood(时~天)/Attitude(周~月)，各自独立时间常数与基线。
4. **Core Affect 收敛到 valence–arousal（±dominance）**；**C（certainty）、N（novelty）移入 Appraisal**——它们是评价/认知信号，不是情感轴（Smith & Ellsworth 1985；Mehrabian）。
5. **多情绪必须并存**：现有实现普遍「替换」，故渊需自补「同对象多情绪多依据」（混合情绪 meta d≈0.77）。
6. **数值 + 离散标签并存**：连续状态负责动力学，离散标签只作视图（GAMYGDALA/WASABI/Sentipolis 一致）。
7. **衰减分两类**：瞬时偏移/强度用逐类型解析式半衰期；Attitude/Attachment/Appraisal **不按时间衰减**，按证据/重评更新。
8. **「情绪结束」不能只靠时钟**：Frijda/Verduyn 证明时长高度可变、强度与时长弱相关 → 需配 reappraisal / 解决 / let_go。
9. **Attitude 不作为 Affect 运行时状态**：它是独立慢对象，Affect 只作输入（OCC 的 attitude 是评价输入，勿混淆）。
10. **旧离散通道复核结果**：attachment 明确错位（属 Relationship/Attitude/Drive）、nostalgia 层级错位（复杂情绪非基本通道）、trust/anticipation 存疑；这些**不能作验收标准**。

---

## 11. 证据索引

> 访问日均为 2026-10-07。等级：E3=只读源码；E2=论文正文/官方文档；E1=README/项目页/摘要。

**E3（源码核查，只读）**
- FAtiMA-Toolkit（`github.com/GAIPS-INESC-ID/FAtiMA-Toolkit`，C#，Apache 2.0）：本报告核读 **master 分支** `Assets/EmotionalAppraisal/ActiveEmotion.cs`（CauseId/Target/EmotionType/Valence/AppraisalVariables/InfluenceMood/Decay/Threshold/Intensity/Potential、`DecayEmotion` 指数式、`ReforceEmotion` log-sum-exp、`IsRelevant = Intensity>0.1`）与 `Assets/EmotionalAppraisal/Mood.cs`（单标量 [-10,10]、`DecayMood`、`UpdateMood`、`MinimumMoodValueForInfluencingEmotions` 归零）。**未安装、未运行；未与 memory-affect-01 §04 的 commit `56b7cbd992f953cfe21a7b12cb1a0e6cdf6ccf9f` 重新比对**。
- 旧故渊代码（只读）：`guyuan/core/affect/schema.py`（DriveId/PADCNAxisId/DiscreteChannelId、DriveAxis/PADCNAxis/DiscreteChannel、AffectSnapshot）、`guyuan/config/affect.yaml`（10 驱力 + 5 PADCN + 10 离散 + interlocks + meaning_weight + bpm_range + persona）。**未修改**。

**E2（论文正文/官方文档）**
- OCC：Ortony, Clore & Collins, *The Cognitive Structure of Emotions*, Cambridge UP, 1988（2nd ed. 2022）；剑桥书目页 cambridge.org/core/books/cognitive-structure-of-emotions/…；OCC 结构综述 people.idsia.ch/~steunebrink/Publications/KI09_OCC_revisited.pdf（22 型、三分支）。
- Scherer CPM：Scherer, "Emotions are emergent processes…", *Phil. Trans. R. Soc. B*, PMC2781886（SEC 表 relevance/implication/coping/normative）；Scherer & Moors 2019, *Annu. Rev. Psychol.*。
- Smith & Ellsworth 1985：*JPSP* 48(4):813–838, doi:10.1037/0022-3514.48.4.813（6 维含 certainty）。
- Russell 1980：*JPSP* 39(6):1161–1178, doi:10.1037/h0077714（circumplex）；Russell 2003：*Psych. Review* 110(1):145–172, doi:10.1037/0033-295X.110.1.145（core affect / emotion / mood）。
- Mehrabian 1995：*Genet Soc Gen Psychol Monogr* 121(3):339–361, PMID 7557355（PAD 三正交轴）；Mehrabian 1996 PAD 论文 PDF（cs.uky.edu）。
- Plutchik 1980 *Emotion: A Psychoevolutionary Synthesis*（8 基本情绪、dyad）；PyPlutchik PMC8409663。
- ALMA：Gebhard 2005, AAMAS'05, dl.acm.org/doi/10.1145/1082473.1082478；alma.dfki.de/papers/aamas05.pdf。
- WASABI：Becker-Asano 2008 PhD thesis（becker-asano.de/…WASABI_Thesis.pdf）；Becker-Asano & Wachsmuth 2009, *AAMAS* doi:10.1007/s10458-009-9094-9；IVA08 PDF（gki.informatik.uni-freiburg.de）。
- EMA：Marsella & Gratch, "EMA: A process model of appraisal dynamics", *Cognitive Systems Research* 2009（stacymarsella.org/…N_Emcsr_Marsella.pdf）。
- FAtiMA 论文：doi:10.1145/3510822；arXiv 2103.03020。
- Sentipolis：arXiv 2601.18027 / ACL Findings 2026（aclanthology.org/2026.findings-acl.368/）：连续 PAD + 双速 + 情绪-记忆耦合 + half-life 衰减 + KNN→Plutchik。
- 情绪时长：Frijda, Mesquita, Sonnemans & van Goozen 1991, *Int. Rev. Studies on Emotion* Vol.1:187–225（经开放教材 psu.pb.unizin.org 转述；**未直读原书**）；Verduyn 等 2013, *Eur. J. Pers.* doi:10.1002/per.1897；Verduyn 等 2015, *Emotion Review* 7(4):330–335, doi:10.1177/1754073915590618。
- 混合情绪：Berrios, Totterdell & Kellett 2015, *Front. Psychol.* 6:428, doi:10.3389/fpsyg.2015.00428（meta d≈0.77）；Berrios 等 2015 *Cogn. Emot.*（goal conflict）。
- 情绪调节：Gross 1998 *JPSP* 74:224–237；Gross 2015 "Emotion Regulation: Current Status and Future Prospects"（johnnietfeld.com/…gross_2015.pdf）。
- 情感作为信息：Schwarz & Clore 1983, *JPSP* 45:513–523。
- 评价粒度：Barrett 等（emotional granularity）PMC8355493。
- nostalgia：Sedikides 等 2015, *Advances in Experimental Social Psychology*（southampton.ac.uk/…Sedikides…）；Van Tilburg, Wildschut & Sedikides 2018, *Cogn. Emot.*（正价/趋近/低唤醒）。
- attachment：Bowlby 1969/1988（attachment = 情感联结，寻求接近），见 attachment theory 综述 PMC3051370。

**E1（项目页/摘要，辅助）**
- FAtiMA GitHub README（E1/E2 之间的项目说明）。
- WASABI / ALMA 项目主页。
- `memo/Nocturne-Memory-Core.md`（AFFIRM/REJECT/SUSPEND、弱化时间衰减、BIAS NOT SCRIPT）——本项目内部参考。

**未核实一览**
- Sentipolis 代码与许可是否开源；其跨模型数字为**论文声称**，未由故渊验证。
- FAtiMA 本报告读 master HEAD，**未比对 memory-affect-01 §04 的 pinned commit**。
- ALMA/WASABI 原实现可获取性；GAMYGDALA 各语言完成度（未核）。
- Frijda 1991 原始书页数字为**开放教材转述**，未直读原文。
- 旧故渊 25 维仅作对照复核，**不构成新方案前提或验收标准**。
