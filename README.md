# Learning Data Engineering

## Docker

Learning to use SQL language with [Postgres data base](https://hub.docker.com/_/postgres/).

Starting the containers

```
mgarcia@arda:~/Work/postgres$ docker compose -f postgres.yml up
```

Checking if the containers are running

```
mgarcia@arda:~$ docker ps
CONTAINER ID   IMAGE      COMMAND                  CREATED         STATUS         PORTS                    NAMES
44d4c6401895   adminer    "entrypoint.sh php -…"   5 minutes ago   Up 5 minutes   0.0.0.0:8080->8080/tcp   postgres-adminer-1
bdaff78278ee   postgres   "docker-entrypoint.s…"   5 minutes ago   Up 5 minutes   5432/tcp                 postgres-db-1
mgarcia@arda:~$
```

Accessing the data base container

```
mgarcia@arda:~$ docker exec -it postgres-db-1 bash
root@bdaff78278ee:/# psql -U postgres
psql (15.1 (Debian 15.1-1.pgdg110+1))
Type "help" for help.

postgres=#
postgres=# quit
root@bdaff78278ee:/# exit
exit
mgarcia@arda:~$
```

> The compose file also include an `adminer` container for convenience, but make sure the _System_ is _PostgreSQL_. The data base is called `postgres`.

Next we create a _volume_ to have a persistent storage

```
mgarcia@arda:~/Work/postgres$ docker volume create postgres_data
postgres_data
mgarcia@arda:~/Work/postgres$ docker volume ls
DRIVER    VOLUME NAME
local     postgres_data
mgarcia@arda:~/Work/postgres$
```

Adding the volume to the compose file

```
services:
  db:
    (...)
    volumes:
      - postgres_data:/var/lib/postgresql/data

volumes:
  postgres_data:
    external: true
```

> Note the indentation of the `volumes` is the `services` not `db.`

```
mgarcia@arda:~/Work/my_data_eng$
mgarcia@arda:~/Work/my_data_eng$
mgarcia@arda:~/Work/my_data_eng$ docker run \
> --detach \
> -e POSTGRES_PASSWORD=example \
> --name sf_fire_calls \
> --volume postgres_data:/var/lib/postgresql/data \
> postgres
260acd52503ffc1920409294ca436af7a3c8f8d6de2cb4f9369ae8f5fb64eb6e
mgarcia@arda:~/Work/my_data_eng$ docker ps
CONTAINER ID   IMAGE      COMMAND                  CREATED         STATUS         PORTS      NAMES
260acd52503f   postgres   "docker-entrypoint.s…"   3 seconds ago   Up 2 seconds   5432/tcp   sf_fire_calls
mgarcia@arda:~/Work/my_data_eng$ docker exec sf_fire_calls bash
mgarcia@arda:~/Work/my_data_eng$ docker exec -it sf_fire_calls bash
root@260acd52503f:/# psql -h localhost -p postgres
psql: error: invalid integer value "postgres" for connection option "port"
root@260acd52503f:/# psql -h localhost -U postgres
psql (15.1 (Debian 15.1-1.pgdg110+1))
Type "help" for help.

postgres=# \l
                                                List of databases
   Name    |  Owner   | Encoding |  Collate   |   Ctype    | ICU Locale | Locale Provider |   Access privileges
-----------+----------+----------+------------+------------+------------+-----------------+-----------------------
 postgres  | postgres | UTF8     | en_US.utf8 | en_US.utf8 |            | libc            |
 template0 | postgres | UTF8     | en_US.utf8 | en_US.utf8 |            | libc            | =c/postgres          +
           |          |          |            |            |            |                 | postgres=CTc/postgres
 template1 | postgres | UTF8     | en_US.utf8 | en_US.utf8 |            | libc            | =c/postgres          +
           |          |          |            |            |            |                 | postgres=CTc/postgres
(3 rows)

postgres=# SELECT name, setting FROM pg_settings WHERE category = 'File Locations';
       name        |                 setting
-------------------+------------------------------------------
 config_file       | /var/lib/postgresql/data/postgresql.conf
 data_directory    | /var/lib/postgresql/data
 extension_destdir |
 external_pid_file |
 hba_file          | /var/lib/postgresql/data/pg_hba.conf
 ident_file        | /var/lib/postgresql/data/pg_ident.conf
(6 rows)

postgres=#
postgres=# SELECT
postgres-# name, context, unit, setting, boot_val, reset_val
postgres-# FROM pg_settings
postgres-# WHERE name IN ('listen_address', 'deadlock_timeout', 'shared_buffers', 'effective_cache_size', 'work_mem', 'maintenance_work_mem')
postgres-# ORDER BY context, name;
         name         |  context   | unit | setting | boot_val | reset_val
----------------------+------------+------+---------+----------+-----------
 shared_buffers       | postmaster | 8kB  | 16384   | 16384    | 16384
 deadlock_timeout     | superuser  | ms   | 1000    | 1000     | 1000
 effective_cache_size | user       | 8kB  | 524288  | 524288   | 524288
 maintenance_work_mem | user       | kB   | 65536   | 65536    | 65536
 work_mem             | user       | kB   | 4096    | 4096     | 4096
(5 rows)

postgres=#
postgres=# CREATE TABLE films(
postgres(# code char(5) CONSTRAINT firstkey PRIMARY KEY,
postgres(# title       varchar(40) NOT NULL
postgres(# );
CREATE TABLE
postgres=# INSERT INTO films VALUES ('Pulp fiction');
ERROR:  value too long for type character(5)
postgres=# INSERT INTO films VALUES ('001', 'Pulp fiction');
INSERT 0 1
postgres=# select * from films;
 code  |    title
-------+--------------
 001   | Pulp fiction
(1 row)

postgres=# exit
root@260acd52503f:/# exit
exit
```

### Importing CSV file

The data is coming from a CSV file, that will be imported into the data base

## SQL

_SQL is much like chess - a few hours to learn, a lifetime to master._[^1]

All SQL queries will contain some combination of these clauses

```
SELECT
FROM
WHERE
GROUP BY
HAVING
ORDER BY
```

But the actual order of executions is

1. Gathers all the data with `FROM`.
1. Filters rows of data with `WHERE`.
1. Groups rows together with the `GROUP BY`.
1. Filters grouped rows with the `HAVING`.
1. Specifies columns to display with the `SELECT`.
1. Rearranes the results with the `ORDER BY`.

[^1]: SQL Pocket Guide, Alice Zhao, O'Really Media Inc.
