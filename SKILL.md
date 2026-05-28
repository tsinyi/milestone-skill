---
name: milestone
description: Use ONLY when user types /milestone. Immediately begin the interactive flow — no greeting, no preamble, just execute Step 1.
---

# /milestone

When invoked, immediately start. Do not explain. Do not preamble. Just execute.

## File Management (Before Step 1)

Check if `milestones/MILESTONE.md` exists. If yes and has entries, parse them and present alongside additional actions:
- Existing entry → Edit Mode: walk Steps 1-7 showing "当前值: ..." at each, offer "保留"/"修改". Step 1 also includes "返回" (return to file management). Steps 2-7 include "返回上一步". When user selects "修改", follow the same sub-flow as new-entry mode (自己输入 / AI 辅助生成 / [跳过] / 返回上一步). When entering text input mode, first display the current content for reference, then prompt: "输入新内容（直接输入覆盖原内容，输入 .. 返回）".
- "+ 新建记录" → proceed to Step 1
- "删除记录" → show a second AskUserQuestion listing all entries (with "取消" option to go back). User picks one, confirm with AskUserQuestion (preview + "确认删除"/"取消"). On "确认删除", remove that entry from the file (the `## [date] title` block through its `---` separator).
- Entries limited to 3 per screen (4th slot for actions). If >3 entries, use "更多..." overflow. No explicit "取消" — user can Esc.
- No file / empty → proceed to Step 1

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
    "header": "主体",
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
    "header": "主体",
    "multiSelect": false,
    "options": [
      {"label": "<agent> + 辅助工具", "description": "agent 借助 skill / plugin / hook 等"},
      {"label": "返回上一步", "description": "回到标题输入"}
    ]
  }]
}
```
Never hardcode agent or model names.

## Step 4: Actor Follow-up

Based on Step 3 choice. Each follow-up is a text prompt — but FIRST ask via AskUserQuestion whether user wants AI assist:

```json
{
  "questions": [{
    "question": "如何填写？",
    "header": "填写方式",
    "multiSelect": false,
    "options": [
      {"label": "自己输入", "description": "手动输入"},
      {"label": "AI 辅助生成", "description": "由 Claude Code 根据上下文生成建议"},
      {"label": "返回上一步", "description": "回到主体选择"}
    ]
  }]
}
```
Then based on choice:
- **自己输入** → text prompt (see below)
- **AI 辅助生成** → generate suggestion from conversation context, show it, AskUserQuestion "确认使用" or "自行修改"

Follow-up text prompts by actor type:
- **开发者** → "哪些开发者？输入名称，多人用逗号分隔". Record as `developer: name1, name2`.
- **LLM** → if multiple models, AskUserQuestion to pick. Record as `LLM: model-name`.
- **agent** → record as detected agent name, no text prompt needed.
- **agent + 辅助工具** → "使用了哪些辅助工具？". If user selected AI assist and doesn't know, scan `~/.claude/skills/` and settings.json, present findings.

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

## Step 7: Tags

Same pattern:
```json
{
  "questions": [{
    "question": "如何填写标签？",
    "header": "标签",
    "multiSelect": false,
    "options": [
      {"label": "自己输入", "description": "手动输入标签，逗号分隔"},
      {"label": "AI 辅助生成", "description": "由 Claude Code 根据上下文建议标签"},
      {"label": "跳过", "description": "不添加标签"}, {"label": "返回上一步", "description": "回到上一步"}
    ]
  }]
}
```
- 自己输入 → text prompt: "标签？（逗号分隔）" → convert to lowercase, trim
- AI 辅助生成 → generate **max 3** tag suggestions from context, present via AskUserQuestion (multiSelect). If more tags are relevant, add an "其他..." option to let user type additional ones.
- 跳过 → leave empty

## Step 8: Confirm — AskUserQuestion

Show the entry preview as markdown. Use AskUserQuestion:
```json
{
  "questions": [{
    "question": "确认保存到 milestones/MILESTONE.md？",
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
| 主体 | actor |
| 标签 | tags |
| 时间 | YYYY-MM-DD HH:MM |

### 背景
context

### 详情
details
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
- Save to `milestones/MILESTONE.md` in current project
- No auto-fill without user choosing AI assist via AskUserQuestion option
- AI-generated suggestions for AskUserQuestion must not exceed 3 items (leaving 4th slot for "其他..." / navigation). If more ideas exist, pick top 3.
