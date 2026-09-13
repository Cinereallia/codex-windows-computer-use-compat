[English](README.md) | [简体中文](README.zh-CN.md)

# Codex Windows Computer Use 兼容 Skill

> **状态：** 临时、非官方的社区 workaround。本项目与 OpenAI 无隶属或背书关系。

本仓库提供一个很小、仅显式调用的 Codex Skill，处理 Windows 原生应用 Computer Use 请求被错误送入 browser-only `cua_repl` 的情况。它使用当前 Codex 任务已经提供的 `mcp__node_repl__js` 与内置 `@oai/sky` runtime；该路径已经实际验证能够枚举、启动、观察并操作 Windows 应用。

它不会 patch Codex，不安装替代 Computer Use 实现，也不修改系统配置。

## 适用场景

在以下条件下使用：

- Codex Desktop 正运行 Windows Native 任务；
- 官方 Computer Use 插件已安装并启用；
- 浏览器 Computer Use 可用，但原生应用缺失或 `apps` 为空；
- `cua.getApp` 或 `cua.listApps` 不可用；
- 当前任务仍暴露 `mcp__node_repl__js`。

不要将它用于纯浏览器任务、WSL、runtime 安装、系统修复或绕过权限。

## 工作方式

显式调用后，该 Skill：

1. 使用 `mcp__node_repl__js`，不进入 browser-only `cua_repl`；
2. 导入并复用内置 `@oai/sky` client；
3. 要求目标为 Windows，并重新枚举应用和窗口；
4. 只操作 native API 实际返回的应用和窗口对象；
5. 使用“观察 → 单次动作 → 再观察”的验证循环。

如果必需工具或 runtime 不可用，它会停止并报告，不创建替代 driver，也不改用 shell 自动化。

## 前置条件

- Codex Desktop 的 Windows Native 任务
- 官方 Computer Use 插件已安装并启用
- Windows 桌面已解锁并可交互
- 当前 Codex 任务暴露 `mcp__node_repl__js`
- 该 trusted REPL 可以导入内置 `@oai/sky`
- 对需要授权的目标应用或动作，由用户完成相应授权

## 安装

在 Codex 中调用内置 Skill Installer，并提供 GitHub 目录 URL：

```text
$skill-installer Install the skill from this GitHub URL:
https://github.com/Cinereallia/codex-windows-computer-use-compat/tree/main/skills/codex-windows-computer-use-compat
```

安装器会把指定的 Skill 子目录复制到当前 Codex 用户 Skill 位置，并在下一轮提供该 Skill；若未检测到，再重启 Codex。

## 使用

该 Skill 有意设置为仅显式调用：

```text
$codex-windows-computer-use-compat Open Notepad, type TEST, and verify that TEST is visibly present. Do not save the file.
```

安装 Skill 不等于预先授权后续 Computer Use 操作；宿主的正常审批和确认规则仍然有效。

## 最小 Notepad 回归测试

只有实际观察到以下全部结果才算通过：

1. 通过 `mcp__node_repl__js` 加载 `@oai/sky`。
2. `sky.target` 为 `windows`。
3. 原生应用/窗口枚举成功。
4. 通过 native Computer Use API 启动 Notepad。
5. 唯一选择并激活 API 返回的 Notepad 窗口。
6. 在可编辑区域输入 `TEST`。
7. 新的窗口状态或截图明确显示 Notepad 中存在 `TEST`。

打开终端、使用 PowerShell/cmd/shell，或仅报告“已经尝试”均不算通过。测试文档不应保存。

## 已验证版本快照

这些是实际观察到的快照，不是硬编码的版本要求。

| 验证级别 | Windows app build | Computer Use plugin | 内置 runtime | 结果 |
| --- | --- | --- | --- | --- |
| 完整 Notepad `TEST` E2E，2026-09-12 | `26.903.9818.0` | `26.903.71938` | cua-node `0.0.11`、Node `24.20.0`、`@oai/sky 0.6.26`、`@oai/cua 0.2.4` | 通过 |
| 独立完整 Notepad `TEST` E2E，2026-09-12 | `26.908.4834.0` | `26.908.40834` | cua-node `0.0.11`、Node `24.20.0`、`@oai/sky 0.6.32`、`@oai/cua 0.2.4`、`@oai/cua-repl 0.1.0` | legacy 路径通过；unified CUA 仍为 browser-only |

## 已知限制

- 这是兼容路径，不是上游修复。
- 它依赖 Codex 内置但未来可能变化或移除的工具和 package。
- 不自动检测版本，也不提供 fallback 框架。
- `mcp__node_repl__js` 或 trusted `@oai/sky` 不可用时无法工作。
- 不会让 unified `cua` facade 出现 native methods。
- 应用审批、管理员策略、UAC、安全桌面、认证和 Computer Use 安全规则仍然有效。
- Windows Computer Use 运行在前台活动桌面。
- 浏览器应用应使用受支持的 browser surface。

## 卸载

只删除 Skill Installer 报告位置中的 `codex-windows-computer-use-compat` Skill 目录；若 Codex 仍显示该 Skill，再重启 Codex。

不要为了卸载本 Skill 而删除官方 Computer Use 插件、Codex runtime、缓存、会话或用户配置。本项目不会创建需要回滚的配置 override。

## 弃用策略

未来 Codex stable 更新后：

1. 完整退出并重新启动 Codex；
2. 在不调用本 Skill 的 fresh Windows Native 任务中使用默认 Computer Use；
3. 重新执行应用枚举、启动 Notepad、输入 `TEST` 并通过新观察确认；
4. 默认路径通过后，停止使用并卸载本 Skill。

维护者随后应在两份 README 顶部标记 Deprecated，注明已验证恢复的 build，发布最终弃用 release，并 archive 仓库。仓库应保留，以便查阅历史与卸载说明。

## Upstream issues 与 prior art

官方背景：[OpenAI Computer Use 文档](https://learn.chatgpt.com/docs/computer-use)与 [OpenAI Skills 文档](https://learn.chatgpt.com/docs/build-skills)。

直接相关的公开报告（最后核对日期：2026-09-12）：

- [openai/codex #43594](https://github.com/openai/codex/issues/43594) — Windows computer surface 被 unified CUA gate 排除
- [openai/codex #43498](https://github.com/openai/codex/issues/43498) — browser-only materialization 与配置重新生成
- [openai/codex #42745](https://github.com/openai/codex/issues/42745) — browser-only CUA 未注册 Sky trusted service
- [openai/codex #42941](https://github.com/openai/codex/issues/42941) — unified facade 未暴露 native Sky methods
- [openai/codex #44393](https://github.com/openai/codex/issues/44393) — Windows Computer Use 已启用但 native enumeration 为空

社区 [codex-router Computer Use Skill](https://github.com/duolahypercho/codex-router/blob/main/skills/codex-computer-use/SKILL.md) 也存在通过 `mcp__node_repl__js` 使用 `@oai/sky` 的先例，主要面向 custom-model session。本项目围绕 Windows browser-only routing 与真实 Notepad E2E 独立编写，不宣称最早或唯一采用该路径。

[codex-computer-use-windows](https://github.com/ezpzai/codex-computer-use-windows) 与 [codex-computer-use](https://github.com/wangye520/codex-computer-use) 属于独立 executor 或移植项目，不是本 Skill 的依赖；本仓库也不复用其实现。

## 来源与许可证

本仓库文字和 Skill 指令根据本机只读调查、Windows 端到端验证、OpenAI 官方文档与上述公开链接原创编写。

本仓库不包含或重新分发：

- OpenAI 内置 Computer Use Skill；
- `@oai/sky`、`@oai/cua` 或其他 runtime package；
- `codex-computer-use.exe`、Codex binary、MSIX/ASAR 或 plugin cache；
- 本机日志、配置、应用清单、截图、session 数据或 credential；
- 从 prior-art 仓库复制的文件。

本仓库原创内容使用 [MIT License](LICENSE)。该许可不适用于 OpenAI 产品，也不授予 OpenAI 商标权。产品名称只用于描述兼容性；OpenAI 不维护或背书本项目。
