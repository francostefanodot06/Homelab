Excalidraw

Excalidraw es una herramienta de diagramación y dibujo colaborativo que utilizo en mi homelab para crear y mantener los diagramas de arquitectura de la infraestructura.

La aplicación está desplegada mediante Docker Compose dentro de un contenedor LXC administrado por Proxmox.

Arquitectura
Proxmox
└── Contenedor LXC
    └── Docker
        └── Excalidraw

El contenedor de Excalidraw utiliza el puerto HTTP de la aplicación y publica un puerto del host para permitir el acceso desde la red autorizada.

Despliegue

El servicio se ejecuta utilizando la imagen oficial de Excalidraw:

excalidraw/excalidraw:latest

La configuración se encuentra en:

docker-compose.yml

Para desplegar el servicio:

docker compose up -d

Para comprobar su estado:

docker ps

Para consultar los logs:

docker logs excalidraw
Persistencia

Actualmente el contenedor no utiliza volúmenes Docker.

Esto significa que la instalación se mantiene como un servicio principalmente orientado a la ejecución de la aplicación, mientras que los archivos de configuración del despliegue se mantienen en el repositorio del homelab.

Si en el futuro se incorpora almacenamiento persistente, se documentará en esta sección.

Actualización

Para actualizar la imagen:

docker compose pull
docker compose up -d

Se recomienda verificar posteriormente que el contenedor se encuentre funcionando correctamente.

Acceso

Excalidraw se encuentra disponible únicamente a través de la red autorizada del homelab.

No se publica directamente en Internet.

El método concreto de acceso se mantiene fuera de la documentación pública para evitar exponer información de la infraestructura.

Seguridad

El servicio forma parte de la infraestructura interna del homelab y no está diseñado para exposición pública directa.

Las direcciones IP, nombres internos, configuración de red y otros datos específicos de la infraestructura se mantienen fuera de este repositorio.

Estado

Estado: Operativo

Método de despliegue: Docker Compose

Entorno: Proxmox + LXC + Debian + Docker
