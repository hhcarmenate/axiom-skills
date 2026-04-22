---
name: PostgreSQL
description: >
  PostgreSQL: advanced schema design, migrations, indexes, transactions, and full-text search.
  Trigger: Designing and optimizing PostgreSQL databases for reliability and performance.
license: MIT
metadata:
  author: Henry Carmenate
  version: "1.0"
  applies-to:
    - claude-code
    - cursor
    - codex
---

## When to Use
Use this skill when:
- Designing database schemas with proper normalization and constraints
- Creating migrations with reversible, versioned SQL
- Optimizing queries with strategic indexes
- Implementing full-text search across documents
- Using advanced features like JSON/JSONB, window functions, or CTEs
- Ensuring data consistency with transactions and isolation levels

## Critical Patterns

### Pattern 1: Schema Design with Constraints
Normalize to 3NF and use constraints to enforce data integrity.

```sql
-- Users table
CREATE TABLE users (
  id BIGSERIAL PRIMARY KEY,
  email VARCHAR(255) NOT NULL UNIQUE,
  username VARCHAR(50) NOT NULL UNIQUE,
  password_hash VARCHAR(255) NOT NULL,
  is_active BOOLEAN NOT NULL DEFAULT true,
  created_at TIMESTAMPTZ NOT NULL DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMPTZ NOT NULL DEFAULT CURRENT_TIMESTAMP,
  
  CONSTRAINT email_format CHECK (email ~* '^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Z|a-z]{2,}$'),
  CONSTRAINT username_length CHECK (LENGTH(username) >= 3)
);

-- Posts table with foreign key
CREATE TABLE posts (
  id BIGSERIAL PRIMARY KEY,
  user_id BIGINT NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  title VARCHAR(500) NOT NULL,
  content TEXT NOT NULL,
  is_published BOOLEAN NOT NULL DEFAULT false,
  published_at TIMESTAMPTZ,
  created_at TIMESTAMPTZ NOT NULL DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMPTZ NOT NULL DEFAULT CURRENT_TIMESTAMP,
  
  CONSTRAINT title_not_empty CHECK (LENGTH(title) > 0),
  CONSTRAINT published_requires_timestamp CHECK (
    (is_published AND published_at IS NOT NULL) OR
    (NOT is_published AND published_at IS NULL)
  )
);

-- Many-to-many: Users and Roles
CREATE TABLE roles (
  id BIGSERIAL PRIMARY KEY,
  name VARCHAR(50) NOT NULL UNIQUE,
  description TEXT
);

CREATE TABLE user_roles (
  user_id BIGINT NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  role_id BIGINT NOT NULL REFERENCES roles(id) ON DELETE CASCADE,
  PRIMARY KEY (user_id, role_id)
);

-- Create indexes for common queries
CREATE INDEX idx_posts_user_id ON posts(user_id);
CREATE INDEX idx_posts_is_published ON posts(is_published);
CREATE INDEX idx_posts_created_at ON posts(created_at DESC);
CREATE INDEX idx_user_roles_role_id ON user_roles(role_id);
```

### Pattern 2: Migrations with Version Control
Always use reversible migrations for reproducibility.

```sql
-- migrations/001_create_users_table.up.sql
CREATE TABLE users (
  id BIGSERIAL PRIMARY KEY,
  email VARCHAR(255) NOT NULL UNIQUE,
  password_hash VARCHAR(255) NOT NULL,
  created_at TIMESTAMPTZ NOT NULL DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_users_email ON users(email);

-- migrations/001_create_users_table.down.sql
DROP INDEX IF EXISTS idx_users_email;
DROP TABLE IF EXISTS users;

-- migrations/002_create_posts_table.up.sql
CREATE TABLE posts (
  id BIGSERIAL PRIMARY KEY,
  user_id BIGINT NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  title VARCHAR(500) NOT NULL,
  content TEXT NOT NULL,
  created_at TIMESTAMPTZ NOT NULL DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_posts_user_id ON posts(user_id);

-- migrations/002_create_posts_table.down.sql
DROP INDEX IF EXISTS idx_posts_user_id;
DROP TABLE IF EXISTS posts;
```

### Pattern 3: Strategic Indexes for Performance
Index strategically based on query patterns, not reflexively.

```sql
-- B-tree indexes (default, for equality and range queries)
CREATE INDEX idx_orders_user_id ON orders(user_id);
CREATE INDEX idx_orders_created_at ON orders(created_at DESC);

-- Composite index (order matters!)
-- Query: WHERE user_id = ? AND status = ? ORDER BY created_at DESC
CREATE INDEX idx_orders_lookup ON orders(user_id, status, created_at DESC);

-- GIN index (for JSON, arrays, full-text search)
CREATE INDEX idx_posts_tags ON posts USING GIN(tags);
CREATE INDEX idx_posts_metadata ON posts USING GIN(metadata jsonb_path_ops);

-- GIST index (for full-text search, geometric types)
CREATE INDEX idx_posts_search ON posts USING GIST(to_tsvector('english', title || ' ' || content));

-- Partial index (only index published posts)
CREATE INDEX idx_posts_published ON posts(created_at DESC) WHERE is_published = true;

-- ANALYZE to update statistics
ANALYZE;
```

### Pattern 4: Full-Text Search
Implement full-text search for document indexing.

```sql
-- Add tsvector column for search
ALTER TABLE posts ADD COLUMN search_vector TSVECTOR;

-- Create function to update search vector
CREATE OR REPLACE FUNCTION update_search_vector()
RETURNS TRIGGER AS $$
BEGIN
  NEW.search_vector := to_tsvector('english', COALESCE(NEW.title, '') || ' ' || COALESCE(NEW.content, ''));
  RETURN NEW;
END;
$$ LANGUAGE plpgsql;

-- Trigger to auto-update on INSERT/UPDATE
CREATE TRIGGER posts_search_trigger
BEFORE INSERT OR UPDATE ON posts
FOR EACH ROW EXECUTE FUNCTION update_search_vector();

-- Create index on tsvector
CREATE INDEX idx_posts_search_vector ON posts USING GIN(search_vector);

-- Search queries
SELECT id, title, ts_rank(search_vector, query) as relevance
FROM posts, plainto_tsquery('english', 'python programming') query
WHERE search_vector @@ query
ORDER BY relevance DESC
LIMIT 20;
```

### Pattern 5: Transactions and Isolation Levels
Use transactions to ensure consistency across multiple operations.

```sql
-- Simple transaction
BEGIN;
  INSERT INTO accounts (user_id, balance) VALUES (1, 1000.00);
  UPDATE users SET account_created = true WHERE id = 1;
COMMIT;

-- Rollback on error
BEGIN;
  UPDATE accounts SET balance = balance - 100 WHERE user_id = 1;
  UPDATE accounts SET balance = balance + 100 WHERE user_id = 2;
COMMIT; -- Both succeed or both fail

-- Isolation levels
-- READ UNCOMMITTED: allows dirty reads (use only for reporting)
-- READ COMMITTED: default, prevents dirty reads
-- REPEATABLE READ: prevents phantom reads
-- SERIALIZABLE: strictest, highest overhead

BEGIN TRANSACTION ISOLATION LEVEL REPEATABLE READ;
  SELECT balance FROM accounts WHERE user_id = 1 FOR UPDATE;
  -- Other queries
COMMIT;

-- Pessimistic locking (prevents concurrent updates)
BEGIN;
  SELECT * FROM accounts WHERE id = 1 FOR UPDATE; -- Lock this row
  -- ... make changes ...
  UPDATE accounts SET balance = 500 WHERE id = 1;
COMMIT;
```

### Pattern 6: Window Functions for Analytics
Use window functions for ranking, running totals, and analytics.

```sql
-- Rank posts by user within month
SELECT 
  id,
  user_id,
  title,
  created_at,
  ROW_NUMBER() OVER (PARTITION BY user_id, DATE_TRUNC('month', created_at) ORDER BY created_at) as post_number,
  RANK() OVER (PARTITION BY user_id ORDER BY views DESC) as popularity_rank
FROM posts;

-- Running total of revenue
SELECT 
  order_date,
  amount,
  SUM(amount) OVER (ORDER BY order_date) as cumulative_revenue,
  LAG(amount) OVER (ORDER BY order_date) as previous_amount,
  LEAD(amount) OVER (ORDER BY order_date) as next_amount
FROM orders
ORDER BY order_date;
```

## Folder Structure
```
migrations/
├── 001_create_users_table.up.sql
├── 001_create_users_table.down.sql
├── 002_create_posts_table.up.sql
├── 002_create_posts_table.down.sql
└── ... (numbered sequentially)

seeds/
├── users.sql
├── roles.sql
└── initial_data.sql

queries/
├── user_queries.sql
├── post_queries.sql
└── analytics.sql

indexes/
└── optimization.sql

schema/
├── tables.sql
├── views.sql
└── functions.sql
```

## Key Principles
- **Normalize to 3NF**: Reduce redundancy and anomalies.
- **Use constraints**: NOT NULL, UNIQUE, CHECK, FOREIGN KEY enforce integrity.
- **Index purposefully**: Index query columns, not reflexively. Measure before and after.
- **Transactions for atomicity**: Multi-step operations must succeed or fail as a unit.
- **Prepared statements**: Always use parameterized queries to prevent SQL injection.
- **EXPLAIN ANALYZE**: Profile queries before optimizing.
- **Migrations are immutable**: Never modify old migrations; create new ones for changes.
- **JSONB for semi-structured data**: Use JSONB (not JSON) for flexible fields; it's indexed and faster.

## Commands & Setup
```bash
# Connect to PostgreSQL
psql -U postgres -d mydb

# Create database
createdb mydb

# Run migrations (using migrate tool)
migrate -path ./migrations -database "postgresql://user:pass@localhost/mydb" up

# Or with Knex.js (Node.js)
npm install knex pg
npx knex migrate:latest

# Backup database
pg_dump -U postgres mydb > backup.sql

# Restore from backup
psql -U postgres mydb < backup.sql

# Run SQL file
psql -U postgres -d mydb -f script.sql
```
