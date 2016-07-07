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




