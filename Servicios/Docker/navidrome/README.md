🎧 Navidrome — Personal Music Server

Servidor de música personal desplegado mediante Docker utilizando Navidrome.

El objetivo del servicio es disponer de una biblioteca musical centralizada y accesible desde distintos dispositivos dentro del homelab.

🏗️ Arquitectura

Navidrome se ejecuta como un contenedor Docker independiente.

┌───────────────────────────────┐
│        Debian Docker          │
│                               │
│  ┌─────────────────────────┐  │
│  │       Navidrome         │  │
│  │                         │  │
│  │  Web UI / Music Server  │  │
│  │                         │  │
│  │       Port: 4533        │  │
│  └────────────┬────────────┘  │
│               │               │
│        ┌──────┴──────┐        │
│        │             │        │
│      /data         /music     │
│        │             │        │
│  Application      Music      │
│     data          library    │
└───────────────────────────────┘
🐳 Docker

Imagen utilizada:

deluan/navidrome:latest

El contenedor utiliza una política de reinicio:

unless-stopped

El servicio utiliza el puerto:

4533

El almacenamiento se divide en dos volúmenes:

Directorio	Función	Modo
/data	Datos y configuración de Navidrome	RW
/music	Biblioteca musical	RO

La biblioteca musical se monta en modo read-only para evitar modificaciones accidentales sobre los archivos originales desde el contenedor.

⚙️ Configuración

Las principales variables utilizadas son:

ND_LOGLEVEL=info
ND_SESSIONTIMEOUT=24h
ND_MUSICFOLDER=/music
ND_DATAFOLDER=/data
ND_CONFIGFILE=/data/navidrome.toml
ND_PORT=4533

La configuración específica de la infraestructura no se incluye en el repositorio.

El docker-compose.yml incluido en este proyecto utiliza rutas relativas y sirve como ejemplo reproducible del despliegue.

🎵 Biblioteca musical

La biblioteca se almacena fuera del contenedor y se monta mediante un bind mount.

Esto permite:

Mantener la música independiente del contenedor.
Recrear el contenedor sin perder la biblioteca.
Mantener los archivos originales protegidos contra escritura.
Separar los datos de aplicación de los archivos multimedia.
🏷️ Organización y metadatos

La biblioteca utiliza metadatos musicales para mantener organizada la colección.

Para la gestión y corrección de metadatos se utiliza MusicBrainz Picard, permitiendo normalizar información como:

Artista.
Álbum.
Título.
Número de pista.
Portada.
Metadatos adicionales.

Esto permite que Navidrome pueda identificar y organizar correctamente la biblioteca.

📊 Funcionamiento

Navidrome proporciona:

Interfaz web.
Streaming de música.
Organización por artistas y álbumes.
Gestión de playlists.
Historial de reproducción.
Scrobbling.
Compatibilidad con clientes Subsonic/OpenSubsonic.
Acceso desde distintos dispositivos.

Durante las pruebas se verificó correctamente el streaming de diferentes archivos de la biblioteca.

🔍 Administración

Para comprobar el estado del contenedor:

docker ps

Consultar los logs:

docker logs navidrome

Seguir los logs en tiempo real:

docker logs -f navidrome

Inspeccionar la configuración del contenedor:

docker inspect navidrome

Reiniciar el servicio:

docker restart navidrome
🛡️ Consideraciones de seguridad

El repositorio público no contiene:

Direcciones IP privadas.
Rutas reales del sistema.
Credenciales.
Contraseñas.
Tokens.
Configuraciones específicas de la infraestructura.
Información personal de usuarios.

El docker-compose.yml incluido está sanitizado y utiliza rutas relativas para que pueda utilizarse como referencia sin revelar la estructura real del homelab.

📁 Estructura

Una implementación típica del proyecto puede utilizar:

navidrome/
├── docker-compose.yml
├── data/
└── music/

Los directorios data/ y music/ no necesitan formar parte del repositorio.

🚀 Despliegue

Crear la estructura:

mkdir -p navidrome/{data,music}
cd navidrome

Colocar el docker-compose.yml en el directorio y ejecutar:

docker compose up -d

Comprobar que el contenedor está funcionando:

docker ps
📌 Estado actual

Estado: 🟢 Operativo

Navidrome se encuentra desplegado mediante Docker y funcionando correctamente como servidor de música personal dentro del homelab.

La biblioteca musical se encuentra separada del contenedor mediante bind mounts y montada en modo read-only.

La organización de metadatos se realiza mediante MusicBrainz Picard y Navidrome se encarga de proporcionar la interfaz y el streaming de la biblioteca.
