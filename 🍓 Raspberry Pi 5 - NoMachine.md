# 🍓 Raspberry Pi 5 — Configuración de NoMachine

![Raspberry Pi](https://img.shields.io/badge/Hardware-Raspberry%20Pi%205-C51A4A?logo=raspberrypi&logoColor=white)
![NoMachine](https://img.shields.io/badge/Remote-NoMachine-orange)
![X11](https://img.shields.io/badge/Display-X11-lightgrey)

> **Resumen:** Guía para configurar un entorno de escritorio remoto de alto rendimiento con interfaz gráfica usando **NoMachine** sobre el túnel **Tailscale**.

---

## 📋 Introducción

Para garantizar la compatibilidad y el rendimiento en la Raspberry Pi 5 (Bookworm), es necesario migrar del compositor **Wayland** al sistema **X11**, ya que NoMachine funciona de manera nativa y más estable sobre este último.

```mermaid
graph LR
    A[Cliente Remoto] -->|Tailscale VPN| B(Raspberry Pi 5)
    B -->|X11 Display| C[Entorno de Escritorio]
    style B fill:#C51A4A,stroke:#333,stroke-width:2px,color:white
```

---

## ⚙️ 1. Configuración del Sistema (X11)

Podemos realizar estos ajustes conectándonos vía SSH (Powershell/Terminal):
`ssh <Usuario>@<IP_Tailscale>`

Ejecutamos la herramienta de configuración:

```bash
sudo raspi-config
```

Navega a través de los menús y realiza los siguientes cambios:

| Categoría | Ruta de Menú | Acción/Selección |
| :--- | :--- | :--- |
| **Sistema de Ventanas** | `6 Advanced Options` → `A6 Wayland` | Seleccionar **W1 X11** |
| **Compositor** | `2 Display Options` → `D4 Composite` | Seleccionar **No/Disable** |
| **Suspensión** | `2 Display Options` → `D2 Screen Blanking` | Seleccionar **No/Disable** |

> **Nota:** Al finalizar, el sistema pedirá reiniciar. Acepta o ejecuta `sudo reboot` manualmente.

### Verificación
Una vez reiniciado, confirma que estás usando X11:

```bash
echo $XDG_SESSION_TYPE
# Salida esperada: x11
```

---

## 📥 2. Instalación de NoMachine

Descargamos e instalamos el paquete `.deb` para arquitectura ARM64.

```bash
# 1. Descargar paquete
wget [https://web9001.nomachine.com/download/9.2/Raspberry/nomachine_9.2.18_3_arm64.deb](https://web9001.nomachine.com/download/9.2/Raspberry/nomachine_9.2.18_3_arm64.deb)

# 2. Instalar
sudo dpkg -i nomachine_9.2.18_3_arm64.deb
```

---

## 🚀 3. Optimización de Rendimiento

Para mejorar la fluidez a través del túnel VPN, aplicaremos reglas específicas al servidor NX (compresión JPEG y modo rendimiento).

```bash
sudo /etc/NX/nxserver --ruleadd --class node --type display --value "performance" --option "proxy-extra-options" --value "pack=16m-jpeg-9"
```

Finalmente, aseguramos el inicio del servicio y reiniciamos para aplicar todo:

```bash
sudo /etc/NX/nxserver --startup
sudo reboot
```
