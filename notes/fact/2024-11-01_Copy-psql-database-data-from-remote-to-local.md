---
date: 2024-11-01
type: fact
aliases:
  -
hubs:
  - "[[Linux]]"
---

# Copy psql database data from remote to local

```bash
# from
ORIGINS='["http://localhost:5173"]'
DB_HOST='192.168.66.221'
DB_PORT=5432
DB_USER='warroom_rw'
DB_PASSWORD='warroom@Ml22099478!'

# to
DB_HOST='localhost'
DB_PORT=5432
DB_USER='postgres'
DB_PASSWORD='123'
```
假設要 copy database `mirle_dashboard` 的資料, 可以使用以下指令:

```bash
pg_dump -h 192.168.66.221 -U warroom_rw -d mirle_dashboard > mirle_dashboard_dump.sql

```
上面指令會產生 `mirle_dashboard_dump.sql` 檔案, 接著使用以下指令將資料匯入到 local 的 database:

```bash
psql -h localhost -U postgres -d mirle_dashboard < mirle_dashboard_dump.sql

```

注意: 上面的 dump 檔案是包含 schema 的, 如果只要匯入資料, 可以使用以下指令:

```bash
pg_dump -h 192.168.66.221 -U warroom_rw -d mirle_dashboard --data-only > mirle_dashboard_data_dump.sql
```

差別是增加 `--data-only` 參數, 這樣就只會 dump 資料而不包含 schema 了。
