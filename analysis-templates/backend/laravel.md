# Laravel Analysis Template

## Framework Info

- **Framework**: Laravel
- **Architecture Label**: Laravel API Server
- **Indicator Files**: `app/Http/Controllers/` + `artisan`

## Directory Paths

| Category | Path |
|---|---|
| Controller dir | `app/Http/Controllers/` |
| Middleware dir | `app/Http/Middleware/` |
| Route file | `routes/api.php` |
| Model dir | `app/Models/` |
| Config dir | `config/` |
| SQL file | `database/migrations/` |
| Env file | `.env` |
| Entry point | `public/index.php` |

## Search Keywords

| Category | Keywords |
|---|---|
| DB query pattern | `DB::table` `Model::where` `Model::create` |
| Request param pattern | `$request->input` `$request->file` |
| File upload pattern | `$request->file` `Storage::put` |
| HTTP client pattern | `Http::get` `Http::post` |
| Auth pattern | `Auth::user` `auth middleware` |

## Data Collection Batches

### Batch 1: Project Structure

```
SearchCodebase: "路由定义和API端点注册" → target: routes/
SearchCodebase: "控制器类和方法定义" → target: app/Http/Controllers/
SearchCodebase: "数据库迁移 Schema create" → target: database/migrations/
Glob: "app/Http/Controllers/*.php"
Glob: "app/Models/*.php"
Glob: "app/Http/Middleware/*.php"
Glob: "config/*.php"
```

### Batch 2: Security Scan

```
SearchCodebase: "Http::get Http::post 外部HTTP请求" → target: app/
SearchCodebase: "$request->file Storage::put 文件上传" → target: app/
SearchCodebase: "$request->input 参数接收 无验证" → target: app/Http/Controllers/
SearchCodebase: "hardcoded 硬编码 password secret key" → target: app/
SearchCodebase: "Auth middleware 中间件" → target: app/Http/Middleware/
```

### Batch 3: Logic Validation

```
SearchCodebase: "字段名不匹配 列不存在 database column mismatch" → compare controller fields vs migrations
Grep: "DB::table|Model::where|Model::create|->insert|->update" → target: app/Http/Controllers/
Grep: "$request->input|$request->file" → target: app/Http/Controllers/
```

### Batch 4: Redundancy Scan

```
Glob: "app/Http/Controllers/*.php" → cross-reference with routes/api.php
Glob: "app/Models/*.php" → cross-reference with controller imports
SearchCodebase: "未使用的组件 孤立文件" → check route imports vs actual files
```

## Cross-Reference Rules

1. Cross-reference controller field names with migration column names → identify field mismatches
2. Cross-reference `routes/api.php` paths with actual controller methods → identify dead routes
3. Cross-reference model files with controller imports → identify unused models
4. Check `$request->input()` calls for missing validation (FormRequest)
5. Check `Http::get/post()` for SSRF vulnerabilities
6. Check `$request->file()` calls for upload validation

## Common Vulnerability Patterns

| Pattern | Severity | Description |
|---|---|---|
| `Http::get($userInput)` | Critical | SSRF - user-controlled URL |
| `$request->file()` without validation | High | Unrestricted file upload |
| `$request->input()` without FormRequest | Medium | Missing input validation |
| `DB::raw()` with user input | Critical | SQL injection |
| Hardcoded credentials in .env committed | High | Credentials exposure |
| Missing auth middleware on routes | High | Authentication bypass |

## Architecture Diagram Template

```
Frontend SPA ⇄ Laravel API Server ⇄ MySQL
     |              |                  |
  localStorage   Storage::    database/migrations/
```

## Data Flow Template

```
前端组件 → API Service → Axios HTTP → CORS中间件 → Auth中间件 → Controller → Eloquent/DB → JSON响应
```
