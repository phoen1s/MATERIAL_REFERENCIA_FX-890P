# Especificaciones del conector de 30 pines

　La Z-1/FX-890P tiene dos conectores para conectar circuitos externos: un conector de borde de tarjeta y un conector de 30 pines.
Esta sección explica el conector de 30 pines.

**1. Disposición de los pines**  

　Las especificaciones del conector de 30 pines son compatibles con otras computadoras de bolsillo Casio (VX-3, VX-4, FX-870P).  

<p align="center">
  <img src="https://github.com/user-attachments/assets/df4f140b-5c02-46c6-83cf-bbcd858c5cc7" width="300" height="80"><br>
  Disposición de pines vista desde el frente del conector
</p>

<div align="center">

| Número PIN | Nombre | Dirección | Explicación |
|-----------:|--------|-----------|-------------|
| 1  | Vcc | — | Salida de +5V |
| 2  | PB8 | in | RS-232C DSR |
| 3  | PB7 | in | RS-232C CD |
| 4  | PB6 | in | RS-232C CTS |
| 5  | PB5 | in | No usado |
| 6  | PB4 | in | Impresora BUSY |
| 7  | PB3 | in | No usado |
| 8  | PB2 | in | No usado |
| 9  | RXD | in | RS-232C DATA |
| 10 | PA4 | out | INIT (activo en nivel bajo) |
| 11 | PA3 | out | STB (activo en nivel bajo) |
| 12 | PA2 | out | RS-232C DTR |
| 13 | PA1 | out | RS-232C RTS |
| 14 | TXD | out | RS-232C DATA |
| 15 | RST | out | RESET |
| 16 | DB4 | in/out | Bus de datos |
| 17 | DB5 | in/out | Bus de datos |
| 18 | DB3 | in/out | Bus de datos |
| 19 | DB6 | in/out | Bus de datos |
| 20 | DB2 | in/out | Bus de datos |
| 21 | DB7 | in/out | Bus de datos |
| 22 | DB1 | in/out | Bus de datos |
| 23 | DB8 | in/out | Bus de datos |
| 24 | A0  | out | Bus de direcciones |
| 25 | A1  | out | Bus de direcciones |
| 26 | A2  | out | Bus de direcciones |
| 27 | OE  | out | Output Enable |
| 28 | WE  | out | Write Enable |
| 29 | CS3 | out | Selección de chip 3 |
| 30 | GND | — | Tierra |

</div>
※ La dirección in/out indica el sentido de la señal visto desde el pocket computer.  


**2. Características eléctricas**  

Los niveles de tensión son TTL  
(HI = 5V / LOW = 0V)  
	•	Corriente permitida:  
	•	IOL = 4 mA  
	•	IOH = 2 mA  
  
Por lo tanto, está bien conectar directamente las líneas de señal a circuitos integrados TTL, pero si se toma la alimentación desde el lado de la Pocket Computer (calculadora de bolsillo), es necesario tener cuidado de no colocar mucha carga. (No está claro hasta qué punto la línea de alimentación puede soportar la carga...)  

Para mayor seguridad, es mejor tomar la alimentación desde una fuente externa.  
La sincronización de cada línea de señal es la siguiente:  
<p align="center">
  <img src="https://github.com/user-attachments/assets/abdaca9c-0972-4ee7-bc3a-ed99dd6228de" width="300" ><br>
  Disposición de pines vista desde el frente del conector
</p>  
...Funciona de esta manera.  
Es decir, el bus de direcciones y el bus de datos son válidos solo en el momento en que CS3 está en estado LOW y WE o OE cambian de LOW a HIGH (flanco de subida). En cualquier otro momento, su estado es indeterminado.  
Por lo tanto, especialmente al enviar datos, puede ser necesario capturar (latch) el bus de datos en ese momento específico, dependiendo del caso.  
Por cierto, el momento para enviar datos se obtiene mediante WE, y el momento para recibir datos se obtiene mediante OE  <br><br>  
  
  **３. Entrada/Salida de Datos**  

　
Al manipular cada línea de señal desde un programa, básicamente se debe usar la BIOS. Específicamente, las funciones BIOS 02H, 2AH～2CH y 70H～71H están relacionadas con el conector de 30 pines.  

En pocas palabras, el conector de 30 pines es una interfaz que permite intercambiar datos de 8 bits con hasta 8 circuitos externos, posee 4 líneas de entrada, 4 líneas de salida y un puerto serie.  

Si solo se conecta un circuito externo, el bus de direcciones puede ignorarse, lo que simplifica considerablemente tanto el circuito como el programa. Por ejemplo, si se ignora el bus de direcciones, cualquier dirección especificada en las funciones BIOS 70H～71H será equivalente.  

(Añadido el 23/6/2011)  
Sobre cómo acceder al conector de 30 pines desde BASIC.  

Las líneas de dirección y datos del conector de 30 pines están conectadas a los puertos de E/S &H2C0 en adelante.  
Ejemplo:  
Dirección 0 del conector de 30p = Dirección de E/S &H2C0  
Dirección 1 del conector de 30p = Dirección de E/S &H2C1  

Para obtener datos desde E/S con comandos BASIC, se usa la instrucción INP().  
Ejemplo:  
Para obtener datos de la dirección 0 del conector de 30p: A = INP(&H2C0)  

Para enviar datos a E/S con comandos BASIC, se usa la instrucción OUT.  
Ejemplo:  
Para enviar el valor 12 a la dirección 0 del conector de 30p: OUT &H2C0, 12  

**４. Sobre este documento**  

Este documento es una reedición editada de los artículos relacionados con el conector de 30 pines publicados en «Z-1/FX-890P Katsuyō Kenkyū (Kōgakusha)».  

Además, por favor, no consulte el contenido de este documento con Kōgakusha ni con CASIO.  


