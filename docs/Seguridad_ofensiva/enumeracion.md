---
title: Enumeración de servicios

description: FTP,SMB,NFS,DNS,SMTP,IMAP/POP3. Apuntes, teoría, prácticas, ejercicio del curso de especialización de ciberseguridad. IES severo ochoa Elche. Hacking ético. incidentes de seguridad, puesta en producción segura.
---

Para esta sesión, vamos a ver una serie de mecanimos a la hora de enumerar los servicios más comunes que nos podemos encontrar en nuestro proceso de pentesting.

## FTP
*FTP (File Transfer Protocol)* es un protocolo de red estándar utilizado para la transferencia de archivos entre un cliente y un servidor en una red TCP/IP, como Internet.

Fue diseñado para permitir la carga y descarga de archivos.

Características principales de FTP:

+ **Autenticación**: Requiere usuario y contraseña para acceder al servidor, aunque también puede configurarse en modo "anónimo" (sin autenticación) para permitir el acceso público.
+ **Transferencias de archivos:** Permite enviar y recibir archivos desde y hacia un servidor.
+ **Gestión de archivos**: Soporta comandos para manejar archivos y directorios en el servidor, como crear, renombrar, eliminar y listar archivos y carpetas.
+ **Conexión de control y datos**: FTP utiliza dos conexiones separadas: una para el control (comandos y respuestas) y otra para la transferencia de datos.
+ **Seguridad**: La versión básica de FTP no cifra los datos, por lo que no es segura para transmitir información confidencial. Sin embargo, existen versiones seguras, como FTPS (FTP sobre SSL/TLS) y SFTP (Secure File Transfer Protocol, que usa el protocolo SSH) que proporcionan cifrado y mayor seguridad.

### Uso común de FTP:

FTP es común en aplicaciones de administración de servidores web, donde se usa para cargar y descargar archivos del sitio web, hacer copias de seguridad y gestionar datos.

Es adecuado para transferencias grandes, pero, dado que no es seguro de forma predeterminada, suele utilizarse en redes de confianza o con versiones seguras (FTPS o SFTP).

## TFTP

*TFTP (Trivial File Transfer Protocol)* es un protocolo de red simple diseñado para la transferencia de archivos en redes TCP/IP. A diferencia de FTP, TFTP es muy básico y no tiene autenticación ni encriptación, lo que lo hace menos seguro pero adecuado para situaciones donde la simplicidad y rapidez son prioritarias.
Características principales de TFTP:

+ **Protocolo ligero**: TFTP está diseñado para realizar transferencias de archivos de forma muy sencilla, con un conjunto mínimo de comandos.
+ **Sin autenticación**: No requiere usuario ni contraseña, lo cual simplifica su uso pero reduce la seguridad.
+ **Sin encriptación**: La falta de cifrado hace que cualquier archivo transferido sea vulnerable a ser interceptado.
+ **Basado en UDP**: TFTP usa el protocolo UDP (en el puerto 69), lo que hace que las transferencias sean rápidas pero sin confirmación de entrega completa o control de errores exhaustivo.
+ **Operaciones básicas**: Permite solamente leer y escribir archivos desde o hacia un servidor; no tiene comandos para listar directorios, renombrar o eliminar archivos.

## Uso común de TFTP

Dado que es un protocolo no seguro, TFTP se utiliza en redes locales o en entornos controlados. Es comúnmente empleado para tareas como:

+ Carga y actualización de configuraciones en dispositivos de red (como routers, switches y puntos de acceso).
+ Actualización de firmware en equipos que lo soportan.
+ Arranque remoto de sistemas en redes locales (por ejemplo, usando PXE para arrancar computadoras sin disco duro).

En resumen, TFTP es un protocolo útil para transferencias rápidas y simples en entornos seguros y confiables, donde la seguridad avanzada y el control de archivos no son necesarios.

## Técnicas de enumeración FTP

### Obteniendo la versión 

```bash
nmap -sV -p21,2121 192.168.100.129
```

Haciendo uso del script *banner* de nmap:

```bash
nmap -sV -p21,2121 -sC banner 192.168.100.129
```

O incluso, intentando conectarnos directamente via Telnet;

```bash
telnet 192.168.100.129 21
```

Podemos lanzar los scripts relacionados con FTP que posee nmap:

```bash
nmap -sV -p21,2121 -sC "ftp*" 192.168.100.129
```

Si se permite el login anónimo podríamos acceder al servidor y descargar contenido:

```bash
ftp 192.168.100.129

get <archivo>
```

## SMB

SMB (Server Message Block) es un protocolo de red utilizado principalmente para compartir archivos, impresoras y recursos en una red local (LAN). Fue desarrollado originalmente por IBM y posteriormente extendido y popularizado por Microsoft. Este protocolo permite que dispositivos de una red, como computadoras y servidores, accedan y administren archivos de forma compartida, además de facilitar otras funciones de comunicación entre equipos.
Principales características de SMB

+ **Intercambio de archivos**: SMB permite que varios usuarios accedan y manipulen archivos almacenados en servidores o computadoras de una red, ofreciendo servicios como abrir, leer, escribir, y cerrar archivos de forma remota.

+ **Impresión remota**: Además de archivos, SMB facilita el uso de impresoras en red. Con este protocolo, es posible enviar documentos a una impresora conectada a otro dispositivo en la red.

+ **Autenticación y permisos**: SMB incluye mecanismos de autenticación y permisos que permiten a los administradores controlar quién puede acceder a ciertos archivos o recursos. Esto es importante para la seguridad, especialmente en redes empresariales.

Versiones de SMB

+ *SMBv1*: La primera versión ampliamente usada de SMB, que incluía vulnerabilidades como las explotadas en el ataque de ransomware WannaCry.
+ *SMBv2* y *SMBv3*: Estas versiones mejoraron la eficiencia y seguridad del protocolo, reduciendo la vulnerabilidad a ciertos tipos de ataques y añadiendo soporte para cifrado.

### Usos de SMB en la seguridad informática

SMB es importante en la seguridad informática porque, si no se configura correctamente, puede ser vulnerable a ataques. Los atacantes pueden aprovechar conexiones SMB abiertas o mal configuradas para acceder a archivos, instalar malware o ejecutar ataques como SMB relay o brute force en el contexto de redes corporativas.

Nmap, por ejemplo, ofrece scripts como smb-enum-shares o smb-os-discovery que permiten detectar y explorar servicios SMB en redes para evaluar su seguridad.

## Técnicas de enumeración SMB

Existen múltiples herramientas para llevar a cabo la enumeración de este servicio. Todas ellas nos permiten, como se puede intuir leyendo el título de la sección, la enumeración y recopilación de todo tipo de información sobre el servicio SMB para, más adelante, pasar a la fase de explotación haciendo uso de la misma.

Algunas de las herramientas más conocidas y utilizadas son:

+ **nbtscan**: permite escanear servicios NetBIOS, entre ellos SMB.
+ **smbap**: es una herramienta de código abierto diseñada para facilitar la enumeración y el acceso a recursos compartidos en redes que utilizan el protocolo SMB (Server Message Block). Es especialmente útil para realizar auditorías de seguridad y pruebas de penetración en entornos de Windows. SMBMap permite a los usuarios explorar y verificar los permisos de acceso a los recursos compartidos en dispositivos que utilizan SMB, lo que puede ayudar a identificar vulnerabilidades o configuraciones inseguras.
+ **nmap**: también posee scripts específicos para SMB
+ **smbclient**: es una herramienta de línea de comandos utilizada para interactuar con servidores que implementan el protocolo SMB (Server Message Block). Forma parte del paquete de software Samba, que permite a sistemas operativos basados en Unix y Linux comunicarse con redes de Windows y acceder a recursos compartidos, como archivos e impresoras. smbclient actúa como un cliente FTP para recursos compartidos de SMB, permitiendo la navegación, el acceso y la manipulación de archivos.
+ **rpcclient**: es una herramienta de línea de comandos que forma parte del paquete Samba, diseñada para interactuar con el servicio Remote Procedure Call (RPC) de sistemas que utilizan el protocolo SMB (Server Message Block). Esta herramienta permite a los usuarios realizar una variedad de tareas administrativas y de gestión de red en servidores que implementan el servicio RPC, que es común en sistemas Windows.
+ **enum4linux**: que no es más que una metaherramienta que combina información obtenida de smbclient, rpclient, net y nmblookup.

Si queremos comenzar a enumerar con una herramienta ya conocida, como es **nmap**, lanzando los scripts *default*, podemos hacer:

```bash
nmap -sC -p 139,445 -sV 192.168.100.129
```
Y obtendremos información como la versión de samba y veremos como la ngeociación de Samba versión 2 (SMBv2) ha resultado fallida, por lo que extraemos que quizás se esté utilizando el inseguro SMBv1.

Otros scripts que se pueden utilizar con nmap para SMB son:
+ smb-protocols: descubrir que versiones maneja la víctima (<SMBv3 sería vulnerable)
+ smb-os-discovery
+ smb-enum-shares
+ smb-enum-users
+ smb-vuln-*: para probar vulnerabilidades concretas de SMB

Con **smbmap** obtendremos una lista de shares:

```bash
smbmap -H 192.168.100.129 -R
```
Según la ayuda de smbmap, textualmente:

!!!quote
    Main arguments:
    
        -H HOST               IP of host
        --host-file FILE      File containing a list of hosts
        -u USERNAME           Username, if omitted null session assumed
        -p PASSWORD           Password or NTLM hash
        -s SHARE              Specify a share (default C$), ex 'C$'
        -d DOMAIN             Domain name (default WORKGROUP)
        -P PORT               SMB port (default 445)
        -v                    Return the OS version of the remote host
        --admin               Just report if the user is an admin

Y si quisiéramos conectarnos directamente al servidor, podríamos usar **smbclient**. Para listar los *shares* usando una sesión nula (sin credenciales):

```bash
smbclient -N -L //192.168.100.129
```
Y si quisiéramos acceder al servidor y movernos por los directorios:

```bash
smbclient //192.168.100.129/tmp
```

También con la herramiento **rpclient** podemos ejecutar diversas peticiones para obtener información sobre SMB tras conectarnos al servidor:

```bash
rpcclient -U "" 192.168.100.129
```
Algunas de las peticiones que podemos hacer;

|          Petción          |                             Descripción                            |
|:-----------------------:|:------------------------------------------------------------------:|
| srvinfo                 | Información del servidor                                                |
| enumdomains             | Enumerar todos los dominios desplegados en la red            |
| querydominfo            | Proporciona dominio, servidor e información de usuario de los dominios existentes domains. |
| netshareenumall         | Enumera los shares disponibles                                  |
| netsharegetinfo <share> | Información acerca de algún share específico                       |
| enumdomusers            | Enumera usuarios del dominio                                       |
| queryuser <RID>         | Proporciona información sobre un usuario concreto                       |

Y, como ya comnentábamos, podríamos obtener una salida combinada con **enum4linux**:

```bash
enum4linux 192.168.100.129 -A
```

## NFS

NFS (Network File System) es un protocolo de red que permite compartir archivos entre computadoras en una red de manera que los usuarios puedan acceder a ellos como si estuvieran en su sistema local. Fue desarrollado originalmente por Sun Microsystems en la década de 1980 y se ha convertido en un estándar ampliamente utilizado, especialmente en sistemas Unix y Linux, aunque también es compatible con otros sistemas operativos.
Principales características de NFS:

+ **Transparencia**: Los archivos y directorios compartidos a través de NFS aparecen en la red como parte del sistema de archivos local. Esto permite que los usuarios accedan a archivos remotos sin necesidad de moverlos o copiarlos.

+ **Arquitectura cliente-servidor**: NFS sigue un modelo en el que un servidor central aloja los archivos y los distribuye a los clientes que se conectan a él. Los clientes pueden montar los sistemas de archivos del servidor en su propio sistema y trabajar con los archivos directamente.

+ **Protocolos de red**: NFS utiliza típicamente el protocolo TCP/IP para la transmisión de datos en la red. También puede emplear otros protocolos de seguridad, como Kerberos, para autenticación y cifrado.

+ **Permisos y control de acceso**: NFS ofrece control de acceso mediante permisos de archivos Unix (propiedad, grupo y permisos de lectura, escritura y ejecución) y se puede integrar con otros sistemas de autenticación y seguridad.

+ **Compatibilidad y escalabilidad**: Es compatible con varios sistemas operativos y permite que varias máquinas se conecten a los archivos compartidos, haciéndolo ideal para redes de oficina y entornos de computación distribuida.

### Ejemplos de uso de NFS

+ *Almacenamiento compartido en servidores*: Permite que varias máquinas accedan a una misma base de datos o sistema de archivos.
+ *Entornos de desarrollo*: Facilita la colaboración entre desarrolladores, permitiendo el acceso a los mismos archivos desde múltiples dispositivos.
+ *Virtualización y contenedores*: En entornos donde se crean instancias de contenedores o máquinas virtuales, NFS permite almacenar archivos de configuración y datos de manera centralizada.

NFS es una solución eficiente y robusta para compartir archivos en redes internas de manera segura y escalable.

### ¿Cómo funciona NFS?

Primero, el cliente solicitará montar un directorio de un host remoto en un directorio local de la misma manera que puede montar un dispositivo físico. El servicio de montaje actuará  entonces para conectarse al demonio de montaje correspondiente mediante RPC. 

El servidor comprueba si el usuario tiene permiso para montar el directorio solicitado. A continuación devolverá un manejador de fichero que identifica unívocamente cada fichero y directorio  que está en el servidor.

Si alguien quiere acceder a un archivo utilizando NFS, se realiza una llamada RPC a NFSD (el demonio NFS) en el servidor. Esta llamada toma parámetros como:

+ El manejador del archivo
+ El nombre del archivo al que se quiere acceder
+ ID de usuario del usuario
+ El ID de grupo del usuario

Estos datos se utilizan para determinar los derechos de acceso al archivo especificado. Esto es lo que controla los permisos de usuario, es decir, la lectura y escritura de archivos.

### Ejemplo de enumeración de NFS

El primer paso es como siempre, realizar un escaneo general de la máquina:

```bash
nmap -p- -sT -T5 10.10.192.87   
```

Cuando descubrimos que existe un servicio NFS, podemos listar las comparticiones visibles con:
```bash
showmount -e 10.10.192.87
```

Nos creamos un directorio ad-hoc y montamos el share que hemos visto anteriormente:

```bash
mkdir /tmp/mount

sudo mount -t nfs 10.10.192.87:home /tmp/mount/ -nolock
```

## SMTP

SMTP (Simple Mail Transfer Protocol) es un protocolo de comunicación usado para enviar correos electrónicos a través de redes de internet. Es fundamental en la transmisión de mensajes de correo entre servidores de correo electrónico y desempeña un papel clave en el sistema de correo electrónico.

### Funcionamiento básico de SMTP
1. **Envío del correo**: Cuando un usuario envía un correo, su cliente de correo (como Outlook o Gmail) usa SMTP para enviar el mensaje al servidor de correo saliente.
2. **Transferencia entre servidores**: El servidor de correo del remitente utiliza SMTP para entregar el mensaje al servidor de correo del destinatario. 
3. **Recepción del correo**: Una vez que el servidor del destinatario recibe el mensaje, el usuario puede leerlo a través de protocolos de recepción como IMAP o POP3.

### Características clave de SMTP
- **Conexión**: Opera en el puerto 25 (aunque también usa 465 o 587 para conexiones seguras).
- **Simples comandos y respuestas**: Utiliza comandos básicos, como `HELO`, `MAIL`, `RCPT`, y `DATA`, para establecer la conexión y transmitir el mensaje.
- **Textos sin formato**: SMTP fue diseñado para enviar mensajes de texto sin formato; para enviar contenido multimedia, se usa una codificación adicional (como MIME).

SMTP es uno de los protocolos de red más antiguos y sigue siendo el estándar para enviar correos electrónicos hoy en día.

### Enumeración SMTP

Iniciamos, como casi siempre, la enumeración con un escaneo con nmap

```bash
nmap -p- -sT -T5 10.10.192.87   
```

Si descubrimos un servidor SMTP (puerto 25 por defecto), podemos por ejemplo enumerar usuarios con algú nombre o lista que hayamos recolectado previamente:

```bash
smtp-user-enum -M VRFY -u admin -t 10.10.192.87

smtp-user-enum -M VRFY -U lista_usuarios.txt -t 10.10.192.87
```

Podemos intentar ver qué [comandos](https://mailtrap.io/es/blog/smtp-commands-and-responses/) se nos permiten para interactuar con el servidor de correo o descubrir si es un [open-relay](https://es.wikipedia.org/wiki/Open_relay):

```bash
nmap -p25 --script smtp-commands 10.10.192.87
nmap -p25 --script smtp-open-relay 10.10.192.87 -v
```