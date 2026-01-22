# Método de Control del Controlador LCD para FX-890P/Z-1


<p>Al intentar crear juegos con gráficos completos en la serie Z-1/FX-890P (en adelante, Z-1), el problema principal es cómo mantener la velocidad.<br>
Ciertamente, utilizando las rutinas de procesamiento gráfico proporcionadas por la BIOS, se pueden mostrar gráficos de manera relativamente fácil, pero para su uso en juegos son demasiado lentas y, además, la estructura de VRAM es demasiado compleja.<br>
Por lo tanto, como método para resolver estos problemas de una vez, se propone operar directamente el controlador LCD (en adelante, LCDC). Al poder operar el LCDC, se pueden enviar datos directamente al LCD y elegir entre varios modos de visualización el más óptimo en cada momento.<br>
Así que a continuación explicaré los métodos de operación del LCDC, entre otros.<br>
Nota: Se omitirán las partes que se consideren menos importantes.</p>

<p>※ <a href="file/FX-LCDC.LZH">Programa de ejemplo</a></p>

## 1. Método de operación del LCDC

<p>El LCDC utilizado en la Z-1 es el HD66108T de Hitachi. Se opera para escribir datos en el LCD, pero ¿cómo se opera realmente? El LCDC tiene 7 registros internos que se utilizan para su operación.<br>
Estos registros internos están conectados a la RAM principal, y sus direcciones son:</p>
<pre>
（1010 xxxx xxxx xxxx xxx0B）：Registro de dirección
（1010 xxxx xxxx xxxx xxx1B）：Otros registros
</pre>
<p>Aquí, "x" puede ser 0 o 1. (Por ejemplo, el registro de dirección puede ser A0000H o A0002H, es lo mismo).<br>
Al leer o escribir en los registros internos, primero se establece en el "registro de dirección":</p>
<pre>
b7　b6　b5　b4　b3　b2　b1　b0
│　│　│　│　│　└─┴─┴─ Número de registro
│　│　│　└─┴─────── Sin usar (establecer como 0)
│　│　└─────────── DISP
│　└───────────── STBY
└─────────────── BUSY
</pre>
<p>DISP: Encendido/apagado del LCD (1-encendido)</p>
<p>Luego se leen o escriben los datos en "otros registros".<br>
Dado que normalmente no se utilizan BUSY y STBY, se omite su explicación (establecer como 0).</p>

## 2. Registros internos

<p>La correspondencia entre cada registro y su número es la siguiente:</p>
<pre>
0：DRAM（memoria de visualización）
1：XAR（dirección X）
2：YAR（dirección Y）
3：FCR（control）
4：MDR（modo）
5：CSR（selección C）
</pre>
<p>De estos, los números 4 y 5 probablemente no se usarán, por lo que se omite su explicación.</p>
<ul>
    <li><strong>Registro 0 (DRAM)</strong><br>
        Los datos escritos aquí se envían al LCD. La dirección se autoincrementa en la dirección especificada por INC del registro FCR.<br>
        Cuando WLS del registro FCR es "1", los 2 bits superiores se invalidan.</li>
    <li><strong>Registro 1 (XAR)</strong><br>
        Especifica la dirección X del LCD a la que accede el registro DRAM.</li>
    <li><strong>Registro 2 (YAR)</strong><br>
        Especifica la dirección Y del LCD a la que accede el registro DRAM.</li>
    <li><strong>Registro 3 (FCR)</strong><br>
        Se utiliza para cambiar el modo de operación.</li>
</ul>
<pre>
b7　b6　b5　b4　b3　b2　b1　b0
│　│　│　│　│　└─┴─┴─ DUTY（normalmente 101B）
│　│　│　└─┴─────── ROS（normalmente 10B）
│　│　└─────────── PON（normalmente 1B）
│　└───────────── WLS
└─────────────── INC
</pre>
<p>INC: Dirección de incremento de dirección (0-dirección Y, 1-dirección X)<br>
WLS: Selección de longitud de palabra (0-8 bits, 1-6 bits)</p>
<p>Dado que normalmente no se cambiarán PON, ROS y DUTY, se omite su explicación (establecer como: PON:1B, ROS:10B, DUTY:101B).</p>
<p>Por cierto, el modo de operación estándar de la Z-1 es:</p>
<pre>
DISP: Encendido
INC: Dirección Y
WLS: 6 bits
</pre>
<p>Pero al crear juegos, es más fácil procesar si se establece como:</p>
<pre>
DISP: Encendido
INC: Dirección X
WLS: 8 bits
</pre>
<p>A partir de ahora, asuma que está en este modo de operación a menos que se indique lo contrario.</p>

## 3. Estructura de VRAM

<p>Tenga cuidado porque la LCD de la Z-1 está dividida en izquierda y derecha en el centro de la pantalla.</p>
<p>La relación entre la pantalla y las direcciones es:</p>
<pre>
(4,0) (5,0) ～ (16,0) (4,32) ～ (16,32) (17,32)
(4,1) (5,1) ～ (16,1) (4,33) ～ (16,33) (17,33)
・
・
・
(4,30) (5,30) ～ (16,30) (4,62) ～ (16,62) (17,62)
(4,31) (5,31) ～ (16,31) (4,63) ～ (16,63) (17,63)
</pre>
<p>Pero en realidad, no todo este rango se muestra en pantalla; existen partes que no se muestran.<br>
Tomando la línea superior como ejemplo, el rango que realmente se muestra en pantalla es:</p>
<p align="center"><img src="/web/20230603022946im_/http://stear.c.ooco.jp/fx890p/fxlcdc2.gif" align="middle" width="452" height="128"></p>
<p>En esta relación, la parte "blanca" no se muestra en pantalla, y las partes "roja" y "azul" son las que se muestran en pantalla.<br>
Como se puede ver, hay partes no mostradas en los lados izquierdo y derecho y en el centro, y además en el centro las LCD izquierda y derecha se superponen a la mitad.</p>

## 4. Ejemplo concreto de uso

<p>Hasta ahora he explicado sobre el LCDC, pero solo con esto puede que no se entienda qué hacer, así que aquí explicaré usando como ejemplo un programa que realmente envía gráficos al LCD.</p>
<p>Primero, vea "PRINT.ASM" incluido con este archivo.<br>
Este programa es una subrutina que envía datos de VRAM virtual de 25*32 bytes desde la etiqueta "SCREEN" al LCD.<br>
A continuación explicaré siguiendo el flujo de este programa.</p>
<p>Primero se cambia el modo de operación del LCDC, pero es útil cambiar el modo solo al acceder al LCD de esta manera, ya que permite usar la BIOS para mostrar texto, etc.<br>
En el siguiente bucle PRLP1, se escriben datos en el LCD, pero como se explicó anteriormente, la dirección Y de la mitad derecha e izquierda del LCD difiere en 32, y además en el centro, la derecha y la izquierda se superponen a la mitad (4 bits cada una), por lo que se escriben por separado.<br>
PRSUB transfiere 13 bytes de datos desde la dirección especificada por el registro SI a las direcciones LCD (4,DL)～(16,DL). Se transfiere en el bucle PRSLP1, pero las instrucciones de pila aquí se usan para esperar. Esto se debe a que el LCDC tarda un tiempo en procesar después de escribir datos en el registro DRAM.<br>
Finalmente, se restaura el modo de operación estándar y se termina. Nota: Si no se establece el registro del LCDC en el registro DRAM en este momento, habrá problemas al volver a BASIC.</p>
<p>Como programa de ejemplo que usa esta subrutina, he preparado "STAR.ASM" para referencia. Al ejecutarlo, las estrellas fluyen por la pantalla.<br>
(El mismo principio de funcionamiento que el desplazamiento del fondo en "2D-SHOT").</p>

## 5. Método de visualización de caracteres

<p>Con la rutina anterior se pueden mostrar gráficos, pero solo con eso no se puede crear un juego. Sí, es necesario conocer el método de visualización de caracteres.<br>
Así que explicaré cómo mostrar caracteres en la VRAM virtual. (Aquí se explica para caracteres de 8*8 puntos).</p>
<p>El problema al mostrar caracteres es la coordenada X. Es fácil mover la coordenada Y en unidades de 1 punto (solo cambiar la dirección), pero la coordenada X es 1 byte = 8 puntos debido a la estructura de la VRAM virtual, por lo que si se hace directamente, solo se moverá 8 puntos en la dirección X.<br>
Entonces, ¿qué hacer? Se puede realizar un desplazamiento de bits.<br>
Concretamente, divida la coordenada X entre 8, sume el resultado a la dirección, desplace los datos del carácter hacia la derecha según el resto e escriba esos datos en la VRAM virtual.<br>
Por ejemplo:</p>
<p>★ Mostrar datos de patrón 37H en (21,3). Sin embargo, el tamaño de la pantalla de visualización es de 25 bytes en la dirección X y la VRAM virtual comienza en 2000H.</p>
<p>Dividir 21 entre 8 da 2 y resto 5, por lo que desplazar los datos del patrón cinco veces hacia la derecha.</p>
<p align="center">00110111 00000000<br>
↓<br>
00000001 10111000</p>
<p>Escribir esto desde la dirección 204DH (2000H+3*25+2) con la instrucción OR.</p>
<p>El programa que implementa este algoritmo es "CHRSET.ASM".<br>
Además, he preparado "MOVE.ASM" como programa de ejemplo que lo usa.<br>
Al ejecutarlo, el carácter se mueve libremente por la pantalla.</p>

## 6. Finalmente

<p>Con esto termino la explicación sobre el LCDC.<br>
Si se entiende más o menos lo escrito aquí, crear juegos con gráficos completos no es tan difícil.<br>
Además, los programas presentados aquí son software libre. El autor renuncia a todos los derechos relacionados, por lo que puede modificarlos, reutilizar rutinas, etc., libremente.<br>
Olvidé mencionarlo, para los programas de ejemplo, reserve aproximadamente 500H en el área de lenguaje máquina, ensámblelo con el ensamblador incorporado y luego ejecútelo desde 2000H.<br>
Presione cualquier tecla excepto [BRK] para finalizar.</p>

## 7. Referencias bibliográficas


