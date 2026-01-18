<env-spec-template>

<purpose>
Template and guidance for generating environment variable documentation.
Used by /sensei:generate-specs to create .env documentation and .env.example files.
</purpose>

<detection_patterns>
## Environment Variable Detection

### From .env Files
```bash
DATABASE_URL=postgresql://...
JWT_SECRET=secret123
STRIPE_API_KEY=sk_test_...
```

### From Code Usage

#### Node.js
```javascript
process.env.DATABASE_URL
process.env['API_KEY']
```

#### With Defaults
```javascript
const port = process.env.PORT || 3000;
const debug = process.env.DEBUG === 'true';
```

#### With Validation (Zod)
```typescript
const envSchema = z.object({
  DATABASE_URL: z.string().url(),
  PORT: z.coerce.number().default(3000),
  NODE_ENV: z.enum(['development', 'production', 'test'])
});
```

#### With @t3-oss/env-nextjs
```typescript
export const env = createEnv({
  server: {
    DATABASE_URL: z.string().url(),
  },
  client: {
    NEXT_PUBLIC_API_URL: z.string().url(),
  },
});
```

### From Docker/Docker Compose
```yaml
environment:
  - DATABASE_URL=${DATABASE_URL}
  - REDIS_URL=redis://redis:6379
```

### From CI/CD Configs
```yaml
# GitHub Actions
env:
  DATABASE_URL: ${{ secrets.DATABASE_URL }}
```
</detection_patterns>

<extraction_rules>
## Information Extraction

### Per Variable
- Name (exact case)
- Description (from comments or context)
- Type (string, number, boolean, url, etc.)
- Required vs optional
- Default value (if any)
- Sensitive (secrets, keys, passwords)
- Environment (dev/staging/prod)
- Example value (non-sensitive)

### Categorization
- Database (DATABASE_URL, DB_HOST, DB_PORT)
- Authentication (JWT_SECRET, SESSION_SECRET)
- API Keys (STRIPE_KEY, SENDGRID_KEY)
- Feature Flags (ENABLE_FEATURE_X)
- URLs (API_URL, FRONTEND_URL)
- Ports (PORT, API_PORT)
- Environment (NODE_ENV, APP_ENV)
- Logging (LOG_LEVEL, DEBUG)
</extraction_rules>

<output_template>
## Environment Variable Documentation Template

```markdown
# Environment Variables

> Configuration variables for {project_name}

**Last Updated:** {timestamp}

## Quick Start

1. Copy `.env.example` to `.env`
2. Fill in required values (marked with *)
3. Generate secrets for production

```bash
cp .env.example .env
# Edit .env with your values
```

## Variable Reference

### Database

| Variable | Type | Required | Default | Description |
|----------|------|----------|---------|-------------|
| `DATABASE_URL` | `url` | Yes* | - | PostgreSQL connection string |
| `DB_POOL_SIZE` | `number` | No | `10` | Connection pool size |

#### DATABASE_URL

Full connection string for the database.

**Format:**
```
postgresql://USER:PASSWORD@HOST:PORT/DATABASE?schema=SCHEMA
```

**Example:**
```bash
# Development
DATABASE_URL="postgresql://postgres:password@localhost:5432/myapp_dev"

# Production (use connection pooling)
DATABASE_URL="postgresql://user:pass@db.example.com:5432/myapp?pgbouncer=true"
```

---

### Authentication

| Variable | Type | Required | Default | Description |
|----------|------|----------|---------|-------------|
| `JWT_SECRET` | `string` | Yes* | - | Secret for signing JWTs |
| `JWT_EXPIRES_IN` | `string` | No | `7d` | Token expiration time |
| `SESSION_SECRET` | `string` | Yes* | - | Session encryption secret |

#### JWT_SECRET

**Security:** Must be at least 32 characters. Use a cryptographically secure random string.

**Generate:**
```bash
openssl rand -base64 32
# or
node -e "console.log(require('crypto').randomBytes(32).toString('base64'))"
```

---

### External Services

| Variable | Type | Required | Default | Description |
|----------|------|----------|---------|-------------|
| `STRIPE_SECRET_KEY` | `string` | Prod* | - | Stripe API secret key |
| `STRIPE_WEBHOOK_SECRET` | `string` | Prod* | - | Stripe webhook signing secret |
| `SENDGRID_API_KEY` | `string` | No | - | SendGrid email API key |

#### STRIPE_SECRET_KEY

**Development:** Use `sk_test_...` from Stripe dashboard
**Production:** Use `sk_live_...` (never commit!)

**Get it:** [Stripe Dashboard → API Keys](https://dashboard.stripe.com/apikeys)

---

### Application

| Variable | Type | Required | Default | Description |
|----------|------|----------|---------|-------------|
| `NODE_ENV` | `enum` | No | `development` | Environment mode |
| `PORT` | `number` | No | `3000` | Server port |
| `LOG_LEVEL` | `enum` | No | `info` | Logging verbosity |
| `APP_URL` | `url` | Yes* | - | Public application URL |

#### NODE_ENV

| Value | Description |
|-------|-------------|
| `development` | Local development, verbose logging |
| `test` | Test environment, mocked services |
| `production` | Production, optimized, no debug |

---

### Feature Flags

| Variable | Type | Required | Default | Description |
|----------|------|----------|---------|-------------|
| `ENABLE_ANALYTICS` | `boolean` | No | `false` | Enable analytics tracking |
| `ENABLE_BETA_FEATURES` | `boolean` | No | `false` | Enable beta features |

---

### Client-Side (Public)

Variables prefixed with `NEXT_PUBLIC_` are exposed to the browser.

| Variable | Type | Required | Description |
|----------|------|----------|-------------|
| `NEXT_PUBLIC_API_URL` | `url` | Yes | Public API endpoint |
| `NEXT_PUBLIC_GA_ID` | `string` | No | Google Analytics ID |

**Warning:** Never put secrets in `NEXT_PUBLIC_*` variables!

---

## Environment Files

| File | Purpose | Git |
|------|---------|-----|
| `.env` | Local development values | Ignored |
| `.env.example` | Template with dummy values | Committed |
| `.env.local` | Local overrides | Ignored |
| `.env.test` | Test environment values | Optional |
| `.env.production` | Production values | Never committed |

## Per-Environment Values

| Variable | Development | Staging | Production |
|----------|-------------|---------|------------|
| `NODE_ENV` | development | production | production |
| `DATABASE_URL` | localhost | staging-db | prod-db |
| `LOG_LEVEL` | debug | info | warn |
| `APP_URL` | http://localhost:3000 | https://staging.app.com | https://app.com |

## Validation

This project validates environment variables at startup using {zod/joi/custom}.

```typescript
// src/env.ts
import { z } from 'zod';

const envSchema = z.object({
  DATABASE_URL: z.string().url(),
  JWT_SECRET: z.string().min(32),
  NODE_ENV: z.enum(['development', 'test', 'production']).default('development'),
  PORT: z.coerce.number().default(3000),
});

export const env = envSchema.parse(process.env);
```

## Troubleshooting

### "Missing required environment variable"
```
Error: Missing required environment variable: DATABASE_URL
```
**Solution:** Ensure `.env` file exists and contains `DATABASE_URL`

### "Invalid environment variable"
```
Error: Invalid environment variable DATABASE_URL: Expected url, received string
```
**Solution:** Check the format. DATABASE_URL must be a valid URL.

---
*Auto-generated by BuildSensei. Last updated: {timestamp}*
```
</output_template>

<env_example_template>
## .env.example Template

```bash
# ===========================================
# {Project Name} Environment Configuration
# ===========================================
# Copy this file to .env and fill in values
# Required fields marked with (required)
# ===========================================

# ----- Database -----
DATABASE_URL="postgresql://user:password@localhost:5432/dbname"  # (required)
# DB_POOL_SIZE=10

# ----- Authentication -----
JWT_SECRET="generate-a-secure-random-string-at-least-32-chars"  # (required)
# JWT_EXPIRES_IN="7d"
SESSION_SECRET="another-secure-random-string"  # (required)

# ----- External Services -----
# STRIPE_SECRET_KEY="sk_test_..."
# STRIPE_WEBHOOK_SECRET="whsec_..."
# SENDGRID_API_KEY="SG...."

# ----- Application -----
NODE_ENV="development"
PORT=3000
# LOG_LEVEL="info"
APP_URL="http://localhost:3000"  # (required)

# ----- Feature Flags -----
# ENABLE_ANALYTICS=false
# ENABLE_BETA_FEATURES=false

# ----- Client-Side (exposed to browser) -----
NEXT_PUBLIC_API_URL="http://localhost:3000/api"  # (required)
# NEXT_PUBLIC_GA_ID=""
```
</env_example_template>

<file_organization>
## Output File Structure

```
docs/spec/
├── env.md                # Full documentation
└── .env.example          # Template file (also at project root)

# Project root
.env.example              # Canonical template
```
</file_organization>

<categorization_rules>
## Variable Categorization

### By Prefix Pattern
| Prefix | Category |
|--------|----------|
| `DB_`, `DATABASE_` | Database |
| `JWT_`, `AUTH_`, `SESSION_` | Authentication |
| `STRIPE_`, `SENDGRID_`, `AWS_` | External Services |
| `NEXT_PUBLIC_`, `VITE_` | Client-Side |
| `LOG_`, `DEBUG_` | Logging |
| `ENABLE_`, `FEATURE_` | Feature Flags |
| `REDIS_`, `CACHE_` | Caching |
| `SMTP_`, `MAIL_` | Email |
| `S3_`, `STORAGE_` | Storage |

### Sensitive Variable Patterns
Mark as sensitive (redact in logs, never expose):
- Contains: `SECRET`, `KEY`, `PASSWORD`, `TOKEN`, `CREDENTIAL`
- Ends with: `_KEY`, `_SECRET`, `_PASSWORD`, `_TOKEN`
- Specific: `DATABASE_URL` (contains password)
</categorization_rules>

</env-spec-template>
