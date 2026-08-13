class: center, middle, inverse

# Benchmarking de algoritmos de sorting

## Observable Pattern + métricas + CSV

???

Objetivo de la clase: que los alumnos entiendan cómo medir empíricamente un algoritmo sin ensuciar la lógica del sorter con código de medición.

---

# Problema

Ya tenemos algoritmos de ordenamiento.

Pero ahora queremos responder preguntas como:

* ¿Cuántas comparaciones hace cada algoritmo?
* ¿Cuántos intercambios realiza?
* ¿Cuánto tarda para distintos tamaños de entrada?
* ¿Cómo se comporta con distintos perfiles de datos?
* ¿Cómo guardamos esos resultados para analizarlos después?

???

La idea importante: no alcanza con decir “este algoritmo es O(N²)” o “este es O(N log N)”. También queremos medir implementaciones reales sobre datos reales o simulados.

---

# Análisis teórico vs análisis empírico

* Análisis teórico:
  * Estima el comportamiento usando modelos matemáticos
  * Generalmente habla de orden de crecimiento: O(N), O(N log N), O(N²)

* Análisis empírico:
  * Ejecuta el algoritmo
  * Mide resultados concretos
  * Permite comparar implementaciones, constantes y casos particulares

---

# ¿Qué queremos medir?

Para cada corrida queremos registrar:

* Algoritmo utilizado
* Perfil de entrada
* Tamaño del set de datos
* Cantidad de comparaciones
* Cantidad de swaps/intercambios
* Tiempo de ejecución

```text
SorterType,Profile,Size,Swaps,Comparisons,Time
```

???

Esta es la estructura que después termina escrita en el CSV. El CSV no es el objetivo final; es el formato intermedio para poder abrirlo con planillas, scripts o herramientas de análisis.

---

# El riesgo de medir mal

Una forma rápida, pero pobre, sería meter contadores dentro de cada algoritmo:

```java
if (comparator.compare(a, b) < 0) {
    comparisons++;
}

swap(a, i, j);
swaps++;
```

Problemas:

* Repetimos lógica en todos los sorters
* Mezclamos ordenamiento con medición
* Es fácil olvidarse de contar alguna operación
* El sorter deja de tener una única responsabilidad

---

# Separación de responsabilidades

El sorter debería ordenar.

El benchmark debería medir.

El exportador debería escribir resultados.

```text
Sorter       -> ordena
Listener     -> observa operaciones
Execution    -> representa una corrida
CsvWriter    -> persiste resultados
```

???

Este es el punto central de diseño. Si mezclan todo en una clase, funciona para el TP chico, pero escala mal y se vuelve difícil de testear.

---

# Observable Pattern

El patrón Observable permite que un objeto notifique eventos sin conocer en detalle quién los consume.

```text
ObservableSorter
       |
       | notifica eventos
       v
SorterListener
```

El sorter emite eventos.

El listener decide qué hacer con esos eventos.

---

# Observable Pattern aplicado a sorting

Durante el ordenamiento ocurren operaciones observables:

* Comparar dos posiciones
* Intercambiar dos elementos
* Copiar entre estructuras auxiliares
* Marcar rangos o posiciones para visualización

El sorter no necesita saber si esos eventos se usan para:

* Dibujar una animación
* Contar operaciones
* Loguear pasos
* Construir métricas de benchmarking

---

# ObservableSorter

La idea de `ObservableSorter` es que un sorter pueda recibir listeners.

```java
public void addSorterListener(SorterListener listener)

public void removeSorterListener(SorterListener listener)
```

Entonces, antes de ejecutar una corrida, podemos conectar un listener que observe la ejecución.

???

No hace falta que todos los alumnos recuerden la firma exacta. Lo importante es el concepto: el sorter expone puntos de suscripción.

---

# SorterListener

Un listener reacciona ante eventos del sorter.

Eventos típicos:

```java
void greater(int i, int j)
void equals(int i, int j)
void swap(int i, int j)
void copy(int from, int to, boolean copyToAux)
void box(int from, int to)
```

Cada método representa algo que pasó durante la ejecución.

---

# AbstractSorter

Los sorters concretos no cuentan operaciones directamente.

Usan métodos auxiliares del `AbstractSorter`:

```java
less(i, j, list, comparator)
greater(i, j, list, comparator)
equals(i, j, list, comparator)
exch(i, j, list)
```

Esos métodos hacen dos cosas:

* Ejecutan la operación real
* Notifican a los listeners

---

# Ejemplo: comparación

```java
<T> boolean greater(int i, int j, List<T> list, Comparator<T> comparator) {
    for (SorterListener listener : listeners) {
        listener.greater(i, j);
    }
    return comparator.compare(list.get(i), list.get(j)) > 0;
}
```

La comparación sigue perteneciendo al sorter.

La medición queda delegada al listener.

---

# Ejemplo: swap

```java
<T> void exch(int i, int j, List<T> list) {
    final T t = list.set(i, list.get(j));
    list.set(j, t);

    for (SorterListener listener : listeners) {
        listener.swap(i, j);
    }
}
```

Cada intercambio puede ser observado sin modificar cada algoritmo concreto.

---

# Listener para benchmarking

Para benchmarkear, implementamos un listener que cuenta operaciones.

```java
public class SorterListenerImpl implements SorterListener {
    private int comps;
    private int swaps;

    public void greater(int i, int j) { comps++; }
    public void equals(int i, int j) { comps++; }
    public void swap(int i, int j) { swaps++; }
}
```

El listener no ordena.

Sólo cuenta lo que observa.

---

# ¿Por qué esto está bien diseñado?

Porque el mismo sorter puede usarse con distintos listeners:

```text
Sorter + Listener visual       -> animación
Sorter + Listener contador     -> benchmark
Sorter + Listener logger       -> debug
Sorter + sin listener          -> ejecución normal
```

No cambiamos el algoritmo para cambiar qué queremos observar.

???

Buen lugar para remarcar Open/Closed Principle: podemos agregar nuevas formas de observación sin modificar los sorters existentes.

---

# Benchmarking

Benchmarking significa ejecutar pruebas controladas para medir comportamiento.

Un benchmark básico necesita definir:

* Qué algoritmos se van a ejecutar
* Con qué tamaños de entrada
* Con qué perfiles de datos
* Cuántas veces se ejecuta cada caso
* Qué métricas se registran

---

# Perfil de entrada

El resultado de un sorter depende mucho de los datos.

Ejemplos de perfiles:

* Datos aleatorios
* Datos ya ordenados
* Datos ordenados inversamente
* Datos con muchos repetidos
* Datos casi ordenados

???

Insertion Sort, por ejemplo, puede comportarse muy bien con datos casi ordenados. Si sólo medimos random, nos perdemos parte de la historia.

---

# Tamaño de entrada

No alcanza con probar un solo N.

Queremos observar crecimiento:

```text
N = 100
N = 1.000
N = 10.000
N = 100.000
```

La pregunta no es sólo cuánto tarda.

La pregunta es cómo crece cuando crece N.

---

# Ejecución de una corrida

Una corrida debería seguir esta secuencia:

```text
1. Elegir sorter
2. Generar datos de entrada
3. Resetear contadores
4. Tomar tiempo inicial
5. Ejecutar sort
6. Tomar tiempo final
7. Guardar métricas
```

---

# Medición de tiempo

La clase `Execution` recibe el tiempo inicial:

```java
long start = System.currentTimeMillis();

sorter.sort(comparator, list);

Execution execution = new Execution(
    sorterType,
    profile,
    start,
    swaps,
    comps,
    size
);
```

Internamente calcula:

```java
time = System.currentTimeMillis() - start;
```

???

Para una práctica inicial está bien. Para benchmarking más serio, se puede discutir `System.nanoTime()`, warm-up de JVM y frameworks como JMH. Pero no conviene meter eso como primer paso si aún están aprendiendo la idea.

---

# Execution

`Execution` representa el resultado de una corrida.

```java
private final String sorterType;
private final String profile;
private final int size;
private final int swaps;
private final int comps;
private final long time;
```

Es una foto de lo que ocurrió en una ejecución puntual.

---

# Una fila del CSV

La salida de `Execution.toString()` genera una línea separada por comas:

```java
@Override
public String toString() {
    return sorterType + "," + profile + "," + size + "," +
           swaps + "," + comps + "," + time;
}
```

Ejemplo:

```text
INSERTION,Random,1000,240921,501203,18
```

---

# CsvWriter

El `CsvWriter` recibe muchas ejecuciones y las escribe en un archivo.

```java
CsvWriter.writeCsvFile("benchmark.csv", executions);
```

Estructura:

```text
header
execution 1
execution 2
execution 3
...
```

---

# Archivo CSV resultante

```csv
SorterType,Profile,Size,Swaps,Comparisons,Time
SELECTION,Random,1000,999,499500,12
INSERTION,Random,1000,251000,502000,18
QUICK,Random,1000,2400,12000,3
MERGE_TOP_DOWN,Random,1000,0,10000,4
```

Con esto después podemos:

* Abrirlo en una planilla
* Graficar curvas
* Comparar algoritmos
* Detectar casos malos o inesperados

---

# Flujo completo

```text
Datos de entrada
      |
      v
ObservableSorter -----> SorterListenerImpl
      |                         |
      |                         v
      |                  comparaciones / swaps
      v
Resultado ordenado
      |
      v
Execution
      |
      v
CsvWriter
      |
      v
benchmark.csv
```

---

# Ejemplo conceptual

```java
SorterListenerImpl listener = new SorterListenerImpl();
ObservableSorter observableSorter = (ObservableSorter) sorter;

observableSorter.addSorterListener(listener);

long start = System.currentTimeMillis();
sorter.sort(comparator, values);

Execution execution = new Execution(
    sorter.getType().toString(),
    profile,
    start,
    listener.getSwaps(),
    listener.getComps(),
    values.size()
);
```

---

# Importante: resetear

Si usamos el mismo listener para varias corridas, hay que limpiar los contadores.

```java
listener.reset();
```

Si no lo hacemos, las métricas quedan acumuladas y el benchmark queda mal.

???

Este es un error muy común. El CSV puede parecer correcto, pero los números son basura porque cada fila arrastra la anterior.

---

# Importante: no reutilizar datos ya ordenados

Si el mismo array/lista se usa en varias corridas, el primer sorter modifica la entrada.

```text
Corrida 1: recibe datos random
Corrida 2: recibe datos ya ordenados por la corrida 1
Corrida 3: recibe datos ya ordenados por la corrida 1
```

Eso invalida la comparación.

Cada sorter debe recibir una copia equivalente de la entrada original.

---

# Importante: comparar lo mismo

Para comparar algoritmos de forma justa:

* Mismo tamaño
* Mismo perfil
* Misma distribución de datos
* Mismo comparator
* Misma máquina, dentro de lo posible
* Misma forma de medir

Si cambia más de una variable, la comparación pierde valor.

---

# Comparator

El sorter no debería depender de un tipo concreto.

Por eso recibe un `Comparator<T>`.

Ejemplo con nombres:

```java
public int compare(FullName o1, FullName o2) {
    if (o1.getLastname().compareTo(o2.getLastname()) == 0) {
        return o1.getFirstname().compareTo(o2.getFirstname());
    }
    return o1.getLastname().compareTo(o2.getLastname());
}
```

Primero ordena por apellido.

Si hay empate, ordena por nombre.

---

# FullName

```java
public class FullName {
    private String firstname;
    private String lastname;

    public String toString() {
        return lastname + " " + firstname;
    }
}
```

El dato puede ser cualquier objeto.

Lo importante es que exista una regla clara de comparación.

---

# Qué esperamos que vean los alumnos

Al correr benchmarks, deberían aparecer patrones:

* Algoritmos cuadráticos crecen muy rápido
* Algoritmos O(N log N) escalan mejor
* Algunos algoritmos son mejores con entradas casi ordenadas
* Algunos algoritmos sufren con ciertos perfiles
* La cantidad de comparaciones y swaps ayuda a explicar el tiempo

---

# Cuidado con conclusiones falsas

Un benchmark chico puede mentir.

Ejemplos:

* Un algoritmo lento puede ganar para N pequeño
* La JVM puede afectar las primeras corridas
* El sistema operativo puede introducir ruido
* Medir una sola vez puede dar resultados accidentales
* El tiempo no explica todo si no miramos comparaciones y swaps

???

Acá conviene bajar expectativas: el benchmark de la práctica sirve para aprender y comparar tendencias, no para publicar resultados científicos.

---

# De datos a análisis

El CSV permite pasar de ejecución a análisis:

```text
benchmark.csv
      |
      v
Planilla / script / gráfico
      |
      v
Conclusiones
```

Ejemplos de gráficos útiles:

* Tiempo vs tamaño
* Comparaciones vs tamaño
* Swaps vs tamaño
* Comparación de sorters para un mismo perfil

---

# Lo que deberían implementar

Una práctica razonable debería permitir:

* Elegir varios sorters
* Generar datasets de distintos tamaños
* Ejecutar cada sorter con cada dataset
* Medir tiempo, comparaciones y swaps
* Guardar cada resultado como una fila de CSV
* Analizar los resultados obtenidos

---

# Checklist mental

Antes de confiar en el CSV:

* ¿Se reseteó el listener entre corridas?
* ¿Cada sorter recibió una copia limpia de los datos?
* ¿El comparator es consistente?
* ¿El tamaño registrado coincide con la entrada?
* ¿El perfil registrado describe correctamente los datos?
* ¿Las columnas del CSV son siempre las mismas?

---

# Cierre

La idea central no es solamente medir.

La idea central es diseñar bien la medición.

```text
Observable Pattern desacopla el algoritmo de la observación.
Benchmarking convierte ejecuciones en evidencia.
CSV permite conservar y analizar esa evidencia.
```

Un buen benchmark no reemplaza el análisis teórico.

Lo complementa.
