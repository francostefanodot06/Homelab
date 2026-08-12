# Proxmox

**Proxmox VE** es la plataforma de virtualización utilizada como base del homelab. Sobre este nodo se ejecutan las máquinas virtuales y contenedores que proporcionan los diferentes servicios de la infraestructura.

El objetivo de esta configuración es mantener los servicios aislados y facilitar su administración, experimentación y recuperación.

## 🏗️ Rol dentro del homelab

Proxmox actúa como capa de virtualización principal.

Sobre él se ejecutan:

* Máquinas virtuales (VM).
* Contenedores LXC.
* Servicios Docker.
* Servicios auxiliares de infraestructura.
* Almacenamiento local.
* Integraciones de red y acceso remoto.

La infraestructura está organizada de forma que los servicios puedan mantenerse separados entre sí, evitando que un problema en un servicio afecte directamente al resto del sistema.

## ⚙️ Hardware del nodo

El nodo utiliza un sistema basado en:

| Componente               | Especificación              |
| ------------------------ | --------------------------- |
| CPU                      | AMD Ryzen 7 2700X           |
| Núcleos / hilos          | 8 / 16                      |
| RAM                      | 16 GB DDR4                  |
| GPU                      | AMD Radeon RX 9060 XT 16 GB |
| Almacenamiento principal | SSD NVMe ~480 GB            |
| Almacenamiento adicional | SSD ~240 GB                 |
| Almacenamiento adicional | SSD ~480 GB                 |
| Almacenamiento adicional | HDD ~1 TB                   |
| Virtualización           | AMD-V / SVM                 |

La información anterior corresponde al hardware destinado al nodo de virtualización y no incluye identificadores únicos ni información de red.

## 🧠 Proxmox VE

Versión utilizada actualmente:

```text
Proxmox VE 9.2
```

Componentes principales:

* Proxmox VE 9.2.0
* Proxmox Manager 9.2.5
* Kernel Proxmox 7.0.x
* QEMU/KVM
* LXC
* LVM-Thin
* `ifupdown2`
* Proxmox Firewall

La versión exacta del entorno puede consultarse directamente mediante:

```bash
pveversion
pveversion --verbose
```

## 💾 Almacenamiento

El nodo utiliza diferentes dispositivos de almacenamiento para separar el sistema, las máquinas virtuales y los datos.

La instalación principal de Proxmox utiliza:

* SSD NVMe para el sistema y almacenamiento LVM-Thin.
* SSD adicionales para almacenamiento local.
* HDD para almacenamiento de mayor capacidad.

La distribución concreta de los discos puede consultarse directamente en el nodo mediante:

```bash
lsblk
df -h
```

### LVM-Thin

El almacenamiento principal de Proxmox utiliza **LVM-Thin**, permitiendo asignar almacenamiento a las máquinas virtuales de manera flexible.

Esto facilita:

* Creación de discos virtuales.
* Asignación dinámica de espacio.
* Gestión centralizada desde Proxmox.
* Uso eficiente del almacenamiento disponible.

## 🖥️ Máquinas virtuales y contenedores

Actualmente el nodo ejecuta tanto VMs como contenedores LXC.

La infraestructura incluye, entre otros:

* Un contenedor destinado a servicios Docker.
* Una máquina virtual utilizada como entorno principal para Puck y sus servicios.

La lista actual puede consultarse mediante:

```bash
pct list
qm list
```

### LXC

Los contenedores LXC se utilizan cuando el servicio no necesita una virtualización completa.

Ventajas:

* Menor consumo de recursos.
* Inicio rápido.
* Administración sencilla.
* Integración directa con el sistema Proxmox.

### QEMU/KVM

Las máquinas virtuales QEMU/KVM se utilizan cuando se necesita mayor aislamiento o acceso a características específicas de virtualización.

## 🎮 GPU Passthrough

Una de las partes más importantes del homelab es la utilización de la GPU dedicada para cargas de trabajo relacionadas con inteligencia artificial.

La **AMD Radeon RX 9060 XT** está disponible para las cargas de trabajo correspondientes mediante passthrough.

El objetivo es permitir que los servicios de IA puedan utilizar la GPU sin que Proxmox tenga que ejecutar directamente las cargas de inferencia.

La configuración de GPU se mantiene separada de este README para evitar mezclar la documentación general del hipervisor con la configuración específica del hardware.

## 🌐 Red

La configuración de red del nodo utiliza un bridge de Proxmox para conectar las máquinas virtuales y contenedores con la red del homelab.

Por motivos de seguridad, este repositorio **no documenta**:

* Direcciones IP reales.
* Direcciones MAC.
* Rangos de red privados utilizados.
* Identificadores de interfaces.
* Direcciones IPv6.
* Información específica del router.
* Información de acceso remoto.

La configuración de red puede inspeccionarse directamente desde el nodo mediante:

```bash
ip -br addr
ip -br link
```

## 🔐 Acceso remoto

El acceso remoto a determinados servicios del homelab se realiza mediante **Tailscale**.

La configuración de Tailscale se documentará independientemente:

```text
tailscale/
```

De esta manera, Proxmox mantiene documentada su función como hipervisor mientras que la configuración de acceso remoto queda aislada en su propio módulo.

## 🔋 UPS y gestión de energía

El nodo también forma parte del sistema de alimentación ininterrumpida del homelab.

La comunicación con el UPS se realiza mediante **NUT (Network UPS Tools)**.

La documentación correspondiente se encuentra separada:

```text
nut-ups/
```

El objetivo es poder:

* Monitorizar el estado del UPS.
* Detectar cortes de alimentación.
* Obtener métricas eléctricas.
* Ejecutar acciones automáticas ante una pérdida prolongada de energía.
* Permitir un apagado controlado de los servicios.

## 📊 Monitorización

Como parte de la evolución del homelab se está desarrollando un dashboard para visualizar información relacionada con la infraestructura y el UPS.

La documentación del dashboard estará separada:

```text
dashboard/
```

La intención es disponer de una interfaz web sencilla para consultar métricas relevantes sin necesidad de acceder directamente a la terminal del servidor.

## 🧪 Diagnóstico

Durante la configuración y mantenimiento del nodo se utilizan principalmente las herramientas nativas de Linux y Proxmox.

### Información general

```bash
pveversion
pveversion --verbose
```

### Hardware

```bash
lscpu
lspci -nn
lsusb
```

### Memoria

```bash
free -h
```

### Almacenamiento

```bash
lsblk
df -h
```

### Red

```bash
ip -br addr
ip -br link
```

### Contenedores

```bash
pct list
```

### Máquinas virtuales

```bash
qm list
```

Estas herramientas permiten obtener una visión rápida del estado del nodo sin depender exclusivamente de la interfaz web de Proxmox.

## 🛠️ Filosofía de configuración

La configuración del homelab sigue algunos principios:

* Separación de servicios.
* Virtualización cuando aporta aislamiento.
* Contenedores cuando aportan simplicidad.
* Documentación reproducible.
* Automatización siempre que sea posible.
* Evitar dependencias innecesarias.
* Mantener las configuraciones sensibles fuera del repositorio público.

El repositorio busca documentar **cómo está construido el homelab y por qué**, no almacenar una copia literal de la configuración privada del servidor.

## 📁 Estructura relacionada

La documentación de Proxmox se complementa con diferentes componentes:

```text
proxmox/
├── README.md
├── tailscale/
├── nut-ups/
└── dashboard/
```

Cada componente tendrá su propia documentación y configuración.

Los diagramas de arquitectura se incorporarán posteriormente mediante **Excalidraw**, manteniéndolos como recursos independientes en lugar de utilizar diagramas ASCII dentro de los README.

## 🔒 Seguridad y privacidad

Este repositorio puede ser público, por lo que se evita deliberadamente incluir información que permita identificar o acceder directamente a la infraestructura.

No deben publicarse:

* IPs privadas o públicas.
* Direcciones MAC.
* Hostnames sensibles.
* Tokens.
* API keys.
* Contraseñas.
* Secret keys.
* Credenciales.
* Configuraciones de VPN.
* Información de acceso al router.
* Dumps de configuración completos.
* Identificadores únicos de hardware cuando no sean necesarios.

Cuando una configuración necesite mostrar un valor sensible, debe utilizarse un placeholder:

```text
<HOST_IP>
<DOMAIN>
<TAILSCALE_IP>
<API_KEY>
<SECRET>
```

## 📚 Referencias

* [Proxmox VE](https://www.proxmox.com/en/proxmox-virtual-environment)
* [Proxmox Documentation](https://pve.proxmox.com/pve-docs/)
* [Linux Containers](https://linuxcontainers.org/)
* [QEMU](https://www.qemu.org/)
* [Network UPS Tools](https://networkupstools.org/)
* [Tailscale](https://tailscale.com/)

---

## 📌 Estado actual

**Estado:** 🟢 Operativo

Proxmox funciona como plataforma principal de virtualización del homelab, proporcionando la infraestructura sobre la que se ejecutan los diferentes servicios de Puck y el resto de componentes del laboratorio.

La documentación específica de acceso remoto, UPS y monitorización se mantiene separada para conservar una estructura modular y facilitar su mantenimiento.
