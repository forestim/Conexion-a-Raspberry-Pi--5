# 🍓 Raspberry Pi 5 — Acceso Remoto con Tailscale

![Raspberry Pi](https://img.shields.io/badge/Hardware-Raspberry%20Pi%205-C51A4A?logo=raspberrypi&logoColor=white)
![Tailscale](https://img.shields.io/badge/Network-Tailscale-black?logo=tailscale&logoColor=white)
![VPN](https://img.shields.io/badge/Security-VPN-green)

> **Resumen:** Guía para desplegar un túnel seguro, habilitar el acceso SSH remoto y configurar la Raspberry Pi como un **Exit Node** (punto de salida a internet) seguro.

---

## 📋 Introducción: ¿Qué es un Exit Node?

Al configurar la Raspberry Pi como *Exit Node*, permites que tus otros dispositivos (móvil, laptop) enruten **todo** su tráfico de internet a través de la conexión de tu casa, cifrando la navegación cuando estás en redes públicas (cafeterías, aeropuertos).

```mermaid
graph LR
    A[ Laptop / Móvil ] -->|Túnel Encriptado| B(Raspberry Pi 5)
    B -->|Salida Segura| C[Internet]
    style B fill:#C51A4A,stroke:#333,stroke-width:2px,color:white
```

---

## 🚀 1. Instalación de Tailscale

Actualizamos los repositorios y ejecutamos el script de instalación oficial.

```bash
sudo apt update && curl -fsSL [https://tailscale.com/install.sh](https://tailscale.com/install.sh) | sh
```

---

## 🔧 2. Configuración de Red (IP Forwarding)

Para que la Raspberry Pi pueda "pasar" el tráfico de internet de un dispositivo a otro (funcionar como router/Exit Node), debemos activar el reenvío de paquetes IPv4 e IPv6 en el kernel.

Ejecuta los siguientes comandos bloque por bloque:

```bash
# 1. Habilitar IPv4 Forwarding
echo 'net.ipv4.ip_forward = 1' | sudo tee -a /etc/sysctl.d/99-tailscale.conf

# 2. Habilitar IPv6 Forwarding
echo 'net.ipv6.conf.all.forwarding = 1' | sudo tee -a /etc/sysctl.d/99-tailscale.conf

# 3. Aplicar cambios inmediatamente
sudo sysctl -p /etc/sysctl.d/99-tailscale.conf
```

---

## 📡 3. Inicialización del Servicio

Iniciamos Tailscale declarando explícitamente las capacidades que queremos activar.

```bash
sudo tailscale up --ssh --advertise-exit-node
```

### Parámetros utilizados:
| Flag | Descripción |
| :--- | :--- |
| `--ssh` | Habilita **Tailscale SSH**. Permite conectarte por SSH desde cualquier lugar sin abrir puertos en tu router y sin gestionar claves SSH manualmente. |
| `--advertise-exit-node` | Le dice a la red de Tailscale que este dispositivo *puede* ser usado como salida a internet, aunque requiere aprobación manual en el panel web. |

---

## 🛡️ 4. Aprobación en el Panel de Administración

Este paso es crítico y se realiza vía web. Accede a [login.tailscale.com/admin/machines](https://login.tailscale.com/admin/machines).

Busca tu Raspberry Pi en la lista, haz clic en el menú de tres puntos (**...**) y realiza estas dos acciones:

1.  **✔️ Disable Key Expiry (Desactivar expiración de clave):**
    * *Por qué:* Por defecto, Tailscale requiere re-autenticar cada 180 días. Esto asegura que tu Raspberry Pi (que suele estar desatendida) no pierda conexión nunca.

2.  **✔️ Edit Route Settings (Editar rutas) → Usar como Exit Node:**
    * *Por qué:* Aunque lo anunciamos en la terminal (paso 3), por seguridad debes aprobar manualmente la casilla **"Use as exit node"** en el panel web.
