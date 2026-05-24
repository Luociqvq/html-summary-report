# ThinkPHP Analysis Template

## Framework Info

- **Framework**: ThinkPHP 8
- **Architecture Label**: ThinkPHP 8 API Server
- **Indicator Files**: `server/think` + `server/app/controller/`

## Directory Paths

| Category | Path |
|---|---|
| Controller dir | `server/app/controller/` |
| Middleware dir | `server/app/middleware/` |
| Route file | `server/route/app.php` |
| Model dir | `server/app/model/` |
| Config dir | `server/config/` |
| SQL file | `server/database.sql` |
| Env file | `server/.env` |
| Entry point | `server/public/index.php` |

## Search Keywords

| Category | Keywords |
|---|---|
| DB query pattern | `Db::name` `->where` `->insert` `->update` |
| Request param pattern | `request->param` `request->file` |
| File upload pattern | `request->file` `Filesystem::putFile` |
| HTTP client pattern | `file_get_contents` `curl_exec` |
| Auth pattern | `middleware/Auth.php` |

## Data Collection Batches

### Batch 1: Project Structure

```
SearchCodebase: "路由定义和API端点注册" → target: server/route/
SearchCodebase: "控制器类和方法定义" → target: server/app/controller/
SearchCodebase: "数据库表结构定义 CREATE TABLE" → target: server/database.sql
Glob: "server/app/controller/*.php"
Glob: "server/app/model/*.php"
Glob: "server/app/middleware/*.php"
Glob: "server/config/*.php"
```

### Batch 2: Security Scan

```
SearchCodebase: "file_get_contents curl_exec HTTP请求外部URL" → target: server/app/
SearchCodebase: "file upload 文件上传 request->file putFile" → target: server/app/
SearchCodebase: "request->param 参数接收 无验证" → target: server/app/controller/
SearchCodebase: "hardcoded 硬编码 password secret key" → target: server/
SearchCodebase: "middleware 中间件 Auth Cors" → target: server/app/middleware/
```

### Batch 3: Logic Validation

```
SearchCodebase: "字段名不匹配 列不存在 database column mismatch" → compare controller fields vs database.sql
Grep: "Db::name|->insert|->update|->where" → target: server/app/controller/
Grep: "request->param" → target: server/app/controller/
```

### Batch 4: Redundancy Scan

```
Glob: "server/app/controller/*.php" → cross-reference with route/app.php
Glob: "server/app/model/*.php" → cross-reference with controller imports
SearchCodebase: "未使用的组件 孤立文件" → check router imports vs actual files
```

## Cross-Reference Rules

1. Cross-reference controller field names with `database.sql` column names → identify field mismatches
2. Cross-reference `route/app.php` paths with actual controller methods → identify dead routes
3. Cross-reference model files with controller imports → identify unused models
4. Check `request->param()` calls for missing validation
5. Check `file_get_contents()` / `curl_exec()` for SSRF vulnerabilities
6. Check `request->file()` calls for upload validation (file type, size limits)

## Common Vulnerability Patterns

| Pattern | Severity | Description |
|---|---|---|
| `file_get_contents($userInput)` | Critical | SSRF - user-controlled URL |
| `request->file()` without validation | High | Unrestricted file upload |
| `request->param()` without validation | Medium | Missing input validation |
| `Db::name()` with raw user input | High | SQL injection risk |
| Hardcoded credentials in config | High | Credentials exposure |
| Missing Auth middleware on routes | High | Authentication bypass |

## Architecture Diagram Template

```
Vue 3 SPA ⇄ ThinkPHP 8 API Server ⇄ MySQL
   |              |                    |
localStorage   public/storage      database.sql
```

## Data Flow Template

```
Vue组件 → Pinia Store → Axios HTTP → CORS中间件 → Auth中间件 → Controller → MySQL → JSON响应
```
