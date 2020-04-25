---
weight: 2
title: Installation
---

# Installation

> You can clone the repository by using this code

```bash
git clone "https://github.com/ito-org/api-backend"
```

To start with this application, download the current version by using `git clone`.

The recommended way to install the API server is to use docker with `docker-compose`.

## Docker Compose

To start `docker-compose` you need to set the following environment variables so that the server knows how to connect to your Postgres database. For that you can add an `.env` file to the root of the api-backend folder with the following lines.


### Environment Variables

> Example `.env` file

```ini
POSTGRES_HOST=<Database host>
POSTGRES_DB=<Database name>
POSTGRES_USER=<Username for db>
POSTGRES_PASSWORD=<secretpw>
```

Parameter | Required | Description
--------- | ------- | -----------
POSTGRES_HOST | yes | The host of the Postgres database
POSTGRES_DB | yes | The name of the Postgres database
POSTGRES_USER | yes | A Postgres user with access to the database
POSTGRES_PASSWORD | yes | The database user's secret password

### Configuration

> Example `docker-compose.yaml`

```yml
version: "3.7"

services:
  go-backend:
    build:
      context: .
      dockerfile: Dockerfile
    ports:
      - 8080:8080
    environment:
      - POSTGRES_PASSWORD
      - POSTGRES_USER
      - POSTGRES_DB
    depends_on:
      - postgres
    networks:
      - itonet
    
  postgres:
    image: postgres:12
    environment:
      - POSTGRES_PASSWORD
      - POSTGRES_USER
      - POSTGRES_DB
    ports:
      - 5432:5432
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

You can copy the `docker-compose.yml.example` file in the repository to `docker-compose.yml` to configure a fully working backend with the default configuration.

Run `docker-compose up` to start the containers in foreground mode or use `docker-compose up -d` to start them as daemons (in detached mode).

In the default configuration, the HTTP server will be accessible on port `8080`.