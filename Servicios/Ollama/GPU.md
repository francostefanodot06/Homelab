# 🎮 AMD Radeon RX 9060 XT — GPU Passthrough

Documentación de la configuración de la **AMD Radeon RX 9060 XT de 16 GB de VRAM** utilizada para acelerar las cargas de inteligencia artificial del homelab.

La GPU se encuentra instalada físicamente en el servidor Proxmox y es expuesta a una **Linux Container (LXC)** donde se ejecutan Ubuntu, Ollama y los servicios relacionados con Puck.

> **Nota:** Esta documentación está preparada para publicación en un repositorio público. Se omiten identificadores de red, direcciones MAC, IPs y otros datos específicos de la infraestructura que no son necesarios para reproducir la configuración.

---

# 🎯 Objetivo

El objetivo de esta configuración es permitir que los servicios de inteligencia artificial del homelab utilicen la GPU física del servidor desde una LXC de Proxmox.

La GPU es utilizada principalmente por **Ollama** para ejecutar modelos de lenguaje localmente.

La arquitectura resultante es:

```text
┌──────────────────────────────────────────────┐
│                  Hardware                    │
│                                              │
│          AMD Radeon RX 9060 XT               │
│                 16 GB VRAM                   │
└──────────────────────┬───────────────────────┘
                       │
                       ▼
┌──────────────────────────────────────────────┐
│                 Proxmox VE                   │
│                                              │
│       Configuración de dispositivos LXC      │
│                                              │
│  /dev/kfd                                    │
│  /dev/dri/card0                              │
│  /dev/dri/renderD128                         │
└──────────────────────┬───────────────────────┘
                       │
                       ▼
┌──────────────────────────────────────────────┐
│                Ubuntu LXC                    │
│                                              │
│              Puck / Ollama                   │
└──────────────────────┬───────────────────────┘
                       │
                       ▼
┌──────────────────────────────────────────────┐
│                    ROCm                      │
│                                              │
│          Stack de computación AMD            │
└──────────────────────┬───────────────────────┘
                       │
                       ▼
                  Modelos LLM
```

---

# 🏗️ Arquitectura

La infraestructura utiliza Proxmox VE como hipervisor y una LXC dedicada para los servicios de inteligencia artificial.

Ollama se ejecuta directamente dentro de Ubuntu mediante `systemd`, mientras que otros componentes de Puck, como Open WebUI y SearXNG, se ejecutan mediante Docker.

```text
                       Proxmox VE
                           │
                           ▼
                    ┌─────────────┐
                    │ Ubuntu LXC  │
                    │   Puck      │
                    └──────┬──────┘
                           │
             ┌─────────────┴─────────────┐
             │                           │
             ▼                           ▼
          Ollama                      Docker
             │                           │
             ▼                    ┌──────┴──────┐
          ROCm                    │             │
             │                 Open WebUI    SearXNG
             ▼
       RX 9060 XT
         16 GB
```

---

# 🖥️ Hardware

| Componente           | Especificación        |
| -------------------- | --------------------- |
| GPU                  | AMD Radeon RX 9060 XT |
| VRAM                 | 16 GB                 |
| CPU del servidor     | AMD Ryzen 7 2700X     |
| RAM del servidor     | 16 GB DDR4            |
| Hipervisor           | Proxmox VE            |
| Entorno de ejecución | Linux Container (LXC) |
| Sistema operativo    | Ubuntu 22.04.5 LTS    |

La RX 9060 XT está dedicada principalmente a las cargas de inteligencia artificial del homelab.

---

# 📦 Configuración de la LXC

La GPU se expone a la LXC mediante los dispositivos del kernel necesarios para acceder al hardware.

La configuración relevante de la CT incluye:

```ini
arch: amd64
cores: 4
features: nesting=1,keyctl=1
memory: 8192
onboot: 1
ostype: ubuntu
rootfs: local-lvm:vm-<CT_ID>-disk-0,size=256G
swap: 512
```

Los identificadores específicos de la infraestructura se omiten deliberadamente en esta documentación pública.

---

# 🔌 GPU Passthrough

En este homelab se utiliza **device passthrough hacia una LXC**, en lugar de realizar un PCI passthrough completo hacia una máquina virtual.

Esto permite exponer a la LXC los dispositivos necesarios para utilizar la GPU desde el sistema operativo invitado.

Los principales dispositivos utilizados son:

```text
/dev/kfd
/dev/dri/card0
/dev/dri/renderD128
```

La relación entre las capas es:

```text
GPU física
    │
    ▼
Proxmox / Linux kernel
    │
    ├── /dev/kfd
    │
    └── /dev/dri/
            ├── card0
            └── renderD128
                    │
                    ▼
                 Ubuntu LXC
                    │
                    ▼
                   ROCm
                    │
                    ▼
                  Ollama
```

---

# ⚙️ Configuración de dispositivos

Los dispositivos DRM utilizados por la GPU se habilitan mediante reglas de dispositivos de cgroup:

```ini
lxc.cgroup2.devices.allow: c 226:0 rwm
lxc.cgroup2.devices.allow: c 226:128 rwm
```

Posteriormente se exponen dentro de la LXC mediante bind mounts:

```ini
lxc.mount.entry: /dev/dri/card0 dev/dri/card0 none bind,optional,create=file
lxc.mount.entry: /dev/dri/renderD128 dev/dri/renderD128 none bind,optional,create=file
```

El acceso a `/dev/kfd` también se habilita para permitir las operaciones de cómputo de AMD:

```ini
dev0: /dev/kfd
```

y:

```ini
lxc.cgroup2.devices.allow: c 10:200 rwm
```

> Los valores de dispositivos y rutas mostrados corresponden a la configuración actual del homelab. Pueden variar dependiendo del hardware, kernel y configuración de Proxmox.

---

# 🧩 AMD ROCm

La aceleración de IA utiliza el stack de computación de AMD **ROCm**.

ROCm proporciona las herramientas y librerías necesarias para que las aplicaciones compatibles puedan utilizar las GPU AMD para cargas de cómputo.

La cadena de software utilizada es:

```text
AMD Radeon RX 9060 XT
          │
          ▼
       amdgpu
          │
          ▼
   /dev/kfd + /dev/dri
          │
          ▼
         ROCm
          │
          ▼
        Ollama
          │
          ▼
       Modelo LLM
```

La instalación y configuración de ROCm se realizó tomando como referencia la documentación oficial de AMD:

[Documentación oficial de instalación de ROCm — AMD](https://rocm.docs.amd.com/en/latest/install/rocm.html?utm_source=chatgpt.com)

> La documentación oficial de AMD debe utilizarse para comprobar compatibilidad entre GPU, sistema operativo, kernel y versión de ROCm antes de realizar una instalación.

---

# 🐧 Configuración dentro de Ubuntu

La LXC ejecuta:

```text
Ubuntu 22.04.5 LTS
```

La primera comprobación después de configurar el passthrough consiste en verificar que los dispositivos de la GPU estén disponibles:

```bash
ls -la /dev/kfd
ls -la /dev/dri
```

Se espera encontrar, entre otros:

```text
/dev/kfd
/dev/dri/card0
/dev/dri/renderD128
```

Si estos dispositivos no están disponibles, el problema debe investigarse primero en la configuración de Proxmox/LXC antes de continuar con ROCm u Ollama.

---

# 🔬 Verificación del stack AMD

Una vez disponibles los dispositivos dentro de Ubuntu, se pueden utilizar las herramientas correspondientes del stack ROCm para comprobar la detección de la GPU.

Ejemplos:

```bash
rocminfo
```

y, cuando esté disponible en la instalación:

```bash
amd-smi
```

Estas herramientas permiten comprobar que el entorno Linux puede detectar correctamente la GPU y exponer sus capacidades de cómputo.

---

# 🤖 Integración con Ollama

Ollama se ejecuta directamente dentro de Ubuntu mediante `systemd`.

```text
Ubuntu
  │
  ├── ROCm
  │
  └── Ollama
        │
        ▼
  RX 9060 XT 16 GB
        │
        ▼
     Modelos LLM
```

La instalación de Ollama está documentada por separado:

```text
services/ollama/README.md
```

Actualmente se utiliza:

```text
Ollama 0.32.6
```

El ejecutable se encuentra en:

```text
/usr/local/bin/ollama
```

---

# 🧪 Verificación de Ollama

Comprobar la versión:

```bash
ollama --version
```

Listar modelos:

```bash
ollama list
```

Ver modelos actualmente cargados:

```bash
ollama ps
```

Ejecutar un modelo para realizar una prueba:

```bash
ollama run qwen3:8b
```

Mientras un modelo está ejecutándose, se pueden utilizar las herramientas del stack AMD para observar el estado y utilización de la GPU.

---

# 🧠 Modelos disponibles

La RX 9060 XT dispone de 16 GB de VRAM, permitiendo ejecutar modelos locales de diferentes tamaños.

Actualmente se encuentran instalados:

| Modelo              | Tamaño aproximado | Uso                    |
| ------------------- | ----------------: | ---------------------- |
| `qwen2.5:7b`        |            4.7 GB | General / rápido       |
| `qwen2.5vl:7b`      |            6.0 GB | Multimodal             |
| `mistral-small:24b` |             14 GB | Tareas complejas       |
| `qwen3:8b`          |            5.2 GB | General / razonamiento |
| `qwen2.5-coder:14b` |            9.0 GB | Programación           |
| `gemma3:12b`        |            8.1 GB | General                |
| `deepseek-r1:14b`   |            9.0 GB | Razonamiento           |
| `llama3:latest`     |            4.7 GB | General                |

La selección del modelo depende del tipo de tarea, el contexto requerido, la velocidad deseada y la memoria disponible.

---

# 🛠️ Problemas encontrados

La configuración actual fue el resultado de varias iteraciones y pruebas.

Uno de los principales desafíos fue conseguir que la GPU física pudiera utilizarse correctamente desde el entorno virtualizado.

Los problemas investigados incluyeron:

* Configuración del passthrough de GPU hacia la LXC.
* Exposición de `/dev/kfd`.
* Exposición de `/dev/dri`.
* Permisos mediante `lxc.cgroup2.devices.allow`.
* Bind mounts de dispositivos DRM.
* Configuración del entorno Linux.
* Instalación y configuración del stack AMD/ROCm.
* Integración de ROCm con Ollama.
* Verificación de la utilización de GPU.
* Selección de modelos en función de la VRAM disponible.

La experiencia demostró que un problema de aceleración de GPU puede encontrarse en diferentes capas:

```text
Hardware
   ↓
Proxmox
   ↓
LXC
   ↓
Linux
   ↓
amdgpu
   ↓
ROCm
   ↓
Ollama
   ↓
Modelo
```

Por este motivo, las comprobaciones se realizan progresivamente desde las capas inferiores hacia las superiores.

---

# 🔄 Migración desde NVIDIA RTX 3050

Antes de la RX 9060 XT, el homelab utilizaba una **NVIDIA RTX 3050 de 8 GB de VRAM**.

Durante esa etapa se realizaron pruebas para ejecutar Ollama y diferentes modelos de IA dentro de una máquina virtual de Proxmox.

La arquitectura anterior era:

```text
Proxmox VE
    │
    ▼
   VM
    │
    ├── Linux
    ├── NVIDIA drivers
    └── Ollama
           │
           ▼
       RTX 3050
         8 GB
```

Esta etapa permitió experimentar con:

* PCI passthrough.
* Máquinas virtuales.
* Drivers NVIDIA.
* Ollama.
* Modelos LLM locales.
* Asignación de recursos a máquinas virtuales.
* Integración entre GPU y workloads de IA.

Sin embargo, la implementación presentó diferentes problemas relacionados con el passthrough, los drivers y la estabilidad de la máquina virtual.

La experiencia fue útil para entender que la virtualización de GPU requiere una configuración correcta tanto en el hipervisor como en el sistema operativo invitado y el stack de aceleración.

---

# 🔄 Cambio de arquitectura

La experiencia con la RTX 3050 llevó a rediseñar la infraestructura de IA.

La arquitectura anterior utilizaba:

```text
RTX 3050
    ↓
Proxmox
    ↓
VM
    ↓
Drivers NVIDIA
    ↓
Ollama
```

La arquitectura actual utiliza:

```text
RX 9060 XT
    ↓
Proxmox
    ↓
LXC
    ↓
/dev/kfd + /dev/dri
    ↓
ROCm
    ↓
Ollama
```

Además del aumento de VRAM de **8 GB a 16 GB**, la nueva arquitectura permite mantener Puck dentro de una LXC dedicada y separar el acceso al hardware de los servicios que se ejecutan mediante Docker.

---

# 🧠 Lecciones aprendidas

La implementación del GPU passthrough permitió comprender que la aceleración de IA no depende únicamente de instalar una GPU y un runtime de inferencia.

Existe una cadena completa de dependencias:

```text
Hardware
   ↓
Hipervisor
   ↓
Virtualización
   ↓
Dispositivos del kernel
   ↓
Drivers
   ↓
Runtime de aceleración
   ↓
Runtime de inferencia
   ↓
Modelo
```

Cada capa debe funcionar correctamente antes de diagnosticar la siguiente.

La migración desde la RTX 3050 también permitió experimentar con dos arquitecturas diferentes y comparar las ventajas y dificultades de utilizar una VM frente a una LXC para workloads que requieren acceso a GPU.

---

# 📚 Referencias

* AMD — Documentación oficial de instalación de ROCm.
* Proxmox VE — Documentación oficial de Linux Containers.
* Ollama — Documentación oficial.
