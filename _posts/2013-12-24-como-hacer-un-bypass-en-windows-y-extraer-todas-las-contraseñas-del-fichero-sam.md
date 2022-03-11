---
title: Como hacer un bypass en Windows y extraer todas las contraseñas del fichero SAM
date: 2013-12-24 23:29:53
categories:
- tutorial
tags:
- Windows
- Bypass
- Cracking
---

Hola a todos. En este post voy a enseñaros a cómo hacer un bypass en sistemas de Windows y extraer todas las contraseñas del fichero SAM. El fichero SAM (Security Account Manager) es un archivo que se encarga de almacenar las credenciales de los usuarios del sistema en Windows. Dentro de la SAM las contraseñas se almacenan en formato hash con LM/NTLM así que vamos a tener que crackear esos hashes para poder obtener las contraseñas.

Imaginemos que tenemos un ordenador que tiene un sistema de Windows instalado y que tiene una cuenta que está protegida con una contraseña. Nosotros no conocemos esa contraseña y lo que queremos no es solamente acceder al sistema, sino también conseguir la contraseña de esa cuenta y de cualquier otra cuenta existente en ese sistema.

Para esto primero vamos a hacer un bypass para ganar acceso al sistema, después vamos a extraer los hashes del fichero SAM usando PwDump v8.2 y finalmente vamos a proceder a crackear los hashes utilizando John the Ripper.

Para hacer el bypass primero hay que preparar un CD/USB de instalación con Windows: [https://www.microsoft.com/es-es/software-download/windows10](https://www.microsoft.com/es-es/software-download/windows10)

Después vamos a bootear el CD o USB, esperamos a que los archivos carguen y cuando aparezca la pantalla para elegir el idioma, formato de hora y moneda, y teclado o método de entrada hacemos click en el botón de «Siguiente»:

![Programa de instalación de Windows](https://i.ibb.co/S67h828/windows-1.png)

En la siguiente pantalla hacemos clic en «Reparar el equipo»:

![Reparar el equipo](https://i.ibb.co/JkTRdpy/windows-2.png)

Y ahora seleccionamos «Solucionar problemas», y en la siguiente pantalla hacemos click en «Restablecer tu PC»:

![Solucionar problemas > Restablecer tu PC](https://i.ibb.co/3zbh6HC/windows-3.png)

Seleccionamos «Símbolo del sistema» y se abrirá una consola de comandos:

![Opciones avanzadas > Símbolo del sistema](https://i.ibb.co/frJgfdr/windows-4.png)

Introducimos los siguientes comandos:

```sh
C:\
cd windows\system32
ren utilman.exe utilman.exe.bak
copy cmd.exe utilman.exe
```

Con estos comandos lo que hacemos es ir al directorio Windows\System32, renombrar el archivo utilman.exe a utilman.exe.bak para hacer una copia de seguridad, creamos una copia del archivo cmd.exe y la renombramos como utilman.exe para que así cuando llamemos a utilman.exe se nos abra cmd.exe en su lugar. Utilman.exe es un fichero que tiene Windows para sacar el teclado en pantalla, la lupa y alguna otra cosa, y que podemos ejecutar desde la página de inicio de sesión en Windows así que cambiando su nombre vamos a engañar al sistema para que cuando llamemos a ultiman.exe se abra cmd.exe lo que nos permitirá ejecutar varias acciones desde la consola de comandos.

Una vez hecho esto cerramos la consola, apagamos el ordenador y quitamos el CD o USB para que Windows arranque normalmente, y cuando nos encontremos en la pantalla del login presionamos «Tecla de Windows + U» o simplemente hacemos click sobre el icono de accesibilidad y a continuación se nos abrirá una consola de comandos.

En este punto ya podriamos resetear la contraseña de cualquier cuenta y acceder con la nueva contraseña, pero lo que queremos es obtener la contraseña y no cambiarla, así que lo que vamos a hacer es habilitar la cuenta de administrador para acceder al sistema y después vamos a extraer los hashes del fichero SAM para crackearlos.

Para activar la cuenta de administrador introducimos el siguiente comando:

```sh
net user administrator /active:yes
```

Una vez activada la cuenta de administrador reiniciamos el ordenador y accedemos a la cuenta sin contraseña.

Ya estamos dentro del sistema y además con permisos de administrador así que ya hemos realizado el bypass saltándonos el login de Windows, y ahora vamos a proceder a extraer los hashes del fichero SAM con PwDump.

PwDump es un programa de Openwall que sirve para extraer los hashes de las contraseñas del fichero SAM.

Vamos a descargar `PwDump (Versión v8.82)` desde la página de openwall.net:

![https://download.openwall.net/pub/projects/john/contrib/pwdump/pwdump8-8.2.zip](https://download.openwall.net/pub/projects/john/contrib/pwdump/pwdump8-8.2.zip)

NOTA IMPORTANTE: PwDump es un programa que suele ser detectado cómo malware, así que antes de descargarlo tienes que desactivar la protección de antivirus o en su defecto añadir el programa a lista de amenazas permitidas.

Para ejecutar PwDump no basta con ejecutar el archivo.exe normalmente, hay que ejecutarlo desde la consola de comandos con permisos de administrador así que para hacer esto primero ejecutamos el CMD como administrador, y después nos ubicamos en el directorio donde hemos extraido el PwDump.

Una vez ubicados en la ruta del PwDump introducimos el siguiente comando:

```sh
pwdump8.exe
```

![PwDump](https://i.ibb.co/gt7Yc9Q/pwdump-1.png)

A continuación se mostrará en pantalla un listado con todos los hashes de contraseñas extraidos y ya podríamos proceder a guardarlos, pero antes quiero explicar cómo se compone el formato que nos muestra PwDump:

Nombre de usuario : ID de usuario : Hash de la contraseña en LM : Hash de la contraseña en NTLM

El hash que guarda la contraseña es el hash de la contraseña en NTLM, así que ese es el hash que necesitamos para intentar crackear la contraseña.

Como se puede ver en la imagen los usuarios «Administrador», «Invitado» y «DefaultAccount» tienen exactamente el mismo hash de contraseña en NTLM: «31D6CFE0D16AE931B73C59D7E0C089C0». Este hash es el resultado de una contraseña vacía, así que si crackeamos este hash no obtendremos nada.

En este caso la cuenta de la que queremos extraer la contraseña se llama «PH300» y como se puede ver en la imagen tiene un hash NTLM diferente.

Lo que tenemos que hacer ahora es volcar todos los hashes en un fichero.

Podemos copiar el resultado que nos muestra PwDump en un fichero de texto o introducir este comando para automatizar el proceso desde CMD:

```sh
pwdump8.exe > pwdump.txt
```

Una vez hecho esto ya hemos extraido todos los hashes del fichero SAM, así que ya lo único que nos queda es crackearlos para obtener las contraseñas, pero como he dicho tenemos hashes de cuentas por defecto con contraseñas vacías así que es recomendable modificar el archivo de texto pwdump.txt y eliminar los hashes que sabemos que nos van a devolver resultados nulos.

En mi caso, yo he modificado el archivo pwdump.txt de la siguiente manera:

![PwDump.txt sin modificar](https://i.ibb.co/wSYnpr1/notepad-1.png)

![PwDump.txt modificado](https://i.ibb.co/zVRj2B8/notepad-2.png)

Para crackear los hashes utilizaremos un programa llamado John the Ripper:

![John The Ripper](https://i.ibb.co/n3g4sZh/john-the-ripper.png)

John the Ripper es un programa de criptografía que aplica fuerza bruta para descifrar contraseñas. Es capaz de romper varios algoritmos de cifrado o hash, y es una herramienta muy popular disponible para sistemas Windows y Linux.

Como todos los pasos de este articulo se están haciendo desde Windows, en esta ocasión voy a usar la versión de John the Ripper para Windows aunque para este tipo de cosas yo prefiero Linux porque que me siento más cómodo.

Bueno, ahora vamos a descargar la versión de John the Ripper para Windows:

![https://www.openwall.com/john/k/john-1.9.0-jumbo-1-win64.zip](https://www.openwall.com/john/k/john-1.9.0-jumbo-1-win64.zip)

Después extraemos los ficheros y ejecutamos el CMD como administrador.

Nos ubicamos en el directorio donde hemos extraido los archivos y entramos en la carpeta «john-1.9.0-jumbo-1-win64», después nos metrmos en «run» y luego introducimos el siguiente comando para ejecutar John the Ripper:

```sh
john.exe
```

A continuación se mostrará por pantalla la versión del programa, un ejemplo de uso y una lista de argumentos que podemos ejecutar con John the Ripper:

![John the Ripper-2](https://i.ibb.co/RBL6505/john-the-ripper-2.png)

Para realizar un ataque correctamente hay que indicar el tipo de hash y el diccionario que queremos usar. Si no indicamos el tipo de hash, el programa intentará identificar el tipo de hash por si mismo y si no indicamos ningún diccionario se usará password.lst que es el diccionario que viene por defecto.

Yo voy a indicar el tipo de hash, y voy a probar con el diccionario por defecto:
    
```sh
john.exe –format=NT C:\Tools\pwdump8\pwdump.txt
```

![John the Ripper-3](https://i.ibb.co/9VGNY4K/john-the-ripper-3.png)

Como se puede ver en la imagen, la contraseña de mi hash ha sido crackeada correctamente utilizando el diccionario por defecto, pero si no la hubiese encontrado podría probar utilizando otro diccionario de la siguiente manera:

```sh
john.exe –format=NT –wordlist=C:\Tools\john\run\rockyou.txt
```

Rockyou.txt es uno de los diccionarios más populares pero existen infinidad de diccionarios en internet que puedes utilizar para crackear contraseñas.

Bueno, una vez obtenida la contraseña podemos visualizarla introduciendo:

```sh
john.exe –show –format=NT C:\Users\PH300\Documents\pwdump.txt
```

![PwDump-2](https://i.ibb.co/4RDLG2L/pwdump-2.png)

Y pues ya está. Con esto y un bizcocho hemos hecho un bypass en Windows, hemos extraido las contraseñas del fichero SAM utilizando PwDump y hemos crackeado las contraseñas con la versión de John the Ripper para Windows.
