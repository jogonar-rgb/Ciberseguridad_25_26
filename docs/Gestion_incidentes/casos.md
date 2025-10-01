# Casos prácticos de gestión de incidentes

## **1. Ejercicio de simulación de ransomware**

## Objetivo

Simular un ataque de ransomware y evaluar la capacidad de respuesta del equipo SOC en todos los roles (L1, L2, L3 y SOC Manager).

## Escenario

Durante una monitorización rutinaria, el SOC observa actividad inusual en la red de la empresa. Varios archivos parecen estar encriptados y se descubre una nota de rescate exigiendo el pago en Bitcoin. El ejercicio comienza con este descubrimiento y procede a través del ciclo de vida de respuesta a incidentes.


## Alerta de red (SIEM)

- ID de Alerta: 3029  
- Gravedad Alta  
- IP de origen: 192.168.1.24  
- IP de destino: 45.77.89.120  
- Descripción: Detectada gran transferencia de datos salientes.


## Log del Endpoint (EDR)

| Hora | Evento | Usuario| Acción |
|------------|-----------------------------|--------|---|
| 10:02:34 AM | Archivo sospechoso descargado | user_123 | Archivo: `invoice_0321.exe` | 10:04:56 AM
| 10:04:56 AM |Ficheros encriptados detectados | user_123 | Ficheros encriptados: `*.docx`, `*.xls` |
| 10:06:12 AM | Nota de rescate creada | user_123 | Nota: «Pague 5 BTC para recuperar sus datos». |


## Fase 1: Detección inicial y triaje

### Inyección 1: Actividad sospechosa descubierta

1. Una alerta SIEM informa de tráfico saliente inusual hacia una IP no reconocida.
2. Los registros EDR revelan una descarga de archivos sospechosa seguida de un rápido cifrado de archivos.

#### Preguntas para el analista L1:

1. ¿Qué medidas iniciales debe tomar al recibir la alerta SIEM?  
2. ¿Cómo valida si esta actividad es maliciosa?  
3. ¿Cuándo debería escalar esta situación a L2?

#### Preguntas para el analista de L2:

1. ¿Cómo confirma si el ransomware está activo?  
2. ¿Qué registros o herramientas adicionales examinaría para comprender el alcance?

#### Preguntas para el analista L3:

1. ¿Qué técnicas forenses avanzadas puedes aplicar para analizar el ransomware?  
2. ¿Cómo identificaría el vector de ataque y evitaría que se produjeran más ataques?

#### Preguntas para el Responsable del SOC:

1. ¿Cómo priorizar los siguientes pasos garantizando una interrupción operativa mínima?  
2. ¿Qué estrategias de comunicación aplicaría para las partes interesadas internas y externas?


## Fase 2: Contención

### Inyección 2: Propagación de la infección

- TI informa de que tres servidores del departamento de Finanzas son inaccesibles.  
- El análisis del tráfico de red muestra conexiones continuas a la IP externa (45.77.89.120).

#### Preguntas para el analista L1:

1. ¿Qué medidas debe tomar para aislar los sistemas afectados?  
2. ¿Cómo documenta el incidente para su posterior análisis?

#### Preguntas para el analista L2:

1. ¿Cómo utiliza los datos de tráfico de red para contener la amenaza?  
2. ¿Qué técnicas de mitigación deben aplicarse para evitar el movimiento lateral?

#### Preguntas para el analista L3:

1. ¿Cómo se analiza el malware para crear indicadores de peligro procesables?  
2. ¿Qué herramientas avanzadas pueden utilizarse para evaluar el alcance completo de los activos afectados?

#### Preguntas para SOC Manager:

1. ¿Cómo se asegura de que las acciones de contención se ajustan a las prioridades de la empresa?  
2. ¿Qué recursos se necesitan para la recuperación inmediata?


## Fase 3: Análisis de la causa raíz

### Inyección 3: Vector de ataque descubierto

- Los registros del gateway de correo electrónico revelan que el ransomware se originó a partir de un correo electrónico de phishing enviado a `user_123`, con un adjunto malicioso llamado `invoice_0321.exe`.

#### Preguntas para el analista L1:

1. ¿Cómo rastreas el correo electrónico de phishing hasta su origen?  
2. ¿Qué indicadores buscaría en los correos electrónicos de otros usuarios?

#### Preguntas para el analista L2:

1. ¿Cómo correlacionas el correo electrónico de phishing con la actividad del endpoint?  
2. ¿Qué recomendaciones haría para reforzar la seguridad del correo electrónico?

#### Preguntas para el analista L3:

1. ¿Cómo utiliza la inteligencia sobre amenazas para evaluar el riesgo de ataques similares?  
2. ¿Qué medidas adicionales pueden tomarse para reforzar los puntos finales?

#### Preguntas para el responsable del SOC:

1. ¿Cómo presentas los resultados a los directivos?  
2. ¿Cómo asigna recursos para mejoras a largo plazo?