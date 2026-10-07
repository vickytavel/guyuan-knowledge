# Guyuan Knowledge · 云端知识库种子包

> 生成日期：2026-10-08  
> 来源：`D:/48314/Ithaca` 的只读整理副本。  
> 当前用途：供手机端 ChatGPT Work / Codex Cloud / GitHub 云端研究读取。  
> **注意：当前 D 盘项目仍是正式 source of truth。此仓库在明确迁移前只是云端协作副本。**

## 新窗口最少阅读顺序

1. `00-entry/AGENTS.md`
2. `00-entry/当前交接.md`
3. `00-entry/OPEN-QUESTIONS.md`
4. 与当前任务对应的 `current/` 文件
5. 必要时再进入 `research/`、`refs/`、`tasks/`

不要先把整个仓库全部塞进上下文。按任务只读必要层。

## 目录职责

- `00-entry/`：规则、当前交接、开放问题。新窗口入口。
- `current/`：当前架构、当前候选、当前阶段裁决。优先级高于历史研究。
- `decisions/`：ADR，保存已经明确做过的架构决定。
- `handoffs/`：checkpoint 与窗口恢复规则。
- `research/`：调研正文与框架比较，保存证据和设计演变。
- `tasks/`：当前仍有参考价值的研究任务卡。
- `refs/memos/`：用户提供或此前整理的参考材料，不自动等于 current。

## 云端工作纪律

- GitHub / Work / Codex 研究可以直接新增 report / checkpoint。
- 未经复核，不要直接把 research 结论改成 current。
- 重大裁决保留：原状态 → 新证据/用户决定 → 裁决 → 理由 → 影响 → 剩余 OPEN。
- 外部项目有源码时，关键工程主张尽量做到固定 commit 的 E3。
- E3 是“具体主张被源码支持”，不是“这个仓库存在源码所以全项目自动 E3”。
- 任何云端修改回本地前，应先与 D 盘最新版本对照，避免双真源漂移。

## 当前最适合纯云端做的任务

- `Pyruslili/Nocturne-Memory-Core` fixed-commit 源码追溯。
- E1/E2 参考中“有公开源码但未做 E3”的漏检审计。
- 公开 GitHub / 官方文档 / 论文调研。
- framework / Context / Capability / Initiative 架构讨论与报告草拟。

## 暂不上传 / 不应进入知识仓

- `.env`、API key、token、cookie、session、SSH key。
- Serein 实际数据库、私人聊天原文、摄像头素材、敏感 Evidence。
- 大型运行日志、实验缓存、下载依赖、虚拟环境。
- 旧世界模拟和已退出核心的大量历史 task，除非某次研究明确需要。
- 产品代码本身应继续留在独立代码仓，不混入本知识仓。
