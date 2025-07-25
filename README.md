# React + Vite

Este proyecto utiliza un flujo de CI/CD automatizado con GitHub Actions para construir y desplegar la aplicación en un servidor EC2 usando Docker Compose.

## Flujo de las GitHub Actions

1. **Build de la imagen Docker**
   - El workflow se dispara automáticamente al hacer push a la rama `master` o cualquier rama `feature/*`, o manualmente desde la interfaz de GitHub Actions.
   - El job `docker` utiliza un runner `ubuntu-latest` (máquina virtual potente y efímera) para:
     - Hacer checkout del repositorio.
     - Loguearse en el GitHub Container Registry (GHCR).
     - Construir la imagen Docker de la app y subirla a GHCR.

2. **Despliegue en EC2**
   - El job `deploy-compose` depende de que termine el build.
   - Utiliza un runner `self-hosted` que debe estar configurado en el servidor EC2 donde se desea desplegar la app.
   - Este runner:
     - Hace checkout del repositorio.
     - Se loguea en GHCR para poder descargar la imagen recién construida.
     - Ejecuta `docker compose up -d --pull always` para levantar (o actualizar) el servicio usando la última imagen.

## ¿Por qué dos tipos de runners?

- **ubuntu-latest**: Se usa para el build porque es más potente y rápido, ideal para tareas pesadas y efímeras.
- **self-hosted**: Se usa para el despliegue porque está conectado directamente al servidor EC2, permitiendo ejecutar comandos y levantar los contenedores en el entorno de producción.

## Requisitos para el runner self-hosted

- Debe estar registrado en el repositorio o la organización de GitHub.
- Debe tener Docker y Docker Compose instalados.
- Debe tener acceso al repositorio y permisos para ejecutar workflows.

---

Para más detalles sobre la configuración de runners self-hosted, consulta la [documentación oficial de GitHub](https://docs.github.com/en/actions/hosting-your-own-runners/about-self-hosted-runners).
