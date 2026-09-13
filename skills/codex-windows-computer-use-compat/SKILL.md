---
name: codex-windows-computer-use-compat
description: Use this temporary compatibility route for Windows-native Codex Desktop app control when explicitly invoked and the unified Computer Use surface is browser-only. Requires the host-provided mcp__node_repl__js tool and bundled @oai/sky; do not use for browser tasks, WSL, or runtime repair.
license: MIT
---

# Windows Computer Use compatibility route

Apply this temporary route only when the user explicitly invokes this Skill or explicitly asks for the legacy Windows Sky Computer Use path.

## Required route

- Use the host-provided `mcp__node_repl__js` tool for every Computer Use call in this workflow. Discover it through the host's normal tool discovery when necessary.
- Do not use any `cua_repl` tool or the unified `cua` facade for Windows native-app actions.
- If `mcp__node_repl__js` is not callable, stop and report: `mcp__node_repl__js is unavailable, so this compatibility Skill cannot run. No fallback was attempted.`
- Do not start a separate REPL, helper executable, service, or pipe client.

In each fresh trusted REPL session, initialize the bundled client once:

```js
globalThis.sky ??= (await import("@oai/sky")).sky;
```

Require `sky.target === "windows"`. If the import fails or the target is not Windows, stop and report the concrete limitation without changing tools.

## Target selection

1. Call `sky.list_apps()` or `sky.list_windows()` immediately before choosing a target.
2. Use only app and window objects returned by the latest enumeration. Never invent IDs, handles, paths, element indexes, coordinates, or screenshot IDs.
3. Continue only after the intended target resolves to exactly one returned window.
4. If an explicitly requested app is absent, `sky.launch_app` may use its exact `.exe` identifier or path. Re-enumerate afterward and select the actual returned window; never keep operating on the launch input.
5. If the target remains missing or ambiguous, stop and report the returned candidates.

## Observe, act once, observe

- Capture fresh window state before every state-derived action.
- Verify the target and focus through current accessibility data or a fresh screenshot; process metadata and a title alone are insufficient for document input.
- Perform one action using only the current observation, then immediately capture fresh state.
- After any state change, carry forward the returned `state.window` and discard older element indexes, coordinates, screenshot IDs, and window bindings.
- Use `type_text` only for literal text and `press_key` for control keys or shortcuts.
- If an action or refresh fails with an unknown outcome, do not repeat it blindly. Re-enumerate and re-observe once; stop if the outcome remains uncertain.
- Claim success only when the post-action observation visibly or accessibly confirms the requested result.

## Boundaries

- Do not use shell, PowerShell, Command Prompt, terminal applications, the Windows Run dialog, SendKeys, or other automation as a substitute for Computer Use.
- Do not modify Codex configuration, generated MCP files, plugin caches, environment variables, registry entries, ACLs, security settings, or runtime files.
- Do not bypass app approval, host confirmation, administrator policy, authentication, UAC, secure desktop, or any other safety boundary.
- This Skill grants no additional permission. Follow the host's current Computer Use approval and confirmation policy for every action.
- Do not use this route for browser-only work; use the supported browser surface instead.
- If the desktop is locked, required approval is unavailable, native enumeration fails, or a trustworthy target cannot be established, stop and report.
