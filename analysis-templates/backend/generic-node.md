# Generic Node.js Analysis Template

## Framework Info

- **Framework**: Node.js (No specific framework)
- **Architecture Label**: Node.js API Server
- **Indicator Files**: `server/` + `package.json` (no framework detected)

## Directory Paths

| Category | Path |
|---|---|
| Controller dir | `server/routes/` or `server/controllers/` |
| Middleware dir | `server/middleware/` |
| Route file | `server/app.js` or `server/index.js` |
| Model dir | `server/models/` |
| Config dir | `server/config/` or `server/.env` |
| SQL file | `server/migrations/` or `server/database.sql` |
| Env file | `server/.env` |
| Entry point | `server/index.js` or `server/app.js` |

## Search Keywords

| Category | Keywords |
|---|---|
| DB query pattern | `query` `execute` `find` `create` |
| Request param pattern | `req.body` `req.params` `req.query` |
| File upload pattern | `multer` `fs.writeFile` `formData` |
| HTTP client pattern | `axios` `fetch` `node-fetch` `http.request` |
| Auth pattern | `jwt` `bcrypt` `passport` `session` |

## Data Collection Batches

### Batch 1: Project Structure

```
SearchCodebase: "路由定义 app.get app.post router" → target: server/
SearchCodebase: "控制器和处理器定义" → target: server/controllers/ or server/routes/
SearchCodebase: "数据模型定义" → target: server/models/
Glob: "server/**/*.js"
Glob: "server/**/*.ts"
Glob: "server/models/*"
Glob: "server/config/*"
```

### Batch 2: Security Scan

```
SearchCodebase: "http.request axios fetch 外部HTTP请求" → target: server/
SearchCodebase: "multer fs.writeFile 文件上传" → target: server/
SearchCodebase: "req.body req.params 参数接收 无验证" → target: server/
SearchCodebase: "hardcoded 硬编码 password secret key" → target: server/
SearchCodebase: "jwt bcrypt passport auth 认证" → target: server/middleware/
```

### Batch 3: Logic Validation

```
SearchCodebase: "字段名不匹配 列不存在 database column mismatch" → compare model fields vs database
Grep: "query|execute|find|create|update|delete" → target: server/
Grep: "req.body|req.params|req.query" → target: server/
```

### Batch 4: Redundancy Scan

```
Glob: "server/**/*.js" → cross-reference with app.js routes
Glob: "server/models/*" → cross-reference with controller imports
SearchCodebase: "未使用的组件 孤立文件" → check route registrations vs actual files
```

## Cross-Reference Rules

1. Cross-reference model field names with database schema → identify field mismatches
2. Cross-reference route registrations with actual handler files → identify dead routes
3. Cross-reference model files with controller imports → identify unused models
4. Check `req.body` calls for missing validation
5. Check HTTP client calls for SSRF vulnerabilities
6. Check file upload handling for validation

## Common Vulnerability Patterns

| Pattern | Severity | Description |
|---|---|---|
| HTTP client with user-controlled URL | Critical | SSRF |
| File upload without validation | High | Unrestricted file upload |
| `req.body` without validation | Medium | Missing input validation |
| Raw SQL with string interpolation | Critical | SQL injection |
| `eval()` / `child_process` with user input | Critical | Code/command injection |
| Missing auth middleware | High | Authentication bypass |
| Hardcoded secrets | High | Credentials exposure |

## Architecture Diagram Template

```
Frontend SPA ⇄ Node.js API Server ⇄ Database
     |              |                  |
  localStorage   uploads/        migrations/
```

## Data Flow Template

```
前端组件 → API Service → Axios HTTP → CORS中间件 → Auth中间件 → Handler → Database → JSON响应
```
