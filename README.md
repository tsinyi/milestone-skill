# milestone | 里程碑

**EN:** A Claude Code skill for recording developer milestones — problems solved, key decisions, major proposals, and notable events — into a structured, searchable Markdown knowledge base.

**中文：** 一个 Claude Code Skill，用于结构化记录开发过程中的重要节点：解决的问题、做出的决策、提出的方案，形成可检索的 Markdown 知识库。

## Features | 功能

- 8-step guided interactive flow using native AskUserQuestion menus
- AI-assisted content generation (titles, context, details, tags)
- Actor attribution — track who contributed (developer / LLM / agent / agent+tools)
- Edit and delete existing entries
- Stores records in `milestones/MILESTONE.md`

## Install | 安装

```bash
mkdir -p ~/.claude/skills/milestone
curl -o ~/.claude/skills/milestone/SKILL.md \
  https://raw.githubusercontent.com/<your-username>/milestone-skill/main/SKILL.md
```

Restart Claude Code or `/clear`, then type `/milestone`. | 重启 Claude Code 或 `/clear`，然后输入 `/milestone`。

## License | 许可

MIT
