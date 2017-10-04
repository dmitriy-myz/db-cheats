active queries
9.1
```sql

select
 procpid,
 usename,
 NOW() - query_start AS duration,
 waiting,
 current_query
from
 pg_stat_activity
where
 current_query <> '<IDLE>'
ORDER BY duration DESC;
```

9.3
```sql
select
  pid,
  usename,
  NOW() - query_start AS duration,
  waiting,
  query
from
  pg_stat_activity
where
  state <> ‘idle’
ORDER BY duration DESC;
```


grants for user
```sql
SELECT grantee, table_schema || '.' || table_name AS relname, string_agg(privilege_type, ', ') AS privileges
FROM information_schema.role_table_grants
WHERE grantee = 'UserName' 
GROUP BY grantee, relname,table_name;
```
replication state
```sql
select client_addr, state, sent_location, write_location,
    flush_location, replay_location from pg_stat_replication;
```

replication lag (run on slave)
```sql
SELECT
CASE
WHEN pg_last_xlog_receive_location() = pg_last_xlog_replay_location() THEN 0
 ELSE EXTRACT (EPOCH FROM now() - pg_last_xact_replay_timestamp())::INTEGER
END
AS replication_lag;
```
tables size
```sql
SELECT
schemaname||'.'||tablename AS full_tname,
pg_size_pretty(pg_total_relation_size(schemaname||'.'||tablename)) AS total_usage,
pg_size_pretty((pg_total_relation_size(schemaname||'.'||tablename) - pg_relation_size(schemaname||'.'||tablename))) AS external_table_usage
FROM pg_catalog.pg_tables
ORDER BY pg_total_relation_size(schemaname||'.'||tablename) DESC
```
indexes size
```sql
SELECT
schemaname||'.'||indexname AS full_iname,
pg_size_pretty(pg_total_relation_size(schemaname||'.'||indexname)) AS total_usage,
pg_size_pretty((pg_total_relation_size(schemaname||'.'||indexname) - pg_relation_size(schemaname||'.'||indexname))) AS external_index_usage
FROM pg_catalog.pg_indexes
ORDER BY pg_total_relation_size(schemaname||'.'||indexname) DESC
```
vacuum count
```sql
SELECT
schemaname||'.'||relname as full_relname,
vacuum_count, autovacuum_count, analyze_count, autoanalyze_count
from  pg_stat_all_tables;
```

