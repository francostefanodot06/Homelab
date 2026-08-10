# 🧠 Ollama

Ollama es el motor de inferencia utilizado por **Puck**, mi asistente de inteligencia artificial local.

El servicio se ejecuta directamente sobre Ubuntu y utiliza la **AMD Radeon RX 9060 XT de 16 GB de VRAM** para ejecutar modelos de lenguaje de forma local.

La ejecución local permite experimentar con diferentes modelos de IA sin depender de APIs externas para la inferencia.

---

## 🎯 Objetivo

El objetivo de Ollama dentro del homelab es proporcionar un runtime local para ejecutar y administrar modelos de lenguaje (LLM).

Ollama funciona como la capa de inferencia de Puck:

```text
Usuario
   │
   ▼
Open WebUI
   │
   ▼
Puck
   │
   ▼
Ollama
   │
   ▼
Modelo LLM
   │
   ▼
RX 9060 XT 16 GB
```

---

## 🏗️ Arquitectura

Ollama no se ejecuta dentro de Docker en este homelab.

La arquitectura actual utiliza Ollama como un servicio administrado mediante `systemd`, mientras que otros componentes de Puck, como Open WebUI y SearXNG, se ejecutan mediante Docker.

```text
                    Proxmox
                       │
                       ▼
                Ubuntu 22.04.5 LTS
                 "PuckProject"
                       │
          ┌────────────┴────────────┐
          │                         │
          ▼                         ▼
       Ollama                    Docker
       systemd                     │
          │                 ┌──────┴──────┐
          │                 │             │
          ▼                 ▼             ▼
    RX 9060 XT         Open WebUI       SearXNG
       16 GB                │
                            │
                            ▼
                           Puck
```

### Componentes

| Componente         | Función                                                        |
| ------------------ | -------------------------------------------------------------- |
| Proxmox VE         | Hipervisor del homelab                                         |
| Ubuntu 22.04.5 LTS | Sistema donde se ejecuta Puck                                  |
| Ollama             | Motor de inferencia local                                      |
| RX 9060 XT 16 GB   | Aceleración por GPU                                            |
| Open WebUI         | Interfaz web para interactuar con los modelos                  |
| SearXNG            | Motor de búsqueda utilizado para las funciones de búsqueda web |
| Puck               | Asistente de IA local                                          |

---

## 🖥️ Infraestructura

### Sistema operativo

```text
Ubuntu 22.04.5 LTS
```

### Ollama

```text
Versión: 0.32.6
```

### Ubicación del ejecutable

```text
/usr/local/bin/ollama
```

### Hardware

```text
CPU: AMD Ryzen 7 2700X
GPU: AMD Radeon RX 9060 XT
VRAM: 16 GB
RAM: 16 GB
```

La GPU está dedicada a las tareas de inferencia de IA del homelab.

---

## 📦 Instalación

Ollama está instalado directamente en el sistema operativo y no como contenedor Docker.

El ejecutable se encuentra en:

```bash
/usr/local/bin/ollama
```

La instalación directa permite administrar el servicio mediante `systemd` y mantener el acceso de Ollama al hardware de aceleración disponible en el servidor.

---

## ⚙️ Configuración

La configuración principal de Ollama se encuentra gestionada mediante `systemd`.

Archivo principal:

```text
/etc/systemd/system/ollama.service
```

Además, existe un override:

```text
/etc/systemd/system/ollama.service.d/override.conf
```

El override utilizado actualmente contiene:

```ini
[Service]
User=root
Group=root
Environment="OLLAMA_HOST=0.0.0.0"
```

### Exposición de la API

Ollama se configura para escuchar en todas las interfaces de red:

```text
OLLAMA_HOST=0.0.0.0
```

Esto permite que otros servicios del homelab puedan comunicarse con la API de Ollama.

> ⚠️ Si Ollama se expone fuera de la red de confianza, se deben implementar medidas adicionales de seguridad y control de acceso.

---

## 🚀 Systemd

Ollama se ejecuta como un servicio de `systemd`.

Archivo:

```text
/etc/systemd/system/ollama.service
```

Configuración utilizada:

```ini
[Unit]
Description=Ollama Service
After=network-online.target

[Service]
ExecStart=/usr/local/bin/ollama serve
User=ollama
Group=ollama
Restart=always
RestartSec=3

[Install]
WantedBy=default.target
```

El override modifica el usuario de ejecución y la dirección de escucha:

```ini
[Service]
User=root
Group=root
Environment="OLLAMA_HOST=0.0.0.0"
```

### Comandos útiles

Consultar el estado:

```bash
systemctl status ollama
```

Reiniciar Ollama:

```bash
systemctl restart ollama
```

Detener Ollama:

```bash
systemctl stop ollama
```

Iniciar Ollama:

```bash
systemctl start ollama
```

Ver los logs:

```bash
journalctl -u ollama
```

Seguir los logs en tiempo real:

```bash
journalctl -u ollama -f
```

---

## 🎮 Aceleración por GPU

Ollama utiliza la **AMD Radeon RX 9060 XT de 16 GB** como acelerador para la inferencia de modelos.

La GPU permite ejecutar modelos de diferentes tamaños localmente y aprovechar la VRAM disponible para aumentar el rendimiento de inferencia.

```text
RX 9060 XT
    │
    ▼
Aceleración GPU
    │
    ▼
Ollama
    │
    ▼
Modelos LLM
```

La configuración y verificación de la aceleración por hardware forma parte de la infraestructura de IA del homelab.

---

## 🧠 Modelos disponibles

Actualmente se encuentran instalados los siguientes modelos:

| Modelo              | Tamaño aproximado | Uso                         |
| ------------------- | ----------------: | --------------------------- |
| `qwen2.5:7b`        |            4.7 GB | Modelo general y rápido     |
| `qwen2.5vl:7b`      |            6.0 GB | Tareas multimodales         |
| `mistral-small:24b` |             14 GB | Tareas de mayor complejidad |
| `qwen3:8b`          |            5.2 GB | Uso general y razonamiento  |
| `qwen2.5-coder:14b` |            9.0 GB | Programación                |
| `gemma3:12b`        |            8.1 GB | Uso general                 |
| `deepseek-r1:14b`   |            9.0 GB | Razonamiento                |
| `llama3:latest`     |            4.7 GB | Uso general                 |

Los modelos se seleccionan dependiendo de la tarea y de los recursos disponibles.

Los modelos más pequeños permiten priorizar velocidad, mientras que los modelos más grandes pueden utilizarse para tareas que requieren mayor capacidad de razonamiento.

---

## 🔍 Administración de modelos

Listar los modelos instalados:

```bash
ollama list
```

Ejecutar un modelo:

```bash
ollama run qwen3:8b
```

Consultar los modelos actualmente cargados:

```bash
ollama ps
```

Eliminar un modelo:

```bash
ollama rm nombre-del-modelo
```

---

## 🌐 API

Ollama proporciona una API HTTP para permitir que otras aplicaciones interactúen con los modelos.

La API se encuentra disponible en el puerto:

```text
11434
```

Arquitectura de comunicación:

```text
Open WebUI
     │
     │ HTTP
     ▼
Ollama :11434
     │
     ▼
Modelo LLM
```

Esta API es utilizada por Open WebUI para enviar las consultas de los usuarios al motor de inferencia.

---

## 🔗 Integración con Open WebUI

Open WebUI se ejecuta como un contenedor Docker independiente.

La arquitectura separa la interfaz de usuario del motor de inferencia:

```text
┌──────────────────┐
│    Open WebUI    │
│     Docker       │
└────────┬─────────┘
         │
         │ API
         ▼
┌──────────────────┐
│      Ollama      │
│     systemd      │
└────────┬─────────┘
         │
         ▼
┌──────────────────┐
│   RX 9060 XT     │
│     16 GB        │
└──────────────────┘
```

Esta separación permite mantener el runtime de IA independiente de la interfaz web.

---

## 🔗 Integración con Puck

Puck utiliza Ollama como backend de inferencia local.

```text
                  Puck
                   │
                   ▼
              Open WebUI
                   │
                   ▼
                Ollama
                   │
          ┌────────┴────────┐
          │                 │
          ▼                 ▼
       Modelo            RX 9060 XT
```

Open WebUI proporciona la interfaz y las funciones adicionales, mientras que Ollama se encarga de ejecutar los modelos.

---

## 🔍 Verificación

Comprobar la versión instalada:

```bash
ollama --version
```

Resultado actual:

```text
ollama version is 0.32.6
```

Comprobar los modelos instalados:

```bash
ollama list
```

Comprobar los modelos actualmente en ejecución:

```bash
ollama ps
```

Comprobar el estado del servicio:

```bash
systemctl status ollama
```

Consultar los logs:

```bash
journalctl -u ollama -f
```

---

## 🛠️ Troubleshooting

Durante la implementación de Puck se realizaron diferentes pruebas con modelos, configuraciones de Open WebUI, búsqueda web y aceleración por hardware.

Los problemas encontrados y sus respectivas soluciones se documentan progresivamente a medida que se realizan cambios en la infraestructura.

Algunos de los aspectos investigados incluyen:

* Integración entre Ollama y Open WebUI.
* Selección de modelos según rendimiento.
* Uso de la GPU para inferencia.
* Integración con SearXNG.
* Configuración de Web Search en Open WebUI.
* Rendimiento de diferentes modelos.
* Configuración de Function Calling.
* Persistencia de los modelos.

---

## 📊 Rendimiento

El rendimiento de los modelos depende principalmente de:

* Modelo utilizado.
* Tamaño del modelo.
* Contexto utilizado.
* Cantidad de parámetros.
* VRAM disponible.
* Configuración de Ollama.
* Aceleración mediante GPU.

Se planea incorporar benchmarks de los diferentes modelos para comparar velocidad de generación, utilización de VRAM y comportamiento bajo diferentes cargas.

---

## 🧠 Lecciones aprendidas

La implementación de Ollama permitió experimentar con una infraestructura de inferencia completamente local y entender la relación entre:

```text
Hardware
   ↓
Sistema operativo
   ↓
Servicio systemd
   ↓
Ollama
   ↓
Modelos
   ↓
Open WebUI
   ↓
Puck
```

Una de las decisiones principales fue mantener Ollama fuera de Docker para separar el motor de inferencia del resto de los servicios y facilitar el acceso directo al hardware de aceleración.

La infraestructura continúa evolucionando a medida que se incorporan nuevos modelos, herramientas y servicios al homelab.

---

