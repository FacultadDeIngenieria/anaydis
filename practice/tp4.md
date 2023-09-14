---
title: Práctica 4
layout: practice
permalink: /practice/4
---

# Práctica 4: Searching

* Fecha: 24 de agosto, 2023
* Fecha de entrega: 15 de septiembre, 2023

## Ejercicio 1

Usando la interfaz anaydis.search.Map (subset modificado de la interfaz java.util.Map) debe implementar, respetando la documentación, la interfaz Map con la siguiente estructura subyacente:

- anaydis.search.RWayTrieMap (usando R-Way Trie internamente)
- anaydis.search.TSTTrieMap (usando TST Trie)
- anaydis.search.BinaryTrieMap (usando Binary Trie)

## Ejercicio 2

Utilizar el Map para contar la cantidad de ocurrencias de las primeras N palabras del Quijote.
El mapa deberá tener como key las palabras, y como value un Integer con la cantidad de ocurrencias.
Luego de generado el mapa, efectuar N búsquedas de palabras y medir el tiempo total de ejecución (no de cada búsqueda, sino de las N).
Realizar esto para las tres implementaciones de Map, tabular los tiempos y comparar.
Como se desea analizar el peor caso, es importante que la mayoría de las búsquedas sean “misses”. Para ello, luego de construir el mapa con N palabras, tomar esas palabras e invertirlas (ej: “cantar” -> “ratnac”). Tomar este nuevo set de N palabras y utilizarlo para realizar las búsquedas.
Realizar esto para N = {5000, 50000, 100000, 150000, 200000}.

NOTAS:
Recordar que el Map no acepta claves repetidas, ni nulas.

Es importante leer la documentación (javadoc) de la interfaz Map antes de implementarla.

Para realizar las búsquedas deben utilizarse las palabras invertidas. Se recomienda, para disminuir el tiempo de los experimentos y para no repetir trabajo, generar un archivo con las primeras 200000 palabras del Quijote invertidas. De esta forma utilizaremos un archivo para hacer la construcción del índice y otro para buscar sobre éste.
