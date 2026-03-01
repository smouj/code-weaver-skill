---
name: code-weaver
version: 2.1.0
author: SMOUJBOT
description: Automatically stitches together code components, APIs, and services into seamless integrations using proven patterns
tags:
  - integration
  - automation
  - weaving
  - patterns
  - composition
  - microservices
  - boilerplate
category: devops
maintainer: ops@flickclaw.io
last_updated: 2026-03-01
compatibility:
  openclaw: ">=2.0.0"
  node: ">=18.0.0"
  typescript: ">=5.0.0"
dependencies:
  - file: "../lib/code-weaver-lib"
    required: true
  - command: "git"
    purpose: "version control operations"
  - command: "node"
    purpose: "runtime execution"
  - package: "prettier"
    version: ">=3.0.0"
    purpose: "code formatting"
  - package: "typescript"
    version: ">=5.0.0"
    purpose: "type checking"
required_env_vars:
  - name: "CODE_WEAVER_DRY_RUN"
    default: "true"
    description: "Default dry-run mode"
  - name: "CODE_WEAVER_BACKUP_DIR"
    default: "/tmp/code-weaver-backups"
    description: "Backup directory path"
  - name: "CODE_WEAVER_PATTERN_PATH"
    default: "/opt/openclaw/patterns"
    description: "Custom pattern definitions path"
capabilities:
  - weave-integration
  - validate-patterns
  - create-pattern
  - apply-template
  - rollback-weave
  - generate-tests
  - update-dependencies
output_format: "structured"
---

# Code Weaver Skill

Automatically stitches together code components, APIs, and services into seamless integrations using proven architectural patterns. Handles boilerplate generation, dependency wiring, and consistency validation across microservices and monoliths.

## Purpose

Code Weaver solves real integration problems:
- **API Gateway + Microservices**: Auto-wire service discovery, routing, and middleware chains
- **Database Layer Integration**: Stitch ORM models, migrations, and repository patterns
- **Authentication Flows**: Integrate OAuth providers, JWT middleware, and session management
- **Event-Driven Architectures**: Connect message queues, event handlers, and consumer services
- **CI/CD Pipeline Assembly**: Generate GitHub Actions, Docker Compose, and deployment manifests
- **Legacy Refactoring**: Extract services from monoliths with proper boundary definitions

## Scope

### Primary Commands

```bash
# Core weaving operations
code-weaver weave <target-dir> --pattern <pattern-name> [--dry-run] [--force] [--custom-vars <json>]
code-weaver validate <target-dir> --pattern <pattern-name>
code-weaver list-patterns [--available] [--applied]
code-weaver create-pattern <name> --from <template-dir> --output <pattern-file>

# Template management
code-weaver apply-template <template-name> <target> --vars <json-file>
code-weaver list-templates [--category <cat>]
code-weaver export-config <output-file>
code-weaver import-config <input-file> --merge

# Analysis and reporting
code-weaver analyze <target-dir> --output <report-format>
code-weaver status [--detailed]
code-weaver conflicts <target-dir> --resolve-strategy <strategy>

# Maintenance
code-weaver update-patterns [--from <repo-url>]
code-weaver clean-backups [--older-than <days>]
```

### Pattern Types

- `rest-api`: Full REST service with Express/Fastify, validation, OpenAPI
- `graphql-service`: GraphQL server with schema, resolvers, data loaders
- `event-consumer`: Message queue consumer with dead letter handling
- `auth-service`: OAuth2/OIDC provider integration with token management
- `data-pipeline`: ETL/ELT pipeline with scheduling and monitoring
- `microservice-bridge`: Service-to-service communication with circuit breakers
- `monolith-extract`: Extract bounded context from existing monolith

## Detailed Work Process

### 1. Pre-weave Analysis
```bash
# Step 1: Analyze target directory
code-weaver analyze ./src/services/user-service --output json > analysis.json

# Step 2: Check for conflicts with existing patterns
code-weaver conflicts ./src --resolve-strategy interactive

# Step 3: Generate dry-run preview
code-weaver weave ./src/services --pattern rest-api --dry-run > weave-plan.txt
```

### 2. Environment Setup Verification
```bash
# Verify dependencies exist
node --version  # >= 18.0.0
npm list prettier typescript
git status  # clean working directory required

# Check pattern availability
code-weaver list-patterns --available | grep "rest-api"

# Validate custom variables if provided
echo '{"port": 3001, "db_type": "postgres"}' | jq .  # must be valid JSON
```

### 3. Backup Creation (Automatic)
```bash
# Code Weaver creates timestamped backups in $CODE_WEAVER_BACKUP_DIR
# Backups include:
# - All modified files (git diff)
# - Original package.json
# - Environment configurations
# - Database schema snapshots (if applicable)

# Manual backup if needed
code-weaver export-config /tmp/pre-weave-backup.json
```

### 4. Weave Application
```bash
# Basic weave with pattern
code-weaver weave ./services/payment --pattern event-consumer

# With custom variables (JSON)
code-weaver weave ./services/order --pattern microservice-bridge \
  --custom-vars '{"service_name":"order-service","dependencies":["inventory","billing"]}'

# Force overwrite existing files (use with caution)
code-weaver weave ./services --pattern rest-api --force

# Verbose output
CODE_WEAVER_DEBUG=1 code-weaver weave ./src --pattern data-pipeline
```

### 5. Post-weave Validation
```bash
# Automatic validation after successful weave
code-weaver validate ./services/payment --pattern event-consumer

# Run type checking
cd ./services/payment && npm run typecheck

# Run tests if they exist
npm test -- --grep "Integration"

# Format code
npx prettier --write .

# Generate final report
code-weaver status --detailed > weave-report.txt
```

### 6. Commit Changes
```bash
# Stage woven files
git add services/payment/src/ services/payment/package.json

# Commit with standardized message
git commit -m "feat(integration): weave event-consumer pattern for payment service

- Add RabbitMQ connection handling
- Implement dead letter queue retry logic
- Add monitoring endpoints
- Update Docker Compose configuration"

# Verify no secrets in commit
git diff --cached | grep -i "password\|secret\|key" || echo "No secrets detected"
```

## Golden Rules

1. **Always dry-run first**: `--dry-run` flag shows exact changes before modification
2. **Clean working directory**: Git must have no uncommitted changes outside weave scope
3. **Backup verification**: Confirm backup created in `$CODE_WEAVER_BACKUP_DIR` before proceeding
4. **Pattern matching**: Use `code-weaver list-patterns --available` to confirm pattern exists for your stack
5. **Custom vars validation**: JSON passed to `--custom-vars` must pass `jq empty` validation
6. **One weave at a time**: Do not run multiple concurrent weaves in same repo
7. **Test immediately**: Run `npm test` within 5 minutes of weave completion
8. **Document deviations**: If manual changes made post-weave, document in `WEAVE_OVERRIDES.md`
9. **Never weave production**: Always weave in feature branch, never on main/master
10. **Rollback readiness**: Keep backup until PR merges and passes CI

## Examples

### Example 1: Create REST API Service
**User input:**
```bash
code-weaver weave ./services/user --pattern rest-api --custom-vars '{"port": 3002, "auth_required": true, "features": ["rate-limit","cors"]}'
```

**Generated files:**
```
services/user/
├── src/
│   ├── routes/
│   │   ├── index.ts      # Router aggregation
│   │   ├── users.ts      # CRUD endpoints
│   │   └── health.ts     # Health check endpoint
│   ├── middleware/
│   │   ├── auth.ts       # JWT validation
│   │   ├── rate-limit.ts # Rate limiting
│   │   └── cors.ts       # CORS configuration
│   ├── controllers/
│   │   └── users.ts      # Request handlers
│   ├── validators/
│   │   └── schemas.ts    # Zod validation schemas
│   ├── services/
│   │   └── user.ts       # Business logic
│   └── index.ts          # App bootstrap
├── tests/
│   ├── integration/
│   │   └── routes.test.ts
│   └── unit/
│       └── user.test.ts
├── Dockerfile
├── docker-compose.yml
├── package.json          # Updated with dependencies
├── .env.example
└── openapi.yaml          # Generated OpenAPI spec
```

### Example 2: Integrate Event Consumer
**User input:**
```bash
code-weaver weave ./services/notification --pattern event-consumer \
  --custom-vars '{"queue":"email-events","exchange":"notifications","routing_key":"email.send"}'
```

**Output:**
```
✅ Pattern 'event-consumer' applied successfully
📦 Dependencies added: amqplib, @typegoose/typegoose
🔧 Generated: src/consumers/email-consumer.ts
📝 Updated: package.json, docker-compose.yml
🌊 Configured: RabbitMQ connection pool (max 5 connections)
💾 Persistence: MongoDB for event state tracking
⚡ Retry logic: exponential backoff (max 3 attempts)
📊 Monitoring: /metrics endpoint with queue depth
```

### Example 3: Validate Existing Integration
**User input:**
```bash
code-weaver validate ./services/payment --pattern event-consumer
```

**Output:**
```
🔍 Validating pattern 'event-consumer' in ./services/payment

✅ All required files present
✅ RabbitMQ connection configured
✅ Dead letter queue handler found
✅ Idempotency key processing implemented
✅ Prometheus metrics exported
⚠️  Warning: Missing retry delay configuration (default: 1000ms)
✅ Health check endpoint responding on /health
✅ All type definitions valid

Validation complete: 6/7 checks passed (1 warning)
```

### Example 4: Analyze for Pattern Application
**User input:**
```bash
code-weaver analyze ./src --output json
```

**Output (JSON):**
```json
{
  "target": "./src",
  "stack": "typescript-express",
  "detected_patterns": ["rest-api"],
  "missing_components": [
    "rate-limiting",
    "request-logging",
    "structured-logging"
  ],
  "recommended_patterns": [
    {
      "name": "rest-api",
      "confidence": 0.94,
      "reason": "Express app detected with routes directory"
    }
  ],
  "conflicts": [
    {
      "file": "src/middleware/auth.ts",
      "conflict_type": "overlap",
      "resolution": "merge-with-existing"
    }
  ]
}
```

## Rollback Commands

### Immediate Rollback (Last Weave)
```bash
# Automatic rollback to pre-weave state
code-weaver rollback --last --target ./services/user

# With force to discard post-weave changes
code-weaver rollback --last --target ./services/user --force

# Specify backup manually if needed
code-weaver rollback --backup /tmp/code-weaver-backups/20260301_143022_user-service.json
```

### Full Restore from Backup
```bash
# List available backups
ls -la $CODE_WEAVER_BACKUP_DIR

# Restore specific backup
code-weaver restore \
  --backup $CODE_WEAVER_BACKUP_DIR/backup-20260301-143022.json \
  --target ./services/user \
  --preserve-new-files

# Verify restore success
code-weaver status --target ./services/user
```

### Git-Based Rollback (Preferred)
```bash
# If weave was committed, use git revert
git log --oneline --grep="weave" -1
git revert <commit-hash>

# If not committed, use stash
git stash push -m "Pre-weave backup" -- services/user/
git stash list
git stash pop stash@{0}
```

### Rollback Verification Checklist
```bash
# 1. Check files restored
diff -r backup-orig/ services/user/src/ || echo "Files restored successfully"

# 2. Verify package.json
jq '.dependencies' services/user/package.json | grep -q "amqplib" && echo "❌ Still has weave deps" || echo "✅ Clean"

# 3. Validate git status
git status --short services/user/ | grep -q "^??" && echo "⚠️  Orphaned files present"

# 4. Test compilation
cd services/user && npm run build
```

### Emergency Procedures
```bash
# If Code Weaver itself fails mid-weave:
# 1. Stop all processes
pkill -f code-weaver

# 2. Manual backup of current state
tar -czf /tmp/manual-backup-$(date +%s).tar.gz services/user/

# 3. Restore from last known good
code-weaver rollback --last --target ./services/user --force

# 4. Report issue with debug log
CODE_WEAVER_DEBUG=1 code-weaver analyze ./services/user --output json > debug.log
```

## Troubleshooting

### "Pattern not found" error
```bash
# Check available patterns
code-weaver list-patterns --available

# If custom pattern missing, verify CODE_WEAVER_PATTERN_PATH
echo $CODE_WEAVER_PATTERN_PATH
ls -la $CODE_WEAVER_PATTERN_PATH

# Update patterns from repository
code-weaver update-patterns --from https://github.com/org/patterns.git
```

### "JSON parse error" for custom-vars
```bash
# Validate JSON before passing
echo '{"port": 3001}' | jq .  # Should output parsed JSON

# Common issues:
# - Single quotes instead of double quotes
# - Trailing commas
# - Unescaped special characters

# Fix:
cat > vars.json << 'EOF'
{
  "port": 3001,
  "db_type": "postgres",
  "features": ["rate-limit", "cors"]
}
EOF

code-weaver weave ./src --pattern rest-api --custom-vars @vars.json
```

### "Working directory not clean" error
```bash
# Check git status
git status

# Option 1: Stash changes
git stash push -m "Temporary stash for weave"

# Option 2: Commit changes
git add .
git commit -m "WIP: temporary commit"

# Option 3: Use --force (overwrites uncommitted changes - DANGEROUS)
code-weaver weave ./src --pattern rest-api --force
```

### TypeScript compilation errors post-weave
```bash
# Check TypeScript version
tsc --version  # Must be >= 5.0.0

# Clean and rebuild
rm -rf node_modules/.cache
npm run clean
npm run build

# If missing dependencies from weave:
npm install  # package.json should have been updated

# Verify prettier formatting
npx prettier --check src/
npx prettier --write src/
```

### Backup directory not writable
```bash
# Check backup directory
echo $CODE_WEAVER_BACKUP_DIR
ls -ld $CODE_WEAVER_BACKUP_DIR

# Create if missing with proper permissions
mkdir -p $CODE_WEAVER_BACKUP_DIR
chmod 700 $CODE_WEAVER_BACKUP_DIR

# Or override for session
export CODE_WEAVER_BACKUP_DIR=/tmp/weaver-backups
mkdir -p $CODE_WEAVER_BACKUP_DIR
```

## Pattern Development

### Create Custom Pattern
```bash
# 1. Scaffold from existing template
code-weaver create-pattern my-custom-pattern \
  --from ./templates/rest-api \
  --output ./patterns/my-custom-pattern.json

# 2. Edit pattern definition
vim ./patterns/my-custom-pattern.json

# 3. Pattern structure:
# {
#   "name": "my-custom-pattern",
#   "version": "1.0.0",
#   "description": "Custom integration pattern",
#   "files": [
#     {
#       "path": "src/{{component}}.ts",
#       "template": "templates/service.ts.hbs",
#       "conditions": ["has_database"]
#     }
#   ],
#   "dependencies": {
#     "npm": ["express", "dotenv"],
#     "dev": ["@types/express"]
#   },
#   "validation": {
#     "required_files": ["src/index.ts"],
#     "required_dependencies": ["express"]
#   }
# }

# 4. Test pattern locally
code-weaver weave ./test-project --pattern my-custom-pattern --dry-run

# 5. Share pattern
code-weaver export-config ./my-pattern.json
```

## Performance Considerations

- Weave time scales with target directory size (typically 50-500 files)
- Large repositories: use `--target-dir` to narrow scope
- Dry-run is fast (< 1s); actual weave includes file I/O (5-30s)
- Validation runs type checking - can be slow on large codebases
- Use `--no-format` to skip prettier during weave for speed

## Security Notes

- Never weave with secrets in environment; use `--custom-vars` with file argument
- Backups stored in `$CODE_WEAVER_BACKUP_DIR` - ensure directory is secured
- Patterns execute shell commands - only use patterns from trusted sources
- Review generated Dockerfiles for exposed ports and secrets
```