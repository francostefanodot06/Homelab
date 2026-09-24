# 🔐 Vaultwarden: Gestión de Secretos y Criptografía Autoalojada

Implementación de un servidor backend de gestión de credenciales ultraligero escrito en Rust, totalmente compatible con el ecosistema de clientes de Bitwarden. Este servicio garantiza la soberanía absoluta de los datos y secretos sensibles de la infraestructura.

## 🛡️ Postura de Seguridad Implementada
* **Cifrado de Extremo a Extremo (E2EE):** La bóveda se cifra y descifra únicamente a nivel de cliente; el servidor central de Frankdevlab solo almacena y transita datos completamente ofuscados.
* **Aislamiento Perimetral:** El contenedor opera en una subred de Docker aislada y se expone exclusivamente a través de Nginx Proxy Manager, requiriendo validación estricta de certificados SSL (HTTPS).
* **Gestión de Variables de Entorno:** Separación de los datos de configuración sensibles utilizando archivos `.env` que permanecen excluidos del control de versiones (Git), protegiendo el *Admin Token* y las credenciales de base de datos.
