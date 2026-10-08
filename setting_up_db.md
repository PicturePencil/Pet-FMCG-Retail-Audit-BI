### Installing on server

A PostgreSQL DBMS was selected.
Setting up was tested on the Debian OS 13.7.0-amd64-netinst (Trixie) and set up trhrough Docker container:

#### docker-compose.yml
``` yaml
version: '3.8'

services:
#PGSQL
 db:
  image: postgres:15-alpine
  container_name: postgres_db
  restart: no
  env_file:
   - database.env
  ports:
   - "5432:5432" #standart port for postgreSQL
  volumes:
   - pgdata:/var/lib/postgresql/data 

volumes:
 pgdata:
```

### database.env (for creating superuser)
``` env
POSTGRES_USER=#username 
POSTGRES_PASSWORD=#password
POSTGRES_DB=#databasename
```