class: center, middle, inverse

# Backtracking

---

# Introduction

* Problems where finding a solution with an efficient algorithm is impossible

.center[![]({{site.baseurl}}/presentation/backtracking/bt-intro.png)]

---

# Introduction

* We default in using brute-force, examining feasible solutions to a problem but...
  * systematically pruning infeasible ones
* **The Paradigm:**
  * When there are several possible choices, you make one choice and recurse
  * If that choice hits a dead end, you backtrack to the previous choice and pick the next available option
* **The Benefit of Pruning**
  * Identifying dead ends early allows you to "prune" the search tree
  * dramatically reducing execution time compared to pure brute-force exhaustive searches

---

# Example - N-queens problem

* Q. How many ways are there to place N queens on an N-by-N board so that no queen can attack any other?

.center[![]({{site.baseurl}}/presentation/backtracking/queens-representation.png)]

* **Challenge** Enumerate (or even count) the solutions

---

# Example - 4-queens

.center[![]({{site.baseurl}}/presentation/backtracking/4-queens.png)]

---

# Example - 4-queens

.center[![]({{site.baseurl}}/presentation/backtracking/4-queens-pruned.png)]

---

# Example - Diagonal conflict

* Checking for diagonal conflicts is the most critical step for "pruning" invalid paths
* Two queens placed at coordinates *(r_1, c_1)* and *(r_2, c_2)* are on the same diagonal if the absolute difference between their rows equals the absolute difference between their columns:
  * *|r_1 - r_2| = |c_1 - c_2|*

---