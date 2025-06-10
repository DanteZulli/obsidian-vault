Un **registry privado** permite almacenar y gestionar imágenes Docker de forma interna 

# Configurar un Registry Privado

## Paso 1: Ejecutar el Registry de Docker

Usando la imagen oficial `registry:2`:

```bash
docker run -d \
  -p 5000:5000 \
  --name my-registry \
  -v /ruta/local/registry:/var/lib/registry \
  --restart always \
  registry:2
# -v: Volumen para persistencia de datos (guarda las imágenes en el servidor)
# --restart always: Reinicia el contenedor si falla o el servidor se reinicia.
```

## Paso 2: Autenticación (Opcional)

Para aumentar la seguridad, podemos proteger el registry con usuario y contraseña.

```bash

# Crear el archivo del tipo htpasswd
docker run --entrypoint htpasswd httpd:2 -Bbn usuario contraseña > auth/htpasswd

# Ejecutar el registry con autenticacion
docker run -d \
  -p 5000:5000 \
  --name my-registry \
  -v /ruta/local/registry:/var/lib/registry \
  -v /ruta/auth:/auth \
  -e REGISTRY_AUTH=htpasswd \
  -e REGISTRY_AUTH_HTPASSWD_REALM="Registry Realm" \
  -e REGISTRY_AUTH_HTPASSWD_PATH=/auth/htpasswd \
  --restart always \
  registry:2

```

# Subir y Administrar Imágenes

## Subir y Descargar una Imagen del Registry

```bash
# Etiquetar la imagen con la URL del registry:
docker tag mi-imagen:1.0 mi-servidor.com:5000/mi-imagen:1.0 --insecure-registry
# - `mi-servidor.com:5000`: Dirección del registry (si no es HTTPS, se añade `--insecure-registry` al cliente Docker).
# Y luego se deberá agregar esta línea:
#   "insecure-registries": ["svrpruebas:5000"] (Con el servidor y la IP de la registry en cuestión) al daemon de docker destinado a realizar las operaciones
# en la registry (`/etc/docker/daemon.json`)



# Iniciar sesión (si hay autenticación)
docker login mi-servidor.com:5000

# Subir la imagen
docker push mi-servidor.com:5000/mi-imagen:1.0

# Descargar una imagen
docker pull mi-servidor.com:5000/mi-imagen:1.0
```

## Listar Imágenes en el Registry

```bash
curl -X GET http://mi-servidor.com:5000/v2/_catalog
# Respuesta: {"repositories":["mi-imagen"]}

# Listar tags de una imagen
curl -X GET http://mi-servidor.com:5000/v2/mi-imagen/tags/list
```

# Mantenimiento del Registry

## Eliminar Imágenes Antiguas

El registry no borra automáticamente las imágenes. 

Para eliminar una capa:
- Activa la opción de borrado en el registry (configuración con `REGISTRY_STORAGE_DELETE_ENABLED=true`).
- Usa la API para borrar por digest: `curl -X DELETE http://mi-servidor.com:5000/v2/mi-imagen/manifests/<digest>`
- Ejecutar el garbage-collect `docker exec -it my-registry bin/registry garbage-collect /etc/docker/registry/config.yml`

## Monitoreo y Backups

- **Monitoreo de espacio**: Verifica el volumen asignado (`/ruta/local/registry`).
- **Backups**: Copia regularmente el directorio del volumen (`/ruta/local/registry`).

# Ejemplo con Docker Compose

```yaml
services:
  registry:
    image: registry:2
    ports:
      - "5000:5000"
    volumes:
      - ./registry-data:/var/lib/registry
      - ./certs:/certs # Certificados si los hubiere
      - ./auth:/auth
    environment:
      - REGISTRY_HTTP_TLS_CERTIFICATE=/certs/fullchain.pem # Certificados si los hubiere
      - REGISTRY_HTTP_TLS_KEY=/certs/privkey.pem # Certificados si los hubiere
      - REGISTRY_AUTH=htpasswd
      - REGISTRY_AUTH_HTPASSWD_PATH=/auth/htpasswd
      - REGISTRY_AUTH_HTPASSWD_REALM="Registry Realm"
    restart: always
```

# Recursos Extra:

- **Portainer**: Interfaz gráfica para administrar contenedores y registries.  
    [Documentación de Portainer](https://docs.portainer.io/)
- **Registry Docker Image**: Imagen oficial de docker para la registry.
    [registry en DockerHub](https://hub.docker.com/_/registry)
- **CNCF Distribution Registry**: Documentación sobre el uso de la registry privada.
    [Enlace](https://distribution.github.io/distribution/)
- **Docker Registry REST API**:
    [Enlace](https://docker-docs.uclv.cu/registry/spec/api/)