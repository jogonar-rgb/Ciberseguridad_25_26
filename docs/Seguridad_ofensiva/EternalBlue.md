---
title: Explotación de vulnerabilidad EternalBlue (MS17-010) 

description: Explotación manual y automática de EternalBlue, Windows. Apuntes, teoría, prácticas, ejercicio del curso de especialización de ciberseguridad. IES severo ochoa Elche. Hacking ético. incidentes de seguridad, puesta en producción segura.
---

## Explotación de EternalBlue sin Metasploit

Para este ejercicio vamos a hacer uso de una máquina de TryHackMe, especialmente diseñada para ser vulnerable a EternalBlue: [Sala de THM](https://tryhackme.com/room/blue)

En este caso no vamos a completar la sala tal cual está escrita en THM, sino que el proceso lo realizaremos *manualmente*, sin utilizar la herramienta Metasploit. No obstante, nos aprovecharemos de esta máquina.

Os detallo los apartados que debéis documentar a la hora de entregar el informe del proceso completo en Aules (explicación + capturas de pantalla)

1. El primer paso será, como ya vimos en clases anteriores, realizar un escaneo de puertos con Nmap. Recordad los conceptos ya vistos.
   
!!!Tip "Pista"
    Una vez hayáis descubierto los puertos abiertos, haced un nuevo escaneo pero esta vez indicad directamente el puerto de SMB (¿cuál es?) utilizando el switch u opción de nmap adecuada.

    De la misma forma, puesto que vamos en busca y captura de EternalBlue, una vulnerabilidad, utilizad la opción de los scripts de Nmap e indicándole la categoría *vuln*.

2. A la hora de explotar la vulnerabilidad, podéis clonaros [este repositorio de Github](https://github.com/3ndG4me/AutoBlue-MS17-010), que contiene los scripts y archivos que necesitaremos.
   Leed atentamente las instrucciones que vienen explicadas en el repositorio, especialmente las del apartado *Usage*

    + Utilizando el script eternal_checker.py, comprobad si la máquina objeto del ataque está parca.
  
    + Seguid los pasos y compilad el shellcode adecuado.
    !!!Info "Concepto"
        Un shellcode es un código escrito normalmente en ensamblador y compilado para un desterminado procesador que se inyecta en la memoria de un proceso para ejecutar lo que se le indique. En nuestro caso, una shell inversa.
    
    !!!Warning "Atención"

        + Elegiremos la opción de generar la shell inversa (reverse shell) con ***msfvenom***. Msfvenom es una instancia en línea de comandos que se usa para generar y codificar (para evitar detecciones de antimalware) payloads o shellcodes.

        + Compilaremos para la arquitectura de 32 bits (x86) y para la de 64 (x64), puesto que en principio no sabemos qué versión de sistema operativo estamos atacando.
  
        + Os preguntará a qué puerto debe conectarse la reverse shell para cada una de las dos arquitecturas. **Poned puertos diferentes.**

        + Le diremos que queremos generar una shell normal (cmd) y no una shell de meterpreter (terminal en Metasploit)

        + Por último, le diremos que queremos generar un payload *stageless*.

3. El shellcode generado tendrá el nombre `sc_all.bin` y se habrá generado dentro de la carpeta de los shellcodes.
   
4. Finalmente utilizaremos el script en python que nos permitirá explotar la vulnerabilidad. Los scripts están nombrados como `e..._exploitX.py`, donde la X es el número de versión del sistema operativo. Del escaneo de Nmap o de la salida del script de chequeo del estado de parcheo, habréis obtenido la versión adecuada.

## Explotación de EternalBlue con Metasploit

En este caso, vamos a explotar EternalBlue haciendo uso de Metasploit.

Debéis seguir el proceso que ya se ha comentado en la sección correspondiente; iniciar `msfconsole`, buscar un exploit para EternalBlue, configurar las opciones correspondientes y lanzar el exploit.

Este proceso debe quedar reflejado con detalle, incluyendo capturas de pantalla, en un informe que se entregará en Aules.