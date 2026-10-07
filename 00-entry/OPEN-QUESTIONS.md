# OPEN QUESTIONS

> 跨 requirements 专题的未决问题入口。  
> 这里不是候选方案堆放区，只登记真正会影响后续架构的开放问题。

建议条目：

```text
## Q-xxx · <topic>

Status: OPEN | BLOCKED | RESOLVED
Question:
Why it matters:
Dependencies:
Candidate answers:
Current evidence:
Resolved by:
```

专题内部的小问题可留在专题 README / reference map；跨模块或长期未决的问题再提升到这里。

---

## memory-affect · 当前跨模块入口（2026-10-07）

本轮只提升会影响长期认知运行的跨模块问题；第二阶段原 OPEN / NEW ID 保留，具体参数仍在阶段文件 §6。下列条目不是实现卡。

### Q-MA-01 · Self Ownership 与 V/A Projection 分工

Status: RESOLVED（原则；不是实现完成）  
Question: 原 OPEN-4：谁决定/签发主体认领？原 OPEN-6：谁定义/修改投影？  
Current evidence: 用户本轮明确重申；接手时第二阶段 §10 已有原 OPEN 与补充。  
Resolved by: [ADR-001](./decisions/ADR-001-self-ownership-and-affect-projection-2026-10-07.md)：Subject-owned decision + Host-issued authority receipt；Versioned Base Projection + slow-changing Subject Calibration。  
Remaining: 具体域组装/参数/失效/共同确认等不随本项解决，见下列条目。

### Q-MA-02 · Authority、capability 与 receipt 的运行边界

Status: OPEN  
Question: 横向职责如何最小组装；live capability 到期/撤销/重放、跨进程重验、并发目标版本与幂等怎样连接持久 receipt？  
Why it matters: 历史签发不等于当前授权；模型/缓存不能恢复 live grant，未知执行结果不能凭重试补成功。  
Dependencies: Q-MA-01、运行复杂度选择、执行隔离边界。  
Candidate answers: 复用 Host 检查/签发路径或按需抽取模块；尚未选择独立服务。  
Current evidence: [第二阶段 §10.3/10.6 NEW-5](./memory-affect-02-第二阶段-内部状态表示与运行契约裁决-2026-10-07.md)的文档复核；capability 与审计 receipt 分开，具体机制未定。

### Q-MA-03 · Shared Grounding 与 Commitment

Status: OPEN  
Question: 人+AI 的共同理解最小确认条件是什么；AI 怎样承担 commitment，解除/转移/完成由谁凭什么验真？  
Why it matters: 单方 Self Ownership 不构成他人事实/共同事实；承担自己部分不等于可单方解除对方权利。  
Dependencies: Q-MA-01/02、合法生命周期与实际执行结果。  
Current evidence: 第二阶段 NEW-2/3、00b §5.2/7.3；内容决定者、确认方与 Host 签发责任已有边界，具体成立/迁移条件未裁。

### Q-MA-04 · Task / Trigger / Wake / Permission 消费边界

Status: OPEN  
Question: 谁在候选被消费后请求当前权限，拒绝时怎样回退；Task/Trigger 的登记/取消/恢复、授权撤销与外部结果未知如何协调；隐式兴趣能否进入候选？  
Why it matters: 消费不授予权限、唤醒不等于执行；长期任务和代做归属不能被对话摘要替代。  
Dependencies: Q-MA-02/03、运行宿主与调度/执行边界；故渊不必承担 wake loop 的原则已 ACCEPTED，具体框架/配套设施归属仍 OPEN（见 ADR-002）。  
Current evidence: 第二阶段 NEW-4、00c §6/7.2/11；最小职责组织与三档仅候选。

### Q-MA-05 · Projection / Calibration 与表达

Status: OPEN  
Question: Calibration 的长期证据门槛、历史分类/修订怎样定；D 的增量价值怎样观测，discrete/interlocks/文本锚如何处置，Baseline Chord 是否需要？  
Why it matters: 版本化坐标变化不能伪造感受史；V/A Calibration 不自动成为 Need 校准。  
Dependencies: Q-MA-01/02、Affect 维度语义与可观测场景。  
Current evidence: 第二阶段 OPEN-1/2/3/8、NEW-1/6；前 T10 倾向保持 OPEN，前 N14 扩域降为候选。历史 Annotation 保留当时版本的原则已确认。

### Q-MA-06 · 生命周期、派生结算与消费参数

Status: OPEN  
Question: 满足/放弃/过期怎样编码；各处理链时序预算、revision/版本/freshness、防抖、Echo/Event/Mood 及不应期参数怎样标定；Echo 是否需要独立持久历史？  
Why it matters: 已有语义不能靠旧默认值或档位补齐；纯读时重算与 anchor/权威历史需保持真源边界。  
Dependencies: Q-MA-02/04/05、复杂度选择、后续授权隔离验证。  
Current evidence: 第二阶段 OPEN-5/7/9 与 §6 待标清单；未有本轮运行验收或参数定值。

### Q-CTX-01 · Context Projection / Context Epoch 运行边界

Status: OPEN  
Question: Active Continuity 到模型可见上下文之间是否正式引入 Context Projection / Context Epoch；stable prefix、semantic projection、ephemeral tail、compaction/hydration、consumer cursor 与 provider cache 怎样分工？  
Why it matters: 永久左删式滚动窗口会把“近期状态”“prompt 可见投影”“缓存复用”“工具大结果寿命”混在一起；需要同时保证连续性、正确性、可恢复与 token/latency/cost 可控。  
Dependencies: framework-07 组合与配置可修改性、最终 Host/Harness 选择、Retrieval/Injection contract、Active Continuity、provider-specific cache 机制。  
Candidate answers: 有界 Context Epoch；Active Continuity → Context Projection → Context Epoch；State Revision / Projection Revision / Epoch 分离；同 epoch 优先 append，高水位后 compaction/hydration。  
Current evidence: [讨论记录](./故渊上下文管理与Context-Epoch-讨论记录-2026-10-08.md)；00c §3.3 已标 CANDIDATE。  
Remaining: high/low watermark、raw tail、semantic threshold/hysteresis、bridge summary、主对话/本人任务/子代理是否独立 consumer、tool result 生命周期、cache-aware serializer、Pi/OpenAI/Codex OAuth 缓存与计量行为、cache telemetry 是否值得纳入观测。  
Resolved by: 未裁。

### Q-TOOL-01 · Tool / Skill / MCP 的动态暴露与 Tool Slim

Status: OPEN  
Question: 随 MCP/Skill 增长，如何把 Registered / Discoverable / Loaded / Granted / Invoked / Receipt 分开；主窗口怎样只看到少量 affordance，Tool Slim 怎样同时覆盖调用前 Capability Projection 与调用后 Observation Projection？  
Why it matters: 永久暴露全部工具会增加上下文、选择困难与权限面；一律委派又会把论坛/游戏/购物等主体日常体验变成代理摘要。  
Dependencies: Host/Harness 组合、Q-MA-02 authority/capability、Q-CTX-01 context projection、Skill/MCP 接入方式。  
Candidate answers: capability category → affordance → specific capability → concrete tool；Skill index 常驻、正文按需加载；体验型可主窗口直用，长程/上下文污染型优先委派；raw receipt 与 prompt-visible observation 分开。  
Current evidence: [Tool/Skill/MCP/Capability 讨论记录](./Tool-Skill-MCP-Capability与主动性循环-讨论记录-2026-10-08.md)；00c §9.2 已标 CANDIDATE。  
Remaining: interaction_mode、bundle 协议、动态 tool registration、Tool Slim extractor、主窗口暴露上限、tool schema 与 cache 协调、摄像头/购物/消息等生活 MCP 的具体权限。  
Resolved by: 未裁。

### Q-INIT-01 · Opportunity / Desire / Wake / Initiative Loop

Status: OPEN  
Question: 如何让低成本后台 watcher / worker 持续发现外部机会，同时保留“想不想做”由主体 Motivation + Deliberation 决定；没有外部输入时，内部 Drive/Concern/Interest 如何产生行动候选而不自动越级？  
Why it matters: 只有工具没有主动性时模型容易长期被动；但 Opportunity 直接升级为 Task/Action 又会把主体变成自动脚本。  
Dependencies: Motivation、Concern、Wake、Task/Trigger、Q-TOOL-01、后台调度/监听承载。  
Candidate answers: Opportunity Candidate → Drive/Concern/Interest/Affect → Desire Candidate → Attention/Wake → Deliberation → Intention → Capability Projection；scheduled/conditional watcher 只产结构化机会候选，不替主体声明欲望或执行。  
Current evidence: [Tool/Skill/MCP/Capability 讨论记录](./Tool-Skill-MCP-Capability与主动性循环-讨论记录-2026-10-08.md)；00c §11.1 已标 CANDIDATE。  
Remaining: periodic exploration 是否需要、频率/预算、去重/freshness/relevance、何时推主窗口、内部动机候选生成、工具落灰的软探索机制。  
Resolved by: 未裁。

### Q-MA-07 · Light / Balanced / Full 选择与预算

Status: OPEN  
Question: 选择或组合哪些运行安排；怎样定义整理时效、模型预算、执行并发与资源限制？  
Why it matters: 三档必须共享本体/authority，不能用档位取消契约；不存在已实测容量/费用支持默认选 Full。  
Dependencies: Q-MA-02 至 Q-MA-06；未选的新后端主运行/框架及额外接线成本必须纳入比较，Serein 分工另待明确。  
Candidate answers: [Light / Balanced / Full](./memory-affect-01/00d-Light-Balanced-Full运行复杂度方案-2026-10-07.md)；Balanced 是本窗口比较建议，非用户决定。  
Current evidence: 三方案为文件化设计对照，未实现/运行；[checkpoint](../handoffs/memory-affect-长期认知连续性-Checkpoint-2026-10-07.md)保存阶段与停止点。

2026-10-07 先澄清三档流程为纸面场景，随后用户明确故渊可不负责 wake loop：框架可作为主宿主，旧模拟定时唤醒不继承，见 [ADR-002](./decisions/ADR-002-host-and-wake-ownership-2026-10-07.md)。此原则 ACCEPTED，具体组合仍 OPEN。已有 framework-04/05 选型/离线证据，应针对新需求续接，核对 Core/Harness/部署层/持久引擎分别覆盖什么、剩余成本多少；不从零广搜、不强制先选三档，未授权安装或实现。

研究/窄实验已交付，见 [framework-06 任务卡](../../guyuan/docs/tasks/framework-06-host-and-scheduling-review.md)与[主窗口复核（2026-10-08）](../reports/framework-06/主窗口复核-2026-10-08.md)。并发不必然多进程，调度/隔离可用配套设施；组合仍未选，原Linux恢复组500ms；新增P1扩展主动shutdown与P2恢复后立即EOF在Pi1.0.4下已核账，旧完整C6/Windows修复的限定保留，下一入口建议先讨论组合，Q-MA-07 仍 OPEN；本记录不自动开启实施。2026-10-08用户要求下一步任务卡并增加设定／配置可修改性比较，已编制[framework-07](../../guyuan/docs/tasks/framework-07-composition-and-configurability-review.md)，[讨论记录](./运行设定与配置可修改性-讨论记录-2026-10-08.md)保存配置源／生效范围／在途任务与结构修改成本等OPEN。研究已交付且主窗口局部复核完成，见[复核记录](../reports/framework-07/主窗口复核-2026-10-08.md)。A2与A1均须按domain落实签发与执行检查，A1另核跨栈传播；不预定调度自建、配置双真源或旧任务冻结。Q-MA、Q-CTX-01与具体组合／配置策略未裁，不授权实验或实现。
