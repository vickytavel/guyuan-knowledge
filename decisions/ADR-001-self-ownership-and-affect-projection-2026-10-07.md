# ADR-001 · Self Ownership 与 V/A Projection 的权威分工

Date: 2026-10-07（Asia/Shanghai）  
Status: ACCEPTED（仅下列两项用户决定；横向层组装仍 CANDIDATE）  
来源：用户本轮任务明确重申；接手时第二阶段 §10 已保存原 OPEN 与补充。不是本窗口新研究结论，也不是实现授权。

## Context

OPEN-4 缺少主体认领的合法来源，可能让普通模型叙述绕过长期对象/感受事件的门。OPEN-6 在固定公式垄断个体投影与单轮随意改权重之间未有分工。原问题与理由见 [第二阶段 §10.1/10.2](../memory-affect-02-第二阶段-内部状态表示与运行契约裁决-2026-10-07.md#10-开放项裁决补充2026-10-07)。

## Decision

1. **Self Ownership = Subject-owned decision + Host-issued authority receipt**。主体决定自身理解的 AFFIRM / REJECT / SUSPEND；Host 验证 actor、领域资格与目标后签发。普通叙述没有该权威；Source receipt 不替代 Ownership。
2. **V/A Projection = Versioned Base Projection + slow-changing Subject Calibration**。系统坐标系由显式配置迁移维护；主体投影差异有长期证据、provenance、慢变与认领，独立版本化。单轮 Mood/Emotion 不能改权重；历史 Annotation 保留当时判读/投影/校准版本。

## Why

第一项让主体保持对自身立场的决定权，同时防止模型自报“已认领”成为权限来源。第二项保留稳定坐标与个体体验，避免把投影当可写感受槽或用当前校准回写过去。

## Rejected alternatives

- 普通自然语言直接取得认领权；adapter 来源证明代替主体认领。
- 只靠固定公式定义个体体验；主体/模型每轮改 Base 权重。
- 认领他人的事实、单方生成共同事实、代对方签承诺。

详细旧文本在 [吸收前快照](../archive/memory-affect/2026-10-07-authority吸收前/memory-affect-02-第二阶段-内部状态表示与运行契约裁决-2026-10-07.md)，不作为 current。

## Consequences

- Ownership、Source、Shared Grounding、Commitment、Calibration 与 Permission 不能互换证明。
- capability 是受限操作的运行时资格；receipt 保存当次域内动作/结果，不自带下一次权限。横向层最小职责是以上原则的 **CANDIDATE 组织方式**，不是已选独立服务。
- 注入与历史投影需可辨 Base/Calibration 版本；版本/时间陈旧分别暴露，具体表示待设计。
- 对 Affect 变化的认领才可走 Durable Event 覆盖；其他对象的认领不自动生成感受历史。
- 配置迁移、跨进程校验、失效/重放、共同确认、AI Commitment 验真、Calibration 历史类型/门槛仍 OPEN。

## Affected docs

- [第二阶段裁决 §10](../memory-affect-02-第二阶段-内部状态表示与运行契约裁决-2026-10-07.md)
- [current 00c](../memory-affect-01/00c-长期认知系统-整体架构候选框架-2026-10-07.md)、[参考地图 00b](../memory-affect-01/00b-记忆情绪系统-研究参考地图-2026-10-07.md)
- [OPEN 入口](../OPEN-QUESTIONS.md)、[阶段 checkpoint](../../handoffs/memory-affect-长期认知连续性-Checkpoint-2026-10-07.md)

实现状态：未实现；未改 schema、API、配置、产品代码或模型路由。
