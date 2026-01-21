# Método para verificar la entrada simultánea de teclas  

Este documento presenta un método para realizar la detección de entrada simultánea de teclas en la Z-1/FX-890P.  

Además, en este documento se asume como condición previa un conocimiento básico del ensamblador 8086 para su explicación. Por lo tanto, sería prudente que quienes se pregunten "¿qué es el ensamblador?" no continúen leyendo más allá.  

La forma más sencilla de manejar la entrada de teclas desde ensamblador es usar la BIOS. Sin embargo, el proceso de entrada de teclas de la BIOS obtiene datos del búfer de teclas, por lo que no puede manejar situaciones como presionar múltiples teclas simultáneamente o conocer en qué momento exacto se presiona una tecla.  

Para hacer posible esto, es necesario verificar directamente el estado de la matriz de teclas. Aquí se explica el método para manejar la matriz de teclas desde ensamblador.  

１．Puertos de teclado

## Tabla de Puertos de E/S

<table border="1" style="background-color: #999999; border-collapse: collapse; margin: 20px 0;">
  <tbody>
    <tr>
      <th style="padding: 4px; text-align: center;">DIRECCIÓN</th>
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


