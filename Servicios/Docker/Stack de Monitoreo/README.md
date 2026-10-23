## 👁️ SRE Stack: Observabilidad, Monitoreo y Telemetría

Para mantener el control absoluto sobre los recursos del clúster (hardware físico, Proxmox, LXC y Docker), el Homelab cuenta con un stack de observabilidad proactivo y de grado empresarial. La arquitectura se divide en tres pilares fundamentales: recolección de métricas, agregación de logs distribuidos y visualización/alerting.

Esta infraestructura me permite no solo reaccionar ante caídas de servicios, sino anticiparme a cuellos de botella mediante análisis de tendencias históricas.

#### Stack Principal: Prometheus, Grafana, Loki, Promtail, cAdvisor y Node Exporter
<p align="center">
  <img src="Servicios/Assets/Alertas-en-Grafana.png" alt="Alertas en Grafana funcionando y a la espera        del error o problema que llegue a manifestarse" width="100%"> <br>
  <em>Alertas del Homelab en Proxmox (Grafana)</em>
</p>

### 🧩 Arquitectura de Componentes

La mayoría de estos servicios corren unificados bajo el stack `monitoring-stackpa` para asegurar una resolución de nombres (DNS interno de Docker) fluida y segura entre los contenedores.

#### 📊 1. Pipeline de Métricas (Time-Series Data)
*   **Prometheus (`prom/prometheus:latest`):** El corazón del monitoreo. Base de datos de series temporales que realiza *scraping* (recolección activa) de todos los exportadores de la red cada ciertos intervalos de tiempo.
*   **Node Exporter (`quay.io/prometheus/node-exporter:latest`):** Un sensor de bajo nivel anclado al hardware. Expone métricas críticas del sistema operativo anfitrión (uso real de CPU, RAM disponible, operaciones I/O en los discos WD Red y NVMe, y temperaturas del Ryzen/Radeon).
*   **cAdvisor (`gcr.io/cadvisor/cadvisor:latest`):** (Container Advisor). Proporciona telemetría detallada, uso de recursos y características de rendimiento en tiempo real de cada contenedor Docker que corre en el sistema.
*   **NUT Exporter (`ghcr.io/druggeri/nut_exporter:latest`):** Puente de comunicación vital. Traduce el estado de la UPS (Lyonn 1200VA) gestionada por Network UPS Tools (NUT) a formato Prometheus, permitiendo monitorear cortes de luz, carga de batería y consumo en Watts.

#### 📝 2. Pipeline de Logs Distribuidos
*   **Loki (`grafana/loki:latest`):** Sistema de agregación de logs altamente eficiente. A diferencia de soluciones pesadas como ELK, Loki solo indexa la metadata (etiquetas), comprimiendo y almacenando el texto crudo. Esto ahorra una cantidad masiva de RAM y CPU en el servidor.
*   **Promtail (`grafana/promtail:latest`):** El agente recolector. Lee constantemente los archivos de logs de los contenedores locales y los envía (pushea) directamente a Loki, adjuntando las mismas etiquetas que usa Prometheus para cruzar datos fácilmente.

#### 📈 3. Visualización y Gestión
*   **Grafana (`grafana/grafana:latest`):** El cerebro visual de la operación. Centraliza la data de Prometheus y Loki en dashboards interactivos. Además, su motor interno de Alertmanager evalúa reglas con filtros anti-spam (ej: CPU > 85% durante 5 min) y dispara webhooks ante emergencias.
*   **Portainer Agent (`portainer/agent:latest`):** Agente Edge ligero que permite gestionar este nodo de Docker de forma remota y segura desde la instancia principal de Portainer.

---


