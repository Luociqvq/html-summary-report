# Spring Boot Analysis Template

## Framework Info

- **Framework**: Spring Boot
- **Architecture Label**: Spring Boot API Server
- **Indicator Files**: `src/main/java/` + `pom.xml` / `build.gradle`

## Directory Paths

| Category | Path |
|---|---|
| Controller dir | `src/main/java/**/controller/` |
| Middleware dir | `src/main/java/**/filter/` |
| Route file | `src/main/java/**/config/` |
| Model dir | `src/main/java/**/entity/` |
| Config dir | `src/main/resources/` |
| SQL file | `src/main/resources/schema.sql` |
| Env file | `application.yml` / `.env` |
| Entry point | `src/main/java/**/Application.java` |

## Search Keywords

| Category | Keywords |
|---|---|
| DB query pattern | `repository.findById` `repository.save` |
| Request param pattern | `@RequestParam` `@RequestBody` `@PathVariable` |
| File upload pattern | `MultipartFile` `Files.write` |
| HTTP client pattern | `RestTemplate` `WebClient` |
| Auth pattern | `@PreAuthorize` `SecurityContext` |

## Data Collection Batches

### Batch 1: Project Structure

```
SearchCodebase: "Controller RequestMapping 端点定义" → target: src/main/java/**/controller/
SearchCodebase: "Entity Table 数据库实体" → target: src/main/java/**/entity/
SearchCodebase: "Repository 数据访问层" → target: src/main/java/**/repository/
Glob: "**/controller/*.java"
Glob: "**/entity/*.java"
Glob: "**/repository/*.java"
Glob: "**/config/*.java"
```

### Batch 2: Security Scan

```
SearchCodebase: "RestTemplate WebClient 外部HTTP请求" → target: src/main/java/
SearchCodebase: "MultipartFile 文件上传 Files.write" → target: src/main/java/
SearchCodebase: "@RequestParam @RequestBody 参数接收 无验证" → target: src/main/java/**/controller/
SearchCodebase: "hardcoded 硬编码 password secret key" → target: src/main/resources/
SearchCodebase: "@PreAuthorize SecurityConfig 认证授权" → target: src/main/java/**/config/
```

### Batch 3: Logic Validation

```
SearchCodebase: "字段名不匹配 列不存在 database column mismatch" → compare entity fields vs schema.sql
Grep: "findById|save|findBy|findAll" → target: src/main/java/**/repository/
Grep: "@RequestParam|@RequestBody|@PathVariable" → target: src/main/java/**/controller/
```

### Batch 4: Redundancy Scan

```
Glob: "**/controller/*.java" → cross-reference with config/RouteConfig
Glob: "**/entity/*.java" → cross-reference with repository imports
SearchCodebase: "未使用的组件 孤立文件" → check controller mappings vs actual services
```

## Cross-Reference Rules

1. Cross-reference entity field names with `schema.sql` column names → identify field mismatches
2. Cross-reference controller mappings with actual service methods → identify dead endpoints
3. Cross-reference entity files with repository imports → identify unused entities
4. Check `@RequestParam/@RequestBody` for missing validation (`@Valid`, `@NotNull`)
5. Check `RestTemplate` calls for SSRF vulnerabilities
6. Check `MultipartFile` calls for upload validation

## Common Vulnerability Patterns

| Pattern | Severity | Description |
|---|---|---|
| `RestTemplate` with user-controlled URL | Critical | SSRF |
| `MultipartFile` without validation | High | Unrestricted file upload |
| `@RequestBody` without `@Valid` | Medium | Missing input validation |
| Raw SQL with string concatenation | Critical | SQL injection |
| `Runtime.exec()` with user input | Critical | Command injection |
| Missing `@PreAuthorize` on endpoints | High | Authorization bypass |
| Hardcoded credentials in application.yml | High | Credentials exposure |

## Architecture Diagram Template

```
Frontend SPA ⇄ Spring Boot API Server ⇄ MySQL/PostgreSQL
     |              |                        |
  localStorage   uploads/               schema.sql / Flyway
```

## Data Flow Template

```
前端组件 → API Service → Axios HTTP → CORS Filter → Security Filter → Controller → Repository → JSON响应
```
