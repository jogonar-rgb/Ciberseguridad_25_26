---
title: Monitorización utilizando Security Onion

description: Apuntes, teoría, prácticas, ejercicio del curso de especialización de ciberseguridad. IES severo ochoa Elche. Hacking ético. incidentes de seguridad, puesta en producción segura. En este capítulo vamos a utilizar la distribución Security Onion para realizar la monitorización y respuesta/administración de incidentes de ciberseguridad mediante la integración de logs de distintas aplicaciones como Zeek, Suricata o Wazuh, de acuerdo a este periodo dedicado a la seguridad defensiva o Blue Team
---

## Introducción

Como parte de nuestra estrategia Blue Team ya hemos visto técnica tales como el análisis de logs de forma *clásica*, con herramientas como Elastic Stack, además de utilidades como los IDS/IPS.

En este caso vamos a ver una herramienta que aglutine a varias de las anteriores. Estamos hablando de **Security Onion**, una distribución de Linux que nos permitiría fácilmente poder montar un SOC.

![](./img/securityonion.jpg){: style="height:175px;width:150px"}

Tal y como podemos leer en su [documentación](https://docs.securityonion.net/en/2.4/about.html):

!!!quote
    Security Onion is a free and open platform built by defenders for defenders. It includes network visibility, host visibility, intrusion detection honeypots, log management, and case management. Security Onion has been downloaded over 2 million times and is being used by security teams around the world to monitor and defend their enterprises. Our easy-to-use Setup wizard allows you to build a distributed grid for your enterprise in minutes! 

Como vemos, Security Onion incluye herramientas para la visibilidad de red, visibilidad de host, honeypots de detección de intrusiones, gestión de registros y gestión de casos. Para ello utiliza herramientas conocidas y suyas propias. Entre las herramientas conocidas están Suricata o ELK pero echemos un vistazo rápido a otras:

### Zeek

Hasta 2018 Zeek fue conocido con el nombre de **Bro**. 

![](./img/zeek.jpg){: style="height:175px;width:400px"}

Zeek es un una herramienta de código abierta que realiza un análisis de red pasivo. Utiliza y visualiza grandes cantidades de metadatos de la red para tener una visión de alto nivel. Para cada flujo de tŕafico, los metadatos tipicos constan de una tupla de 5 parámetros: IPs, protocolo, puertos. Pero también se hacen servir atributos específicos del tráfico que permita a los analistas entrar más en detalla como información DNS, detalles del cifrado TLS o información de capa 7.

#### Suricata/Snort vs Zeek

Suricata/Snort pueden o bien inspeccinar el tráfico de forma pasiva o bloquearlo de forma. Todo ello en función de unas alertas basadas en reglas.

Zeek es un analizador de tráfico que puede extraer información para su posterior disección con el fin de investigar una sucesión de acontecimientos. Genera varios tios de logs (conn, http, ssl...) y provee, como ya hemos dicho, todos los detalles de las conexiones realizadas.


### Wazuh

Wazuh es una plataforma gratuita y de código abierto que se usa para prevención de amenazas, detección y respuesta. Es capaz de ofrecer protección en entorno on-premise, virtualizados, contenerizados o en cloud.

![](./img/wazuh.png){: style="height:175px;width:300px"}

Wazuh consta de un agente de seguridad para un endpoint, desplegado para monitorizar los sitemas y un server de gestión o administración, que recolecta y analiza los datos que recopila de todos los agentes. Además, Wazuh tiene integración completa con Elastic Stack, proporcionando un mecanismo de búsqueda y visualización que permite a los usuarios navegar a través de sus alerta de seguridad.-

Para ver en detalle las capacidades de Wazuh, podéis consultar su [GitHub](https://github.com/wazuh/wazuh#wazuh-capabilities).


### CyberChef

![](./img/cyberchef.jpg){: style="height:175px;width:300px"}

Aunque ya lo hemos usado anteriormente, la mejor forma de definir Cyberchef es acudir directamente a sus creadores:

!!!quote
    A simple, intuitive web app for analysing and decoding data without having to deal with complex tools or programming languages. CyberChef encourages both technical and non-technical people to explore data formats, encryption and compression.

### Resto de herramientas de Security Onion

La mejor forma de descubrir las herramientas que integra Security Onion es acudir directamente a su [documentación](https://docs.securityonion.net/en/2.4/introduction.html)

## Demostración práctica

Investigación de un caso de exfiltración de datos haciendo uso de Security Onion.

## Ejercicio/Práctica

### Importar archivos PCAP

Si queremos borrar las alertas generadas y las visualizaciones en ElasticSearch, ejecutaremos estas dos instrucciones:

```bash
sudo so-nsm-clear
sudo so-elastic-clear
```
También tendremos que borrar las carpetas con los hashes de los ficheros importados que se encuentran en /nsm/import/

Y, tras ello, podemos importar el .pcap así:

```bash
sudo so-import-pcap /ruta/absoluta/a/archivo.pcap
```

En principio no debería dar ningún problema pero, si lo diera, reinicidad la máquina. Puesto que Security Onion está basada en contenedores Docker para todas sus herramientas, puede que tras reiniciar tarde un rato en tenerlo todo listo. 

Podéis comprobar el estado en el que están los contenedores en tiempo real con:

```bash
watch sudo so-status
```

No os preocupéis si los últimos tres contenedores os salen como `DISABLED`, eso es porque no está capturando en tiempo real pero los pcap si los analizará con Suricata y Zeek.





### Tráfico malware

##### **PRIMER FLUJO TCP**

1. ¿Cómo podemos ver la secuencia temporal de las alertas, de menos reciente a más reciente? 
2. Vemos un primer flujo TCP, de una IP interna hacia un host de internet, ¿qué IPs (origen y destino) y qué puertos (origen y destino) se ven implicados?
3. Hay una alerta que nos llama la atención puesto que vemos que se realiza una petición a un PDF pero diretamente a una dirección IP, en lugar de a un dominio que sería lo más normal, ¿de qué alerta se trata?
4. Previo a esa alerta vemos alguna otra que nos indica que se ha observado un user-agent que se identifica como [*curl*](https://www.hostinger.es/tutoriales/comando-curl)
5. ¿Qué nos dice que ha ocurrido la última alerta perteneciente a ese flujo?
6. ¿Si cojo cualquier alerta de las pertenecientes a ese flujo TCP, cómo puedo ver en detalle todos los mensajes...? ¿P...?
7. ¿En este flujo detallado, qué *user-agent* podemos ver?
8. ¿Se ve algo sospechoso en el cuerpo de la respueta?
9. ¿Dónde podemos mandar este tráfico, haciendo uso de un sombrero de cocina, para jugar con él?
10. Una vez allí, ¿qué podemos ir quitándole (incluso con una varita mágica) hasta ver algo interesante para nosotros?
11. Una vez llegado al meollo del asunto, ¿qué referencias raras podemos ver?

##### **SEGUNDO FLUJO TCP**

1. Identifica las alerta que pertenecen al siguiente flujo gracias a los números de puerto y las IPs
2. Volvemos a mirar todo el flujo de tráfico detallado de la misma manera que en el caso anterior
3. ¿Se ve algo raro aquí?

##### **TERCER FLUJO TCP**

1. Identifica las dos alertas que identifican este flujo, así como sus parámetros.
2. Volvemos a mirar todo el flujo de tráfico de la misma manera que en los casos anteriores
3. ¿Qué user-agent se está utilizando y qué acción realiza esta petición?
4. Vemos que tras este flujo, hay una alerta más de tráfico HTTPS, ¿sabrías decir a qué corresponde este tráfico viendo lo que está ocurriendo?

##### **CUARTO FLUJO TCP**

1. Ahora aparecen un gran número de alertas avisando de varias cosas, ¿qué tiene pinta de haber ocurrido?
2. ¿Cómo puedes mirar detalladamente que ha ocurrido a partir de las alertas?
3. ¿Qué vemos en esta vista detallada?

!!!note 
    Esto es un breve vistazo a las alertas que hemos generado pero, por normal general, querremos saber cómo lidiar con incidentes así cuando demos con atacantes más sofisticados que sean capaces de evadir nuestras firmas, veamos cómo.

1. Pestaña `Hunt`
2. Utilizamos una query predefinida que ordene por evento módulo y evento dataset
3. En *Group Metrics*, ¿qué clase de logs vemos que ha recopilado Zeek?
4. En el menú desplegable de las querys, elegir que nos muestre todas las conexiones por ip origen, ip destino, protocolo de red y puerto destino.
5. Agrupad ahora en ese mismo desplegable por conexiones HTTP y puerto destino.
6. ¿Aparece en *Group Metrics* algún puerto que no sea el usual de **HTTP**?
7. Si aparece --> *Include*
8. ¿Aparece algo digno de echarle un vistazo en *Events*? Miradlo de forma detallada como ya sabéis.
9. ¿Qué tenemos aquí?

#### OTRAS FORMAS DE DETECCIÓN

1. Agrupar en la query por conexiones HTTP, por método http y por user-agent.
2. ¿Qué cosa interesante se ve aquí que podría servirnos para identificar también el tráfico malicioso?
3. Agrupa ahora por conexiones HTTP  por virtual host.
4. ¿Hay algo que te llame la atención por raro y qué pueda ponernos sobre la pista de tráfico malicioso?
5. Agrupa ahora por dataset notice (Zeek).
6. ¿Qué cosa sospechosa se ve?
7. En *Events*, agrupa por el campo `notice.sub_message`
8. ¿De qué entidad son la mayoría de los certificados?¿Qué conclusión extraes?




!!!tip "Tarea"
    Documenta esta práctica con todas las explicaciones y capturas de pantalla pertinentes. Además, incluye una conclusión personal de todo este proceso al final del informe de la práctica.




