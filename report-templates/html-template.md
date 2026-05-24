# HTML Report Template

## Output File

`summary-report.html` (project analysis) or `skill-analysis-report.html` (skill analysis)

## Asset Files

CSS and JS are loaded from `report-assets/` directory (copied from skill directory).

## HTML Structure

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

    <!-- Sections are inserted here based on user selection -->
  </div>
  <script src="report-assets/report.js"></script>
</body>
</html>
```

## Section HTML Patterns

### Overview Dashboard

```html
<section id="overview">
  <div class="section-header collapsed" onclick="toggleSection(this)">
    <span class="icon">📊</span><h2>项目概览</h2><div class="toggle">▼</div>
  </div>
  <div class="section-body hidden">
    <div class="stats-bar"><!-- stat cards --></div>
    <div class="health-scores"><!-- progress bars --></div>
  </div>
</section>
```

### System Architecture Diagram

```html
<section id="architecture">
  <div class="section-header collapsed" onclick="toggleSection(this)">
    <span class="icon">🏗️</span><h2>系统架构总览</h2><div class="toggle">▼</div>
  </div>
  <div class="section-body hidden">
    <p class="desc">{{architecture description}}</p>
    <div class="arch-diagram">
      <div class="arch-row">
        <div class="arch-box frontend">{{Frontend}}<br><small>{{desc}}</small></div>
        <span class="arch-arrow">⇄</span>
        <div class="arch-box backend">{{Backend}}<br><small>{{desc}}</small></div>
        <span class="arch-arrow">⇄</span>
        <div class="arch-box data">{{Database}}<br><small>{{desc}}</small></div>
      </div>
    </div>
  </div>
</section>
```

### Data Request Flow Diagram

```html
<section id="flow">
  <div class="section-header collapsed" onclick="toggleSection(this)">
    <span class="icon">🔄</span><h2>数据请求流程</h2><div class="toggle">▼</div>
  </div>
  <div class="section-body hidden">
    <p class="desc">{{flow description}}</p>
    <div class="flow-diagram">
      <div class="flow-row">
        <!-- flow-step nodes with flow-arrow between them -->
      </div>
    </div>
  </div>
</section>
```

### Vulnerability Card (with Fix Code)

```html
<div class="issue-card critical">
  <div class="ic-head">
    <span class="badge critical">严重</span>
    <span class="ic-title">{{title}}</span>
    <span class="ic-loc">{{file:line}}</span>
  </div>
  <div class="ic-desc">{{description}}</div>
  <div class="code-fix-pair">
    <div>
      <span class="code-fix-label vuln">⚠️ 漏洞代码</span>
      <pre class="code-block"><code><span class="err-line">{{vulnerable line}}</span>{{other lines}}</code></pre>
    </div>
    <div>
      <span class="code-fix-label fix">✅ 修复代码</span>
      <pre class="code-block"><code><span class="fix-line">{{fixed line}}</span>{{other lines}}</code></pre>
    </div>
  </div>
  <div class="ic-impact">影响：{{impact}}</div>
  <div class="ic-fix">修复：{{fix suggestion}}</div>
</div>
```

### Roadmap Phase (with Checkboxes)

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
      </div>
      <ul class="phase-items">
        <li onclick="toggleItem(this)">
          <span class="item-checkbox">✓</span>
          <span class="item-dot p0"></span>
          {{task description}}
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

### Incremental Comparison

```html
<div class="section" id="comparison">
  <div class="section-header collapsed" onclick="toggleSection(this)">
    <span class="icon">📊</span><h2>增量对比报告</h2>
    <span class="header-badge new">{{new_count}}</span>
    <span class="header-badge fixed">{{fixed_count}}</span>
    <div class="toggle">▼</div>
  </div>
  <div class="section-body hidden">
    <div class="note-block">上次报告时间：{{prev_date}} | 本次报告时间：{{curr_date}}</div>
    <h3>健康分数变化</h3>
    <table class="diff-table"><!-- diff rows --></table>
    <h3>🆕 新增问题</h3>
    <table class="diff-table"><!-- new issue rows --></table>
    <h3>✅ 已修复问题</h3>
    <table class="diff-table"><!-- fixed issue rows --></table>
    <h3>📝 仍存在的问题</h3>
    <table class="diff-table"><!-- remaining issue rows --></table>
  </div>
</div>
```

## Design Rules

1. **All sections collapsed by default**: `section-header` has class `collapsed`, `section-body` has class `hidden`
2. **Checkable Roadmap**: Clickable checkbox toggles completion state
3. **Progress Auto-update**: Checked/unchecked items update progress bars automatically
4. **localStorage Persistence**: Roadmap progress saved and restored on reload
5. **Code Highlighting**: `<pre class="code-block"><code>` with dark background
6. **Fix Code Side-by-Side**: Critical/High/Medium use `.code-fix-pair` grid
7. **Responsive**: Works on desktop and mobile
8. **Dark Mode**: CSS variables, toggled via `toggleTheme()`, persisted in localStorage
9. **Copy Button**: Each code block has hover copy button
10. **Navigation Sidebar**: Floating nav with scroll-spy
11. **Table Sort**: Clickable column headers
12. **Severity Badges in Headers**: Collapsed headers show issue count badges

## Split Generation Strategy

When HTML content exceeds ~50KB, use split-and-merge:

1. Create `summary-report-parts/` directory
2. Generate parts sequentially:
   - `part1-head-overview.html` — head, nav, header, overview, architecture, flow
   - `part2-task1.html` — Task 1: Feature tables and file lists
   - `part3-task2-3.html` — Task 2+3: Vulnerabilities, redundancy, logic issues
   - `part4-task4-5-compare-roadmap-js.html` — Task 4+5, comparison, roadmap, summary, script tag
3. Merge using PowerShell:
   ```powershell
   $parts = @("part1-head-overview.html","part2-task1.html","part3-task2-3.html","part4-task4-5-compare-roadmap-js.html")
   $content = ""; foreach ($p in $parts) { $content += (Get-Content $p -Raw -Encoding UTF8) + "`n" }
   [System.IO.File]::WriteAllText("summary-report.html", $content, [System.Text.Encoding]::UTF8)
   ```
4. Verify merged file starts with `<!DOCTYPE html>` and ends with `</html>`
5. Delete `summary-report-parts/` directory

## Self-Contained Mode (Optional)

If user requests a single portable file, inline CSS and JS:
```html
<style>/* paste report-assets/report.css here */</style>
<!-- ... -->
<script>/* paste report-assets/report.js here */</script>
```
