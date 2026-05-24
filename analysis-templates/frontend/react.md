# React Analysis Template

## Framework Info

- **Framework**: React (CRA / Vite)
- **Architecture Label**: React SPA
- **Indicator Files**: `react-scripts` in `package.json` OR `vite.config.js` + `src/*.jsx`

## Directory Paths

| Category | Path |
|---|---|
| Entry files | `src/index.js`, `src/App.jsx` |
| Pages | `src/pages/` or `src/views/` |
| Components | `src/components/` |
| Router | `src/router/` or `src/App.jsx` |
| State (Redux/Zustand) | `src/store/` or `src/stores/` |
| API layer | `src/api/` or `src/services/` |
| Config | `vite.config.js` or `package.json` |
| Styles | `src/assets/`, `src/styles/` |

## Data Collection Batches

### Batch 1: Project Structure

```
SearchCodebase: "React组件定义 function component" → target: src/pages/ or src/views/
SearchCodebase: "状态管理 Redux Zustand useStore" → target: src/store/ or src/stores/
SearchCodebase: "路由定义 Route Routes" → target: src/router/ or src/App.jsx
Glob: "src/pages/*.jsx" or "src/views/*.jsx"
Glob: "src/components/*.jsx"
Glob: "src/store/*.js" or "src/stores/*.js"
Glob: "src/api/*.js" or "src/services/*.js"
```

### Batch 2: Security Scan

```
SearchCodebase: "dangerouslySetInnerHTML XSS 危险渲染" → target: src/
SearchCodebase: "localStorage sessionStorage 敏感数据存储" → target: src/
SearchCodebase: "hardcoded 硬编码 password secret token" → target: src/
```

### Batch 3: Logic Validation

```
SearchCodebase: "API调用 接口请求 axios fetch" → target: src/api/ or src/services/
Grep: "useSelector|useDispatch|createSlice|createStore" → target: src/store/
Grep: "Route|Routes|useNavigate|useParams" → target: src/
```

### Batch 4: Redundancy Scan

```
Glob: "src/pages/*.jsx" → cross-reference with router
Glob: "src/components/*.jsx" → cross-reference with page imports
SearchCodebase: "未使用的组件 孤立文件" → check route imports vs actual files
```

## Cross-Reference Rules

1. Cross-reference component imports with route definitions → identify orphan components
2. Cross-reference store usage with actual store files → identify unused slices
3. Cross-reference API function calls with actual service definitions → identify dead API calls
4. Check `dangerouslySetInnerHTML` usage for XSS vulnerabilities
5. Check `localStorage` usage for sensitive data storage
6. Check route protection for missing auth guards

## Common Vulnerability Patterns

| Pattern | Severity | Description |
|---|---|---|
| `dangerouslySetInnerHTML` with user input | High | XSS vulnerability |
| Sensitive data in localStorage | Medium | Data exposure |
| Missing route protection | High | Authentication bypass |
| Hardcoded API keys in source | High | Credentials exposure |
| Missing useEffect cleanup | Low | Memory leak |

## Architecture Diagram Template

```
React SPA (Vite/CRA) ⇄ Backend API Server
     |
  Redux/Zustand ←→ React Router
     |
  API Layer (Axios/Fetch)
```

## Data Flow Template

```
React组件 → Store Action/Dispatch → Axios HTTP → Backend API → JSON响应 → State更新 → 组件重渲染
```

## File List Categories

- **Entry**: index.js, App.jsx
- **Router**: router/index.jsx
- **Store**: store/index.js, store/slices/*.js
- **API**: api/*.js, services/*.js
- **Pages**: pages/*.jsx
- **Components**: components/*.jsx
- **Config**: vite.config.js, package.json
- **Styles**: assets/css/, styles/
