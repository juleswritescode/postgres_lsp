> 🚧 This is in active development and not ready for use yet.

# postgres_lsp

A Language Server for Postgres. Not SQL with flavours, just Postgres.

## Rationale

Despite an ever rising popularity of Postgres, support for the PostgreSQL language in the IDE or editor of your choice is still very sparse. There are a few proprietary ones (e.g. [DataGrip](https://www.jetbrains.com/datagrip/)) that work well, but are only available within the respective IDE. Open Source attempts (e.g. [sql-language-server](https://github.com/joe-re/sql-language-server), [pgFormatter](https://github.com/darold/pgFormatter/tree/master), [sql-parser-cst](https://github.com/nene/sql-parser-cst)) mostly attempt to provide a generic SQL language server, and implement the Postgres syntax only as a flavor of their parser. This always falls short due to the ever evolving and complex syntax of PostgreSQL. This project only ever wants to support PostgreSQL, and leverages parts of the PostgreSQL server source (see [libg_query](https://github.com/pganalyze/libpg_query)) to parse the source code reliabaly. This is slightly crazy, but is the only reliable way of parsing all valid PostgreSQL queries. You can find a longer rationale on why This is the way™ [here](https://pganalyze.com/blog/parse-postgresql-queries-in-ruby). Of course, libg_query was built to execute SQL, and not to build a language server, but all of the resulting shortcomings were successfully mitigated in the [`parser`](./crates/parser/src/lib.rs) crate.

Once the parser is stable, and a robust and scalable data model is implemented, the language server will not only provide basic features such as semantic highlighting, code completion and syntax error diagnostics, but also serve as the user interface for all the great tooling of the Postgres ecosystem.

## Roadmap

At this point however, this is merely a proof of concept for building both a concrete syntax tree and an abstract syntax tree from a potentially malformed PostgreSQL source code. The `postgres_lsp` crate was created only to proof that it works e2e, and is just a very basic language server with semantic highlighting and error diagnostics. Before actual feature development can start, we have to do a bit of groundwork.

1. _Finish the parser_
   - The parser works, but the enum values for all the different syntax elements and internal conversations are manually written or copied, and, in some places, only cover a few elements required for a simple select statement. To have full coverage without possibilities for a copy and past error, they should be generated from pg_query.rs source code.
   - There are a few cases such as nested and named dollar quoted strings that cause the parser to fail due to limitations of the regex-based lexer. Nothing that is impossible to fix, or requires any change in the approach though.
2. _Implement a robust and scalable data model_
   - TODO
3. _Setup the language server properly_
   - TODO
4. _Implement basic language server features_
   - Semantic Highlighting
   - Syntax Error Diagnostics
   - Show SQL comments on hover
   - Auto-Completion
   - Code Actions, such as `Execute the statement under the cursor`, or `Execute the current file`
   - ... anything you can think of really
5. _Integrate all the existing open source tooling_
   - Show migration file lint errors from [squawk](https://github.com/sbdchd/squawk)
   - Show plpsql lint errors from [plpsql_check](https://github.com/okbob/plpgsql_check)
6. _Build missing pieces_
   - An optionated code formatter (think prettier for PostgreSQL)
7. _(Maybe) Support advanced features with declarative schema management_
   - Jump to definition
   - ... anything you can think of really

## Acknowledgments

- [rust-analyzer](https://github.com/rust-lang/rust-analyzer) for implementing such a robust, well documented, and feature-rich language server. Great place to learn from.
- [squawk](https://github.com/sbdchd/squawk) and [pganalyze](https://pganalyze.com) for inspiring the use of libg_query.
