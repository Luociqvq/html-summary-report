# Skill Analysis Template

## When to Use

When the user asks to analyze a skill (e.g., "分析这个skill", "这个skill是做什么的", "帮我分析xxx skill"), use this template.

## Analysis Workflow

1. **Identify the target skill**: Determine which skill the user wants to analyze:
   - A skill in the `.trae/skills/` directory
   - A skill directory the user points to
   - A skill the user describes by name

2. **Read the skill's SKILL.md**: Use the Read tool to read the target skill's `SKILL.md` file. This is the primary source of information.

3. **Read additional skill files** (if referenced in SKILL.md):
   - Template files in subdirectories
   - Asset files (CSS/JS)
   - Documentation files

4. **Analyze the skill**: Extract and summarize the following dimensions:

### Extraction Dimensions

| Dimension | What to Extract |
|---|---|
| **Name & Description** | Skill name, description, author info |
| **Trigger Conditions** | When should this skill be invoked? What keywords/phrases? |
| **Core Functionality** | What are the main features and capabilities? |
| **Workflow/Process** | What steps does the skill follow when executed? |
| **Input/Output** | What does the skill need as input? What does it produce? |
| **Dependencies** | What external tools, files, or APIs does it rely on? |
| **Limitations** | Any known constraints or edge cases? |
| **Template Architecture** | Does it use external template files? How are they loaded? |

## Report Structure

### Markdown Format

```markdown
# 🔍 Skill 分析报告: {{SKILL_NAME}}

> 分析日期: {{DATE}}

---

## 📋 基本信息

| 属性 | 内容 |
|------|------|
| 名称 | {{skill name}} |
| 描述 | {{skill description}} |
| 作者 | {{author}} |
| 邮箱 | {{email}} |
| GitHub | {{github}} |

---

## 🎯 触发条件

- {{trigger condition 1}}
- {{trigger condition 2}}
- ...

---

## ⚡ 核心功能

### 功能1: {{function name}}
{{description}}

### 功能2: {{function name}}
{{description}}

---

## 🔄 工作流程

1. **步骤1**: {{description}}
2. **步骤2**: {{description}}
3. ...

---

## 📥 输入 / 📤 输出

| 类型 | 说明 |
|------|------|
| 输入 | {{what the skill needs}} |
| 输出 | {{what the skill produces}} |

---

## 🔗 依赖项

- {{dependency 1}}
- {{dependency 2}}

---

## ⚠️ 限制与注意事项

- {{limitation 1}}
- {{limitation 2}}

---

## 💡 使用建议

{{practical advice on how to best use this skill}}
```

### HTML Format

For HTML format skill analysis, use the same HTML template structure as project analysis but with skill-analysis-specific sections. The CSS/JS assets are reused from `report-assets/`. Output file: `skill-analysis-report.html` or `skill-analysis.md`.

## Output File Naming

| Format | File Name |
|---|---|
| HTML | `skill-analysis-report.html` |
| Markdown | `skill-analysis.md` |
