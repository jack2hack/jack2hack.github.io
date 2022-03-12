---
title: Crackeando los hashes del archivo /etc/shadow en Linux 
date: 2020-03-15 23:30:04
categories:
- Linux
tags:
- Linux
- Cracking
---

Hola a todos. En este post os voy a demostrar cómo se pueden crackear los hashes del archivo /etc/shadow en Linux. El fichero /etc/shadow es un archivo que almacena las contraseñas cifradas de las cuentas de usuario en Linux, y que también guarda otra informácion útil cómo la última modificación de la contraseña, la fecha de caducidad, etc. Cada usuario del sistema tendrá una entrada en el archivo shadow y para poder leerlo necesitaremos permisos de root.

Para visualizar el contenido por pantalla abrimos una terminal e introducimos el siguiente comando: 

```sh
sudo cat /etc/shadow
```
