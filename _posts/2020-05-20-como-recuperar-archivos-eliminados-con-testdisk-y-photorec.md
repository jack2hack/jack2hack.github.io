---
title: Como recuperar archivos eliminados con TestDisk & PhotoRec 
date: 2020-05-20 23:30:09
categories:
- Forensics
tags:
- forensics
---

Hola a todos. En este post voy a enseñaros a recuperar archivos con TestDisk y PhotoRec, pero antes quiero explicar un poco algo para que podamos entender cómo funciona el borrado de archivos en los sistemas informáticos de hoy en día; ¿Qué dirías si te digo que cuando eliminas un archivo este no es eliminado realmente?

¿Y si te digo que ese archivo sigue existiendo en tu ordernador o unidad de memoria? Pues así es. Cuando eliminamos un archivo este no es eliminado realmente y sigue existiendo en el mismo sector del disco donde estaba ubicado, lo que pasa realmente cuando eliminamos un archivo es que el sistema operativo deja de indexarlo y por lo tanto no podemos acceder a él pero el sector que ocupaba ese espacio sigue conteniendo los datos «eliminados» y no los sobreescribe hasta que sea necesario utilizar exactamente ese mismo sector.

Por lo tanto, sabiendo esto, nos podemos hacer una idea de si lo que queremos recuperar es posible o no dependiendo de las circustancias.

Para recuperar archivos eliminados tenemos un montón de programas, pero en este caso yo voy a usar TestDisk y PhotoRec porque son las herramientas que me han dado mejores resultados y en este articulo voy a trata de recuperar archivos eliminados de una unidad de memoria USB con 8 gigabytes que contiene un sistema de ficheros exFAT.
