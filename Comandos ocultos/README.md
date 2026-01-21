## Comandos Ocultos de BASIC


<p>Existen varios comandos en la Z-1/FX-890P que no están documentados en el manual.<br>
Aquí presentamos esos comandos ocultos.</p>

<p>※Nota: Este documento está basado en material para la Z-1/FX-890P, por lo que no necesariamente todas las funciones aquí descritas serán válidas para la Z-1GR.</p>

<ul>
    <li><font size="5">SYSTEM *</font><br>
        Este comando está destinado originalmente para verificar configuraciones del sistema, capacidad, etc., pero al ingresar "<strong>SYSTEM
        *</strong>" se puede entrar al modo de depuración (debug).<br>
        En el modo de depuración es posible realizar verificaciones de la LCD, memoria, etc., pero algunos elementos pueden borrar el contenido de la memoria, por lo que<u>se debe tener precaución al usarlo</u>.<br>
        <br>
        </li>
    <li><font size="5">MODE n<br>
        </font>El manual solo explica el rango de 4 a 8, pero en realidad tiene varias otras funciones útiles.<br>
        Especialmente las funciones relacionadas con gráficos (50～56) tienen un gran valor de uso.<br>
        <ul>
            <li><strong>MODE
                10</strong><br>
                Realiza redondeo después de operaciones aritméticas básicas.<br>
                </li>
            <li><strong>MODE
                11</strong><br>
                No realiza redondeo después de operaciones aritméticas básicas.<br>
                </li>
            <li><strong>MODE
                30,n</strong><br>
                n=0～15
                Cuanto mayor sea el valor, más lento será<br>
                Especifica el número de espera (wait) durante la entrada/salida del puerto de expansión de 30 pines.<br>
                Se usa cuando el hardware conectado al puerto de expansión es lento y no puede seguir la velocidad de procesamiento de la Pocket Computer.<br>
                </li>
            <li><strong>MODE
                40,n</strong><br>
                n=0～255
                [segundos]<br>
                Configura el tiempo de espera (timeout) para salida a impresora.<br>
                </li>
            <li><strong>MODE
                41,n</strong><br>
                n=0～255
                [segundos]<br>
                Configura el tiempo de espera (timeout) para recepción RS-232C.<br>
                </li>
            <li><strong>MODE
                42,n</strong><br>
                n=0～255
                [segundos]<br>
                Configura el tiempo de espera (timeout) para transmisión RS-232C.<br>
                </li>
            <li><strong>MODE
                50,(SX,SY)-(EX,EY)</strong><br>
                SX,SY=coordenadas de inicio<br>
                EX,EY=coordenadas finales<br>
                Dibuja un rectángulo usando los dos puntos especificados como diagonales opuestas (esencialmente el comando BOX).<br>
                </li>
            <li><strong>MODE
                51,(SX,SY)-(EX,EY)</strong><br>
                SX,SY=coordenadas de inicio<br>
                EX,EY=coordenadas finales<br>
                Borra el área correspondiente al rectángulo definido por los dos puntos especificados como diagonales opuestas.<br>
                </li>
            <li><strong>MODE
                52,(X,Y),r</strong><br>
                X,Y=coordenadas del centro<br>
                r=radio<br>
                Dibuja un círculo en la posición especificada (esencialmente el comando CIRCLE).<br>
                </li>
            <li><strong>MODE
                53,X,Y<br>
                </strong>X,Y=coordenadas especificadas<br>
                Rellena un área cerrada que contenga las coordenadas especificadas (esencialmente el comando PAINT).<br>
                </li>
            <li><strong>MODE
                54,(SX,SY)-(EX,EY)<br>
                </strong>SX,SY=coordenadas de inicio<br>
                EX,EY=coordenadas finales<br>
                Captura la imagen de puntos (bitmap) del área definida por los dos puntos especificados como diagonales opuestas y la almacena en memoria (¿comando GET?).<br>
                Para ejecutar esto se requiere al menos 1540 bytes de memoria libre.<br>
                Además, no es posible mantener múltiples imágenes simultáneamente, por lo que puede requerir cierta técnica al usarlo.<br>
                </li>
            <li><strong>MODE
                55,(SX,SY)-(EX,EY)<br>
                </strong>Su funcionamiento es igual a MODE 54, pero después de capturar la imagen de puntos, borra esa área.<br>
                </li>
            <li><strong>MODE
                56,(X,Y)<br>
                </strong>X,Y=coordenadas especificadas<br>
                Despliega la imagen de puntos capturada con MODE 54 o MODE 55 en las coordenadas especificadas (¿comando PUT?).<br>
                Las coordenadas se pueden especificar en unidades de píxeles, pero si la imagen de puntos se despliega fuera de la pantalla, se producirá un error.<br>
                </li>
            <li><strong>MODE
                60,var1,var2<br>
                </strong>var1,var2=variables del mismo tipo<br>
                Intercambia el contenido de dos variables.<br>
                </li>
            <li><strong>MODE
                61,[P][F][PF]<br>
                </strong>P=área de programa<br>
                F=área de archivo<br>
                PF=ambas<br>
                Borra todo el contenido del área de programa y/o área de archivo.<br>
                </li>
            <li><strong>MODE
                62,xx,xx …<br>
                </strong>xx=código ASCII en notación hexadecimal (no se añade &amp;H al inicio)<br>
                Almacena los códigos especificados en el buffer del teclado.<br>
                Se pueden almacenar hasta 16 caracteres.<br>
                </li>
            <li><strong>MODE
                212<br>
                </strong>Configura la velocidad de comunicación RS-232C a 19200bps.<br>
                Sin embargo, la función de comunicación de F.COM no puede seguir esta velocidad. Para seguir los 19200bps es necesario escribir un controlador de comunicación propio.<br>
                </li>
            <li><strong>MODE
                220,SX,SY,EX,EY</strong><br>
                SX,SY=coordenadas de inicio<br>
                EX,EY=coordenadas finales<br>
                Configura el área de visualización de caracteres.<br>
                A partir de entonces, no será posible mostrar texto fuera del rango establecido (similar al comando WIDTH).<br>
                </li>
            <li><strong>MODE
                230,n</strong><br>
                n=0～9 corresponde a P0～P9<br>
                Muestra la dirección de almacenamiento del área de programa especificada.<br>
                </li>
            <li><strong>MODE
                231,n</strong><br>
                n=0～9 corresponde a F0～F9<br>
                Muestra la dirección de almacenamiento del área de archivo especificada.<br>
                </li>
            <li><strong>MODE
                232,n</strong><br>
                n=0～9 corresponde a P0～P9<br>
                Muestra el checksum del área de programa especificada.<br>
                </li>
            <li><strong>MODE
                233,n</strong><br>
                n=0～9 corresponde a F0～F9<br>
                Muestra el checksum del área de archivo especificada.<br>
                <br>
                </li>
        </ul>
    </li>
    <li><font size="5">POKE xxxx,n</font><br>
        Este no es un comando oculto, pero al usarlo para modificar áreas del sistema se pueden ejecutar funciones especiales.<br>
        <ul>
            <li><strong>POKE
                &amp;H16BB,PEEK(&amp;H16BB) OR &amp;H80<br>
                </strong>Cambia el conjunto de caracteres a la especificación internacional.<br>
                </li>
            <li><strong>POKE
                &amp;H16BB,PEEK(&amp;H16BB) AND &amp;H7F<br>
                </strong>Cambia el conjunto de caracteres a la especificación japonesa.<br>
                </li>
            <li><strong>POKE
                &amp;H16E2,255<br>
                </strong>Desbloqueo forzado de contraseña.<br>
                </li>
            <li><strong>POKE
                &amp;H16F7,n<br>
                </strong>Configura el tiempo hasta el apagado automático en minutos.<br>
                Nota: Si se especifica 0, el apagado ocurre inmediatamente.<br>
                </li>
            <li><strong>POKE
                &amp;H1775,PEEK(&amp;H1775) OR &amp;H20<br>
                </strong>A partir de entonces, el texto mostrado en pantalla aparecerá en modo inverso (negativo).<br>
                </li>
            <li><strong>POKE
                &amp;H1775,PEEK(&amp;H1775) AND &amp;HDF<br>
                </strong>Desactiva la visualización inversa.<br>
                </li>
            <li><strong>POKE
                &amp;H178B,PEEK(&amp;H178B) OR &amp;H02<br>
                </strong>Activa el sonido de clic del teclado.<br>
                </li>
            <li><strong>POKE
                &amp;H178B,PEEK(&amp;H178B) AND &amp;HFD<br>
                </strong>Desactiva el sonido de clic del teclado.<br>
                </li>
        </ul>
    </li>
</ul>
