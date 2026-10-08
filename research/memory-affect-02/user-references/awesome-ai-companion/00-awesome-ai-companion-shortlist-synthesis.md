# Awesome AI Companion 定向筛选：三域 shortlist 与机制综合

> 核查日期：2026-10-07（Asia/Shanghai）。  
> STATUS: CANDIDATE / REFERENCE SYNTHESIS。推荐供讨论，不是架构决定、实现授权或运行验收。  
> 执行：按用户指示，由三个 GPT-6 Luna（high）子代理独立筛选 Memory / Identity、Affect / Emotion、Motivation / Drive；主窗口复核、去重并综合。各域未交流 shortlist，也未读取开放式第二轮研究产出。  
> 来源范围：任务卡指定的 [Awesome 中文目录固定快照](https://github.com/DasterProkio/awesome-ai-companion/blob/81bc98bf94521ce5761d1ee1db59409a55e99c0b/README.zh-CN.md)，commit `81bc98bf94521ce5761d1ee1db59409a55e99c0b`；17 个记忆/身份条目与 9 个情绪/驱动条目，共 26 个独立条目。Awesome 只作 E0 入口。

本轮最值得继续对照的是 **Kin Mind 的来源与状态生命周期、DuduLove 的身份/来源治理、emotion-system 的情绪观测与重算链**。这三者分别回答不同问题，不能拼起来就宣称故渊架构已经完成。Chord 规范仅进入表示层讨论；Drivesoid 主要保留局部动力学与意图生成反例；WrenWen 继续引用既有 E2 报告。

本轮按全量粗筛→少数深挖完成。8 个项目的局部机制查到固定源码 E3；chord-affect-anchors 最高 E2；WrenWen 只复用既有 E2 报告，没有重做。未安装、运行候选或调用真实模型，没有 E4；官方材料中的生产效果、测试数和心理效度不算本轮验收。

## 1. 阅读入口与三域 shortlist

- [Memory / Identity：17 项粗筛及深挖](./01-memory-identity-screen.md)
- [Affect / Emotion：9 项及允许跨域补项](./02-affect-emotion-screen.md)
- [Motivation / Drive：9 项及允许跨域补项](./03-motivation-drive-screen.md)
- [WrenWen 既有参考报告](../WrenWen-reference-report.md)：本轮沿用 E2，不重复分析。
- [当前候选框架入口](../../../memory-affect-01/README.md)、[00c](../../../memory-affect-01/00c-长期认知系统-整体架构候选框架-2026-10-07.md)、[00b 参考地图](../../../memory-affect-01/00b-记忆情绪系统-研究参考地图-2026-10-07.md)。本轮均只读。

| 领域 | Top 3：继续逐机制对照 | Next 2：局部机制 | 推荐的实际含义 |
|---|---|---|---|
| Memory / Identity | DuduLove、Kin Mind、Serein | Moraine、Paramecium | 身份和来源准入；经历/关切/自我判断的修订；原话绑定与叙事材料治理。不推荐整套沿用 |
| Affect | emotion-system、chord-affect-anchors、Kin Mind | Drivesoid、Ombre-Brain | 观测与依据复核、表示层、状态与派生视图。Chord 只对表示问题强匹配，不是完整情绪引擎 |
| Motivation | Kin Mind、WrenWen、Drivesoid | emotion-system、Ombre-Brain | 有来源的愿望生命周期；抽象意图与唤醒分权；pending/outcome 的局部对照。Drivesoid 的随机意图生成同时是反例 |

各域名单是阅读优先级，不是整体采用排名。尤其不能将“列入 Top 3”解释为其本体、权限和主动行为都已适配故渊。Kin Mind 在三域重复出现，综合阶段合并为一个项目、保留三个不同问题面，不形成三份重复的采用建议。

## 2. 跨域矩阵

强/中/弱/无表示**本轮读到的相关机制覆盖程度**，不是可靠性、性能或适配分数。“无”表示本轮未见对应机制证据，不证明仓库绝无该功能。Self 指自我理解/修订，稳定身份认证本身不等同 Self Model。Runtime 指本轮相关的持久状态处理和宿主接线，不是通用执行框架评测。

| 项目 | Memory | Self | Affect | Motivation | Wake | Runtime | 最值得借 |
|---|---|---|---|---|---|---|---|
| Kin Mind | 强 | 强 | 强 | 强 | 中 | 强 | 依据绑定、经历去重、Concern/Wish 生命周期、修订失效、状态与派生视图 |
| DuduLove Memory | 强 | 中 | 无 | 弱 | 弱 | 中 | 可信主体归属、服务端来源核验、拒绝防复活、召回分路与会话冷却 |
| Serein | 强 | 弱 | 弱 | 弱 | 弱 | 中 | 原话引用、Event/Scene/Arc 生命周期、材料漂移校验与预览后保存 |
| Moraine | 强 | 中 | 无 | 无 | 无 | 中 | 显式证据成员的待审经历线、提案/权威层分离、版本检查与回滚 |
| Paramecium | 中 | 无 | 无 | 无 | 无 | 中 | 原文全文检索与精确回源；摘要/向量不替代原始材料 |
| emotion-system | 弱 | 无 | 强 | 弱 | 中 | 中 | 未观测与否认分开、变化依据复核、版本化判读答案与确定性重算 |
| chord-affect-anchors | 弱 | 无 | 中 | 无 | 无 | 无 | 具体情境加情绪行进序列；仅规范 E2，表示层限定 |
| Drivesoid | 弱 | 无 | 强 | 中 | 弱 | 中 | 快慢态回落、强偏差向 mood 巩固、习惯化；无对象随机 intention 作反例 |
| Ombre-Brain | 中 | 中 | 中 | 弱 | 无 | 中 | 历史记忆情感标注、Feel/Plan/Self 分工；召回自强化风险须单独处理 |
| WrenWen（既有 E2） | 强 | 强 | 中 | 强 | 强 | 中 | 抽象 Intent→Capability、真实来源念头、Wake 分权、Self 跨时间认领 |

WrenWen 的强项按既有官方文档评价，不能与其他项目的 E3 实现证据混用。DuduLove 的 Wake 相关材料限于活动简报/awakening 消费；Drivesoid 提供状态和宿主轮询建议，未由本轮证据证实独立 wake sender；emotion-system 的缺席阈值是联系机会，不是完整 Wake policy。

## 3. 与 current 的差异：真冲突、术语不同、架构影响

### 3.1 真冲突或明确的适配障碍

| 机制 | 冲突依据 | 可保留部分与边界 |
|---|---|---|
| 无对象、无依据的随机 intention | Drivesoid `maybeRollIntention` 生成项仅带 ID/创建/到期时间；数值越线后概率产生，不能对应来源明确的 Desire | 保留 pending→满足/拒绝/过期的结算思路；增加对象、依据和主体认领是故渊适配提案，原代码没有替我们实现 |
| Recall 触碰影响长期权重或存活 | Ombre 的 activation/touch 与排序、衰减耦合；Paramecium 的 README 也指出 access 热度遗留；WrenWen 既有报告提出 actual-use 强化 | 不继承“出现次数→生活重要性”。若研究 utility，另行定义检索效用，并观察反馈环；真正使用也不等于事实更真 |
| 当前数值被当成历史感受 | 持续 snapshot、bucket 情感标签和每轮判读各有不同粒度，不能用当前值重写当时记录 | 历史 Annotation 保留发生/判读时的来源与版本；当前状态只影响当前消费。未找到一套可直接替代故渊全部历史语义的实现 |
| 未确认自我判断直接当稳定人格 | Kin `traits.py` 明确 proposed trait 已可起作用，establish 才表示独立经历支持；其作用面未由本轮全量追完 | 可比较 Fluid/Evolving Self 的软影响，但不得未经裁决等同 Stable Self 或高权威事实 |
| 身体/角色关系剧本成为默认动力 | Eventide/Tidefall 是身体模拟；Drivesoid 和 emotion-system 也含特定身体/亲密/关系规则 | 当前新核心已退出身体模拟；只保留抽象状态演变或来源处理，不照搬身体变量或关系脚本 |

共享 SQLite、同一事务或同一次提交同时携带 Affect 与 Motivation **本身不构成本体冲突**。Kin 的风险要看维度含义、更新者及作用权限，不能仅因 `AffectiveEvent` 带独立 `motivations` 字段就推出必须分库、分事务。Drivesoid 则确实把身体、关系、情绪和欲望混在一组维度中；不照搬其统一维度表。

### 3.2 只是名称或边界不同，不能按词直接映射

- Kin 的 `Motivation.target` 是 0–100 的**数值目标值**，不是 Desire 指向的对象；愿望对象化来自 content/topic/kind 等描述。
- DuduLove 的 Resident 是持久主体身份；它的 Identity lane 是上下文组装策略，二者均不自动成为故渊 Self Model 的演变层。
- DuduLove 的 `open_loop` 是已记录的 lifecycle；Kin Concern/Wish、Ombre plan、Serein Arc 又有自己的职责。不能分别按词替换故渊 Concern、Commitment、Intention 或 Task。
- Moraine experience thread 是显式成员、待审且可重建的视图；Serein Arc 是材料绑定和正文修订流程。它们支持 Narrative Line 的机制讨论，但没有自动证明故渊连续归线目标已完成。
- Dataojitori/nocturne_memory 是本轮目录里的 URI/图记忆项目；不能与本地 `memo/Nocturne-Memory-Core.md` 因同名混同。

### 3.3 ARCHITECTURE IMPACT：留下裁决问题，不改 current

| 待裁决面 | 为什么可能改变架构或契约 | 本轮建议 |
|---|---|---|
| Current Chord 与历史音乐锚 | chord-affect-anchors 写“情境+和弦行进”；00c 的 Chord 是整体 Affect 状态投影，输入/时间粒度不同 | 先比较状态投影和历史 Annotation 两个用途；不因名字相同合并，不新增第二套本体 |
| Mood 是权威状态还是派生视图 | Drivesoid 持有慢态；Kin 派生 undertone/lingering；emotion-system 主要是每维 baseline 回落 | 等开放式动力学研究后比较更新、保存和重算职责；不把某项目三时间尺度直接定为 schema |
| Self candidate 的作用权限 | Kin proposed 已影响当前，WrenWen 跨时间转正，DuduLove 有身份推断准入 | 明确候选能影响什么、何时可常驻、怎样撤回；主体认证与认识论确认分别处理 |
| 拒绝与来源更正的依赖传播 | DuduLove 拒绝正文/来源并阻止普通整理重入；Kin 来源变化会使依赖视图待复核 | 将问题挂到现有 provenance/supersession/Consolidation 边界，别只做 UI 隐藏或统一删除 |
| Motivation 与 Wake 分工 | WrenWen 文档明确分权；Kin 记录复核原因；其他项目常把阈值接联系机会 | 区分候选方向、复核原因、唤醒时机与行动许可；具体服务/调度接口仍待设计 |

## 4. 相对 00b 参考地图新增的机制线索

“新增”只表示在本轮逐项对照的 00b 中未找到这种具体机制，不是宣称所有旧资料从未讨论过。以下都是 candidate，不新增 canonical 对象。

| 新线索 | 固定依据与等级 | 与既有原则相比新增了什么 |
|---|---|---|
| 可引用来源不等于可信来源：宿主签发独立凭据 | DuduLove [`sourceAuthority.js`](https://github.com/VITASID57/dudulove-memory/blob/2ea7c7a856133d5d693497586e2a28f3bd026869/memory-core/sourceAuthority.js)，E3 | 模型可见 sourceRefs 与服务端 opaque grant 分开；不是让模型自行填 trusted 字段。可用于把 provenance 原则落实到工具准入 |
| 拒绝后防重入，而非仅退出当前召回 | DuduLove 的 admission/provenance/journal，详见 Memory 报告 E2/E3 | 相同正文/来源和依赖派生的后续入库也受约束；来源仍能用于审计，避免后台整理把已否决内容重新写回 |
| 按“阅读目的”控制配置、例子与经历的混入 | 主窗口补核 Kin [`read_policy.py`](https://github.com/mycyg/kin-mind/blob/b9704b3f95d5a821ec0c2e688e0c98d6ce26ad81/src/eventmem/core/read_policy.py) 及 [`architecture.md`](https://github.com/mycyg/kin-mind/blob/b9704b3f95d5a821ec0c2e688e0c98d6ce26ad81/docs/architecture.md)，E3/E2 | experience_recall、self_knowledge_view、audit 对不同材料类别有不同准入；配置和合成示例不是“亲历事实”。这扩展的是消费规则，具体分类及用户配置请求例外不能原样继承 |
| 自我特征的支持次数按独立经历去重 | Kin [`traits.py`](https://github.com/mycyg/kin-mind/blob/b9704b3f95d5a821ec0c2e688e0c98d6ce26ad81/src/kin_mind/traits.py)，E3 | trait/episode/polarity 有唯一约束，同一互动的多条消息不算多次独立支持；支持与反证、撤回与墓碑分别保存，参数与晋升次数不继承 |
| 显著变化才补问，判读和演化分别重算 | emotion-system questions/engine/pipeline，详见 Affect 报告 E3 | 未提及、否认、没有不同；第二阶段询问对象/证据/新颖性/提示回声；答案保存题库与引擎版本，换规则可重放。不是把生成的可见思考当真实心理测量 |
| 快慢态之间以偏差强度门控巩固，重复刺激习惯化 | 主窗口及 Affect 域核查 Drivesoid [`worker.js`](https://github.com/A1batr055/Drivesoid/blob/91ff6117c372355029eacd10cabadd0d4b123f38/src/worker.js)，E3 | base-mood 偏差影响慢态追随速度，同标签近期重复降低增量；比“存在 mood/有衰减”更具体，公式和数值有效性未验 |
| 高影响提案绑定版本并需另一角色复核 | Moraine [`governance.py`](https://github.com/ceniran/moraine-home/blob/05fa97d4eab4420267f57731745ef5aa3dcc2791/src/moraine/governance.py)，E3 | 提案保存 expected_version，陈旧版本拒绝；部分高影响变更需另一个角色签名。函数只返回副本和回滚记录，不自行持久化；可信角色认证与原子 adapter 仍需实现 |

以下只记为**额外支持**：原始 Evidence 不被摘要替代；派生结构能回源；叙事线不等于任务；Concern 不因沉默就已解决；愿望/念头来自真实材料；历史被取代不静默删除；Wake 与执行分权；Chord 是表达候选；召回不能自动提高 semantic importance。它们已在 current/reference map 中有对应方向，不包装成这轮新对象。

## 5. 接线、维护和资源边界

| 项目/机制 | 静态可见的额外接线成本 | 本轮未验 |
|---|---|---|
| Kin Mind 的跨域闭环 | 依赖其 SQLite、来源版本、revision/事务、评估队列、宿主回执与状态投影；抽取机制也需故渊承担这些契约 | 完整移植工作量、恢复可靠性、模型评估稳定性、VPS 内存/CPU、并发容量 |
| DuduLove 的身份/准入/召回 | 在可信适配层绑定主体和来源，再接自有对象/权限与上下文预算；不可由模型自报 principal | 跨入口认证、并发写入、具体框架与真实个人记忆的接线 |
| emotion-system | 需要可引用的回复材料、外部判读服务或替代实现、状态/答案保存、回放和故障降级；纯计算不负责业务持久化 | 判读质量、成本/延迟、跨模型稳定性、长期闭环及回声抑制效果 |
| Chord 文本锚 | writer/reader 接线少；主要工作在来源契约、表示含义、模型一致性和质量评估 | 作者 pilot 没有构成本轮消融或效果证明；不能据此选择底层动力学 |
| Drivesoid | 侧车、分类调用、事件供给、状态读取、时间驱动与运行配置都需宿主接入；Wake sender/策略另做 | 状态曲线与分类有效性、运行成本、独立 Wake/Permission/Task 适配 |
| Moraine / Serein / Paramecium | 分别是候选审阅/保存、叙事预览与来源校验、原文捕获/检索的机制；都需要适配故渊自有事件/任务职责 | 自动化程度、完整证据保存、原子提交、部署和现有服务的真实接线 |

上述成本判断基于静态依赖，是推断，不是实测工时或资源预算。未替故渊选择 harness、数据库、情绪维数、固定阈值或模型路由。外部许可证如考虑复用再按实际复用范围核查，本轮没有复制产品实现或作许可裁决。

## 6. 下一步分类

### 立即值得另写独立 reference report（推荐，尚未开始）

1. **Kin Mind**：合并三个问题面，聚焦来源/独立经历、Concern/Wish/Self 生命周期、状态投影与执行回执；明确每个作用权限。不要再写三份互相重复的完整项目介绍。
2. **DuduLove Memory**：聚焦可信身份/来源准入、拒绝依赖传播、identity 与 association/evidence 消费分路。尤其区分主体身份连续性与自我认知演变。
3. **emotion-system**：聚焦未观测/否认、新事件/提示回声复核、保存判读与重算；作为 Affect 工程参考，不接身体或亲密脚本。

这里只列研究建议，本卡完成后不自动开启这些任务。WrenWen 已有独立报告，继续引用。

### 等开放调研结果后一起比较

- **chord-affect-anchors**：与 Chord 表示/Annotation 粒度研究合流，保持纯规范 E2。
- **Drivesoid**：只比较 mood 动力学和意图结算；随机无对象 intention 留作反例。
- **Moraine**：比较自动归线与候选审阅程度、authority store 与派生线、提案的版本/权限。
- **Serein**：保留已查原话/Arc 材料治理；不据本轮局部机制反向决定用户日常实例的保留、扩展、迁移或路由。
- **WrenWen**：用既有 E2 报告比较 Thought Pool、抽象 Intent 与 Wake 分权，不重复分析。

### 只留线索

- **Paramecium**：原文回源规则已经足够清楚，暂不为其窄层扩写完整架构报告。
- **Ombre-Brain**：情感 metadata、Feel/Plan/Self 分工和召回反馈风险；不将 V/A 标签当 current Mood。
- **Memory Constellations、rolling-memory、nocturne_memory、livingmemory**：分别保留归线、近期连续性、图/回滚、生命周期线索，见粗筛停止依据。
- **OmniDimen-Emotion**：识别/生成模型组件线索；若以后需要 classifier，再单独评估，不列入完整 Affect 系统。

### 排除本轮系统候选

- **Eventide/Tidefall**：身体模拟整体退出当前新核心；技术类比不重开路线。
- **pilulier**：单轮 UI/prompt 生命周期，非内部 Affect/Motivation 系统。
- **dreams**：生成梦境不能当已发生经历；本轮未见可替代持续情绪/愿望模型的证据。
- **astrbot_plugin_self_learning**：群聊风格/黑话学习，不作为主体自我认识演变的系统候选。
- **omemo、Aelios、kiwi-mem、ai-memory-gateway/Pawwake、imprint-memory、ai-companion-cot-emotion**：按本轮 README 粗筛未命中足够独特机制，停止当前深挖；这不是对整个项目质量或未来价值的否定。

## 7. 固定版本与主窗口复核范围

| 深挖项目 | 固定 commit | 证据边界 |
|---|---|---|
| Kin Mind | `b9704b3f95d5a821ec0c2e688e0c98d6ce26ad81` | 局部机制 E3，完整恢复/生产效果未验 |
| DuduLove | `2ea7c7a856133d5d693497586e2a28f3bd026869` | 准入、provenance、组装/冷却局部 E3 |
| Serein | `abd28f2ec800a317dfcaf1efd9c43a10f1a76c87` | 原话/Scene 实现 E3；叙事流程有 E2；非日常实例审计 |
| Moraine | `05fa97d4eab4420267f57731745ef5aa3dcc2791` | thread、governance、projection、ledger 局部 E3 |
| Paramecium | `7695d520e60bdbecc15086168d64321f2d1e039a` | FTS 原文/元数据及 gateway 局部 E3 |
| emotion-system | `25d3329e0892f275efd33f1162bca96683d71d32` | 判读/演化/记录结构局部 E3，未调用判读服务 |
| Drivesoid | `91ff6117c372355029eacd10cabadd0d4b123f38` | state/worker/classifier 局部 E3，宿主 Wake 接线建议非实现证明 |
| Ombre-Brain | `115d831d979bf67efc7150f976e2cb34c333c935` | plan、ranking/decay 局部 E3 |
| chord-affect-anchors | `cf33b9b49b0f4430bf683683cf53a811b3de9100` | 官方规范 E2，无可运行源码 |
| WrenWen | 仅复用既有报告，本轮不取新版本 | E2，作者描述的运行经验不是本轮验收 |

主窗口阅读三份完整报告和 current 的 00b/00c，对以下证据作了局部抽查：Kin `architecture.md` / `read_policy.py` / `state.py` / `traits.py`；DuduLove `architecture.md` / `principal.js` / `sourceAuthority.js`；Moraine `governance.md` / `governance.py`；emotion-system `pipeline.js` / `engine.js` / `missing.js`；Drivesoid `README.zh.md` / `state.js` / `worker.js`；Paramecium `build-fts.py`；Serein `narratives.md`。其他深挖证据由相应领域报告记录，主窗口没有声称逐行重读全部实现。

复核纠正了 Kin 核心机制仅由配置文件支撑、proposed trait 作用边界、数值 target 与对象混同、FTS 存正文的表述、Moraine E2/E3 残留、Nocturne 同名混同、Drivesoid 宿主唤醒建议与实际实现的混用，以及部分固定文件链接。文档检查范围与结果见任务卡；它不替代产品测试或候选运行验证。

**汇合边界：** 本轮独立完成指定参考库筛选，没有将开放式研究名单喂给子代理或读取其研究结论。后续才执行 Open research + User-curated screening → Synthesis → Architecture Discussion；本文件只是用户参考路线内部 synthesis，不冒充两路最终架构汇总。
