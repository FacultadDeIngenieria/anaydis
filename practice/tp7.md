---
title: Práctica 7
layout: practice
permalink: /practice/7
---

# Práctica 7: Immutability

* Fecha: 20 de septiembre, 2022
* Fecha de Entrega: 6 de octubre, 2022

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
