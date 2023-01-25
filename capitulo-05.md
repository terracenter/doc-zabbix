# Capítulo 5: Construyendo tus propias plantillas estructuradas
Es hora de empezar con una de las tareas más importantes en Zabbix: la construcción de plantillas estructuradas. Una buena configuración de Zabbix depende en gran medida de las plantillas, y hay una gran diferencia entre una buena y una mala plantilla. Así que, si eres nuevo en Zabbix o aún no has empezado a construir tus propias plantillas, entonces presta mucha atención a este capítulo.

En este capítulo, vamos a repasar cómo configurar tus plantillas, y cómo llenarlas con los elementos y disparadores adecuados. También, es importante hacer uso de macros y Low-Level Discovery (LLD) de la manera correcta. Después de seguir estas recetas, estarás más que preparado para construir sólidas plantillas Zabbix con el formato correcto, e incluso LLD.

En este capítulo, trataremos las siguientes recetas:
- Creación de tu plantilla Zabbix
- Configuración de etiquetas a nivel de plantilla
- Creación de elementos de plantilla
- Creación de disparadores de plantilla
- Configuración de diferentes tipos de macros
- Uso de LLD en plantillas
- Anidamiento de plantillas Zabbix

## Requisitos técnicos
Necesitaremos nuestro servidor Zabbix del Capítulo 4, Trabajando con disparadores y alertas, para monitorizar nuestro host SNMP (Simple Network Management Protocol). Para el host SNMP, podemos utilizar el host que configuramos en la receta Trabajar con monitorización SNMP del Capítulo 3, Configurar la monitorización de Zabbix.
