# Database Documentation

This project uses **Supabase** (PostgreSQL) for its data persistence layer.

## Development Concerns

The database is designed to be easily reset and restored to a working baseline. This allows for seamless iteration on schema changes without disruptions.

Supabase manages the database environment, eliminating the need for manual container setup. Configuration settings are stored in **Supabase Environment Variables**.

By convention, our database environment settings are:

```
SUPABASE_DB_USER=postgres
SUPABASE_DB_PASSWORD=postgres
SUPABASE_DB_HOST=db.supabase.co
SUPABASE_DB_PORT=5432
SUPABASE_DB_NAME=brightstar
```

### Managing the Database

#### Creating & Resetting the Database

Supabase provides built-in migration tools and SQL scripts for managing schema changes. Use the Supabase CLI to apply migrations:

```sh
supabase db push
```
To reset the database completely:
```
supabase db reset
```

### Seeding Data

Use the following scripts to load data:
• Demo Data: Preloads the database with sample data for testing features.

Testing Data: Loads minimal data for automated tests.

```
supabase db seed test.sql
```

### Database Access

Using Supabase Studio
• Navigate to Supabase Dashboard
• Select your project and open the SQL Editor to run queries.

Using VSCode PostgreSQL Extension
• Install the PostgreSQL Extension in VSCode.
• Connect using:
• Username: postgres
• Password: postgres
• Port: 5432
• Database: brightstar

### Summary
• Use Supabase CLI for database management (supabase db push/reset).
• Use Supabase Studio or VSCode PostgreSQL Extension for direct queries.
• Demo/Test data can be loaded via seeding scripts.

For more details, visit Supabase Docs.

