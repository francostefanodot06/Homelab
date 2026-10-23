# ⚙️ Ansible Automation - Proxmox Homelab

Este repositorio contiene los playbooks de **Ansible** utilizados para automatizar la creación, aprovisionamiento y mantenimiento de infraestructura en un entorno **Proxmox VE**. 

El objetivo de este proyecto es aplicar los principios de **Infraestructura como Código (IaC)** para eliminar tareas manuales, estandarizar configuraciones de seguridad (Hardening) y gestionar contenedores LXC de forma declarativa y predecible.

## 🛠️ Stack Tecnológico y Herramientas
* **Hipervisor:** Proxmox VE.
* **Automatización:** Ansible + Ansible Galaxy (`community.general.proxmox`).
* **Sistemas Operativos:** Debian 12 (Contenedores LXC unprivileged con `nesting=1`).
* **Seguridad & Redes:** Tailscale (VPN Mesh), Fail2ban, OpenSSH (Hardening).
* **Gestión de Cargas:** Docker & Watchtower.

## 📂 Arquitectura de Playbooks

El flujo de trabajo se divide lógicamente en tres fases para garantizar idempotencia y seguridad:

### 1. Creación por API (`lxc-api-create.yml`)
Interactúa directamente con la API de Proxmox (puerto 8006) para desplegar el contenedor.
* Asigna recursos (Cores, RAM, Storage local/SSD).
* Inyecta dependencias estructurales necesarias (`features: ["nesting=1"]`) para soportar systemd 252 y cargas de Docker.
* Maneja tiempos de espera prolongados (`timeout`) para extracciones de plantillas pesadas.

### 2. Aprovisionamiento Interno (`lxc-provision.yml`)
En lugar de depender de una IP dinámica inicial o exponer SSH por defecto, este playbook se conecta al Host físico y utiliza comandos `pct exec` para configurar el contenedor desde adentro de forma segura.
* **Hardening:** Deshabilita el login por contraseña en SSH y configura `PermitRootLogin prohibit-password`.
* **Seguridad:** Instala y activa Fail2ban.
* **Redes:** Instala Tailscale y mapea dispositivos TUN (`/dev/net/tun`) para acceso seguro a la red.
* **Base:** Establece zona horaria local y actualiza repositorios `apt`.

### 3. Mantenimiento Continuo (`docker-update.yml`)
Playbook diseñado para ejecutarse sobre el inventario de nodos de Docker, orquestando **Watchtower** de forma efímera (`--run-once --cleanup`) para mantener las imágenes al día sin intervención manual, respetando la regla del *Release Lag* programando su ejecución según sea necesario.

## 🚀 Uso y Ejecución

*Nota: Las variables sensibles y contraseñas se pasan en tiempo de ejecución o mediante Ansible Vault para no exponer credenciales en el repositorio.*

**Desplegar un nuevo contenedor:**
```bash
ansible-playbook playbooks/lxc-api-create.yml -e "vmid=410 template=debian-12-standard_12.12-1_amd64.tar.zst hostname=servicio-api cores=2 memory=2048 pve_password=TuPasswordSeguro"

Aprovisionar y aplicar Hardening:

Bash
ansible-playbook playbooks/lxc-provision.yml -i inventory.ini -c ssh --ask-pass -e "vmid=410"
