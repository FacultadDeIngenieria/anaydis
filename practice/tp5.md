---
title: Práctica 5
layout: practice
permalink: /practice/5
---

# Práctica 5: Searching

* Fecha: 13 de Septiembre, 2018
* Fecha de Entrega: 20 de Septiembre, 2018

## Ejercicio 1

En las librerías se encuentra una interfaz anaydis.search.Map (Es un subset modificado de la interfaz java.util.Map).

Este ADT representa lo que se conoce como “Diccionario” y en particular no soporta claves duplicadas ni nulas.
Debe implementar, respetando la documentación, la interfaz Map con las siguientes estructuras subyacentes:

* ArrayMap: utilizando listas con búsqueda binaria.
* RandomizedTreeMap: utilizando un árbol probabilístico.

## Ejercicio 2

Utilizar el Map para contar la cantidad de ocurrencias de las primeras N palabras del Quijote.
El mapa deberá tener como key las palabras, y como value un Integer con la cantidad de ocurrencias.
Luego de generado el mapa, efectuar N búsquedas de palabras y medir el tiempo total de ejecución (no de cada búsqueda, sino de las N).
Realizar esto para ambas implementaciones de Map, tabular los tiempos y comparar.
Como se desea analizar el peor caso, es importante que la mayoría de las búsquedas sean “misses”. Para ello, luego de construir el mapa con N palabras, tomar esas palabras e invertirlas (ej: “cantar” -> “ratnac”). Tomar este nuevo set de N palabras y utilizarlo para realizar las búsquedas.
Realizar esto para N = {5000, 50000, 100000, 150000, 200000}.

NOTAS:
Recordar que el Map no acepta claves repetidas, ni nulas.

Es importante leer la documentación (javadoc) de la interfaz Map antes de implementarla.

Para realizar las búsquedas deben utilizarse las palabras invertidas. Se recomienda, para disminuir el tiempo de los experimentos y para no repetir trabajo, generar un archivo con las primeras 200000 palabras del Quijote invertidas. De esta forma utilizaremos un archivo para hacer la construcción del índice y otro para buscar sobre éste.
