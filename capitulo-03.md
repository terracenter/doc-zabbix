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
Cuando añadas nuevos repositorios a tu sistema, consulta siempre la página de descargas de Zabbix. Aquí podemos encontrar el repositorio actualizado adecuado para nuestro sistema:
https://www.zabbix.com/download

#### Uso de un agente Zabbix en modo pasivo
Empecemos construyendo un agente Zabbix con comprobaciones pasivas:

Después de instalar el agente 2 de Zabbix, vamos a abrir el fichero de configuración del agente Zabbix para editarlo:




