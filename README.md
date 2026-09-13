[English](README.md) | [简体中文](README.zh-CN.md)

# Codex Windows Computer Use Compatibility

> Temporary, explicit-only community workaround. Not affiliated with or endorsed by OpenAI.

Use this Skill when Codex Desktop is in Windows Native mode, browser Computer Use works, but native Windows apps are missing or cannot be controlled. It reuses the bundled `mcp__node_repl__js + @oai/sky` path; it does not patch Codex or change system configuration.

## Install

```text
$skill-installer Install the skill from this GitHub URL:
https://github.com/Cinereallia/codex-windows-computer-use-compat/tree/main/skills/codex-windows-computer-use-compat
```

After installation, invoke the Skill explicitly. If Codex does not detect it, start a new task or restart Codex. The official Computer Use plugin and `mcp__node_repl__js` must already be available.

## Use

Invoke it explicitly for a Windows native-app task:

```text
$codex-windows-computer-use-compat Open a Windows desktop app and complete this GUI task: inspect its current UI and report what is visible. Do not use shell.
```

The Skill uses native `@oai/sky` APIs, never shell automation or the browser-only `cua_repl` route. If the required tool/runtime is unavailable, it stops and reports the blocker.

## Details

For implementation details, validated snapshots, smoke-test criteria, safety boundaries, upstream reports, prior art, provenance, and deprecation guidance, see [technical background](docs/technical-background.md) or [中文技术背景](docs/technical-background.zh-CN.md).
