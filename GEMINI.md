# Attention

- Follow instructions in this file strictly.

## Gemini Added Memories

- When the user types the special command `$commit`, I must execute a specific workflow: 1. Identify staged files using `git diff --cached --name-only`. 2. Analyze the content of staged changes using `git diff --cached`. 3. Infer the change type (feat, fix, refactor, etc.), scope, and main intent from the diff. 4. Generate a single-line commit message in English following the Conventional Commits standard (`<type>(<scope>): <subject>`) and matching the style from `git log --oneline`. The message must be imperative and have no period. The word after the scope (egg: "feat:" or similar) must start with lowercase letter. 5. My output must be ONLY the generated commit message string, with no explanations, comments, or other text. Don't ever add other files to git or commit, just generate the message.

## Best Practices for PostgreSQL Architecture and Naming Conventions

This section defines the global standard for projects using PostgreSQL.

### General Principles
* **PostgreSQL as the Standard:** PostgreSQL is the preferred relational database.

* **Native Features:** Explicit preference for native database features (constraints, indexes, CTEs, enums, jsonb) instead of reimplementing logic in the application.

* **Philosophy:** Clarity, consistency, and predictability are the cornerstones of database architecture.

### Naming Conventions
* **Tables:** `snake_case` and in the **plural**.

* Ex: `users`, `order_items`.

* **Columns:** `snake_case`, descriptive, and without ambiguous abbreviations.

* Ex: `created_at`, `updated_at`, `user_id` (do not use `dt_create`, `uid`).

* **Primary Keys:** Name the PK column simply as `id`.

* **Foreign Keys:** Format `<entity>_id`.

* Ex: `user_id` referencing `users.id`.

* **Indexes and Constraints:** Explicit names that indicate their function and the columns involved.

* Ex: `idx_users_email`, `fk_orders_user_id`, `uq_users_email`.

### Data Modeling
* **Normalization:** Prefer normalization (3NF) by default to ensure integrity.

* **Denormalization:** Only with a clear and documented performance justification.

* **JSONB:** Use judiciously for semi-structured or frequently varying data, maintaining the relational structure for the "core" of the domain.

* **Enums:** Use native `ENUM` types for closed and stable value domains.

* **Auditing:** Main tables should have `created_at` and `updated_at` columns (with triggers or defaults for automatic updates).

### Integrity and Constraints
Data integrity should **never** be the sole responsibility of the application. Use constraints in the database:
* `PRIMARY KEY` (required)
* `FOREIGN KEY` (required for relationships)
* `UNIQUE`
* `NOT NULL` (default, unless optional)
* `CHECK` (for simple domain validations, e.g., `price >= 0`)

### Indexes
* **Required:** In all foreign keys.

* **Recommended:** Columns frequently used in filters (`WHERE`), joins (`JOIN`), and sorting (`ORDER BY`).
* **Cautions:** Avoid redundant indexes or indexes on columns with low cardinality (e.g., booleans), unless it is a partial index.
* **Composite:** Prefer composite indexes when there is a clear access pattern involving multiple columns (pay attention to the order of the columns in the index).

### Migrations
* **Versioning:** All schema changes should be made via versioned migrations.

* **Determinism:** Migrations should be deterministic.

* **Immutability:** Never edit a migration that has already been executed in any shared environment (staging, production). Create a new migration to correct or revert.

### Performance Best Practices
* **SELECT *:** Avoid returning all columns. Select only what is necessary.

* **Analysis:** Use `EXPLAIN` and `EXPLAIN ANALYZE` to diagnose and optimize slow or critical queries.

* **Pagination:** Required on endpoints or queries that may return large volumes of data (prefer cursor/keyset pagination for large datasets).

### References
* Official PostgreSQL Documentation.

* PostgreSQL industry and community standards.
