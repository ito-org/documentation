---
weight: 2
title: Install Server
---

# Installing Server

> You can clone the repositoring by using this code

```bash
git clone https://github.com/ito-org/api-backend.git
```

To start with this application, download the current version by using git clone.

The recommanded way to install the Server is to use docker with docker-compose.

## Docker Compose

To start the ``docker-compose`` you need to add a ``.env`` file to the root of the api-backend folder with the following lines.


### .env Variables

> Example of the .env file

```
POSTGRES_DB:       <Database name>
POSTGRES_USER:     <Username for db>
POSTGRES_PASSWORD: <secretpw>
```

Parameter | Requied | Description
--------- | ------- | -----------
POSTGRES_DB | yes | The name of the postgres database
POSTGRES_USER | yes | The postgres user with access to the database
POSTGRES_PASSWORD | yes | A secret password for the database user

### Docker-Compose Example

> Example docker-compose.yaml

```docker
version: "3"

services:
  go-backend:
    build:
      context: .
      dockerfile: Dockerfile
    env_file: .env
    ports:
      - 8080:8080
    depends_on:
      - postgres
    networks:
      - itonet
    
  postgres:
    image: postgres:12
    env_file: .env
    volumes:
      - ./db/db.sql:/docker-entrypoint-initdb.d/db.sql
      - dbvol:/var/lib/postgresql/data
    networks:
      - itonet
    restart: always
  
networks:
  itonet:

volumes:
  dbvol:
```

You can use the example ``docker-compose.yml`` in the repository to start a fully working installation backend.

Starting the containers with ``docker-compose up`` to start it in forground mode or use ``docker-compose up -d`` to start it as a deamon. 