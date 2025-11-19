# Control Remoto Raspberri Pi 5 - Baja latencia y ancho de banda

Este repositorio busca ser una guía para la configuración de una raspberri Pi 5 en entornos de baja conectividad a la red de internet.

El diseño del sistema a configurar busca ser optimizado para redes donde la velocidad de internet es en promedio 3 MBPs de Dowload y de Upload. Por lo que tener una conexión estable, de baja latencia y con un entorno gráfico, es una tarea de bastante complejidad.

Dentro de esto ejemplificaremos las conexiones del sistema donde se haran las pruebas.

Alimentación --> Switch --> Converter POE --> Router 4g
             --> Switch --> Raspberry pi 5 

## Especificaciones y Configuraciones Físicas
### Router Teltonika RUT 200

Dentro de las principales limitaciones que nos encontramos con este tipo de router son:

* Módulo LTE Cat 4, lo cual limita su velocidad máxima de red móvil a ~150 Mbps de bajada y ~50 Mbps de subida.
* Wi-Fi en estándar 802.11 b/g/n (Wi-Fi 4), además sólo usa la banda 2.4 GHz.
* Puertos Ethernet de 10/100 Mbps, no gigabit
* 128 MB de RAM

### Antenas Teltonika PR1US440

* Ganancia de 4dbi
* Frecuencia Rango 698-960/1710-2690 MHz
* Polarización Lineal
* Conector SMA Male

### POE Converter PS5724AT-RJ (Splitter)
Este dispositivo se utiliza como conexión entre el Switch - Raspberry y Switch - Router, con el fin de solo alimentar con corriente directa al Switch.

* SN: 07255724AT4228
* Data Speed: 10/100 Mbps

*OBS*: No se encontro especificación del cable que contiene el convertidor POE, por lo cual puede generar un cuello de botella en la conexión a internet, además el manejo de potencia puede generar ruido, interferencias o ciertas pérdidas si el convertidor no esta bien diseñado o si el calbe es de mala calidad.

### Switch Mercusys MS105GP

* 5-Port Gigabit
* 4-Port POE+
* *Modo Extend*: Esto aumenta la distancia de transmisión POE pero limita la velocidad de los puertos a 10 Mbps. Se desactiva en el mismo switch en parte superior.
* *Modo Isolation*: Este modo aisla los puertos para que solo se comuniquen son el puerto 5, mejorando la seguridad y si se tienen multiples cámara conectadas, estas solo se comunicaran con el puerto 5. Para esta configuración es mejor desactivar esta opción.
*
### Raspberry pi 5 (4GB)

* Permite hasta ~1 Gb/s.
* Wi-Fi 802.11ac (Wi-Fi 5)

## Configuraciones de Dispositivo
Dejo 2 archivos con todas las configuraciones necesarias para *exprimir* la conexión siendo lo más fluida posible con un ancho de banda mínimo.

* Configuración Raspberry pi 5 (4Gb)
* Configuración acceso desde Windows 11
