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
