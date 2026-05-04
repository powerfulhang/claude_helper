# claude-helper

**中文 Claude Code 指令速查与意图匹配助手**

A Chinese-language command helper and intent-based recommender for Claude Code.

---

## 功能 / Features

- 输入 `/claude-helper`，输出中文速查表，按用途分组列出常见 Claude Code 指令
- 输入 `/claude-helper 我想保全对话`，只推荐 1-3 个最相关指令并附中文说明
- 支持 Claude Code 斜杠命令补全提示

Type `/claude-helper` to get a Chinese quick-reference table grouped by use case.
Type `/claude-helper <intent>` to get 1–3 recommended commands with Chinese explanations.

## 安装 / Installation

将 `SKILL.md` 复制到 Claude Code 的 skill 目录：

```bash
mkdir -p ~/.claude/skills/claude-helper
cp SKILL.md ~/.claude/skills/claude-helper/SKILL.md
```

Copy the `SKILL.md` file to your Claude Code skills directory:

```bash
mkdir -p ~/.claude/skills/claude-helper
cp SKILL.md ~/.claude/skills/claude-helper/SKILL.md
```

## 使用 / Usage

### 无参数模式 / Without arguments

```
/claude-helper
```

输出中文速查表，涵盖会话管理、消耗查看、权限配置、代码审查等常见指令。

Displays a Chinese quick-reference table covering session management, usage tracking, permissions, code review, and more.

### 有参数模式 / With arguments

```
/claude-helper 我想保全对话
```

输出：

```
推荐：/compact [instructions]

作用：压缩当前对话，释放上下文，可通过 instructions 指定需要保留的重点。
示例：/compact 请保留当前目标、关键决策、已修改文件、待办事项和风险。
```

Recommends the most relevant command(s) with a Chinese explanation and example.

## 要求 / Requirements

- Claude Code CLI 或桌面应用
- Claude Code CLI or desktop app

## 许可 / License

MIT
