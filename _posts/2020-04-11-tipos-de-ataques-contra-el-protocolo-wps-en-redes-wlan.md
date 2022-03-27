---
title: Tipos de ataques contra el protocolo WPS en redes WLAN
date: 2020-04-11 23:30:04
categories:
- Wireless
tags:
- Wireless
- Cracking
---

Hola a todos. En este vamos a ver los tipos de ataques que existen contra el procolo WPS y vamos a realizar un par de ejemplos prácticos. WPS (Wireless Protection Setup) es un sistema que tiene por funcionalidad básica la de ofrecer una manera controlada de conectarse a una red Wi-Fi escribiendo sólo un PIN de 8 dígitos en lugar de la contraseña de red. Al usar el botón WPS, el sistema puede funcionar de distintas maneras, si bien de entre las cuatro más comunes, la más extendida es la que se basa en el intercambio de PIN. El dispositivo debe transmitir un PIN de 8 digitos al router y a cambio este último le envía los datos para acceder a la red.

Hola a todos. En este post vamos a explicar en qué consisten los ataques de fuerza bruta y vamos a realizar un ejemplo práctico con Hashcat. Un ataque de fuerza bruta consiste en probar todas las combinaciones posibles para descifrar una contraseña, nombre de usuario o lo que sea. Obviamente un ataque de fuerza bruta no se puede realizar sin indicar un charset. Un charset es un set de caracteres que compone una palabra, username o contraseña.


**Un charset puede componerse de minúsculas, mayúsculas, digitos y símbolos**, y a la hora de realizar un ataque de fuerza bruta hay que indicar el charset. Si sabemos que el hash (username, contraseña, etc) que vamos a crackear contiene 8 caracteres, minúsculas, y mayúsculas, lo indicaremos de tal forma ~~y si no sabemos nada estamos jodidos.~~

Existen ciertas limitaciones a la hora de realizar un ataque de fuerza bruta y hay que tener en cuenta varios factores como el **charset**, el **tipo de hash** a crackear, el modelo/número de **GPUs** y las capacidades de nuestros **procesadores**.

Normalmente los que se dedican al cracking de hashes suelen tener **varios Pc´s en cluster** y utilizan **procesadores para servidores** como el **Intel Xeon** para poder utilizar **varias GPU's a la vez** y aumentar así la potencia de cómputo.

Por lo tanto los ataques de fuerza bruta con Pc's tradicionales de 4 y 8 núcleos o tarjetas gráficas poco potentes, son poco viables aunque no son imposibles con ciertos tipos de charsets, y para una mejor explicación voy a hacer una demostración aquí con Hascat utilizando la GPU de mi portátil **(GTX 1050)** y mi procesador **(Intel Core i7-7700HQ)**.

---------------------------------------------------------------------------------------------------------------------------------------------
