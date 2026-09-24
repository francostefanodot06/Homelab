
# 🎬 Media Server & Automation Stack: Infraestructura de Automatización y Orquestación Multimedia

Este repositorio contiene la configuración de infraestructura como código mediante Docker Compose para desplegar un ecosistema de servicios interconectados en el homelab **Frankdevlab**. El entorno automatiza el ciclo de vida completo de adquisición, gestión y distribución de archivos a través de una red P2P, garantizando alta disponibilidad y una organización estructurada.

## 📦 Arquitectura de Servicios

El *stack* se compone de microservicios contenerizados que se comunican de forma segura en una red aislada, compartiendo volúmenes persistentes con gestión estricta de permisos mediante variables PUID/PGID:

* **Jellyfin:** Servidor central para la decodificación y distribución de medios en tiempo real, operando como el *front-end* del ecosistema de streaming.
* **Sonarr & Radarr:** Motores de automatización y flujos de trabajo (*workflow managers*) encargados de monitorizar APIs externas, delegar tareas de transferencia de datos y organizar la estructura de directorios en el almacenamiento (HDD).
* **Bazarr:** Servicio auxiliar de procesamiento por lotes para la extracción y sincronización automática de metadatos y subtítulos, basado en perfiles de idioma configurados.
* **Prowlarr:** Indexador centralizado y gestor de *proxies* que unifica y administra las comunicaciones y autenticaciones con múltiples bases de datos externas.
* **qBittorrent:** Cliente de transferencias P2P de alto rendimiento, orquestado remotamente a través de webhooks por el *stack* de gestión.

📂 Estructura de Volúmenes Requerida

Es fundamental que los contenedores compartan la misma estructura base para que los "Hardlinks" funcionen correctamente y no dupliques espacio en el disco.

/media/multimedia/Descargas

/media/multimedia/Peliculas

/media/multimedia/Series
