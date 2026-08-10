# 🔎 SearXNG

Documentación del despliegue de **SearXNG** dentro del homelab y su integración con **Open WebUI** y **Puck**, el asistente de inteligencia artificial local.

SearXNG funciona como el motor de búsqueda web utilizado por Puck para obtener información actualizada de Internet sin depender directamente de una API comercial de búsqueda.

---

## 🎯 Objetivo

Incorporar capacidades de búsqueda web al asistente local Puck mediante una instancia propia de SearXNG.

El objetivo es construir el siguiente flujo:

```text
Usuario
   │
   ▼
Open WebUI
   │
   │ Web Search
   ▼
SearXNG
   │
   ├── Motor de búsqueda A
   ├── Motor de búsqueda B
   ├── Motor de búsqueda C
   └── ...
   │
   ▼
Resultados web
   │
   ▼
Open WebUI
   │
   │ Contexto recuperado
   ▼
Modelo local (Qwen)
   │
   ▼
Respuesta de Puck
```

De esta forma, el modelo de lenguaje continúa ejecutándose localmente mientras SearXNG se encarga de consultar fuentes externas.

---

# 🏗️ Arquitectura

SearXNG se ejecuta como un contenedor Docker independiente dentro del servidor del homelab.

```text
                    ┌──────────────────────┐
                    │      PuckProject     │
                    │    Ubuntu 22.04 LTS  │
                    └──────────┬───────────┘
                               │
                         Docker Engine
                               │
              ┌────────────────┴────────────────┐
              │                                 │
      ┌───────▼────────┐               ┌────────▼───────┐
      │   Open WebUI   │               │     SearXNG    │
      │     :3000      │──────────────►│      :8080     │
      └───────┬────────┘  Web Search   └────────┬───────┘
              │                                  │
              │                                  ▼
              │                           Motores externos
              │
              ▼
          Ollama
              │
              ▼
        Modelos locales
              │
              ▼
             Puck
```

---

# 🖥️ Entorno

| Componente        | Tecnología                   |
| ----------------- | ---------------------------- |
| Servidor          | PuckProject                  |
| Sistema operativo | Ubuntu 22.04 LTS             |
| Contenedores      | Docker                       |
| Orquestación      | Docker Compose               |
| Motor de búsqueda | SearXNG                      |
| Interfaz de IA    | Open WebUI                   |
| Inferencia        | Ollama                       |
| Modelos           | Qwen y otros modelos locales |
| GPU               | AMD Radeon RX 9060 XT 16 GB  |

SearXNG forma parte de la infraestructura Docker del servidor y no requiere una máquina virtual independiente.

---

# 🐳 Despliegue con Docker Compose

El despliegue se realiza mediante Docker Compose.

Archivo:

```text
services/docker/services/searxng/docker-compose.yml
```

Configuración utilizada:

Servicios/SearXNG/docker-compose.yml

## Parámetros principales

### Imagen

```yaml
image: searxng/searxng:latest
```

Se utiliza la imagen oficial de SearXNG disponible para Docker.

### Puerto

```yaml
ports:
  - "8080:8080"
```

El puerto `8080` del servidor se publica hacia el puerto `8080` del contenedor.

### Persistencia

```yaml
volumes:
  - ./searxng:/etc/searxng:rw
```

La configuración de SearXNG se mantiene fuera del contenedor.

Esto permite modificar la configuración sin perderla cuando el contenedor es recreado.

### Reinicio automático

```yaml
restart: unless-stopped
```

El contenedor se reinicia automáticamente después de un fallo o reinicio del servidor, salvo que haya sido detenido manualmente.

### Logs

Los logs utilizan el driver `json-file` con un límite de tamaño:

```yaml
logging:
  driver: "json-file"
  options:
    max-size: "1m"
    max-file: "1"
```

Esto evita que los logs del contenedor crezcan indefinidamente.

---

# 📁 Estructura del despliegue

En el servidor, la configuración de SearXNG se encuentra en:

```text
/opt/searxng-setup/
├── docker-compose.yml
├── searxng-working-backup.tar.gz
└── searxng/
    └── settings.yml
```

El repositorio público contiene únicamente los archivos apropiados para documentar y reproducir el despliegue.

No se publica información sensible ni credenciales utilizadas por la instancia.

---

# ⚙️ Configuración de SearXNG

El archivo principal de configuración utilizado por la instancia es:

```text
/etc/searxng/settings.yml
```

Este archivo se encuentra persistido mediante el volumen:

```text
./searxng:/etc/searxng:rw
```

Entre las configuraciones relevantes se encuentran:

* Configuración general de la instancia.
* Motores de búsqueda habilitados.
* Formatos de respuesta.
* Configuración de suspensión de motores.
* Parámetros de búsqueda.
* Configuración relacionada con seguridad y privacidad.
* Integración con componentes auxiliares cuando corresponde.

La configuración fue inicialmente basada en una guía de la comunidad y posteriormente fue adaptada, depurada y probada dentro del homelab.

---

# 🔐 Consideraciones de seguridad

SearXNG puede utilizar valores sensibles en su configuración, especialmente para determinados motores que requieren credenciales o API keys.

Por este motivo:

* No se publican secretos reales.
* No se publican API keys.
* No se publican contraseñas.
* Las claves secretas se mantienen fuera del repositorio.
* Los archivos de configuración utilizados en producción son revisados antes de publicarse.

La `secret_key` de SearXNG no se almacena como un valor sensible dentro del repositorio.

---

# 🔎 Integración con Open WebUI

La función principal de esta instancia es proporcionar búsqueda web a Open WebUI.

El flujo utilizado es:

```text
Open WebUI
     │
     │ consulta de búsqueda
     ▼
  SearXNG
     │
     │ consulta motores externos
     ▼
 Resultados
     │
     ▼
 Open WebUI
     │
     │ contexto recuperado
     ▼
 Modelo local
```

Open WebUI utiliza los resultados obtenidos por SearXNG como contexto para que el modelo pueda responder preguntas que requieren información externa o actualizada.

Esto permite mantener la inferencia del modelo local mientras se agrega acceso controlado a información disponible en Internet.

---

# 🧠 Integración con Puck

SearXNG es una pieza fundamental de la arquitectura de Puck.

Sin búsqueda web:

```text
Usuario
   ↓
Puck
   ↓
Modelo local
   ↓
Respuesta basada principalmente en conocimiento existente
```

Con SearXNG:

```text
Usuario
   ↓
Puck
   ↓
Open WebUI
   ↓
SearXNG
   ↓
Internet
   ↓
Resultados relevantes
   ↓
Contexto
   ↓
Modelo local
   ↓
Respuesta
```

Esto permite que Puck pueda trabajar con información que cambia con el tiempo, como documentación, noticias, proyectos, versiones de software o información técnica reciente.

---

# 🛠️ Bitácora de despliegue

La implementación de SearXNG fue uno de los componentes que más problemas presentó durante el desarrollo del homelab.

La experiencia permitió practicar diagnóstico de contenedores, análisis de logs, configuración YAML, comunicación entre servicios y debugging de aplicaciones distribuidas.

---

## 💥 Problema 1 — Fallos durante el arranque de SearXNG

### Síntoma

Al levantar el contenedor mediante Docker Compose, SearXNG podía entrar en un ciclo de reinicios o finalizar durante el proceso de inicialización.

Los errores estaban relacionados principalmente con el procesamiento de la configuración.

### Investigación

Se revisaron los logs del contenedor para determinar si el problema provenía de Docker o de la propia aplicación.

La investigación mostró que determinados parámetros de la configuración inicial no eran adecuados para la versión utilizada.

También se encontraron problemas relacionados con:

* Sintaxis YAML.
* Indentación.
* Parámetros heredados de configuraciones anteriores.
* Motores de búsqueda que podían no funcionar correctamente.

### Solución

Se depuró `settings.yml` y se fueron validando sus componentes hasta obtener una configuración funcional.

Se prestó especial atención a:

```yaml
search:
```

y a:

```yaml
formats:
  - html
  - json
```

La habilitación de JSON fue especialmente importante para la integración con herramientas que necesitan consumir los resultados de búsqueda programáticamente.

### Aprendizaje

Un archivo YAML aparentemente correcto puede producir errores difíciles de diagnosticar cuando contiene una gran cantidad de configuración.

Una de las primeras estrategias utilizadas fue reducir el problema a una configuración mínima y posteriormente volver a incorporar componentes progresivamente.

---

# 💥 Problema 2 — Configuración de `limiter.toml`

### Síntoma

Durante las pruebas aparecieron errores y advertencias relacionados con el sistema de limitación de solicitudes de SearXNG.

Esto generaba dudas sobre si el problema estaba relacionado con Docker, los motores de búsqueda o la configuración interna de SearXNG.

### Investigación

Se revisó la configuración utilizada por la versión instalada y se identificó la necesidad de disponer de una configuración adecuada para el sistema de limitación.

### Solución

Se creó y persistió la configuración correspondiente dentro del directorio utilizado por SearXNG:

```text
/etc/searxng/limiter.toml
```

También se ajustaron las reglas necesarias para permitir el funcionamiento esperado dentro del entorno del homelab.

### Aprendizaje

No todos los errores de un contenedor indican un problema con Docker.

En este caso fue necesario comprender cómo los componentes internos de SearXNG utilizaban los archivos de configuración antes de modificar el Compose.

---

# 💥 Problema 3 — Errores `403`, CAPTCHA y motores externos

### Síntoma

Durante las búsquedas aparecieron respuestas como:

```text
403 Forbidden
```

y desafíos CAPTCHA provenientes de determinados motores.

En algunos casos una búsqueda podía devolver pocos resultados o incluso ninguno.

### Causa

SearXNG funciona como intermediario entre el usuario y múltiples motores de búsqueda externos.

Estos motores aplican sus propias políticas contra tráfico automatizado, rate limiting y scraping.

Por lo tanto, que SearXNG esté funcionando correctamente no garantiza que todos los motores externos respondan correctamente.

### Solución

Se revisaron los motores utilizados y los tiempos de suspensión configurados en SearXNG.

Los motores que presentaban errores recurrentes podían ser suspendidos temporalmente por SearXNG en lugar de provocar el fallo de toda la búsqueda.

La configuración incluía parámetros como:

```yaml
suspended_times:
```

para controlar el comportamiento después de determinados errores.

También se redujo la dependencia de motores que presentaban problemas frecuentes.

### Aprendizaje

Una instancia de SearXNG no depende de un único backend.

La disponibilidad de los resultados depende de múltiples motores externos y sus respectivas políticas.

Por eso una configuración robusta debe tolerar que algunos motores fallen temporalmente.

---

# 💥 Problema 4 — Diagnóstico mediante `curl`

### Síntoma

En determinados momentos Open WebUI no conseguía utilizar correctamente la búsqueda web.

No estaba claro si el problema estaba en:

* Open WebUI.
* SearXNG.
* Docker.
* La configuración de búsqueda.
* La respuesta JSON.
* La comunicación entre servicios.

### Diagnóstico

En lugar de continuar modificando Open WebUI a ciegas, se decidió probar SearXNG directamente.

Se utilizó `curl` para realizar solicitudes al endpoint de búsqueda.

Ejemplo:

```bash
curl -X POST "http://localhost:8080/search" \
  -d "q=test&format=json"
```

La finalidad era verificar si SearXNG podía recibir una consulta y devolver una respuesta válida independientemente de Open WebUI.

### Resultado

Este procedimiento permitió separar el problema en componentes.

El diagnóstico pasó de:

```text
"No funciona la búsqueda web"
```

a:

```text
¿Funciona SearXNG?
        ↓
¿Responde JSON?
        ↓
¿Devuelve resultados?
        ↓
¿Open WebUI puede consumirlos?
        ↓
¿El modelo procesa correctamente el contexto?
```

### Aprendizaje

Este fue uno de los aprendizajes más importantes del despliegue:

> Cuando una arquitectura tiene múltiples componentes, hay que probar cada componente individualmente antes de asumir que el problema está en el sistema completo.

---

# 💥 Problema 5 — Comunicación con Open WebUI

### Síntoma

SearXNG podía funcionar correctamente de forma independiente, pero Open WebUI no siempre conseguía utilizarlo.

En algunos casos la interfaz indicaba que la búsqueda web no estaba disponible o directamente no aparecía contexto web en la conversación.

### Causa

La integración dependía de que Open WebUI utilizara correctamente el endpoint de búsqueda de SearXNG.

La URL configurada debía apuntar al endpoint adecuado y utilizar el parámetro de búsqueda esperado.

El formato utilizado durante las pruebas fue:

```text
http://<IP-DEL-SERVIDOR>:8080/search?q={query}
```

La dirección final depende de cómo se acceda al servicio dentro de la infraestructura Docker y de la configuración utilizada por Open WebUI.

### Solución

Se revisó:

* Dirección del servicio.
* Puerto.
* Endpoint `/search`.
* Parámetro `q`.
* Formato de respuesta.
* Configuración de Web Search en Open WebUI.

### Aprendizaje

Que un servicio sea accesible desde el navegador no significa necesariamente que otra aplicación pueda utilizarlo correctamente.

La API y el formato de respuesta deben coincidir con lo que espera el consumidor.

---

# 💥 Problema 6 — Native Function Calling y búsqueda web

### Síntoma

Uno de los problemas más particulares apareció al utilizar modelos locales con la función de búsqueda web.

En determinadas configuraciones, el modelo intentaba generar llamadas estructuradas a herramientas en lugar de procesar correctamente el contexto recuperado.

Esto podía provocar:

* Respuestas incompletas.
* Llamadas de funciones incorrectas.
* Texto estructurado inesperado.
* Búsquedas que no terminaban correctamente.

### Investigación

Se realizaron pruebas cambiando la configuración de Function Calling y del flujo utilizado por Open WebUI.

El problema no parecía estar exclusivamente en SearXNG.

La interacción involucraba:

```text
Modelo
   ↕
Open WebUI
   ↕
Web Search
   ↕
SearXNG
```

Por lo tanto, el comportamiento podía variar según el modelo utilizado y las capacidades de tool/function calling soportadas.

### Solución

Para el flujo que se estaba utilizando se optó por desactivar el uso de Native Function Calling cuando interfería con la búsqueda web y utilizar el flujo de recuperación de contexto de Open WebUI.

El flujo resultante era:

```text
Consulta
   ↓
Open WebUI
   ↓
SearXNG
   ↓
Resultados
   ↓
Contexto recuperado
   ↓
Prompt del modelo
   ↓
Qwen
   ↓
Respuesta
```

### Aprendizaje

El funcionamiento de una herramienta de IA no depende únicamente del modelo.

La combinación:

```text
Modelo + UI + herramientas + contexto + configuración
```

puede producir comportamientos diferentes incluso utilizando el mismo modelo.

---

# 🧪 Diagnóstico y comandos útiles

## Ver contenedores

```bash
docker ps
```

## Ver logs de SearXNG

```bash
docker logs searxng
```

Para seguirlos en tiempo real:

```bash
docker logs -f searxng
```

## Inspeccionar el contenedor

```bash
docker inspect searxng
```

## Comprobar el endpoint

```bash
curl "http://localhost:8080/search?q=test&format=json"
```

## Comprobar el estado HTTP

```bash
curl -I "http://localhost:8080"
```

## Reiniciar SearXNG

```bash
docker restart searxng
```

## Ver el Compose

```bash
docker compose config
```

Este comando es especialmente útil para detectar errores de sintaxis o problemas en la configuración de Compose antes de levantar los servicios.

---

# 💾 Persistencia

La configuración se mantiene mediante un bind mount:

```yaml
volumes:
  - ./searxng:/etc/searxng:rw
```

Esto significa que la configuración no depende exclusivamente del filesystem interno del contenedor.

Si el contenedor es eliminado y recreado, los archivos presentes en el directorio persistente continúan disponibles.

---

# 🔄 Actualización

El servicio utiliza:

```yaml
image: searxng/searxng:latest
```

Para actualizar la imagen:

```bash
docker compose pull
docker compose up -d
```

Después de una actualización se deben revisar los logs:

```bash
docker logs searxng --tail 100
```

y verificar que las búsquedas continúen funcionando correctamente.

No se recomienda actualizar componentes críticos del homelab sin comprobar previamente cambios de configuración o compatibilidad.

---

# 🛡️ Consideraciones de seguridad y privacidad

SearXNG ayuda a centralizar las búsquedas realizadas por el asistente y evita depender directamente de una API comercial única.

Sin embargo, esto no significa que las consultas sean completamente privadas frente a Internet.

SearXNG debe comunicarse con motores externos para obtener resultados.

Por lo tanto:

```text
Usuario
   ↓
SearXNG
   ↓
Motores externos
```

sigue implicando tráfico hacia terceros.

Además:

* No deben almacenarse secretos directamente en el repositorio.
* Las API keys deben mantenerse fuera de Git.
* Las contraseñas no deben incluirse en `settings.yml` público.
* Las configuraciones de producción deben revisarse antes de publicarse.
* La instancia no debe exponerse públicamente sin una estrategia de seguridad adecuada.

---

# 📚 Recursos utilizados

La configuración inicial de SearXNG se basó parcialmente en documentación y contenido de la comunidad.

El proceso no consistió simplemente en copiar una configuración y ejecutarla: fue necesario adaptarla, depurarla y solucionar problemas específicos del entorno Docker y de la integración con Open WebUI.

Los recursos utilizados se mantienen como referencia en la documentación del proyecto.

---

# 🧠 Lecciones aprendidas

Este servicio terminó siendo mucho más que una instalación de un contenedor.

Durante el proceso se practicaron conceptos relacionados con:

* Docker.
* Docker Compose.
* YAML.
* Bind mounts.
* Persistencia.
* Logs de contenedores.
* Diagnóstico mediante `curl`.
* APIs HTTP.
* JSON.
* Comunicación entre servicios.
* Rate limiting.
* Motores de búsqueda.
* Errores HTTP.
* Integración de herramientas con modelos de IA.
* RAG.
* Function Calling.
* Debugging de sistemas distribuidos.

Uno de los principales aprendizajes fue dejar de tratar el sistema como una única aplicación.

Cuando Puck no podía realizar una búsqueda correctamente, el problema podía estar en cualquiera de las capas:

```text
Modelo
  ↓
Open WebUI
  ↓
Configuración Web Search
  ↓
HTTP
  ↓
SearXNG
  ↓
Motor de búsqueda
  ↓
Internet
```

Por eso el diagnóstico se realizó progresivamente desde cada componente hasta encontrar el punto exacto de fallo.

---

# 🚀 Estado actual

**Estado:** ✅ Operativo

SearXNG se encuentra desplegado mediante Docker y funciona como backend de búsqueda web para Open WebUI/Puck.

La infraestructura continúa en desarrollo y la configuración puede evolucionar a medida que se incorporen nuevos servicios, modelos y mecanismos de seguridad.

---

## 📌 Próximas mejoras

* [ ] Revisar y documentar completamente `settings.yml`.
* [ ] Revisar configuración de `limiter.toml`.
* [ ] Incorporar monitoreo de SearXNG.
* [ ] Mejorar la observabilidad de las búsquedas.
* [ ] Evaluar reverse proxy.
