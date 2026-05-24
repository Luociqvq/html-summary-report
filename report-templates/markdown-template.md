# Markdown Report Template

## Output File

`summary-report.md` (project analysis) or `skill-analysis.md` (skill analysis)

## Markdown Structure

```markdown
# {{PROJECT_NAME}} 项目审查总结报告

> 生成日期: {{DATE}} | 技术栈: {{TECH_STACK}} | 审查深度: {{DEPTH}}

---

## 📊 项目概览

| 指标 | 数值 |
|------|------|
| 后端控制器 | {{count}} |
| 前端视图 | {{count}} |
| 数据库表 | {{count}} |
| API端点 | {{count}} |
| 状态管理 | {{count}} |

### 健康分数

| 维度 | 分数 | 状态 |
|------|------|------|
| 🔒 安全性 | {{score}}/100 | {{status}} |
| 🧠 逻辑性 | {{score}}/100 | {{status}} |
| 🔗 兼容性 | {{score}}/100 | {{status}} |
| ✨ 代码质量 | {{score}}/100 | {{status}} |

---

## 🏗️ 系统架构总览

{{architecture description}}

```
Frontend ⇄ Backend ⇄ Database
```

---

## 🔄 数据请求流程

{{flow description}}

```
前端组件 → API调用 → 中间件 → Controller → 数据库 → JSON响应
```

---

## 📋 功能表与文件列表

### 模块功能卡片

#### 📦 {{Module Name}}
{{description}}

**能力标签**: `CRUD` `统计` `筛选`

---

### API端点列表

| 方法 | 路径 | 描述 | 控制器 |
|------|------|------|--------|
| GET | /api/xxx | {{desc}} | {{controller}} |
| POST | /api/xxx | {{desc}} | {{controller}} |

---

### 前端文件列表

| 文件 | 路径 | 描述 |
|------|------|------|
| {{name}} | {{path}} | {{desc}} |

---

### 后端文件列表

| 文件 | 路径 | 描述 |
|------|------|------|
| {{name}} | {{path}} | {{desc}} |

---

### 数据库表列表

| 表名 | 关键列 | 关联模块 | 估计行数 |
|------|--------|---------|---------|
| {{table}} | {{columns}} | {{module}} | {{count}} |

---

## 🔒 漏洞与冗余审查

### 严重漏洞 🔴

**{{vulnerability title}}**
- 📍 位置: `{{file:line}}`
- 📝 描述: {{description}}
- ⚠️ 漏洞代码:
```{{language}}
{{vulnerable code}}
```
- ✅ 修复代码:
```{{language}}
{{fixed code}}
```
- 💥 影响: {{impact}}
- 🔧 修复: {{fix suggestion}}

---

### 高危漏洞 🟠

{{same pattern as critical}}

---

### 中危漏洞 🟡

{{same pattern}}

---

### 低危漏洞 🟢

| # | 描述 | 文件 | 建议 |
|---|------|------|------|
| 1 | {{desc}} | {{file}} | {{suggestion}} |

---

### 冗余代码与文件

| 文件路径 | 类型 | 原因 | 建议 |
|---------|------|------|------|
| {{path}} | {{type}} | {{reason}} | {{suggestion}} |

---

## 🧠 逻辑问题检查

### 致命逻辑错误

**{{error title}}**
- 📍 位置: `{{file:line}}`
- 📝 描述: {{description}}
- ⚠️ 错误代码:
```{{language}}
{{error code}}
```
- ✅ 修复代码:
```{{language}}
{{fixed code}}
```

---

### 严重逻辑问题

{{same pattern}}

---

### 一般逻辑问题

| # | 描述 | 文件 | 建议 |
|---|------|------|------|
| 1 | {{desc}} | {{file}} | {{suggestion}} |

---

## 🔗 运行匹配验证

### 模块兼容性表

| 模块 | 前端→后端 | 后端→数据库 | 状态 |
|------|----------|-----------|------|
| {{module}} | {{status}} | {{status}} | ✅/⚠️/❌ |

---

### 关键不匹配项

> ⚠️ {{mismatch description}}

---

## 🚀 优化评估建议

### P0 🚨 紧急修复

- **{{suggestion}}** — 影响: {{impact}}
  - 修复步骤: {{steps}}

### P1 🔴 高优先级

- **{{suggestion}}** — 影响: {{impact}}

### P2 🟡 中优先级

- **{{suggestion}}** — 影响: {{impact}}

### P3 🟢 体验提升

- **{{suggestion}}** — 影响: {{impact}}

---

## 📌 优化实施路线图

### 阶段1: P0 紧急修复
- [ ] {{task 1}}
- [ ] {{task 2}}

### 阶段2: P1 高优先级
- [ ] {{task 1}}
- [ ] {{task 2}}

### 阶段3: P2 中优先级
- [ ] {{task 1}}

### 阶段4: P3 体验提升
- [ ] {{task 1}}

### 阶段5: 长期规划
- [ ] {{task 1}}

---

## 📊 增量对比报告

> 上次报告: {{prev_date}} | 本次报告: {{curr_date}}

### 健康分数变化

| 维度 | 上次 | 本次 | 变化 |
|------|------|------|------|
| 安全性 | {{prev}} | {{curr}} | {{diff}} |

### 🆕 新增问题
| 严重性 | 描述 | 文件 |
|--------|------|------|
| {{level}} | {{desc}} | {{file}} |

### ✅ 已修复问题
| 严重性 | 描述 | 文件 |
|--------|------|------|
| {{level}} | {{desc}} | {{file}} |

### 📁 文件变化
| 变化 | 文件 |
|------|------|
| + 新增 | {{file}} |
| - 删除 | {{file}} |

---

## 📝 总结

{{final summary with key findings and recommendations}}
```

## Markdown Formatting Rules

1. Use proper Markdown headings (h1-h4) for section hierarchy
2. Use tables for structured data
3. Use code blocks with syntax highlighting for code snippets
4. Use emoji icons for visual distinction
5. Use checkboxes for roadmap milestones
6. Use blockquotes for important notes
7. Use horizontal rules (`---`) to separate major sections
8. Only include sections the user selected
