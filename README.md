Documentación sprint 6

Primero nos descargamos docker un ubuntu.
Para empezar nos logueamos en docker con el comando “docker login”,Una vez dentro nos descargamos la imagen oficial de nessus.
Arrancamos el contenedor de nessus con el parámetro docker run “-it” para ponerlo en modo iterativo y “-d” para en modo secundario y -p para conducirlo para un puerto. No es necesario pero para entrar dentro del contenedor con los parametros -it y /bin/bash para entrar en la terminal.
Una vez instalada la imagen y arrancado el contenedor para entrar en nessus vamos a nuestro buscador y introducimos “https://NUESTRA IP:el puerto que hayamos seleccionado”, esperamos a que se inicialice y introducimos un código de activación que podemos encontrar por internet. 
Una vez dentro elegimos la opción “advanced Scan” Ponemos el nombre que queramos y la descripción que nos ayude a identificarlo para luego, lo importante es “targets” que será donde pondremos la ip de la máquina metasploitable.
Programamos un escaneo, para empezar a escanear le damos al triángulo de play,y como veremos se empieza a escanear.
Aquí está el resultado final, como vemos  metasploitables está llena de vulnerabilidades.
Este es el escaneo donde hemos hecho varios hardenings y configuraciones de seguridad, está mucho mejor asegurada.
NMAP
Escaneamos con el comando nmap al servidor y la máquina metasploitables.















Documentación sprint 5

INSTALAR SSH
Primero instalaremos el SSH server con el comando “sudo apt install openssh-server”, con el comando sudo chmod 600 /etc/ssh/sshd_config establecemos solo permisos de lectura y escritura para el usuario root

CONFIGURACIÓN DE SESIÓN

Habilitamos el módulo PAM poniendo el parámetro UsePAM yes en la dirección “etc/ssh/sshd_config”, esto se usa para las conexiones ssh para permitir un mayor control de los accesos de las cuentas de usuarios. Añadimos el parámetro MaxStartups 10:30:60 que significa el número máximo de conexiones no autenticadas concurrentes para evitar problemas de denegación de servicios. El parámetro “10” se refiere al número de conexiones no autenticadas permitidas antes de empezar a rechazar conexiones nuevas, “30” se refiere al porcentaje de conexiones que se empezaran a rechazar una vez pasadas las 10 iniciales y el “60” se refiere al máximo número de conexiones posibles, una vez llegado a este valor todas las nuevas conexiones serán rechazadas.
Descomentamos las líneas “ClientAliveInterval” y “ClientAliveCountMax” para así poder controlar el tiempo de expiración de la sesión SSH. El primero indica el intervalo de tiempo sobre el que el sistema comprueba si existe actividad y el segundo el número de veces que debe preguntar y recibir respuesta de inactividad antes de cerrar sesión. Descomentamos el parámetro “MaxAuthTries” que especifica el número máximo de intentos de autenticación permitidos por conexión le damos el valor 4 así evitamos riesgos de ataques de fuerza bruta,
Descomentamos el parámetro ignore Rhosts el cual indica que se ignorarán los ficheros .rhosts y .shosts para la autenticación basada en RSA y Host, de esta forma forzamos a que solo podamos acceder a través de una contraseña, El parámetro “HostbasedAuthentication” lo descomentamos y le damos el valor de NO.
Esto lo que hace es permitir la conexión basada en hosts de confianza. Con este parámetro y el de ignorerhosts obliga a autenticarse mediante contraseña, El parámetro PermitEmptyPasswords permite autenticarse sin contraseña por lo cual lo desmontamos y lo dejaremos en No. En el valor “PermitRootLogin” le ponemos no así evitamos el autenticarse directamente como root .

CONFIGURACIÓN DEL CIFRADO

Se deben utilizar algoritmos que no tengan vulnerabilidades conocidas y con una clave lo suficientemente robusta para que no pueda ser comprometida, para configurar estas opciones añadiremos las líneas correspondientes a los parámetros Cipher, MACs y KexAlgorithms.
Instalamos el paquete del módulo PAM para la doble autenticación con el siguiente comando “sudo apt install libpam-google-authenticator -y”, Añadimos la siguiente línea “auth required pam googleauthenticator.so” en el fichero /etc/pam.d/sshd.Configurar SSH para que haga uso de este módulo PAM. Ahora nos dirigimos a /etc/ssh/sshd_config y añadimos la línea “ChallengeResponseAuthentication yes”. Reiniciamos es sistema con el comando “restart sshd”:

Con el comando google-authenticator vamos a generar un código qr y con la aplicación “google-authenticator” lo escaneamos, nos dará una contraseña que cambia cada 30 segundos por motivos de seguridad. Introducimos esa clave y nos generara 5 contraseñas de emergencia que solo se podrán usar una vez, Con la aplicación PuTTY me he conectado remotamente a mi servidor linux con mi dirección ip que es 192.168.18.128, Una vez dentro nos pedirá el nombre de usuario y contraseña para luego pedirnos el código de verificación que nos la dará la aplicación.





Documentación sprint 4

Lo primero que vamos ha hacer va a ser instalar apache en nuestro servidor linux.(antes debemos actualizar nuestros repositorios) una vez instalado debemos comprobar que funciona.
Seguidamente explicamos los ficheros de configuraciones y aplicamos la configuración de usuarios y grupos en la directiva /etc/apache2/apache2.conf, luego en la directiva /etc/apache2/envvars podemos ver los valores de estas variables y podremos comprobar que el servidor arrancará con el usuario www-data.
Tratamos con la ocultación de versiones donde nos dirigimos al fichero /etc/apache2/conf-enabled/security.conf y añadimos las líneas  ServerTokens ProductOnly y ServerSignature Off.
En la exposición mínima de módulos es posible que no necesitemos todos los módulos que vienen habilitados por defecto, con el comando a2dismod podremos deshabilitarlos. Con el comando apache2ctl -M podemos listarlos y ver cuales tenemos activos.
En la directiva /etc/apache2/sites-enabled/000-default.conf podremos editar la directiva options, Para evitar que el servidor muestre el contenido podemos usar la directiva options junto a -indexes como muestra la foto anterior. Así protegemos el recurso privado.
La configuración con el Required pone restricciones, básicamente hemos configurado el servidor de tal forma que para entrar al servidor tienes que loguearte con un usuario y su contraseña, previamente ya hemos creado el usuario y lo añadimos en la configuración del servidor. Con el comando htpasswd -c password incibe creamos el usuario y le ponemos una contraseña, Como podremos ver si intentamos acceder nos pide un usuario y una contraseña.
Los ficheros .htaccess permiten personalizar la configuración de directivas, su flexibilidad de configuración les proporciona alta probabilidad de usos incorrectos.
Para crear un certificado en la configuración del site le añadimos las siguientes del bloque #certificado ssl y como podremos ver por conexión https nos sale el certificado autofirmado.
 ¿Qué es mod_security? ModSecurity es un módulo para diversos servidores web de la empresa SpiderLabs  que protege de ataques web comúnmente conocidos.
Luego realizamos el ataque DoS mediante Metasploit (slowloris) y comprobamos que elo servidor está totalmente inaccesible.
Luego Clonamos e instalamos las reglas recomendadas OWASP. Habilitamos mod_security.
De nuevo realizamos otro ataque Dos y comprobamos que el servidor está accesible.




Documentación Sprint 3 Copias de seguridad:

El primer paso será instalar el Duplicati en nuestro servidor.

Con el Duplicati instalado creamos la primera copia con la ruta del servidor y hacemos que cree una copia en google drive, cumpliendo el requisito de que solo podemos permitir la pérdida de datos durante una hora y que solo se genere información de lunes a viernes.

Luego hacemos una restauración.

Creamos la segunda copia con origen en la carpeta “imágenes” de nuestro servidor  y con destino a google drive, esta vez solo podemos aceptar la pérdida de información durante 1 día y solo se genera información de lunes a domingo.

Instalamos 2 herramientas de recuperación de archivos eliminados y hacemos una prueba con las 2 herramientas, luego volvemos ha hacer la misma prueba pero primero hacemos un formateo rápido a nuestro USB y luego lo volvemos a probar pero con un formateo completo.

Con la aplicación Eraser eliminamos cualquier archivo e intentamos recuperar el mismo archivo.






Documentación Sprint 2 - Hardening de Windows:

- Instala un S.O Windows 11 o 10. Para simular en mejores condiciones la red local de los empleados del entorno empresarial, este equipo Windows 10 tendrá un usuario local (tu nombre y apellidos, sin active directory) Este equipo será utilizado por un empleado que tendrá mínimos privilegios (no podrá instalar nada), por tanto, debe existir otro usuario administrador. Crea los dos usuarios con los privilegios adecuados, y comprueba su configuración.

Para empezar crearemos un usuario con nuestro nombre, en mi caso “Marc Bauzá” y le asignaremos el rango de cuenta local sin ningún permiso. Como podemos comprobar al intentar instalar cualquier aplicación nos pedirá la contraseña de administrador indicándonos que el usuario que hemos creado no es administrador.

- Utilizar Seguridad de Windows. Comprueba que efectivamente tienes habilitado Windows Defender.
Si queremos verificar que tenemos los sistemas de seguridad activos podemos buscarlo y configurarlo desde el apartado de “Seguridad De Windows”, ç

- prueba que tienes habilitado la protección contra ransomware. Acceso controlado a carpetas: es sumamente importante mantenerlo activo. Ya que esto permitirá que potenciales programas maliciosos no puedan tener control de tus archivos y carpetas. Además, tendrás opción de visualizar el historial de los bloqueos, agregar o quitar carpetas protegidas y gestionar las aplicaciones que tienen acceso controlado a tus archivos o carpetas. Comprueba que tienes protegida la carpeta de Documentos del perfil del usuario, ya que esta es la carpeta que utilizará el usuario para el trabajo diario.

el siguiente paso será activar el acceso controlado de carpetas y añadimos la carpeta que nos indica el enunciado.


Comprueba que si hubiera un intento de intrusión en uno de los portátiles si podrían resetear la contraseña del administrador y accedera la cuenta.

Para simular un ataque a nuestro ordenador nos instalaremos una herramienta live, en este caso “Kali-linux-2022.3-live-amd64.iso”.
Al iniciar la la herramienta cargará el sistema operativo entramos en su terminal y nos registramos como root, con el comando fdisk -l nos listará todas las particiones existentes en el disco duro, identificamos la partición que contiene el sistema operativo de windows 10 en mi caso como “/dev/sda2”. Para realizar el montaje de este tipo de sistemas se utilizará el comando mount -t ntfs-3g /dev/sda2 /media donde /media es un directorio existente en Kali Linux, así le daremos acceso a los archivos de windows 10. las contraseñas de los usuarios se suelen guardan en un archivo el cual es almacenado en C:/Windows/System32/config/SAM, Con chntpw se podrán realizar diferentes acciones sobre el archivo SAM de Windows, entre ellas se podrán administrar los grupos de usuarios añadiendo o quitando usuarios, eliminar contraseñas de usuarios o modificarlas. Utilizaremos el comando chntpw -i /media/Windows/System32/config/SAM y se nos enseñara un menú y utilizaremos la opción 1 para editar los datos de contraseñas de usuarios. Luego nos preguntará el usuario al que se le debe modificar la contraseña, nosotros utilizaremos el admin, una vez seleccionado el administrador seleccionaremos la opción 1 que eliminará la contraseña del usuario.

Cifrado de disco.
Cuando hayamos cifrado el disco nos pedirá una contraseña para entrar a la máquina y cuando intentemos limpiar la contraseña con el disco cifrado nos dará error constantemente.
