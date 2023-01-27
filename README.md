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

## SQL

All SQL queries will contain some combination of these clauses[^1]

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
