# Scan Strategy Reference

Detailed rules for the resume-scan quick probe, scale branching, and value extraction phases.

## Quick Probe Checklist

Run these checks for every project before deciding on scan depth:

### Root Structure
- `ls` top-level → detect monorepo (packages/, services/, apps/), single-app, or library layout
- Count top-level directories to estimate project scope

### README / Documentation
- README.md: project purpose, architecture description, badges (CI status, coverage, version)
- docs/ directory: API docs, architecture diagrams, ADRs (Architecture Decision Records)
- CHANGELOG.md: release cadence, feature velocity

### Dependency Files (by ecosystem)
| Ecosystem | File | Extract |
|-----------|------|---------|
| Node.js | package.json | dependencies, scripts, engines, workspaces |
| Go | go.mod | module path, Go version, key dependencies |
| Python | requirements.txt, pyproject.toml, setup.py | frameworks, ML libs, tooling |
| Java/Kotlin | pom.xml, build.gradle | Spring, frameworks, plugins |
| Rust | Cargo.toml | crates, features, workspace members |

### Infrastructure / DevOps Config
- Dockerfile, docker-compose.yml → containerization
- .github/workflows/, .gitlab-ci.yml, Jenkinsfile → CI/CD maturity
- Makefile, Taskfile, justfile → build automation
- k8s/, helm/, terraform/, pulumi/ → infrastructure-as-code
- .env.example → environment variable discipline

### AI Signal Files
- CLAUDE.md, .cursorrules, .cursor/ → AI-assisted development workflow
- Skills/, MCP configs → agent framework usage
- Files importing AI SDKs (openai, anthropic, langchain, llamaindex)
- Prompt templates, RAG pipelines, fine-tuning configs, embeddings code

## Scale-Based Branching Rules

### Small Projects (< 50 files)
- **Strategy**: Full scan — read every source file
- **Rationale**: Complete understanding is cheap and yields highest accuracy
- **Process**: Glob all source files → Read each → extract patterns, architecture, quality signals

### Medium Projects (50–500 files)
- **Strategy**: Key files — prioritized reading of high-signal files
- **Priority order**:
  1. Entry points (main.go, index.ts, app.py, Application.java)
  2. Route/handler definitions (router, controller, handler directories)
  3. Core domain/model files (models/, domain/, entities/)
  4. Database schemas and migrations (migrations/, schema/, *.sql)
  5. Test files (for coverage and quality signal)
  6. Interface/API definitions (proto/, openapi/, graphql/)
  7. Configuration and middleware
- **Budget**: Read up to ~80 key files, skim the rest via Glob patterns

### Large Projects (> 500 files)
- **Strategy**: Smart sampling with parallel Subagents
- **Module detection**: Identify top-level modules from directory structure
- **Per-module Subagent**: Each Subagent scans one module independently
- **Sampling rules**:
  - Always read: entry points, public API surfaces, README per module
  - Sample: 10–15% of implementation files per module, biased toward files with most git activity from target author
  - Skip: generated code, vendor/, node_modules/, build artifacts, test fixtures/snapshots

## Key File Priority by Project Type

### Go Projects
1. main.go, cmd/*/main.go (entry points)
2. internal/*/handler.go, internal/*/service.go (business logic)
3. pkg/ public interfaces
4. proto/*.proto (gRPC definitions)
5. migrations/*.sql (database schema)

### Node.js / TypeScript Projects
1. src/index.ts, src/app.ts (entry points)
2. src/routes/, src/controllers/ (API surface)
3. src/models/, src/services/ (domain logic)
4. prisma/schema.prisma, knex migrations (database)
5. src/middleware/ (cross-cutting concerns)

### Python Projects
1. app.py, main.py, manage.py, wsgi.py (entry points)
2. */views.py, */api.py, */routes.py (API surface)
3. */models.py, */schemas.py (domain)
4. alembic/versions/ (migrations)
5. */tasks.py, */celery.py (async processing)

### Java / Kotlin Projects
1. *Application.java (Spring Boot entry)
2. */controller/*.java (REST endpoints)
3. */service/*.java (business logic)
4. */entity/*.java, */model/*.java (domain)
5. */repository/*.java (data access)

### Rust Projects
1. src/main.rs, src/lib.rs (entry points)
2. src/api/, src/handlers/ (API surface)
3. src/models/, src/domain/ (domain logic)
4. migrations/ (database)
5. src/config.rs, src/error.rs (infrastructure)

## Code Quality Signal Extraction

### Engineering Maturity
- **Tests**: test file count, test/src ratio, test frameworks used, integration vs unit split
- **CI/CD**: pipeline complexity, stages (lint, test, build, deploy), environment promotion
- **Error handling**: custom error types, error wrapping, panic recovery, graceful shutdown
- **Logging**: structured logging (zap, slog, winston, loguru), log levels, request tracing
- **Documentation**: code comments density, API docs, architecture docs, ADRs

### Design Quality
- **Layered architecture**: clear separation of handler/service/repository/model layers
- **Design patterns**: dependency injection, factory, strategy, observer, middleware chain
- **Interface design**: clean API contracts, versioning, backward compatibility
- **DB design**: normalized schemas, indexes, migrations discipline, query optimization
- **Separation of concerns**: bounded contexts, module independence, minimal cross-cutting

### Security Awareness
- **Auth**: JWT, OAuth2, RBAC, session management implementations
- **Input validation**: request validation middleware, sanitization, parameterized queries
- **Secrets management**: vault integration, env-based config, no hardcoded secrets
- **Dependency security**: dependabot, snyk, audit scripts, lockfile integrity

### AI Signals
- CLAUDE.md, .cursorrules → AI-assisted development workflow maturity
- Skills packages, MCP server configs → agent framework experience
- AI SDK imports (openai, anthropic, langchain, llamaindex) → hands-on AI integration
- Prompt templates, system prompts → prompt engineering
- RAG pipelines, vector stores, embeddings → retrieval-augmented generation
- Fine-tuning scripts, dataset preparation → model customization

## Value Point Scoring (0–100)

Each extracted value point is scored across five dimensions, 0–20 each:

| Dimension | 0 pts | 10 pts | 20 pts |
|-----------|-------|--------|--------|
| **Quantification** | No numbers | Vague scale ("large") | Specific metrics with evidence |
| **0-to-1 Creation** | Maintained existing | Extended existing | Built from scratch |
| **Business Impact** | Internal tooling | Team-level impact | Product/revenue impact |
| **Technical Depth** | CRUD / boilerplate | Moderate complexity | Novel algorithm / architecture |
| **Scarcity** | Common skill combo | Uncommon domain | Rare expertise intersection |

Points scoring 60+ are prioritized for resume inclusion. Points scoring below 30 are dropped unless they fill a gap in the narrative.

## Cross-Project Correlation Analysis (--merge mode)

When multiple projects are scanned together with `--merge`, perform additional cross-project analysis:

### Tech Stack Evolution
- Sort projects by time (using scan_params.since or first commit date)
- Track technology transitions across projects
- Generate evolution narrative: "从 Node.js 单体演进到 Go 微服务架构"

### Common Capability Extraction
- Find value_point categories that appear across 2+ projects
- Shared patterns are stronger signals than project-specific ones
- Output: "在三个不同业务领域均承担核心中间件和 API 设计"

### Role Upgrade Trajectory
- Compare author_ratio across projects ordered by time
- Detect progression: contributor → core dev → tech lead
- Look for mentoring signals (code review patterns, onboarding commits)
- Output: "从开发者成长为技术负责人，贡献占比从 15% 提升至 45%"

### Merged Resume Structure Strategy
| Scenario | Strategy |
|----------|----------|
| Similar tech stacks | Merge into one "技术能力" section, projects show business results |
| Very different stacks | Group by tech direction, show breadth |
| Clear time progression | Reverse chronological, emphasize growth |
| Unequal importance | Top project detailed (4 bullets), others brief (1-2 bullets) |
