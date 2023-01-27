# Learning Data Engineering

## SQL

Learning to use SQL language with Postgres containers.

Starting the containers

```
mgarcia@arda:~/Work/postgres$ docker compose -f stack.yml up
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
