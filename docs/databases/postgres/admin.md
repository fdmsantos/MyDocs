# Postgres

## Active Connections

* show active connections

```sql
select pid as process_id, 
       usename as username, 
       datname as database_name, 
       client_addr as client_address, 
       application_name,
       backend_start,
       state,
       state_change
from pg_stat_activity;
```

* Terminate Active Sessions

```sql
select pg_terminate_backend(pid) from pg_stat_activity where datname='<database>';
```