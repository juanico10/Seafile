# Seafile
Seafile es un sistema de software de alojamiento de archivos multiplataforma de código abierto

# About

<p align="center">
<img src="https://github.com/JuanRodenas/Seafile/blob/main/seafile.png" />
</p>

Seafile is an open-source, cross-platform file-hosting software system. Files are stored on a central server and can be synchronized with personal computers and mobile devices through apps or through the web interface.

* [Github](https://github.com/haiwen/seafile)
* [Documentation](https://manual.seafile.com/docker/deploy_seafile_with_docker/)
* [Docker Image](https://hub.docker.com/r/seafileltd/seafile-mc)

## docker-compose
Links to the following [docker-compose.yml](docker-compose.yml) and the corresponding [.env](.env).

* docker-compose.yml
  ```yaml
  version: '3'

  services:
    db:
      image: mariadb:10.5
      container_name: seafile-mysql
      restart: unless-stopped
      volumes:
        - ./seafile-mysql/db:/var/lib/mysql
      environment:
        - MYSQL_ROOT_PASSWORD=${DB_ROOT_PASSWD}  # Requested, set the root's password of MySQL service.
        - MYSQL_LOG_CONSOLE=true
      networks:
        - seafile-net
      labels:
        # Watchtower Update
        - "com.centurylinklabs.watchtower.enable=true"

    memcached:
      image: memcached:latest
      container_name: seafile-memcached
      restart: unless-stopped
      entrypoint: memcached -m 256
      networks:
        - seafile-net
      labels:
        # Watchtower Update
        - "com.centurylinklabs.watchtower.enable=true"

    seafile:
      image: seafileltd/seafile-mc:latest
      container_name: seafile
      restart: unless-stopped
      volumes:
        - ./shared:/shared
      environment:
        - DB_HOST=db
        - DB_ROOT_PASSWD=${DB_ROOT_PASSWD}  # Requested, the value shuold be root's password of MySQL service.
        - SEAFILE_ADMIN_EMAIL=${SEAFILE_ADMIN_EMAIL} # Specifies Seafile admin user, default is 'me@example.com'.
        - SEAFILE_ADMIN_PASSWORD=${SEAFILE_ADMIN_PASSWORD}     # Specifies Seafile admin password, default is 'asecret'.
        - SEAFILE_SERVER_LETSENCRYPT=false   # Whether to use https or not.
        - SEAFILE_SERVER_HOSTNAME=${TRAEFIK_SEAFILE} # Specifies your host name if https is enabled.
        - SEAFILE_SERVICE_URL=https://${TRAEFIK_SEAFILE}
      networks:
        - proxy
        - seafile-net
      depends_on:
        - db
        - memcached
      labels:
        - "traefik.enable=true"
        - "traefik.http.routers.seafile.rule=Host(`${TRAEFIK_SEAFILE}`)"
        - "traefik.http.routers.seafile.entrypoints=https"
        - "traefik.http.routers.seafile.tls=true"
        - "traefik.http.routers.seafile.tls.certresolver=mydnschallenge"
        # Watchtower Update
        - "com.centurylinklabs.watchtower.enable=true"
        # Ip filtering
        - "traefik.http.routers.seafile.middlewares=whitelist@file"

  networks:
    seafile-net:
    proxy:
      external: true
  ```
* .env
  ```ini
  TRAEFIK_SEAFILE=seafile.example.com
  DB_ROOT_PASSWD=xxxxxxxxxxxxxxx 
  SEAFILE_ADMIN_EMAIL=admin@example.com 
  SEAFILE_ADMIN_PASSWORD=xxxxxxxxxxxxxxxx     
  ```

# Usage

## Requirements
- [Traefik up and running](../traefik).
- A subdomain of your choice, this example uses `seafile`.
    - You should be able to create a subdomain with your DNS provider, use a `A record` with the same IP address as your root domain.

## Configuration
Replace the environment variables in `.env` with your own, then run :

```bash
docker-compose up -d
```

You should then be able to access the seafile web-ui with the SEAFILE_ADMIN_EMAIL and SEAFILE_ADMIN_PASSWORD.

# Update
The image is automatically updated with [watchtower](../watchtower) thanks to the following label :

```yaml
  # Watchtower Update
  - "com.centurylinklabs.watchtower.enable=true"
```
