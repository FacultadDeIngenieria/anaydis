---
title: Práctica 6
layout: practice
permalink: /practice/6
---

# Práctica 6: Searching - Tries

* Fecha: 16 de septiembre, 2021
* Fecha de Entrega: 23 de septiembre, 2021

## Ejercicio 1

Usando la interfaz anaydis.search.Map (subset modificado de la interfaz java.util.Map) utilizada en la práctica pasada debe implementar, respetando la documentación, la interfaz Map con la siguiente estructura subyacente:
RWayTrieMap (usando R-Way Trie internamente).

NOTA: Cómo punto bonus realizar una de estas dos implementaciones TSTTrieMap (usando TST Trie) o BinaryTrieMap (usando Binary Trie).

## Ejercicio 2

Efectuar las mismas pruebas de la práctica anterior para N = {5000, 50000, 100000, 150000, 200000}. Sacar conclusiones.

NOTAS:
Recordar que el Map no acepta claves repetidas, ni nulas.

Es importante leer la documentación (javadoc) de la interfaz Map antes de implementarla.

Para realizar las búsquedas deben utilizarse las palabras invertidas. Se recomienda, para disminuir el tiempo de los experimentos y para no repetir trabajo, generar un archivo con las primeras 200000 palabras del Quijote invertidas. De esta forma utilizaremos un archivo para hacer la construcción del índice y otro para buscar sobre éste.
