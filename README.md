# milestone

A Claude Code skill for recording developer milestones into a structured Markdown knowledge base.

一个 Claude Code Skill，用于结构化记录开发过程中的重要节点，形成可检索的 Markdown 知识库。

---

## English

### What it does

A structured journal for your development work, built for how AI-assisted development actually happens. When you solve a hard bug with Claude, make a key architectural decision, or propose a significant solution — `/milestone` walks you through 8 steps to capture the full story, with AI filling in the blanks wherever you want it to.

### Why it works

- **AI-native recording** — the skill was designed to be *run by* AI. Every text field offers one-tap AI-assisted generation drawn from your live conversation context. You approve, edit, or write your own. No context-switching, no blank-page friction.
- **Complete record, not a one-liner** — the guided 8-step flow ensures every milestone captures what happened, why it mattered, who contributed, and which domain it belongs to. No more half-finished entries in a scratch file.
- **Know who did what** — every entry carries a contributor label (developer, LLM, agent, or agent + tools). Trace your best decisions back to the human, the model, or the skill that helped make them.
- **Truly searchable** — each milestone is its own Markdown file with domain tags. grep it, browse it, version it with Git. Your knowledge survives projects and time.

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

### 它能做什么

一个为 AI 辅助开发场景设计的结构化日志工具。当你和 Claude 一起解决了一个棘手问题、做出了关键架构决策、或提出了重要方案时，`/milestone` 用 8 个步骤引导你完成记录——AI 随时介入帮你填充内容，你只需确认或修改。

### 为什么用它

- **AI 原生记录** — 这个 skill 被设计为*由 AI 来运行*。每个文本字段都提供一键 AI 辅助生成，内容直接从你的会话上下文中提取。你只需要确认、修改或者自己写。没有上下文切换，没有白纸恐惧。
- **完整记录，而非一句话** — 8 步引导流程确保每条里程碑都包含事件本身、为什么重要、谁完成的、属于哪个领域。不再是草稿文件里半途而废的一句话。
- **谁做了什么，一目了然** — 每条记录标注贡献来源（开发者、LLM、agent、agent+工具）。最好的决策可以追溯到人、模型或者背后的 skill。
- **真正能检索** — 每条里程碑是一个独立的 Markdown 文件，带领域标签。grep、浏览、Git 版本控制，知识库跨项目、跨时间生存。

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
