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

* Alternativa a Huffman que permite representar caracteres con longitudes fraccionales de bits.
* Esto hace, por ejemplo, que caracteres con mucha frecuencia puedan ocupar menos de un bit.
* Funciona representando la entrada como un número racional en el intervalo [0, 1) .
* A medida que se va procesando la entrada va restringiendo el intervalo a uno más pequeño.
* Basándose en la probabilidad del símbolo procesado.
 
---

# Compresión aritmética - Ejemplo

.center[![]({{site.baseurl}}/presentation/compression/arit_01.png)]

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

.center[![]({{site.baseurl}}/presentation/compression/arit_02.png)]

---

# Compresión aritmética. Implementación

* Los números racionales son representados por enteros con la coma a la izquierda
* Ejemplos con enteros de 8 bits
 
.center[![]({{site.baseurl}}/presentation/compression/arit_03.png)]
 
* El primer intervalo es entonces 00000000...000, 11111111...111
* A medida que se achican los intervalos los primeros bits van a ser iguales.
* Ejemplo: [0.0, 0.125) es 00000000 00100000
* Puedo entonces grabar los 'n' primeros bits iguales, y 'shiftear' el resto.

---

# Transformadas 

* En matemática hay problemas complejos que se simplifican luego de aplicar una transformación. Ej: Transformada de Laplace
* En compresión la idea de usar transformadas es lograr una salida con menos Entropía de forma de poder aplicar un Entropy Encoder con mayor eficiencia
* Transformada de Burrows-Wheeler
* Move-To-Front

---

# BWT: Transformada de Burrows-Wheeler

* El algoritmo fue publicado por Michael Burrows y David Wheeler en un reporte de investigación del año 1994.
* El algoritmo de BWT toma un bloque de datos y lo transforma usando un algoritmo de ordenamiento.
* La salida contiene exactamente los mismos datos de la entrada pero en un orden diferente.
* La transformación es reversible, con lo cual los datos originales pueden recuperarse sin pérdida de precisión.

???

https://www.drdobbs.com/database/data-compression-with-the-burrows-wheele/184409957
https://marknelson.us/posts/1996/09/01/bwt.html

---

# BTW - Ejemplo

* Vemos acá un String de 7 elementos
* En la realidad se trabaja con bloques de cientos de Kb.

.center[![]({{site.baseurl}}/presentation/compression/bwt_01.png)]

---

# Primer paso – Generar Rotaciones

* Considerar todas las rotaciones del String
  * No es necesario hacer N copias (alcanza con tener punteros a las posiciones).

.center[![]({{site.baseurl}}/presentation/compression/bwt_02.png)] 

---

# Segundo paso – Ordenar

* Ordenar el set de Strings rotados.
  * Se precisa un Comparator especial para trabajar sobre el mismo String basado en el pointer.

.center[![]({{site.baseurl}}/presentation/compression/bwt_03.png)] 
 
---

# Segundo paso – Ordenar

.center[![]({{site.baseurl}}/presentation/compression/bwt_03.png)] 

* El String original paso a estar en la cuarta fila.
* La columna F contiene al String original ordenado. Esto es "BBDDORS".
* En la columna L cada caracter en L es el caracter que esta ANTES en el String que el caracter en F.

---

# Salida del Algoritmo

* Aunque parezca extraño la salida de la transformación es:
  * La columna L.
  * Un entero que nos dice en que fila el caracter de L es el primero del String original.

* En este caso la salida es "OBRSDDB" y 5.

---

# Transformada Inversa

* Vector de Transformación.
  * Este Vector es un arreglo con un índice para cada fila en la columna F
  * Para una fila i T[i] es la fila donde encuentro S[i + 1]
  * El vector de transformación nos permite ir de S[ i ] a S[ i + 1]
* Por ejemplo en la figura anterior la fila 3 contiene S0 y la fila 5 contine S1 por lo tanto T3 = 5.
* Para este caso el vector es { 1, 6, 4, 5, 0, 2, 3 }.

.center[![]({{site.baseurl}}/presentation/compression/bwt_04.png)]

---

# Cálculo de T

* Teniendo L y F puedo calcular T
* El caracter 'O' en la fila 0 se mueve a la fila 4 de F con lo cual T[ 4 ] = 0 .
* Y la fila 1?
  * La 'B' podría corresponder a la fila 0 o a la 1.
* Como F esta Ordenada:
  * La 'B' de la fila 1 de L se mueve a la fila 0 de F.
  * La 'B" en la fila 6 de L se mueve a la fila 1 de F.
* Para calcular F solo preciso ordenar L

.center[![]({{site.baseurl}}/presentation/compression/bwt_05.png)]

---

# Decode

```java
int T[] = { 1, 6, 4, 5, 0, 2, 3 };
char L[] = "OBRSDDB";
int primary_index = 5;

String decode()
{
    String cout= "";
    int index = primary_index;
    for ( int i = 0 ; i < 7 ; i++ ) {
        cout += L[ index ];
        index = T[ index ];
    } 
    return cout;
}
```

---

# ¿Y por qué sirve ?

* Acá vemos un conjunto de Strings que todos comienzan con los caracteres 'hat'
* La mayoría de ellos está prefijado por la letra t
* Esto implica una repetición -> oportunidad de compresión.
* L en este caso es : "tttWtwttt" -> Baja entropía.
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

---

# Move to Front - Encode

* Se empieza por un diccionario con todos los caracteres: “abcde…”
* Cada letra que aparece se emite con el índice actual y se modifica el diccionario poniendo adelante esa letra.

.center[![]({{site.baseurl}}/presentation/compression/mtf_01.png)]

---

# Move to Front - Decode

* Vuelvo a comenzar con un diccionario con todos los caracteres: “abcde…”
* Por cada índice que voy procesando, emito la letra y se modifica el diccionario poniendo adelante esa letra.

.center[![]({{site.baseurl}}/presentation/compression/mtf_02.png)]

---

# Combinando Todo

* Podemos aplicar en cadena los siguientes algoritmos.
* RLE | BWT | MTF | HUF
* RLE : Elimino los caracteres con muchas repeticiones para no degradar BWT
* BWT : Aplico la transformada.
* MTF : Como tengo muchas repeticiones localizadas logro tener un conjunto de números bajos.
* HUF : Huffman u otro Entropy Encoder.

---

# Compresión por diccionarios

* Se intenta codificar secuencias de caracteres repetidos con un código.
* En general se usa el texto ya procesado para construir un diccionario.
  * Lempel-Ziv 1977
  * LZW (Lempel-Ziv-Welch)

---

# LZ77

* Se toma una ventana de texto y se divide en dos partes.
* La primera parte se usa como diccionario y la segunda se encodea.
* Se busca el texto a encodear en la primera parte.
* Como salida se obtiene la posición y la longitud del match.
* Se avanza la ventana y se comienza de vuelta.

---

# LZW (Lempel-Ziv-Welch)

* En lugar de una ventana se mantiene una tabla de secuencias con índice asociado.
* Se busca en esa tabla tratando de encontrar la secuencia más larga.
* Si se encuentra un match M se emite el índice seguido del siguiente símbolo s.
* Se agrega la secuencia Ms al diccionario

---

# Implementación

* Se utiliza un trie para crear la tabla de secuencias.
  * De esa forma los prefijos ya quedan almacenados
* Codificación:
  * Buscar la entrada en el trie.
  * Se emite el índice en el último match.
  * Se agrega un nodo al trie.
* ¿Qué hacer cuando se 'llena' el trie ?
  * Descartarlo y empezar de nuevo: gif
  * Descartar las entradas que se usaron poco: gzip
