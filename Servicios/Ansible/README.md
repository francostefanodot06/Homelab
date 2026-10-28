# 🏗️ Infraestructura como Código (IaC) y Automatización CI/CD

Este módulo implementa **Infraestructura como Código (IaC)** para eliminar intervenciones manuales y lograr despliegues predecibles en un entorno **Proxmox VE**[cite: 2]. Se utiliza **Ansible** para la configuración declarativa de los contenedores LXC (Debian 12) y **Ansible Semaphore** como motor de ejecución (CI/CD)[cite: 2].

![Ejecución de Tareas en Semaphore](./assets/tareas-semaphore.png)
*Interfaz de Task Templates en Semaphore evidenciando la correcta ejecución de los pipelines de creación, aprovisionamiento y actualización[cite: 1].*

## 🛠️ Stack Tecnológico
* **Hipervisor:** Proxmox VE[cite: 2].
* **Gestión de Configuración:** Ansible y la colección `community.general.proxmox`[cite: 2].
* **Seguridad y Redes:** Hardening de OpenSSH, Fail2ban y Tailscale para VPN Mesh[cite: 2].
* **Manejo de Contenedores:** Docker administrado mediante actualizaciones efímeras con Watchtower[cite: 2].

## 📂 Arquitectura del Pipeline y Playbooks

El flujo de trabajo automatizado se divide en tres fases para garantizar la idempotencia, el aislamiento y la seguridad desde el primer segundo de vida del servidor[cite: 2]:

### Fase 1: Aprovisionamiento de Infraestructura (`lxc-api-create.yml`)
* Interactúa directamente con la API de Proxmox para aprovisionar contenedores sin necesidad de acceso SSH previo[cite: 2, 3].
* Asigna recursos dinámicamente e inyecta la directiva `nesting=1` para soportar cargas de trabajo de Docker[cite: 2, 3].

### Fase 2: Hardening y Configuración (`lxc-provision.yml`)
En lugar de depender de credenciales por defecto expuestas a la red, este playbook se ejecuta a través del host físico utilizando comandos `pct exec` para un aprovisionamiento "Zero-Network"[cite: 2, 4].
* **Sincronización:** Establece la zona horaria del contenedor en `America/Argentina/Buenos_Aires`[cite: 4].
* **Seguridad Perimetral:** Instala Fail2ban y deshabilita la autenticación por contraseña (`PasswordAuthentication no`) y el acceso root (`PermitRootLogin prohibit-password`) en SSH[cite: 2, 4].
* **Identidad:** Inyecta automáticamente la llave pública SSH (`semaphore_key.pub`) de Ansible Semaphore para delegar el control seguro[cite: 4].
* **Redes:** Habilita los dispositivos TUN (`/dev/net/tun`) en la configuración del LXC y despliega Tailscale para acceso a través de la red Mesh[cite: 4].

### Fase 3: Mantenimiento Continuo (`docker-update.yml`)
* Orquesta el ciclo de vida de los servicios contenerizados ejecutando **Watchtower** de manera efímera mediante los comandos `--run-once` y `--cleanup`[cite: 2, 5]. Esto mantiene las imágenes de Docker actualizadas sin dejar procesos residuales en ejecución[cite: 2, 5].
