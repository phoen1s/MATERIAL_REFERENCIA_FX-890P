# Lista de BIOS (versión resumida)

<p>Este documento presenta las funciones de la BIOS incorporada en la Z-1/FX-890P. Esta documentación es necesaria al programar en ensamblador.<br>
Además, en este documento se asume como condición previa un conocimiento básico del ensamblador 8086 para su explicación. Por lo tanto, sería prudente que quienes se pregunten "¿qué es el ensamblador?" no continúen leyendo más allá.<br>
<br>
Originalmente, la BIOS incorporada en la Z-1/FX-890P contiene más de 180 funciones, pero aquí se presentan aquellas seleccionadas por su alto valor de uso.<br>
<br>
※Nota: Este documento está basado en material para la Z-1/FX-890P, por lo que no necesariamente todas las funciones aquí descritas serán válidas para la Z-1GR.<br>
...aunque probablemente las funciones que se presentarán a continuación no hayan sido modificadas.<br>
</p>
<p><font size="5"><u>１. Precauciones sobre el uso de la BIOS</u></font></p>

<p>Básicamente, la BIOS se utiliza llamándola desde ensamblador mediante interrupción de software (INT 41H), pero hay varias precauciones a tener en cuenta en ese momento.</p>

<ul>
  <li>Los registros SS y DS deben ser 0.
  <li>El contenido de los registros no especificados explícitamente en la salida se preserva.
  <li>Si ocurre un error durante la comunicación RS-232C, se mostrará un error de BASIC y se interrumpirá el procesamiento (no se volverá al programa).
  <li>Si se presiona la tecla [BRK] durante la ejecución de funciones BIOS relacionadas con entrada de teclas, el procesamiento del programa se interrumpirá, a menos que se especifique lo contrario.<br>
</ul>
<p><font size="5"><u>２. Método de uso de la BIOS</u></font></p>

<p>Para ejecutar la BIOS, primero se establece el número de BIOS en el registro AH, luego se configuran los datos necesarios en los otros registros, y finalmente se ejecuta la interrupción de software 41H (INT 41H) para realizar el procesamiento específico.</p>
<p>Ejemplo:</p>

<table border="0" cellspacing="1">
  <tbody>
    <tr>
      <td width="60">MOV</td>
      <td width="80">AH,01H</td>
      <td># Establecer número de BIOS</td>
    </tr>
    <tr>
      <td>MOV</td>
      <td>DL,65</td>
      <td># Establecer datos</td>
    </tr>
    <tr>
      <td>INT</td>
      <td>41H</td>
      <td># Llamar a la BIOS</td>
    </tr>
    <tr>
      <td>IRET</td>
      <td>　</td>
      <td>　</td>
    </tr>
  </tbody>
</table>


<p><font size="5"><u>３. Tabla de funciones de la BIOS</u></font></p>

<p>A continuación se muestra una lista de números de BIOS y sus funciones en formato de tabla.<br>
Los números que faltan corresponden a funciones no identificadas o que se consideraron de bajo valor de uso personal y no se incluyeron.</p>
<p><a href="#00">00H=Transición forzada al modo CAL</a><br>
<a href="#01">01H=Salida de 1 byte a LCD</a><br>
<a href="#02">02H=Salida de 1 byte a impresora</a><br>
<a href="#03">03H=Entrada de 1 byte desde teclado (con parpadeo de cursor)</a><br>
<a href="#04">04H=Entrada de 1 byte desde teclado (sin parpadeo de cursor)</a><br>
<a href="#05">05H=Especificación de destino al presionar la tecla [BRK]</a><br>
<a href="#06">06H=Abrir RS-232C</a><br>
<a href="#07">07H=Cerrar RS-232C</a><br>
<a href="#08">08H=Salida de 1 byte a RS-232C</a><br>
<a href="#09">09H=Entrada de 1 byte desde buffer de recepción RS-232C</a><br>
<a href="#0a">0AH=Verificación del buffer de comunicación RS-232C</a><br>
<a href="#0b">0BH=Establecer modo de visualización inversa en LCD</a><br>
<a href="#0c">0CH=Establecer modo de visualización normal en LCD</a><br>
<a href="#0f">0FH=Cambiar posición del cursor</a><br>
<a href="#10">10H=Limpiar pantalla</a><br>
<a href="#11">11H=Salida del contenido del buffer de puntos a pantalla LCD</a><br>
<a href="#12">12H=Encender/apagar punto en coordenadas especificadas del buffer de puntos<br>
</a><a href="#13">13H=Dibujar línea en el buffer de puntos<br>
</a><a href="#14">14H=Dibujar rectángulo en el buffer de puntos<br>
</a><a href="#15">15H=Dibujar círculo en el buffer de puntos<br>
</a><a href="#17">17H=Retornar dirección superior del buffer de puntos<br>
</a><a href="#1a">1AH=Verificar si el buffer de teclas está vacío<br>
</a><a href="#1b">1BH=Limpiar contenido del buffer de teclas<br>
</a><a href="#1d">1DH=Extraer código de un carácter del buffer de teclas<br>
</a><a href="#1e">1EH=Examinar código del siguiente carácter en el buffer de teclas<br>
</a><a href="#1f">1FH=Realizar muestreo de la tecla [BRK] y del interruptor de encendido<br>
</a><a href="#20">20H=Mostrar cadena especificada en pantalla LCD<br>
</a><a href="#22">22H=Transmitir cadena especificada a RS-232C<br>
</a><a href="#23">23H=Ingresar cadena usando editor de pantalla<br>
</a><a href="#28">28H=Emitir sonido BEEP<br>
</a><a href="#29">29H=Emitir zumbador a frecuencia especificada<br>
</a><a href="#2a">2AH=Establecer líneas de señal RS-232C/Centrónicas en HIGH<br>
</a><a href="#2b">2BH=Establecer líneas de señal RS-232C/Centrónicas en LOW<br>
</a><a href="#2c">2CH=Verificar estado de líneas de señal RS-232C/Centrónicas<br>
</a><a href="#2f">2FH=Esperar tiempo especificado<br>
</a><a href="#3c">3CH=Retornar posición actual del cursor<br>
</a><a href="#41">41H=Retornar dirección superior del área de almacenamiento de fuentes de caracteres definidos por usuario<br>
</a><a href="#43">43H=Mostrar 1 dígito hexadecimal en LCD de los 4 bits inferiores de DL<br>
</a><a href="#44">44H=Mostrar 2 dígitos hexadecimales en LCD del contenido de DL<br>
</a><a href="#45">45H=Mostrar 4 dígitos hexadecimales en LCD del contenido de DX<br>
</a><a href="#46">46H=Mostrar 5 dígitos BCD en LCD del contenido de DX<br>
</a><a href="#47">47H=Repetir tecla actualmente presionada<br>
</a><a href="#67">67H=Inicialización de todo el sistema<br>
</a><a href="#68">68H=Comando PAINT<br>
</a><a href="#6f">6FH=Cambiar tiempo de espera de INPORT, OUTPORT<br>
</a><a href="#70">70H=Salida de datos al puerto de expansión de 30 pines<br>
</a><a href="#71">71H=Entrada de datos desde puerto de expansión de 30 pines<br>
</a><a href="#7e">7EH=Realizar reset P<br>
</a><a href="#7f">7FH=Apagar alimentación<br>
</a></p>

<p><font size="5"><u>４. Detalles de funciones de la BIOS</u></font></p>

<p>Se muestra el detalle de las condiciones de entrada/salida y el contenido del procesamiento de cada BIOS.</p>

<p>----------</p>

<table border="0" cellspacing="1">
  <tbody>
    <tr>
      <td valign="top" width="33" align="right"><a name="00"></a>Número</td>
      <td valign="top">:</td>
      <td>00H=Transición forzada al modo CAL</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Entrada</td>
      <td valign="top">:</td>
      <td>AH=00H</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Salida</td>
      <td valign="top">:</td>
      <td>Ninguna</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Función</td>
      <td valign="top">:</td>
      <td>Finaliza forzosamente el programa de usuario y realiza una transición forzada al modo CAL.</td>
    </tr>
  </tbody>
</table>

<p>----------</p>

<table border="0" cellspacing="1">
  <tbody>
    <tr>
      <td valign="top" width="33" align="right"><a name="01"></a>Número</td>
      <td valign="top">:</td>
      <td>01H=Salida de 1 byte a LCD</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Entrada</td>
      <td valign="top">:</td>
      <td>AH=01H<br>
      DL=Código de carácter</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Salida</td>
      <td valign="top">:</td>
      <td>Ninguna</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Función</td>
      <td valign="top">:</td>
      <td>Muestra el carácter especificado en DL en la posición del cursor LCD.<br>
      Cuando el carácter es un código de control, ejecuta esa función.</td>
    </tr>
  </tbody>
</table>


<p>----------</p>

<table border="0" cellspacing="1">
  <tbody>
    <tr>
      <td valign="top" width="33" align="right"><a name="02"></a>Número</td>
      <td valign="top">:</td>
      <td>02H=Salida de 1 byte a impresora</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Entrada</td>
      <td valign="top">:</td>
      <td>AH=02H<br>
      DL=Código de carácter</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Salida</td>
      <td valign="top">:</td>
      <td>Ninguna</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Función</td>
      <td valign="top">:</td>
      <td>Envía el carácter especificado en DL a la impresora.</td>
    </tr>
  </tbody>
</table>


<p>----------</p>

<table border="0" cellspacing="1">
  <tbody>
    <tr>
      <td valign="top" width="33" align="right"><a name="03"></a>Número</td>
      <td valign="top">:</td>
      <td>03H=Entrada de 1 byte desde teclado (con parpadeo de cursor)</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Entrada</td>
      <td valign="top">:</td>
      <td>AH=03H</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Salida</td>
      <td valign="top">:</td>
      <td>DL=Código de carácter del carácter ingresado</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Función</td>
      <td valign="top">:</td>
      <td>Espera hasta que se ingrese un carácter y retorna su código.<br>
      Permite entrada kana y entrada con shift.<br>
      Funciona la función de apagado automático.</td>
    </tr>
  </tbody>
</table>


<p>----------</p>

<table border="0" cellspacing="1">
  <tbody>
    <tr>
      <td valign="top" width="33" align="right"><a name="04"></a>Número</td>
      <td valign="top">:</td>
      <td>04H=Entrada de 1 byte desde teclado (sin parpadeo de cursor)</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Entrada</td>
      <td valign="top">:</td>
      <td>AH=04H</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Salida</td>
      <td valign="top">:</td>
      <td>DL=Código de carácter del carácter ingresado</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Función</td>
      <td valign="top">:</td>
      <td>Espera hasta que se ingrese un carácter y retorna su código.<br>
      Permite entrada kana y entrada con shift.<br>
      Funciona la función de apagado automático.</td>
    </tr>
  </tbody>
</table>


<p>----------</p>

<table border="0" cellspacing="1">
  <tbody>
    <tr>
      <td valign="top" width="33" align="right"><a name="05"></a>Número</td>
      <td valign="top">:</td>
      <td>05H=Especificación de destino al presionar la tecla [BRK]</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Entrada</td>
      <td valign="top">:</td>
      <td>AH=05H<br>
      CX=Dirección de segmento del destino<br>
      BX=Dirección de desplazamiento del destino</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Salida</td>
      <td valign="top">:</td>
      <td>Ninguna</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Función</td>
      <td valign="top">:</td>
      <td>Especifica el destino de procesamiento cuando se presiona la tecla [BRK] durante entrada de teclas.<br>
      La transición al destino se realiza mediante la instrucción "JMP".</td>
    </tr>
  </tbody>
</table>


<p>----------</p>

<table border="0" cellspacing="1">
  <tbody>
    <tr>
      <td valign="top" width="33" align="right"><a name="06"></a>Número</td>
      <td valign="top">:</td>
      <td>06H=Abrir RS-232C</td>
    </tr>
    <tr>
      <td valign="top"  width="33" align="right">Entrada</td>
      <td valign="top">:</td>
      <td>AH=06H<br>
      BL=b7 b6 b5 b4 b3 b2 b1 b0<br>
..........│..│..│..│..└--┴--┴--┴- Velocidad (baudrate)<br>
          │  │  │  │　　　　　　　　 0 0 0 0 : 75bps<br>
          │  │  │  │　　　　　　　　 0 0 0 1 : 150bps<br>
          │  │  │  │　　　　　　　　 0 0 1 0 : 300bps<br>
          │  │  │  │　　　　　　　　 0 0 1 1 : 600bps<br>
          │  │  │  │　　　　　　　　 0 1 0 0 : 1200bps<br>
          │  │  │  │　　　　　　　　 0 1 0 1 : 2400bps<br>
          │  │  │  │　　　　　　　　 0 1 1 0 : 4800bps<br>
          │  │  │  │　　　　　　　　 0 1 1 1 : 9600bps<br>
          │  │  │  │　　　　　　　　 1 0 0 0 : 19200bps<br>
          │  │  │  └─────── Paridad　 (0:EVEN / 1:ODD )<br>
          │  └────────- Paridad　 (0:ON / 1:OFF )<br>
          │  └────────── Bits de stop (0:1 bit / 1:2 bits)<br>
          └───────────- Longitud de datos (0:8 bits / 1:7 bits)<br>
      BH=b7 b6 b5 b4 b3 b2 b1 b0<br>
      　　　　　　│ │ │ │ └─ SI/SO　　(0:no controlar / 1:controlar)<br>
      　　　　　　│ │ │ └──- Xon/Xoff (0:no controlar / 1:controlar)<br>
      　　　　　　│ │ └──── CTS　　　(0:no verificar / 1:verificar)<br>
      　　　　　　│ └─────- CD　　　 (0:no verificar / 1:verificar)<br>
      　　　　　　└─────── DSR　　　(0:no verificar / 1:verificar)</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Salida</td>
      <td valign="top">:</td>
      <td>Ninguna</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Función</td>
      <td valign="top">:</td>
      <td>Realiza la apertura hardware de RS-232C y permite la interrupción de comunicación serial.</td>
    </tr>
  </tbody>
</table>


<p>----------</p>

<table border="0" cellspacing="1">
  <tbody>
    <tr>
      <td valign="top" width="33" align="right"><a name="07"></a>Número</td>
      <td valign="top">:</td>
      <td>07H=Cerrar RS-232C</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Entrada</td>
      <td valign="top">:</td>
      <td>AH=07H</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Salida</td>
      <td valign="top">:</td>
      <td>Ninguna</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Función</td>
      <td valign="top">:</td>
      <td>Realiza el cierre hardware de RS-232C y prohíbe la interrupción de comunicación serial.</td>
    </tr>
  </tbody>
</table>


<p>----------</p>

<table border="0" cellspacing="1">
  <tbody>
    <tr>
      <td valign="top" width="33" align="right"><a name="08"></a>Número</td>
      <td valign="top">:</td>
      <td>08H=Salida de 1 byte a RS-232C</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Entrada</td>
      <td valign="top">:</td>
      <td>AH=08H<br>
      DL=Código</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Salida</td>
      <td valign="top">:</td>
      <td>Ninguna</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Función</td>
      <td valign="top">:</td>
      <td>Envía el código en DL a RS-232C.<br>
      Ejecutar después de abrir RS-232C.<br>
      Internamente monitorea la tecla [BRK] y el interruptor de encendido.</td>
    </tr>
  </tbody>
</table>


<p>----------</p>

<table border="0" cellspacing="1">
  <tbody>
    <tr>
      <td valign="top" width="33" align="right"><a name="09"></a>Número</td>
      <td valign="top">:</td>
      <td>09H=Entrada de 1 byte desde buffer de recepción RS-232C</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Entrada</td>
      <td valign="top">:</td>
      <td>AH=09H</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Salida</td>
      <td valign="top">:</td>
      <td>DL=Datos recibidos</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Función</td>
      <td valign="top">:</td>
      <td>Lee 1 byte de datos del buffer de recepción RS-232C.<br>
      Si ocurre un error durante la comunicación, el procesamiento no retorna al programa de usuario.</td>
    </tr>
  </tbody>
</table>


<p>----------</p>

<table border="0" cellspacing="1">
  <tbody>
    <tr>
      <td valign="top" width="33" align="right"><a name="0a"></a>Número</td>
      <td valign="top">:</td>
      <td>0AH=Verificación del buffer de comunicación RS-232C</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Entrada</td>
      <td valign="top">:</td>
      <td>AH=0AH</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Salida</td>
      <td valign="top">:</td>
      <td>DH=0:sin datos 1:con datos</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Función</td>
      <td valign="top">:</td>
      <td>Verifica si hay datos recibidos en el buffer de comunicación RS-232C.</td>
    </tr>
  </tbody>
</table>


<p>----------</p>

<table border="0" cellspacing="1">
  <tbody>
    <tr>
      <td valign="top" width="33" align="right"><a name="0b"></a>Número</td>
      <td valign="top">:</td>
      <td>0BH=Establecer modo de visualización inversa en LCD</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Entrada</td>
      <td valign="top">:</td>
      <td>AH=0BH</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Salida</td>
      <td valign="top">:</td>
      <td>Ninguna</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Función</td>
      <td valign="top">:</td>
      <td>Al ejecutar esta BIOS, las siguientes visualizaciones de texto en LCD serán en modo inverso.</td>
    </tr>
  </tbody>
</table>


<p>----------</p>

<table border="0" cellspacing="1">
  <tbody>
    <tr>
      <td valign="top" width="33" align="right"><a name="0c"></a>Número</td>
      <td valign="top">:</td>
      <td>0CH=Establecer modo de visualización normal en LCD</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Entrada</td>
      <td valign="top">:</td>
      <td>AH=0CH</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Salida</td>
      <td valign="top">:</td>
      <td>Ninguna</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Función</td>
      <td valign="top">:</td>
      <td>Al ejecutar esta BIOS, las siguientes visualizaciones de texto en LCD serán en modo normal.</td>
    </tr>
  </tbody>
</table>


<p>----------</p>

<table border="0" cellspacing="1">
  <tbody>
    <tr>
      <td valign="top" width="33" align="right"><a name="0f"></a>Número</td>
      <td valign="top">:</td>
      <td>0FH=Cambiar posición del cursor</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Entrada</td>
      <td valign="top">:</td>
      <td>AH=0FH<br>
      BL=Coordenada X del cursor<br>
      BH=Coordenada Y del cursor</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Salida</td>
      <td valign="top">:</td>
      <td>Ninguna</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Función</td>
      <td valign="top">:</td>
      <td>Mueve el cursor a la posición especificada.<br>
      Si la posición especificada está fuera de la pantalla, las coordenadas no cambian.</td>
    </tr>
  </tbody>
</table>


<p>----------</p>

<table border="0" cellspacing="1">
  <tbody>
    <tr>
      <td valign="top" width="33" align="right"><a name="10"></a>Número</td>
      <td valign="top">:</td>
      <td>10H=Limpiar pantalla</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Entrada</td>
      <td valign="top">:</td>
      <td>AH=10H</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Salida</td>
      <td valign="top">:</td>
      <td>Ninguna</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Función</td>
      <td valign="top">:</td>
      <td>Borra la pantalla LCD.</td>
    </tr>
  </tbody>
</table>


<p>----------</p>

<table border="0" cellspacing="1">
  <tbody>
    <tr>
      <td valign="top" width="33" align="right"><a name="11"></a>Número</td>
      <td valign="top">:</td>
      <td>11H=Salida del contenido del buffer de puntos a pantalla LCD</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Entrada</td>
      <td valign="top">:</td>
      <td>AH=11H<br>
      BH=Posición inicial de visualización de pantalla virtual</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Salida</td>
      <td valign="top">:</td>
      <td>Ninguna</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Función</td>
      <td valign="top">:</td>
      <td>Muestra el contenido del buffer de puntos en LCD.<br>
      Dado que hay 8 líneas de pantalla virtual, se especifica en BH qué parte mostrar.</td>
      <table border="0" cellspacing="1">
        <tbody>
          <tr>
            <td valign="top">BH=</td>
            <td valign="top">0: Muestra líneas 1～4.</td>
          </tr>
          <tr>
            <td valign="top">　</td>
            <td valign="top">1: Muestra líneas 2～5.</td>
          </tr>
          <tr>
            <td valign="top">　</td>
            <td valign="top">2: Muestra líneas 3～6.</td>
          </tr>
          <tr>
            <td valign="top">　</td>
            <td valign="top">3: Muestra líneas 4～7.</td>
          </tr>
          <tr>
            <td valign="top">　</td>
            <td valign="top">4: Muestra líneas 5～8.</td>
          </tr>
        </tbody>
      </table>
      </td>
    </tr>
  </tbody>
</table>


<p>----------</p>

<table border="0" cellspacing="1">
  <tbody>
    <tr>
      <td valign="top" width="33" align="right"><a name="12"></a>Número</td>
      <td valign="top">:</td>
      <td>12H=Encender/apagar punto en coordenadas especificadas del buffer de puntos</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Entrada</td>
      <td valign="top">:</td>
      <td>AH=12H<br>
      BL=Coordenada X (00H～BFH)<br>
      BH=Coordenada Y (00H～3FH)<br>
      DL=0:Establecer punto. 1:Limpiar punto.</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Salida</td>
      <td valign="top">:</td>
      <td>Ninguna</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Función</td>
      <td valign="top">:</td>
      <td>Establece/limpia el punto del buffer de puntos en las coordenadas especificadas.<br>
      Si las coordenadas están fuera de pantalla, no hace nada.<br>
      Solo ejecutar esta BIOS no cambia la LCD.</td>
    </tr>
  </tbody>
</table>


<p>----------</p>

<table border="0" cellspacing="1">
  <tbody>
    <tr>
      <td valign="top" width="33" align="right"><a name="13"></a>Número</td>
      <td valign="top">:</td>
      <td>13H=Dibujar línea en el buffer de puntos</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Entrada</td>
      <td valign="top">:</td>
      <td>AH=13H<br>
      BL=Coordenada X inicial (00H～BFH)<br>
      BH=Coordenada Y inicial (00H～3FH)<br>
      CL=Coordenada X final (00H～BFH)<br>
      DH=Coordenada Y final (00H～3FH)<br>
      DL=b7 b6 b5 b4 b3 b2 b1 b0<br>
      　 │　　　　　　　　　 └─ 0:Dibujar línea 1:Borrar línea<br>
      　 └───────────- 0:Mostrar en LCD 1:No mostrar en LCD</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Salida</td>
      <td valign="top">:</td>
      <td>Ninguna</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Función</td>
      <td valign="top">:</td>
      <td>Dibuja una línea en el buffer de puntos conectando las coordenadas inicial y final.<br>
      Si alguna coordenada está fuera de pantalla, no hace nada.</td>
    </tr>
  </tbody>
</table>


<p>----------</p>

<table border="0" cellspacing="1">
  <tbody>
    <tr>
      <td valign="top" width="33" align="right"><a name="14"></a>Número</td>
      <td valign="top">:</td>
      <td>14H=Dibujar rectángulo en el buffer de puntos</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Entrada</td>
      <td valign="top">:</td>
      <td>AH=14H<br>
      BL=Coordenada X inicial (00H～BFH)<br>
      BH=Coordenada Y inicial (00H～3FH)<br>
      CL=Coordenada X final (00H～BFH)<br>
      DH=Coordenada Y final (00H～3FH)<br>
      DL=b7 b6 b5 b4 b3 b2 b1 b0<br>
      　 │　　　　　　　　　 └─ 0:Dibujar rectángulo 1:Borrar rectángulo<br>
      　 └───────────- 0:Mostrar en LCD 1:No mostrar en LCD</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Salida</td>
      <td valign="top">:</td>
      <td>Ninguna</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Función</td>
      <td valign="top">:</td>
      <td>Dibuja un rectángulo en el buffer de puntos usando las coordenadas inicial y final como vértices.<br>
      Si alguna coordenada está fuera de pantalla, no hace nada.</td>
    </tr>
  </tbody>
</table>


<p>----------</p>

<table border="0" cellspacing="1">
  <tbody>
    <tr>
      <td valign="top" width="33" align="right"><a name="15"></a>Número</td>
      <td valign="top">:</td>
      <td valign="top">15H=Dibujar círculo en el buffer de puntos</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Entrada</td>
      <td valign="top">:</td>
      <td valign="top">AH=15H<br>
      BL=Coordenada X del centro (00H～BFH)<br>
      BH=Coordenada Y del centro (00H～3FH)<br>
      CL=Radio (01H～BFH)<br>
      DL=b7 b6 b5 b4 b3 b2 b1 b0<br>
      　 │　　　　　　　　　 └─ 0:Dibujar círculo 1:Borrar círculo<br>
      　 └───────────- 0:Mostrar en LCD 1:No mostrar en LCD</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Salida</td>
      <td valign="top">:</td>
      <td valign="top">Ninguna</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Función</td>
      <td valign="top">:</td>
      <td valign="top">Dibuja un círculo en el buffer de puntos con las coordenadas especificadas como centro.<br>
      Si las coordenadas especificadas están fuera de pantalla, no se realiza el procesamiento, pero si la circunferencia está fuera de pantalla, sí se realiza.</td>
    </tr>
  </tbody>
</table>


<p>----------</p>

<table border="0" cellspacing="1">
  <tbody>
    <tr>
      <td valign="top" width="33" align="right"><a name="17"></a>Número</td>
      <td valign="top">:</td>
      <td>17H=Retornar dirección superior del buffer de puntos</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Entrada</td>
      <td valign="top">:</td>
      <td>AH=17H</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Salida</td>
      <td valign="top">:</td>
      <td>BX=Dirección de entrada del buffer de puntos (LEDTOP)</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Función</td>
      <td valign="top">:</td>
      <td>Retorna la dirección inicial del buffer de puntos.<br>
      La dirección de segmento está fijada en 0.</td>
    </tr>
  </tbody>
</table>


<p>----------</p>

<table border="0" cellspacing="1">
  <tbody>
    <tr>
      <td valign="top" width="33" align="right"><a name="1a"></a>Número</td>
      <td valign="top">:</td>
      <td>1AH=Verificar si el buffer de teclas está vacío</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Entrada</td>
      <td valign="top">:</td>
      <td>AH=1AH</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Salida</td>
      <td valign="top">:</td>
      <td>DH=0:Buffer vacío 1:Hay datos en el buffer</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Función</td>
      <td valign="top">:</td>
      <td>Verifica si hay datos en el buffer de teclas.<br>
      No afecta el contenido del buffer de teclas.</td>
    </tr>
  </tbody>
</table>


<p>----------</p>

<table border="0" cellspacing="1">
  <tbody>
    <tr>
      <td valign="top" width="33" align="right"><a name="1b"></a>Número</td>
      <td valign="top">:</td>
      <td>1BH=Limpiar contenido del buffer de teclas</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Entrada</td>
      <td valign="top">:</td>
      <td>AH=1BH</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Salida</td>
      <td valign="top">:</td>
      <td>Ninguna</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Función</td>
      <td valign="top">:</td>
      <td>Limpia el contenido del buffer de teclas e invalida las entradas de teclas anteriores.</td>
    </tr>
  </tbody>
</table>


<p>----------</p>

<table border="0" cellspacing="1">
  <tbody>
    <tr>
      <td valign="top" width="33" align="right"><a name="1d"></a>Número</td>
      <td valign="top">:</td>
      <td>1DH=Extraer código de un carácter del buffer de teclas</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Entrada</td>
      <td valign="top">:</td>
      <td>AH=1DH</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Salida</td>
      <td valign="top">:</td>
      <td>DL=Código de carácter<br>
      DH=0:Buffer de teclas vacío 1:Hubo datos</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Función</td>
      <td valign="top">:</td>
      <td>Extrae datos de un carácter del buffer de teclas.</td>
    </tr>
  </tbody>
</table>


<p>----------</p>

<table border="0" cellspacing="1">
  <tbody>
    <tr>
      <td valign="top" width="33" align="right"><a name="1e"></a>Número</td>
      <td valign="top">:</td>
      <td>1EH=Examinar código del siguiente carácter en el buffer de teclas</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Entrada</td>
      <td valign="top">:</td>
      <td>AH=1EH</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Salida</td>
      <td valign="top">:</td>
      <td>DL=Código de carácter<br>
      DH=0:Buffer de teclas vacío 1:Hubo datos</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Función</td>
      <td valign="top">:</td>
      <td>Lee datos del siguiente carácter del buffer de teclas.<br>
      No afecta el contenido del buffer de teclas.</td>
    </tr>
  </tbody>
</table>


<p>----------</p>

<table border="0" cellspacing="1">
  <tbody>
    <tr>
      <td valign="top" width="33" align="right"><a name="1f"></a>Número</td>
      <td valign="top">:</td>
      <td>1FH=Realizar muestreo de la tecla [BRK] y del interruptor de encendido</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Entrada</td>
      <td valign="top">:</td>
      <td>AH=1FH</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Salida</td>
      <td valign="top">:</td>
      <td>Ninguna</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Función</td>
      <td valign="top">:</td>
      <td>Verifica el estado de la tecla [BRK] y del interruptor de encendido, y realiza el procesamiento correspondiente a cada uno.</td>
    </tr>
  </tbody>
</table>


<p>----------</p>

<table border="0" cellspacing="1">
  <tbody>
    <tr>
      <td valign="top" width="33" align="right"><a name="20"></a>Número</td>
      <td valign="top">:</td>
      <td>20H=Mostrar cadena especificada en pantalla LCD</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Entrada</td>
      <td valign="top">:</td>
      <td>AH=20H<br>
      DI=Dirección de desplazamiento superior de la cadena</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Salida</td>
      <td valign="top">:</td>
      <td>Ninguna</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Función</td>
      <td valign="top">:</td>
      <td>Muestra la cadena especificada en la posición actual del cursor en LCD.<br>
      La cadena es una cadena ASCIIZ terminada en 0.<br>
      Además, si hay códigos de control en la cadena, también se procesan.<br>
      La dirección de segmento está fijada en 0.</td>
    </tr>
  </tbody>
</table>


<p>----------</p>

<table border="0" cellspacing="1">
  <tbody>
    <tr>
      <td valign="top" width="33" align="right"><a name="22"></a>Número</td>
      <td valign="top">:</td>
      <td valign="top">22H=Transmitir cadena especificada a RS-232C</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Entrada</td>
      <td valign="top">:</td>
      <td valign="top">AH=22H<br>
      DI=Dirección de desplazamiento superior de la cadena</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Salida</td>
      <td valign="top">:</td>
      <td valign="top">Ninguna</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Función</td>
      <td valign="top">:</td>
      <td valign="top">Transmite la cadena especificada a RS-232C.<br>
      Si la cadena contiene códigos de control RS-232C, es posible que la transmisión no se realice correctamente.<br>
      Ejecutar después de abrir RS-232C.<br>
      La dirección de segmento está fijada en 0.</td>
    </tr>
  </tbody>
</table>


<p>----------</p>

<table border="0" cellspacing="1">
  <tbody>
    <tr>
      <td valign="top" width="33" align="right"><a name="23"></a>Número</td>
      <td valign="top">:</td>
      <td valign="top">23H=Ingresar cadena usando editor de pantalla</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Entrada</td>
      <td valign="top">:</td>
      <td valign="top">AH=23H</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Salida</td>
      <td valign="top">:</td>
      <td valign="top">DI=Cadena ingresada</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Función</td>
      <td valign="top">:</td>
      <td valign="top">Ingresa una cadena usando el editor de pantalla.<br>
      Se ingresa la línea lógica al presionar retorno, se agrega 0 al final de la cadena y se retorna su dirección de desplazamiento inicial.<br>
      La dirección de segmento está fijada en 0.</td>
    </tr>
  </tbody>
</table>


<p>----------</p>

<table border="0" cellspacing="1">
  <tbody>
    <tr>
      <td valign="top" width="33" align="right"><a name="28"></a>Número</td>
      <td valign="top">:</td>
      <td>28H=Emitir sonido BEEP</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Entrada</td>
      <td valign="top">:</td>
      <td>AH=28H<br>
      DL=0:Emitir sonido bajo (equivalente a BEEP0) 1:Emitir sonido alto (equivalente a BEEP1)</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Salida</td>
      <td valign="top">:</td>
      <td>Ninguna</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Función</td>
      <td valign="top">:</td>
      <td>Emitir el mismo sonido que "BEEP" de BASIC.<br>
      No retorna al programa de usuario hasta que termine el sonido.</td>
    </tr>
  </tbody>
</table>


<p>----------</p>

<table border="0" cellspacing="1">
  <tbody>
    <tr>
      <td valign="top" width="33" align="right"><a name="29"></a>Número</td>
      <td valign="top">:</td>
      <td>29H=Emitir zumbador a frecuencia especificada</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Entrada</td>
      <td valign="top">:</td>
      <td>AH=29H<br>
      DX=Frecuencia (3.6864E6/(8*DX)[Hz])<br>
      CX=Duración del sonido (4*DX*CX/3.6864E6[segundos])</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Salida</td>
      <td valign="top">:</td>
      <td>Ninguna</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Función</td>
      <td valign="top">:</td>
      <td>Emitir sonido a la frecuencia especificada durante el tiempo especificado.<br>
      No retorna al programa de usuario hasta que termine el sonido.</td>
    </tr>
  </tbody>
</table>


<p>----------</p>

<table border="0" cellspacing="1">
  <tbody>
    <tr>
      <td valign="top" width="33" align="right"><a name="2a"></a>Número</td>
      <td valign="top">:</td>
      <td>2AH=Establecer líneas de señal RS-232C/Centrónicas en HIGH</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Entrada</td>
      <td valign="top">:</td>
      <td>AH=2AH<br>
      BL=b7 b6 b5 b4 b3 b2 b1 b0<br>
      　　　　　　　 │ │ │ └─ INIT<br>
      　　　　　　　 │ │ └──- STB<br>
      　　　　　　　 │ └──── RTS<br>
      　　　　　　　 └─────- DTR</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Salida</td>
      <td valign="top">:</td>
      <td>Ninguna</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Función</td>
      <td valign="top">:</td>
      <td>Establece en HIGH las líneas de señal correspondientes a los bits especificados como 1 en BL.<br>
      El estado de otras líneas de señal no cambia.</td>
    </tr>
  </tbody>
</table>


<p>----------</p>

<table border="0" cellspacing="1">
  <tbody>
    <tr>
      <td valign="top" width="33" align="right"><a name="2b"></a>Número</td>
      <td valign="top">:</td>
      <td>2BH=Establecer líneas de señal RS-232C/Centrónicas en LOW</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Entrada</td>
      <td valign="top">:</td>
      <td>AH=2BH<br>
      BL=b7 b6 b5 b4 b3 b2 b1 b0<br>
      　　　　　　　 │ │ │ └─ INIT<br>
      　　　　　　　 │ │ └──- STB<br>
      　　　　　　　 │ └──── RTS<br>
      　　　　　　　 └─────- DTR</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Salida</td>
      <td valign="top">:</td>
      <td>Ninguna</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Función</td>
      <td valign="top">:</td>
      <td>Establece en LOW las líneas de señal correspondientes a los bits especificados como 1 en BL.<br>
      El estado de otras líneas de señal no cambia.</td>
    </tr>
  </tbody>
</table>


<p>----------</p>

<table border="0" cellspacing="1">
  <tbody>
    <tr>
      <td valign="top" width="33" align="right"><a name="2c"></a>Número</td>
      <td valign="top">:</td>
      <td>2CH=Verificar estado de líneas de señal RS-232C/Centrónicas</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Entrada</td>
      <td valign="top">:</td>
      <td>AH=2CH</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Salida</td>
      <td valign="top">:</td>
      <td>BL=b7 b6 b5 b4 b3 b2 b1 b0<br>
      　 │ │ │ │ │ │ │ └─ BUSY<br>
      　 │ │ │ │ │ │ └──- CD<br>
      　 │ │ │ │ │ └──── DSR<br>
      　 │ │ │ │ └─────- CTR<br>
      　 │ │ │ └─────── INIT<br>
      　 │ │ └────────- STB<br>
      　 │ └────────── RTS<br>
      　 └───────────- DTR</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Función</td>
      <td valign="top">:</td>
      <td>Verifica el estado de las líneas de señal RS-232C/Centrónicas.<br>
      Cuando la señal está en HIGH es 1, cuando está en LOW es 0.</td>
    </tr>
  </tbody>
</table>


<p>----------</p>

<table border="0" cellspacing="1">
  <tbody>
    <tr>
      <td valign="top" nowrap width="33" align="right"><a name="2f"></a>Número</td>
      <td valign="top">:</td>
      <td valign="top">2FH=Esperar tiempo especificado</td>
    </tr>
    <tr>
      <td valign="top" nowrap width="33" align="right">Entrada</td>
      <td valign="top">:</td>
      <td valign="top">AH=2FH<br>
      DX=Tiempo de espera (4*DX/3.6864E6[segundos])</td>
    </tr>
    <tr>
      <td valign="top" nowrap width="33" align="right">Salida</td>
      <td valign="top">:</td>
      <td valign="top">Ninguna</td>
    </tr>
    <tr>
      <td valign="top" nowrap width="33" align="right">Función</td>
      <td valign="top">:</td>
      <td valign="top">Espera durante el tiempo especificado.<br>
      Durante la espera se procesan interrupciones.<br>
      Debido a que usa el temporizador 1 para contar el tiempo, esta BIOS no se puede usar si el programa de usuario está usando el temporizador 1.</td>
    </tr>
  </tbody>
</table>


<p>----------</p>

<table border="0" cellspacing="1">
  <tbody>
    <tr>
      <td valign="top" width="33" align="right"><a name="3c"></a>Número</td>
      <td valign="top">:</td>
      <td>3CH=Retornar posición actual del cursor</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Entrada</td>
      <td valign="top">:</td>
      <td>AH=3CH</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Salida</td>
      <td valign="top">:</td>
      <td>BL=Coordenada X del cursor<br>
      BH=Coordenada Y del cursor</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Función</td>
      <td valign="top">:</td>
      <td>Retorna la posición actual del cursor.</td>
    </tr>
  </tbody>
</table>


<p>----------</p>

<table border="0" cellspacing="1">
  <tbody>
    <tr>
      <td valign="top" width="33" align="right"><a name="41"></a>Número</td>
      <td valign="top">:</td>
      <td valign="top">41H=Retornar dirección superior del área de almacenamiento de fuentes de caracteres definidos por usuario</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Entrada</td>
      <td valign="top">:</td>
      <td valign="top">AH=41H</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Salida</td>
      <td valign="top">:</td>
      <td valign="top">BX=Dirección de desplazamiento donde se almacenan las fuentes de caracteres definidos por usuario</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Función</td>
      <td valign="top">:</td>
      <td valign="top">Retorna la dirección de desplazamiento donde se almacenan los datos de fuentes de caracteres definidos por usuario (códigos de carácter 252～255, caracteres definidos con "DEFCHR$").<br>
      La dirección de segmento está fijada en 0.<br>
      Las fuentes se almacenan en orden de códigos de carácter 252, 253, ..., 255, con un área de 8 bytes por carácter, 32 bytes en total.<br>
      Nota: Hay un desplazamiento de 90° respecto a los datos especificados en "DEFCHR$".</td>
      
      <p>・・□■■■□□ = 1CH<br>
      ・・■□□□■□ = 22H<br>
      ・・■□□□■□ = 22H<br>
      ・・■■■■■□ = 3EH = 1CH,22H,22H,3EH,22H,22H,22H,00H<br>
      ・・■□□□■□ = 22H<br>
      ・・■□□□■□ = 22H<br>
      ・・■□□□■□ = 22H<br>
      ・・□□□□□□ = 00H</p>
      
      </td>
    </tr>
  </tbody>
</table>


<p>----------</p>

<table border="0" cellspacing="1">
  <tbody>
    <tr>
      <td valign="top" width="33" align="right"><a name="43"></a>Número</td>
      <td valign="top">:</td>
      <td>43H=Mostrar 1 dígito hexadecimal en LCD de los 4 bits inferiores de DL</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Entrada</td>
      <td valign="top">:</td>
      <td>AH=43H<br>
      DL=Datos</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Salida</td>
      <td valign="top">:</td>
      <td>Ninguna</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Función</td>
      <td valign="top">:</td>
      <td>Muestra 1 dígito hexadecimal de los 4 bits inferiores de DL en la posición actual del cursor.</td>
    </tr>
  </tbody>
</table>


<p>----------</p>

<table border="0" cellspacing="1">
  <tbody>
    <tr>
      <td valign="top" width="33" align="right"><a name="44"></a>Número</td>
      <td valign="top">:</td>
      <td>44H=Mostrar 2 dígitos hexadecimales en LCD del contenido de DL</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Entrada</td>
      <td valign="top">:</td>
      <td>AH=44H<br>
      DL=Datos</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Salida</td>
      <td valign="top">:</td>
      <td>Ninguna</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Función</td>
      <td valign="top">:</td>
      <td>Muestra 2 dígitos hexadecimales del contenido de DL en la posición actual del cursor.</td>
    </tr>
  </tbody>
</table>


<p>----------</p>

<table border="0" cellspacing="1">
  <tbody>
    <tr>
      <td valign="top" width="33" align="right"><a name="45"></a>Número</td>
      <td valign="top">:</td>
      <td>45H=Mostrar 4 dígitos hexadecimales en LCD del contenido de DX</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Entrada</td>
      <td valign="top">:</td>
      <td>AH=45H<br>
      DX=Datos</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Salida</td>
      <td valign="top">:</td>
      <td>Ninguna</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Función</td>
      <td valign="top">:</td>
      <td>Muestra 4 dígitos hexadecimales del contenido de DX en la posición actual del cursor.</td>
    </tr>
  </tbody>
</table>


<p>----------</p>

<table border="0" cellspacing="1">
  <tbody>
    <tr>
      <td valign="top" width="33" align="right"><a name="46"></a>Número</td>
      <td valign="top">:</td>
      <td>46H=Mostrar 5 dígitos BCD en LCD del contenido de DX</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Entrada</td>
      <td valign="top">:</td>
      <td>AH=46H<br>
      DX=Datos<br>
      BL=0:Alineación izquierda 1:Alineación derecha</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Salida</td>
      <td valign="top">:</td>
      <td>Ninguna</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Función</td>
      <td valign="top">:</td>
      <td>Muestra 5 dígitos BCD del contenido de DX en la posición actual del cursor.</td>
    </tr>
  </tbody>
</table>


<p>----------</p>

<table border="0" cellspacing="1">
  <tbody>
    <tr>
      <td valign="top" width="33" align="right"><a name="47"></a>Número</td>
      <td valign="top">:</td>
      <td>47H=Repetir tecla actualmente presionada</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Entrada</td>
      <td valign="top">:</td>
      <td>AH=47H</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Salida</td>
      <td valign="top">:</td>
      <td>Ninguna</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Función</td>
      <td valign="top">:</td>
      <td>Repite la tecla actualmente presionada.<br>
      Esta BIOS debe colocarse inmediatamente después de una BIOS que realiza entrada de teclas.</td>
    </tr>
  </tbody>
</table>


<p>----------</p>

<table border="0" cellspacing="1">
  <tbody>
    <tr>
      <td valign="top" width="33" align="right"><a name="67"></a>Número</td>
      <td valign="top">:</td>
      <td>67H=Inicialización de todo el sistema</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Entrada</td>
      <td valign="top">:</td>
      <td>AH=67H<br>
      SI=4249H<br>
      DI=4F53H</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Salida</td>
      <td valign="top">:</td>
      <td>Ninguna</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Función</td>
      <td valign="top">:</td>
      <td>Ejecuta ALL RESET.<br>
      Realiza el mismo procesamiento que presionar el botón ALL RESET en la parte frontal del dispositivo.<br>
      Realmente borra todo, así que tener mucho cuidado al usarlo.</td>
    </tr>
  </tbody>
</table>


<p>----------</p>

<table border="0" cellspacing="1">
  <tbody>
    <tr>
      <td valign="top" width="33" align="right"><a name="68"></a>Número</td>
      <td valign="top">:</td>
      <td>68H=Comando PAINT</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Entrada</td>
      <td valign="top">:</td>
      <td>AH=68H<br>
      BL=Coordenada X (0～191)<br>
      BH=Coordenada Y (0～63)</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Salida</td>
      <td valign="top">:</td>
      <td>Ninguna</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Función</td>
      <td valign="top">:</td>
      <td>Rellena un espacio cerrado que contenga las coordenadas especificadas.<br>
      Esta BIOS procesa el buffer de puntos y luego se muestra en LCD.</td>
    </tr>
  </tbody>
</table>


<p>----------</p>

<table border="0" cellspacing="1">
  <tbody>
    <tr>
      <td valign="top" width="33" align="right"><a name="6f"></a>Número</td>
      <td valign="top">:</td>
      <td>6FH=Cambiar tiempo de espera de INPORT, OUTPORT</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Entrada</td>
      <td valign="top">:</td>
      <td>AH=6FH<br>
      DL=Número de estados de espera (00H～0FH)</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Salida</td>
      <td valign="top">:</td>
      <td>Ninguna</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Función</td>
      <td valign="top">:</td>
      <td>Cambia los estados de espera al ejecutar "INPORT" y "OUTPORT" al número de estados especificado.<br>
      El valor inicial del número de estados de espera es 0.</td>
    </tr>
  </tbody>
</table>


<p>----------</p>

<table border="0" cellspacing="1">
  <tbody>
    <tr>
      <td valign="top" width="33" align="right"><a name="70"></a>Número</td>
      <td valign="top">:</td>
      <td>70H=Salida de datos al puerto de expansión de 30 pines</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Entrada</td>
      <td valign="top">:</td>
      <td>AH=70H<br>
      BL=Dirección (00H～07H)<br>
      DL=Datos</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Salida</td>
      <td valign="top">:</td>
      <td>Ninguna</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Función</td>
      <td valign="top">:</td>
      <td>Envía datos a la dirección especificada del puerto de expansión de 30 pines.</td>
    </tr>
  </tbody>
</table>


<p>----------</p>

<table border="0" cellspacing="1">
  <tbody>
    <tr>
      <td valign="top" width="33" align="right"><a name="71"></a>Número</td>
      <td valign="top">:</td>
      <td>71H=Entrada de datos desde puerto de expansión de 30 pines</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Entrada</td>
      <td valign="top">:</td>
      <td>AH=71H<br>
      BL=Dirección (00H～07H)</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Salida</td>
      <td valign="top">:</td>
      <td>DL=Datos</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Función</td>
      <td valign="top">:</td>
      <td>Lee datos desde la dirección especificada del puerto de expansión de 30 pines.</td>
    </tr>
  </tbody>
</table>


<p>----------</p>

<table border="0" cellspacing="1">
  <tbody>
    <tr>
      <td valign="top" width="33" align="right"><a name="7e"></a>Número</td>
      <td valign="top">:</td>
      <td>7EH=Realizar reset P</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Entrada</td>
      <td valign="top">:</td>
      <td>AH=7EH</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Salida</td>
      <td valign="top">:</td>
      <td>Ninguna</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Función</td>
      <td valign="top">:</td>
      <td>Reinicia el dispositivo.<br>
      Realiza el mismo procesamiento que presionar el botón P en la parte posterior del dispositivo.</td>
    </tr>
  </tbody>
</table>


<p>----------</p>

<table border="0" cellspacing="1">
  <tbody>
    <tr>
      <td valign="top" width="33" align="right"><a name="7f"></a>Número</td>
      <td valign="top">:</td>
      <td>7FH=Apagar alimentación</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Entrada</td>
      <td valign="top">:</td>
      <td>AH=7FH</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Salida</td>
      <td valign="top">:</td>
      <td>Ninguna</td>
    </tr>
    <tr>
      <td valign="top" width="33" align="right">Función</td>
      <td valign="top">:</td>
      <td>Establece el mismo estado que el apagado automático de alimentación.</td>
    </tr>
  </tbody>
</table>

<p><font size="5"><u>５. Sobre este documento</u></font></p>

<p>Este documento se ha elaborado tomando como referencia «Z-1/FX-890P Katsuyō Kenkyū (Kōgakusha)», corrigiendo algunos errores presentes en la obra original y añadiendo varias notas complementarias.<br>
<br>
Además, por favor, no consulte el contenido de este documento con Kōgakusha ni con CASIO.<br>
</p>


