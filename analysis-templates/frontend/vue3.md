# Vue 3 Analysis Template

## Framework Info

- **Framework**: Vue 3 + Vite
- **Architecture Label**: Vue 3 SPA
- **Indicator Files**: `vite.config.js` + `src/*.vue`

## Directory Paths

| Category | Path |
|---|---|
| Entry files | `src/main.js`, `src/App.vue` |
| Views | `src/views/` |
| Components | `src/components/` |
| Router | `src/router/index.js` |
| Stores (Pinia) | `src/stores/` |
| API layer | `src/api/` or `src/utils/request.js` |
| Config | `vite.config.js`, `.env.*` |
| Styles | `src/assets/`, `src/style/` |

## Data Collection Batches

### Batch 1: Project Structure

```
SearchCodebase: "Vue组件和视图定义" → target: src/views/
SearchCodebase: "Pinia store状态管理 defineStore" → target: src/stores/
SearchCodebase: "路由定义 router routes" → target: src/router/
Glob: "src/views/*.vue"
Glob: "src/components/*.vue"
Glob: "src/stores/*.js"
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
SearchCodebase: "API调用 接口请求 axios fetch" → target: src/api/ or src/stores/
Grep: "defineStore|useStore|storeToRefs" → target: src/stores/
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
2. Cross-reference Pinia store usage with actual store files → identify unused stores
3. Cross-reference API function calls with actual API endpoint definitions → identify dead API calls
4. Check `v-html` usage for XSS vulnerabilities
5. Check `localStorage` usage for sensitive data storage
6. Check router guards for missing auth checks

## Common Vulnerability Patterns

| Pattern | Severity | Description |
|---|---|---|
| `v-html` with user input | High | XSS vulnerability |
| Sensitive data in localStorage | Medium | Data exposure |
| Missing router auth guards | High | Authentication bypass on frontend |
| Hardcoded API keys in source | High | Credentials exposure |
| Missing error boundary | Low | Unhandled component errors |

## Architecture Diagram Template

```
Vue 3 SPA (Vite) ⇄ Backend API Server
     |
  Pinia Stores ←→ Vue Router
     |
  API Layer (Axios)
```

## Data Flow Template

```
Vue组件 → Pinia Store → Axios HTTP → Backend API → JSON响应 → Store更新 → 组件响应式更新
```

## File List Categories

- **Entry**: main.js, App.vue
- **Router**: router/index.js
- **Stores**: stores/*.js
- **API**: api/*.js, utils/request.js
- **Views**: views/*.vue
- **Components**: components/*.vue
- **Config**: vite.config.js, .env.*, index.html
- **Styles**: assets/css/, style/
