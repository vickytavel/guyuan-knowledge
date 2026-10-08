# memory-affect-02 · 专题 02：Motivation / Drive / Desire 架构

> 日期：2026-10-07
> 性质：外部机制调研报告。不盘点 Serein 现状，不选最终 schema，不改产品代码，不安装/运行候选项目。
> 统一坐标：沿用 memory-affect-01 §2（evidence / event / fact / interpretation / commitment / open-loop state / affect state / person-world entry）与 memory-affect-02 任务卡 §1（Affect ≠ Motivation；Drive ≠ Desire ≠ Intention ≠ Task）。
> 证据上限：E3（只读源码）。访问日期均为 2026-10-07。
> 本报告是「历史候选」复核，不是验收标准：旧「驱力 10 维」允许改名 / 移出 / 拆分 / 删除。

---

# 1. 本专题真正要解决的问题

旧故渊把「驱力 10 维（表达欲…胜任欲）+ PADCN 5 + 离散 10」统一称为「情绪三层」，其中驱力层被标成「月/周时间尺度 + 人格调性 key」。这一轮暴露的问题不是「少了几维」，而是三类基础分类错误：

1. **本体混装**：把「感觉怎样（Affect）」和「被什么推动、想靠近/避开什么（Motivation）」放进同一个状态向量。
2. **时间尺度混装**：把近人格稳定量（玩心、表达欲）与对象相关瞬态（想念、独处欲）与身体运行态（疲惫）标成同一层、同一尺度。
3. **动力学混装**：把「数值可算」默认当成「应当长期持久保存 + 每轮衰减」。

因此本专题要回答的**真正问题**有三个：

- **Q-A 本体边界**：Need / Drive / Motive / Desire / Goal / Action Tendency / Intention / Task 的层级与判据，哪些属于 Motivation，哪些其实属于 Cognition / Body / Relationship / Self / Affect。
- **Q-B 动力学**：Drive 是否应自然增长/衰减，satiation / deprivation / frustration 怎样表达，多 Drive 冲突怎样裁。
- **Q-C 越级护栏**：Desire 怎样形成/满足/放弃/搁置，怎样防止它绕过认知与权限直接变成 Intention / Task / Wake 行为；「主动性」到底需不需要 Drive。

**不在本专题范围**：情绪生成本体（专题 01/04）、Chord 表达层（专题 04）、运行时落盘与持久化边界（专题 05）、召回工程（07）、具体 schema 与存储选型。本报告只给机制候选、接口候选、成本与失败模式。

---

# 2. 必要术语与边界

## 2.1 术语表（对应必答问题 1）

统一坐标建议（本报告口径）：

| 术语 | 边界定义（本报告采用） | 判据（可用于判定分类的硬条件） |
|---|---|---|
| **Need 需要** | 一种相对持久、可被满足/剥夺的**基础条件缺口**；描述「为了正常运作所必需的东西」。分生理性（homeostatic）与心理性（SDT：自主/胜任/联结）。 | ① 有 setpoint / 目标态语义；②「被满足」是合法谓词；③ 通常无固定对象（不指向具体目标）；④ 时间尺度最慢（周/月/年或近人格）。 |
| **Drive 驱力** | 由**未满足程度（deficit）× 激励/机会（incentive）**在当前算出的动机强度信号；是「推动力的大小」，不是行为本身。 | ① 是标量/向量强度，不是命题；② 通常无固定对象；③ 会随满足回落；④ 只能 bias，不能单独决定行动。 |
| **Motive 动机/动机倾向** | 更接近**内容/类型层面的行动理由或长期倾向**（「我这类人会追求成就/权力/支配」）。偏类目或特质，而非当前强度。 | ① 描述「偏向哪一类」而非「此刻多强」；② 与 trait / disposition 邻近；③ 不能直接满足/剥夺。 |
| **Desire 欲望** | Drive 在**当前对象与情境上的具体化**（「想继续读这个仓库」「想靠近某个人」）。 | ① 必须有（或隐含可解析的）对象/命题内容；② 可满足、可消失、可不承诺；③ 强度可变。 |
| **Goal 目标** | 主体试图达成的一个**目标状态（state of affairs）**；可抽象（价值式）也可具体（对象式）。是「要被实现的东西」，不等于已承诺的执行。 | ① 有目标态条件（goal condition）；② 可被追求但不必已认领/承诺；③ 可与其他 goal 冲突。 |
| **Action Tendency 行动倾向**（= Frijda action readiness） | 由情绪触发的**短时方向性就绪状态**（趋近/回避/攻击/僵住/停留）。是情绪成分之一，不等于行为，也不等于决定。 | ① 短时（秒~分），随情绪变化；② 有方向（趋向/避开）但无承诺；③ 不约束后续推理。 |
| **Intention 意图** | 经认知判断与**主体认领**后作出的**行动承诺**；会约束后续推理，需专门的 reconsideration 才能放弃。 | ① 有承诺（commitment）：不可随意同时持有冲突意图；② 约束后续计划；③ 需 reconsideration 才能撤；④ 仍不是 Task。 |
| **Task 任务** | 正式登记、可执行、可暂停/完成/恢复的**工作对象**（工程对象，不是心理态度）。 | ① 有生命周期状态机；② 有执行权/权限边界；③ 必须由登记步骤产生。 |

**关键边界规则**（防止混装）：

- **Need vs Drive**：Need 是「条件缺口」，Drive 是「此刻的推动强度」。Need 慢、近人格；Drive 快、可算、可回落。
- **Drive vs Motive**：Drive 是强度（how much），Motive 是内容/类型（what kind）。工程上 Drive 做数值，Motive 更宜做类目/标签或合并进 trait。
- **Drive vs Emotion**：Drive 无 valence 本体（是推力），Emotion 有 valence + cause。二者可强耦合但不同本体（红线：不把 Drive 塞回 Affect）。
- **Desire vs Intention**：Desire 是「想」，Intention 是「认领并承诺做」。中间必须经过 Cognitive Deliberation。
- **Intention vs Task**：Intention 是心理承诺，Task 是登记的执行对象。承诺 ≠ 登记（BDI 证据见 §4.5）。
- **Action Tendency vs Intention**：Action Tendency 由情绪短时触发、无承诺；Intention 由认知形成、有承诺。

## 2.2 十二个必答问题逐条回答

**答 1（术语边界）**：见 §2.1。核心分层：Need（条件缺口）→ Drive（deficit×incentive 强度）→ Desire（对象化）→ Intention（认知承诺）→ Task（登记执行）；Motive 是内容/类型层，Goal 是目标状态层，Action Tendency 是情绪侧短时就绪态。**证据**：SDT（E2，[SDT-theory]）、homeostatic RL 的 drive 定义（E2，[Keramati2014]）、BDI（E2/E3，[Herzig2016][Jason-src]）、Frijda action tendency（E2，[Frijda-review]）。

**答 2（哪些做长期 baseline，哪些只能是对象相关状态）**：
- **适合长期 baseline（慢/近人格）**：Need 参数（SDT 的自主/胜任/联结；可加探索）、Value/Principle（属 Self，见答 9）、temperament / causality orientation（SDT COT，E2）。
- **只能是当前对象/情境相关状态**：Desire（必须有对象）、Action Tendency（短时）、Intention（承诺态）、以及对某个对象的想念/好奇/独处欲。
- **判定依据**：看它是否「无固定对象 + 可满足 + 慢」→ 可 baseline；若它「必然指向对象」或「秒~分」→ 只能是当前态。
- **证据**：SDT BPNT（需要是发展条件，E2）；Loewenstein 信息缺口（好奇有对象，E2）；Frijda（action tendency 短时，E2）。

**答 3（Drive 应否自然增长/衰减）**：
- **不建议把「自由自然增长（free-running accrual）」当默认动力学**。理论支持的是**deficit 驱动**：homeostatic RL 把 drive 定义为内部状态到 setpoint 的距离 `D(H)`，并只在与结果交互时变化（E2，[Keramati2014]）。SDT 也只说「需要被支持/被 thwarted 有后果」，并未提出无输入也线性上报的「meter」。
- **建议**：Drive 强度 = f(未满足程度 deficit，当前激励/机会 incentive，环境，记忆/价值)。只有**真正的身体变量**（疲惫/能量）才配「随时间 accrual」的 homeostat，而那属于 Body（见 §5 疲惫）。
- **衰减/回落**：可解析式向 baseline 回弹（analytical relaxation），不 tick 累加——与 memory-affect-01 §6/§8 的既有结论一致（E2/E3）。

**答 4（satiation / deprivation / frustration 怎样表达）**：
- **deprivation 剥夺**：deficit 升高 → Drive 升高、且对结果的「激励价值」升高（homeostatic RL 中 deprivation 对 reward value 有兴奋作用；E2，[Keramati2014]）。表示为 `satisfaction_level`（满足度）低 / `deficit` 高。
- **satiation 饱足**：消费/满足后 deficit 下降；可设**短时不应期（饱足期 refractory）**避免立刻重新拉满。Loewenstein 明确指出好奇「很容易被满足」（E2，[Loewenstein1994]）——支持「满足后有回落/不应期」。
- **frustration 受挫**：**不属于 Drive 变量**，而属于 Affect/Appraisal 边界（目标受阻 → 负性情绪/行动倾向）。理论依据：OCC / CPM 的 goal-conduciveness（E2，[Scherer-review]）；Murray 认为心理需要被挫折是心理痛苦来源，但也把「frustration」当作结果/情绪侧而非驱力维度（E1/E2，[Murray1938]）。
- **结论**：deprivation / satiation 放 Motivation（drive 水平）；frustration 放 Affect/Appraisal（goal obstruction）。

**答 5（多 Drive 冲突）**：
- 理论：Murray 明确「需要可以互相支持或冲突」，并有 `subsidation`（子需合并服务更强者）与 `fusion`（一个行为满足多个需要）（E1/E2，[Murray1938]）。**没有**普遍成立的固定优先级（Maslow 的 prepotency 层次经验支持弱、非严格，E1，[Maslow1943]）。
- **工程建议**：用**带上下文权重的加权优先级 + 迟滞（hysteresis）**，并显式检测「两个 Drive 指向互斥行动」；冲突最终**交给 Cognition 裁决**（BDI 的 option/intention 选择函数，E3，[Jason-src]）。Drive 只提供带偏置的输入，不自行裁决。

**答 6（Desire 是否必须有 target/object）**：
- **原则上必须是对象/情境化的**。项目自身前提即「Desire = Drive 在当前对象与情境上的具体化」；Bratman/BDI 的 desire 是「对命题/结果的 pro-attitude」；Loewenstein 的好奇 = 对**缺失信息（有对象）**的欲望（E2，[Loewenstein1994][goal-paper]）。
- **工程建议**：允许 Desire 诞生时 `target_ref` 为「未解析」，但**要进入 Drive→Desire→Intention 管道必须可解析到对象**；否则它只是 Mood/Drive 的背景偏置，不登记为 Desire。

**答 7（Desire 如何形成/满足/放弃/搁置）**：
- **形成**：Drive + 对象激励显著度 + appraisal relevance + 记忆/上下文 → 候选 Desire（BDI 里是「option」，尚未承诺）。
- **满足**：目标达成 / 需要满足 → 关闭，可留下 Affect annotation（满足感）。
- **放弃**：不可达或不再想要 → drop，但保留历史（supersession，不删）。
- **搁置**：BDI 的 Intention 存在 `waiting / suspended` 状态（E3，[Jason-src]）；Nocturne 的 `SUSPEND`（暂不确认，保持开放）。二者都支持把「搁置」做成**独立状态**而非「放弃」的子类。
- **建议状态机**：`candidate → active →（satisfied | abandoned | suspended=Concern | promoted→Intention）`，全程留历史。

**答 8（Desire 与 Concern 的区别）**：
- **Desire** 是动机性、对象化的「想得到/想做」，强度可变、可被满足而消失。
- **Concern** 是「这件事还没结束」的**开放状态**（00b 定义），**不要求存在一个『想要』**；它强调对世界开放状态的持续追踪，需要显式关闭或消退。
- **边界**：Desire 可在长期未解决时**升级为 Concern**（主体持续挂着），但 Concern 不是 Desire（无必需的 want）。同理 00b 已定「兴趣 ≠ Concern」。**证据**：00b/00c（项目内部，E2）；GTD open loops（E1）。

**答 9（Goal / Value / Principle 属 Motivation 还是 Cognition）**：
- **Value / Principle 属 Self / Cognition**（Stable Self 层），不是 Drive。它们作为**约束与过滤器**决定哪些 Desire/Intention 可被认领（BDI 里 norms 过滤 mental attitudes；BOID；[Herzig2016][goal-paper]，E2），并作为 appraisal 的 standard（OCC 的 standards 分支、CPM 的 normative significance，E2）。
- **Goal 跨两层**：作为「动机内容」它可以是 Desire 的一种；作为「被登记/被追求的目标状态」它属 Cognition/Deliberation 层，喂给 Intention。
- **结论**：Motivation 只持有 Drive（强度）与 Action Tendency；Goal/Value/Principle 放 Cognition/Self，作为 Motivation 的**输入与闸门**。

**答 10（Intention 怎样避免自动变成 Task）**：
- BDI/Bratman：Intention 是**对计划/手段的承诺**，会约束后续推理（intention is conduct-controlling），但仍不是执行对象。Jason 实现里 Intention = 一个 `IntendedMeans` 栈，Option = Plan+Unifier+Event，`selectOption` 选计划、`selectIntention` 出队，状态 `running/waiting/suspended`，且带 goal-condition 满足即移除（E3，[Jason-src]）。
- **工程护栏**：① Intention 只在认知/agent 循环内；② 必须有 deliberation（option 选择）与**主体认领**；③ 到 Task 需要**独立登记步骤**（可要求 Permission）；④ 允许 reconsideration 但有触发条件（避免 thrash）。BDI 说明：承诺与 reconsideration 可以内建，同时**从不自动变成外部效应**。

**答 11（Intrinsic Motivation 成熟度）**：
- **RL/好奇心**：机制层**已成熟**（ICM、RND、count-based；Aubret 综述把它系统化成类目），但面向的是「稀疏奖励下的探索」，不是长期目标生活（E2，[Aubret2019][Pathak2017][Burda2018]）。
- **LLM agent**：**尚不成熟、多为研究原型**。ProactiveAgent 用数据驱动 + 奖励模型预测用户想要的主动任务（E2，[ProactiveAgent]）；LLM Agents Beyond Utility 的开放式 agent 能自生成任务，但论文自陈「倾向于重复生成任务」「无法形成自我表征」（E2，[LLMBeyondUtility]）。
- **结论**：内在动机作为「探索奖励」成熟；作为「陪伴型 agent 的动机架构」不成熟。

**答 12（主动性需要 Drive 还是只需 Goal/Planner）**：
- **只有 Goal/Planner 也能产生主动性**，但前提是**有外部目标来源**（ProactiveAgent 从上下文预测任务，无需 Drive，E2）。
- **自发的、无外部指令的兴趣/探索（autotelic）需要类似 Drive/内在动机**（LLM Beyond Utility、Voyager 的 goal 自生成，E2；SDT 的内在动机源自需要满足，E2）。
- **代价对比**：Goal/Planner-only 便宜、可审计，但无法解释「没被要求也想动」；Drive-based 能自发起意与分级紧迫，但有失控、反馈环、调参成本高，且**需要 Cognition/Permission 闸门**才安全。
- **结论（分层）**：Drive/好奇负责**候选生成与偏置（软）**；Goal/Planner + Cognition 负责选择；Permission/Wake 负责闸门。Drive ≠ 行动，Goal ≠ 自发。

## 2.3 一句话边界

> **Need 是缺口，Drive 是推力，Desire 是推力找到对象，Intention 是主体认领承诺，Task 是登记执行；Value/Principle 属自我，frustration 属情绪。**

---

# 3. 理论 / 机制候选总表

| 候选 | 来源类型 | 证据等级 | 核心机制 | 对故渊的可用性 |
|---|---|---|---|---|
| Self-Determination Theory (SDT) | 心理学理论 | E2 [SDT-theory][DeciRyan2000] | 3 个基本心理需要（自主/胜任/联结）；6 个 mini-theory；内外动机连续体 internalization | 高：Need 层最小集；内在动机来源；但**需要 ≠ 数值 Drive**，不能直接当驱动器 |
| Drive-reduction (Hull 1943) | 心理学理论 | E1 [Hull-wiki] | drive=剥夺产生的普遍能量；强化=驱力削减 | 中：历史根基；**纯 drive-reduction 已被 incentive 理论超越**，不能照搬 |
| Incentive motivation (Bolles/Bindra/Toates) | 心理学理论 | E0/E1 | 行为由外部激励刺激的显著度驱动，而非仅内部 deficit | 高：解释「同一 deficit 下对象不同→Drive 不同」，支撑 `incentive` 项 |
| Murray psychogenic needs (1938) | 心理学理论 | E1/E2 [Murray1938] | 20+ psychogenic needs，need×press；subsidation/fusion；会冲突 | 高：旧 10 维的主要谱系来源；提供「need 可冲突/可合并」的机制 |
| Maslow hierarchy (1943) | 心理学理论 | E1 [Maslow1943] | 需要按 prepotency 分层 | 低：经验支持弱；**不建议做硬层次** |
| Homeostatic RL (Keramati & Gutkin 2014) | 论文（计算模型） | E2 [Keramati2014] | drive = 内部态到 setpoint 的距离 `D(H)`；reward = drive 削减量；deprivation 放大激励值 | 高：给 Drive 一个**可算、可解释、可回落**的定义；把 body 与 motivation 接起来 |
| Control theory / self-regulation (Carver & Scheier) | 心理学理论 | E1/E2 [CarverScheier] | 负反馈回路，reference value=goal；分层回路 | 高：Drive 做「误差信号」、Goal 做 reference value 的框架；配合解析回落 |
| BIS / BAS (Gray; Carver & White 1994) | 心理学理论 | E2 [CarverWhite] | 趋近系统(BAS) vs 抑制/回避系统(BIS)；对奖励/惩罚线索敏感 | 中高：给「安全欲/回避」一个双系统结构；**BIS/BAS 个体差异近 trait**，宜做参数而非态 |
| Approach–avoidance motivation (Elliot) | 心理学理论 | E1/E2 | 趋近/回避两轴 + 成就目标 2×2 | 中：Action Tendency 的方向轴；用于 Desire/AT 的方向编码 |
| Frijda action tendency / action readiness | 心理学理论 | E2 [Frijda-review] | 情绪成分=行动就绪态；短时、有方向、不承诺 | 高：定义 Action Tendency，接 Affect→Motivation 的方向通道 |
| Information-gap theory of curiosity (Loewenstein 1994) | 论文 | E2 [Loewenstein1994] | 好奇=感知到知识缺口而生的认知性 deprivation；对象=缺口；易满足 | 高：给「好奇」精确定位（Drive+对象），拆旧维度用 |
| BDI architecture (Bratman; Rao & Georgeff; Jason) | 理论+代码 | E2 [Herzig2016][goal-paper] / **E3** [Jason-src] | Belief/Desire/Intention；intention=承诺；reconsideration；option 选择 | 高：给 Desire→Intention→Task 的**权威分层与越级护栏** |
| Intrinsic motivation in RL (ICM/RND) | 论文+实现 | E2 [Aubret2019][Pathak2017][Burda2018] | 预测误差/随机网络作内在奖励，驱动探索 | 中：机制成熟但面向 RL 探索，不直接是陪伴 agent 动机架构 |
| Active inference / expected free energy (Friston; pymdp) | 理论+代码 | E2 [EFE-cost] / **E3** [pymdp-src] | 以偏好先验(C)与 EFE 统一感知/行动/学习；epistemic value=好奇 | 中：理论漂亮；**计算成本高**（[EFE-cost] 明承此问题），仅作候选 |
| LLM proactive agents (ProactiveAgent; Beyond Utility) | 论文+实现 | E2 [ProactiveAgent][LLMBeyondUtility] | 从上下文预测/自生成任务；奖励模型评分 | 中：证明「主动性可数据驱动」，但也暴露重复生成、无自我表征 |

> 共 15 项；覆盖「心理学理论」与「AI 工程/代码」两类以上来源（满足完成标准）。代码候选的关键工程结论见 §4.5、§4.7（E3）。

---

# 4. 重点候选深查

## 4.1 SDT：Need 层最小集与「需要不是驱动器」（E2）

SDT 六个 mini-theory：CET（内在动机）、OIT（外在动机内化连续体 external→introjection→identification→integration）、COT（因果定向，近 trait）、BPNT（自主/胜任/联结三需要）、GCT（目标内容）、RMT（关系动机）。SDT 明确：需要是「growth / integrity / well-being 的必要条件」，被支持则蓬勃发展、被 thwarted 则功能受损（E2，[SDT-theory][DeciRyan2000]）。

- **吻合**：给 Need 层一个**最小、可辩护**的集合（自主/胜任/联结，可加探索），且「需要被满足/被剥夺」有后果——正好映射故渊的 `satiation/deprivation`。
- **不吻合/关键警告**：SDT 的需要**不是数值驱力**，它不提出「无输入也线性上涨的 meter」；把 SDT 三需要直接落成「每 tick 上涨的驱力」是对理论的误用。故渊若用 SDT，应把它当**慢参数（baseline 阈值）+ 内在动机来源**，而 Drive 由 deficit×incentive 现算。
- **未核实**：SDT 的实证效应量未逐一核对；只读到官方理论页与 2000 目标论文导言。

## 4.2 Drive / Need / Incentive：从 Hull 到激励理论（E1/E2）

- **Hull 1943 drive-reduction**：drive 由剥夺产生、是普遍能量源，强化 = 驱力削减（E1，[Hull-wiki][Hull-psycnet]）。
- **批判**：纯 drive-reduction 无法解释**探索/好奇**（没有明显 deficit 也探索），1960s 起被 **incentive motivation**（Bolles/Bindra/Toates）取代——行为由外部激励刺激的显著度驱动（E0/E1，[Incentive-summary]）。Loewenstein 亦指出「 deprive 与探索」是驱动理论的老难题（E2，[Loewenstein1994] 的 satiation/curiosity 讨论）。
- **对故渊的意义**：**不要**只做内部 deficit 的「自然增长」；必须显式保留 `incentive`（当前对象/机会的显著度）项，否则无法解释「同一 deficit 下对不同对象欲望不同」。

## 4.3 Homeostatic RL / 控制论：Drive 的可算定义（E2）

- **机制**：定义 homeostatic space，`drive D(H_t)=Σ|h*−h_t|`（到 setpoint 的距离）；结果 `K_t` 使状态迁移，reward `r=D(H_t)−D(H_{t+1})`（即 drive 削减量）；deprivation 对激励值有兴奋作用（E2，[Keramati2014]）。
- **吻合**：Drive 可算、可解释、可回落；reward 就是「drive 削减」——天然支持 `satiation`。
- **工程价值**：把 Drive 定义成**误差/距离**，而非独立累积变量；满足即削减误差。这与 control theory 的负反馈回路（reference value=goal，E1/E2，[CarverScheier]）一致。
- **不吻合**：原模型是生理变量（葡萄糖/体温/渗透压）；把心理「需要」也套成 setpoint 距离需要**自定 setpoint 与量纲**，属故渊需补。**心理 setpoint 无天然客观值**——这是最大落地点风险。

## 4.4 BIS/BAS 与 approach–avoidance：方向轴（E1/E2）

- BAS 调控趋近（对奖励线索敏感），BIS 调控回避/抑制（对惩罚/新颖线索敏感）；Carver & White 1994 量表测量个体差异（E2，[CarverWhite][ActionTendency]）。
- **吻合**：给「安全欲/回避」一个双系统结构；给 Desire/Action Tendency 一个**方向轴（趋近/回避）**。
- **警告**：BIS/BAS 是**个体差异（近 trait）**，宜作**参数/权重**，不是随轮变化的态。故渊若把「安全欲」做成每轮波动的 Drive，需与「BIS 敏感度是近人格参数」区分。

## 4.5 BDI：Desire → Intention → Task 的越级护栏（E2 理论 + **E3 源码**）

**理论**：Bratman 把 goals 细化为 desires 与 intentions；intention 是承诺、会约束后续推理、需 reconsideration 才能撤（E2，[Herzig2016]）。目标/欲望/意图可被看作「acceptable outcome」的不同 facet，belief 与 norms 用来**过滤**出意图（E2，[goal-paper]）。BDI 架构调查（2020）系统梳理各组件与权衡（E2，[BDI-survey]）。

**源码核查（E3）**：`jason-lang/jason`，main @ commit `2c2d7e1c1ea3bd5cef9712d551632ba367109741`（2026-10-07 访问），路径 `jason-interpreter/src/main/java/jason/asSemantics/`：
- `Agent.selectOption(List<Option>)`：返回 `options.removeFirst()`（默认取第一个 relevant+applicable 的 option），可被 `select__option` 自定义（E3）。
- `Agent.selectIntention(Queue<Intention>)`：`return intentions.poll();`，注释明写「make sure no intention will 'starve'」——即**意图选择默认是轮转、不饿死**（E3）。
- `Intention`：是 `IntendedMeans` 的**栈**；`State enum { running, waiting, suspended, undefined }`；有 `isFinished()`、`setSuspended(boolean)`、`getSuspendedReason()`（E3）。
- `Option`：= `Plan + Unifier + Event`，注释「an Option is a Plan and the Unifier that has made it relevant and applicable for an Event」（E3）。
- `TransitionSystem`：有 `applicablePlans/relevantPlans`、goal-condition 满足即移除意图、意图栈超 3000 有告警（E3）。

**对故渊的意义**：BDI 是**已实现**的「Desire→Intention 不自动变 Task」的范本——intention 有生命周期与 reconsideration，但从不自动产生外部效应。这是「Intention ≠ Task」最硬的一手证据。

## 4.6 Frijda action readiness：Action Tendency 的定位（E2）

心情/情绪的「行动就绪态」是情绪成分，短时、有方向、不承诺；同一情绪可导致不同行动倾向，且受当下认知/生理能力修正（E2，[Frijda-review][ActionTendency]）。**对故渊**：Action Tendency 应属 **Affect × Motivation 的接口对象**——由情绪触发、表达供 Motivation/行动选择参考的**方向偏置**，不构成承诺。

## 4.7 Active inference / EFE：理论漂亮但成本高（E2 + **E3**）

- **机制**：以偏好先验 C 与 expected free energy 统一感知/行动/学习；epistemic value（信息增益）即「好奇心」；pymdp 用 `Agent.infer_states → infer_policies`（`neg_efe`）→ `sample_action`（E2，[pymdp-JOSS]）。
- **源码核查（E3）**：`infer-actively/pymdp`，`pymdp/agent.py`（main 浮动，**未固定 commit**）：`Agent` 持 `A/B/C/D/E`、`policies`、`gamma/alpha`，以及 `H`（goal states）与 `I`（reachability）用于 inductive inference；方法 `infer_states / infer_policies / sample_action`；`policy_len` 为规划深度（E3）。
- **成本警告（关键工程结论）**：`On efficient computation in active inference`（E2，[EFE-cost]）**明确承认** active inference 在复杂环境因**计算成本高**与**目标分布难设定**而难以智能行动，需专门的动态规划加速。故渊 VPS + LLM 场景下，完整 EFE 规划**不建议**作为默认；最多借「偏好作先验 + epistemic value 的概念」。
- **不吻合**：pymdp 面向 POMDP 网格/控制，非陪伴型长程认知；把 EFE 塞进每轮会显著抬成本。

## 4.8 内在动机（RL 与 LLM）：机制成熟度分层（E2）

- **RL**：ICM（Pathak 2017，预测误差内在奖励）、RND（Burda 2018，随机网络预测误差）；Aubret 2019 综述按信息论给出分类并提出 developmental architecture 作**建议**（E2，[Aubret2019][Pathak2017][Burda2018]）。**成熟度：探索层成熟**。
- **LLM agent**：ProactiveAgent 用真实人类活动数据训练奖励模型来预测主动任务（E2，[ProactiveAgent]）；LLM Agents Beyond Utility 让 agent 自生成任务、跨 run 累积知识，但自陈「重复生成任务」「无法形成自我表征」（E2，[LLMBeyondUtility]）。**成熟度：原型**。

---

# 5. 旧故渊方案逐项复核（旧 10 维 Drive）

旧 10 维原始来源：`guyuan/docs/tasks/task-14-affect-25dim.md`（驱力层 10 维，标「月/周 + 人格调性 key」）。下表逐项分类，**不为保留旧方案硬凑**。

| 旧维度 | 建议分类 | 证据与理由 | 证据等级 |
|---|---|---|---|
| **表达欲** | **Drive**（近似 SDT 自主 / Murray Exhibition 的动机面） | 属「被推动去表达」，无固定对象、可满足、近 baseline；SDT autonomy 与 Murray exhibition 都归 motivation 而非 affect。**但**「表达」若指「想被看见/被理解」则更近 Relationship/Concern，需拆。 | E2（SDT/Murray 谱系） |
| **关心欲** | **关系联结的 Motivation**（Murray Nurturance；SDT relatedness 的「给予」面），偏向关系域 | 它**指向关系对象**（关心谁），既非纯 baseline 也无 valence 本体；更像「关系域内的 Drive/Desire」。建议不作为独立 baseline 维度，而由 relatedness need + 关系模型派生。 | E1/E2 |
| **好奇** | **Drive + 对象化 Desire 的混合**（Loewenstein 信息缺口） | 好奇 = 对**知识缺口**的认知性 deprivation，**有对象**（缺口）、易满足（E2）；既是「推力」又绑定对象。**应移出『驱力层基线』**，按对象现算。 | E2 |
| **玩心** | **近 trait/disposition 或 Affect-flavored**（Murray Play；SDT 视 play 为内在动机原型） | 「玩心」更像稳定倾向/风格，不是可满足的 drive；且常表现为「玩得开心」的 Affect。**不建议做 Drive 数值**；宜并入 temperament/Play 倾向或归 Affect。 | E2/E1 |
| **连接欲** | **Drive**（SDT relatedness；Murray Affiliation） | 属基础关系需要，近 baseline、可满足；是 SDT 三需要之一。**保留**但应作为「需要 → Drive」的一支，而非独立数值。 | E2 |
| **独处欲** | **Desire / Action Tendency 或 人格偏好**，并受 fatigue/过度刺激驱动 | SDT **无**独立的 solitude 需要；它更像「当下想退开」的状态相关结果（可由疲惫/过载触发）。**对象相关/状态相关**，不适合做长期 baseline 维度。 | 推断（E2 负向证据：无对应基本需要） |
| **疲惫** | **Body / Runtime State**（身体/资源耗竭变量），**必须移出 Motivation** | 疲惫是生理/资源状态，不是「被什么推动」；对应 homeostatic variable（E2，[Keramati2014] 的 setpoint 变量谱系）。可作为 **Affect 的输入**（疲惫感）与 Drive 的调制因子，但本体属 Body。 | E2（homeostatic 框架） |
| **安全欲** | **Drive（回避向）**（Murray Harmavoidance；BIS） | 属回避动机，近 baseline；BIS 敏感度近 trait（E2）。**保留**，但注意「BIS 敏感度=参数、当前回避强度=态」。 | E2 |
| **想念** | **Desire（对象化，指向某人）**，也可能伴随 Relationship/Emotion | 想念**必有对象**（想谁），会波动、可满足/缓解；Nocturne 把 Longing 作为 Attachment 入账闸门的参与者（项目参考）。**绝不适合做长期 baseline 维度**。 | E1/E2（Nocturne + 对象性推断） |
| **胜任欲** | **Drive**（SDT competence；Murray Achievement） | 属基础胜任需要，近 baseline、可满足；SDT 三需要之一。**保留**，同连接欲，应作为「需要→Drive」的一支。 | E2 |

**旧 10 维复核总评（关键结论）**：

1. **本体错误集中在两处**：**疲惫 = Body/Runtime State**（应移出 Motivation）；**想念 / 独处欲 = 对象/状态相关**（不能做 baseline 维度）。
2. **好奇被错误地当『基线』**：它其实有对象（信息缺口），应按对象现算。
3. **玩心更像 trait 或 Affect**，做数值 Drive 违背其性质。
4. **表达欲 / 关心欲 概念偏模糊**，需拆分（表达 vs 被看见；关心 vs 联结需要）。
5. **真正可保留为「需要/Drive」的只有**：连接欲、胜任欲、安全欲——且**最稳妥是归并为 SDT 三需要（+探索）**，由「需要」派生 Drive，而非直接保留 10 个平行数值。
6. **旧的「人格调性 key（月/周）」把 trait/disposition 与 drive-state 混为一谈**，是时间尺度错误：trait 归 Self/参数，drive-state 归 Motivation 现算。

---

# 6. 对新架构的可用性拆分

## 6.1 baseline vs 当前对象相关状态（回答必答问题 2）

| 对象 | 长期 baseline? | 时间尺度 | 说明 |
|---|---|---|---|
| Need（自主/胜任/联结/探索） | ✅ 是（慢参数/阈值） | 周/月/近人格 | 变化慢，作 Drive 的输入来源与 setpoint |
| Tempoerament / causality orientation (COT) | ✅ 是 | 近人格 | 作权重参数（如 BIS 敏感度） |
| Value / Principle | ✅ 是（属 Self） | 长 | 作闸门/过滤器（答 9） |
| Drive（强度） | ❌ 否 | 分~时 | deficit×incentive 现算，可回落 |
| Desire | ❌ 否 | 分~时（对象在场） | 必须对象化 |
| Action Tendency | ❌ 否 | 秒~分 | 情绪触发 |
| Goal（具体目标态） | ❌ 否 | 视对象 | 可登记但不必承诺 |
| Intention | ❌ 否 | 承诺期 | 需 reconsideration |
| Concern | 🟡 半持久 | 天~周 | 开放状态，需显式关闭 |

## 6.2 横向必答（每个变量逐项）

| 变量 | 类别 | cause / target | 时间尺度 | snapshot/ledger/derived | provenance? | 影响方式 | 允许模型自由写? | 衰减/失效 | 反馈环风险 | 接口 |
|---|---|---|---|---|---|---|---|---|---|---|
| Need | Motivation（慢）/ Self | 无 cause；无 target | 周/月+ | derived（读时算，不落库） | 若人工设定则要 | 只做偏置/参数 | ❌ 否（是设定值） | 不衰减（近静态） | 低 | 输入 Drive；接 Self |
| Drive | Motivation | cause=deficit/incentive | 分~时 | derived view（可重算） | 是（来源事件） | bias-only | 🟡 仅给候选 | 解析式向 baseline 回落 | **中高**（Drive→行动→Drive） | 输入 Desire；被 Affect 调制 |
| Motive | Cognition/Self（类目） | 无 cause | 长/近人格 | baseline 标签 | 若推断则要 | 偏置 | ❌ | 不衰减 | 低 | Self Model |
| Desire | Motivation | cause=Drive×对象；**target 必需** | 分~时 | ledger（候选→状态迁移） | 是 | bias-only（不得直接执行） | 🟡 只产候选 | 满足/放弃/搁置→失效 | 中 | → Concern / → Cognition→Intention |
| Goal | Cognition（具体目标态） | cause=deliberation | 视范围 | ledger | 是 | 影响 deliberation | 🟡 候选 | 达成/放弃 | 低 | ↔ Intention |
| Value / Principle | Self/Cognition | 无 cause | 长 | baseline（Stable Self） | 是（认领记录） | **闸门/过滤**（可硬可软） | ❌ | 高门槛变更，留版本 | 低 | 过滤 Motivation 候选 |
| Action Tendency | Affect×Motivation 接口 | cause=emotion/appraisal | 秒~分 | snapshot（运行态） | 弱 | bias-only | 🟡 | 随情绪回落 | 中 | Affect→行动选择 |
| Intention | Cognition（承诺） | cause=deliberation+认领 | 承诺期 | ledger（状态机） | 是 | 约束后续推理 | ❌ 需认领 | reconsideration / 达成 | 中 | → Task（经登记/Permission） |
| Concern | Continuity/Memory | cause=开放事件 | 天~周 | ledger | 是 | 影响 Wake/注意 | 🟡 候选 | 显式关闭或消退 | 低 | ↔ Wake/Task |
| 想念(longing) | Desire（对象化） | target=某人；cause=分离×联结需要 | 分~天 | derived/ledger | 是 | bias-only | 🟡 | 依对象在场/联结回落 | 中 | 关系模型 + Desire |
| 疲惫(fatigue) | **Body/Runtime** | cause=资源耗竭 | 时~天 | snapshot + 解析回落 | 弱 | 调制 Drive/Affect | ❌ 确定性算 | 休息回落 | 低 | → Affect / Drive 权重 |
| 好奇(curiosity) | Drive+Desire（对象化） | cause=信息缺口；target=缺口 | 分~时 | derived | 是 | bias-only | 🟡 | 缺口闭合即满足 | 中 | → Exploration / Retrieval |
| 内在奖励(RL) | 机制（工程） | cause=预测误差 | 步/回合 | 运行态 | 否 | 训练信号 | ❌ | 依算法 | 高（noisy TV） | 不直接接故渊 |
| 安全欲 | Drive（回避） | cause=威胁线索（BIS） | 分~时 | derived | 是 | bias-only | 🟡 | 威胁解除回落 | 中高（焦虑环） | → Affect/回避 |

## 6.3 可用性归类

- **可借鉴机制**：SDT 三需要（Need 最小集）；homeostatic RL 的 `drive=距离` 与 `reward=drive 削减`；incentive 项；BDI 的 option/intention/reconsideration；Frijda action tendency（方向偏置）；BIS/BAS 作参数。
- **可直接复用代码（算法，非整库）**：homeostatic reward 公式；BDI 意图状态机语义（学结构，不引 Java 栈）；解析式衰减式（沿用 `core/decay.py`）。
- **故渊需补**：心理 need 的 setpoint 与量纲（无客观值）；Desire 状态机与历史保留；Drive→Desire→Intention 的权威写者与闸门；「frustration 归 Affect」的接口。
- **不建议采用**：无输入自由的 Drive 线性增长；Maslow 式硬层次；把 active inference/EFE 作为每轮默认规划；把 BIS/BAS trait 当轮态。
- **未核实**：Hull 原著；Murray 1938 原著；Carver & White 1994 原文；Bratman 1987 原书；Frijda 1986 原书（读的是综述）；Elliot 模型细节；Schwartz values（仅检索摘要）；pymdp 浮动 main 未固定 commit。

---

# 7. 跨模块接口候选

**Motivation 读取（输入）**：
- `need_params`（慢，来自 Self/配置）：自主/胜任/联结/探索的 setpoint 与权重；
- `world/self/concern`（当前对象与情境，来自 02/03/06）；
- `affect_state`（当前情绪/mood，来自 04）；
- `memory_salience`（对象显著度，来自 01/05）；
- `values/principles`（作为过滤/闸门，来自 Self）。

**Motivation 产出（输出）**：
- `drive_state`：`{drive_id, intensity, deficit, incentive_ref, basis, valid_at}`，**derived（读时现算，不落库衰减值）**；
- `desire_candidate`：`{desire_id, drive_ref, **target_ref**, intensity, state(candidate/active/suspended/abandoned/satisfied), evidence, provenance}`；
- `action_tendency`：`{direction(approach/avoid), intensity, source_emotion_ref}`，**snapshot，只 bias**；
- `intention_candidate`：**仅候选**，交给 Cognition/Deliberation。

**对外信号**：`motivation_shift(drive_id?, delta, basis)`（供 Wake 作软信号、供 05 作软排序）。

**护栏（硬约束）**：
- Drive/Desire/Action Tendency **只能 bias，不能直接执行**；
- Intention 由 Cognition/主体认领产生，**不是 Motivation 直接写**；
- Task 需独立登记步骤（可要求 Permission）；
- Wake 可读 Drive/Desire 作**候选信号**，但**不得**由强 Drive 直接生成 Task 或对外行动。

---

# 8. 失败模式与风险

1. **自由增长失控**：Drive 若每 tick 自然上涨，长时间不活动会累积成虚假紧迫 → 刷屏/乱行动。缓解：deficit 现算 + 解析回落 + 上限。
2. **自我强化环**：Drive→行动→（结果正反馈）→更强 Drive。缓解：有界标量 + 半衰期 + 冷却 + 外部独立信号（参考 05 的 Echo Gap 结论）。
3. **Desire→Task 越权**：强烈欲望绕过认知/权限直接建 Task/发消息。缓解：BDI 式分层 + 登记步骤 + 主体认领。
4. **模型幻觉欲望**：LLM 自由写「我想要 X」可能编造动机。缓解：Desire 只产候选、必须带 `basis` 锚到证据（事件/缺口）。
5. **setpoint 漂移**：心理 need 无客观 setpoint，人工设错 → 全系统偏。缓解：setpoint 可配、可审、可版本化。
6. **Maslow 硬层次误用**：用固定优先级压多 Drive 冲突会僵化。缓解：上下文加权 + hysteresis，不设普适层次。
7. **BIS/BAS trait 当轮态**：把近人格敏感度做成每轮波动 → 无意义抖动。缓解：trait 参数化。
8. **active inference 成本爆炸**：EFE 每轮规划在 VPS + LLM 场景不可负担（[EFE-cost] 自承）。缓解：不默认采用。
9. **frustration 归错层**：把「受阻」当 Drive 会与 Affect 重复。缓解：frustration 归 Affect/Appraisal。
10. **对象不明的 Desire 泛滥**：无对象欲望堆积 → 无法裁决。缓解：进入管道前必须可解析对象，否则降级为 Mood/Drive 偏置。
11. **持久化膨胀**：每轮 Drive Δ 都落盘 → 日志爆炸。根治：`drive_state` 做 derived view，只在**跨越阈值/被认领**时形成持久事件（与 04/05 结论一致）。
12. **不必要地「像人」**：硬套神经科学（BIS/BAS/多巴胺）却无工程收益。缓解：只借结构，不借生物学叙事。

---

# 9. 推荐机制：A / B / C / D（至少 2 个可比较候选，不要求选定）

**A · 双基座 + 对象化（保守派）**
- 结构：Need/Value 作慢 baseline（Self/参数）；Drive = deficit×incentive **现算**（derived，不落库）；Desire 对象化且短时（ledger，带状态机）；Intention 在 Cognition 层，需认领。
- 获得：可解释、可重算、成本低、越级护栏清晰。
- 牺牲：自发性偏弱（需显式 incentive/机会输入）；无法「无来由地想动」。
- 证据：SDT + homeostatic RL + BDI（E2/E3）。

**B · Drive Ledger（Nocturne 式，历史丰富派）**
- 结构：少量持久 Drive 维度（如 3–6），事件驱动入账，按来源/强度/置信度产生轻量偏移并自然回归；Longing 等作为入账闸门参与者（项目参考 Nocturne）。
- 获得：可审计、可追溯「为什么变了多少」，支持长期演变叙事。
- 牺牲：需要**入账规则与防膨胀**；易把 trait 与 state 混回一个 ledger；有自我强化风险。
- 证据：Nocturne（E1，项目参考）+ homeostatic RL（E2）。

**C · Active Inference / EFE 驱动派**
- 结构：偏好先验 C 表示 needs/values，Drive=先验偏好，Desire=policy 后验，epistemic value=好奇。
- 获得：理论统一、内建好奇心与不确定性。
- 牺牲：**计算成本高、目标分布难设**（[EFE-cost] 自承），与 VPS/LLM 成本冲突；落地面窄。
- 证据：pymdp（E3 源码）+ EFE-cost（E2）。

**D · BDI / 符号优先（无连续 Drive 派）**
- 结构：不设连续 Drive 数值；Desire=option，Intention=承诺栈，Drive 以「goal-blocked」符号表达。
- 获得：结构清晰、越级护栏天然、无浮点调参。
- 牺牲：难表达「强度/紧迫度」与分级偏置；主动性的「冲劲」表达弱。
- 证据：Jason 源码（E3）。

**可比性小结**：

| 维度 | A 双基座 | B Drive Ledger | C Active Inference | D BDI 符号 |
|---|---|---|---|---|
| 自发性 | 中 | 中高 | 高 | 低 |
| 可审计/可追溯 | 高 | 很高 | 中（黑箱） | 高 |
| 计算成本 | 低 | 中 | **高** | 低 |
| 可重算 | 高 | 中 | 低 | 高 |
| 失控风险 | 低 | 中 | 中高 | 低 |
| 与故渊现状契合 | 高 | 高（近 Nocturne） | 低 | 中 |

> 不要求现在选定；四个候选都可在下一轮整体组合里与 Affect（专题 01/03）、Chord（04）、持久化（05）对齐后再裁。

---

# 10. 给总窗口的输入（≤10 条）

1. **本体分层定为**：Need（慢）→ Drive（现算强度）→ Desire（对象化）→ Intention（认知承诺）→ Task（登记执行）；Motive 是内容层，Goal 是目标态层，Action Tendency 是情绪侧方向偏置。
2. **禁止自由的 Drive 自然增长**：用 `deficit×incentive` 现算 + 解析回落；只有身体变量（疲惫）才配 homeostat，且归 Body。
3. **satiation/deprivation 归 Motivation，frustration 归 Affect/Appraisal**——不要混层。
4. **旧 10 维整改结论**：疲惫移出（→Body）；想念、独处欲、好奇按对象现算（不做 baseline）；玩心降为 trait/Affect；真正保留的连接欲/胜任欲/安全欲**建议归并进 SDT 三需要（+探索）**派生 Drive。
5. **Desire 必须对象化**：无对象欲望只能作 Mood/Drive 偏置；深链条 `Drive→Desire→Intention→Task` 每段都要有权威写者与闸门。
6. **「主动性的两个来源」**：Goal/Planner 可从外部目标产生主动（便宜、可审计）；自发兴趣需类似 Drive/内在动机（有失控成本）。建议分层：Drive 产候选（软）→ Cognition 选择 → Permission/Wake 闸门。
7. **BDI 是越级护栏范本**（E3）：intention 有生命周期与 reconsideration，但从不自动产生外部效应——故渊的 Intention→Task 应保留独立登记步骤。
8. **Active inference/EFE 仅作候选**：成本高、目标分布难设（[EFE-cost] 自承），不建议作默认；最多借「偏好先验 + epistemic value」概念。
9. **横向契约**：Drive/Desire/ActionTendency 一律 **derived 或 ledger + bias-only + 带 provenance**；每轮 Δ 不落盘，只在跨阈/被认领时形成持久事件（交 04/05 统一）。
10. **冲突裁决不设普适层次**：用上下文加权 + hysteresis + 显式冲突检测，最终交 Cognition（BDI option 选择）——不要用 Maslow 硬层次。

---

# 11. 证据索引

访问日期均为 2026-10-07。

**E3（源码核查，只读，未安装/未运行）**
- `jason-lang/jason`，main @ commit `2c2d7e1c1ea3bd5cef9712d551632ba367109741`；核查路径 `jason-interpreter/src/main/java/jason/asSemantics/{Agent.java, TransitionSystem.java, Intention.java, Option.java}`（`Agent.selectOption/selectIntention`、`Intention.State{running,waiting,suspended}`、`Option=Plan+Unifier+Event`）。
- `infer-actively/pymdp`，`pymdp/agent.py`（**main 浮动，未固定 commit**）：`Agent` 字段 `A/B/C/D/E`、`policies`、`gamma/alpha`、`H/I`，方法 `infer_states/infer_policies/sample_action`。

**E2（论文正文 / 官方文档）**
- Deci, E. L., & Ryan, R. M. (2000). *The "What" and "Why" of Goal Pursuits*. Psychological Inquiry 11(4):227–268. https://selfdeterminationtheory.org/SDT/documents/2000_DeciRyan_PIWhatWhy.pdf
- SDT 官方理论页（六 mini-theories）：https://selfdeterminationtheory.org/the-theory
- Carver, C. S., & White, T. L. (1994). BIS/BAS scales（作者页含量表）：https://www.psy.miami.edu/faculty/ccarver/bisbas.html
- Keramati, M., & Gutkin, B. (2014). *Homeostatic reinforcement learning…*. eLife 3:e04811. https://pmc.ncbi.nlm.nih.gov/articles/PMC4270100/ （drive `D(H)` 公式见正文 Eq.1–2）
- Loewenstein, G. (1994). *The Psychology of Curiosity*. Psychological Bulletin 116(1):75–98. https://www.cmu.edu/dietrich/sds/docs/loewenstein/PsychofCuriosity.pdf
- Herzig, A., Lorini, E., Perrussel, L., Xiao, Z. (2016). *BDI Logics for BDI Architectures*. KI. https://link.springer.com/article/10.1007/s13218-016-0457-5
- *The Rationale behind the Concept of Goal*（desire/goal/intention 统一为 outcome，norms 过滤）arXiv:1512.04021. https://ar5iv.labs.arxiv.org/html/1512.04021
- de Silva, L., Meneguzzi, F., Logan, B. (2020). *BDI Agent Architectures: A Survey*. IJCAI. https://www.ijcai.org/proceedings/2020/0684.pdf
- Aubret, A., Matignon, L., Hassas, S. (2019). *A survey on intrinsic motivation in RL*. arXiv:1908.06976. https://arxiv.org/abs/1908.06976
- Paul, A., Sajid, N., Da Costa, L., Razi, A. *On efficient computation in active inference*（自承计算成本高）. arXiv:2307.00504. https://ar5iv.labs.arxiv.org/html/2307.00504
- Heins, C., et al. (2022). *pymdp: A Python library for active inference in discrete state spaces*. JOSS. https://arxiv.org/abs/2201.03904
- *LLM Agents Beyond Utility: An Open-Ended Perspective*. arXiv:2510.14548. https://arxiv.org/html/2510.14548v1
- Lu, Y., et al. *Proactive Agent: Shifting LLM Agents from Reactive Responses to Active Assistance*. arXiv:2410.12361（ICLR 2025）. https://arxiv.org/html/2410.12361v3
- *The Feeling of Action Tendencies: On the Emotional Regulation of Goal-Directed Behavior*（Frijda action readiness 综述，PMC3246364）. https://pmc.ncbi.nlm.nih.gov/articles/PMC3246364/

**E1（公开简介 / 二手）**
- Murray, H. A. (1938). *Explorations in Personality*（经 Wikipedia「Murray's system of needs」转述 need/press/subsidation/fusion）. https://en.wikipedia.org/wiki/Murray%27s_system_of_needs
- Hull, C. L. (1943). *Principles of Behavior*（drive-reduction）. https://en.wikipedia.org/wiki/Drive_reduction_theory_(learning_theory) ；记录 https://psycnet.apa.org/record/1944-00022-000
- Maslow, A. H. (1943). *A Theory of Human Motivation*. https://psychclassics.yorku.ca/Maslow/motivation.htm
- Carver, C. S., & Scheier, M. F. control theory of self-regulation（书目/摘要级）. https://pmc.ncbi.nlm.nih.gov/articles/PMC6707771/
- Action tendency（BIS/BAS、approach/withdrawal、Frijda 概述）. https://en.wikipedia.org/wiki/Action_tendency
- Schwartz 基本价值理论（仅检索摘要）. https://en.wikipedia.org/wiki/Theory_of_basic_human_values
- Nocturne-Memory-Core（项目参考，9 维 Drive Ledger / Longing 参与 Attachment 闸门）. `memo/Nocturne-Memory-Core.md`

**E0（仅作线索，未采用为结论）**
- incentive motivation (Bolles/Bindra/Toates) 与 drive-reduction 批判的检索摘要；Pathak 2017 (ICM)、Burda 2018 (RND) 为经综述转述的引用（原始论文未直读）。

**未核实一览**
- Hull 1943 / Murray 1938 / Bratman 1987 / Frijda 1986 / Carver & White 1994 原文未直读（部分经二手或综述转述）。
- Elliot 层级模型细节、Schwartz 价值内容仅检索级。
- pymdp `main` 未固定 commit；Jason 为只读核查，未运行、未验证语义。
- Pathak/Burda 原始论文未直读（经 Aubret 综述引用）。
- SDT 各行实证效应量未逐一核对。

---

> **本报告完成状态**：覆盖两类以上不同来源（心理学理论 + AI 工程/代码，含两条 E3 源码线索）；主要结论 E2；对旧 10 维不回避冲突（疲惫/想念/独处欲/玩心判为分类错误）；给出具体接口候选、运行成本与失败模式；可进入下一轮整体组合，未定 schema。
