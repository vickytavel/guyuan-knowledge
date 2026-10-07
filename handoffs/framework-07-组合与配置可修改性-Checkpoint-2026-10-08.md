# framework-07 · 运行组合与配置可修改性 · 阶段 Checkpoint

> 日期：2026-10-08（Asia/Shanghai）。
> 任务卡：[framework-07：运行组合裁决准备与设定／配置可修改性](../../guyuan/docs/tasks/framework-07-composition-and-configurability-review.md)（卡末 §8 为执行记录）。
> 执行者：Hermes 主窗口（开工、冲突复核与综合建议）＋两名子代理（各写一份依据报告）。
> 本文件按卡 §6 分段：**开工记录**（目标／已复用证据／未查项）／**Checkpoint 1**（组合与修改路径整理完成后）／**Checkpoint 2**（综合建议形成后）。
> 当前交接仍是项目进度入口；本文件不是 current 架构，不含新裁决。

---

## 开工记录 · 领卡、范围与复用证据（2026-10-08）

- **我在哪一步**：framework-07 任务卡由主窗口于 2026-10-08 编制、用户交给 Hermes 执行。本卡是**研究／架构讨论准备**，不是实施卡，也不是退出补测卡；继承 framework-04/05/06 的证据链，不重跑任何实验。
- **本次目标**：沿已有证据形成可供用户裁决的**最小运行组合**（A2＝PydanticAI Core＋按需 Harness 为比较基线，A1＝PydanticAI＋Pi 执行器须证明**具体增量收益**），并把日后修改设定／运行参数／规则的实际成本纳入同一比较；回答「日常调整能否经界面完成、哪些需重新加载／新 run／重启、哪些仍需代码或迁移」。LangGraph 只在图流程／恢复或部署能力会改变结论时回查。
- **本次范围与禁令（卡 §1）**：只查官方资料、源码与既有证据，做纸面比较。⛔ 不新增原型或依赖、不改运行配置／schema／API／产品代码、不运行框架或产品测试、不补跑 Windows 或 Pi 0.85.1、不安装／SSH／启动容器或服务、不接 Serein／真实模型／正式数据／凭据。framework-06 容器实验授权**不转移**到本卡。
- **交付物**（卡 §7）：综合报告 `docs/reports/framework-07/运行组合与配置可修改性-裁决准备-2026-10-08.md`；两份依据 `docs/reports/framework-07/组合职责与增量收益-2026-10-08.md`、`docs/reports/framework-07/设定与配置可修改性-2026-10-08.md`；本 checkpoint；任务卡 §8 执行记录；交接一条「研究已交付、待复核」事实摘要（最近更新保持五条并归档旧条）。
- **已复用证据（只读，不重跑）**：
  - framework-06 [主窗口复核](../reports/framework-06/主窗口复核-2026-10-08.md)（尤其 §3、§4、§6 与 P1/P2 追加核账）、[综合报告](../reports/framework-06/宿主与调度复核-2026-10-07.md)、[补充分工比较](../reports/framework-06/补充-宿主与执行器分工比较-2026-10-07.md)、三份候选依据 `candidates/{pydantic-ai,langgraph,pi}.md`；核账 JSON 与 Linux 证据目录。
  - [ADR-002](../requirements/decisions/ADR-002-host-and-wake-ownership-2026-10-07.md)（宿主与 wake 归属）、[00c](../requirements/memory-affect-01/00c-长期认知系统-整体架构候选框架-2026-10-07.md) §6/§7/§9.1/§11/§14、[00d](../requirements/memory-affect-01/00d-Light-Balanced-Full运行复杂度方案-2026-10-07.md)、[讨论记录](../requirements/运行设定与配置可修改性-讨论记录-2026-10-08.md)、[OPEN-QUESTIONS](../requirements/OPEN-QUESTIONS.md)（Q-MA-02 至 07）、[VPS 容量报告](../requirements/VPS容量与框架资源预算-2026-10-07.md)（用户报告，非实测）。
  - 既有版本基线（原型锁文件）：PydanticAI Core 2.51.0＋Harness 0.36.0；LangGraph 1.2.13＋checkpoint-sqlite 3.1.1；Pi `@earendil-works/pi-coding-agent` 1.0.4（tag `v1.0.4`＝commit `7c10bd43`）。
- **本轮明确未查／留给子代理核对的项**：① 现行官方文档中 A2 可选配套（Harness 子代理／StepPersistence／deferred 工具／workspace＋sandbox／durable engine）与 Pi v1.0.4 执行面（extensions／MCP／codemode／容器化／settingsManager／resourceLoader）的**版本对应与所在层**；② 三候选的**配置来源、优先级、加载边界与热更新**官方依据（答不了即标未知）；③ 组合七格中「仍缺的接线」与 A1 的**净增量收益**逐格证据；④ 六类改动在 A2/A1 下的真实生效点与在途任务影响；⑤ framework-04/05 主窗口复核的按需回查（仅在与本轮结论冲突时）。
- **不做**：不选框架／三档／durable engine，不裁 Q-MA OPEN，不改 current 本体或 authority，不把推荐写成 ACCEPTED，不自动进入 schema/API/implementation。
- **下一步**：派两名子代理各写一份依据报告（互不改共享正文）→ 收齐后写 Checkpoint 1 → 主窗口综合报告与 Checkpoint 2 → 任务卡 §8 与交接事实摘要 → 停止待复核。

---

## Checkpoint 1 · 两份依据报告收齐、组合与修改路径整理完成后（2026-10-08）

- **已完成**：两名子代理并行交付，各只写自己的文件：
  - [组合职责与增量收益-2026-10-08.md](../reports/framework-07/组合职责与增量收益-2026-10-08.md)（卡 §3：六职责同表＋A1 净增量收益＋三场景差异＋对七组与三档的影响；约 30.7KB、13 表）。
  - [设定与配置可修改性-2026-10-08.md](../reports/framework-07/设定与配置可修改性-2026-10-08.md)（卡 §4/§5：六类改动主表＋逐类影响面＋保存值≠生效值＋配置优先级/加载/热更新官方依据＋两场景；约 35.0KB、5 表）。
  两份均标 RESEARCH/CANDIDATE，含未验清单与「只建议不执行」的窄验证建议。
- **越界检查**：共享正文（AGENTS／交接／ADR／current／README／任务卡／讨论记录）在子代理运行期间无本轮写入；核对方式是文件 mtime ＋ 内容比对。⚠️ 同期**另有窗口**新增 Context Projection／Context Epoch 候选（`docs/requirements/故渊上下文管理与Context-Epoch-讨论记录-2026-10-08.md`、00c §3.3、OPEN-QUESTIONS 的 Q-CTX-01、交接 §6.4）。本卡**不改**其文件，只在综合报告 §1.4 记录衔接点（投影/epoch/cache 参数属 §4 ⑤ 认知参数类）。
- **新证据（本轮，官方文档 2026-10-08 现场核对，非转述）**：Pi `settings.md`「`/reload` enables tools newly added to `defaultTools`. It does not disable tools removed from it…」；Pi `configuration.md` project 覆盖 user（同名不合并，trust 后加载）；Pi `sdk.md` `session.systemPrompt` 只读且含未发出的改动、工具变更在下一次请求前声明；Pi `containerization.md` 四种隔离；PydanticAI `message-history`「If `message_history` is set and not empty, a new system prompt is not generated」＋ `ReinjectSystemPrompt`；PydanticAI `harness` 官方可选 Shell／Modal Sandbox／Code Mode／MCP／Browser Use／Playwright；`durable_execution` 现行文档列**七个** engine＋builder（framework-06 的「DBOS 是候选之一」据此更新）。
- **收紧项**：`model_settings` 正文列 **model→agent→run 三层**（run 最高）；capability 携带 model settings 但精确合并顺序未在该页确认 → 标未验（依据报告 B 原写「四层」，已在综合报告 §1.3 收紧）。
- **未裁决**：框架组合、三档、durable engine、配置生效策略、Q-MA-02 至 07、Q-CTX-01 全部保留。本轮**没有**用户新决定。
- **已更新文件（本段）**：本 checkpoint；两份依据报告（子代理新建）、综合报告（主窗口新建）。
- **下一步**：主窗口写综合报告与 Checkpoint 2 → 任务卡 §8 → 交接事实摘要 → 停止。

---

## Checkpoint 2 · 综合建议形成后（本轮停止，2026-10-08）

- **综合报告**：[运行组合与配置可修改性-裁决准备-2026-10-08.md](../reports/framework-07/运行组合与配置可修改性-裁决准备-2026-10-08.md)（CANDIDATE）完成，含：首页推荐／成立条件 C1–C6／相对收益与代价、职责同表（六职责×A2/A1×五层）、A1 净增量收益与代价、六类改动条件化结论与「保存值≠生效值」、两个变更场景、对七组与三档的影响、仍未验与**一项**窄验证建议（配置生效边界）、回写清单、停止点。
- **候选结论（CANDIDATE，未采纳）**：① 主运行骨架仍建议 **A2＝PydanticAI Core＋按需 Harness**；② 调度/外部变化检测**恒在框架外**；③ **A1 保留为条件性同列分支**（仅当隔离执行面是第一等需求且目标版本收益大于 A2 官方可选项）；④ LangGraph 仅按需回查。**这不是已选 A2，也不是已否 A1。**
- **可修改性结论（条件化）**：日常内容/参数与结构/流程维护分开；不承诺「连上界面即可改一切」；不以「文件多/扩展多」作为维护优势；A1 每类通常多一处配置源且两侧优先级无官方规定；撤销权限不得因配置回退隐含恢复。
- **替代项**：LangGraph 部署层（要现成 cron/队列/多 worker）、LangGraph OSS（要图/状态机流程）、Pi 作主宿主（原生无 durable/调度/子代理）；A2＋项目侧轻量隔离执行器（不引 Pi 全运行时）。
- **影响范围**：不改 current 本体／authority／ADR；七组裁决不被推翻（**仅当选 A1** 需补一条「authority 唯一归口」约束）；三档不因本轮改变，Balanced 仍是比较建议。**回写清单**（OPEN-QUESTIONS／00c §14／README／待定清单／ADR）列在综合报告 §9，**先交主窗口复核**，本轮不自行改。
- **已更新文件（本轮合计）**：本 checkpoint；任务卡 §8；交接 §9（一条事实摘要，仍五条，旧五条归档 `docs/handoffs/archive/2026-10/交接最近更新-2026-10-08-framework-07交付前.md`）与 §6.3；`docs/reports/framework-07/` 三份报告；辅助检查脚本 `check_framework07.py`（暂存，非项目资产）。
- **未解决**：综合报告 §7.1 未验项全部；Q-MA-02 至 07；Q-CTX-01；配置生效策略与「当前有效配置」视图形态；Serein 分工、OS/容器隔离、资源预算；Pi Windows 退出根因。
- **停止点**：交付后停止，等待主窗口复核与用户组合决定；不自动进入 schema/API/implementation、补验、部署或下一卡；framework-06 容器实验授权不转移到本卡。


## 主窗口局部复核 checkpoint · 2026-10-08

- **原OPEN／新证据**：组合／配置策略未定；主窗口核官方Agent页、history页、Pi固定settings／containerization与DBOS调度文档。原Hermes判断和入口按原字节保存至framework-07复核前归档。
- **纠正与理由**：调度可由官方配套／engine／成熟设施承担，不恒在框架外；同栈不自动authority归口；现行模型设置四层明确，综合报告漏读后段；当前instructions不受“非空history不新建system_prompt”同一规则约束，Reinject主要补缺失；动态参数可重求值，不预定冻结；配置消费者不等于真源；Pi四种是外部部署指导，不是内建四沙箱；现行durable页面八种solution不反证DBOS候选。
- **候选／替代项／影响**：建议A2作为主运行候选，A1按模型／工具／资源接入与维护的具体净收益保留分支，不仅隔离需求。两组合均按domain落实authority，A1增加跨栈范围；多位点检查可共用决定。current本体／ADR／Context Projection候选不变，Q-MA/Q-CTX仍OPEN。
- **下一入口／停止点**：用户组合选择后明确该路径的配置采用与加载策略；不把Pi重载实验作为必须前置。窄验证建议已分清instructions/system_prompt、活数据/版本快照与SDK/RPC接口；没有执行授权，本窗口不补跑、不进schema/API/implementation。
- **持久化**：[主窗口复核](../reports/framework-07/主窗口复核-2026-10-08.md)、三份报告当前关键条款、任务与交接、README/OPEN/待定/配置讨论入口同步；未改其他窗口Context Projection正文。


局部文档检查（2026-10-08）：check_framework07_review.py检查6份报告／任务／checkpoint、98个本地链接与10份原文归档SHA-256；check_documents.py检查18份current／入口／任务／需求文档、294个本地链接、5个标题锚点，交接最近更新五条。两项退出码0、errors=[]；前一结果保存为docs/reports/framework-07/主窗口文档检查-2026-10-08.json。仅文档及原文校验，没有框架／产品测试；Git原有改动保留。
