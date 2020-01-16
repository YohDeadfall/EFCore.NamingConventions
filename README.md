# Naming Conventions for Entity Framework Core Tables and Columns

[![Nuget](https://img.shields.io/nuget/v/EFCore.NamingConventions)](https://www.nuget.org/packages/EFCore.NamingConventions/)

By default, EF Core will map to tables and columns named exactly after your .NET classes and properties. For example, mapping a typical Customer class to PostgreSQL will result in SQL such as the following:

```sql
CREATE TABLE "Customers" (
    "Id" integer NOT NULL GENERATED BY DEFAULT AS IDENTITY,
    "FullName" text NULL,
    CONSTRAINT "PK_Customers" PRIMARY KEY ("Id")
);

SELECT c."Id", c."FullName"
    FROM "Customers" AS c
    WHERE c."FullName" = 'John Doe';
```

For PostgreSQL specifically, this forces double-quotes to be added since unquoted identifiers are automatically converted to lower-case - and all those quotes are an eye-sore. But even if we're using another database such as SQL Server, maybe we just hate seeing upper-case letters in our database, and would rather have another naming convention.

Down with same-name identifier tyranny! Simply add a reference to [EFCore.NamingConventions](https://www.nuget.org/packages/EFCore.NamingConventions/1.0.0-rc1) and enable a naming convention in your model's `OnConfiguring` method:

```c#
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseNpgsql(...)
        .UseSnakeCaseNamingConvention();
```

This will automatically make all your table and column names have snake_case naming:

```sql
CREATE TABLE customers (
            id integer NOT NULL GENERATED BY DEFAULT AS IDENTITY,
            full_name text NULL,
            CONSTRAINT "PK_customers" PRIMARY KEY (id);

SELECT c.id, c.full_name
        FROM customers AS c
        WHERE c.full_name = 'John Doe';
```

Currently, only snake_case is supported, but we can add more conventions as people request them.

Some important notes:

* If you have an existing database, adding this naming convention will cause a migration to produced, renaming everything. Be very cautious when doing this (the process currently involves dropping and recreating primary keys).
* This plugin will work with any database provider and isn't related to PostgreSQL or Npgsql in any way.
* This is a community-maintained plugin: it isn't an official part of Entity Framework Core and isn't supported by Microsoft in any way.
