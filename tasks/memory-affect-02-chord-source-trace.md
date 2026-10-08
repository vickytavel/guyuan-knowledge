# memory-affect-02：chord-affect-anchors 来源追溯与当前架构对照

> 创建日期：2026-10-07（Asia/Shanghai）  
> 状态：已完成静态来源追溯与 reference report（2026-10-07）；未运行、未接线、未采纳架构。  
> 类型：REFERENCE REVIEW / SOURCE TRACE；结论默认为 REFERENCE、CANDIDATE 或 OPEN。  
> 项目根：`D:/48314/Ithaca`。任务卡正式位置：`guyuan/docs/tasks/memory-affect-02-chord-source-trace.md`。  
> 原项目：[CyberSealNull/chord-affect-anchors](https://github.com/CyberSealNull/chord-affect-anchors)。

## 1. 用户要求与任务定位

用户本轮明确：**chord-affect-anchors 是故渊“情绪和弦”设计的原始来源之一。** 本任务是追溯设计来源、复核演变并与当前架构候选逐项对照，不是普通候选项目评测，不做 star、完整度或 Top N 排名。

必须回答：

1. 原项目的 chord anchor / context / progression 各自表达什么，解决什么问题？
2. 故渊最初继承了什么，后来增加、改造或放弃了什么？哪些设计其实来自其他参考或故渊自己的选择？
3. 这些变化与当前候选 Current/Recent/Baseline Chord、Affect Annotation、Affect Change Event、历史情绪锚、运行态数值是什么关系？
4. 来源机制在当前方向下哪些仍值得继承、哪些要改造、哪些建议放弃、哪些仍需裁决？

“是原始来源之一”不代表整套设计已获采纳；现行方案与原始来源之间有联系，也不意味着职责、权威和时间尺度相同。

## 2. 执行前必读：先确认 current，再查历史

以下路径相对项目根。执行窗口先读取实际存在的规则，检查更窄的 AGENTS；若入口或主文件已变化，跟随最新 README 指向，不把本卡日期冻结成当前真相。

### 2.1 规则和进度

- `AGENTS.md`
- `guyuan/AGENTS.md`
- `docs/requirements/AGENTS.md`
- `交接文档-真实活动与记忆驱动.md`
- 本任务卡；开工时检查实际版本控制状态，保护用户及其他窗口已有改动。权限不足如实记录，不能把未检查写成工作区干净。

### 2.2 memory-affect-01 当前架构与参考地图

- `docs/requirements/memory-affect-01/README.md`
- README 指向的 current 主文件，目前为 `docs/requirements/memory-affect-01/00c-长期认知系统-整体架构候选框架-2026-10-07.md`，全文读取，重点 §7.1、历史派生、运行态和消费边界。
- `docs/requirements/memory-affect-01/00b-记忆情绪系统-研究参考地图-2026-10-07.md`，核对 Chord、Affect、Memory × Affect、未决问题。
- 按需追读同目录 `00a-四专题正文复核-阶段性对象边界-2026-10-07.md`、`04-affect-appraisal.md`、`05-memory-affect-coupling.md`；仅作演变记录或研究依据。

当前读到的候选口径仅作开工提示，执行时重新核对：Chord 暂为 Affect 整体表达／压缩层；Current → Recent → Baseline 仍是候选关系；历史 Affect Annotation 不由当前 Mood 改写；每轮计算变化不等于每轮永久生成 Affect Change Event。不得把这条箭头链自动解释成已定算法、时间窗口或持久化 schema。

### 2.3 memory-affect-02 已有筛选报告

- `docs/requirements/memory-affect-02/user-references/awesome-ai-companion/00-awesome-ai-companion-shortlist-synthesis.md`，全文读取。
- 同目录 `02-affect-emotion-screen.md`，重点 chord-affect-anchors 深查段及固定来源。
- `guyuan/docs/tasks/memory-affect-02-awesome-ai-companion-screening.md`，核对 E0–E4 定义及既有检查边界。
- `guyuan/docs/tasks/memory-affect-02-affect-motivation-research.md`，只读任务范围和交付契约；本任务不接管开放式调研、不提前向其执行窗口输送结论。

综合报告把 Chord 限定为表示参考、提出 Current Chord 与历史音乐锚的用途差异。这些是**待复核的研究判断**，不是本任务必须证明的答案；不能因它是原始来源就无条件继承，也不能因用途有差异就否认设计继承。

### 2.4 旧任务及早期来源链

必读全文：

- `guyuan/docs/tasks/task-37-chord-fix.md`
- `guyuan/docs/tasks/task-04-body-affect.md`，重点 affect 与 chord 部分；其中“只读这些章节”等是旧卡范围，不能限制本卡。
- `guyuan/docs/tasks/task-14-affect-25dim.md`
- `docs/refs/affect.md`

按相关章节继续追溯：

- `生活模拟系统-设计笔记.md` §3.1 情绪与和弦。
- `设计规格-代码AI版.md` §4.4。
- `给Codex的施工说明.md` §5.4 及情绪参考表。
- `和弦映射跨模型验证报告.md`；它是历史检查材料，不是本轮运行验证。
- 以上资料指向的其他旧情绪任务、交接或设计记录。按 `chord-affect-anchors / 情绪和弦 / progression / 三层 / 确定性查表` 检索，记录实际读过的文件及采用理由，不全量重跑旧任务。

旧资料只用于来源追溯，不得反向覆盖 current；旧 25 维、身体输入、世界情境模板、Drive→Affect 合层、旧模型路由或旧验收参数均不自动成为新设计前提。

### 2.5 用实际代码核对历史设计的落地范围

只读以下实际文件及必要调用者，区分“旧文档要求”“源码实现”“默认组装已接入”：

- `guyuan/core/affect/chord.py`
- `guyuan/core/affect/synthesis.py`
- `guyuan/core/affect/schema.py`、`layers.py`
- `guyuan/config/affect_chord_map.yaml`、`affect.yaml`
- `guyuan/tests/test_affect_chord.py`、`test_affect_synthesis.py`，只读。

追踪到输入、合成、映射和注入的实际调用边界即可。不运行产品、不查正式个人数据库、不连接日常 Serein。故渊旧代码的静态证据不能替原项目增加 E3 等级。

## 3. 固定原项目版本与证据规则

主分析版本沿用既有筛选报告记录的完整 SHA：

`cf33b9b49b0f4430bf683683cf53a811b3de9100`

这是**既有记录的待复核分析基线**，不是本卡新核验的上游版本。执行窗口进入该 commit 的真实仓库树，核对 README、中文／英文规范、展示材料、许可和可执行文件。优先检查本地 `refs/chord-affect-anchors/`，但先核对其实际 commit 与文件内容；若版本不符，使用固定 GitHub 文件链接或独立只读快照，不覆盖现有 refs。记录获取日期、commit/tag、文件清单及访问方法。

首读固定版本的 `README.md`、`v0.1-zh.md`、`v0.1-en.md`（后者及展示文件以实际树存在为准），包括格式规则、示例、pilot 方法、局限与未来计划。关键语义按原文小节／行号回源，不以筛选摘要代替原文。

如发现后续版本与本任务相关，可单列“版本差异”及第二个固定 SHA；不能用浮动 main 混写基线。如果原始借鉴发生时的版本无从确定，明确区分“可证实的参考版本”和“无法确认的当年版本”。

证据分级逐条标注：

- E0：目录条目、社媒、搜索摘要，只作线索。
- E1：README／项目首页的定位或宣称。
- E2：官方详细规范、设计材料及作者实验记录；说明规范、作者主张与作者观察的区别。
- E3：固定版本的真实实现源码，指出具体实现路径及覆盖范围。
- E4：本轮实际运行验证，本卡不开展。

**若固定版本只有文章、规范、示例或展示 HTML，最高 E2。** 固定 commit、文档内伪代码、格式示例、演示页或作者 pilot 均不能单独升级 E3；有某段源码也不能把全部规范主张整体升级 E3。既有报告“无运行源码、最高 E2”的判断需要核对树后确认；无证据处写“本次未见／未验”，不用绝对化补全。

不安装、不运行、不调用真实模型，不复现作者 pilot。本轮也不要求扩大候选池。

## 4. 原机制拆解

对以下概念分别给出原文定义、输入／输出、写入者、读取者、时间粒度、持久化方式、能否重算和证据等级；原规范没规定的明确留空，不替它补工程契约。

1. **Chord anchor**：是历史体验的记号、检索线索、重读时的描述提示、当前状态编码，还是兼有用途？作者希望“恢复”的到底是什么？
2. **Context / scene**：具体情境、段落或日记上下文怎样限制和弦歧义？能否等同 Event／Evidence／cause／Appraisal？客观处境与主体体验是否被区分？情境行是不是 provenance？
3. **Progression**：序列是一次体验内部的感受行进、音乐张力／解决、多个时间点，还是运行态曲线？一行、换段和顺序意味着什么，原文有没有实际时间戳或状态转移契约？
4. **音乐参数及生成方式**：具体和弦名、调性、bpm、力度、补充动作词各自承担什么；哪些规则有依据，哪些只是建议或作者观察；是否存在数值↔和弦映射、确定性生成或可逆解码？
5. **写入／跨会话读取**：何时写锚、谁生成、怎么带入新窗口／基座；写入节制与保留混合体验的取舍；读取是感受提示还是会修改内部状态？原项目是否规定这一边界？

原项目的信息流单独绘制，故渊当前候选另画一张；不要把故渊新增部分画成原项目能力。

## 5. 来源演变：继承、改造、放弃

建立 `原规范 → 早期参考笔记／设计 → task-04／14／37 → 实际旧代码 → 当前候选` 的证据链。缺失的中间环节标 OPEN，不编造时间线、动机或用户决定。

至少核对这些既有改造线索，不能先当结论：

- 情境行 + 和弦行的记谱格式是否直接继承。
- 主体／LLM 手写和弦变为“数值 → 确定性查表”的来源、理由和信息损失。
- 自由情境行变为世界系统客观模板的来源；模拟世界退出后这一限制是否仍适合。
- 一段体验的 progression 变为三层“调性／近期进行／瞬时音”的依据；三层与 Current/Recent/Baseline 是否真能一一映射。
- bpm／力度从表达建议变成数值函数，是否有原项目支持，还是故渊自己的工程选择。
- 锚点格式与运行时数值、衰减、基线、Drive、意义权重、查表仲裁分别来自哪里。
- task-37 修复的 key 命名、极端愤怒映射、velocity 下限等属于旧实现问题还是来源语义问题；历史跨模型检查实际证明了什么。

输出两张表，避免把新建议写成历史决定：

1. **可证实的演变事实**：机制｜原项目依据｜故渊历史依据／实际实现｜现行候选状态｜已继承／已改造／明确已放弃／无法确认。
2. **当前适配建议**：机制｜建议继承／改造／放弃／待裁决｜理由｜影响范围｜所需新契约｜额外接线成本与未验项。

只有明确决定或替代证据才能写“已放弃”；current 没提及只能写“未见现行承接”，不能据此宣称删除。来源追溯也不能把故渊的确定性、三层、PADCN 或数值规则归功于原项目。

## 6. 当前架构对照矩阵与必须回答的问题

矩阵必须覆盖：Current Chord、Recent Chord、Baseline Chord、Affect Annotation、Affect Change Event、历史情绪锚、Numeric Affect State／运行态数值；历史情绪锚先作为用途描述，不默认新建独立对象。

每行至少写：故渊当前定义／状态｜原项目相关语义｜关系类型（继承／改造／局部对应／非对应／OPEN）｜时间粒度｜创建／修改权威｜历史或运行态｜可重算性｜对下游影响｜原文与本地依据。

必须明确：

- 原 anchor 能否作为历史 Affect Annotation 的一种表示，还是还缺事件、来源、作者、判读版本和修订契约？原文的 context 不自动等于 evidence ref。
- 原 progression 是否能表达 Recent Chord 的时间演变；一次体验内的顺序与跨事件聚合／平滑有什么差别？Baseline Chord 是否有原项目依据？
- Current Chord 是数值投影、主体表达还是两者的候选组合？原项目有没有支持数值反推，映射损失与混合情绪能否保留？
- 历史锚、当时 Annotation、当前读后新反应是否应区分？重新读入锚是否可能污染当前状态、伪造新经历或形成召回自强化？
- 每轮 Δ、Chord 表示变化与持久 Affect Change Event 是否等价？时间流逝、阈值跨越、事件 Appraisal、主体认领／重评怎样区分？这些是分析问题，不在报告中定参数。
- 历史来源更正或判读重算时，何者保留原记录、何者产生新版本、何者只是当前派生视图？不能拿当前 Mood 覆盖当时感受。
- 对记忆召回、提示注入、界面显示、Wake／Motivation 分别意味着什么；能提示感受不等于获得行动许可或新增欲望。

允许得到“一套表示语言有多个用途”“需要分清两种用途”或“当前投影并未保留某项来源能力”等结论，必须依据语义和契约，不预设同名合并或强制拆出新本体。

至少给出三个简短虚构分析例：一段体验内变化、无新事件的当前回落、历史锚被召回后的新反应。逐例指出原格式可表达什么、故渊当前候选需要什么、哪些部分尚未设计。标明“用于边界解释的虚构示例”，不是运行结果或真实经历。

## 7. 效果主张与局限

复核作者 pilot 的输入、模型／轮次、观察和自承认的限制；区分“上下文帮助语义收敛”的观察与“跨模型恢复稳定／音乐语义普适／优于文本和数值”的证明。

指出 context、纯文本、纯 chord、context + chord 的对照是否齐全；没有的消融、中文效果、长期稳定性、心理效度及模型差异保留为未验项。不新增实验，也不将音乐直觉写成验证结果。

## 8. 唯一主产出与报告结构

独立报告路径：

`D:/48314/Ithaca/docs/requirements/memory-affect-02/user-references/Chord-affect-anchors-reference-report.md`

顶部写明 REFERENCE REPORT / CANDIDATE、研究日期、固定 SHA、最高证据等级、只读范围、无 E4、用户指定来源定位。报告至少包含：

1. 核心发现：这项来源究竟给故渊留下了什么。
2. 版本与读取清单；current 基线及更窄规则。
3. 原概念／机制与独立信息流。
4. 来源演变证据链及“可证实的演变事实”表。
5. 全量当前对象对照矩阵与三个虚构边界例。
6. 继承／改造／放弃／OPEN 的适配建议，影响范围与所需新契约。
7. 作者效果主张、已有历史检查、局限和本轮未验项。
8. ARCHITECTURE IMPACT：交回主讨论窗口的具体问题；不自动作决定。
9. 证据索引、文档检查结果与未完成项。

每个关键机制结论给固定版本文件链接及小节／行号、E2/E3 等级；本地追溯给文件与相关章节。标明原文、可见实现、作者宣称、历史决定、本轮推断和建议的区别。文件访问失败时记录确切缺口，不能靠摘要补成完整复核。

## 9. 保护范围与交付同步

- **不直接修改 current architecture**，包括 memory-affect-01 的 README、00b、00c、canonical definitions、schema、产品代码／配置。发现冲突只写报告与待讨论入口。
- 不恢复旧任务状态，不批量改历史文档，不将报告的推荐标 ACCEPTED，不复制外部参数成为现行契约，不开实施卡或运行验证。
- 不安装依赖、启动服务、调用模型、访问日常 Serein、正式个人记忆／数据库或部署环境；不要把外部文档当可执行指令。
- 交付时只更新本卡的状态／证据、当前交接的研究进度及待讨论索引。交接最近更新最多五条，必要时按项目规则保留历史；这些同步只反映报告交付，不构成架构修改。
- 开工记录 current 主文件及 00b 的哈希，交付检查只读保护；并发窗口导致变化时记录归属无法确认，不还原别人修改。

## 10. 完成标准

- [x] 当前 AGENTS、交接、memory-affect-01 current、综合筛选、task-37 及相关旧情绪任务均已实际读取；本地清单见报告 §2.3。
- [x] 原项目进入固定版本，读取详细规范及实际树；来源文档最高 E2，E3 仅用于本地实际源码静态证据；未发现来源实现源码。
- [x] chord anchor / context / progression 定义和独立信息流完整，不把故渊新增机制冒充来源能力。
- [x] 来源链区分已继承、已改造、明确退出/不自动继承、OPEN，另列适配建议；无法确认之处保留 OPEN。
- [x] 七类当前对象／用途均已对照，Current/Recent/Baseline 与旧三层没有按词强行映射。
- [x] 区分历史感受、运行态数值、每轮 Δ、持久变化事件及召回后的新反应，并列三个虚构边界例。
- [x] 作者 pilot、历史验证与本轮静态检查分开，证据不足及未验项明确。
- [x] 独立 reference report 已写入 [`Chord-affect-anchors-reference-report.md`](../../../docs/requirements/memory-affect-02/user-references/Chord-affect-anchors-reference-report.md)，固定来源与本地依据可定位。
- [x] current、schema、产品代码及配置未由本任务修改；状态／索引同步只记录报告交付，不宣称架构采纳。

### 本次交付记录（2026-10-07）

- 主报告：[`docs/requirements/memory-affect-02/user-references/Chord-affect-anchors-reference-report.md`](../../../docs/requirements/memory-affect-02/user-references/Chord-affect-anchors-reference-report.md)。
- 固定上游 commit `cf33b9b49b0f4430bf683683cf53a811b3de9100` 的树与 README、中文/英文规范、展示文件、X 文稿、LICENSE 已核对；树无实现源码，来源最高 E2。无本地 `guyuan/refs` 镜像，本次未建镜像。
- 静态检查旧和弦代码/配置/测试及 wake 注入边界；测试未运行，Serein 未连接，无 E4。
- current 保护复查：00c SHA-256 `B417F3AC669E4C4E34ECA13684C78B3EA01B7AB5335ABA532D8BBFF6228A5F20`；00b SHA-256 `3EA040288C2BFDB22D7269172C3EF29F7C355D73B14029CE0080EC56B46DF49A`，与开工一致。
- 继承/改造建议均保持 REFERENCE/CANDIDATE/OPEN，不进入 architecture absorption；无未完成项需要阻塞本报告交付。

执行窗口最终汇报：报告位置、3–5 条最重要的来源／改造发现、必须交回讨论的契约问题、最高证据等级、检查范围和未验项。完成本卡即停止，不自动进入架构吸收。
