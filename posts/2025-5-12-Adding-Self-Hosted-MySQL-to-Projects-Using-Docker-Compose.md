# Adding Self-Host MySQL to Projects Using Docker Compose

## Why Bother?

** Because it's faster the sqlite for managing large tabular data **.

When we use docker compose, we set it to use a unique "data" dir inside the project which we will add to our .gitignore.

In some senses, this is akin to a sqlite .db file.

However, we expect a true MySQL instance to be significantly faster than pure file-based sqlite.

## Concerning Multiple Projects

Though it is useful to have a unique mysql container for each project, you will need to choose
unique host ports for the mysql server to bind to. We will be placing this in a .env file next to docker-compose file.

Alternatively, you can bind each project to thesame port, and ensure that appropriate containers are stopped before others are started.
However I do not recommend that approach.

Either way, this is a manual process. In the future I may write or research software to automate this, but I do not currently have time.

The default port of mysql is 3306.

Suppose for my project I want to bind host port 3307 to container port 3306. The docker compose file would look like

```yml
version: '3.8'

services:
  db:
    image: mysql:8.0
    container_name: mysql
    # expose 3306 inside, map to host port from .env
    ports:
      - "${PORT}:3306"
    # bind-mount local ./data for persistence
    volumes:
      - ./data:/var/lib/mysql
    # pass through any MySQL-specific env vars you have defined
    environment:
      MYSQL_ROOT_PASSWORD: "${MYSQL_ROOT_PASSWORD}"
      MYSQL_DATABASE:      "${MYSQL_DATABASE}"
      MYSQL_USER:          "${MYSQL_USER}"
      MYSQL_PASSWORD:      "${MYSQL_PASSWORD}"
    # no auto-restart on docker or host reboot
    restart: "no"
```

Because it's only for local testing, it is ok to set a not-so-secure user and password.

Here would be an example .env file:

```bash
PORT=3307
MYSQL_ROOT_PASSWORD=rootpass
MYSQL_DATABASE=maindb
MYSQL_USER=mainuser
MYSQL_PASSWORD=mainuserpass
```

But with that said, remember to follow secure practices such as:

 - Do not expose PORT to the inernet
 - Only use this for testing.
    - For production, you will need to carefully design your own docker-compose file and manage credentials properly.
 - Turn the database on with `docker compose up -d` when workign on the project and turn it off with `docker compose down` when done.
    - It is an unnecessary security risk to leave the server running when not in use.