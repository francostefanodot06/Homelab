# 🐳 Portainer: Orquestación y Gestión Centralizada de Contenedores

Plataforma de administración visual desplegada para monitorizar y gestionar de forma centralizada el ciclo de vida de los contenedores Docker dentro de la infraestructura de Proxmox.

## 🛠️ Capacidades Implementadas
* **Despliegue Declarativo:** Gestión ágil de archivos `docker-compose.yml` (Stacks) para levantar entornos complejos con control de versiones.
* **Observabilidad y Monitoreo:** Supervisión en tiempo real del consumo de recursos (CPU, memoria, I/O de red) de cada microservicio del nodo.
* **Auditoría y Logs:** Acceso directo a la salida estándar (STDOUT/STDERR) de cada contenedor para agilizar el *troubleshooting* y la depuración de errores.
* **Gestión de Volúmenes y Redes:** Aislamiento seguro y administración de la persistencia de datos (Appdata) garantizando que la configuración sobreviva a reinicios.
