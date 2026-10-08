# 专题 03 · Affect × Motivation 双向耦合

> 日期：2026-10-07
> 性质：第二轮并行调研报告（研究，不是设计定稿、不是 schema、不是实施授权）。
> 任务卡：`guyuan/docs/tasks/memory-affect-02-affect-motivation-research.md` §6 专题 03。
> 输入本体（上游结论，本报告不重复其结论，只在其之上做耦合）：`01-affect-ontology-dynamics.md`、`02-motivation-drive-desire.md`；共用背景 `AGENTS.md`、`memory-affect-01/README.md`、`00c`、`00b`、`04-affect-appraisal.md`、`05-memory-affect-coupling.md`、`guyuan/docs/tasks/task-14-affect-25dim.md`、`memo/Nocturne-Memory-Core.md`。
> 证据上限：**E3**。本报告未安装、未运行任何候选项目；E3 结论均为**转引** 01/02/04/05 已做的源码核查（本报告未重新核读源码）。
> 禁令遵守声明：未写产品代码；未改 01/02 报告；未改 00b/00c/README；未在 `memory-affect-02/` 下新建除本文件外的任何文件或目录；未把 Drive 塞回 Affect；未把 Emotion label 当情绪系统；未把 Desire 当计划、Intention 当 Task；未让 Motivation 绕过 Cognition/Permission；未把旧 25 维当验收标准。

---

## 1. 本专题真正要解决的问题

新一轮已经接受「Affect ≠ Motivation」「Drive ≠ Desire ≠ Intention ≠ Task」两套系统分开。**本专题只解决一个真正的问题：两套系统分开以后，箭头到底怎么连。**

把它拆成五个必须同时成立的子问题：

1. **方向**：每条耦合箭头从哪来、到哪去，是单向、双向还是并行？哪一端先变？
2. **确定性**：哪条箭头可由数值/规则确定性算，哪条只能由模型给出 appraisal 候选？
3. **权威**：哪条箭头允许写长驻状态，哪条只能改运行态（snapshot/derived），哪条只能提供 bias？
4. **阻尼**：耦合必然形成回路（情绪↔心境、欲望↔受阻），每条回环靠什么避免自激放大？
5. **可表达**：两套系统会互相矛盾（想做却没心情；心情好却不想动），表示层怎样不把它们压成一个标量。

**本专题不解决**：不选最终数值轴集/阈值（01/02 各自处理）；不决定 Chord 具体映射（04）；不决定每轮算/落盘/事件化规则（05）；不重开 Evidence/provenance/双时间（沿用 memory-affect-01）；不替用户判断故渊「应该」被什么推动。

**本报告发现并显式指出的上游不一致（只指出，未改其文件）**：
- 01 把 **D（dominance）判为「半属 appraisal」**、把 frustration 归 Affect；02 把 frustration 归 Affect/Appraisal、把「受阻」当情绪侧结果——两报告一致，但**都没说清「D 到底在耦合里当 Affect 偏置还是当 appraisal/coping 输入」**，本报告在 §4.3、§7.2 给出分叉。
- 01 说 **Attitude 移出 Affect 运行时**、02 说 attachment 属 Relationship/Attitude/Drive——两者在「关系域」边界上有重叠地带（attachment 的归属、想念的归属），本报告 §2.4-Q9 明确拆分。
- 01/02 都给了 Action Tendency 一个位置，但**01 把它当 Affect 输出、02 把它当 Affect×Motivation 接口**——本报告采用 02 的口径，并说明这是一条**跨模块边**而非某模块内部状态。

---

## 2. 必要术语与边界

### 2.1 本专题新增/关键术语（含来源）

| 术语 | 定义（本报告口径） | 关键来源 |
|---|---|---|
| **Appraisal 变量** | 事件「对我的目标/标准/偏好意味着什么」的结构化判断（goal_relevance / desirability / goal_conduciveness / expectedness / agency / coping / norm / novelty…） | OCC 1988；Scherer CPM（E2） |
| **Action Tendency 行动倾向** | 由情绪触发的短时、有方向、无承诺的就绪态（趋近/回避/攻击/僵住/停摆…）：**是情绪的构成成分**，不是情绪的影响结果 | Frijda 1986；Fontaine & Scherer 2013（E2） |
| **Motivational Intensity 动机强度** | 「朝/离某刺激移动的冲动大小」，是 affect 的一维，与 valence 可分（正面高动机强度如 desire 会**收窄**认知范围，低动机强度如 contentment 会**拓宽**） | Gable & Harmon-Jones 2008/2010；Harmon-Jones, Gable & Price 2013（E2） |
| **affect-as-information 情感即信息** | 把当前感觉当作对判断对象的信息（「我感觉如何？」），来源被识别/折扣后效应消失 | Schwarz & Clore 1983（E2） |
| **appraisal tendency 评价倾向** | 某情绪被激活后，留下与该情绪核心评价维度一致的、跨情境延续的认知/动机倾向（恐惧→不确定性/风险放大；愤怒→确定性/控制） | Lerner & Keltner 2000/2001（E2） |
| **wanting / liking 分离** | 「想要」（incentive salience，动机）与「喜欢」（pleasure，享乐）在机制上可分离 | Berridge & Robinson 1993/2016（E2） |
| **motivational priming 动机启动** | 情绪建立在防御/食欲两套动机系统上；同一系统内启动促进、跨系统抑制 | Bradley & Lang 2001/2007（E2） |
| **attachment 依恋** | 一种**行为系统/情感联结**（威胁时寻求接近），非情绪；其个体差异表现为焦虑/回避两维，是**情感调节理论** | Bowlby 1969/1982；Mikulincer, Shaver & Pereg 2003（E2） |
| **longing 想念** | **对象化的 Desire**（target = 某人），由分离 × 联结需要触发，可满足/缓解；非长期基线维度 | 02 专题；Nocturne（E1） |

### 2.2 耦合必须遵守的六条边界

1. **因果边 ≠ 相关边**：情绪与动机共变（如正面情绪常伴趋近）不代表一条因果边。要显式标「定义性成分 / 因果影响 / 仅相关」。
2. **定义性 ≠ 影响性**：Action Tendency 是情绪的**构成成分**（Frijda），不是「情绪影响出来的另一个量」；而「心境偏置欲望强度」是**影响边**。两者混为一谈会让回路分析失效。
3. **bias ≠ gate**：耦合边默认只改**强度/顺序/优先级**，不设「有无」硬门。任何让情绪/心境决定某记忆/欲望**是否成立**的边，都属越权（与 05「情绪只进排序层不进相关性硬门」一致）。
4. **运行态 ≠ 长期写**：每轮耦合算出的 Δ 默认只留运行态；只有跨阈/持续/被主体认领才形成长期对象（沿用 01 Q7、05）。
5. **确定性 vs 模型**：能由数值/规则算的（衰减、聚合、偏置、AT 查表）与只能由模型给候选的（appraisal 语义本身）必须分开，且模型只产候选（沿用 AGENTS「模型优先产候选」）。
6. **凡回环必须有阻尼**：情绪↔心境、欲望↔受阻、召回↔显著度三类回路都存在正反馈风险，必须有基线/半衰期/饱和/冷却/上限中的至少两项。

### 2.3 必答 10 问逐条回答

> 下列 10 条即任务卡 §6「必须回答」。先给结论，证据见 §3/§4/§11。

**Q1. Event 是先改变 Drive，还是先 Appraisal → Emotion，还是并行？**
**结论：在事件摄入层是「并行 + 共享 relevance 预读」，不是严格先后；而且「先改哪个」取决于事件类型。**
- 理由：一个事件同时被两条线读取，读的是**同一事件 × 不同参照**：Affect 线读「对目标/标准/态度意味着什么」，Motivation 线读「对某个 deficit 的激励值与机会（incentive）是多少」。二者都先需要一个**共享的 relevance 预读**（这个事件触及了哪些 goal/need/concern/attitude）——这正是 OCC/CPM 里 relevance 属**最先跑**的评价类（Scherer CPM relevance SEC 最先，E2）。
- 事件类型决定方向：**世界事件**（读到的消息、别人的行动）先走 appraisal → emotion，同时可能改某个 Drive 的 incentive；**自身状态事件**（累、满足）先体现在 Body/Drive（deficit 变化），随后**经 appraisal** 才生成情绪（满足→joy、受阻→frustration）。所以不存在「永远 Drive 先」或「永远 Emotion 先」。
- 确定性与权威：Drive/Desire 重算可由数值确定性做；appraisal 语义只能由模型产候选。二者在同一事件上并行，再在下游用有界回边互相偏置（§9 模型 B）。
- 反面：若强制「Drive 先 → 再 appraisal → 再 emotion」，会把「同一 deficit 下对不同对象欲望不同」压成单线（incentive 理论证过必须保留外部激励项，02 §4.2，E0/E1）。

**Q2. Drive / Goal / Value 是否应该进入 Appraisal 输入？**
**是的，必须进入——它们正是 appraisal 的参照物（referent），不是 appraisal 的输出。**
- 证据：OCC 的情绪 = 对事件/行动/客体以 **goals / standards / tastes(attitudes)** 评价（E2）；Scherer CPM 的 relevance=goal relevance、normative significance=internal/external standards / self-ideal（E2）；Lazarus 的 primary appraisal=goal relevance/congruence、secondary=coping（E2）。没有 goal/value 作参照，appraisal 无法算 relevance/desirability。
- 输入而非输出：Drive/Goal/Value/Concern/Attitude 作为**只读参照**喂入 appraisal；appraisal 输出必须带 `referent_ref`（用了哪个 goal/standard/attitude）与 `cause_ref`（锚到事件），两者一起构成 `appraisal_basis`（沿用 01 Q2、05）。
- 反向也存在：**长期 Desire/Drive 会抬高某类事件的 relevance**（00c §7.3、02 §2.2-答 12）——这是一条 **Motivation → Appraisal** 的有界边，不是让 Drive 决定情绪，而是让某类事件「更可能被评价为相关」。需带饱和（避免越关注越关注）。
- 边界：Value/Principle 同时是**闸门/过滤器**（02 §2.2-答 9），即既进 appraisal 作 standard，又在 Motivation→Intention 处过滤候选。

**Q3. Emotion 能影响哪些东西？**
逐通道给结论（bias 还是定义性、确定性还是模型，见 §7.2 表）：
- **attention**：**能，但只是偏置**。高**动机强度**（desire/enthusiasm/disgust/fear）**收窄**注意与认知范围，低动机强度（joy/sadness/contentment）**拓宽**（Gable & Harmon-Jones 2008/2010，E2）；affect-as-information 进一步给出「正性→启发式/依赖内部，负性→系统式/外部信息」的处理风格（Schwarz & Clore，E2）。故渊落点：情绪只改**注意预算/排序/广度**，不改「什么算相关」。
- **retrieval**：**能，但只在排序层做软信号**。心境一致记忆（Bower 1981）稳健性有限（Matt 等 1992 meta 显示正常非抑郁者反而是**正向偏好**，心境一致主要在临床/诱导组显著，E2）；05 已定「情绪进排序层，不进相关性硬门」，本报告沿用。
- **desire strength**：**能，乘性有界偏置**。affect-as-information + appraisal tendency 会放大/缩小欲望强度；Berridge 的 wanting/liking 分离说明**欲望强度是另一个量**，情绪只能调制它、不能等于它（E2）。落点：`desire.strength' = base × (1 ± k·affect_bias)`，clamp。
- **action tendency**：**这不是「影响」，而是「定义性成分」**。Frijda：情绪 = 行动就绪态的改变，action tendency 是情绪的构成成分（E2）；Steunebrink 等 2009 把「负性情绪 → 能降低该情绪强度的行动集合」形式化，并按 gain 排序（E2）。落点：AT 由情绪**确定性映射**（查表）或模型给出方向，二者都要保留 `source_emotion_ref`。
- **intention selection**：**不能直接选，只能经 Cognition 偏置**。appraisal tendency 会改变风险偏好/候选权重（ATF，E2），但 Intention 需要 deliberation + 主体认领（02 §4.5，E2/E3）。情绪至多提高候选优先级、供给 AT，**绝不许直接写 Intention/Task**。

**Q4. Drive 满足 / 受阻怎样改变 Emotion？**
**结论：不「直接」改，而是「作为事件的 goal_conduciveness 被 appraisal → 生成情绪」；Drive 水平本身只调制情绪强度。**
- 满足：目标达成 / 需要满足 → 奖励（homeostatic RL 里 reward = drive 削减量，Keramati & Gutkin 2014，E2）→ 经 appraisal（goal_conduciveness 正）生成正性情绪（OCC 的 Joy/Satisfaction）。注意 Berridge：膳食/cookie 的「喜欢」与「想要」分离，**满足感是享乐侧（liking）**，不等同于 drive 变小本身（E2）。
- 受阻：目标受阻 → appraisal（goal obstruction + agency/blame）→ frustration/anger + 对抗性 AT。**frustration 属 Affect/Appraisal（02 已定）**，理由是它是对「受阻」的评价结果，不是 drive 维度（OCC/CPM goal-conduciveness，E2；frustration-aggression 的认知修订版：受阻只在被评价为**任意/不公/有意**时才稳定产生 anger，E2）。
- **Drive 水平调制情绪强度**：越 deprivated，同一相关事件的情绪越强（deprivation 放大激励值，Keramati 2014，E2；FAtiMA 里 mood 也反向放大同价情绪 potential，E3 转引）。落点：`emotion.intensity ← f(appraisal, drive_relevance)`，drive 只抬升**强度**不决定**有无**。
- 风险：drive 受阻 → frustration → 对抗 AT → 更强 pursuit → 更易受阻，是自激环（§8）。

**Q5. Mood 是否可以给 Desire / Action Tendency 加偏置？多大程度才不变成脚本？**
**可以，但必须是有界、饱和、加/乘性的偏置；一旦它变成「决定有无/决定选哪个」，就是脚本。**
- 证据：FAtiMA 的 mood 反向偏置后续同价情绪的潜在强度，默认系数 **0.3**（E3 转引 01 §4.5）；emotional_memory 的 `adaptive_weights()` 用心境做**权重调制器**（tanh/高斯平滑门），文档明写 *not a hard filter*（E3 转引 05 §4.1）；affect-as-information（mood 作判断信息，被识别来源后折扣，E2）；appraisal tendency（情绪特定、跨情境延续，E2）。
- 程度（工程判据）：
  1. **有界**：`|Δ| ≤ b` 或乘子 `∈ [1-b, 1+b]`，`b` 取 ~0.2–0.3；
  2. **饱和**：用 tanh/gaussian 而非线性，避免极端 mood 线性放大；
  3. **只改强度/顺序，不改成员资格**：不新增一个原本不存在的 Desire，不把某候选直接设成「必胜」；
  4. **必须与其他输入合流后再选择**：偏置后的 Desire/AT 仍需与 drive、world、values 一起进 Cognition；
  5. **脚本检测**：若把 mood 拉到极值后，无论如何都唯一选中同一行动 → 已脚本化（坏）。
- 落点：mood 对 Desire/AT 是**上游乘性偏置**；方向轴（趋近/回避）应由**情绪自身的 AT**（定义性）给，而不是只由 mood 给。

**Q6. 情绪与欲望的因果循环怎样防止自我放大？**
**结论：靠「负反馈基线 + 饱和 + 环路增益 < 1 + 外部独立信号 + 显式降温」四类约束的组合，而不是靠单一系数。**
- 三类已知自激形态（均有独立证据）：① 情绪→mood→偏置情绪→更强 mood（FAtiMA 用 0.3 + 向 baseline 回落缓解，E3 转引）；② 召回/自评自我强化（05 引 Echo Gap arXiv 2608.00017、neural howlround arXiv 2504.07992，E2 转引；emotional_memory 的 resonance Hebbian **单调无衰减**会饱和到 1.0，E3 转引）；③ 欲望受阻→frustration→更强 pursuit（本报告推断，依据 Q4）。
- 约束清单（至少取两项落每一条回环）：
  1. **负反馈参考**：状态是「对 baseline 的偏离」，有解析式回落（不是自由累积）；
  2. **饱和非线性**：tanh/clamp，使增益随偏离下降；
  3. **环路增益条件**：`g(emotion→mood) × g(mood→appraisal) × g(appraisal→emotion) < 1`，否则不收敛；
  4. **外部独立信号**：判断/评估须有一个与自身偏差**误差独立**的外部信号（Echo Gap 的「误差独立性」必要条件，E2 转引）——例如不拿 mood-congruence 当唯一裁判；
  5. **冷却/不应期**：同类回路有 refractory，防连续叠加；
  6. **显式降温**：reappraisal / let_go 作为**有成本的主动控制输入**（Gross 2015，E2；Nocturne REJECT/let_go，E1），而不是靠时钟。
- 反面：仅靠「给个 0.3 系数」不足够——系数只保证单步有界，保证不了多轮闭环（05 的 Echo Gap 正说明单点阻尼不够）。

**Q7. 「我很想做但心情很差」怎样自然表达？**
**结论：靠「Desire 强度通道高 + Core Affect valence 低」两个独立量直接表达，而不是压成一个标量。**
- 理论支撑：wanting ≠ liking（Berridge，E2）；approach motivation 与 positive affect **可分离**（Harmon-Jones 等：极端 approach 可与正性情感解离；anger = 负性价 + 趋近动机，E2）。所以「想做」（高 MOT）+「心情差」（低 valence）是可表示的合法组合。
- 表达机制：把 `desire.strength`（Motivation，由 deficit×incentive 现算）与 `core_affect.valence`（Affect）**分开存**；mood 对 desire 的偏置 `b` 必须小到**不足以抹掉** desire（即低 mood 只降低努力/增加重评，不删除欲望）。E-STEER（arXiv 2604.00005）报告：低 valence/低 dominance 状态下 agent **更频繁重规划**而非放弃计划（**E1 论文声称，未复现**）——可作为「心情差→更犹豫/更频繁重估，但欲望仍在」的工程旁证。
- 别把它当异常：这不是不一致，而是两套系统本来就允许不一致（pre-goal 高动机强度 + 负性情感）。

**Q8. 「心情很好但没有动力做事」怎样自然表达？**
**结论：靠「Core Affect pleasure 高 + Motivational Intensity/Drive 低」表达；正面情绪 ≠ 有动机。**
- 理论支撑：**motivational intensity 模型**——正性情感**低**在 approach 动机强度时（contentment、amusement）会**拓宽**认知且是**被动**的（Gable & Harmon-Jones，E2）；post-goal 满足是低 approach 正性情感（E2）。Frijda 的 AT 表里 contentment=inactivity（休整），joy=free activation（泛化就绪）——两者都是正性但动机强度不同（E2）。
- 表达机制：`core_affect.pleasure` 高、`drive/desire` 低、`action_tendency=低激活/停滞`。mood 的偏置**不得凭空制造 desire**（Q5 规则 3）；正面情绪至多**拓宽**注意/探索候选（broaden），但没有活跃 drive 时不会形成强 Desire。
- 别把它当异常：这正是「低动机强度正面情感」，也解释为什么「开心但闲」在人类里常见。

**Q9. attachment / longing 这种跨域现象，应该拆成哪些对象？**
**结论：拆成 6 类对象，分属 3 个模块，并用 attachment 取向当「耦合权重参数」。**
1. **Attachment 取向（Relationship/Attitude，慢，per-person）**：「情感联结」的焦虑/回避两维个体差异；不是情绪，是长期对象（Bowlby 1969；Mikulincer & Shaver，E2）。负责**调制**下面几条边的增益（见第 6 条）。
2. **Relatedness Need / 依恋系统激活（Motivation，慢-近 baseline，无 target）**：威胁/分离时被激活的**行为系统**（Bowlby 的 behavioral system，E2）；对应 02 的连接欲/相关需要，产出 Drive 强度。
3. **Longing 想念（Motivation，Desire，对象化，target=某人）**：分离 × 联结需要触发，强度波动、可被接触满足/缓解（02 §5；Nocturne Longing 参与 attachment 入账闸门，E1）。
4. **Emotion 片段（Affect，快，有 cause_ref+target）**：接触/分离时生成的具体情绪（喜悦、失落…），带 cause 与 appraisal_basis（01）。
5. **Affect Annotation（Memory，冻结）**：关系事件发生时的情绪快照，不被当前 mood 改写（05）。
6. **Concern（Continuity，天~周，开放状态）**：关系「还没闭合」时的持续关切，不等于 Desire（02 §2.2-答 8）。
- **耦合（关键）**：attachment 取向（慢对象）作为**参数**调制「威胁→依恋系统激活→longing 强度」「情绪调节策略选择」「情绪↔drive 回环增益」——Mikulincer：焦虑=**超激活**（放大 jealousy/anger 等情绪），回避=**去激活/压制**（E2）。所以 attachment 不进情绪生成本体，而是**耦合层的权重**。
- 旧方案把 attachment 列为「0.5h 半衰期的瞬时离散通道」（task-14）是**分类错误**（01 Q10、02 均已指出）。

**Q10. 哪些耦合应确定性计算，哪些只能由模型提供 appraisal 候选？**
**结论见下表。底线：appraisal 语义 = 模型候选；appraisal 之后的动力学与偏置 = 确定性。**

| 环节 | 谁做 | 理由/来源 |
|---|---|---|
| Event → 共享 relevance 预读（触及哪些 goal/need/concern） | **模型候选 + 确定性粗门**（实体/关键词匹配活跃 concern 作快门） | relevance 是语义判断；纯关键词门只能筛不能判 |
| appraisal 变量（goal_conduciveness / desirability / expectedness / agency / coping / novelty / norm） | **模型候选**（带 basis，锚事件+referent） | OCC/CPM 评价本质是语义（E2）；EMA 证明可非语言算但需显式因果图，故渊未必有（E2） |
| 情绪类型 + 强度（给定 appraisal 后） | **确定性映射/公式为主，模型命名候选为辅** | OCC/FAtiMA 评价→情绪是规则（E2/E3） |
| 情绪 → mood 更新、衰减、baseline 回落 | **确定性** | FAtiMA/emmem 均解析式（E3 转引） |
| mood/emotion → desire/AT 的有界偏置 | **确定性**（模型只给 appraisal） | 偏置是数值运算 |
| Action Tendency 方向映射（情绪→趋近/回避/…） | **确定性查表**（Frijda 表）或模型候选 | Steunebrink 2009 给出可形式化映射（E2） |
| Drive deficit×incentive 重算、satiation/refractory | **确定性**（incentive 的「显著度」可由模型候选） | 02 §3/§4.3（E2） |
| Desire 强度（wanting） | **确定性为主，模型只给对象候选** | Berridge wanting≠liking，不宜由模型从心情反推欲望（E2） |
| Intention 选择 | **Cognition/模型**（不可由情绪/驱动确定性决定） | BDI（E2/E3）；02 越级护栏 |
| 写长期 Affect Change Event / Concern | **规则触发（阈值/持续/认领）** | 01 Q7、05 |

---

## 3. 理论机制候选总表

> 证据：论文正文/官方文档 = E2；只读源码 = E3（本报告**转引**，未重核）；README/摘要 = E1。年份为原文年份。

| 候选 | 来源 | 等级 | 核心机制 | 对本专题（耦合）的可用性 |
|---|---|---|---|---|
| **OCC** | Ortony, Clore & Collins 1988 | E2 | 事件→goals、行动→standards、客体→attitudes；goal_congruence 生成 joy/distress | 高：**goal 进 appraisal** 的三入口，正答 Q2 |
| **Scherer CPM** | Scherer 2001/2009 | E2 | 序贯 SEC：relevance（最先）→implication→coping→normative | 高：给出**事件→情绪的最小输入顺序**，支撑 Q1「共享 relevance 预读」 |
| **Frijda action tendency** | Frijda 1986 | E2 | 情绪=行动就绪态改变；AT 是情绪**构成成分**；列 16/17 种关系性 AT | 高：Q3 的「定义性 vs 影响性」关键证据 |
| **Action tendency 形式化** | Steunebrink, Dastani & Meyer 2009（EPIA09） | E2 | 负性情绪→可降低其强度的行动集合；按 gain 排序 | 高：给「情绪→AT→候选排序」一个可算范式（不引其逻辑栈） |
| **Motivational intensity 模型** | Gable & Harmon-Jones 2008/2010；Harmon-Jones, Gable & Price 2013 | E2 | 高动机强度收窄认知范围、低动机强度拓宽；valence 与动机强度可分 | 高：Q3 attention、Q8「心情好但没动力」的直接依据 |
| **Motivational intensity theory** | Brehm & Self 1989；Gendolla 综述 2025 | E2 | 努力随难度升高，直到「不可能」或「不值得」；affect 通过告知**任务需求**影响投入 | 高：effort 上界机制，防「情绪→无限努力」 |
| **affect-as-information** | Schwarz & Clore 1983 | E2 | 感觉被当作判断信息；来源被识别则折扣 | 高：mood 作软信息的边界（Q5） |
| **appraisal-tendency framework** | Lerner & Keltner 2000/2001 | E2 | 情绪留下与核心评价维度一致的跨情境倾向；同价性情绪影响相反（fear vs anger） | 高：**Emotion→Appraisal/判断**的边；说明「按离散情绪而非价性」 |
| **wanting/liking** | Berridge & Robinson 1993/2016 | E2 | incentive salience（想）= 动机系统；pleasure（喜欢）= 享乐小系统；可解离 | 高：**Q7「想做但心情差」**、Desire 强度不由情绪反推 |
| **motivational priming** | Bradley & Lang 2001/2007 | E2 | 情绪建立在食欲/防御两套动机系统；同系统促进、跨系统抑制 | 高：AT 的方向轴 + 情绪→注意/唤醒；跨系统抑制=天然阻尼 |
| **attachment/affect regulation** | Bowlby 1969/1982；Mikulincer, Shaver & Pereg 2003 | E2 | 依恋是行为系统；焦虑=超激活、回避=去激活，调制情绪调节 | 高：Q9 的拆分与「取向作耦合参数」 |
| **emotion regulation（过程模型）** | Gross 1998/2015；"Emotion Regulation Is Motivated" | E2 | 前因聚焦（情境/注意/认知改变=reappraisal）与反应聚焦（suppression）；调节本身是被动机驱动的 | 高：emo→motivation 的**主动降温**通道（Q6） |
| **interest/curiosity 评价结构** | Silvia 2005/2008；Loewenstein 1994 | E2 | interest = 新颖性 × 应对潜能（可理解性）；好奇=信息缺口 deprivation | 高：curiosity 的 Drive/Affect 双重定位（§5、§4.6） |
| **FAtiMA Toolkit** | GAIPS-INESC-ID/FAtiMA-Toolkit | **E3**（转引 01/04） | ActiveEmotion（cause/appraisal/decay）；mood 单标量；**mood↔emotion 双向偏置 0.3**；重评替换 | 高：**情绪↔心境回边**的可运行结构与系数 |
| **EMA** | Marsella & Gratch 2009 | E2 | 因果图→评价变量；**coping 反作用于信念/愿望/意图** | 高：appraisal↔motivation 的工程范式（coping 是动机反作用） |
| **WASABI** | Becker-Asano 2008/2009 | E2 | core affect 连续演化→后分类；mood-congruent 唤起 | 中高：连续底色 + 心境一致 |
| **emotional_memory** | gianlucamazza/emotional-memory | **E3**（转引 05） | 六信号召回；`adaptive_weights()` 心境作**权重调制非过滤**；唤醒调制遗忘；resonance Hebbian（单调、会饱和——反面） | 高：Q3 retrieval、Q6 自激的反面教材 |
| **LLM agent 情绪→行为** | E-STEER arXiv 2604.00005；arXiv 2510.13195；arXiv 2507.22326 | E1（论文声称） | 情绪调制多步 agent 的计划/注意/行动选择；valence/dominance 低→更频繁重规划；情绪→desire→objective | 中：**当前 LLM agent 的落地旁证**，未复现、未核代码 |
| **Echo Gap / howlround** | arXiv 2608.00017；arXiv 2504.07992（转引 05） | E2 | 自评/递归强化会乘性复利；纠偏需**误差独立**的外部信号 | 高：Q6 阻尼的硬约束 |

> 共 21 项，覆盖「评价理论 / 动机与情绪神经/心理 / 情绪调节 / 工程实现 / 近年 LLM agent」五类来源，满足「至少两类不同来源」。代码候选关键工程结论均标 E3（转引）。

---

## 4. 重点候选深查

### 4.1 OCC + CPM：goal 如何进入情绪（E2）
- OCC：情绪由对**事件**（以 goals 评价）、**行动**（以 standards）、**客体**（以 attitudes）的价性反应生成；`goal_congruence` 直接决定 joy/distress（E2）。→ 直接回答 Q2：**Goal（及其派生的 Drive/Desire）是 appraisal 的必要参照**。
- CPM：序贯 SEC 中 **relevance 类最先**（novelty / intrinsic pleasantness / **goal relevance**），implication 类的 **goal conduciveness** 决定情绪；后类递归修正前类（E2）。→ 支撑 Q1 的「共享 relevance 预读」与 Q4 的「受阻经 goal_conduciveness 转成情绪」。
- 局限：两者都不规定衰减/累积，也不规定耦合回边——回边由实现自定（故渊需自补，§9）。

### 4.2 Frijda action tendency + Steunebrink 形式化（E2）
- Frijda：情绪的核心是**行动就绪态的改变**；AT 是情绪的**构成成分**（不是后果）；列了 approach/avoidance/being-with/attending/agonistic/inhibition/inactivity 等（E2；经 Fontaine & Scherer 2013 GRID 复现为「防御 vs 食欲 + 脱离 vs 干预 + 屈服 vs 攻击」三因子，E2）。
- Steunebrink 等 2009：把「负性情绪 → 能**降低该情绪强度**的可执行行动」形式化为 AT，并按「预期情绪强度下降的 gain」排序（E2）。→ 这给了故渊一条**可算**的「情绪→AT→候选排序」范式（不必引其逻辑栈）。
- 对故渊的关键区分：**Emotion → Action Tendency = 定义性边**（应由情绪类型确定性映射或模型给方向 + 保留 source_emotion_ref）；而 **Action Tendency → Desire 强度/优先级 = 影响边**（可被 mood/appraisal 偏置）。两者不能混。

### 4.3 motivational intensity（E2）——注意、努力与「心情好却没动力」
- 定义：motivational intensity = 「朝/离刺激移动的冲动大小」，是 affect 的一维；与 valence 可分、与 arousal 可分（Gable & Harmon-Jones，E2）。
- 效应：**高**动机强度（desire、enthusiasm、disgust、fear）**收窄**注意与认知范围；**低**动机强度（joy、sadness、contentment）**拓宽**（Gable & Harmon-Jones 2008；Gable & Harmon-Jones 2010「blues broaden, nasty narrows」；Harmon-Jones, Gable & Price 2013，E2）。**这推翻了「正性拓宽、负性收窄」的简单说**（E2）。
- Brehm 动机强度理论：努力随**难度**升高，直到「不可能」或「不值得」（potential motivation 上限）；affect 主要**通过告知任务需求**影响投入（Gendolla 2025 综述，E2）。
- 对故渊：① Q3 attention 的机制；② Q8「心情好但没动力」= 低动机强度正性情感（contentment），可表达；③ 防「情绪→无限努力」的上界机制（effort cap）。

### 4.4 affect-as-information + appraisal tendency（E2）——情绪如何回流成判断偏置
- affect-as-information：人把当前感觉当判断信息；**当感觉来源被识别为无关时效应消失**（Schwarz & Clore 1983，天气-生活满意度实验；经多次复制，E2）。→ 故渊含义：mood 作软信息可行，但**必须能被「归因到无关来源」而折扣**；纯 mood 直连 Action 是缺了折扣机制。
- appraisal tendency：离散情绪（即便同价性）留下方向不同的跨情境倾向；**fear→放大不确定/风险，anger→放大确定/控制**；carryover 有**匹配约束**（只影响与该情绪评价维度匹配的判断域）（Lerner & Keltner 2000/2001，E2）。→ 故渊含义：**情绪→appraisal 的偏置要按离散情绪类型而非价性**；且是**域匹配**的（不是全局污染）。
- 局限：两项都是人类实验结论，落到 agent 需自建折扣/匹配层（故渊缺口）。

### 4.5 wanting / liking（E2）——Desire 与 Affect 为什么必须分存
- incentive salience（想）由中脑多巴胺等大系统介导；pleasure（喜欢）由受限的享乐热点介导；二者可解离（Berridge & Robinson，E2）。
- 对故渊：**Desire 强度不应由情绪（liking）反推**；两套系统分存才使 Q7/Q8 两类「矛盾」可表达。这条也是 Q5「情绪只能调制欲望、不能等于欲望」的根据。

### 4.6 attachment / longing（E2 + 项目 E1）——跨域拆分
- Bowlby：依恋是**行为系统**（威胁时寻求接近），不是情绪；成年后被推广到恋爱/社会关系（E2）。Mikulincer & Shaver：依恋理论即**情感调节理论**；焦虑=系统**超激活**（情绪放大、反刍），回避=**去激活**（压制、脱离）（E2）。
- 对故渊：attachment 取向是**慢对象/参数**，调制「威胁→依恋系统激活→longing」与「情绪↔drive 回环增益」；longing 是**对象化 Desire**（target=人）；具体情绪片段另属 Affect；关系事件的情绪快照进 Affect Annotation；关系未闭合进 Concern（详见 §2.4-Q9）。
- 旧错误：把 attachment 当 0.5h 瞬时情绪通道（task-14）已被 01/02 判为分类错误。

### 4.7 emotion regulation 对 motivation 的影响（E2）
- Gross 过程模型：五族策略（情境选择/修正、注意部署、认知改变=reappraisal、反应调整=suppression）；前因聚焦策略更早、更便宜（E2）。
- "Emotion Regulation Is Motivated"：**是否调节、调节到何目标**本身是被动机驱动的（hedonic vs instrumental）；调节目标可与人当下的情绪相反（counterhedonic）（E2）。
- 对故渊：emo→motivation 有一条**主动降温/调节边**（reappraisal/let_go），它是「欲望/情绪回环」的可控泄压阀，且必须**有动机来源**（不能无条件自动降）。不建议 suppression 式假装（与 provenance 冲突，沿 01/04）。

### 4.8 agent 侧实现（E3 转引 + E1 近年）
- **FAtiMA（E3 转引 01/04）**：`ActiveEmotion` 带 CauseId/AppraisalVariables；mood 单标量 `[-10,10]`；`UpdateMood: mood += valence·I·0.3`；反向 `potential += valence·mood·0.3`（**情绪↔心境双向弱耦合的现成结构与系数**）；同 cause 重评=替换。
- **EMA（E2 转引 01/04）**：因果图算评价变量；**coping 反作用于信念/愿望/意图**——即 **Motivation→Appraisal** 的工程范式（coping 是动机对评价的反作用）。
- **emotional_memory（E3 转引 05）**：`adaptive_weights()` 用 tanh/高斯门把心境当**权重调制器**（明写 *not a hard filter*）——Q3/Q5 的现成算子；`resonance` Hebbian 单调无衰减会饱和——Q6 反面。
- **E-STEER arXiv 2604.00005（E1，论文声称，未复现）**：情绪调制 LLM/agent 的单步与多步行为；**低 valence/低 dominance 状态下 agent 更频繁重规划、更不信任初始计划**；情绪影响在决策链上累积。→ 与 Q7/Q8 直觉一致，但**数字/结论未经故渊验证**。
- **arXiv 2510.13195（E1）**：情绪状态触发 desire 生成与 objective 更新，构成「state→desire→objective→decision→action」闭环——与 00c §7.2 候选链同构，但**未核代码**。

---

## 5. 旧故渊方案逐项复核（耦合相关）

> 对象：旧「25 维三层」（驱力 10 + PADCN 5 + 离散 10）。耦合层证据来自 task-14 的映射表与 01 §5 对旧代码 `core/affect/*`、`config/affect.yaml` 的 E3 转引。

| 旧耦合假设 | 出处 | 复核判断 |
|---|---|---|
| **「驱力超阈值直接驱动行为，独立于情绪」** | task-14 §关键机制 | **错误且违规**。这正是一条「Drive 绕过 Cognition 直接执行」的越权边；且「独立于情绪」与耦合事实相反（受阻→frustration 需经 appraisal）。应删除（归 02/05）。 |
| **「驱力满足度 → 情感轴」（确定性直连）** | task-14 三层映射图 | **方向可取、机制过直**。满足→正性情感应**经 appraisal(goal_conduciveness)** 生成，而非直接抄到 PADCN 轴；否则 Drive 与 Affect 在耦合层又混装。 |
| **「情感轴 → 离散通道激活」（确定性直连）** | task-14 三层映射图 | **原则上可保留但降级**。core affect→离散标签是**视图映射**（WASABI 顺序，E2），不是情绪生成；不能当「为何产生」的本体。 |
| **离散通道互锁（共激活/抑制）** | `config/affect.yaml` interlocks | **不得当生成层**（01 §5.4 已判）。作为**表达层冲突规则**可留候选；作为耦合/生成会把「为何产生」掩盖。 |
| **PADCN 稳态回弹（向 0 指数回归）** | 旧代码 | **保留**（这是阻尼，不是耦合本体）；但**不能推广到 attachment/attitude/longing**（01 Q6）。 |
| **意义权重乘数（note×2.5/reflection×4/mention×1.8/let_go×0.5）** | task-14 §Q43 | **思想可取**：把「主体主动留下/放下」接进情绪动力学 = 一条 **Cognition/Agency → Affect 动力学**的有界边（等价于延长有效半衰期 / 显式降温）。应并入 Affect Change Event / salience，不是 core affect 参数。 |
| **Chord 从全部 25 维（含驱力）确定性映射** | task-14 / chord.py | **耦合层混装**：若驱力色彩进入 Chord，等于让 Motivation 借表达层回流 Affect。Chord 应只做 **Affect 表达层**（01 Q8/§9，归 04）。 |
| **mood↔emotion 双向偏置** | 旧代码沿 FAtiMA 谱系（01 §4.5） | **保留**：这是需保留的正向耦合回边；须配 ≤0.3 系数 + baseline + 饱和（Q6）。 |
| **「人格调性 key（月/周）」把 trait 与 state 混层** | task-14 | **错误**：trait 归 Self/参数，drive-state 归 Motivation 现算（02 §5）。耦合层因此不能拿「驱力层」当慢 reference。 |

**总评**：旧方案的耦合错误集中在**两点**——(1) 存在一条 Drive 越权直连行动的边；(2) 在耦合/表达层把 Affect 与 Motivation 重新混装（满足度直连情感轴、驱力色彩进 Chord）。可保留的耦合资产只有：mood↔emotion 双向弱耦合、稳态回弹、意义权重（认领→降温）。

---

## 6. 对新架构的可用性拆分

| 拆分 | 内容 | 来源/等级 |
|---|---|---|
| **可直接复用（机制/算法，非整库）** | ① 情绪↔心境双向弱耦合系数式（mood += Σval·I·k；potential += val·mood·k，k≈0.3）；② 情绪→AT 的确定性映射表（Frijda 表/GRID 三因子）；③ 心境作**权重调制器**的 tanh/高斯门；④ Brehm 努力上界（effort ∝ min(难度, 值得)） | FAtiMA E3、Frijda/Fontaine E2、emmem E3、Brehm E2 |
| **可借鉴机制** | ① OCC/CPM 的 goal 作 appraisal 参照；② Steunebrink 的「负性情绪→降低其强度的行动→按 gain 排序」；③ appraisal-tendency 的**域匹配 + 按离散情绪**偏置；④ affect-as-information 的**归因折扣**；⑤ Mikulincer 的依恋取向作**回环增益参数**；⑥ Gross 的 reappraisal/let_go 作主动降温；⑦ EMA 的 coping 作 Motivation→Appraisal 反作用 | E2 |
| **故渊需补（缺口）** | ① 显式 appraisal 变量 schema（CPM/OCC 落地）；② 「共享 relevance 预读」的确定性粗门 + 模型精判双层；③ 环路增益 ≥1 的检测与限速（冷却/上限）；④ 「想做但心情差 / 心情好但没动力」两态的最小表示；⑤ attachment 取向→耦合权重的映射；⑥ 按离散情绪的域匹配偏置表 | 综合 |
| **不建议采用** | ① Drive 超阈直连行动；② 情绪/心境作召回或欲望的**硬门**；③ 满足度直连情感轴（跳过 appraisal）；④ 驱力色彩进 Chord；⑤ 单一标量同时表示「想不想」与「感觉好不好」；⑥ 无条件自动情绪调节；⑦ 把 attachment 当瞬时情绪通道 | 综合 |

---

## 7. 跨模块接口

### 7.1 接口候选（输入 / 输出 / 信号）

**耦合层输入（只读，来自各模块）**
- `event`：主体/动作/对象/时间 + 视角（01/02）。
- **`referent_set`（本专题新增，核心）**：当前活跃的 goal / standard·norm / value·principle / concern / attitude[object] / need·drive 的**只读引用**（来自 Self/Cognition/Motivation/06）。它是 appraisal 的参照，也是「共享 relevance 预读」的输入。
- `prior_affect`：core affect 快照 + mood + 相关对象的 emotion 集合（Affect）。
- `prior_motivation`：drive_state（derived）+ 活跃 desire 候选（Motivation）。
- `relationship_weights`：对相关对象的 attachment 取向（慢对象，来自 Relationship/Attitude）。

**耦合层输出（候选/信号，不直接执行）**
- `appraisal_candidate`：`{axis: goal_relevance|goal_conduciveness|desirability|expectedness|agency|coping|novelty|norm, value, cause_ref, referent_ref, basis, confidence}`——**模型产出，锚事件+referent**。
- `emotion_candidate`：`{type, intensity, valence, target?, cause_ref, appraisal_basis}`（Affect 落账）。
- `action_tendency`：`{direction(approach/avoid/attend/reject/agonistic/inhibit…), intensity, source_emotion_ref}`——**snapshot，只 bias**。
- `drive_bias_signal`：`{drive_id, delta, source_emotion_ref|source_appraisal_ref}`——事件/情绪对 Drive 的**有界**影响。
- `desire_bias_signal`：`{desire_ref|class, factor(1±b), source_affect_ref}`——有界乘性偏置。
- `motivation_gate_hint`：Motivation→Appraisal 的**relevance 抬升建议**（带饱和，只提相关性不决定结果）。

**对外信号**：`affect_motivation_coupled(delta, basis)`——供 05 订阅落盘决策；`coupling_loop_health`（环路增益/冷却状态）供 05 监控。

**硬边界（护栏）**
- 耦合层**只能产 bias/候选**，不得直接写 Intention/Task/Wake，不得越权改 Commitment/Fact/Value。
- 情绪/心境**只进排序层/强度层**，不得作召回或欲望成员的硬门。
- 模型只写 `appraisal_candidate`/`emotion_candidate`（带 basis）；**mood 值、长期 attachment、drive 值由确定性层写**。

### 7.2 横向必答表（每个耦合变量）

| 变量 | 类别 | cause/target | 时间尺度 | snapshot/ledger/derived | provenance? | 影响方式 | 模型可写? | 衰减/失效 | 自我强化环? | 与 Self/Concern/Task/Wake 接口 |
|---|---|---|---|---|---|---|---|---|---|---|
| **Appraisal 变量** | Cognition（Affect 输入，含 Motivational 参照） | target=事件；referent=goal/standard/attitude | 瞬时 | ledger（可重算） | **是**（锚 event+referent） | 只作生成/偏置输入 | 是（**仅候选**） | 不衰减（证据链） | 否 | → Affect/Motivation |
| **Emotion 片段** | Affect | 有 cause_ref+可 target | 秒~分（可数日） | ledger | 是 | 定义性产 AT + 软偏置 | 是（类型/依据候选） | 逐类型半衰期 + 重评/解决 | 是（→mood→偏置情绪），需有界 | → 05/03 |
| **Mood** | Affect | 无单一 cause | 时~天 | snapshot/derived | 否 | **软偏置（≤0.3，饱和）** | 否（确定性） | 慢向 baseline 回落 | 是（与情绪双向），需有界 | 注入 05/07 |
| **Core Affect (V/A,±D)** | Affect | 无对象 | 分~天 | snapshot | 否 | 软偏置/底色 | 否（确定性） | 向 baseline 解析回落 | 弱（经 mood） | 注入 05/07 |
| **Action Tendency** | **Affect×Motivation 接口** | cause=emotion；无承诺 | 秒~分 | snapshot | 弱（source_emotion_ref） | **只 bias**，产候选 | 是（方向候选） | 随情绪回落 | 中（→desire→更强 AT） | → Cognition 选择 |
| **Drive（强度）** | Motivation | cause=deficit×incentive；无 target | 分~时 | derived（可重算） | 是 | bias-only | 否（确定性；incentive 可候选） | 解析回落 + satiation/refractory | **中高** | → Desire；接 Self |
| **Desire（强度/状态）** | Motivation | cause=Drive×对象；**target 必需** | 分~时（对象在场） | ledger（状态机） | 是 | bias-only，不直接执行 | 只产候选 | 满足/放弃/搁置 | 中 | → Concern / → Cognition→Intention |
| **frustration（受阻情绪）** | **Affect**（不是 Drive） | cause=goal obstruction+agency（appraisal） | 秒~时 | ledger（情绪，同 Emotion） | 是 | 经 AT 影响候选 | 是（appraisal 候选） | 同情绪半衰期 + 解决/重评 | 是（→更强 pursuit），需有界 | → 03/05 |
| **curiosity（好奇）** | **Drive + 对象化 Desire** | cause=信息缺口（Loewenstein）；target=缺口 | 分~时 | derived/ledger | 是 | bias-only；产探索候选 | 是（缺口候选） | 缺口闭合即满足 | 中 | → Retrieval/Exploration；可作 Wake 软信号 |
| **motivational intensity** | Affect 的一维（可派生） | 由情绪/驱动派生 | 瞬时 | derived view | 否 | 调注意广度 + effort 上界 | 否（派生） | 随情绪回落 | 否 | → attention/effort（05/07） |
| **attachment（取向）** | **Relationship/Attitude** | target=人；无单一 cause | 周~月 | ledger/derived view | 是（证据累积） | **耦合权重参数**（bias） | 候选（高门槛） | 按证据更新，不按时间衰减 | 弱 | → 06；调制耦合增益 |
| **longing（想念）** | Motivation（对象化 Desire） | target=某人；cause=分离×联结需要 | 分~天 | derived/ledger | 是 | bias-only | 是（候选） | 接触满足/时间回落 | 中 | → 03/关系模型；Wake 软信号 |
| **relevance（共享预读）** | Cognition↔Motivation 边 | target=事件；referent=concern 集 | 瞬时 | derived（每轮算） | 是（用到哪些 referent） | 只决定「进不进 appraisal」 | 是（+确定性粗门） | 不衰减（每轮重算） | 否（有饱和） | → Affect/Motivation 分流 |

---

## 8. 失败模式与风险

1. **情绪↔心境自激放大（核心风险）**：emotion→mood→偏置 appraisal→更强 emotion 的正反馈。缓解：有界标量 + 半衰期 + 饱和非线性 + 环路增益 <1 + 冷却（Q6）。
2. **欲望受阻的正反馈**：drive 受阻→frustration→更强 pursuit→更易受阻。缓解：frustration 归 Affect、pursuit 需经 Cognition、限速/冷却、外部进度信号。
3. **脚本化漂移**：mood/emotion 的偏置越滚越大，最终无论其他输入都选同一行动。缓解：Q5 的五条判据 + 「脚本检测」压测（把 mood 拉极值看是否唯一解）。
4. **把 bias 变 gate**：情绪/心境让某记忆或某欲望「不被承认」。缓解：硬边界——情绪只进排序/强度层（05 已定）。
5. **Drive 越权**：强 Drive 直接建 Task/发消息。缓解：BDI 式分层 + 独立登记步骤 + Permission（02 §4.5，E3）。
6. **模型幻觉欲望/原因**：LLM 自由写「我想要 X」「我因 Y 生气」。缓解：appraisal/emotion 只产候选、带 basis 锚事件+referent；drive/desire 强度不由模型从心情反推。
7. **wanting/liking 混同**：用情绪 valence 直接推欲望强度 → Q7/Q8 表达不出来。缓解：Desire 与 Affect 分存（Berridge）。
8. **frustration 归错层**：把「受阻」当 Drive 维度 → 与 Affect 重复。缓解：frustration 归 Affect/Appraisal（02 已定）。
9. **attachment 过度耦合**：把 slow 取向当轮态，或让单次强情绪改写长期态度。缓解：attachment 作**权重参数**，长期对象按证据更新、留版本。
10. **确定性/模型比例失衡**：把 appraisal 语义强行确定性化（僵）或把全部动力学交给模型（不可重算、漂移）。缓解：§2.4-Q10 分工表。
11. **成本**：appraisal 需模型调用；耦合回边若每轮全算会抬成本。缓解：appraisal/情绪标注进批处理派生，确定性耦合在实时链路（沿 04/05）。
12. **未验证**：LLM agent 侧结论（E-STEER 等）全为论文声称，未复现；环路增益阈值的经验标定未做。

---

## 9. 推荐机制：A / B / C / D（三种信息流模型 + 一个反例）

> 给出 **3 个可比较的信息流模型**（不要求选定），并对每条箭头标注：**方向 / 确定性还是模型 / 是否可写状态 / 阻尼或限制**。D 为需避免的反例，用对照。
> 记法：`[确定性]`＝可由数值/规则算；`[模型]`＝只能由模型产候选；`(W)`＝允许写长驻状态；`(w)`＝只写运行态/候选；`⟳`＝该边带阻尼（基线/饱和/半衰期/冷却/上限之一）。

### 模型 A · 分层前馈主干 + 有界回边（保守，贴近 01/02 底）

```
主链（前馈）：
Event ─► [Appraisal 候选] ─► Emotion ─► Action Tendency ─┐
                                                          ▼
        Drive(derived) ─► Desire 候选 ─► [Cognition Deliberation] ─► Intention ─► Task

回边（全部有界）：
(a) Emotion ─► Mood                    [确定性] mood += Σ val·I·k, k≤0.3；⟳ 向 baseline 回落
(b) Mood ─► Appraisal 强度偏置          [模型读] 有界加性 |Δ|≤b；只改强度不改有无；⟳ 饱和
(c) Mood/Emotion ─► Desire 强度偏置     [确定性] ×(1±b), clamp；⟳
(d) Drive/Desire ─► Appraisal relevance [确定性] 未满足度抬升相关 goal 的 relevance；⟳ 饱和
(e) Drive 受阻 ─► Appraisal(goal_conduciveness<0, agency) ─► Emotion(frustration) [模型候选] ⟳
(f) AT ─► 候选优先级(供 Cognition)      [模型/Cognition] 仅 bias；不直接选 (w)
```
- **每条箭头属性**：(a)(c)(d) 确定性、只写运行态；`Mood`(w)/`Drive`(w，derived)/`Emotion`(W，ledger)；`Appraisal`/`Emotion 类型`(模型候选，带 basis)。
- **阻尼**：所有回边带基线/饱和/半衰期；肝环路增益 <1 为设计约束。
- **优点**：工程最省、可重算、与 FAtiMA/01/02 结构最接近；越级护栏清晰。
- **代价**：Event 处理被画成串行（掩盖 Q1 的并行本质）；Drive 与 Affect 的独立性靠「两条独立链」隐式保证，不够显式。

### 模型 B · 双通道并行 + 共享 relevance 预读（本报告主推方向，直接答 Q1）

```
共享预读：
Event × referent_set ─► relevance 预读 [确定性粗门(实体/关键词匹配活跃 concern) + 模型精判] ─┐
                                                                                         │
  ┌──────────────────────── 通道 M（Motivation）────────────────────────┐               │
  │ relevance + incentive ─► Drive/Desire 重算 [确定性] ─► Desire 候选 (w) │◄──────────────┤
  └──────────────────────────────────────────────────────────────────────┘               │
  ┌──────────────────────── 通道 A（Affect）─────────────────────────────┐               │
  │ relevance + appraisal 变量 [模型候选] ─► Emotion [类型/强度确定性映射] │◄──────────────┘
  │        ─► Action Tendency [确定性查表 / 模型方向] (w)                  │
  └──────────────────────────────────────────────────────────────────────┘
跨通道（有界交叉边）：
M→A: Drive 水平 ─► appraisal 强度调制；受阻 ─► goal_conduciveness<0 ─► frustration   [模型候选] ⟳
A→M: Emotion/Mood ─► Desire 强度 ×(1±b)；AT ─► 候选优先级                            [确定性/模型] ⟳
权  重: attachment 取向 ─► M→A 与 A→M 的回环增益 g                              [确定性参数] ⟳
```
- **每条箭头属性**：两条通道**并行**、共享唯一 junction（relevance 预读）；Drive/Desire 重算**不依赖情绪**（保证 Q7/Q8 可表达）；Emotion 由 appraisal 候选 + 确定性映射产生；AT 是 Affect 输出。
- **阻尼**：跨通道边全部有界；attachment 作增益参数可整体调小回路。
- **优点**：**正面回答「并行」**；Desire 与 Affect 物理分存，矛盾态可表达；确定性与模型分工最清晰。
- **代价**：共享 relevance 预读需一个「活跃 concern 集」，其维护成本（Self/Concern 侧）；两层预读（粗门+精判）需调参。

### 模型 C · 稳态回弹控制环 + 情绪作软偏置（控制论，统一 Q4/Q5）

```
参考信号: Needs(setpoint) / Values(gates) / Affect baseline
误差:     Drive = setpoint − 现状 (+ incentive 项)           [确定性, derived] (w)
对象化:   Desire = 由 Drive × 对象/情境 现算                 [确定性] (w)
                                       │
                                       ▼
                          [Cognition Deliberation] ─► Intention ─► Task   (仅此路可登记)
                                       ▲
软偏置（不在 drive 主路上）:            │
  Emotion ← appraisal(goal_conduciveness…)  [模型候选] ⟳
  Emotion ─► attention 广度 / retrieval 排序 / 候选 salience / effort 上界
                                    [确定性偏置] ⟳   （effort ∝ min(难度, 值得)，Brehm）
  Mood ─► Desire/AT 乘性偏置 (1±b)   [确定性] ⟳
```
- **每条箭头属性**：Drive 是误差信号（负反馈，天然有阻尼）；Emotion **不在 drive 主路上**，只是并行软偏置（attention/effort/排序）；唯一能到 Task 的边必须经 Cognition。
- **阻尼**：负反馈（误差→减误差）+ baseline + effort 上界（Brehm）+ 半衰期。
- **优点**：统一 Q4（drive 削减=reward）与 Q5（mood 作有界偏置）；effort 上界天然防「情绪→无限投入」。
- **代价**：需要心理 need 的 setpoint（无客观值，02 §4.3 最大风险）；对「并行事件处理」表达不如 B 直观。

### 模型 D · 单标量「心情+动力」直连（反例，需避免）

```
Affect valence ─┐
                ├─► 单一 mood/energy 标量 ─► 直接选行动 / 直接改 Task
Drive ──────────┘
```
- **问题**：把「感觉好不好」与「想不想动」压成一个标量 → Q7/Q8 表达不出来（wanting/liking 混同）；标量直连行动=情绪驱动脚本（无 Cognition 闸门）；无分项 provenance。
- **用途**：只作对照，说明为什么 A/B/C 都要「分存 + 有界 + 不直连」。

### 模型对比

| 维度 | A 分层前馈 | B 双通道并行 | C 稳态控制环 | D 单标量（反例） |
|---|---|---|---|---|
| Event 处理顺序 | 串行（欠准） | **并行（准）** | 误差驱动 | 无 |
| 情绪能否直接选行动 | 否（经 Cognition） | 否（经 Cognition） | 否（仅软偏置） | **是（坏）** |
| 确定性占比 | 高 | 高 | 高 | 高但语义错误 |
| 可写状态 | Emotion(W)/Mood(w) | Emotion(W)/Desire(w) | Drive(w)/Emotion(软) | 单一标量（混） |
| 阻尼机制 | 基线+半衰期 | 基线+饱和+增益参数 | 负反馈+effort 上界 | 无 |
| Q7/Q8 可表达 | 隐式 | **显式** | 显式（分离 drive/affect） | **否** |
| 主要优点 | 最省、最接近现状 | 正答并行、分工清晰 | 统一满足/偏置 | — |
| 主要代价/风险 | 掩盖并行 | 需维护活跃 concern 集 | need setpoint 无客观值 | 全部风险 |

> 不要求现在选定。B 与 C 可组合（B 的数据流 + C 的负反馈上界）；A 是它们的降级版。

---

## 10. 给总窗口的输入（≤10 条）

1. **耦合不是一条边，是三条**：① Emotion↔Mood 回环（可保留 FAtiMA 0.3 结构）；② Drive/受阻↔Appraisal/Emotion 回环（frustration 归 Affect）；③ relevance/注意的动机→认知偏置。三者都要独立配阻尼。
2. **Q1 的答案必须写成「并行 + 共享 relevance 预读」**：不存在普适的「Drive 先」或「Emotion 先」；事件类型决定先验方向。强制串行会压掉 incentive 项。
3. **Goal/Value/Concern/Attitude 是 appraisal 的参照（referent），必须进 appraisal 输入**，且 appraisal 输出带 `referent_ref`（与 cause_ref 一起构成 basis）。
4. **Emotion→Action Tendency 是定义性边（Frijda），不是影响边**；情绪→attention/retrieval/desire/intention 才是影响边，且 attention/retrieval 只能是**软偏置**（05 已定召回只进排序层）。
5. **Desire 与 Affect 必须物理分存**：否则「想做但心情差」「心情好却没动力」表达不出来（wanting≠liking，Berridge）。mood 对 desire 只有有界乘性偏置（b≈0.2–0.3，饱和）。
6. **防自激靠组合而非单系数**：负反馈 baseline + 饱和 + 环路增益 <1 + 外部误差独立信号 + 冷却 + 显式 reappraisal/let_go。
7. **确定性/模型分工**：appraisal 语义 = 模型候选（带 basis）；衰减/聚合/偏置/AT 映射/驱动重算 = 确定性。情绪只偏置，不写高权威对象。
8. **attachment 作耦合权重参数（慢对象），longing 作对象化 Desire；** 二者都不进情绪生成本体（旧方案作 0.5h 情绪通道是错的）。
9. **旧方案耦合层两处必删/降级**：「Drive 超阈直连行动」删除；「驱力满足度→情感轴」改为经 appraisal；驱力不得进 Chord。
10. **推荐数据流方向 = 模型 B（双通道并行 + 共享 relevance）**，配 C 的负反馈与 effort 上界；A 为降级版；D 为反例。**不指定最终 schema。**

---

## 11. 证据索引

> 访问日期均为 2026-10-07。E3 均为**转引** 01/02/04/05 的源码核查（本报告未重新核读任何源码，未安装未运行）。

**E3（转引，只读源码核查）**
- FAtiMA-Toolkit（`github.com/GAIPS-INESC-ID/FAtiMA-Toolkit`）：`ActiveEmotion`（CauseId/AppraisalVariables/Decay）；mood 单标量 `[-10,10]`；`UpdateMood: mood += valence·I·0.3`、反向 `potential += val·mood·0.3`——情绪↔心境回边结构与系数（转引 01 §4.5、04 §4.1）。
- emotional_memory（`github.com/gianlucamazza/emotional-memory`，commit `93a02ba…`）：`adaptive_weights()` 心境作权重调制器（*not a hard filter*）、tanh/高斯门；`resonance` Hebbian 单调会饱和（转引 05 §4.1）。
- `jason-lang/jason`（commit `2c2d7e1c…`）：Intention 状态机/reconsideration（转引 02 §4.5）——用于「情绪不直接选 Intention」的护栏依据。

**E2（论文正文/官方文档）**
- OCC：Ortony, Clore & Collins, *The Cognitive Structure of Emotions*, Cambridge UP, 1988（2nd ed. 2022）——goal/standard/attitude 三分支、goal_congruence→joy/distress。
- Scherer CPM：Scherer 2001/2009（PMC7963263）；Scherer & Moors 2019, *Annu. Rev. Psychol.*——序贯 SEC、relevance 最先、goal conduciveness。
- Frijda 1986 *The Emotions*（action readiness / AT 表）；Fontaine & Scherer 2013, "Emotion is for doing: the action tendency component", in *Components of Emotional Meaning* (OUP), doi:10.1093/acprof:oso/9780199592746.003.0012（GRID 三因子：防御vs食欲 / 脱离vs干预 / 屈服vs攻击）。
- Steunebrink, Dastani & Meyer 2009, "A Formal Model of Emotion-based Action Tendency"（EPIA09，people.idsia.ch/~steunebrink/Publications/EPIA09_action_tendency.pdf）——AT=可降低情绪强度的行动、按 gain 排序。
- Gable & Harmon-Jones 2008, *Psychol. Sci.* 19(5):476–482, doi:10.1111/j.1467-9280.2008.02112.x（approach-motivated positive affect 收窄注意）；Gable & Harmon-Jones 2010, *Cogn. Emot.* 24(2):322–337, doi:10.1080/02699930903378305（motivational dimensional model）；Gable & Harmon-Jones 2010, *Psychol. Sci.* 21(2):211–215（"blues broaden, nasty narrows"）；Harmon-Jones, Gable & Price 2013, *Curr. Dir. Psychol. Sci.* 22(4):301–307, doi:10.1177/0963721413481353。
- Brehm & Self 1989, "The intensity of motivation", *Annu. Rev. Psychol.* 40:109–131；Gendolla 综述 2025, PMC11774668（"Affective Influences on the Intensity of Mental Effort: 25 Years…"）——effort ∝ 难度、至「不可能/不值得」；affect 通过告知任务需求影响投入。
- Schwarz & Clore 1983, *JPSP* 45:513–523（mood-as-information / 归因折扣）；Schwarz & Clore 2007 综述 PDF（dornsife.usc.edu）——feelings-as-information、aboutness/immediacy principle。
- Lerner & Keltner 2000, *Cogn. Emot.* 14(4):473–493（Beyond valence）；Lerner & Keltner 2001, *JPSP*（Fear, Anger, and Risk）；Han, Lerner & Keltner 2007（appraisal-tendency framework 五原则：integral/incidental、appraisal tendencies、matching constraint）。
- Berridge & Robinson, incentive-sensitization（PMC5171207；PMC2813042）——wanting（incentive salience）≠ liking（享乐）。
- Bradley & Lang 2001, *Emotion* 1(3):276–298（Emotion and motivation I：防御/食欲）；Lang & Bradley 2013, *Emotion Review* 5(3), doi:10.1177/1754073913477511；Lang & Bradley 2010, *Biol. Psychol.* 84:437–450（motivational priming / motivational brain）——情绪建立在食欲/防御两套动机系统、同系统促进跨系统抑制。
- Mikulincer, Shaver & Pereg 2003, *Motiv. Emot.* 27:77–102, doi:10.1023/A:1024515519160（attachment theory and affect regulation：焦虑超激活/回避去激活）；Bowlby 1969/1982（依恋=行为系统）。
- Gross 1998, *JPSP* 74:224–237；Gross 2015（process model 五族策略；johnnietfeld.com PDF）；"Emotion Regulation Is Motivated"（Ovid, doi:10.1037/emo0000635）——调节目标本身被动机驱动、可 counterhedonic。
- Silvia 2005, *Emotion* 5(1):89–102（interest 的 novelty×coping 评价结构）；Silvia 2008, *Curr. Dir. Psychol. Sci.* 17(1), doi:10.1111/j.1467-8721.2008.00548.x（Interest—The Curious Emotion）；Loewenstein 1994, *Psychol. Bull.* 116(1):75–98（信息缺口好奇）。
- Keramati & Gutkin 2014, *eLife* 3:e04811, PMC4270100（drive=setpoint 距离、reward=drive 削减、deprivation 放大激励值）——Q4「满足=奖励」与 drive→emotion 强度。
- 备注：OCC/CPM/Frijda 的 AT 表亦经 CAS-Group 博客、Simply Psychology、psu.pb.unizin.org 等二手材料逐条比对（E0/E1 辅助，结论以 E2 原文为准）。

**E1（近年 LLM agent，论文声称，未复现、未核代码）**
- "How Emotion Shapes the Behavior of LLMs and Agents: A Mechanistic Study"（E-STEER），arXiv 2604.00005——情绪调制 LLM/agent 单步与多步行为；低 valence/dominance→更频繁重规划；情绪在决策链上累积。
- "Emotional Cognitive Modeling Framework with Desire-Driven Objective Optimization…"，arXiv 2510.13195——情绪→desire→objective→decision→action 闭环。
- "An Explainable Emotion Alignment Framework for LLM-Empowered Agents"，arXiv 2507.22326——state-decision-behavior 演化闭环。

**项目内部参考（E1/E2）**
- `memo/Nocturne-Memory-Core.md`——"BIAS, NOT SCRIPT"、AFFIRM/REJECT/SUSPEND、弱化时间衰减、Longing 参与 attachment 入账闸门。
- 05 §7 引 arXiv 2608.00017（Echo Gap：误差独立性为纠偏必要条件）、arXiv 2504.07992（neural howlround）——自激放大的硬约束。

**未核实一览**
- FAtiMA/emotional-memory/Jason 的 E3 结论**转引**自 01/02/05，本报告未重新核读源码、未比对 commit、未运行。
- E-STEER / arXiv 2510.13195 / 2507.22326 的结论、数字、代码许可均**未核实**，仅作旁证。
- OCC 2nd ed.、Scherer CPM 原文、Frijda 1986 原著、Bradley & Lang 各年原文、Berridge 原始论文、Gross 1998 原文——多为经正文/综述读取，部分细节经二手转述，未逐一核对原文实验数字。
- 环路增益阈值、偏置系数 b、饱和参数的**经验标定未做**；「脚本检测」压测未做。
- 心理 need 的 setpoint 无客观值（02 §4.3 风险）；本报告未解决。
