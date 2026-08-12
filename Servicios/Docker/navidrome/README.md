# Navidrome

Servidor de música self-hosted del homelab, desplegado mediante Docker.

Navidrome proporciona una interfaz web para gestionar y reproducir una biblioteca musical, manteniendo los archivos multimedia separados de los datos internos de la aplicación.

## 🧩 Características

La implementación utiliza:

* **Navidrome** como servidor de música.
* **Docker** para el despliegue y aislamiento del servicio.
* Almacenamiento persistente para los datos de la aplicación.
* Un directorio independiente para la biblioteca musical.
* Interfaz web para reproducción y gestión de la biblioteca.
* **MusicBrainz Picard** para normalización y organización de metadatos.

## 🐳 Despliegue

Navidrome se ejecuta como un contenedor Docker independiente.

La imagen utilizada actualmente es:

```text
deluan/navidrome:latest
```

El servicio utiliza el puerto:

```text
4533
```

La configuración de despliegue se mantiene separada de la documentación:

```text
services/docker/services/navidrome/
```

El `docker-compose.yml` contiene únicamente la definición necesaria para reproducir el servicio.

## 💾 Almacenamiento

El contenedor utiliza dos volúmenes independientes:

| Volumen            | Uso                                      | Acceso            |
| ------------------ | ---------------------------------------- | ----------------- |
| Datos de Navidrome | Configuración, base de datos y metadatos | Lectura/escritura |
| Biblioteca musical | Archivos multimedia                      | Solo lectura      |

La biblioteca musical se monta como **read-only** dentro del contenedor.

Esta separación permite:

* Mantener los datos de la aplicación independientes de los archivos multimedia.
* Facilitar backups y migraciones.
* Proteger los archivos originales frente a modificaciones accidentales.
* Gestionar la biblioteca independientemente del contenedor.

## ⚙️ Configuración

Las principales opciones utilizadas actualmente incluyen:

```text
ND_MUSICFOLDER=/music
ND_DATAFOLDER=/data
ND_CONFIGFILE=/data/navidrome.toml
ND_PORT=4533
ND_LOGLEVEL=info
ND_SESSIONTIMEOUT=24h
```

La configuración persistente de Navidrome se almacena dentro del volumen de datos.

No se almacenan credenciales ni información privada directamente en el repositorio.

## 🎼 Gestión de la biblioteca

La biblioteca musical se administra de forma independiente del contenedor de Navidrome.

**MusicBrainz Picard** se utiliza para identificar, corregir y normalizar los metadatos de los archivos musicales.

El flujo general es:

```text
Archivos musicales
        │
        ▼
MusicBrainz Picard
        │
        ▼
Metadatos normalizados
        │
        ▼
Biblioteca musical
        │
        ▼
Navidrome
        │
        ▼
Reproducción
```

Esto permite mantener una biblioteca con información consistente sobre:

* Artistas.
* Álbumes.
* Canciones.
* Géneros.
* Portadas.
* Metadatos.

## ▶️ Reproducción

Navidrome proporciona streaming de los archivos almacenados en la biblioteca.

Durante las pruebas se verificó correctamente:

* Reproducción desde navegador.
* Streaming de archivos MP3.
* Registro de reproducción.
* Actualización del estado de reproducción.
* Indexación de la biblioteca.
* Acceso mediante la interfaz web.

## 🔍 Diagnóstico

### Verificar el contenedor

```bash
docker ps
```

### Inspeccionar la imagen utilizada

```bash
docker inspect navidrome --format '{{json .Config.Image}}'
```

### Comprobar los volúmenes

```bash
docker inspect navidrome --format '{{json .Mounts}}'
```

### Consultar variables de configuración

```bash
docker inspect navidrome --format '{{json .Config.Env}}'
```

### Consultar logs

```bash
docker logs --tail 50 navidrome
```

### Comprobar el estado

```bash
docker inspect navidrome --format '{{.State.Status}}'
```

## 🔐 Seguridad

La documentación del servicio no contiene información sensible de la infraestructura.

No se deben publicar:

* Contraseñas.
* Tokens.
* API keys.
* Credenciales.
* Direcciones IP privadas.
* Rutas específicas del sistema anfitrión.
* Información de acceso a la biblioteca.
* Logs que contengan actividad personal de reproducción.

Los valores específicos de infraestructura deben mantenerse fuera del repositorio público o reemplazarse por placeholders.

## 📁 Estructura

La documentación y configuración relacionada con Navidrome se organiza de forma independiente:

```text
services/
└── docker/
    └── services/
        └── navidrome/
            ├── docker-compose.yml
            └── README.md
```

Los diagramas de arquitectura se documentarán posteriormente mediante **Excalidraw**, manteniéndolos como recursos independientes del README.

## 📚 Referencias

* [Navidrome](https://www.navidrome.org/)
* [Navidrome Documentation](https://www.navidrome.org/docs/)
* [Navidrome GitHub](https://github.com/navidrome/navidrome)

---

## 📌 Estado actual

**Estado:** 🟢 Operativo

Navidrome se encuentra desplegado mediante Docker y funcionando correctamente como servidor de música self-hosted.

La reproducción y el streaming de la biblioteca funcionan correctamente, mientras que los datos de aplicación se mantienen separados de los archivos multimedia.

La biblioteca utiliza MusicBrainz Picard para mantener sus metadatos organizados antes de ser indexada por Navidrome.
