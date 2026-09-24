Homepage - Panel del Homelab 🚀

Este directorio contiene la configuración de despliegue para Homepage, un panel de control moderno, seguro y altamente personalizable que uso como punto de entrada central para la infraestructura de mi homelab (FrankDevLab).

📌 Características

Acceso Centralizado: Agrupa todos los servicios internos (Proxmox, Nginx Proxy Manager, Portainer) y aplicaciones (Jellyfin, Immich, Open WebUI) en una sola interfaz.

Monitoreo de Infraestructura: Configurado con indicadores ping en tiempo real para verificar el estado y la latencia de los contenedores.

Tema Personalizado: Implementación de un tema "Ultra Dark" (Ultra Oscuro) usando negro puro (#050505) y acentos en zinc para una experiencia visual limpia y sin distracciones.

Integración con Proxy Inverso: Expuesto de forma segura a internet mediante DuckDNS y Nginx Proxy Manager.

🛠️ Despliegue

Cloná este repositorio o copiá el archivo docker-compose.yml.

Asegurate de que el directorio de configuración exista en tu host:

mkdir -p /opt/homepage/config


Colocá tus archivos de configuración (settings.yaml, bookmarks.yaml, services.yaml) dentro de la carpeta config.

Levantá el contenedor:

docker-compose up -d


📂 Resumen de Archivos de Configuración

settings.yaml: Controla el diseño global, temas personalizados y estilos de la página.

services.yaml: Define las categorías de infraestructura, aplicaciones y multimedia (con sus respectivos pings).

bookmarks.yaml: Contiene enlaces de acceso rápido (ej. portfolio, GitHub, LinkedIn).
