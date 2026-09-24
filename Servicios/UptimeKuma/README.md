Uptime Kuma - Stack de Monitoreo 📊

Este directorio contiene la configuración de despliegue para Uptime Kuma, una herramienta de monitoreo autoalojada que utilizo para llevar un registro del estado, tiempo de actividad (uptime) y latencia de todos los servicios críticos dentro de mi entorno de Proxmox.

📌 Características

Monitoreo Activo: Rastrea HTTP(s), Ping y resoluciones DNS para todos los contenedores LXC internos y stacks de Docker.

Página de Estado: Aloja una página de estado pública para visualizar la confiabilidad y las métricas del homelab en tiempo real.

Rápido y Ligero: Consumo mínimo de recursos, ideal para correr junto a cargas de trabajo pesadas como LLMs locales (Ollama, Qwen) y servidores multimedia.

Listo para Proxy Inverso: Accesible de forma segura a través de un dominio personalizado de DuckDNS enrutado mediante Nginx Proxy Manager.

🛠️ Despliegue

Cloná este repositorio o copiá el archivo docker-compose.yml.

Creá el directorio de datos en tu host para asegurar la persistencia de la base de datos:

mkdir -p /opt/uptime-kuma/data


Levantá el contenedor usando Docker Compose:

docker-compose up -d


Accedé a la interfaz web en http://<ip-de-tu-servidor>:3001 para crear la cuenta de administrador inicial y configurar tus monitores.

🔒 Backups y Persistencia

Todas las configuraciones de los monitores, el historial y los ajustes se guardan en una base de datos SQLite ubicada dentro del volumen mapeado /opt/uptime-kuma/data. Es fundamental incluir este directorio en las copias de seguridad de rutina del servidor.
