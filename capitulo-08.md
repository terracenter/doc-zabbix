No se puede predicar sobre Zabbix sin predicar sobre el uso de proxies Zabbix - una buena adición al principio, pero una obviedad a estas alturas. Cualquiera que espere configurar un entorno Zabbix de tamaño medio/grande necesitará proxies. La razón principal para utilizar proxies es la escalabilidad, ya que los proxies Zabbix descargan la recogida de datos y la carga de preprocesamiento del servidor Zabbix. De esta forma podemos escalar más y con mayor facilidad nuestro entorno Zabbix.

En este capítulo, primero aprenderemos cómo configurar un proxy Zabbix. Luego aprenderemos cómo trabajar con proxies Zabbix pasivos y activos, y también cómo monitorizar hosts con cualquiera de las dos formas del proxy Zabbix. También cubriremos algo de descubrimiento de red Zabbix usando los proxies, y aprenderemos a monitorizar los proxies Zabbix para mantenerlos sanos. Después de estas recetas, no tendrás más excusas para no configurar los proxies, ya que cubriremos la mayoría de las formas posibles de uso de proxies en este capítulo.

Por lo tanto, vamos a ir a través de las siguientes recetas y comprobar cómo trabajar con proxies Zabbix:
* Configuración de un proxy Zabbix
* Trabajar con proxies Zabbix pasivos
* Trabajar con proxies Zabbix activos
* Monitorización de hosts con un proxy Zabbix
* Uso del descubrimiento con proxies Zabbix
* Monitorizando tus proxies Zabbix

## Requisitos técnicos
Vamos a necesitar varios hosts Linux nuevos para este capítulo, ya que los construiremos como proxies Zabbix

Configure dos proxies instalando CentOS (o su distribución de Linux preferida) en los dos nuevos hosts siguientes:
* lar-book-proxy-passive
* lar-book-proxy-active

También necesitarás el servidor Zabbix, con al menos un host monitorizado. Vamos a utilizar el siguiente nuevo host con un agente Zabbix instalado:
* lar-book-agent-by-proxy

## Configurar un proxy Zabbix
Configurar un proxy Zabbix puede ser bastante desalentador si no tienes mucha experiencia con Linux, pero la tarea es bastante sencilla una vez que le coges el truco. Instalaremos un proxy Zabbix en nuestro servidor `lar-book-proxy-passive`; puedes repetir la tarea en `lar-book-proxy-active`.

## Preparación
Asegúrese de tener su nuevo host Linux listo e instalado. Todavía no necesitaremos nuestro servidor Zabbix en esta receta.

También necesitarás un repositorio Zabbix para esta receta. Echa un vistazo al siguiente enlace para encontrar la última versión: https://www.zabbix.com/download.

## Cómo hacerlo...
1. Empecemos por iniciar sesión en la interfaz de línea de comandos (CLI) de nuestro nuevo `host lar-book-proxy-passive`.
2. Ahora, ejecute el siguiente comando para añadir el repositorio Zabbix para CentOS 8:
```bash
rpm -Uvh https://repo.zabbix.com/zabbix/6.0/rhel/8/ x86_64/zabbix-release-6.0-1.el8.noarch.rpm
dnf clean all
```
Para Ubuntu, ejecute este comando:
```bash
wget https://repo.zabbix.com/zabbix/6.0/ubuntu/pool/ main/z/zabbix-release/zabbix-release_6.0-1+ubuntu20.04_all.deb
dpkg -i zabbix-release_6.0-1+ubuntu20.04_all.deb
apt update
```

3. Ahora, instale el proxy Zabbix ejecutando el siguiente comando.
Sistemas basados en RHEL:
```bash
dnf install zabbix-proxy-sqlite3
```
Sistemas Ubuntu:
```bash
apt install zabbix-proxy-sqlite3
```
**Consejo**
En servidores basados en RHEL, no olvides poner Security-Enhanced Linux (SELinux) en permisivo o permitir el proxy Zabbix en SELinux para producción. Para entornos de laboratorio está bien poner SELinux en permisivo, pero en producción recomendaría dejarlo activado. Para sistemas Ubuntu, en un entorno de laboratorio, podemos desactivar AppArmor.

4. Ahora, edita la configuración del proxy de Zabbix ejecutando el siguiente comando:
```bash
vim /etc/zabbix/zabbix_proxy.conf
```

5. Empecemos por configurar el modo proxy en el proxy pasivo. El modo será 1 en este proxy. En el proxy activo, será 0:
```bash
ProxyMode=1
```
6. Cambia la siguiente línea por la dirección de tu servidor Zabbix:
```bash
Server=10.16.16.152
```

**Nota importante**
Cuando trabaje con un servidor Zabbix en Alta Disponibilidad (HA), asegúrese de añadir aquí las direcciones IP del servidor Zabbix para cada uno de los nodos de su cluster. El proxy sólo enviará datos al nodo activo. Tenga en cuenta que los nodos HA están delimitados por un punto y coma (;) en lugar de una coma (,).

7. Cambie la siguiente línea por el nombre de host de su proxy:
```bash
Hostname=lar-book-proxy-passive
```
8. Como vamos a utilizar la versión `sqlite` del proxy para el ejemplo, cambie el parámetro `DBName` por el siguiente:
```bash
DBName=/tmp/zabbix_proxy.db
```
9. Ahora puede habilitar el proxy Zabbix e iniciarlo con los siguientes dos comandos:
```bash
systemctl enable zabbix-proxy
systemctl start zabbix-proxy
```
10. Es posible que desee comprobar que los registros de proxy Zabbix se reiniciará, con el siguiente comando:
```bash
tail -f /var/log/zabbix/zabbix_proxy.log
```
## Cómo funciona...
Hay tres versiones de Zabbix proxy para trabajar:
* zabbix-proxy-mysql
* zabbix-proxy-pgsql
* zabbix-proxy-sqlite3

Acabamos de hacer la configuración para el paquete `zabbix-proxy-sqlite3`, que es el método más fácil, si me preguntas. La versión `sqlite3` de Zabbix proxy hace posible que podamos configurar un proxy Zabbix con gran facilidad, ya que en realidad no necesitamos preocuparnos demasiado acerca de la configuración de la base de datos.

Tenga en cuenta que las versiones sqlite3 pueden no ser adecuadas para proxies Zabbix con muchos hosts y elementos. Tienes más opciones para escalar una versión mysql o pgsql de Zabbix proxy por los mecanismos de ajuste fino instalados con esos tipos de bases de datos.

**Consejo**
Elija siempre el tipo correcto de proxy Zabbix para lo que espera necesitar en el futuro. Aunque es fácil cambiar de proxy más tarde, no te pases con esta elección ya que podrías ahorrarte horas en el futuro.

Lo mejor de la versión `sqlite3 `es que si tenemos problemas con la base de datos, es muy fácil ejecutar lo siguiente:
```bash
rm -rf /tmp/zabbix_proxy.db
```
Zabbix proxy entonces crea una nueva base de datos en el arranque, y estamos listos para empezar a recoger de nuevo. Tenga en cuenta que podemos perder alguna información que está en la base de datos proxy y que aún no se ha enviado al servidor Zabbix.

## Hay más...
Puede encontrar más información sobre la instalación de proxies Zabbix aquí:
https://www.zabbix.com/documentation/current/manual/installation/install_from_packages

Elige la distribución que estés utilizando, y podrás encontrar las guías para todas las diferentes variantes de instalación de proxy.
***

## Trabajando con proxies Zabbix pasivos
Ahora que hemos instalado nuestro proxy Zabbix en la receta anterior, podemos empezar a trabajar con él. Empecemos configurando nuestro proxy pasivo Zabbix en el frontend y veamos qué podemos hacer con él desde el principio.

### Preparación
Necesitarás el host `lar-book-proxy-passive` para esta receta listo e instalado con el proxy Zabbix. También volveremos a utilizar nuestro servidor Zabbix en esta receta.

### Cómo hacerlo...
1. Vamos a empezar por iniciar sesión en nuestro frontend Zabbix y navegar a **Administración **| **Proxies**:
<p align = "center">
   <img src = "https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_08_001.jpg" alt="Figura 8.1 - Página Administración | Proxies, sin proxies pasivos">
</p>
<p align = "center"> Figura 8.1 - Página Administración | Proxies, sin proxies pasivos </p>

2. Vamos a añadir un nuevo proxy con el botón azul Crear proxy en la esquina superior derecha.

3. Esto nos llevará a la página Crear proxy, donde rellenaremos la siguiente información:
<p align = "center">
   <img src = "https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_08_002.jpg" alt="Figura 8.2 - Administración | Proxies, página Crear proxy, lar-book-proxy-passive">
</p>
<p align = "center"> Figura 8.2 - Administración | Proxies, página Crear proxy, lar-book-proxy-passive </p>

4. Antes de pulsar el botón azul Añadir, echemos un vistazo a la pestaña Cifrado:
<p align = "center">
   <img src = "https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_08_003.jpg" alt="Figura 8.3 - Administración | Proxies, página Crear cifrado de proxy, lar-book-proxy-passive">
</p>
<p align = "center"> Figura 8.3 - Administración | Proxies, página Crear cifrado de proxy, lar-book-proxy-passive</p>

5. Por defecto, No encryption está marcado aquí, lo que dejaremos así para esta receta.
**Nota importante**
Mucha información valiosa se intercambia entre los servidores Zabbix y los proxies Zabbix. Si estás trabajando con redes inseguras o simplemente necesitas una capa extra de seguridad, utiliza el cifrado de proxy Zabbix. Puedes encontrar más información sobre el cifrado de Zabbix aquí: https://www.zabbix.com/documentation/current/en/manual/encryption

6. Ahora, haga clic en el botón azul Añadir, que nos llevará de nuevo a nuestra página de resumen de proxy.
7. La parte Última vez visto (edad) de su proxy recién añadido ahora debe mostrar un valor de tiempo, en lugar de Nunca:
<p align = "center">
   <img src = "https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_08_004.jpg" alt="Figura 8.4 - Página Administración | Proxies, Última visita (edad)">
</p>
<p align = "center">Figura 8.4 - Página Administración | Proxies, Última visita (edad)</p>

### Cómo funciona...
Añadir proxies no es la tarea más difícil después de que ya hemos hecho la parte de instalación. Después de los pasos que dimos en esta receta, estamos listos para empezar a monitorear con este proxy.

El proxy que acabamos de agregar es un proxy pasivo. Estos proxies funcionan recibiendo configuración del servidor Zabbix, que el servidor Zabbix envía al proxy Zabbix en el puerto 10051:

<p align = "center">
   <img src = "https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_08_005.jpg" alt="Figura 8.5 - Diagrama de la conexión proxy activa">
</p>
<p align = "center">Figura 8.5 - Diagrama de la conexión proxy activa</p>

Una vez que el proxy pasivo sabe qué monitorizar, cada vez que el servidor Zabbix sondea en busca de datos, éstos se envían de vuelta en el mismo flujo TCP. Esto significa que la conexión siempre se inicia desde el lado del servidor Zabbix. Una vez configurado, el servidor Zabbix seguirá enviando cambios de configuración y seguirá sondeando para obtener nuevos datos.
***

## Trabajando con proxies Zabbix activos
Ahora sabemos cómo instalar y añadir proxies. Vamos a configurar nuestro proxy activo, como hicimos con el proxy pasivo en la receta anterior, y ver cómo funciona.

### Preparación
Necesitarás el host `lar-book-proxy-active` para esta receta, listo e instalado con el proxy Zabbix. También usaremos nuestro servidor Zabbix en esta receta.

### Cómo hacerlo...
1. Vamos a empezar por iniciar sesión en nuestro frontend Zabbix y navegar a Administración | Proxies:

<p align = "center">
   <img src = "https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_08_006.jpg" alt="Figura 8.6 - Página Administración | Proxies, sin proxies activos">
</p>
<p align = "center">Figura 8.6 - Página Administración | Proxies, sin proxies activos</p>

2. Nuestra página de Proxies es donde hacemos toda la configuración relacionada con los proxies.
3. Vamos a añadir un nuevo proxy pulsando el botón azul Crear proxy en la esquina superior derecha.

4. Esto nos llevará a la página Crear proxy, donde rellenaremos la siguiente información:
<p align = "center">
   <img src = "https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_08_007.jpg" alt="Figura 8.7 - Administración | Proxies, Página Crear proxy, lar-book-proxy-active">
</p>
<p align = "center">Figura 8.7 - Administración | Proxies, Página Crear proxy, lar-book-proxy-active</p>

**Consejo**
La dirección del Proxy es opcional para nuestro proxy activo. No es necesario añadirla para que el proxy Zabbix funcione, pero se recomienda para mantener las cosas claras. La adición de la dirección Proxy también funciona como una especie de lista blanca en este caso, ya que sólo la dirección IP en la lista se le permitirá conectarse.

5. Antes de pulsar el botón azul Añadir, echemos un vistazo a la pestaña Cifrado:
<p align = "center">
   <img src = "https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_08_008.jpg" alt="Figura 8.8 - Administración | Proxies, página Crear cifrado de proxy, lar-book-proxy-active">
</p>
<p align = "center">Figura 8.8 - Administración | Proxies, página Crear cifrado de proxy, lar-book-proxy-active</p>

6. Por defecto, No encryption está marcado aquí, lo que dejaremos así para esta receta.
7. Ahora, haga clic en el botón azul Add, que nos llevará de vuelta a la página de resumen del proxy.
8. Inicie sesión en la CLI y compruebe la configuración con el siguiente comando:
```bash
vim /etc/zabbix/zabbix_proxy.conf
```

9. Por defecto, la frecuencia con la que el proxy solicita cambios de configuración es de 3.600 segundos (es decir, 1 hora):
<p align = "center">
   <img src = "https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_08_009.jpg" alt="Figura 8.9 - Fichero de configuración del proxy Zabbix, ConfigFrequency">
</p>
<p align = "center">Figura 8.9 - Fichero de configuración del proxy Zabbix, ConfigFrequency</p>

**Sugerencia**
Para proxies Zabbix activos, también es posible forzar al proxy Zabbix a solicitar datos de configuración al servidor Zabbix con `zabbix_proxy -R config_cache_reload`. Tenga en cuenta que esto no funcionará en un proxy Zabbix pasivo.

10. La parte Last seen (age) de su proxy recién añadido debería mostrar ahora un valor de tiempo, en lugar de Never.
<p align = "center">
   <img src = "https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_08_010.jpg" alt="Figura 8.10 - Página Administración | proxies, Última visita (edad)">
</p>
<p align = "center">Figura 8.10 - Página Administración | proxies, Última visita (edad)</p>

Dependiendo de su configuración en el archivo de configuración del proxy, la parte Última vez visto (edad) puede tardar un poco en cambiar.

**Nota importante**
Sondear el servidor Zabbix para la configuración con demasiada frecuencia puede aumentar la carga, pero sondear con poca frecuencia dejará su proxy Zabbix esperando una nueva configuración. Elija la frecuencia que mejor se adapte a su entorno.

### Cómo funciona...
Si has seguido la receta Trabajar con proxies pasivos de Zabbix de este capítulo, los pasos son prácticamente los mismos, excepto la parte en la que añadimos el modo proxy y la parte en la que comprobamos el valor ConfigFrequency.

El proxy que acabamos de añadir es un proxy activo que funciona solicitando una configuración al servidor Zabbix en el puerto 10051.
<p align = "center">
   <img src = "https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_08_011.jpg" alt="Figura 8.11 - Diagrama de la conexión proxy activa">
</p>
<p align = "center">Figura 8.11 - Diagrama de la conexión proxy activa</p>

El proxy Zabbix sigue solicitando cambios en la configuración, y sigue enviando los nuevos datos recogidos al servidor Zabbix cada segundo o envía un heartbeat si no hay datos disponibles.

**Nota importante**
Es recomendable utilizar proxies activos de Zabbix, ya que podemos utilizarlos para reducir la carga de nuestro servidor Zabbix. Utilice el proxy pasivo sólo cuando tenga una buena razón para hacerlo.
***

## Monitorizando hosts con el proxy Zabbix
Tenemos nuestros proxies Zabbix activo y pasivo listos para usar, así que ahora es el momento de añadir algunos hosts a ellos. Configurar el frontend de Zabbix para monitorizar hosts con proxies Zabbix funciona más o menos de la misma manera que monitorizar directamente desde el servidor Zabbix. Sin embargo, el backend y el diseño cambian completamente, lo que explicaré en la sección Cómo funciona... de esta receta.

### Preparación
Asegúrese de tener listos su proxy pasivo `lar-book-proxy-passive` y su proxy activo `lar-book-proxy-active` siguiendo todas las recetas anteriores de este capítulo.

También necesitarás tu servidor Zabbix y al menos dos hosts para monitorizar. Usaremos `lar-book-agent_snmp` y `lar-book-agent` en el ejemplo, pero cualquier host con un agente Zabbix activo y pasivo funcionará.

### Cómo hacerlo...
Configuraremos un host tanto en nuestro proxy activo como en nuestro proxy pasivo para mostrarte cuál es la diferencia entre ambos. Empecemos con el proxy pasivo.

#### Proxy pasivo
1. Vamos a empezar esta receta iniciando sesión en nuestro frontend Zabbix y navegando a Configuración | Hosts.
2. Añadimos el host con el agente pasivo a nuestro proxy pasivo. En mi caso, este es el host `lar-book-agent_snmp`.

3. Haga clic en el host `lar-book-agent_snmp` y cambie el campo Monitorizado por proxy a `lar-book-proxy-passive`, como en la siguiente captura de pantalla:
<p align = "center">
   <img src = "https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_08_012.jpg" alt="Figura 8.12- Configuración | Hosts, página Editar host para el host lar-book-agent_snmp">
</p>
<p align = "center">Figura 8.12- Configuración | Hosts, página Editar host para el host lar-book-agent_snmp</p>

4. Ahora, haga clic en el botón azul Actualizar. Nuestro host será ahora monitorizado por el proxy Zabbix.

**Nota importante**
Debido al intervalo de actualización de la configuración de 1 hora, podemos ver que el icono SNMP se vuelve gris temporalmente, y puede tardar hasta 1 hora antes de que la monitorización continúe. Sea paciente o fuerce una recarga de la caché de configuración tanto en el servidor Zabbix como en el proxy Zabbix.

#### Proxy activo
1. Hagamos lo mismo para nuestro otro host `lar-book-agent` navegando de nuevo a Configuración | Hosts.



2. Haga clic en el host `lar-book-agent` y edite el campo Monitorizado por proxy a `lar-book-proxy-active`, como en la siguiente captura de pantalla:
<p align = "center">
   <img src = "https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_08_013.jpg" alt="Figura 8.13 - Configuración | Hosts, página Editar host para el host lar-book-agent">
</p>
<p align = "center">Figura 8.13 - Configuración | Hosts, página Editar host para el host lar-book-agent</p>

3. Ahora, haga clic en el botón azul Actualizar.
4. En la CLI de nuestro host Linux `lar-book-agent` monitorizado, ejecute el siguiente comando:
```bash
vim /etc/zabbix/zabbix_agent2.conf
```

5. Cuando trabajamos con un agente Zabbix activo necesitamos asegurarnos de añadir la dirección IP del proxy en la siguiente línea:
```bash
ServerActive=
```

Nuestro host será ahora monitorizado por el proxy Zabbix. Una vez más, esto puede tardar hasta una hora.

### Cómo funciona...
Monitorizar hosts con un proxy Zabbix en modo pasivo o activo funciona de la misma manera desde el frontend. Simplemente configuramos qué host es monitorizado por qué proxy en nuestro frontend de Zabbix, y listo.

Veamos como nuestro agente SNMP (Simple Network Management Protocol) es ahora monitorizado por el proxy pasivo:
<p align = "center">
   <img src = "https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_08_014.jpg" alt="Figura 8.14 - Una configuración Zabbix completamente pasiva con proxy">
</p>
<p align = "center">Figura 8.14 - Una configuración Zabbix completamente pasiva con proxy</p>

Nuestro proxy Zabbix pasivo ahora recoge los datos de nuestro agente SNMP, y después de esto, el servidor Zabbix recoge estos datos de nuestro proxy Zabbix. Ya suena como todo un proceso, ¿verdad?

Veamos la configuración de nuestro proxy Zabbix activo:
<p align = "center">
   <img src = "https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_08_015.jpg" alt="Figura 8.15 - Una configuración de Zabbix completamente activa con proxy">
</p>
<p align = "center">Figura 8.15 - Una configuración de Zabbix completamente activa con proxy</p>

Nuestro proxy Zabbix activo recibe datos de nuestro agente Zabbix activo y luego envía estos datos a nuestro servidor Zabbix. Hemos eliminado todos los temporizadores en esta configuración de proxy por completo y ahora estamos recibiendo todos nuestros datos en el servidor Zabbix tan pronto como esté disponible.

Esta es la razón por la que siempre recomiendo trabajar con proxies activos -e incluso agentes activos- tanto como sea posible. Si nos fijamos en la siguiente captura de pantalla, podemos ver una configuración que se podría ver en una empresa:
<p align = "center">
   <img src = "https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_08_016.jpg" alt="Figura 8.16 - Configuración de un proxy Zabbix activo con diferentes tipos monitorizados">
</p>
<p align = "center">Figura 8.16 - Configuración de un proxy Zabbix activo con diferentes tipos monitorizados</p>

Afortunadamente, tenemos la opción de utilizar un montón de diferentes combinaciones de configuraciones. Es perfectamente posible -e incluso lógico- combinar sus comprobaciones desde un proxy, tanto como lo sería con un servidor Zabbix. Podemos monitorizar todos los tipos desde nuestro proxy, ya sea un agente Zabbix, SNMP, o incluso Java Management Extensions (JMX) y la Intelligent Platform Management Interface (IPMI).

**Sugerencia**
Al diseñar una nueva infraestructura de alojamiento Zabbix, comience con la adición de proxies si es posible. De esta manera, usted no tiene que cambiar mucho más tarde. Es fácil añadir y cambiar proxies, pero es más difícil pasar de sólo usar el servidor Zabbix a usar proxies Zabbix en su diseño.

### Hay más...
Ahora tenemos una configuración sólida con algunos proxies en funcionamiento. Hemos entendido la diferencia entre proxies activos y pasivos y cómo afectan a la monitorización. Pero, ¿por qué construir una configuración como esta? Bueno, los proxies Zabbix son geniales para muchos entornos - no sólo los grandes, sino incluso a veces en los más pequeños.

Podemos utilizar proxies Zabbix para descargar el sondeo y el preprocesamiento de nuestro servidor Zabbix principal, manteniendo así el servidor libre para el manejo de datos como cuando se escribe en la base de datos Zabbix.

Podemos usar proxies Zabbix para monitorizar ubicaciones externas, como cuando eres un Proveedor de Servicios Gestionados (MSP) y quieres monitorizar una gran red de clientes. Simplemente colocamos un proxy in situ y lo monitorizamos. Podemos utilizar técnicas estándar de la industria como el monitoreo a través de una red privada virtual (VPN) o simplemente establecer una conexión utilizando el cifrado de proxy Zabbix incorporado.

Cuando la VPN se caiga, nuestro proxy seguirá recopilando datos in situ y los enviará a nuestro servidor Zabbix cuando la VPN vuelva a funcionar.

También podemos utilizar el proxy Zabbix para eludir las complicaciones del cortafuegos. Cuando colocamos un proxy detrás de un cortafuegos en una red monitorizada, sólo necesitamos una regla de cortafuegos entre el servidor Zabbix y el proxy Zabbix. Nuestro proxy Zabbix entonces monitorea los diferentes hosts y envía los datos recogidos en un flujo al servidor Zabbix.

Con esto, ya tienes un montón de opciones para utilizar proxies Zabbix.

### Ver también
Echa un vistazo a esta interesante entrada del blog de Dmitry Lambert sobre algunas ventajas ocultas de los proxies Zabbix: https://blog.zabbix.com/hidden-benefits-of-zabbix-proxy/9359/.

Dmitry es un experimentado ingeniero de Zabbix y jefe de soporte al cliente de Zabbix. Sus entradas en el blog son fáciles de entender y proporcionan algunos nuevos ángulos desde los que mirar a Zabbix.

***

## Uso de la detección con proxies Zabbix
En el [capítulo 7](Pendiente), Uso del descubrimiento para la creación automática, hablamos sobre Zabbix y el descubrimiento. Es una muy buena idea editar tus reglas de descubrimiento si seguiste ese capítulo. Veamos cómo funcionaría en esta receta.

### Preparación
Necesitará haber terminado el [capítulo 7](Pendiente), Uso de la detección para la creación automática, o tener configuradas algunas reglas de detección y autorregistro de agentes activos.

En este ejemplo utilizaré los hosts `lar-book-lnx-agent-auto`, `lar-book-disc-lnx` y `lar-book-disc-win`. También necesitaremos nuestro servidor Zabbix.

### Cómo hacerlo...
Comencemos editando nuestra regla de descubrimiento y luego pasamos a editar nuestro agente activo para auto registrarse en el proxy.
#### Reglas de descubrimiento
Comenzando con las reglas de descubrimiento de Zabbix, vamos a ver cómo asegurarnos de que hacemos esto desde el proxy de Zabbix:

1. Inicie sesión en la CLI de `lar-book-disc-lnx` y edite el archivo `/etc/zabbix/` ``zabbix_agent2.conf``. Edite las siguientes líneas para incluir nuestra dirección de proxy Zabbix:
```bash
Server=127.0.0.1,10.16.16.152,10.16.16.160,10.16.16.161
ServerActive=10.16.16.160
```
2. Reinicie su agente Zabbix ejecutando el siguiente comando:
```bash
systemctl restart zabbix-agent2
```
3. Ahora, asegúrese de iniciar sesión en `lar-book-disc-win` y editar el archivo `C:\Program Files\Zabbix agent\zabbix_agent2`. Edite las siguientes líneas para incluir nuestra dirección proxy Zabbix:
```bash
Server=127.0.0.1,10.16.16.152,10.16.16.160,10.16.16.161
ServerActive=10.16.16.160
```
**Nota importante**
En las líneas `ServerActive` de nuestros ficheros de configuración, asegúrate de incluir sólo el proxy Zabbix al que queremos enviar datos. El agente Zabbix intentará activamente enviar datos a todos nuestros proxies Zabbix o servidores Zabbix listados aquí, por lo que sólo debemos listar el que queremos usar.

4. Reinicie su agente Zabbix ejecutando los siguientes comandos en la línea de comandos de Windows:
```bash
zabbix_agent2.exe --stop
zabbix_agent2.exe --start
```
5. A continuación, vaya a Configuración | Hosts y elimine los hosts descubiertos:
```bash
lar-book-disc-lnx
lar-book-disc-win
```
Hacemos esto para evitar la duplicación de hosts.

6. Ahora, navegue a Configuración | Descubrimiento.
7. Haga clic en Discover Zabbix Agent hosts y cambie el campo Discovered by proxy, como se muestra en la siguiente captura de pantalla:
<p align = "center">
   <img src = "https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_08_017.jpg" alt="Figura 8.17 - Configuración | Acciones, menú desplegable para la detección por proxy lar-book-proxy-active">
</p>
<p align = "center">Figura 8.17 - Configuración | Acciones, menú desplegable para la detección por proxy lar-book-proxy-active</p>

8. Pulse el botón azul Actualizar, y eso es todo lo que hay que hacer para editar su regla de descubrimiento para que sea monitorizada por un proxy.
9. Ahora puede comprobar sus hosts recién descubiertos en Configuración | Hosts y ver que son monitorizados por el proxy:
<p align = "center">
   <img src = "https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_08_018.jpg" alt="Figura 8.18 - Pantalla Configuración | Hosts para hosts descubiertos">
</p>
<p align = "center">Figura 8.18 - Pantalla Configuración | Hosts para hosts descubiertos</p>

#### Auto registro de agentes activos
Pasando al auto registro activo de agentes, veamos cómo podemos hacerlo desde nuestro proxy Zabbix:

1. Empezaremos navegando a Configuración | Hosts y borrando `lar-book-lnx- agent-auto`.
2. Para hacer el auto registro de agente activo a un proxy, tenemos que entrar en el CLI de nuestro host `lar-book-lnx-agent-auto`.
3.Edite el archivo de configuración del agente Zabbix con el siguiente comando:
```bash
vim /etc/zabbix/zabbix_agent2.conf
```

4. Asegúrese de editar la siguiente línea a la dirección del proxy Zabbix en lugar de la dirección del servidor Zabbix:
```bash
ServerActive=10.16.16.160
```
5. Reinicie el agente Zabbix:
```bash
systemctl restart zabbix-agent2
```
6. Ahora podemos ver nuestro host auto registrarse al proxy Zabbix en lugar de al servidor Zabbix:
<p align = "center">
   <img src = "https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_08_018.jpg" alt="Figura 8.19 - Pantalla Configuración | Hosts para nuestros dos hosts auto-registrados">
</p>
<p align = "center">Figura 8.19 - Pantalla Configuración | Hosts para nuestros dos hosts auto-registrados</p>

## Cómo funciona...
El descubrimiento con un proxy Zabbix funciona exactamente igual que el descubrimiento con un servidor Zabbix. Lo único que cambia es la ubicación de donde nos estamos registrando o descubriendo.

Si quieres aprender más sobre el proceso de descubrimiento y registro automático, consulta el [Capítulo 7](Pendiente), Uso del descubrimiento para la creación automática, si aún no lo has hecho.
***

## Monitorización de sus proxies Zabbix
Muchos usuarios de Zabix o incluso usuarios de monitorización en general olvidan una parte muy importante de su monitorización. Se olvidan de monitorizar la infraestructura de monitorización. Quiero asegurarme de que cuando configure proxies Zabbix, también sepa cómo monitorizar la salud de estos proxies.

Vamos a ver cómo hacerlo en esta receta.

### Preparándonos
Para esta receta, necesitaremos nuestro nuevo proxy `lar-book-proxy-active` Zabbix. También necesitaremos nuestro servidor Zabbix para monitorizar el proxy Zabbix.

### Cómo hacerlo...
Vamos a construir algo de monitorización en nuestro frontend de Zabbix, pero también comprobaremos las opciones de monitorización integradas para los proxies de Zabbix. Empecemos por construir el nuestro.

#### Monitorización del proxy con Zabbix
Podemos monitorizar nuestro proxy Zabbix con el propio proxy Zabbix para asegurarnos de que sabemos exactamente lo que está pasando:

1. Vamos a empezar por iniciar sesión en nuestro `lar-book-proxy-activo` Zabbix proxy CLI.
2. Emita el siguiente comando para instalar el agente 2 de Zabbix para sistemas basados en RHEL:
```bash
dnf install zabbix-agent2
```
Para Ubuntu, ejecute este comando:
```bash
apt install zabbix-agent2
```
3. Edite el archivo de configuración del agente Zabbix mediante el siguiente comando:
```bash
vim /etc/zabbix/zabbix_agent2.conf
```
4. Edite las siguientes líneas para que apunten hacia `localhost`:
```bash
Server=127.0.0.1
ServerActive=127.0.0.1
```
5. Además, asegúrese de añadir el nombre de host al archivo del agente de Zabbix:
```bash
Hostname=lar-book-proxy-active
```
6. Ahora, inicia sesión en el frontend de Zabbix y navega a Configuración | Hosts.
7. Haz clic en el botón azul Crear host en la esquina superior derecha y añade el siguiente host:
<p align = "center">
   <img src = "https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_08_020.jpg" alt="Figura 8.20 - Configuración | Hosts, página Crear host, lar-book-proxy-active">
</p>
<p align = "center">Figura 8.20 - Configuración | Hosts, página Crear host, lar-book-proxy-active</p>

8. Tenga especial cuidado en el campo **Monitorizado por proxy** queremos monitorizar desde el proxy porque estamos haciendo comprobaciones internas de Zabbix, que necesitan ser manejadas por el demonio de Zabbix que recibió esta configuración.
9. Antes de pulsar el botón azul **Añadir**, asegúrese de añadir las Plantillas. Añada las siguientes plantillas al host:
<p align = "center">
   <img src = "https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_08_021.jpg" alt="Figura 8.21 - Configuración | Hosts, página Crear host Pestaña de plantillas para el host lar-book-proxy-active">
</p>
<p align = "center">Figura 8.21 - Configuración | Hosts, página Crear host Pestaña de plantillas para el host lar-book-proxy-active</p>

10. Ahora podemos pulsar el botón azul **Añadir **para crear el host.
11. Ahora, vaya a **Monitorización **| **Últimos datos** y añada los siguientes filtros:
<p align = "center">
   <img src = "https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_08_022.jpg" alt="Figura 8.22 - Monitorización | Página de últimos datos con filtros, host lar-book-proxy-active">
</p>
<p align = "center">Figura 8.22 - Monitorización | Página de últimos datos con filtros, host lar-book-proxy-active</p>

Ahora podemos ver las estadísticas de nuestro proxy Zabbix, tales como Número de valores procesados por segundo y Utilización de los procesos internos del sincronizador de configuración:
<p align = "center">
   <img src = "https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_08_023.jpg" alt="Figura 8.23 - Monitorización | Página de últimos datos con datos de nuestro proxy Zabbix">
</p>
<p align = "center">Figura 8.23 - Monitorización | Página de últimos datos con datos de nuestro proxy Zabbix</p>

### Monitorizando el proxy remotamente desde nuestro servidor Zabbix
También podemos monitorizar nuestro proxy Zabbix remotamente desde nuestro servidor Zabbix, así que veamos cómo funciona:

1. Vamos a empezar por iniciar sesión en nuestro lar-book-proxy-active host CLI y editar el siguiente archivo:
```bash
vim /etc/zabbix/zabbix_agent2.conf
```
2. Edite las siguientes líneas para que coincidan con la dirección de su servidor Zabbix (cada nodo en un clúster HA):
```bash
Server=127.0.0.1,10.16.16.152
ServerActive=127.0.0.1,10.16.16.152
```
3.Además, edite el siguiente archivo:
```bash
vim /etc/zabbix/zabbix_proxy.conf
```
4.Edite la siguiente línea para que coincida con la dirección de su servidor Zabbix:
```bash
StatsAllowedIP=127.0.0.1,10.16.16.152
```
5. Ahora, navegue a la interfaz de Zabbix y vaya a Configuración | anfitriones _
6. Haga clic en el botón azul Crear host en la esquina superior derecha y agregue el siguiente host
<p align = "center">
   <img src = "https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_08_024.jpg" alt="Figura 8.24 – Configuración | Hosts, Crear página de host, lar-book-proxy-active_remotely">
</p>
<p align = "center">Figura 8.24 – Configuración | Hosts, Crear página de host, lar-book-proxy-active_remotely</p>

7.Antes al hacer clic en el botón azul Agregar , asegúrese de agregar las Plantillas correctas . Agregue las siguientes plantillas al host:
<p align = "center">
   <img src = "https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_08_025.jpg" alt="Figura 8.25 – Configuración | Hosts, crear nueva página de host, pestaña Plantillas, lar-book-proxy-active_ de forma remota">
</p>
<p align = "center">Figura 8.25 – Configuración | Hosts, crear nueva página de host, pestaña Plantillas, lar-book-proxy-active_ de forma remota</p>


8. Ahora podemos hacer clic en el botón azul Agregar para crear el host.
9. De vuelta en Configuración | Hosts , haga clic en su nuevo lar-book-proxy-active_remotelyhost.
10. Vaya a Macros y agregue las siguientes dos macros:
<p align = "center">
   <img src = "https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_08_026.jpg" alt="Figura 8.26 – Configuración | Hosts, Editar página de host, pestaña Macros, lar-book-proxy-active_remotely">
</p>
<p align = "center">Figura 8.26 – Configuración | Hosts, Editar página de host, pestaña Macros, lar-book-proxy-active_remote</p>

11. Ahora, haga clic en el botón azul Actualizar , y eso es todo para este host.
12. Navegar a Supervisión | Últimos datos y verifique los elementos para este host, podemos ver los datos que ingresan:
<p align = "center">
   <img src = "https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_08_027.jpg" alt="Figura 8.27 – Monitoreo | Última página de datos para host lar-book-proxy-active_remotel">
</p>
<p align = "center">Figura 8.27 – Monitoreo | Última página de datos para host lar-book-proxy-active_remotel</p>


13. Monitoreo del proxy desde la interfaz de Zabbix
14. Vamos a  Administración | cola
15. Utilice el menú desplegable para ir a la descripción general de la cola por proxy:
<p align = "center">
   <img src = "https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_08_028.jpg" alt="Figura 8.28 - Menú desplegable de la página Administration | Queue">
</p>
<p align = "center">Figura 8.28 - Menú desplegable de la página Administration | Queue</p>

Esto nos llevará a la página que se muestra en la siguiente captura de pantalla:
<p align = "center">
   <img src = "https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_08_029.jpg" alt="Figura 8.29 - Página Administración | Cola de espera">
</p>
<p align = "center">Figura 8.29 - Página Administración | Cola de espera</p>

### Cómo funciona...Cómo funciona...
Monitorizar sus proxies Zabbix es una tarea importante, por lo que necesitamos asegurarnos de que cada vez que añadimos un nuevo proxy Zabbix, estamos cuidando de él como lo haríamos con cualquier otro host.

#### Monitorizando el proxy con Zabbix
Al añadir el proxy Zabbix como un host, podemos asegurarnos de que el sistema Linux que está ejecutando nuestro proxy Zabbix es saludable. También nos aseguramos de que las aplicaciones del proxy Zabbix que se ejecutan en este servidor están en buen estado.

Además de tener los disparadores adecuados en estas plantillas, también obtenemos un montón de opciones para solucionar problemas con el proxy Zabbix.

El proxy Zabbix funciona igual que el servidor Zabbix cuando se trata de monitorización. Esto significa que al igual que con el servidor Zabbix, tenemos que mantener los proxies en gran salud ajustando el archivo de configuración del proxy Zabbix a nuestras necesidades.

El escalado de sus proxies se vuelve mucho más fácil una vez que averiguar lo que está pasando con ellos. Por eso nos aseguramos de monitorizarlos siempre. Los monitorizamos desde el propio proxy para asegurarnos de que obtenemos la información correcta con las comprobaciones internas de Zabbix.

#### Monitorizando el proxy remotamente desde nuestro servidor Zabbix
Ahora, cuando monitorizamos con Remote Zabbix la salud del proxy, las cosas van un poco diferentes. En lugar de hacer nuestras comprobaciones desde el propio proxy Zabbix, las hacemos remotamente desde el servidor Zabbix definiendo la dirección y el puerto del proxy Zabbix en las macros. El tipo de comprobación interna de Zabbix se seguirá utilizando para esto, ejecutando las comprobaciones desde el servidor Zabbix al proxy Zabbix remotamente en este caso.

Además de esto, por supuesto se sigue recomendando mantener nuestro agente Zabbix ejecutándose en modo pasivo o activo.
<p align = "center">
   <img src = "https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_08_030.jpg" alt="Figura 8.30 - Agente Zabbix ejecutándose en el proxy Zabbix monitorizado por el servidor Zabbix">
</p>
<p align = "center">Figura 8.30 - Agente Zabbix ejecutándose en el proxy Zabbix monitorizado por el servidor Zabbix</p>

De esta forma, nuestro servidor Zabbix es el que solicita y recibe información. Incluso cuando el proxy tenga problemas, las comprobaciones las seguirá haciendo el servidor Zabbix.

Podemos utilizar esta plantilla como una forma de vigilar más de cerca nuestro proxy si sospechamos problemas con las comprobaciones internas que se realizan localmente, o podemos utilizar esta plantilla para eludir ciertas configuraciones de cortafuegos. Ambas son razones válidas.

#### Monitorizando el proxy desde el frontend de Zabbix
Desde el frontend, podemos usar la página Administration | Queue para monitorizar nuestros proxies. La página Zabbix Queue es una página importante, pero muchos de los nuevos usuarios no conocen ni utilizan completamente esta página.

Cuando una parte de Zabbix empieza a funcionar mal, como nuestro proxy Zabbix de ejemplo aquí, es cuando podemos ver las cosas que pasan en la cola. Hay seis opciones en la cola de Zabbix:::
- 5 segundos
- 10 segundos
- 30 segundos
- 1 minuto
- 5 minutos
- Más de 10 minutos

Lo que significan las opciones de la cola es que el proxy de Zabbix ha estado esperando a recibir un valor configurado más de lo esperado. Yo diría que hasta 1 minuto no tiene por qué ser un problema, pero esto depende del tipo de comprobación. Las opciones de 5 minutos o Más de 10 minutos pueden significar serios problemas de rendimiento con su proxy Zabbix, y usted tendría que solucionar este problema. Asegúrese de mantener un buen ojo en la cola de Zabbix cuando sospeche problemas, que también se incluyen como disparadores en las plantillas que hemos añadido para supervisar nuestros proxies Zabbix.
