class: center, middle, inverse

# Algoritmos Elementales de Sorting

???

[PREGUNTA: QUÉ ES UN ALGORITMO?]

---

# ¿Por qué estudiarlos?

* Son fáciles de codificar

* Son rápidos para archivos pequeños

* Permiten establecer una base o metodología para analizar otros algoritmos

* Son los más rápidos en algunas situaciones especiales

* Pueden extenderse para formar parte de los algoritmos más sofisticados y de propósito general

---

# ¿Por qué no son aptos para cualquier escenario?

* En general son de orden O(N2) para sets de tamaño N de elementos ordenados al azar

* No son aptos para ordenar grandes sets de datos, pues el tiempo de ejecución es inaceptable incluso en equipos de gran capacidad

---

# Reglas de análisis

* Consideramos como objeto de ordenamiento tablas de elementos que poseen clave, y ordenaremos por clave.

* Si la tabla de datos cabe en memoria, los algoritmos se llaman internos. Se puede acceder a los elementos en forma random (aleatoria).

* Si la tabla de datos es un archivo de registros en disco (no cabe en memoria), los algoritmos se llaman externos. Los registros deberían ser accedidos en forma secuencial.

* Analizaremos tiempo de ejecución:
  * Proporcional a la cantidad de comparaciones y de intercambios que se hacen sobre la tabla

???

[PREGUNTA: por qué en el caso de acceder a disco se considera una buena práctica acceder en forma secuencial en vez de aleatoria?]

---

# Reglas de análisis (2)

* Archivo de registros con clave

.center.full-width[![]({{site.baseurl}}/presentation/elemental_sorters/registros_claves.jpg)]

---

# Reglas de análisis (3)

Factores a analizar

* Tiempo de ejecución (cantidad de operaciones) – el factor más importante
* Cantidad de memoria extra utilizada

* Categorías
  * In-place sort: no utilizan memoria extra
  * Indirectos: utilizan una representación con linked-list (o algún tipo de índice) y requieren memoria extra para N referencias
  * Métodos que duplican el file: necesitan crear una copia en memoria del file para poder ordenarlo.

---

# Propiedades

“Adaptivity”:

* Un algoritmo es adaptativo o adaptive cuando la secuencia de operaciones que realiza depende del orden de los elementos a ordenar

* Un algoritmo es no-adaptativo o non-adaptive si la secuencia de pasos no depende del orden (no varía los pasos de ejecución según el resultado de las comparaciones)

---

# Propiedades (2)

* Sorting directo vs indirecto

* Indirecto:
  * Cuando los elementos son grandes es preferible no moverlos, sólo se mueven sus referencias
  * Al finalizar el sorting, a veces es conveniente reordenar los elementos, a veces sólo es necesario dejar ordenadas las referencias

---

# Cualquier tipo

* Objetivo: 
  * Los algoritmos deben poder ordenar cualquier tipo de dato

* ¿Cómo puede el algoritmo saber cómo ordenar cualquier tipo dado?
  * 2 opciones en Java:
  * Los tipos a ordenar implementan Comparable<T>
  * Pasar como argumento un Comparator<T>

???

[PREGUNTA: ¿Cómo puede el algoritmo saber cómo ordenar cualquier tipo dado?]

---

# Implementar Comparable<T>

* Implementar compareTo()

* Debe ser consistente
  * Transitividad: si (a < b) y (b < c) ➔ (a < c)
  * Tricotomía: (a < b) ó (a > b) ó (a = b)

* Tipos comparables
  * Built-in: String, Double, Integer, Date, File, …
  * User-defined: Implementando Comparable

* Define el orden natural del tipo

---

# Abstracción (base class)

* Clase abstracta con operaciones elementales

```java
abstract class AbstractSort {
  boolean less(T v, T w, Comparator<T> comp) {
    return comp.compare(v,w) < 0;
  }
  void exch(T[] a, int i, int j) {
    T t = a[i];
    a[i] = a[j];
    a[j] = t;
  }
  void compExch(T[] a, int i, int j, Comparator<T> comp) {
    if (less(a[j], a[i], comp))
      exch (a, i, j);
  }
  abstract void sort(T[] a, int l, int r, Comparator<T> comp);
}
```

* Recordar! El primer paso de análisis es definir cuáles son las operaciones significativas del algoritmo.

---

# Selection Sort

* Secuencialmente busca el menor elemento y lo intercambia con la última posición no ordenada

* [Externo](http://www.youtube.com/watch?v=TW3_7cD9L1A&feature=related)

* [In-place](http://www.youtube.com/watch?v=TbwkDXtHgOU&feature=related)

* [Sample](http://www.cs.oswego.edu/~mohammad/classes/csc241/samples/sort/Sort2-E.html)

???

Algorithm: ↑ scans from left to right.

Invariants.
Elements to the left of ↑ (including ↑) fixed and in ascending order.
No element to right of ↑ is smaller than any element to its left.

---

# Selection Sort

* In place sorting

.center.full-width[![]({{site.baseurl}}/presentation/elemental_sorters/selection_sort.jpg)]

???

Algorithm: ↑ scans from left to right.

Invariants.
Elements to the left of ↑ (including ↑) fixed and in ascending order.
No element to right of ↑ is smaller than any element to its left.

---

# Implementación

```java
public class Selection extends AbastractSort {
    void sort(Comparable[] a) {
        int N = a.length;
        for (int i = 0; i < N; i++) {
            int min = i;
            for (int j = i+1; j < N; j++) {
                if (less(a[j], a[min])) {
                    min = j;
                }
            }
            exch(a, i, min);
        }
    }
}
```

---

# Análisis

* comparaciones: N-1 +N-2+...+2+1 = N(N-1)/2 =~N2/2, intercambios: N-1
.center.full-width[![]({{site.baseurl}}/presentation/elemental_sorters/selection_sort_analisis.jpg)]

???

Examination of the code reveals that, for each i from 1 to N - 1, there is one exchange and N - i comparisons, so there is a total of N - 1 exchanges and (N - 1) + (N - 2) + ··· + 2 + 1 = N(N - 1)/2 comparisons. 

---

# Análisis (2)

* Non-adaptive: El tiempo de ejecución no depende de los datos ya que siempre recorre todos los elementos para buscar el menor.

* Cuadrático en el # de comparaciones, lineal en cantidad de intercambios → Es bueno cuando necesitamos hacer pocos intercambios

* Es insensible a la entrada, siempre hace la misma cantidad de comparaciones, para claves iguales, para datos ordenados, etc.

* En la práctica, hay escenarios donde las comparaciones y los intercambios no tienen el mismo costo

---

# Insertion Sort

Ordena como uno ordena intuitivamente una mano de cartas.  Va tomando cada elemento y lo deja ordenado con respecto a los anteriores.

* [Animación](http://www.youtube.com/watch?v=ejpFmtYM8Cw&feature=related)
* [Sample](http://www.cs.oswego.edu/~mohammad/classes/csc241/samples/sort/Sort2-E.html)

???

Algorithm. ↑ scans from left to right.

Invariants.
Elements to the left of ↑ (including ↑) are in ascending order.
Elements to the right of ↑ have not yet been seen.

[PREGUNTA: Compara con todos hasta j = 0?]

---

# Insertion Sort

.center.full-width[![]({{site.baseurl}}/presentation/elemental_sorters/insertion_sort.jpg)]

???

Algorithm. ↑ scans from left to right.

Invariants.
Elements to the left of ↑ (including ↑) are in ascending order.
Elements to the right of ↑ have not yet been seen.

[PREGUNTA: Compara con todos hasta j = 0?]

---

# Implementación

```java
class Insertion extends AbstractSort {
    void sort(Comparable[] a) {
        int N = a.length;
        for (int i = 0; i < N; i++) {   
            for (int j = i; j > 0; j--) {   
                if (less(a[j], a[j-1])) { 
                    exch(a, j, j-1);
                } else {
                    break;
                }
            }
        }
    }
}
```

---

# Optimización

```java
void sort(Comparable[] a) {
    for (int i = 1; i < N; i++) {
        int j = i;
        Comparable v = a[i];
        while(j > 0 && less(v,a[j-1])) {
            a[j]= a[j-1];
            j--;
        }
        a[j]= v;
    }
}
```

* En vez de hacer swaps -> moves!
* Costo(2 moves) = Costo(1 swap)

---

# Análisis

* Caso Promedio (sin claves duplicadas):
  * comparaciones: (N-1 +N-2+...+2+1)/2 =~N2/4
  * intercambios: N2/4 (movimientos)

.center.full-width[![]({{site.baseurl}}/presentation/elemental_sorters/insertion_sort_analisis.jpg)]

???

Movimientos != intercambios 
1 movimiento = ½ intercambio

Proposition B. For randomly-ordered data with distinct keys, insertion sort
uses ~ (N^2)/4 compares and (N^2)/4 exchanges on the average.
Pf. For randomly data, we expect each element to move halfway back.

N-by-N Matrix: the elements below the diagonal are counted—all of them, in the worst case. For random input, we expect each element to go about halfway back, on the average, so one-half of the elements below the diagonal should be counted.

---

# Análisis

* Mejor caso: A E E L M O P R S T X
  * Comparaciones: N-1
  * Movimientos: 0

* Peor caso: X T S R P O M L E E A
  * Comparaciones: ~N2/2
  * Movimientos: ~N2/2

???

Mejor caso: orden ascendente
Peor caso: orden descendente

---

# Bubble Sort

* Ordena intercambiando elementos contiguos, al final de cada pasada cada elemento estará en su posición final. [Animación](https://www.youtube.com/watch?v=yIQuKSwPlro)

* Existen dos implementaciones posibles:
  * Los elementos se acumulan en el final de la lista a ordenar [Sample](http://www.cs.oswego.edu/~mohammad/classes/csc241/samples/sort/Sort2-E.html)
  * Los elementos se acumulan al principio de la lista a ordenar:

.center[![]({{site.baseurl}}/presentation/elemental_sorters/bubble_sort.jpg)]

---

# Implementación

```java
class Bubble extends AbstractSort {
    void sort(Comparable[] a) {
        int N = a.length;
        for (int i = 0; i < N-1; i++) {
            for (int j = N-1; j > i; j--) {
                compExch(a, j, j - 1);
            }
        }
    }
}
```

???

static void bubble(ITEM[] a, int l, int r)
{
    for (int i = l; i < r; i++)
        for (int j = r; j > i; j--)
            compExch(a, j-1, j);
}

---

# Análisis

* Casos promedio y peor:
  * Mismo # de comparaciones y exchanges
  * Comparaciones: N-1 +N-2+...+2+1 =N(N-1)/2 =~N2/2
  * Intercambios: ~N2/2

* Mejor caso:
  * Comparaciones: ~N2/2
  * Intercambios: 0

* Es adaptivo respecto de las comparaciones.

* Se pueden hacer optimizaciones como por ejemplo dejar de iterar cuando no hubo cambios en la última iteración. Eso hace que el algoritmo sea adaptive respecto de las comparaciones.

---

# Dinámica de los algoritmos

* Selection sort
![]({{site.baseurl}}/presentation/elemental_sorters/dinamica_selection_sort.jpg)

* Insertion sort
![]({{site.baseurl}}/presentation/elemental_sorters/dinamica_insertion_sort.jpg)

* Bubble sort
![]({{site.baseurl}}/presentation/elemental_sorters/dinamica_bubble_sort.jpg)

---

# Entradas parcialmente ordenadas

* Inversión:
  * Un par de claves fuera de orden

* Un file está parcialmente ordenado si el número de inversiones es O(N).

* Para files parcialmente ordenados, insertion y bubble ejecutan en tiempo lineal.

???

In one type of partially sorted file, each item is close to its final position in the file. For example, some people sort their hand in a card game by first organizing the cards by suit in order to put their cards close to their final position, and then by considering the cards one by one. We shall be considering a number of sorting methods that work in much the same way—they bring elements close to final positions in early stages to produce a partially sorted file with every element not far from where it ultimately must go. Insertion sort and bubble sort (but not selection sort) are efficient methods for sorting such files.

---

# Identificar los algoritmos

.center[![]({{site.baseurl}}/presentation/elemental_sorters/id_algoritmos.jpg)]

???

Izquierda: insertion sort
Derecha: selection sort

---

# Estabilidad

* Un método de sorting es estable si preserva el orden relativo de los items con claves duplicadas en el file/tabla.

.center[![]({{site.baseurl}}/presentation/elemental_sorters/estabilidad.jpg)]

???

En el ejemplo, los elementos ya estaban ordenados por apellido. Se los ordena por número.
Explicar representación de los elementos para analizar estabilidad.

---

# Elementos grandes (2)

Conclusión

* Selection sort corre en tiempo lineal para tablas con elementos grandes y claves pequeñas.

* Puede llegar a servir como método de sort externo.

---

# Análisis Empírico

![]({{site.baseurl}}/presentation/elemental_sorters/analisis_empirico.jpg)

* Sedgewick, Algorithms in Java, 3rd Edition

???

Insertion sort and selection sort are about twice as fast as bubble sort for small files, but running times grow quadratically (when the file size grows by a factor of 2, the running time grows by a factor of 4). 
None of the methods are useful for large randomly ordered files—for example, the numbers corresponding to those in this table are less than 1 for the shellsort algorithm in Section 6.8. 
When comparisons are expensive— for example, when the keys are objects or strings—then insertion sort is much faster than the other two because it uses many fewer comparisons. 
Not included here is the case where exchanges are expensive; then selection sort is best.

---

# Shellsort

* Idea: Mover elementos más de una posición a la vez al “h-ordenar”.
* Un archivo h‐sorted tiene la propiedad de que tomando un subset cada h elementos, resulta un file ordenado.
* Un archivo 3-ordenado (3-sorted) son 3 archivos ordenados entrelazados

.center[![]({{site.baseurl}}/presentation/elemental_sorters/hsort.jpg)]

---

# h-sort
* Aplicamos insertion sort con un incremento de h
* Ejemplo: 3-sort de un file

.center[![]({{site.baseurl}}/presentation/elemental_sorters/3hsort.jpg)]

---

# Implementación h-sort

```java
// ordenando a[l]..a[r]
h = 4;
for (i = l+h; i <= r; i++) { 
    Comparable v = a[i];
    j = i;
    while (j >= l+h && less(v, a[j-h])) { 
        a[j] = a[j-h];
        j -= h; 
    }
    a[j] = v;
}

```

???

r = N-1

---

# Shellsort
* Shellsort: h-ordenar el file para una secuencia decreciente de valores de h. [Animación](http://www.youtube.com/watch?v=kuKHSSvxDzI)

![]({{site.baseurl}}/presentation/elemental_sorters/shell_sort.jpg)

---

# Ejemplo

.center[![]({{site.baseurl}}/presentation/elemental_sorters/shell_sort_ejemplo.jpg)]

???

Notar que los elementos no tienen que moverse muy lejos debido al orden establecido por las pasadas
anteriores.

---

# Trace Visual

![]({{site.baseurl}}/presentation/elemental_sorters/shell_sort_trace_visual.jpg)

---

# Implementación

* Shellsort se implementa con una pasada de insertion‐sort de incremento h, para cada h.

```java
void shell(Comparable[] a, int l, int r) { 
    int h;
    for (h = 1; h <= (r-l)/9; h = 3*h+1);
    
    for ( ; h > 0; h /= 3) {
        for (int i = l+h; i <= r; i++) { 
            int j = i;
            Comparable v = a[i];
            while (j >= l+h && less(v, a[j-h])) { 
                a[j] = a[j-h]; 
                j -= h;
            }
            a[j] = v;
        }
    }
}
```

---

# Implementación

* Tomamos el insertion‐sort y cambiamos los incrementos de 1 por h. El resultante es un h‐sort.

* Además agregamos un outer‐loop que cambia el incremento (h) con la secuencia:
  * {1,4,13,40,121,364,1093,3280,9841}. 
  * Hay una relación de aproximadamente 1/3 entre incrementos.

---

# Complejidad

* La complejidad del algoritmo depende de la secuencia utilizada. Por ejemplo:

* O(N<sup>3/2</sup>) comparaciones para incrementos: 
  * 1 4 13 40 121 364 1093 3280 9841

* O(N<sup>4/3</sup>) para incrementos
  * 1 8 23 77 281 1073 4193 16577

* O(N(log N)<sup>2</sup>) para incrementos
  * 1 2 3 4 6 9 8 12 18 27 16 24 36 54 81

---

# Análisis Empírico

![]({{site.baseurl}}/presentation/elemental_sorters/shell_sort_empirico.jpg)

Key:
* O   1 2 4 8 16 32 64 128 256 512 1024 2048
* K   1 4 13 40 121 364 1093 3280 9841
* G   1 2 4 10 23 51 113 249 548 1207 2655 5843
* S   1 8 23 77 281 1073 4193 16577
* P   1 7 8 49 56 64 343 392 448 512 2401 2744
* I   1 5 19 41 109 209 505 929 2161 3905
* Sedgewick, Algorithms in Java, 3rd Edition

???

Shellsort is many times faster than the other elementary methods, even when the increments are powers of 2, but some increment sequences can speed it up by another factor of 5 or more. The three best sequences in this table are totally different in design. Shellsort is a practical method even for large files, particularly by contrast with selection sort, insertion sort, and bubble sort (see Table 6.1).

---

# Conclusiones

* Shellsort es un ejemplo de una idea simple que logra mejoras significativas de performance
* Problema abierto: no se conoce cuál es la mejor secuencia.
* ¿Por qué insertion sort?
  * Con grandes incrementos (h) → pequeños sub-archivos
  * Con pequeños incrementos → sub-archivos casi ordenados 
  * → Insertion es lineal en las comparaciones, y hay muy pocos movimientos al ir reduciendo h

---

# Ejercicio 

Ordene la siguiente secuencia usando los 4 algoritmos dados en teoría

      E A S Y Q U E S T I O N