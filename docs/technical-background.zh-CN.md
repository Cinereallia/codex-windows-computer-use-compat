# 技术背景

[README](../README.zh-CN.md) · [English](technical-background.md)

## 范围与状态

本仓库是针对一个 Windows Native Codex Desktop 路由故障的临时兼容 Skill。它不是 Computer Use 替代实现、Codex patch 或系统修复工具。

Skill 通过 `agents/openai.yaml` 设置为仅显式调用，不会自动接管无关的 Computer Use 任务。

## 已确认行为

受影响路径会把 unified `cua_repl` 暴露成只有浏览器能力的 surface。常见症状包括 native app list 为空、`cua.getApp`/`cua.listApps` 缺失，或 unified facade 只有浏览器方法，但 Windows helper、named pipe 和 Sky package 实际存在。

已验证的兼容路径是 trusted legacy `mcp__node_repl__js` 加载内置 `@oai/sky` client。它已验证能够：

- 报告 `sky.target === "windows"`；
- 枚举 Windows 应用和窗口；
- 通过 native API 启动 Notepad；
- 选择并激活 API 返回的 Notepad 窗口；
- 只输入一次 `TEST`；
- 通过新的后置截图确认 `TEST`，同时保持文档未保存。

Skill 不修改 `CUA_REPL_ENABLED_SURFACES`、`NODE_REPL_TRUSTED_SERVICES`、`.mcp.json`、Codex 配置、ACL、环境变量、helper binary 或 named pipe。

## 版本证据

以下是观察快照，不是兼容性上下界。

| 验证 | Windows app build | Computer Use plugin | Runtime packages | 结果 |
| --- | --- | --- | --- | --- |
| 完整 E2E，2026-09-12 | `26.903.9818.0` | `26.903.71938` | cua-node `0.0.11`、Node `24.20.0`、`@oai/sky 0.6.26`、`@oai/cua 0.2.4` | Notepad `TEST` 通过 |
| 完整 E2E，2026-09-13 | `26.908.4834.0` | `26.908.40834` | cua-node `0.0.11`、Node `24.20.0`、`@oai/sky 0.6.32`、`@oai/cua 0.2.4`、`@oai/cua-repl 0.1.0` | 发布后 Skill smoke test 通过 |

## 必须遵守的行为与安全边界

Skill 要求宿主提供 `mcp__node_repl__js` 和 trusted `@oai/sky`。如果任一不可用、Sky target 不是 Windows，或应用/窗口枚举为空或有歧义，必须停止并报告。

工作流使用最新返回的 app/window 对象，并遵循“观察 → 单次动作 → 再观察”。不能根据动作尝试、旧坐标、旧 element index 或仅窗口标题宣称成功。

不得使用 PowerShell、Command Prompt、shell、Windows Run、SendKeys、自定义 helper、自定义 pipe client，或配置修改来替代 Computer Use。宿主审批、UAC、管理员策略、安全桌面、认证和应用安全规则仍然有效。

本 Skill 不适用于纯浏览器、WSL、无人值守/锁屏操作、runtime 安装或 Codex 自身修复。

## 安装、卸载与生命周期

通过内置 Skill Installer 安装指定 Skill 子目录：

```text
$skill-installer Install the skill from this GitHub URL:
https://github.com/Cinereallia/codex-windows-computer-use-compat/tree/main/skills/codex-windows-computer-use-compat
```

安装器会递归复制该目录到用户 Skill 目录。卸载时只删除已安装的 `codex-windows-computer-use-compat` 目录，不要删除 Codex cache、runtime、session 或官方 Computer Use plugin。

未来 Codex stable build 在 fresh task 中通过默认 Computer Use 路径完成同一个 Notepad 测试后，应停止调用本 Skill，标记仓库 Deprecated，发布最终弃用 release 并 archive。项目不实现自动版本检测或 fallback 框架。

## Upstream 报告与 prior art

官方背景：[OpenAI Computer Use 文档](https://learn.chatgpt.com/docs/computer-use)与 [OpenAI Skills 文档](https://learn.chatgpt.com/docs/build-skills)。

直接相关的公开报告（最后核对：2026-09-12）：

- [openai/codex #43594](https://github.com/openai/codex/issues/43594) — Windows computer surface 被 unified CUA 错误排除
- [openai/codex #43498](https://github.com/openai/codex/issues/43498) — browser-only materialization 与配置重生成
- [openai/codex #42745](https://github.com/openai/codex/issues/42745) — browser-only CUA 未注册 Sky trusted service
- [openai/codex #42941](https://github.com/openai/codex/issues/42941) — unified facade 未暴露 native Sky methods
- [openai/codex #44393](https://github.com/openai/codex/issues/44393) — Windows Computer Use 已启用但 native enumeration 为空

社区 [codex-router Computer Use Skill](https://github.com/duolahypercho/codex-router/blob/main/skills/codex-computer-use/SKILL.md) 是使用 `mcp__node_repl__js` 与 `@oai/sky` 的 prior art，主要面向 custom-model session。本项目独立记录 Windows browser-only routing 情况和真实 Notepad E2E，不宣称首个或唯一实现。

[codex-computer-use-windows](https://github.com/ezpzai/codex-computer-use-windows) 与 [codex-computer-use](https://github.com/wangye520/codex-computer-use) 是独立的 executor/移植项目，不是本项目依赖，本仓库不分发其文件。

## 来源与许可证

文档与 Skill 指令根据本机只读调查、两次真实 Windows E2E、OpenAI 官方文档和上述公开链接重新编写。

本仓库不包含或重新分发 OpenAI 内置 Skill、`@oai/sky`、`@oai/cua`、`@oai/cua-repl`、`codex-computer-use.exe`、Codex binary、MSIX/ASAR、plugin cache、本机日志、截图、session 数据、credential 或私人配置。

本仓库原创内容使用 [MIT License](../LICENSE)，可安装 Skill 子目录中也保留相同许可证。该许可不适用于 OpenAI 产品，也不授予 OpenAI 商标权；OpenAI 不维护或背书本项目。
