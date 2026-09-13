# Technical Background

[README](../README.md) · [简体中文](technical-background.zh-CN.md)

## Scope and status

This repository is a temporary compatibility Skill for one Windows Native Codex Desktop routing failure. It is not a replacement Computer Use implementation, a Codex patch, or a system repair tool.

The Skill is explicit-only through `agents/openai.yaml`. It does not automatically take over unrelated Computer Use tasks.

## Confirmed behavior

The affected path exposes the unified `cua_repl` surface with browser capabilities only. Typical symptoms are an empty native app list, missing `cua.getApp`/`cua.listApps`, or a browser-only facade even though the Windows helper, named pipe, and Sky package are present.

The validated compatibility path is the trusted legacy `mcp__node_repl__js` tool with the bundled `@oai/sky` client. It was verified to:

- report `sky.target === "windows"`;
- enumerate Windows applications and windows;
- launch Notepad with the native API;
- select and activate the returned Notepad window;
- type `TEST` once; and
- confirm `TEST` in a fresh post-action screenshot while leaving the document unsaved.

The Skill does not modify `CUA_REPL_ENABLED_SURFACES`, `NODE_REPL_TRUSTED_SERVICES`, `.mcp.json`, Codex configuration, ACLs, environment variables, helper binaries, or named pipes.

## Version evidence

These are observation snapshots, not compatibility bounds.

| Verification | Windows app build | Computer Use plugin | Runtime packages | Result |
| --- | --- | --- | --- | --- |
| Full E2E, 2026-09-12 | `26.903.9818.0` | `26.903.71938` | cua-node `0.0.11`, Node `24.20.0`, `@oai/sky 0.6.26`, `@oai/cua 0.2.4` | Notepad `TEST` passed |
| Full E2E, 2026-09-13 | `26.908.4834.0` | `26.908.40834` | cua-node `0.0.11`, Node `24.20.0`, `@oai/sky 0.6.32`, `@oai/cua 0.2.4`, `@oai/cua-repl 0.1.0` | Published-Skill smoke test passed |

## Required behavior and safety boundary

The Skill requires the host-provided `mcp__node_repl__js` tool and trusted `@oai/sky`. It must stop if either is unavailable, if the Sky target is not Windows, or if app/window selection is empty or ambiguous.

The workflow uses fresh returned app/window objects and an observe → act once → observe loop. It must not claim success from an attempted action, stale coordinates, stale element indexes, or a window title alone.

It must not use PowerShell, Command Prompt, shell, Windows Run, SendKeys, a custom helper, a custom pipe client, or configuration edits as substitutes for Computer Use. Host approval, UAC, administrator policy, secure desktop, authentication, and app-specific safety rules remain in force.

The Skill is not for browser-only tasks, WSL sessions, unattended/locked-screen operation, runtime installation, or repairing Codex itself.

## Installation, uninstall, and lifecycle

Install the selected Skill directory through the bundled Skill Installer:

```text
$skill-installer Install the skill from this GitHub URL:
https://github.com/Cinereallia/codex-windows-computer-use-compat/tree/main/skills/codex-windows-computer-use-compat
```

The installer copies the selected directory recursively into the user Skill location. Uninstall by removing only the installed `codex-windows-computer-use-compat` directory. Do not remove Codex caches, runtime directories, sessions, or the official Computer Use plugin.

When a future stable Codex build passes the same Notepad test through the default Computer Use route in a fresh task, stop invoking this Skill, mark the repository deprecated, publish a final deprecation release, and archive the repository. No automatic version detector or fallback framework is included.

## Upstream reports and prior art

Official background: [OpenAI Computer Use documentation](https://learn.chatgpt.com/docs/computer-use) and [OpenAI Skills documentation](https://learn.chatgpt.com/docs/build-skills).

Directly relevant public reports, last checked 2026-09-12:

- [openai/codex #43594](https://github.com/openai/codex/issues/43594) — Windows computer surface incorrectly gated out of unified CUA
- [openai/codex #43498](https://github.com/openai/codex/issues/43498) — browser-only materialization and regenerated configuration
- [openai/codex #42745](https://github.com/openai/codex/issues/42745) — Sky trusted service omitted from browser-only CUA
- [openai/codex #42941](https://github.com/openai/codex/issues/42941) — unified facade omits native Sky methods
- [openai/codex #44393](https://github.com/openai/codex/issues/44393) — enabled Windows Computer Use with empty native enumeration

The community [codex-router Computer Use Skill](https://github.com/duolahypercho/codex-router/blob/main/skills/codex-computer-use/SKILL.md) is prior art for using `mcp__node_repl__js` with `@oai/sky`, primarily in custom-model sessions. This project independently documents the Windows browser-only routing case and a real Notepad E2E test; it does not claim to be first or unique.

[codex-computer-use-windows](https://github.com/ezpzai/codex-computer-use-windows) and [codex-computer-use](https://github.com/wangye520/codex-computer-use) are separate executor/porting projects. They are not dependencies and no files from them are distributed here.

## Provenance and license

The documentation and Skill instructions were rewritten from the local read-only investigation, the two real Windows E2E validations, official OpenAI documentation, and the public links above.

This repository does not contain or redistribute OpenAI's bundled Skill, `@oai/sky`, `@oai/cua`, `@oai/cua-repl`, `codex-computer-use.exe`, Codex binaries, MSIX/ASAR files, plugin caches, local logs, screenshots, session data, credentials, or private configuration.

The original repository contents are licensed under the [MIT License](../LICENSE). The same license is included in the installable Skill directory. It does not apply to OpenAI products or grant rights to OpenAI trademarks. OpenAI does not maintain or endorse this project.
