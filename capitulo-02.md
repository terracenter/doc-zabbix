#  Capítulo 2: Preparando las cosas con la gestión de usuarios de Zabbix
## Gestión de usuarios
En este capítulo, trabajaremos en la creación de nuestros primeros grupos de usuarios, usuarios y roles de usuario. Es muy importante configurar estos de la manera correcta, ya que darán acceso a las personas a su entorno Zabbix con los permisos correctos. Repasando estas cosas paso a paso, nos aseguraremos de que tenemos una configuración estructurada de Zabbix antes de continuar con este libro.

Como un bono, también vamos a configurar algunos avanzados de autenticación de usuario utilizando SAML para hacer las cosas más fáciles para sus usuarios Zabbix y proporcionarles una manera de utilizar las credenciales de inicio de sesión que ya podría estar utilizando a través de su empresa. Vamos a repasar todos estos pasos en el orden de las siguientes recetas:

- Creación de grupos de usuarios
- Uso de los nuevos roles de usuario de Zabbix
- Creación de los primeros usuarios
- Autenticación avanzada de usuarios con SAML

## Requisitos técnicos
Podemos hacer todo el trabajo en este capítulo con cualquier configuración de Zabbix instalada. Si aún no has instalado Zabbix, consulta el capítulo anterior para aprender cómo hacerlo. Vamos a ir a través de nuestra configuración Zabbix para tener todo listo para nuestros usuarios para empezar a iniciar sesión y utilizar el frontend Zabbix.

## Creación de grupos de usuarios
Para iniciar sesión en el frontend de Zabbix, vamos a necesitar usuarios. En este momento, estamos conectados con el usuario por defecto, lo cual es lógico porque necesitamos un usuario para crear usuarios. Sin embargo, esta no es una configuración segura, porque no queremos seguir utilizando zabbix como contraseña. Por lo tanto, vamos a aprender a crear nuevos usuarios y agruparlos en consecuencia.

Es importante elegir cómo desea gestionar los usuarios en Zabbix antes de configurar las cuentas de usuario. Si desea utilizar algo como LDAP o SAML, es una idea inteligente para hacer la elección de utilizar uno de esos métodos de autenticación de inmediato, por lo que no tendrá ningún problema de migración.

### Preparándonos
Ahora que sabemos cómo está estructurada la interfaz de usuario de Zabbix y sabemos cómo navegar por ella, podemos empezar a hacer alguna configuración real. Empezaremos creando algunos grupos de usuarios para familiarizarnos con el proceso y empezar a utilizarlos. De esta manera, nuestra configuración de Zabbix no sólo se vuelve más estructurada, sino también más segura.

Para empezar con esto, necesitaremos un servidor Zabbix como el que usamos en las recetas anteriores y el conocimiento que hemos adquirido allí para navegar a las secciones correctas del frontend.

Observando la siguiente figura, podemos ver cómo está configurada nuestra empresa de ejemplo, Cloud Hoster. Crearemos los usuarios que se ven en el diagrama para crear una configuración de usuarios estructurada y sólida:
<p align = "center">
   <img src = "https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_02_001.png" alt="Figura 2.1 - Diagrama del departamento de Cloud Hoster">
</p>
<p align = "center">Figura 2.1 - Diagrama del departamento de Cloud Hoster</p>

Así, Cloud Hoster tiene algunos departamentos que necesitan acceso al frontend de Zabbix y otros que no lo necesitan en absoluto. Digamos que queremos dar acceso al frontend de Zabbix a los siguientes departamentos:
- Trabajar en red: Configurar y supervisar sus dispositivos de red
- Infraestructura: Configurar y supervisar sus servidores Linux
- Compras e inventario: Para consultar la información de inventario y compararla con otras herramientas internas

### Cómo hacerlo...
Vamos a empezar con la creación de estos tres grupos en nuestra interfaz de usuario Zabbix:

1. Para ello, navega a administración | GRUPOS DE USUARIOS, que te mostrará la siguiente página:
<p align = "center">
   <img src = "https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_02_002.jpg" alt="Figura 2.2 - Ventana de grupos de usuarios de Zabbix">
</p>
<p align = "center">Figura 2.2 - Ventana de grupos de usuarios de Zabbix</p>

2. Ahora, vamos a empezar por crear el grupo de trabajo en red haciendo clic en Crear grupo de usuarios en la esquina superior derecha. Esto le llevará a la siguiente pantalla:
<p align = "center">
   <img src = "https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_02_003.jpg" alt="Figura 2.3 - Ventana de configuración de grupos de usuarios de Zabbix">
</p>
<p align = "center">Figura 2.3 - Ventana de configuración de grupos de usuarios de Zabbix</p>

Necesitaremos rellenar la información, empezando por Nombre del grupo, que por supuesto será Redes. Todavía no hay usuarios para este grupo, así que nos saltaremos esa opción. Frontend access es la opción que nos proporcionará la autenticación; si seleccionamos LDAP aquí, se utilizará la autenticación LDAP para autenticar. Lo mantendremos como System default, que es la autenticación interna de Zabbix.

3. Ahora, vamos a navegar a la siguiente pestaña en esta página, que es Permisos:
<p align = "center">
   <img src = "https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_02_004.jpg" alt="Figura 2.4 - Ventana de configuración de grupos de usuarios de Zabbix Permisos">
</p>
<p align = "center">Figura 2.4 - Ventana de configuración de grupos de usuarios de Zabbix Permisos</p>


Aquí, podemos especificar a qué grupos de host tendrá acceso nuestro grupo. Ya existe un grupo por defecto para Redes, que utilizaremos en este ejemplo.

4. Haga clic en Seleccionar para acceder a una ventana emergente con los grupos de hosts disponibles. Selecciona aquí Plantillas/Dispositivos de red y volverás a la ventana anterior, con el grupo rellenado.
5. Selecciona Lectura-Escritura y haz clic en el botón Añadir para añadir estos permisos.
6. No vamos a añadir nada más, así que haz clic en el botón azul más grande Añadir para terminar de crear este grupo de hosts.

**Consejo**
Cuando se utiliza la autenticación Zabbix como HTTP, LDAP o SAML, todavía tenemos que crear nuestros usuarios internamente con los permisos adecuados. Configure sus usuarios para que coincidan con el nombre de usuario de su método de autenticación en Zabbix y utilice el método de autenticación para la gestión de contraseñas.

Ahora tendremos un nuevo grupo de host llamado Networking que sólo podrá leer y escribir en el grupo de host Templates/Network devices:
<p align = "center">
   <img src = "https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_02_005.jpg" alt="Figura 2.5 - Ventana de grupos de usuarios de Zabbix">
</p>
<p align = "center">Figura 2.5 - Ventana de grupos de usuarios de Zabbix</p>

7. Repitamos este proceso para el grupo de host Infraestructura, excepto que en lugar de añadir el grupo de host Plantillas/Dispositivos de red, añadiremos el grupo de host Servidores Linux, de esta forma:
<p align = "center">
   <img src = "https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_02_006.jpg" alt="Figura 2.6 - Ventana de configuración de permisos de grupos de usuarios de Zabbix con un grupo de hosts">
</p>
<p align = "center">Figura 2.6 - Ventana de configuración de permisos de grupos de usuarios de Zabbix con un grupo de hosts</p>

8. Haga clic en Añadir para guardar este grupo de host.
9. Repite los pasos de nuevo y para añadir Compras e Inventario, haremos algo diferente. Repetiremos el proceso que acabamos de hacer excepto la parte de los permisos. Queremos que Compras e Inventario puedan leer nuestros datos de inventario, pero no queremos que cambien realmente la configuración de nuestro host. Añade tanto Plantillas/Dispositivos de red como Servidores Linux al grupo, pero sólo con permisos de Lectura, de esta forma:
<p align = "center">
   <img src = "https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_02_007.png" alt="Figura 2.7 - Ventana de configuración de permisos de grupos de usuarios de Zabbix con dos grupos de hosts">
</p>
<p align = "center">Figura 2.7 - Ventana de configuración de permisos de grupos de usuarios de Zabbix con dos grupos de hosts</p>

¡Enhorabuena! Terminar esto significa que has terminado con tres grupos de host diferentes y ¡podemos continuar creando nuestros primeros nuevos usuarios! Vamos a ello.

### Hay más...
Los grupos de usuarios de Zabbix son bastante extensos y hay mucho más de lo que parece al principio. Como todo el sistema de permisos se basa en qué grupo(s) de usuarios y rol(es) de usuario formas parte, siempre es una buena idea leer primero la documentación de Zabbix: https://www.zabbix.com/documentation/current/en/manual/config/users_and_usergroups/usergroup.
