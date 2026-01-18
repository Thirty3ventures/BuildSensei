<generate-specs-workflow>

<purpose>
Workflow for generating specifications from implementation code.
Analyzes API routes, components, database models, and environment variables
to produce machine-readable and human-readable documentation.
</purpose>

<when_to_use>
- After completing a phase with significant new code
- When onboarding team members who need documentation
- When setting up API consumers who need specs
- When auditing what exists in the codebase
- Manually via /sensei:generate-specs
</when_to_use>

<required_reading>
@templates/spec/openapi.md - API spec generation patterns
@templates/spec/component.md - Component spec generation patterns
@templates/spec/schema.md - Schema spec generation patterns
@templates/spec/env.md - Environment variable documentation patterns
</required_reading>

<inputs>
- Scope: all | api | components | schema | env
- Source directories (auto-detected or specified)
- Existing specs (for incremental updates)
</inputs>

<outputs>
- docs/spec/api/*.yaml (OpenAPI specifications)
- docs/spec/components/*.md (Component documentation)
- docs/spec/data/schema.md (Database schema documentation)
- docs/spec/env.md (Environment variable documentation)
- .env.example (Updated template)
</outputs>

<process>

<step name="detect_project_type" priority="critical">
## Detect Project Type and Structure

Analyze the codebase to understand what exists:

```bash
# Check for common patterns
ls package.json 2>/dev/null && echo "NODE_PROJECT"
ls requirements.txt pyproject.toml 2>/dev/null && echo "PYTHON_PROJECT"
ls go.mod 2>/dev/null && echo "GO_PROJECT"

# Detect framework
grep -l "next" package.json 2>/dev/null && echo "NEXTJS"
grep -l "express" package.json 2>/dev/null && echo "EXPRESS"
grep -l "fastify" package.json 2>/dev/null && echo "FASTIFY"
grep -l "hono" package.json 2>/dev/null && echo "HONO"
grep -l "react" package.json 2>/dev/null && echo "REACT"
grep -l "vue" package.json 2>/dev/null && echo "VUE"
grep -l "svelte" package.json 2>/dev/null && echo "SVELTE"

# Detect ORM
grep -l "prisma" package.json 2>/dev/null && echo "PRISMA"
grep -l "drizzle" package.json 2>/dev/null && echo "DRIZZLE"
grep -l "typeorm" package.json 2>/dev/null && echo "TYPEORM"
grep -l "mongoose" package.json 2>/dev/null && echo "MONGOOSE"
```

Build detection profile:
```
{
  "type": "node",
  "framework": "nextjs",
  "ui": "react",
  "orm": "prisma",
  "api_style": "app_router"
}
```
</step>

<step name="create_output_dirs" priority="critical">
## Create Output Directory Structure

```bash
mkdir -p docs/spec/api
mkdir -p docs/spec/components
mkdir -p docs/spec/data
```
</step>

<step name="generate_api_specs" priority="normal">
## Generate API Specifications

**Skip if:** Scope doesn't include "api" or "all"

### 1. Find API Routes

**Next.js App Router:**
```bash
find app -name "route.ts" -o -name "route.js" 2>/dev/null
```

**Next.js Pages Router:**
```bash
find pages/api -name "*.ts" -o -name "*.js" 2>/dev/null
```

**Express/Fastify/Hono:**
```bash
# Look for router definitions
grep -rl "router\.\(get\|post\|put\|patch\|delete\)" src/ 2>/dev/null
grep -rl "app\.\(get\|post\|put\|patch\|delete\)" src/ 2>/dev/null
```

### 2. Analyze Each Route File

For each route file:
1. Read the file content
2. Extract HTTP methods and paths
3. Extract request body schema (from validation or types)
4. Extract response shape (from return statements)
5. Extract authentication requirements (middleware)
6. Extract path/query parameters

### 3. Generate OpenAPI Spec

Group routes by resource/tag (e.g., /users/* → users.yaml)

**Template reference:** @templates/spec/openapi.md

For each resource group:
```yaml
# docs/spec/api/{resource}.yaml
openapi: 3.0.0
info:
  title: {resource} API
  version: 1.0.0
paths:
  {extracted_paths}
components:
  schemas:
    {extracted_schemas}
```

### 4. Create Index File

```yaml
# docs/spec/api/index.yaml
openapi: 3.0.0
info:
  title: {project_name} API
  version: {version}
  description: Auto-generated API specification
paths:
  # Combined from all resource files
components:
  schemas:
    # Combined schemas
```

### 5. Validate Generated Specs

```bash
# If spectral is available
npx @stoplight/spectral-cli lint docs/spec/api/index.yaml 2>/dev/null || echo "Spectral not installed, skipping validation"
```
</step>

<step name="generate_component_specs" priority="normal">
## Generate Component Specifications

**Skip if:** Scope doesn't include "components" or "all"

### 1. Find Components

**React:**
```bash
# Find component files (capitalized .tsx/.jsx)
find src components -name "[A-Z]*.tsx" -o -name "[A-Z]*.jsx" 2>/dev/null
```

**Vue:**
```bash
find src components -name "*.vue" 2>/dev/null
```

**Svelte:**
```bash
find src -name "*.svelte" 2>/dev/null
```

### 2. Categorize Components

For each component, determine category:
- UI (Button, Input, Card)
- Layout (Container, Grid, Stack)
- Form (TextField, Select, Checkbox)
- Data Display (Table, List, Avatar)
- Feedback (Alert, Modal, Toast)
- Navigation (Navbar, Sidebar, Breadcrumb)

Categorization based on:
- File path (src/components/ui/, src/components/form/)
- Component name patterns
- Imported dependencies

### 3. Analyze Each Component

For each component file:
1. Extract props interface/type
2. Extract default values
3. Extract events/emits
4. Extract slots (Vue/Svelte)
5. Extract JSDoc comments
6. Detect CSS custom properties
7. Find usage examples in tests/stories

### 4. Generate Component Spec

**Template reference:** @templates/spec/component.md

For each component:
```markdown
# docs/spec/components/{category}/{ComponentName}.spec.md
# {ComponentName}

> {description from JSDoc or inferred}

## Props
{extracted_props_table}

## Events
{extracted_events_table}

## Examples
{usage_examples}
```

### 5. Create Component Index

```markdown
# docs/spec/components/index.md
# Component Library

## UI Components
- [Button](./ui/Button.spec.md) - {description}

## Form Components
- [TextField](./form/TextField.spec.md) - {description}
...
```
</step>

<step name="generate_schema_specs" priority="normal">
## Generate Schema Specifications

**Skip if:** Scope doesn't include "schema" or "all"

### 1. Detect ORM and Find Schema

**Prisma:**
```bash
cat prisma/schema.prisma 2>/dev/null
```

**Drizzle:**
```bash
find src -name "schema.ts" -o -name "*.schema.ts" 2>/dev/null
cat src/db/schema.ts 2>/dev/null
```

**TypeORM:**
```bash
find src -name "*.entity.ts" 2>/dev/null
```

**Mongoose:**
```bash
find src -name "*.model.ts" -o -name "*.schema.ts" 2>/dev/null
```

### 2. Parse Schema

For each model/table:
1. Extract table/collection name
2. Extract fields with types
3. Extract relationships
4. Extract indexes
5. Extract constraints
6. Extract default values

### 3. Generate Schema Documentation

**Template reference:** @templates/spec/schema.md

```markdown
# docs/spec/data/schema.md
# Database Schema

## Models

### User
| Field | Type | Nullable | Description |
|-------|------|----------|-------------|
{extracted_fields}

### Relationships
{extracted_relationships}
```

### 4. Generate ERD (Text-based)

```
┌─────────────┐       ┌─────────────┐
│    User     │──────<│    Post     │
├─────────────┤       ├─────────────┤
│ id (PK)     │       │ id (PK)     │
│ email       │       │ authorId(FK)│
└─────────────┘       └─────────────┘
```
</step>

<step name="generate_env_specs" priority="normal">
## Generate Environment Variable Documentation

**Skip if:** Scope doesn't include "env" or "all"

### 1. Find Environment Variable Sources

```bash
# Check for .env files
ls .env .env.example .env.local .env.development 2>/dev/null

# Check for env validation
cat src/env.ts src/env.js src/lib/env.ts 2>/dev/null
```

### 2. Extract Variables from Code

```bash
# Find process.env usage
grep -rh "process\.env\." src/ --include="*.ts" --include="*.js" 2>/dev/null | \
  grep -oE "process\.env\.[A-Z_]+" | sort -u

# Find import.meta.env usage (Vite)
grep -rh "import\.meta\.env\." src/ --include="*.ts" --include="*.js" 2>/dev/null | \
  grep -oE "import\.meta\.env\.[A-Z_]+" | sort -u
```

### 3. Extract Variables from .env.example

```bash
cat .env.example 2>/dev/null | grep -E "^[A-Z_]+=" | cut -d= -f1
```

### 4. Merge and Categorize

Combine variables from all sources:
- From .env.example (known variables)
- From code (required variables)
- From validation schemas (types and constraints)

Categorize by prefix pattern (see template).

### 5. Generate Documentation

**Template reference:** @templates/spec/env.md

```markdown
# docs/spec/env.md
# Environment Variables

## Database
| Variable | Type | Required | Default | Description |
|----------|------|----------|---------|-------------|
| DATABASE_URL | url | Yes | - | PostgreSQL connection string |

## Authentication
...
```

### 6. Update .env.example

If variables exist in code but not in .env.example, add them:

```bash
# .env.example
# (newly discovered)
NEW_VARIABLE=""  # TODO: Add description
```
</step>

<step name="link_to_context" priority="normal">
## Link Specs to Context Files

### Update CLAUDE.md

Add reference to generated specs:

```markdown
## Documentation

Generated specifications available:
- API: `docs/spec/api/index.yaml`
- Components: `docs/spec/components/index.md`
- Schema: `docs/spec/data/schema.md`
- Environment: `docs/spec/env.md`
```

### Update references.md

Add specs as internal references:

```markdown
## Internal Documentation
- [API Specification](./docs/spec/api/index.yaml) - OpenAPI 3.0 spec
- [Component Library](./docs/spec/components/index.md) - Component documentation
- [Database Schema](./docs/spec/data/schema.md) - Data model documentation
```
</step>

<step name="commit_specs" priority="normal">
## Commit Generated Specifications

```bash
git add docs/spec/
git add .env.example 2>/dev/null
git add CLAUDE.md references.md 2>/dev/null

git commit -m "docs(spec): Generate specifications from codebase

Generated specifications:
- API: {api_route_count} endpoints documented
- Components: {component_count} components documented
- Schema: {model_count} models documented
- Environment: {env_var_count} variables documented

Co-Authored-By: BuildSensei <noreply@buildsensei.dev>"
```
</step>

<step name="report" priority="normal">
## Report Generation Results

```markdown
## Specification Generation Complete

### Generated Files

| Category | Files | Items |
|----------|-------|-------|
| API | docs/spec/api/*.yaml | {endpoint_count} endpoints |
| Components | docs/spec/components/*.md | {component_count} components |
| Schema | docs/spec/data/schema.md | {model_count} models |
| Environment | docs/spec/env.md | {var_count} variables |

### API Endpoints
{list_of_endpoints_by_resource}

### Components
{list_of_components_by_category}

### Database Models
{list_of_models}

### Environment Variables
{list_of_new_or_updated_vars}

---

**Next Steps:**
- Review generated specs for accuracy
- Add descriptions where marked TODO
- Run `/sensei:generate-specs` again after significant changes
```
</step>

</process>

<incremental_mode>
## Incremental Updates

When specs already exist, use incremental mode:

1. **Compare timestamps:**
   - Source file modified after spec → regenerate
   - Spec newer than source → skip

2. **Merge strategy:**
   - New endpoints/components → append
   - Modified signatures → update in place
   - Removed items → mark deprecated or remove

3. **Preserve manual edits:**
   - Look for `<!-- MANUAL -->` markers
   - Don't overwrite manually edited sections
</incremental_mode>

<error_handling>
## Error Handling

### No routes found
```
Warning: No API routes detected.
Checked:
- app/**/route.ts (Next.js App Router)
- pages/api/**/*.ts (Next.js Pages Router)
- src/routes/**/*.ts (Express/Fastify)

If your API uses a different pattern, specify with:
/sensei:generate-specs --api-dir src/api
```

### No components found
```
Warning: No components detected.
Checked:
- src/components/**/*.tsx
- components/**/*.tsx

If components are elsewhere, specify with:
/sensei:generate-specs --components-dir src/ui
```

### Parse errors
```
Warning: Could not parse {file}
Reason: {error}
Skipping this file. You may need to document manually.
```
</error_handling>

</generate-specs-workflow>
