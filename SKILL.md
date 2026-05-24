---
name: "Summary-Report-Skill"
description: "Generates interactive HTML or Markdown summary reports with SVG visual roadmap and checkable milestones. Invoke when user says '总结' or asks for project/skill review summary. If user doesn't specify format, ask them to choose between HTML and Markdown."
author: "Luoci"
email: "luociqaq@qq.com"
github: "https://github.com/luociqvq"
---

# Summary Report Skill

Generates interactive HTML or Markdown reports for project review OR skill analysis. Uses a **template-on-demand architecture**: framework-specific analysis templates are loaded only when needed, saving tokens and improving precision.

## ⛔ Pipeline Rule: Ask Once, Execute Once

```
Phase A (Ask)               Phase B (Execute)
┌─────────────────┐        ┌──────────────────────┐
│ A1: Format      │        │ B1: Framework Detect  │
│ A2: Sections    │ ────►  │ B2: Load Templates    │
│ A3: Depth       │        │ B3: Data Collection   │
└─────────────────┘        │ B4: Generate Report   │
                           └──────────────────────┘
```

1. Phase A 中每个问题只问一次，回答后立即前进，绝不回头。
2. 进入 Phase B 后禁止使用 AskUserQuestion，静默执行全部步骤直到报告生成完毕。
3. 用户模糊回答时使用默认值：HTML格式、全选所有部分、深度模式。

## When to Invoke

- User says "总结" or "生成总结"
- User asks for a project review summary
- User wants to generate a comprehensive review report
- User wants to analyze a skill and understand what it does
- User says "分析skill" or "分析这个skill"

## ═══════════════════════════════════════
## PHASE A: ASK (每个问题只问一次)
## ═══════════════════════════════════════

### A1: Output Format

用户已指定格式则直接使用，否则用 AskUserQuestion 询问一次：

**Question**: "你希望报告以什么格式输出？"
- **HTML（推荐）** — 交互式HTML报告，支持折叠展开、暗色模式、路线图勾选、增量对比等全部功能
- **Markdown** — 纯文本Markdown格式，适合直接阅读或粘贴到文档中，包含所有核心内容但无交互功能

**默认值：HTML**

### A2: Section Selection

用 AskUserQuestion 询问一次：

**Question 1**: "你希望生成的总结报告包含哪些部分？（可多选）"
- 功能表与文件列表 — 项目统计、模块卡片、API端点表、前后端文件列表、数据库表列表
- 漏洞与冗余审查 — 全面漏洞扫描结果（严重/高/中/低）、冗余代码与文件审查
- 逻辑问题检查 — 字段不匹配、数据流错误、业务逻辑缺陷
- 运行匹配验证 — 模块兼容性表、配置对齐、运行状态验证

**Question 2**: "还需要包含以下哪些额外内容？（可多选）"
- 优化评估建议 — P0-P3优先级排序的优化建议
- 优化实施路线图 — 可勾选任务的分阶段实施路线图
- 系统架构总览图 — 前端+后端+数据库架构图
- 数据请求流程图 — 基于项目实际代码分析总结的数据请求流程
- 增量对比报告 — 与上次报告对比，标记新增/已修复/变化项

**默认值：全选**

### A3: Depth Selection

用 AskUserQuestion 询问一次：

**Question**: "选择审查深度？"
- 快速模式 — 仅项目结构+安全扫描（2个并行批次，速度快）
- 深度模式（推荐） — 全部4个并行批次，含逻辑验证和冗余扫描

**默认值：深度模式**

## ═══════════════════════════════════════
## PHASE B: EXECUTE (禁止再问)
## ═══════════════════════════════════════

### B1: Framework Detection

Determine which analysis mode to use:

| User Intent | Mode | Template to Load |
|---|---|---|
| "总结项目", "生成总结", project review | Project Analysis | Backend + Frontend templates |
| "分析skill", "这个skill做什么", skill review | Skill Analysis | `analysis-templates/skill-analysis.md` |

#### Backend Framework Detection

Run these Glob checks **simultaneously**:

```
Glob: "server/think"       → ThinkPHP
Glob: "artisan"            → Laravel
Glob: "manage.py"          → Django/Flask
Glob: "pom.xml"            → Spring Boot
Glob: "go.mod"             → Go
Glob: "nest-cli.json"      → NestJS
Glob: "package.json"       → Check for express/koa/nest dependencies
```

| Indicator File | Framework | Template to Load |
|---|---|---|
| `server/think` + `server/app/controller/` | ThinkPHP | `analysis-templates/backend/thinkphp.md` |
| `app/Http/Controllers/` + `artisan` | Laravel | `analysis-templates/backend/laravel.md` |
| `app.py` / `manage.py` + `views.py` | Django/Flask | `analysis-templates/backend/django.md` |
| `src/main/java/` + `pom.xml` / `build.gradle` | Spring Boot | `analysis-templates/backend/spring-boot.md` |
| `index.js` / `app.js` + `package.json` (express) | Express | `analysis-templates/backend/express.md` |
| `src/index.ts` + `nest-cli.json` | NestJS | `analysis-templates/backend/express.md` |
| `server/` + `package.json` (no framework) | Node.js | `analysis-templates/backend/generic-node.md` |
| `main.go` + `go.mod` | Go | `analysis-templates/backend/go.md` |

#### Frontend Framework Detection

Run these Glob checks **simultaneously**:

```
Glob: "vite.config.js"     → Vue 3
Glob: "vue.config.js"      → Vue 2
Glob: "next.config.js"     → Next.js
Glob: "nuxt.config.ts"     → Nuxt 3
Glob: "package.json"       → Check for react-scripts
```

| Indicator File | Framework | Template to Load |
|---|---|---|
| `vite.config.js` + `src/*.vue` | Vue 3 + Vite | `analysis-templates/frontend/vue3.md` |
| `vue.config.js` + `src/*.vue` | Vue 2 + Webpack | `analysis-templates/frontend/vue2.md` |
| `next.config.js` + `src/app/` | Next.js | `analysis-templates/frontend/nextjs.md` |
| `react-scripts` in `package.json` | React CRA | `analysis-templates/frontend/react.md` |
| `nuxt.config.ts` + `app.vue` | Nuxt 3 | `analysis-templates/frontend/nuxt3.md` |

### B2: Load Templates

After detecting frameworks, **Read the corresponding template files** from the skill directory. Only load what you need:

**For Project Analysis**:
1. Read the detected **backend template** (e.g., `analysis-templates/backend/thinkphp.md`)
2. Read the detected **frontend template** (e.g., `analysis-templates/frontend/vue3.md`)
3. Read the **report template** based on user's format choice from A1:
   - HTML → `report-templates/html-template.md`
   - Markdown → `report-templates/markdown-template.md`

**For Skill Analysis**:
1. Read `analysis-templates/skill-analysis.md`
2. Read the target skill's `SKILL.md`
3. Read the **report template** based on user's format choice from A1

**Template file paths are relative to the skill directory** (`.trae/skills/Summary-Report-Skill/`).

### B3: Data Collection

Follow the **Data Collection Batches** defined in the loaded backend/frontend templates. Use **parallel SearchCodebase calls** to maximize efficiency.

#### Data Aggregation Rules (Common to All Frameworks)

After collecting all data:
1. **Cross-reference** controller field names with database column names → identify mismatches
2. **Cross-reference** router paths with actual controller methods → identify dead routes
3. **Cross-reference** frontend component imports with router registrations → identify orphan components
4. **Count** all statistics (modules, APIs, files, tables, vulnerabilities by severity)
5. **Calculate** health scores: Security = f(critical,high), Stability = f(fatal_logic,field_mismatch), Quality = f(redundancy,naming), Completeness = f(working_modules/total)

#### Quick vs Deep Mode

- **Quick mode**: Only run Batch 1 + Batch 2 (structure + security)
- **Deep mode** (default): Run all 4 batches

Generate ONLY the sections the user selected in A2.

### B4: Generate Report

Follow the **report template** loaded in B2 to generate the final output.

#### Asset Files (HTML Mode Only)

CSS and JS templates are in `report-assets/`:
- `report-assets/report.css` — Copy to target project's `report-assets/`
- `report-assets/report.js` — Copy to target project's `report-assets/`

#### Generation Workflow

1. **Copy assets** (HTML mode): Read CSS/JS from skill directory, write to target project's `report-assets/`
2. **Generate report**: Create `summary-report.html` or `summary-report.md` in project root
3. **Clean up** (HTML split mode): Delete `summary-report-parts/` after merging
4. **Final output**: One report file + `report-assets/` folder (HTML mode only)

#### Output File Naming

| Mode | Format | File Name |
|---|---|---|
| Project Analysis | HTML | `summary-report.html` |
| Project Analysis | Markdown | `summary-report.md` |
| Skill Analysis | HTML | `skill-analysis-report.html` |
| Skill Analysis | Markdown | `skill-analysis.md` |

## Skill Directory Structure

```
.trae/skills/Summary-Report-Skill/
├── SKILL.md                              ← This file (lightweight dispatcher)
├── analysis-templates/
│   ├── backend/
│   │   ├── thinkphp.md                   ← ThinkPHP analysis template
│   │   ├── laravel.md                    ← Laravel analysis template
│   │   ├── django.md                     ← Django/Flask analysis template
│   │   ├── spring-boot.md               ← Spring Boot analysis template
│   │   ├── express.md                    ← Express/NestJS analysis template
│   │   ├── go.md                         ← Go analysis template
│   │   └── generic-node.md              ← Generic Node.js analysis template
│   ├── frontend/
│   │   ├── vue3.md                       ← Vue 3 analysis template
│   │   ├── vue2.md                       ← Vue 2 analysis template
│   │   ├── react.md                      ← React analysis template
│   │   ├── nextjs.md                     ← Next.js analysis template
│   │   └── nuxt3.md                      ← Nuxt 3 analysis template
│   └── skill-analysis.md                 ← Skill analysis template
├── report-templates/
│   ├── html-template.md                  ← HTML report structure template
│   └── markdown-template.md             ← Markdown report structure template
└── report-assets/
    ├── report.css                        ← CSS template
    └── report.js                         ← JS template
```

## Important Notes

- **ALWAYS** include actual file paths in file lists
- **ALWAYS** include code snippets with syntax highlighting for vulnerabilities and logic errors
- **ALWAYS** include fix code side-by-side with vulnerable code for Critical/High/Medium issues (HTML mode)
- **ALWAYS** make roadmap items checkable with localStorage persistence (HTML mode)
- **ALWAYS** save report data to localStorage for incremental comparison on next run
