class: center, middle, inverse

# Quicksort

---

# Introducción a Quicksort

* Uno de los más usados
  * Librerías de Java (J2SE)  → ver java.util.Arrays
* Inventado en 1960 por C. A.R. Hoare
* Muy popular:
  * Fácil de implementar
  * Funciona bien con distintos tipos de entrada
  * Consume menos recursos que el resto de los demás algoritmos de sorting en muchas situaciones

* [Explicación](http://www.youtube.com/watch?v=y_G9BkAm6B8&feature=related)

---

# Pros y Contras

* Ventajas
  * In-place sorting
  * Sólo requiere memoria considerable para el stack
  * Tiempo de ejecución proporcional N * log N  
* Desventajas
 * No estable
 * Cuadrático en el peor caso
 * Frágil: un pequeño error al implementar puede romper la performance

???

For huge files, however, Quicksort is likely to run 5 to 10 times as fast as Shellsort, and it can adapt to be even more efficient for other types of files that might occur in practice.

---

# Algoritmo

* Método divide-and-conquer 
* Idea: 
  * Particionar el array en dos partes
  * luego ordenarlas independientemente (recursivamente) 
* El particionamiento es el paso esencial que define la performance de la implementación

.center[![]({{site.baseurl}}/presentation/quicksort/algorithm.jpg)]

---

# Particionamiento

* Pasos
  * Selección del pivot (v), quedará en su posición final
  * Un puntero busca desde la izquierda un item mayor al pivot
  * Un puntero busca desde la derecha un item menor al pivot
  * Intercambiar los elementos
  * Continuar hasta que se crucen los punteros y ubicar al pivot

.center[![]({{site.baseurl}}/presentation/quicksort/particionamiento.jpg)]

???

[PREGUNTA: ¿Quicksort es estable?]

It is best to stop the left scan for elements greater than or equal to the partitioning element and the right scan for elements less than or equal to the partitioning element, even though this policy might seem to create unnecessary exchanges involving elements equal to the partitioning element 

---

# Particionamiento (2)

* Divide un archivo en dos subarchivos que pueden ser ordenados en forma independiente

.center[![]({{site.baseurl}}/presentation/quicksort/particionamiento2.jpg)]

---

# Estabilidad

* El proceso de particionamiento no es estable, pues una clave puede ser movida cruzando a otras iguales, sin haber sido examinadas, en cualquier intercambio.

---

# Implementación - partition

```java
private static int partition(Comparable[] a, int lo, int hi) {
	int i = lo - 1;
	int j = hi;

	while(true) {
		while( less(a[++i], a[hi]) ) //find item left to swap
			if (i == hi) break;

		while( less(a[hi], a[--j]) ) //find item right to swap
			if (j == lo) break;

		if (i >= j) break; //check if pointers cross

		exch(a, i, j); //swap
	}

	exch(a, i, hi); //swap with partitioning item
	return i; //return index of item now known to be in place
}
```

???

If the file is randomly ordered, choosing a[r] as the partitioning element is the same as choosing any other specific element and will give us a split near the middle on the average.

The (i == hi) test is redundant (in this case, partitioning element is in hi) but the (j == lo) test is not redundant.

When duplicates are present, it is (counter-intuitively) best to stop on elements equal to the partitioning element.

---

# Implementación - partition

.center[![]({{site.baseurl}}/presentation/quicksort/particionamiento3.jpg)]

---

# Implementación - Quicksort

```java
private static void sort(Comparable[] a, int lo, int hi) {
	if(hi <= lo) return;
	int i = partition(a, lo, hi);
	sort(a, lo, i - 1);
	sort(a, i + 1, hi);
}
```

---

# Quicksort - ejemplo

.center[![]({{site.baseurl}}/presentation/quicksort/sample.jpg)]

???

Ignorar random shuffle!

---

# Análisis de Performance

* Está definida por el particionamiento
  * y en particular por la selección del pivot
* Problema de quicksort
  * Degenera con casos típicos → tiempo de ejecución cuadrático
* Peor caso 
  * Archivo ordenado ascendente o descendente
  * Comparaciones: N-1 + N-2 + ... + 2 + 1 = N(N-1)/2 =  ~N<sup>2</sup>/2
  * El tamaño del stack es ~N → no aceptable para archivos muy grandes
* Mejor caso
  * Cuando el particionamiento divide al archivo por la mitad
  * C<sub>N</sub> = 2C<sub>N/2</sub> + N → comparaciones: ~N log N
  * 2C<sub>N/2</sub> es el costo de ordenar los dos sub-archivos
* Caso promedio
  * Puede demostrarse que:
  * Comparaciones: ~2N ln N ~= 1.39 N log N 
  * 39% mayor al mejor caso 
* Para casos de archivos con muchas claves duplicadas y para el peor caso mencionado, quicksort no logra buena performance
* Para archivos muy grandes, puede fallar la ejecución (stack overflow)
* Para ser de propósito general → Necesita improvements!

???

The 2C<sub>N/2</sub> covers the cost of sorting the two subfiles; the N is the cost of examining each element, using one partitioning index or the other. 

Note that 2N ln N 1.39N lg N, so the average number of comparisons is only about 39 percent higher than in the best case. 

---

# Tamaño del Stack

* Para un archivo random:
  * El tamaño de stack es O(log N) 
* Para casos en que quicksort degenera:
  * El tamaño del stack es O(N)
* Con archivos muy grandes puede producirse un error en runtime

---

# Tamaño del Stack (2)

* Izquierda y Centro, entrada aleatoria; Derecha (peor caso) entrada ordenada o inversa

.center[![]({{site.baseurl}}/presentation/quicksort/stack.jpg)]

---

# Implementación no recursiva

```java
static void quicksortNonRecursive(Comparable[] a, int l, int r)
{
	IntStack S = new IntStack(50);
	S.push(l);
	S.push(r);
	while (!S.empty()) {
		r = S.pop();
		l = S.pop();
		if (r <= l) {
			continue;
		}
		int i = partition(a, l, r);
		if (i - l > r - i) {
			S.push(l);
			S.push(i - 1);
		}
		S.push(i + 1);
		S.push(r);
		if (r - i >= i – l) {
			S.push(l);
			S.push(i - 1);
		}
	}
}
```

---

# Implementación no recursiva (2)

* Siempre se coloca el mayor subarchivo primero en el stack
* Se ordena siempre primero el más chico
* Se asegura un tamaño máximo del stack de log N para N elementos 
* Esta técnica no siempre representa una mejora respecto de implementaciones recursivas
  * depende de que la plataforma provea end-recursion removal
* Si la plataforma/compilador son eficientes para invocaciones recursivas, entonces la no-recursiva puede no proveer mejora

???

The policy of putting the larger of the small subfiles on the stack ensures that each entry on the stack is no more than one-half of the size of the one below it, so that the stack needs to contain room for only about lg N entries.

If the smaller of the two subfiles is sorted first, then the stack never has more than lg N entries when quicksort is used to sort N elements.

The worst-case stack size must be less than TN, where TN satisfies the recurrence with T1 = T0 = 0. This recurrence is a standard one of the type that is considered in Chapter 5 = 0. This recurrence is a standard one of the type that is considered in Chapter 5 (see Exercise 7.13). 

end- or tail-recursion removal. If the last action of a procedure is to call another procedure, some programming environments will arrange things such that local variables are cleared from the stack before, rather than after, the call. Without end-recursion removal, we cannot guarantee that the stack size will be small for quicksort. For example, a call to quicksort for a file of size N that is already sorted will result in a recursive call to such a file of size N - 1, in turn resulting in a recursive call for such a file of size N - 2, and so on, ultimately resulting in a stack depth proportional to N. 

---

# Quicksort Híbrido

Idea:
* Un sort recursivo se invoca a sí mismo para muchos pequeños subarchivos → El método que usa para estos debería ser el mejor posible
  * Insertion sort es el mejor para ordenar archivos que están parcialmente ordenados
* Mejora:
  * Al principio de la llamada recursiva:

```java
if (hi - low <= M) {  	
	insertion(a, low, hi);        
	return; 
}
```

---

# Quicksort Híbrido

* Otra forma de implementarlo

```java
private final static int M = 10;

static void quicksort(Comparable[] a, int l, int r)
{
	if (r-l <= M) return;
	int i = partition(a, l, r);
	quicksort(a, l, i - 1);
	quicksort(a, i + 1, r);
}

static void hybridsort(Comparable[] a, int l, int r)
{ 
	quicksort(a, l, r); 
	insertion(a, l, r); 
}
```

* Insertion sort logra el mismo resultado, pero no hay overhead de las llamadas a cada invocación de insertion sort.

---

# Quicksort Híbrido (2)

.center[![]({{site.baseurl}}/presentation/quicksort/hibrido.jpg)]

---

# Quicksort Híbrido (3)

* M: valor de corte (parámetro a optimizar)
* Las pruebas muestran que lo recomendable es entre 5 y 25

.center[![]({{site.baseurl}}/presentation/quicksort/m.jpg)]

---

# Problema del peor caso

* Cómo evitar que degenere? 
* Algoritmo probabilístico:
  * Utilizar un elemento al azar como pivot
  * Disminuye la probabilidad del peor caso 
* Mediana-de-3:
  * Idea: Tomar muestras del archivo desconocido y usarlas para estimar 
  * Tomar 3 elementos y utilizar la mediana

---

# Mediana-de-3

```java
private final static int M = 10;

static void quicksort(Comparable[] a, int lo, int hi)
{
	if (hi-lo <= M) return;
	exch(a, (lo+hi)/2, hi-1);
	compExch(a, lo, hi-1);
	compExch(a, lo, hi);
	compExch(a, hi-1, hi);
	int i = partition(a, lo+1, hi-1);
	quicksort(a, lo, i-1);
	quicksort(a, i+1, hi);
}

static void hybridsort(Comparable[] a, int lo, int hi)
{ 
	quicksort(a, lo, hi); 
	insertion(a, lo, hi); 
}
```

* El primer intercambio se hace para los archivos que ya están ordenados (en el caso random no tiene efecto).
  * Logra particionamiento óptimo para el peor caso.

---

# Mediana-de-3

Mejoras a quicksort
* El peor caso tiene mucha menos probabilidad de ocurrencia
* Para que tome tiempo cuadrático, dos de los tres elementos examinados deben ser de los mayores o menores, ocurriendo en sucesivas particiones
* Además: reduce el tiempo promedio de ejecución en un 5%
* Dicho de otra forma: mediana-de-3 encuentra un mejor pivot!

---

# Claves duplicadas

* Quicksort tiene baja performance en casos de muchas claves duplicadas (degenera en N<sup>2</sup>)
* Three-way-partitioning:
  * Separar las claves en tres conjuntos: menores, mayores e iguales al pivot – ir eliminando las iguales en cada particionamiento


.center[![]({{site.baseurl}}/presentation/quicksort/3-way.jpg)]

???

When there are many duplicate keys present in the file to be sorted, the quicksort implementations that we have considered do not have unacceptably poor performance, but they can be substantially improved. For example, a file that consists solely of keys that are equal (just one value) does not need to be processed further, but our implementations so far keep partitioning down to small subfiles, no matter how big the file is (see Exercise 7.8). In a situation where there are large numbers of duplicate keys in the input file, the recursive nature of quicksort ensures that subfiles consisting solely of items with a single key value will occur often, so there is potential for significant improvement.

---

# Three-way partitioning Bentley and McIlroy, 1993

* Mantener las claves iguales al pivot encontradas en el sub-archivo izquierdo a la izquierda del archivo, y las iguales encontradas en el sub-archivo derecho en el extremo derecho del archivo.
* Al cruzarse los índices, conociendo la ubicación de las claves iguales al pivot, colocamos en posición estos elementos.
* Resultado:
  * Maneja claves duplicadas eficientemente y produce un mínimo overhead para los casos sin claves duplicadas

.center[![]({{site.baseurl}}/presentation/quicksort/3-way2.jpg)]

* http://www.mathcs.emory.edu/~cheung/Courses/171/Syllabus/08-Sort/quick-sort.html

---

# Análisis Empírico

* Se analiza la cantidad de comparaciones para un texto grande (muchas palabras repetidas)

.center[![]({{site.baseurl}}/presentation/quicksort/empirico.jpg)]

???

This table gives relative costs for several different versions of quicksort on the task of sorting the first
N words of Moby Dick. Using insertion sort directly for small subfiles, or ignoring them and insertion
sorting the same file afterward, are equally effective strategies, but the cost savings is slightly more
than for integer keys (see Table 7.1) because comparisons are more expensive for strings. If we do not
stop on duplicate keys when partitioning, then the time to sort a file with all keys equal is quadratic; the
effect of this inefficiency is noticeable on this example, because there are numerous words that appear
with high frequency in the data. For the same reason, three-way partitioning is effective; it is 30 to 35
percent faster than Program 7.1.
