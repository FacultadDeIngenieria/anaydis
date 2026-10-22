class: center, middle, inverse

# Criptografía: hands-on

## Diffie-Hellman en papel

---

# El desafío

* Alice y Bob quieren acordar una **clave secreta**.
* Sólo pueden comunicarse con **papelitos que todos ven**.
* Eve lee todo lo que pasa por el canal.

--

* ¿Se puede? Al final de la actividad, Alice y Bob van a tener el mismo número secreto... y vamos a ver si Eve también.

???

Duración total: entre 30 y 40 minutos (ronda 1: ~20, ronda 2: ~10, cierre: ~5).

Conviene hacerlo después de la slide de Diffie-Hellman de la teórica. Ese ejemplo usa p = 17 y g = 3; acá usamos p = 23 y g = 5 para que no haya respuestas a la vista.

---

# Armado

* Grupos de 3 o 4 personas. Tres roles:
  * **Alice**: grupos Alice 1, Alice 2, ...
  * **Bob**: grupos Bob 1, Bob 2, ... (Alice 1 habla con Bob 1, Alice 2 con Bob 2, ...)
  * **Eve**: uno o dos grupos.

--

* El **canal**: todos los papelitos pasan por Eve.
  * Eve los lee y anota lo que quiera.
  * En la ronda 1, Eve **no puede modificarlos**: sólo los entrega.

--

* Cada grupo necesita papel y una calculadora online (o Google): `7^5 mod 23` → 17.

???

Si queda un grupo impar, que se sume a Eve.

Anotar los pares en el pizarrón (Alice 1 ↔ Bob 1, ...) para que no haya dudas de a quién va cada papelito.

Eve conviene ubicarla físicamente "en el medio" del aula: los papelitos tienen que pasar por sus manos.

Ojo con la precisión: las cuentas del paso 4 llegan a números de hasta 29 dígitos (22²¹). Una calculadora que trabaja con punto flotante (double) da un resto equivocado en ~17% de los casos (ej: 19²¹ mod 23 da 11 en double; el correcto es 17). Probar antes de clase la calculadora que se va a usar con `19^21 mod 23`: tiene que dar 17. WolframAlpha calcula exacto.

---

# Parámetros públicos

* Todos, incluida Eve, conocen:

.center[
## p = 23
## g = 5
]

* **p**: un número primo. Todas las cuentas se hacen **módulo p**.
* **g**: la base.

--

* Recordatorio: **x mod 23** es el resto de dividir x por 23. Siempre da entre 0 y 22.

???

Escribir p = 23 y g = 5 en el pizarrón y dejarlos a la vista toda la clase.

---

# Paso 1: elegir el secreto

* **Alice**: elige un número secreto **a** entre 2 y 21.
* **Bob**: elige un número secreto **b** entre 2 y 21.

--

* Lo anotan y **no se lo muestran a nadie**: ni a Eve, ni al otro grupo.

--

* **Eve**: mientras tanto, arma una tabla con 5ˣ mod 23 para x = 1, 2, ..., 22.

```
x     │ 1  2  3  4  5 ...
5ˣ    │ 5  2 10  ...
```

???

Con la calculadora, la tabla le lleva a Eve unos minutos, más o menos lo mismo que a Alice y Bob los pasos 1 a 4.

Tabla completa, para controlar:

```
x   1  2  3  4  5  6  7  8  9 10 11 12 13 14 15 16 17 18 19 20 21 22
5ˣ  5  2 10  4 20  8 17 16 11  9 22 18 21 13 19  3 15  6  7 12 14  1
```

Aparecen todos los valores del 1 al 22 exactamente una vez: por eso 5 es un **generador** módulo 23.

---

# Paso 2: el valor público

* **Alice** calcula **A = 5ᵃ mod 23**.
* **Bob** calcula **B = 5ᵇ mod 23**.


???

Recorrer los grupos y ayudar a quien se trabe con el mod. Es la cuenta más fácil: siempre se multiplica por 5.

---

# Paso 3: el intercambio

* Cada grupo escribe su valor en un papelito y lo manda **por el canal** (pasando por Eve):

```
+----------------------+          +----------------------+
| De: Alice 1          |          | De: Bob 1            |
| Para: Bob 1          |          | Para: Alice 1        |
| A = ...              |          | B = ...              |
+----------------------+          +----------------------+
```

--

* **Eve**: anota todos los valores que ve pasar, pero en esta ronda los entrega sin cambiarlos.

---

# Paso 4: el secreto compartido

* **Alice** calcula **S = Bᵃ mod 23**, con el B que le mandó Bob y su propio **a**.
* **Bob** calcula **S = Aᵇ mod 23**, con el A que le mandó Alice y su propio **b**.


--

* **Eve**: ¿podés calcular el S de algún par con lo que anotaste?

???

Ejemplo para controlar: a = 4, b = 9.

* A = 5⁴ mod 23 = 4, B = 5⁹ mod 23 = 11.
* Alice: S = 11⁴ mod 23 = 13. Bob: S = 4⁹ mod 23 = 13.

Si un par no coincide, casi siempre es un error de cuentas en el paso 2: que recalculen A o B.

---

# Paso 5: usar el secreto

* Alice elige una palabra corta (hasta 5 letras), la cifra con **César con corrimiento S** y se la manda a Bob por el canal.

```
A  B  C  D  E  F  G  H  I  J  K  L  M  N  O  P  Q  R  S  T  U  V  W  X  Y  Z
0  1  2  3  4  5  6  7  8  9 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 25
```

* Ej: con S = 3, HOLA → KROD.

--

* Bob descifra corriendo S letras para atrás.
* **Eve**: intenta leer los mensajes.

???

Ejemplo con S = 13: PIZZA → CVMMN.

Acá S cumple el papel de la clave simétrica: en un sistema real sería la clave de AES, no un corrimiento de César.

---

# ¿Qué pasó?

* Primero, **Eve** dice qué mensajes leyó y cuál cree que es el S de cada par.

--

* Después, cada par revela su S. ¿Coincidieron Alice y Bob? ¿Acertó Eve?

--

* ¿Qué pasó por el canal? **p, g, A y B** (y el mensaje cifrado).
* ¿Qué nunca pasó por el canal? **a, b y S**.

???

Lo esperable: Eve acierta todos. Busca A en su tabla, encuentra a, y calcula S = Bᵃ mod 23 igual que Alice.

Preguntar a Eve cómo lo hizo antes de explicarlo.

---

# ¿Por qué coinciden?

* Alice calcula: Bᵃ = (5ᵇ)ᵃ = 5ᵃᵇ (mod 23)
* Bob calcula: Aᵇ = (5ᵃ)ᵇ = 5ᵃᵇ (mod 23)

--

* Los dos llegan a **5ᵃᵇ mod 23** sin que nadie haya mandado a ni b.
* Eve tiene 5ᵃ y 5ᵇ, pero multiplicarlos da 5ᵃ⁺ᵇ, no 5ᵃᵇ.

???

Que B ya esté reducido mod 23 no cambia el resultado: (x mod p)ᵃ mod p = xᵃ mod p, porque lo que se descartó es un múltiplo de p.

Si algún par tiene S = 1 o S = 22: alguno eligió 11. Como 5¹¹ mod 23 = 22 ≡ −1, cualquier potencia da 1 o 22. En la práctica se evitan esos valores.

---

# ¿Cómo ganó Eve?

* Eve vio A y buscó en su tabla qué exponente da A: así obtuvo **a**.
  * Es el **logaritmo discreto**: dado 5ᵃ mod p, encontrar a.
* Con a y B, calcula S = Bᵃ mod 23, igual que Alice.

--

* Con p = 23, la tabla tiene 22 filas.
* Con p de **2048 bits**, la tabla tendría del orden de 2²⁰⁴⁸ filas.
  * No se conoce un algoritmo eficiente para el logaritmo discreto.

--

* Para Alice y Bob, en cambio, calcular 5ᵃ mod p sigue siendo rápido aun con números de 2048 bits.

???

El punto clave: lo que hacen Alice y Bob (exponenciar) es fácil; lo que tiene que hacer Eve (deshacerlo) es difícil. Es la misma idea que la función trampa de RSA.

Hay algoritmos mejores que la tabla (baby-step giant-step, index calculus), pero ninguno alcanza para p de 2048 bits.

---

class: center, middle, inverse

# Ronda 2: Eve en el medio

---

# Ronda 2: Eve en el medio

* Mismos pares, **nuevos secretos** a y b.
* Ahora Eve puede **cambiar los papelitos**.

--

* Eve elige su propio secreto **e** y calcula **E = 5ᵉ mod 23**.
* Cuando pasa el papelito de Alice, Eve lo cambia por uno con **E**. Lo mismo con el de Bob.

```
 Alice ── A ──► [ Eve ] ── E ──► Bob
 Alice ◄── E ── [ Eve ] ◄── B ── Bob
```

--

* Alice y Bob repiten los pasos 1 a 5 sin saber qué hizo Eve.

???

Darle la consigna a Eve en voz baja, o por escrito, para que Alice y Bob no sepan que esta vez Eve modifica los papelitos. Si se lo dice en voz alta a toda la clase, igual funciona: Alice y Bob no tienen forma de detectarlo.

Eve tiene que reescribir los papelitos con la misma forma ("De: Alice 1, Para: Bob 1"): los nombres no prueban nada.

---

# Ronda 2: ¿qué pasó?

* Alice calcula S₁ = Eᵃ mod 23. Eve calcula el mismo: S₁ = Aᵉ mod 23.
* Bob calcula S₂ = Eᵇ mod 23. Eve calcula el mismo: S₂ = Bᵉ mod 23.

--

* Eve descifra el mensaje de Alice con S₁, lo lee, **lo vuelve a cifrar con S₂** y se lo pasa a Bob.
* Bob lo descifra sin problemas. Nadie notó nada.

--

* Esta vez Eve no necesitó la tabla: **no rompió nada**, sólo se hizo pasar por cada uno.

???

Ejemplo para controlar: a = 4, b = 9, e = 13.

* A = 4, B = 11, E = 21.
* S₁ = 21⁴ mod 23 = 16 = 4¹³ mod 23.
* S₂ = 21⁹ mod 23 = 17 = 11¹³ mod 23.
* PIZZA con S₁ = 16 → FYPPQ. Eve lo recifra con S₂ = 17 → GZQQR.

Preguntar: ¿cómo se hubieran dado cuenta Alice y Bob? Sólo comparando S por otro canal, que es justamente lo que no tienen.

---

# ¿Cómo se evita?

* Diffie-Hellman garantiza que **nadie que sólo escucha** obtiene la clave.
* No garantiza **con quién** acordé la clave: no autentica.

--

* Solución: que Bob **firme** su B con su clave privada (firma digital).
  * Alice verifica la firma con la clave pública de Bob.
  * Si Eve cambia B por E, la firma no verifica.

--

* ¿Y cómo sabe Alice cuál es la clave pública de Bob? **Certificados**.
* Es lo que pasa en cada conexión HTTPS: Diffie-Hellman + firma del servidor + certificado.

---

# Resumen

* Diffie-Hellman permite acordar una clave por un canal público.
* Su seguridad depende de que el **logaritmo discreto** sea difícil: con p = 23 no lo es; con p de 2048 bits sí.
* La clave acordada se usa después con un **cifrado simétrico** (AES, no César).
* Sin autenticación, un atacante en el medio hace un Diffie-Hellman con cada uno.
