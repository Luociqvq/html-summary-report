# Go Analysis Template

## Framework Info

- **Framework**: Go (Gin/Echo/Fiber/Standard)
- **Architecture Label**: Go API Server
- **Indicator Files**: `main.go` + `go.mod`

## Directory Paths

| Category | Path |
|---|---|
| Controller dir | `internal/handler/` or `*/handler/` |
| Middleware dir | `internal/middleware/` |
| Route file | `internal/router/` |
| Model dir | `internal/model/` |
| Config dir | `config/` or `.env` |
| SQL file | `migrations/` |
| Env file | `.env` |
| Entry point | `main.go` |

## Search Keywords

| Category | Keywords |
|---|---|
| DB query pattern | `db.Query` `db.Exec` |
| Request param pattern | `c.Param` `c.Query` `c.Bind` |
| File upload pattern | `FormFile` `os.Create` |
| HTTP client pattern | `http.Get` `http.Post` |
| Auth pattern | `jwt.Parse` `middleware` |

## Data Collection Batches

### Batch 1: Project Structure

```
SearchCodebase: "路由定义 Handle Func Group" → target: internal/router/ or main.go
SearchCodebase: "Handler 处理函数定义" → target: internal/handler/
SearchCodebase: "Model Struct 数据模型" → target: internal/model/
Glob: "**/handler/*.go"
Glob: "**/model/*.go"
Glob: "**/middleware/*.go"
Glob: "**/router/*.go"
```

### Batch 2: Security Scan

```
SearchCodebase: "http.Get http.Post 外部HTTP请求" → target: ./
SearchCodebase: "FormFile os.Create 文件上传" → target: ./
SearchCodebase: "c.Param c.Query c.Bind 参数接收 无验证" → target: internal/handler/
SearchCodebase: "hardcoded 硬编码 password secret key" → target: ./
SearchCodebase: "jwt.Parse middleware Auth 认证" → target: internal/middleware/
```

### Batch 3: Logic Validation

```
SearchCodebase: "字段名不匹配 列不存在 database column mismatch" → compare struct fields vs migrations
Grep: "db.Query|db.Exec|db.QueryRow" → target: internal/handler/
Grep: "c.Param|c.Query|c.Bind|c.JSON" → target: internal/handler/
```

### Batch 4: Redundancy Scan

```
Glob: "**/handler/*.go" → cross-reference with router
Glob: "**/model/*.go" → cross-reference with handler imports
SearchCodebase: "未使用的组件 孤立文件" → check router registrations vs actual handlers
```

## Cross-Reference Rules

1. Cross-reference struct field names with migration column names → identify field mismatches
2. Cross-reference router registrations with actual handler functions → identify dead routes
3. Cross-reference model files with handler imports → identify unused models
4. Check `c.Bind()` calls for missing validation (validator tags)
5. Check `http.Get/Post()` for SSRF vulnerabilities
6. Check `FormFile` calls for upload validation

## Common Vulnerability Patterns

| Pattern | Severity | Description |
|---|---|---|
| `http.Get(userInput)` | Critical | SSRF - user-controlled URL |
| `FormFile` without validation | High | Unrestricted file upload |
| `c.Bind()` without struct tags | Medium | Missing input validation |
| Raw SQL with string concatenation | Critical | SQL injection |
| `os.Exec()` with user input | Critical | Command injection |
| Missing auth middleware on routes | High | Authentication bypass |
| Hardcoded secrets in source code | High | Credentials exposure |

## Architecture Diagram Template

```
Frontend SPA ⇄ Go API Server ⇄ PostgreSQL/MySQL
     |              |                |
  localStorage   uploads/       migrations/
```

## Data Flow Template

```
前端组件 → API Service → Axios HTTP → CORS中间件 → Auth中间件 → Handler → DB Query → JSON响应
```
