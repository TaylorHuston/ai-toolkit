---
# === Metadata ===
template_type: "documentation"
last_updated: "{current-date}"
status: "Active"
target_audience: ["AI Assistants", "Development Team", "Database Engineers"]
description: "Complete database schema reference with ER diagrams, cascade behavior, index strategy, and query patterns"
title: "Data Model Documentation"
category: "Database"
tags: ["database-schema", "data-model", "entity-relationships", "orm"]
---

# Data Model Documentation

## Overview

The {app-name} database schema consists of **{N} core models** organized in {hierarchical/flat/graph} structure:

- **{ModelName}**: {Brief description of purpose}
- **{ModelName}**: {Brief description of purpose}
- **{ModelName}**: {Brief description of purpose}

{If applicable, list legacy/deprecated models}

## Entity Relationship Diagram

```
{ASCII/Mermaid diagram showing:
- Entity boxes with key fields
- Relationship lines with cardinality
- Cascade delete indicators (CASCADE/SET NULL/RESTRICT)
- Arrows showing direction of relationships

Example structure:
┌──────────────┐
│  ParentModel │
│──────────────│
│ id: String   │
│ name: String │
└──────┬───────┘
       │
       │ CASCADE
       ▼
┌──────────────┐
│  ChildModel  │
│──────────────│
│ id: String   │
│ parentId     │
└──────────────┘
}
```

**Relationship Key:**
- **{Parent} → {Child}**: {One-to-Many/Many-to-Many} with {CASCADE DELETE/SET NULL/RESTRICT}
- **{Entity A} ↔ {Entity B}**: {Relationship type and behavior}

## Core Models

### {ModelName}

**Purpose**: {What this model represents in the domain}

**Key Properties:**
- `id` ({Type}): {Description and constraints}
- `{fieldName}` ({Type}, {UNIQUE/NOT NULL/optional}): {Description}
- `{fieldName}` ({Type}): {Description}
- `createdAt` (DateTime): {Auto-managed timestamp}
- `updatedAt` (DateTime): {Auto-managed timestamp}

**Relationships:**
- {Has many/Belongs to} `{RelatedModel}` ({CASCADE DELETE/SET NULL/RESTRICT})
- {Relationship description with cascade behavior}

**Use Cases:**
- "{Example query pattern}"
- "{Example query pattern}"
- "{Example query pattern}"

{Repeat for each core model}

## Cascade Delete Behavior

### CASCADE DELETE

When a parent entity is deleted, all child entities are **automatically deleted**.

```
DELETE {ParentModel}
  └──> CASCADE deletes all {ChildModel}
  └──> CASCADE deletes all {RelatedModel}
```

**Example:**
```{language}
// Example code showing cascade delete
await {orm}.{model}.delete({ where: { id: '{id}' } });
// Automatically deletes:
// - {List of cascaded deletions}
```

### SET NULL

When a parent entity is deleted, child entities **remain** but their foreign key is set to `NULL`.

```
DELETE {OptionalParent}
  └──> SET NULL on {Child}.{parentId} ({explanation of result})
```

**Example:**
```{language}
// Example code showing SET NULL behavior
await {orm}.{model}.delete({ where: { id: '{id}' } });
// {Explanation of what happens to child records}
```

### RESTRICT

When a parent entity has children, deletion is **prevented** until children are removed.

```
DELETE {ProtectedParent} → ERROR if children exist
```

{If applicable, add other cascade behaviors: NO ACTION, CASCADE UPDATE, etc.}

### Why This Design?

**{Model} CASCADE**: {Rationale for using CASCADE DELETE}

**{Model} SET NULL**: {Rationale for using SET NULL}

**{Model} RESTRICT**: {Rationale for using RESTRICT}

## Index Strategy

### Primary Access Patterns

All models are indexed to optimize common query patterns:

#### {ModelName} Indexes

```{schema-language}
@@index([{field}])                // {Query pattern this optimizes}
@@index([{field}])                // {Query pattern this optimizes}
@@index([{field1}, {field2}])     // Composite: {query pattern}
```

**Example Queries:**
```{language}
// Optimized by @@index([{field1}, {field2}])
await {orm}.{model}.findMany({
  where: {
    {field1}: '{value}',
    {field2}: '{value}'
  }
});

// Optimized by @@index([{field}])
await {orm}.{model}.findMany({
  where: { {field}: '{value}' },
  orderBy: { {field}: '{direction}' },
  take: 10
});
```

{Repeat for each model}

### Composite Index Benefits

Composite indexes like `@@index([{field1}, {field2}])` enable efficient queries filtering by **both** fields:

**Without composite index:**
```sql
-- {Explanation of inefficient query execution}
SELECT * FROM {Table} WHERE {field1} = ? AND {field2} = ?;
```

**With composite index:**
```sql
-- {Explanation of efficient query execution}
SELECT * FROM {Table} WHERE {field1} = ? AND {field2} = ?;
```

## Foreign Key Enforcement

### {Database} Configuration

{If database requires special configuration for FK enforcement, document it here}

**Current Implementation:**

```{language}
// {Location of configuration}
{code snippet showing FK configuration}
```

{Explain what this configuration enables}

**Without {configuration}:**
- {List of problems that occur}
- {Data integrity issues}

**Testing Foreign Keys:**
```{language}
// This should FAIL with foreign key constraint violation
await {orm}.{model}.create({
  data: {
    {foreignKey}: 'non-existent-id',
    // ... other fields
  }
});
// Error: {Expected error message}
```

### {Alternative Database} Migration Notes

When migrating to {alternative database}:

1. **{Feature} is {default behavior}**: {Explanation}
2. **{Feature} works {differently}**: {Explanation}
3. **Consider {enhancement}**: {Recommendation with code example}
4. **Migration strategy:**
   - {Step 1}
   - {Step 2}
   - {Step 3}

## Enum Definitions

### {EnumName}

```{schema-language}
enum {EnumName} {
  {VALUE}  // {Description of what this value means}
  {VALUE}  // {Description of state/status/type}
  {VALUE}  // {Description and when used}
}
```

{Repeat for each enum}

## Database Connection Configuration

**Development:**
```
DATABASE_URL="{connection-string}"
# {Explanation of what this creates/configures}
```

**Test:**
```
DATABASE_URL="{test-connection-string}"
# {Explanation of test database setup}
```

**Production:**
```
DATABASE_URL="{production-connection-string}"
# {Notes about production database}
```

## Common Query Patterns

### {ModelName} Queries

```{language}
// {Description of query purpose}
const {result} = await {orm}.{model}.{operation}({
  where: { {conditions} },
  include: {
    {relations}: true
  }
});

// {Another common query pattern}
const {result} = await {orm}.{model}.{operation}({
  where: { {conditions} },
  orderBy: { {field}: '{direction}' }
});

// {Query with aggregation/grouping if applicable}
const {result} = await {orm}.{model}.{operation}({
  by: ['{field1}', '{field2}'],
  where: { {conditions} },
  _count: true
});
```

{Repeat for each model with common query examples}

## Testing Considerations

### Test Database Setup

```{language}
// {test-setup-location}
import { {ORM} } from '{orm-import}';

export const testDb = new {ORM}({
  datasources: {
    db: {
      url: '{test-database-url}'
    },
  },
});

beforeEach(async () => {
  // Reset database before each test
  {cleanup-code-in-dependency-order}
});
```

### Test Factories

{If using test factories, document location and usage}

```{language}
import { create{Model} } from '{factory-location}';

// Create test data with sensible defaults
const {instance} = await create{Model}(testDb);
const {related} = await create{Related}(testDb, {instance}.id);
```

## Migration History

Current migration: `{migration-timestamp}_{migration-name}`

**Changes:**
- {List of schema changes in this migration}
- {Enums added/modified}
- {Indexes created}
- {Constraints added}

**Future migrations:** Track in `{migrations-directory}` directory.

## Performance Considerations

### Query Optimization

**N+1 Query Prevention:**
```{language}
// ❌ BAD: N+1 queries
const {parents} = await {orm}.{model}.findMany();
for (const parent of {parents}) {
  const children = await {orm}.{child}.findMany({
    where: { {parentId}: parent.id }
  });
}

// ✅ GOOD: Single query with include
const {parents} = await {orm}.{model}.findMany({
  include: { {children}: true }
});
```

**Pagination:**
```{language}
// Cursor-based pagination for large datasets
const {results} = await {orm}.{model}.findMany({
  take: 20,
  skip: 1,
  cursor: { id: {lastId} },
  orderBy: { {field}: '{direction}' }
});
```

### Database Maintenance

**Regular maintenance tasks:**
- {Task 1 - e.g., VACUUM, ANALYZE}
- {Task 2 - e.g., Index rebuilding}
- {Task 3 - e.g., Backup strategy}

## Schema Evolution Guidelines

When modifying the schema:

1. **Always create migrations**: {migration-command}
2. **Test migrations in development**: {test-command}
3. **Review generated SQL**: Check `{migrations-directory}/{timestamp}/migration.sql`
4. **Update this documentation**: Keep data model docs in sync
5. **Update test factories**: Ensure factories match new schema
6. **Consider backwards compatibility**: {Guidelines for non-breaking changes}

## Related Documentation

- [{Schema File}]({path}) - Full schema with inline documentation
- [{Architecture Overview}]({path}) - System design and architecture decisions
- [{ADR}]({path}) - Database technology selection rationale
- [{Testing Standards}]({path}) - Test database setup and conventions

## Changelog

- **{date}**: {Description of change}
- **{date}**: {Description of change}
- **{date}**: Initial data model documentation created
