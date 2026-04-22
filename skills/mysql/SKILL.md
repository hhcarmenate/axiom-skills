---
name: MySQL
description: >
  MySQL 8.0+: schema design with InnoDB, indexes, transactions, and query optimization.
  Trigger: Building reliable MySQL databases with proper indexing and transaction handling.
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
- Designing MySQL 8.0+ schemas with InnoDB storage engine
- Creating indexes for query optimization
- Implementing transactions for data consistency
- Setting up migrations with reversible SQL
- Handling character encoding properly (utf8mb4)
- Tuning queries with EXPLAIN analysis

## Critical Patterns

### Pattern 1: Table Creation with InnoDB and Constraints
Always use InnoDB (not MyISAM) for ACID compliance and foreign key support.

```sql
-- Users table
CREATE TABLE users (
  id BIGINT AUTO_INCREMENT PRIMARY KEY,
  email VARCHAR(255) NOT NULL UNIQUE,
  username VARCHAR(50) NOT NULL UNIQUE,
  password_hash VARCHAR(255) NOT NULL,
  is_active BOOLEAN NOT NULL DEFAULT true,
  created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  
  CONSTRAINT email_format CHECK (email REGEXP '^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\\.[A-Z|a-z]{2,}$'),
  CONSTRAINT username_length CHECK (CHAR_LENGTH(username) >= 3),
  
  INDEX idx_email (email),
  INDEX idx_created_at (created_at DESC)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;

-- Posts table with foreign key
CREATE TABLE posts (
  id BIGINT AUTO_INCREMENT PRIMARY KEY,
  user_id BIGINT NOT NULL,
  title VARCHAR(500) NOT NULL,
  content LONGTEXT NOT NULL,
  is_published BOOLEAN NOT NULL DEFAULT false,
  published_at TIMESTAMP NULL,
  view_count INT NOT NULL DEFAULT 0,
  created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  
  CONSTRAINT fk_posts_user FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE,
  CONSTRAINT title_not_empty CHECK (CHAR_LENGTH(title) > 0),
  
  INDEX idx_user_id (user_id),
  INDEX idx_is_published (is_published),
  INDEX idx_created_at (created_at DESC),
  INDEX idx_user_published (user_id, is_published)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;

-- Many-to-many: Users and Roles
CREATE TABLE roles (
  id BIGINT AUTO_INCREMENT PRIMARY KEY,
  name VARCHAR(50) NOT NULL UNIQUE,
  description TEXT,
  created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;

CREATE TABLE user_roles (
  user_id BIGINT NOT NULL,
  role_id BIGINT NOT NULL,
  assigned_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  
  PRIMARY KEY (user_id, role_id),
  CONSTRAINT fk_user_roles_user FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE,
  CONSTRAINT fk_user_roles_role FOREIGN KEY (role_id) REFERENCES roles(id) ON DELETE CASCADE,
  
  INDEX idx_role_id (role_id)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

### Pattern 2: Migrations with Alembic or Raw SQL
Version control all schema changes with reversible migrations.

```sql
-- migrations/001_create_users_table.up.sql
CREATE TABLE users (
  id BIGINT AUTO_INCREMENT PRIMARY KEY,
  email VARCHAR(255) NOT NULL UNIQUE,
  username VARCHAR(50) NOT NULL UNIQUE,
  password_hash VARCHAR(255) NOT NULL,
  created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  
  INDEX idx_email (email)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;

-- migrations/001_create_users_table.down.sql
DROP TABLE IF EXISTS users;

-- migrations/002_add_posts_table.up.sql
CREATE TABLE posts (
  id BIGINT AUTO_INCREMENT PRIMARY KEY,
  user_id BIGINT NOT NULL,
  title VARCHAR(500) NOT NULL,
  content LONGTEXT NOT NULL,
  created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  
  CONSTRAINT fk_posts_user FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE,
  INDEX idx_user_id (user_id)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;

-- migrations/002_add_posts_table.down.sql
DROP TABLE IF EXISTS posts;
```

### Pattern 3: Index Strategy for Query Optimization
Create indexes based on query patterns, not reflexively.

```sql
-- Single-column indexes for equality
CREATE INDEX idx_posts_user_id ON posts(user_id);
CREATE INDEX idx_users_email ON users(email);

-- Composite index (order matters for query optimization)
-- Optimizes: WHERE user_id = ? AND is_published = ? ORDER BY created_at DESC
CREATE INDEX idx_posts_lookup ON posts(user_id, is_published, created_at DESC);

-- Covering index (includes all columns needed for query)
-- Query: SELECT id, title FROM posts WHERE user_id = ?
CREATE INDEX idx_posts_covering ON posts(user_id) INCLUDE (id, title);

-- Partial index (MySQL 5.7+, but use WHERE clause differently)
-- Only index published posts for faster reads
CREATE INDEX idx_posts_published ON posts(created_at DESC) 
WHERE is_published = true;

-- Full-text index for text search
ALTER TABLE posts ADD FULLTEXT INDEX ft_posts_search (title, content);

-- Check index usage with EXPLAIN
EXPLAIN SELECT * FROM posts WHERE user_id = 1 AND is_published = true;
```

### Pattern 4: Transactions with Isolation Levels
Use transactions to ensure consistency and prevent race conditions.

```sql
-- Simple transaction (autocommit off)
START TRANSACTION;
  UPDATE accounts SET balance = balance - 100 WHERE user_id = 1;
  UPDATE accounts SET balance = balance + 100 WHERE user_id = 2;
COMMIT;

-- Rollback on error
START TRANSACTION;
  INSERT INTO orders (user_id, total) VALUES (1, 50.00);
  -- If this fails, entire transaction rolls back
  UPDATE inventory SET quantity = quantity - 1 WHERE product_id = 123;
COMMIT;

-- Isolation levels (READ UNCOMMITTED < READ COMMITTED < REPEATABLE READ < SERIALIZABLE)
-- Set for session
SET SESSION TRANSACTION ISOLATION LEVEL REPEATABLE READ;

START TRANSACTION ISOLATION LEVEL REPEATABLE READ;
  SELECT balance FROM accounts WHERE user_id = 1; -- Consistent view
  -- ... other queries ...
COMMIT;

-- Pessimistic locking (prevents concurrent updates)
START TRANSACTION;
  SELECT * FROM accounts WHERE id = 1 FOR UPDATE; -- Lock row
  UPDATE accounts SET balance = balance - 100 WHERE id = 1;
COMMIT; -- Release lock

-- Optimistic locking (detect conflicts)
SELECT version FROM users WHERE id = 1;
-- Later, update if version hasn't changed
UPDATE users SET name = 'John', version = version + 1 
WHERE id = 1 AND version = 3;
```

### Pattern 5: Query Optimization with EXPLAIN
Analyze query execution plans to find bottlenecks.

```sql
-- EXPLAIN output shows if index is used
EXPLAIN SELECT * FROM posts WHERE user_id = 1 AND is_published = true;
-- Look for: type=range/ref (good), type=index/ALL (bad)

-- EXPLAIN EXTENDED shows optimizer insights
EXPLAIN EXTENDED SELECT p.id, p.title, u.username 
FROM posts p 
JOIN users u ON p.user_id = u.id 
WHERE p.is_published = true;

-- Check if query uses index
EXPLAIN FORMAT=JSON SELECT * FROM posts WHERE user_id = 1\G

-- Use Index hints when optimizer is wrong
SELECT * FROM posts 
USE INDEX (idx_posts_lookup)
WHERE user_id = 1 AND is_published = true;

-- ANALYZE TABLE to update statistics
ANALYZE TABLE posts;
ANALYZE TABLE users;
```

### Pattern 6: Bulk Operations and Batching
Handle large data loads efficiently.

```sql
-- Bulk insert (faster than individual inserts)
INSERT INTO posts (user_id, title, content) VALUES
  (1, 'Post 1', 'Content 1'),
  (1, 'Post 2', 'Content 2'),
  (2, 'Post 3', 'Content 3');

-- Load from file (very fast)
LOAD DATA INFILE '/path/to/file.csv'
INTO TABLE posts
FIELDS TERMINATED BY ','
(user_id, title, content);

-- Batch update
UPDATE posts 
SET view_count = CASE 
  WHEN id = 1 THEN 100
  WHEN id = 2 THEN 50
  ELSE view_count
END
WHERE id IN (1, 2);

-- Delete in batches to avoid locking entire table
DELETE FROM audit_logs WHERE created_at < DATE_SUB(NOW(), INTERVAL 30 DAY) LIMIT 10000;
-- Repeat until no rows deleted
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
├── analytics.sql
└── optimization.sql

backups/
└── daily_backup.sql

schema/
├── tables.sql
├── indexes.sql
└── views.sql
```

## Key Principles
- **Always use InnoDB**: Provides ACID compliance, foreign keys, and row-level locking.
- **utf8mb4 charset**: Supports emojis and international characters (not utf8, which is limited).
- **Normalize to 3NF**: Reduces redundancy and update anomalies.
- **Index strategically**: Index columns used in WHERE, JOIN, and ORDER BY. Measure impact with EXPLAIN.
- **Transactions for consistency**: Multi-step operations must succeed or fail as a unit.
- **Parameterized queries**: Always use prepared statements to prevent SQL injection.
- **Migrations are immutable**: Never modify old migrations; create new ones.
- **Character encoding**: Always specify utf8mb4_unicode_ci for consistency.

## Commands & Setup
```bash
# Connect to MySQL
mysql -u root -p mydb

# Create database
mysql -u root -p -e "CREATE DATABASE mydb CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;"

# Run migration (using migrate tool)
migrate -path ./migrations -database "mysql://user:password@tcp(localhost:3306)/mydb" up

# Or with Knex.js (Node.js)
npm install knex mysql2
npx knex migrate:latest

# Backup database
mysqldump -u root -p mydb > backup.sql

# Restore from backup
mysql -u root -p mydb < backup.sql

# Check table status
SHOW TABLE STATUS FROM mydb;

# Check index usage
SELECT * FROM INFORMATION_SCHEMA.STATISTICS WHERE TABLE_SCHEMA = 'mydb';
```
