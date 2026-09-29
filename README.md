# Agent Reliability Patterns

Field notes, failure patterns, and practical guardrails for long-running LLM agents.

这个仓库用于积累不同模型、Agent Harness 和长时程任务中的可复现问题，以及经过验证的缓解措施。它关注的不是“哪个模型更强”，而是以下工程问题：

- 一个问题是在特定模型中出现，还是跨模型存在？
- 问题来自模型、Harness、项目上下文，还是任务治理方式？
- 一条 Prompt 规则是否真的降低了失败率，还是只让输出看起来更规范？
- 观察、根因假设、可注入规则和验证结果如何分开保存？

## 仓库结构

```text
cases/       脱敏后的现场时间线、命令证据、因果链和计量口径
patterns/    带适用范围和证据边界的模型/Harness 观察记录
guardrails/  可复制到 system prompt、AGENTS.md 或项目契约的短规则
evals/       可重复运行的对照场景、通过条件和评价指标
templates/   新增观察与复现实验的文档模板
```

首个案例来自一次通过 pi 运行 GLM-5.3 的长会话：

- [GLM-5.3 + pi 长会话中的六类可靠性不足模式](patterns/glm-5.3-pi-long-session-failure-modes.md)
- [长会话 Agent 可靠性护栏（可注入短版）](guardrails/long-session-reliability-guardrails.md)

第二个案例来自一次 Kimi K3 + pentest-agent 长会话中的执行控制故障链：

- [现场案例：SSH 转发、重复检查与子进程阻塞](cases/2026-09-29-kimi-pentest-agent-execution-failures.md)
- [模式抽象：零证据重试、基线前复杂化与生命周期错配](patterns/kimi-pentest-agent-long-session-execution-failures.md)
- [执行控制护栏](guardrails/execution-control-guardrails.md)
- [执行控制评测场景](evals/execution-control-scenarios.md)

## 产出流程

```text
现场案例 → 模式候选 → 可注入护栏 → 对照评测 → 状态晋级或证伪
```

1. `cases/` 先保存发生了什么，保留时间线、证据单位和因果依赖；
2. `patterns/` 再回答哪些行为可能跨任务复现，明确命中与排除条件；
3. `guardrails/` 将缓解措施压缩为可以执行和检查的短规则；
4. `evals/` 固定输入、变量、指标和门槛，比较有无护栏的差异；
5. 根据复现结果更新状态，不因一次案例直接声称跨模型成立。

新增现场记录时复制[现场案例模板](templates/case-observation-template.md)；形成可泛化假设后再使用[模式研究模板](templates/pattern-study-template.md)。

## 为什么不直接把研究文档命名为 `AGENTS.md`

`AGENTS.md` 是运行时指令入口，不适合同时承担研究报告、根因假设和历史记录。把长篇分析全部注入上下文，会增加指令竞争和注意力成本，也不利于判断哪一条规则真正有效。

本仓库将两者分开：

1. `patterns/` 保存完整观察、证据、假设和限制；
2. `guardrails/` 只保存能够执行和检查的短规则；
3. 使用者按任务风险选择规则，再复制到自己的上下文文件；
4. 规则效果通过前后对照指标验证，不以“模型说自己会遵守”为证据。

## 证据等级

每篇模式文档应标明以下状态之一：

| 状态 | 含义 |
| --- | --- |
| `field-observation` | 在真实任务中观察到，但没有受控复现 |
| `reproduced` | 在同一模型与 Harness 中重复出现 |
| `cross-model` | 在多个模型或 Harness 中独立复现 |
| `mitigated` | 缓解措施经过前后对照并达到预设门槛 |
| `not-reproduced` | 后续实验未能复现，保留记录但不作为通用规则 |

## 使用边界

- 模型行为会随版本、推理预算、工具权限、上下文压缩和 Harness 实现变化。
- 单次长会话可以产生有价值的假设，但不能独自证明“所有 LLM 都如此”。
- 根因描述默认是待验证假设，除非存在模型提供方或 Harness 实现证据。
- 护栏也有成本。过多、重复或互相冲突的规则可能降低任务效果。
- 仓库不收录 API Key、私有代码、客户数据或未脱敏的会话记录。

## 新增案例

复制 [模式研究模板](templates/pattern-study-template.md)，记录模型、Harness、任务长度、工具权限、上下文策略、复现步骤、观察证据和评价指标。优先提交能够被第三方复现或反驳的材料。
