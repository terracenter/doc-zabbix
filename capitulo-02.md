#  Capítulo 2: Preparando las cosas con la gestión de usuarios de Zabbix
## Gestión de usuarios
En este capítulo, trabajaremos en la creación de nuestros primeros grupos de usuarios, usuarios y roles de usuario. Es muy importante configurar estos de la manera correcta, ya que darán acceso a las personas a su entorno Zabbix con los permisos correctos. Repasando estas cosas paso a paso, nos aseguraremos de que tenemos una configuración estructurada de Zabbix antes de continuar con este libro.

Como extra, también configuraremos alguna autenticación de usuario avanzada utilizando SAML para facilitar las cosas a tus usuarios de Zabbix y proporcionarles una forma de utilizar las credenciales de inicio de sesión que ya podrían estar utilizando en toda tu empresa. Vamos a repasar todos estos pasos en el orden de las siguientes recetas:

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

------------
## Uso de los nuevos roles de usuario de Zabbix
Se ha introducido una nueva funcionalidad en esta versión de Zabbix 6.0 LTS. Ahora es posible crear tus propios roles de usuario en Zabbix. En versiones anteriores de Zabbix, teníamos la posibilidad de asignar uno de tres tipos de usuario:

- Usuario
- Administrador
- Super admin

Lo que estos tipos de usuario hacían en versiones anteriores era restringir lo que los usuarios de Zabbix podían ver en el frontend. Sin embargo, esto siempre estaba predefinido. Ahora, con la adición de los roles de usuario que podemos crear nosotros mismos, podemos establecer nuestras propias restricciones relacionadas con el frontend, haciendo posible mostrar sólo ciertas partes de la interfaz de usuario a determinados usuarios de Zabbix, sin dejar de respetar los permisos establecidos mediante grupos de usuarios.

### Preparación
Para esta receta, necesitaremos un servidor Zabbix, preferiblemente el configurado en la receta anterior. En la receta anterior, configuramos diferentes grupos de usuarios, para proporcionar diferentes permisos en grupos de hosts. Completamente separado del grupo de usuarios, aplicaremos ciertos roles de usuario a nuestros usuarios para determinar lo que pueden ver en la interfaz de usuario. Veamos cómo configurar nuestros roles de usuario.

### Cómo hacerlo...
Primero, navega al frontend de Zabbix y ve a Administración | Roles de usuario. Esto nos mostrará los roles de usuario por defecto tal y como los conoces de versiones anteriores de Zabbix.

1. En primer lugar, navegue hasta el frontend de Zabbix y vaya a Administración | Roles de usuario. Esto nos mostrará los roles de usuario por defecto tal y como los conoces de versiones anteriores de Zabbix.
<p align = "center">
   <img src = "https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_02_008.jpg" alt="Figura 2.8 - Ventana de configuración por defecto de los roles de usuario de Zabbix">
</p>
<p align = "center">Figura 2.8 - Ventana de configuración por defecto de los roles de usuario de Zabbix</p>

2. Aquí, podemos hacer clic en el botón azul Crear rol de usuario en la esquina superior derecha.
3. Configuraremos un nuevo rol de usuario llamado  `Rol + Usuario`. Este rol será para usuarios de Zabbix que sólo tendrán permisos de lectura, pero que necesitan más acceso que sólo los elementos de navegación Monitoreo, Inventario e Informes.
<p align = "center">
   <img src = "https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_02_009.jpg" alt="Figura 2.9 - Parte superior de la ventana de configuración de un nuevo rol de usuario de Zabbix">
</p>
<p align = "center">Figura 2.9 - Parte superior de la ventana de configuración de un nuevo rol de usuario de Zabbix</p>

4. Lo primero es lo primero, asegúrese de rellenar Nombre como `usuario + rol`.
5. Centrémonos primero en la parte donde dice Acceso a los elementos de la interfaz de usuario. Cuando se selecciona Usuario para el tipo de Usuario, no somos capaces de añadir algún acceso al rol de usuario. Así que vamos a cambiar el tipo de usuario seleccionando Admin en el menú desplegable.
6. Específicamente quiero que este rol de usuario llamado Usuario + role tenga la habilidad de acceder a la página de mantenimiento. Configurando esto se verá así:
<p align = "center">
   <img src = "https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_02_010.jpg" alt="Figura 2.10 - Un nuevo rol Zabbix User+ con acceso a Mantenimiento">
</p>
<p align = "center">Figura 2.10 - Un nuevo rol Zabbix User+ con acceso a Mantenimiento</p>

7. Asegúrese de cambiar también la sección Acceso a acciones del formulario desmarcando Gestionar informes programados como se indica a continuación:
<p align = "center">
   <img src = "https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_02_011.jpg" alt="Figura 2.11 - Un nuevo rol Zabbix User+ con la configuración correcta de Acceso a acciones">
</p>
<p align = "center">Figura 2.11 - Un nuevo rol Zabbix User+ con la configuración correcta de Acceso a acciones</p>

8.  Por último, pero no menos importante, haga clic en el botón azul Añadir en la parte inferior del formulario para añadir este nuevo rol de usuario.

### Cómo funciona...
En primer lugar, vamos a desglosar las opciones que tenemos al crear roles de usuario en Zabbix:
- **Nombre**: Aquí podemos establecer un nombre personalizado para nuestro rol de usuario.
- **Tipo de usuario**: Los tipos de usuario siguen existiendo en Zabbix 6, aunque ahora forman parte de los roles de usuario. Todavía hay un límite a lo que puede ser visto por un determinado tipo de usuario y el tipo Super admin sigue siendo sin restricciones cuando se trata de permisos.
- **Acceso a los elementos de la interfaz de usuario**: Aquí podemos restringir lo que un usuario puede ver en la interfaz de usuario de Zabbix cuando se le asigna este rol de usuario.
- **Acceso a servicios**: Aquí se puede restringir la monitorización de servicios o SLAs, ya que quizás no queramos que todos los usuarios tengan acceso a ellos.
- **Acceso a módulos**: Los módulos frontales personalizados de Zabbix están totalmente integrados en el sistema de roles de usuario, lo que significa que podemos seleccionar qué módulos frontales puede ver un usuario de Zabbix.
- **Acceso a la API**: La API de Zabbix puede ser restringida a ciertos roles de usuario. Por ejemplo, es posible que sólo quieras un rol de usuario API específico, limitando el acceso del resto de usuarios a la API de Zabbix.
- **Acceso a acciones**: En los roles de usuario de Zabbix, ciertas acciones pueden ser limitadas, como la capacidad de editar tableros, tokens de API de mantenimiento, y más.

Ahora, veamos lo que hemos cambiado entre el rol de usuario llamado `User role` y el rol de usuario llamado `User+ role`. El rol de usuario por defecto llamado `rol de Usuario` tiene el siguiente acceso a los elementos de la UI:
<p align = "center">
   <img src = "https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_02_012.jpg" alt="Figura 2.12 - Rol de usuario predeterminado de Zabbix denominado Rol de usuario Acceso a los elementos de la interfaz de usuario">
</p>
<p align = "center">Figura 2.12 - Rol de usuario predeterminado de Zabbix denominado Rol de usuario Acceso a los elementos de la interfaz de usuario</p>

Por defecto, tenemos tres roles de usuario en Zabbix 6, que reflejan los tipos de usuario que están disponibles. El rol de usuario que vemos aquí en `Nombre` refleja el tipo de usuario que tenemos llamado Usuario. Nos da acceso a los elementos de la UI vistos arriba, restringiendo el rol de usuario llamado `User role` para que sólo pueda ver ciertas cosas y no hacer cambios de configuración.

Por ejemplo, se considera un permiso de impacto el poder configurar **Mantenimiento**. Porque claro, podrías restringir notificaciones importantes configurando **Mantenimiento**. Pero aquí viene la trampa, ¿qué pasa si quieres explícitamente que un usuario de Zabbix sólo sea capaz de leer la información, pero aún así no tiene acceso a las páginas de configuración? En Zabbix 5.0, esto no era posible porque sólo se podía seleccionar el tipo de usuario User, Admin, o Super admin, dando acceso inmediatamente a toda la sección de configuración cuando se utilizaban los tipos de usuario Admin y Super admin.

Ahora, veamos lo que hicimos creando un nuevo rol de usuario llamado `rol Usuario`:
<p align = "center">
   <img src = "https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_02_013.jpg" alt="Figura 2.13 - Nuevo rol de usuario de Zabbix llamado Rol Usuario+ Acceso a elementos de la UI">
</p>
<p align = "center">Figura 2.13 - Nuevo rol de usuario de Zabbix llamado Rol Usuario+ Acceso a elementos de la UI</p>

Aquí podemos ver lo que ocurre si cambiamos el tipo de usuario a Admin pero no seleccionamos todos los elementos disponibles de Acceso a la interfaz de usuario. Ahora tenemos un rol de usuario sin acceso a páginas de configuración importantes, pero con acceso a Mantenimiento.

Combinando esto con la configuración de Acceso a acciones, donde añadimos la configuración Crear y editar mantenimiento como se ve en la *Figura 2.11*, tendríamos acceso completo a la configuración de mantenimiento.

Cuando asignemos este rol a un usuario en la siguiente receta e iniciemos sesión con ese usuario, podremos ver lo siguiente en nuestra barra lateral de Zabbix.
<p align = "center">
   <img src = "https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_02_014.jpg" alt="Figura 2.14 - Rol de usuario personalizado Barra lateral de Zabbix">
</p>
<p align = "center">Figura 2.14 - Rol de usuario personalizado Barra lateral de Zabbix</p>

Esto, por supuesto, es sólo uno de los muchos tipos de configuraciones que puede utilizar. Usted tiene la capacidad de permitir a los usuarios de Zabbix el acceso a los menús y opciones a través de una serie de parámetros bajo un montón de roles de usuario personalizados. Usted es libre de configurar esto como quiera, añadiendo mucha flexibilidad al usuario dentro de Zabbix.

### Hay más...
Zabbix se encuentra actualmente en el proceso de elaboración de los roles de usuario, lo que significa que algunas partes todavía pueden faltar o puede ver problemas con ellos. Como es una nueva característica, está siendo constantemente mejorada y ampliada. Consulta la documentación de Zabbix para más información sobre esta característica: https://www.zabbix.com/documentation/current/en/manual/web_interface/frontend_sections/administration/user_roles.

------------
## Creando tus primeros usuarios
Con nuestros grupos de usuarios recién creados, hemos dado nuestro primer paso hacia una configuración de Zabbix más estructurada y segura. El siguiente paso es asignar realmente algunos usuarios a los grupos de usuarios recién creados para asegurarnos de que se les asignan nuestros nuevos permisos de usuario del grupo.
### Preparación
Para empezar, necesitaremos el servidor y los grupos de usuarios recién creados en la última receta. Así que, comencemos la configuración.

Ahora sabemos que hay tres departamentos en la empresa llamada Cloud Hoster que van a utilizar nuestra instalación Zabbix. Hemos creado grupos de host para ellos, pero también hay usuarios en esos departamentos que realmente quieren utilizar nuestra instalación. Vamos a conocerlos:
<p align = "center">
   <img src = "https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_02_015.jpg" alt="Figura 2.15 - Diagrama de usuarios de Cloud Hoster">
</p>
<p align = "center">Figura 2.15 - Diagrama de usuarios de Cloud Hoster</p>

Estos son los usuarios que necesitamos configurar para que Cloud Hoster los utilice.
### Cómo hacerlo...
Empecemos a crear los usuarios. Empezaremos con nuestro departamento de Redes:
1. Navegue a Administración | Usuarios, que le llevará a esta página:
<p align = "center">
   <img src = "https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_02_016.jpg" alt="Figura 2.16 - Ventana Usuarios de Zabbix">
</p>
<p align = "center">Figura 2.16 - Ventana Usuarios de Zabbix</p>

2. Aquí es donde ocurre toda la magia de la creación de usuarios, ya que estaremos administrando todos nuestros usuarios desde esta página. Para crear nuestro primer usuario del departamento de **Redes** llamado `s_network`, haga clic en el botón **Crear usuario** en la esquina superior derecha, lo que nos llevará a la siguiente pantalla:
<p align = "center">
   <img src = "https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_02_017.jpg" alt="Figura 2.17 - Ventana de configuración de Usuarios de Zabbix">
</p>
<p align = "center">Figura 2.17 - Ventana de configuración de Usuarios de Zabbix</p>
3. Rellena **Username** para proporcionarnos el nombre de usuario que utilizará este usuario, que será `s_network`.
4. Además, es importante añadir este usuario al grupo que acabamos de crear para darle a nuestro usuario los permisos adecuados. Haga clic en **Seleccionar** y elija nuestro grupo llamado Networking.
5. Por último, pero no menos importante, establece una **contraseña** segura en los campos Contraseña; no la olvides porque la utilizaremos más adelante.
6. Después de esto, pasa a la pestaña **Permisos**, ya que no vamos a configurar los medios de comunicación todavía:
<p align = "center">
   <img src = "https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_02_018.jpg" alt="Figura 2.18 - Ventana de configuración de permisos de usuario de Zabbix">
</p>
<p align = "center">Figura 2.18 - Ventana de configuración de permisos de usuario de Zabbix</p>

7. Seleccione aquí la opción Rol denominada Rol de superadministrador. Esto permitirá a nuestro usuario acceder a todos los elementos de la interfaz de usuario y ver y editar información sobre todos los grupos de hosts en el servidor Zabbix.
Los siguientes roles de usuario están disponibles en Zabbix por defecto:
<p align = "center">
   <img src = "https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_02_019_new.jpg" alt="Figura 2.19 - Tabla que detalla los diferentes roles de usuario de Zabbix">
</p>
<p align = "center">Figura 2.19 - Tabla que detalla los diferentes roles de usuario de Zabbix</p>
8. Repitamos los pasos anteriores para el usuario llamado `y_network` pero en la pestaña Permissions, seleccionemos la opción Admin role de la siguiente manera:
<p align = "center">
   <img src = "https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_02_020.jpg" alt="Figura 2.20 - Ventana de configuración de permisos de usuario de Zabbix">
</p>
<p align = "center">Figura 2.20 - Ventana de configuración de permisos de usuario de Zabbix</p>
Después de crear estos dos usuarios, pasemos a crear el usuario de infraestructura, `r_infra`. Repite los pasos que dimos para `s_network`, cambiando el nombre de usuario, por supuesto. Después, añade este usuario al grupo y dale a nuestro usuario los permisos adecuados. Haga clic en Seleccionar y elija nuestro grupo llamado Infraestructura. Se verá así:
<p align = "center">
   <img src = "https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_02_021.jpg" alt="Figura 2.21 - Ventana de configuración de usuario de Zabbix para r_infra">
</p>
<p align = "center">Figura 2.21 - Ventana de configuración de usuario de Zabbix para r_infra</p>
Por último, haga de este usuario otro **Super admin**.

9. Ahora, para nuestro último usuario, vamos a repetir nuestros pasos de nuevo, cambiando el Nombre de Usuario y el grupo en la pestaña Usuario de la siguiente manera:
<p align = "center">
   <img src = "https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_02_022.jpg" alt="Figura 2.22 - Ventana de configuración de Zabbix User para e_buy">
</p>
<p align = "center">Figura 2.22 - Ventana de configuración de Zabbix User para e_buy</p>

10. Si no has seguido la receta anterior, puedes cambiar el Rol de este usuario por el de Usuario en la pestaña Permisos. Pero si seguiste la receta anterior, podemos usar el rol Usuario+ que creamos así:
<p align = "center">
   <img src = "https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_02_023.jpg" alt="Figura 2.23 - Ventana de configuración de usuarios de Zabbix para e_buy">
</p>
<p align = "center">Figura 2.23 - Ventana de configuración de usuarios de Zabbix para e_buy</p>

Configurar el usuario con el `rol Usuario` también permitirá al usuario `e_buy` crear periodos de mantenimiento.

Cuando haya terminado, terminará con lo siguiente:
- `s_red`: Un usuario con acceso a los permisos del grupo de usuarios **Networking** y que tiene el rol **Super admin**.
- `y_network`: Un usuario con acceso a los permisos del grupo de usuarios Networking y que tiene el rol **Admin**.
- `r_infra`: Un usuario con acceso a los permisos del grupo de usuarios **Infraestructura** y que tiene el rol de** Super admin**.
- `e_buy`: Un usuario con acceso a los permisos del grupo de usuarios **Compras e Inventario** y que tiene el rol `Usuario` o el `rol Usuario`.

------------






