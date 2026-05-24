# 📋 Summary Report Skill — 使用文档

> 多项目通用的交互式项目审查报告生成器 v2.0
>
> **作者**：Luoci · 📧 [luociqaq@qq.com](mailto:luociqaq@qq.com) · 🐙 [GitHub](https://github.com/luociqvq)

## 核心特性

| 特性 | 说明 |
|------|------|
| 🧩 模板按需加载 | SKILL.md 仅 213 行轻量调度器，检测框架后按需加载对应模板，节省 60-70% token |
| 🔍 自动框架检测 | 7种后端 + 5种前端框架自动识别，动态加载对应分析模板 |
| 📝 双格式输出 | HTML 交互式报告 + Markdown 纯文本报告，用户未指定时交互选择 |
| 🔍 Skill 分析 | 除项目审查外，还可分析其他 Skill 的功能并生成结构化说明 |
| ⚡ 并行数据采集 | 4批次并行搜索，快速高效收集项目数据 |
| 🔒 漏洞+修复并排 | Critical/High/Medium漏洞附漏洞代码和修复代码双栏对比 |
| 🗺️ 可勾选路线图 | 分阶段优化路线，checkbox勾选，进度自动更新并持久化 |
| 🌙 暗色模式 | CSS变量驱动，一键切换，状态保存到localStorage |
| 📊 增量对比 | 与上次报告对比，标记新增🆕和已修复✅问题 |

## 触发方式

| 触发词 | 说明 | 示例 |
|--------|------|------|
| `总结` | 最常用触发词 | "帮我总结一下项目" |
| `生成总结` | 同上 | "生成总结报告" |
| `分析skill` | Skill 分析模式 | "分析这个skill是做什么的" |
| 指定格式 | 跳过格式选择 | "用 Markdown 格式总结项目" |

## 模板按需加载架构

### 设计理念

传统做法把所有框架的分析指令全部写在一个 SKILL.md 里，导致文件超过 800 行，Agent 一次性读取时容易丢失上下文、浪费 token、不同框架的指令互相干扰。

本 Skill 采用**模板按需加载架构**：

```
SKILL.md（轻量调度器，仅 213 行）
  ├── 只包含：触发条件、格式选择、框架检测表、模板加载指令、通用规则
  └── 不包含：任何框架专属的分析内容

analysis-templates/（框架专属模板，按需读取）
  ├── backend/thinkphp.md   ← 检测到 ThinkPHP 时才读取
  ├── backend/laravel.md    ← 检测到 Laravel 时才读取
  ├── frontend/vue3.md      ← 检测到 Vue 3 时才读取
  └── ...                   ← 其他框架同理
```

### 对比

| 对比项 | 全量加载（v1.0） | 按需加载（v2.0） |
|--------|---------|---------|
| SKILL.md 行数 | 800+ 行 | 213 行 |
| Token 消耗 | 全部模板一次性喂入 | 只加载 1-2 个相关模板 |
| 分析精准度 | 不同框架指令可能互相干扰 | 只看到当前框架的指令 |
| 维护成本 | 改一个框架要改大文件 | 新增框架只需加一个 .md 文件 |

## 完整工作流程

### 项目分析模式

```
1️⃣ 格式选择 → 2️⃣ 框架检测 → 3️⃣ 加载模板 → 4️⃣ 交互选板 → 5️⃣ 数据采集 → 6️⃣ 生成报告
```

1. **格式选择** — 如果用户没有指定输出格式，通过 AskUserQuestion 询问选择 HTML 还是 Markdown
2. **框架检测** — 并行 Glob 检测指示文件，识别前后端框架
3. **加载模板** — 根据检测结果，只读取对应的框架分析模板文件（如 `analysis-templates/backend/thinkphp.md`）
4. **交互选板** — 通过 AskUserQuestion 询问用户3个问题：需要哪些板块、额外内容、审查深度（快速/深度）
5. **数据采集** — 按模板中定义的搜索批次，并行采集项目数据
6. **生成报告** — 按报告模板（`report-templates/html-template.md` 或 `markdown-template.md`）生成最终输出

### Skill 分析模式

```
1️⃣ 格式选择 → 2️⃣ 读取目标 Skill → 3️⃣ 加载 Skill 分析模板 → 4️⃣ 分析提取 → 5️⃣ 生成报告
```

分析维度包括：基本信息、触发条件、核心功能、工作流程、输入输出、依赖项、限制与注意事项、使用建议。

## 输出结构

**目标项目输出（HTML 模式）：**

```
{project-root}/
├── summary-report.html         ← 最终报告（引用外部CSS/JS）
└── report-assets/
    ├── report.css              ← 从skill目录复制
    └── report.js               ← 从skill目录复制
```

**目标项目输出（Markdown 模式）：**

```
{project-root}/
└── summary-report.md           ← Markdown 纯文本报告
```

**Skill 源目录：**

```
.trae/skills/Summary-Report-Skill/
├── SKILL.md                    ← 轻量调度器（仅含通用逻辑和框架检测表）
├── explain.md                  ← 中文说明文档
├── analysis-templates/         ← 框架专属分析模板
│   ├── backend/                ← 后端框架模板（7个）
│   ├── frontend/               ← 前端框架模板（5个）
│   └── skill-analysis.md       ← Skill 分析模板
├── report-templates/           ← 报告输出模板
│   ├── html-template.md        ← HTML 报告结构
│   └── markdown-template.md    ← Markdown 报告结构
└── report-assets/              ← 报告静态资源
    ├── report.css              ← CSS模板
    └── report.js               ← JS模板
```

---

## 框架适配系统

Skill 会在数据采集前**首先检测项目的技术栈**，然后**按需加载**对应的框架分析模板。每个模板包含该框架特有的目录路径、搜索关键词、数据采集批次、漏洞模式等内容。

> ⚠️ **重要**：框架检测是数据采集的**第一步**。检测到框架后，Agent 会读取对应的模板文件，按模板中的指令执行分析。

### 后端框架检测（7种）

| 指示文件 | 框架 | 模板文件 |
|---------|------|---------|
| `server/think` + `server/app/controller/` | ThinkPHP | `backend/thinkphp.md` |
| `app/Http/Controllers/` + `artisan` | Laravel | `backend/laravel.md` |
| `app.py` / `manage.py` + `views.py` | Django / Flask | `backend/django.md` |
| `src/main/java/` + `pom.xml` / `build.gradle` | Spring Boot | `backend/spring-boot.md` |
| `index.js` / `app.js` + `package.json`(express) | Express | `backend/express.md` |
| `src/index.ts` + `nest-cli.json` | NestJS | `backend/express.md` |
| `server/` + `package.json`(无框架) | Node.js | `backend/generic-node.md` |
| `main.go` + `go.mod` | Go | `backend/go.md` |

### 前端框架检测（5种）

| 指示文件 | 框架 | 模板文件 |
|---------|------|---------|
| `vite.config.js` + `src/*.vue` | Vue 3 + Vite | `frontend/vue3.md` |
| `vue.config.js` + `src/*.vue` | Vue 2 + Webpack | `frontend/vue2.md` |
| `next.config.js` + `src/app/` | Next.js | `frontend/nextjs.md` |
| `react-scripts` in `package.json` | React CRA | `frontend/react.md` |
| `nuxt.config.ts` + `app.vue` | Nuxt 3 | `frontend/nuxt3.md` |

### 每个框架模板包含什么

| 章节 | 内容 |
|------|------|
| Framework Info | 框架名称、架构标签、检测指示文件 |
| Directory Paths | 该框架的标准目录路径（控制器、中间件、路由、模型等） |
| Search Keywords | 该框架特有的搜索关键词（DB查询模式、参数接收模式、文件上传模式等） |
| Data Collection Batches | 4个并行批次的搜索指令（项目结构、安全扫描、逻辑验证、冗余扫描） |
| Cross-Reference Rules | 交叉引用规则（字段匹配、路由匹配、组件匹配等） |
| Common Vulnerability Patterns | 该框架常见的漏洞模式及严重性等级 |
| Architecture Diagram Template | 架构图模板文本 |
| Data Flow Template | 数据请求流程模板文本 |

---

## 报告板块详解

用户可通过交互选择需要生成的板块。所有板块**默认折叠**，点击标题栏展开。

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

### 可选板块

| 板块 | 内容描述 |
|------|---------|
| 📊 概览 | 统计卡片网格 + 健康分数进度条（安全/逻辑/兼容/质量） |
| 🏗️ 架构 | Flexbox彩色盒子+箭头的系统架构图，分层展示 |
| 🔄 流程 | 请求生命周期流程图 |
| 📋 功能表 | 模块卡片 + API端点表 + 前后端文件列表 + 数据库表列表 |
| 🔒 漏洞 | 按严重性分级的漏洞（附修复代码）、冗余代码审查 |
| 🧠 逻辑 | 字段不匹配、数据流错误、业务逻辑缺陷 |
| 🔗 匹配 | 前后端兼容性表格，✅匹配/⚠️警告/❌错误状态 |
| 🚀 优化 | P0-P3优先级排序的优化建议 |
| 🗺️ 路线图 | 5阶段可勾选任务 + 自动进度条 + localStorage持久化 |
| 📊 对比 | 增量对比：健康分数变化+新增🆕/已修复✅/仍存在🔴问题 |

---

## 资源文件管理

> ✅ **多项目便携性**：CSS/JS模板存储在skill目录内，每次生成报告时自动复制到目标项目的 `report-assets/` 目录。换到其他项目时无需手动配置。

### 修改资源文件流程

1. 编辑 **skill目录** 中的 `.trae/skills/Summary-Report-Skill/report-assets/report.css` 或 `report.js`
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

1. 在 `analysis-templates/backend/` 或 `frontend/` 下创建新的 `.md` 文件
2. 按照统一结构编写：Framework Info → Directory Paths → Search Keywords → Data Collection Batches → Cross-Reference Rules → Common Vulnerability Patterns → Architecture/Flow Template
3. 在 SKILL.md 的框架检测表中添加一行：指示文件 → 框架名 → 模板文件路径
4. 无需修改其他任何文件

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

1. 在 `report-templates/html-template.md` 和 `markdown-template.md` 中添加新板块模板
2. 在 SKILL.md 的交互选板问题中添加新选项
3. 在 report.css 中添加新板块的CSS类
4. 在 HTML 模板的 nav-sidebar 中添加新导航项

---

## 常见问题

**❓ 换到 Python 后端项目能用吗？**

可以。Skill 会自动检测 `manage.py` / `app.py` / `views.py` 等指示文件，识别为 Django/Flask 后端，然后加载 `analysis-templates/backend/django.md` 模板，使用 Python 特定的搜索指令进行数据采集。

**❓ 框架检测不到怎么办？**

如果所有指示文件都不存在，Skill 会使用通用路径和关键词进行搜索。你可以在 `analysis-templates/backend/` 下创建新的模板文件，并在 SKILL.md 的检测表中添加新框架的指示文件来扩展支持。

**❓ 报告太大生成失败怎么办？**

Skill 自动使用分部分生成策略，将内容拆分为多个 part 文件分别生成，最后用 PowerShell 合并。如果仍然失败，可以选择"快速模式"减少板块数量。

**❓ 如何修改报告样式？**

编辑 `.trae/skills/Summary-Report-Skill/report-assets/report.css`，修改 `:root` 中的 CSS 变量即可全局调整颜色和间距，然后重新生成报告。

**❓ 增量对比是怎么工作的？**

每次打开报告时，`report.js` 会将当前问题列表和健康分数保存到 `localStorage`。下次生成报告时，Skill 读取上次的数据与当前数据对比，标记新增🆕和已修复✅的问题。

**❓ 可以只生成部分板块吗？**

可以。生成前会通过 AskUserQuestion 交互询问你需要哪些板块和深度，未选择的板块不会出现在报告中。

**❓ 如何添加对新框架的支持？**

只需3步：1) 在 `analysis-templates/` 下创建新模板文件；2) 在 SKILL.md 的检测表中添加一行；3) 完成。详见"自定义与扩展"章节。

**❓ Markdown 和 HTML 格式有什么区别？**

HTML 格式支持全部交互功能（折叠展开、暗色模式、路线图勾选、增量对比等），Markdown 格式包含所有核心内容但无交互功能，适合直接阅读或粘贴到文档中。

**❓ Skill 分析模式是什么？**

当你说"分析skill"时，Skill 会读取目标 Skill 的 SKILL.md，按照 `analysis-templates/skill-analysis.md` 模板提取其触发条件、核心功能、工作流程、输入输出等信息，生成结构化的功能说明报告。

---

## 更新日志

### v2.0 (2026-05-18)

- [重构] **模板按需加载架构**：SKILL.md 从 808 行瘦身至 213 行
  - 创建 `analysis-templates/backend/` 目录，包含 7 个后端框架模板（thinkphp/laravel/django/spring-boot/express/go/generic-node）
  - 创建 `analysis-templates/frontend/` 目录，包含 5 个前端框架模板（vue3/vue2/react/nextjs/nuxt3）
  - 创建 `analysis-templates/skill-analysis.md` Skill 分析模板
  - 创建 `report-templates/html-template.md` HTML 报告输出模板
  - 创建 `report-templates/markdown-template.md` Markdown 报告输出模板
  - SKILL.md 改为轻量调度器：先检测框架 → 再按需读取对应模板 → 按模板执行分析
  - 优势：节省约 60-70% token，避免无关框架模板干扰，分析结果更精准
- [新增] **AskUserQuestion 交互选择输出格式**（HTML/Markdown），用户未指定格式时自动询问
- [新增] **Skill 分析模式**，可分析其他 Skill 的 SKILL.md 并生成结构化功能说明报告
- [优化] 将 skill 名称从 html-summary-report 重命名为 Summary-Report-Skill
- [优化] 更新 SKILL.md 和 README.md 中的名称引用和目录路径

### v1.0 (2026-05-16)

- 初始版本：9+5框架适配、并行数据采集、漏洞+修复并排、可勾选路线图、暗色模式、增量对比、分部分生成、多项目便携
