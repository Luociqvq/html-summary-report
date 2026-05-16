# 📋 HTML Summary Report Skill — 使用文档

> 多项目通用的交互式项目审查报告生成器 v1.0
>
> **作者**：Luoci · 📧 [luociqaq@qq.com](mailto:luociqaq@qq.com) · 🐙 [GitHub](https://github.com/luo-ci)

## 核心特性

| 特性 | 说明 |
|------|------|
| 🔍 自动框架检测 | 9种后端 + 5种前端框架自动识别，动态调整搜索策略 |
| ⚡ 并行数据采集 | 4批次17路并行搜索，快速高效收集项目数据 |
| 🔒 漏洞+修复并排 | Critical/High/Medium漏洞附漏洞代码和修复代码双栏对比 |
| 🗺️ 可勾选路线图 | 分阶段优化路线，checkbox勾选，进度自动更新并持久化 |
| 🌙 暗色模式 | CSS变量驱动，一键切换，状态保存到localStorage |
| 📊 增量对比 | 与上次报告对比，标记新增🆕和已修复✅问题 |
| 🏗️ 架构图+流程图 | Flexbox盒子布局的系统架构图和数据请求流程图 |
| 📦 多项目便携 | CSS/JS模板存储在skill目录，自动复制到目标项目 |

## 触发方式

| 触发词 | 说明 | 示例 |
|--------|------|------|
| `总结` | 最常用触发词 | "帮我总结一下项目" |
| `生成总结` | 同上 | "生成总结报告" |
| HTML格式审查请求 | 任何暗示需要项目审查的请求 | "检查项目漏洞并生成HTML报告" |

## 完整工作流程

```
1️⃣ 框架检测 → 2️⃣ 交互选板 → 3️⃣ 并行采集 → 4️⃣ 生成HTML → 5️⃣ 输出报告
```

1. **框架检测** — 并行 Glob 检测指示文件（`server/think`, `artisan`, `manage.py`, `pom.xml`, `go.mod` 等），识别前后端框架，设置 `{{BACKEND_FRAMEWORK}}` 和 `{{TECH_STACK}}` 变量
2. **交互选板** — 通过 AskUserQuestion 询问用户3个问题：需要哪些板块、额外内容、审查深度（快速/深度）
3. **并行采集** — 根据检测到的框架，使用框架特定的关键词执行4批次并行搜索（项目结构5路 + 安全扫描5路 + 逻辑验证3路 + 冗余扫描4路）
4. **生成HTML** — 按模板生成HTML内容，超过50KB时自动分part生成后合并，复制CSS/JS到项目目录
5. **输出报告** — 清理临时part文件，最终输出 `summary-report.html` + `report-assets/` 目录

> 💡 **深度模式 vs 快速模式**：深度模式运行全部4批次（17路搜索），包含逻辑验证和冗余扫描；快速模式仅运行批次1+2（10路搜索），跳过深度逻辑验证，速度更快。

## 输出结构

**目标项目输出：**

```
{project-root}/
├── summary-report.html         ← 最终报告（引用外部CSS/JS）
└── report-assets/
    ├── report.css              ← 从skill目录复制
    └── report.js               ← 从skill目录复制
```

**Skill 源目录：**

```
.trae/skills/html-summary-report/
├── SKILL.md                    ← 指令文件（Agent读取执行）
├── DOCS.html                   ← HTML格式文档
├── DOCS.md                     ← Markdown格式文档
└── report-assets/
    ├── report.css              ← CSS模板（布局/组件/暗色/打印/响应式）
    └── report.js               ← JS模板（折叠/路线图/主题/复制/排序/对比）
```

**引用模式：**

| 模式 | 适用场景 | 引用方式 |
|------|---------|---------|
| 外部引用（默认） | 本地查看、常规使用 | `<link rel="stylesheet" href="report-assets/report.css">` |
| 内联模式 | 邮件发送、单文件分发 | `<style>/* CSS内容 */</style>` |

---

## 框架适配系统

Skill 会在数据采集前 **首先检测项目的技术栈**，然后根据检测结果 **动态调整** 搜索关键词和目录路径。

> ⚠️ **重要**：框架检测是数据采集的**第一步**。如果跳过此步骤，搜索关键词将默认使用 ThinkPHP 模式，导致其他框架项目采集不到数据。

### 后端框架检测（9种）

| 指示文件 | 框架 | 架构标签 |
|---------|------|---------|
| `server/think` + `server/app/controller/` | ThinkPHP | `ThinkPHP 8 API Server` |
| `app/Http/Controllers/` + `artisan` | Laravel | `Laravel API Server` |
| `src/Controller/` + `composer.json`(symfony) | Symfony | `Symfony API Server` |
| `app.py` / `manage.py` + `views.py` | Django / Flask | `Python API Server` |
| `src/main/java/` + `pom.xml` / `build.gradle` | Spring Boot | `Spring Boot API Server` |
| `index.js` / `app.js` + `package.json`(express) | Express | `Express API Server` |
| `src/index.ts` + `nest-cli.json` | NestJS | `NestJS API Server` |
| `server/` + `package.json`(无框架) | Node.js | `Node.js API Server` |
| `main.go` + `go.mod` | Go | `Go API Server` |

### 前端框架检测（5种）

| 指示文件 | 框架 | 架构标签 |
|---------|------|---------|
| `vite.config.js` + `src/*.vue` | Vue 3 + Vite | `Vue 3 SPA` |
| `vue.config.js` + `src/*.vue` | Vue 2 + Webpack | `Vue 2 SPA` |
| `next.config.js` + `src/app/` | Next.js | `Next.js SSR` |
| `react-scripts` in `package.json` | React CRA | `React SPA` |
| `nuxt.config.ts` + `app.vue` | Nuxt 3 | `Nuxt 3 SSR` |

### 搜索关键词映射

检测到框架后，所有搜索关键词和目录路径会 **自动替换** 为对应框架的模式：

| 类别 | ThinkPHP | Laravel | Django/Flask | Spring Boot | Express/NestJS | Go |
|------|----------|---------|-------------|-------------|----------------|-----|
| **控制器** | `server/app/controller/` | `app/Http/Controllers/` | `views.py` | `**/controller/` | `src/controllers/` | `internal/handler/` |
| **中间件** | `server/app/middleware/` | `app/Http/Middleware/` | `middleware.py` | `**/filter/` | `src/middleware/` | `internal/middleware/` |
| **路由** | `server/route/app.php` | `routes/api.php` | `urls.py` | `**/config/` | `src/routes/` | `internal/router/` |
| **模型** | `server/app/model/` | `app/Models/` | `models.py` | `**/entity/` | `src/models/` | `internal/model/` |
| **DB查询** | `Db::name` `->where` | `DB::table` `Model::where` | `objects.filter` | `repository.findById` | `Model.find` | `db.Query` |
| **参数接收** | `request->param` | `$request->input` | `request.POST` | `@RequestParam` | `req.body` | `c.Param` |
| **文件上传** | `request->file` | `$request->file` | `request.FILES` | `MultipartFile` | `multer` | `FormFile` |
| **HTTP客户端** | `file_get_contents` | `Http::get` | `requests.get` | `RestTemplate` | `axios` | `http.Get` |
| **认证** | `Auth.php` | `Auth::user` | `@login_required` | `@PreAuthorize` | `jwt.verify` | `jwt.Parse` |

### 框架特定数据采集示例

**Django/Flask：**

| 批次 | 搜索目标 |
|------|---------|
| Batch1 | `views.py`, `models.py`, `urls.py`, `settings.py`, `migrations/` |
| Batch2 | `requests.get`, `request.FILES`, `os.system`, `subprocess`, `eval(`, `exec(` |
| Batch3 | `objects.filter`, `objects.create`, `field mismatch`, `serializer` |
| Batch4 | `*/views.py`, `*/models.py`, `*/urls.py`, `*/migrations/*.py` |

**Spring Boot：**

| 批次 | 搜索目标 |
|------|---------|
| Batch1 | `controller/`, `entity/`, `repository/`, `application.yml`, `schema.sql` |
| Batch2 | `RestTemplate`, `@RequestParam`, `MultipartFile`, `Runtime.exec` |
| Batch3 | `findById`, `save`, `findBy`, `field mismatch`, `DTO` |
| Batch4 | `**/controller/*.java`, `**/entity/*.java`, `**/repository/*.java` |

**Express/NestJS：**

| 批次 | 搜索目标 |
|------|---------|
| Batch1 | `routes/`, `controllers/`, `models/`, `prisma/schema.prisma`, `app.js` |
| Batch2 | `eval(`, `child_process`, `fs.writeFile`, `req.body`, `multer` |
| Batch3 | `Model.find`, `Model.create`, `findByIdAndUpdate`, `field mismatch` |
| Batch4 | `src/**/*.controller.ts`, `src/**/*.module.ts`, `src/**/*.service.ts` |

---

## 报告板块详解

用户可通过交互选择需要生成的板块。所有板块 **默认折叠**，点击标题栏展开。

### 交互功能

| 功能 | 说明 |
|------|------|
| 📂 折叠/展开 | 点击标题栏切换，默认全部折叠，带旋转箭头动画 |
| 🗺️ 可勾选路线图 | checkbox勾选任务，进度条自动更新，localStorage持久化 |
| 🌙 暗色模式 | CSS变量驱动，一键切换，状态保存到localStorage |
| 📋 代码复制 | hover代码块显示复制按钮，点击复制到剪贴板 |
| 📊 表格排序 | 点击表头按列排序，支持升序/降序切换 |
| 🧭 导航侧边栏 | 浮动导航，滚动监听高亮当前板块 |
| 💾 进度持久化 | 路线图勾选状态保存到localStorage，刷新不丢失 |
| 📊 增量对比 | 与上次报告对比，标记新增🆕和已修复✅问题 |

### 板块模板与CSS类

| 板块 | 内容描述 | 关键CSS类 |
|------|---------|----------|
| 📊 概览 | 统计卡片网格 + 健康分数进度条（安全/逻辑/兼容/质量） | `.module-card` `.health-row` `.bar-fill` |
| 🏗️ 架构 | Flexbox彩色盒子+箭头的系统架构图，分层展示 | `.arch-diagram` `.arch-box.frontend/backend/data` |
| 🔄 流程 | 请求生命周期流程图 | `.flow-diagram` `.flow-node.client/middleware/server/db` |
| 📋 功能表 | 模块卡片：emoji+标题+摘要+能力标签chips+详细描述 | `.module-card` `.tag.blue/green/yellow/purple/red` |
| 🔌 API列表 | 方法(颜色编码)+路径+功能+参数的完整API表格 | `.api-table` `.method.get/post/put/delete` |
| 📁 文件列表 | 前后端文件表格：图标+文件名+完整路径+说明 | `.file-table` `.ft-icon/name/path/desc` |
| 🔒 漏洞 | 严重性卡片 + 漏洞代码/修复代码双栏并排对比 | `.issue-card.critical/high/medium` `.code-fix-pair` `.err-line` `.fix-line` |
| 🧠 逻辑 | 逻辑问题卡片 + 冗余代码/文件审查 | `.issue-card` `.code-fix-pair` |
| 🔗 匹配 | 前后端兼容性表格，✅匹配/⚠️警告/❌错误状态 | `.compat-table` `.status-ok/warn/err` |
| 🚀 优化 | P0-P3优先级排序的优化建议卡片 | `.module-card` |
| 📊 对比 | 增量对比：健康分数变化+新增🆕/已修复✅/仍存在🔴问题 | `.diff-table` `.diff-added/removed/changed/same` |
| 🗺️ 路线图 | 5阶段可勾选任务 + 自动进度条 + localStorage持久化 | `.roadmap` `.roadmap-phase/item` `.ri-priority.p0-p3` |

---

## 资源文件管理

> ✅ **多项目便携性**：CSS/JS模板存储在skill目录内，每次生成报告时自动复制到目标项目的 `report-assets/` 目录。换到其他项目时无需手动配置。

### 修改资源文件流程

1. 编辑 **skill目录** 中的 `.trae/skills/html-summary-report/report-assets/report.css` 或 `report.js`
2. 重新生成报告时，skill会自动将修改后的文件复制到目标项目的 `report-assets/` 目录
3. **不要**在SKILL.md中重复CSS/JS内容，该文件仅包含引用模式

---

## 分部分生成策略

当HTML内容超过 **~50KB** 时，自动使用分部分生成避免内容截断：

```
Part1(头部+概览+架构+流程) + Part2(功能表+API列表) + Part3(文件列表) + Part4(漏洞+逻辑) + Part5(匹配+优化+对比+路线图) → 合并+清理
```

1. 创建 `summary-report-parts/` 临时目录
2. 按顺序生成 p1.html ~ p5.html，每个part包含一段完整HTML片段
3. 使用 PowerShell 合并所有part为 `summary-report.html`（UTF-8编码）
4. 验证合并文件以 `<!DOCTYPE html>` 开头、`</html>` 结尾
5. 删除 `summary-report-parts/` 临时目录，仅保留最终报告

---

## 自定义与扩展

### 添加新框架支持

在 SKILL.md 的框架检测表中添加新行：

```
| `指示文件路径` | 框架名 | 架构标签 |
```

同时在"框架特定搜索关键词"映射表中添加对应的列，填入控制器目录、中间件目录、DB查询模式等信息。

### 修改报告样式

编辑 `report-assets/report.css` 中的 CSS 变量即可全局调整：

```css
:root {
  --bg: #f8fafc;        /* 页面背景 */
  --card: #fff;         /* 卡片背景 */
  --primary: #6366f1;   /* 主色调 */
  --radius: 12px;       /* 圆角大小 */
  --shadow: ...;        /* 阴影效果 */
}
```

### 添加新报告板块

1. 在 SKILL.md 的 "Report Structure" 中添加新板块描述
2. 在 "Pre-Generation: Interactive Section Selection" 中添加新选项
3. 在 report.css 中添加新板块的CSS类
4. 在 HTML 模板的 nav-sidebar 中添加新导航项

---

## 常见问题

**❓ 换到 Python 后端项目能用吗？**

可以。Skill 会自动检测 `manage.py` / `app.py` / `views.py` 等指示文件，识别为 Django/Flask 后端，然后使用 `objects.filter`、`request.POST`、`requests.get` 等 Python 特定关键词进行数据采集。

**❓ 框架检测不到怎么办？**

如果所有指示文件都不存在，Skill 会标记为 "Custom Backend"，使用通用路径和关键词进行搜索。你可以在 SKILL.md 的检测表中添加新框架的指示文件来扩展支持。

**❓ 报告太大生成失败怎么办？**

Skill 自动使用分部分生成策略，将内容拆分为多个 part 文件分别生成，最后用 PowerShell 合并。如果仍然失败，可以选择"快速模式"减少板块数量。

**❓ 如何修改报告样式？**

编辑 `.trae/skills/html-summary-report/report-assets/report.css`，修改 `:root` 中的 CSS 变量即可全局调整颜色和间距，然后重新生成报告。

**❓ 增量对比是怎么工作的？**

每次打开报告时，`report.js` 会将当前问题列表和健康分数保存到 `localStorage`。下次生成报告时，Skill 读取上次的数据与当前数据对比，标记新增🆕和已修复✅的问题。

**❓ 可以只生成部分板块吗？**

可以。生成前会通过 AskUserQuestion 交互询问你需要哪些板块和深度，未选择的板块不会出现在报告中。

**❓ 如何添加对新框架的支持？**

在 SKILL.md 的框架检测表中添加新行的指示文件路径和框架名，同时在搜索关键词映射表中添加对应的列。详见"自定义与扩展"章节。

---

## 版本历史

| 版本 | 日期 | 变更 |
|------|------|------|
| v1.0 | 2026-05-16 | 初始版本：9+5框架适配、并行数据采集、漏洞+修复并排、可勾选路线图、暗色模式、增量对比、分部分生成、多项目便携 |
