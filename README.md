# Digipres practice index as parquet

Download the SQLite file

```
wcurl https://github.com/digipres/digipres-practice-index/raw/main/releases/practice.db
```

Launch DuckDB

```
$ duckdb
```

Install [SQLite Extension](https://duckdb.org/docs/extensions/sqlite.html)

```
INSTALL sqlite;
LOAD sqlite;
```

Attach the SQLite database

```
ATTACH 'practice.db' (TYPE SQLITE);
```

Schema inspect

```
.schema
CREATE TABLE publications(source_name VARCHAR, landing_page_url VARCHAR, document_url VARCHAR, slides_url VARCHAR, notes_url VARCHAR, "year" BIGINT, title VARCHAR, abstract VARCHAR, "language" VARCHAR, creators VARCHAR, institutions VARCHAR, license VARCHAR, size BIGINT, "type" VARCHAR, date VARCHAR, keywords VARCHAR);
CREATE TABLE publications_fts(title BLOB, creators BLOB, abstract BLOB, keywords BLOB, institutions BLOB, "type" BLOB);
CREATE TABLE publications_fts_config(k BLOB PRIMARY KEY, v BLOB);
CREATE TABLE publications_fts_data(id BIGINT PRIMARY KEY, block BLOB);
CREATE TABLE publications_fts_docsize(id BIGINT PRIMARY KEY, sz BLOB);
CREATE TABLE publications_fts_idx(segid BLOB, term BLOB, pgno BLOB, PRIMARY KEY(segid, term));
```

Export to parquet

```
COPY practice.publications TO digipres_index.parquet;
```

Example queries

```sql
$ duckdb

SELECT title, landing_page_url FROM digipres_index.parquet WHERE keywords like '%database%';
```
