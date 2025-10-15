---
title: Agregando Privacidad a los Nodos en Linux
tags: [bitcoin, cli, nodo, node, macos]

---

# Agregando Privacidad a los Nodos en Linux
###### tags: `bitcoin` `Linux` `nodo` `Tor` `i2P`

Agregar privacidad a un nodo de Bitcoin implica principalmente anonimizar tanto el tráfico de red como la identidad del operador, dificultando el rastreo de la ubicación y las actividades del nodo. 
- Hay varias técnicas y configuraciones recomendadas, siendo el uso de redes Tor e I2P las más relevantes y efectivas actualmente, junto a buenas prácticas generales de seguridad y privacidad.
- El proceso estándar se realiza habitualmente en sistemas Linux, pero se puede adaptar para macOS y, con ciertas herramientas, también para Windows.


**Tabla de contenido**
[TOC]

## Autores
Jonny_Ji. 
Twitter para correcciones, comentarios o sugerencias: [@JonnyJi50127056](https://x.com/JonnyJi50127056)

El presente tutorial fue elaborado para el curso [Nodes from zero to hero](https://libreriadesatoshi.com/courses/nodes-from-zero-to-hero) a través de [@libreriadesatoshi](https://twitter.com/libdesatoshi).

## Cómo empezar
En el siguiente enlace puedes encontrar la documentación de referencia:
[El Proyecto Tor](https://www.torproject.org/)

[El Proyecto Internet Invisible (I2P)](https://geti2p.net/es/)

## La Privaciad en los Nodos

Agregar privacidad a un nodo implica principalmente anonimizar tanto el tráfico de red como la identidad del operador, dificultando el rastreo de la ubicación y las actividades del nodo.

Al momento de conectarse a internet, los nodos se conectan a través de la clearnet, es decir, vía TCP/IP pública, esto no es problema en la mayoría de los casos, pero si prefieres aumentar la privacidad de tu nodo, puedes configurar las conexiones para que se hagan a través de las redes de privacidad Tor e i2P. También puedes usar una VPN para que tu conexión IP siempre este oculta, pero eso solo te cubre frente a otros pares, no frente al proveedor de la VPN. 

**Mejores prácticas para privacidad de nodos Bitcoin**
- **Actualizar regularmente el software**: Mantener el nodo actualizado evita vulnerabilidades conocidas y mejora la protección frente a exploits recientes.
- **Usar firewall y cifrado**: Configurar firewalls estrictos y cifrado en conexiones RPC ayuda a evitar accesos y fugas no deseadas desde redes locales o externas.
- **Nunca utilices nodos de terceros si buscas máxima privacidad**: Ejecutar tu propio nodo es esencial para evitar filtraciones mediante "*espías*" que pueden ver transacciones asociadas a tus claves públicas.
- **No reutilices direcciones y limita la exposición a espacios públicos y espacios con KYC**: Son prácticas complementarias para la privacidad general de la actividad en bitcoin
- **Configurar el nodo para operar completamente sobre Tor**
- **Configurar Bitcoin Core para usar I2P**
  - i2P Se puede usar de forma combinada con Tor para mayor redundancia y resistencia a censura.

Agregar privacidad a tu nodo le da mayor protección ante vigilancia y censura, y refuerza la robustez general de la red. `Tor` es la solución más sencilla pero, para resiliencia, se recomienda explorar también `I2P` como complemento.

Es necesario ejecutar tu nodo sobre la red Tor, no sólo por la privacidad, sino porque de esa forma no tenemos que hacer ninguna redirección de puertos en el router que tengamos y tampoco usar servicios DNS dinámico para que se refresque nuestra IP en caso que no sea fija, que es el caso más típico en instalaciones de Internet domésticas.

## Instalar Tor como Servicio en Linux

Para instalar `Tor` como servicio en `Linux`, usamos la línea de cómandos en una terminal.
- Ejecutamos el siguiente comando para saber el nombre de la distribución linux que estamos usando:
```shell
lsb_release -c
```
- Crear el siguiente archivo:
```shell
sudo nano /etc/apt/sources.list.d/tor_repo.list
```
- Copia el siguiente contenido en el archivo:
```shell 
deb http://deb.torproject.org/torproject.org bullseye main 
deb-src http://deb.torproject.org/torproject.org bullseye main
```

- Edita donde dice "bullseye" por el nombre de la distribución que te haya aparecido a ti al ejecutar el comando `lsb_release -c` y presiona Ctrl+x , luego presiona ‘s’ y enter para guardar los cambios, y poder bajar los repositorios en donde se aloja el código fuente.
- Ejecuta el siguiente comando para bajar la llave GPG del repositorio para poderla instalar:
```shell
curl https://deb.torproject.org/torproject.org/A3C4F0F979CAA22CDBA8F512EE8CBC9E886DDD89.asc | sudo apt-key add -
```

- Ejecutamos este comando para actualizar los repositorios que acabamos de añadir:
```shell
sudo apt-get update
```

- Ahora instalamos Tor:
```shell
sudo apt-get install tor
```

- El siguiente paso es ir a la siguiente ruta para editar el archivo de configuración `torrc`
```shell
cd /etc/tor
sudo nano torrc
```

- Y añadimos las siguientes líneas:
```shell
SOCKSPort 9050
Log notice stdout
ControlPort 9051
CookieAuthentication 1 
CookieAuthFileGroupReadable 1 
```

Ahora vamos a añadir el usuario en donde está la instalación de bitcoin al grupo de Tor.

- Con este comando encontramos el nombre del Tor group:
```shell
grep User /usr/share/tor/tor-service-defaults-torrc
```

- En nuestro caso el resultado que aparece es `debian-tor`, en el siguiente comando deberá sustituir el nombre del grupo por el que le aparece a usted.
- También en el siguiente comando debe sustituir `tu_usuario` por su nombre de usuario y lo añade a este grupo:
```shell
sudo usermod -a -G debian-tor <tu_usuario>
```
Deslogueate y logueate de nuevo para que se apliquen las modificaciones o reinicia el pc.

- Ahora, vamos a configurar Tor para que arranque cada vez que inicie el pc:
```shell
sudo systemctl enable tor
```

- Y para lanzarlo en este momento ejecutamos
```shell
sudo systemctl start tor
```

Ahora la máquina está conectada sobre la red Tor.

## Instalar i2P como Servicio en Linux
Para instalar `i2P` como servicio en `Linux`, usamos la línea de cómandos en una terminal.

- Instalamos el repositorio
```shell
sudo add-apt-repository ppa:purplei2p/i2pd
```

- Posteriormente actualizamos para que lea el nuevo repositorio:
```shell
sudo apt update
```

- Instalamos el software i2P:
```shell
sudo apt install i2pd
```

- Una vez instalado, tenemos que habilitar el servicio i2pd, iniciarlo y revisar su estatus:
```shell
sudo systemctl enable i2pd.service
```
```shell
sudo systemctl start i2pd.service
```
```shell
sudo systemctl status i2pd.service
```

Ahora la máquina está conectada sobre la red i2P.

## Agregar la Configuración para que Bitcoin Core  se conecte a las redes de privacidad 

Ahora, en nuestro archivo de configuraciones de Bitcoin Core, llamado `bitcoin.conf`, vamos a incluir las siguientes líneas de configuración que es lo que nos va a permitir habilitar y conectar con la red Tor y la red i2P.
```shell
listen=1
debug=1
logips=1
listenonion=1
onlynet=onion
proxy=127.0.0.1:9050
debug=i2p
onlynet=i2p
i2psam=127.0.0.1:7656
i2pacceptincoming=1
```
- Guardas los cambios y cierras el archivo.
- Reinicias el nodo y listo, el nodo ya se conectará a través de la red Tor y la red i2p.

# Team "Librería de Satoshi"