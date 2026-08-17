🎨 Excalidraw — Homelab

Self-hosted Excalidraw para diseñar, documentar y visualizar la arquitectura del homelab.

Excalidraw es una herramienta de diagramación que utilizo para documentar visualmente la infraestructura del homelab, incluyendo servidores, contenedores, servicios y conexiones entre componentes.

La aplicación está desplegada de forma self-hosted mediante Docker Compose dentro de un contenedor LXC administrado por Proxmox.

🏗️ Arquitectura
🖥️ Proxmox
   │
   └── 📦 Contenedor LXC
        │
        └── 🐳 Docker
             │
             └── 🎨 Excalidraw

El servicio se ejecuta como un contenedor Docker independiente y se accede desde la red autorizada del homelab.

🚀 Despliegue

Excalidraw se ejecuta utilizando la imagen oficial:

excalidraw/excalidraw:latest

El despliegue se gestiona mediante Docker Compose.

📁 Estructura
excalidraw/
└── docker-compose.yml
▶️ Iniciar
docker compose up -d
🔍 Comprobar estado
docker ps
📋 Ver logs
docker logs excalidraw
🔄 Reiniciar
docker compose restart
💾 Persistencia

Actualmente Excalidraw no utiliza volúmenes Docker persistentes.

La configuración del servicio se mantiene mediante Docker Compose, mientras que los diagramas importantes del homelab pueden exportarse y almacenarse junto con la documentación del proyecto.

💡 Si en el futuro se incorpora almacenamiento persistente, esta sección será actualizada.

🔐 Acceso

El servicio está destinado al uso interno del homelab y no se encuentra expuesto directamente a Internet.

El acceso remoto se realiza mediante la infraestructura de red privada del homelab.

Por motivos de seguridad, este repositorio no contiene:

🌐 Direcciones IP privadas
🔑 Información de acceso
🛜 Configuración detallada de la red
🖥️ Identificadores específicos de infraestructura
🔒 Información sensible del entorno
🛠️ Actualización

Para actualizar la imagen:

docker compose pull
docker compose up -d

Después de actualizar, se recomienda comprobar que el contenedor se encuentre funcionando correctamente:

docker ps
🧩 Tecnologías
Tecnología	Uso
🐧 Debian	Sistema operativo del entorno
📦 Proxmox LXC	Virtualización / contenedorización
🐳 Docker	Ejecución del servicio
⚙️ Docker Compose	Orquestación
🎨 Excalidraw	Diagramación
📌 Estado

🟢 Operativo

Tipo: Servicio self-hosted
Despliegue: Docker Compose
Entorno: Homelab
Uso: Documentación y diagramación de infraestructura
<p align="center"> 🎨 <b>Excalidraw</b> · 🏠 Homelab · 🐳 Docker · ⚙️ Proxmox </p>
