# memory-affect-02 · 第一阶段：本体边界 / 旧 25 维迁移 / 证据血缘仲裁 / Affect×Motivation 信息流

> 日期：2026-10-07
> 性质：**综合与仲裁，不是新调研**。输入＝ `docs/requirements/memory-affect-02/` 五份报告（01 本体动力学 / 02 Motivation / 03 双向耦合 / 04 Chord 表达 / 05 Runtime 与行动边界）**全文**。
> 本阶段**只**产出四件：① 本体边界表 ② 旧 25 维迁移表 ③ 冲突与证据血缘仲裁 ④ Affect×Motivation 新信息流。
> 本阶段**不做**：不选数值轴集/阈值、不设计 schema、不选存储/数据库、不发模型请求、不重开 Evidence/Event/provenance（沿用 memory-affect-01）、不把旧 25 维当验收标准。
> 证据标注沿用五报口径：E3＝只读源码，E2＝论文正文/官方文档，E1＝README/项目内/摘要，E0＝检索摘要。
> **数值参数一律三档标注**：`[理论]` 有外部理论支持 ｜ `[工程]` 来自某个具体实现的默认值或旧故渊自定 ｜ `[待标]` 无来源或未标定未实测。

---

## 0. 三条阅读约定

1. **证据强度 ≠ 数值正确性**。下表「依据」列回答的是「这条**结论**有没有独立来源支持」，不回答「这个**数**对不对」。绝大多数具体数字都是 `[工程]`——它是某个人拍的，不是研究出来的。
2. **报告一致 ≠ 独立验证**。五份报告在多处互相引用同一来源（详见 §3.2）。同一来源被三份报告引用，仍是**一份证据**。本文件已逐条去重。
3. **来源分两类**：「旧 25 维的缺陷」来自**同一份**内部文档 `task-14-affect-25dim.md` + 同一棵旧代码树；「新方案依据」来自外部文献。前者五报告重复列举 **不构成五次独立复核**。

---

## 1. 本体边界表

### 1.1 总表

> 「报告一致度」列：`一致`＝≥3 份报告同向；`分叉`＝报告间口径不同，指向 §3.1 的冲突编号；`单报`＝只有一份报告提出。

| # | 对象 | 归属模块 | 本体判据（cause / target） | 时间尺度 | 落点 | 权威写者 | 模型可写 | 失效方式 | 报告一致度 |
|---|---|---|---|---|---|---|---|---|---|
| 1 | **Core Affect**（V/A，±D） | Affect 运行态 | 无 cause、可无对象 | 分~天 | derived / 低频 snapshot | 确定性层 | ❌ | 解析式向 baseline 回落 | 一致 |
| 2 | **Emotion 片段**（portfolio） | Affect | **有 cause_ref + 可 target** | 秒~分（可数日） | 运行态 + 事件化 | 确定性层 + appraisal 候选 | ✅ 类型/依据候选 | 逐类型半衰期 **+ 重评/解决** | 一致 |
| 3 | **Mood** | Affect | **无单一 cause** | 时~天 | snapshot / derived | 确定性层 | ❌ | 慢解析式回 baseline（可归零） | 一致（形式分叉：标量 vs 向量，见 C5） |
| 4 | **Appraisal 变量** | Cognition（Affect 输入） | target＝事件；referent＝goal/standard/attitude | 瞬时 | ledger（可重算） | 模型产候选 | ✅ **仅候选** | **不衰减**（证据链） | 一致 |
| 5 | **Action Tendency** | **Affect×Motivation 接口** | cause＝emotion；无承诺 | 秒~分 | snapshot | 确定性查表 / 模型给方向 | ✅ 方向候选 | 随情绪回落 | **分叉 C2** |
| 6 | **Attitude**（对对象的长期态度） | 独立慢对象（**非 Affect 运行态**） | 有 target＝对象 | 周~月 | ledger / 版本链 | 证据累积（高门槛） | 🟡 候选 | **不按时间衰减**，按证据更新 | **分叉 C9** |
| 7 | **Attachment 取向**（焦虑/回避） | Relationship/Attitude（**耦合权重参数**） | target＝人；无单一 cause | 周~月 | ledger / derived view | 证据累积 | 🟡 高门槛候选 | 按证据更新 | 一致 |
| 8 | **Affect Annotation** | Memory 派生 | 锚 event | **冻结** | 不可变 | 编码期一次写 | ❌ | 不衰减、不改 | 一致 |
| 9 | **Affect Change Event** | Affect 长期留痕 | 有 cause（可归因时） | 事件时间 | ledger（append-only） | 确定性触发规则 | ✅ 候选 | 不变（历史） | 一致（触发条件分叉 C4） |
| 10 | **Chord / notation** | Affect **表达视图** | 无 cause | 三级 | derived view | 确定性映射 | ❌（gloss 例外，候选） | 跟随底层，可整层重算 | 一致（层名分叉 C3） |
| 11 | **Need**（自主/胜任/联结/探索） | Motivation 慢层 / Self | 无 cause、无 target | 周~月+ | derived（读时算，不落库） | 配置/人工设定 | ❌ | 近静态 | 一致 |
| 12 | **Drive**（强度） | Motivation | cause＝deficit×incentive；无 target | 分~时 | **derived**（不落每轮值） | 确定性层 | 🟡 仅候选（incentive 项） | 解析回落 + satiation/refractory | 一致 |
| 13 | **Motive** | Cognition/Self（类目） | 无 cause | 近人格 | baseline 标签 | — | ❌ | 不衰减 | 单报（02） |
| 14 | **Desire** | Motivation | **target 必需**；cause＝Drive×对象 | 分~时（对象在场） | **ledger（状态机）** | Desire 管理器 | 🟡 只产候选 | 满足/放弃/搁置 → 失效 | 一致 |
| 15 | **Goal** | Cognition（具体目标态） | cause＝deliberation | 视范围 | ledger | Deliberation | 🟡 候选 | 达成/放弃 | 单报（02） |
| 16 | **Value / Principle** | Self / Cognition | 无 cause | 长 | baseline（Stable Self） | 主体认领 | ❌ | 高门槛变更、留版本 | 一致 |
| 17 | **Intention** | Cognition（承诺） | cause＝deliberation + 认领 | 承诺期 | ledger（状态机） | Deliberation **+ 主体认领** | ❌ | reconsider / 达成 | 一致 |
| 18 | **Task** | 执行层 | cause＝登记 | 任务期 | ledger（状态机） | **独立登记步骤**（可要求 Permission） | ❌ | 完成/取消 | 一致 |
| 19 | **Concern** | Continuity / Memory | cause＝开放事件 | 天~周 | ledger | 升级规则（Desire→Concern / 开放事件） | 🟡 候选 | 显式关闭 / 长期无证据归档 | 一致 |
| 20 | **Wake candidate / Wake Gate** | 调度 | cause＝gate 越阈 | 瞬时 | 运行态（触发后事件化） | **确定性 gate** | ❌ | 消费即失效 | 一致 |
| 21 | **frustration**（受阻情绪） | **Affect / Appraisal**（明确**不是** Drive） | cause＝goal obstruction + agency | 秒~时 | 同 Emotion | 模型 appraisal 候选 | ✅ 候选 | 同情绪 | 一致 |
| 22 | **curiosity**（好奇） | Drive **+ 对象化 Desire** | cause＝信息缺口；target＝缺口 | 分~时 | derived / ledger | 候选 + 确定性 | ✅ 缺口候选 | 缺口闭合即满足 | 一致 |
| 23 | **longing / 想念** | Motivation（对象化 Desire） | target＝某人；cause＝分离×联结需要 | 分~天 | derived / ledger | — | ✅ 候选 | 接触满足 / 时间回落 | 一致 |
| 24 | **fatigue / 疲惫** | **Body / Runtime** | cause＝资源耗竭 | 时~天 | snapshot + 闭式回落 | 确定性层 | ❌（确定性算） | 休息回落 | 一致 |
| 25 | **nostalgia**（怀旧） | 复合情绪 / 记忆派生 | 有（记忆 + 自我） | 中 | derived | — | ✅ | **非 0.5h** | 一致 |
| 26 | **motivational intensity** | Affect 的一维（派生） | 由情绪/驱动派生 | 瞬时 | derived view | — | ❌ | 随情绪回落 | 单报（03） |
| 27 | **relevance（共享预读）** | Cognition↔Motivation 的**边**（不是独立对象） | target＝事件；referent＝活跃 concern 集 | 瞬时 | derived（每轮重算） | 模型精判 + 确定性粗门 | ✅ | 不衰减（每轮重算，带饱和） | 单报（03） |
| 28 | **D（dominance）** | **未裁**：Affect 偏置 vs appraisal/coping 输入 | — | — | — | — | — | — | **分叉 C1** |

### 1.2 三条不变量（五报无争议，本阶段确认为硬约束）

1. **Affect ≠ Motivation**：Affect 答「我现在感觉怎样」，Motivation 答「我被什么推动」。二者物理分存，**不允许压成同一标量**（03 §2.4-Q7/Q8 的核心论据：wanting≠liking，Berridge）。
2. **内部状态 → 对外行动必须过闸**：Drive / Desire / 情绪强度**只能 bias**；Intention 需 Deliberation ＋ 主体认领；Task 需独立登记。**旧「驱力超阈值直接驱动行为」必须删除**（01/02/03/05 一致）。
3. **衰减只能解析式 `f(t)`**：⛔ 禁止任何 per-tick 累积的 drive/mood meter；不活动 ＝ 无 tick ＝ 无累积。已核旧代码本就如此（`config/affect.yaml` 头注释 line 10：「衰减 = baseline + (x−baseline)·0.5^(Δt/half_life)，复用 core/decay.py」）。

### 1.3 边界争议（本阶段**未裁**，留给下一阶段拍板）

- **C1 · D（dominance）**：01 判「半属 appraisal/coping」（依据 WASABI 的 D 由认知层推断；Mehrabian 承认 D 与 potency/控制相关）；03 明确指出 01/02 **都没说清** D 在耦合里当 Affect 偏置还是 appraisal 输入；04 判「D→根音」降级/存疑。→ 三报三种口径，**未收敛**。
- **Attachment 的归属重叠**：01 把 attachment 归 Attitude/Relationship，02 归 Relationship/Attitude/Drive，03 拆成「慢取向 + 行为系统激活 + longing + 情绪片段 + annotation + concern」六类。→ 03 的六类拆分最细，建议以此为基准；但它引入「Period Relatedness Need 激活」这一对象，02 的 Need 表里没有独立项，属**接口未对齐**。
- **Motive（13）**：只有 02 提出，且 02 自己说「宜作类目/标签或并入 trait」。是否保留为独立对象＝未决。

---

## 2. 旧 25 维迁移表

> 旧对象＝ `guyuan/core/affect/schema.py`（`DriveId` / `PADCNAxisId` / `DiscreteChannelId`＋`AffectSnapshot` 三层）＋ `guyuan/config/affect.yaml` ＋ `guyuan/core/affect/{chord,synthesis,meaning_weight,layers}.py`。
> **已核（E3，2026-10-07 实读）**：25 维＝驱力 10 ＋ PADCN 5 ＋ 离散 10 的结构、10 个驱力名与「月/周＋人格调性 key」定位、PADCN 半衰期 36h/baseline 0.0、离散通道 0.5h、interlocks 8 条、meaning_weight 五档、bpm 40–120、`persona.key=C_major`、`key_table/chord_table` 为空、`chord.py` 的「先到先得 + `compose_25dim` + task-37 把 P 合进 drive 查表」——**与五报描述一致**。
> 「处置」列：`保留` / `改名` / `移出` / `拆分` / `降级` / `删除` / `待裁`。

### 2.1 驱力 10（旧定位：月/周 ＋ 人格调性 key；每维含 `expected`＋`half_life`＋`satiety_period`＋`threshold`）

| 旧维度 | 迁移目标 | 处置 | 依据 / 证据强度 |
|---|---|---|---|
| 表达欲 | Drive（≈SDT 自主 / Murray Exhibition）；若语义是「想被看见」则拆入 Concern/Relationship | **拆分** | 概念模糊，需拆分 —— [工程] 判断，E2 谱系支撑 |
| 关心欲 | 关系域 Motivation（relatedness 的「给予」面），由 Need 派生 | **移出为派生** | 指向关系对象，非无对象 baseline —— E1/E2 |
| 好奇 | Drive ＋ **对象化 Desire**（信息缺口） | **移出基线、按对象现算** | Loewenstein 1994 信息缺口，E2 |
| 玩心 | Self/trait 参数，或 Affect 倾向 | **降级为 trait** | SDT 视 play 为内在动机原型；做数值 Drive 违背其性质 —— E1/E2 |
| 连接欲 | Need(relatedness) → Drive | **归并入 Need** | SDT 三需要，E2 |
| 独处欲 | Desire / Action Tendency / 人格偏好；可被 fatigue 触发 | **移出基线** | SDT **无**对应基本需要（负向证据）—— E2 负向 |
| 疲惫 | **Body / Runtime State** | **移出 Motivation** | homeostatic 变量谱系 —— E2 |
| 安全欲 | Drive（回避向，BIS） | **保留**（但 BIS 敏感度是参数、当前强度是态） | Carver & White 1994，E2 |
| 想念 | **对象化 Desire（longing）**，target＝某人 | **移出基线、按对象现算** | Nocturne ＋ 对象性 —— E1/E2 |
| 胜任欲 | Need(competence) → Drive | **归并入 Need** | SDT 三需要，E2 |

> **本层结论**：10 个平行数值 → 收敛为 **Need 三+一（自主/胜任/联结/探索）→ Drive 现算**；疲惫移入 Body；想念/独处欲/好奇对象化；玩心降为 trait。旧「月/周 人格调性 key」把 trait 与 state 混层，是**时间尺度错误**。
> 驱动强度公式建议：`Drive = f(deficit, incentive, 环境, 记忆, 价值)`，**禁止自由自然增长**（02 答 3）——依据 homeostatic RL（Keramati 2014，E2）。

### 2.2 PADCN 5（旧值域 [-1,1]，半衰期 36h，baseline 0.0）

| 旧维 | 迁移目标 | 处置 | 依据 / 证据强度 |
|---|---|---|---|
| P 愉悦 | Core Affect（valence） | **保留** | Mehrabian 1995 / Russell 1980，E2 |
| A 唤醒 | Core Affect（arousal） | **保留** | 同上，E2 |
| D 支配 | Affect 偏置 **或** appraisal/coping 输入 | **待裁** | WASABI 的 D 由认知层推断（E2）；Mehrabian 承认 D 与 potency 相关 —— 见 C1 |
| C 确定性 | **Appraisal**（implication 类：certainty/expectedness） | **移出（明确错位）** | Smith & Ellsworth 1985 六维含 certainty；Mehrabian PAD 无 C —— E2 |
| N 新颖性 | **Appraisal**（relevance 类 SEC：novelty） | **移出（明确错位）** | Scherer CPM；Russell 1978「额外维是关于前因/后果的信念」—— E2 |

> **36h 这个数**：`[工程]` 旧故渊自定，**无外部依据**；且与两条新证据都不对齐（快层 relaxation 分钟级 `[理论]` Garcia 2016；事件对 baseline 扰动约 10 天 `[理论]` PMC13456410）。**判为 `[待标]`**，不是「错了」，是「没有理由」。
> 结论：core affect 收敛到 **V/A（±D，D 待裁）**；C、N 离开 Affect 进入 Appraisal。

### 2.3 离散 10（旧：Plutchik 8 ＋ nostalgia ＋ attachment，半衰期一律 0.5h）

| 旧通道 | 迁移目标 | 处置 | 依据 / 证据强度 |
|---|---|---|---|
| joy, fear, surprise, sadness, disgust, anger（6） | 表达/标签层（**不是本体**） | **降级为视图** | Plutchik 1980，E2 |
| trust, anticipation | 同上，但**是否属「基本情绪」存疑** | **降级 + 存疑** | 学界有争议 —— [待标] |
| nostalgia | 复合情绪 / 记忆派生 tint（可选标注） | **推翻 0.5h 通道定位** | Sedikides 2015 / Van Tilburg 2018：自我相关、过去指向、mostly positive、低唤醒 —— E2 |
| attachment | Relationship / Attitude / **耦合权重参数** | **推翻（分类错误）** | Bowlby：情感联结/行为系统，**不是情绪** —— E2 |

> 10 条离散通道一律 0.5h 半衰期 = `[工程]` 旧故渊自定。经核实 `config/affect.yaml` 中这 10 条**逐条写死 0.5h**，未见逐类型差异——与「半衰期须逐类型可配」的结论直接冲突（01 Q6）。
> **interlocks 8 条**（fear→joy suppress 0.6、anger→trust 0.6、sadness→joy 0.5、disgust→trust 0.5、fear→anger 0.4、joy→sadness 0.3、trust→fear 0.3、surprise→joy amplify 0.2；threshold 0.3–0.5）＝ `[工程]` 自定，**不得当情绪生成层**，只能作表达/标签层冲突规则。

### 2.4 机制 / 参数 / 表达层迁移

| 旧机制 | 处置 | 理由 |
|---|---|---|
| 解析式衰减 `x0*0.5^(dt/half_life)` | ✅ **保留（红线）** | 可重算、不 tick；全报告地基 |
| 单一时钟 `core/clock.py` | ✅ **保留** | core 之外不得有第二处 `datetime.now()` |
| 数值层读时现算、衰减值不落库 | ✅ **保留** | 与 ES / derived 一致 |
| unknown 不填默认值 | ✅ **保留** | 与 provenance 一致 |
| Chord 确定性查表、零 LLM | ✅ **保留**（归表达层） | 可复现/可审计/可单测/可整层重算 |
| PADCN 稳态回弹（向 baseline=0 指数回归） | 🟡 **保留但收敛** | 方向对；不能推广到 attitude/attachment/longing |
| `meaning_weight`（note 2.5 / reflection 4.0 / mention 1.8 / none 1.0 / let_go 0.5） | 🟡 **保留但归位** | 本质＝「有效半衰期 × 倍数」，应并入 **Affect Change Event / 显著性**，**不是 core affect 参数**；数值 `[工程]` 自定 |
| 驱力 `threshold`（行为触发阈值）/`satiety_period` | 🟡 **部分保留** | `expected` 可作 baseline/need；`satiety_period` → satiation 不应期；**`threshold` 那条越权路径删除** |
| ~~「驱力超阈值直接驱动行为，独立于情绪」~~ | ❌ **删除** | 越权边，与 02 §7 / 05 §6 / 00c §11 护栏冲突 |
| wake loop「唤醒时三层演化→查表→注入」 | 🟡 **结构保留，语义收窄** | 唤醒时用 `f(t)` 现算；注入为**软信号**；不得由强 Drive 越级生成 Task |
| serein 异步副 LLM 产 PADCN | ✅ **保留但归位** | 属 appraisal 批处理，**只产候选**，不进实时链 |
| `persona.key`（C_major / drift_rate_per_day 0.0002 / switch_threshold 0.5） | ❌ **推翻（作为 Affect 的一部分）** | 由 Drive 查表得 key ＝ 把 Motivation 塞进 Affect 表达；这就是「P=−0.8 却 G 大调」的结构性根因 |
| `progression`（实为单个查表单和弦） | 🟡 **降级 / 改义** | 源码 `compose_25dim` 只产出单个 `progression_chord`；建议改指「近窗轨迹」 |
| `momentary chord` | 🟡 **保留为 Current 视图，重定义输入** | 输入白名单只留 Affect 维（P/A/±D） |
| BPM 由 A 线性映射（40–120） | ✅ **保留为独立连续参数** | 三模型一致认可；tempo↔arousal 有神经证据，E2 |
| `velocity` 由 A 映射（`min_velocity` 默认 0，另有 `V2_VELOCITY_MIN`） | 🟡 **保留但加感知曲线 + 下限** | 旧 `velocity=6` 争议；参数 `[工程]` |
| C→和声功能、N→外音 两条映射 | ❌ **删除** | C/N 非 core affect（同 §2.2） |
| nostalgia / attachment 的特殊和弦映射 | ❌ **推翻** | 同 §2.3 |

### 2.5 核验附注：五份报告**未覆盖**的旧配置（本阶段实读发现）

以下三项在五报中**均未出现**，但确实存在于 `config/affect.yaml`，直接影响「旧 25 维」的完整性判断：

1. **V1 兼容层 `axes:`（line 196–208）**：valence(half_life 0.5h) / arousal(0.25h, baseline **0.3**) / **tension**(0.33333h)。→ **`tension`（张力）是一条第四类情感轴，既不在 PADCN 5 里，也不在离散 10 里**；五报的「25 维」枚举对它零覆盖。
2. **V1 分层半衰期 `layers:`（line 213–240）**：persona(valence 4320h／tension 2160h，update_scale 720h)、recent(valence 72h／arousal 36h／tension 48h，update_scale 24h)。→ 于是同一文件里存在**三套并行的半衰期定义**（`drives`/`padcn`/`discrete` 块、`axes` 块、`layers` 块），且 `recent.arousal` 的 36h 与 `padcn.arousal` 的 36h 只是**巧合相同**。报告把「36h」当成 PADCN 的唯一时间常数，**不完整**。
3. **两套 velocity 常量**（`min_velocity` 默认 0，`V2_VELOCITY_MIN`，`bpm_from_arousal` 与 `bpm_from_arousal_v2` 并存）＋ `chord.py` line 449 的 `task-37: 合并 P 到 drive_values` —— 后者是「为了给错配打补丁而把两层搅在一起」的实证，报告 04 已指出，此处确认源码真在。

> **结论**：所谓「旧 25 维」在配置层其实是 **25 维主表 ＋ 3 维 V1 轴 ＋ 两层 V1 半衰期表**。迁移表若只处理 25 维，会漏掉 `tension` 与 `layers` 两处。**建议下一阶段把 `axes`/`layers`/`chord_table`/`key_table` 明确定为「死代码——随 V1 一起删」，或明确保留理由**，否则新方案会带着一层没人记得的旧状态跑。

---

## 3. 冲突与证据血缘仲裁

### 3.1 冲突清单（按严重度排序）

| # | 冲突 | 各方口径 | 我的裁定建议 | 严重度 |
|---|---|---|---|---|
| **C4** | **长期事件的触发条件** | 01 Q7 与 05 Q5：「三选一（可组合），**任一即触发**」；04 Q8：「**阈值作候选 ∩ 持续作门 ∪ 认领作覆盖**」，明确反对「任一即触发」（纯阈值＝噪声爆炸，纯持续＝漏骤变，纯认领＝靠模型心情） | **采纳 04 的三段式**。01/05 的「OR」在工程上会过写，04 给了逐条反例 | 高 |
| **C9** | **Attitude 挂在谁身上** | 01 Q8：「**不留在 Affect 运行时**，是独立长期对象」；05 §7.1 却把 `attitude[object]` 列进「**Affect 长期对象**」清单 | **采纳 01**：Attitude 是独立对象（Relationship/人物模型），Affect 只作它的输入证据。05 的归类是文档归属表述，若被实现读成「Affect 模块持有并写入」就与 01 冲突 | 高 |
| **C2** | **Action Tendency 是 Affect 输出还是跨模块边** | 01 当 Affect 输出；02 当 Affect×Motivation 接口；03 采用 02 并说明是跨模块边 | **采纳 02/03**：AT 是**边上的对象**，由情绪确定性映射产生、供 Cognition 消费，不属任一模块的属性 | 中 |
| **C8** | **appraisal 精判在不在实时链** | 05 Q16：「appraisal 只产候选，**进批处理派生，不进实时链路**」；03 模型 B 把「模型精判」画在事件摄入路径上，与 relevance 粗门并列 | **两者兼容但必须写死**：确定性粗门（实体/关键词匹配活跃 concern）可实时；**模型精判必须落短延迟/后台层**。03 的图需加注「精判≠同回合同步」 | 中 |
| **C1** | **D（dominance）归属** | 01「半属 appraisal」；03「未说清」；04「降级/存疑」 | **本阶段不裁**。最小风险做法：D 默认**不进 core affect 主体**，只作 appraisal/coping 的输入输出之一；是否保留为 Affect 第三维作为下一阶段的独立选项 | 中 |
| **C3** | **时间层命名** | 04 主张**弃用 Recent**，改 `current / lingering / baseline`（Recent 混淆「时间窗」与「未散去的余波」）；05 Q6 仍用 `Current / Recent / Baseline` | 机制无冲突，**纯术语**。采纳 04 的命名；05 的「Recent＝滚动窗口聚合」等价于 04 的次选 `window-mean` | 低 |
| **C5** | **Mood 是标量还是向量** | 01：方案 A 标量 / B 向量，未定；03：沿用 FAtiMA 标量 + 双向 0.3 偏置 | **不裁**（属下一阶段的轴集选择）。但**必须先定**，因为它是 §4 里 A↔M 边的载体 | 低 |
| **C10** | **nostalgia 是否保留** | 01「层级错位」；04「推翻瞬时通道，但允许作记忆派生 tint 的可选标注」 | 采纳 04：**不进瞬时查表**，可作记忆派生的可选标注 | 低 |
| **C11** | **旧 36h 是否错** | 01 只是列出 36h 并在 Q6 说「单一全局半衰期是工程简化」 | **判为 `[待标]`**，不是错；但**不得**作为新方案的默认值直接沿用 | 低 |

### 3.2 证据血缘去重表（**关键**：哪些「多处引用」其实是同一份证据）

> 左列＝来源；「出现处」＝引用它的报告；「实读者」＝真正读过原始材料的那一份；「独立证据数」＝去重后算几份。

| 来源 | 出现处 | 实读者 | 独立证据数 | 备注 |
|---|---|---|---|---|
| **FAtiMA-Toolkit 源码**（`ActiveEmotion.cs` / `Mood.cs`） | 01、03、05 | **01 实读 master HEAD**；03/05 均标「转引」 | **1** | ⚠️ **「情绪↔mood 双向偏置系数 0.3」在 01/03/05 三处出现，实为同一份源码里的同一个默认值**。且 01 读的是 master HEAD，memory-affect-01 §04 读的是 commit `56b7cbd`，**两版未比对** |
| **Jason 源码**（`Intention.State` / `selectOption`） | 02、03、05 | **02 实读**（commit `2c2d7e1c`）；03/05 转引 | **1** | 「Intention≠Task」的护栏依据只有这一条源码链 |
| **pymdp 源码** | 02 | 02 | 1 | `main` 浮动，未固定 commit |
| **emotional_memory 源码**（`adaptive_weights` / `resonance`） | 01?(snapshot)、03、05 | 01 `_snapshots/…/emotional-memory-*.py`；05 自称「01/05 已核」 | **1** | 03 明标「转引 05」 |
| **旧故渊源码树** | 01、02、03、04、05 | 01 实读 `schema.py`＋`affect.yaml`；04 实读 `chord.py`＋`synthesis.py`＋两张 yaml；**05 明说未重读、引 task-14** | **1 棵树 / 3 段部分实读** | ⚠️ 五报的「旧方案复核」＝**同一棵树的重复引用**，不是五次独立复核 |
| **`task-14-affect-25dim.md`**（旧方案的第二手来源） | 02、03、04、05 | 无（均引其记载） | **1** | ⚠️ **旧 25 维最常见引文其实是一份内部任务卡，不是代码**。本阶段已用源码校正其主要项（一致） |
| **`memo/Nocturne-Memory-Core.md`** | 01、02、03、04、05 | 无（均引） | **1** | 「AFFIRM/REJECT/SUSPEND」「弱化时间衰减」「BIAS NOT SCRIPT」全出自这一份内部备忘 |
| **`00b` / `00c`**（memory-affect-01） | 02、03、05 | 无（均引） | **1** | 「Concern 无必需 want」「Wake 软信号」出自同一处 |
| **Generative Agents**（arXiv 2304.03442） | 01、04、05 | 01/04/05 均引同一论文 | **1** | recency 0.995 / importance 阈值 150 被三处引用 |
| **Berrios 2015 meta**（d≈0.77） | 01、04 | 同上 | **1** | 「混合情绪稳健」只有一个 meta 来源 |
| **Keramati & Gutkin 2014** | 02、03 | 同上 | 1 | drive 距离/reward＝削减 |
| **OCC / Scherer CPM / Smith&Ellsworth / Russell / Mehrabian / Plutchik** | 01（主）、03、04 | 01 | 各 1 | 03 的 OCC/CPM 是独立表述但同一原文；04 引 01 的 C/N 结论 |
| **Bowlby（attachment 非情绪）** | 01、02、03 | 各引 | 1 | |
| **Gross / Schwarz & Clore** | 01、03 | 各引 | 各 1 | |
| **Frijda 系** | 01 §4.10（**Frijda et al. 1991 时长研究，经开放教材转述**）；02 §4.6（**1986 经 `PMC3246364` 综述**）；03 §4.2（1986 原著＋Fontaine&Scherer 2013 GRID） | **原著均未直读** | **≥2 份不同材料，但都不是原著** | ⚠️ **同名作者的三个不同材料**——不要把 01 的「50% 情绪 >1 小时」与 02/03 的「AT 是情绪构成成分」当成互相印证 |
| **Sentipolis**（arXiv 2601.18027） | 01、04 | 引论文，**代码/许可是否开源未核** | 1 | |
| **Echo Gap 2608.00017 / howlround 2504.07992** | 03（转引 05）、05 | 05 | 1 | 「误差独立性」这条硬约束只有这一条链 |
| **`和弦映射跨模型验证报告.md`**（2026-10-01，内部） | 04 | 04 | **1** | ⚠️ **「P=−0.8→G 大调」这个最关键 bug 只有这一份内部弱测**（3 模型、5 用例、prompt 含完整规则、**非盲测**）。文件确实存在（已核） |
| **`chord-affect-anchors`**（E1） | 04 | 04（只读到 README，**未读正文、无源码**） | 1 | Q12 的唯一公开先行件 |
| **TGL / heartbeat / 2601.16087 / BOCPD / 2604.27872 / Overeem 2021 等** | 05 | 05 | 各 1 | 数字均「论文声称，未复现」 |

**血缘仲裁的三条结论**：

1. **五报的「共识」有很大一部分是引文回声**。最典型：情绪↔mood 的 `0.3`、Intention 状态机、importance 阈值、混合情绪 d≈0.77 —— 都是**单来源、多引用**。把它们写成「多份报告一致支持」会**高估**证据强度。
2. **旧方案的缺陷清单同理**：五报各自独立地"复核"了旧方案，但用同一棵代码树＋同一份 `task-14`。若旧方案真有一处错，应该是**一次**发现被复制五遍；若五处描述互有出入，那才说明有人真读了代码——本阶段实读源码，**未发现与五报的实质出入**（除 §2.5 三项遗漏）。
3. **最弱的一环是「和弦映射跨模型验证报告」**：它是 04 号报告最大 bug 陈述的唯一来源，内部、非盲测、prompt 泄露规则。**它是线索，不是证据**。

### 3.3 未核实与必须降级的清单（沿用五报自陈，本阶段未补做）

| 项 | 状态 | 处理 |
|---|---|---|
| Sentipolis 代码/许可是否开源；跨模型数字 | 未核 | 只作方向参照 |
| FAtiMA master HEAD vs commit `56b7cbd` 的差异 | 未比对 | 引用其结构时标注 |
| `chord-affect-anchors` 正文与 pilot 原始数据 | 未读 | 「粒度随上下文密度提升」只作经验，不作依据 |
| 情绪↔mood `0.3`、偏置界 `b`、环路增益、触发阈值/N、BOCPD 成本、二阶动量 | **未标定/未实测** | 全部 `[待标]` |
| 心理 need 的 setpoint 与量纲 | **无客观值**（02 自陈最大风险） | `[待标]`；须可配、可审、可版本化 |
| LLM appraisal 的稳定性与成本 | 未测 | 只作批处理 |
| Chord 跨模型语义稳定性 | 无干净基准 | 列待办，**不采信他方数字** |
| E-STEER / arXiv 2510.13195 / 2507.22326（LLM agent 情绪→行为） | 论文声称，未复现、未核代码 | E1，只作旁证 |

---

## 4. Affect × Motivation 新信息流（第一阶段版本）

> 采用 03 的**模型 B（双通道并行 ＋ 共享 relevance 预读）**作主干，配 03 的**模型 C 的负反馈与 effort 上界**；A 为其降级版，D（单标量直连）为反例。
> 记法：`[确定性]` 数值/规则可算 ｜ `[模型]` 只能产候选 ｜ `(W)` 可写长期状态 ｜ `(w)` 只运行态/候选 ｜ `⟳` 该边带阻尼。
> **字段不是 schema**——只写"这条边流的是什么语义"。

### 4.1 主干

```
[事件摄入]
Event(主体/动作/对象/时间 + 视角) × referent_set(活跃 goal/standard/value/concern/attitude/need)
        │
        ▼
共享 relevance 预读   [确定性粗门(实体/关键词匹配活跃 concern) + 模型精判]
        │             └─ 只决定「进不进 appraisal / 算不算 incentive」；带饱和 ⟳
        ├──────────────────────────────┬──────────────────────────────┐
        ▼                              ▼                              │
┌── 通道 A（Affect）────────────────┐  ┌── 通道 M（Motivation）──────┐ │
│ appraisal 变量 [模型] (w)          │  │ relevance + incentive       │ │
│   → Emotion 类型/强度             │  │   → Drive 重算 [确定性] (w)  │ │
│     [确定性映射为主] (W, 带cause) │  │   → Desire 候选 (w)          │ │
│   → Action Tendency [确定性查表]  │  │   → (deficit×incentive)      │ │
│     (w, 带 source_emotion_ref)    │  │                              │ │
└───────────────────────────────────┘  └──────────────────────────────┘ │
        │                              │                              │
        │◄──── 跨通道有界交叉边 ────────►│                              │
        │   M→A: Drive 水平→appraisal 强度调制；受阻→frustration [模型] ⟳
        │   A→M: Emotion/Mood→Desire 强度 ×(1±b) [确定性, clamp] ⟳
        │   A→M: AT→候选优先级 [bias only]
        │   参数: attachment 取向 → 回环增益 g [确定性参数] ⟳
        │
        ▼
[Cognition Deliberation]  ← Values/Principles 作闸门
        │  + 主体认领
        ▼
   Intention (W, ledger 状态机: running/waiting/suspended/satisfied/dropped)
        │  经 independent 登记步骤 (可要求 Permission)
        ▼
      Task (W, 唯一可执行对象)

侧路（默认沉默）:
  Concern / Commitment / Task / Trigger + 外部状态 + 权限 → [确定性 gate] → Wake candidate
  Drive/Desire/情绪 → 只贡献「值得重新考虑」的软分，不得越级
```

### 4.2 边的属性表

| 边 | 方向 | 确定性 / 模型 | 可写状态 | 阻尼 ⟳ | 依据 / 强度 |
|---|---|---|---|---|---|
| E0 | Event × referent_set → relevance 预读 | 粗门 `[确定性]` ＋ 精判 `[模型]` | (w) | 饱和 | CPM relevance 最先（E2）；**精判须落后台层**（见 C8） |
| E1 | appraisal 变量 → Emotion 类型/强度 | 变量 `[模型候选]`；映射 `[确定性]` | **Emotion (W, ledger)** | 逐类型半衰期 | OCC/FAtiMA（E2/E3） |
| E2 | Emotion → Mood | `[确定性]` | Mood (w) | 基线回落 ＋ **k≤0.3** <br>⚠️ 单来源（FAtiMA） | E3 单实现 |
| E3 | Mood → appraisal 强度偏置 | `[模型读]`，有界加性 \|Δ\|≤b | (w) | 饱和 | affect-as-information（E2）；**b 未标定** |
| E4 | Emotion/Mood → Desire 强度 | `[确定性]` 乘性 ×(1±b)，clamp | Desire (w) | clamp + 回落 | wanting≠liking（E2）；b `[工程]`≈0.2–0.3 |
| E5 | Emotion → Action Tendency | `[确定性查表]` 或 `[模型给方向]` | **AT (w)** | 随情绪回落 | **定义性成分**，非影响边（Frijda，E2） |
| E6 | AT → 候选优先级（供 Cognition） | `[确定性]` bias | (w)，**不直接选** | — | Steunebrink 2009（E2） |
| E7 | Drive/Desire → appraisal relevance 抬升 | `[确定性]` | (w) | 饱和 | 00c §7.3 ＋ 02 答 12（E1/E2） |
| E8 | Drive 受阻 → goal_conduciveness<0 ＋ agency → frustration | `[模型候选]` | Emotion (W) | 同情绪 | OCC/CPM（E2）；**frustration 归 Affect** |
| E9 | Drive/Desire → Wake candidate 软分 | `[确定性 gate]` | (w) → 触发后事件化 | 消费即失效 | 00c §11（E1）；TGL（E2，未复现） |
| E10 | Desire → Intention | Desire 只产 `(w)` 候选；**Deliberation ＋ 认领**才写 (W) | Intention (W) | reconsideration 触发条件（防 thrash） | BDI/Jason（E2/E3） |
| E11 | Intention → Task | **独立登记步骤**（可要求 Permission） | Task (W) | 完成/取消 | BDI（E2/E3） |
| E12 | attachment 取向 → 回环增益 g | `[确定性参数]` | 慢对象 | 按证据更新 | Mikulincer：焦虑超激活 / 回避去激活（E2） |
| E13 | reappraisal / let_go → 主动降温 | `[模型候选]`，**有动机来源、有成本** | — | 显式降温 | Gross（E2）；Nocturne（E1） |
| E14 | 误差独立的外部信号 → 纠偏 | `[确定性]` | — | 环路增益 <1 的必需条件 | Echo Gap（E2，单链） |

### 4.3 硬护栏（从信息流里**禁止**出现的边）

1. ❌ 情绪 / mood / Drive / Desire → **直接** Task 或对外行动（旧「驱力超阈值驱动行为」）。
2. ❌ 情绪 / mood 作召回或欲望的**硬门**（只能进排序层/强度层）。
3. ❌ 驱动满足度 → **直连**情感轴（必须经 appraisal 的 goal_conduciveness）。
4. ❌ 驱力色彩进入 Chord（Motivation 借表达层回流 Affect）。
5. ❌ 单一标量同时表示「想不想」与「感觉好不好」。
6. ❌ 心跳每跳调 LLM；❌ 每 tick 累加 drive/mood。
7. ❌ 模型自由改写高权威对象（长期态度 / 核心身份 / Value）。

---

## 5. 数值参数三档标注表

### 5.1 `[理论]` —— 有外部理论/研究支持（注意：**多数只有单一来源**）

| 参数 / 结论 | 值 | 来源 | 单源？ |
|---|---|---|---|
| 情绪时长高度可变；50% 被回忆情绪 >1h | — | Frijda 1991（**经开放教材转述，未直读**）＋ Verduyn 2013/2015 | 部分重叠 |
| 时长排序 悲伤 > 内疚 > 愤怒 > 羞耻 > 厌恶/恐惧 | — | Verduyn | 单源 |
| 强度与时长仅弱-中相关 | — | Verduyn | 单源 |
| 秒级情绪脑态约 5–15s 转换 | 5–15 s | PMC9026845 | 单源 |
| 快层 relaxation 时间常数 | τ_v≈2.7 min / τ_a≈2.4 min | Garcia 2016 | 单源 |
| 事件对长期情感态的扰动回归 | 约 10 天 | PMC13456410 | 单源 |
| 个体基线「略偏正、唤醒中低」 | — | Facebook EPJ 2019 | 单源 |
| 混合情绪正负同现稳健 | d≈0.77 | Berrios 2015 meta | 单源 |
| 环路增益 <1 / 需误差独立的外部信号 | — | 控制论 ＋ Echo Gap | 单链 |
| effort 上界 ＝ min(难度, 值得) | — | Brehm & Self 1989 | 单源 |
| 高动机强度收窄注意 / 低拓宽 | — | Gable & Harmon-Jones | 单源 |
| mode↔valence、tempo↔arousal 两轴 | — | Eerola；Trochidis & Bigand（EEG） | 有神经证据 |
| 整体情感符号只能可靠到「区域」 | 仅 anger→红 >75% | Universal Visualization 2026 | 单源 |
| certainty 属评价维、非 core affect | 6 个正交评价维含 certainty | Smith & Ellsworth 1985 | 单源 |
| Mehrabian PAD 无 C、N | 3 近正交轴 | Mehrabian 1995/1996 | 单源 |

### 5.2 `[工程]` —— 来自某个具体实现的默认值，或旧故渊自定（**无外部依据**）

| 参数 | 值 | 出处 |
|---|---|---|
| 情绪↔mood 双向偏置系数 | **0.3** | FAtiMA（E3，单实现；⚠️ 被 01/03/05 三处引用） |
| 情绪 / mood 半衰期 | ≈15 tick / ≈60 tick | FAtiMA |
| `IsRelevant` 强度阈值 / mood 值域 / 归零阈值 | 0.1 / [-10,10] / `MinimumMoodValueForInfluencingEmotions` | FAtiMA |
| 情绪强度聚合 | log-sum-exp（`ReforceEmotion`） | FAtiMA |
| recency 衰减因子 / importance 反思阈值 | 0.995 / 150 | Generative Agents |
| 唤醒调制衰减 | `base×(1−0.5·arousal)×1/(1+0.1·count)` | emotional_memory（E3） |
| 召回心境调制门 | tanh / 高斯，*not a hard filter* | emotional_memory |
| 旧故渊：PADCN 半衰期 / baseline | **36h / 0.0** | `config/affect.yaml`（本阶段实读核实） |
| 旧故渊：驱力半衰期 | 168h（7 天） | 同上 |
| 旧故渊：离散通道半衰期 | **一律 0.5h** | 同上（**与"逐类型可配"结论冲突**） |
| 旧故渊：meaning_weight | note 2.5 / reflection 4.0 / mention 1.8 / none 1.0 / let_go 0.5 | 同上 |
| 旧故渊：interlocks | 8 条，strength 0.3–0.6，threshold 0.3–0.5 | 同上 |
| 旧故渊：bpm 区间 / persona key / drift | 40–120 / `C_major` / 0.0002·day⁻¹，switch 0.5 | 同上 |
| 旧故渊：V1 轴 `tension` 半衰期 / V1 `layers` | 0.33333h / persona 4320h·2160h，recent 72h·36h·48h | 同上（**五报均未覆盖**） |
| 偏置界 b | ≈0.2–0.3 | 由 FAtiMA 0.3 外推 |
| VPS 规格 / 每轮 CPU / 存储量 / 日志缩减 | L1 2vCPU·2GB；<1ms；2–12KB/对话；50–100× | 05 的**工程推算**（`basis: world_prior`），非实测 |

### 5.3 `[待标]` —— 无来源、未标定或未实测（**不得当默认值使用**）

- 长期事件的**幅度阈值**、**持续窗 N**、**最短驻留时间**（迟滞上下阈）。
- **心理 need 的 setpoint 与量纲**（02 自陈最大风险）。
- 环路增益的**具体数值**与限速/冷却参数。
- 二阶动量阻尼系数；`lingering` / `baseline` 在故渊场景的**时间常数**。
- `satiation` 不应期时长；Desire → Concern 的升级阈值 T。
- BOCPD 在线成本（剪枝/限维）——未实测。
- LLM appraisal 的**每对话调用次数**与稳定性。
- Chord 跨模型语义稳定性——无干净基准。
- `velocity` 感知曲线参数。
- TGL 的 11.13 ms/event、约 220 MiB BF16 —— **论文声称，未复现**。
- 旧 `36h` 与新证据（分钟级快衰减 / 十天级基线扰动）的对齐值。

---

## 5.4 后续来源校正：Chord 语义需拆分理解

在第一阶段完成后，补做了 `CyberSealNull/chord-affect-anchors` 的固定版本来源追溯。该项目的外部来源最高为 E2；固定树中无实现源码。详细见：

`docs/requirements/memory-affect-02/user-references/Chord-affect-anchors-reference-report.md`

这份来源追溯对第一阶段 **Chord 相关结论作如下语义校正，但不重写本阶段本体边界与信息流**：

1. 原项目的核心是**历史体验的音乐化记忆锚**：具体情境 + 手写/选择的 chord progression。
2. 原 `progression` 表示**单次体验内部的感觉展开**，不等于当前候选中的 Recent / Lingering，也不构成 Baseline 的来源依据。
3. 故渊后续的“数值状态 → 确定性查表 → Chord”、三层覆盖和 runtime 注入属于自身改造，不能反向归因给原项目。
4. 后续讨论应区分：
   - **Historical / Annotation Chord**：过去体验的表达；
   - **Runtime / State Chord**：当前 Affect State 的派生投影。
   两者可以共用音乐语言，但来源、生命周期、权威不同。当前仅做语义分离，**不预设 schema 必须拆成两个正式对象**。
5. 历史 Chord 被召回时只作为当前输入；若产生新的情绪反应，应形成新的 appraisal / affect 过程，不能把旧 Affect Annotation 直接恢复到现在，也不能回写旧记录。
6. `Chord 字符变化`、`Numeric Affect Δ`、`Affect Change Event` 是三个不同层次，不能互相等同。
7. `Baseline Chord` 的必要性重新标为 **OPEN**。原项目没有 baseline；旧 persona key 也不能作为新 Baseline Chord 的充分依据。

因此，本阶段 §6 的“Chord 表达层”移交问题应按以上语义继续进入后续压力测试与第二阶段，而不是把 Current / Recent / Baseline 当成原项目继承来的三层。

---

## 6. 第一阶段结论与移交开口

**本阶段确立（可直接进入下一阶段作为约束）**：

1. 本体边界见 §1；**28 个对象、3 条不变量、3 处未裁争议**。
2. 旧 25 维迁移见 §2，且**追加三项五报未覆盖的旧配置**（`tension` 轴、V1 `layers` 半衰期、两套 velocity）——须在下一阶段显式处置。
3. 冲突 9 条，其中 **C4（触发条件用 04 的三段式）与 C9（Attitude 不属 Affect 模块）建议直接采纳**；C1（D）留待轴集阶段。
4. 证据血缘见 §3.2：**多条被当作"多来源一致"的结论实为单来源多引用**，最典型是 FAtiMA 的 `0.3`、Generative Agents 的阈值、`task-14` 与 `Nocturne` 备忘，以及 04 号报告最大 bug 的唯一来源（内部非盲测的跨模型报告）。
5. 信息流见 §4（模型 B ＋ C 的负反馈）。

**移交下一阶段（第二阶段）的开口清单**——本阶段刻意不碰：

| # | 开口 | 需拍板的点 |
|---|---|---|
| 1 | **数值轴集** | core affect 用 V/A 还是 VAD？D 去留（C1）；mood 标量还是向量（C5） |
| 2 | **触发参数化** | 阈值/持续窗/认领三段式的具体值与可配方式 |
| 3 | **need setpoint** | 三+一需要的 setpoint、量纲、可审机制 |
| 4 | **Appraisal 变量最小集** | CPM 序贯 SEC 落到几个变量；谁产、何时产、落在哪一层 |
| 5 | **Chord 表达层** | 选 A/B/C/D；锚点集与一致性校验层；旧 `tension` 是否进映射 |
| 6 | **运行时形态** | L1/L2/L3 取舍；Affect ledger 与 snapshot 的具体落点 |
| 7 | **旧配置收尾** | `axes`/`layers`/`key_table`/`chord_table` 明确定为删除或保留 |
| 8 | **未验证项** | 跨模型 Chord 语义一致性自测；LLM appraisal 成本实测；触发门槛压测 |

> 本文件不含 schema、不含存储选型、不含阈值定值——按第一阶段约定，这些全部留到下一阶段。
