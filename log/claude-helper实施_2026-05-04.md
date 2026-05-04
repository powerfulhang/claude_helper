# claude-helper Standalone Skill 实施日志

日期：2026-05-04
操作人：Claude Code (automated)
任务：按照设计文档在本机创建 claude-helper standalone skill

---

## 一、技术验证结果

### 1.1 Skill 路径与结构

| 项目 | 设计文档 | 官方文档验证 | 结论 |
|------|---------|-------------|------|
| Personal skill 路径 | `~/.claude/skills/claude-helper/SKILL.md` | 官方确认 | 一致 |
| `$ARGUMENTS` 占位符 | 使用 `$ARGUMENTS` 判断空/非空 | 官方确认支持 | 一致 |
| `disable-model-invocation: true` | 阻止自动加载 | 官方确认 | 一致 |
| `argument-hint` 字段 | 补全时显示提示 | 官方确认 | 一致 |
| 斜杠命令名 = 目录名 | `/claude-helper` | 官方确认 | 一致 |

来源：https://code.claude.com/docs/en/skills

### 1.2 意图映射修正

设计文档中部分命令经过与官方 Commands Reference 对比后进行了修正：

| 设计文档命令 | 官方文档状态 | 处理 |
|-------------|-------------|------|
| `/compact [instructions]` | 已确认 | 保留 |
| `/clear` | 已确认 | 保留 |
| `/resume` | 已确认（别名 `/continue`） | 保留，补充别名 |
| `/rewind` | 已确认（别名 `/undo`） | 保留，补充别名 |
| `/context` | **未找到** | 移除，无等价替代 |
| `/cost` | 已确认（`/usage` 别名） | 保留 |
| `/usage` | 已确认 | 保留 |
| `/permissions` | 已确认（别名 `/allowed-tools`） | 保留 |
| `/config` | **未在命令列表中找到** | 改为 `/status` |
| `/memory` | 已确认 | 保留 |
| `/add-dir` | **未在命令列表中找到** | 移除 |
| `/mcp` | 已确认 | 保留 |
| `/agents` | **未在命令列表中找到** | 改为 `/skills` |
| `/review` | 已确认 | 保留 |
| `/help` | 已确认 | 保留 |

来源：https://code.claude.com/docs/en/commands

### 1.3 新增的官方命令

设计文档未覆盖但官方存在的常用命令，已补充到速查表：

| 命令 | 作用 |
|------|------|
| `/model [model]` | 选择或切换 AI 模型 |
| `/recap` | 生成当前会话的一句话摘要 |
| `/init` | 初始化项目的 CLAUDE.md |
| `/status` | 打开设置界面 |
| `/security-review` | 分析安全漏洞 |
| `/simplify [focus]` | 审查代码质量和效率 |
| `/plugin` | 管理插件 |
| `/skills` | 列出可用 skills |
| `/hooks` | 查看 hook 配置 |
| `/plan [description]` | 进入计划模式 |
| `/theme` | 更换颜色主题 |
| `/voice` | 切换语音输入 |

---

## 二、创建的文件

### 2.1 `~/.claude/skills/claude-helper/SKILL.md`

- 文件大小：3,677 bytes
- 编码：UTF-8
- Frontmatter 字段：`name`、`description`、`argument-hint`、`disable-model-invocation`
- 内容结构：
  - 无参数模式：中文速查表，按 7 个分组（会话与上下文、模型与消耗、项目记忆、权限与配置、代码审查与回滚、MCP/插件/技能、其他常用）
  - 有参数模式：13 条意图映射，输出 1-3 个推荐指令 + 中文说明 + 示例
  - 兜底策略：建议用户运行 `/help` 查看完整命令列表

---

## 三、与设计文档的差异汇总

| 差异项 | 设计文档 | 实施结果 | 原因 |
|--------|---------|---------|------|
| `/context` 命令 | 存在 | 移除 | 官方命令列表中无此命令 |
| `/add-dir` 命令 | 存在 | 移除 | 官方命令列表中无此命令 |
| `/config` 命令 | 存在 | 改为 `/status` | `/config` 未在命令列表中出现，`/status` 打开设置界面 |
| `/agents` 命令 | 存在 | 改为 `/skills` | `/agents` 未在命令列表中出现，`/skills` 列出可用技能 |
| 别名信息 | 未提及 | 补充 | 官方文档列出了多个别名（`/continue`、`/undo`、`/cost` 等） |
| 速查表分组 | 6 组 | 7 组 | 新增"其他常用"组，覆盖 `/plan`、`/theme`、`/voice` 等 |
| 意图映射数量 | 11 条 | 13 条 | 新增"安全检查"→`/security-review`、"简化代码"→`/simplify` 映射 |

---

## 四、验证状态

- [x] SKILL.md 文件已创建
- [x] 文件路径正确：`~/.claude/skills/claude-helper/SKILL.md`
- [x] Frontmatter 格式正确
- [x] `$ARGUMENTS` 占位符已使用
- [ ] Skill 自动发现（需在新会话或 `/skills` 命令中验证）

**注意**：根据官方文档，Claude Code 会监视 skill 目录的文件变更，新增 skill 应在当前会话内自动生效。但由于 skill 列表在会话开始时加载，`/claude-helper` 可能需要在下一次会话中才能通过 `/` 菜单看到。

---

## 五、后续步骤

1. 在新会话中输入 `/claude-helper` 验证无参数模式
2. 输入 `/claude-helper 我想保全对话` 验证有参数模式
3. 内容稳定后，按设计文档的 Plugin 包装方案转换为插件
