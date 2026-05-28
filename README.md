# milestone

A Claude Code skill for recording developer milestones into a structured Markdown knowledge base.

一个 Claude Code Skill，用于结构化记录开发过程中的重要节点，形成可检索的 Markdown 知识库。

---

## English

### Features

- Native AskUserQuestion arrow-key menus
- AI-assisted content generation (title, context, details, tags)
- Source attribution — who contributed (developer / LLM / agent / agent+tools)

### 8-Step Flow

1. **Category** — problem solved / solution proposed / key decision / notable event
2. **Title** — one-line summary with AI assist option
3. **Source** — who did this? Developer / LLM / Agent / Agent+Tools (with inline follow-up)
4. **Domain** — which field this applies to, AI-suggested (optional)
5. **Context** — why this matters, what was at stake
6. **Details** — multi-paragraph description (optional)
7. **Supplement** — additional notes or follow-up info (optional)
8. **Confirm** — preview and save to `milestones/YYYY-MM-DD-slug.md`

### Install

```bash
mkdir -p ~/.claude/skills/milestone
curl -o ~/.claude/skills/milestone/SKILL.md \
  https://raw.githubusercontent.com/tsinyi/milestone-skill/main/SKILL.md
```

Restart Claude Code or `/clear`. Then type `/milestone`.

---

## 中文

### 功能

- 原生 AskUserQuestion 方向键交互菜单
- AI 辅助生成内容（标题、背景、详情、标签）
- 来源归因 — 记录谁完成了这件事（开发者 / LLM / Agent / Agent+工具）

### 8 步流程

1. **类别** — 问题解决 / 方案提出 / 关键决策 / 重要事件
2. **标题** — 一句话概括，支持 AI 辅助生成
3. **来源** — 谁做的？开发者 / LLM / Agent / Agent+工具（追问内联在选项后）
4. **领域** — 适用领域，AI 可建议（可选）
5. **背景** — 为什么这是里程碑，什么值得记录
6. **详情** — 多段描述（可选）
7. **补充** — 额外说明或后续跟进信息（可选）
8. **确认** — 预览后保存至 `milestones/YYYY-MM-DD-slug.md`

### 安装

```bash
mkdir -p ~/.claude/skills/milestone
curl -o ~/.claude/skills/milestone/SKILL.md \
  https://raw.githubusercontent.com/tsinyi/milestone-skill/main/SKILL.md
```

重启 Claude Code 或 `/clear`，然后输入 `/milestone`。

---

## License

MIT
