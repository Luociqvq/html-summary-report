# Summary Report Skill 说明文档

## 这是什么？

这是一个 Trae IDE 的 Skill 插件，用于生成**项目审查报告**或**Skill 功能分析报告**。支持 HTML 和 Markdown 两种输出格式。

## 核心设计理念：模板按需加载

传统做法是把所有框架的分析指令全部写在一个 SKILL.md 里，导致文件超过 800 行，Agent 一次性读取时容易丢失上下文、浪费 token、不同框架的指令互相干扰。

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

**优势**：

| 对比项 | 全量加载 | 按需加载 |
|--------|---------|---------|
| SKILL.md 行数 | 800+ 行 | 213 行 |
| Token 消耗 | 全部模板一次性喂入 | 只加载 1-2 个相关模板 |
| 分析精准度 | 不同框架指令可能互相干扰 | 只看到当前框架的指令 |
| 维护成本 | 改一个框架要改大文件 | 新增框架只需加一个 .md 文件 |

## 工作流程

### 项目分析模式

当用户说"总结"、"生成总结"时触发。

```
1️⃣ 格式选择 → 2️⃣ 框架检测 → 3️⃣ 加载模板 → 4️⃣ 交互选板 → 5️⃣ 数据采集 → 6️⃣ 生成报告
```

**详细步骤**：

1. **格式选择**：如果用户没有指定输出格式，通过 AskUserQuestion 工具询问选择 HTML 还是 Markdown
2. **框架检测**：同时运行多个 Glob 检查，识别项目使用的后端和前端框架
3. **加载模板**：根据检测结果，只读取对应的框架分析模板文件
4. **交互选板**：询问用户需要报告包含哪些板块、审查深度
5. **数据采集**：按模板中定义的搜索批次，并行采集项目数据
6. **生成报告**：按报告模板生成最终输出文件

### Skill 分析模式

当用户说"分析skill"、"这个skill是做什么的"时触发。

```
1️⃣ 格式选择 → 2️⃣ 读取目标 Skill 的 SKILL.md → 3️⃣ 加载 Skill 分析模板 → 4️⃣ 分析提取 → 5️⃣ 生成报告
```

分析维度包括：基本信息、触发条件、核心功能、工作流程、输入输出、依赖项、限制与注意事项、使用建议。

## 支持的框架

### 后端框架（7 个模板）

| 框架 | 检测方式 | 模板文件 |
|------|---------|---------|
| ThinkPHP | `server/think` + `server/app/controller/` | `backend/thinkphp.md` |
| Laravel | `app/Http/Controllers/` + `artisan` | `backend/laravel.md` |
| Django/Flask | `manage.py` / `app.py` + `views.py` | `backend/django.md` |
| Spring Boot | `src/main/java/` + `pom.xml` | `backend/spring-boot.md` |
| Express/NestJS | `package.json` (express) / `nest-cli.json` | `backend/express.md` |
| Node.js（通用） | `server/` + `package.json`（无框架） | `backend/generic-node.md` |
| Go | `main.go` + `go.mod` | `backend/go.md` |

### 前端框架（5 个模板）

| 框架 | 检测方式 | 模板文件 |
|------|---------|---------|
| Vue 3 + Vite | `vite.config.js` + `src/*.vue` | `frontend/vue3.md` |
| Vue 2 + Webpack | `vue.config.js` + `src/*.vue` | `frontend/vue2.md` |
| React | `react-scripts` in `package.json` | `frontend/react.md` |
| Next.js | `next.config.js` + `src/app/` | `frontend/nextjs.md` |
| Nuxt 3 | `nuxt.config.ts` + `app.vue` | `frontend/nuxt3.md` |

## 每个框架模板包含什么

每个框架模板都有统一的结构，确保分析思路一致：

| 章节 | 内容 |
|------|------|
| **Framework Info** | 框架名称、架构标签、检测指示文件 |
| **Directory Paths** | 该框架的标准目录路径（控制器、中间件、路由、模型等） |
| **Search Keywords** | 该框架特有的搜索关键词（数据库查询模式、参数接收模式、文件上传模式等） |
| **Data Collection Batches** | 4 个并行批次的搜索指令（项目结构、安全扫描、逻辑验证、冗余扫描） |
| **Cross-Reference Rules** | 交叉引用规则（字段匹配、路由匹配、组件匹配等） |
| **Common Vulnerability Patterns** | 该框架常见的漏洞模式及严重性等级 |
| **Architecture Diagram Template** | 架构图模板文本 |
| **Data Flow Template** | 数据请求流程模板文本 |

## 报告输出

### HTML 格式

生成交互式 HTML 报告，功能包括：
- 所有板块默认折叠，点击展开
- 暗色模式切换
- 可勾选的优化路线图，进度自动更新
- 漏洞代码与修复代码并排对比
- 增量对比（与上次报告比较）
- 浮动导航栏

输出文件：`summary-report.html` + `report-assets/` 目录

### Markdown 格式

生成纯文本 Markdown 报告，适合直接阅读或粘贴到文档中。

输出文件：`summary-report.md`

## 报告板块

用户可以选择包含以下板块：

| 板块 | 内容 |
|------|------|
| 📊 项目概览 | 统计卡片 + 健康分数（安全性/逻辑性/兼容性/代码质量） |
| 🏗️ 系统架构 | 前后端分层架构图 |
| 🔄 请求流程 | 完整数据请求生命周期流程图 |
| 📋 功能表与文件列表 | 模块卡片、API端点表、前后端文件列表、数据库表列表 |
| 🔒 漏洞与冗余审查 | 按严重性分级的漏洞（附修复代码）、冗余代码审查 |
| 🧠 逻辑问题检查 | 字段不匹配、数据流错误、业务逻辑缺陷 |
| 🔗 运行匹配验证 | 前后端兼容性验证、配置对齐 |
| 🚀 优化评估建议 | P0-P3 优先级排序的优化建议 |
| 📌 优化路线图 | 可勾选的分阶段实施路线图 |
| 📊 增量对比 | 与上次报告对比，标记新增/已修复/变化项 |

## 目录结构

```
Summary-Report-Skill/
├── SKILL.md                    ← 轻量调度器（仅含通用逻辑和框架检测表）
├── explain.md                  ← 本说明文档
├── analysis-templates/         ← 框架专属分析模板
│   ├── backend/                ← 后端框架模板
│   │   ├── thinkphp.md
│   │   ├── laravel.md
│   │   ├── django.md
│   │   ├── spring-boot.md
│   │   ├── express.md
│   │   ├── go.md
│   │   └── generic-node.md
│   ├── frontend/               ← 前端框架模板
│   │   ├── vue3.md
│   │   ├── vue2.md
│   │   ├── react.md
│   │   ├── nextjs.md
│   │   └── nuxt3.md
│   └── skill-analysis.md       ← Skill 分析模板
├── report-templates/           ← 报告输出模板
│   ├── html-template.md        ← HTML 报告结构
│   └── markdown-template.md    ← Markdown 报告结构
├── report-assets/              ← 报告静态资源
│   ├── report.css              ← 样式文件
│   └── report.js               ← 交互逻辑
├── screenshots/                ← 报告截图
├── DOCS.html                   ← HTML 格式使用文档
├── DOCS.md                     ← Markdown 格式使用文档
├── README.md                   ← 项目说明
├── LICENSE                     ← 许可证
└── summary-report_example.html ← 示例报告
```

## 如何新增框架支持

1. 在 `analysis-templates/backend/` 或 `frontend/` 下创建新的 `.md` 文件
2. 按照统一结构编写：Framework Info → Directory Paths → Search Keywords → Data Collection Batches → Cross-Reference Rules → Common Vulnerability Patterns → Architecture/Flow Template
3. 在 SKILL.md 的框架检测表中添加一行：指示文件 → 框架名 → 模板文件路径
4. 无需修改其他任何文件

## 触发方式

| 触发词 | 示例 |
|--------|------|
| `总结` | "帮我总结一下项目" |
| `生成总结` | "生成总结报告" |
| `分析skill` | "分析这个skill是做什么的" |
| 指定格式 | "用 Markdown 格式总结项目" |

## 作者

**Luoci**
- 📧 Email: luociqaq@qq.com
- 🐙 GitHub: https://github.com/luociqvq
