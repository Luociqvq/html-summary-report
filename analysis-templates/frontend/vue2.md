# Vue 2 Analysis Template

## Framework Info

- **Framework**: Vue 2 + Webpack
- **Architecture Label**: Vue 2 SPA
- **Indicator Files**: `vue.config.js` + `src/*.vue`

## Directory Paths

| Category | Path |
|---|---|
| Entry files | `src/main.js`, `src/App.vue` |
| Views | `src/views/` |
| Components | `src/components/` |
| Router | `src/router/index.js` |
| Store (Vuex) | `src/store/` |
| API layer | `src/api/` or `src/utils/request.js` |
| Config | `vue.config.js`, `.env.*` |
| Styles | `src/assets/`, `src/style/` |

## Data Collection Batches

### Batch 1: Project Structure

```
SearchCodebase: "Vue组件和视图定义" → target: src/views/
SearchCodebase: "Vuex store状态管理 state mutations actions" → target: src/store/
SearchCodebase: "路由定义 router routes" → target: src/router/
Glob: "src/views/*.vue"
Glob: "src/components/*.vue"
Glob: "src/store/*.js"
Glob: "src/api/*.js"
```

### Batch 2: Security Scan

```
SearchCodebase: "v-html XSS 危险渲染" → target: src/
SearchCodebase: "localStorage sessionStorage 敏感数据存储" → target: src/
SearchCodebase: "hardcoded 硬编码 password secret token" → target: src/
```

### Batch 3: Logic Validation

```
SearchCodebase: "API调用 接口请求 axios fetch" → target: src/api/ or src/store/
Grep: "state|mutations|actions|getters" → target: src/store/
Grep: "import.*views|import.*components" → target: src/router/
```

### Batch 4: Redundancy Scan

```
Glob: "src/views/*.vue" → cross-reference with router/index.js
Glob: "src/components/*.vue" → cross-reference with view imports
SearchCodebase: "未使用的组件 孤立文件" → check router imports vs actual files
```

## Cross-Reference Rules

1. Cross-reference Vue component imports with router registrations → identify orphan components
2. Cross-reference Vuex store usage with actual store files → identify unused modules
3. Cross-reference API function calls with actual API endpoint definitions → identify dead API calls
4. Check `v-html` usage for XSS vulnerabilities
5. Check `localStorage` usage for sensitive data storage
6. Check router `beforeEach` for missing auth guards

## Common Vulnerability Patterns

| Pattern | Severity | Description |
|---|---|---|
| `v-html` with user input | High | XSS vulnerability |
| Sensitive data in localStorage | Medium | Data exposure |
| Missing router auth guards | High | Authentication bypass on frontend |
| Hardcoded API keys in source | High | Credentials exposure |
| Deprecated Vue 2 APIs | Low | Maintenance risk |

## Architecture Diagram Template

```
Vue 2 SPA (Webpack) ⇄ Backend API Server
     |
  Vuex Store ←→ Vue Router
     |
  API Layer (Axios)
```

## Data Flow Template

```
Vue组件 → Vuex Action → Axios HTTP → Backend API → JSON响应 → Mutation → State更新 → 组件响应式更新
```

## File List Categories

- **Entry**: main.js, App.vue
- **Router**: router/index.js
- **Store**: store/index.js, store/modules/*.js
- **API**: api/*.js, utils/request.js
- **Views**: views/*.vue
- **Components**: components/*.vue
- **Config**: vue.config.js, .env.*, babel.config.js
- **Styles**: assets/css/, style/
