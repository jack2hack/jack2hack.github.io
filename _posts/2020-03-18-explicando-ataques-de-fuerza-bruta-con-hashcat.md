---
title: Explicando ataques de fuerza bruta con Hashcat
date: 2020-03-15 23:30:04
categories:
- Criptografía
tags:
- Hashcat
- Cracking
---

Hola a todos. En este post vamos a explicar en qué consisten los ataques de fuerza bruta y vamos a realizar un ejemplo práctico con Hashcat. Un ataque de fuerza bruta consiste en probar todas las combinaciones posibles para descifrar una contraseña, nombre de usuario o lo que sea. Obviamente un ataque de fuerza bruta no se puede realizar sin indicar un charset. Un charset es un set de caracteres que compone una palabra, username o contraseña.


Un charset puede componerse de minúsculas, mayúsculas, digitos y símbolos, y a la hora de realizar un ataque de fuerza bruta hay que indicar el charset. Si sabemos que el hash (username, contraseña, etc) que vamos a crackear contiene 8 caracteres, minúsculas, y mayúsculas, lo indicaremos de tal forma ~~y si no sabemos nada estamos jodidos.~~

Existen ciertas limitaciones a la hora de realizar un ataque de fuerza bruta y hay que tener en cuenta varios factores como el charset, el tipo de hash a crackear, el modelo/número de GPUs y las capacidades de nuestros procesadores.

Normalmente los que se dedican al cracking de hashes suelen tener varios Pc´s en cluster y utilizan procesadores especiales para servidores como el Intel Xeon para utilizar varias GPU's a la vez y aumentar la potencia de cómputo.

Por lo tanto los ataques de fuerza bruta con Pc's tradicionales de 4 y 8 núcleos o tarjetas gráficas poco potentes, son poco viables aunque no son imposibles con ciertos tipos de charsets, y para una mejor explicación voy a hacer una demostración con Hascat utilizando la GPU de mi portátil (GTX 1050) y mi procesador (Intel Core i7-7700HQ).

---------------------------------------------------------------------------------------------------------------------------------------------

Para esta demo he capturado el handshake de mi red wifi y por lo tanto el tipo de hash a crackear es WPA/WPA2.

Sabemos que la contraseña tiene 8 caracteres y que se compone de letras minúsculas y mayúsculas así que utilizaremos un ataque de fuerza bruta para probar todas las combinaciones posibles indicando un custom charset:

```sh
hashcat -d 1 -m 22000 test.hc22000 -a 3 --custom-charset1 ?l?u ?1?1?1?1?1?1?1?1
```

De esta manera se probarían todas las combinaciones posibles con una longitud de 8 caracteres que se compongan de letras minúsculas y mayúsculas, y para que el ataque fuera efectivo el hash/contraseña que queremos crackear debe tener exactamente 8 caracteres, ya que si tuviera mas o menos caracteres la combinación nunca sería generada porque nuestro custom charset prueba únicamente todas las combinaciones posibles entre 8 caracteres.

Y como resultado he obtenido... ¡¡¡53 trillones de posibilidades y un tiempo estimado de 18 años!!! ¡¡¡Una locura!!! Obviamente no es un tiempo estimado comprensible así que descartamos la posibilidad de crackear este charset.

Probamos a bajar un poco el nivel y le ponemos un charset de 8 caracteres con se componga de letras minúsculas:

```sh
hashcat -d 1 -m 22000 test.hc22000 -a 3 --custom-charset1 ?l ?1?1?1?1?1?1?1?1
```

Resultado: 50 billones de posibilidades y un tiempo estimado de 27 días. El tiempo estimado sigue siendo mucho pero hay que tener en cuenta que estamos probando con 1 GPU GTX 1050 así que sumandole potencia a nuestra máquina añadiendo varias GPU´s de mayor categoría podríamos reducir el tiempo estimado considerablemente.

Por último vamos a bajar aún más el nivel metiendo un charset de 8 caracteres que se componga solo de digitos:

```sh
hashcat -d 1 -m 22000 test.hc22000 -a 3 -d ?d?d?d?d?d?d?d?d
```

En mi caso, para crackear un hash WPA/WPA2 de 8 caracteres que se componga únicamente de números del 0 al 9 habría 50 milllones de posibilidades y utilizando mi GPU (GTX 1050) el tiempo estimado es de 24 horas de trabajo.

Hascat tiene una función que guarda el progreso y apaga la GPU cuando detecte que sus niveles de calor este llegando a su límite (para evitar un apagado repentino por pantalla azul) así que podemos trabajar con seguridad realizando crackeos que duren varias horas e incluso días sin miedo a perder el progreso o dañar nuestro hardware.

De este modo y tras 24 horas el hash sería crackeado 100% y obtendríamos la contraseña ya que se probarían todas las combinaciones posibles y si el charset indicado es correcto no hay posibilidad de fallo.

Llegados a este punto me pregunté de lo que sería capaz una máquina lo suficientemente potente y encontré un benchmark en github probando 8 gráficas GTX 1080 a la vez con un procesador Intel Xeon E5-2697 v3 en Hascat:

[https://gist.github.com/epixoip/a83d38f412b4737e99bbef804a270c40](https://gist.github.com/epixoip/a83d38f412b4737e99bbef804a270c40)

Si nos fijamos en la velocidad del crack WPA esto es lo que obtenemos con 8 tarjetas GTX 1080:

![Crackeando 3200000 llaves por segundo](https://i.postimg.cc/nhzq3wXF/3200000-llaves-segundos-1.jpg)

**¡Una sola tarjeta Nvidia GTX 1080 prueba 400,000 claves WPA por segundo!** Lo que nos da cerca de 3,200,000 claves por segundo si usamos 8 a la vez. Lo que significa 10.800.000.000 claves por hora así que ya sabemos como podríamos crackear un charset de 8 caracteres con letras minúsculas o mayúsculas en aproximadamente 5 horas.

3,200,000 claves por segundo son 259.200.000.000 por cada 24 horas y por lo tanto todavía estaríamos muy lejos del objetivo principal para crackear un charset de 8 caracteres compuesto de letras mayúsculas y minúsculas (53 trillones) ya que conseguiríamos 1 trillón en unos 4 días y por lo tanto necesitaríamos 200 días para los 50 trillones.

Después me puse a buscar más información sobre este tema y encontré (gracias a hive systems) una referencia de tiempo de cracking, en la actualidad, con el último hardware disponible, tanto de CPU como de GPU en el mercado:

![Time-to-Crack-Password](https://i.postimg.cc/g2bS6LkB/Time-to-Crack-Password.png)

También encontré varios servicios en internet como por ejemplo [GPUHASH.me](https://gpuhash.me/) que se dedican a crackear hashes:

![GPUHash.me](https://4.bp.blogspot.com/-7K3kRkCuFw4/XLXaztyImlI/AAAAAAAAI78/UsCROdc7IMsuUQ0ocB4Ci70BdmXtW3CxwCLcBGAs/s640/1.png)

Este servicio es uno de los más completos que he encontrado, ya que permite crackear una gran cantidad de hashes muy diferentes (wireless, windows, wallets, office, compresión, etc). Su funcionamiento es bastante básico, y de forma sencilla podremos cargar nuestro hash, o lista de hashes, para crackear, indicando el tipo de algoritmo utilizado para su generación y seleccionando la técnica que queremos que utilicen para proceder al crackeo que varía entre muchas opciones, desde la fuerza bruta clásica, al uso de diccionarios o incluso combinaciones de ellos.
