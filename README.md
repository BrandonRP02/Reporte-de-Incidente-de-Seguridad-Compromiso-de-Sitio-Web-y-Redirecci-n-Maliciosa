# Reporte de Incidente de Seguridad: Compromiso de Sitio Web y Redirección Maliciosa

## Sección 1: Identificación del Protocolo de Red
* **Protocolos Identificados:** HTTP, DNS y TCP.
* **Justificación:** Los registros de tráfico y el análisis del entorno aislado (*sandbox*) revelan que el navegador del usuario inicia consultas DNS para resolver los nombres de dominio (tanto el legítimo `yummyrecipesforme.com` como el malicioso `greatrecipesforme.com`) a direcciones IP. Una vez obtenida la IP, el navegador utiliza el protocolo HTTP (que opera sobre TCP en la capa de transporte) para solicitar el contenido de la página y procesar la descarga del archivo ejecutable malicioso.

## Sección 2: Documentación del Incidente
* **Resumen del Evento:** Varios clientes informaron al servicio de asistencia que el sitio web de la empresa les incitó a descargar un archivo para acceder a recetas. Tras la ejecución de este archivo, los usuarios fueron redirigidos a otra URL y sus equipos experimentaron una degradación del rendimiento (lentitud). 
* **Investigación Técnica:** Se habilitó un entorno aislado (*sandbox*) donde un analista capturó el tráfico con `tcpdump`. La simulación confirmó que al acceder a `yummyrecipesforme.com`, se presenta un aviso fraudulento para actualizar el navegador mediante un archivo ejecutable. Al ejecutarse, este archivo redirige el tráfico HTTP hacia el dominio malicioso `greatrecipesforme.com`.
* **Causa Raíz:** Un analista senior revisó el código fuente del sitio y confirmó la inyección de código JavaScript malicioso. El equipo de ciberseguridad determinó que el vector de entrada inicial fue un ataque de fuerza bruta contra el panel de administración. Un antiguo empleado logró acceder debido a que la cuenta de administrador mantenía la contraseña predeterminada y no existían controles de bloqueo ante intentos repetidos.

## Sección 3: Recomendaciones de Seguridad (Prevención de Fuerza Bruta)
Para prevenir futuros ataques de fuerza bruta al panel de administración, se recomienda implementar de manera prioritaria la siguiente medida:

* **Limitar el número de intentos de inicio de sesión (Account Lockout):** Se debe configurar el sistema de autenticación para que bloquee temporalmente la cuenta (por ejemplo, durante 15 o 30 minutos) tras un número específico de intentos fallidos consecutivos (ej. 3 o 5 intentos).
* **Eficacia:** Esta medida es altamente eficaz contra los ataques de fuerza bruta automatizados. Al restringir la cantidad de intentos permitidos en un periodo de tiempo, resulta matemáticamente inviable para un atacante o un *script* automatizado adivinar contraseñas iterando miles de combinaciones. Esto mitiga directamente la vulnerabilidad que permitió el compromiso inicial del servidor.
