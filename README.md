# ScyllaDb/Cassandra Object-Relation Mapper

[![Latest Version](https://img.shields.io/crates/v/scylla_orm.svg)](https://crates.io/crates/scylla_orm)
[![Build Status](https://img.shields.io/github/workflow/status/jasperav/scylla_orm/scylla/master)](https://github.com/jasperav/scylla_orm/actions)

# Features
This library contains several crates with the following features:

- Automatic map tables to Rust `struct`s: **scylla_orm_table_to_struct**. See [Usage](#automatic-map-tables-to-rust).
- Compile time checked queries: **scylla_orm_macro** (`scylla_orm_macro::query`). See [Usage](#scylla_orm_macro).
- Plugin derive macro to generate business logic: **scylla_orm_macro** (`scylla_orm_macro::mirror`)
- Automatic JSON mapping. When there is a column with type `text`, you can implement the
`Transformer` trait and map it to a type that implements `serde::Serialize` and `serde::Deserialize`
- All queries are executed as a prepared statement
- Support for Materialized Views (and mapping between the base table if the columns are the same)

_Not all types are supported yet due to https://github.com/scylladb/scylla-rust-driver/issues/104_

## Query types
Depending on a query, a certain query type can be derived. These can be found [here](/scylla_orm/src/query_transform.rs).
These are the commonly used query types:

- `SelectMultiple`: Can be used as an iterator to iterate over the rows
- `SelectUnique`: Selects an optional unique row by full primary key
- `SelectUniqueExpect`: Same as `SelectUnique`, but fails if the row doesn't exist
- `SelectUniqueExpect` with `Count` as entity type: has the special `select_count` method for queries like "select count(*) from ..."

There are also `struct`s for CRUD operations.

## Usage
### Automatic map tables to Rust
💡You can see an example on how to generate Rust `struct`s in the [example](/scylla_orm_table_to_struct/example) dir.

- In the build.rs file the `Transformer` trait is implemented and used for json mapping
- In the generated dir you can see the generated `struct`s

How to implement it yourself (step-by-step guide how to mimic the `example` crate):

- Add a build-dependency: `scylla_orm_table_to_struct = "0.1"`
- Create a build.rs file
- Optionally implement the `Tranformer` trait (or use `DefaultTransformer`)
- Call `scylla_orm_table_to_struct::generate`
- Build the project

### Macros
Crate `scylla_orm_macro` holds several macros that can be used to ensure compile/type checked queries.
Examples on how this can be used can be found in [lib.rs](/scylla_orm_table_to_struct/example/src/lib.rs), method `qmd`.

How to implement it yourself:
- Add dependency: `scylla_orm_macro = "0.1"`
- The `query` macro returns the correct [query type](#query-types) for the given query
- The `query_base_table` macro transforms a `select` query to the materialized view table, to a `select` query of the base table
- `mirror` and `primary_key` can be used for other derive macros