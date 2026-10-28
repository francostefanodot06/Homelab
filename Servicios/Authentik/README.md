# 🔐 Security, IAM & Active Defense (Zero Trust)

Este módulo define la postura de ciberseguridad de la infraestructura, alineándose con las mejores prácticas de **DevSecOps**. Combina un modelo de confianza cero (**Zero Trust**) para el acceso humano con una defensa activa y colaborativa contra amenazas automatizadas y botnets.

El objetivo es proteger los servicios expuestos (túneles y proxies inversos), garantizar la auditoría continua y aislar las interfaces de administración del escaneo público.

## 🛡️ Identity & Access Management (IAM): Authentik

**Authentik** actúa como el Proveedor de Identidad (IdP) unificado del entorno. Elimina la necesidad de gestionar credenciales aisladas en cada aplicación y orquesta las políticas de autorización.

* **Single Sign-On (SSO):** Integración nativa mediante protocolos estándar (OIDC y SAML) con herramientas de infraestructura compatibles (Proxmox VE, Grafana, Forgejo, Semaphore).
* **Forward Authentication (Proxy-Auth):** Para servicios legados o que no soportan SSO, Authentik funciona como middleware en el Proxy Inverso (Nginx Proxy Manager). Intercepta el tráfico entrante, validando la identidad antes de permitir el enrutamiento hacia el contenedor de destino.
* **Políticas de Acceso Condicional:** Aplicación de autenticación multifactor (MFA) obligatoria para flujos críticos y auditoría centralizada de inicios de sesión para la detección de anomalías.

## 🚨 Intrusion Prevention & Threat Intelligence: CrowdSec

Para proteger la capa de aplicación y red de ataques automatizados, se implementa **CrowdSec**, un motor de seguridad colaborativo (IPS/IDS) que analiza comportamientos maliciosos en tiempo real.

* **Análisis de Logs y Comportamiento:** Inspecciona continuamente los flujos de logs de Nginx Proxy Manager, SSH y aplicaciones expuestas, detectando ataques de fuerza bruta (L7), escaneos de vulnerabilidades y DDoS de capa de aplicación.
* **Inteligencia Colaborativa (CTI):** Se integra con la red global de CrowdSec, recibiendo listas de bloqueo (Blocklists) actualizadas en tiempo real basadas en telemetría mundial. Esto permite bloquear de forma proactiva IPs pertenecientes a botnets conocidas, incluso antes de que ataquen el servidor.
* **Remediación mediante Bouncers:** Utiliza agentes de mitigación ("Bouncers") a nivel de firewall del sistema operativo (iptables/nftables) y dentro del proxy inverso para dropear conexiones o presentar desafíos CAPTCHA a actores maliciosos identificados.
