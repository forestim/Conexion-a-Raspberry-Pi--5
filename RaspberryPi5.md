# Optimización Raspberry pi 5 (4GB)
- Raspberry pi OS 64 bits

La Raspberry Pi 5 es muy potente, pero el entorno de escritorio por defecto (basado en Wayland) y la configuración de fábrica no están pensados para la máxima eficiencia en acceso remoto.

Al usar Tailscale (VPN) y NoMachine (Escritorio Remoto), nuestro cuello de botella será el ancho de banda de subida y la latencia de codificación de video.

## Optimización de graficos

### Cambio de motor gráfico
Volveremos a utilizar el motor gráfico *X11*, esto debido a que el motor *Wayland* utiliza más recursos y tiene ciertos problemas de compatibilidad con NoMachine.

`sudo raspi-config `

`6 Advanced Options -> A7 Wayland -> W1 X11 `

Luego se debe reiniciar la raspberry
`sudo reboot`

Para verificar si esta bien configurado `echo $XDG_SESSION_TYPE` debe aparece `x11`

### Desactivar Compositor y Screen Blanking
Esto desactiva los efectos, animaciones y el apagado automatico de la pantalla.

`sudo raspi-config `

`2 Display Options -> D4 Composite -> Disable `

`2 Display Options -> D2 Screen Blanking -> Disable `

## Configuraciones de firmware + watchdog
Watchdog: viene siendo una herramienta *salvavidas* donde si el SO no reacciona debido a un pico elevado de voltaje o un congelamiento, este reinicia automaticamente el SO, ideal si tenemos la raspberry en un lugar inaccesible para un reinicio manual.

`sudo nano /boot/firmware/config.txt`

Añadir al final `dtparam=watchdog=on`
Guardar con `Ctrl+O)` y reinicia `sudo reboot`

### Reglas de inicio
`sudo nano /etc/watchdog.conf`

Descomentar o agregar estas lineas de codigo

Apunta al dispositivo de hardware

`watchdog-device = /dev/watchdog`

 Tiempo de espera antes de reiniciar (15s es el estándar de hardware)
 
`watchdog-timeout = 15`

Reiniciar si la carga de CPU es insostenible durante 1 min
 (24 es un valor seguro para una CPU de 4 núcleos como la Pi 5)
 
`max-load-1 = 24`

 Prioridad alta para que el propio Watchdog no sea eliminado por falta de RAM
 
`realtime = yes`
`priority = 1`
### Inicio del servicio
`sudo systemctl enable watchdog`

`sudo systemctl start watchdog`

`sudo systemctl status watchdog`
### Prueba de funcionamiento
Generar un congelamiento del sistema a proposito para verificar el reinicio automatico.
`:(){ :|:& };:`

## Monitores de conexión y rendimiento

### bmon (monitor de paquetes)
Instalacion `sudo apt install bmon -y`

ejecución `bmon` para salir se debe presionar `q`

### htop (monitor de rendimiento)
Viene instalado, se ejecuta mediante `htop` y se sale presionando `q`

## Creación de tunel vpn (TrailScale)
Instalación de los paquetes

`curl -fsSL https://tailscale.com/install.sh | sh`

Editar configuración de la red para TailScale

`echo 'net.ipv4.ip_forward = 1' | sudo tee -a /etc/sysctl.d/99-tailscale.conf`

`echo 'net.ipv6.conf.all.forwarding = 1' | sudo tee -a /etc/sysctl.d/99-tailscale.conf`

Aplicar los cambios

`sudo sysctl -p /etc/sysctl.d/99-tailscale.conf`

Iniciar TailScale: `sudo tailscale up --ssh --advertise-exit-node`
### Configuración IMPORTANTE TAILSCALE
Ahora se debe iniciar sesión en el sitio web ademas de configurar `Desactivar expiración de clave` + `Aprobar el Exit Node`.
* Expiración de clave: cada 180 días la PI se desconectará y se pierde la conexión remota.
* Exit Node: Se enruta todo el trafico de red mediante la raspberry, capa de seguridad para ocultar las direcciones IP.
Pasos a seguir
*1 https://www.google.com/search?q=admin.console.tailscale.com
  sdasda
