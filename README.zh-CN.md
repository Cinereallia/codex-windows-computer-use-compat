[English](README.md) | [简体中文](README.zh-CN.md)

# Codex Windows Computer Use 兼容 Skill

> 临时、仅显式调用的社区 workaround。非 OpenAI 官方项目，也不代表官方背书。

当 Codex Desktop 运行 Windows Native、浏览器 Computer Use 正常，但原生 Windows 应用缺失或无法控制时，使用本 Skill。它复用内置 `mcp__node_repl__js + @oai/sky` 路径，不 patch Codex，也不修改系统配置。

## 安装

```text
$skill-installer Install the skill from this GitHub URL:
https://github.com/Cinereallia/codex-windows-computer-use-compat/tree/main/skills/codex-windows-computer-use-compat
```

安装后显式调用本 Skill。如果 Codex 没有检测到它，再开启新的 Codex 任务或重启 Codex。前提是官方 Computer Use 插件和 `mcp__node_repl__js` 已可用。

## 使用

针对 Windows 原生应用任务显式调用：

```text
$codex-windows-computer-use-compat Open a Windows desktop app and complete this GUI task: inspect its current UI and report what is visible. Do not use shell.
```

Skill 使用 native `@oai/sky` API，不使用 shell 自动化或 browser-only `cua_repl`。如果必需工具/runtime 不可用，会停止并报告阻塞原因。

## 详细说明

实现细节、已验证快照、回归测试标准、安全边界、upstream 报告、prior art、来源、弃用和卸载说明见 [中文技术背景](docs/technical-background.zh-CN.md) 或 [English technical background](docs/technical-background.md)。
