## Nextcloud with Redis and MariaDB
This example defines one of the basic setups for Nextcloud. NextCloud is a true open source, self-hosted cloud storage service and a fork of OwnCloud. 
Like DropBox and other cloud storage services, NextCloud provides similar functions and unlike the other proprietary storage services, NextCloud is free to use.
Here I used mariadb database to store the data.


Project structure:
```
.
├── docker-compose.yaml
└── README.md
```

[_docker-compose.yaml_](docker-compose.yaml)
```
version: '3'
services:
  database:
    image: mariadb
    command: --transaction-isolation=READ-COMMITTED --binlog-format=ROW
    restart: always
    volumes:
      - db:/var/lib/mysql
    environment:
      - MYSQL_ROOT_PASSWORD=redhat
      - MYSQL_PASSWORD=redhat
      - MYSQL_DATABASE=mydatabase
      - MYSQL_USER=redhat

  app:
    image: nextcloud
    ports:
      - 80:80
    links:
      - database
    volumes:
      - nextcloud:/var/www/html
    networks:
      - mynet
    restart: always

volumes:
  nextcloud:
  db:
networks:
  mynet:

When deploying this setup, docker-compose maps the nextcloud container port 80 to
port 80 of the host as specified in the compose file.

## Deploy with docker-compose

```
To deploy the onfiguration, use this below command.
$ docker-compose up -d

## Expected result

Check containers are running and the port mapping:
```
$ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                NAMES
6541add4d648        nextcloud:apache    "/entrypoint.sh apac…"   35 seconds ago      Up 34 seconds       0.0.0.0:80->80/tcp   nextcloud-mariadb_nc_1
6d4c6630a4a3        mariadb             "docker-entrypoint.s…"   35 seconds ago      Up 34 seconds       3306/tcp             nextcloud-mariadb_db_1
```

Navigate to `http://localhost:80` in your web browser to access the installed
Nextcloud service.

Stop and remove the containers

```
$ docker-compose down
```

To delete all data, remove all named volumes by passing the `-v` arguments:
```
$ docker-compose down -v
```
