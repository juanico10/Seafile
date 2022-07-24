## Seafile
Seafile es un sistema de software de alojamiento de archivos multiplataforma de código abierto

## About

<p align="center">
  <img src="https://github.com/JuanRodenas/Seafile/blob/main/seafile.png"
       width="700"/>
</p>

<p align="center">
  <img src="https://logos-world.net/wp-content/uploads/2021/02/Docker-Symbol.png" 
       width="300"/>
</p>

Seafile es un sistema de software de alojamiento de archivos de código abierto y multiplataforma. Los archivos se almacenan en un servidor central y pueden sincronizarse con ordenadores personales y dispositivos móviles a través de aplicaciones o de la interfaz web.

* [Github](https://github.com/haiwen/seafile)
* [Documentation](https://manual.seafile.com/docker/deploy_seafile_with_docker/)
* [Docker Image](https://hub.docker.com/r/seafileltd/seafile-mc)

## Files
-Creamos carpeta en el directorio de persistencia:
```
mkdir seafile/shared
cd seafile
```

- Descargamos el repositorio:
```
git clone "https://github.com/JuanRodenas/Seafile.git"
```

### Configuration
Sustituya las variables de entorno en `.env` por las suyas propias, y luego ejecute :

```bash
docker-compose up -d
```

Entonces debería poder acceder a la web-ui de seafile con el `SEAFILE_ADMIN_EMAIL` y `SEAFILE_ADMIN_PASSWORD`.

## Update
Si quiere actualizar la imagen automáticamente con watchtower gracias a la siguiente etiqueta:

```yaml
  # Watchtower Update
  - "com.centurylinklabs.watchtower.enable=true"
```
