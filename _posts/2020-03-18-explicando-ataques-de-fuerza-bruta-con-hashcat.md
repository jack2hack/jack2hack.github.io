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

De esta manera se probarían todas las combinaciones posibles con una longitud de 8 caracteres que se compongan de letras minúsculas y mayúsculas a-z/A-Z, y para que el ataque fuera efectivo la contraseña que queremos crackear debe tener exactamente 8 caracteres, ya que si tuviera mas o menos caracteres la combinación nunca sería generada.

En mi caso, para crackear una hash WPA/WPA2 de 8 caracteres que se componga únicamente de números del 0 al 9 habría 50 milllones de posibilidades y utilizando mi GPU (GTX 1050) el tiempo estimado es de 24 horas de trabajo.

Hascat tiene una opción activada por defecto que guarda el progreso y apaga la GPU cuando detecte que sus niveles de calor este llegando a su límite (para evitar un apagado repentino por pantalla azul) así que podemos trabajar con seguridad.

Y tras 24 horas el hash será crackeado 100% y obtendremos la contraseña ya que se probarán todas las combinaciones posibles y si el charset indicado es correcto no hay posibilidad de fallo.

Claramente mi red wifi no tiene 8 caracteres compuestos por digitos pero cambié la clave para realizar esta prueba. Mi clave se compone de compone de minúsculas, mayúsculas, digitos, símbolos y un número secreto de caracteres :B

Pero la clave por defecto de mi router es de 8 caracteres y se compone de letras minúsculas y mayúsculas así que probé a crackearlo con mi GTX 1080 indicando el siguiente charset:

hashcat hjfhdakfjafhnjks

Y como resultado se obtienen... ¡53 trillones de posibilidades y un tiempo estimado de 18 años! Una locura y obviamente no es un tiempo estimado comprensible así que lo descartamos.

Probamos a bajar un poco el nivel y le ponemos 8 caracteres con minúscualas (quitando las mayúsculas):

hashcat ajfhskjfhasfkshkf

Resultado: 50 billones de psibilidades y un tiempo estimado de 27 días. El tiempo estimado sigue siendo mucho pero hay que tener en cuenta que estamos probando con 1 GPU GTX 1050 así que sumandole potencia nuestra máquina podríamos reducir el tiempo estimado considerablemente.

Llegados a este punto me pregunté de lo que sería capaz una máquina lo suficientemente potente y encontré un benchmark en github probando 8 gráficas GTX 1080 con Hascat.

El benchmark probando 8 GTX 1080 se puede ver aquí: 

https://gist.github.com/epixoip/a83d38f412b4737e99bbef804a270c40

Una sola tarjeta Nvidia GTX 1080 prueba 400,000 claves WPA por segundo. Lo que nos da cerca de 3,200,000 claves por segundo si usamos 8 a la vez.

Mi GTX 1050 prueba 300,000 claves por segundo así que utilizando 8 GTX 1080 podemos reducir los 27 días del charset de 8 caracteres con a-z.

Después me puse a buscar y encontré un benchmark probando 8 RTX 2060:

Y también encontré un benchmark probando 8 RTX 3090:

El procesador utilizado en cada benchmark es un Intel Xion utilizado por servidores y necesario para poder aprovechar la potencia de las 8 GPUs a la vez.

Si queremos buscar lo mas TOP de los benchmarks con Hascat podemos buscar los que utilizan las tarjetas graficas Titan valoradas entre 4000-5000 euros:

https://hashcat.net/forum/thread-4314.html

Una tarjeta RTX Titan 
