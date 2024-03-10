# Energia-LMIC-MSP430F5529 (Bajo Consumo)

Es una mejora o adaptación en español de la libreria Arduino-LMIC para que pueda funcionar con el IDE Energia de Texas-Instrument desarrollada por [DeuxVis](https://github.com/DeuxVis). Esta aplicación tambien facilmente se puede usar en LoraWAN / [The Things Network](https://github.com/DeuxVis).

Personalmente, solo lo he probado en MSP430F5529.

## Como conectar :

Para Usar en este Launchpad y con el modulo (RFM95W) o SX1276, se conecta de la siguiente manera :

                                              | Launchpad | RFM95 |
                                              | --------- | ----- |
                                              | 3V3       | 3.3V  |
                                              | GND       | GND   |
                                              | P1.6      | DIO0  |
                                              | P3.5      | DIO1  |
                                              | P3.2      | SCK   |
                                              | P3.1      | MISO  |
                                              | P3.0      | MOSI  |
                                              | P7.4      | RESET |
                                              | P2.2      | NSS   |

Esta porción de código abajo, es la configuración adicional que se tuvo que modificar para que funcionara con Energia.

```
const lmic_pinmap lmic_pins = {
    18,   //nss
    LMIC_UNUSED_PIN,    //rxtx
    17,  //rst
    {5, 30, LMIC_UNUSED_PIN},  //dio0,1,2
};
```

Aqui se puede mostrar visualmente:

[![Energia.jpg](https://i.postimg.cc/cLyrknrV/Energia.jpg)](https://postimg.cc/w72q735V)

# Librería Arduino-LMIC

Este repositorio contiene la biblioteca IBM LMIC (LoraMAC-in-C), ligeramente
modificado para ejecutarse en el entorno Energía, permitiendo utilizar el SX1272,
Transceptores SX1276 y módulos compatibles (como algunos HopeRF RFM9x
módulos).

## Instalación de esta nueva biblioteca en el entorno de Energía

Para instalar esta biblioteca:

- Descargue este archivo zip desde github usando el botón "Descargar ZIP" y
  instálelo usando el IDE ("Sketch" -> "Incluir biblioteca" -> "Agregar .ZIP
  Biblioteca...")
- Clona este repositorio de git en tu carpeta de cuaderno de bocetos/bibliotecas.

Para obtener más información, consulte https://www.arduino.cc/en/Guide/Libraries

## Features

La biblioteca LMIC proporciona una LoRaWAN Clase A y Clase B bastante completa.
implementación, soportando las bandas EU-868 y US-915. Sólo un limitado
Varias funciones se probaron utilizando este puerto en hardware Arduino, así que tenga cuidado.
Tenga cuidado al utilizar cualquiera de las funciones no probadas.

Lo que ciertamente funciona:

- Envío de paquetes por enlace ascendente, teniendo en cuenta el ciclo de trabajo.
- Comprobación de cifrado y integridad de mensajes.
- Recibir paquetes de enlace descendente en la ventana RX2.
- Frecuencias personalizadas y configuraciones de velocidad de datos.
- Activación inalámbrica (OTAA / unión).

Lo que no se ha probado:

- Recibir paquetes de enlace descendente en la ventana RX1.
- Recepción y procesamiento de comandos MAC.
- Operación clase B.

Si prueba una de estas funciones no probadas y funciona, asegúrese de dejar que
háganoslo saber (crear un problema de github es probablemente la mejor manera de hacerlo).

## Configuración

Se pueden configurar o desactivar varias funciones editando el
Archivo `config.h` en la carpeta de la biblioteca. Desafortunadamente el Arduino
El entorno no ofrece ninguna forma de hacer esto (tiempo de compilación).
configuración del boceto, así que tenga cuidado de volver a verificar su
configuración cuando cambia entre bocetos o actualiza la biblioteca.

Como mínimo, debería configurar el tipo correcto de transceptor (SX1272
vs SX1276) en config.h, la mayoría de los demás valores deberían estar bien en su
valores predeterminados.

## Hardware Soportado

Esta biblioteca está diseñada para usarse con transceptores LoRa simples,
conectándose a ellos mediante SPI. En particular, el SX1272 y el SX1276
Se admiten familias (que deben incluir SX1273, SX1277, SX1278 y
SX1279 que sólo se diferencian en las frecuencias disponibles, anchos de banda y
factores de dispersión). Ha sido probado tanto con SX1272 como con SX1276.
chips, utilizando la placa de evaluación Semtech SX1272 y el HopeRF RFM92
y placas RFM95 (que supuestamente contienen un chip SX1272 y SX1276
respectivamente).

Esta biblioteca contiene una pila LoRaWAN completa y está destinada a impulsar
estos transceptores directamente. _No_ está destinado a ser utilizado con
dispositivos de pila completa como el Microchip RN2483 y el Embit LR1272E.
Estos contienen un transceptor y un microcontrolador que implementa el
pila LoRaWAN y expone una interfaz serie de alto nivel en lugar de la
Interfaz transceptor SPI de bajo nivel.

Esta biblioteca está diseñada para usarse dentro del entorno Arduino. Él
debe ser independiente de la arquitectura, por lo que debería ejecutarse en AVR "normal"
arduinos, pero también en los basados en ARM, y se ha visto cierto éxito
También se ejecuta en la placa ESP8266. Fue probado en el Arduino Uno,
Pinoccio Scout, Teensy LC y 3.x, ESP8266, Arduino 101.

## Conexiones

Tenga en cuenta que el módulo SX1272 funciona a 3,3 V y probablemente no le gusten los 5 V encendidos.
sus pines (aunque la hoja de datos no dice nada sobre esto, y mi
El transceptor obviamente no se rompió después de usar accidentalmente E/S de 5 V para
unas pocas horas). Para estar seguro, asegúrese de utilizar una palanca de cambios de nivel o un
Arduino funcionando a 3,3V. La placa de evaluación Semtech tiene resistencias de 100 ohmios en
serie con todas las líneas de datos que podrían evitar daños, pero no lo haría
cuenta con eso.

### Power

The SX127x transceivers need a supply voltage between 1.8V and 3.9V.
Using a 3.3V supply is typical. Some modules have a single power pin
(like the HopeRF modules, labeled 3.3V) but others expose multiple power
pins for different parts (like the Semtech evaluation board that has
`VDD_RF`, `VDD_ANA` and `VDD_FEM`), which can all be connected together.
Any _GND_ pins need to be connected to the Arduino _GND_ pin(s).

### SPI

La forma principal de comunicarse con el transceptor es a través de SPI.
(Interfaz Periférica Serial). Esto utiliza cuatro pines: MOSI, MISO, SCK y
SS. Los tres primeros deben estar conectados directamente: entonces MOSI a MOSI,
MISO a MISO, SCK a SCK. Dónde se encuentran estos pines en tu Arduino
varía, consulte, por ejemplo, la sección "Conexiones" del [Arduino SPI
documentación](SPI).

La conexión SS (selección de esclavo) es un poco más flexible. En el SPI
lado esclavo (el transceptor), este debe conectarse al pin
(normalmente) etiquetado como _NSS_. En el lado del maestro SPI (Arduino), este pin
Se puede conectar a cualquier pin de E/S. La mayoría de los Arduinos también tienen un pin con la etiqueta "SS",
pero esto sólo es relevante cuando el Arduino funciona como esclavo SPI, lo cual
No es el caso aquí. Cualquiera que sea el pin que elijas, debes informarle al
biblioteca qué pin utilizó a través del mapeo de pines (ver más abajo).

[SPI]: https://www.arduino.cc/en/Reference/SPI

### DIO pins

Los pines DIO (E/S digital) en la placa del transceptor se pueden configurar
para diversas funciones. La biblioteca de países de ingresos bajos y medianos los utiliza para obtener un estatus instantáneo
información del transceptor. Por ejemplo, cuando una transmisión LoRa
comienza, el pin DIO0 se configura como una salida TxDone. Cuando el
La transmisión está completa, el transceptor eleva el pin DIO0,
que puede ser detectado por la biblioteca de LMIC.

La biblioteca LMIC solo necesita acceso a DIO0, DIO1 y DIO2, el otro
Los pines DIOx se pueden dejar desconectados. En el lado de Arduino, pueden
conectarse a cualquier pin de E/S, ya que la implementación actual no utiliza
interrupciones u otras características especiales del hardware (aunque esto podría ser
añadido en la función, consulte también la sección "Tiempo").

En modo LoRa, los pines DIO se utilizan de la siguiente manera:

- DIO0: TxDone y RxDone
- DIO1: RxTimeout

En modo FSK se utilizan de la siguiente manera::

- DIO0: PayloadReady and PacketSent
- DIO2: TimeOut

Ambos modos necesitan sólo 2 pines, pero el traceptor no permite mapear
ellos de tal manera que todas las interrupciones necesarias se asignen a los mismos 2 pines.
Entonces, si se utilizan los modos LoRa y FSK, los tres pines deben estar
conectado.

Los pines utilizados en el lado de Arduino deben configurarse en el pin
mapeo en su boceto (ver más abajo).

### Reset

El transceptor tiene un pin de reinicio que se puede utilizar para restablecer explícitamente
él. La biblioteca LMIC utiliza esto para garantizar que el chip esté en un estado consistente.
estado al inicio. En la práctica, este pin se puede dejar desconectado, ya que
El transceptor ya estará en un estado normal al encenderlo, pero
conectarlo puede evitar problemas en algunos casos.

En el lado de Arduino, se puede utilizar cualquier pin de E/S. El número PIN utilizado debe
configurarse en el mapeo de pines (ver más abajo).

### RXTX

El transceptor contiene dos conexiones de antena independientes: una para RX
y uno para TX. Una placa transceptora típica contiene un interruptor de antena.
chip, que permite cambiar una sola antena entre estos RX y TX
conexiones. A un conmutador de antena de este tipo normalmente se le puede decir qué
su posición debe ser a través de un pin de entrada, a menudo etiquetado como _RXTX_.

La forma más sencilla de controlar el interruptor de la antena es utilizar el pin _RXTX_
en el transceptor SX127x. Este pin se establece automáticamente en alto durante TX
y bajo durante RX. Por ejemplo, las placas HopeRF parecen tener esto
conexión en su lugar, por lo que no exponen ningún pin _RXTX_ y el pin
se puede marcar como no utilizado en el mapeo de pines.

Algunas placas exponen el pin del conmutador de antena y, a veces, también el
Pasador SX127x _RXTX_. Por ejemplo, la placa de evaluación SX1272 llama al
el primero _FEM_CTX_ y el segundo _RXTX_. Nuevamente, simplemente conectando estos
junto con un cable de puente es la solución más sencilla.

Alternativamente, o si el pin SX127x _RXTX_ no está disponible, LMIC puede ser
configurado para controlar el interruptor de antena. Conecte el interruptor de la antena.
pin de control (por ejemplo, _FEM_CTX_ en la placa de evaluación de Semtech) a cualquier E/S
pin en el lado de Arduino y configure el pin utilizado en el mapa de pines (consulte
abajo). No está del todo claro por qué _no_ querría que el transceptor
Sin embargo, controla la antena directamente.

### Mapeo de pines

Como se describió anteriormente, la mayoría de las conexiones pueden usar pines de E/S arbitrarios en el
Lado Arduino. Para informar a la biblioteca de países de ingresos bajos y medianos sobre esto, se utiliza una estructura de mapeo de pines.
se utiliza en el archivo de boceto.

Por ejemplo, esto podría verse así:

     lmic_pinmap lmic_pins = {
         .nss = 6,
         .rxtx = LMIC_UNUSED_PIN,
         .primero = 5,
         .dio = {2, 3, 4},
     };

Los nombres se refieren a los pines del lado del transceptor, los números se refieren
a los números de pin de Arduino (para usar los pines analógicos, use constantes como
`A0`). Para los pines DIO, los tres números se refieren a DIO0, DIO1 y DIO2.
respectivamente. Cualquier pasador que no sea necesario debe especificarse como
`LMIC_UNUSED_PIN`. Se requieren los pines nss y dio0, los demás pueden
potencialmente excluidos (dependiendo de los entornos y requisitos,
consulte las notas anteriores para saber cuándo se puede o no omitir un pin).

El nombre de esta estructura siempre debe ser `lmic_pins`, que es un nombre especial
reconocido por la biblioteca.lways be `lmic_pins`, which is a special name
recognized by the library.

#### LoRa Nexus de Ideetron

This board uses the following pin mapping:

    const lmic_pinmap lmic_pins = {
        .nss = 10,
        .rxtx = LMIC_UNUSED_PIN,
        .rst = LMIC_UNUSED_PIN, // hardwired to AtMega RESET
        .dio = {4, 5, 7},
    };

## Archivos Ejemplo

Esta biblioteca proporciona actualmente tres ejemplos:

- `ttn-abp.ino` muestra una transmisión básica de "¡Hola, mundo!" mensaje
  utilizando el protocolo LoRaWAN. Contiene algunas configuraciones de frecuencia y
  claves de cifrado destinadas a su uso con The Things Network, pero estas
  también corresponden a la configuración predeterminada de la mayoría de las puertas de enlace, por lo que
  También debería funcionar con otras redes y puertas de enlace. este ejemplo
  utiliza activación por personalización (ABP, preconfiguración de un dispositivo
  direcciones y claves de cifrado), y no emplea conexiones inalámbricas
  activación.

  Recepción de paquetes (en respuesta a la transmisión, utilizando el RX1 y
  También se admiten ventanas de recepción RX2).

- `ttn-otaa.ino` también envía un "¡Hola mundo!" mensaje, pero usa más
  la activación aérea (OTAA) para unirse primero a una red para establecer una
  claves de sesión y seguridad. Esto fue probado con The Things Network,
  pero también debería funcionar (quizás con algunos cambios) para otras redes.

- `raw.ino` muestra cómo acceder a la radio en un nivel algo bajo,
  y permite enviar paquetes sin procesar (no LoRaWAN) entre nodos directamente.
  Esto es útil para verificar la conectividad básica y cuando no hay ninguna puerta de enlace.
  disponible, pero este ejemplo también pasa por alto las verificaciones del ciclo de trabajo, así que tenga cuidado.
  Tenga cuidado al cambiar la configuración.

## Velocidad de datos del enlace descendente

Tenga en cuenta que la velocidad de datos utilizada para los paquetes de enlace descendente en la ventana RX2
El valor predeterminado es SF12BW125 según la especificación, pero algunas redes
usan valores diferentes (iot.semtech.com y The Things Network usan
SF9BW). Al utilizar la activación personalizada (ABP), es su
responsabilidad de establecer la configuración correcta, p. agregando esto a tu
sketch (después de llamar a `LMIC_setSession`). `ttn-abp.ino` ya lo hace
este.

      PIBM.dn2Dr = DR_SF9;

Cuando se utiliza OTAA, la red comunica la configuración de RX2 en el
mensaje de aceptación de unirse, pero la biblioteca de LMIC no procesa actualmente
estos ajustes. Hasta que esto se resuelva (consulte el problema n.° 20), debe
configure manualmente la tasa RX2, _después_ de unirse (consulte el manejo de
`EV_JOINED` en `ttn-otaa.ino` como ejemplo).

## Licencia

La mayoría de los archivos fuente de este repositorio están disponibles en el
Licencia pública Eclipse v1.0. Los ejemplos que utilizan un más liberal
licencia. Parte del código AES está disponible bajo LGPL. Consulte cada
archivo fuente individual para más detalles.
