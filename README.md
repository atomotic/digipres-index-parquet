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

```sql
INSTALL sqlite;
LOAD sqlite;
```

Attach the SQLite database

```sql
ATTACH 'practice.db' (TYPE SQLITE);
```

Schema inspect

```sql
.schema
CREATE TABLE publications(source_name VARCHAR, landing_page_url VARCHAR, document_url VARCHAR, slides_url VARCHAR, notes_url VARCHAR, "year" BIGINT, title VARCHAR, abstract VARCHAR, "language" VARCHAR, creators VARCHAR, institutions VARCHAR, license VARCHAR, size BIGINT, "type" VARCHAR, date VARCHAR, keywords VARCHAR);
CREATE TABLE publications_fts(title BLOB, creators BLOB, abstract BLOB, keywords BLOB, institutions BLOB, "type" BLOB);
CREATE TABLE publications_fts_config(k BLOB PRIMARY KEY, v BLOB);
CREATE TABLE publications_fts_data(id BIGINT PRIMARY KEY, block BLOB);
CREATE TABLE publications_fts_docsize(id BIGINT PRIMARY KEY, sz BLOB);
CREATE TABLE publications_fts_idx(segid BLOB, term BLOB, pgno BLOB, PRIMARY KEY(segid, term));
```

Export to parquet

```sql
COPY practice.publications TO digipres_index.parquet;
```

Example queries

```sql
$ duckdb

SELECT title, landing_page_url FROM digipres_index.parquet WHERE keywords like '%database%';
```

Use remote parquet: https://atomotic.github.io/digipres-index-parquet/digipres_index.parquet

```sql
SELECT title FROM 'https://atomotic.github.io/digipres-index-parquet/digipres_index.parquet' limit 10;
```

Use it in browser with [DuckDB shell](https://shell.duckdb.org/#queries=v0,select-title-from-'https%3A%2F%2Fatomotic.github.io%2Fdigipres%20index%20parquet%2Fdigipres_index.parquet'-limit-10~,select-title%2Clanding_page_url-from-'https%3A%2F%2Fatomotic.github.io%2Fdigipres%20index%20parquet%2Fdigipres_index.parquet'-where-keywords-like-'%25warc%25'~)
