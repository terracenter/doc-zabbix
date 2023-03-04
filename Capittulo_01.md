# Capítulo 1: Instalando Zabbix y Empezando a Usar el Frontend

Zabbix 6 es como una continuación de Zabbix 5, ya que esta vez no estamos viendo grandes cambios en la interfaz de usuario. Sin embargo, en Zabbix 6, usted todavía encontrará un montón de mejoras, tanto en la interfaz de usuario y componentes básicos. Por ejemplo, la introducción de alta disponibilidad para el servidor Zabbix. Detallaremos todos los cambios importantes a lo largo del libro.

En este capítulo, vamos a instalar el servidor Zabbix y explorar la interfaz de usuario Zabbix para que se familiarice con ella. Vamos a ir sobre la búsqueda de sus hosts, triggers, dashboards, y más para asegurarse de que se sienta seguro de sumergirse en el material más profundo más adelante en este libro. La interfaz de usuario de Zabbix tiene un montón de opciones para explorar, así que si estás empezando, no te sientas abrumado. Está bastante estructurada y una vez que le cojas el truco, estoy seguro de que encontrarás tu camino sin problemas. Aprenderás todo sobre estos temas en las siguientes recetas:

* Instalación del servidor Zabbix
* Configuración del frontend Zabbix
* Habilitar la alta disponibilidad del servidor Zabbix
* Uso del frontend Zabbix
* Navegación por el frontend Zabbix

## Requisitos técnicos

Empezaremos este capítulo con una máquina (virtual) Linux vacía. Siéntete libre de elegir una distribución Linux basada en RHEL o Debian. A continuación, vamos a configurar un servidor Zabbix desde cero en este host.

Así que antes de empezar, asegúrate de tener tu máquina Linux lista.

## Instalación del servidor Zabbix

Antes de hacer nada dentro de Zabbix, necesitamos instalarlo y prepararnos para empezar a trabajar con él. En esta receta, vamos a descubrir cómo instalar el servidor Zabbix 6.

### Preparándonos

Antes de instalar el servidor Zabbix, vamos a necesitar cumplir con algunos requisitos previos. Vamos a utilizar MariaDB principalmente a lo largo de este libro. MariaDB es popular y hay mucha información disponible sobre su uso con Zabbix.

En este punto, deberías tener un servidor Linux preparado delante de ti ejecutando una distribución basada en RHEL o Debian. Yo instalaré CentOS y Ubuntu 20.04 en mi servidor; vamos a llamarlos `lar-book-centos` y `lar-book-ubuntu`.

Cuando tengas tu servidor listo, podemos empezar el proceso de instalación.

### Cómo hacerlo...

1. Comencemos añadiendo el repositorio Zabbix 6.0 a nuestro sistema.
   Para sistemas basados en RHEL:

   ```bash
   rpm -Uvh https://repo.zabbix.com/zabbix/6.0/rhel/8/x86_64/zabbix-release-6.0-1.el8.noarch.rpm
   dnf clean all
   ```

   Para sistemas Ubuntu:

   ```bash
   wget https://repo.zabbix.com/zabbix/6.0/ubuntu/pool/main/z/zabbix-release/zabbix-release_6.0-1+ubuntu20.04_all.deb
   dpkg -i zabbix-release_6.0-1+ubuntu20.04_all.deb
   apt update
   ```

   Para Debian:

   ```bash
   wget https://repo.zabbix.com/zabbix/6.0/debian/pool/main/z/zabbix-release/zabbix-release_6.0-4%2Bdebian11_all.deb
   dpkg -i zabbix-release_6.0-4+debian11_all.deb
   apt update
   ```
2. Ahora que el repositorio está añadido, vamos a añadir el repositorio MariaDB en nuestro servidor:

   Según este libro, pero instala una versión no soportada por Zabbix, a la fecha

   ```bash
   wget https://downloads.mariadb.com/MariaDB/mariadb_repo_setup
   chmod +x mariadb_repo_setup
   ./mariadb_repo_setup
   ```

Nota:
Must not be higher than (10.10.xx). Use of supported database version is highly recommended
Override by setting AllowUnsupportedDBVersions=1 in Zabbix server configuration file at your own risk.

Para Debian 11 (estoy usando), como dice la documentacion https://mariadb.com/kb/en/mariadb-package-repository-setup-and-usage/

```bash
curl -LsS https://r.mariadb.com/downloads/mariadb_repo_setup | sudo bash -s -- --mariadb-server-version="mariadb-10.10" --os-type=debian  --os-version=11
```

3. A continuación, instálelo y habilítelo mediante los siguientes comandos:

   ```bash
   dnf install mariadb-server
   systemctl enable mariadb
   systemctl start mariadb
   ```

   Para sistemas Ubuntu/Debian:

   ```bash
   apt install -y zabbix-server-mysql zabbix-sql-scripts
   ```

   ```bash
   apt install -y mariadb-server
   systemctl enable mariadb
   systemctl start mariadb
   ```
4. Después de instalar MariaDB, asegúrese de proteger su instalación con el siguiente comando:

   ```bash
   /usr/bin/mariadb-secure-installation
   ```
5. Asegúrate de responder a las preguntas con un sí (Y) y configura una contraseña de root que sea segura.
6. Ejecute la configuración de instalación segura y asegúrese de guardar su contraseña en algún lugar. Es muy recomendable utilizar una bóveda de contraseñas.
7. Ahora, vamos a instalar nuestro servidor Zabbix con soporte MySQL.

   Para sistemas basados en RHEL:

   ```bash
   dnf install zabbix-server-mysql zabbix-sql-scripts
   ```

   Para sistemas basados en Debian / Ubuntu:

   ```bash
   apt install zabbix-server-mysql zabbix-sql-scripts
   ```
8. Con el servidor Zabbix instalado, estamos listos para crear nuestra base de datos Zabbix. Inicie sesión en MariaDB con lo siguiente:

   ```bash
   mysql -u root -p
   ```
9. Introduzca la contraseña que estableció durante la instalación segura y cree la base de datos Zabbix con los siguientes comandos. No olvide cambiar la `contraseña` en el segundo comando:

   ```bash
   create database zabbix character set utf8mb4 collate utf8mb4_bin;
   create user zabbix@localhost identified by 'password';
   grant all privileges on zabbix.* to zabbix@localhost identified by 'password';
   flush privileges;
   quit
   ```

   **Consejo**

   Para aquellos que lo necesiten, Zabbix también soporta `utf8mb4` ahora. Hemos cambiado `utf8` por `utf8mb4` en el comando anterior y todo funcionará. Para una referencia, consulte el ticket de soporte de Zabbix aquí: https://support.zabbix.com/browse/ZBXNEXT-3706
10. Ahora necesitamos importar nuestro esquema de base de datos Zabbix a nuestra recién creada base de datos Zabbix:

    Sistemas (Ubuntu y RedHad) ??

    ```bash
    zcat /usr/share/doc/zabbix-sql-scripts/mysql/server.sql.gz | mysql -u zabbix -p zabbix
    ```

    Debian

    ```bash
    zcat /usr/share/zabbix-sql-scripts/mysql/server.sql.gz | mysql -u zabbix -p zabbix
    ```

    **Nota importante**

    En este punto, puede parecer que está atascado y que el sistema no responde. No se preocupe, ya que sólo tardará un poco en importar el esquema SQL.

Ahora hemos terminado con los preparativos para nuestro lado MariaDB y estamos listos para pasar al siguiente paso, que será la configuración del servidor Zabbix:

1. El servidor Zabbix se configura utilizando el archivo de configuración del servidor Zabbix. Este archivo se encuentra en `/etc/zabbix/`. Vamos a abrir este archivo con nuestro editor favorito; Voy a utilizar **Vim** en todo el libro:

   ```bash
   vim /etc/zabbix/zabbix_server.conf
   ```
2. Ahora, asegúrese de que las siguientes líneas del archivo coinciden con el nombre de su base de datos, el nombre de usuario de la base de datos y la contraseña del usuario de la base de datos:

   ```bash
   DBName=zabbix
   DBUser=zabbix
   DBPassword=password
   ```

   **Consejo**

   Antes de iniciar el servidor Zabbix, debe configurar SELinux o AppArmor para permitir el uso del servidor Zabbix. Si se trata de una máquina de prueba, puede utilizar una postura permisiva para SELinux o desactivar AppArmor, pero se recomienda no hacer esto en producción.
3. Todo hecho; ahora estamos listos para iniciar nuestro servidor Zabbix:

   ```bash
   systemctl enable zabbix-server
   systemctl start zabbix-server
   ```
4. Comprueba si todo arranca como se espera con lo siguiente:

   ```bash
   systemctl status zabbix-server 
   ```
5. Alternativamente, supervise el archivo de registro, que proporciona una descripción detallada del proceso de inicio de Zabbix:

   ```bash
   tail -f /var/log/zabbix/zabbix_server.log 
   ```
6. La mayoría de los mensajes en este archivo están bien y pueden ser ignorados con seguridad, pero asegúrese de leer bien y ver si hay algún problema con el arranque de su servidor Zabbix.

### Cómo funciona...

El servidor Zabbix es el proceso principal de nuestra configuración Zabbix. Es responsable de nuestra monitorización, alertas de problemas, y muchas de las otras tareas descritas en este libro. Una pila completa de Zabbix consiste en al menos lo siguiente:

* Una base de datos (MySQL, PostgreSQL u Oracle)
* Un servidor Zabbix
* Apache o NGINX ejecutando el frontend Zabbix con PHP 7.2+, pero PHP 8 no está soportado actualmente

  **Nota**:

  A la fecha funciona con 7.2.5 o superior, 8.0 - 8.2, según documentación: https://www.zabbix.com/documentation/6.0/en/manual/installation/requirements

Podemos ver los componentes y cómo se comunican entre sí en la siguiente figura:

![Figura 1.1 - Diagrama de comunicaciones de la configuración de Zabbix](https://static.packt-cdn.com/products/9781803246918/graphics/image/B18275_01_001.jpg)

Figura 1.1 - Diagrama de comunicaciones de la configuración de Zabbix

Acabamos de configurar el servidor Zabbix y la base de datos; ejecutando estos dos, estamos básicamente listos para empezar a monitorizar. El servidor Zabbix se comunica con la base de datos Zabbix para escribir en ella los valores recogidos.

Sin embargo, todavía hay un problema: no podemos configurar nuestro servidor Zabbix para hacer nada. Para ello, vamos a necesitar nuestro frontend Zabbix, que vamos a configurar en la siguiente receta.
