
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
