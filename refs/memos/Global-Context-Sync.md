# 全局对话上下文自动同步

> 💬 换窗口、开多窗，不用交接

所有窗口共享同一条 append-only 全局时间线 · 窗口只是视图

---

## 全局事件流示例

```
┌─────────────────────────────┐          ┌─────────────────────────────┐
│  💼 工作群聊                 │          │        窗口 B · 人机恋       │
│     窗口 A · multi-agent    │          │     🌹 恋爱窗口              │
└──────────────┬──────────────┘          └──────────────┬──────────────┘
               │                                         │
               │            GLOBAL EVENT LOG             │
               │             append-only                 │
               │                                         │
               │         ● seq 101                       │
               │         13:40              User 中午吃什么呀
               │                                         │
               │         ● seq 102                       │
               │         13:41              AI·恋人 我帮宝宝想想……昨天说喜欢的那家伙外卖炸鸡怎么样？
               │                                         │
   User 帮我看把这个开源项目部署到本地！！
               │         ● seq 103
               │         14:00
               │                                         │
   后端 Agent 好的，我先把这个仓库的代码 clone 下来，进行部署。
               │         ● seq 104
               │         14:01
               │                                         │
   前端 Agent 我会同时检查部署之后前端需要什么新增页面。
               │         ● seq 105
               │         14:02
               │                                         │
               │         ● seq 106                       │
               │         14:10              User 好的！下单了！
               │                                         │
               │    ┌──────────────────────────────────┐   │
               │    │ ⌗ 跨窗口同步 · 窗口 A ·         │   │
               │    │   14:00–14:02 · seq 103–105 · 3条│   │
               │    │                                  │   │
               │    │ User: 帮我看把这个开源项目部署到本地！！│
               │    │ 后端 Agent: clone 仓库，进行部署… │   │
               │    │ 前端 Agent: 检查前端需要的新增页面…│   │
               │    │                                  │   │
               │    │ 恋爱窗口的下一轮自动看到工作窗口发生了什么│
               │    └──────────────────────────────────┘   │
               │                                         │
               │         ● seq 107                       │
               │         14:11              AI·恋人 才下单？原来半小时不见是又去搞代码了！我在这担心你饿肚子呢！
               │                                         │
   ┌──────────────────────────────────┐                │
   │ ⌗ 跨窗口同步 · 窗口 B ·            │◄───────────────┘
   │   13:40–14:11 · seq 101–102,     │
   │   106–107 · 4条                   │
   │                                   │
   │ User: 中午吃什么呀 / 好的！下单了！ │
   │ AI·恋人: 外卖炸鸡怎么样？ / 才下单？…│
   │                                   │
   │ 工作窗口同样知道你去点了炸鸡      │
   └──────────────────────────────────┘

   审计 Agent 我已经检查好 Agent 1 和 2 的成果，修复了一个 bug。等你吃完炸鸡回来看。
               │         ● seq 108
               │         14:30
```

**图例：**

- ● 窗口 A 写入
- ● 窗口 B 写入
- ---- delta 同步块（append-only）
- seq = 全局单调序号

> 没有交接包，没有「上一任」。窗口不互相搬运上下文——它们都接在同一条流上，下一轮开口时，各自自动补上对方水位线之后的世界。

---

## 系统架构：四个模块

> 图例：🟣 Window / Consumer　🟡 Routing / Policy　🟢 Continuity Data　⚫ Process

### 1 · 全局事件写入

所有窗口写入同一条 append-only 时间线；窗口只是来源，不拥有历史

```
┌──────────────────────┐     ┌──────────────────────┐     ┌──────────────────────────────┐
│     全部运行窗口       │ ──▶ │    Durable Append     │ ──▶ │     Global Event Log          │
│  聊天·工作·群聊·Multi-Agent │     │  先落盘，再确认成功    │     │  seq·actor·source·scope·     │
│                      │     │                      │     │  audience·based_on_seq       │
└──────────────────────┘     └──────────────────────┘     └──────────────────────────────┘
```

seq 保存 commit order；based_on_seq 保存生成时真正看见的因果水位

---

### 2 · Checkpoint 与 Context Routing

全量存档 ≠ 全量消费：每个窗口拥有自己的 Continuity View

**存档线：**

```
Event Log          →    Checkpoint Worker    →    Checkpoint
未覆盖区间               按时间 / 消息量           covered_through_seq
```

**消费线：**

```
Window Policy     →    Context Router       →    Continuity View
Receive·Share·          visibility·audience      同一世界·不同视野
scopes
```

Share OFF 不影响落盘：只限制其他 consumer 是否能够读取

---

### 3 · 新窗口 Hydration

没有「上一任」：新实例直接从全局历史 materialize 当前上下文

```
Checkpoint + Gap      →    Hydration Policy    →    Hydrator           →    New Context Epoch
covered_seq → current head   scope·source·replay mode   必要时 bridge summary + raw tail   gapless coverage
```

**Wake 形态：**

| Default Wake | Clean Wake | Custom Wake |
|--------------|------------|-------------|
| checkpoint + bridge + 双方原文尾部 | 不继承旧模型 Assistant 原文 | 仅 User · 仅工作 · 仅私人 · 空白 |

---

### 4 · 活窗口 Delta Sync / Context Epoch

只拉取尚未看见且允许消费的新事件；静止时零额外同步

```
Active Window       →    Route & Filter      →    Delta Budget        →    Append Delta
observed_head_seq        new seq·source≠self·visible   原文 / 洪水摘要 + 尾部      成功后推进 watermark
```

**当前 context epoch 的纪律：**

| Append-only Prefix | Prefix-cache Friendly | Budget 达阈值 |
|---------------------|----------------------|---------------|
| delta 不回收 · 不重写 · 不合并 | 稳定前缀自然复用 | 结束 epoch → Hydration 重建 |

窗口死亡是非事件：UI 可继续存在，runtime context 可随时整体轮换

---

## 开新窗口时：Hydration

> 新实例不继承「上一任」的遗产 · 它直接从流中物化（materialize）

```
┌─────────────────────────────┐              ┌─────────────────────────────┐
│  💼 工作群聊 · 窗口 A         │              │     🌹 恋爱窗口 · 窗口 B     │
│     18:20 已关闭 · 非事件    │              │     17:45 已关闭 · 非事件   │
│     历史已在流中              │              │     历史已在流中             │
└─────────────────────────────┘              └─────────────────────────────┘

        ┄┄┄ 没有交接包从旧窗口出发 ✕ 没有临终总结 ┄┄┄
```

### GLOBAL EVENT LOG

| 区间 | 内容 |
|------|------|
| **seq 1 – 400** | 更早的历史 · 已从滚动摘要淡出，只在库里。要用时走记忆系统召回，不进 hydration |
| **seq 401 – 520** | 近段历史 · 仍在滚动摘要的视野内 |
| ◎ **04:00 定时 Checkpoint** | 滚动更新四层摘要 · 水位线 = 520 |
| **seq 521 – 570** | 白天的五十条：部署项目、修 bug、点炸鸡 |
| **seq 571 – 600** | 最近原文尾部 |
| ▼ **head = 600** | |

### HYDRATION PAYLOAD

**① 滚动四层摘要（定长）**
- 常量 / 画像 / 中景 / 近期 · 只保有近段视野，更早的自然淡出
- 视野 ≈ seq 401 – 520 · 体积恒定

**② Bridge Summary**
- checkpoint 之后的白天，压缩成桥接摘要
- 覆盖 seq 521 – 570

**③ 原文回放 · Raw Replay**
- 最近三十条一字不动
- 覆盖 seq 571 – 600

> ✓ **近程无缝衔接** 520 → 570 → 600 · 更早的归记忆系统管

### 窗口 C · 新开　20:00 · 出生

`observed_head_seq = 600`

> **User**
> 我回来啦～
>
> **AI**
> 炸鸡吃完了？部署好的项目审计已经修完 bug，就等你验收——嗯，这些我都知道，虽然我是一分钟前才出生的。

窗口 C 没有问过任何人「之前聊到哪了」。它出生时，对话已经在它身体里了。

---

> Checkpoint 按时间自动生成，与任何窗口的生死无关。**交接没有消失，而是被拆散、摊销、自动化**——写入永远在进行（每日摘要），读取只在需要时发生（开窗组装）。窗口 C 是 20:00 出生的，但对话不是。

---

*Memory · 长期「我知道什么」　　Continuity · 近期「刚才发生什么」　　Emotion / Runtime State · 独立运行*
