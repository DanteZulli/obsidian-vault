# Conceptos Básicos

## ¿Qué es Docker?

Docker es una herramienta de **contenedorización** que permite empaquetar aplicaciones y sus dependencias en un entorno aislado y portable llamado **contenedor**.
Los contenedores comparten el kernel del sistema operativo anfitrión (host), lo que los hace más ligeros que las máquinas virtuales (VMs).

## Conceptos Clave

1. **Contenedor**
    - Entorno aislado y ejecutable que incluye:
        - Código de la aplicación.
        - Dependencias (bibliotecas, configuraciones).
        - Variables de entorno.
    - **Ejemplo**: Un contenedor con Node.js, tu app y su `package.json`.
2. **Imagen**
    - Plantilla de solo lectura para crear contenedores.
    - Definida por un **Dockerfile** (instrucciones para construirla).
    - Se organiza en capas (layers) para optimizar el almacenamiento.
3. **Dockerfile**
    - Archivo de texto con instrucciones para construir una imagen.        
4. **Registry (Registro)**
    - Repositorio de imágenes (ej: [**Docker Hub**](https://hub.docker.com/)).
    - Permite compartir/descargar imágenes (`nginx`, `postgres`, etc.).
5. **Volúmenes**    
    - Mecanismo para persistir datos (evita perderlos al eliminar contenedores).
    - Ejemplo: Base de datos guardada en un volumen.
6. **Redes (Networks)**
    - Aislamiento y comunicación entre contenedores (ej: frontend y backend).
7. **Docker Compose**
    - Herramienta para definir y gestionar aplicaciones multi-contenedor (archivo YAML: `docker-compose.yml`).

## Comandos basicos

```bash
# Construir una imagen desde un Dockerfile
docker build -t mi-app:1.0 .

# Ejecutar un contenedor
docker run -d -p 3000:80 --name mi-contenedor mi-app:1.0
  # -d: Modo detached (en segundo plano)
  # -p: Mapear puertos (host:contenedor)

# Gestionar contenedores
docker ps           # Listar contenedores activos
docker stop <id>    # Detener contenedor
docker rm <id>      # Eliminar contenedor
docker logs <id>    # Ver logs

# Gestionar imágenes
docker images       # Listar imágenes locales
docker pull nginx  # Descargar imagen de Docker Hub
docker push user/mi-imagen  # Subir imagen a un registry

# Docker Compose
docker-compose up -d --build # Iniciar servicios definidos en docker-compose.yml
  # -d: Modo detached (en segundo plano)
  # --build: Vuelve a construir las imagenes si estas tuvieron algun cambio.
docker-compose down
docker-compose restart
```

## Consideraciones de Seguridad

- **Imágenes oficiales**: Preferir imágenes validadas y con versionado (ej: `nginx:1.27-alpine` en vez de `nginx:latest`).
- **Actualizar**: Mantener imágenes y dependencias actualizadas.

## Casos de Uso Comunes

1. **Entornos de desarrollo**: Todos los desarrolladores usan la misma configuración.
2. **CI/CD**: Pruebas automatizadas en entornos consistentes.
3. **Microservicios**: Aislar servicios en contenedores independientes.
4. **Despliegue rápido**: "Funciona en mi máquina" → "Funciona en producción".    

---

# Resumen

- Docker simplifica el empaquetado y distribución de aplicaciones.
- **Imagen** → Receta. **Contenedor** → Instancia en ejecución.
- Usa `Dockerfile` para definir imágenes y `docker-compose.yml` para orquestación.

```bash
docker run hello-world  # Prueba tu instalación
```

--- 

¡Listo para empezar! 🐳

# Recursos extra para aprender Docker

- **[Docker Documentation](https://docs.docker.com/)**  
    La fuente más confiable y completa. Incluye guías, comandos, ejemplos y buenas prácticas.
- **[Dockerfile Reference](https://docs.docker.com/engine/reference/builder/)**  
    Especificaciones detalladas de cómo escribir un Dockerfile.
- **[Docker Curriculum](https://docker-curriculum.com/)**  
    Tutorial interactivo para principiantes con ejemplos prácticos.
- **[Docker Labs (GitHub)](https://github.com/docker/labs/)**  
    Laboratorios oficiales de Docker: desde conceptos básicos hasta orquestación con Kubernetes.
- **[Play with Docker Labs](https://training.play-with-docker.com/)**  
    Entorno interactivo gratuito para practicar Docker sin instalar nada.
- **"The Docker Book"** (James Turnbull)  
    Clásico introductorio con ejemplos prácticos.  
    [Enlace](https://www.dockerbook.com/)
- **[OWASP Docker Security](https://cheatsheetseries.owasp.org/cheatsheets/Docker_Security_Cheat_Sheet.html)**  
    Guía de seguridad para contenedores (por OWASP).
- **[Docker Compose Documentation](https://docs.docker.com/compose/)**  
    Guía oficial para orquestar múltiples contenedores.
- **[Docker Hub](https://hub.docker.com/)**  
    Repositorio público de imágenes oficiales y comunitarias.
- **[Docker Cheat Sheet](https://dockerlabs.collabnix.com/docker/cheatsheet/)**  
    Comandos esenciales en una sola página.
- **[Curso de Docker de PeladoNerd](https://www.youtube.com/watch?v=CV_Uf3Dq-EU)** (Video de 1 hora).  
    Tutorial en español desde cero.
