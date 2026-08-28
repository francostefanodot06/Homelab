# 📸 Immich Server (Proxmox LXC)

> Autentico monstruo de gestión fotográfica autoalojado, corriendo de forma nativa en un contenedor LXC dentro de Proxmox VE. 🚀


---

## 🏗️ Arquitectura y Almacenamiento

Para evitar saturar el sistema y garantizar un rendimiento brutal, el almacenamiento está dividido estratégicamente:

* **🗂️ Disco Raíz (`/`):** Aloja el sistema operativo base (Debian 13), dependencias de Node.js, paquetes y la base de datos **PostgreSQL 16** con extensiones vectoriales.
* **💾 Almacenamiento Multimedia (`/var/lib/immich/upload`):** Montado directamente sobre un disco o pool secundario de gran capacidad para almacenar todos los archivos multimedia (fotos y videos en alta resolución) de forma totalmente aislada.

---

## ⚙️ Especificaciones Técnicas

* **SO:** Debian GNU/Linux 13 🐧
* **Base de datos:** PostgreSQL 16 (Local) 🗄️
* **Entorno:** Node.js (v24+) 🟢
* **Puertos principales:** `2283` (Web/API) | `5432` (PostgreSQL) 🔌

---

## 🧰 Mantenimiento y Comandos Útiles

### 1️⃣ Limpieza de espacio en la raíz
Si necesitás liberar espacio de paquetes temporales acumulados:
```bash
apt-get clean
rm -rf /var/cache/apt/archives/*
