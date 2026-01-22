# Plan: {Feature Name}

**Feature ID:** {XXX-feature-name}
**Status:** DRAFT
**Created:** {DATE}

## Technical Approach

{High-level technical solution}

## Architecture

### Component Diagram

```
┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│   Frontend  │───▶│    API      │───▶│  Database   │
│             │    │   Gateway   │    │             │
└─────────────┘    └─────────────┘    └─────────────┘
```

### Technology Choices

| Component | Technology | Rationale |
|-----------|------------|-----------|
| API | {Choice} | {Why} |
| Database | {Choice} | {Why} |
| Caching | {Choice} | {Why} |

## Data Model

### Entity: {Entity Name}

```sql
CREATE TABLE {table_name} (
    id BIGSERIAL PRIMARY KEY,
    {field1} {type} NOT NULL,
    {field2} {type},
    created_at TIMESTAMP DEFAULT NOW(),
    updated_at TIMESTAMP DEFAULT NOW()
);
```

### Relationships

{Describe relationships between entities}

## API Design

### Endpoints

| Method | Path | Description |
|--------|------|-------------|
| POST | /api/resource | Create resource |
| GET | /api/resource/:id | Get resource |
| PUT | /api/resource/:id | Update resource |
| DELETE | /api/resource/:id | Delete resource |

## External Dependencies

| Dependency | Version | Purpose |
|------------|---------|---------|
| {Library} | {Version} | {Why needed} |

## Security Considerations

- {Security item 1}
- {Security item 2}

## Performance Considerations

- {Performance item 1}
- {Performance item 2}

## Migration Strategy

{How to migrate from current state}

## Rollback Strategy

{How to rollback if needed}
