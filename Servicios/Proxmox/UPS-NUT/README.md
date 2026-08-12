# NUT — Network UPS Tools

Sistema de gestión y monitorización del UPS del homelab mediante **Network UPS Tools (NUT)**.

NUT permite obtener información del estado del UPS, monitorizar sus variables eléctricas y gestionar eventos relacionados con alimentación y apagado seguro del sistema.

## ⚡ Arquitectura

La implementación utiliza NUT directamente sobre el host de virtualización.

Los componentes principales son:

* **NUT driver:** `nutdrv_qx`
* **NUT server:** `upsd`
* **NUT monitor:** `upsmon`
* **UPS:** Lyonn 1200VA
* **Conexión:** USB
* **Protocolo detectado:** Voltronic-QS-Hex

El driver se comunica directamente con el UPS y expone sus variables a los servicios de NUT.

## 🔌 UPS

El dispositivo es identificado dentro de NUT como:

```text
lyonn
```

Información detectada:

```text
Lyonn 1200VA UPS
```

El UPS utiliza una topología:

```text
offline / line interactive
```

El estado actual reportado por NUT es:

```text
OL
```

`OL` significa **Online**, indicando que actualmente el UPS está funcionando con alimentación de línea y no mediante batería.

## 🧩 Componentes de NUT

### `nutdrv_qx`

Es el driver encargado de comunicarse directamente con el UPS.

La implementación utiliza:

```text
nutdrv_qx
```

Versión del driver:

```text
2.8.1
```

Protocolo de datos detectado:

```text
Voltronic-QS-Hex 0.10
```

El servicio correspondiente se encuentra gestionado por systemd:

```text
nut-driver@lyonn.service
```

Estado actual:

```text
active (running)
```

### `upsd`

`upsd` funciona como servidor de información de NUT.

Recibe los datos proporcionados por el driver y permite que otros componentes consulten el estado del UPS.

Servicio:

```text
nut-server.service
```

Estado actual:

```text
active (running)
```

### `upsmon`

`upsmon` se encarga de monitorizar el estado del UPS y actuar ante determinados eventos de alimentación.

Servicio:

```text
nut-monitor.service
```

Estado actual:

```text
active (running)
```

## 📊 Variables monitorizadas

Actualmente NUT expone información como:

| Variable             | Descripción           |
| -------------------- | --------------------- |
| `battery.voltage`    | Voltaje de batería    |
| `input.voltage`      | Voltaje de entrada    |
| `output.voltage`     | Voltaje de salida     |
| `output.frequency`   | Frecuencia de salida  |
| `ups.load`           | Carga conectada       |
| `ups.status`         | Estado actual del UPS |
| `ups.beeper.status`  | Estado del buzzer     |
| `ups.delay.shutdown` | Retardo de apagado    |
| `ups.delay.start`    | Retardo de encendido  |
| `ups.type`           | Tipo de UPS           |

Estas variables forman la base para la futura monitorización mediante un dashboard.

## 🔍 Consulta de información

La información disponible puede consultarse mediante:

```bash
upsc lyonn
```

Para listar los dispositivos registrados:

```bash
upsc -l
```

Para obtener una descripción de los UPS disponibles:

```bash
upsc -L
```

Una consulta típica devuelve variables como:

```text
battery.voltage
input.voltage
output.voltage
output.frequency
ups.load
ups.status
ups.type
```

## 🛠️ Diagnóstico

### Comprobar el driver

```bash
systemctl status nut-driver@lyonn --no-pager
```

### Comprobar el servidor NUT

```bash
systemctl status nut-server --no-pager
```

### Comprobar el monitor

```bash
systemctl status nut-monitor --no-pager
```

### Consultar el estado del UPS

```bash
upsc lyonn
```

### Listar dispositivos

```bash
upsc -l
```

## 📁 Configuración

Los archivos de configuración de NUT se encuentran en:

```text
/etc/nut/
```

Entre los principales archivos utilizados se encuentran:

```text
nut.conf
ups.conf
upsd.conf
upsd.users
upsmon.conf
```

La configuración se mantiene fuera del repositorio cuando contiene información específica de la infraestructura o credenciales.

El repositorio documenta la arquitectura y los procedimientos de administración, pero no almacena secretos ni credenciales reales.

## 🔐 Seguridad

La configuración de NUT puede incluir información sensible dependiendo del modo de operación.

No se deben publicar:

* Contraseñas.
* Usuarios de monitorización.
* Credenciales de NUT.
* Direcciones de red privadas.
* Configuraciones que permitan acceder al servicio desde redes externas.
* Información específica de infraestructura que no sea necesaria para reproducir el concepto.

Los valores sensibles deben sustituirse por placeholders antes de realizar un commit.

## 📈 Dashboard

Como siguiente etapa del proyecto se plantea desarrollar un dashboard independiente para visualizar las métricas proporcionadas por NUT.

El objetivo es disponer de una interfaz que permita visualizar, entre otros:

* Voltaje de entrada.
* Voltaje de salida.
* Voltaje de batería.
* Frecuencia.
* Carga.
* Estado del UPS.
* Estado de alimentación.
* Historial de métricas.
* Eventos relacionados con cambios de alimentación.

La implementación del dashboard se documentará por separado.

```text
services/ups/dashboard/
```

## 📚 Referencias

* [Network UPS Tools](https://networkupstools.org/)
* [NUT Documentation](https://networkupstools.org/docs/)
* [NUT Drivers](https://networkupstools.org/docs/man/nutdrv_qx.html)

---

## 📌 Estado actual

**Estado:** 🟢 Operativo

NUT se encuentra funcionando directamente sobre el host y mantiene comunicación activa con el UPS Lyonn 1200VA mediante USB.

El driver, servidor y monitor de NUT se encuentran activos y el dispositivo expone correctamente sus principales variables eléctricas.

El siguiente paso es implementar una interfaz de monitorización independiente para visualizar estas métricas históricamente.
