# claude-helper

Claude Code 中文指令速查与意图匹配助手。

## 项目结构

```
claude_helper/
├── CLAUDE.md              # 项目说明
├── .claude/               # Claude Code 配置
│   └── settings.local.json  # 本地权限配置（不追踪）
├── docs/                  # 设计文档
│   └── claude-helper-design.md
└── log/                   # 实施日志
```

## Skill 安装位置

standalone skill 文件位于 `~/.claude/skills/claude-helper/SKILL.md`，不在本项目目录内。

## 使用方式

在 Claude Code 中输入：
- `/claude-helper` — 显示中文速查表
- `/claude-helper 我想保全对话` — 推荐最相关指令
