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


