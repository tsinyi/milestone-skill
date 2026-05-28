---
name: milestone
description: Use ONLY when user types /milestone. Immediately begin the interactive flow — no greeting, no preamble, just execute Step 1.
---

# /milestone

When invoked, immediately start. Do not explain. Do not preamble. Just execute.

## File Management (Before Step 1)

Each milestone is stored as a separate `.md` file in `milestones/`, named `YYYY-MM-DD-slug.md`.

Check if `milestones/` has any `.md` files. Parse each file's title from the `## [date] title` line.
- Existing file → Edit Mode: walk Steps 1-7 with current values. When user selects "修改" for a text field, **first display the full current content**, then prompt for new input.
- "+ 新建记录" → proceed to Step 1
- "删除记录" → show second AskUserQuestion listing entries. On confirm, `rm` the file directly.
- Entries limited to 3 per screen (4th slot for actions). No explicit "取消" — user can Esc.
- No files → proceed to Step 1

## Step 1: Category — AskUserQuestion

```json
{
  "questions": [{
    "question": "记录什么类别的里程碑？",
    "header": "类别",
    "multiSelect": false,
    "options": [
      {"label": "问题解决", "description": "解决了一个非平凡的 bug、崩溃或阻塞问题"},
      {"label": "方案提出", "description": "提出了一个有价值的架构、模式或方法"},
      {"label": "关键决策", "description": "做出了一个有长期影响的取舍选择"},
      {"label": "其他...", "description": "更多类别选项"}
    ]
  }]
}
```
If user selects "其他...", immediately show a second AskUserQuestion with the remaining options:
```json
{
  "questions": [{
    "question": "选择类别：",
    "header": "类别",
    "multiSelect": false,
    "options": [
      {"label": "重要事件", "description": "工具切换、发现等值得记录的事"},
      {"label": "取消", "description": "退出，不记录"}
    ]
  }]
}
```

## Step 2: Title

First AskUserQuestion:
```json
{
  "questions": [{
    "question": "如何填写标题？",
    "header": "标题",
    "multiSelect": false,
    "options": [
      {"label": "自己输入", "description": "手动输入一句话标题"},
      {"label": "AI 辅助生成", "description": "由 Claude Code 根据会话上下文自动生成"},
      {"label": "返回上一步", "description": "回到类别选择"}
    ]
  }]
}
```
- 自己输入 → text prompt: "一句话标题？"
- AI 辅助生成 → generate title from context, show, AskUserQuestion "确认使用" or "自行修改"

## Step 3: Actor — AskUserQuestion

Adapt question text to category:
- 问题解决 → "谁解决了这个问题？"
- 方案提出 → "谁提出了这个方案？"
- 关键决策 → "谁做出了这个决策？"
- 重要事件 → "谁/什么触发了这个事件？"

Detect agent name and LLM model(s) from runtime context. Fill dynamically:
```json
{
  "questions": [{
    "question": "<adapted-text>",
    "header": "来源",
    "multiSelect": false,
    "options": [
      {"label": "开发者", "description": "由开发者独立完成"},
      {"label": "LLM: <detected-model(s)>", "description": "直接使用大模型回答"},
      {"label": "<detected-agent>", "description": "当前 agent 独立完成"},
      {"label": "其他...", "description": "更多选项（辅助工具 / 返回上一步）"}
    ]
  }]
}
```
If user selects "其他...", show second AskUserQuestion:
```json
{
  "questions": [{
    "question": "<adapted-text>",
    "header": "来源",
    "multiSelect": false,
    "options": [
      {"label": "<agent> + 辅助工具", "description": "agent 借助 skill / plugin / hook 等"},
      {"label": "返回上一步", "description": "回到标题输入"}
    ]
  }]
}
```
Never hardcode agent or model names.

After user selects an actor type from Step 3:
- **开发者** → immediately text prompt: "哪些开发者？输入名称，多人用逗号分隔". Record as `developer: name1, name2`. First AskUserQuestion for 自己输入/AI辅助生成/返回上一步.
- **LLM** → if multiple models, AskUserQuestion to pick. Record as `LLM: model-name`.
- **agent** → record as detected agent name, no follow-up.
- **agent + 辅助工具** → text prompt: "使用了哪些辅助工具？". If user doesn't know, scan `~/.claude/skills/` and settings.json. Record as `<agent> + 辅助工具 (name1, name2)`.

## Step 4: Domain | 领域

Same pattern:
```json
{
  "questions": [{
    "question": "如何填写领域？",
    "header": "领域",
    "multiSelect": false,
    "options": [
      {"label": "自己输入", "description": "手动输入领域，逗号分隔"},
      {"label": "AI 辅助生成", "description": "由 Claude Code 根据上下文建议"},
      {"label": "跳过", "description": "不添加领域"}, {"label": "返回上一步", "description": "回到上一步"}
    ]
  }]
}
```
- 自己输入 → text prompt: "领域？（逗号分隔）" → convert to lowercase, trim
- AI 辅助生成 → generate **max 3** domain suggestions from context, present via AskUserQuestion (multiSelect).
- 跳过 → leave empty

## Step 5: Context

First AskUserQuestion:
```json
{
  "questions": [{
    "question": "如何填写背景？",
    "header": "背景",
    "multiSelect": false,
    "options": [
      {"label": "自己输入", "description": "手动输入背景说明"},
      {"label": "AI 辅助生成", "description": "由 Claude Code 根据上下文生成"},
      {"label": "跳过", "description": "不填写背景"}, {"label": "返回上一步", "description": "回到上一步"}
    ]
  }]
}
```
- 自己输入 → text prompt: "一两句话背景？为什么这是个里程碑？"
- AI 辅助生成 → generate, show, confirm/edit
- 跳过 → leave empty

## Step 6: Details

Same pattern:
```json
{
  "questions": [{
    "question": "如何填写详情？",
    "header": "详情",
    "multiSelect": false,
    "options": [
      {"label": "自己输入", "description": "手动输入详细内容"},
      {"label": "AI 辅助生成", "description": "由 Claude Code 根据上下文生成"},
      {"label": "跳过", "description": "不填写详情"}, {"label": "返回上一步", "description": "回到上一步"}
    ]
  }]
}
```
- 自己输入 → text prompt: "详细内容？（多段直接输入）"
- AI 辅助生成 → generate, show, confirm/edit
- 跳过 → leave empty

## Step 7: Supplement — Text Prompt

First AskUserQuestion:
```json
{
  "questions": [{
    "question": "需要补充信息吗？",
    "header": "补充",
    "multiSelect": false,
    "options": [
      {"label": "自己输入", "description": "手动输入补充内容"},
      {"label": "AI 辅助生成", "description": "由 Claude Code 根据上下文生成"},
      {"label": "跳过", "description": "不需要补充"},
      {"label": "返回上一步", "description": "回到上一步"}
    ]
  }]
}
```
- 自己输入 → text prompt: "补充内容？"
- AI 辅助生成 → generate supplement from context, show, confirm/edit
- 跳过 → leave empty

## Step 8: Confirm — AskUserQuestion

Show the entry preview as markdown. Use AskUserQuestion:
```json
{
  "questions": [{
    "question": "确认保存到 milestones/YYYY-MM-DD-slug.md？",
    "header": "确认",
    "multiSelect": false,
    "options": [
      {"label": "保存", "description": "追加写入文件"},
      {"label": "修改", "description": "返回修改"}
    ]
  }]
}
```
Include the formatted entry as `preview` on "保存".

## Entry Format

```markdown
## [YYYY-MM-DD] title

| 类别 | category |
|------|----------|
| 来源 | actor |
| 领域 | domain |
| 时间 | YYYY-MM-DD HH:MM |

### 背景
context

### 详情
details

### 补充
supplement
---
```

## Navigation

- AskUserQuestion max 4 options. If >4, use "其他..." first screen, show rest on second.
- "返回上一步" appears where it fits (or in "其他..." submenu). Step 1 uses "取消" instead.
- Every text input field is prefixed with an AskUserQuestion offering "自己输入" / "AI 辅助生成"
- When AI assist generates content, always confirm with AskUserQuestion before accepting

## Rules

- Never skip any step
- Agent/model names always detected from runtime, never hardcoded
- Save to `milestones/YYYY-MM-DD-slug.md` in current project
- No auto-fill without user choosing AI assist via AskUserQuestion option
- AI-generated suggestions for AskUserQuestion must not exceed 3 items (leaving 4th slot for "其他..." / navigation). If more ideas exist, pick top 3.
