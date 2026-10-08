# 04 · 情绪生成与内部状态（appraisal / emotion / mood / attitude）

> 专题 04 报告。外部机制调研，不盘点故渊/Serein 现状，不选最终 schema。证据上限 E3（只读源码）；写入 2026-10-07。FAtiMA 核查 commit 见 §10。

## 1. 故渊要解决的问题

**需求**：一件事（真实经历）不只变成记忆条目，而能产生带**原因**的自身情绪，且情绪跨轮、跨天持续、累积、衰减，供自主安排与语气/主动性。要回答：经历为何产生某种情绪；短期 emotion、较慢 mood/attitude 各由什么驱动、以何速度更新与回落；矛盾情绪与长期关系态度怎么表示。

**边界**：只研究"事件→意义评价→情绪状态"的生成与演化；情绪反作用于注意/写入/检索属 05，情绪触发唤醒属 03/07；情绪**呈现方式**（如旧和弦）不在核心。

**接口**：输入依赖 01（事件/证据）、06（人物/世界条目、共同约定）；输出给 05（情绪作记忆可写信号）与 07（当前情绪快照注入）。**不解决**：不选数值轴集/阈值、不决定是否落库、不替用户判断"应否"有情绪；不把用户情绪识别当故渊自身情绪。

## 2. 关键概念与必要区分

**统一坐标（本专题定义，沿用任务卡 §2 名称，不另造名）**：evidence=不可改写原始片段（唯一事实来源）；event=可评价单元(主体/动作/对象)，appraisal 入口；fact/interpretation/commitment/person-world entry/open-loop state=appraisal 所参照的目标、标准、关系（属 01/03/06，只当输入）；affect state=本专题对象，分三档(下)。

**理论分歧（关键）**：
1. **分类 vs 维度**：OCC 给离散类型（22 种），PAD/VAD 给连续维；工程上常并用（内部分类+强度，可再投影到维度，GAMYGDALA 即提供 OCC→PAD 转译），非二选一。
2. **快/慢三档（操作定义，综合 ALMA/WASABI/Sentipolis/FAtiMA）**：**emotion（快，秒~分）**绑定具体事件/对象、**有原因、会衰减消失**（FAtiMA 情绪半衰期 15 tick、mood 60）；**mood（中，时~天）**弥散、无具体对象，被情绪推高/压低并向基线回落（ALMA 的 PAD mood 有 pull/push；FAtiMA 用单标量）；**attitude/personality（慢，周~月）**对**特定对象**（人/话题/作品）的长期倾向或人格基线（OCC 的 attitude=对客体喜好 love/hate，是对人态度的落点；ACT 把长期关系情感单列）。
3. **appraisal ≠ sentiment**：appraisal 是"事件对我的目标/规范/偏好意味着什么"的结构化判断，必须能回答"为什么"；极性标签不能回答原因。**最易混淆**：识别用户情绪 / 故渊自身情绪 / 给记忆贴情绪标签 / 情绪驱动行为——四者不同，任一项目只证明其一不能推出其余（任务卡 §4）。

## 3. 候选总表

| 候选 | 类型 | 解决什么 | 核心机制 | 实现 | 证据 | 年份 | 适配 |
|---|---|---|---|---|---|---|---|
| OCC 模型 | 理论 | 事件→22 种情绪 | 事件/主体/客体三分支，对 goals/standards/attitudes；local 强度+4 全局变量 | FAtiMA/GAMYGDALA/EmoACT | E2 | 1988 | 高：最小契约 |
| Scherer CPM | 理论 | 评价要问哪些问题 | 序贯 SEC 4 类：相关性/含义/应对/规范；后类递归修正前类 | CPM-MultiAgent | E2 | 2009/2013 | 高：输入清单 |
| PAD / VAD | 理论/格式 | 情绪连续表示 | 愉悦-唤醒-支配(+确定/新颖)；8 卦限 mood | ALMA/WASABI/Sentipolis | E2 | Mehrabian 1996 | 中：表示非生成 |
| 情感控制论 ACT | 理论 | 关系期待被打破→情绪 | EPA 空间；impression−identity 偏转生成情绪 | EmoACT(2504.12125) | E2 | Heise 1979 | 中：关系态度落点 |
| 三层 ALMA | 理论/设计 | 快/中/慢如何互作 | emotion 短/mood 中/personality 长；VEC pull-push；向人格位回归 | 论文（原实现停产） | E2 | Gebhard 2005 | 高：三层骨架 |
| **FAtiMA Toolkit** | 代码 | OCC 可运行实现(含衰减/情绪记录) | AppraisalFrame→ActiveEmotion(带 CauseId/AppraisalVariables/Decay)→Mood 单标量；指数半衰期 | C#，Apache 2.0 | **E3** | c.56b7cbd | 高：结构可照搬 |
| GAMYGDALA | 代码 | 游戏 NPC 轻量 OCC | goals+事件标 goal congruence→OCC 情绪；指数衰减；关系值 | JS(MIT)/C#/Java | E1–E2 | 2014 | 中：输入 schema |
| Sentipolis | LLM 框架 | LLM agent 情绪长期连续 | 连续 PAD+双速动态+情绪-记忆耦合 | 论文(代码未核实) | E2 | 2026 | 高：最贴合，待验证 |

> 8 项，未补弱候选。ACT/ALMA/GAMYGDALA 分别从"关系态度/三层/输入 schema"角度保留。

## 4. 重点候选深查

### 4.1 FAtiMA Toolkit（E3，最完整可照搬）
- **仓库**：`GAIPS-INESC-ID/FAtiMA-Toolkit`，commit `56b7cbd…`(2024-05-31)，Apache 2.0，C#/.NET；纯规则、无 LLM，评价规则 GUI 预配、事件外部喂入。
- **机制**：`AppraisalFrame`(事件+具名 appraisal 变量+视角 Perspective)经 `OCCAffectDerivationComponent` 转 `IEmotion`，再由 `ConcreteEmotionalState.AddEmotion` 落 `ActiveEmotion`。OCC 22 型分六支：Desirability→WellBeing(Joy/Distress)、Praiseworthiness→Attribution(Pride/Shame/Admiration/Reproach)、Like→Attraction(Love/Hate)、合成→Composed、×为他人→FortuneOfOthers、GoalSuccessProbability 前后对比→Hope/Fear/Relief/Disappointment/Satisfaction/FearsConfirmed。
- **状态**：`ActiveEmotion`={CauseId(指向自传记忆事件 id)+EventName+Target(指向对象)+EmotionType+Valence+**AppraisalVariables**(产生它的评价变量名)+InfluenceMood+Decay+Threshold+Intensity}；**自带原因与依据**，直接答"为何产生"。`Goal`=Name+Significance+Likelihood。
- **更新/衰减**：同 CauseId 再评=**reappraisal**(替换旧、不重复计 mood)；同类不同事件**聚合**；`ReforceEmotion` 用 log-sum-exp 叠强度；`Intensity=intensityATt0·exp(ln(HalfLifeDecayConstant)/EmotionalHalfLifeDecayTime·Decay·Δt)`（解析式、逐类型独立 Decay），强度≤0.1 移除。
- **mood**：单标量[-10,10]；`mood+=valence·Intensity·EmotionInfluenceOnMoodFactor`，按 MoodHalfLifeDecayTime 指数回落，|mood|<0.5 归零；反向 `potential+=valence·mood·MoodInfluenceOnEmotionFactor`（**mood 偏置后续同价情绪**）。默认系数均 0.3，Emotion half-life=15、Mood=60。
- **吻合**：情绪带原因与依据、快/慢分离、解析式衰减、可重算——全中"带依据、可追溯"。**不吻合**：mood 单标量(无 P/D/关系维)；无对**特定人**的长期态度对象；矛盾情绪无结构化处理(同 cause 替换)；无记忆-情绪检索耦合；C# 与故渊 Python 栈不同。**未核实**：仅静态只读、未运行、未验证衰减观感。

### 4.2 Scherer CPM（E2，最完整的 input 规格）
- **机制**：情绪由**序贯 SEC** 触发，4 类按复杂度/时间展开：**Relevance**(novelty/intrinsic pleasantness/goal relevance)→**Implication**(cause/expectedness/outcome probability/goal conduciveness/urgency)→**Coping potential**(control/power/adjustment)→**Normative significance**(self/social norms/self-ideal)；后类依赖并**递归修正**前类，结果因果性分化出感受/生理/表达/行动倾向四分量。**价值**：给出"事件→情绪需要哪些输入"的**完整清单**，正答本专题必答项；relevance 先跑、normative 最后(可缺失)，天然支持快/慢/分层。
- **不吻合/未核实**：原为人类心理学，无标准开源实现；落成可计算接口是**我们需补**。CPM-MultiAgent(2607.07824) 用 4 个 LLM 评价 agent 对应 4 类，属"论文声称"，未核源码。

### 4.3 OCC 模型（E2，最小可实现契约）
- **机制**：情绪=对**事件**(后果)/**主体**(行动)/**客体**(属性)的 valence 反应，分别以 **goals/standards/attitudes** 评价；22 种由 successive differentiation 分出；强度=分支 local 变量(desirability/praiseworthiness/appealingness)×4 全局变量(sense of reality/proximity/unexpectedness/arousal)。**价值**：三入口恰对应 06 的目标、共同约定、人物偏好，全局变量给强度可解释调节项。**不吻合**：OCC 是"情绪空间的地图"非时间线，不规定衰减/累积(各实现自定)，其 attitude 是客体喜好、≠对一个人的长期关系态度。

### 4.4 情感控制论 ACT / EmoACT（E2 理论 + E1–E2 实现）
- **机制**：所有要素(身份/行为/情绪/mood)在 EPA(评价/权势/活跃)三维有文化情感值；事件造成 impression 偏离 identity 的**偏转 deflection**，情绪由偏转算出(EmoACT 给逐维公式)；人再调整行为/重定义以减少 deflection；长期反复偏转可改变角色**关系身份**(mood)。**价值**：长期关系态度与"期待被打破→情绪"的成熟数学化路径，EPA 与 06 人物模型天然衔接。**不吻合**：依赖大规模文化 sentiment 词典(需选/建)，偏离"事件因果"叙事；EmoACT 代码/许可未核实。

### 4.5 Sentipolis / LLM 双层（E2，最贴近，待验证）
- **机制(论文声称)**：每 agent 持连续 PAD 作**持久状态**；**双速**更新——fast 对当轮对话、slow 在 reflection 时整合检索历史(引 EMA"评价快、推断慢")；情绪**表示级**耦合记忆(记忆连同 PAD 派生情绪标签存储，可回取以条件后续情绪与行动)；KNN 把 PAD 映到语义情绪词并生成情绪段落注入 prompt。**价值**：与"情绪持久化+记忆耦合+快慢分层"几乎逐条对应。**不吻合/未核实**：基准是**社交仿真**非私人长期陪伴；代码是否开源未核实；"情绪段落注入 prompt"有自激风险。

## 5. 对故渊的可用性拆分表

| 拆分 | 内容 |
|---|---|
| **可直接复用代码** | 无 Python 现成完整实现(FAtiMA=C#、GAMYGDALA=JS)。可复用**算法**：指数半衰期衰减式、log-sum-exp 情绪聚合式、ACT 的 EPA 情绪公式。 |
| **可借鉴机制** | ①FAtiMA 的 ActiveEmotion 记录结构；②快情绪/慢 mood 双速与双向弱耦合；③OCC 三分支评价入口；④CPM 4 类 SEC 作输入清单；⑤ACT 的 deflection→关系态度；⑥ALMA/WASABI 的 mood 向基线 pull 回落。 |
| **我们需要补** | 把 CPM/OCC 落成**显式评价变量 schema**；情绪**多因并存**(矛盾情绪，FAtiMA 只替换)；对具体人/话题的**长期 attitude** 作独立对象；情绪→记忆检索耦合(属 05)；LLM 评价成本与缓存；若走 ACT 需中文词典。 |
| **不建议采用** | 纯 VAD 维度生成(无原因、不可解释)；离散互锁当**生成**层(掩盖原因)；Ghost Town 式涌现情绪(不可归因)。 |
| **未核实** | Sentipolis/CPM-MultiAgent/EmoACT 代码与许可；ALMA/WASABI 原实现可获取性；GAMYGDALA C#/Java 完成度；各论文跨模型一致性(一律"论文/实现声称"，非我们验证)。 |

## 6. 跨模块接口候选

- **输入**：`event`(主体/动作/对象/时间，来自 01/02)；`appraisal_context`(相关 goal、标准/约定、对目标对象当前 attitude，来自 03/06)；`prior_affect`(现情绪快照+mood+对相关对象 attitude)。
- **输出(产生/更新)**：`emotion`{type,intensity,valence,target,**cause_ref**,**appraisal_basis**,decay 参数}；`mood`(标量或向量)；`attitude[object]`(慢变)。
- **读取的稳定对象**：事件不可变 id(作 cause_ref)；对象稳定引用(人/话题，来自 06)。**对外信号**：`affect_changed(object?,delta)`，强度/极性供 05、07 订阅。
- **必须保留来源/依据**：每条情绪留 cause_ref+评价依据；被取代的历史情绪只标 superseded 不删(对应"过去事实≠当前理解")，且被取代者不参与后续软加权。
- **只软影响不硬决定**：mood 仅作强度偏置与召回软权，**不得**硬过滤或替用户决策；情绪不自动改事项状态或发消息；mood/attitude 更新只产候选。

## 7. 风险、失败模式与开放问题

- **自激放大**：情绪→mood→偏置后续情绪→更强 mood 的正反馈(FAtiMA 用 0.3 系数+基线回落缓解；Sentipolis 的 prompt 注入更险)。需有界标量+半衰期+冷却。
- **原因漂移/幻觉**：LLM 评价可能编造"为何生气"；appraisal_basis 必须锚到被评价事件原文，评价只产候选。
- **矛盾情绪**：FAtiMA 同 cause 直接替换会丢"既高兴又难过"；需允许同对象多情绪并存并定义聚合/呈现。
- **关系态度漂移**：对一个人的态度若被单次强情绪改写会长期不一致；需独立慢时间常数+历史依据可溯。
- **状态漂移/回算**：mood/attitude 若不可重算，调参即漂移。FAtiMA 与故渊旧模块都用**解析式衰减、读时现算、不落库衰减值**——该原则值得保留。
- **未验证**：LLM 评价的稳定性与成本；衰减参数在"真实活动+长跨度"下的观感；appraisal 变量最小可用集。
- **情绪调节(regulation)**：Gross 区分前因聚焦(改情境/注意/cognitive change=reappraisal)与反应聚焦(suppression)。对故渊最可落地=**reappraisal**(重评同事件→替换情绪，FAtiMA 已内建)+显式 let_go(降权)；不建议 suppression 式"装作没情绪"(与 provenance 冲突)。

**对旧 25 维设计的对照(只作参考，非前提)**：**有价值思想**=按**时间尺度分层**(分/秒–天/周–月/周)、向基线**稳态回弹**、**多层覆盖合成**、**意义权重乘数**(被写下/被提起→衰减更慢，可并入 4.1 的 Decay)。**不沿用**：离散通道互锁(lock/suppress)当情绪**生成**会掩盖"为何产生"；和弦查表当情绪本体。旧 3 轴 V1 与 25 维均**不构成上限**。

## 8. 本专题结论（A/B/C/D）

**A · 建议进入组合设计（机制，非项目）**：①**OCC 式三分支评价入口**(goal/standard-attitude/object-like)作情绪生成最小契约；②**FAtiMA 的情绪记录结构+解析式半衰期衰减+log-sum-exp 聚合**，情绪带 cause_ref 与 appraisal_basis、可追溯可重算；③**快情绪/慢 mood 双层+双向弱耦合**(mood 偏置强度≤0.3、向基线回落)；④**CPM 4 类 SEC 作评价输入清单**(relevance→implication→coping→normative，序贯、后类修正前类)；⑤**情绪、依据、被取代者全保留(supersession 标记不删)**的 provenance 原则。

**B · 值得借鉴但不直接采用**：ALMA 的 pull-push mood 算法；ACT 的 deflection/EPA 关系态度(需选词典)；GAMYGDALA 的"事件标 goal congruence"输入 schema(借结构，不借其无记忆)；WASABI 的一级/二级情绪区分。

**C · 保留候选需验证**：Sentipolis(双速+记忆耦合，最贴合，代码/许可待核)；CPM-MultiAgent、Chains-of-Affect(LLM 评价分解)；Dynamic Affective Memory Management(态度更新，仅摘要)。

**D · 当前不建议**：纯 VAD/维度即情绪(不可解释原因)；离散互锁当生成层；Ghost Town 式涌现情绪(不可归因)；任何"情绪自动改事项/替用户决定"的耦合。

## 9. 给最终汇总窗口的输入（≤10）

1. 情绪生成用 **OCC 三分支 + CPM 序贯 SEC** 组合：OCC 定"评什么"，CPM 定"按什么顺序、要哪些输入"。
2. **情绪必须带 cause_ref + appraisal_basis**(FAtiMA 已验证可做到)，否则无法满足"为何产生"与可追溯。
3. **快/慢分层是硬需求**：emotion(分)/mood(时~天)/attitude(周~月)，各自独立时间常数与基线。
4. **对具体人的长期态度需独立对象**(ACT/OCC-attitude 落点)，不能被单次情绪覆盖；与专题 06 共同决策其存储位置。
5. **矛盾情绪需并存**：现有实现普遍"替换"，故渊需自补"同对象多情绪多依据"。
6. **衰减一律解析式、读时现算、不落库、可整层重算**(FAtiMA 与故渊旧模块共同印证)。
7. **mood/attitude 只作软信号**，不硬过滤、不替用户决策——与 05、07 的"软/硬"决策相连。
8. 旧 25 维的**分层+稳态回弹+意义权重乘数**可保留到设计，但**和弦查表与离散互锁不进入情绪生成本体**。
9. LLM 路线(Sentipolis 双速+情绪-记忆耦合)是**候选待验证**，非既成能力；其 prompt 注入有自激风险，留给 05。
10. 组合阶段设**统一"评价变量"接口**(goal_relevance/desirability/praiseworthy/expectedness/coping/norm/attitude_to_object)，作 01/03/06 与情绪模块的唯一契约。

## 10. 证据索引

访问日均为 2026-10-07。

- **E3**：FAtiMA-Toolkit（github.com/GAIPS-INESC-ID），commit `56b7cbd992f953cfe21a7b12cb1a0e6cdf6ccf9f`(2024-05-31)，Apache 2.0；核查路径 `Assets/EmotionalAppraisal/{ActiveEmotion,ConcreteEmotionalState,Mood,EmotionalAppraisalConfiguration,EmotionDisposition,AppraisalVariables}.cs` + `OCCModel/{OCCEmotionType,OCCAffectDerivationComponent}.cs`（只读，未安装/未运行）。
- **E2**：Ortony/Clore/Collins《The Cognitive Structure of Emotions》1988；水卢 CS886 Lec5(cs.uwaterloo.ca/~jhoey/teaching/cs886-affect/lecture5.pdf)；AAAI07《A Logic of Emotions for Intelligent Agents》(cdn.aaai.org/AAAI/2007/AAAI07-021.pdf)；Scherer CPM(pmc.ncbi.nlm.nih.gov/articles/PMC7963263)+Scherer & Moors 2019(Annu. Rev. Psychol.)；FAtiMA 论文(doi 10.1145/3510822)；ALMA(dl.acm.org/doi/10.1145/1082473.1082478, Gebhard 2005)；WASABI(link.springer.com/article/10.1007/s10458-009-9094-9, 2008)；Affect Control Theory(affectcontroltheory.org/the-theory/overview)；Sentipolis(arxiv.org/abs/2601.18027)；CPM-MultiAgent(arxiv.org/abs/2607.07824)；Chains-of-Affect(PLOS ONE 2024, doi:10.1371/journal.pone.0301033)；Gross 调节模型(cs.vu.nl/~wai/Papers/CSR09emreg.pdf)。
- **E1–E2**：GAMYGDALA(github.com/broekens/gamygdala，浮动 main，MIT)+Popescu 2014 IEEE TAC；EmoACT(arxiv 2504.12125)。
- **E1（仅作 D 类反例）**：Ghost Town predictive-appraisal poster。

> Sentipolis/CPM-MultiAgent 跨模型一致性为**论文声称**，未由故渊验证。旧 25 维仅作对照，引 `core/affect/{schema,layers}.py`、`config/affect.yaml`、`docs/tasks/task-14-affect-25dim.md`，不构成上限。
