---
title: Crackeando los hashes del archivo /etc/shadow en Linux 
date: 2020-03-15 23:30:04
categories:
- Linux
tags:
- Linux
- Cracking
---

Hola a todos. En este post os voy a demostrar cómo se pueden crackear los hashes del archivo /etc/shadow en Linux. El fichero /etc/shadow es un archivo que almacena las contraseñas cifradas de las cuentas de usuario en Linux, y que también guarda otra informácion muy útil cómo la última modificación de la contraseña, la fecha de caducidad, etc. Cada usuario del sistema tiene una entrada en el archivo shadow y para poder leerlo necesitamos permisos de root.

Para visualizar el contenido por pantalla abrimos una terminal e introducimos el siguiente comando: 

```sh
sudo cat /etc/shadow
```

![Resultado del comando sudo cat /etc/shadow](https://i.ibb.co/Rvnhtq1/kali1.png)

Como puede verse en la imagen de arriba, el contenido del archivo /etc/shadow tiene varias entradas registradas y cada una de ellas tiene nueve campos que se representan como se muestra en la siguiente imagen a continuación:

![Explicación del archivo shadow en Linux](https://i.ibb.co/VJY6jdq/shadow.png)

Este archivo almacena la contraseña en formato cifrado y lo que hay entre los signos del dólar indica el tipo de cifrado que en el caso de esta imagen es **«$6$»** lo que quiere decir que el tipo de cifrado utilizado aquí es **«SHA-512»**.

Este es un resumen de los cifrados más conocidos en el archivo shadow:

**`$1$=MD5`
`$2$=Blowfish`
`$5$=SHA-256`
`$6$=SHA-512`**

Ya que sabemos un poquito más acerca del archivo shadow y de los tipos de cifrado que utiliza este fichero vamos a pasar a la práctica y vamos a crackear este mismo fichero de mi propio sistema Kali Linux instalado en mi virtualbox.

Si no tuvieramos privilegios de root tendríamos que buscar la forma de escalar privilegios en Linux, pero como en este caso se trata de nuestro propio sistema no será necesario porque tenemos permisos y control total del sistema.

Bueno, lo primero que vamos a hacer va a ser copiar el contenido del archivo /etc/shadow/ en otro fichero para proceder a crackear los hashes utilizando **John the Ripper** así que abrimos una terminal y escribimos lo siguiente:

```sh
sudo cp /etc/shadow shadow
```

Y ahora vamos a visualizar el contenido del archivo shadow para averiguar cual es el tipo de algortimo de cifrado que se ha utilizado para cifrar la contraseña de la cuenta que queremos crackear, en mi caso la cuenta se llama **"kali"**:

```sh
sudo cat shadow
```

![Resultado del comando sudo cat /etc/shadow](https://i.ibb.co/C1bfbdP/kali2.png)

Como podemos ver en la imagen de arriba, el cifrado es **«$y$»** lo que por una simple búsqueda en google podemos descubrir que se trata de **«yescrypt»**, y que también se trata del nuevo cifrado por defecto utilizado en GNU/Linux.

Sabiendo cual es el tipo de cifrado, ahora ya podemos proceder a crackearlo con John the Ripper ejecutando:


```sh
john –format=crypt shadow
```

Como hemos indicado el tipo de cifrado pero no hemos indicado ningún diccionario, se utilizará password.lst que es el diccionario que viene por defecto en John the Ripper y que suele funcionar muy bien con contraseñas sencillas.

![Resultado del comando john –format=crypt shadow](https://i.ibb.co/NT693tY/kali3.png)

Y como puede verse en la imagen la contraseña ha sido encontrada utilizado el diccionario por defecto porque es una contraseña relativamente sencilla. Para visualizar la contraseña de nuevo utilizaremos el siguiente comando:


```sh
john –show –format=crypt shadow
```

![Resultado del comando john –show –format=crypt shadow](https://i.ibb.co/CQCMn18/kali4.png)

Si no tuvieramos éxito con el diccionario por defecto podemos utilizar otros diccionarios como por ejemplo «rockyou» con el argumento «wordlist=path». Con «path» me refiero a la ruta donde tenemos guardado el diccionario que queremos utilizar pero para una mejor explicación aquí va un ejemplo en la terminal de comandos:

```sh
john –format=crypt –wordlist=home/kali/Desktop/rockyou.txt
```

Y pues ya está. Con esto ya hemos terminado. Así de fácil que es crackear las contraseñas del archivo /etc/shadow. Recordad que lo primero que tenemos que hacer es identificar el tipo de algortimo de cifrado que se ha utilizado para cifrar la contraseña y después indicarselo a John the Ripper con el argumento «–format=», y tened en cuenta que tendremos que utilizar uno o varios diccionarios dependiendo de la complejidad de la contraseña.
