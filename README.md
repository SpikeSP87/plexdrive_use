# plexdrive_use
Una miniguía para el uso de plexdrive (en su última versión). El motivo es básicamente por algunas 
dudas que surgen, y aclarar como sería lo correcto en cuanto a uso/organización.

1. En mi caso cuando instalo un binario, me gusta que esté disponible para su ejecución de la 
misma forma que usamos cualquier utilidad en la shell. Es por ello que lo primero que deberíamos 
hacer es obtener plexdrive, descomprimirlo, y moverlo a'/usr/local/bin' ó a '/bin'.
2. Damos permiso de ejecución para todos los usuarios al binario, en el lugar en el que lo hemos movido: 'chmod a+x 
/usr/local/bin/plexdrive'
3. La primera vez que realizamos la configuración de plexdrive, nos pide el id y secret que 
tenemos que realizar en nuestra cuenta de drive (crear una credencial OAuth). Para ello nos 
dirigimos a 
'https://console.developers.google.com'; iniciamos sesión en nuestra cuenta, habilitamos la api de 
google drive, creamos un proyecto (le damos un nombre), y dentro del proyecto, en 'APIs & auth', 
seleccionamos crear una nueva credencial 'OAuth 2.0 client ID'. Hay varios sitios en los que se 
explica como realizarlo, por ejemplo: 
'http://www.iperiusbackup.net/es/activar-la-api-de-google-drive-y-obtener-las-credenciales-para-la-copia-de-seguridad/'
4. Obtenida nuestra credencial (id y secret los cuales no debemos de proporcionarselos a nadie y que 
debemos guardar y usar con seguridad), vamos a crear el directorio de montaje, como ejemplo, voy a 
realizarlo en '/mnt/drive1': 'mkdir /mnt/drive1'
5. De la misma forma, voy a crear un directorio para la configuración de esta cuenta de drive. Yo 
aconsejo darle un nombre (bautizar la cuenta) similar al del montaje; es algo personal, pero es más sencillo para la 
organización en caso de querer tener más de una cuenta montada en el sistema (más adelante lo 
veremos). Para ello crearé un directorio bajo el directorio que usará plexdrive por defecto (como 
digo es algo subjetivo, que cada uno puede crear donde quiera, pero sería lo correcto). A modo 
ejemplo que estamos siguiendo: 'mkdir -p
/home/tu_nombre_usuario/.plexdrive/drive1' (NOTA: con -p creamos los directorios no existentes en 
la ruta, por si no existe .plexdrive. Otra opción es crear el directorio anteriormente de forma 
individual).
6. Es ahora cuando podemos comenzar a realizar la primera inicialización de la cuenta con 
plexdrive. Para ello lanzamos el comando de montaje, siguiendo con el ejemplo: 'plexdrive mount -c 
/home/tu_nombre_usuario/.plexdrive/drive1 -o allow_other /mnt/drive1'
7. A continuación nos informa de que necesitamos el id y secret de nuestra credencial que 
deberíamos haber creado en el paso 3. Lo insertamos conforme nos pide, y nos debería de dar un enlace sobre el que tendríamos que acceder, para proporcionarnos token que debemos indicar/pegar. En caso de que no nos de el enlace, reiniciar el equipo, y lanzar de nuevo la orden de montaje. No podemos seguir hasta que no completemos esto (el token es necesario y a veces se atasca ahí, ó tarda un poco en acceder a la API). Una vez hecho, se quedará que parece que no hace nada, adueñado de la shell.
8. Pulsamos 'CTRL+C' para matar el proceso, y a continuación lanzamos el comando de montaje con el 
'&' al final. De esta forma no se apodera de la shell, y a partir de ahora sería la forma ideal de 
realizar el montaje quedando en segundo plano y sin necesidad de usar screen. Siguiendo el ejemplo: 'plexdrive mount -c
/home/tu_nombre_usuario/.plexdrive/drive1 -o allow_other /mnt/drive1 &'
9. Teóricamente ya habríamos terminado, no obstante, puede que queramos realizar el montaje de 
otra cuenta, por tanto, realizaríamos los pasos de nuevo, desde el 3 al 8, pero cambiando nuestro 
identificador de la cuenta, es decir, yo en el ejemplo, he usado por así decirlo un nombre drive1, 
pues podría ser para esta segunda cuenta, drive2, y realizaría el montaje en /mnt/drive2, así como 
crear su credencial OAuth, y su directorio de configuración 
/home/tu_nombre_usuario/.plexdrive/drive2.

POSIBLES ERRORES Y SOLUCIONES

- Que en nuestro sistema no tengamos permisos de montaje con allow_other. Nos dirigimos a /etc/fuse.conf y descomentamos la linea (en caso de existir), ó incluimos: user_allow_other 
- Si el montaje en algún momento se queda pinzado por alguna configuración incorrecta (posibles espacios al introducir los id, secret ó token), desmontar usando: 'fusermount -uz /turutademontaje'
- Para reiniciar/resetear la config de una cuenta de drive, eliminamos los ficheros del directorio de configuración, por ejemplo: 'rm * /home/tu_nombre_usuario/.plexdrive/drive1/' (cuidado con las rutas y revisadlo bien). Básicamente son 2 los ficheros que eliminar, el config.json y token.json.

ACLARACIONES:

- En primer lugar, según los sistemas, puede ser necesario ser un administrador, ó tener 
privilegios de administrador. Las rutas pueden variar en ese aspecto. El principal problema puede 
venir porque sea necesario que para realizar montajes en el sistema no pueda realizarlo un usuario 
cualquiera. Esto hay que tenerlo en cuenta si tenemos problemas con el montaje.
- Verificar los permisos de los directorios involucrados.
- En segundo lugar, importante que puede ser necesario y conveniente usar el debug de plexdrive 
mediante el modificador '-v 3', ej: 'plexdrive -v 3 mount -c
/home/tu_nombre_usuario/.plexdrive/drive1 -o allow_other /mnt/drive1 &'
- Puede ser que durante el cacheo de contenido, nos llevemos algún ban, si esto pasa, cuando desmontamos y volvemos a montar con el debug, observaremos que se 
queda en 'openning connection', verificar si hay ban en la cuenta (no de subida, si no de peticiones). En caso de ban, el montaje no falla, pero no veríamos 
nada en el directorio del montaje.
- El directorio de trabajo por defecto de plexdrive es el 'home del usuario'/.plexdrive (si no indicamos un lugar de configuración en el comando de montaje, 
será este el directorio de configuración por defecto). Además, en este directorio, se establecerá la base de datos Bolt a modo caché. Pero no es lo único que 
usa como caché. En la base de datos se almacena la estructura de directorios (ó arbol de directorios del drive).
- Para el uso de más de una cuenta de drive con plexdrive, es estrictamente necesario usar directorios de configuración diferentes. 
- Puede ser interesante si usamos más de una cuenta con plexdrive, separar las caches de cada cuenta, para ello haríamos uso del modificador en el comando de montaje '-cache-file=/home/tu_nombre_usuario/.plexdrive/cache_drive1.bolt'  y en la segunda cuenta '-cache-file=/home/tu_nombre_usuario/.plexdrive/cache_drive2.bolt'. No obstante en la v5, no he conseguido realizar esta configuración. Tampoco lo deja muy claro su dev. De hecho revisando el código no veía el lugar donde recogiese el comando para realizar la configuración. Esto queda pendiente de observación y testeo.

Se irá ampliando y mejorando la miniguía, incluso si se desea, se pueden añadir usos relacionados con la herramienta. No obstante, comentar que el desarrollador 
de plexdrive, está en un proyecto similar, multiplataforma, y la herramienta no es perfecta. Así también tenemos rclone como una gran alternativa, actualmente 
en desarrollo de un modo caché similar a plexdrive, y bueno, cualquier recomendación será bienvenida. Para ello, podéis abrir un issue, y en la medida de lo 
posible, iremos corrigiendo errores y mejorando.

Un cordial saludo. 



