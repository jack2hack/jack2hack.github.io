---
title: Hacer un bypass en Windows
date: 2013-12-24 23:29:53
categories:
- Foo
tags:
- Foo
- Bar
- Baz
---

Hola a todos. En este post voy a enseñaros a cómo hacer un bypass en sistemas de Windows y extraer todas las contraseñas del fichero SAM.

El fichero SAM (Security Account Manager) es un archivo que se encarga de almacenar las credenciales de los usuarios del sistema en Windows.

Dentro de la SAM las contraseñas se almacenan en formato hash así que vamos a tener que crackear esos hashes para poder obtener las contraseñas.

Imaginemos que tenemos un ordenador que tiene un sistema de Windows instalado y que tiene una cuenta que está protegida con una contraseña. Nosotros no conocemos esa contraseña y lo que queremos no es solamente acceder al sistema, sino también conseguir la contraseña de esa cuenta.
