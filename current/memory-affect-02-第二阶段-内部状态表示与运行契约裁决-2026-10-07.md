# 第二阶段：内部状态表示与运行契约裁决

> 日期：2026-10-07（Asia/Shanghai）
> 性质：**裁决/契约层文档**。2026-10-07 本窗口接手已存在的 §10 补充，复核横向治理并按用户授权吸收成熟结论到 `memory-affect-01/00b`、`00c`。不含 schema 实现，不改产品代码或配置。
> 本次范围与验收：补记/核对 OPEN-4、OPEN-6；梳理最小 authority 职责与七组影响；先保存 checkpoint，再文档复核与 current 吸收，形成三套运行复杂度候选后停止。验收为决定来源可追溯、状态词不跨级、current 无相关冲突、入口/链接可用；不是产品测试。
> 输入：memory-affect-02 五专题报告（01–05）｜《第一阶段·本体边界与迁移仲裁》｜《00b·四候选外部实现压力测试》｜《Chord-affect-anchors 来源追溯》（固定 SHA `cf33b9b49b0f4430bf683683cf53a811b3de9100`，来源最高 E2，无实现源码）
> 边界：本轮未运行、未接线、未改任何代码或 yaml；候选实现的 E3 均为固定 commit 只读复核。
> 标注约定：`[理论]` 有理论/文献支持｜`[工程]` 工程惯例或某实现里的一个取值，**基本等于"某个人拍的"**｜`[待标]` 必须实验或观测才能定。**冲突 ID 沿用第一阶段 C1–C9；本阶段裁决 ID 用 A/M/N/T/P/R/X 前缀。**

---

## 0. 本轮位置

| 阶段 | 问题 | 产出 |
|---|---|---|
| 第一阶段 | 有什么对象、旧 25 维怎么迁、哪两处真冲突 | 本体边界表、迁移表、冲突仲裁、信息流 v1 |
| **第二阶段（本文件）** | **状态用什么表示、慢层怎么存、目标值怎么定、两级时间怎么分、谁签 provenance、注入算到哪里、动机能否改情绪回落** | 裁决表、信息流 v2、Runtime/Persistence 契约、迁移更新表、待标清单、三方案分层 |

本阶段**新增**判断依据：chord-affect-anchors 追溯报告给出两条此前未纳入的硬边界——① `synthesis.py` 注释已写明"时间衰减后的值**读取时刻现算**、不由本模块落库"（旧代码层已存在 derived 先例）；② **Δ ≠ Chord 字符变化 ≠ 持久 Affect Change Event**，Chord 变化还受量化边界与映射表配置影响。第二条件直接改变了 §4 的触发结构。

---

## 1. 第二阶段裁决总表

状态：`ACCEPTED` 采纳｜`REJECTED` 否决｜`OPEN` 保留待裁。

### 1.1 Affect 状态表示（A）

| ID | 裁决项 | 结论 | 状态 |
|---|---|---|---|
| A1 | 三类方案取舍 | 采 **C 混合**：命名维度为运行本体；低维轴（V/A）只作确定性投影 | ACCEPTED |
| A2 | 低维轴作运行本体 | 否决。V/A 无法承载"这是委屈还是受挫"，无法回源到 cause | REJECTED |
| A3 | 低维轴作投影 | 采纳。且**是必需项**，不是美化：Chord 的 mode↔valence / tempo↔arousal、跨维统一阈值、检索软排序、外部接口都依赖它 | ACCEPTED |
| A4 | 维度数 | 不预设数量。判据改为：**必须有独立 baseline / half-life 才配独立成维**，否则合并 | ACCEPTED |
| A5 | D / dominance 作 Core Affect 第三轴 | 否决（功能性理由见 §2.1，不是"候选没人用"） | REJECTED |
| A6 | 掌控感 / 能动性 | 在**命名维度层**保留该类维度；在 **appraisal** 保留 control / power / coping | ACCEPTED |
| A7 | D 的加回条件 | 保留为**可证伪假设**：若出现"两个事件在命名维度上读数相同、但掌控感差异明显且改变下游选择"，则把 D 加回为**投影轴**（仍不是独立状态） | OPEN |
| A8 | certainty / novelty 归属 | 留在 **appraisal**，移出 Affect | ACCEPTED |
| A9 | Affect 侧的"不确定感/困惑" | 允许，但它是**命名维度**（感受，有 cause，会衰减），不是 certainty 的数值 | ACCEPTED |
| A10 | 旧 PADCN 的 P / A | 转成**投影轴**（derived），不再作为可写状态槽 | ACCEPTED |
| A11 | 旧 PADCN 的 C / N | 移入 appraisal（SEC 类） | ACCEPTED |
| A12 | 旧 PADCN 的 36h | 不作为任何默认值；进入待标清单 | ACCEPTED |
| A13 | V/A 投影权威（原 OPEN-6） | **Versioned Base Projection + slow-changing Subject Calibration**；显式配置迁移与主体慢变认领分开，不回写历史 | ACCEPTED（用户本轮重申，§10.2） |

### 1.2 Mood 的存在形态（M）

| ID | 裁决项 | 结论 | 状态 |
|---|---|---|---|
| M1 | 三形态取舍 | 采 **B：从快层派生的慢层** | ACCEPTED |
| M2 | A 独立持久 Mood 对象 | 否决：与各维 baseline 重复，且产生"谁是真源"的第二次真源问题（旧 36h 回弹已经真实发生过这一重复） | REJECTED |
| M3 | C 不设 Mood | 否决：丢掉 affect-as-information 的载体——"说不清原因但一直持续"没有落点 | REJECTED |
| M4 | 是否影响 Chord / retrieval / appraisal | 三者都依赖一个慢层值；B 直接供给；C 会让三者各自临时算，重复 | ACCEPTED |
| M5 | 是否持久化 | **不落常规快照**；只落**曲线拐点 anchor**，可整层重算 | ACCEPTED |
| M6 | 是否存在重复状态 | B 无重复；为此追加硬约束：**Mood 不得成为第二个可写状态**，任何"事件直接写 Mood"都是违规 | ACCEPTED |

### 1.3 Drive / Need 的目标机制（N）

| ID | 裁决项 | 结论 | 状态 |
|---|---|---|---|
| N1 | homeostatic setpoint | **不作通用机制**；仅保留给 Body / Resource 类 | REJECTED（限定） |
| N2 | baseline / neutral target | 作为**默认**形态 | ACCEPTED |
| N3 | lower / upper bound | 采纳（floor / cap 作为兜底，防止闭式回落穿过 0 或超上界） | ACCEPTED |
| N4 | context-dependent target | 采纳，但**目标值由宿主/配置给出，不由模型现编** | ACCEPTED（限定） |
| N5 | 无目标值、仅事件驱动 | 否决其作唯一形态（失去"未满足"语义，等待/持续无法表达）；但**它是"倾向/preference"类的正确形态** | REJECTED（限定） |
| N6 | "未满足→增长"的判据 | 需要同时满足：**(i) 存在可被剥夺的持续条件；(ii) 无输入时会真的变差**。真需要者仅 Body/Resource 类 | ACCEPTED |
| N7 | 联结 / 想念 | **不由时间自动增长**；由"分离事件 + 跨度"作为事件证据驱动 | ACCEPTED |
| N8 | 玩心 / 探索倾向 / 好奇基线 | 属 Self / preference 参数，**不是 Drive** | ACCEPTED |
| N9 | 好奇 | 由**信息缺口对象**驱动：缺口存在则强度在，缺口闭合即满足 | ACCEPTED |
| N10 | satiation | 采纳，形态为**不应期（refractory）**而非"减一个数"；仅对可被消费的动机 | ACCEPTED |
| N11 | 避免无输入自动上涨 | 三条硬约束：状态一律 derived 不落库 / 时间只作**回落**项 / 若确需变差必须限 Body 类且闭式 + floor + cap | ACCEPTED |
| N12 | 可检查判据 | **任何动机量的当前值必须能由 (上一权威点, 时间, 参数) 闭式重算** | ACCEPTED |
| N13 | target 的语义 | target 是**回落参照**，不是"必须维持的设定值"；need 的"目标"应是**被满足的对象/条件**，不是数值 | ACCEPTED |

### 1.4 两级时间持续机制（T）

| ID | 裁决项 | 结论 | 状态 |
|---|---|---|---|
| T1 | Echo 与 Durable Event 是否拆开 | **正式拆开**，独立阈值、独立时间常数、独立落点 | ACCEPTED |
| T2 | 用同一阈值解决两件事 | 明确禁止：`echo_enter` 与 `event_candidate` 必须是两组参数，且 `echo_enter < event_candidate` | REJECTED（禁令） |
| T3 | Echo 落点 | derived（读时算）+ 可丢，**不产生历史** | ACCEPTED |
| T4 | Echo 时长 | 由**幅度映射时长**（分段/对数），带 floor 与 max_hours；不是固定半衰期 | ACCEPTED |
| T5 | Durable Event 触发结构 | 三段式：**候选（幅度门槛）∩ 门（时空维持 + 双阈值迟滞）∪ 覆盖（该 Affect 变化的显式主体认领 + Ownership Receipt）** | ACCEPTED（覆盖来源补全，§10.1/10.4） |
| T6 | Echo 是否为 Event 的前置门 | **不是**。小回声可以永不成为事件；大事件即使回声很快消失仍是事件 | ACCEPTED |
| T7 | Event 的门是否复用 Echo 半衰期 | 禁止复用 | REJECTED（禁令） |
| T8 | Chord 字符变化能否单独触发 Event | **不能**。Δ、Chord 变化、Event 三者不等价（来源追溯 §5.1#5） | ACCEPTED |
| T9 | 过严门槛风险 | 采纳 countermeasure：**"覆盖"路径必须独立成立**，使严判据不会把真实变化整体拒掉 | ACCEPTED |

### 1.5 Provenance Authority（P）

| ID | 裁决项 | 结论 | 状态 |
|---|---|---|---|
| P1 | 性质升级 | 从"字段规范"升级为**权威契约** | ACCEPTED |
| P2 | 模型可提出的 | `appraisal_basis`、`cause_ref` **候选**、`reason`/`origin`、Desire 的 `content`/`topic`、命名维度读数候选 | ACCEPTED |
| P3 | 必须宿主/服务端签发的 | source authority（来源主体与真实性）、`receipt`、trust level、`principal`、`scope`/权限、attestation | ACCEPTED |
| P4 | 模型可否自报可信 | **绝对不可以**。模型可以提出"这次让我委屈"，不可以提出"这条来源可信" | REJECTED（禁令） |
| P5 | 防伪机制（抽象，不照搬 WeakMap） | ① 不可序列化的能力句柄；② 角色 + 能力双校验且不同来源不可互换；③ 反序列化字段白名单 | ACCEPTED |
| P6 | 载荷中永不接受的字段 | `trusted`/`verified`/`authority`/`attestation`/`receipt`/`role`/`principal`/`scope`/模型自报 `confidence`/"已完成·已发送"断言 | ACCEPTED |
| P7 | 外部执行 receipt 的权威 | **外部执行结果**必须来自执行端，摘要与模型叙述不是 receipt；Ownership 等域 receipt 证明其域内动作，不能代替执行结果（§10.3） | ACCEPTED（范围澄清） |
| P8 | 无 authority 时 | 降级为"未验证来源候选"，不得进入长期权威对象；**禁止默认可信** | ACCEPTED |
| P9 | 主体认领（AFFIRM/REJECT/SUSPEND）由谁签 | **Subject-owned decision + Host-issued authority receipt**：主体决定内容，宿主只验证并签发"该认领动作来自具有该领域权限的主体"。原措辞"是否与 adapter 同源"作废——它是**独立 authority domain**（详见 §10.1） | ACCEPTED（2026-10-07 补充裁决，原 OPEN 记录保留） |

### 1.6 Runtime State 与 Injection Contract（R）

| ID | 裁决项 | 结论 | 状态 |
|---|---|---|---|
| R1 | 必带信息 | `computed_through_event_id`（权威游标）、`computed_through_turn_id`、`as_of_time`、`state_revision`、`engine_version`、`dimension_set_version`、`mapping_version`，并标明 `projection_version` / `calibration_version`（未校准也须可辨） | ACCEPTED（契约信息，不定 schema） |
| R2 | freshness 语义 | 四态：`current` / `aging` / `stale` / `expired` | ACCEPTED |
| R3 | 落点形态 | 采 **anchor-at-turning-point**（Kin 形态：慢层曲线拐点 anchor + read 不 write），优先评估项采纳 | ACCEPTED |
| R4 | 每轮 snapshot | 否决：落盘膨胀 + 与真源重复 | REJECTED |
| R5 | 纯 derived read-time | 部分否决：保留为"从 anchor/事件重算"的兜底；不作默认，因其导致注入 revision 随分钟级衰减抖动 | REJECTED（限定） |
| R6 | anchor 的地位 | **不是权威状态**，是复算输入；丢失可从事件流重建 | ACCEPTED |
| R7 | Active Continuity 与 Affect State 时间边界 | **共用同一游标**；游标不同必须显式暴露差异，不允许一方假装最新 | ACCEPTED |
| R8 | 状态过旧处理 | 三层降级：aging 照常注入 + 标注 / stale 降为软背景 + 触发后台结算 / expired **不注入数值**，只注入"上次结算在某时、之后未结算" + 触发结算 | ACCEPTED |
| R9 | 注入是否自述陈旧 | 必须。注入块要显式说明"该状态截至……，其后的消息尚未结算" | ACCEPTED |
| R10 | 注入抖动的量化粒度 | 需要（旧实现以 5bpm 取整使 revision 只在语义变化时动）；具体粒度待标 | OPEN |

### 1.7 Affect × Motivation 新耦合边（X）

| ID | 裁决项 | 结论 | 状态 |
|---|---|---|---|
| X1 | 新增边：Motivation / Concern 生命周期 → Affect 回落 target / pace | **接受其形状**，但只允许权威对象触发 | ACCEPTED（限定） |
| X2 | 可改回落目标的对象 | Concern 关闭/重开；Desire 满足/放弃/过期；Intention 达成/取消；Attitude 证据累积（慢层目标移动） | ACCEPTED |
| X3 | 不允许的入边 | 单次情绪、单轮 appraisal 候选、模型自报"我现在不想了" | REJECTED |
| X4 | 如何避免直写 Emotion | ① 只改**参数**（target/pace）不改**当时数值**；② 入边必须是**生命周期状态迁移**（已认领），不是分值；③ **单向** | ACCEPTED |
| X5 | 是否仍须经 appraisal | **生成边必须经**（受阻→frustration、达成→正向情绪，走 goal_conduciveness）；**结算边不必经**（"这件事结束了，别再续航"不是感受）。即这条边是**结算边，不是生成边** | ACCEPTED |
| X6 | 过期 ≠ 满足 | **必须区分**满足 / 放弃 / 过期。过期只回落 baseline，**不产生正向情绪**；只有"达成"才可能产生正向情绪且必须经 appraisal | ACCEPTED |
| X7 | 认领的反向 | Affect 不得反向写 Motivation 生命周期，只能通过 bias 影响候选优先级 | ACCEPTED |

---

## 2. 逐项裁决理由

### 2.1 Affect 状态表示（A）

**为什么是 C 而不是 A 或 B。** B（命名维度 + 每维 baseline/half-life）是唯一能承载"回源到 cause + 有语义名"的形态：一个命名维度自带"这是什么感受"，而 V/A 只能说"不太好且较激动"，说不出"是委屈还是受挫"。三个真实实现（Kin ≥28 维、Drivesoid 15 维、emotion-system 17 维）**没有一个把我们意义上的 V/A 当本体**，全部是命名维度 + 逐维时间常数——但**这不能作为删除低维轴的理由**（见下）。A（低维本体）的问题不是"信息少"，而是**无法回源**：单次事件写一个 P/A 位移，之后没人能说清这个位移对应哪条 cause，而这正是第一阶段不变量"emotion 必须带 cause/evidence"要拦的东西。

**但低维轴是必需的（A3）**，理由是可枚举的功能缺口，不是审美：
1. Chord 的两根轴断供——mode↔valence、tempo↔arousal 是旧方案里唯一有外部依据的映射；没有投影轴，Chord 只能逐维查表，失去"整体温度"的表达能力；
2. 跨维统一阈值——Echo / Event / coping 若逐维各设一套参数，参数量爆炸且无法比较"这次变化比上次大"；
3. 检索软排序与注入摘要需要一个低维整体量；
4. 与任何外部组件（情绪识别、生成、UI）对接都要求一个可比较的公共坐标。

**D 的功能性论证（A5/A6/A7）。** 按要求，不因四候选都不用就删，逐条查它的不可替代性：

| D 的候选功能 | 是否被既有对象覆盖 | 判断 |
|---|---|---|
| (a) 评价层的 control/power/coping 的聚合读数 | 被 appraisal 的 SEC/control/power 覆盖，且**那是判断、这是读数**，读数可由判断确定 | 覆盖 → 不构成理由 |
| (b) 行动倾向的方向（趋近/回避） | 由情绪类型 + goal_conduciveness 决定（GRID/Frijda），D 不提供增量 | 覆盖 → 不构成理由 |
| (c) 表达层的稳定性／根音 | 第一阶段已判"证据弱、应降级/存疑" | 不成立 → 不构成理由 |
| (d) 关系中的示弱／依赖／支配（"能不能对这个人软下来"） | **唯一可能有独立语义的用途**；但两个独立实现都用**命名维度**表达（Kin 的 vulnerability/reassurance、emotion-system 的 vulnerability），粒度更细 | 可被更细地承担 → 不构成保留理由 |

→ 结论：**(a)(b)(c) 与既有对象重复，(d) 已被命名维度以更细粒度承担**——这才是否决 D 的功能性理由，而不是"没人用"。同时不把它永久钉死：A7 给出可证伪的加回条件（两个事件维度读数相同、掌控感差异显著、且改变下游选择），届时它作为**投影轴**回归，不回到状态本体。

**certainty / novelty 为什么不能进 Affect（A8/A9）。** 三条理由：
1. **语义错**：一件事的确定性不会因为时间过去而降低，它只随新证据变化——而 Affect 的一切都会衰减，放进来会得到一个"确定性随时间下降"的荒谬动力学；
2. **双写**：同一量既在 appraisal 又在 affect，产生第二次真源问题；
3. **污染阈值**：certainty/novelty 若带自己的衰减，会进入 Echo/Event 的幅度判定，让"这次变化大不大"变得不可解释。
边界要写清：**`certainty` 是判断（无 cause 不成立、不衰减）；"困惑感"是感受（有 cause、会衰减）**。两者不是同一个东西，后者允许存在为命名维度。

**旧 PADCN（A10–A12）。** P/A 保留但降为投影轴——关键是**旧 P 在旧方案里同时承担"底色"和"可直接写"两重身份，迁移后只保留第一重**。C/N 移 appraisal。D 见上。36h 不给任何默认地位：它既不是错，也不是有理由，标 `[待标]`。

### 2.2 Mood（M）

| | A 独立对象 | **B 派生慢层（采）** | C 不设 |
|---|---|---|---|
| 获得 | 可写、可整块注入、可查询 | 可复算、永不与现实冲突、能表达"说不清原因但一直持续" | 状态最少 |
| 代价 | 与各维 baseline 重复；产生第二次真源；写入者权限问题 | 需要一个可复算的低通投影 | 丢掉 affect-as-information 载体；Chord/检索/appraisal 各自临时算 |
| 是否持久 | 是（落快照） | **只落拐点 anchor** | 无 |
| 重复状态 | 有 | 无 | 无 |

决定性理由：**"心境"是 Affect 层唯一能解释"今天整体偏冷/偏暖"的东西**，也是第一阶段保留的 Mood→appraisal 强度偏置这条边的载荷。C 会逼着三个下游各自现算一个临时值，那才是真正的重复。A 的代价已经在旧方案里真实出现过：PADCN 的 36h 回弹既是"慢状态"又是"基线回归"，同一件事存了两处。B 用"派生 + anchor"同时拿到功能与唯一真源。

M6 是必要补丁：**慢层不得成为第二个可写状态**。唯一例外是 X 组——动机生命周期可以改它的**参数**，但也不是写它的值。

### 2.3 Drive / Need（N）

五种目标机制的归位：

| 机制 | 归位 | 理由 |
|---|---|---|
| homeostatic setpoint | **仅 Body/Resource 类** | 只有生理量的"未满足→变差"是真实的；心理 need 的 setpoint 无客观值（第一阶段结论保留） |
| baseline / neutral target | **默认** | 表达"回落参照"，不需要主张存在必须维持的设定值 |
| lower/upper bound | 采纳为兜底 | 闭式回落会穿过 0 或超上界，需要 floor/cap（Drivesoid 的 `DIM_FLOOR` 就是这个作用） |
| context-dependent target | 采纳，值由宿主/配置给 | 目标可依附"当前对象/情境"（如针对某人），但不允许模型现编目标值 |
| 无目标值、仅事件驱动 | 倾向/preference 的**正确**形态，但不是 Drive 的唯一形态 | 否则"未满足"这个语义无处安放，等待与持续无从表达 |

**"哪些 Drive 真需要未满足→增长"** 用 N6 的判据筛：只有 Body/Resource 类通过；联结/想念（N7）由事件证据驱动；好奇（N9）由信息缺口对象驱动，缺口闭合即满足；玩心/探索（N8）根本不是 Drive。

**satiation（N10）** 采不应期而非减数：减数会被模型用"我满足了"直接操纵，不应期是宿主侧的时间窗，模型改不动。

**避免无输入自动上涨（N11/N12）** 要同时检查两件事：N12 的闭式重算排除 tick/轮次依赖，**本身不证明没有增长**（增长函数也可以是闭式）。还须遵守 N11 的动力学限制：没有新事件或合法目标变更时，不新增心理剥夺/累积增长机制；合法事件目标的回落与受限 Body/Resource 例外要保留依据。这是本窗口逻辑澄清，不新增参数或身体运行机制。

**N13 是一条容易踩的坑**：Kin 的 `target` 是 0–100 的**数值**，容易被读成"心理 need 的设定值"。我们明确规定：target 是回落参照；need 的"目标"应是被满足的**对象/条件**——这才保住第一阶段的"必须的对象化"。

### 2.4 两级时间持续机制（T）

两个问题必须分开，因为它们是两种不同的东西：

**Echo / Lingering ——"这次变化还在当前状态中持续多久"**

| 要素 | 候选定义 |
|---|---|
| 触发 | 单次事件的维度位移 ≥ `echo_enter[v]`（按维度量纲归一） |
| 状态 | `(dim, sign, t_enter, magnitude0)` |
| 时长 | **幅度→时长映射**（分段或对数），不是固定半衰期。旧实现的实证锚：12 分→约 1.5h，50 分→约 4.5h |
| 退出 | 低于 `echo_floor` 或超过 `echo_max_hours` |
| 落点 | derived（读时算），**可丢，不产生历史** |

**Durable Affect Change Event ——"这次变化是否值得成为长期历史"**

| 要素 | 候选定义 |
|---|---|
| 候选 | Δ ≥ `event_candidate[v]`，且 `echo_enter < event_candidate` |
| 门 | 候选 **∩** 跨 N 轮/时间窗维持，用**双阈值迟滞**（进入阈值 > 退出阈值）+ 最短驻留 |
| 覆盖 | 对**这次 Affect 变化**的显式主体认领/重评 + 对应 Ownership Receipt；独立于幅度/维持门。对其他对象的 AFFIRM / REJECT / SUSPEND 只记录其自身迁移，不自动生成 Affect Event |
| 附加 | 实质改变长期对象（态度/关注/baseline）或进入记忆/注意 |
| 落点 | append-only、不可变、不衰减 |

三条禁令与一条新禁令：
- **T2**：同一阈值不得解决两件事——`echo_enter` 与 `event_candidate` 是两组参数；
- **T7**：Event 的"门"不得复用 Echo 的半衰期（否则两件事又绑在一起）；
- **T6**：Echo 不是 Event 的前置门；
- **T8（本轮新增，来自来源追溯）**：**Chord 字符变化不得单独触发 Event**。Chord 变化还受量化边界和映射表配置影响，一个字符变化可能只是取整跳变。判定必须回落到维度层。

**T9 的必要性有实测支撑**：emotion-system 源码注释记着，过严的二级判据把 33 个真实变化拒掉了 27 个（约 82%）。所以"覆盖"路径必须独立成立——这就是三段式相对"单一阈值"的真实价值。

### 2.5 Provenance Authority（P）

从"事件里有个 source 字段"升级为**契约**，四层：

**第一层 · 谁能提出什么**

| 可由模型提出 | 必须由宿主/服务端签发 |
|---|---|
| `appraisal_basis`（我认为这条对我的哪个参照意味着什么） | source authority（这条来源确实来自该主体） |
| `cause_ref` 的**候选**指向 | `receipt`（外部行为的真实结果） |
| `reason` / `origin` | trust level |
| Desire 的 `content` / `topic` | `principal`（谁在说话）、`scope` / 权限 |
| 命名维度的读数候选 | attestation |

分界线一句话：**模型可以提出"这次让我委屈"，不可以提出"这条来源可信"**。

**第二层 · 防伪（抽象，不照搬 WeakMap 实现）**
1. **不可序列化的能力句柄**：authority 以进程/服务内的不透明句柄存在，**不进任何会被模型读到的载荷**，校验在签发侧完成；
2. **角色 + 能力双校验**：签发需明确角色（如 adapter / maintenance）与显式能力（如 `provenance:attest`），且**不同 authority 来源不可互换**（adapter 签的不能由 maintenance 冒充）；
3. **反序列化字段白名单**：只接受白名单字段，trust/receipt/verified 类字段**永不接受来自载荷**。

**第三层 · 载荷中永不信任**（P6）：`trusted` / `verified` / `authority` / `attestation` / `receipt` / `role` / `principal` / `scope` / 模型自报的 `confidence` / 任何"已完成、已发送"的断言。

**第四层 · 失败与降级（P8）**：拿不到 authority 就标"未验证来源候选"，不得进入长期权威对象，**禁止默认可信**。

**P7 的范围是外部执行结果**：执行端报告的是该次执行的观察结果，摘要不是。Ownership / Grounding / 配置迁移等 receipt 只能证明其域内的动作或处理结论；都不能被叙述伪造，也不能推导世界事实必然为真（§10.3）。

### 2.6 Runtime State 与 Injection Contract（R）

**落点三选一（R3–R6）**

| 方案 | 结论 | 理由 |
|---|---|---|
| 每轮 snapshot | REJECTED | 落盘膨胀 + 与真源重复 + 每轮都产生"状态记录"会反过来鼓励把状态当证据 |
| 纯 derived read-time | REJECTED（限定） | 注入项 revision 会随分钟级衰减持续抖动；且每次要全参数。保留为"可从 anchor/事件重算"的兜底 |
| **anchor-at-turning-point** | **ACCEPTED** | 只在**曲线形状改变**处落一点（事件、目标变更、动机到期、baseline 更新）；读时从最近 anchor 闭式投影；**read 不 write**；修订只携带被移动的 anchor |

关键约束（R6）：**anchor 不是权威状态**，它是复算输入。权威仍然是事件流与权威对象；anchor 丢了能从事件重建。这条划清了"落盘"与"真源"，避免了旧方案那种"存了一份状态，然后开始争论它是不是真源"的泥潭。

**Injection Contract（R1/R2/R8/R9）**

```
injection {
  computed_through_event_id : <游标：算到哪条输入>
  computed_through_turn_id  : <轮次要口>
  as_of_time                : <该读数对应的物理时间>
  state_revision            : <状态修订号>
  engine_version            : <用哪套规则算的>
  dimension_set_version     : <维度表版本>
  projection_version        : <Base Projection 版本>
  calibration_version       : <Subject Calibration 版本或明确未校准>
  mapping_version           : <Chord 映射表版本>
  freshness                 : current | aging | stale | expired
  staleness_note            : <自 computed_through 之后是否有未结算输入，必须自述>
}
```

- **R7 时间边界对齐**：Active Continuity 回答"窗口外发生过什么还没被处理"，Affect State 回答"这些已处理的事件把状态推到哪了"。两者用**同一个游标**；游标不同就必须显式暴露差异，**不允许其中一个假装是最新的**。
- **R8 过旧处理**三层：`aging` 照常注入 + 标注；`stale` 降级为**软背景**（不再给阈值类/机会类信号）+ 触发后台结算；`expired` **不注入数值**，只注入"上次结算在某时，之后未结算"这一事实 + 触发结算。**禁止拿旧值当现值**。
- **R9 自述陈旧**：注入块必须自带"截至何时、之后未结算"，否则下游会把一个陈旧读数当现在。

### 2.7 Affect × Motivation 新耦合边（X）

**这条边是什么。** 形态：`Motivation/Concern 的生命周期迁移 → 对应 Affect 维度的回落 target / pace`。旧实现里能看到这个形状：某个维度的曲线在动机未到期时朝 target 走，**动机到期后改朝 baseline 走、并换一个节奏**。

**是否通用（X1）**：**形状通用，触发者必须受限**。只有权威对象（X2 清单）的生命周期迁移能改回落参数。

**如何避免 Direct Write（X4）** 三条：
1. 只改**参数**，不改**当时的数值**——数值永远由曲线闭式算出；
2. 入边是**生命周期状态迁移**（已完成认领），不是分值；
3. **单向**——Affect 只通过 bias 影响候选优先级，不能反写生命周期（X7）。

**是否仍须经 appraisal（X5）**——这是本组最要紧的区分：
- **生成边**：动机受阻 → frustration；达成 → 正向情绪。**必须经 appraisal 的 goal_conduciveness**（第一阶段结论不变）；
- **结算边**：动机结束 → 别再续航。**不必经 appraisal**，因为它表达的不是"我感受到什么"，而是"这件事结束了"。

一句话：**这条边是结算边，不是生成边。**

**X6 是 Kin 形态里的一个隐患，必须补。** 它的曲线只区分"动机还在（朝 target）"与"动机结束了（朝 baseline）"——但"结束"有三种语义：**达成 / 放弃 / 过期**。若三者混成一件事，会产生一个具体错误：**一个过期的愿望会像"达成了"一样把情绪拉回基线，甚至被误读为释然**。所以：过期只回落 baseline，**不产生正向情绪**；只有达成才可能产生正向情绪，且必须经 appraisal。

---

## 3. 新版 Affect × Motivation 信息流（v2）

```
                        ┌──────────── 权威对象层（append-only / 状态机）────────────┐
                        │ Event·Evidence   AppraisalRec   Desire/Intention/Concern │
                        │ Attitude/StableSelf   Receipt（宿主签发）                 │
                        └───────────────────────────┬──────────────────────────────┘
                                                    │
   ┌────────────────────────── 生成边（必须经 appraisal）──────────────────────────┐
   │  E1 Event → appraisal(goal_conduciveness 等) → 命名维度 Δ                      │
   │  E2 Motivation 生命周期/优先 → appraisal relevance（偏置，不是写值）            │
   │  E3 Motivation 强度 → 生成强度（调制）                                        │
   │  E4 受阻 → frustration（经 goal_conduciveness）                                │
   └───────────────────────────────────────────────────────────────────────────────┘
                                                    │
                            ┌───────────────────────▼───────────────────────┐
                            │  命名维度状态（derived，读时闭式算，不落库）      │
                            │  每维：baseline / half_life / floor·cap / target │
                            └───────┬───────────────────────┬───────────────┘
                                    │                       │
                    ┌───────────────▼──────────┐   ┌────────▼────────────────────┐
                    │ Base + Calibration → V/A  │   │ 慢层 Mood（derived，τ 低通） │
                    │ → Chord / 检索 / 统一阈值  │   │ → appraisal 强度偏置(E6)     │
                    └───────────────┬──────────┘   │ → Chord Baseline/Recent     │
                                    │              └────────┬────────────────────┘
   ┌────────────────────────────────▼───────────────────────▼──────────────────────┐
   │ 两级时间（互不代偿）                                                           │
   │  Echo：位移≥echo_enter → 幅度→时长 → floor/max → 消失（derived，可丢，不留史）    │
   │  Durable Event：候选∩门(维持+双阈值迟滞) ∪ 覆盖(该变化认领+Receipt) → 留史      │
   │  ⛔ Chord 字符变化不得单独触发 Event（T8）                                       │
   └────────────────────────────────┬──────────────────────────────────────────────┘
                                    │
   ┌────────────────────────────────▼──────────────────────────────────────────────┐
   │ 结算边（不必经 appraisal，只改参数）                                            │
   │  X1 Concern 关闭/重开 · Desire 满足/放弃/过期 · Intention 达成/取消              │
   │     · Attitude 证据累积 → 改该维度 relaxation target / pace                    │
   │  ⛔ 过期 ≠ 满足（X6）：过期只回落 baseline，不产生正向情绪                        │
   └────────────────────────────────┬──────────────────────────────────────────────┘
                                    │
   ┌────────────────────────────────▼──────────────────────────────────────────────┐
   │ 认知持久项两类 + 运行交付账（R3/R6）                                          │
   │  ① 权威变化 → append-only 事件/版本                                            │
   │  ② 曲线拐点 → anchor{dim, m, x, at, fp}（复算输入，非权威状态，可重建）          │
   │  ③ 注入交付账 → computed_through / as_of / revision / freshness                │
   └────────────────────────────────┬──────────────────────────────────────────────┘
                                    │
   ┌────────────────────────────────▼──────────────────────────────────────────────┐
   │ 消费（消费 ≠ 授权）                                                            │
   │  表达/提示：Chord、Annotation 展示、注入文本                                     │
   │  软偏置：Drive/Desire 候选优先级（E8）                                          │
   │  ⛔ 不得直接获得 Motivation / Task / Permission / Wake 权限                      │
   └───────────────────────────────────────────────────────────────────────────────┘
```

信息流边表：

| 边 | 从 → 到 | 类型 | 必须经过 | 可否改值 |
|---|---|---|---|---|
| E1 | Event → 命名维度 | 生成 | appraisal（goal_conduciveness） | 是（生成新值） |
| E2 | Motivation 生命周期 → appraisal relevance | 偏置 | appraisal | 否 |
| E3 | Motivation 强度 → 生成强度 | 调制 | appraisal | 否 |
| E4 | 受阻 → frustration | 生成 | goal_conduciveness | 是 |
| E5 | 投影 → Chord / 检索 / 阈值 | 派生 | — | 否（read-only） |
| E6 | Mood 慢层 → appraisal 强度 | 调制 | — | 否 |
| E7 | Echo / 慢层 → 注入与表达 | 表达 | 注入契约（R1/R9） | 否 |
| E8 | Affect → Drive/Desire 候选优先级 | 软偏置 | — | 否（不改生命周期） |
| E9 | Drive/Need → Desire 候选 | 方向 | — | 否 |
| E10 | 认领/重评 → 长期对象（baseline / Attitude） | 权威变更 | provenance 契约 | 是 |
| E11 | 权威变化 / 拐点 → anchor | 落盘 | — | 否 |
| E12 | 动机生命周期迁移 → 回落 target/pace | **结算（参数）** | **不必经 appraisal** | 是（改参数） |

不变量：I1 Affect 与 Motivation 分存｜I2 数值状态 derived，只有权威变化与拐点落盘｜I3 emotion 必带 cause/evidence｜I4 多情绪并存（portfolio）不互相替换｜I5 动机不得直连行动｜I6 provenance 不可自报｜I7 禁无输入上涨｜I8 禁 tick 累积（状态值必须可由 (x0,t0,参数) 闭式重算）｜I9 Δ ≠ Chord 变化 ≠ Event｜I10 消费 ≠ 授权。

---

## 4. 新版 Runtime / Persistence 契约

落盘口径：**只有"权威变化"与"曲线拐点"落盘；其余一律读时现算。**

| 对象 | 定义层 | 落点 | 谁写 | 写入时机 | version | 失效 / 降级 | 可重算 |
|---|---|---|---|---|---|---|---|
| Event / Evidence | 权威 | append-only | 宿主 | 事实发生 | — | 不失效 | — |
| AppraisalRec | 派生判读历史（带 basis，候选/生效分开，不等于客观真值） | append-only | appraisal 候选 + 领域规则处理 | 事件结算时 | 判读版本 | 新版本并存，不覆盖 | 判读可重跑，原历史保留 |
| Receipt | 域内动作/处理结果的审计证据，不是新 capability | append-only | **宿主/执行端按域签发** | 动作受理、执行结果或拒绝/失败 | 绑定当次目标版本与规则依据（具体形式 OPEN） | 历史记录保留；未来授权须重查，不能重放取得权限 | 否；派生索引可重建，原动作不可编造 |
| ProvenanceGrant | 权威 | **不落库**（能力句柄）或只落引用 | **宿主/服务端签发** | 签发动作 | — | 立即不可迁移 | 否 |
| 命名维度状态 | 派生 | 不落库 | — | read-time | `dimension_set_version` | 陈旧见 freshness | 是（从 anchor/事件） |
| Anchor（拐点） | 复算输入 | 落盘（部分修订） | 结算器 | 曲线形状改变 | `engine_version` | 可由事件重建 | 是 |
| Mood（慢层） | 派生 | 只落 anchor | — | read-time | `engine_version` | 同上 | 是 |
| Echo | 派生 | **不落盘** | — | read-time | `engine_version` | 自动消失 | 是 |
| Durable Affect Change Event | 权威 | append-only | 结算器 + 认领 | 三段式成立 | — | 不可变 | 从 basis 重建候选 |
| Affect Annotation | 历史记录（不等于无条件事实真值） | append-only | 编码时写入 | 体验发生/编码 | 判读版本；若含 V/A 则保留当时 projection/calibration 版本 | **不被当前 Mood / Calibration 改写** | 新解释另留版本，原记录保留 |
| ChordView（Current/Recent/Baseline） | 派生 | 不落库 | — | read-time | `mapping_version` | 随维度变化 | 是 |
| Need / Drive 状态 | 派生 | 不落库 | — | read-time | 参数版本 | 陈旧见 freshness | 是（闭式） |
| Desire / Intention / Concern | 权威 | 状态机 + append-only 迁移事件 | 主体/宿主 + 认领 | 生命周期迁移 | — | 迁移不可撤销，只能新迁 | 否 |
| Attitude / StableSelf | 主体认领的慢对象 | 保留证据与认领版本 | 主体决定，Host 验证签发 | 证据支持 + 领域内显式认领；宿主不代决定内容 | 认领版本 | 不凭召回次数变 | 聚合视图可重算，主体决定历史不可重算 |
| Injection 交付账 | 运行态 | 落盘（小） | 注入器 | 每次注入 | — | — | 是 |

**注入契约信息**（与 §2.6 一致）：`computed_through_event_id` / `computed_through_turn_id` / `as_of_time` / `state_revision` / `engine_version` / `dimension_set_version` / `projection_version` / `calibration_version` / `mapping_version` / `freshness` / `staleness_note`。信息名沿用既有裁决作说明，不是最终字段或接口设计。

**修订传播四条**（吸收来源追溯 §5.1#6）：
1. 更正原始 evidence → **新增版本**，原记录保留；
2. 重跑 appraisal / annotation → **新增 AppraisalRec 版本**，历史 Annotation 不动；
3. 重算 Current / Recent / Baseline / Mood → **只重算派生视图**，不产生历史对象；
4. **任何情况下不得用当前状态覆盖当时感受**。

---

## 5. 旧配置迁移更新表

（更新第一阶段 §2 与 00b §6；`处置` 为第二阶段定论后口径）

| # | 旧结构 | 当前位置 | 处置 | 目标对象 | 依据 |
|---|---|---|---|---|---|
| 1 | `drives:` 10 维主表（expected/half_life 168h/satiety_period/threshold） | `affect.yaml` | **拆分改造**：Body/Resource 类 → Need（baseline + floor/cap + 闭式）；关系/倾向类 → 命名维度或 Self 参数；`half_life` 逐维重标 | Need / 命名维度 / Self | N1·N2·N6 |
| 2 | 同上 `threshold:`（动作触发阈值） | `affect.yaml` | **迁移到事件层**：动作触发不属 Affect | Echo/Event 阈值 | T1·T5 |
| 3 | 同上 `satiety_period:` | `affect.yaml` | **改造为不应期** | 不应期 | N10 |
| 4 | `padcn:` P | `affect.yaml` | **降为投影轴**（derived 加权和，不再可写） | 投影轴 | A10 |
| 5 | `padcn:` A | 同上 | 同上 | 投影轴 | A10 |
| 6 | `padcn:` D | 同上 | **删除**（A7 保留加回条件） | — | A5·A7 |
| 7 | `padcn:` C / N | 同上 | **移入 appraisal** | appraisal（SEC 类） | A8·A11 |
| 8 | `padcn:` 36h | 同上 | **删除默认值**，进待标清单 | — | A12 |
| 9 | `discrete:` 10 通道 | 同上 | **改造**：不再承载状态，转为表达层量化通道；0.5h 一律 → 逐维参数 | 表达层 | §2.1 A4 |
| 10 | `interlocks:` 8 条 | 同上 | **移出 Affect** → 表达层约束（或删） | 表达层 | T9 精神 |
| 11 | `meaning_weight:` 5 档 | 同上 | **并入 Event 显著性（salience）** | Durable Event | T5 |
| 12 | `bpm_range:` 40–120 | 同上 | **保留** | 表达层 | — |
| 13 | `persona.key` | 同上 | **删除**（越权边：Drive 查表得调性） | — | 第一阶段 C6 |
| 14 | `persona.drift_rate_per_day` | 同上 | **改造**：命名维度 baseline 的慢漂移率 | baseline | M5 |
| 15 | `persona.switch_threshold` | 同上 | **改造**：Chord 表达量化边界 | 表达层 | R10 |
| 16 | `persona.key_table: []` | 同上 | **删除**（空占位） | — | — |
| 17 | V1 `axes:`（valence / arousal / **tension**）+ `momentary_update_scale_hours` | 同上 | valence·arousal → 投影轴；**tension 需处置**："紧张"是真实感受，应成为**命名维度**而非第四类轴；整体 V1 兼容层删除 | 投影轴 / 命名维度 | A1·A3 |
| 18 | V1 `layers:`（persona 4320h / recent 72·36·48h + update_scale） | 同上 | **删除**：统一为"唯一一套时间常数"（各维 half_life + Mood τ），禁止三套并行 | — | M5·A4 |
| 19 | `chord_table: []` | 同上 | **删除**（空占位） | — | — |
| 20 | 两套 velocity（`min_velocity` 0 / `V2_VELOCITY_MIN`） | `affect.yaml` + `chord.py` | **保留一套**，另一套删 | 表达层 | — |
| 21 | `chord.py` 的 `task-37: 合并 P 到 drive_values` | `chord.py` | **删除**（随输入白名单收敛） | — | 来源追溯 §4.1 |
| 22 | `schema.py` 的 `AffectSnapshot` 三层 + Drive/PADCN/Discrete 枚举 | `core/affect/schema.py` | **必须与 1–9 一起迁移**（新对象模型替代），不能只改 yaml | 新对象模型 | §2.1 |
| 23 | `drift_half_life_days`（schema）vs `drift_rate_per_day`（yaml） | schema + yaml | **对齐或删**（同名不同语义） | — | 实读 |
| 24 | `affect_chord_map.yaml` 的 `key` / `progression_chord` / `momentary_chord` | `config/` | **改名或删**：旧音乐标签不得当 Current/Recent/Baseline 定义 | Chord 视图 | 来源追溯 §7.1 |
| 25 | `synthesis.py`"衰减值读取时刻现算、不落库" | `core/affect/synthesis.py` | **保留并升级为契约条文**（R3/R5 的既有先例） | Runtime 契约 | 来源追溯 §7.1 |
| 26 | `wake_loop.py` optional `synthesis_provider` + `inject.py` `<guyuan_now>` | `runtime/` | **保留接口、改造注入契约**（加 computed_through / as_of / revision / freshness） | Injection 契约 | R1·R9 |

**必须一起迁移的最小集合**：#1 #4 #5 #6 #7 #9 #17 #18 #22 #23 #24。这十一项只改其中任何一项都会留下两套真源。

---

## 6. 尚需实验标定的参数清单

标注：`[理论]` 有理论/文献支持｜`[工程]` 工程惯例或某实现里的取值（基本等于"某个人拍的"）｜`[待标]` 必须实验或观测才能定。

### 6.1 每维参数（最大工作量）

| 参数 | 建议起点 | 标注 | 如何定 |
|---|---|---|---|
| `baseline[v]` | 无统一默认值 | `[待标]` | 观测"无事件时的稳态读数" |
| `half_life[v]` | 4h–30h 区间作参照 | `[工程]` | 逐维可观测量设计（旧"一律 0.5h"无理由） |
| `floor[v]` / `cap[v]` | 需设，值待定 | `[待标]` | 防闭式穿过 0 / 超上界 |
| `echo_enter[v]` | — | `[待标]` | 观测"主观上还能感觉到"的最小位移 |
| `event_candidate[v]` | 必须 > `echo_enter` | `[待标]` | 与"值得留史"的相关性标定 |
| 量纲 / 归一化 | 建议统一到可比区间 | `[工程]` | 否则跨维阈值不可比 |

### 6.2 Echo

| 参数 | 建议起点 | 标注 | 如何定 |
|---|---|---|---|
| 幅度→时长映射的**分段点** | 参照 12 分→1.5h、50 分→4.5h | `[工程]` | 主观时长的直接标定 |
| `echo_floor` | — | `[待标]` | 同上 |
| `echo_max_hours` | 参照 6h 量级 | `[工程]` | 同上 |

### 6.3 Durable Event

| 参数 | 建议起点 | 标注 | 如何定 |
|---|---|---|---|
| 持续窗 N（轮/时间） | — | `[待标]` | 观测"真实长期变化"的持续分布 |
| 最短驻留 | — | `[待标]` | 同上 |
| 迟滞比（enter/exit） | — | `[待标]` | 防抖标定 |

### 6.4 Mood 慢层

| 参数 | 建议起点 | 标注 | 如何定 |
|---|---|---|---|
| τ（低通时间常数） | 24h 量级作参照 | `[工程]` | 让"心境"与"一天"同尺度 |
| anchor 判定粒度（拐点阈值） | — | `[待标]` | 与落盘量权衡 |

### 6.5 投影轴

| 参数 | 建议起点 | 标注 | 如何定 |
|---|---|---|---|
| 维度 → V/A 权重表（**Base Projection Map**） | 固定、版本化 | `[工程]` | 系统测量坐标系：显式配置迁移才能改，普通模型输出不可改（§10.2） |
| **Subject Calibration** 偏移（个体差异层） | 低频、慢变 | `[待标]` | 需长期证据 + 主体认领；不得因单轮 mood/emotion 修改（§10.2） |
| 量化粒度（注入 revision 防抖） | 参照 5bpm 取整 | `[工程]` | 观测"语义变化"vs"取整跳变" |

### 6.6 Drive / Need

| 参数 | 建议起点 | 标注 | 如何定 |
|---|---|---|---|
| `target[v]` / `floor[v]` | — | `[待标]` | 回落参照的标定 |
| 不应期时长 | — | `[待标]` | 观测"满足后多久才该再次被触发" |
| 闭式参数 | — | `[理论]`（求解方式）+ `[待标]`（系数） | 闭式形式确定，系数待标 |

### 6.7 appraisal 与注入

| 参数 | 建议起点 | 标注 | 如何定 |
|---|---|---|---|
| appraisal 变量最小集 | — | `[待标]` | 砍到不再降低判读质量为止 |
| goal_conduciveness 判读粒度 | — | `[待标]` | 观测误判代价 |
| 模型 / 确定性的分工边界 | — | `[工程]` | 成本与稳定性权衡 |
| `aging` / `stale` / `expired` 时间界 | — | `[待标]` | 观测"多久后旧值开始误导" |

### 6.8 不需标定但需列清单的

- provenance：**哪些外部行为必须产生 receipt**（清单类，不是参数）；
- Chord：映射表版本化粒度；跨模型一致性需**重做盲测**（现有历史检查是非盲、prompt 含完整规则，不构成证据）。

---

## 7. 运行复杂度分层的共同前提

**本节原三档表已被替代**：原文保存在 [吸收前快照](./archive/memory-affect/2026-10-07-authority吸收前/memory-affect-02-第二阶段-内部状态表示与运行契约裁决-2026-10-07.md)。原表把 Echo 固定半衰期、迟滞、不应期、receipt 覆盖、映射版本等当作可删减功能，与 T4/T5、N10、R1 和 OPEN-6 不一致，也预设了维度数及 Body 类运行。

三套方案必须共享以下契约，只有运行安排与成本不同：

1. 命名 Affect 为本体、版本化 Base Projection + 慢变 Subject Calibration 为派生路径；不按档位换维度语义，不预设 8–12 或“全量”。
2. Mood 是派生慢层；拐点 anchor + read-time derived；缓存/anchor 可重建，权威事件和主体决定不能编造。
3. Echo 与 Durable Event 分开；幅度→时长映射、边界、维持/迟滞与合法认领覆盖不能由档位取消；具体参数仍待标。
4. Need 不预设心理 setpoint；不凭时间生长；可消费动机的满足需有依据与不应期；不恢复身体/生活模拟。
5. Affect/Motivation 分开；生成边经 Appraisal，结算边只改回落参数；达成/放弃/过期语义不可混淆，编码形态仍 OPEN。
6. provenance 不可自报；Subject-owned decision + Host-issued authority receipt；各域不互换；capability 与持久 receipt 分开（§10.3）。
7. 同一事件游标体系、as-of、规则/投影/校准版本、freshness 和未结算差异必须可见；过旧降级不因档位取消。
8. 原始证据、历史 Annotation、修订与归属保留；召回不自动强化 importance；消费不授予权限。

完整候选比较在复核与 current 吸收后形成，见 [Light / Balanced / Full 运行复杂度方案](./memory-affect-01/00d-Light-Balanced-Full运行复杂度方案-2026-10-07.md)。它不选择框架、数据库、部署或参数，也不授权实现。

---

## 8. 未裁决项与风险

| # | 项 | 状态 | 影响 |
|---|---|---|---|
| OPEN-1 | D 的加回条件如何观测（A7） | OPEN | 需要一个具体的可测场景定义 |
| OPEN-2 | `discrete` 通道与命名维度是否重复 | OPEN | 取决于投影设计，可能整块删除 |
| OPEN-3 | `interlocks` 去向（保留/移表达层/删） | OPEN | 影响离散通道的处置 |
| ~~OPEN-4~~ | 主体认领的 authority 由谁签发（P9） | **已裁决（2026-10-07 补充）**：→ §10.1 Subject-owned decision + Host-issued authority receipt | 原影响说明保留：关系到"认领"能否作为覆盖路径的合法来源 |
| OPEN-5 | 过期 / 达成 / 放弃 是否需在对象层显式区分（X6） | OPEN | 当前只裁"过期不产生正向情绪"，未定是否三态建模 |
| ~~OPEN-6~~ | 投影权重表由谁定 | **已裁决（2026-10-07 补充）**：→ §10.2 Base Projection Map 固定版本化 + 独立 Subject Calibration Layer | 原影响说明保留：主体可改 vs 配置固定，关系到"主观感受谁说话" |
| OPEN-7 | 注入抖动的量化粒度具体取值（R10） | OPEN | 影响 revision 语义 |
| OPEN-8 | 旧"情境行 + 和弦行"文本锚是否保留为 Annotation 的可选呈现 | OPEN | 来源能力保留问题，不影响本阶段契约 |
| OPEN-9 | Echo 是否真的永不持久化 | OPEN | 若出现"跨会话回声"需求需回改 |

**风险（按严重度）**

1. **高**：三套半衰期（`padcn` 36h / `axes` 0.25–0.5h / `layers` 36–4320h）若不同时删除，会长期保留"同一状态两个真源"，且任何一次回退都会让新契约失效。→ §5 最小集合即为对策。
2. **高**：`schema.py` 的三层 `AffectSnapshot` 若不与 yaml 一起迁移，配置改了而对象模型没改，等于没迁（§5 #22）。
3. **中**：噪声点——投影权重表由公式或一个人拍定，会让"整体温度"与真实感受脱钩；6.5 已要求主体参与。
4. **中**：Echo 与 Event 若在实现时又被合成一个阈值，T2/T7 的拆分会失效，回到"一次小波动就留史"。
5. **低**：Chord 映射的跨模型一致性仍无有效证据（历史检查非盲），不应作为映射表定值的依据。

---

## 9. 交付边界

- 本文件原阶段只产出裁决，不修改 current；该历史执行边界保存在吸收前快照，不再表示本次任务的禁令。
- 本次用户已授权：核对 OPEN-4/6、横向最小职责与七组影响、checkpoint、文档复核后吸收 00b/00c、形成三套运行复杂度候选。current 吸收状态以 checkpoint 为准。
- 未改 schema、产品代码、配置、模型路由；未安装、运行产品测试、启动服务、连接 Serein 或访问正式个人记忆。
- ACCEPTED 的两项来源是用户本轮明确重申；横向层的组装、域清单、失效/并发细节是 CANDIDATE / OPEN，不把文档复核当成用户新拍板或运行验收。
- 三套方案交付后停止，待用户选择及裁决剩余 OPEN；不自动进入 schema / API / implementation。

---

## 10. 开放项裁决补充（2026-10-07）

> 本节为**追加**，不覆盖前文。OPEN-4 / OPEN-6 的**原始 OPEN 记录与当时的影响判断原文保留在本节 10.1.0 / 10.2.0 与 §8 表中**（§8 已就地标注"已裁决"，历史行不删除）。原裁决不被推翻，只被补充与收紧。
> 本节仍属**裁决/契约口径**；原补充已存在于接手时。本次对照用户重申与现行 current 复核后维护本节，新增治理细节保留 CANDIDATE / OPEN；不含 schema、未改代码/配置、未运行。

### 10.1 OPEN-4：主体认领 authority —— 已裁决

#### 10.1.0 原 OPEN 记录（原文保留）

- §1.5 P9 原文：`主体认领（AFFIRM/REJECT/SUSPEND）由谁签 | 需明确 capability（是否与 adapter 同源）| OPEN`
- §8 原文：`OPEN-4 | 主体认领的 authority 由谁签发（P9）| OPEN | 关系到"认领"能否作为覆盖路径的合法来源`

当时的决策过程记录：本项被列为 OPEN 的原因是——§2.4 的 T5 把"主体认领"定为 Durable Affect Change Event 的**覆盖路径**（即使不满足幅度门槛与维持门也强制成立），但**没有定义"认领"这一动作本身凭什么算数**。若不定义，模型可以自行宣称"我已经认领/我否定了这个理解"，覆盖路径就变成绕过全部门槛的后门。另有一个未定的实现细节：认领的签发是否应与 adapter（外部来源适配器）共用同一 capability。**当时未裁的部分：签发主体与 capability 归属。**

#### 10.1.1 裁决

> **Subject-owned decision + Host-issued authority receipt**

拆分职责：
- **主体本人**决定动作内容：`AFFIRM` / `REJECT` / `SUSPEND`；
- **宿主不替主体决定内容**，只负责验证并签发"这次认领动作确实来自具有该领域权限的主体"。

候选流程（只作边界说明，不是接口设计）：

```text
旧 Interpretation / Attitude / Self claim
        ↓ recall / reappraisal
主体形成当前判断
        ↓
self_ownership_action(
  target_ref,
  action = AFFIRM | REJECT | SUSPEND,
  rationale / basis
)
        ↓
Host 检查 actor + capability + target domain
        ↓
签发 Ownership Receipt
        ↓
成为合法状态变化来源
```

对原 OPEN 的收口：capability **不与 adapter 同源**——Self Ownership 是**独立 authority domain**（见 §10.3），与 Source Authority 并列且不可互换。

#### 10.1.2 领域权限边界（本次新增判据）

| 对象类别 | 主体可做什么 | 不可做什么 |
|---|---|---|
| 主体自己的 Self / Principle / Interpretation / Attitude | 可认领（AFFIRM/REJECT/SUSPEND），Host 签发 receipt | — |
| 关于**其他人**的 Fact / Preference | 最多认领"**这是我当前的理解**" | **不能**通过 AFFIRM 把自己的推断升级成"对方的"事实 |
| Shared / Common Ground | 单方表达 | **单方认领不能生成共同事实**；需多方 grounding / acknowledgement |
| Commitment | 只能签**自己那一侧**的承诺状态 | 不能代对方签承诺 |

**红线**：普通自然语言输出**不得**直接拥有 AFFIRM / REJECT 权威——认领必须是显式动作 + Host 签发，正文里的"我认可了"不构成认领。

#### 10.1.3 对既有裁决的影响

| 受影响项 | 变化 |
|---|---|
| §1.5 P9 | OPEN → ACCEPTED（已就地更新） |
| §2.4 T5（覆盖路径） | **补全**：覆盖路径须携带 Ownership Receipt 才成立。T5 的结构不变，合法性来源补上了 |
| §1.5 P2/P3 | 得到一个新面：模型可以**提出认领请求**，但不能**自认领成功**——与"可以提出 cause 候选、不能自报可信"同构 |
| §2.3 N4 / X2 | 由 10.1.2 派生新约束：**关于他人的态度/推断**即使被认领，也只是"我的理解"，不得成为共同事实（见 §10.4 第 7 条） |

### 10.2 OPEN-6：Named Affect → V/A projection authority —— 已裁决

#### 10.2.0 原 OPEN 记录（原文保留）

- §6.5 原文（参数清单内）：`维度 → V/A 权重表 | — | [待标] | 需要主体参与（主观感受的投影不该由公式垄断）`
- §8 原文：`OPEN-6 | 投影权重表由谁定 | OPEN | 主体可改 vs 配置固定，关系到"主观感受谁说话"`

当时的决策过程记录：本项被列为 OPEN 的原因，是 §2.1 采纳 C（命名维度为本体、V/A 为派生投影）之后留下一个真空——**投影一旦由固定公式给定，等于由公式垄断"整体温度"的读数**；但若允许主体随意调整权重，投影就失去了可比性与跨会话稳定性，且"整体温度"会随单轮情绪漂移。当时未裁的部分：**谁有权定义与修改投影，以及个体差异往哪里放。**

#### 10.2.1 裁决

> **Base Projection Map 固定、版本化；主体个体差异通过独立的慢变 Calibration Layer 表达。**

**Base Projection Map** —— 属于**系统测量坐标系**：
- 配置固定；
- 带版本号；
- 普通模型输出**不可直接修改**；
- 修改属于**显式系统配置迁移**；
- **历史投影必须能知道自己使用哪个 projection version**。

它回答：**系统如何把 Named Affect Dimensions 投影到统一 V/A 空间。**

**Subject Calibration** —— 允许主体形成长期、可审计的个体差异，例如：
- 某类 curiosity 对该主体通常更偏正 valence；
- 某些高 arousal 状态对该主体未必体验为压力。

但必须同时满足：低频 / 慢变 / 有 provenance / 有足够长期证据 / **可通过主体认领**（走 §10.1 的 Self Ownership 通路）/ **不因单轮 mood·emotion 临时变化而修改** / **不直接改写 Base Projection Map**。

候选关系（只作边界说明）：

```text
Named Affect Dimensions
        ↓
Versioned Base Projection
        ↓
Subject Calibration
        ↓
Derived V/A
```

原则：**坐标系由系统定义，个人体验由主体校准。**

#### 10.2.2 对既有裁决的影响

| 受影响项 | 变化 |
|---|---|
| §6.5 参数表 | 已拆为两行：Base Projection Map（固定版本化，`[工程]`）与 Subject Calibration（低频慢变，`[待标]`） |
| §1.6 R1 | **已同步两项版本信息**：`projection_version` 与 `calibration_version`；不是最终字段设计 |
| §2.2 M6 | 同构风险重申：**Calibration 是可版本化的慢变参数，不得成为第二套即时 Affect 状态**；参数变更须认领，作用仅在投影路径，不改命名维度/Mood |
| §2.7 X 组 | Calibration 的变更**必须走认领**，这正好堵住"单轮情绪改基线"的路径 |
| §2.4 T5 | 派生新问题：Calibration 变更是否算 Durable Affect Change Event？→ 倾向"不算"（坐标系变更 ≠ 感受变化），但**列为未决**（§10.6 NEW-1） |

### 10.3 Authority / Capability / Receipt Layer：最小职责候选

**状态：CANDIDATE（本窗口文档复核）**。复用 P 组、OPEN-4/6 的已确认原则，集中说明共同治理责任；不新增数据库、API、通用政策引擎或必须独立部署的服务。是否作为独立模块组装仍 OPEN。

**Authority**：某主体在某领域有资格作出哪一种决定或证明哪一种来源。**Capability**：Host 为当前受限操作校验的运行时资格。**Receipt**：Host/执行端记录某次域内动作、检查或结果的可审计凭据；它不能授予下一次动作权限。

```text
模型提出内容 / 主体显式决定 / 外部执行端报告
       ↓
Host 检查 actor + domain + scope + capability + 目标及适用版本
       ↓
域内规则决定是否受理（不替主体作主观判断）
       ↓
记录域内 receipt 与变更；被拒则保留候选及原因
       ↓
派生视图重算 / consumer 消费；外部执行另过 Permission
```

#### 10.3.1 最小 domain 责任表（候选组织方式）

| Domain | 内容决定/依据来自谁 | Host 责任 | 不能证明什么 |
|---|---|---|---|
| Source Authority（含执行结果来源） | 已认证输入/adapter/实际执行端 | 证明来源主体、接收与观察范围 | 来源真实不等于内容必然为真；执行结果 receipt 不替代 Self Ownership |
| Self Ownership | 主体对自身 Self / Interpretation / Attitude 的显式决定 | 验证主体与领域资格，签发 Ownership Receipt | 不证明关于他人的事实，不代表共同确认，不代表对外行动授权 |
| Shared Grounding | 参与方对具体内容的 acknowledgement | 检查参与者及所确认的内容范围，保留确认依据 | 单方认领不构成共同理解；双主体最小成立条件仍 NEW-2 |
| Commitment | 承担方自己决定所承担部分；解除/转移还须遵守合法迁移规则 | 验证承担方及迁移资格 | 自己签署不等于可单方解除对方权利或代签；AI 承担与验真仍 NEW-3 |
| Subject Calibration | 主体基于长期证据认领个体投影差异 | 按校准域检查证据、版本与认领资格 | 不改 Base，不直写即时 Affect/Mood/Need；不自动生成感受事件 |
| Base Projection 配置迁移 | 有该配置维护权限的操作者 | 验证显式迁移并保留版本依据 | Self Ownership / Calibration receipt 不能给配置写权限 |
| Permission | 有授予权的用户/政策或合法委派者；Host 执行检查 | 检查当前行为、对象、scope、限制及失效 | Host 不自行扩权；Affect、承诺、receipt 不等于 Permission |

前补充把 Calibration 与 Base 称作“同域不同权限级”；本次保留不可混用原则，表中分列两种责任以免主体认领被误作配置维护权。域注册/命名/委派实现仍 OPEN，不预设独立服务。

#### 10.3.2 六项最小职责

1. **确定操作边界**：复用对象所属领域的 authority 原则，明确谁可提候选、谁可决定、谁可授予权限、谁负责记录结果。模型 payload 不提供自身角色或可信度证明。
2. **检查资格与上下文**：检查真实 actor、capability、domain、scope、目标和适用版本；不同域不可替代。并发更正/撤销后的处置另列 OPEN，不静默覆盖。
3. **记录受理与结果**：receipt 绑定当次动作与依据；授权检查通过、请求受理、实际成功分别记录其含义，不能把受理当完成。Ownership Receipt 不裁判主观内容的“真伪”。
4. **分开能力与审计证据**：不透明运行时 capability 不交给模型载荷，不把历史字符串恢复成 live grant；receipt 可持久引用以保存审计链。跨进程再校验方式、签名/存储机制仍 OPEN。
5. **失败保留边界**：缺少合法 authority 时保存未验证候选或待处理请求，不能升级为当前权威对象；拒绝/失败原因可审阅。候选、原始证据仍可落盘，不能把“无 receipt”理解为删除原始输入。
6. **分开历史与未来权限**：已发生动作的 receipt 留作历史；再次执行必须重查当前授权。撤销、到期、幂等和重放语义仍 NEW-5；本轮只确认历史记录不应自带未来权限。

签发不是产生主观决定：Host 也不是通用真值判官。Source、Ownership、共同确认、Commitment、Calibration、配置迁移、Permission 的结果都只证明各自范围。外部执行状态未知时如实记未知，不能凭模型总结补成功。

#### 10.3.3 新增横向责任的六问

| 治理问题 | 本次答案 |
|---|---|
| 描述什么 | 决定/证明资格与域内动作的审计边界；不描述感受、事实内容或新的认知对象 |
| 时间尺度 | capability 受当前运行/授权有效期约束；receipt 是一次历史动作；authority 规则随显式决定/配置版本演变 |
| 谁创建/修改 | 主体/参与方/配置维护者按域决定，Host 验证与签发；普通模型输出只能提出候选或显式请求 |
| 能否重算 | 当前派生视图、索引可重算；主体决定、执行观察与原始 receipt 不可重算编造 |
| 影响谁 | 控制 Self、Grounding、Commitment、Calibration、持久 Affect Event 与外部执行的合法路径；Retrieval/Injection/Wake 只消费允许内容 |
| 需要独立对象/服务吗 | 暂不需要新增认知对象或独立服务；capability 与 receipt 已有职责。最小实现组织方式随运行方案选定后再设计 |

### 10.4 横向治理对第二阶段七组裁决的影响复核

| 组 | 保留的裁决 | 本次收紧/补全 | 未决边界 |
|---|---|---|---|
| A Affect | 命名维度为本体，V/A 只派生 | Versioned Base + slow-changing Subject Calibration；历史读数可追溯两个版本。D 若重开只能通过显式坐标系版本变更 | 维度集、权重、D 观测判据与离散表达冗余 |
| M Mood | 派生慢层，不作第二套可写感受状态 | Calibration 是投影参数，不代替 Mood、不改 Mood anchor；新投影只重算视图 | τ、anchor 粒度、Mood 表达/消费预算 |
| N Need | 回落参照由合法宿主/配置提供，心理 setpoint 不预设 | 不由模型临时编 target；N4 配置/情境依据须可追溯。**不把 V/A Calibration 自动扩展到 Need** | 前补充 N14 的“Need 个体差异走 Calibration 域”降为候选，若需要另裁；本轮不新增该机制 |
| T Echo/Event | 两级拆分、幅度→时长、独立门、Chord 字符不是事件 | T5 覆盖需对应**Affect 变化**的显式认领 + Ownership Receipt；其他对象认领只记录自身迁移 | Calibration-only 变更是否另设历史类型仍 NEW-1；本轮不把倾向性 T10 升为 ACCEPTED |
| P Provenance | 内容候选与宿主权威分开，禁止自报可信 | P7 限外部执行结果；P9 已裁；P10 不同域不互换是 P5 与 OPEN-4 的必要边界；capability ≠ receipt | 可信来源到内容置信的规则、具体防伪与信任存储 |
| R Runtime/Injection | 拐点 anchor + 读时派生、游标对齐、freshness 降级 | R1 已补 projection/calibration 版本；版本变更与时间陈旧分别暴露，不能只看 freshness | 两类版本如何与 revision 组合、防抖/兼容与 freshness 数值界 |
| X Affect×Motivation | 生成经 Appraisal；结算只改参数；反向只 bias | 已验证的域内迁移才触发结算；认领他人态度仍只是“我的理解”；承诺/执行/权限分别校验 | 迁移编码、解除/完成验真、合法 target/pace 变化规则 |

**汇总**：七组本体选择不变。已确认两项决定进入主表/注入信息；横向组装与新增治理机制保持 CANDIDATE。修正了前补充的 receipt/capability 混用、N14 无依据扩域、T10 倾向冒充裁决及旧三档表的契约降级。没有新增最终字段、接口或权限实现。

---

### 10.5 current 吸收清单（已对照现行全文）

本窗口已读 00b/00c 全文，不依旧行号定位。**先写 checkpoint，再复核与吸收**；最终状态见 [阶段 checkpoint](../handoffs/memory-affect-长期认知连续性-Checkpoint-2026-10-07.md)。

| 目标 | 现行位置 | 成熟结论/最小变化 |
|---|---|---|
| 00c | §2 总图、§5 Consolidation | 横向治理标明候选；整理产候选，不自动升级 Self/Commitment/Task |
| 00c | §4.2、§7.1 | Annotation 不被当前 Mood/Calibration 改写；命名维度、V/A Base+Calibration、Mood 派生慢层、Echo/Event 两级 |
| 00c | §7.2/7.3 | Need 不预设心理 setpoint、不凭时间上涨；生成边与结算边分开 |
| 00c | §9、§9.1 | 主体决定 + Host receipt；域不互换、capability 与审计记录分开；新组织细节 CANDIDATE |
| 00c | §10.3、§11、§14 | 游标/as-of/版本/freshness；消费不授予 Permission；三套方案待选择后仍须处理未决契约 |
| 00b | §3.4、§6、§6A | 同步历史版本、状态/投影/慢层/两级时间、Need 与耦合；研究依据不冒充新实测 |
| 00b | §5、§7、§8、§10/11 | 写者/认领与共同理解边界；authority 最小职责候选与 OPEN 集中入口；最新阶段/方案入口 |
| 入口 | README、OPEN-QUESTIONS、待定问题清单、当前交接 | 两项已决与其他 OPEN 分开，指向 checkpoint/ADR/三方案；停止点明确 |

原文已在 archive 保存，当前正文原位维护；不制造第二份 current。

---

### 10.6 仍未解决的问题（不授权实施）

原有 OPEN 保留：OPEN-1（D 的加回条件观测方式）、OPEN-2（`discrete` 通道与命名维度是否重复）、OPEN-3（`interlocks` 去向）、OPEN-5（过期 / 达成 / 放弃 是否三态建模）、OPEN-7（注入抖动量化粒度）、OPEN-8（旧文本锚是否保留为 Annotation 呈现）、OPEN-9（Echo 是否永不持久化）。

本轮新增（NEW）：

| ID | 问题 | 来源 |
|---|---|---|
| NEW-1 | Calibration 变更历史如何归类；纯投影变化不应凭 Chord 差异伪造感受事件，是否独立类型仍待裁 | §10.2.2 / §10.4 |
| NEW-2 | Shared Grounding 的"多方 acknowledgement"在**双主体**（人 + AI）场景下的最小成立条件：谁 ack、怎样计为达成 | §10.3.1 |
| NEW-3 | Commitment Authority 的主体边界：AI 能否承担 commitment、承担后由谁验真 | §10.3.1 |
| NEW-4 | Permission Authority 与 I10"消费 ≠ 授权"的**接口位置**（谁在读 Affect 之后请求权限，拒绝时如何回退） | §10.3.1 + X 组 |
| NEW-5 | live capability 的到期/撤销/重放，与历史 receipt 的保留/失效关联；跨进程重验、幂等与并发目标版本如何处理 | §10.3.2 |
| NEW-6 | Calibration 的"足够长期证据"门槛未定（与 §6 待标清单联动） | §10.2.1 |

**本阶段停止实施**。按用户本轮授权继续文档复核/current 吸收与三套运行复杂度候选，不进入 schema/API/implementation。


## 11. 本窗口复核记录（2026-10-07）

- **原状态**：接手时 §10 已补 OPEN-4/6，但交接仍写“待补”；receipt 一处写 append-only，一处写不可序列化/迁移；旧 §7 三档表存在契约降级。
- **新决定/证据**：用户本轮明确重申两项决定并授权复核、checkpoint、current 吸收和三方案；本次依据为指定入口、现行 00b/00c 与本文件相互对照，未新增外部证据等级。
- **裁决/修正**：两项为 ACCEPTED；能力与审计记录分开；T5 只覆盖所认领的 Affect 变化；前 N14 扩域降为候选；T10 保持 OPEN；三套方案只分运行复杂度。
- **理由**：否则停机后无法审计认领，模型可能用其他对象认领制造感受历史，轻量档可能违反同一份契约。
- **替代项**：原 §7、§10.3/10.4 的详细旧表保存在吸收前快照；原 OPEN-4/6 和理由仍在 §10.1/10.2。
- **影响范围**：本文件主表、注入/持久边界、七组影响、00b/00c、入口与 OPEN 索引；产品/配置无变化。
- **剩余 OPEN**：§8 未解决项、§10.6 NEW、最小横向组装与复杂度方案选择。文档复核不是新政策拍板或产品验收。

续接入口：[checkpoint](../handoffs/memory-affect-长期认知连续性-Checkpoint-2026-10-07.md)；两项决定依据：[ADR](./decisions/ADR-001-self-ownership-and-affect-projection-2026-10-07.md)。

## 12. 宿主术语补正与阶段影响（2026-10-07）

用户允许故渊不负责 wake loop，运行宿主可由 agent 框架及配套设施承担，旧模拟定时唤醒不继承；决定与原口径追溯见 [ADR-002](./decisions/ADR-002-host-and-wake-ownership-2026-10-07.md)。本文件的 Host 指可信检查/签发执行侧，不特指旧 WakeLoop 或独立治理服务；适用的框架受控工具/hook 可落实职责。

对七组裁决的检查：Runtime 的版本/freshness 契约仍在；Affect 的本体/投影原则不变；Need 不恢复模拟驱动；两级持续的计时不要求持续模型唤醒；Ownership 的主体决定与 Host receipt 不变；Injection 消费仍不授予权限；跨模块生成/结算与真实结果边界不变。**七组均未被推翻，仅解除实现承载的旧宿主前提。**具体框架、调度层、权限/登记/恢复接线继续 OPEN，没有 schema/API/实现变化。续接见 checkpoint 5。
