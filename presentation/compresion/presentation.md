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

---

# Run-Length Encoding (RLE)

* Reducir repeticiones sucesivas de un símbolo. Ej:
  * AAAAAAABCCCCCCCCCDABBBBBBBBBBBBB -> 7AB9CDA13B

* Para caracteres:
  * Usar un prefijo (ej: 0xFF) (escape character)
  * AAAAAAABCCCCCCCCCDABBBBBBBBBBBBB 
  * 0xFF 0x07 'A' 'B' 0xFF 0x09 'C' 'D' 0xFF 0x0D 'B’

* En binario: alternar número de ceros, número de unos
  * 00000000000000000000110000000000000000 
  * 0x14 0x02 0x10

---

# Compresión de una imagen B&W

* Letra ‘q’ de 19 x 51 pixels
* Encoding natural: 
  * (19 x 51) + 6 = 975 bits
 
.center[![]({{site.baseurl}}/presentation/compresion/q.png)]
 
???

B&W: en gral son imágenes con muchos ceros (sparse)
  
---
 
# Compresión de una imagen B&W

* RLE: (63 x 6) + 6 = 384 bits (63 6-bit run lengths)

.center[![]({{site.baseurl}}/presentation/compresion/q_bits.png)]

---

# Breve Historia

* Ideas básicas:
  * Código Morse, Braille.

.center[![]({{site.baseurl}}/presentation/compresion/morse_braille.png)]
 
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

.center[![]({{site.baseurl}}/presentation/compresion/entropia.png)]

* Redundancia:

.center[![]({{site.baseurl}}/presentation/compresion/redundancia.png)]

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
  * La entropía mide el número promedio de bits necesarios para codificar un alfabeto.

* Entropy Encoding:
  * Codificación donde cada símbolo tiene una cantidad de bits basado en la probabilidad de ocurrencia

* Ejemplos:
  * Morse: E: \*, A: \* _, Z: _ _ \* \*
  * Huffman
  * Compresión Aritmética

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

.center[![]({{site.baseurl}}/presentation/compresion/huff_step1.png)]

---
 
# Paso 2

* Los valores más chicos son ahora de 20: B, R y C+D.
* Conectamos 2 cualquiera de estos

.center[![]({{site.baseurl}}/presentation/compresion/huff_step2.png)]

---

# Paso 3

* R con 20 tiene el valor más bajo. Los demás A y B+C+D todos valen 40
* Conectamos R con cualquiera de estos
 
.center[![]({{site.baseurl}}/presentation/compresion/huff_step3.png)]
 
---
# Paso 4

* Conectar los ultimos 2 nodos

.center[![]({{site.baseurl}}/presentation/compresion/huff_step4.png)]

---

# Paso 5

* Asignar 0 a las bifurcaciones a izquierda, 1 a la derecha
* Cada código es el camino desde la raíz

.center[![]({{site.baseurl}}/presentation/compresion/huff_step5.png)]

* A = 0,B = 100,C = 1010,D = 1011,R = 11
* Nunca hay prefijos !
* Porque en un trie binario no hay valores en los nodos intermedios
 
---

# Decodificación

* Voy recorriendo el árbol hasta llegar a una hoja
* Emito ese símbolo y comienzo de nuevo

.center[![]({{site.baseurl}}/presentation/compresion/huff_decode_01.png)]

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

* Alternativa a Huffman que permite representar caracteres con longitudes fraccionales de bits.
* Esto hace, por ejemplo, que caracteres con mucha frecuencia puedan ocupar menos de un bit.
* Funciona representando la entrada como un número racional en el intervalo [0, 1) .
* A medida que se va procesando la entrada va restringiendo el intervalo a uno más pequeño.
* Basándose en la probabilidad del símbolo procesado.
 
---

# Compresión aritmética - Ejemplo

.center[![]({{site.baseurl}}/presentation/compresion/arit_01.png)]

* El proceso consiste en ir achicando el rango en forma proporcional a la probabilidad del símbolo.
* La fórmula para calcular los nuevos intervalos es :
  * Dado el intervalo [ L, R) .
  * Si el símbolo a procesar tiene un intervalo de probabilidad [ x, y ) .
  * El nuevo intervalo es [ L + x (R-L) , L + y (R-L) ) .
 
---

# Compresión aritmética. Decodificando

* Tengo como dato el valor inferior del último intervalo.
* Verifico en qué intervalo de probabilidad de caracter cae.
  * En el ejemplo anterior 0.8125 cae en el intervalo de 'B' [ 0.4 , 0.9 )
  * Escalo el número a la inversa.
  * n = (n - x) / (y -x)
  * 0.8125 pasa a ser (0.8125 - 0.4) / (0.9 - 0.4) = 0.825
* Sigo hasta completar la longitud (O también se puede incluir un caracter de EOF)

.center[![]({{site.baseurl}}/presentation/compresion/arit_02.png)]

---

# Compresión aritmética. Implementación

* Los números racionales son representados por enteros con la coma a la izquierda
* Ejemplos con enteros de 8 bits
 
.center[![]({{site.baseurl}}/presentation/compresion/arit_03.png)]
 
* El primer intervalo es entonces 00000000...000, 11111111...111
* A medida que se achican los intervalos los primeros bits van a ser iguales.
* Ejemplo: [0.0, 0.125) es 00000000 00100000
* Puedo entonces grabar los 'n' primeros bits iguales, y 'shiftear' el resto.
