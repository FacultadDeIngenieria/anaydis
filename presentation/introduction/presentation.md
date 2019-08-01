class: center, middle, inverse

# Introducción al Análisis de Algoritmos

???

[PREGUNTA: QUÉ ES UN ALGORITMO?]

---

# Conceptos

* Problema computacional: especificación de una relación deseada entre un conjunto de valores de entrada y un conjunto de valores de salida.
  * Ej:
  * Input: Una secuencia de n números〈a1, a2, ..., an〉.
  * Output: Una permutación (reordenamiento)〈a1’, a2’, ..., an’〉de la entrada tal que: a1’ <= a2’ <= ... <= an’
* Instancia de problema: para un problema dado, una instancia es un conjunto de valores de entrada determinado.

* Algoritmo: secuencia de pasos computacionales que transforman la entrada en la salida esperada.
  * Otra definición: “En computer science, un método para resolver un determinado problema, apropiado para ser implementado como un programa de computadora.”, Robert Sedgewick

* Referencia: Cormen, Introduction to Algorithms, 2nd Edition

---

# Conceptos (2)

* Correctitud
  * Un algoritmo se denomina correcto si para cada instancia de problema, obtiene la salida esperada.
  * En este caso decimos que el algoritmo resuelve el problema computacional dado.
* Estructura de Datos
  * Una estructura de datos es una manera de almacenar y organizar la información para facilitar el acceso y las modificaciones.
  * La mayoría de los algoritmos bajo estudio implican métodos para organizar datos envueltos en la ejecución. Para ello se utilizan estructuras de datos.
* Análisis de Algoritmos
  * Rama de la Ciencia de la Computación que estudia las características de performance de los algoritmos.

???

[PREGUNTA: PARA QUÉ ESTUDIAR LA PERFORMANCE O EFICIENCIA DE UN ALGORITMO?]

---

# Motivación

* ¿Por qué estudiar análisis de algoritmos?
  * Queremos lograr programas que corran más rápido y procesen mayores volúmenes de información.
  * Los recursos computacionales de procesamiento (CPU) y almacenamiento (memoria) son escasos:
  * Al existir más de un approach/método para resolver un problema, necesitamos compararlos y elegir el más eficiente.
  * Al procesar grandes volúmenes de información, un buen algoritmo puede mejorar la velocidad millones de veces, pero invertir en capacidad de procesamiento sólo puede mejorar la performance en un factor de 10 o 100.
  * Permite estimar la performance al diseñar un nuevo software – mejorar la calidad de desarrollo de software

---

# Motivación (2)

* Aplicaciónes reales de los algoritmos
  * Proyecto del Genoma Humano 
  * Análisis de grandes volúmenes de datos (datamining)
  * Internet
  * Ruteo de información, motores de búsqueda
  * E-Commerce (security)
  * Public-key cryptography, digital signatures
  * Manufactura (maximización de beneficios, toma de decisiones)
  * Programación lineal

???

The Human Genome Project has the goals of identifying all the 100,000 genes in human DNA, determining the sequences of the 3 billion chemical base pairs that make up human DNA, storing this information in databases, and developing tools for data analysis. Each of these steps requires sophisticated algorithms. While the solutions to the various problems involved are beyond the scope of this book, ideas from many of the chapters in this book are used in the solution of these biological problems, thereby enabling scientists to accomplish tasks while using resources efficiently. The savings are in time, both human and machine, and in money, as more information can be extracted from laboratory techniques.

The Internet enables people all around the world to quickly access and retrieve large amounts of information. In order to do so, clever algorithms are employed to manage and manipulate this large volume of data. Examples of problems which must be solved include finding good routes on which the data will travel (techniques for solving such problems appear in Chapter 24The Internet enables people all around the world to quickly access and retrieve large amounts of information. In order to do so, clever algorithms are employed to manage and manipulate this large volume of data. Examples of problems which must be solved include finding good routes on which the data will travel (techniques for solving such problems appear in Chapter 24), and using a search engine to quickly find pages on which particular information resides (related techniques are in Chapters 11The Internet enables people all around the world to quickly access and retrieve large amounts of information. In order to do so, clever algorithms are employed to manage and manipulate this large volume of data. Examples of problems which must be solved include finding good routes on which the data will travel (techniques for solving such problems appear in Chapter 24), and using a search engine to quickly find pages on which particular information resides (related techniques are in Chapters 11 and 32).

Electronic commerce enables goods and services to be negotiated and exchanged electronically. The ability to keep information such as credit card numbers, passwords, and bank statements private is essential if electronic commerce is to be used widely. Public-key cryptography and digital signatures (covered in Chapter 31) are among the core technologies used and are based on numerical algorithms and number theory.

In manufacturing and other commercial settings, it is often important to allocate scarce resources in the most beneficial way. An oil company may wish to know where to place its wells in order to maximize its expected profit. A candidate for the presidency of the United States may want to determine where to spend money buying campaign advertising in order to maximize the chances of winning an election. An airline may wish to assign crews to flights in the least expensive way possible, making sure that each flight is covered and that government regulations regarding crew scheduling are met. An Internet service provider may wish to determine where to place additional resources in order to serve its customers more effectively. All of these are examples of problems that can be solved using linear programming, which we shall study in Chapter 29.

---

# Eficiencia de Algoritmos

* Antes de poder analizar un algoritmo asegurarse que:
  * El problema está bien planteado
  * El algoritmo es correcto
* Factores  que podemos analizar de un algoritmo:
  * Tiempo de ejecución como función de la entrada -  Escalabilidad
  * Necesidades de memoria de programa y disco
  * Si es paralelizable
  * Correctitud
  * Claridad y dificultad del approach (dificultad para implementar)
  * Robustez (cuán bien maneja casos excepcionales o entradas erróneas, cuán bien tolera la carga extrema)

???

In order to learn more about an algorithm, we can 'analyze' it. By this we mean to study the specification of the algorithm and to draw conclusions about how the implementation of that algorithm--the program--will perform in general. But what can we analyze? We can 

determine the running time of a program as a function of its inputs; 
determine the total or maximum memory space needed for program data; 
determine the total size of the program code; 
determine whether the program correctly computes the desired result; 
determine the complexity of the program--e.g., how easy is it to read, understand, and modify; and, 
determine the robustness of the program--e.g., how well does it deal with unexpected or erroneous inputs? 

---

# Eficiencia de Algoritmos (2)

* Los factores que nos interesa analizar son:
  * Complejidad temporal
  * Necesidad de memoria y almacenamiento
* Modelo simplificado de computadora para el análisis:
  * Medida de tiempo: cantidad de instrucciones o pasos de ejecución
  * Medida de espacio: cantidad de posiciones de memoria referenciadas

???

Ver modelo simplificado de la JVM (cada operación tiene su tiempo… etc)

---

# Tipos de análisis	

* Empírico
  * Someter al algoritmo implementado a pruebas y monitorear la performance
  * Es necesario tener una implementación completa y correcta
  * Para poder comparar, los factores del contexto deben trascender las distintas pruebas (ej: computadora, sistema operativo, compilador, …)
  * Se debe seleccionar el correcto conjunto de datos de entrada
* Puede ser:
  * Real (para medir el costo de una ejecución real)
  * Aleatorio (para testear al algoritmo)
  * Perverso (para verificar que el algoritmo puede manejar cualquier tipo de entrada)
* Sirven para verificar el comportamiento, pero no para caracterizar un algoritmo

???

[PREGUNTA: CÓMO SE VISUALIZA EL SET DE MUESTRAS PARA ANALIZAR LOS RESULTADOS?]

Ejemplos de pruebas de performance (parte de tests no funcionales):

-vLoad Testing:  cómo se comporta bajo cierto valor de carga esperada.

Escalabilidad: Incrementando carga, se analiza si el algoritmo es escalable o no.
Dará un tiempo de respuesta satisfactorio al aumentar la carga?

Stress Testing: buscar el “punto de quiebre” (ej: OutOfMemory) – analiza robustez del programa/algoritmo
- Hasta qué valor de carga soportará mi algoritmo? (ej: algoritmo recursivo VS iterativo)

Endurance Testing (Soak Testing): se analiza la sustentabilidad del algoritmo en el tiempo, bajo carga esperada. Se observan uso de memoria y degradación de performance.
 Puedo dejarlo correr días sin preocuparme? O tendré que reiniciarlo porque falla?

---

# Tipos de análisis (2)

* Matemático
  * Utilizar herramientas matemáticas para estudiar la performance de los algoritmos y caracterizarlos.

* Permite:
  * Comparar algoritmos que realizan una misma tarea
  * Predecir performance en nuevos ambientes
  * Setear los parámetros para optimizar un algoritmo

* Utilizaremos un modelo simplificado para abstraernos del hardware y plataforma en que es ejecutado e implementado

---

# Análisis Matemático

Pasos:
* Identificar las operaciones abstractas en que se basa el algoritmo para separar el análisis de la implementación (cantidad de operaciones vs tiempo de ejecución en milisegundos)
* Determinar cuáles operaciones son pasos significativos (los que determinan la performance)
  * Las partes que más se ejecutan
* Definir el tipo de entrada que se presenta al algoritmo
  * Estudio de Caso Promedio: utilizando entrada aleatoria
  * Estudio de Peor Caso: utilizando entrada “perversa”

???

Important factors in a precise analysis are usually outside a given programmer's domain of influence. First, Java programs are translated into bytecode, and the bytecode is interpreted or translated into runtime code on a virtual machine (VM). The compiler, translator, and VM implementations all have an effect on which instructions on an actual machine are executed, so it can be a challenging task to figure out exactly how long even one Java statement might take to execute. In an environment where resources are being shared, even the same program can have varying performance characteristics at two different times. Second, many programs are extremely sensitive to their input data, and performance might fluctuate wildly depending on the input. Third, many programs of interest are not well understood, and specific mathematical results may not be available. Finally, two programs might not be comparable at all: one may run much more efficiently on one particular kind of input and the second may run efficiently under other circumstances.
All these factors notwithstanding, it is often possible to predict precisely how long a particular program will take, or to know that one program will do better than another in particular situations. Moreover, we can often acquire such knowledge by using one of a relatively small set of mathematical tools. It is the task of the algorithm analyst to discover as much information as possible about the performance of algorithms; it is the task of the programmer to apply such information in selecting algorithms for particular applications. In this and the next several sections, we concentrate on the idealized world of the analyst. To make effective use of our best algorithms, we need to be able to step into this world, on occasion.

---

# Aplicación

* Análisis Matemático para:
  * Caracterizar y categorizar algoritmos
  * Poder seleccionar el más apropiado para un problema dado.
  * Predecir performance.
  * Aprender a desarrollar aplicaciones eficientes y escalables, robustas y que se adapten a la demanda requerida. Mejorar la calidad de los productos desarrollados.
* Análisis Empírico para:
  * En investigación: verificar el análisis teórico. 
  * En desarrollo de aplicaciones: verificar la performance y escalabilidad del producto.

???

[PREGUNTA: Qué pasa si un programa que procesa transacciones de un banco no es escalable o no es robusto?]
[PREGUNTA: Qué implica que no sea escalable?] → con un poco más de carga, el tiempo de respuesta es inaceptable

---

# Complejidad de Algoritmos (Crecimento de funciones)

* Parámetro N: aquel que determina el tiempo de ejecución 
  * Ej: grado de polinomio, tamaño de archivo en el cual buscar, cantidad de caracteres de un String
* La complejidad temporal se representa con una función T(n), que indica el número de operaciones que realiza un algoritmo para una entrada de tamaño n
  * T(n) también puede representar unidades de tiempo o ciclos de procesador

???

Si hay más de un parámetro (M), uno de ellos se considera constante para el análisis o se expresa como función del otro.

---

# Casos de análisis

* Peor caso
  * Tpeor(n) = max(t(i))
  * i: instancia de entrada de tamaño n
  * Representa el mayor tiempo que puede tomar el algoritmo sobre una instancia de problema de tamaño n
  * Garantía: nunca se comportará peor
  * Garantiza que el numero de operaciones es menor a una función del tamaño de la entrada, independientemente del valor de las entradas.
* Caso promedio
  * Tprom(n) = ΣiЄI |i |=n P(i)*t(i)
  * Tiempo promedio o esperado sobre instancias típicas
  * Suma del tiempo de cada suceso multiplicado por su probabilidad
  * Es necesario conocer la distribución estadística de la entrada para calcularlo – muchas veces se realizan simplificaciones incorrectas
  * Permite hacer predicciones sobre el tiempo de ejecución de los algoritmos.

???

Hay casos complejos respecto del tipo de entrada:
Por ejemplo – cómo caracterizo la entrada para un algoritmo que procesa lenguaje natural (Inglés, Español) ?

---

# Análisis Asintótico de la Eficiencia

* El estudio de tiempo de ejecución cuando el tamaño de la entrada “tiende a infinito”
  * → sólo el orden de crecimiento de la función es relevante

![]({{site.baseurl}}/presentation/introduction/asintotico.jpg)

???

When we look at input sizes large enough to make only the order of growth of the running time relevant, we are studying the asymptotic efficiency of algorithms. That is, we are concerned with how the running time of an algorithm increases with the size of the input in the limit, as the size of the input increases without bound. Usually, an algorithm that is asymptotically more efficient will be the best choice for all but very small inputs.

---

# Análisis Asintótico (2)

* Algoritmos (tecnología) vs capacidad de procesamiento
* Un algoritmo rápido permite solucionar un problema en una máquina lenta, pero una máquina rápida no ayuda cuando el algoritmo es lento.

![]({{site.baseurl}}/presentation/introduction/asintotico2.jpg)

* Fuente: Sedgewick

---

# Cotas – Herramientas Matemáticas

* Permiten eliminar los detalles al analizar algoritmos, prestando atención a los términos significativos y obteniendo aproximaciones matemáticas precisas

* Notaciones:

* O-Grande: O(g(n))
  * Expresión asintótica, cota superior
  * Cuánto tarda como máximo

* omega: Ω(g(n))
  * Cota asintótica inferior
  * Cuánto tarda como mínimo

* Theta: Θ(g(n))
  * Orden acotado por inferior y superior
  * Conozco ambas cotas

---

# O-Grande

* Se dice f(n) = O(g(n)) (“f es O(g(n))”) si: 
  * existe una constante positiva c y n0 tal que  0 ≤ f(n) ≤ cg(n) para todo n ≥ n0
* Es la notación más utilizada ya que permite analizar el peor caso

![]({{site.baseurl}}/presentation/introduction/ogrande.jpg)

???

Volviendo a T(n)
Si para un algoritmo sabemos que Tpeor es O(g), podemos asegurar que
para entradas de tamaño creciente, en todos los casos el tiempo será a lo
sumo proporcional a g.

Si lo que sabemos es Tprom ∈ O(g), entonces, para entradas de tamaño
creciente, en promedio, el tiempo será proporcional a g.

---

# Ventajas de utilizar notación O-Grande

* Propósito
  * Acotar el error que se genera al ignorar términos pequeños en las fórmulas matemáticas.
  * Acotar el error que se genera al ignorar partes del programa que contribuyen poco al tiempo total analizado.
  * Permite clasificar los algoritmos según su cota superior de tiempo de ejecución

---

# Omega

![]({{site.baseurl}}/presentation/introduction/omega.jpg)

---

# Theta

![]({{site.baseurl}}/presentation/introduction/theta.jpg)

---

# Propiedades de la notación O-Grande

![]({{site.baseurl}}/presentation/introduction/propiedades.jpg)

---

# Convenciones

* Eliminar los términos no significativos
* Eliminar coeficientes constantes
* No escribir la base de los logaritmos
* La diferencia es despreciable en el análisis asintótico

???

Sólo es necesario diferenciar con ln n de log n en el caso que sea característico del algoritmo.

Utilizar log n para logaritmo en base 2 y ln n para logaritmo neperiano  (cuando anotamos la base de los logaritmos)

---

# Familias de Órdenes más interesantes

* O(1): complejidad constante
  * La mayoría de las instrucciones se invocan solo una o K veces

* O(log(n)): complejidad logarítmica
  * Recordemos: logb (n) = log2(n)/log2(b) = c. log2(n)
  * Se hace más lento cuando n crece. Tipicamente programas que resuelven problemas grandes dividiéndolos en problemas mas pequeños. (se duplica recién cuando n2)

* O(n): complejidad lineal
  * Tipicamente problemas que realizan algun procesamiento para cada elemento (por ejemplo incrementar en uno todos los elementos de un arreglo o calcular su promedio)

* O(n*log(n)): complejidad lineal-logaritmica
  * Tipicamente se descompone el problema en partes que se procesan y luego se combinan para llegar a la solucion. (crece un poco mas velozmente que n, pero no mucho...)

---

# Familias de Órdenes más interesantes (2)

* O(n2): complejidad cuadrática.
  * Puede usarse en problemas pequeños, tipicamente problemas que procesan los datos de a pares con dos loops, etc. (cuando n se duplica, el tiempo se multiplica por 4)

* O(n3): complejidad cúbica.
  * Similar al anterior (cuando n se duplica, el tiempo se multiplica por 8)

* O(nk ), k ≥ 1: complejidad polinomial.
  * idem

* O(2n): complejidad exponencial.
  * Muy poco usados, tipicamente son soluciones de “fuerza bruta” (cuando n se duplica, el tiempo de ejecucion se aumenta al cuadrado)

---

# Familias de Órdenes más interesantes (3)

![]({{site.baseurl}}/presentation/introduction/ordenes.jpg)

---

# Notación O-Grande Ajustada

* La notación O-Grande provee una cota para el comportamiento asintótico, pero no dice cuán cerca de la cota está la función de complejidad real.

* Una cota ajustada es la “mejor” cota posible.

* Sea la función f(n)=O(g(n)). Si para toda función h(n) tal que f(n)=O(h(n)) se cumple que g(n)=O(h(n)), por lo tanto g(n) es una cota asintótica ajustada de f(n).

* Ejemplo: Considere la función f(n)=8n+128. f(n)=O(n2). Como f(n) es polinómica, luego f(n)=O(n). O(n) es una cota “más ajustada” del comportamiento asintótico de f(n). 

---

# Observación

* Si tenemos dos programas que solucionan un mismo problema: 
  * A que es O(n2) y B que es O(n3) 
  * (siendo O-grande acotadas)

* No podemos concluir que deberíamos usar A antes que B para solucionar una instancia particular del problema. Lo que sí sabemos es que para un n lo suficientemente grande, A es la mejor opción.

* Ante la duda (ej: no puedo anticipar el tipo de problema), lo recomendable es optar por A, pues es la opción más segura.

---

![]({{site.baseurl}}/presentation/introduction/ejemplo.jpg)

* Fuente: Data Structures and Algorithms with Object-Oriented Design Patterns in Java, Bruno R. Preiss

---

![]({{site.baseurl}}/presentation/introduction/ejemplo2.jpg)

???

Por la regla de la suma, el O grande se obtiene de forma trivial.

---

# Ejemplo: Búsqueda Secuencial

* Operación de búsqueda: 
  * Sea un array de enteros a[]. El algoritmo retorna la posición del elemento buscado v o -1 si no lo encuentra. 
* Implementación secuencial:

```java
static int search(int a[], int v, int l, int r) { 
	int i; 
	for (i = l; i <= r; i++) {
		if (v == a[i]) return i;
	}
	return -1;  
}
```

---

# Búsqueda Secuencial (2)

* Peor caso:
  * Examina N elementos para cada búsqueda no satisfactoria → T(N)=N

* Caso promedio:
  * Examina N/2 elementos (aproximadamente) en cada búsqueda en promedio. 
  * Si cada número tiene igual probabilidad de ser objeto de búsqueda:
  * (1+2+…+N)/N=(N+1)/2=T(N)

* Orden del algoritmo
  * O(N)=N → complejidad lineal

???

[PREGUNTA: Podemos mejorar este método de búsqueda?]

El tiempo de ejecución depende de que el elemento buscado esté en el array y donde esté ubicado.
Podemos garantizar que no más de N elementos serán examinados.
Para poder predecir, debemos asumir que la información está distribuida aleatoriamente (uniformemente) – cada elemento tiene la misma probabilidad de ser objeto de búsqueda.

We note immediately that the running time depends on whether or not the object sought is in the array. We can determine that the search is unsuccessful only by examining each of the N objects, but a search could end successfully at the first, second, or any one of the objects.
Therefore, the running time depends on the data. If all the searches are for the number that happens to be in the first position in the array, then the algorithm will be fast; if they are for the number that happens to be in the last position in the array, it will be slow. We discuss in Section 2.7 the distinction between being able to guarantee performance and being able to predict performance. In this case, the best guarantee that we can provide is that no more than N numbers will be examined.
To make a prediction, however, we need to make an assumption about the data. In this case, we might choose to assume that all the numbers are randomly chosen. This assumption implies, for example, that each number in the table is equally likely to be the object of a search. On reflection, we realize that it is that property of the search that is critical, because with randomly chosen numbers we would be unlikely to have a successful search at all (see Exercise 2.48). For some applications, the number of transactions that involve a successful search might be high; for other applications, it might be low. To avoid confusing the model with properties of the application, we separate the two cases (successful and unsuccessful) and analyze them independently. This example illustrates that a critical part of an effective analysis is the development of a reasonable model for the application at hand. Our analytic results will depend on the proportion of searches that are successful; indeed, it will give us information that we might need if we are to choose different algorithms for different applications based on this parameter.
Property 2.1

---

# Búsqueda Binaria

* Asumimos que el conjunto de datos está ordenado
* Idea: Podemos eliminar la mitad de los elementos al comparar el del medio con el buscado.

```java
static int search(int a[], int v, int l, int r) { 
	while (r >= l) { 
		int m = (l+r)/2; 
		if (v == a[m]) return m; 
		if (v < a[m]) r = m-1; else l = m+1; 
	} 
	return -1;
} 
```

???

It is based on the idea that if the numbers in the table are in order, we can eliminate half of them from consideration by comparing the one that we seek with the one at the middle position in the table. If it is equal, we have a successful search. If it is less, we apply the same method to the left half of the table. If it is greater, we apply the same method to the right half of the table. Figure 2.7 is an example of the operation of this method on a sample set of numbers.

---

# Búsqueda Binaria (2)

* T(N) representa el número de comparaciones en el peor caso

![]({{site.baseurl}}/presentation/introduction/busbin.jpg)

* La cota para el peor caso es:

![]({{site.baseurl}}/presentation/introduction/busbin2.jpg)

* Luego la búsqueda binaria es O(log N) 
   * Algoritmo logarítmico

???

The proof of this property illustrates the use of recurrence relations in the analysis of algorithms. If we let TN represent the number of comparisons required for binary search in the worst case, then the way in which the algorithm reduces search in a table of size N to search in a table half the size immediately implies that
To search in a table of size N, we examine the middle number, then search in a table of size no larger than N/2 . The actual cost could be less than this value either because the comparison might cause us to terminate a successful search or because the table to be searched might be of size N/2 - 1 (if N is even). As we did in the solution of Formula 2.2, we can prove immediately that TN n + 1 if N = 2n and then verify the general result by induction. 
Property 2.3 says that we can solve a huge search problem with up to 1 billion numbers with at most 30 comparisons per transaction, and that is likely to be much less than the time it takes to read the request or write the result on typical computers.

---

# Observaciones

* La operación significativa en la búsqueda es la comparación. Por lo tanto se cuenta el número de comparaciones → T(n)

* Al duplicar el tamaño de la entrada N, el tiempo de ejecución de la búsqueda binaria casi no cambia, mientras que el de la búsqueda secuencial se duplica.

* Al crecer N, se alejan notablemente las funciones.

---

# Análisis Empírico (Verificación)

![]({{site.baseurl}}/presentation/introduction/verificacion.jpg)
 
* S: secuencial, B: binaria, N: cantidad de elementos (input), M: cantidad de búsquedas. Fuente: Algorithms in Java, Sedgewick

---

# Verificación Empírica: Observaciones y Técnicas

* El tiempo de ejecución en una prueba se ve afectado por:
  * El hardware
  * Lenguaje y compilador/intérprete
  * Implementación del Algoritmo
  * Datos de entrada
  * Caching
  * Garbage Collection
  * Uso de CPU por otras aplicaciones

---

# Verificación Empírica (2): Observaciones y Técnicas

* Si medimos tiempo de ejecución, hay que usar estadísticas para asegurar que la prueba es válida:
  * Correr la misma prueba M veces
  * Si el desvío estándar es relativamente grande respecto de la media, no obtuvimos una medida confiable

* OJO: Sucede más frecuentemente de lo esperado!

???

Dar ejemplo!

---

# Verificación Empírica (3): Observaciones y Técnicas

* Una buena forma es medir número de operaciones críticas ejecutadas:
  * Abstrae la prueba del hardware, plataforma y otras condiciones aleatorias
  * Es necesario instrumentar el código

???

Dar ejemplo!
[PREGUNTA: ALGUIEN SABE QUÉ ES INSTRUMENTAR?]
