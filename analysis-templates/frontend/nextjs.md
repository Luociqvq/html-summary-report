# Next.js Analysis Template

## Framework Info

- **Framework**: Next.js
- **Architecture Label**: Next.js SSR
- **Indicator Files**: `next.config.js` + `src/app/`

## Directory Paths

| Category | Path |
|---|---|
| Entry files | `src/app/layout.tsx`, `src/app/page.tsx` |
| Pages (App Router) | `src/app/*/page.tsx` |
| Pages (Pages Router) | `src/pages/*.tsx` |
| Components | `src/components/` |
| API Routes | `src/app/api/*/route.ts` or `src/pages/api/*.ts` |
| State | `src/store/` or `src/stores/` |
| Config | `next.config.js`, `.env.*` |
| Styles | `src/app/globals.css`, `src/styles/` |

## Data Collection Batches

### Batch 1: Project Structure

```
SearchCodebase: "Next.js页面和布局定义" → target: src/app/
SearchCodebase: "API路由定义 Route Handler" → target: src/app/api/ or src/pages/api/
SearchCodebase: "状态管理 store" → target: src/store/
Glob: "src/app/**/page.tsx"
Glob: "src/app/**/layout.tsx"
Glob: "src/components/*.tsx"
Glob: "src/app/api/**/route.ts"
```

### Batch 2: Security Scan

```
SearchCodebase: "dangerouslySetInnerHTML XSS 危险渲染" → target: src/
SearchCodebase: "localStorage sessionStorage 敏感数据存储" → target: src/
SearchCodebase: "hardcoded 硬编码 password secret token" → target: src/
SearchCodebase: "Server Component 客户端数据泄露" → target: src/app/
```

### Batch 3: Logic Validation

```
SearchCodebase: "API调用 fetch 接口请求" → target: src/
Grep: "use client|use server" → target: src/
Grep: "getServerSideProps|getStaticProps" → target: src/pages/
```

### Batch 4: Redundancy Scan

```
Glob: "src/app/**/page.tsx" → check all routes
Glob: "src/components/*.tsx" → cross-reference with page imports
SearchCodebase: "未使用的组件 孤立文件" → check imports vs actual files
```

## Cross-Reference Rules

1. Cross-reference page components with app router structure → identify orphan pages
2. Cross-reference API routes with frontend fetch calls → identify unused APIs
3. Check `use client` boundary for unnecessary client components
4. Check `dangerouslySetInnerHTML` usage for XSS
5. Check server components for client-side data leakage
6. Check middleware.ts for auth protection

## Common Vulnerability Patterns

| Pattern | Severity | Description |
|---|---|---|
| `dangerouslySetInnerHTML` with user input | High | XSS vulnerability |
| Sensitive data in localStorage | Medium | Data exposure |
| Server Component leaking secrets | Critical | Data leakage to client |
| Missing middleware auth | High | Authentication bypass |
| Hardcoded API keys | High | Credentials exposure |
| Unnecessary `use client` | Low | Performance degradation |

## Architecture Diagram Template

```
Next.js SSR ⇄ Backend API Server (or self-hosted API Routes)
     |
  App Router ←→ Server Components
     |
  Client Components ←→ API Routes
```

## Data Flow Template

```
用户请求 → Next.js Server → Server Component渲染 → HTML响应
客户端交互 → Client Component → API Route/Fetch → 数据响应 → 组件更新
```

## File List Categories

- **Entry**: app/layout.tsx, app/page.tsx
- **Pages**: app/*/page.tsx
- **API Routes**: app/api/*/route.ts
- **Components**: components/*.tsx
- **Store**: store/*.ts
- **Config**: next.config.js, .env.*, middleware.ts
- **Styles**: app/globals.css, styles/
