<schema-spec-template>

<purpose>
Template and guidance for generating database schema specifications from ORM models.
Used by /sensei:generate-specs to create data model documentation.
</purpose>

<detection_patterns>
## Schema Detection Patterns

### Prisma
```prisma
model User {
  id        String   @id @default(cuid())
  email     String   @unique
  name      String?
  posts     Post[]
  createdAt DateTime @default(now())
}
```

### Drizzle
```typescript
export const users = pgTable('users', {
  id: serial('id').primaryKey(),
  email: varchar('email', { length: 255 }).notNull().unique(),
  name: varchar('name', { length: 255 }),
  createdAt: timestamp('created_at').defaultNow()
});
```

### TypeORM
```typescript
@Entity()
export class User {
  @PrimaryGeneratedColumn('uuid')
  id: string;

  @Column({ unique: true })
  email: string;

  @Column({ nullable: true })
  name: string;

  @OneToMany(() => Post, post => post.author)
  posts: Post[];
}
```

### Sequelize
```typescript
User.init({
  id: { type: DataTypes.UUID, primaryKey: true },
  email: { type: DataTypes.STRING, unique: true },
  name: { type: DataTypes.STRING, allowNull: true }
}, { sequelize });
```

### Mongoose
```typescript
const userSchema = new Schema({
  email: { type: String, required: true, unique: true },
  name: { type: String },
  posts: [{ type: Schema.Types.ObjectId, ref: 'Post' }]
});
```

### Kysely (Type-only)
```typescript
interface Database {
  users: {
    id: Generated<number>;
    email: string;
    name: string | null;
    created_at: Generated<Date>;
  };
}
```
</detection_patterns>

<extraction_rules>
## Information Extraction

### Per Model/Table
- Table/collection name
- Description (from comments)
- Fields with types
- Primary key(s)
- Unique constraints
- Indexes
- Default values
- Nullable status

### Relationships
- One-to-one
- One-to-many
- Many-to-many
- Self-referential
- Polymorphic

### Constraints
- Foreign keys
- Check constraints
- Unique combinations
- Cascading rules

### Metadata
- Created/updated timestamps
- Soft delete fields
- Versioning fields
</extraction_rules>

<output_template>
## Schema Specification Template

```markdown
# Database Schema

> {Project name} data model specification

**Database:** {PostgreSQL | MySQL | MongoDB | SQLite}
**ORM:** {Prisma | Drizzle | TypeORM | Mongoose | None}
**Last Updated:** {timestamp}

## Overview

{High-level description of the data model}

### Entity Relationship Diagram

```
┌─────────────┐       ┌─────────────┐
│    User     │       │    Post     │
├─────────────┤       ├─────────────┤
│ id (PK)     │───┐   │ id (PK)     │
│ email       │   │   │ title       │
│ name        │   └──<│ authorId(FK)│
│ createdAt   │       │ content     │
└─────────────┘       │ createdAt   │
                      └─────────────┘
```

## Models

---

### {ModelName}

> {One-line description}

**Table:** `{table_name}`
**Primary Key:** `{pk_field}`

#### Fields

| Field | Type | Nullable | Default | Description |
|-------|------|----------|---------|-------------|
| {field_name} | `{db_type}` | {Yes/No} | {default} | {description} |

#### Indexes

| Name | Fields | Type | Description |
|------|--------|------|-------------|
| {index_name} | `{fields}` | {UNIQUE/BTREE/GIN} | {purpose} |

#### Relationships

| Relation | Target | Type | Foreign Key | On Delete |
|----------|--------|------|-------------|-----------|
| {relation_name} | `{TargetModel}` | {1:1/1:N/N:M} | `{fk_field}` | {CASCADE/SET NULL/RESTRICT} |

#### Constraints

| Constraint | Type | Expression |
|------------|------|------------|
| {name} | {CHECK/UNIQUE} | {expression} |

#### Example Record

```json
{
  "id": "{example_id}",
  "field": "{example_value}"
}
```

---

## Relationships Map

### User → Post (One-to-Many)
- A User has many Posts
- A Post belongs to one User
- Cascade: Deleting User deletes all Posts

### Post ↔ Tag (Many-to-Many)
- A Post can have many Tags
- A Tag can be on many Posts
- Junction table: `post_tags`

## Enums

### {EnumName}

| Value | Description |
|-------|-------------|
| `{VALUE}` | {description} |

## Migrations

| Version | Date | Description |
|---------|------|-------------|
| {version} | {date} | {description} |

### Recent Migration: {migration_name}

```sql
-- Up
{up_sql}

-- Down
{down_sql}
```

## Queries

### Common Queries

#### Get User with Posts
```sql
SELECT u.*, p.*
FROM users u
LEFT JOIN posts p ON p.author_id = u.id
WHERE u.id = $1;
```

#### ORM Equivalent
```typescript
// Prisma
const user = await prisma.user.findUnique({
  where: { id },
  include: { posts: true }
});
```

## Data Integrity Rules

1. **Email uniqueness:** User emails must be unique across the system
2. **Referential integrity:** Posts must have valid author references
3. **Soft deletes:** {Models} use soft delete with `deletedAt` field

## Performance Notes

- **Index on `users.email`:** Speeds up login queries
- **Composite index on `posts(authorId, createdAt)`:** Optimizes user post listings

---
*Auto-generated by BuildSensei. Last updated: {timestamp}*
```
</output_template>

<file_organization>
## Output File Structure

```
docs/spec/data/
├── schema.md             # Complete schema documentation
├── erd.md                # Entity relationship diagram
├── models/
│   ├── User.md           # Detailed User model
│   ├── Post.md           # Detailed Post model
│   └── {Model}.md
├── migrations.md         # Migration history
└── queries.md            # Common query patterns
```

### For Large Schemas
Split by domain:

```
docs/spec/data/
├── overview.md
├── auth/                 # Auth domain
│   ├── User.md
│   ├── Session.md
│   └── Token.md
├── content/              # Content domain
│   ├── Post.md
│   ├── Comment.md
│   └── Tag.md
└── billing/              # Billing domain
    ├── Customer.md
    ├── Subscription.md
    └── Invoice.md
```
</file_organization>

<type_mapping>
## ORM to Documentation Type Mapping

### Prisma Types
| Prisma | SQL | Documentation |
|--------|-----|---------------|
| String | VARCHAR | `string` |
| Int | INTEGER | `integer` |
| BigInt | BIGINT | `bigint` |
| Float | DOUBLE | `float` |
| Decimal | DECIMAL | `decimal` |
| Boolean | BOOLEAN | `boolean` |
| DateTime | TIMESTAMP | `datetime` |
| Json | JSONB | `json` |
| Bytes | BYTEA | `bytes` |

### Drizzle Types
| Drizzle | SQL | Documentation |
|---------|-----|---------------|
| serial | SERIAL | `auto-increment integer` |
| varchar | VARCHAR | `string(length)` |
| text | TEXT | `text` |
| integer | INTEGER | `integer` |
| boolean | BOOLEAN | `boolean` |
| timestamp | TIMESTAMP | `datetime` |
| json | JSON | `json` |
| uuid | UUID | `uuid` |

### Mongoose Types
| Mongoose | MongoDB | Documentation |
|----------|---------|---------------|
| String | string | `string` |
| Number | number | `number` |
| Date | date | `datetime` |
| Boolean | bool | `boolean` |
| ObjectId | objectId | `reference` |
| Array | array | `array` |
| Mixed | object | `any` |
</type_mapping>

<relationship_notation>
## Relationship Documentation

### One-to-One
```
User ──── Profile
  │         │
  └─────────┘
  user.profile / profile.user
```

### One-to-Many
```
User ────< Post
  │         │
  └─────────┘
  user.posts / post.author
```

### Many-to-Many
```
Post >────< Tag
  │    │    │
  │    │    │
  └────┴────┘
  (via PostTag junction)
```

### Self-Referential
```
User ──┐
  │    │
  └────┘
  user.manager / user.reports
```
</relationship_notation>

</schema-spec-template>
