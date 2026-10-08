# framework-06 候选依据 · LangGraph（核心库 / 必要部署层）

Status: RESEARCH/CANDIDATE（framework-06 候选依据）
日期：2026-10-07（Asia/Shanghai） 执行者：Hermes 子代理（LangGraph 候选）
范围：仅回答任务卡 §4 七格 + §5 三场景的逐条考证与结论。未安装依赖、未建 venv、未运行框架/原型/产品测试、未连接模型或服务、未拆分部署。
上下游：[任务卡](../../../../guyuan/docs/tasks/framework-06-host-and-scheduling-review.md)、[ADR-002](../../../requirements/decisions/ADR-002-host-and-wake-ownership-2026-10-07.md)、[framework-04 主窗口复核](../../framework-04/主窗口复核与横向比较-2026-10-07.md)、[framework-04 LangGraph 报告](../../framework-04/langgraph.md)、[framework-05 主窗口复核](../../framework-05/主窗口复核-2026-10-07.md)。

## 层次图例（本报告每格都标注属哪一层）

- **①框架核心原生**：`langgraph` OSS 包自身提供（`StateGraph`/`Pregel`/checkpointer 协议/`interrupt`/`Command`/`Send`/`langgraph-prebuilt` 的 `ToolNode`/`BaseStore`/`durability`/`RetryPolicy`/`TimeoutPolicy`/`error_handler`/`RunControl`/`stream_events`）。
- **②官方可选能力或部署层**：`langgraph-checkpoint-sqlite`/`-postgres` 等 saver、`langgraph.store.*`、`langgraph_sdk`、Agent Server（`langgraph-api`，Elastic-2.0）/ LangSmith Deployment（cron、webhook、任务队列、SSE 端点、托管或自托管）。
- **③已有项目资产**：故渊旧 `runtime/wake_loop.py`、`core/wake/run.py`、`EventStore`、API/SSE 语义；以及 framework-04 原型中自写的 JSON task/effect 账本。
- **④必要新增适配**：为接故渊新方向要新写的东西（Serein chat-model 适配、task/artifact/source manifest 契约、幂等与副作用对账、authority 检查节点与 receipt、外部条件检测、API/SSE 桥）。
- **⑤尚缺或未验**：现行文档没有承诺、或本候选无运行证据的能力（跨版本图/checkpoint 迁移、多进程并发写同一 thread 的协调、任意外部系统 exactly-once、真实模型/网搜/OS 沙箱、自托管许可与成本）。

**证据分列约定**：凡标「旧原型已验」的，均给出 `D:/codex/prototypes/framework-04/langgraph/` 下的具体文件与字段；凡标「现行文档（滚动页）」的，只表示官方 2026-10-07 查阅时的描述，**不代表旧原型 1.2.13 已验证**。

---

## 0. 结论摘要

1. **能直接承担（①原生）**：图流程运行（`StateGraph`/`Pregel` invoke/stream）、模型↔工具往返分派（`ToolNode`+`tools_condition`）、按 `thread_id` 的 super-step 级持久化与恢复（checkpointer→`BaseCheckpointSaver`）、HITL 停点与恢复（`interrupt()` / `Command(resume=...)`）、图内并行/映射（`Send` 的 map-reduce）、子图嵌套、跨 thread 的键值长期记忆（`BaseStore`）、每节点的重试/超时/错误处理（`RetryPolicy`/`TimeoutPolicy`/`error_handler`）、协作式优雅停机（`RunControl`/`request_drain`）、流式投影（`stream_events` v1/v2/v3）。
2. **最小组合**：**只装开源库即可**跑「图 + checkpoint + interrupt + store」的在进程内持久恢复，无需部署层。安装集 = `langgraph` + 其 6 个运行时依赖（`langchain-core`、`langgraph-checkpoint`、`langgraph-prebuilt`、`langgraph-sdk`、`pydantic`、`xxhash`）；持久层再加 `langgraph-checkpoint-sqlite`（开发，官方标 partial）或 `langgraph-checkpoint-postgres`（生产）。
3. **调度（cron/webhook/任务队列/多 worker/SSE 端点）不在 OSS 库**，属 **②Agent Server / LangSmith Deployment** 部署层；开源 `langgraph` 本身**没有任何内建 cron/定时器**（本候选最关键的缺口）。
4. **子代理的“运行单元”**：`Send`/子图是**同一进程内**的图调用（同一 super-step、靠 reducer 汇聚），不是独立 OS 进程/worker；要独立后台 worker 需 ②Agent Server 的 run 模型或项目自建进程。旧原型只验了「同进程两个 graph thread 顺序跑」。
5. **恢复的重放语义**：checkpoint 只落在 **super-step 边界**；崩溃/中断恢复时，**被中断的那个节点函数从头重入**，其 `interrupt()` 之前的代码（含外部写）会再执行一遍 → **必须幂等/对账**。旧原型对此有最直接的运行证据。
6. **认知/authority 接入点**：LangGraph 没有专门“authority hook”API，受控扩展点就是**节点函数 + 工具**（可写一个 Host 检查节点、把 receipt 落 `BaseStore`）；模型正文不能自报已授权/已完成，须由节点/Host 工具判定。
7. **旧原型已验（1.2.13 + sqlite saver 3.1.1）**：同 graph/同 thread 跨进程 interrupt→resume（`long-start`/`long-resume`）、节点重入（`story.jsonl` 两条 `review-node-entry`）、崩溃后节点重放并复用已完成的合成 effect 而不重复（`effects.jsonl` 的 `reconciled-existing-completion`）、`ToolNode` 真实分派（`tools.jsonl`）。
8. **旧原型未验**：真实模型 provider/Serein、真实网搜/浏览器、`Send`/子图并行、任意 Agent 回合中途崩溃、跨版本图 schema 迁移、多进程并发写同 thread、cron/调度、Agent Server、OS 沙箱。
9. **仍需项目自建/适配（④）**：task/artifact/source manifest 账本、副作用幂等与对账、Serein 模型适配与窗口语义、外部条件检测、API/SSE 桥、权限隔离。框架减少的是“显式图推进 + 按 thread 保存运行态 + interrupt 等待恢复”这部分通用机制，**不等于替项目承担业务账本与安全边界**。
10. **ADR-002 一致性**：与 ADR-002「cron 属部署层、不能写成只装库即得」一致，无冲突；仅一处细化（见 §6 末）。

---

## 1. 版本与来源表

查阅日期：2026-10-07（Asia/Shanghai）。OSS 文档为滚动页，无固定 tag；此处把「旧原型固定版本」与「现行文档」分列。

### 1.1 旧原型固定版本（来自原型 lock，非记忆）

| 包 / 文件 | 版本 | 来源 / 路径 |
|---|---|---|
| `langgraph` | 1.2.13 | `D:/codex/prototypes/framework-04/langgraph/requirements-lock.txt` L17；[framework-04 报告](../../framework-04/langgraph.md) §组件 |
| `langgraph-checkpoint-sqlite` | 3.1.1 | requirements-lock.txt L19；[PyPI 3.1.1 元数据](https://pypi.org/pypi/langgraph-checkpoint-sqlite/3.1.1/json)（报告引用） |
| `langgraph-checkpoint` | 4.2.0 | requirements-lock.txt L18 |
| `langchain-core` | 1.6.7 | requirements-lock.txt L15 |
| `langgraph-prebuilt` | 1.1.0 | requirements-lock.txt L20（提供 `ToolNode`/`tools_condition`） |
| `langgraph-sdk` | 0.4.6 | requirements-lock.txt L21（原型**未**调用 Agent Server） |
| `pydantic` / Python | 2.13.5 / 3.14.5 | requirements-lock.txt；[framework-04 报告](../../framework-04/langgraph.md) §组件 |
| 原型入口 / 证据 | — | `prototype.py`、`evidence/{story,effects,tools,tasks,model-stub,input-sha256}.json(l)`、`state/{checkpoints.sqlite,tasks.json,effects.json}`、`output/long-task-manifest.json` |

> 版本纪律：现行 PyPI 已到 `langgraph` 1.2.14、1.2.13 本身是最新补丁之一（[releases](https://github.com/langchain-ai/langgraph/releases)、[1.2.13](https://newreleases.io/project/github/langchain-ai/langgraph/release/1.2.13)）；**不得**把下方 1.2 现行文档的新能力（`RunControl`/`TimeoutPolicy`/`stream_events` v3/`DeltaChannel`）倒算为 1.2.13 原型已验证——原型脚本里并未使用这些 API。

### 1.2 现行官方文档（2026-10-07 查阅，滚动页）

| 主题 | 链接 | 属层 |
|---|---|---|
| Checkpointers / super-step / durability modes / 接口 | https://docs.langchain.com/oss/python/langgraph/checkpointers | ①② |
| Persistence / checkpointer vs store | https://docs.langchain.com/oss/python/langgraph/persistence | ①② |
| Durable execution（含 durability modes 引用） | https://docs.langchain.com/oss/python/langgraph/durable-execution | ① |
| Interrupts（`interrupt()`/`Command(resume)`，节点重入规则） | https://docs.langchain.com/oss/python/langgraph/interrupts | ① |
| Graph API（Durability/Idempotency/Determinism） | https://docs.langchain.com/oss/python/langgraph/graph-api | ① |
| Use the graph API（`Send` map-reduce） | https://docs.langchain.com/oss/python/langgraph/use-graph-api | ① |
| Stores（`BaseStore`，跨 thread） | https://docs.langchain.com/oss/python/langgraph/stores | ① |
| Fault tolerance（`RetryPolicy`/`TimeoutPolicy`/`error_handler`） | https://docs.langchain.com/oss/python/langgraph/fault-tolerance | ① |
| Streaming（`stream`/`astream`，stream_mode） | https://docs.langchain.com/oss/python/langgraph/streaming | ① |
| Event streaming（`stream_events(...version="v3")` 投影） | https://docs.langchain.com/oss/python/langgraph/event-streaming | ① |
| Functional API（`@task`/`@entrypoint` determinism/idempotency） | https://docs.langchain.com/oss/python/langgraph/functional-api | ① |
| `interrupt` 参考（`response_schema`、resume 匹配） | https://reference.langchain.com/python/langgraph/types/interrupt | ① |
| `StateGraph.add_node`（`timeout`/`retry_policy`/`error_handler`） | https://reference.langchain.com/python/langgraph/graph/state/StateGraph/add_node | ① |
| 1.2.0a6 release（timeouts/error handler/graceful shutdown/DeltaChannel/v3） | https://github.com/langchain-ai/langgraph/releases/tag/1.2.0a6 | ① |
| Agent Server（线程/runs/cron/任务队列/持久化） | https://docs.langchain.com/langsmith/agent-server | ② |
| LangSmith Deployment（Cloud/BYOC/自托管/Standalone 许可） | https://docs.langchain.com/langsmith/deployment | ② |
| Cron jobs | https://docs.langchain.com/langsmith/cron-jobs | ② |
| Use webhooks（run 完成回调） | https://docs.langchain.com/langsmith/use-webhooks | ② |
| Enqueue concurrent（`multitask_strategy`） | https://docs.langchain.com/langsmith/enqueue-concurrent | ② |
| Deployment components（Agent Server/CLI/SDK/RemoteGraph） | https://docs.langchain.com/langsmith/components | ② |

许可：`langgraph` OSS 为 **MIT**；Agent Server 运行时（`langgraph-api`）为 **Elastic-2.0**，自托管需自带 LangSmith 许可（[deployment 页](https://docs.langchain.com/langsmith/deployment) 明确 “Bring your own PostgreSQL, Redis, and LangSmith license”；自托管 LangSmith Deployment 需 Enterprise 计划）。此许可分层来自官方 deployment 页 + PyPI 元数据；具体商业条款未逐条审计。

---

## 2. §4 七格对照表

> 每格：判定层次（①–⑤）+ 具体包/模块 + 依据。旧原型证据用 `原型:` 前缀，现行文档用 `文档:` 前缀。

### 4.1 主会话与运行（谁承载模型/工具循环、状态注入、工具结果/原文引用、消息排队、取消与结束；旧 WakeLoop/RunSegment 可退出项）

| 要点 | 层次 | 具体包/模块 | 依据 |
|---|---|---|---|
| 图运行 + 模型/工具循环 | ① | `langgraph.graph.StateGraph`/`MessagesState`、`langgraph.prebuilt.ToolNode`+`tools_condition`、`Pregel.invoke/stream` | 文档：graph-api、checkpointers。原型：`prototype.py` `build_agent()` 用 `StateGraph.add_node("model")`+`add_node("tools",ToolNode(tools))`+`add_conditional_edges("model",tools_condition)` |
| 状态注入 / 工具结果 | ① | 节点入参=channel values；工具结果经 `ToolMessage` 回模型节点 | 原型：`model-stub.jsonl`、`tools.jsonl`（`search_sources`/`read_source`/`write_research_report` 真实经 `ToolNode`） |
| 消息排队 | ①无 / ②有 | OSS 无内建队列，靠调用方 `invoke/stream`；`langgraph-api` Agent Server 有 `multitask_strategy` = `enqueue`(默认)/`reject`/`rollback`/`interrupt` | 文档：`MultitaskStrategy`（langgraph_sdk 参考）、enqueue-concurrent |
| 取消 | ①(1.2) / ② | ①`RunControl.request_drain()`（`langgraph.runtime`，协作式，super-step 边界停）+ 节点超时（见 4.7/1.2）；②Agent Server worker “signaled to cancel a run” | 文档：1.2.0a6 release（graceful shutdown）、agent-server。原型：`cancel_case()` 用 `threading.Event` 由本地工具**协作**检查——是**包装层**取消，非框架取消 |
| 结束 | ① | 无 `next` 节点即结束；`StateSnapshot.next==()` | 文档：checkpointers |
| 旧 WakeLoop/RunSegment 可退出 | ③→① | 旧 `runtime/wake_loop.py`/`core/wake/run.py` 的通用模型/工具往返可换为图节点循环 | 文档：overview（LangGraph=orchestration runtime）；但**结束原因/错误语义/回合边界**需映射（④），见 [三候选深查](../../../requirements/Agent三候选深查与融合收益比较-2026-10-06.md) §融合 |
| 真实模型 provider | ④ / ⑤ | OSS 不含模型；需 `langchain-openai` 类适配 + Serein 网关（窗口头/流/工具调用） | 文档：overview（“you don’t need LangChain to use LangGraph”，模型自备）。原型：scripted 本地 node，**非** `BaseChatModel`，未验 provider/Serein |

### 4.2 本人任务与子代理（本人 vs 代做；长工作/等待期间继续主对话；子代理是什么运行单元；谁接产物）

| 要点 | 层次 | 具体包/模块 | 依据 |
|---|---|---|---|
| 子代理=图内并行/映射 | ① | `langgraph.types.Send("node", payload)` 列表，条件边扇出；目标 channel **必须有 reducer** 才能聚合并发写 | 文档：use-graph-api（map-reduce）、graph-api |
| 子代理=子图 | ① | 编译后的子图可直接 `add_node` 作父图节点；子图有**自己的 checkpoint namespace**（`checkpoint_ns` = `node_name:uuid`） | 文档：checkpointers（`checkpoint_ns`）、persistence（父图不一定即时看到子图状态） |
| 运行单元性质 | ① | **同一进程内**、同一/嵌套 `Pregel` 执行；`Send` 分支在同一 super-step 并发、齐了才前进 | 文档：use-graph-api、checkpointers |
| 独立后台 worker | ② / ④ | ②Agent Server 的 run 模型（API server 入队、queue worker 执行）；OSS 无独立 worker，须项目自建进程 | 文档：agent-server。原型：`delegate()` 是**同进程两个 graph thread 顺序**运行 |
| 本人 vs 代做 | ③ / ④ | 框架无 actor 概念；`actor_kind`/`parent_task_id` 是原型自写 JSON | 原型：`tasks.jsonl` L18–22（`actor_kind:"本人隔离执行"` / `"委派执行"`、`parent_task_id:"review-parent-001"`）；`state/tasks.json` |
| 长工作/等待期间继续主对话 | ①(同进程线程) / ② | ①把主 graph 与子任务放不同 thread/进程由调用方并发；②Agent Server 后台 run | 原型：R1 中 worker 在 `threading.Thread`，本机 loopback 主窗口桩同时响应（见 4.6） |
| 谁接产物 | ③ / ④ | 框架不持有 task/artifact；产物与来源接由项目 task/artifact 账本 | 原型：`long-task-manifest.json`（`artifact_path/artifact_sha256/artifact_version/source_refs/next_step`） |
| 缺口 | ⑤ | 可独立关停/重启的 child worker、父等待/child 超时、回执持久重投、子代理权限 scope | [framework-04 报告](../../framework-04/langgraph.md) §未验项；[framework-04 主窗口复核](../../framework-04/主窗口复核与横向比较-2026-10-07.md) §2 D1 行 |

### 4.3 真实定时与条件（一次性时刻/周期/外部变化；注册/取消/去重/重启后到期项；在哪层决定重检/执行/通知）

| 要点 | 层次 | 具体包/模块 | 依据 |
|---|---|---|---|
| 一次性时刻 / 周期 cron | **①无** | 开源 `langgraph` 无任何 cron/scheduler；图是被动调用 | 文档：checkpointers/persistence 全篇无调度；cron 只在 Agent Server |
| cron 承载方 | ② | LangSmith Deployment / Agent Server `crons`（`client.crons.create_for_thread` / stateless）；**UTC 解释**；存 PostgreSQL（core resource data） | 文档：cron-jobs、agent-server |
| 外部变化检测 | ④ | 框架 timer 到点**不知道网站/论坛是否变化**；需数据抓取适配 + 条件判断 | 文档无；ADR-002 §旧机制表明确“调度到点不自动知道网站变化，仍需数据接入/条件判断” |
| 注册/取消/去重/重启到期 | ②（cron 层）/ ⑤ | cron 的增删改查在 Agent Server API；去重、重启补检语义落在部署层，本候选**未验** | 文档：cron-jobs（“sends the same input to the thread every time”）；未验 |
| 重检/执行/通知分层 | ④ | 需拆成：检测→Wake candidate→当前权限检查→执行→通知；框架不替业务合法性 | 文档无；ADR-002、[00c](../../../requirements/memory-affect-01/00c-长期认知系统-整体架构候选框架-2026-10-07.md) §11 |
| 旧原型 | — | 无定时器；只有协作式取消 | 原型：无 schedule 代码，无 cron 证据 |

### 4.4 持久恢复（会话重开 / 同 run 崩溃 / 从作品续做；断点恢复可能重复哪些动作；如何核对真实结果）

| 要点 | 层次 | 具体包/模块 | 依据 |
|---|---|---|---|
| checkpointer | ① | `BaseCheckpointSaver`（put/put_writes/get_tuple/list）；`InMemorySaver`（进程内存）/`SqliteSaver`（开发，官方标 partial）/`PostgresSaver`（生产） | 文档：checkpointers、persistence。原型：`SqliteSaver.from_conn_string(state/checkpoints.sqlite)` |
| 会话重开 | ① | 同 `thread_id` 再次 `invoke` 即续；`graph.get_state/history` 查状态 | 文档：checkpointers |
| 同 run 崩溃恢复 | ① | super-step 边界 checkpoint + `checkpoint_writes`（同 super-step 已成功节点的写不重跑，**待恢复/被中断节点从头重入**） | 文档：checkpointers（pending writes）、interrupts（node restarts from beginning） |
| 从作品续做 | ③/④ | 新 thread + 应用 manifest/artifact；**不搬 checkpoint 格式** | 原型：`portable()` 先断言新 thread 无旧 state，再读 `long-task-manifest.json`+`workspace/story-v1.md` 续写 |
| 断点可能重复的动作 | ①/④ | `interrupt()` 之前、或崩溃节点的**节点内**外部写会重放；须幂等 key / 查后写 / 对账 | 文档：graph-api（“Code and side effects before the pause run again… Idempotency”）、interrupts（“Side effects called before interrupt must be idempotent”） |
| 核对真实结果 | ④ | 框架只重放/重入，**不造假成功**；完成真值来自执行端 receipt/ledger | 原型：`effects.jsonl` L1 `synthetic-effect-completed, ack_saved_to_graph:false` → L2 `reconciled-existing-completion`；节点重入时先查 `effects.json` 再决定 |
| 节点重入证据 | ①已验 | 同 thread 恢复后暂停节点重入 | 原型：`story.jsonl` L1 `review-node-entry, artifact_write:"created-or-updated"`；L2 同节点再入 `"already-identical"` |
| L1 跨进程恢复证据 | ①已验 | `long-start` 新进程 interrupt → `long-resume` 新进程 `Command(resume="approve")` | 原型：`prototype.py` `long_start/long_resume`；`state/tasks.json` `story-001.resumed_in_new_process:true`、`artifact_version:2` |
| durability 模式 | ① | `exit` / `async`(Agent Server 默认) / `sync` | 文档：checkpointers、agent-server（“async (default)”） |
| 缺口 | ⑤ | 跨版本图 schema/checkpoint 迁移、多进程并发写同一 thread 的租约/协调、任意 Agent 回合中途精确续跑 | [framework-04 报告](../../framework-04/langgraph.md) §未验项 |

### 4.5 认知/authority 接入（受控扩展点递入 current、结算、主体认领、receipt；模型正文不能自报已授权/已完成）

| 要点 | 层次 | 具体包/模块 | 依据 |
|---|---|---|---|
| 受控扩展点 | ① | **节点函数**（`add_node`，可插入一个 Host 检查节点）、**工具**（`@tool`/`ToolNode`）、`Runtime` 注入（`runtime.store`/`runtime.context`）、`config`（thread/context） | 文档：graph-api、add-memory（`Runtime` 注入 store） |
| receipt / current / 认领落点 | ①+④ | receipt/候选写 `BaseStore`（namespace 键值）；**语义**（域、资格、失效、重放）须项目实现 | 文档：stores（namespaces、`aput/asearch`）。原型：**无** authority，仅 `actor_kind` 标签 |
| 模型不能自报授权/完成 | ④ | 由节点/Host 工具在受控路径判定并签发，不能信正文 | 文档无（属业务语义）；ADR-002 §术语、[00c](../../../requirements/memory-affect-01/00c-长期认知系统-整体架构候选框架-2026-10-07.md) §9.1 |
| 是否有专门 hook API | ①（无独立 hook） | OSS 核心无 middleware/hook 层；middleware 在 LangChain `create_agent`/Agent Server 层 | 文档：overview（LangGraph 低层 orchestration；LangChain=agent framework） |

### 4.6 对话/前端边界（主动结果与无文本结束如何递回；SSE/断线重连谁承担）

| 要点 | 层次 | 具体包/模块 | 依据 |
|---|---|---|---|
| 流式投影 | ① | `stream_events(..., version="v1"|"v2"|"v3")` 的 `stream.messages/values/subgraphs/interrupts/interrupted/output`；或 `stream(stream_mode=[...])` | 文档：event-streaming、streaming |
| SSE/HTTP 传输 | ② / ④ | OSS 是**进程内迭代器**，SSE 传输由应用/部署层；Agent Server 提供 `/threads/{id}/runs/stream` 等 SSE 端点 | 文档：event-streaming（“To stream against … Agent Server, see LangSmith Streaming API”）、use-webhooks |
| 主动结果 / 无文本结束 | ② / ④ | 图无 `next` 即结束，`stream.output` 取终态；主动推送给前端需应用通知层（或 Agent Server run + webhook） | 文档：event-streaming、use-webhooks |
| 旧原型 | ③ | 本机 loopback `ThreadingHTTPServer` 桩返回 `{"status":"responded"}` | 原型：`prototype.py` `MainHandler`/`r1()`；`state/tasks.json` `main_window_response_ms:147.344` |
| 断线重连 | ② / ④ | ②Agent Server thread/run 可查询+重连 stream；OSS 须应用自管 | 文档：agent-server |

### 4.7 运行组合与成本（仅装核心库是否够；可选层/持久引擎/外部调度何时必要；实际新增维护/部署负担）

| 要点 | 层次 | 具体包/模块 | 依据 |
|---|---|---|---|
| 仅装 OSS 库 | ① 够 | `langgraph` + 6 运行时依赖（`langchain-core`/`langgraph-checkpoint`/`langgraph-prebuilt`/`langgraph-sdk`/`pydantic`/`xxhash`） | requirements-lock.txt；PyPI 元数据（[skillfed](https://skillfed.io/packages/langgraph)，6 runtime deps） |
| 持久引擎何时必要 | ② | 要跨进程恢复就需持久 saver：`langgraph-checkpoint-sqlite`（开发 partial）或 `-postgres`（生产） | 文档：persistence（`SqliteSaver`: local/dev；`PostgresSaver`: 生产） |
| 部署层何时必要 | ② | 需要 cron/webhook/任务队列/多 worker/SSE 端点/托管持久化时才引入 Agent Server（`langgraph-api`） | 文档：agent-server、deployment、cron-jobs |
| 外部调度替代 | ④ | 若不加部署层，可用 OS/systemd 定时器在外部按时刻调用图（须项目自建） | 框架不含；ADR-002 §旧机制表 |
| 许可/部署含义 | ② | OSS=MIT；Agent Server 运行时=Elastic-2.0，自托管需自带 LangSmith 许可；自托管 Deployment 需 Enterprise | 文档：deployment；PyPI 元数据 |
| 常驻进程/外部依赖 | — | OSS：无独立常驻进程（库嵌入你方进程）；启用部署层：API server + queue worker（+ PostgreSQL/Redis）；生产 saver/store：外部 DB | 文档：agent-server（single host / split / distributed；`N_JOBS_PER_WORKER`） |
| 维护边界 | ④ | 图 schema 版本、saver 迁移、checkpoint 保留/裁剪（文档建议 cron 清理）、task/artifact 账本仍在项目侧 | 文档：durable-execution（“Consider adding a cron job to delete checkpoints older than N days”）、[framework-04 报告](../../framework-04/langgraph.md) §相比框架自带能力 |
| 旧原型成本 | ③ | 41 个安装包；SQLite 单文件 `state/checkpoints.sqlite` 172,032 bytes（本地演示，非容量证据） | [framework-04 报告](../../framework-04/langgraph.md) §组件/§运行命令 |

---

## 3. §5 三个纸面场景

> 采用虚构资料，不接个人真实记录；以下只做「接口与责任对应」，非运行验收。层次标注同 §4。

### 场景 1 · 研究期间继续交谈

设定：主体开展长研究（主任务），委派一个**只读**子代理核对资料，等待期间用户发消息。

| 环节 | 层次 | 运行单元 / 去向 | 原生覆盖 | 缺口 | 失败/未知 | 证据 |
|---|---|---|---|---|---|---|
| 主研究 | ① | thread A 上的 graph run（模型↔`ToolNode`循环） | `StateGraph`+`ToolNode` | Serein 模型适配 | 真实模型未验 | 原型 R1：`tools.jsonl` 4 次工具、`research-v1.md` |
| 委派只读子代理 | ① | **同进程**：`Send("child",payload)` 扇出或子图作节点；**不同进程**：需 ②Agent Server run 或项目自建 worker | `Send`/子图(文档)；同进程两 thread(原型) | 独立 child worker/OS 隔离(⑤)、权限 scope(④) | 子图崩溃拖垮同进程；`Send` 未在原型验证 | 原型 `delegate()`：`tasks.jsonl` L18–22 同进程两 thread |
| 等待期用户消息 | ①/② | 打到 thread A 的下一轮 `invoke`；或另开 run | thread 隔离(①)；`enqueue`(②) | 并发排队在 OSS 需自管 | OSS 无队列 | 原型 R1 主窗口桩与 worker 并发 |
| 产物引用 | ③/④ | 引用放 app manifest；图 state 只存 refs | `BaseStore`/state(①) | artifact/来源契约(④) | — | `long-task-manifest.json` |
| 结果回流 | ①/③ | 子图 return 进父 state（靠 reducer）；或独立 run 由 app 收 | reducer 汇聚(①) | 回执持久重投(⑤) | 无 reducer 会丢并发写 | 文档 use-graph-api |
| 最小承载组合 | — | 只读子代理**同进程**时，OSS 库 + 持久 saver 即可；要真正后台并行/隔离才加部署层或自建进程 | — | — | — | — |

### 场景 2 · 真实时刻与回复变化

设定：主体合法登记「明日下午重新查看某虚构论坛主题」；无新回复则沉默；有新回复先重检、再按当前授权行动/通知。**不加深保底心跳**。

| 环节 | 层次 | 承担方 | 原生覆盖 | 缺口 | 失败/未知 | 证据 |
|---|---|---|---|---|---|---|
| 登记「明日某刻」 | ② / ④ | ①OSS **无**；②Agent Server `crons`（UTC）；或外部 systemd/OS timer | cron(②) | OSS 无定时器(⑤) | cron 未验；不加部署层则须自建 | 文档 cron-jobs |
| 数据接入（论坛） | ④ | 项目抓取适配 | 无 | 框架 timer 不知内容变化 | 网络/反爬未验 | ADR-002、无原型证据 |
| 条件判断（有无新回复） | ④ | 节点/工具比对已见回复集（去重） | 节点可载逻辑(①) | 去重状态存哪(④) | 误判/漏判未验 | 无原型证据 |
| 权限边界 | ④ | Host 节点/工具在行动前查 capability | 节点/工具是扩展点(①) | authority 语义(④) | — | ADR-002 §术语 |
| 通知 | ③/④ | app/API/SSE；或 ②run webhook | webhook(②) | 主动推送层(④) | — | 文档 use-webhooks |
| 保底心跳 | — | **不加入**（ADR-002 明确退出新核心默认） | — | — | — | ADR-002 |

### 场景 3 · 停机后继续创作

设定：工具已写入作品 v1，进程停止，但**完成回执未到**；重启后继续任务。

| 环节 | 层次 | 承担方 | 原生覆盖 | 缺口 | 失败/未知 | 证据 |
|---|---|---|---|---|---|---|
| 恢复的是 session/run 还是成果 | ①/③ | ①同 `thread_id` 续图状态；③从 manifest/artifact 开新 thread | checkpointer(①)/manifest(③) | 跨版本迁移(⑤) | — | 原型 L1 `long-resume`（同 thread，新进程）；L2 `portable()`（新 thread，无旧 state） |
| 辨别已做动作 | ②/④ | 崩溃节点会**从头重入**；须查 effect ledger/幂等 key | 节点重入(①) | 幂等/对账(④) | 节点内、super-step 边界内的写会重放 | 原型 `effects.jsonl`：崩溃后重入节点先查 `effects.json` |
| 不靠重放补编成功 | ④ | 框架只重放/重入，真值来自执行端 receipt | 无框架保证 | 外部 receipt(④) | exactly-once 外部(⑤) | 原型 L1 `synthetic-effect-completed, ack_saved_to_graph:false` → L2 `reconciled-existing-completion` |
| 成品续写 | ①/③ | 节点读 v1 写 v2 | 节点内 IO(①) | artifact 原子性(④) | 崩溃在写中间 | 原型 `story.jsonl` 两次 `review-node-entry`（第二次 `already-identical`） |
| 并发恢复 | ⑤ | OSS 无跨进程租约，两进程可同跑同 thread | — | 协调缺失(⑤) | 并发未验 | [framework-04 报告](../../framework-04/langgraph.md) §未验项 |

---

## 4. 最小组合与真实剩余工作

**要装什么（①+②可选）**
- 必装：`langgraph`（含 6 运行时依赖）。
- 要持久化（跨进程恢复）：`langgraph-checkpoint-sqlite`（开发/单机，官方标 partial）或 `langgraph-checkpoint-postgres`（生产）。
- 要跨 thread 长期记忆：`langgraph.store.*`（`InMemoryStore` 开发 / `PostgresStore` 生产）。
- 要 cron/webhook/任务队列/多 worker/SSE 端点：才加 Agent Server（`langgraph-api`，Elastic-2.0，需许可）+ PostgreSQL/Redis。

**要写的适配（④）**（均为项目侧，非框架提供）
1. Serein chat-model 适配：每个普通轮/工具续轮/摘要都经 Serein，窗口头/流/错误/取消/归档保留。
2. task/artifact/source manifest 契约：目标、主体、授权、作品版本、阶段停点、未决副作用（原型 `long-task-manifest.json` 是雏形）。
3. 幂等与副作用对账：effect ledger + 幂等 key + “查后写”，应对节点重入。
4. authority 检查节点/工具 + receipt（落 `BaseStore`）。
5. 外部条件检测器（网站/论坛）；与「重检/执行/通知」分层。
6. API/SSE 桥与前端查看。
7. OS/容器权限隔离（子代理≠沙箱）。

**产物由谁接收**：故渊/项目 **task & artifact 层**（③/④）接收；LangGraph 只持有「按 thread 的图状态」与其 checkpoint，不持有 task/artifact 账本，框架内部 checkpoint **不可**当作通用任务 manifest。

---

## 5. 部署与资源约束（只写组成/常驻进程/外部依赖/配置边界，无数字）

- **组成**：
  - 纯 OSS：库 + 你的运行时进程；持久化 = 一个 saver 后端（SQLite 文件或 Postgres）。
  - 加部署层：Agent Server（API server + queue worker）+ 其数据面（PostgreSQL、Redis 等）+ 控制面（Cloud/BYOC/自托管三选）。
- **常驻进程**：
  - OSS 无独立常驻服务（嵌入式库）；调度需外部进程（cron/systemd）或放弃。
  - Agent Server：API server 与 queue worker 可单机（single host）或拆分（split/distributed）；worker 并发由 `N_JOBS_PER_WORKER` 限。
- **外部依赖**：生产持久化需 DB（Postgres 等）；Agent Server 需 DB+Redis；自托管需自带 LangSmith 许可并（按官方）保留许可校验出网。
- **配置边界**：`thread_id`（持久化主键）、`durability`（exit/async/sync）、`checkpoint_ns`（子图命名空间）、`BaseCheckpointSaver`/`BaseStore` 子类、图 schema 版本、checkpoint 保留/裁剪策略、subgraph `checkpointer` 模式（`False`/`None`/`True`）。
- **维护边界**：图 schema 升级与历史迁移、saver 迁移、checkpoint 增长裁剪、task/artifact 账本与安全隔离仍在项目侧。
- （按任务卡要求：不写内存/工期/节省比例/VPS 并发数字；SQLite 原型文件大小仅作本地演示尺寸，非容量证据。）

---

## 6. 未验项清单 + 一项最小离线验证建议

**未验项（⑤）**
- 真实模型 provider / Serein 窗口、流、工具调用兼容。
- 真实网搜/抓页/浏览器/任意文件命令/Git。
- `Send`/map-reduce 与子图并行、子图 checkpoint 命名空间冲突边界。
- 任意 Agent 回合**中途**崩溃的精确续跑（原型只验了 super-step 边界与节点重入）。
- 跨 LangGraph 版本 / 图 schema 迁移；多进程并发写同 thread 的协调/租约。
- cron/webhook/任务队列/Agent Server/SSE 端点；自托管许可与成本。
- SQLite saver 并行写、长期保留、进程启动恢复队列。
- OS/容器权限隔离、凭据边界。
- 1.2 新 API（`RunControl`/`TimeoutPolicy`/`stream_events` v3/`DeltaChannel`）**未**在 1.2.13 原型验证。

**一项最小离线验证建议（不在本轮执行）**
- **问题**：`Send`/子图作为“子代理”到底是不是**同进程**运行单元，以及并发写 reducer 与子图 `checkpointer` 模式在真实执行下的边界——静态文档只能给语义，旧原型未覆盖。
- **做法**：在既有离线限制内（本地 scripted model 桩、受限工具、无模型/无网络、loopback-only），用 `langgraph` 1.2.13 + `langgraph-checkpoint-sqlite` 3.1.1 建一个父图，用 `add_conditional_edges` 返回多个 `Send("worker", {...})` 扇出到同一节点、目标 channel 带 `operator.add` reducer；再建一个 `checkpointer=True` 子图作父节点，观察 `checkpoint_ns`。
- **通过条件**（全部满足才算通过）：(a) 记录到 `Send` 分支与父图在**同一进程/PID**内、同一 super-step 并发执行；(b) 去掉 reducer 时并发写如实冲突/丢数据，加上 reducer 时全部汇聚；(c) 子图 `checkpointer=True` 在同一节点内被调用多次时按官方“namespace 冲突”如实报错或产生可观察冲突；(d) 全过程不触发任何非 loopback 连接。
- **不做**：不接模型、不装部署层、不改产品代码、不部署。

**与任务卡 / ADR-002 的一致性**
- 与任务卡一致：旧基线 `LangGraph 1.2.13` + `langgraph-checkpoint-sqlite 3.1.1` 已从 `requirements-lock.txt` 核对，非凭记忆；旧证据与现行文档已分列；未把新文档能力倒算为旧原型已验。
- 与 ADR-002 一致：cron/webhook 属部署层（Agent Server / LangSmith Deployment），**不能**写成只装 OSS 库即得的调度；OSS 库确无内建 cron。无冲突。
- 一处**细化（非冲突）**：ADR-002 §术语说 Host 可在“受控工具、hook 或适配路径”实现；LangGraph OSS 核心**没有**独立 middleware/hook 层，对应扩展点是**节点函数 + 工具**（middleware 属 LangChain `create_agent`/Agent Server 层）。这只影响「Host 在 LangGraph 里放哪」的措辞，不改 ADR-002 的职责结论。

---

本报告只写任务卡 §4 七格 + §5 三场景的逐条考证与结论，未修改交接/current/ADR/OPEN/README/任务卡或其它候选文件，未安装/运行/连接任何服务。
