# Nuxt 3 Analysis Template

## Framework Info

- **Framework**: Nuxt 3
- **Architecture Label**: Nuxt 3 SSR
- **Indicator Files**: `nuxt.config.ts` + `app.vue`

## Directory Paths

| Category | Path |
|---|---|
| Entry files | `app.vue` |
| Pages | `pages/*.vue` |
| Components | `components/*.vue` |
| Composables | `composables/*.ts` |
| Server API | `server/api/*.ts` |
| Store (Pinia) | `stores/*.ts` |
| Config | `nuxt.config.ts`, `.env.*` |
| Styles | `assets/css/`, `app.vue` |

## Data Collection Batches

### Batch 1: Project Structure

```
SearchCodebase: "Nuxt页面定义" → target: pages/
SearchCodebase: "Composable组合函数定义" → target: composables/
SearchCodebase: "Server API路由定义" → target: server/api/
Glob: "pages/*.vue"
Glob: "components/*.vue"
Glob: "composables/*.ts"
Glob: "server/api/*.ts"
```

### Batch 2: Security Scan

```
SearchCodebase: "v-html XSS 危险渲染" → target: ./
SearchCodebase: "localStorage sessionStorage 敏感数据存储" → target: ./
SearchCodebase: "hardcoded 硬编码 password secret token" → target: ./
SearchCodebase: "useFetch useAsyncData 服务端数据泄露" → target: ./
```

### Batch 3: Logic Validation

```
SearchCodebase: "API调用 useFetch $fetch 接口请求" → target: composables/ or pages/
Grep: "defineStore|useStore" → target: stores/
Grep: "useFetch|$fetch|useAsyncData" → target: ./
```

### Batch 4: Redundancy Scan

```
Glob: "pages/*.vue" → check all auto-registered routes
Glob: "components/*.vue" → check auto-imported components
SearchCodebase: "未使用的组件 孤立文件" → check usage vs actual files
```

## Cross-Reference Rules

1. Cross-reference page files with auto-registered routes → identify orphan pages
2. Cross-reference server API routes with frontend fetch calls → identify unused APIs
3. Cross-reference composables with page/component usage → identify unused composables
4. Check `v-html` usage for XSS vulnerabilities
5. Check `useFetch`/`useAsyncData` for sensitive data leakage to client
6. Check server middleware for auth protection

## Common Vulnerability Patterns

| Pattern | Severity | Description |
|---|---|---|
| `v-html` with user input | High | XSS vulnerability |
| Sensitive data in localStorage | Medium | Data exposure |
| Server-side data leaking to client | Critical | Data leakage |
| Missing server middleware auth | High | Authentication bypass |
| Hardcoded API keys | High | Credentials exposure |

## Architecture Diagram Template

```
Nuxt 3 SSR ⇄ Server API Routes (or External Backend)
     |
  Auto-imports ←→ File-based Routing
     |
  Composables ←→ Pinia Stores
```

## Data Flow Template

```
用户请求 → Nuxt Server → SSR渲染 → HTML响应
客户端交互 → Composable → useFetch/$fetch → Server API → 数据响应 → 组件更新
```

## File List Categories

- **Entry**: app.vue
- **Pages**: pages/*.vue
- **Components**: components/*.vue
- **Composables**: composables/*.ts
- **Server**: server/api/*.ts, server/middleware/*.ts
- **Store**: stores/*.ts
- **Config**: nuxt.config.ts, .env.*
- **Styles**: assets/css/
