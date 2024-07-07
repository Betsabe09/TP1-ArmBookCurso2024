# Módulo USER-CONTROL

Este proyecto se implementa utilizando Mbed, sin el uso de interrupciones y sin estructuras complejas. Los estados definidos son `OFF`, `MONITOR` y `PANIC`, y el cambio entre estos estados se controla a través de la presión de botones pull-up, que se activan con un valor false.

## Documentación

Puedes encontrar la documentación completa generada por Doxygen [aquí](https://betsabe09.github.io/TP1-ArmBookCurso2024/user-control/Docs/html/index.html).

## Descripción

### Estados y Transiciones

1. **Estado OFF:**
   - **Descripción:** Es el estado inicial del programa, que puede ser `OFF DEAD` o `OFF TERMINAL`.
   - **Comportamiento:**
     - **OFF DEAD:** Se encienden los LEDs por 3 segundos y luego se apagan. Con todos los LEDs están apagados, no se transmiten ni reciben mensajes y los timers están detenidos.
     - **OFF TERMINAL:** Los LEDs 1 y 2 están encendidos, se envía el carácter 'o' por el puerto serie y se espera una respuesta 'O' mayúscula. Si el tiempo de espera supera los 5 segundos, ambos LEDs parpadean intermitentemente cada 1 segundo hasta recibir una respuesta válida. Luego, los LEDs permanecen encendidos por 3 segundos antes de apagarse y regresar a `OFF DEAD`.
     - El estado se alterna entre `OFF DEAD` y `OFF TERMINAL` al presionar el botón 3.

2. **Estado MONITOR:**
   - **Descripción:** En este estado, el sistema monitorea constantemente el puerto serie.
   - **Comportamiento:**
     - Se activa con el botón 1.
     - LED1 está encendido, se envía el carácter 'm' por el puerto serie y se espera una respuesta 'M' mayúscula.
     - Si el tiempo de espera supera los 5 segundos, el LED1 parpadea intermitentemente cada 1 segundo hasta recibir una respuesta válida.
     - Al recibir una respuesta válida, el timer se resetea y el LED1 permanece encendido.

3. **Estado PANIC:**
   - **Descripción:** Este estado se activa en situaciones de emergencia.
   - **Comportamiento:**
     - Se activa con el botón 2 o recibiendo una 'P' durante el estado `MONITOR` o `PANIC`.
     - LED2 está encendido y LED1 se apaga.
     - Se envía el carácter 'p' por el puerto serie y se espera una respuesta 'P' mayúscula.
     - Si el tiempo de espera supera los 5 segundos, el LED2 parpadea intermitentemente cada 1 segundo.
     - Al recibir una respuesta válida, el timer se detiene y LED2 permanece encendido.

## Requisitos Técnicos

1. **Comunicación Serie:**
   - Utiliza `UnBufferedSerial` para la comunicación serie.
   - La recepción de caracteres por el puerto serie controla los cambios de estado.

2. **Medición de Tiempo:**
   - Para medir el tiempo, se utiliza `chrono::duration_cast<chrono::seconds>(timer.elapsed_time()).count()`, que funciona de manera similar a `millis()` en Arduino, pero obteniendo mediciones en segundos.
   - Esta medición es crucial para determinar el tiempo transcurrido en los estados y para gestionar el parpadeo de los LEDs en los 3 estados.
   - Se eligió utilizar este método para evitar código bloqueante.
