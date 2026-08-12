# Tailscale

**Tailscale** proporciona acceso remoto seguro a los servicios del homelab mediante una red privada basada en WireGuard.

En este proyecto se utiliza principalmente para poder administrar Proxmox y acceder a servicios internos sin exponerlos directamente a Internet.

## 🧩 Función dentro del homelab

Tailscale está instalado directamente sobre el **host Proxmox**, funcionando como servicio del sistema.

Esto permite acceder remotamente al nodo y, dependiendo de la configuración de red y permisos, alcanzar otros servicios de la infraestructura.

La interfaz de red creada por Tailscale aparece como:

```text
tailscale0
```

La instalación se mantiene separada de los contenedores y máquinas virtuales, proporcionando una capa de acceso remoto independiente.

## ⚙️ Instalación

Tailscale se ejecuta mediante el servicio:

```text
tailscaled.service
```

El servicio está configurado para iniciarse automáticamente con el sistema.

Estado actual:

```text
Active: active (running)
```

La versión instalada actualmente es:

```text
1.102.2
```

La versión puede comprobarse mediante:

```bash
tailscale version
```

## 🔐 Autenticación y red privada

La autenticación se realiza mediante la cuenta de Tailscale asociada al homelab.

Cada dispositivo autorizado recibe una identidad dentro de la tailnet y una dirección privada de Tailscale.

Por motivos de seguridad, este repositorio **no documenta**:

* Direcciones IP de Tailscale.
* Direcciones IPv6 de la tailnet.
* Correo electrónico de la cuenta.
* Lista de dispositivos.
* Machine IDs.
* Auth keys.
* Tokens.
* ACLs privadas.
* Información específica de la tailnet.

## 🖥️ Integración con Proxmox

Tailscale se ejecuta directamente en el host Proxmox en lugar de instalarse individualmente en cada servicio.

Esto permite utilizar el nodo Proxmox como punto de administración remota de la infraestructura.

La configuración de red del host puede verificarse mediante:

```bash
ip -br addr
```

La presencia de la interfaz:

```text
tailscale0
```

indica que la interfaz de Tailscale está disponible.

## 🧪 Verificación

### Comprobar el servicio

```bash
systemctl status tailscaled --no-pager
```

### Comprobar la versión

```bash
tailscale version
```

### Consultar dispositivos conectados

```bash
tailscale status
```

### Consultar las direcciones asignadas

```bash
tailscale ip
```

Estas herramientas permiten comprobar rápidamente que el agente está funcionando y que el nodo está conectado a la tailnet.

## 🛠️ Troubleshooting

En caso de problemas, el primer paso es comprobar el estado del servicio:

```bash
systemctl status tailscaled --no-pager
```

Después se puede comprobar el estado de la conexión:

```bash
tailscale status
```

Y verificar que la interfaz de red exista:

```bash
ip -br addr
```

Si `tailscale0` está presente y `tailscaled.service` aparece como `active (running)`, la instalación base de Tailscale está funcionando correctamente.

## 🔒 Consideraciones de seguridad

Tailscale permite evitar la exposición directa de determinados servicios del homelab a Internet.

Sin embargo, utilizar una red privada no elimina la necesidad de controlar el acceso.

Las credenciales, claves de autenticación y políticas de acceso deben mantenerse fuera del repositorio.

Nunca se deben publicar:

```text
Auth Keys
API Keys
Access Tokens
Machine Keys
Private Keys
ACLs privadas
Información de la cuenta
```

Si una configuración necesita documentar uno de estos valores, debe utilizarse un placeholder:

```text
<TAILSCALE_IP>
<TAILSCALE_AUTH_KEY>
<TAILSCALE_API_KEY>
<TAILSCALE_DOMAIN>
```

## 📌 Estado actual

**Estado:** 🟢 Operativo

Tailscale está funcionando directamente sobre el host Proxmox y se inicia automáticamente mediante `systemd`.

La conectividad de la tailnet se encuentra operativa y el nodo puede utilizarse para administración remota del homelab.

---

## 📚 Referencias

* [Tailscale](https://tailscale.com/)
* [Tailscale Documentation](https://tailscale.com/docs/)
* [WireGuard](https://www.wireguard.com/)
