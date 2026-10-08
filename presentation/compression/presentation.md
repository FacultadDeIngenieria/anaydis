class: center, middle, inverse

# Compresión

---

# Introducción	

* La compresión de datos busca la identificación y extracción de la redundancia en la información.
* De forma de:
  * Ahorrar espacio de almacenamiento
  * Ahorrar tiempo de transmisión
 
---

# Tipos de Algoritmos

* Sin pérdida de precisión (Lossless)
  * Texto 
  * Ejecutables 
  * Objetos para edición

* Con pérdida de precisión (Lossy)
  * Imágenes 
  * Sonido 
  * Video

.center[![]({{site.baseurl}}/presentation/compression/data_compress_model.png)]

---

# Run-Length Encoding (RLE)

* Reducir repeticiones sucesivas de un símbolo. Ej:
  * AAAAAAABCCCCCCCCCDABBBBBBBBBBBBB -> 7AB9CDA13B

--

* Para caracteres:
  * Usar un prefijo (ej: 0xFF) (escape character)
  * AAAAAAABCCCCCCCCCDABBBBBBBBBBBBB 
  * 0xFF 0x07 'A' 'B' 0xFF 0x09 'C' 'D' 'A' 0xFF 0x0D 'B’

--

* En binario: alternar número de ceros, número de unos
  * 00000000000000000000110000000000000000 
  * 0x14 0x02 0x10

---

# Compresión de una imagen B&W

* Letra ‘q’ de 19 x 51 pixels
* Encoding natural: 
  * (19 x 51) + 6 = 975 bits
 
.center[![]({{site.baseurl}}/presentation/compression/q.png)]
 
???

B&W: en gral son imágenes con muchos ceros (sparse)
6 bits extra para guardar el length del row
  
---
 
# Compresión de una imagen B&W

* RLE: (63 x 6) + 6 = 384 bits (63 6-bit run lengths)

.center[![]({{site.baseurl}}/presentation/compression/q_bits.png)]

???

6 bits extra para guardar el length del row

---

# Breve Historia

* Ideas básicas:
  * Código Morse, Braille.

.center[![]({{site.baseurl}}/presentation/compression/morse_braille.png)]
 
---
 
# Breve Historia

* Teoría de la información: Shannon 1948. Entropy Encoders.
  * Huffman 1952
  * Compresión aritmética 1980

* Compresión por diccionarios
  * LZ77 (1977)
  * LZMW (1984)

* Métodos combinados. Transformadas
  * Ej. Transformada de Burrows-Wheeler 1995

???

Entropía: Paper "A Mathematical Theory of Communication". Mide la incertidumbre de una fuente de información: también se puede considerar como la cantidad de información promedio que contienen los símbolos usados.
 
---
 
# Teoría de la Información

* Rama de las matemáticas que busca cuantificar el concepto de información (Claude Shannon).

* Concepto de Entropía aplicado a la Información

* Una secuencia previsible contiene poca información. Ejemplos:
  * 11011011011011011011011011 (Que sigue?)
  * No gané la lotería esta semana.
  * Mañana no se acaba el mundo.

* Una secuencia imprevisible contiene mucha información. Ejemplo:
  * 01000001110110011010010000 (Que sigue?)
  * Acabo de ganar la lotería!
  * Mañana hay un tsunami en Buenos Aires.

---

# Teoría de la Información

* La cantidad de información de un mensaje es inversamente proporcional a la probabilidad de ocurrencia. 
* Para pasarlo a bits hacemos el log2
* Definición de Entropía de la información como:

.center[![]({{site.baseurl}}/presentation/compression/entropia.png)]

* Redundancia:

.center[![]({{site.baseurl}}/presentation/compression/redundancia.png)]

---

# Ejemplo

| P<sub>X</sub> | log<sub>2</sub> 1/P<sub>X</sub> | P<sub>X</sub> log<sub>2</sub> 1/P<sub>X</sub>  |
| ------------- |---------------| ------|
| 1/16 | 4 | 1/4 |
| 1/16 | 4 | 1/4 |
| 1/16 | 4 | 1/4 |
| 1/16 | 4 | 1/4 |
| 1/16 | 4 | 1/4 |
| 1/16 | 4 | 1/4 |
| 1/16 | 4 | 1/4 |
| 1/16 | 4 | 1/4 |
| 1/16 | 4 | 1/4 |
| 1/16 | 4 | 1/4 |
| 1/16 | 4 | 1/4 |
| 1/16 | 4 | 1/4 |
| 1/16 | 4 | 1/4 |
| 1/16 | 4 | 1/4 |
| 1/16 | 4 | 1/4 |
| 1/16 | 4 | 1/4 |
| 1.0  |   | 4.0 |

---

# Ejemplo

| P<sub>X</sub> | log<sub>2</sub> 1/P<sub>X</sub> | P<sub>X</sub> log<sub>2</sub> 1/P<sub>X</sub>  |
| ------------- |---------------| ------|
| 1/2 | 1 | 0.5 |
| 1/4 | 2 | 0.5 |
| 1/8 | 3 | 0.375 |
| 1/16 | 4 | 0.375 |
| 1/32 | 5 | 0.1563 |
| 1/64 | 6 | 0.938 |
| 1/128 | 7 | 0.0547 |
| 1/256 | 8 | 0.0313 |
| 1/512 | 9 | 0.0176 |
| 1/1024 | 10 | 0.0098 |
| 1/2048 | 11 | 0.0054 |
| 1/4096 | 12 | 0.0029 |
| 1/8192 | 13 | 0.0016 |
| 1/16384 | 14 | 0.0009 |
| 1/32768 | 15 | 0.0005 |
| 1/65536 | 16 | 0.0002 |
| 1.0 |  | 2.0 |

---
 
# Entropy Encoding

* Conclusión:
  * La entropía mide el número promedio de bits necesarios para codificar un alfabeto a través de un codificador óptimo.
  * Determina el límite máximo de compresión de un mensaje sin ninguna pérdida de información (demostrado analíticamente por Shannon):
    * el límite de compresión (en bits) es igual a la entropía multiplicada por el largo del mensaje.

* Entropy Encoding:
  * Codificación donde cada símbolo tiene una cantidad de bits basado en la probabilidad de ocurrencia

* Ejemplos:
  * Morse: E: \*, A: \* _, Z: _ _ \* \*
  * Huffman
  * Compresión Aritmética

???
Un codificador óptimo es aquel que utiliza el mínimo número de bits para codificar un mensaje.

---

# Ejemplo de código de Huffman

* A = 0
* B = 100
* C = 1010
* D = 1011
* R = 11
* ABRACADABRA = 01001101010010110100110
* 11 Letras en 23 bits 
  * (vs 88 bits para ascii y 33 para un código fijo de 3 bits)
* Ningún código es prefijo de otro

---

# Creando un código de Huffman

* Para cada símbolo a codificar (letras en el ejemplo), asociar una frecuencia (cantidad de apariciones)
  * La probabilidad sería Frecuencia / N.
* Crear un trie binario donde las hojas son los símbolos con su frecuencia
* Tomar los dos nodos con menor frecuencia y generar un nodo nuevo que apunte a esos dos y cuya frecuencia sea la suma
* Repetir el procedimiento hasta llegar a una única raíz.

---

# Ejemplo. Paso 1

* Suponiendo que las frecuencias relativas son:
  * A: 40
  * B: 20
  * C: 10
  * D: 10
  * R: 20
* Los más chicos son 10 y 10 (C and D), entonces los conectamos:

.center[![]({{site.baseurl}}/presentation/compression/huff_step1.png)]

---
 
# Paso 2

* Los valores más chicos son ahora de 20: B, R y C+D.
* Conectamos 2 cualquiera de estos

.center[![]({{site.baseurl}}/presentation/compression/huff_step2.png)]

---

# Paso 3

* R con 20 tiene el valor más bajo. Los demás A y B+C+D todos valen 40
* Conectamos R con cualquiera de estos
 
.center[![]({{site.baseurl}}/presentation/compression/huff_step3.png)]
 
---
# Paso 4

* Conectar los ultimos 2 nodos

.center[![]({{site.baseurl}}/presentation/compression/huff_step4.png)]

---

# Paso 5

* Asignar 0 a las bifurcaciones a izquierda, 1 a la derecha
* Cada código es el camino desde la raíz

.center[![]({{site.baseurl}}/presentation/compression/huff_step5.png)]

* A = 0,B = 100,C = 1010,D = 1011,R = 11
* Nunca hay prefijos !
* Porque en un trie binario no hay valores en los nodos intermedios
 
---

# Decodificación

* Voy recorriendo el árbol hasta llegar a una hoja
* Emito ese símbolo y comienzo de nuevo

.center[![]({{site.baseurl}}/presentation/compression/huff_decode_01.png)]

---
 
# Consideraciones

* No es práctico crear un código para Strings pequeños
  * Para decodificar precisamos la tabla de códigos
  * Si incluimos la tabla en el mensaje ocupamos más lugar que antes de comprimir.
* La compresión sólo se da si:
  * El String a comprimir es sustancialmente más largo que la tabla de códigos
  * Usamos una tabla conocida

---

# Compresión aritmética

* Alternativa a Huffman que permite representar símbolos con una cantidad **fraccionaria** de bits.
  * En Huffman cada símbolo ocupa un número **entero** de bits.
  * Con aritmética, un símbolo muy frecuente puede ocupar mucho menos de un bit.
* Representa **todo el mensaje** como un único número racional en el intervalo [0, 1).
* Empieza con [0, 1) y, por cada símbolo procesado, restringe el intervalo a uno más pequeño.
  * El tamaño del nuevo intervalo es proporcional a la probabilidad del símbolo.

--

* Huffman redondea a bits enteros **en cada símbolo**.
* La compresión aritmética redondea **una sola vez**, al final del mensaje.

---

# Compresión aritmética - Ejemplo

.center[![]({{site.baseurl}}/presentation/compression/arit_01.png)]

* El proceso consiste en ir achicando el intervalo en forma proporcional a la probabilidad del símbolo.
* La fórmula para calcular los nuevos intervalos es:
  * Dado el intervalo [ L, R ).
  * Si el símbolo a procesar tiene un intervalo de probabilidad [ x, y ).
  * El nuevo intervalo es [ L + x (R-L) , L + y (R-L) ).

---

# Compresión aritmética - ¿Cuántos bits?

* Cada símbolo multiplica el ancho del intervalo por su probabilidad:
  * Ancho final = 0.5 · 0.5 · 0.5 · 0.1 = 0.0125 (el ancho de [0.8125, 0.825))
* Para identificar un intervalo de ancho w hacen falta ≈ −log₂ w bits:
  * −log₂ 0.0125 = 1 + 1 + 1 + 3.32 = **6.32 bits**
  * Es la suma de la información (−log₂ p) de cada símbolo → la **entropía** del mensaje.
* Se emite un número dentro del intervalo con la menor cantidad de bits posible.
  * Garantía: alcanzan ⌈−log₂ w⌉ + 1 bits (en el ejemplo, 8).
  * La implementación que vemos más adelante emite `1101000` (7 bits).

???

0.1101₂ = 0.8125 está dentro del intervalo, así que con 4 bits "alcanzaría", pero es casualidad: 0.8125 es una fracción binaria exacta y el decoder estaría asumiendo que después vienen ceros. En un stream real lo que sigue son otros datos, por eso hace falta el +1 de la garantía.

Ojo: con un mensaje corto no tiene sentido comparar contra Huffman (Huffman da 00011 = 5 bits para BBBC, también por debajo de 6.32). La comparación justa es el promedio sobre mensajes largos (ver "Comparación con Huffman").

---

# Compresión aritmética. Decodificando

* El decoder necesita la misma tabla de probabilidades que el encoder.
* Tomo como dato un número dentro del último intervalo (ej: 0.8125).
* Verifico en qué intervalo de probabilidad de símbolo cae.
  * En el ejemplo anterior 0.8125 cae en el intervalo de 'B' [ 0.4 , 0.9 ) → emito B.
  * Escalo el número a la inversa: n = (n - x) / (y - x)
  * 0.8125 pasa a ser (0.8125 - 0.4) / (0.9 - 0.4) = 0.825
* Sigo hasta completar la longitud (o también se puede incluir un símbolo de EOF).

.center[![]({{site.baseurl}}/presentation/compression/arit_02.png)]

---

# Comparación con Huffman

Con las mismas probabilidades del ejemplo:

| Símbolo | p | Costo ideal (−log₂ p) | Código Huffman | Bits Huffman |
|:-:|:-:|:-:|:-:|:-:|
| B | 0.5 | 1 bit | `0` | 1 |
| A | 0.4 | 1.32 bits | `10` | 2 |
| C | 0.1 | 3.32 bits | `11` | 2 |

* Huffman tiene que redondear cada símbolo a un número entero de bits.

--

* Promedio de bits por símbolo:
  * Entropía: 0.5·1 + 0.4·1.32 + 0.1·3.32 = **1.361**
  * Huffman: 0.5·1 + 0.4·2 + 0.1·2 = **1.5** (≈ 10% más)
  * Aritmética: **1.363** (medido sobre 100.000 símbolos)

???

Huffman sólo alcanza la entropía cuando todas las probabilidades son potencias de ½. Por ejemplo, para ABRACADABRA la entropía es 22.4 bits y Huffman usa 23: ahí la aritmética casi no gana nada. La diferencia aparece cuando las probabilidades están lejos de potencias de ½ (siguiente slide).

---

# Caso extremo: p = 0.99

Ej: una imagen blanco y negro casi toda blanca.

| Símbolo | p | Costo ideal (−log₂ p) | Código Huffman |
|:-:|:-:|:-:|:-:|
| A (blanco) | 0.99 | 0.0145 bits | `0` → 1 bit |
| B (negro) | 0.01 | 6.64 bits | `1` → 1 bit |

* Huffman **nunca** puede usar menos de 1 bit por símbolo.
* Entropía: 0.99·0.0145 + 0.01·6.64 = **0.081 bits por símbolo**

--

* Mensaje de 100 símbolos (99 A y 1 B):
  * Huffman: **100 bits**
  * Aritmética: −log₂(0.99⁹⁹ · 0.01) ≈ 8.1 → **9 bits** (medido)

---

# Caso extremo: intuición

* Cada A deja el 99% del intervalo → casi no lo achica.
  * Hacen falta ≈ 69 A seguidas para partir el intervalo a la mitad (= 1 bit).
* Una B lo deja en el 1% → es una "sorpresa" y cuesta ≈ 6.6 bits.
* Lo esperado es casi gratis y lo inesperado es caro: eso es lo que mide la entropía.

---

# Compresión aritmética. Implementación

* Los números racionales son representados por enteros con la coma a la izquierda.
* Ejemplos con enteros de 8 bits:

.center[![]({{site.baseurl}}/presentation/compression/arit_03.png)]

* El primer intervalo es L = 00000000, H = 11111111 (H es inclusivo: 0.111...₂ = 1).
* Ejemplo: [0.0, 0.125) es L = 00000000, H = 00011111.
  * Los 3 primeros bits son iguales y **ya no pueden cambiar** → los grabo: `000`.
  * Los 'shifteo': entran 0s por derecha en L y 1s en H → L = 00000000, H = 11111111.
* Así nunca hace falta precisión infinita: los bits se emiten a medida que se codifica.

???

- ¿Por qué no usar double? Tiene 53 bits de mantisa, y el ancho del intervalo de un mensaje largo puede ser 2^-10000 o menos, imposible de representar con punto flotante.
- Idea clave: si L y H empiezan con el mismo bit, **todos** los números del intervalo empiezan con ese bit. Se puede emitir y duplicar el intervalo (zoom x2), que es lo mismo que hace el decoder al revés.
- H es inclusivo: representa 0.HHHH1111... (infinitos unos), por eso al shiftear entran 1s por derecha en H y 0s en L.
- En la práctica se usan 32 bits en vez de 8 y las probabilidades se expresan como frecuencias enteras (ej: A, B, C = 4, 5, 1 sobre un total de 10). El redondeo de la división entera pierde una cantidad despreciable de compresión.
- Pregunta para la clase: ¿cuándo se emite un bit? Cuando el intervalo cae entero en la mitad inferior [0, ½) o en la superior [½, 1).

---

# Implementación. Underflow

* Problema: el intervalo se achica alrededor de ½ sin que coincida el primer bit.
  * Ej: L = 0.0111111₂, H = 0.1000001₂ → no puedo emitir nada y me quedo sin precisión.

???

- Antes de mostrar la solución, preguntar: ¿qué pasa si el intervalo queda en [0.4999, 0.5001)? L empieza con 0 y H con 1, así que no se emite nada, pero el intervalo se sigue achicando.
- Con enteros, H − L puede quedar más chico que el total de frecuencias: algunos símbolos quedarían con un intervalo vacío y el encoder ya no puede codificarlos.

--

* Solución: si L ≥ ¼ y H < ¾ (L = 01..., H = 10...):
  * Expando alrededor del centro: L = 2 (L − ¼), H = 2 (H − ¼).
  * Incremento un contador `pending`: todavía no sé si el próximo bit es 0 o 1.
* Cuando finalmente se decide un bit b, emito b seguido de `pending` veces el bit opuesto.
  * Si el intervalo estaba en [¼, ½) → `0111...`
  * Si el intervalo estaba en [½, ¾) → `1000...`

???

- Expandir alrededor del centro es restar ¼ y duplicar: lleva [¼, ¾) a [0, 1). No se emite nada porque todavía no se sabe de qué lado de ½ va a quedar.
- Por qué funciona: si L = 01... y H = 10..., el número final empieza con 01 (si cae debajo de ½) o con 10 (si cae arriba). Con k expansiones pendientes, el resultado es 0 seguido de k unos, o 1 seguido de k ceros.
- Las tres operaciones (emitir 0, emitir 1, expandir el centro) son las del paper clásico: Witten, Neal & Cleary, "Arithmetic Coding for Data Compression", Communications of the ACM, 1987.

---

# Huffman vs Aritmética

| | Huffman | Aritmética |
|---|---|---|
| Bits por símbolo | Entero (≥ 1) | Fraccionario |
| Exceso sobre la entropía | < 1 bit por símbolo | ≈ 2 bits por mensaje |
| Velocidad | Más rápido (tabla) | Más lento (mult. y div.) |
| Uso | zip, gzip, PNG, JPEG | 7-zip, xz, JPEG 2000, H.264 |

???

- Huffman alcanza exactamente la entropía sólo si todas las probabilidades son potencias de ½.
- La compresión aritmética estuvo cubierta por patentes de IBM durante años, por eso JPEG con codificación aritmética casi no se usó y Huffman dominó.
- Variante moderna: ANS (Asymmetric Numeral Systems), usada en zstd. Comprime como aritmética con velocidad cercana a Huffman.
- En H.264 / H.265 el coder aritmético es CABAC.

---

# Transformadas 

* En matemática hay problemas complejos que se simplifican luego de aplicar una transformación. Ej: Transformada de Laplace
* En compresión la idea de usar transformadas es lograr una salida con menos Entropía de forma de poder aplicar un Entropy Encoder con mayor eficiencia
* Transformada de Burrows-Wheeler
* Move-To-Front

???

- Otro ejemplo conocido: JPEG aplica la DCT (transformada discreta del coseno) antes de cuantizar y comprimir.
- Ojo: BWT sola **no** cambia la entropía de orden 0, porque es una permutación y las frecuencias de cada símbolo quedan iguales. Lo que hace es agrupar símbolos parecidos; MTF convierte esa agrupación en números chicos y ahí baja la entropía.
- Medido sobre el .md de esta presentación (≈ 24.000 caracteres), en bits por caracter: texto 5.1, BWT 5.1 (igual), MTF sola 5.3 (peor), BWT + MTF **3.0**, con más de la mitad de ceros.

---

# BWT: Transformada de Burrows-Wheeler

* El algoritmo fue publicado por Michael Burrows y David Wheeler en un reporte de investigación del año 1994.
* El algoritmo de BWT toma un bloque de datos y lo transforma usando un algoritmo de ordenamiento.
* La salida contiene exactamente los mismos datos de la entrada pero en un orden diferente.
* La transformación es reversible, con lo cual los datos originales pueden recuperarse sin pérdida de precisión.

???

- Paper original (SRC Research Report 124, 1994): https://web.archive.org/web/20060427023016/http://www.hpl.hp.com/techreports/Compaq-DEC/SRC-RR-124.pdf
- Artículo de Mark Nelson (Dr. Dobb's, 1996), de donde salen los ejemplos: https://marknelson.us/posts/1996/09/01/bwt.html
- Uso real: bzip2.

---

# BWT - Ejemplo

* Vemos acá un String de 7 elementos
* En la realidad se trabaja con bloques de cientos de Kb.

.center[![]({{site.baseurl}}/presentation/compression/bwt_01.png)]

???

- bzip2 usa bloques de 100 a 900 KB (opciones -1 a -9). Un bloque más grande tiene más contextos repetidos y comprime mejor, pero usa más memoria y es más lento de ordenar.

---

# Primer paso – Generar Rotaciones

* Considerar todas las rotaciones del String
  * No es necesario hacer N copias (alcanza con tener punteros a las posiciones).

.center[![]({{site.baseurl}}/presentation/compression/bwt_02.png)] 

???

- Cada rotación se representa con un único int k: la rotación Sk empieza en la posición k y da la vuelta. El caracter i de Sk es s.charAt((k + i) % n).
- Memoria: n enteros en lugar de n² caracteres.

---

# Segundo paso – Ordenar

* Ordenar el set de Strings rotados.
  * Se precisa un Comparator especial para trabajar sobre el mismo String basado en el pointer.

.center[![]({{site.baseurl}}/presentation/compression/bwt_03.png)] 

???

- El Comparator compara las rotaciones a y b caracter por caracter: s.charAt((a + i) % n) vs s.charAt((b + i) % n).
- Peor caso: un input muy repetitivo ("aaaa...") hace que cada comparación recorra todo el bloque, O(n) por comparación, O(n² log n) en total. Por eso se aplica RLE antes (ver "Combinando Todo").
- Alternativas: radix sort / quicksort de 3 vías para strings, o algoritmos de suffix array en O(n).
 
---

# Segundo paso – Ordenar

.center[![]({{site.baseurl}}/presentation/compression/bwt_03.png)] 

* El String original pasó a estar en la cuarta fila.
* La columna F contiene al String original ordenado. Esto es "BBDDORS".
* En la columna L cada caracter en L es el caracter que está ANTES en el String que el caracter en F.

???

- Pregunta para la clase: ¿alcanza con guardar F? No: F es sólo el String ordenado y se obtiene de cualquier permutación. Toda la información está en L.
- L[i] precede a F[i] en el String (circularmente): por ejemplo, en la fila 0 la 'O' está antes de "BBS...".

---

# Salida del Algoritmo

* Aunque parezca extraño la salida de la transformación es:
  * La columna L.
  * Un entero que nos dice en qué fila el caracter de L es el primero del String original.

* En este caso la salida es "OBRSDDB" y 5.

???

- El String original está en la fila 3, pero se guarda 5: es la fila de S1 = "RDOBBSD", cuyo último caracter (en L) es S[0] = 'D'. El decoder arranca leyendo L[5].
- Es una convención: otras implementaciones guardan la fila del String original (3) y decodifican de atrás para adelante.

---

# Transformada Inversa

* Vector de Transformación.
  * Este Vector es un arreglo con un índice para cada fila en la columna F
  * Si la fila i contiene la rotación Sk, T[i] es la fila que contiene Sk+1.
  * El vector de transformación nos permite ir de Sk a Sk+1.
* Por ejemplo en la figura anterior la fila 3 contiene S0 y la fila 5 contiene S1 por lo tanto T3 = 5.
* Para este caso el vector es { 1, 6, 4, 5, 0, 2, 3 }.

.center[![]({{site.baseurl}}/presentation/compression/bwt_04.png)]

???

- Recorrido desde el índice 5: 5 → 2 → 4 → 0 → 1 → 6 → 3. Leyendo L en esas filas: D R D O B B S.

---

# Cálculo de T

* Teniendo L y F puedo calcular T
* El caracter 'O' en la fila 0 se mueve a la fila 4 de F con lo cual T[ 4 ] = 0 .
* Y la fila 1?
  * La 'B' podría corresponder a la fila 0 o a la 1.
* Como F está ordenada:
  * La 'B' de la fila 1 de L se mueve a la fila 0 de F.
  * La 'B' en la fila 6 de L se mueve a la fila 1 de F.
* Para calcular F solo preciso ordenar L

.center[![]({{site.baseurl}}/presentation/compression/bwt_05.png)]

???

- Propiedad clave: la k-ésima 'B' de L es la k-ésima 'B' de F. Las filas que empiezan con 'B' están ordenadas por lo que sigue a la 'B', que es el mismo orden en que esas 'B' aparecen en L.
- Es decir, T sale de un **sort estable** de L: si el sort no fuera estable, las dos 'B' se podrían cruzar y T quedaría mal. Conecta con la slide de estabilidad de los sorters elementales.
- Como el alfabeto es chico (256 bytes), se puede hacer con counting sort en O(n).

---

# Decode

```java
int[] T = { 1, 6, 4, 5, 0, 2, 3 };
char[] L = "OBRSDDB".toCharArray();
int primaryIndex = 5;

String decode() {
    StringBuilder out = new StringBuilder();
    int index = primaryIndex;
    for (int i = 0; i < L.length; i++) {
        out.append(L[index]);
        index = T[index];
    }
    return out.toString();
}
```

???

- La decodificación es O(n): un paso por caracter. La parte cara de BWT es el sort del encoder.

---

# ¿Y por qué sirve?

* Acá vemos un conjunto de Strings que todos comienzan con los caracteres 'hat'
* La mayoría de ellos está prefijado por la letra t
* Esto implica una repetición -> oportunidad de compresión.
* L en este caso es: "tttWtwtttttt" -> Muchas repeticiones locales.
  * t:   hat acts like this:<13><10><1 
  * t:   hat buffer to the constructor 
  * t:   hat corrupted the heap, or wo 
  * W: hat goes up must come down<13 
  * t:   hat happens, it isn't  likely 
  * w: hat if you want to dynamicall 
  * t:   hat indicates an error.<13><1 
  * t:   hat it removes arguments from 
  * t:   hat looks like this:<13><10>< 
  * t:   hat looks something like this 
  * t:   hat looks something like this 
  * t:   hat once I detect the mangled 

???

- Ejemplo del artículo de Mark Nelson: rotaciones de un texto en inglés que empiezan con "hat". Casi siempre las precede una 't' ("that"); la 'w' y la 'W' vienen de "what" y "What".
- Al ordenar, los contextos parecidos quedan juntos, y entonces los caracteres que los preceden también. L tiene las mismas frecuencias que la entrada, pero con los caracteres iguales agrupados.

---

# Move to Front - Encode

* Se empieza por un diccionario con todos los caracteres: “abcde…”
* Cada letra que aparece se emite con el índice actual y se modifica el diccionario poniendo adelante esa letra.

.center[![]({{site.baseurl}}/presentation/compression/mtf_01.png)]

???

- Un caracter repetido da 0 y un caracter usado hace poco da un número chico. Después de BWT la salida tiene muchos 0 y 1, que es justo lo que un Entropy Encoder comprime bien.
- En la práctica el diccionario son los 256 valores de un byte.
- Pregunta para la clase: ¿qué emite MTF para "aaaaaa"? → el índice de 'a' y después todos 0.

---

# Move to Front - Decode

* Vuelvo a comenzar con un diccionario con todos los caracteres: “abcde…”
* Por cada índice que voy procesando, emito la letra y se modifica el diccionario poniendo adelante esa letra.

.center[![]({{site.baseurl}}/presentation/compression/mtf_02.png)]

???

- Encoder y decoder hacen exactamente las mismas actualizaciones del diccionario, así que no hace falta transmitirlo. Es la misma idea que un modelo adaptativo.

---

# Combinando Todo

* Podemos aplicar en cadena los siguientes algoritmos.
* RLE | BWT | MTF | HUF
* RLE : Elimino los caracteres con muchas repeticiones para no degradar BWT
* BWT : Aplico la transformada.
* MTF : Como tengo muchas repeticiones localizadas logro tener un conjunto de números bajos.
* HUF : Huffman u otro Entropy Encoder.

???

- Así funciona bzip2: RLE → BWT → MTF → RLE de los ceros → Huffman.
- El primer bzip (1996) usaba compresión aritmética. bzip2 la reemplazó por Huffman por las patentes (ver la sección de compresión aritmética).
- Medido sobre el .md de esta presentación: 5.1 bits por caracter en la entrada, 3.0 después de BWT + MTF.
