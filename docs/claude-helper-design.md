# claude-helper 设计汇总

日期：2026-05-04

## 目标

`claude-helper` 的目标是在 Claude Code 中提供一个中文指令速查与意图匹配助手：

- 输入 `/claude-helper` 且不带参数时，输出常见 Claude Code 指令及中文说明。
- 输入 `/claude-helper 我想保全对话` 这类自然语言需求时，只输出最相关的 Claude Code 指令、用途说明和示例。
- 支持 Claude Code 的斜杠命令补全提示，尽量保持短命令名 `/claude-helper`。

## 推荐形态

优先做成 standalone Skill，而不是一开始就做成 Plugin。

建议路径：

```text
~/.claude/skills/claude-helper/SKILL.md
```

原因：

- 官方文档说明 standalone `.claude/` 配置更适合个人工作流、单项目自定义、快速实验，以及想要短 skill 名称的场景；Plugin 更适合团队或社区分发、版本化发布、复用到多个项目，并且通常会以 `/plugin-name:skill-name` 的形式命名空间化。来源：Claude Code Docs, "Create plugins", "When to use plugins vs standalone configuration"。
- 官方文档说明 Claude Code 新插件推荐使用 `skills/`，`commands/` 是 legacy 格式但仍兼容。来源：Claude Code Docs, "Plugins in the SDK", "What are plugins?"。
- Claude Help Center 的 cheatsheet 说明 Skill 可以像斜杠命令一样调用，也可以在相关上下文中自动加载；Command 是任何以 `/` 开头的输入，自定义项可由 `.claude/skills/<name>/SKILL.md` 定义。来源：Claude Help Center, "Claude Code cheatsheet"。

因此，`claude-helper` 第一版适合用 Skill 实现。等内容稳定、需要分享给团队或发布到 marketplace 时，再把同一个 Skill 包装成 Plugin。

## Plugin 与 Skill 的取舍

| 方案 | 适合场景 | 对 claude-helper 的影响 |
| --- | --- | --- |
| Standalone Skill | 自用、快速迭代、想保留短命令名 | 推荐。可以直接得到 `/claude-helper` |
| Plugin 内置 Skill | 团队分发、版本化、marketplace、跨项目统一安装 | 后续推荐。默认调用名可能是 `/claude-helper:claude-helper` 或类似命名空间形式 |
| Legacy command markdown | 简单提示模板、兼容旧项目 | 不作为首选，因为官方新文档更推荐 `skills/` |
| MCP prompt | 动态发现、需要外部服务或工具能力 | 当前需求不需要 MCP |

## 用户体验设计

### 无参数模式

输入：

```text
/claude-helper
```

输出一份中文速查表，按用途分组：

- 会话与上下文
- 模型与消耗
- 项目记忆
- 权限与配置
- 代码审查与回滚
- MCP、插件、子代理

输出应短而可扫读，不要变成完整手册。

### 有参数模式

输入：

```text
/claude-helper 我想保全对话
```

输出：

```text
推荐：/compact [instructions]

作用：压缩当前对话，释放上下文，同时可通过 instructions 指定需要保留的重点。
示例：/compact 请保留当前目标、关键决策、已修改文件、待办事项和风险。
```

有参数时只推荐 1-3 个最相关指令，不输出完整速查表。

## 初版意图映射

| 用户意图 | 推荐指令 | 说明 |
| --- | --- | --- |
| 保全对话、保存上下文、快到上下文上限 | `/compact [instructions]` | 压缩当前对话，可附带保留重点 |
| 清空重新开始 | `/clear` | 清除当前会话历史，项目记忆仍保留 |
| 恢复之前会话 | `/resume` | 恢复已有 session |
| 回到之前状态、撤回改动 | `/rewind` | 回退对话和/或代码到更早检查点 |
| 看还剩多少上下文 | `/context` | 查看当前上下文窗口加载情况 |
| 看 token、花费、消耗 | `/cost` | 查看当前 session 的 token 使用和花费 |
| 看套餐用量或限额 | `/usage` | 查看计划用量和 rate limit 状态 |
| 修改权限 | `/permissions` | 查看或调整工具权限 |
| 配置 Claude Code | `/config` | 打开配置界面 |
| 管理项目记忆、CLAUDE.md | `/memory` | 查看或编辑 memory 文件 |
| 给 Claude 增加可访问目录 | `/add-dir` | 授权额外工作目录 |
| 管理 MCP | `/mcp` | 查看和管理 MCP server 连接 |
| 管理子代理 | `/agents` | 配置或查看 subagents |
| 请求代码审查 | `/review` | 让 Claude 做 code review |
| 看所有命令 | `/help` | 查看当前可用命令 |

注：命令名称和可用性会随 Claude Code 版本、平台、安装方式和计划而变化；以当前本机 `/help` 输出为最终准。

## 建议的 SKILL.md

```markdown
---
description: 中文 Claude Code 指令速查与意图匹配助手
argument-hint: "[你想做什么，例如：我想保全对话 / 我想回滚 / 我想看消耗]"
disable-model-invocation: true
---

# Claude Helper

你是 Claude Code 中文指令速查助手。

如果 `$ARGUMENTS` 为空：
输出一份精简中文速查表，按用途分组列出常见 Claude Code 指令及作用：
- 会话与上下文
- 模型与消耗
- 项目记忆
- 权限与配置
- 代码审查与回滚
- MCP、插件、子代理

如果 `$ARGUMENTS` 不为空：
只输出与用户需求最相关的 1-3 个 Claude Code 指令。
先给推荐指令，再用中文说明为什么适合。
不要展开完整速查表。

特别映射：
- “保全对话 / 保存上下文 / 快到上下文上限” -> 推荐 `/compact [instructions]`
- “清空重新开始” -> 推荐 `/clear`
- “恢复之前会话” -> 推荐 `/resume`
- “回到之前状态 / 撤回改动” -> 推荐 `/rewind`
- “看还剩多少上下文” -> 推荐 `/context`
- “看消耗 / token / 花费” -> 推荐 `/cost` 或 `/usage`
- “改权限” -> 推荐 `/permissions`
- “配置 Claude Code” -> 推荐 `/config`
- “管理记忆 / CLAUDE.md” -> 推荐 `/memory`
- “看有哪些命令” -> 推荐 `/help`

用户输入：$ARGUMENTS
```

## 后续 Plugin 包装方案

当需要把 `claude-helper` 分发给团队或社区时，可以转换为插件：

```text
claude-helper/
├── .claude-plugin/
│   └── plugin.json
├── skills/
│   └── claude-helper/
│       └── SKILL.md
└── README.md
```

`plugin.json` 示例：

```json
{
  "name": "claude-helper",
  "description": "Chinese Claude Code command helper and intent-based command recommender",
  "version": "0.1.0",
  "author": {
    "name": "HangShi"
  }
}
```

本地测试方式可参考官方文档：

```text
claude --plugin-dir ./claude-helper
```

根据官方文档，开发中可用 `/reload-plugins` 重新加载 plugin、skills、agents、hooks 等组件。

## 类似项目调研

没有找到明确同名、同定位的 `claude-helper` 项目。[UNVERIFIED] 这不是全网穷尽结论，只表示当前检索结果中未发现。

找到的相近项目包括：

- `slash-command-factory`：用于通过问答生成 Claude Code slash commands，输出生产可用的 command 文件。
- `slash-commands`：提供创建 Claude Code slash commands 的参考框架、内置命令列表和示例。

这些项目更像“slash command/skill 生成器或教程”，不是“中文 Claude Code 指令意图匹配助手”。因此 `claude-helper` 仍有清晰差异化：中文、面向日常指令选择、以自然语言需求映射到命令。

## 来源

- Claude Code Docs: Create plugins, "When to use plugins vs standalone configuration"  
  https://code.claude.com/docs/en/plugins
- Claude Code Docs: Plugins in the SDK, "What are plugins?", "Plugin structure reference", "Using plugin skills"  
  https://code.claude.com/docs/en/agent-sdk/plugins
- Claude Help Center: Claude Code cheatsheet, "Commands"  
  https://support.claude.com/en/articles/14553413-claude-code-cheatsheet
- MCP Hub: slash-command-factory  
  https://www.aimcp.info/en/skills/0e792838-588f-4996-b11d-f49148fdd093
- MCP Hub: slash-commands  
  https://www.aimcp.info/en/skills/18ec98fa-8006-492e-94b9-ac0454aaabdc

