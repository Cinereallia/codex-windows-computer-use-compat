[English](README.md) | [简体中文](README.zh-CN.md)

# Codex Windows Computer Use Compatibility

> **Status:** Temporary, unofficial community workaround. This project is not affiliated with or endorsed by OpenAI.

This repository contains a small, explicit-only Codex Skill for Windows native-app Computer Use tasks that are incorrectly routed to a browser-only `cua_repl` surface. The Skill uses the host-provided `mcp__node_repl__js` tool and bundled `@oai/sky` runtime that have been verified to enumerate, launch, observe, and operate Windows applications.

It does not patch Codex, install a replacement Computer Use implementation, or change system configuration.

## When to use it

Use this Skill when:

- Codex Desktop is running in Windows Native mode;
- the official Computer Use plugin is installed and enabled;
- browser Computer Use is available, but native apps are missing or `apps` is empty;
- `cua.getApp` or `cua.listApps` is unavailable; and
- the native `mcp__node_repl__js` tool is still available.

Do not use it for browser-only work, WSL sessions, runtime installation, system repair, or as a way to bypass permissions.

## What it does

When explicitly invoked, the Skill:

1. uses `mcp__node_repl__js`, never the browser-only `cua_repl` path;
2. imports and reuses the bundled `@oai/sky` client;
3. requires a Windows target and fresh app/window enumeration;
4. operates only on app and window objects returned by the native API; and
5. follows an observe → act once → observe verification loop.

If the required tool or runtime is unavailable, it stops and reports the limitation. It does not create a replacement driver or switch to shell automation.

## Requirements

- Codex Desktop running a Windows Native task
- Official Computer Use plugin installed and enabled
- An unlocked, interactive Windows desktop
- `mcp__node_repl__js` exposed by the current Codex task
- The bundled `@oai/sky` package available to that trusted REPL
- User approval for any target app or action that requires it

## Installation

In Codex, invoke the built-in Skill Installer with the GitHub directory URL:

```text
$skill-installer Install the skill from this GitHub URL:
https://github.com/Cinereallia/codex-windows-computer-use-compat/tree/main/skills/codex-windows-computer-use-compat
```

The installer copies the selected Skill directory to the current Codex user Skill location and makes it available on the next turn. If it is not detected, restart Codex.

## Usage

This Skill is intentionally explicit-only:

```text
$codex-windows-computer-use-compat Open Notepad, type TEST, and verify that TEST is visibly present. Do not save the file.
```

Installing the Skill does not grant permission for later Computer Use actions. Normal host approvals and confirmation rules still apply.

## Minimal Notepad regression test

The regression passes only when all of the following are observed:

1. `@oai/sky` loads through `mcp__node_repl__js`.
2. `sky.target` is `windows`.
3. Native app/window enumeration succeeds.
4. Notepad is launched through the native Computer Use API.
5. The returned Notepad window is uniquely selected and activated.
6. `TEST` is entered into its editable surface.
7. A fresh state or screenshot visibly confirms `TEST` in Notepad.

Opening a terminal, using PowerShell/cmd/shell, or merely reporting that an action was attempted does not pass this test. The test document should remain unsaved.

## Verified snapshots

These are observed snapshots, not hard version requirements.

| Verification | Windows app build | Computer Use plugin | Bundled runtime | Result |
| --- | --- | --- | --- | --- |
| Full Notepad `TEST` E2E, 2026-09-12 | `26.903.9818.0` | `26.903.71938` | cua-node `0.0.11`, Node `24.20.0`, `@oai/sky 0.6.26`, `@oai/cua 0.2.4` | Passed |
| Independent full Notepad `TEST` E2E, 2026-09-12 | `26.908.4834.0` | `26.908.40834` | cua-node `0.0.11`, Node `24.20.0`, `@oai/sky 0.6.32`, `@oai/cua 0.2.4`, `@oai/cua-repl 0.1.0` | Passed through the legacy route; unified CUA remained browser-only |

## Known limitations

- This is a compatibility route, not an upstream fix.
- It relies on a bundled tool and package that may change or disappear in a future Codex build.
- It performs no automatic version detection and has no fallback framework.
- It cannot help when `mcp__node_repl__js` or trusted `@oai/sky` access is unavailable.
- It does not make native methods appear on the unified `cua` facade.
- App approvals, administrator policy, UAC, secure desktop, authentication, and Computer Use safety rules remain in force.
- Windows Computer Use runs on the active foreground desktop.
- Browser applications should use the supported browser surface instead.

## Uninstall

Remove only the installed `codex-windows-computer-use-compat` Skill directory from the location reported by Skill Installer, then restart Codex if it still appears.

Do not remove the official Computer Use plugin, Codex runtime, caches, sessions, or user configuration when uninstalling this Skill. This project does not create a configuration override that needs rollback.

## Deprecation policy

After a future stable Codex update:

1. fully restart Codex;
2. start a fresh Windows Native task without invoking this Skill;
3. use the default Computer Use route to enumerate apps, open Notepad, type `TEST`, and verify it in a fresh observation; and
4. if that succeeds, stop using and uninstall this Skill.

The maintainers should then mark both READMEs as deprecated, identify the verified fixed build, publish a final deprecation release, and archive the repository. The repository should remain available for history and uninstall guidance.

## Upstream issues and prior art

Official background: [OpenAI Computer Use documentation](https://learn.chatgpt.com/docs/computer-use) and [OpenAI Skills documentation](https://learn.chatgpt.com/docs/build-skills).

Relevant public reports, last checked 2026-09-12:

- [openai/codex #43594](https://github.com/openai/codex/issues/43594) — Windows computer surface gated out of unified CUA
- [openai/codex #43498](https://github.com/openai/codex/issues/43498) — browser-only materialization and regenerated configuration
- [openai/codex #42745](https://github.com/openai/codex/issues/42745) — Sky trusted service omitted from browser-only CUA
- [openai/codex #42941](https://github.com/openai/codex/issues/42941) — unified facade omits native Sky methods
- [openai/codex #44393](https://github.com/openai/codex/issues/44393) — enabled Windows Computer Use with empty native enumeration

Direct use of `mcp__node_repl__js` with `@oai/sky` also appears in the community [codex-router Computer Use Skill](https://github.com/duolahypercho/codex-router/blob/main/skills/codex-computer-use/SKILL.md), primarily for custom-model sessions. This project was written independently around the Windows browser-only routing failure and a real Notepad E2E test; it does not claim to be the first or only use of that route.

[codex-computer-use-windows](https://github.com/ezpzai/codex-computer-use-windows) and [codex-computer-use](https://github.com/wangye520/codex-computer-use) are separate executor or porting projects. They are not dependencies of this Skill, and this repository does not reuse their implementations.

## Provenance and license

The prose and Skill instructions in this repository were written from a local, read-only investigation, an end-to-end Windows validation, official OpenAI documentation, and the public links above.

This repository does not contain or redistribute:

- OpenAI's bundled Computer Use Skill;
- `@oai/sky`, `@oai/cua`, or other runtime packages;
- `codex-computer-use.exe`, Codex binaries, MSIX/ASAR files, or plugin caches;
- local logs, configuration, app lists, screenshots, session data, or credentials; or
- files copied from prior-art repositories.

The original contents of this repository are licensed under the [MIT License](LICENSE). That license does not apply to OpenAI products or grant rights to OpenAI trademarks. Product names are used only to describe compatibility. OpenAI does not maintain or endorse this project.
