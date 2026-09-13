[English](README.md) | [简体中文](README.zh-CN.md)

# Codex Windows Computer Use Compatibility

> Temporary, explicit-only community workaround. Not affiliated with or endorsed by OpenAI.

Use this Skill when Codex Desktop is in Windows Native mode, browser Computer Use works, but native Windows apps are missing because the request is routed to browser-only `cua_repl`. It reuses the bundled `mcp__node_repl__js + @oai/sky` path; it does not patch Codex or change system configuration.

## Install

```text
$skill-installer Install the skill from this GitHub URL:
https://github.com/Cinereallia/codex-windows-computer-use-compat/tree/main/skills/codex-windows-computer-use-compat
```

Start a new Codex task after installation. The official Computer Use plugin and `mcp__node_repl__js` must already be available.

## Use

Invoke it explicitly for a Windows native-app task:

```text
$codex-windows-computer-use-compat Open Notepad, type TEST, and verify that TEST is visibly present. Do not save the file.
```

The Skill uses native `@oai/sky` APIs, never shell automation or the browser-only `cua_repl` route. If the required tool/runtime is unavailable, it stops and reports the blocker.

## Verify

A passing smoke test must show: `sky.target === "windows"`, successful native app/window enumeration, Notepad opened and uniquely selected, `TEST` entered once, and a fresh observation visibly confirming `TEST`.

For implementation details, version snapshots, safety boundaries, upstream reports, prior art, provenance, and deprecation guidance, see [technical background](docs/technical-background.md) or [中文技术背景](docs/technical-background.zh-CN.md).
