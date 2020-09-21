---
layout: post
title:  "La interfaz gráfica no detecta bluetooth en Ubuntu"
date:   2020-09-19 9:00 +0200
categories: jekyll update
tags: linux wireless ubuntu
---

El otro día me encontré con que no podía encender ni apagar el bluetooth desde la interfaz gráfica de configuración del sistema en  Ubuntu y no detectaba ningún dispositivo. En vez de reiniciar el ordenador hasta que se arreglara solo (que probablemente habría funcionado), decidí investigar cómo arreglar esto desde terminal.

## ¿Cómo funciona bluetooth en Ubuntu?

Bluetooth es un protocolo de arquitectura de capa[1] que se utiliza para comunicación en redes de corto rango (hasta 100 metros con repetidores) y utiliza un enlace por radiofrecuencia en la banda ISM de 2.4 GHz. 

La pila de protocolos de Bluetooth en Linux, que se llama [BlueZ](http://www.bluez.org), está dividida entre el kernel de Linux y el _userspace_. 

En el kernel encontramos los protocolos de bajo nivel (L2CAP, RFCOMM, BNEP, HIDP, etc.), los de seguridad (SSP, SMP), los _drivers_ para el _hardware_ y interfaces para el _userspace_ basadas en _sockets_ (HCI, SCO, MGMT, BNEP, HIDP).

El _userspace_ lo forman `bluetoothd` y `obexd`. El primero es el _daemon_ central; maneja los dispositivos bluetooth y su configuración se hace en `/etc/buetooth/main.conf`, provee una interfaz de D-Bus[2] y reduce la exposición a detallse de bajo nivel. `obexd` es un _daemon_ para perfiles OBEX[3]; provee también una interfaz de D-Bus y tiene una arquitectura similar a la de `bluetoothd`.


### `bluetoothctl`

`bluetoothctl` es el comando principal para configurar dispositivos bluetooth en Linux, forma parte del paquete `bluez-utilities`. Permite escanear dispositivos cercanos, _pairing_, declarar algunos como _trust_, bloquear, conectar...

Para utilizarlo, primero tendremos que entrar en su propio prompt, con `bluetoothctl` y luego podremos utilizar las opciones. 


## Activar el bluetooth desde terminal

Primero, comprobaremos que el _daemon_ del bluetooth esté corriendo:

`$ sudo /etc/init.d/bluetooth status -l`


Si está corriendo vamos bien y sino lo levantamos:

`$ sudo systemctl start bluetooth`

Una vez tengamos el servicio corriendo, podemos utilizar bluetoothctl para escanear los dispositivos disponibles y conectarnos a ellos, o hacer el mismo proceso desde la interfaz gráfica en la configuración de bluetooth.

```
$ sudo bluetoothctl
		[bluetooth]# default-agent
		[bluetooth]# scan on
```
En el caso de que `scan on` nos dé el error `No default controller available`, abrimos una nueva pestaña en la terminal con ctrl+shift+T y comprobamos con `rfkill` qué dispositivos _wireless_ tenemos funcionando:

`$ rfkill list all`

En mi caso, indicaba que 0: tpacpi_bluetooth_sw estaba "soft blocked", así que lo he desbloqueado con:

`$ rfkill unblock 0`

En este punto ya se me ha conectado desde la interfaz gráfica, pero si quisiéramos conectarnos desde terminal tendríamos que hacerlo desde bluetoothctl:

```
[bluetooth]# pair <número:identificador:del:dispositivo>
bluetooth]# trust <número:identificador:del:dispositivo> #sólo si queremos que sea de confianza
$ sudo rfcomm connect hci <número:identificador:del:dispositivo>
```




[1] Protocolo formado por una pila de protocolos que resuelven tareas menores. Por ejemplo, TCP/IP.

[2] "D-Bus es un sistema de comunicación entre procesos (IPC) y una llamada a procedimiento remoto (RPC), para aplicaciones de software con el fin de comunicarse entre sí". [_D-Bus_, Wikipedia](https://es.wikipedia.org/wiki/D-Bus) 

[3] "Protocolo de comunicaciones que facilita el intercambio de objetos binarios entre dispositivos." [_Obex_, Wikipedia](https://es.wikipedia.org/wiki/Obex)





