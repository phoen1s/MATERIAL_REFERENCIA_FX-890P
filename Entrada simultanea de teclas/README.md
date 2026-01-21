# Método para verificar la entrada simultánea de teclas  

Este documento presenta un método para realizar la detección de entrada simultánea de teclas en la Z-1/FX-890P.  

Además, en este documento se asume como condición previa un conocimiento básico del ensamblador 8086 para su explicación. Por lo tanto, sería prudente que quienes se pregunten "¿qué es el ensamblador?" no continúen leyendo más allá.  

La forma más sencilla de manejar la entrada de teclas desde ensamblador es usar la BIOS. Sin embargo, el proceso de entrada de teclas de la BIOS obtiene datos del búfer de teclas, por lo que no puede manejar situaciones como presionar múltiples teclas simultáneamente o conocer en qué momento exacto se presiona una tecla.  

Para hacer posible esto, es necesario verificar directamente el estado de la matriz de teclas. Aquí se explica el método para manejar la matriz de teclas desde ensamblador.  

１．Puertos de teclado

## Tabla de Puertos de E/S

| Dirección | Contenido |
|-----------|-----------|
| **0200H** | **KO port (Key Out)**<br><br>**Desglose de bits:**<br><br>`b15` | `b14` | `b13` | `b12` | `b11` | `b10` | `b9` | `b8` | `b7` | `b6` | `b5` | `b4` | `b3` | `b2` | `b1` | `b0`<br>--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | ---<br>- | - | - | DATA | KOS | KO10 | KO9 | KO8 | KO7 | KO6 | KO5 | KO4 | KO3 | KO2 | KO1 | KO0 |
| **0202H** | **KI port (Key In)**<br><br>**Desglose de bits:**<br><br>`b15` | `b14` | `b13` | `b12` | `b11` | `b10` | `b9` | `b8` | `b7` | `b6` | `b5` | `b4` | `b3` | `b2` | `b1` | `b0`<br>--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | ---<br>KINT | SW | 0 | 0 | KIS | 0 | KI9 | KI8 | KI7 | KI6 | KI5 | KI4 | KI3 | KI2 | KI1 | KI0 |
| **0204H** | **KEYCTL**<br>b0: 0 = interrupción de teclado deshabilitada<br>b1: 1→0 = borrar interrupción de teclado |

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


