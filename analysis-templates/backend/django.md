# Django/Flask Analysis Template

## Framework Info

- **Framework**: Django/Flask
- **Architecture Label**: Python API Server
- **Indicator Files**: `app.py` / `manage.py` + `views.py`

## Directory Paths

| Category | Path |
|---|---|
| Controller dir | `views.py` or `*/views/` |
| Middleware dir | `middleware.py` or `*/middleware/` |
| Route file | `urls.py` |
| Model dir | `models.py` or `*/models/` |
| Config dir | `settings.py` |
| SQL file | `migrations/` |
| Env file | `.env` |
| Entry point | `manage.py` / `app.py` |

## Search Keywords

| Category | Keywords |
|---|---|
| DB query pattern | `Model.objects.filter` `Model.objects.create` |
| Request param pattern | `request.POST` `request.FILES` `request.GET` |
| File upload pattern | `request.FILES` `default_storage.save` |
| HTTP client pattern | `requests.get` `requests.post` |
| Auth pattern | `@login_required` `request.user` |

## Data Collection Batches

### Batch 1: Project Structure

```
SearchCodebase: "URL路由定义 path url" → target: urls.py or */urls.py
SearchCodebase: "视图函数和类定义" → target: views.py or */views/
SearchCodebase: "模型类定义 Model" → target: models.py or */models/
Glob: "*/views.py"
Glob: "*/models.py"
Glob: "*/urls.py"
Glob: "*/migrations/*.py"
```

### Batch 2: Security Scan

```
SearchCodebase: "requests.get requests.post 外部HTTP请求" → target: ./
SearchCodebase: "request.FILES default_storage 文件上传" → target: ./
SearchCodebase: "request.POST request.GET 参数接收 无验证" → target: */views/
SearchCodebase: "hardcoded 硬编码 password secret key" → target: ./
SearchCodebase: "login_required authentication 中间件" → target: ./
```

### Batch 3: Logic Validation

```
SearchCodebase: "字段名不匹配 列不存在 database column mismatch" → compare view fields vs models
Grep: "objects.filter|objects.create|objects.get|objects.update" → target: */views/
Grep: "request.POST|request.GET|request.FILES" → target: */views/
```

### Batch 4: Redundancy Scan

```
Glob: "*/views.py" → cross-reference with urls.py
Glob: "*/models.py" → cross-reference with views imports
SearchCodebase: "未使用的组件 孤立文件" → check url imports vs actual views
```

## Cross-Reference Rules

1. Cross-reference view field names with model field names → identify field mismatches
2. Cross-reference `urls.py` paths with actual view functions → identify dead routes
3. Cross-reference model files with view imports → identify unused models
4. Check `request.POST/GET` calls for missing validation (forms/serializers)
5. Check `requests.get/post()` for SSRF vulnerabilities
6. Check `request.FILES` calls for upload validation

## Common Vulnerability Patterns

| Pattern | Severity | Description |
|---|---|---|
| `requests.get(user_input)` | Critical | SSRF - user-controlled URL |
| `request.FILES` without validation | High | Unrestricted file upload |
| `request.POST` without form/serializer | Medium | Missing input validation |
| Raw SQL with user input | Critical | SQL injection |
| `os.system()` / `subprocess` with user input | Critical | Command injection |
| Missing `@login_required` | High | Authentication bypass |
| `eval()` / `exec()` with user input | Critical | Code injection |

## Architecture Diagram Template

```
Frontend SPA ⇄ Django/Flask API Server ⇄ PostgreSQL/MySQL
     |              |                        |
  localStorage   media/                  migrations/
```

## Data Flow Template

```
前端组件 → API Service → Axios HTTP → CORS中间件 → Auth装饰器 → View → ORM → JSON响应
```
