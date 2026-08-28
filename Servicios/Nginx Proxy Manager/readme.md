# 🌐 Servicio: Nginx Proxy Manager (NPM)

## 📌 Datos del Contenedor
* **LXC ID:** `<ID_LXC>` | **Hostname:** `npm-lxc`
* **IP Local:** `<IP_LOCAL_NPM>` | **Tipo:** Unprivileged LXC
* **Método de Instalación:** Proxmox VE Helper-Scripts (`tteck`)

---

## 🛠️ Puertos
* **Admin UI:** `http://<IP_LOCAL_NPM>:81`
* **HTTP / HTTPS:** `80` / `443`

---

## 🔒 Certificados & DNS
* **Proveedor DNS:** DuckDNS (Validación vía DNS Challenge)
* **Token:** Guardado en gestor de contraseñas
* **Certificados:** Individuales / Wildcard para `<TU_DOMINIO>.duckdns.org`

---

## 🔄 Mantenimiento
* **Actualización:** Ejecutar `update` en la consola del LXC.
* **Datos Persistentes:** `/data` (SQLite interna y configs) y `/etc/letsencrypt`

---

## 📋 Ejemplo de mapeo de Servicios

| Subdominio | Host / IP Destino | Puerto | SSL |
| :--- | :--- | :--- | :--- |
| `excalidraw-<DOMINIO>` | `<IP_DOCKER>` | `5000` | ✅ |
| `navidrome-<DOMINIO>` | `<IP_DOCKER>` | `4533` | ✅ |
| `ai-<DOMINIO>` | `<IP_OPENWEBUI>` | `<PUERTO>` | ✅ |
