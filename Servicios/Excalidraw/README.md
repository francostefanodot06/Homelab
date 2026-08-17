Excalidraw

Excalidraw es una herramienta de diagramación colaborativa que utilizo para documentar visualmente la infraestructura del homelab.

Despliegue

Está desplegado mediante Docker Compose dentro de un contenedor LXC de Proxmox.

Imagen: excalidraw/excalidraw:latest
Contenedor: excalidraw
Puerto: 5000
Puerto interno: 80
Reinicio: unless-stopped
Persistencia

Actualmente no se utilizan volúmenes Docker para este servicio.

Los diagramas importantes se pueden exportar desde Excalidraw y almacenar junto con la documentación del homelab.

Acceso

El servicio se utiliza principalmente dentro de la red del homelab y mediante acceso remoto seguro.

No se expone directamente a Internet.

Administración

Iniciar:

docker compose up -d

Detener:

docker compose down

Ver estado:

docker ps

Ver logs:

docker logs excalidraw

Actualizar:

docker compose pull
docker compose up -d
