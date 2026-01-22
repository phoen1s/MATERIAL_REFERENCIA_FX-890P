# Técnica de Interpretación de Música de Fondo (BGM)


<p>BGM (Música de Fondo) se refiere a la ejecución simultánea de un procesamiento (como un juego) y la reproducción de música.<br>
En computadoras y consolas de videojuegos, tienen chips de sonido dedicados, por lo que el BGM es fácil de manejar, pero nuestra Pocket Computer no tiene hardware tan avanzado (aunque algunas personas conectan chips de sonido), e incluso algunos modelos ni siquiera tienen función de sonido (sonido BEEP).<br>
Esto probablemente se debe a que las Pocket Computers fueron vistas solo como extensiones de calculadoras (desde la perspectiva de los fabricantes), lo cual es realmente lamentable.<br>
Sin embargo, afortunadamente la FX-890P tiene un zumbador incorporado, por lo que producir sonidos es relativamente fácil, pero para reproducir música se requiere conocimiento bastante avanzado y especializado.</p>

<p>Este informe resume la técnica para realizar BGM en Pocket Computers.<br>
Básicamente asume que el BGM se hará en ensamblador (porque hacer BGM solo con BASIC es imposible), así que quienes no sepan ensamblador deberían estudiarlo primero.<br>
Aquí se usa la FX-890P como ejemplo, pero probablemente métodos similares puedan usarse en otros modelos.</p>

<p>※ <a href="https://github.com/phoen1s/MATERIAL_REFERENCIA_FX-890P/raw/main/Musica%20de%20fondo/BGMDOC.LZH">
Programa de ejemplo
</a></p>

## 1. ¿Qué es el "sonido"?

<p>Como es sabido, la naturaleza del "sonido" es la "vibración de un objeto".<br>
El período de esta vibración determina el tono, la amplitud determina el volumen, y los cambios en el período y la amplitud determinan el timbre. Manipular estos elementos es lo que comúnmente llamamos "tocar música", así que para hacer música con computadoras solo necesitamos saber cómo implementar estos 3 elementos.</p>

<p>Lo que se explicará a continuación es sobre "tono" y "volumen", no sobre "timbre", pero esto puede lograrse manipulando "tono" y "volumen", así que experimenten por su cuenta.</p>

## 2. Produciendo tonos

<p>Antes de explicar cómo producir tonos, primero necesitamos saber cómo crear "vibración de un objeto", así que comenzaremos con eso.</p>

<p>La forma más sencilla de crear "vibración de un objeto" es usar el altavoz piezoeléctrico incorporado en la FX-890P.<br>
El control del altavoz piezoeléctrico es muy simple: al enviar 3 a la E/S 206H, el voltaje en el altavoz cambia entre 0[V]←→5[V], produciendo así sonido.<br>
Sin embargo, la forma de onda resultante está lejos de ser una curva sinusoidal, siendo más bien pulsada, por lo que el sonido tiende a ser metálico, pero eso es inevitable.</p>
<p>Ejemplo:</p>
<pre>
MOV DX,206H
MOV AL,3
OUT DX,AL
</pre>

<p>Ahora que sabemos cómo producir sonido, vayamos al tema principal.<br>
Para producir un tono específico, necesitamos generar vibración (sonido) a intervalos periódicos fijos, lo que requiere conocer la relación entre tono y período.<br>
La relación entre cada nota y la frecuencia es:</p>
<p>Cuando la octava sube uno, la frecuencia se duplica.<br>
El "La" de la octava 4 es 440[Hz]</p>
<p>Basado en esto, yo uso:</p>
<p>Frecuencia[Hz] = 440 * 2^(O-4 + C/12)</p>
<p>O: Octava<br>
C: Nota (0=La, 1=La#, 2=Si, 3=Do de octava+1,…10=Sol de octava+1, 11=Sol# de octava+1)</p>

<p>No estoy seguro si esta fórmula es correcta, así que tómenla solo como referencia. (Aunque suena bastante bien).<br>
La relación entre frecuencia y período es:</p>
<p>Período[segundos] = 1 / Frecuencia[Hz]</p>

<p>Así podemos conocer el período necesario para producir el tono deseado.<br>
Luego, simplemente hacemos vibrar el altavoz piezoeléctrico según este período para producir el tono.</p>

## 3. ¿Cómo medir el período?

<p>Una vez que conocemos el período para cada tono, el siguiente problema es "cómo medir el período".<br>
Esta parte es crucial, ya que de ella depende si el BGM puede implementarse o no.</p>

<p>Si solo fuera "medir el período", podríamos usar un bucle de software para esperar, pero eso haría que durante la producción de sonido no pudiéramos hacer nada más, imposibilitando el "BGM" que es nuestro tema, así que esta opción queda descartada.</p>

<p>Entonces, ¿qué hacemos?</p>

<p>"Medir el período" significa "hacer algo a intervalos regulares", y las computadoras tienen una función perfecta para esto.<br>
¡Exacto! La "interrupción de temporizador".<br>
Esto permite que las interrupciones ocurran automáticamente a intervalos regulares, permitiendo así la reproducción de BGM en paralelo con el procesamiento del juego.<br>
La FX-890P permite especificar el período de interrupción del temporizador, así que configurando período de interrupción = período de sonido, podemos lograr BGM sin problemas, lo cual es muy conveniente.<br>
Incluso si el período de interrupción fuera fijo, habría métodos.<br>
Podríamos hacer de la reproducción musical el procesamiento principal y ejecutar el juego por interrupciones. Este método sacrificaría bastante la calidad de sonido, pero al menos permitiría implementar BGM.</p>

## 4. Interrupción de temporizador

<p>La FX-890P tiene 3 sistemas de temporizador, todos usados por el sistema de la Pocket Computer, por lo que usarlos hará que algunas funciones dejen de funcionar (aunque dependiendo del software, esto podría evitarse).<br>
Esto no es gran problema durante la ejecución de código máquina, pero en BASIC podría causar bloqueos, así que tengan cuidado.</p>

<p>De los 3 temporizadores, el temporizador 1 no se usa para funciones críticas (se usa en WAIT, BEEP, INKEY$, etc.), así que lo usaré como ejemplo para BGM.</p>

<p>El vector de interrupción del temporizador 1 es "INT 12H", el contador está en E/S 38H, la configuración del período está en E/S 3AH y 3CH para período A y B respectivamente, y la configuración de operación está en E/S 3EH.<br>
Como pueden ver, dependiendo de la configuración se pueden especificar dos períodos, pero en este caso no tiene sentido, así que para usar solo el período A ejecutamos:</p>
<pre>
MOV AX,0E001H
OUT 3EH,AX
</pre>

<p>El período B400H corresponde a 0.05[segundos], así que calculamos el valor deseado y configuramos:</p>
<pre>
MOV AX,valor_deseado
OUT 3AH,AX
XOR AX,AX
OUT 38H,AX
</pre>

<p>Así, "INT 12H" será llamado en el intervalo especificado, y solo necesitamos conectar allí nuestra rutina de producción de sonido.</p>
<pre>
PUSH ES
XOR AX,AX
MOV ES,AX
MOV DI,48H
MOV AX,OFFSET Rutina_procesamiento_BGM
STOSW
MOV AX,CS
STOSW
POP ES
</pre>

<p>Rutina_procesamiento_BGM:</p>
<pre>
PUSH AX
PUSH DX
MOV DX,206H
MOV AL,3
OUT DX,AL
MOV AX,8000H
OUT 2,AX
POP DX
POP AX
IRET
</pre>

<p>Cuando usen procesamiento por interrupción, no olviden restaurar las partes modificadas antes de volver a BASIC.</p>

<p>El procesamiento de BGM puede dividirse en análisis de datos musicales y configuración del período, y operación del altavoz piezoeléctrico. Si estas operaciones toman mucho tiempo, la velocidad general de ejecución disminuirá, así que deberían ser lo más livianas posible.<br>
Puntos importantes durante el procesamiento de interrupciones: al terminar, enviar 8000H a EOI (E/S 02H); tener cuidado con el flag de dirección al usar instrucciones de cadena; los segmentos no siempre son 0000H.</p>

## 5. ¿Cómo cambiar el volumen?

<p>Ahora podemos producir tonos, pero no debemos conformarnos solo con eso.<br>
Sin poder cambiar el volumen, no es suficiente.<br>
Poder cambiar el volumen permite modificar el volumen general de la pieza, y expresar timbres mediante cambios sutiles de volumen.</p>

<p>El volumen es la magnitud de la amplitud, así que cambiando la amplitud podemos cambiar el volumen.<br>
Entonces, ¿cómo reducimos la variación de amplitud del altavoz piezoeléctrico?<br>
Simplificando, reduciríamos el voltaje aplicado al altavoz, pero eso es imposible sin un convertidor D/A.</p>

<p>Cambiemos completamente nuestra perspectiva.</p>

<p>El altavoz piezoeléctrico produce sonido cuando cambia el voltaje aplicado, pero el sonido no se produce instantáneamente.<br>
En realidad, la velocidad de cambio físico del altavoz es más lenta que la del cambio de señal eléctrica, por lo que toma algo de tiempo hasta que el sonido se produce completamente.<br>
¿Qué pasaría si devolviéramos la señal al altavoz antes de que el sonido se produzca completamente?<br>
Obviamente, el volumen sería menor que en condiciones normales.<br>
Concretamente, haríamos:</p>
<p>Ejemplo:</p>
<pre>
MOV DX,206H
MOV AL,3
OUT DX,AL
OUT DX,AL ; Este es el truco
</pre>

<p>El ajuste de volumen se logra controlando el tiempo entre el primer OUT y el siguiente.<br>
Sin embargo, este tiempo es muy sensible; incluso 1 ciclo de reloj hace una gran diferencia en el volumen, así que probablemente necesiten rutinas de sonido específicas para cada volumen para lograr diferencias claras.</p>

## 6. Duración de las notas

<p>Ahora que saben cómo producir sonidos, hablemos sobre la duración del sonido.<br>
Esto es obvio para quienes tienen conocimientos musicales, pero para quienes no, puede ser difícil, así que lo explicaré.</p>

<p>En una pieza musical, el tempo se refiere concretamente a la duración de la nota entera.</p>
<p>Duración nota entera[segundos] = 240 / tempo</p>

<p>Las notas de media y cuarto son 1/2 y 1/4 de la duración de la entera respectivamente, y los puntillos aumentan la duración en 1.5 veces cada uno.</p>

<p>En el procesamiento real, contando las veces que se produce el sonido se puede conocer la duración a partir del período y el conteo, pero quizás sea mejor calcularlo de antemano.</p>

## 7. ¿Cómo hacer reproducción múltiple?

<p>Todas las explicaciones anteriores asumían sonido monofónico, pero también explicaré la reproducción múltiple... aunque me gustaría, nunca la he implementado realmente, así que solo explicaré la metodología.</p>

<p>Por cierto, para acordes de hasta 3 voces se pueden conectar rutinas de reproducción a cada uno de los 3 temporizadores (método usado en el programa de ejemplo).</p>

<hr>
<p>1. Método por interrupción de período fijo</p>
<p>El concepto básico es el mismo que TSS (Sistema de Tiempo Compartido), asignando secuencialmente el procesamiento de interrupción que ocurre periódicamente a cada parte.</p>

<table border="2">
    <tr><th>Número de interrupciones</th><th>Parte procesada</th></tr>
    <tr><td align="center">1</td><td align="center">A</td></tr>
    <tr><td align="center">2</td><td align="center">B</td></tr>
    <tr><td align="center">3</td><td align="center">C</td></tr>
    <tr><td align="center">4</td><td align="center">D</td></tr>
    <tr><td align="center">5</td><td align="center">A</td></tr>
    <tr><td align="center">6</td><td align="center">B</td></tr>
    <tr><td align="center">:</td><td align="center">:</td></tr>
</table>

<p>• El período de interrupción se fija al número de partes × la frecuencia del tono más alto (aquí ×4).</p>
<p>Si el tono más alto es La octava 5 y hay 4 partes:</p>
<p>1/(880*4) = 0.28[ms]</p>

<p>• Para producir tonos desde cada parte, se especifica cuántas divisiones del tono más alto producir.</p>
<p>Si la parte A produce sonido cada dos veces → La octava 4</p>

<p>Los problemas de este método: los tonos más altos se desafinan; las interrupciones ocurren incluso cuando no hay sonido, lo que es ineficiente; aumentar las partes dificulta producir sonidos agudos.</p>

<p>2. Método por interrupción de período variable</p>
<p>La idea es sintetizar las formas de onda de salida de cada parte.</p>

<pre>
__|__________|__________|__________|____  Salida parte A
__|______|______|______|______|______|__  Salida parte B
__|_______|_______|_______|_______|_____ Salida parte C
__|_________|_________|_________|_______ Salida parte D
__|______||_||__|_|___|||_|___|_|_||_|__ Salida sintetizada
</pre>

<p>• Preparar datos de tiempo hasta el próximo sonido y período para cada parte.</p>

<table border="2">
    <tr><th>Parte</th><th>Período</th><th>Tiempo</th></tr>
    <tr><td align="center">A</td><td align="right">10</td><td align="right">10</td></tr>
    <tr><td align="center">B</td><td align="right">6</td><td align="right">6</td></tr>
    <tr><td align="center">C</td><td align="right">7</td><td align="right">7</td></tr>
    <tr><td align="center">D</td><td align="right">9</td><td align="right">9</td></tr>
</table>

<p>• Restar el tiempo mínimo (parte B: 6) de todas las partes y establecer este tiempo como período de interrupción.</p>

<table border="2">
    <tr><th>Parte</th><th>Período</th><th>Tiempo</th></tr>
    <tr><td align="center">A</td><td align="right">10</td><td align="right">4</td></tr>
    <tr><td align="center">B</td><td align="right">6</td><td align="right">0</td></tr>
    <tr><td align="center">C</td><td align="right">7</td><td align="right">1</td></tr>
    <tr><td align="center">D</td><td align="right">9</td><td align="right">3</td></tr>
</table>

<p align="center">Período de interrupción: 6</p>

<p>• Procesar producción de sonido para partes con tiempo 0 y añadir período al tiempo.</p>

<table border="2">
    <tr><th>Parte</th><th>Período</th><th>Tiempo</th></tr>
    <tr><td align="center">A</td><td align="right">10</td><td align="right">4</td></tr>
    <tr><td align="center">B</td><td align="right">6</td><td align="right">6</td></tr>
    <tr><td align="center">C</td><td align="right">7</td><td align="right">1</td></tr>
    <tr><td align="center">D</td><td align="right">9</td><td align="right">3</td></tr>
</table>

<p>• Fin del procesamiento de interrupción.<br>
Cuando ocurra la siguiente interrupción, repetir desde "•".</p>

<p>• Para cambiar el tono, modificar el período durante el procesamiento de producción de sonido.</p>

<p>El problema de este método: en algunos casos, el período de interrupción puede volverse demasiado corto.</p>

<hr>

## 8. Ejemplo

<p>Como dice el refrán, "más vale una imagen que mil palabras", así que mostraré un ejemplo de cómo sería en un programa real.<br>
"PLAY.ASM" incluido en el archivo es el programa de ejemplo.<br>
(Para terminar el programa: [BRK])</p>

<pre>
SOFLG : ¿Producir sonido? (0-SOUND ON)
FADE : El volumen general disminuye según este valor (0～7)

BGMON  Inicia reproducción BGM (y simultáneamente reproduce canción 0 y SE 0)
BGMOFF Termina reproducción BGM
BGMCHG Especificación de canción BGM (AH: si no es 0, fija esa canción, AL: especificación de canción)
SECHG  Especificación de SE (AH: si no es 0, fija ese SE, AL: especificación de SE)
</pre>

<p>El formato de datos es:</p>
<pre>
MELODY:
DW Puntero a datos parte A canción 0
DW Puntero a datos parte B canción 0
DW Puntero a datos parte A canción 1
DW Puntero a datos parte B canción 1
:
</pre>

<pre>
EFFECT:
DW Puntero a datos SE0
DW Puntero a datos SE1
:
</pre>

<p>Estructura de datos BGM</p>
<pre>
RETURN ADDRESS,TEMPO,NOTE,LENGTH,NOTE,LENGTH,…,0FFH,0
</pre>

<p>Estructura de datos SE</p>
<pre>
TEMPO,NOTE,LENGTH,NOTE,LENGTH,…,0FFH,0
</pre>

<table border="2">
    <tr><th>NOTE</th><th>LENGTH</th><th>Significado</th></tr>
    <tr><td align="center">0～59</td><td align="center">0～255</td><td>Nota (corresponde a Do octava 2～Si octava 6)</td></tr>
    <tr><td align="center">60～63</td><td align="center">0～255</td><td>Silencio (LENGTH 0 y 1: silencio entero, 2: silencio de media…)</td></tr>
    <tr><td align="center">80H</td><td align="center">----</td><td>Ruido OFF</td></tr>
    <tr><td align="center">81H～8FH</td><td align="center">----</td><td>Ruido ON</td></tr>
    <tr><td align="center">C0H～C7H</td><td align="center">----</td><td>Volumen (pequeño 0～7 grande)</td></tr>
    <tr><td align="center">FFH</td><td align="center">0</td><td>Fin de datos (mover puntero a RETURN ADDRESS)</td></tr>
</table>

<p>Esto es solo un ejemplo, así que no se limiten por ello y experimenten con sus propias ideas.</p>

## 9. Finalmente

<p>Bueno, he explicado rápidamente, ¿entendieron sobre la reproducción de BGM?<br>
Puede que la explicación sobre interrupciones de temporizador sea demasiado breve, pero si explicara eso en detalle, no terminaría nunca, así que consulten libros especializados.</p>

<p>Pueden usar libremente los métodos y programas presentados aquí, pero si ocurre algún problema, no me hago responsable.</p>



