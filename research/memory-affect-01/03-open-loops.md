# 03 未完成事项与持续关切

> 专题 03 报告 · memory-affect-01 · 2026-10-07
> 只研究外部机制与可复用实现，不盘点 Serein／故渊现状；两份 memo 只作问题来源。证据口径见 §10，默认最高 **E3（只读源码）**。本轮未安装/运行任何候选，未发模型请求。

## 1. 故渊要解决的问题

**需求**：怎样识别"这件事还没结束、在等待或受阻"，并把它作为可追踪、可召回、可演变的内部结构保存下来，**又不擅自替主体规划接下来要做什么**？必须回答：①什么证据足以判定未完成；②怎样避免把兴趣变成计划；③waiting/blocked/paused/abandoned/resolved 等状态如何表达；④怎样把事项与事件线、承诺、外部等待、真实任务关联；⑤什么只应成为记忆、什么可成为唤醒候选。

**边界**：只定"未完成事项的对象、状态维度、证据门槛、与其它对象的引用、记忆/唤醒的分界"；不定最终 schema、不定调度协议、不设计情绪数值（归 04/05）、不定事件线本体字段（归 02）、不定世界书正文（归 06）、不定召回排序（归 07）。

**接口**：←01 的 `commitment`（共同约定）与 `open-loop state`（事项状态）；←02 的 event/line id（用于归线与进展边）；←04/05 的情绪对"关切度"的软影响；←06 的 person-world entry（承诺常涉及对方）；→07 的召回与整理；→ 运行层的"候选触发"契约。

**不解决**：不把研究建议升级为用户决定；不实现任务执行器；不预设同库；不以 Serein 的约定台账／Arc／Event 为上限。

## 2. 关键概念与必要区分

### 2.1 统一坐标（沿用 01，逐项落到本专题；只增一处切分，见末行）

| 对象 | 本专题定义 | 可变性 |
|---|---|---|
| 证据 evidence | 不可变原始单元（对话原文/附件/工具回执），带来源+作者+写入时间；判"未完成"的一切依据只能来自它 | 只增不改 |
| 事件 event | 从证据识别"发生了什么"，指向证据，不带评价 | 写定即不可覆盖 |
| 派生事实 fact | 从证据抽出的可断言语句（如"用户说过要在 X 前完成 Y"） | 可重算 |
| 当前理解 interpretation | 主体当下对某事项意义的判断（含"这还没完"） | 可更新，不回写前二者 |
| 共同约定 commitment | 双方共同确立、跨会话有效的约束/共识 | 历史不可变，状态可重算 |
| 事项状态 open-loop state | 某线/约定/任务的进行状态（未完/等待/受阻/已结） | 可重算 |
| 情绪状态 affect state | 对某锚点对象的情绪评价（本专题只读不改） | 衰减可重算，历史留痕 |
| 人物世界条目 person-world entry | 关于某主体/实体的聚合条目 | 聚合可重建 |

**本专题增切分（明写理由）**：文献里"commitment"同时指①双方共同约定（social commitment，即 01 的 `共同约定`）、②单方许诺（agent_promise/pledge）、③单方意图（intention）。三者证据门槛与状态机不同，故在 `open-loop state` 之下加一个**来源子类 `kind`**：`shared_commitment`（双方，沿用 01）／`pledge`（单方对他人许诺）／`intention`（单方愿望或打算）／`task`（已登记、可执行的真实任务）。**不换对象名，只加分类字段**，与 01/06 对齐。

### 2.2 四组必须分清的能力

- **未完成 ≠ 自动规划未来**：判定"没结束"只需历史证据；替对方定"下一步做什么"是另一件事，必须由主体决定。
- **兴趣/愿望 ≠ 承诺/任务**：心理侧（Einstein & McDaniel 1990）与工程侧（GTD、OpenClaw）都把"未来意图"与"一句想法"分开——**触发条件是否成立、是否有明确时窗**是分界。
- **事件归线 ≠ 事项管理**：一条 Arc 可以结束而事项未完，反之亦然；归线回答"是不是同一件事"，状态机回答"它到哪了"。
- **记忆被记下 ≠ 可唤醒**：所有状态变更都该进记忆；只有满足"未来时点/条件 + 可达触发源 + 时窗未过 + 未被取消/完成"者才进唤醒候选（对齐 07 与运行层）。
- **回路不同**：前瞻记忆（PM）与回溯记忆（RM）是可分离能力——"记得住"不等于"该做时会想起来"。

### 2.3 状态词从哪来

waiting／blocked／paused／abandoned／resolved 不是学术定名，而是工程实践收敛出的状态族（GTD 的 Waiting-For/Someday；Paperclip 的 blocked/in_review/done/cancelled；OpenClaw 的 pending/sent/dismissed/snoozed/expired；PM-Bench 的 active/completed/canceled + due）。理论侧提供判定依据：事件触发 vs 时间触发（event/time-based PM）、自发提取 vs 费力监控（multiprocess framework）、未完任务的记忆优先/侵入性（Zeigarnik）。

## 3. 候选总表

| 候选 | 类型 | 解决什么 | 核心机制 | 实现 | 证据 | 年份 | 适配 |
|---|---|---|---|---|---|---|---|
| PM-Bench | 基准+schema | 前瞻记忆的可判定 schema、due 判定、更新/取消/跨天/隐藏通道 | task=label+trigger+action；due set；11 通道 delta/snapshot/process；cancel/reschedule/override | github.com/genglinliu/PMBench | **E3** | 2026 | 强：状态+证据门槛母版 |
| PIS（Prospective Intention Store） | 机制/论文 | 把推迟承诺建成"可修订类型记录"，生命周期在代码、语义接地给模型 | 类型 I=(φ,α,σ)；Form→Revise→Filter→Decide；channel judge；due set | 代码未放出 | E2 | 2026 | 强：机制最贴 |
| Proactive Memory Agent | 代码 | 长程任务的"选择性干预"：何时把状态注入回执行 | status/knowledge/procedural 三库；Phase2 默认 no_intervention | github.com/yifannnwu/proactive-memory-agent | **E3** | 2026 | 强：记忆/唤醒分界 |
| OpenClaw inferred commitments | 代码（PR） | 从对话推断"跟进候选"并按时投递 | kind∈{event_check_in,deadline_check,care_check_in,open_loop}；status∈{pending,sent,dismissed,snoozed,expired}；dueWindow；dedupe | openclaw/openclaw PR #74189 | E2 | 2026 | 强：落地形态+失败模式 |
| Paperclip 执行语义 | 工程文档 | "blocked 必须有可路由等待路径"，stalled 与 blocked 区分 | blockedByIssueIds／待批准／{owner,action}；external wait 须持久化 | paperclipai/paperclip | E2 | 2026 | 强：等待/受阻表达 |
| IDSS | 机制/论文 | 显式"情境状态"：意图状态×变量×约束 | intent status∈{active,pending,completed,blocked}；变量 askable/retrievable/derivable + known；约束 satisfied/violated | 无代码 | E2 | 2026 | 中强：状态+缺槽 |
| 社会承诺/Commitment Store | 理论 | 承诺生命周期的标准算子 | create/discharge/cancel/release/delegate/assign；practical vs dialectical | Hamblin1970/Singh1999/DIAGAL | E2 | 1970–2006 | 中强：义务与结算 |
| GTD 开环管理 | 实践框架 | 开环的澄清与状态分层 | 澄清分派 trash/reference/someday-maybe/next-action/waiting-for；每周回顾 | 无单一代码 | E1/E2 | 2001 | 中：状态词汇来源 |

（TriggerBench、SSTA-32、MCB、Zeigarnik 作为判据与风险来源见 §2/§4/§9，不单列。）

## 4. 重点候选深查

### 4.1 PM-Bench（E3，schema 与 due 判定母版）
- **机制**：把前瞻记忆建成"每个时间步决定哪些推迟任务现在到期"的动态决策；核心设计目标是把**记住什么**与**何时行动**分开。
- **结构**（commit e1093c4）：task=`{id,label,type(event|time),cue_id,regular,encoding,action_text}`（time 另有 target_time/window_before/window_after；event 另有 cue_channel）。运行时状态：`completed/completed_at/cue_seen/active/canceled/canceled_by_dependency/updated/result`；step 带 `groundtruth.status∈{due,none}`。题目含 7 天 83 任务、11 个隐藏通道（clock=snapshot；email/calendar…=delta；shipment/laundry=snapshot(process)）。
- **更新/演化**：update∈{cancel,reschedule,override}；cancel 级联取消依赖项；due 判定分事件（cue 在 narrative 或 queried channel）与时间（step.time==target_time，迟到窗 60 分钟）。
- **来源/自动化**：证据即 scenario JSON + 每步动作日志；评分 set-F1（命中/迟到/漏/误报）。
- **成熟度/许可/依赖**：Python≥3.10，依赖仅 openai>=1.0.0；**未见 LICENSE 文件（许可未核实）**。
- **吻合**：①给出"未完成"的可判定 schema（label+触发条件+动作）；②把"外部等待"建成需**主动查询**的隐藏通道（proactive monitoring）；③区分 active/completed/canceled 与 due，回应"未完 vs 现在该做"。
- **不吻合**：面向模拟周，非真实长期记忆；不给"兴趣 vs 承诺"的语义分离；不给来源回原话的 provenance 链。**未核实**：许可；未运行。
- **证据**：arXiv:2607.12385；repo 见快照。

### 4.2 PIS：前瞻意图店（E2，机制最贴）
- **机制**：推迟承诺=可修订类型记录 `I=(φ,α,σ)`（语义锚/条件/状态），生命周期与"看板"逻辑放**代码**，模型只做**受限语义接地**；流程 Form→Revise→Filter→Decide（可选 channel judge 先查询隐藏通道）。论文明确："PM 是 schema 约束的状态跟踪，而非开放式推理"。
- **结构/更新**：`satisfaction sat(φ,V_t)` 判是否满足；每步产 due set；授权（κ）在 Decide 内提交修订；错误可按算子边界归因。
- **来源**：论文称"接收后放出方法与代码"→**代码未核实**，仅有论文正文（E2）。
- **吻合**：直答"状态机放代码、语义判给模型"的分工，正是"不擅自规划"与"可判定"的折中；channel judge 对应外部等待。
- **不吻合/未核实**：无开源实现；只在 PM-Bench 上评。**证据**：arXiv:2609.01272。

### 4.3 Proactive Memory Agent（E3，记忆/唤醒分界）
- **机制**：独立"记忆 agent"与执行 agent 并行，读近期轨迹、维护结构化记忆库，并**决定是否把记忆注入**下一轮——记忆=选择性干预策略，而非被动检索。
- **结构**（commit 89e5c0d）：`UniversalMemory` 三层——`status`（当前进度工作记忆，常驻）、`knowledge`（[ENV]/[PATH]/[FACT]）、`procedural`（[BUG]/[PERF]）；`MemoryEntry{id,content,created_at,access_count,last_accessed}`。Phase1 用工具 save/delete/update；Phase2 输出 `<context_for_action>` 或 `<no_intervention/>`。
- **更新/自动化**：触发 interval（默认每步）+ 首步；**是否发言由记忆 agent 自己判**。Phase2 指令："默认 `<no_intervention/>`，仅在确有缺口时介入；以观察而非命令表述；不注入轨迹里已可见的信息；帮它记得，不要试图控制它。"
- **成熟度/许可/依赖**：Apache-2.0；Python 3.12+；litellm；vendored Harbor（Terminal-Bench 运行需 Enroot/Docker）。
- **吻合**：①把"什么只应成为记忆 vs 什么可成为唤醒/注入"做成**默认沉默**的显式判定；②"观测/提示而非命令"正对"不擅自规划"；③status 常驻 + knowledge/procedural 分层。
- **不吻合**：面向编码任务（子目标=修 bug/补要求），非长期生活关切；无情绪、无 provenance 回原话。**未核实**：未运行；RL 干预策略可否离线复用。
- **证据**：arXiv:2607.08716；repo 见快照。

### 4.4 OpenClaw 推断承诺（E2，落地形态与失败模式）
- **机制**：每轮（非 heartbeat）后台把对话喂给隐藏 LLM 抽取器（禁工具、批处理），产出**推断的跟进承诺**，按 agent/channel 作用域存平文件；到期项经 heartbeat 注入，可 list/dismiss。
- **结构**：kind∈{event_check_in,deadline_check,care_check_in,**open_loop**}；sensitivity∈{routine,personal,care}；source∈{inferred_user_context,agent_promise}；status∈{pending,sent,dismissed,snoozed,expired}；字段含 reason/suggestedText/dedupeKey/confidence/dueWindow{earliest,latest,tz}/…
- **判定门槛（关键）**：显式请求（"remind me…"）**属 cron，必须跳过**；话题已解决→跳过；earliest 必须为未来；confidence 低于阈值（care 类更高）→丢弃；**"Prefer no candidate over weak candidates"**。
- **成熟度/许可**：官方 PR diff 中的源码；**合并状态未核实**——`src/commitments/extraction.ts` 在 main 下抓取 404，故只按"官方 PR diff 中的源码"引用（E2），**不当作已合并能力**。
- **吻合**：①"未完成判定"的语言模式+时窗+去重全套工程化；②把"推断候选"与"显式安排"分流，正对"兴趣≠计划"；③状态族完整（含 snoozed/expired）。
- **失败模式（已报告，极重要）**：用户/助手原话被注入**带工具**的 heartbeat prompt → prompt injection 执行路径；commitment 投递强制 `heartbeat.target="last"` 绕过 `target="none"` → personal/care 检查可能**泄漏到错误收件人**。
- **证据**：openclaw/openclaw PR #74189（见快照）。

### 4.5 等待/受阻的表达：社会承诺算子 + Paperclip "blocked 契约"（E2）
- **社会承诺理论**：承诺是对某人的**指向性义务**（debtor→creditor），公众可见、需**社会确立**才成立；标准算子 create/discharge(履行)/cancel/release/delegate/assign（Hamblin 1970 起，Singh 1999；DIAGAL 给了请求/提议/取消/释放/更新/结算的对话博弈）。分 **practical**（要做某事）vs **dialectical**（对命题的立场）。
- **Paperclip `blocked` 契约（工程侧最严）**：进入 `blocked` **必须**命名"可路由等待路径"之一——一等的 blocker 依赖／待回应的交互或批准／结构化 `{owner, action}` 解阻描述符；**纯散文式 blocked 不被接受**（会路由给 nobody），只能标 `needs_attention`。区分 **blocked（有等待路径）** 与 **stalled（无活路径）**；"故意的等待≠丢失的运行"；外部等待只有在控制面**持久化**（带 nextCheckAt/超时策略）才算活路径。
- **附证**：GTD 用 `Waiting-For`（谁+委派日+跟催日）与 `Someday/Maybe`（兴趣池）分离；oh-my-pi issue #3581 主张给 TODO 加 `blocked`，理由是"完成了所有可达工作、在等人"的 agent 无诚实状态，且 blocked 应**排除在 stop-time 未完成提醒之外**（=不进唤醒）。
- **吻合**：给出"等待/受阻"如何**不靠自己臆测下一步**——只要求"命名等待对象与解阻动作"，把"接下来做什么"留给 owner。**未核实**：均为工程/理论择取，无一是为本项目定制。

## 5. 对故渊的可用性拆分表

| 类别 | 内容 |
|---|---|
| **可直接复用代码** | 目前**无直接可用**：PM-Bench 是基准（其 schema/判定器可**照抄设计**，不宜直接当库）；proactive-memory-agent 是编码任务库（机制可搬、领域需改）；OpenClaw 抽取器未证实已合并。 |
| **可借鉴机制** | ①PM-Bench 的 task schema + due 判定 + 隐藏通道（delta/snapshot/process）；②PIS 的"生命周期在代码、语义接地给模型"+channel judge；③proactive-memory-agent 的**默认沉默**选择性注入；④OpenClaw 的"推断候选 vs 显式安排分流"+dueWindow+dedupe+confidence 门槛+"prefer none"；⑤Paperclip 的"blocked 须有可路由等待路径"+blocked/stalled 区分；⑥社会承诺的生命周期算子。 |
| **需要我们补** | ①来源回原话的 provenance（各候选都弱）；②"共同约定 vs 单方许诺 vs 兴趣 vs 真实任务"的语义分离与升级路径；③把 open-loop 与 event line、情绪、真实任务引用起来的**跨对象契约**；④唤醒候选的**调度协议**（candidate→trigger→wake）与配额/静默时间；⑤被撤回/放弃时的可逆与"不回写过去"；⑥记忆/唤醒双层账（记下≠发言）。 |
| **不建议采用** | ①照搬 OpenClaw 式"每轮都推断承诺并自动投递"（默认开会诱发射出与泄漏，应改为**候选+阈值+人审开关**）；②无 dueWindow/无等待对象就标 blocked；③把"连续未提及"当作"已解决"（各基准都禁止从沉默推断完成）。 |
| **未核实** | PIS 代码；OpenClaw 合并状态与文件存在性；PM-Bench 许可；IDSS/SSTA/MCB 是否有开源实现；各候选在真实长期记忆+中文语料下的表现。 |

## 6. 跨模块接口候选

- **输入**：来自 01 的 `evidence`/`fact`；来自 02 的 `event`/`line_id`；来自 06 的 `person`；消息面原话（判据的唯一一级来源）。
- **产生的稳定对象**：`open_loop{ id, kind∈{shared_commitment,pledge,intention,task}, label, trigger{type:event|time, cue_id/channel, time/window}, dueWindow{earliest,latest,tz}, status, blocker{owner,action}|null, source_evidence[], confidence, dedupeKey }`（字段为机制综合，非定稿）。
- **更新对象**：`status`（迁移须带证据与时间戳）；`blocker`；`dueWindow`（reschedule）。
- **对外事件/信号**：`loop_opened / loop_updated / loop_blocked / loop_unblocked / loop_resolved / loop_canceled / loop_expired`。
- **必须保留的来源/依据**：每条 loop 指回 source_evidence；状态变更须给触发证据；被取代的旧状态留链不删。
- **只能软影响、不能硬决定**：情绪/关切度只作排序与阈值软信号；模型对"是否未完成/是否该发言"的判定只作**候选**，不得直接落成执行计划或对外发送。

## 7. 风险、失败模式与开放问题

- **过度承诺（overcommitment）**：SSTA-32 显示默认执行对非完整任务的**过度承诺率 41.7%**；给出**类型化本体**（ANSWER/CLARIFY/REQUEST_SUPPORT/ABSTAIN）后类型化延迟准确率升到 91.7%。→ 必须显式给"未完/受阻/需澄清/可做"的类型决策路径，否则模型会擅自开做。
- **唤醒侧假阳性**：PM-Bench 显示"多召回"常靠**刷动作**换命中（30 分钟自动 heartbeat：489 次误报、set-F1 反降）；TriggerBench 的 negative-clean 用例证明"总是提醒"会失败。→ 唤醒须**默认沉默** + 上限 + 去重 + 冷却。
- **从沉默推断完成**：把"OK""thanks"当已解决是危险信号；须要求**显式证据**（各状态机的一致铁律）。
- **等待无路径**：把"我在等 X"写成散文而无可路由对象 → 该项永远无人负责（Paperclip 的教训）。→ blocked 必须带 {owner,action} 或监控唤醒。
- **注入/泄漏**：OpenClaw 两处真实事故——带工具上下文的 prompt injection、绕过静音配置的私密检查外发。→ 唤醒候选的**可见性/收件人**必须与内容敏感度绑定。
- **state drift / 悬空**：更新（reschedule/override）后旧 cue 仍可能被触发（PM-Bench 用"旧 cue 不可再评分"规避）；须以"当前有效的 cue"为准。
- **反馈循环**：salience/情绪软信号可能自放大"越关心越常想起"；需衰减与上限（与 05 协同）。
- **开放问题**：①"兴趣"保留在记忆→升级为候选→升级为承诺，三条线分别由谁触发（人/模型/规则）？②snoozed/expired 默认时长与重开条件？③跨会话 dedupe 的稳定键怎么定？④被主体"放下"的项如何留史又不再唤醒？

## 8. 本专题结论

**A 建议进入组合设计（机制）**
- **A1 类型化 open-loop 对象 + due 判定**（PM-Bench schema）：task 由 `label+trigger+action` 组成，`due` 是运行时判定而非存储状态；事件/时间/跨天/隐藏通道分类。
- **A2 生命周期与状态机放代码、语义判定给模型**（PIS + PM-Bench + Paperclip 综合）：状态迁移由确定性规则执行，模型只产"候选/接地"。
- **A3 记忆与唤醒分离 + 默认沉默**（proactive-memory-agent + TriggerBench）：所有变更入记忆；唤醒候选需满足未来时点/条件+可达触发+未取消/完成；注入默认 `no_intervention`，心态是"帮它记得，不是控制它"。
- **A4 "blocked 须有可路由等待路径"契约**（Paperclip）：把"在等/受阻"表达成 `{owner, action}` 或持久化监控，而不是散文；区分 blocked 与 stalled。
- **A5 推断候选 ≠ 显式安排；宁可不出**（OpenClaw）：显式提醒归调度层；推断只产候选，带 dueWindow+confidence+dedupeKey，低置信丢弃。
- **A6 不能从沉默推断完成**（各状态机一致铁律）：resolved/abandoned 需显式证据。

**B 值得借鉴但不直接采用**
- 社会承诺生命周期算子（create/discharge/cancel/release/delegate/assign）+ practical/dialectical 区分——定义"共同约定"的结算语义，但须与单方意图分开。
- GTD 的澄清分派（someday-maybe / next-action / waiting-for）——"兴趣↔计划"分层的产品语言参考。
- IDSS 的变量缺槽（askable/retrievable/derivable + known）——表达"受阻=缺某个槽"。

**C 保留候选需验证**
- PIS（代码未放出，机制最贴，发布后单列评估）。
- PM-Bench 作为**自建评测**蓝本（能否用于中文真实长期记忆）。

**D 当前不建议**
- 照搬"每轮自动推断承诺并自动外发"（OpenClaw 原形）；从沉默推断完成；无等待对象标 blocked；把情绪 salience 直接当唤醒触发器。

## 9. 给最终汇总窗口的输入（≤10）

1. **主结论**：open-loop 应以**类型化对象**落地——`kind∈{共同约定/单方许诺/兴趣/任务}`、`trigger{event|time}`、`dueWindow`、`status`、`blocker{owner,action}`、`source_evidence[]`。
2. **状态空间**：把"未完成"拆成 3 维——**生命周期**（open/completed/canceled/expired）× **可执行性**（actionable/blocked/snoozed/unsupported）× **等待对象**（self/other/external-channel）；`resolved/abandoned` 需显式证据，**不得从沉默推断**。
3. **证据门槛**：判"未完成"需 显式未来意图语句 + 触发条件或时窗 + 来源原话；"兴趣"缺一环即降为记忆态，不升级为承诺/任务（OpenClaw 的"prefer none"）。
4. **记忆 vs 唤醒**：所有变更入记忆；唤醒候选=未来时点/条件成立+可达触发源+未取消/完成；注入默认**沉默**（proactive-memory-agent），唤醒层只做"提醒/候选"，不代做计划。
5. **外部等待是一等公民**：隐藏状态通道需**主动查询**（PM-Bench 的 delta/snapshot/process）；等待须**持久化**（nextCheckAt/超时）才算活路径（Paperclip）。
6. **跨专题连接点**：与 **01** 共用 `commitment`/`open-loop state`，本专题只加 `kind` 子类；与 **02** 交换 event/line id 作进展依据；与 **04/05** 约定"关切度只作软信号"；与 **06** 共享"承诺常涉及对方 person"；与 **07** 约定"记下≠发言"的双层账。
7. **最重要冲突**：**"自动推断承诺" vs "不擅自规划"**——建议以"候选 + 阈值 + 默认沉默 + 人审开关"折中，而非全自动。
8. **需共同决策**：唤醒候选是否允许**隐式兴趣**驱动（用户已把此项列为待确认）；兴趣→承诺→任务的**升级权威**归谁。
9. **最强失败模式**（须写入总架构护栏）：overcommitment（默认 41.7%）、刷动作式假阳性、带工具上下文的注入、敏感检查外发泄漏、blocked 无路径悬空。
10. **跨专题线索**：TriggerBench(2606.23459)/SSTA-32(2604.16752)/MCB(2608.19564) 主要是**评测与判据**，建议归入 07 的评测设计；PIS(2609.01272) 若放出代码，需与 07 一起评。

## 10. 证据索引

> 访问日期均为 2026-10-07。默认最高 E3；代码候选给 commit；未能固定者标"浮动 main/未核实"。本轮未安装/运行任何候选，未发模型请求。

| 来源 | 版本/commit | 证据 |
|---|---|---|
| PM-Bench 论文 | arXiv:2607.12385 | E2 |
| PM-Bench repo | github.com/genglinliu/PMBench @ e1093c470c8981daf522d4ef047a7c3a71e077d7（2026-07-13） | E3 |
| PIS 论文 | arXiv:2609.01272（2026-09-01） | E2（代码未放出） |
| TriggerBench | arXiv:2606.23459；repo 仅 README（commit c426163…） | E1 |
| Proactive Memory Agent | arXiv:2607.08716；repo github.com/yifannnwu/proactive-memory-agent @ 89e5c0d6aadfe531a1aee42fd290d48be89973dd（Apache-2.0） | E3 |
| OpenClaw 推断承诺 | openclaw/openclaw PR #74189 diff；HEAD 观测 3fc3205…（main 路径 404，合并未核实） | E2 |
| Paperclip 执行语义 | github.com/paperclipai/paperclip doc/execution-semantics.md | E2 |
| oh-my-pi blocked 状态提案 | github.com/can1357/oh-my-pi issue #3581 | E1/E2 |
| IDSS | arXiv:2608.15755 | E2 |
| SSTA-32 | arXiv:2604.16752 | E2 |
| MCB | arXiv:2608.19564 | E2 |
| 前瞻记忆理论 | Einstein & McDaniel 1990, JEP:LMC 16(4):717-726；multiprocess 框架 | E2 |
| 社会承诺 | Hamblin 1970（经二手正文引用）；Singh 1999, AI&Law 7:97-113；DIAGAL | E2 |
| Zeigarnik | 1927；工作恢复 meta 分析（Anxiety Stress & Coping 39(4)） | E2 |
| GTD | David Allen《Getting Things Done》(2001)；gtd-structure.md | E1/E2 |

快照：`docs/requirements/memory-affect-01/_snapshots/03-open-loops/`（pm_bench_schema.md、openclaw_commitments.md、candidates_evidence.md）。
