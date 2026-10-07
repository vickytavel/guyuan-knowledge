# memory-affect-01：记忆 × 情绪系统七专题并行调研

> 创建日期：2026-10-07。状态：待分窗执行。性质：研究任务卡，不是设计定稿、实施计划或 Serein 改造卡。
> 本卡把新记忆系统、情绪系统及二者关联机制的前置研究拆成七个可并行专题。各窗口用统一证据口径和统一报告格式，最后由主窗口读取七份结果做横向汇总与组合方案。

## 1. 总目标

回答的不是哪个记忆项目排名第一，而是：经历怎样成为可追溯、可演变、可修订、带情绪意义的长期内部结构；这些结构怎样支持未完成事项、人物/共同理解、持续活动，并在之后被整理、召回和使用？

本轮以机制为主要选择对象，项目、论文、代码库是机制证据和可复用实现的来源。允许最终结论是多个来源组合，不强求一个全包系统。

明确边界：
- 不以 Serein 已有 schema、Arc、Event 分类或现有实现作为新系统上限。
- 本轮不先盘点 Serein 可复用部分；Serein 留到组合方案形成后再做实现与迁移对照。
- 不修改产品代码，不接日常 Serein，不迁移数据，不选最终 schema。
- 不安装或运行候选项目，不启动服务，不发模型请求，不做部署或基准。
- 不把论文模型、README 宣传或概念图自动当成可运行能力。
- 不预设记忆与情绪必须同库、必须图数据库、必须向量库或必须由 LLM 全自动维护。

## 2. 每个窗口共同先读

1. D:/48314/Ithaca/AGENTS.md
2. D:/48314/Ithaca/交接文档-真实活动与记忆驱动.md
3. D:/48314/Ithaca/docs/requirements/记忆与情绪系统-参考调研范围-2026-10-07.md
4. D:/48314/Ithaca/memo/记忆管线架构.md
5. D:/48314/Ithaca/memo/Nameless-Coastline-记忆系统.md
6. 本任务卡。

两份 memo 是候选架构与问题来源，不是定稿。除非本专题的外部资料直接需要比较，否则不要专门调查 Serein，不要把时间花在证明它已有基础功能上。

## 3. 七个并行专题

每个窗口只认领一个专题，只写对应报告。不要修改其他专题文件、当前交接、共享规格或产品代码。

| ID | 专题 | 核心问题 | 输出文件 |
|---|---|---|---|
| 01 | 记忆本体与数据模型 | 一次经历到底存成什么？证据、事件、事实、理解、偏好、约定、反思如何共存而不互相覆盖？ | docs/requirements/memory-affect-01/01-memory-ontology.md |
| 02 | 连续事件线与状态演变 | 同一件事怎样从起点、推进、转折到当前状态？叙事演变与当前状态机怎样分工？ | docs/requirements/memory-affect-01/02-event-evolution.md |
| 03 | 未完成事项与持续关切 | 怎样识别这件事还没结束、在等待或受阻，又不擅自规划主体接下来要做什么？ | docs/requirements/memory-affect-01/03-open-loops.md |
| 04 | 情绪生成与内部状态 | 经历为何产生某种情绪？appraisal、短期 emotion、较慢 mood/attitude 如何更新、累积、衰减？ | docs/requirements/memory-affect-01/04-affect-appraisal.md |
| 05 | 记忆 × 情绪双向耦合 | 经历怎样改变情绪；情绪怎样影响注意、写入、巩固、检索与后续活动；态度变化怎样保留历史依据？ | docs/requirements/memory-affect-01/05-memory-affect-coupling.md |
| 06 | 人物模型、世界书与共同理解 | 事实、对人的理解、偏好、关系认知、共同约定、共同语境如何表达、修订和溯源？ | docs/requirements/memory-affect-01/06-world-person-model.md |
| 07 | 整理、召回与运行工程 | 上述结构怎样增量整理、聚类/连线、召回、注入、反馈和后台运行；成本与可维护性如何控制？ | docs/requirements/memory-affect-01/07-consolidation-retrieval-runtime.md |

### 01 记忆本体与数据模型
重点：episodic / semantic / autobiographical memory；provenance；event sourcing；immutable evidence + derived views；一源多视图、多投影、多重归属；belief/fact/interpretation 分离；supersession/revision/contradiction。
必须回答：原始证据、事件、派生事实、当前理解分别是什么对象；同一经历能否多投影；后来的理解怎样不改写过去；哪些可重算、哪些必须不可变；一源多视图是否有成熟理论或工程对应物。

### 02 连续事件线与状态演变
重点：event graph、temporal knowledge graph、narrative memory、longitudinal memory、process/lifecycle/state transition、event coreference/linking、trajectory/milestone/turning point。
必须回答：多次推进怎样识别为同一条线；起点/转折/当前状态/结局如何表示；叙事线与事项状态机是否分开；一事件多线；错误归线与拆线如何可逆；理解变化怎样表达。

### 03 未完成事项与持续关切
重点：open loops、unfinished intentions、prospective memory 的待续部分、commitment/obligation/pending state、suspended/waiting/blocked/unresolved、task memory 与 conversational memory 边界。
必须回答：什么证据足以判未完成；怎样避免把兴趣变成计划；waiting/blocked/paused/abandoned/resolved 等状态如何表达；怎样关联事件线、承诺、外部等待与真实任务；什么只应成为记忆，什么可成为唤醒候选。

### 04 情绪生成与内部状态
重点：OCC、Scherer CPM 等 appraisal theory；valence/arousal/dominance；goal/belief/expectation/relation-based appraisal；emotion vs mood vs attitude；affect dynamics、decay、accumulation、regulation；FAtiMA 等实现。
必须回答：事件到意义评价到情绪变化需要哪些输入；哪些状态快、哪些慢；情绪是否保存原因与依据；矛盾情绪和长期关系态度怎样处理；旧 25 维哪些思想有价值但不是前提；哪些理论能落成接口。

### 05 记忆 × 情绪双向耦合
本轮重点专题之一。不要把给记忆加 emotion tag 当成完成。
重点：affective memory、mood-congruent/dependent memory、emotional salience、emotion and memory consolidation、affective memory updating、appraisal-driven memory、attitude/belief updating，以及近年的 LLM/agent affective memory 论文、基准、开源实现。
必须分别研究：记忆到情绪更新；情绪到注意/写入/巩固；情绪到召回；长期态度变化到当前理解但历史仍可追溯。
还必须回答：情绪参与召回应硬过滤还是软信号；如何避免 salience 自我放大；同一对象态度演变怎么存；稳定引用、事件通知、共享状态、同库强耦合各有什么代价；有哪些真正测情绪记忆更新的基准。

### 06 人物模型、世界书与共同理解
重点：user/person model、preference evolution、common ground、shared knowledge、belief store、relationship memory、belief revision/truth maintenance、provenance-aware profile/world model。
必须回答：用户曾说、当前偏好、AI 当前理解、双方共同约定是否是不同对象；事实/推断/反思/共同约定的来源与置信度；偏好变化后旧偏好如何保留又不干扰当前；世界书哪些常驻、关键词触发或按需检索；单方理解与共同理解如何分层。

### 07 整理、召回与运行工程
本专题建立在前六类需求之上，不用检索技术反向决定本体。
重点：consolidation、reflection、schema induction、clustering、graph community、event linking、contradiction/supersession/merge/split、lexical + vector + graph + temporal retrieval、episode vs line retrieval、reranker/judge、gating、context budget、feedback/evaluation、shadow mode、incremental jobs、idempotency/recovery、VPS 与维护成本。
必须回答：哪些整理可确定性做、哪些要模型、哪些只产候选；实时/收口/夜间/批处理怎样分工；召回相关性/排序/席位是否分层；情绪/时间/状态在哪层参与；怎样区分没召回、召回错、召回了但没用；怎样控制 LLM 与后台复杂度。

## 4. 统一研究方法

候选来源至少覆盖理论/论文、开源实现、成熟工程材料中的两类，适用时尽量三类都有。优先论文全文、作者项目、官方源码、官方文档与正式发布记录。二手内容只用于找线索。

每专题先广搜，再收敛到约 4–8 个真正相关候选；深入核查约 3–5 个。不要做项目大全，也不要为了数量补弱候选。

证据等级：
- E0 线索：搜索摘要、二手提及，尚未核实。
- E1 公开简介：README、项目首页、论文摘要。
- E2 正式材料：论文正文、官方文档、设计说明、发布说明。
- E3 源码核查：固定版本或 commit 下查看相关实现路径。
- E4 运行验证：实际安装/运行/基准。

本轮默认最高做到 E3。E4 未授权，不执行。代码候选尽量记录仓库、版本/tag/commit、许可证、语言/依赖、核心路径；无法固定时明确写浮动 main 或未固定。

共同区分：
- 过去事实 ≠ 当前理解。
- 用户情绪识别 ≠ AI 自身情绪。
- 事件带情绪标签 ≠ 情绪系统。
- 事件归线 ≠ 未完成事项管理。
- 未完成 ≠ 自动规划未来。
- 兴趣/愿望 ≠ 承诺/任务。
- 记忆被召回 ≠ 记忆被实际使用。
- 模型输出候选 ≠ 事实已生效。
- 有代码 ≠ 适合部署。
- 理论合理 ≠ 工程成本可接受。

## 5. 七份报告统一格式

每份报告都保留下列一级结构，章节内可以扩展：

1. 故渊要解决的问题：具体需求、本专题边界、与其他专题接口、明确不解决什么。
2. 关键概念与必要区分：术语、理论分歧、容易混淆的能力。
3. 候选总表：候选、类型、解决什么、核心机制、代码/实现、证据等级、版本/年份、初步适配。
4. 重点候选深查：核心机制；数据/状态结构；更新机制；来源与可追溯性；自动化与模型调用；成熟度/许可证/依赖；吻合处；不吻合处；未核实项；证据。
5. 对故渊的可用性拆分表：可直接复用代码 / 可借鉴机制 / 我们需要补 / 不建议采用 / 未核实。
6. 跨模块接口候选：输入、输出、读取的稳定对象、产生/更新对象、对外事件/信号、必须保留的来源/依据、哪些只能软影响不能硬决定。
7. 风险、失败模式与开放问题：幻觉、误归因、状态漂移、反馈循环、自动整理误伤、隐私/可见性、模型调用、后台成本、迁移/重算成本、待验证问题。
8. 本专题结论：A 建议进入组合设计；B 值得借鉴但不直接采用；C 保留候选需验证；D 当前不建议。推荐机制，不只排项目名次。
9. 给最终汇总窗口的输入：最多 10 条，写最值得带入总架构的机制、跨专题连接点、最重要冲突和需共同决策的问题。
10. 证据索引：一手来源、版本/commit、访问日期、证据等级。

## 6. 统一横向比较维度

各报告尽量覆盖：需求覆盖度、来源可追溯性、历史与当前状态分离、时间与演变表达、情绪关联、可解释性、错误可逆/可重算、自动化与人工负担、跨模块耦合、LLM 调用成本、存储/索引/VPS 成本、运维维护成本、实现成熟度、渐进实施能力。

可以写强/中/弱/未知或 1–5，但不要机械加总分。最终决策以关键短板、机制取舍和组合为主。

## 7. 并行写入与冲突规则

- 每个窗口只写自己的专题输出文件。
- 不写当前交接，不改本任务卡，不改其他专题报告。
- 不共同编辑一个总报告；总报告留给七份完成后的主窗口。
- 需要研究快照时，分别使用 D:/codex/research/memory-affect-01/01-memory-ontology/ 等对应独立目录。
- 同一候选被不同专题重复研究没关系，各自从自己的问题角度研究。
- 发现明显属于别的专题的重要资料，在本报告第 9 节留下跨专题线索，不替另一个窗口定结论。

## 8. 最终汇总阶段：本卡暂不执行

七份报告完成后，主窗口统一读取并形成：
1. 需求—机制矩阵。
2. 不同专题建议之间的数据模型、更新权威、自动化程度冲突矩阵。
3. 机制来源表：论文、现成代码、自建。
4. 事件、派生理解、事项状态、情绪、世界书、召回之间的信息流草图。
5. 2–3 套组合架构：轻量、平衡、完整。
6. 每套方案分别写获得什么、牺牲什么、复杂度来源、待验证假设。
7. 用户选方向后，再进入 schema/接口设计、Serein 差异对照、迁移/复用评估和窄验证。

最终汇总优先选机制组合，不做 GitHub 项目排行榜。

## 9. 操作边界

允许读取项目文档和 memo、浏览公开网页/论文/官方仓库、只读源码、保存必要研究快照、写本专题报告。

禁止修改故渊产品代码；连接/读取/测试日常 Serein；安装/运行候选系统或数据库；发真实模型请求；SSH/VPS 操作；修改生产配置；部署、合并、提交或对外发消息；将研究建议自动升级为用户决定。

## 10. 单专题完成标准

- 已说明真正问题，不被 Serein 现状限制。
- 有多个不同来源或不同机制候选，而非只看一个热门项目。
- 核心候选至少 E2；代码候选的重要工程结论尽量 E3。
- 明确区分可复用代码、可借鉴机制、需要补、未核实。
- 有跨模块接口候选，不停在理论介绍。
- 有失败模式、成本和可逆性分析。
- 最终推荐机制而不只是项目名。
- 报告能直接进入主窗口横向汇总。

## 11. 当前状态

- 2026-10-07：用户确认拆成七个并行窗口，统一格式后由主窗口最终汇总。
- 本轮不先围绕 Serein 做能力盘点；新系统需求定义独立于其现状。
- 记忆管线架构与 Nameless Coastline 已由主窗口读取，作为候选机制与缺口参考。
- 七专题尚未执行；尚无架构选择、schema 定稿、实现或运行验证。