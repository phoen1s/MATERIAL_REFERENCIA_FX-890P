# Método para verificar la entrada simultánea de teclas  

Este documento presenta un método para realizar la detección de entrada simultánea de teclas en la Z-1/FX-890P.  
Además, en este documento se asume como condición previa un conocimiento básico del ensamblador 8086 para su explicación. Por lo tanto, sería prudente que quienes se pregunten "¿qué es el ensamblador?" no continúen leyendo más allá.  

La forma más sencilla de manejar la entrada de teclas desde ensamblador es usar la BIOS. Sin embargo, el proceso de entrada de teclas de la BIOS obtiene datos del búfer de teclas, por lo que no puede manejar situaciones como presionar múltiples teclas simultáneamente o conocer en qué momento exacto se presiona una tecla.  
Para hacer posible esto, es necesario verificar directamente el estado de la matriz de teclas. Aquí se explica el método para manejar la matriz de teclas desde ensamblador.  

## 1. Puertos de Teclado

La matriz de teclas está conectada a los puertos de E/S.

<table border="1" style="background-color: #999999; border-collapse: collapse; margin: 20px 0;">
  <tbody>
    <tr>
      <th style="padding: 4px; text-align: center;">Address</th>
      <th style="padding: 4px; text-align: center;">CONTENIDO</th>
    </tr>
    <tr>
      <td style="padding: 4px; text-align: center;">0200H</td>
      <td style="padding: 4px;">
        KO port (Key Out)<br>
        <div style="text-align: center;">
        <table cellspacing="0" style="background-color: #999999; border: 1px solid; border-collapse: collapse; margin: 8px auto;">
          <tbody align="center">
            <tr>
              <th style="padding: 2px;">b15</th>
              <th style="padding: 2px;">b14</th>
              <th style="padding: 2px;">b13</th>
              <th style="padding: 2px;">b12</th>
              <th style="padding: 2px;">b11</th>
              <th style="padding: 2px;">b10</th>
              <th style="padding: 2px;">b9</th>
              <th style="padding: 2px;">b8</th>
              <th style="padding: 2px;">b7</th>
              <th style="padding: 2px;">b6</th>
              <th style="padding: 2px;">b5</th>
              <th style="padding: 2px;">b4</th>
              <th style="padding: 2px;">b3</th>
              <th style="padding: 2px;">b2</th>
              <th style="padding: 2px;">b1</th>
              <th style="padding: 2px;">b0</th>
            </tr>
            <tr>
              <td style="padding: 2px;">-</td>
              <td style="padding: 2px;">-</td>
              <td style="padding: 2px;">-</td>
              <td style="padding: 2px;">DATA</td>
              <td style="padding: 2px;">KOS</td>
              <td style="padding: 2px;">KO10</td>
              <td style="padding: 2px;">KO9</td>
              <td style="padding: 2px;">KO8</td>
              <td style="padding: 2px;">KO7</td>
              <td style="padding: 2px;">KO6</td>
              <td style="padding: 2px;">KO5</td>
              <td style="padding: 2px;">KO4</td>
              <td style="padding: 2px;">KO3</td>
              <td style="padding: 2px;">KO2</td>
              <td style="padding: 2px;">KO1</td>
              <td style="padding: 2px;">KO0</td>
            </tr>
          </tbody>
        </table>
        </div>
      </td>
    </tr>
    <tr>
      <td style="padding: 4px; text-align: center;">0202H</td>
      <td style="padding: 4px;">
        KI port (Key In)<br>
        <table cellspacing="0" style="background-color: #999999; border: 1px solid; border-collapse: collapse; margin: 8px ;">
          <tbody align="center">
            <tr>
              <th style="padding: 2px;">b15</th>
              <th style="padding: 2px;">b14</th>
              <th style="padding: 2px;">b13</th>
              <th style="padding: 2px;">b12</th>
              <th style="padding: 2px;">b11</th>
              <th style="padding: 2px;">b10</th>
              <th style="padding: 2px;">b9</th>
              <th style="padding: 2px;">b8</th>
              <th style="padding: 2px;">b7</th>
              <th style="padding: 2px;">b6</th>
              <th style="padding: 2px;">b5</th>
              <th style="padding: 2px;">b4</th>
              <th style="padding: 2px;">b3</th>
              <th style="padding: 2px;">b2</th>
              <th style="padding: 2px;">b1</th>
              <th style="padding: 2px;">b0</th>
            </tr>
            <tr>
              <td style="padding: 2px;">KINT</td>
              <td style="padding: 2px;">SW</td>
              <td style="padding: 2px;">0</td>
              <td style="padding: 2px;">0</td>
              <td style="padding: 2px;">KIS</td>
              <td style="padding: 2px;">0</td>
              <td style="padding: 2px;">KI9</td>
              <td style="padding: 2px;">KI8</td>
              <td style="padding: 2px;">KI7</td>
              <td style="padding: 2px;">KI6</td>
              <td style="padding: 2px;">KI5</td>
              <td style="padding: 2px;">KI4</td>
              <td style="padding: 2px;">KI3</td>
              <td style="padding: 2px;">KI2</td>
              <td style="padding: 2px;">KI1</td>
              <td style="padding: 2px;">KI0</td>
            </tr>
          </tbody>
        </table>
      </td>
    </tr>
    <tr>
      <td style="padding: 4px; text-align: center;">0204H</td>
      <td style="padding: 4px;">
        KEYCTL<br>
        •	b0: cuando es 0, se deshabilita la interrupción del teclado<br>
	      •	b1: al cambiar de 1 → 0, se limpia (borra) la interrupción del teclado
      </td>
    </tr>
  </tbody>
</table>  

## <u>２．Matriz de Teclas</u>

|       | KO0 | KO1 | KO2 | KO3 | KO4 | KO5 | KO6 | KO7 | KO8 | KO9 | KO10 | KOS |
|-------|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|------|-----|
| **KI0**  | BRK |     |     |     |     |     |     |     |     |     |      |     |
| **KI1**  |     | TAB | W   | R   | Y   | I   | P   | MENU | CAL | DEGR | SQR  |     |
| **KI2**  |     | Q   | E   | T   | U   | O   | S   | log  | ln  | sin  | X²   |     |
| **KI3**  |     | RESET | S   | F   | H   | K   | ;   | MR   | M+  | (    | ENG  |     |
| **KI4**  |     | A   | D   | G   | J   | L   | :   | 7    | 8   | )    | CLS  |     |
| **KI5**  |     | CAPS | X   | V   | N   | ,   | ↑   | 4    | 5   | 9    | cos  |     |
| **KI6**  |     | Z   | C   | B   | M   | INS | DEL | 1    | 2   | 6    | ^    |     |
| **KI7**  |     |     | SRCH | OUT | SPACE | ← | →   | .    | 3   | *    | ANS  |     |
| **KI8**  |     |     | IN  | CALC | =   | ↓   | 0   | E    | +   | /    |      |     |
| **KI9**  |     |     |     |     |     |     |     | ↵   | -   | BS   | tan  |     |
| **KIS**  |     |     |     |     |     |     |     |     |     |     |      | SHIFT | 

## ３．Método de Operación

El procedimiento para leer la matriz de teclas es:

1. Deshabilitar la interrupción de entrada de teclas.
2. Establecer (a 1) el bit KO correspondiente a la tecla que se desea leer y enviarlo al KO port mediante OUT.
3. Esperar un tiempo determinado.
4. Leer el KI port mediante IN. El bit KI correspondiente a la tecla presionada se establecerá en 1.
5. Operar el KEYCTL para inicializar (borrar) el flag de interrupción.

Por ejemplo, para verificar si la tecla "G" está presionada:  

<table border="1" style="width: 400px; border-collapse: collapse; margin: 20px 0; background-color: #999999;">
  <tbody>
    <tr>
      <td style="padding: 10px; font-family: 'Courier New', monospace; font-size: 14px; line-height: 1.4;">
        <span style="color: #0000FF;">KOUT EQU 200H</span><br>
        <span style="color: #0000FF;">KIN   EQU 202H</span><br>
        <span style="color: #0000FF;">KCTL  EQU 204H</span><br>
        <br>
        <span style="color: #008000; font-weight: bold;">INKEY:</span><br>
        　<span style="color: #0000FF;">PUSH SI</span><br>
        　<span style="color: #0000FF;">PUSH DX</span><br>
        　<span style="color: #0000FF;">PUSH CX</span><br>
        　<span style="color: #0000FF;">PUSH BX</span><br>
        　<span style="color: #0000FF;">CLI</span><br>
        　<span style="color: #0000FF;">MOV DX,KOUT</span><br>
        　<span style="color: #0000FF;">MOV AX,1FFFH</span><br>
        　<span style="color: #0000FF;">OUT DX,AX</span><br>
        　<span style="color: #0000FF;">XOR AX,AX</span><br>
        　<span style="color: #0000FF;">OUT DX,AX</span><br>
        　<span style="color: #0000FF;">MOV CX,8</span><br>
        　<span style="color: #0000FF;">MOV SI,OFFSET KDATA</span><br>
        <span style="color: #008000; font-weight: bold;">INLP1:</span><br>
        　<span style="color: #0000FF;">MOV AX,[SI]</span><br>
        　<span style="color: #0000FF;">ADD SI,2</span><br>
        　<span style="color: #0000FF;">MOV BX,[SI]</span><br>
        　<span style="color: #0000FF;">ADD SI,2</span><br>
        　<span style="color: #0000FF;">CALL KSUB</span><br>
        　<span style="color: #0000FF;">DEC CL</span><br>
        　<span style="color: #0000FF;">JNZ INLP1</span><br>
        　<span style="color: #0000FF;">MOV AX,7FFH</span><br>
        　<span style="color: #0000FF;">OUT DX,AX</span><br>
        　<span style="color: #0000FF;">CALL WAIT</span><br>
        　<span style="color: #0000FF;">MOV DX,KCTL</span><br>
        　<span style="color: #0000FF;">MOV AL,3</span><br>
        　<span style="color: #0000FF;">OUT DX,AL</span><br>
        　<span style="color: #0000FF;">DEC AL</span><br>
        　<span style="color: #0000FF;">OUT DX,AL</span><br>
        　<span style="color: #0000FF;">MOV AL,CH</span><br>
        　<span style="color: #0000FF;">STI</span><br>
        　<span style="color: #0000FF;">POP BX</span><br>
        　<span style="color: #0000FF;">POP CX</span><br>
        　<span style="color: #0000FF;">POP DX</span><br>
        　<span style="color: #0000FF;">POP SI</span><br>
        <span style="color: #008000; font-weight: bold;">WAIT:</span><br>
        　<span style="color: #0000FF;">RET</span><br>
        <br>
        <span style="color: #008000; font-weight: bold;">KSUB:</span><br>
        　<span style="color: #0000FF;">OUT DX,AX</span><br>
        　<span style="color: #0000FF;">CALL WAIT</span><br>
        　<span style="color: #0000FF;">MOV DX,KIN</span><br>
        　<span style="color: #0000FF;">IN AX,DX</span><br>
        　<span style="color: #0000FF;">MOV DX,KOUT</span><br>
        　<span style="color: #0000FF;">TEST AX,BX</span><br>
        　<span style="color: #0000FF;">JZ KSJP1</span><br>
        　<span style="color: #0000FF;">STC</span><br>
        <span style="color: #008000; font-weight: bold;">KSJP1:</span><br>
        　<span style="color: #0000FF;">RCR CH,1</span><br>
        　<span style="color: #0000FF;">RET</span><br>
        <br>
        <span style="color: #008000; font-weight: bold;">KDATA:</span><br>
        　<span style="color: #0000FF;">DW 100H,040H</span> <span style="color: #FF0000;">#2</span><br>
        　<span style="color: #0000FF;">DW 080H,020H</span> <span style="color: #FF0000;">#4</span><br>
        　<span style="color: #0000FF;">DW 200H,040H</span> <span style="color: #FF0000;">#6</span><br>
        　<span style="color: #0000FF;">DW 100H,010H</span> <span style="color: #FF0000;">#8</span><br>
        　<span style="color: #0000FF;">DW 010H,080H</span> <span style="color: #FF0000;">#SPACE</span><br>
        　<span style="color: #0000FF;">DW 800H,800H</span> <span style="color: #FF0000;">#SHIFT</span><br>
        　<span style="color: #0000FF;">DW 080H,200H</span> <span style="color: #FF0000;">#↵</span><br>
        　<span style="color: #0000FF;">DW 001H,001H</span> <span style="color: #FF0000;">#BRK</span>
      </td>
    </tr>
  </tbody>
</table>

AL = Estado de la tecla（0:OFF 1:ON）

<table border="1" cellspacing="0" cellpadding="2" style="border-collapse: collapse; margin: 10px auto;">
  <tbody align="center">
    <tr>
      <th style="padding: 6px; background-color: #C0C0C0; border: 1px solid black;">b7</th>
      <th style="padding: 6px; background-color: #C0C0C0; border: 1px solid black;">b6</th>
      <th style="padding: 6px; background-color: #C0C0C0; border: 1px solid black;">b5</th>
      <th style="padding: 6px; background-color: #C0C0C0; border: 1px solid black;">b4</th>
      <th style="padding: 6px; background-color: #C0C0C0; border: 1px solid black;">b3</th>
      <th style="padding: 6px; background-color: #C0C0C0; border: 1px solid black;">b2</th>
      <th style="padding: 6px; background-color: #C0C0C0; border: 1px solid black;">b1</th>
      <th style="padding: 6px; background-color: #C0C0C0; border: 1px solid black;">b0</th>
    </tr>
    <tr>
      <td style="padding: 6px; border: 1px solid black; background-color: #FFFFFF;">BRK</td>
      <td style="padding: 6px; border: 1px solid black; background-color: #EBEBFF;">↵</td>
      <td style="padding: 6px; border: 1px solid black; background-color: #EBEBFF;">SHIFT</td>
      <td style="padding: 6px; border: 1px solid black; background-color: #EBEBFF;">SPACE</td>
      <td style="padding: 6px; border: 1px solid black; background-color: #EBEBFF;">8</td>
      <td style="padding: 6px; border: 1px solid black; background-color: #EBEBFF;">6</td>
      <td style="padding: 6px; border: 1px solid black; background-color: #EBEBFF;">4</td>
      <td style="padding: 6px; border: 1px solid black; background-color: #EBEBFF;">2</td>
    </tr>
  </tbody>
</table>

##４．Sobre este documento

Este documento se ha elaborado tomando como referencia «Z-1/FX-890P Katsuyō Kenkyū (Kōgakusha)» [Estudio y Aplicación de la Z-1/FX-890P (Editorial Kōgakusha)], corrigiendo algunos errores presentes en la obra original y añadiendo varias notas complementarias.

Además, por favor, no consulte el contenido de este documento con Kōgakusha ni con CASIO.
