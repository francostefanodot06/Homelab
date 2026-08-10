# Open WebUI

Interfaz web utilizada como frontend principal para **Puck**, el asistente de IA local del homelab.

Open WebUI proporciona la interfaz de conversación, gestión de modelos, RAG, herramientas y configuración de búsqueda web. La inferencia de los modelos se realiza mediante una instancia de **Ollama** ejecutándose directamente en la CT `PuckProject`.

## 🏗️ Arquitectura

La implementación está dividida en componentes independientes:



## 🐳 Despliegue

Open WebUI se ejecuta como contenedor Docker independiente.

El `docker-compose.yml` utilizado para desplegar el servicio se encuentra en:

```text
services/docker/services/openwebui/docker-compose.yml
```

El Compose contiene únicamente la definición necesaria para reproducir el despliegue. La configuración específica del servicio y su integración se documentan en este README.

## 🤖 Integración con Ollama

Durante el desarrollo se evaluaron diferentes formas de conectar Open WebUI con Ollama.

La configuración final utiliza **Ollama ejecutándose fuera del contenedor de Open WebUI**, directamente dentro de la CT `PuckProject`.

Ollama escucha en:

```text
0.0.0.0:11434
```

Desde el contenedor de Open WebUI, el acceso al servicio se realiza mediante la puerta de enlace de Docker:

```text
http://172.17.0.1:11434
```

La comunicación fue comprobada directamente desde el contenedor:

```bash
docker exec open-webui curl -s http://172.17.0.1:11434/api/tags
```

La respuesta permitió verificar que Open WebUI puede acceder correctamente a la instancia de Ollama y consultar los modelos disponibles.

### ¿Por qué Ollama está fuera del contenedor?

Esta separación permite mantener independientes:

* La interfaz de Open WebUI.
* El runtime de inferencia.
* Los modelos.
* El acceso a la GPU AMD.
* La configuración del sistema operativo.

Además, evita tener que modificar el contenedor de Open WebUI para gestionar directamente la GPU.

## 🧠 Modelos disponibles

La instancia de Ollama contiene actualmente modelos locales utilizados para diferentes tareas:

| Modelo              | Tamaño aproximado | Uso                        |
| ------------------- | ----------------: | -------------------------- |
| `qwen2.5:7b`        |            4.7 GB | Uso general                |
| `qwen2.5vl:7b`      |            6.0 GB | Visión                     |
| `qwen3:8b`          |            5.2 GB | Uso general / razonamiento |
| `qwen2.5-coder:14b` |            9.0 GB | Programación               |
| `gemma3:12b`        |            8.1 GB | Uso general                |
| `deepseek-r1:14b`   |            9.0 GB | Razonamiento               |
| `mistral-small:24b` |             14 GB | Modelos de mayor tamaño    |
| `llama3:latest`     |            4.7 GB | Modelo adicional           |

Los modelos no se almacenan dentro del contenedor de Open WebUI.

La gestión de modelos se realiza directamente mediante Ollama:

```bash
ollama list
ollama ps
```

## 🔎 Integración con SearXNG

Open WebUI también se encuentra integrado con una instancia local de **SearXNG**.

El objetivo es permitir que Puck pueda realizar búsquedas web y utilizar los resultados como contexto para responder.



La configuración y despliegue de SearXNG se documentan por separado.

```text
services/docker/services/searxng/
```

## 🧩 RAG y búsqueda web

Durante las pruebas se detectó que la integración de búsqueda podía comportarse de manera diferente dependiendo del modelo y de la configuración de **Native Function Calling**.

La configuración final utiliza el flujo de búsqueda web de Open WebUI para:

1. Recibir la consulta.
2. Consultar SearXNG.
3. Obtener los resultados.
4. Extraer información relevante.
5. Inyectar el contenido recuperado como contexto.
6. Enviar el contexto junto con la consulta al modelo mediante Ollama.

Esto permitió obtener un comportamiento más estable que depender exclusivamente de llamadas de herramientas generadas por el modelo.

## 🧪 Diagnóstico y troubleshooting

Uno de los problemas principales durante el despliegue fue determinar si los errores provenían de Open WebUI, Ollama o de la comunicación entre ambos.

Por este motivo se realizaron pruebas directamente desde el contenedor.

### Verificar el contenedor

```bash
docker ps
```

### Verificar acceso a Ollama

```bash
docker exec open-webui curl -s http://172.17.0.1:11434/api/tags
```

Una respuesta JSON con la lista de modelos confirma que Open WebUI puede comunicarse con Ollama.

### Verificar modelos desde el host

```bash
ollama list
```

### Verificar modelos actualmente cargados

```bash
ollama ps
```

Este último comando permite comprobar qué modelos están actualmente en ejecución.

## ⚠️ Problemas encontrados durante el desarrollo

### Ollama no era un contenedor Docker

Inicialmente se esperaba encontrar un contenedor llamado `ollama`.

Sin embargo:

```bash
docker ps
```

mostró que Ollama no estaba ejecutándose mediante Docker.

La instalación real correspondía a un servicio del sistema:

```text
/etc/systemd/system/ollama.service
```

Esto cambió completamente el enfoque de documentación y conectividad.

### `host.docker.internal` no estaba disponible

Se probó:

```bash
docker exec open-webui getent hosts host.docker.internal
```

sin obtener resolución.

En lugar de depender de `host.docker.internal`, se utilizó directamente la puerta de enlace de la red bridge de Docker:

```text
172.17.0.1
```

La comunicación fue validada correctamente mediante `curl`.

### Diferencia entre `localhost` y el contenedor

Un punto importante durante el troubleshooting fue recordar que:

```text
localhost
```

dentro de Open WebUI **no representa al host PuckProject**.

Representa al propio contenedor.

Por eso, una dirección como:

```text
http://localhost:11434
```

no permite acceder automáticamente al Ollama ejecutándose fuera del contenedor.

## 🔐 Consideraciones de seguridad

Este repositorio documenta la arquitectura y configuración reproducible del servicio, pero **no debe contener secretos reales**.

No se deben subir:

* API keys.
* Tokens.
* Contraseñas.
* Secret keys.
* Credenciales de servicios.
* Tokens de acceso.
* Dumps de bases de datos.
* Configuraciones que contengan información privada del homelab.

Los valores sensibles deben sustituirse por placeholders antes de realizar un commit.

## 📚 Referencias

* [Open WebUI](https://github.com/open-webui/open-webui)
* [Ollama](https://github.com/ollama/ollama)
* [SearXNG](https://github.com/searxng/searxng)

## 🔗 Documentación relacionada

* **Ollama:** [`services/docker/services/ollama/`] (https://github.com/francostefanodot06/Homelab/tree/246def23fb9b0305465e923df79003bd41ceae99/Servicios/SearXNG)
* **SearXNG:** `services/docker/services/searxng/` https://github.com/francostefanodot06/Homelab/blob/246def23fb9b0305465e923df79003bd41ceae99/Servicios/SearXNG/README.md
* **GPU / AMD ROCm:** documentación específica del passthrough de la RX 9060 XT. https://github.com/francostefanodot06/Homelab/blob/246def23fb9b0305465e923df79003bd41ceae99/Servicios/Ollama/GPU.md
* **Proxmox:** `services/proxmox/`

---

## 📌 Estado actual

**Estado:** 🟢 Operativo

Open WebUI funciona como frontend de Puck, conectado a Ollama mediante la red Docker y utilizando los recursos de la GPU AMD disponibles en la infraestructura.

La búsqueda web mediante SearXNG también se encuentra integrada y funcional.
