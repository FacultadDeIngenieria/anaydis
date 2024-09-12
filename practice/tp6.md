---
title: Práctica 6
layout: practice
permalink: /practice/6
---

# Práctica 6: Immutability

* Fecha: 12 de septiembre, 2023
* Fecha de Entrega: 11 de octubre, 2023

## Ejercicio 1

* Implementar una Linked List inmutable.
* La clase deberá llamarse `anaydis.immutable.Node` e implementar la interfaz `anaydis.immutable.List` presente en las librerías.

## Ejercicio 2

* Implementar una Banker’s Queue inmutable utilizando las Linked Lists definidas en el punto anterior.
* La clase deberá llamarse `anaydis.immutable.BankersQueue` e implementar la interfaz `anaydis.immutable.Queue`presente en las librerías.
* La clase `BankersQueue` deberá especificar un constructor publico que reciba como parámetro dos `(@NotNull List<T> in, @NotNull List<T> out)` listas immutables.

## Ejercicio 3

* Implementar un Binary Tree immutable. Al insertar un nodo siempre devuelve la referencia al nuevo root del tree.
* La clase deberá llamarse `anaydis.immutable.BinaryTree` e implementar la interfaz `anaydis.immutable.Map` presente en las librerías.
* La clase BinaryTree deberá especificar un constructor publico que reciba como parámetro un `Comparator<K>` para utilizar durante las comparaciones en la implementación.
