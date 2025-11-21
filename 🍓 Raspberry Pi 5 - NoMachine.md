# 🍓 Raspberry Pi 5 (4GB) — Configuración de NoMachine
Guía para configurar un entorno remoto con interfaz gráfica usando **NoMachine** y el túnel creado con **TailScale**.

---

## 🖥️ Configuración del motor gráfico
Para reducir conflictos y uso de recursos, ajustaremos la Raspberry Pi 5.  
Esto también puede hacerse por **SSH** desde PowerShell:

```bash
ssh <Usuario>@<IP>
```

---

## 1️⃣ Cambios en la configuración
Abrir menú de configuración:

```bash
sudo raspi-config
```

Ruta a seleccionar:

```
6 Advanced Options → A7 Wayland → W1 X11
```

Reiniciar:

```bash
sudo reboot
```

Verificación:

```bash
echo $XDG_SESSION_TYPE
```

Debe mostrar:

```
x11
```

---

## 🔧 Desactivar compositor y screen blanking

```bash
sudo raspi-config
```

Luego:

```
2 Display Options → D4 Composite → Disable
2 Display Options → D2 Screen Blanking → Disable
```

---

## 2️⃣ Instalación de NoMachine

Descargar:

```bash
wget https://web9001.nomachine.com/download/9.2/Raspberry/nomachine_9.2.18_3_arm64.deb
```

Instalar:

```bash
sudo dpkg -i nomachine_9.2.18_3_arm64.deb
```

Configurar reglas de rendimiento:

```bash
sudo /etc/NX/nxserver --ruleadd --class node --type display --value "performance" --option "proxy-extra-options" --value "pack=16m-jpeg-9"
```

Iniciar servicio:

```bash
sudo /etc/NX/nxserver --startup
```

Reiniciar:

```bash
sudo reboot
```

---
