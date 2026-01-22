# Convertidor de Nivel Serial


<p>　Un convertidor de nivel serial es un dispositivo de conversión que ajusta los niveles de voltaje de la señal del estándar RS-232C para permitir la transferencia de datos entre una computadora de bolsillo y dispositivos como módems o PCs.<br>
Las señales en el estándar RS-232C utilizan valores de voltaje de +15V a -15V. Sin embargo, las computadoras de bolsillo solo pueden manejar voltajes de 0V a 5V. Si se conectaran directamente, la computadora de bolsillo se dañaría. Por lo tanto, la función del convertidor de nivel serial es situarse entre ellos y ajustar los voltajes de señal respectivos.<br>
　Para la serie FX-890P/Z-1, existe una interfaz oficial llamada FA-8. Sin embargo, para ser honesto, no es muy conveniente de usar porque es bastante grande y requiere una fuente de alimentación (incluso pilas secas están bien).<br>
Así que, aquí presentamos una forma de hacer un convertidor de nivel serial que no requiere energía externa y es compacto.</p>

<p>　El convertidor de nivel serial presentado aquí se basa en un diagrama de circuito publicado en "Z-1/FX-890P Utilization Research," modificado para permitir el control de flujo por software.<br>
Tenga en cuenta que no nos hacemos responsables por ningún daño que pueda ocurrir en su computadora de bolsillo, PC u otro equipo como resultado del uso de esta interfaz. Úselo bajo su propia responsabilidad.</p>

## 1. Principio de Funcionamiento

<p>　Los niveles de voltaje de señal para RS-232C y la computadora de bolsillo son los siguientes:</p>
<div align="center"><center><table border="1" cellspacing="1">
    <tr>
        <td align="center">　</td>
        <td align="center">RS-232C</td>
        <td align="center">FX-890P/Z-1</td>
    </tr>
    <tr>
        <td align="center">Bit "1"</td>
        <td align="center">-15V～-5V</td>
        <td align="center">5V</td>
    </tr>
    <tr>
        <td align="center">Bit "0"</td>
        <td align="center">5V～15V</td>
        <td align="center">0V</td>
    </tr>
</table>
</center></div>

<p>　Como se muestra, no son compatibles, por lo que no se pueden conectar directamente.<br>
Especialmente dado que RS-232C utiliza voltajes negativos, cómo manejar esto es el principal desafío.<br>
(La razón por la que RS-232C usa voltaje negativo es que originalmente era un estándar de comunicación de larga distancia. En distancias largas, ocurre capacitancia parásita, lo que hace que la electricidad se acumule en la línea de comunicación. Aplicar un voltaje en la dirección opuesta previene esta acumulación).</p>

<p>　El puerto serial FX-890P/Z-1 puede usar un conector de 30 pines o un conector de 3 pines. El de 30 pines puede cablear todas las líneas de señal incluyendo las líneas de control, pero el de 3 pines solo tiene líneas de datos (RXD, TXD) y GND.<br>
Las líneas de control son esenciales para el control de flujo por hardware y para obtener el estado del módem, pero aún se puede realizar la transferencia de datos sin ellas.<br>
Por lo tanto, para simplificar el circuito, no cablearemos las líneas de control y solo cablearemos las líneas de datos. Al mismo tiempo, reutilizaremos el voltaje en las líneas de control como fuente de alimentación para las líneas de datos, eliminando la necesidad de una fuente de alimentación separada.<br>
Además, dado que es poco común controlar un módem desde una computadora de bolsillo, consideraremos el dispositivo de conexión como una PC.</p>

<p>　Cuando el lado de la PC está en estado de comunicación, entre las líneas de control, las que son salidas son "DTR" y "RTS", ambas en estado de bit "0".<br>
Podemos usar esto como fuente de alimentación positiva. Sin embargo, no hay una línea de control que proporcione un voltaje negativo de manera estable.<br>
Como no hay otra opción, reutilizaremos "GND"... es decir, 0V como fuente de alimentación negativa.<br>
Puede parecer incorrecto desde la perspectiva del estándar RS-232C, pero en realidad, parece que incluso 0V es reconocido como bit "1". (Sin embargo, dado que se desvía del estándar original, no podemos quejarnos si algún dispositivo no funciona).</p>

## 2. Diagrama del Circuito

<p>　</p>
<div align="center"><center>

<table border="0" cellpadding="0" cellspacing="0">
    <tr>
        <td align="center"><p align="center"><font size="3"><img src="/web/20170825072324im_/http://stear.c.ooco.jp/fx890p/SIRif.gif" width="400" height="400"></font></p>
        <p align="center"><font size="3"><u>Diagrama del Circuito</u></font></p>
        </td>
        <td width="16">　</td>
        <td align="center"><font size="4"><img src="/web/20170825072324im_/http://stear.c.ooco.jp/fx890p/NAME.gif" width="136" height="378"></font><p><font size="3"><u>Nombres</u></font></p>
        </td>
    </tr>
</table>
</center></div>

<p>　</p>

## 3. Materiales
<div align="center"><center>

<table border="1" cellspacing="1">
    <tr>
        <td align="center">Diodo</td>
        <td align="center">IS1588</td>
        <td align="center">3 unidades</td>
    </tr>
    <tr>
        <td align="center">Diodo Zener</td>
        <td align="center">02Z5.1A</td>
        <td align="center">2 unidades</td>
    </tr>
    <tr>
        <td align="center">Transistor (NPN)</td>
        <td align="center">2SC1000</td>
        <td align="center">2 unidades</td>
    </tr>
    <tr>
        <td align="center">Transistor (PNP)</td>
        <td align="center">2SA841</td>
        <td align="center">2 unidades</td>
    </tr>
    <tr>
        <td align="center">Resistencia</td>
        <td align="center">3.3KΩ<br>
        4.7KΩ<br>
        10KΩ<br>
        36KΩ<br>
        39KΩ</td>
        <td align="center">1 de cada</td>
    </tr>
    <tr>
        <td align="center">Conector estéreo de 2.5φ</td>
        <td align="center">　</td>
        <td align="center">1 unidad</td>
    </tr>
    <tr>
        <td align="center">Conector D-sub 9 pines (hembra)</td>
        <td align="center">　</td>
        <td align="center">1 unidad</td>
    </tr>
    <tr>
        <td align="center">Cable blindado de 2 hilos</td>
        <td align="center">　</td>
        <td align="center">cantidad apropiada</td>
    </tr>
    <tr>
        <td align="center">Placa universal</td>
        <td align="center">　</td>
        <td align="center">1 unidad</td>
    </tr>
</table>
</center></div>

<p>　</p>

## 4. Explicación

<ul>
    <li>Los transistores utilizados pueden ser cualquiera para señal pequeña.</li>
    <li>Para los diodos Zener, use aquellos con aproximadamente 5V en estado de funcionamiento. (Tenga cuidado ya que puede haber variaciones).</li>
    <li>Los números en el lado del conector de 9 pines corresponden a los números de pin para puertos seriales de PCs de la serie DOS/V.</li>
    <li>Conecte todas las tierras (GND).</li>
    <li>Use la placa universal solo si la desea.<br>
        Por cierto, yo monté todo este circuito dentro del conector de 9 pines.</li>
    <li>Dependiendo del conector estéreo que use, puede haber problemas de contacto con el conector de la computadora de bolsillo.<br>
        En ese caso, limar un poco el plástico alrededor del puerto del conector de la computadora de bolsillo puede mejorar la situación.<br>La referencia es hasta que el plástico negro del conector quede expuesto.</li>
</ul>

