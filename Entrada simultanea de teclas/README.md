# Método para verificar la entrada simultánea de teclas  

Este documento presenta un método para realizar la detección de entrada simultánea de teclas en la Z-1/FX-890P.  

Además, en este documento se asume como condición previa un conocimiento básico del ensamblador 8086 para su explicación. Por lo tanto, sería prudente que quienes se pregunten "¿qué es el ensamblador?" no continúen leyendo más allá.  

La forma más sencilla de manejar la entrada de teclas desde ensamblador es usar la BIOS. Sin embargo, el proceso de entrada de teclas de la BIOS obtiene datos del búfer de teclas, por lo que no puede manejar situaciones como presionar múltiples teclas simultáneamente o conocer en qué momento exacto se presiona una tecla.  

Para hacer posible esto, es necesario verificar directamente el estado de la matriz de teclas. Aquí se explica el método para manejar la matriz de teclas desde ensamblador.  

１．Puertos de teclado

## Tabla de Puertos de E/S

<table border="1" style="background-color: #999999; border-collapse: collapse; margin: 20px 0; width: 100%;">
  <tbody>
    <tr>
      <th style="padding: 6px; text-align: center; background-color: #C0C0C0;">DIRECCIÓN</th>
      <th style="padding: 6px; text-align: center; background-color: #C0C0C0;">CONTENIDO</th>
    </tr>
    
    <!-- Fila 1: 0200H -->
    <tr>
      <td style="padding: 8px; text-align: center; vertical-align: top; width: 70px;"><strong>0200H</strong></td>
      <td style="padding: 8px;">
        <strong>KO port (Key Out)</strong><br>
        
        <!-- Tabla de bits en formato vertical compacto -->
        <div style="margin: 8px 0; font-size: 0.85em;">
          <table border="1" style="background-color: #999999; border-collapse: collapse; width: 100%;">
            <tr style="background-color: #C0C0C0; text-align: center;">
              <th style="padding: 2px; width: 20%;">Bits</th>
              <th style="padding: 2px;">Función</th>
            </tr>
            <tr style="background-color: #FFFFFF;">
              <td style="padding: 3px; text-align: center;"><strong>b15-b13</strong></td>
              <td style="padding: 3px;">- (no usado)</td>
            </tr>
            <tr style="background-color: #FFFFFF;">
              <td style="padding: 3px; text-align: center;"><strong>b12</strong></td>
              <td style="padding: 3px;">DATA</td>
            </tr>
            <tr style="background-color: #FFFFFF;">
              <td style="padding: 3px; text-align: center;"><strong>b11</strong></td>
              <td style="padding: 3px;">KOS</td>
            </tr>
            <tr style="background-color: #FFFFFF;">
              <td style="padding: 3px; text-align: center;"><strong>b10</strong></td>
              <td style="padding: 3px;">KO10</td>
            </tr>
            <tr style="background-color: #FFFFFF;">
              <td style="padding: 3px; text-align: center;"><strong>b9</strong></td>
              <td style="padding: 3px;">KO9</td>
            </tr>
            <tr style="background-color: #FFFFFF;">
              <td style="padding: 3px; text-align: center;"><strong>b8</strong></td>
              <td style="padding: 3px;">KO8</td>
            </tr>
            <tr style="background-color: #FFFFFF;">
              <td style="padding: 3px; text-align: center;"><strong>b7</strong></td>
              <td style="padding: 3px;">KO7</td>
            </tr>
            <tr style="background-color: #FFFFFF;">
              <td style="padding: 3px; text-align: center;"><strong>b6</strong></td>
              <td style="padding: 3px;">KO6</td>
            </tr>
            <tr style="background-color: #FFFFFF;">
              <td style="padding: 3px; text-align: center;"><strong>b5</strong></td>
              <td style="padding: 3px;">KO5</td>
            </tr>
            <tr style="background-color: #FFFFFF;">
              <td style="padding: 3px; text-align: center;"><strong>b4</strong></td>
              <td style="padding: 3px;">KO4</td>
            </tr>
            <tr style="background-color: #FFFFFF;">
              <td style="padding: 3px; text-align: center;"><strong>b3</strong></td>
              <td style="padding: 3px;">KO3</td>
            </tr>
            <tr style="background-color: #FFFFFF;">
              <td style="padding: 3px; text-align: center;"><strong>b2</strong></td>
              <td style="padding: 3px;">KO2</td>
            </tr>
            <tr style="background-color: #FFFFFF;">
              <td style="padding: 3px; text-align: center;"><strong>b1</strong></td>
              <td style="padding: 3px;">KO1</td>
            </tr>
            <tr style="background-color: #FFFFFF;">
              <td style="padding: 3px; text-align: center;"><strong>b0</strong></td>
              <td style="padding: 3px;">KO0</td>
            </tr>
          </table>
        </div>
      </td>
    </tr>
    
    <!-- Fila 2: 0202H -->
    <tr>
      <td style="padding: 8px; text-align: center; vertical-align: top;"><strong>0202H</strong></td>
      <td style="padding: 8px;">
        <strong>KI port (Key In)</strong><br>
        
        <!-- Tabla de bits en formato vertical compacto -->
        <div style="margin: 8px 0; font-size: 0.85em;">
          <table border="1" style="background-color: #999999; border-collapse: collapse; width: 100%;">
            <tr style="background-color: #C0C0C0; text-align: center;">
              <th style="padding: 2px; width: 20%;">Bits</th>
              <th style="padding: 2px;">Función</th>
            </tr>
            <tr style="background-color: #FFFFFF;">
              <td style="padding: 3px; text-align: center;"><strong>b15</strong></td>
              <td style="padding: 3px;">KINT</td>
            </tr>
            <tr style="background-color: #FFFFFF;">
              <td style="padding: 3px; text-align: center;"><strong>b14</strong></td>
              <td style="padding: 3px;">SW</td>
            </tr>
            <tr style="background-color: #FFFFFF;">
              <td style="padding: 3px; text-align: center;"><strong>b13-b12</strong></td>
              <td style="padding: 3px;">0 (no usado)</td>
            </tr>
            <tr style="background-color: #FFFFFF;">
              <td style="padding: 3px; text-align: center;"><strong>b11</strong></td>
              <td style="padding: 3px;">KIS</td>
            </tr>
            <tr style="background-color: #FFFFFF;">
              <td style="padding: 3px; text-align: center;"><strong>b10</strong></td>
              <td style="padding: 3px;">0 (no usado)</td>
            </tr>
            <tr style="background-color: #FFFFFF;">
              <td style="padding: 3px; text-align: center;"><strong>b9</strong></td>
              <td style="padding: 3px;">KI9</td>
            </tr>
            <tr style="background-color: #FFFFFF;">
              <td style="padding: 3px; text-align: center;"><strong>b8</strong></td>
              <td style="padding: 3px;">KI8</td>
            </tr>
            <tr style="background-color: #FFFFFF;">
              <td style="padding: 3px; text-align: center;"><strong>b7</strong></td>
              <td style="padding: 3px;">KI7</td>
            </tr>
            <tr style="background-color: #FFFFFF;">
              <td style="padding: 3px; text-align: center;"><strong>b6</strong></td>
              <td style="padding: 3px;">KI6</td>
            </tr>
            <tr style="background-color: #FFFFFF;">
              <td style="padding: 3px; text-align: center;"><strong>b5</strong></td>
              <td style="padding: 3px;">KI5</td>
            </tr>
            <tr style="background-color: #FFFFFF;">
              <td style="padding: 3px; text-align: center;"><strong>b4</strong></td>
              <td style="padding: 3px;">KI4</td>
            </tr>
            <tr style="background-color: #FFFFFF;">
              <td style="padding: 3px; text-align: center;"><strong>b3</strong></td>
              <td style="padding: 3px;">KI3</td>
            </tr>
            <tr style="background-color: #FFFFFF;">
              <td style="padding: 3px; text-align: center;"><strong>b2</strong></td>
              <td style="padding: 3px;">KI2</td>
            </tr>
            <tr style="background-color: #FFFFFF;">
              <td style="padding: 3px; text-align: center;"><strong>b1</strong></td>
              <td style="padding: 3px;">KI1</td>
            </tr>
            <tr style="background-color: #FFFFFF;">
              <td style="padding: 3px; text-align: center;"><strong>b0</strong></td>
              <td style="padding: 3px;">KI0</td>
            </tr>
          </table>
        </div>
      </td>
    </tr>
    
    <!-- Fila 3: 0204H -->
    <tr>
      <td style="padding: 8px; text-align: center; vertical-align: top;"><strong>0204H</strong></td>
      <td style="padding: 8px; background-color: #FFFFFF;">
        <strong>KEYCTL (Control de Teclado)</strong><br><br>
        <div style="margin-left: 10px;">
          • <strong>b0:</strong> Cuando es 0 → Interrupción de teclado <strong>deshabilitada</strong><br>
          • <strong>b1:</strong> 1→0 (flanco de bajada) → <strong>Borra/limpia</strong> la interrupción de teclado
        </div>
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


