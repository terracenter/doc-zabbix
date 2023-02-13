### Capítulo 3: Configuración de la monitorización de Zabbix
Zabbix está construido para ser flexible y debe ser capaz de monitorizar casi cualquier cosa que pueda necesitar. En este capítulo, vamos a aprender más acerca de cómo trabajar con Zabbix para construir un montón de diferentes opciones para la monitorización. Vamos a ir sobre ellos receta por receta, por lo que va a terminar con una sólida comprensión de cómo funcionan. Cubriremos las siguientes recetas sobre los diferentes tipos de monitorización:
- Configuración de la monitorización del agente Zabbix
- Trabajando con monitorización SNMP
- Creación de comprobaciones simples Zabbix y atrapador (trapper) Zabbix
- Trabajo con elementos calculados y dependientes
- Creación de comprobaciones externas
- Configuración de la monitorización JMX
- Configuración de la monitorización de bases de datos
- Configuración de la monitorización del agente HTTP
- Uso del preprocesamiento de Zabbix para modificar los valores de los elementos

## Requisitos técnicos
Necesitaremos un servidor Zabbix capaz de realizar monitorización, con los siguientes requisitos:
- Un servidor con el servidor Zabbix instalado en una distribución Linux de su elección, como CentOS o Ubuntu, pero una distribución como Debian, Rocky Linux, o cualquier otra le vendrá igual de bien
- Un servidor MariaDB para monitorizar

Utilizaré el mismo servidor que usamos en el capítulo anterior, pero cualquier servidor Zabbix puede servir.

## Configuración de la monitorización del agente 2 de Zabbix
A partir del lanzamiento de Zabbix 5, Zabbix también inició oficialmente el soporte para el nuevo agente Zabbix 2. Zabbix agente 2 trae algunas mejoras importantes e incluso está escrito en otro lenguaje de codificación, que es Golang en lugar de C.  En esta receta, vamos a explorar cómo trabajar con Zabbix agente 2 y explorar algunas de las nuevas características introducidas por ella.

### Preparándonos
Para empezar con el agente 2 de Zabbix, todo lo que necesitamos hacer es instalarlo en un host que queramos monitorizar. Asegúrate de que tienes un host vacío basado en Red Hat Enterprise Linux (RHEL) o Ubuntu Linux listo para monitorizar.

### Cómo hacerlo...
Vamos a ver cómo instalar Zabbix agente 2 y luego pasar a trabajar realmente con él.

#### Instalando el agente 2 de Zabbix
Comencemos instalando el agente 2 de Zabbix en el host Linux que queremos monitorizar. Os mostraré cómo hacer los pasos tanto en sistemas RHEL como Ubuntu:

1. Ejecuta el siguiente comando para añadir el repositorio:
Para sistemas basados en RHEL:
```bash
rpm -Uvh https://repo.zabbix.com/zabbix/6.0/rhel/8/x86_64/zabbix-release-6.0-1.el8.noarch.rpm
```
Para sistemas Ubuntu:
```bash
wget https://repo.zabbix.com/zabbix/6.0/ubuntu/pool/main/z/zabbix-release/zabbix-release_6.0-1+ubuntu20.04_all.deb
dpkg -i zabbix-release_6.0-1+ubuntu20.04_all.deb
```
2. A continuación, ejecute el siguiente comando para instalar el agente 2 de Zabbix:
Para sistemas basados en RHEL:
```bash
dnf -y install zabbix-agent2
```
Para sistemas Ubuntu:
```bash
apt install zabbix-agent2
```
Enhorabuena, el agente 2 de Zabbix ya está instalado y listo para usar.

**Nota importante** 
</br>
Cuando añadas nuevos repositorios a tu sistema, consulta siempre la página de descargas de Zabbix. Aquí podemos encontrar el repositorio actualizado adecuado para nuestro sistema:
https://www.zabbix.com/download

#### Uso de un agente Zabbix en modo pasivo
Empecemos construyendo un agente Zabbix con comprobaciones pasivas:

1. Después de instalar el agente 2 de Zabbix, vamos a abrir el fichero de configuración del agente Zabbix para editarlo:
```bash
vim /etc/zabbix/zabbix_agent2.conf
```
En este archivo, editamos todos los valores de configuración del agente Zabbix que podríamos necesitar desde el lado del servidor.

3. Empecemos por editar los siguientes parámetros
```bash
Server=127.0.0.1
Hostname=Zabbix server
```

3. Cambie el valor de Server por la IP del servidor Zabbix que monitorizará este agente pasivo. Cambiar el valor de Hostname por el nombre de host del servidor monitorizado. Podemos obtener la dirección IP de nuestro servidor con el siguiente comando:
```bash
ip addr
```

4.  Ahora reinicie el proceso del agente 2 de Zabbix:
```bash
systemctl enable zabbix-agent2
systemctl restart zabbix-agent2
```

5. A continuación, vaya al frontend de su servidor Zabbix y añada este host para su monitorización.

6. Vaya a Configuración | Hosts en su frontend Zabbix y haga clic en Crear host en la esquina superior derecha.

7. Para crear este host en nuestro servidor Zabbix, tenemos que rellenar los valores como se ve en la siguiente captura de pantalla
<p align = "center">
   <img src = "https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_03_001.jpg" alt=" Figura 3.1 - Página de creación de host Zabbix para el host lar-book-agent">
</p>   
<p align = "center">
Figura 3.1 - Página de creación de host Zabbix para el host lar-book-agent   
</p>

**Es importante añadir lo siguiente**

 - **Nombre del Host**: Para identificar este host
 -  **Grupos**: Para agrupar hosts de forma lógica
 - **Interfaces**: Para supervisar este host en una interfaz específica. Sin interfaz no hay comunicación. Es posible tener un host sin interfaz en Zabbix 6, si no lo necesitamos. En el caso de un host monitorizado por un agente Zabbix, necesitamos una interfaz de agente.

8. Asegúrese de añadir la dirección IP correcta a la configuración de la interfaz del agente.
9. También es importante añadir una plantilla a este host, que con Zabbix 6 debe hacerse en la misma pestaña. Como se trata de un servidor Linux monitorizado por un agente Zabbix, vamos a añadir la plantilla out-of-the-box correcta, como se muestra en la siguiente captura de pantalla:
<p align = "center">
   <img src = "https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_03_002.jpg" alt="Figura 3.2 - Página de plantilla de host Zabbix para el host lar-book-agent">
</p>   
<p align = "center">  
   Figura 3.2 - Página de plantilla de host Zabbix para el host lar-book-agent
</p>
10. Pulsa el botón azul Añadir y habrás terminado de crear este host agente. Ahora que tienes este host, asegúrate de que el icono ZBX se vuelve verde, indicando que este host está activo y siendo monitorizado por el agente pasivo de Zabbix:
<p align = "center">
   <img src = "https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_03_003.jpg" alt="Figura 3.3 - Página de configuración de hosts de Zabbix, host lar-book-agent">
</p>   
<p align = "center">
  Figura 3.3 - Página de configuración de hosts de Zabbix, host lar-book-agent
</p>
11. Como hemos configurado nuestro host y añadido una plantilla con ítems, ahora podemos ver los valores recibidos en los ítems para este host yendo a **Monitorización** | **Hosts** y marcando el botón **Últimos datos**. Tenga en cuenta que los valores pueden tardar un poco en aparecer:
<p align = "center">
   <img src = "https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_03_004.jpg" alt="Figura 3.4 - Página de últimos datos de Zabbix para el host lar-book-agent">
<p>
<p align = "center">  
   Figura 3.4 - Página de últimos datos de Zabbix para el host lar-book-agent
</p>

------------



### Usando un agente Zabbix en modo activo
Ahora vamos a ver cómo configurar el agente Zabbix con comprobaciones activas. Necesitamos cambiar algunos valores en el lado del host del servidor Linux monitorizado:

1. Comience ejecutando el siguiente comando:
```bash
vim /etc/zabbix/zabbix_agent2.conf
```

2. Ahora vamos a editar el siguiente valor para cambiar este host a un agente activo:
```bash
ServerActive=127.0.0.1
```

3. Cambia el valor de **ServerActive** por la IP del servidor Zabbix que monitorizará este agente pasivo y cambia también el valor de **Hostname** por **lar-book-agent**:
```bash
Hostname=lar-book-agent
```
**Nota importante**
Ten en cuenta que si estás trabajando con múltiples servidores Zabbix o proxies Zabbix, por ejemplo cuando estás ejecutando el servidor Zabbix en alta disponibilidad, necesitas rellenar todas las direcciones IP de los servidores Zabbix o proxies Zabbix en el parámetro **ServerActive**. Los nodos de **Alta Disponibilidad (HA)** están delimitados por un punto y coma (;) y las diferentes IPs del entorno Zabbix por una coma (,).

4. Ahora reinicia el proceso del agente 2 de Zabbix
```bash
systemctl restart zabbix-agent2
```

5. A continuación, vaya al frontend de su servidor Zabbix y vamos a añadir otro host con una plantilla para hacer comprobaciones activas en lugar de pasivas.
6. En primer lugar, vamos a cambiar el nombre de nuestro host pasivo. Para ello, vaya a **Configuración** | **Hosts** en su frontend Zabbix y haga clic en el host que acabamos de crear. Cambia el **nombre del host** como sigue:
<p align = "center">
   <img src = "https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_03_005.jpg" alt="Figura 3.5 - Página de configuración del host Zabbix lar-book-agent_passive">
</p>
    <p align = "center">
    Figura 3.5 - Página de configuración del host Zabbix lar-book-agent_passive
</p>
Hacemos esto porque para un agente Zabbix activo, el nombre de host en el archivo de configuración del agente Zabbix necesita coincidir con la configuración de nuestro host como se ve en el frontend de Zabbix.

7. Haga clic en el botón azul **Actualizar** para guardar los cambios.
8. Vaya a **Configuración** | **Hosts** en su frontend Zabbix y haga clic en **Crear** host en la esquina superior derecha.
9. Ahora vamos a crear el host de la siguiente manera:
<p align = "center">
   <img src = "https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_03_006.jpg" alt="Figura 3.6 - Página de configuración de host Zabbix para el host lar-book-agent">
</p>
<p align = "center">
    Figura 3.6 - Página de configuración de host Zabbix para el host lar-book-agent
</p>
10. Además, asegúrese de añadir la plantilla correcta, llamada `Linux por el agente de Zabbix activo`:
<p align = "center">
   <img src = "https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_03_007.jpg" alt="Figura 3.7 - Página de plantilla de host Zabbix para el host lar-book-agent">
Figura 3.7 - Página de plantilla de host Zabbix para el host lar-book-agent   
</p>
Tenga en cuenta que el icono ZBX no se pondrá verde para un agente activo. Pero cuando navegamos a Monitorización | Hosts y comprobamos Últimos datos, podemos ver nuestros datos activos entrando.

**Consejo**
Como se habrá dado cuenta hace un momento, un agente Zabbix puede funcionar en modo pasivo y activo al mismo tiempo. Tenga esto en cuenta cuando cree sus propias plantillas de agente Zabbix, ya que a veces puede querer combinar los tipos de comprobación.

## Cómo funciona...
Ahora que ya hemos configurado nuestros agentes Zabbix y sabemos cómo deben estar configurados, vamos a ver cómo funcionan los diferentes modos.

### Agente pasivo
El **agente pasivo** funciona recogiendo datos de nuestro host con el agente Zabbix. Cada vez que un elemento de nuestro host alcanza su intervalo de actualización, el servidor Zabbix pregunta al agente Zabbix cuál es el valor actual:
<p align = "center">
   <img src = "https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_03_008.jpg" alt="Figura 3.8 - Diagrama de comunicación entre el servidor y el agente pasivo">
   Figura 3.8 - Diagrama de comunicación entre el servidor y el agente pasivo
</p>

Los agentes pasivos son geniales cuando se trabaja en entornos en los que se quiere mantener la comunicación iniciada desde el lado del servidor Zabbix o del proxy Zabbix, por ejemplo, cuando hay un cortafuegos que sólo permite el tráfico saliente, visto desde el lado del servidor Zabbix o del proxy.

### Agente activo
El agente activo funciona enviando datos desde el agente Zabbix a un servidor Zabbix o proxy Zabbix. Cada vez que un elemento de nuestro agente alcance su intervalo de actualización, el agente enviará el valor a nuestro servidor.
<p align = "center">
   <img src = "https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_03_009.jpg" alt="Figura 3.9 - Diagrama de comunicación entre el servidor y el agente activo">
   Figura 3.9 - Diagrama de comunicación entre el servidor y el agente activo
</p>

El agente activo es genial cuando se trabaja con un entorno en el que hay un cortafuegos que sólo acepta conexiones salientes, como se ve desde el lado del agente de Zabbix. Este es el caso en muchos entornos, y puede mitigar una de las principales preocupaciones de seguridad que se asocia principalmente con la monitorización de hosts.

Por otro lado, el agente Zabbix trabajando en modo activo también puede ser mucho más eficiente. La mayor parte de la carga que viene de obtener datos a su servidor Zabbix está ahora en el lado del agente Zabbix. Debido a que hay más agentes Zabbix que servidores Zabbix o proxies, descargar la carga de esta manera es una gran idea.

Como hemos mencionado, podemos utilizar ambos tipos de comprobaciones al mismo tiempo, dándonos la libertad de configurar cada tipo de comprobación que podamos necesitar. Nuestra configuración se vería así:
<p align = "center">
   <img src = "https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_03_010.jpg" alt="Figura 3.10 - Diagrama de comunicación entre el servidor y los dos tipos de agentes">
   Figura 3.10 - Diagrama de comunicación entre el servidor y los dos tipos de agentes
</p>
Este podría ser el caso en situaciones en las que queremos monitorizar principalmente de forma pasiva, pero, por ejemplo, la monitorización de ficheros log con el agente Zabbix debe hacerse con un agente Zabbix activo. Entonces podemos combinar nuestros modos y asegurarnos de utilizar la escala completa de nuestras características proporcionadas en el agente Zabbix.

## Ver también
Hay un montón de cosas pasando bajo el capó del agente Zabbix 2; si estás interesado en aprender más sobre el núcleo del agente Zabbix 2, echa un vistazo a esta interesante entrada del blog de Alexey Petrov: https://blog.zabbix.com/magic-of-new-zabbix-agent/8460/.

------------

# Trabajando con monitorización SNMP
Ahora vamos a hacer algo de lo que más disfruto cuando trabajo con Zabbix: construir monitorización SNMP. Mis raíces profesionales están en la ingeniería de redes, y he trabajado mucho con monitorización SNMP para monitorizar todos estos diferentes dispositivos de red.
## Preparación
Para empezar, necesitamos los dos hosts Linux que utilizamos en las recetas anteriores:
- Nuestro host servidor Zabbix
- El host que usamos en la receta anterior para monitorizar a través del agente activo de Zabbix
## Cómo hacerlo...
Monitorizar mediante SNMP polling es fácil y muy potente. Empezaremos configurando SNMPv3 en nuestro host Linux monitorizado:

1. Empecemos emitiendo los siguientes comandos para instalar SNMP en nuestro host.
Para sistemas basados en RHEL:
```bash
apt install snmp snmpd libsnmp-dev
```
Para sistemas Ubuntu:
```bash
apt install snmp snmpd libsnmp-dev
```
2. Ahora, vamos a crear el nuevo usuario SNMPv3 que utilizaremos para monitorizar nuestro host. Tenga en cuenta que vamos a utilizar contraseñas inseguras, pero asegúrese de utilizar contraseñas seguras para sus entornos de producción. Emita el siguiente comando:
```bash
net-snmp-create-v3-user -ro -a my_authpass -x my_privpass -A SHA -X AES snmpv3user
```
Esto creará un usuario SNMPv3 con el nombre de usuario `snmpv3user`, la contraseña de autenticación `my_authpass`, y la contraseña de privilegios `my_ privpass`.

3. Asegúrate de editar el archivo de configuración SNMP para permitirnos leer todos los objetos SNMP:
```bash
vim /etc/snmp/snmpd.conf
```
4.  Añade la siguiente línea en el resto de las líneas de la `view systemview`:
```bash
view    systemview    included   .1
```

5. Ahora inicie y habilite el demonio snmpd para permitirnos empezar a monitorizar este servidor:
```bash
systemctl enable snmpd
systemctl start snmpd
```
Esto es todo lo que necesitamos hacer en el lado del host Linux; ahora podemos ir al frontend de Zabbix para configurar nuestro host. Vaya a **Configuración** | **Hosts** en su frontend Zabbix y haga clic en Crear host en la esquina superior derecha.

6. Ahora rellene la página de configuración del host:
<p align="center" >
	<img src="https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_03_011.jpg" width="auto">
	Figura 3.11 - Página de configuración de host Zabbix para el host lar-book-agent_snmp
</p>

7. No olvides cambiar la dirección IP de la interfaz SNMP por tu propio valor.
8. Asegúrese de añadir la plantilla out-of-the-box correcta como se muestra en la siguiente captura de pantalla:
<p align="center" >
	<img src="https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_03_011.jpg" width="auto">
	<img src="" width="auto">
	Figura 3.12 - Añadir la plantilla Linux SNMP al host
</p>
<p>
**Consejo**
</p>
Al actualizar desde una versión anterior de Zabbix a Zabbix 6, no obtendrás todas las nuevas plantillas out-of-the-box. Si crees que te faltan algunas plantillas, puedes descargarlas en el repositorio Git de Zabbix: https://git.zabbix.com/projects/ZBX/repos/zabbix/browse/templates.

9. Estamos utilizando algunas macros en nuestra configuración aquí para el nombre de usuario y contraseña. Podemos utilizar estas macros para añadir un montón de hosts con las mismas credenciales. Esto es muy útil, por ejemplo, si tienes un montón de switches con las mismas credenciales SNMPv3.

Rellenemos las macros en **Administración** | **General** y utilicemos el desplegable para seleccionar **Macros**. Rellena las macros así:
<p align="center" >
	<img src="https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_03_013.jpg" width="auto">
	Figura 3.13 - Página de macros globales de Zabbix con macros SNMP
</p>

<p>**Consejo**</p>
Una característica interesante en Zabbix 6 es la posibilidad de ocultar macros en el frontend utilizando el tipo de macro Texto secreto. Hay que tener en cuenta que las macros de tipo Secret text siguen sin estar encriptadas en la base de datos de Zabbix, y para macros totalmente encriptadas necesitaríamos algo como HashiCorp Vault. Consulta la documentación para más información: https://www.zabbix.com/documentation/current/en/manual/config/secrets.

Usa el desplegable para cambiar {$SNMPV3_AUTH} y {$SNMPV3_PRIV} a Secret text:
<p align="center" >
	<img src="https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_03_014.jpg" width="auto">
Figura 3.14 - Texto secreto de Zabbix utilizado para ocultar datos sensibles (autenticación)
</p>

11. Ahora, después de aplicar estos cambios haciendo clic en Actualizar, deberíamos ser capaces de monitorizar nuestro servidor Linux a través de SNMPv3. Vayamos a **Monitorización** | **Hosts** y comprobemos la página Últimos datos de nuestro nuevo host:
<p align="center" >
	<img src="https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_03_015.jpg" width="auto">
</p>
Figura 3.15 - SNMP Últimos datos para el host lar-book-agent_snmp

Tenga en cuenta que sus datos pueden tardar algún tiempo en aparecer aquí.
<p>**Consejo**</p>
Cuando se trabaja con macros, hay tres niveles en orden de cascada: Macros de nivel global, de plantilla y de host. Cuando trabaje con macros de nivel Global, tenga en cuenta que no se exportan con plantillas o hosts. En la mayoría de los casos, es mejor utilizar el nivel de plantilla y el nivel de host, para mantener sus exportaciones independientes de la configuración global de Zabbix.
## Cómo funciona...
Cuando creamos un host como hicimos en el paso 4, Zabbix sondea el host usando SNMP. El sondeo SNMP funciona con OIDs SNMP. Por ejemplo, cuando sondeamos el elemento llamado Memoria libre, le pedimos al agente SNMP que se ejecuta en nuestro host Linux que nos proporcione el valor de 1.3.6.1.4.1.2021.4.6.0. Ese valor es devuelto a nosotros en el host. Ese valor es entonces devuelto a nosotros en el servidor Zabbix:
<p align="center" >
	<img src="https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_03_016.jpg" width="auto">
Figura 3.16 - Diagrama que muestra la comunicación entre el servidor Zabbix y el host SNMP
</p>
SNMPv3 añade autenticación y encriptación a este proceso, asegurando que cuando nuestro servidor Zabbix solicita información, esa solicitud es primero encriptada y los datos son enviados de vuelta encriptados también.

También incluimos la opción de utilizar solicitudes masivas al configurar nuestro host. Las peticiones masivas solicitan varios OIDs en el mismo flujo, por lo que este es el método preferido para hacer peticiones SNMP ya que es más eficiente. Sólo desactívalo para hosts que no soporten peticiones masivas.

Por último, echemos un vistazo a los OIDs SNMP, la parte más importante de nuestra petición SNMP. Los OIDs funcionan en una estructura de árbol, lo que significa que cada número detrás del punto puede contener otro valor. Por ejemplo, veamos este OID para nuestro host:
```bash
1.3.6.1.4.1.2021.4 = UCD-SNMP-MIB::memory
```
Si sondeamos ese OID con la herramienta SnmpWalk CLI o con nuestro servidor Zabbix, obtendremos varios OID de vuelta:
```bash
.1.3.6.1.4.1.2021.4.1.0 = INTEGER: 0
.1.3.6.1.4.1.2021.4.2.0 = STRING: swap
.1.3.6.1.4.1.2021.4.3.0 = INTEGER: 1679356 kB
.1.3.6.1.4.1.2021.4.4.0 = INTEGER: 1674464 kB
.1.3.6.1.4.1.2021.4.5.0 = INTEGER: 1872872 kB
.1.3.6.1.4.1.2021.4.6.0 = INTEGER: 184068 kB
```
Eso incluye nuestro OID 1.3.6.1.4.1.2021.4.6.0 con el valor que contiene nuestra memoria libre. Así es como se construye SNMP, como un árbol.

------------
## Creación de comprobaciones sencillas de Zabbix y el atrapador de Zabbix
En esta receta, vamos a repasar dos comprobaciones que pueden ayudarte a construir algunas configuraciones más personalizadas. Las comprobaciones simples de Zabbix te proporcionan una forma sencilla de monitorizar algunos datos específicos. El Zabbix trapper se combina con el Zabbix sender para obtener datos de tus hosts en el servidor, permitiendo algunas opciones de scripting. Vamos a empezar.
### Preparación
Para crear estas comprobaciones, necesitaremos un servidor Zabbix y un host Linux para monitorizar. Podemos utilizar el host con un agente Zabbix y monitorización SNMP de las recetas anteriores.

Ten en cuenta que para estas comprobaciones no necesitamos el agente Zabbix.
### Cómo hacerlo...
Trabajar con cheques simples es bastante sencillo, como su nombre indica, así que empecemos.
### Creación de comprobaciones simples
Vamos a crear una comprobación simple para monitorizar si un servicio está ejecutándose y aceptando conexiones TCP en un puerto determinado:

1. Para ello, tendremos que crear un nuevo host en el frontend de Zabbix. Vaya a **Configuración** | **Hosts** en su interfaz Zabbix y haga clic en Crear host en la esquina superior derecha.
2. Cree un host con la siguiente configuración:
<p align="center">
	<img src="https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_03_017.jpg" width="auto">
Figura 3.17 - Página de configuración de host Zabbix para el host lar-book-agent_simple
</p>

3. Ahora vaya a **Configuración** | **Hosts**, haga clic en el host recién creado, y vaya a **Items**. Queremos crear un nuevo elemento aquí haciendo clic en el botón Crear elemento.
Vamos a crear un nuevo elemento con los siguientes valores, y después de hacerlo, haremos clic en el botón Añadir en la parte inferior de la página:
<p align="center">
	<img src="https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_03_018.jpg" width="auto">
Figura 3.18 - Página de configuración de elementos de Zabbix para la comprobación del puerto 22 en el host lar-book-agent_simple
</p>

4. Asegúrate de añadir también una etiqueta al ítem, ya que la necesitamos en varios lugares para filtrar y encontrar nuestro ítem cuando trabajemos con Zabbix. Configúralo así:
<p align="center">
	<img src="https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_03_019.jpg" width="auto">}
	Figura 3.19 - Puerto SSH de Zabbix, pestaña Etiqueta
</p>
<em>**Nota importante**</em>
Aquí estamos añadiendo el elemento clave `net.tcp.services[ssh,,22]`. El puerto en este caso es opcional, ya que podemos especificar el servicio SSH con un puerto diferente si queremos.

5. Ahora deberíamos ser capaces de ver si nuestro servidor está aceptando conexiones SSH en el puerto 22 en nuestra pantalla de Últimos datos. Navegue a Monitorización | Hosts y compruebe en la pantalla Últimos datos nuestro nuevo valor:
<p align="center">
	<img src="https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_03_020.jpg" width="auto">
	Figura 3.20 - Zabbix Última página de datos para el host lar-book-agent_simple, elemento puerto 22 comprobado
</p>
6. Hay otra cosa mal aquí. Como puede ver, actualmente no tenemos una configuración de mapeo de valores. El último valor sólo muestra un 1 o un 0. Para cambiar esto, vuelva a **Configuration** | **Hosts** y edite el host `lar-book-agent_simple`.
7. Haga clic en la ficha Asignación de valores y haga clic en el pequeño botón Agregar para agregar una asignación de valores. Cree lo siguiente:
<p align="center">
	<img src="https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_03_021.jpg" width="auto">
	Figura 3.21 - Host lar-book-agent_simple, ventana de asignación de valores
</p>

8. Pulse el botón azul Añadir y pulse el botón azul Actualizar.
9. A continuación, de vuelta a la lista completa de **Configuration** | **Hosts**, navegue hasta nuestro `host lar-book-agent_simple` y pulse sobre Elementos para este host.
10. Edite el elemento Comprobar si el puerto 22 está disponible y añada la asignación de valores como se indica a continuación:
<p align="center">
	<img src="https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_03_022.jpg" width="auto">
	Figura 3.22 - Host lar-book-agent_simple, ventana de edición de elementos
</p>
Eso es todo lo que hay que hacer para crear tus comprobaciones simples en Zabbix. La última página de datos tendrá ahora este aspecto:
<p align="center">
	<img src="https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_03_023.jpg" width="auto">
Figura 3.23 - Última página de datos de nuestro elemento de comprobación del puerto 22
</p>
Como puede ver, hay un valor legible por humanos que ahora muestra Arriba o Abajo, lo que nos da una entrada legible por humanos, que es más fácil de entender. Ahora pasemos al elemento atrapador de Zabbix.



