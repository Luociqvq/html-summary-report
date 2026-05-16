---
name: "html-summary-report"
description: "Generates interactive HTML summary reports with SVG visual roadmap and checkable milestones. Invoke when user says '总结' or asks for project review summary in HTML format."
author: "Luoci"
email: "luociqaq@qq.com"
github: "https://github.com/luociqvq"
---

# HTML Summary Report Generator

This skill generates an interactive HTML report file summarizing project review findings. The report features **all sections collapsed by default**, a **visual roadmap** with checkable milestones and progress tracking, priority-coded issue cards, health score metrics, detailed file/feature listings, **code highlighting with fix suggestions**, **system architecture diagram**, **data request flow diagram**, and **incremental report comparison**.

## When to Invoke

- User says "总结" or "生成总结"
- User asks for a project review summary in HTML format
- User wants to generate a comprehensive review report

## Data Collection Phase (MANDATORY)

After the user selects sections, you MUST collect all required data BEFORE generating any HTML. Use **parallel SearchCodebase calls** to maximize efficiency.

### Framework Detection (FIRST STEP)

Before running any batch, detect the project's backend framework by checking for these indicator files:

| Indicator File | Framework | Architecture Label |
|---|---|---|
| `server/think` + `server/app/controller/` | ThinkPHP | ThinkPHP 8 API Server |
| `app/Http/Controllers/` + `artisan` | Laravel | Laravel API Server |
| `src/Controller/` + `composer.json` (symfony) | Symfony | Symfony API Server |
| `app.py` / `manage.py` + `views.py` | Django/Flask | Python API Server |
| `src/main/java/` + `pom.xml` / `build.gradle` | Spring Boot | Spring Boot API Server |
| `index.js` / `app.js` + `package.json` (express) | Express | Express API Server |
| `src/index.ts` + `nest-cli.json` | NestJS | NestJS API Server |
| `server/` + `package.json` (no framework) | Node.js | Node.js API Server |
| `main.go` + `go.mod` | Go | Go API Server |

**Detection workflow**:
1. Run `Glob: "server/think"` and `Glob: "artisan"` and `Glob: "manage.py"` and `Glob: "pom.xml"` and `Glob: "go.mod"` simultaneously
2. Check `package.json` for framework dependencies if Node.js detected
3. Set `{{BACKEND_FRAMEWORK}}` and `{{TECH_STACK}}` variables for the report header
4. Use the detected framework to determine:
   - Controller directory path (e.g., `server/app/controller/` for ThinkPHP, `app/Http/Controllers/` for Laravel)
   - Middleware directory path
   - Route file path
   - Config directory path
   - Model directory path

**If framework is unknown**, use generic paths and label as "Custom Backend".

### Framework-Specific Search Keywords (MANDATORY)

After detecting the framework, you MUST adjust all search keywords and directory paths according to the table below. Do NOT use ThinkPHP-specific keywords for other frameworks.

| Category | ThinkPHP | Laravel | Django/Flask | Spring Boot | Express/NestJS | Go |
|---|---|---|---|---|---|---|
| **Controller dir** | `server/app/controller/` | `app/Http/Controllers/` | `views.py` or `*/views/` | `src/main/java/**/controller/` | `src/controllers/` or `src/*.controller.ts` | `internal/handler/` or `*/handler/` |
| **Middleware dir** | `server/app/middleware/` | `app/Http/Middleware/` | `middleware.py` or `*/middleware/` | `src/main/java/**/filter/` | `src/middleware/` | `internal/middleware/` |
| **Route file** | `server/route/app.php` | `routes/api.php` | `urls.py` | `src/main/java/**/config/` | `src/routes/` or `app.js` | `internal/router/` |
| **Model dir** | `server/app/model/` | `app/Models/` | `models.py` or `*/models/` | `src/main/java/**/entity/` | `src/models/` or `prisma/schema.prisma` | `internal/model/` |
| **Config dir** | `server/config/` | `config/` | `settings.py` | `src/main/resources/` | `config/` or `.env` | `config/` or `.env` |
| **DB query pattern** | `Db::name` `->where` `->insert` `->update` | `DB::table` `Model::where` `Model::create` | `Model.objects.filter` `Model.objects.create` | `repository.findById` `repository.save` | `Model.find` `Model.create` `prisma.model.findMany` | `db.Query` `db.Exec` |
| **Request param pattern** | `request->param` `request->file` | `$request->input` `$request->file` | `request.POST` `request.FILES` `request.GET` | `@RequestParam` `@RequestBody` `@PathVariable` | `req.body` `req.params` `req.query` | `c.Param` `c.Query` `c.Bind` |
| **File upload pattern** | `request->file` `Filesystem::putFile` | `$request->file` `Storage::put` | `request.FILES` `default_storage.save` | `MultipartFile` `Files.write` | `multer` `fs.writeFile` | `FormFile` `os.Create` |
| **HTTP client pattern** | `file_get_contents` `curl_exec` | `Http::get` `Http::post` | `requests.get` `requests.post` | `RestTemplate` `WebClient` | `axios` `fetch` `node-fetch` | `http.Get` `http.Post` |
| **Auth pattern** | `middleware/Auth.php` | `Auth::user` `auth middleware` | `@login_required` `request.user` | `@PreAuthorize` `SecurityContext` | `jwt.verify` `passport` | `jwt.Parse` `middleware` |
| **SQL file** | `server/database.sql` | `database/migrations/` | `migrations/` | `src/main/resources/schema.sql` | `prisma/migrations/` | `migrations/` |
| **Env file** | `server/.env` | `.env` | `.env` | `application.yml` / `.env` | `.env` | `.env` |

### Framework-Specific Data Collection (MANDATORY)

After framework detection, replace the generic Batch 1-4 search targets with framework-specific ones. Examples:

**For Django/Flask backend:**
```
Batch 1: SearchCodebase targets → "views.py", "models.py", "urls.py", "settings.py", "migrations/"
Batch 2: Security keywords → "requests.get", "request.FILES", "os.system", "subprocess", "eval(", "exec("
Batch 3: Logic keywords → "objects.filter", "objects.create", "field mismatch", "serializer"
Batch 4: Glob patterns → "*/views.py", "*/models.py", "*/urls.py", "*/migrations/*.py"
```

**For Spring Boot backend:**
```
Batch 1: SearchCodebase targets → "controller/", "entity/", "repository/", "application.yml", "schema.sql"
Batch 2: Security keywords → "RestTemplate", "@RequestParam", "MultipartFile", "Runtime.exec", "SQL injection"
Batch 3: Logic keywords → "findById", "save", "findBy", "field mismatch", "DTO"
Batch 4: Glob patterns → "**/controller/*.java", "**/entity/*.java", "**/repository/*.java"
```

**For Express/NestJS backend:**
```
Batch 1: SearchCodebase targets → "routes/", "controllers/", "models/", "prisma/schema.prisma", "app.js"
Batch 2: Security keywords → "eval(", "child_process", "fs.writeFile", "req.body", "SQL injection", "multer"
Batch 3: Logic keywords → "Model.find", "Model.create", "findByIdAndUpdate", "field mismatch"
Batch 4: Glob patterns → "src/**/*.controller.ts", "src/**/*.module.ts", "src/**/*.service.ts"
```

### Frontend Framework Detection

| Indicator File | Framework | Architecture Label |
|---|---|---|
| `vite.config.js` + `src/*.vue` | Vue 3 + Vite | Vue 3 SPA |
| `vue.config.js` + `src/*.vue` | Vue 2 + Webpack | Vue 2 SPA |
| `next.config.js` + `src/app/` | Next.js | Next.js SSR |
| `react-scripts` in `package.json` | React CRA | React SPA |
| `nuxt.config.ts` + `app.vue` | Nuxt 3 | Nuxt 3 SSR |

### Parallel Batch 1: Project Structure (all independent, run simultaneously)

```
SearchCodebase: "路由定义和API端点注册" → target: server/route/
SearchCodebase: "控制器类和方法定义" → target: server/app/controller/
SearchCodebase: "数据库表结构定义 CREATE TABLE" → target: server/database.sql
SearchCodebase: "Vue组件和视图定义" → target: src/views/
SearchCodebase: "Pinia store状态管理" → target: src/stores/
```

### Parallel Batch 2: Security Scan (all independent, run simultaneously)

```
SearchCodebase: "file_get_contents curl_exec HTTP请求外部URL" → target: server/app/
SearchCodebase: "file upload 文件上传 request->file putFile" → target: server/app/
SearchCodebase: "request->param 参数接收 无验证" → target: server/app/controller/
SearchCodebase: "hardcoded 硬编码 password secret key" → target: server/
SearchCodebase: "middleware 中间件 Auth Cors" → target: server/app/middleware/
```

### Parallel Batch 3: Logic Validation (depends on Batch 1 results)

```
SearchCodebase: "字段名不匹配 列不存在 database column mismatch" → compare controller fields vs database.sql
Grep: "journal_date|is_favorite|location" → target: server/app/controller/ (known mismatch patterns)
Grep: "Db::name|->insert|->update|->where" → target: server/app/controller/ (all DB operations)
```

### Parallel Batch 4: Redundancy Scan (all independent)

```
SearchCodebase: "未使用的组件 孤立文件" → check router imports vs actual files
Glob: "src/views/*.vue" → cross-reference with router/index.js
Glob: "server/app/controller/*.php" → cross-reference with route/app.php
Glob: "server/app/model/*.php" → cross-reference with controller imports
```

### Data Aggregation Rules

After collecting all data:
1. **Cross-reference** controller field names with database.sql column names → identify mismatches
2. **Cross-reference** router paths with actual controller methods → identify dead routes
3. **Cross-reference** Vue component imports with router registrations → identify orphan components
4. **Count** all statistics (modules, APIs, files, tables, vulnerabilities by severity)
5. **Calculate** health scores: Security = f(critical,high), Stability = f(fatal_logic,field_mismatch), Quality = f(redundancy,naming), Completeness = f(working_modules/total)

### Quick vs Deep Mode

- **Quick mode**: Only run Batch 1 + Batch 2 (structure + security), skip deep logic validation
- **Deep mode** (default): Run all 4 batches for comprehensive review

Ask the user which mode they prefer during the section selection step.

## Pre-Generation: Interactive Section Selection

**MANDATORY**: Before generating the report, you MUST use the AskUserQuestion tool to ask the user which sections they want included. Present the following options:

**Question 1**: "你希望生成的HTML总结报告包含哪些部分？（可多选）"
- 功能表与文件列表 — 项目统计、模块卡片、API端点表、前后端文件列表、数据库表列表
- 漏洞与冗余审查 — 全面漏洞扫描结果（严重/高/中/低）、冗余代码与文件审查
- 逻辑问题检查 — 字段不匹配、数据流错误、业务逻辑缺陷
- 运行匹配验证 — 模块兼容性表、配置对齐、运行状态验证

**Question 2**: "还需要包含以下哪些额外内容？（可多选）"
- 优化评估建议 — P0-P3优先级排序的优化建议
- 优化实施路线图 — 可勾选任务的分阶段实施路线图
- 系统架构总览图 — 前端+后端+数据库架构图
- 数据请求流程图 — 前端→后端→数据库的完整请求流程
- 增量对比报告 — 与上次报告对比，标记新增/已修复/变化项

**Question 3**: "选择审查深度？"
- 快速模式 — 仅项目结构+安全扫描（2个并行批次，速度快）
- 深度模式（推荐） — 全部4个并行批次，含逻辑验证和冗余扫描

Generate ONLY the sections the user selected. If user selects all, include all sections.

## External Asset Files (Multi-Project Portable)

CSS and JavaScript templates are stored **inside the skill directory** as `report-assets/report.css` and `report-assets/report.js`. When generating a report for ANY project, these files are **copied** to the target project's `report-assets/` directory.

### Skill Directory Structure
```
.trae/skills/html-summary-report/
├── SKILL.md                    ← This instruction file
└── report-assets/
    ├── report.css              ← CSS template (layout, components, dark theme, print, responsive)
    └── report.js               ← JS template (section toggle, roadmap, theme, copy, sort, comparison)
```

### Target Project Output Structure
```
{project-root}/
├── summary-report.html         ← Final generated report (references external CSS/JS)
└── report-assets/
    ├── report.css              ← Copied from skill directory
    └── report.js               ← Copied from skill directory
```

### Generation Workflow (MANDATORY)

1. **Copy assets**: Read `report-assets/report.css` and `report-assets/report.js` from the skill directory, then write them to the target project's `report-assets/` directory
2. **Generate HTML**: Create `summary-report.html` in the project root, referencing the copied assets via relative paths
3. **Clean up parts**: If using split-generation strategy, delete all `summary-report-parts/` files after merging — only the final `summary-report.html` should remain
4. **Final output**: The user receives exactly ONE HTML file + the `report-assets/` folder

### How to Reference in HTML
```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>{{PROJECT_NAME}} 项目审查总结报告</title>
  <link rel="stylesheet" href="report-assets/report.css">
</head>
<body>
  <!-- ... report content ... -->
  <script src="report-assets/report.js"></script>
</body>
</html>
```

### Self-Contained Mode (Optional)
If the user requests a single portable HTML file (e.g., for email), inline the CSS and JS:
```html
<style>/* paste contents of report-assets/report.css here */</style>
<!-- ... -->
<script>/* paste contents of report-assets/report.js here */</script>
```
By default, use external file references. Only use self-contained mode if the user requests it.

### Updating External Assets
When you need to modify CSS or JS behavior:
1. **Edit** the files in the **skill directory** (`.trae/skills/html-summary-report/report-assets/`)
2. **Re-copy** them to the target project's `report-assets/` directory
3. **Do NOT** duplicate styles/scripts in the SKILL.md — this file only contains the reference pattern

## Report Structure

The HTML report must contain the following sections (based on user selection):

1. **Header** — Project name, report type, generation date, dark mode toggle button
2. **Navigation Sidebar** — Floating nav with scroll-spy for quick section jumping
3. **Overview Dashboard** — Summary statistics cards + health score metric bars
4. **System Architecture Diagram** — Visual architecture overview with boxes and arrows
5. **Data Request Flow Diagram** — Visual flow from frontend to backend to database
6. **Task 1: 功能表与文件列表** — Project overview stats, module feature cards, API endpoint table, frontend/backend file list tables with ACTUAL FILE PATHS, database table list
7. **Task 2: 漏洞与冗余审查** — Severity summary cards, issues grouped by severity WITH CODE SNIPPETS + FIX SUGGESTIONS, redundant files/code table
8. **Task 3: 逻辑问题检查** — Logic errors grouped by severity, field mismatch details WITH CODE SNIPPETS + FIX SUGGESTIONS, data flow issues
9. **Task 4: 运行匹配验证** — Module compatibility table with status badges, critical mismatches, config alignment
10. **Task 5: 优化评估建议** — Priority-ranked optimization suggestions (P0-P3), each with impact and scope
11. **Incremental Comparison** (if selected) — Diff table comparing with previous report, marking new/fixed/changed items
12. **Visual Roadmap** — Checkable milestones with progress tracking, auto-updating progress bars
13. **Final Summary Table** — Cross-task summary with conclusion

## Content Detail Requirements

### System Architecture Diagram
Must use the following HTML structure with CSS styling:
```html
<section id="architecture">
  <h2>系统架构总览</h2>
  <p class="desc">前后端分离架构，Vue 3 SPA + ThinkPHP 8 API Server，通过 RESTful API 通信</p>
  <div class="arch-diagram">
    <div class="arch-row">
      <div class="arch-box frontend">Vue 3 SPA<br><small>前端应用 (port 3210)</small></div>
      <span class="arch-arrow">⇄</span>
      <div class="arch-box backend">ThinkPHP 8<br><small>后端 API (port 9527)</small></div>
      <span class="arch-arrow">⇄</span>
      <div class="arch-box data">MySQL<br><small>数据库</small></div>
    </div>
    <div class="arch-row">
      <div class="arch-box data">localStorage<br><small>前端持久化</small></div>
      <div class="arch-box data">public/storage<br><small>文件存储</small></div>
    </div>
  </div>
</section>
```

### Data Request Flow Diagram
Must show the complete request lifecycle:
```html
<section id="flow">
  <h2>数据请求流程</h2>
  <p class="desc">前端发起HTTP请求 → CORS中间件 → Auth中间件 → 控制器 → 数据库 → 返回JSON</p>
  <div class="flow-diagram">
    <div class="flow-row">
      <div class="flow-step">
        <div class="flow-node client">Vue组件</div>
        <div class="flow-desc">用户操作触发<br>Pinia Store调用API</div>
      </div>
      <span class="flow-arrow">→</span>
      <div class="flow-step">
        <div class="flow-node api">Axios HTTP</div>
        <div class="flow-desc">GET/POST/PUT/DELETE<br>携带JSON数据</div>
      </div>
      <span class="flow-arrow">→</span>
      <div class="flow-step">
        <div class="flow-node middleware">CORS中间件</div>
        <div class="flow-desc">跨域头处理<br>OPTIONS预检</div>
      </div>
      <span class="flow-arrow">→</span>
      <div class="flow-step">
        <div class="flow-node middleware">Auth中间件</div>
        <div class="flow-desc">用户认证<br>注入userId</div>
      </div>
      <span class="flow-arrow">→</span>
      <div class="flow-step">
        <div class="flow-node controller">Controller</div>
        <div class="flow-desc">业务逻辑<br>数据验证</div>
      </div>
      <span class="flow-arrow">→</span>
      <div class="flow-step">
        <div class="flow-node db">MySQL</div>
        <div class="flow-desc">CRUD操作<br>返回数据集</div>
      </div>
      <span class="flow-arrow">→</span>
      <div class="flow-step">
        <div class="flow-node response">JSON响应</div>
        <div class="flow-desc">{code, msg, data}<br>返回前端</div>
      </div>
    </div>
  </div>
</section>
```

### Task 1 Detail Requirements
- **Project Overview**: Stats bar (modules, routes, APIs, tables, stores, controllers) + info table (name, tech stack, ports, API prefix)
- **Module Feature Cards**: Grid of cards (min-width 320px), each card MUST contain: (1) emoji icon + module name as h4, (2) one-line summary description, (3) tag chips showing capabilities (e.g., CRUD, 统计, 标签关联, 分页) with color-coded backgrounds (blue=CRUD, green=关联, yellow=报表, purple=分页, red=安全警告), (4) a detailed description paragraph (font-size 11px, muted color) explaining specific features, implementation details, edge cases, and any known issues. Example: "支持按优先级/状态/标签/关键词筛选，创建时可选多个标签，统计待完成/已完成/逾期数量". MUST include ALL modules found in the project.
- **API Endpoint Table**: Searchable table with columns: Method (color-coded GET/POST/PUT/DELETE), Path, Description, Controller. MUST list ALL API routes from route/app.php.
- **Frontend File List**: Table with columns: Icon, File Name, Path, Description. MUST list EVERY file with its FULL relative path. Group by category (Entry, Stores, Components, Views, Utils, Config). Include ALL files: entry files (main.js, App.vue), router, API layer, all views, all stores, config files (vite.config.js, .env.*), style files. Do NOT skip any file.
- **Backend File List**: Table with columns: Icon, File Name, Path, Description. MUST list EVERY file with its FULL relative path. Group by category (Controllers, Middleware, Models, Config, Routes, Entry, SQL, Utils). Include ALL files: ALL controllers (even ones with non-standard naming like Finance.php, Tag.php, Index.php), ALL middleware, ALL models, base classes (BaseController.php, Request.php, ExceptionHandle.php), config files (database.php, app.php, filesystem.php, etc.), route files, entry point (public/index.php), SQL files, composer.json, .env. Do NOT skip any file.
- **Database Table List**: Table with columns: Table name, Key columns, Related module, Row count estimate. MUST list ALL tables from database.sql.

### Task 2 Detail Requirements
- **Severity Summary Cards**: Grid showing count per severity level (Critical/High/Medium/Low)
- **Critical Issues**: Full detail cards with location tag, description, **VULNERABLE CODE + FIX CODE side-by-side**, impact, fix suggestion
- **High Issues**: Full detail cards with **VULNERABLE CODE + FIX CODE side-by-side**
- **Medium Issues**: Full detail cards with **VULNERABLE CODE + FIX CODE side-by-side**
- **Low Issues**: Compact cards
- **Redundant Files Table**: Table with columns: File Path, Type, Reason, Suggestion. MUST list ALL redundant/orphan files found.

### Vulnerable Code + Fix Code Pattern (MANDATORY for Critical/High/Medium)

Every vulnerability card at Critical/High/Medium level MUST include a **side-by-side code comparison** showing the vulnerable code and the suggested fix:

```html
<div class="issue-card critical">
  <div class="ic-head">
    <span class="badge critical">严重</span>
    <span class="ic-title">SSRF 服务端请求伪造</span>
    <span class="ic-loc">server/app/controller/TutorialController.php:fetchUrl()</span>
  </div>
  <div class="ic-desc">控制器直接使用用户输入的URL发起服务端请求，未做任何域名白名单校验</div>
  <div class="code-fix-pair">
    <div>
      <span class="code-fix-label vuln">⚠️ 漏洞代码</span>
      <pre class="code-block"><code><span class="err-line">$url = $this->request->param('url');</span>
$content = file_get_contents($url);
// No domain whitelist check!</code></pre>
    </div>
    <div>
      <span class="code-fix-label fix">✅ 修复代码</span>
      <pre class="code-block"><code><span class="fix-line">$url = $this->request->param('url');</span>
$allowed = ['jsonplaceholder.typicode.com'];
$host = parse_url($url, PHP_URL_HOST);
if (!in_array($host, $allowed)) {
    return json(['code'=>403,'msg'=>'域名不在白名单']);
}
$content = file_get_contents($url);</code></pre>
    </div>
  </div>
  <div class="ic-impact">影响：攻击者可访问内网服务、读取本地文件</div>
  <div class="ic-fix">修复：添加域名白名单校验，禁止请求内网地址</div>
</div>
```

Key CSS classes for fix code:
- `.code-fix-pair` — Grid layout (2 columns) for side-by-side display
- `.code-fix-label.vuln` — Red label "⚠️ 漏洞代码"
- `.code-fix-label.fix` — Green label "✅ 修复代码"
- `.err-line` — Red background highlight for vulnerable lines
- `.fix-line` — Green background highlight for fixed lines

### Task 3 Detail Requirements
- **Fatal Logic Errors**: Full detail cards with field chain visualization (frontend → backend → DB) AND **VULNERABLE CODE + FIX CODE side-by-side**
- **Severe Logic Issues**: Full detail cards with **VULNERABLE CODE + FIX CODE side-by-side**
- **General Logic Issues**: Compact cards

### Task 4 Detail Requirements
- **Module Compatibility Table**: Columns: Module, Frontend→Backend, Backend→DB, Status badge
- **Critical Mismatches**: Highlighted note block
- **Config Alignment**: Port, env, CORS, middleware verification

### Task 5 Detail Requirements
- **P0 Blockers**: Full detail cards with fix steps
- **P1 High Priority**: Full detail cards
- **P2 Medium Priority**: Full detail cards
- **P3 Low Priority**: Compact cards

### Incremental Comparison Section (if selected)

When the user selects "增量对比报告", add a dedicated section that compares the current report with the previous one. The comparison data is stored in `localStorage` under key `todoflow-report-data`.

**HTML Pattern:**
```html
<div class="section" id="comparison">
  <div class="section-header collapsed" onclick="toggleSection(this)">
    <span class="icon">📊</span>
    <h2>增量对比报告</h2>
    <span class="header-badge new">2</span>
    <span class="header-badge fixed">1</span>
    <div class="toggle">▼</div>
  </div>
  <div class="section-body hidden">
    <div class="note-block">上次报告时间：2026-05-15 14:30:00 | 本次报告时间：2026-05-16 10:00:00</div>

    <h3>健康分数变化</h3>
    <table class="diff-table">
      <tr><td>安全性</td><td class="diff-removed">35%</td><td>→</td><td class="diff-added">60%</td><td class="diff-changed">↑25%</td></tr>
      <tr><td>稳定性</td><td class="diff-same">55%</td><td>→</td><td class="diff-same">55%</td><td class="diff-same">—</td></tr>
      <tr><td>代码质量</td><td class="diff-same">65%</td><td>→</td><td class="diff-added">70%</td><td class="diff-changed">↑5%</td></tr>
    </table>

    <h3>🆕 新增问题</h3>
    <table class="diff-table">
      <tr><td class="diff-added">🆕</td><td>medium</td><td>缺少请求频率限制</td><td>server/app/middleware/</td></tr>
    </table>

    <h3>✅ 已修复问题</h3>
    <table class="diff-table">
      <tr><td class="diff-added">✅</td><td>critical</td><td>SSRF服务端请求伪造</td><td>server/app/controller/TutorialController.php</td></tr>
    </table>

    <h3>📝 仍存在的问题</h3>
    <table class="diff-table">
      <tr><td class="diff-removed">🔴</td><td>high</td><td>无限制文件上传</td><td>server/app/controller/CoupleController.php</td></tr>
    </table>

    <h3>📁 文件变化</h3>
    <table class="diff-table">
      <tr><td class="diff-added">+ 新增</td><td>src/views/NewFeature.vue</td></tr>
      <tr><td class="diff-removed">- 删除</td><td>src/views/DeprecatedView.vue</td></tr>
    </table>
  </div>
</div>
```

**Data Source Logic** (handled by `report.js`):
1. On page load, `report.js` calls `saveReportData()` which saves current issues + health scores to `localStorage`
2. On page load, `report.js` calls `loadPreviousReport()` which returns the previous report's data
3. When generating the HTML, read the previous report data from `localStorage` and compare with current findings
4. If no previous data exists, show a note: "这是首次报告，无对比数据"

## Key Design Rules

1. **All sections collapsed by default**: Every `section-header` must have class `collapsed` and every `section-body` must have class `hidden`
2. **Checkable Roadmap**: Each roadmap item must have a clickable checkbox that toggles completion state
3. **Progress Auto-update**: When items are checked/unchecked, the phase progress bar and overall progress must update automatically
4. **localStorage Persistence**: Roadmap progress must be saved to localStorage and restored on page reload
5. **Code Highlighting**: All code snippets use `<pre class="code-block"><code>` with dark background and monospace font
6. **Fix Code Side-by-Side**: Critical/High/Medium vulnerabilities MUST show vulnerable code and fix code side-by-side using `.code-fix-pair` grid
7. **Responsive**: Layout must work on desktop and mobile
8. **Architecture Diagram**: Use CSS flexbox boxes with arrows between components
9. **Data Flow Diagram**: Use CSS flexbox steps with arrows showing request lifecycle
10. **Dark Mode**: Support theme toggle with CSS variables, persisted in localStorage
11. **Copy Button**: Each code block has a copy button that appears on hover
12. **Navigation Sidebar**: Floating nav with scroll-spy for quick section jumping
13. **Table Sort**: All tables are sortable by clicking column headers
14. **Severity Badges in Headers**: Collapsed section headers show issue count badges

## Roadmap Phase HTML Pattern (with Checkboxes)

Each roadmap phase MUST use checkboxes that users can click to mark items as done. Progress auto-updates.

```html
<div class="phase-row">
  <div class="phase-milestone">
    <div class="milestone-node emergency">1</div>
    <div class="milestone-label">第一阶段</div>
    <div class="phase-connector"><div class="conn-line emergency"></div></div>
  </div>
  <div class="phase-content">
    <div class="phase-card">
      <div class="phase-header">
        <span class="phase-tag emergency">第一阶段</span>
        <span class="phase-title">🚨 紧急修复</span>
        <span class="phase-subtitle">让项目能跑起来</span>
      </div>
      <ul class="phase-items">
        <li onclick="toggleItem(this)">
          <span class="item-checkbox">✓</span>
          <span class="item-dot p0"></span>
          创建 server/.env 环境配置文件
        </li>
      </ul>
      <div class="phase-progress">
        <span>0/1 完成</span>
        <div class="progress-bar"><div class="progress-fill emergency" data-phase="emergency" style="width: 0%"></div></div>
        <span>0%</span>
      </div>
    </div>
  </div>
</div>
```

## HTML Template Structure

Use the following HTML template structure. All sections are **collapsed by default**. The roadmap uses **checkable milestones** with **auto-updating progress bars**. CSS and JS are loaded from external files.

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>{{PROJECT_NAME}} 项目审查总结报告</title>
  <link rel="stylesheet" href="report-assets/report.css">
</head>
<body>
  <div class="nav-sidebar">
    <a href="#overview" class="nav-item active">📊 概览</a>
    <a href="#architecture" class="nav-item">🏗️ 架构</a>
    <a href="#flow" class="nav-item">🔄 流程</a>
    <a href="#features" class="nav-item">📋 功能表</a>
    <a href="#vulnerabilities" class="nav-item">🔒 漏洞</a>
    <a href="#logic" class="nav-item">🧠 逻辑</a>
    <a href="#compatibility" class="nav-item">🔗 匹配</a>
    <a href="#optimization" class="nav-item">🚀 优化</a>
    <a href="#comparison" class="nav-item">📊 对比</a>
    <a href="#roadmap" class="nav-item">🗺️ 路线图</a>
  </div>

  <div class="container">
    <div class="header-bar">
      <div>
        <h1>📋 {{PROJECT_NAME}} 项目审查总结报告</h1>
        <p class="subtitle">生成时间：{{DATE}} | 技术栈：{{TECH_STACK}}</p>
      </div>
      <button class="theme-btn" onclick="toggleTheme()">🌙 暗色</button>
    </div>

    <!-- Overview Dashboard -->
    <!-- System Architecture Diagram (if selected) -->
    <!-- Data Request Flow Diagram (if selected) -->
    <!-- Task 1: 功能表与文件列表 (if selected) -->
    <!-- Task 2: 漏洞与冗余审查 (if selected) -->
    <!-- Task 3: 逻辑问题检查 (if selected) -->
    <!-- Task 4: 运行匹配验证 (if selected) -->
    <!-- Task 5: 优化评估建议 (if selected) -->
    <!-- Incremental Comparison (if selected) -->
    <!-- Visual Roadmap (if selected) -->
    <!-- Final Summary Table -->
  </div>
  <script src="report-assets/report.js"></script>
</body>
</html>
```

Write the complete HTML file to `summary-report.html` in the project root directory. The file references `report-assets/report.css` and `report-assets/report.js` via relative paths.

## Split Generation Strategy

When the HTML content is too large to generate in a single file (typically >50KB), use the **split-and-merge** approach:

1. **Create a directory** `summary-report-parts/` in the project root
2. **Generate parts sequentially**:
   - `part1-head-overview.html` — HTML head (with `<link>` to report.css), nav sidebar, header bar, overview dashboard, architecture diagram, data flow diagram
   - `part2-task1.html` — Task 1: Feature tables and file lists
   - `part3-task2-3.html` — Task 2+3: Vulnerabilities (with fix code), redundancy, logic issues (with fix code)
   - `part4-task4-5-compare-roadmap-js.html` — Task 4+5: Operation verification, optimization, incremental comparison, roadmap, final summary, `<script>` tag referencing report.js
3. **Merge all parts** using PowerShell:
   ```powershell
   $parts = @("part1-head-overview.html","part2-task1.html","part3-task2-3.html","part4-task4-5-compare-roadmap-js.html")
   $content = ""; foreach ($p in $parts) { $content += (Get-Content $p -Raw -Encoding UTF8) + "`n" }
   [System.IO.File]::WriteAllText("summary-report.html", $content, [System.Text.Encoding]::UTF8)
   ```
4. **Verify** the merged file starts with `<!DOCTYPE html>` and ends with `</html>`
5. **Clean up**: Delete the `summary-report-parts/` directory — only the final `summary-report.html` and `report-assets/` should remain in the project root

Part1 must contain the complete `<head>` with `<link>` to report.css, nav sidebar, and open `<body><div class="container">`. Part4 must close with `</div></body></html>` and include `<script src="report-assets/report.js"></script>`.

## Important Notes

- **ALWAYS** use AskUserQuestion before generating to ask which sections to include AND which depth mode
- **ALWAYS** run parallel SearchCodebase batches BEFORE generating any HTML (Data Collection Phase)
- **ALWAYS** include actual file paths in file lists (not just file names)
- **ALWAYS** include code snippets with syntax highlighting for vulnerabilities and logic errors
- **ALWAYS** include fix code side-by-side with vulnerable code for Critical/High/Medium issues
- **ALWAYS** make roadmap items checkable with localStorage persistence
- **ALWAYS** include architecture and flow diagrams when selected
- **ALWAYS** reference external CSS/JS from `report-assets/` directory (not inline)
- **ALWAYS** use split-and-merge strategy when content exceeds ~50KB to avoid truncation
- **ALWAYS** cross-reference data between batches (controller fields vs DB columns, router vs files)
- **ALWAYS** include navigation sidebar and dark mode toggle in the header
- **ALWAYS** save report data to localStorage for incremental comparison on next run
