# Express/NestJS Analysis Template

## Framework Info

- **Framework**: Express / NestJS
- **Architecture Label**: Express API Server / NestJS API Server
- **Indicator Files**: `index.js` / `app.js` + `package.json` (express) OR `src/index.ts` + `nest-cli.json`

## Directory Paths

| Category | Express Path | NestJS Path |
|---|---|---|
| Controller dir | `src/controllers/` or `routes/` | `src/*.controller.ts` |
| Middleware dir | `src/middleware/` | `src/*.guard.ts` / `src/*.interceptor.ts` |
| Route file | `src/routes/` or `app.js` | `src/*.module.ts` |
| Model dir | `src/models/` or `prisma/schema.prisma` | `src/*.service.ts` |
| Config dir | `config/` or `.env` | `config/` or `.env` |
| SQL file | `prisma/migrations/` | `prisma/migrations/` |
| Env file | `.env` | `.env` |

## Search Keywords

| Category | Keywords |
|---|---|
| DB query pattern | `Model.find` `Model.create` `prisma.model.findMany` |
| Request param pattern | `req.body` `req.params` `req.query` |
| File upload pattern | `multer` `fs.writeFile` |
| HTTP client pattern | `axios` `fetch` `node-fetch` |
| Auth pattern | `jwt.verify` `passport` |

## Data Collection Batches

### Batch 1: Project Structure

```
SearchCodebase: "路由定义 Router app.get app.post" → target: src/routes/ or src/
SearchCodebase: "控制器和服务定义" → target: src/controllers/ or src/*.controller.ts
SearchCodebase: "数据模型 Schema Model" → target: src/models/ or prisma/schema.prisma
Glob: "src/**/*.controller.ts" or "src/controllers/*.js"
Glob: "src/**/*.service.ts" or "src/models/*.js"
Glob: "src/**/*.module.ts" or "src/routes/*.js"
Glob: "prisma/schema.prisma"
```

### Batch 2: Security Scan

```
SearchCodebase: "axios fetch node-fetch 外部HTTP请求" → target: src/
SearchCodebase: "multer fs.writeFile 文件上传" → target: src/
SearchCodebase: "req.body req.params 参数接收 无验证" → target: src/ or src/controllers/
SearchCodebase: "hardcoded 硬编码 password secret key" → target: ./
SearchCodebase: "jwt.verify passport auth 中间件" → target: src/middleware/ or src/*.guard.ts
```

### Batch 3: Logic Validation

```
SearchCodebase: "字段名不匹配 列不存在 database column mismatch" → compare model fields vs schema.prisma
Grep: "Model.find|Model.create|findByIdAndUpdate|prisma.\\w+.findMany" → target: src/
Grep: "req.body|req.params|req.query" → target: src/
```

### Batch 4: Redundancy Scan

```
Glob: "src/**/*.controller.ts" → cross-reference with module routes
Glob: "src/**/*.service.ts" → cross-reference with controller imports
SearchCodebase: "未使用的组件 孤立文件" → check route imports vs actual controllers
```

## Cross-Reference Rules

1. Cross-reference model/Prisma schema field names with controller usage → identify field mismatches
2. Cross-reference route definitions with actual controller methods → identify dead routes
3. Cross-reference service files with controller imports → identify unused services
4. Check `req.body` calls for missing validation (joi, class-validator, zod)
5. Check `axios/fetch` calls for SSRF vulnerabilities
6. Check `multer` configuration for upload validation

## Common Vulnerability Patterns

| Pattern | Severity | Description |
|---|---|---|
| `axios(userInput)` / `fetch(userInput)` | Critical | SSRF - user-controlled URL |
| `multer` without file filter | High | Unrestricted file upload |
| `req.body` without validation | Medium | Missing input validation |
| Raw SQL with string interpolation | Critical | SQL injection |
| `eval()` / `child_process` with user input | Critical | Code/command injection |
| Missing JWT verification | High | Authentication bypass |
| Hardcoded secrets in source code | High | Credentials exposure |

## Architecture Diagram Template

```
Frontend SPA ⇄ Express/NestJS API Server ⇄ MongoDB/MySQL
     |              |                          |
  localStorage   uploads/                prisma/migrations/
```

## Data Flow Template

```
前端组件 → API Service → Axios HTTP → CORS中间件 → Auth Guard → Controller → Service → Prisma/ORM → JSON响应
```
