# Módulo USER-CONTROL

Este proyecto se implementa utilizando Mbed, sin el uso de interrupciones y sin estructuras complejas. Los estados definidos son `OFF`, `MONITOR` y `PANIC`, y el cambio entre estos estados se controla a través de la recepción de caracteres específicos por el puerto serie utilizando `UnBufferedSerial`.

## Documentación

Puedes encontrar la documentación completa generada por Doxygen [aquí](https://betsabe09.github.io/TP1-ArmBookCurso2024/auto-control/Docs/html/index.html).

## Descripción

### Estados y Transiciones

1. **Estado OFF:**
   - **Descripción:** Es el estado inicial del programa.
   - **Comportamiento:**
     - LED1, relé y buzzer están apagados.
     - Si se recibe la letra 'o' por el puerto serie, el estado permanece en OFF y se responde con una 'O' mayúscula.
     - No se produce ningún cambio si se recibe cualquier otro carácter.

2. **Estado MONITOR:**
   - **Descripción:** En este estado, el sistema monitorea constantemente el puerto serie.
   - **Comportamiento:**
     - LED1, relé y buzzer están apagados.
     - Si se recibe la letra 'm' por el puerto serie, se responde con una 'M' mayúscula.
     - Se mide el tiempo desde la última respuesta enviada. Si el tiempo contabilizado supera los 5 segundos sin recibir otra 'm', el estado cambia a PANIC.
     - Si se recibe cualquier otro carácter, el estado no cambia.

3. **Estado PANIC:**
   - **Descripción:** Este estado se activa en situaciones de emergencia.
   - **Comportamiento:**
     - Si se recibe la letra 'p' por el puerto serie, se responde con una 'P' mayúscula.
     - LED1 y el buzzer parpadean intermitentemente durante 20 segundos.
     - Después de 20 segundos, el LED1 permanece encendido, el buzzer se apaga y el relé se activa.
     - Además de la activación por el puerto serie, el estado PANIC también puede ser activado por un botón pull-up que, al ser presionado, cambia su valor a false. La activación por medio del botón tiene prioridad sobre el puerto serie. 

## Requisitos Técnicos

1. **Comunicación Serie:**
   - Utiliza `UnBufferedSerial` para la comunicación serie.
   - La recepción de caracteres por el puerto serie controla los cambios de estado.

2. **Medición de Tiempo:**
   - Para medir el tiempo, se utiliza `chrono::duration_cast<chrono::seconds>(timer.elapsed_time()).count()`, que funciona de manera similar a `millis()` en Arduino, pero obteniendo valores en segundos.
   - Esta medición es crucial para determinar el tiempo transcurrido en el estado MONITOR y para gestionar el parpadeo del LED1 y el buzzer en el estado PANIC.
   - Se optó por utilizar esa forma de medir el tiempo para no utilizar ningun temporizador que pudiera ser bloqueante.
