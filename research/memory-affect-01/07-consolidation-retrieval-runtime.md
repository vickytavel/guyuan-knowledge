# 07 · 整理、召回与运行工程

> 专题报告 · memory-affect-01 · 2026-10-07 · 证据上限 E3（只读源码，未安装/未运行/未发模型请求）
> 方法：公开仓库 `git clone --depth 1` 只读源码（固定 commit）+ arXiv 摘要。源码结论给 commit 与路径；未核实处显式标注。

## 1. 故渊要解决的问题

本专题不发明本体（01–06 的事），只回答：**已有的证据、事件线、派生理解、事项状态、情绪状态、世界/人物条目，怎样被增量整理、聚类连线、召回、注入、反馈与后台运行；成本与可维护性怎么控。**

需求映射：写入后要自动整理（派生/聚类/连线/去重/矛盾取代）但原文不被改写；多轮对话要低时延召回并**可解释**；未完成事项/兴趣/情绪是**召回信号**而非本体上限；后台要在 **2–4 核小 VPS（无 GPU）** 上跑，幂等、可恢复、可回滚。

边界：不选 schema、不定模型路由、不盘点 Serein、不安装/运行候选。接口：← 01–06 提供对象与不可变原语；→ 汇总窗口提供**机制清单**与可复用代码坐标。不解决：最终排序权重、席位数量、情绪是否硬过滤（只给分层建议与判据）。

## 2. 关键概念与必要区分

**统一坐标词表（沿用任务卡对象名，本专题口径）：**

| 对象 | 本专题角色 | 流水线位置 |
|---|---|---|
| 证据 evidence | 逐字原文/原话，不可变，唯一事实来源 | 只增不改；一切派生的锚 |
| 事件 event | 一次经历/一段推进的场景单元 | 写入单元；被聚类成线（←02） |
| 派生事实 fact | 从证据抽出的稳定陈述（行级） | 增强段；可重算 |
| 当前理解 interpretation | 系统现时对某对象的理解/反思 | 可被取代、留历史 |
| 共同约定 commitment | 双方约定及其状态机 | 派生行中的台账类 |
| 事项状态 open-loop state | 未完/等待/受阻/已结（←03） | 召回信号 + 状态字段 |
| 情绪状态 affect state | 对事件的意义评价与内部状态（←04） | **只做软信号**，不硬过滤 |
| 人物/世界条目 person-world entry | 事实/偏好/关系/共同语境的稳定视图（←06） | 常驻或按需检索 |

本专题切分与任务卡一致，无需另立一套名字。

**必须区分的四对能力**：① 存储≠整理（能写向量库不等于会去重/取代/连线）；② 召回≠使用（进上下文≠被答到，用"进池/上桌/落座"三态拆开）；③ 模型出候选≠事实生效（派生/摘要默认候选）；④ "evolution/consolidate"是宣传词，源码里可能是完全不同量级（见 §4.4）。

**检索分层共通抽象**（三来源收敛）：候选生成（池，宽）→ 过滤/门（相关性，非排序）→ 排序（相关×时间×重要×冷却）→ 席位（限额：每线一席/新卡窗口/硬上限）→ 判官（灰区最终准入，可空选）。

## 3. 候选总表

| # | 候选 | 类型 | 核心机制 | 实现 | 证据 | 版本/年份 | 适配 |
|---|---|---|---|---|---|---|---|
| 1 | Graphiti (getzep) | 代码库 | 边失效、确定性去重/聚类、LLM 社区摘要、RRF/MMR/交叉编码器重排、Saga 叙事线 | Python/Apache-2.0，Neo4j/FalkorDB/Kuzu | E3 | 0.30.2 @`aa5bb27` 2026-10-06 | 高 |
| 2 | Mem0 (mem0ai) | 代码库 | 8 阶段增量写入、MD5 去重、ADD-only(v3)/ADD-UPDATE-DELETE(v1)、BM25+语义+实体 boost、5 种 reranker | Python/Apache-2.0，Qdrant 默认 | E3 | 2.2.1 @`c93420c` 2026-10-05 | 高 |
| 3 | Microsoft GraphRAG | 代码库 | hierarchical Leiden、社区报告、map-reduce 全局检索、local/drift 检索、`update_*` 增量、缓存包 | Python/MIT | E3 | 3.2.0 @`769542f` 2026-09-23 | 中高 |
| 4 | A-MEM (agiresearch) | 代码库 | Zettelkasten 笔记、LLM 生成链接、`update_neighbor` 改写邻居、周期性 consolidate | Python/MIT，ChromaDB | E3 | @`ceffb86` 2025-12-12 | 中 |
| 5 | HippoRAG 2 (OSU-NLP) | 代码库 | OpenIE 三元组图、个性化 PageRank、DSPy LLM 事实过滤、passage/entity/fact 分离嵌入 | Python/MIT，torch+igraph | E3 | @`d5c8329` 2026-10-06 | 中 |
| 6 | Generative Agents | 论文+代码 | memory stream、递归 reflection、recency×relevance×importance 打分 | Python | E2 | arXiv 2304.03442 | 中 |
| 7 | Sleep-time compute | 论文 | 离线预计算降在线成本 | 论文 | E2 | arXiv 2504.13171 | 中 |
| 8 | LongMemEval/LoCoMo/MemEmo | 基准 | 抽取/多会话推理/时序/更新/拒答；超长对话；情绪记忆 | 数据集 | E2 | 2410.10813 等 | 中 |

（排序/席位佐证：LLM-as-judge 2306.05685、位置偏置 2307.03172，见 §10。）

## 4. 重点候选深查（5 个）

### 4.1 Graphiti —— 最完整的"整理+召回"骨架
- **确定性（无 LLM）**：`dedup_helpers.py` 归一化精确名匹配**总是先跑**，不中才进模糊路；模糊用 **MinHash(32 排列)+LSH(band=4)+Jaccard**，阈值 `_FUZZY_JACCARD_THRESHOLD=0.9`，短/低熵名用信息熵门（`_NAME_ENTROPY_THRESHOLD=1.5`）挡误并；歧义才升级 LLM。社区检测 `community_operations.py::label_propagation` 是**纯确定性图算法**（邻接边计数+平局取大）。→ 去重/聚类确定性优先、模型兜底。
- **模型部分**：社区摘要 `build_community`（成对两两 LLM 归并成树+社区名）；边消解 `edge_operations.py::resolve_extracted_edge`（LLM 判 duplicate/invalidate）；轻量抽时间。
- **状态与更新**：EntityEdge 带 `valid_at/invalid_at/expired_at`；`resolve_edge_contradictions` 设失效字段**不物理删**→ 历史可溯、可时间推理。`add_episode` 抽节点/边 → 保存 → `update_community`（`summarize_pair` 增量重写社区摘要），亦可整表重建。Saga 节点把 episodes 串成叙事线。
- **召回**：`search/search_config_recipes.py` 配方；方法 `cosine/bm25/bfs`，重排器 `rrf/node_distance/episode_mentions/mmr/cross_encoder`；`search_utils.py::rrf` 标准倒数排名融合（`1/(i+rank_const)`, `rank_const=1`）；`DEFAULT_MIN_SCORE=0.6`、`DEFAULT_MMR_LAMBDA=0.5`、`MAX_SEARCH_DEPTH=3`。交叉编码器用本地 `BAAI/bge-reranker-v2-m3`。
- **依赖/吻合**：Apache-2.0；neo4j/pydantic/openai/tenacity/numpy；BM25 交后端。不可变+可重算+边失效+分层召回+席位，高度贴合。**不吻合/成本**：默认要**图数据库**（VPS 显著成本）。**未核实**：社区分区质量、中文语料 RRF 收益、真实调用次数。

### 4.2 Mem0 —— 写入管线与重排工程
- **8 阶段管线**（`memory/main.py::add`，源码注释 `# Phase 0..8`）：上下文 → 取相似旧记忆 → **单次 LLM 抽取** → 批嵌入 → CPU 处理 + **MD5 去重**（`hashlib.md5(text)`）→ 批量落库 → 实体链接（search-then-update-or-insert）→ 存消息返回。
- **更新**：主线 **ADD-only**（v3：只增不改，冲突交检索期排序，历史保留）；旧 prompt 支持 ADD/UPDATE/DELETE/NOOP。上游 PR #5821 正加 `enable_memory_management` 并**默认关闭**（审稿指静默删除=数据丢失风险）——重要工程教训。
- **召回**：`_search_vector_store` 先 `internal_limit=max(limit*4,60)` 过取，再 语义 + `keyword_search`(BM25,lemmatize) + 实体 boost，`score_and_rank(threshold=0.1)` 融合，`normalize_bm25` 归一。候选生成/过滤/排序三层清晰。
- **重排器**：LLM reranker（默认 gpt-5-mini,temp=0,系统提示打分）、HuggingFace 交叉编码器（`BAAI/bge-reranker-base`）、sentence-transformer（`cross-encoder/ms-marco-MiniLM-L-6-v2`）、Cohere、ZeroEntropy。**"判官"岗位有现成实现**。
- **依赖/成本/不吻合**：Apache-2.0；默认 qdrant-client；BM25 需 store 支持 `keyword_search`。论文（E2）称 LOCOMO 上 LLM-as-judge 相对 +26%、p95 时延 -91%、token -90%，**未复现**。面向"每轮对话抽 fact"，对一源多视图支持弱。**未核实**：`evaluation/` 固定 commit 下未见实质文件。

### 4.3 Microsoft GraphRAG —— 社区整理与批处理范式
- **核心**：`index/operations/cluster_graph.py` → `hierarchical_leiden`+`stable_lcc`，`max_cluster_size` 限簇；`create_community_reports` 为每层社区生成报告；检索 `query/structured_search/{local,global,drift}`，全局 **map-reduce over 社区报告**，另有 `dynamic_community_selection`。
- **增量/批处理**：工作流分 `create_*` 与 `update_*`（`update_communities/update_community_reports/update_entities_relationships/update_text_embeddings…`）——**原生增量**；配 `graphrag-cache`（json/memory/noop+cache_key）可复算缓存。
- **依赖**：MIT；纯 Python 包，无强制外部图库（图存 parquet）。**吻合**：确定性聚类+LLM 只写摘要；缓存+增量是运行工程样板。**不吻合**：定位语料级主题问答，全局 map-reduce 对单用户属过度工程。**未核实**：Leiden 规模稳定性、`update_*` 并发行为。

### 4.4 A-MEM —— "演化"宣传 vs 实现的落差（E3）
- **机制**：`add_note`→`process_memory`：取向量近邻 top-5 → LLM 决策 `should_evolve` 及动作 `strengthen`（给新笔记加 links/tags）或 `update_neighbor`（**改写邻居 context/tags**）；`consolidate_memories()` **仅重建 Chroma 集合**，每 `evo_threshold`(默认 100) 次可演化写入触发。
- **重要发现**：README 的"evolution/consolidation"在源码里是(a)LLM 直接改写既有邻居字段、(b)consolidate 只是索引重建，**非语义合并或事实巩固**；且 `update_neighbor` 用近邻循环序号当全局列表下标取邻居（`noteslist[memorytmp_idx]`），**存在改错邻居风险**（代码阅读结论，未运行验证）。`search()` 基本只走 Chroma 向量，`rank_bm25` 未见于生产路径——"hybrid"宣称弱于实现。MIT；末次提交 2025-12，**不吻合**：就地改写历史（与不可变原则冲突）。

### 4.5 HippoRAG 2 —— 关联检索与恢复范式
- **机制**：`src/hipporag/HippoRAG.py` —— LLM OpenIE 出 NER+三元组建图 → **个性化 PageRank** 多跳关联召回 → `rerank.py::DSPyFilter` LLM 过滤/重排事实三元组 → passage/entity/fact 分离嵌入。
- **恢复/幂等**：显式状态校验（`_validate_openie_provenance`、`_load_chunk_metadata`、`StateConsistencyError`、`force_index_from_scratch`）——**换抽取器强制全量重抽**，避免脏派生。
- **依赖/成本**：MIT；`torch 2.5.1`/`transformers`/`python-igraph`——**重，小 VPS 不友好**；论文声称关联记忆 +7%。**不吻合**：离线索引 + GPU 级依赖。

## 5. 对故渊的可用性拆分表

**可直接复用代码（Apache/MIT，语言匹配）**：Graphiti `dedup_helpers.py`（MinHash/LSH 去重）、`label_propagation`、`search_utils.py::rrf`；Mem0 8 阶段写入骨架 + MD5 幂等 + `reranker/*`；GraphRAG `cluster_graph.py`（Leiden+LCC）、`graphrag-cache` 缓存键。

**可借鉴机制（不搬代码）**：边**失效而非删除**；社区/簇用**确定性算法**、摘要交模型且只做候选；召回四层（生成/门/排序/席位），判官只在灰区、可空选；RRF(名次)+MMR(去冗余)+交叉编码器(精排) 分工；三账反馈+影子模式+评测集。

**需要我们补**：中文分词 BM25 与语义权重；情绪/时间/状态作**软加权层**的接法；反思触发与"只产候选"门控（接 04）；单机幂等与恢复（内容哈希+状态文件+唯一作业函数）；独立席位规则（每线一席/新卡窗口/冷却）。

**不建议采用/直接放弃**：图数据库作核心存储；无门控 LLM 自动 ADD/UPDATE/DELETE；A-MEM 式就地改写邻居；HippoRAG 级 torch/igraph 依赖。

**未核实**：见 §10 末行。

## 6. 跨模块接口候选

| 方向 | 输入 | 输出 | 读稳定对象 | 产生/更新 | 信号 | 必须留来源 | 只能软影响 |
|---|---|---|---|---|---|---|---|
| 写入整理 | 证据 episode | 派生候选 | 证据、旧派生 | 派生增强段、去重表 | `write:pending` | episode↔派生出处数组 | 分类/理解待审 |
| 聚类连线 | 向量/tag/显式线索/人工种子 | 族/线候选+成员 | 事件、派生 | 族/线索引(数组)、边表 | `cluster:proposed` | 成员归属来源 | 不硬改事件 |
| 召回 | 冻结查询句、探针 | 候选池 | 卡/行向量、倒排、边、时间元表 | 决策日志 | `recall:trace` | 命中来源(关键词/向量/图/时间) | 情绪/时间/状态=排序层软权 |
| 判官 | 用户原话+菜单 | 席位(可空) | 池内候选 | 选中留痕 | `recall:selected` | 选中理由 | 非灰区不硬过滤 |
| 反馈 | 使用自报/纠错/叉 | 评测样本、健康账 | 决策日志 | 错题本/评测集 | `feedback:recorded` | 判官视角可复现 | 只调整，不自动改语义 |

**原则**：一切"整理/理解/情绪"产物默认只产**候选**；检索只读**稳定对象**；召回决策必须落可复现日志（含早退原因、各路命中、判官菜单与判词）。

## 7. 风险、失败模式与开放问题

- **幻觉/误归因**：LLM 抽派生、写社区摘要会编造 → 原文为唯一事实来源、派生落出处数组、机械+二审抽查。
- **状态漂移/反馈循环**：salience 自我放大、召回推高召回（需"召回不强化"、冷却、新卡窗口）。
- **自动整理误伤**：就地改写/自动删会丢历史 → 失效/取代标记 + 全量可回滚。
- **隐私/可见性**：私密内容默认不进检索通道（单闸函数锚到所有写入入口）。
- **成本与重算**：每次写入多次小模型调用、社区摘要随规模线性；换嵌入模型全库重做向量、换抽取器强制全量重抽。
- **未验证**：影子模式是否覆盖时延（影子里跑得动≠生产延迟达标，须另探真实延迟再开阀）；评测尺子自身抖动。

## 8. 本专题结论（推荐机制）

**A · 建议进入组合设计**
1. **整理分级**：确定性可做（去重：归一化精确名+MinHash/LSH；聚类：label propagation/Leiden+LCC；时间戳解析；哈希幂等）**不调模型**；续整理交模型但**只产候选**；模型输出默认待审，原文永不改写。
2. **失效而非删除**：边/事实带 `valid_at/invalid_at/expired_at`；取代留链、检索删已取代项。
3. **召回分层**：候选生成（关键词∪向量∪图∪时间；过取 4×、下限 60）→ 门 → 排序（相关×时间×重要×冷却）→ 席位（每线一席/新卡窗口/硬上限）→ 判官（灰区、可空选）；融合 **RRF**、精排**交叉编码器**、去冗余 **MMR**。
4. **情绪/时间/状态**：一律**排序/菜单层软加权**，不硬过滤（解析失败静默降级）。
5. **反馈三账**：进池/上桌/落座分开记账 + 使用自报得真实分母；评测台→影子→生产延迟探针→逐机制开关，四段不跳步。
6. **运行分工**：实时=改写+候选+判官；收口=落证据+落隔离候审；夜间=cron 增量写卡/派生/摘要（幂等、占位记时间戳、单函数多入口）；批处理=全量重算派生/聚类/社区（dry-run 出 worklog、apply 只写审过的那份）。

**B · 值得借鉴但不直接采用**：GraphRAG 全局 map-reduce 社区摘要（成本高、面向语料级）；A-MEM"新记忆触发旧记忆演化"直觉（就地改写不可取）；HippoRAG 个性化 PageRank 关联召回（重）；sleep-time 离线预计算（查询可预测才划算）。

**C · 保留候选需验证**：LLM reranker/judge 仅用于灰区（时延/成本待探）；动态社区选择（GraphRAG）；分离嵌入 + LLM 事实过滤。

**D · 当前不建议**：图数据库做核心存储；无门控 LLM 自动 UPDATE/DELETE；任何就地覆盖历史的"整理/演化"；全量 LLM 自由重写摘要并直接入召回。

## 9. 给最终汇总窗口的输入（≤10 条）

1. **整理必须分级**：确定性（去重/聚类/时间/幂等）→ 模型候选（派生/摘要/矛盾）→ 待审生效，是整套工程地基。
2. **"失效而非删除"**是历史与当前共存的唯一低成本解；矛盾/取代走 `invalid_at/expired_at`。
3. **召回四层**（生成/门/排序/席位）+ 灰区判官，是多来源收敛的形状，跨专题可复用。
4. **RRF+MMR+交叉编码器**分工明确：先上 RRF（零依赖、稳），精排按需。
5. **情绪/时间/状态只做软加权**——与 04/05 的接口定在"排序层"，别进本体。
6. **三账反馈**（进池/上桌/落座）+ 使用自报，是区分"没召回/召回错/召回没用"的唯一手段。
7. **运行分工**（实时/收口/夜间/批处理）+ 单函数多入口幂等 + 内容哈希，决定能否在 2–4 核 VPS 稳定。
8. **可复用代码坐标**：Graphiti（去重/社区/RRF/边失效）、mem0（8 阶段写入/重排器）、GraphRAG（Leiden+增量工作流+缓存）。
9. **成本红线**：图数据库、torch/igraph、无门控自动改写判 D；论文时延/token 收益是**声称**，未复现。
10. **跨专题线索**：Graphiti `Saga`（→02）、边 `valid_at/invalid_at`（→01）、摘要"只产候选"与"反思"（→04/05）、`person-world entry` 常驻 vs 按需（→06）。

## 10. 证据索引

**代码（E3，公开仓库只读；访问日期 2026-10-07）**

| 仓库 | 版本/commit | 许可 | 核心路径 |
|---|---|---|---|
| getzep/graphiti | 0.30.2 @`aa5bb2706929fce502d99d8c7c4ddbb77bff4995`(2026-10-06) | Apache-2.0 | `graphiti_core/{search/{search_config_recipes,search_utils}.py, utils/maintenance/{dedup_helpers,community_operations,edge_operations}.py, cross_encoder/bge_reranker_client.py}` |
| mem0ai/mem0 | 2.2.1 @`c93420c49a6b14c3d446bdb156d96811908fd90a`(2026-10-05) | Apache-2.0 | `mem0/memory/main.py`（8 阶段）、`mem0/reranker/*`、`mem0/configs/prompts.py` |
| microsoft/graphrag | 3.2.0 @`769542fbf1d8e5b4c6a8677fefc34621c87894c5`(2026-09-23) | MIT | `packages/graphrag/…/index/operations/cluster_graph.py`、`…/workflows/update_*.py`、`packages/graphrag-cache/` |
| agiresearch/A-mem | @`ceffb860f0712bbae97b184d440df62bc910ca8d`(2025-12-12) | MIT | `agentic_memory/memory_system.py` |
| OSU-NLP-Group/HippoRAG | @`d5c8329422e0a0b834a15874545cb6a74b4f9b26`(2026-10-06) | MIT | `src/hipporag/{HippoRAG.py,rerank.py,information_extraction/}` |

源码快照：`_snapshots/07-consolidation-runtime/`。

**论文（E2，arXiv 摘要经 `export.arxiv.org` 抓取，2026-10-07）**：Generative Agents 2304.03442；Sleep-time compute 2504.13171；LongMemEval 2410.10813（声称商业助手跨会话掉约 30%）；LoCoMo 2402.17753；Zep/Graphiti 2501.13956（DMR 94.8% vs 93.4%、LongMemEval 最高 +18.5%、时延 -90%）；Mem0 2504.19413；HippoRAG 2 2502.14802；A-MEM 2502.12110；GraphRAG 2404.16130；RAPTOR 2401.18059；LLM-as-judge 2306.05685；Lost in the Middle 2307.03172。

**其它（E1/E0）**：Mem0 迁移/架构文档 docs.mem0.ai；mem0 PR #5821（`enable_memory_management` 门控与数据丢失风险）；MemEmo（情绪记忆评测，编号待复核→未核实）。

**未核实项**：影子模式/评测框架强一手项目；社区分区质量与中文语料检索收益；各库真实模型调用次数与延迟；MemEmo 确切编号；A-MEM 邻居错位是否在生产路径触发。
