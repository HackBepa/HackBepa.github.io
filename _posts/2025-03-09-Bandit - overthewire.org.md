---
layout: post
title: Bandit - overthewire.org
summary: Esta es mi resolucion al wargame bandit de overthewire.org
tags: bandit 
---
Overthewire.org es un recurso donde mediante conexiones a una máquina accederemos a diferentes usuarios que serán los "niveles", para pasar al siguiente nivel deberemos resolver el enunciado publicado en la web.

Esta página tiene diferentes dificultades siendo Bandit la más baja, ya que está centrada para introducir a Linux.

Este post está creado bajo las normas de la comunidad de overthewire por lo que no se mostrara ninguna contraseña, también si estáis intentando realizar el wargame recomiendo leer el enunciado, intentar resolver por vuestra cuenta y volver al blog para solucionar dudas o para ver como lo he hecho yo.

¡Muchas Gracias por leer!

### Nivel 0 

El objetivo de este nivel es que inicies sesión en el juego mediante SSH. El host al que debes conectarte es bandit.labs.overthewire.org, en el puerto 2220. El nombre de usuario es bandit0 y la contraseña es bandit0. Una vez que hayas iniciado sesión, ve a la página del Nivel 1 para descubrir cómo superar el Nivel 1.

Para conectarnos a la máquina mediante SSH, usaremos el siguiente comando:

`$ ssh bandit0@bandit.labs.overthewire.org -p 2220`

Un consejo cuando entremos a los usuarios banditX, normalmente no nos dejará ejecutar comandos como clear o hacer ctrl + L,
para poder navegar de manera normal pondremos `export TERM=xterm`.

### Nivel 0 --> Nivel 1

La contraseña para el siguiente nivel se almacena en un archivo llamado README ubicado en el directorio de inicio. Usa esta contraseña para iniciar sesión en bandit1 mediante SSH. Siempre que encuentres una contraseña para un nivel, usa SSH (en el puerto 2220) para iniciar sesión en ese nivel y continuar el juego.

Para superar el nivel 0, deberemos hacer un 'cat' al archivo readme, que tenemos en el /home/bandit0

Este ya nos proporcionará la contraseña del nivel 1

### Nivel 1 --> Nivel 2

La contraseña para el siguiente nivel se almacena en un archivo llamado - ubicado en el directorio de inicio

En este nivel nos encontramos que tenemos que leer un archivo llamado " - ", el problema es que el comando cat usa parámetros por lo que si escribimos '$ cat -' intentara buscar parámetros infinitamente y no listara el contenido del archivo "-".

Para visualizarlo hay varias maneras.

1. Indicar la ruta absoluta del archivo `cat /home/bandit1/-`
2. Escribir la ruta relativa `$ cat ./-` (entendiendo que estamos en /home/bandit1)
3. Hacer una búsqueda por palabras con `$ grep -r "\w"` (esto nos buscará cualquier contenido que tenga la "W", como puede ser el flag)

Yo lo he hecho de la siguiente manera:
`echo -e "\nTu contraseña es --> $(grep -r "\w" 2>/dev/null |tail -n 24 | head -n 1 | awk '{print $2}' FS=":")\n"`

Consejo: Intentar utilizar funciones de bash para que la maquina nos devuelva el output exacto en este caso la contraseña de bandit2, de esta manera podreis practicar maneras de filtrar en bash, muy util para hacer scripts.

### Nivel 2 --> Nivel 3

La contraseña para el siguiente nivel se almacena en un archivo con espacios en el nombre, esta ubicado en el directorio de inicio

Para el nivel 2 tenemos un archivo con espacios en su nombre, por lo que cuando intentamos hacer un cat, este intentara abrir archivos por separado, generando un error

Para visualizar este archivo podemos:

1. Ponerlo entre comillas `$ cat "spaces in this filename"`
2. Podemos usar un filtro `$ cat s*`
3. Podemos usar `$ cat *` porque es el unico archivo del directorio

### Nivel 3 --> Nivel 4
La contraseña para el siguiente nivel se almacena en un archivo oculto en el directorio inhere.

Este nivel nos dice que la contraseña esta en un archivo oculto dentro de un directorio también oculto.

1. Para resolver esto podemos jugar con el `ls -a`, que nos permite listar archivos ocultos, dentro del directorio inhere, listaremos nuevamente archivos ocultos y realizaremos un 
`$cat ...Hiden-From-You`, para ver su contenido

2. Con el comando `$ find . -type f` podemos buscar todos los archivos ocultos

Yo lo he realizado el punto 2 y he concatenado con un cat para que liste su contenido
`$find . -type f | xargs cat`

### Nivel 4 --> Nivel 5

La contraseña para el siguiente nivel se almacena en el único archivo legible por humanos en el directorio inhere. TIP: si tu terminal está en problemas, prueba el comando “reset”.

En este nivel nos encontramos archivos con guion "-" y que la contraseña esta en un solo archivo legible, los demás son binarios y no se pueden leer.

1. Hacer una búsqueda por palabras con `$ grep -r   "\w"`

### Nivel 5 --> Nivel 6

Aquí nos hace buscar un archivo con las siguientes características

1. human-readable
2. 1033 bytes in size
3. not executable

Yo lo he solucionado así:

`$find  . -type f  -readable ! -executable  -size 1033c | xargs cat`

### Nivel 6 --> Nivel 7

Para este ejercicio tenemos que encontrar en algún lugar del servidor un archivo con estas características: 

1. owned by user bandit 7
2. owned by group bandit 6
3. 33 bytes in size

`$find / -user bandit7 -group bandit6 -size 33c 2>/dev/null | xargs cat`

### Nivel 7 --> Nivel 8

Para este nivel nos pide buscar la contraseña en un archivo con muchísimos datos, la pista que nos proporciona es que la contraseña está cerca de la palabra "millionth"

`$cat data.txt | grep "millionth" | awk 'NF{print$NF}'`

### Nivel 8 --> Nivel 9

Para este ejercicio tenemos que buscar dentro del archivo data.txt, la única contraseña que no se repite

Para esto tenemos que primero ordenar alfabéticamente con el comando `sort` y filtraremos cadenas unicas con el comando `$ uniq -u`

`$sort  data.txt | uniq -u`

### Nivel 9 --> Nivel 10

Ahora la contraseña esta en un archivo no legible, las pistas que tenemos es que la contraseña esta al lado de multiples "=".

Para visualizar la contraseñas nos ayudaremos del comando `strings`, este comando ordenara por las cadenas legibles

`$strings data.txt | grep "====="| tail -n 1 | awk 'NF{print $2}$NF' | grep -vE "="`

### Nivel 10 --> Nivel 11

La contraseña esta en un archivo encriptado en `base64`

`$cat data.txt | base64 -d | awk 'NF{print$4}$NF' | grep -vE "pass"`

### Nivel 11 --> Nivel 12

Este ejercicio plantea que los caracteres de la contraseña han sido rotados 13 posiciones.

Si no acabais de entender este concepto podeis buscar encriptacion metodo cesar.

`$cat data.txt |tr '[A-Za-z]' '[N-ZA-Mn-za-m]' | awk 'NF{print $4}$NF' | grep -vE "passw"`

### Nivel 12 --> Nivel 13

En este ejercicio tenemos la contraseña en hexadecimal, dentro de un archivo que a sido comprimido varias veces.

`$ cat data.txt  | xxd -r | sponge data.txt`

![Ejemplo Nivel 12]({{ https://hackbepa.github.io }}/assets/img/bandit/EjemploEj12.png)

Luego tenemos que ir descomprimiendo cada archivo con `$ 7z x <Archivo>`, pero al ser tantos archivos uno dentro de otro he creado el siguiente scripts Decompressor.sh

**Decompressor.sh**
```bash
#!/bin/bash
function ctrl_c(){
 echo -e "\n \n [!] Saliendo..... \n \n"
 exit 1
}

# Ctrl+ C
trap ctrl_c INT

firts_file_name="data.gz"

decompressed_file_name="$(7z l data.gz | tail -n 3 | head -n 1 | awk 'NF{print $NF}')"

7z x $firts_file_name  &>/dev/null

while [ $decompressed_file_name ]; do
 echo -e "\n [+] Nuevo archivo descomprimido: $decompressed_file_name"
 7z x $decompressed_file_name &>/dev/null
 decompressed_file_name="$(7z l $decompressed_file_name 2>/dev/null| tail -n 3 | head -n 1 | awk 'NF{print $NF}')"
done
```

Para descomprimir usaremos el comando `7z l` para listar el primer archivo y `7z x` para descomprimirlo, una vez tengamos el primer archivo descomprimido, haremos un bucle `while` que vaya repitiendo el proceso de descomprimir archivos hasta que no exista ninguna mas.

Consejo: Este TIP me lo dio un profesor "Todo lo que se tenga que hacer más de 2 veces mejor crea un script"

### Nivel 13 --> Nivel 14

En este nivel no tendremos que buscar una contraseña sino que tendremos que lograr conectarnos directamente al bandit14 por ssh. 

La maquina nos facilita una clave ssh en el archivo **sshkey.private**, sabiendo como funcionan las claves SSH, podemos conectarnos sin saber la contraseña.

`ssh  -i sshkey.private bandit14@localhost -p 2220`

### Nivel 14 --> Nivel 15

En este nivel tendremos que buscar la contraseña en el puerto 3000.

Para pasar este nivel es necesario el comando nc que hace referencia al netcat, como su nombre indica es un comando que nos accede a la información de un puerto.

`nc localhost 30000`

### Nivel 15 --> Nivel 16

Para este nivel tendremos que volver a buscar la contraseña en el puerto 30001 pero esta vez tendremos que proporcional la contraseña del nivel actual cifrado en SSL.

Para esto podemos usar el comando `$ncat --ssl 127.0.0.1 30001`

### Nivel 16 --> Nivel 17

Para este nivel tendremos que hacer algo muy parecido al nivel anterior, tenemos que mandar la contraseña de bandit 16, a un puerto que esta abierto entre los 31000-32000, y este nos devolverá la contraseña de bandit 17.

Para resolver esto primero de todo escanearemos con NMAP:

`$ nmap --open -T5 -v -n -p31000-32000 localhost`

```console
Explicacion:
-- open --> Filtra por los puertos abiertos
-- T5 --> Hace un escaneo muy rapido
-- v --> Muestra los resultados a medida que los descubre
-- n --> Desactiva la resolucion DNS
-- p --> Especifica los puertos a escanear
```

También podemos hacer un script para controlar mejor el escaneo de puertos.

Esto nos devolverá los puertos abiertos entre el rango seleccionado, ahora usaremos el comando nc tal como hemos visto en el nivel anterior, he iremos mandando la contraseña de bandit 16 hasta que nos conteste con la flag de bandit 17.

### Nivel 17 --> Nivel 18
Para este nivel tenemos 2 archivos: passwords.new y passwords.old, deberemos encontrar la manera para listar las líneas cambiadas entre los dos archivos.
Esta línea será la contraseña de bandit 18.

Para solucionarlo podemos hacer uso del comando diff que sirve para comparar dos archivos.

`$ diff passwords.new  passwords.old`

### Nivel 18 --> Nivel 19 
La contraseña para el siguiente nivel se almacena en un archivo README en el directorio de inicio. Desafortunadamente, alguien ha modificado .bashrc para cerrar tu sesión cuando inicias sesión con SSH.

Para resolver este ejercicio deberemos comprender que ssh nos deja ejecutar comandos.

Nosotros normalmente escribimos este comando para acceder a bandit18

`sshpass -p 'x2gLTTjFwMOhQ8oWNbMN362QKxfRqGlO' ssh bandit18@bandit.labs.overthewire.org -p 2220`

Pues para ejecutar un comando es tan simple como agregar un espacio y escribir, si el usuario tiene permisos ejecutara el comando.

La solución es encender una bash con este método y abrir el fichero readme con la contraseña.
`sshpass -p 'x2gLTTjFwMOhQ8oWNbMN362QKxfRqGlO' ssh bandit18@bandit.labs.overthewire.org -p 2220 bash`

### Nivel 19 --> Nivel 20
Para obtener acceso al siguiente nivel, debe utilizar el binario setuid en el directorio de inicio. Ejecútelo sin argumentos para saber cómo usarlo. La contraseña para este nivel se puede encontrar en el lugar habitual (/etc/bandit_pass), después de haber utilizado el binario setuid.

Consejo: El permiso SUID --> Cuando se activa sobre un fichero ara que quien lo ejecute tenga los mismos permisos que el creador del archivo.

### Nivel 20 --> Nivel 21

Hay un binario setuid en el directorio de inicio que hace lo siguiente: establece una conexión con localhost en el puerto que especifica como argumento de línea de comando. Luego lee una línea de texto de la conexión y la compara con la contraseña del nivel anterior (bandit20). Si la contraseña es correcta, transmitirá la contraseña para el siguiente nivel (bandit21).

NOTA: Intente conectarse a su propio daemon de red para ver si funciona como cree

Para solucionar este ejercicio usaremos el comando netcat para abrir un puerto en especifico, luego usaremos el script con SUID (suconnect) de la home de bandit20 para conectarnos al puerto que nosotros hemos abierto.

Luego proporcionamos la contraseña de bandit20 y nos contestara con la del siguiente nivel.

![nivel20]({{ https://hackbepa.github.io }}/assets/img/bandit/nivel20.png)

### Nivel 21 --> Nivel 22

Aqui tengo que hacer un pequeño parentesis este nivel es el primero de una serie de niveles que se basan en cron, si no habis tenido contacto con esta herramienta investigar, puede ser dificil de entender a la primera, yo en un futuro hare un post sobre esta herramienta.

Un programa se ejecuta automáticamente a intervalos regulares desde cron, el programador de tareas basado en tiempo. Busque en /etc/cron.d/ la configuración y vea qué comando se está ejecutando.

Ahora que entendemos mejor el uso de Cron, en este nivel se nos plantea que existe una configuración en /etc/cron.d.

Si revisamos nos encontramos con la siguiente tarea:
![lvl 21 cron 1]({{ https://hackbepa.github.io }}/assets/img/bandit/nivel21_cron_1.png)

Esta ejecutando un script cada minuto.

Si hacemos un cat en el script nos sale lo siguiente.
![lvl 21 cron 2]({{ https://hackbepa.github.io }}/assets/img/bandit/nivel21_cron_2.png)

Este script modifica los permisos de una archivo temporal y acto seguido escribe en el archivo de la flag de bandit22 algo alojado en dicho archivo.

Si volvemos a hacer un cat a este archivo nos aparecerá la flag del siguiente nivel. 

### Nivel 22 --> Nivel 23
Un programa se ejecuta automáticamente a intervalos regulares desde cron, el programador de tareas basado en tiempo. Busque en /etc/cron.d/ la configuración y vea qué comando se está ejecutando.

NOTA: Ver scripts de shell escritos por otras personas es una habilidad muy útil. El script para este nivel está diseñado para que sea fácil de leer. Si tiene problemas para comprender lo que hace, intente ejecutarlo para ver la información de depuración que imprime.

Este nivel es casi igual al anterior para solucionarlo también se utiliza cron pero con la diferencia que el script es el siguiente:
![lvl 22 cron 1]({{ https://hackbepa.github.io }}/assets/img/bandit/nivel22_cron_1.png)

Ese script tiene dos variables principales, la variable **myname** que recoge el nombre del usuario y la variable **mytarget** que recoge la cadena **echo I am user $myname** la encripta en md5 y coge el usuario como argumento.

Luego lista un mensaje de copiando archivo de contraseña del usuario especificado a una carpeta temporal con el nombre de **mytarget**

Ahora que entendemos el script, podemos copiar el comando de **mytarget** y en lugar de que utilice la variable **myname** ponemos nosotros bandit23, esto generara el código en md5 necesario para conocer el nombre del archivo que contiene la flag.

![lvl 22 cron 2]({{ https://hackbepa.github.io }}/assets/img/bandit/nivel22_cron_2.png)

### Nivel 23 --> Nivel 24

Un programa se ejecuta automáticamente a intervalos regulares desde cron, el programador de tareas basado en tiempo. Busque en /etc/cron.d/ la configuración y vea qué comando se está ejecutando.

NOTA: Este nivel requiere que cree su primer script de shell. ¡Este es un gran paso y debería estar orgulloso de sí mismo cuando supere este nivel!

NOTA 2: Tenga en cuenta que su script de shell se elimina una vez ejecutado, por lo que es posible que desee conservar una copia...

Este nivel contiene un script que ejecuta y borra todos los archivos dentro de **/var/spool/bandit24/foo** creados por bandit23.

Mi solucion a sido crearme un directorio temporal con `mktemp -d`, crear el siguiente script teniendo en cuenta que será bandit24 que lo ejecutara:

![lvl 23]({{ https://hackbepa.github.io }}/assets/img/bandit/nivel23.png)

Aquí estoy copiando la información del directorio de la contraseña de bandit24 a un archivo metido en mi carpeta temporal luego doy permiso al grupo otros para poder leer el contenido de este archivo y obtener la flag.

### Nivel 24 --> Nivel 25

Un daemon está escuchando en el puerto 30002 y te dará la contraseña de bandit25 si le das la contraseña de bandit24 y un código PIN secreto de 4 dígitos. No hay forma de recuperar el código PIN excepto si se recorren las 10 000 combinaciones, lo que se denomina fuerza bruta.
No necesitas crear nuevas conexiones cada vez

Para hacer un script de fuerza bruta tenemos que tener claro como podemos hacer que recorra una serie de números

En bash lo podemos conseguir con un bucle for de la siguiente manera

for i in {0000..9999}

Esto ara que i recorra por cada posible combinación de 4 dígitos.

Ahora tenemos que guardar todos los inputs en un archivo para luego mandarlo al puerto 30002.

yo he realizado el siguiente script:

![lvl 24 1]({{ https://hackbepa.github.io }}/assets/img/bandit/nivel24_1.png)

![lvl 24 2]({{ https://hackbepa.github.io }}/assets/img/bandit/nivel24_2.png)

### Nivel 25 --> Nivel 26

Iniciar sesión en bandit26 desde bandit25 debería ser bastante fácil... El shell del usuario bandit26 no es /bin/bash, sino algo diferente. Descubre qué es, cómo funciona y cómo salir de él.

Este ejercicio es bastante raro, tenemos la clave privada de bandit26, pero como dice el enunciado no ejecuta una shell si no un txt con un banner y nos cierra la conexion

![lvl 25 1]({{ https://hackbepa.github.io }}/assets/img/bandit/nivel25_1.png)

Vamos a intentar indagar en lo que realmente esta ejecutando.

Iremos a **/etc/passwd** de bandit 26 y veremos lo siguiente:

![lvl 25 2]({{ https://hackbepa.github.io }}/assets/img/bandit/nivel25_2.png)
Efectivamente esta ejecutando un archivo showtext, tenemos permisos para leerlo a si que vamos a ello.

![lvl 25 3]({{ https://hackbepa.github.io }}/assets/img/bandit/nivel25_3.png)

El archivo ejecuta el comando more a un texto en la /home de bandit 26 que suponemos que es el banner que vemos.

Aquí empieza lo enrevesado, si buscamos info sobre el comando more, nos damos cuenta que cuando los textos son largos podemos usar las flechas de dirección para ir mas arriba o mas abajo cosa que con cat no pasa.

Pero también usando los ":" podemos setear variables y utilizarlas.

El método que he encontrado para acceder a una shell de bandit26 es el siguiente:

Redimensionamos nuestra shell hasta que sea bastante pequeña, así desbloquearemos el menú de more, luego presionaremos "v" para acceder al modo visual.

![lvl 25 4]({{ https://hackbepa.github.io }}/assets/img/bandit/nivel25_4.png)

Dentro presionaremos escape + shift + : y setearemos una variable llamada shell que apunte a /bin/bash

![lvl 25 5]({{ https://hackbepa.github.io }}/assets/img/bandit/nivel25_5.png)

Ahora haremos **escape + shift + :** y usaremos la variable definida anteriormente

![lvl 25 6]({{ https://hackbepa.github.io }}/assets/img/bandit/nivel25_6.png)

Esto nos dara una bash completamente normal como bandit26

### Nivel 26 --> Nivel 27

¡Buen trabajo consiguiendo un shell! ¡Ahora date prisa y consigue la contraseña para bandit27!

Aquí tenemos otro script con el permiso SUID activado, como he explicado antes todo lo que se ejecute con este permiso lo hace con contexto de su usuario creador.

Aquí he aprovechado en hacer un cat contra el archivo de la contraseña de bandit27 /etc/bandit_pass/bandit27

### Nivel 27 --> Nivel 28

Aqui sucede lo mismo que nos a pasado anteriormente con cron, los siguientes niveles estan basados en Git, otra herramienta super util para nuestro dia a dia, si no la conoces te animo a investigar, seguro que te encanta!!!

Hay un repositorio git en `ssh://bandit27-git@localhost/home/bandit27-git/repo` a través del puerto 2220. La contraseña para el usuario bandit27-git es la misma que la del usuario bandit27.

Clona el repositorio y encuentra la contraseña para el siguiente nivel.

Tal y como ha dicho el enunciado lo que debemos hacer es clonar un repositorio.

El unico apunte que tengo que hacer es que cuando yo realice el nivel pedia conectarse a un puerto en especifico.

Para esto simplemente agregaremos el numero de puerto al final de localhost de la siguiente manera.
`ssh://bandit27-git@localhost:2220/home/bandit27-git/repo`

### Nivel 28 --> Nivel 29

Hay un repositorio git en ssh://bandit28-git@localhost/home/bandit28-git/repo a través del puerto 2220. La contraseña para el usuario bandit28-git es la misma que la del usuario bandit28.

Clona el repositorio y encuentra la contraseña para el siguiente nivel.

Este nivel empieza idéntico al anterior pero una vez que hemos clonado el repositorio vemos que la contraseña no es visible.

En git puedes consultar versiones pasadas de un repositorio con `git log`.

`git log` nos revela que el nombre del ultimo commit es "Reparada la filtración de datos".

Nosotros podemos ver ese cambio con `git show identificador`.

### Nivel 29 --> Nivel 30

A partir de aqui los enunciados siguen la misma mecanica que los dos ejercicios anteriores por lo que no los voy a indicar.

En este caso nos encontramos con que la contraseña no esta y nunca a estado en el README.

Lo que realmente esta pasando es que estamos viendo la rama del proyecto puesta en producción.

con `git branches -a` podemos ver otras ramas anteriores.

Si nos conectamos a la rama con `git chekout devs` tenemos el README con la contraseña al siguiente nivel

### Nivel 30 --> Nivel 31
En este nivel nos encontramos con:

![lvl 30]({{ https://hackbepa.github.io }}/assets/img/bandit/nivel30.png)

Para resolver el problema he probado en listar las tags del proyecto con `git tag` y justamente había una tag "secret"

A si que simplemente hacemos un `git show secret` para ver que hay en la tag y nos encontramos con la pass del siguiente nivel.

### Nivel 31 --> Nivel 32

Para este nivel el README nos pide que creemos un archivo **key.txt** con una frase y lo subamos al repositorio.

Después de crear el archivo lo agregaremos al repositorio con `git add -f key.txt`

Luego tenemos que hacer un `git commit -m "mensaje que queramos"` por ultimo haremos un `git push` para subirlo el cual nos contestara con la contraseña de bandit32

### Nivel 32 --> Nivel 33

Este nivel nos pide que escapemos de una shell modificada que nos pone todo lo que pongamos en mayusculas imposibilitando la ejecución de comandos.

Para escapar debemos jugar con los argumentos posicionales de bash.

Nuestra "uppershell" es esencialmente un script ejecutándose en una bash

A si que si ponemos $0 haremos alusión a la shell que esta ejecutando saliendo del script.

A partir de aquí ya tenemos acceso a la shell de bandit33.

### Felicidades

Ya hemos completado el wargame bandit de overthewire.org, espero que os aya gustado mucho y sobretodo que hayais aprendido mucho o hayais repasado conceptos.

Esta a sido mi primera entrada mas seria al blog, por favor podeis escribirme al gmail o al discord (axelbepa) para darme consejos tanto de conocimientos tecnicos como de escritura (que me hacen falta jajaja)
