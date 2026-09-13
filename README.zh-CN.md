[English](README.md) | [简体中文](README.zh-CN.md)

# Codex Windows Computer Use 兼容 Skill

> 临时、仅显式调用的社区 workaround。非 OpenAI 官方项目，也不代表官方背书。

当 Codex Desktop 运行 Windows Native、浏览器 Computer Use 正常，但原生 Windows 应用因请求被送入 browser-only `cua_repl` 而不可用时，使用本 Skill。它复用内置 `mcp__node_repl__js + @oai/sky` 路径，不 patch Codex，也不修改系统配置。

## 安装

```text
$skill-installer Install the skill from this GitHub URL:
https://github.com/Cinereallia/codex-windows-computer-use-compat/tree/main/skills/codex-windows-computer-use-compat
```

安装后开启新的 Codex 任务。前提是官方 Computer Use 插件和 `mcp__node_repl__js` 已可用。

## 使用

针对 Windows 原生应用任务显式调用：

```text
$codex-windows-computer-use-compat Open Notepad, type TEST, and verify that TEST is visibly present. Do not save the file.
```

Skill 使用 native `@oai/sky` API，不使用 shell 自动化或 browser-only `cua_repl`。如果必需工具/runtime 不可用，会停止并报告阻塞原因。

## 验证

通过标准是：`sky.target === "windows"`，原生应用/窗口枚举成功，Notepad 被打开并唯一选中，`TEST` 只输入一次，并由新的窗口观察明确确认 `TEST` 可见。

实现细节、版本快照、安全边界、upstream 报告、prior art、来源、弃用和卸载说明见 [中文技术背景](docs/technical-background.zh-CN.md) 或 [English technical background](docs/technical-background.md)。
