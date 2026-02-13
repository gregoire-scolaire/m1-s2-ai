# Guide complet - Librairie `python-constraint`

## 1) A quoi sert la lib

`python-constraint` permet de resoudre des problemes de contraintes (CSP):
- on declare des variables,
- on donne un domaine a chaque variable,
- on ajoute des contraintes logiques,
- le solveur cherche une ou plusieurs affectations valides.

C'est une lib ideale pour les exos de:
- emploi du temps,
- affectation de ressources,
- Sudoku, N-Queens,
- coloriage de graphe,
- mini problemes de combinatoire.

## 2) Installation et import

```bash
pip install python-constraint
```

```python
from constraint import (
    Problem,
    AllDifferentConstraint,
    AllEqualConstraint,
    ExactSumConstraint,
    MaxSumConstraint,
    MinSumConstraint,
    InSetConstraint,
    NotInSetConstraint,
    SomeInSetConstraint,
    SomeNotInSetConstraint,
)
```

## 3) API essentielle a connaitre

### Classe `Problem`

```python
problem = Problem()
```

Methodes les plus utiles:
- `addVariable(name, domain)`
- `addVariables(names, domain)`
- `addConstraint(constraint, variables=None)`
- `getSolution()`
- `getSolutions()`
- `getSolutionIter()`

### Contraintes les plus utiles

- `AllDifferentConstraint()`: toutes les variables sont differentes.
- `AllEqualConstraint()`: toutes les variables sont egales.
- `ExactSumConstraint(total)`: somme exacte.
- `MaxSumConstraint(max_total)`: somme <= max_total.
- `MinSumConstraint(min_total)`: somme >= min_total.
- `InSetConstraint(values)`: variable dans un ensemble autorise.
- `NotInSetConstraint(values)`: variable hors d'un ensemble interdit.
- `SomeInSetConstraint(values, n=...)`: au moins `n` variables dans `values`.
- `SomeNotInSetConstraint(values, n=...)`: au moins `n` variables hors `values`.

Tu peux aussi passer une fonction Python:
```python
problem.addConstraint(lambda a, b: a < b, ("a", "b"))
```

## 4) Exemples (beaucoup)

## Exemple 1 - Base absolue

```python
from constraint import Problem

p = Problem()
p.addVariable("a", [1, 2, 3])
p.addVariable("b", [4, 5, 6])
print(p.getSolutions())
```

## Exemple 2 - Contrainte fonction

```python
from constraint import Problem

p = Problem()
p.addVariable("a", [1, 2, 3])
p.addVariable("b", [4, 5, 6])
p.addConstraint(lambda a, b: 2 * a == b, ("a", "b"))
print(p.getSolutions())  # [{'a': 2, 'b': 4}, {'a': 3, 'b': 6}]
```

## Exemple 3 - `addVariables` + `AllDifferentConstraint`

```python
from constraint import Problem, AllDifferentConstraint

p = Problem()
p.addVariables(["x", "y", "z"], [1, 2, 3])
p.addConstraint(AllDifferentConstraint(), ["x", "y", "z"])
print(p.getSolutions())
```

## Exemple 4 - Somme exacte

```python
from constraint import Problem, ExactSumConstraint

p = Problem()
p.addVariables(["a", "b", "c"], [0, 1, 2, 3, 4, 5])
p.addConstraint(ExactSumConstraint(7), ["a", "b", "c"])
print(p.getSolutions())
```

## Exemple 5 - Somme max

```python
from constraint import Problem, MaxSumConstraint

p = Problem()
p.addVariables(["cpu", "ram", "disk"], [1, 2, 3, 4, 5])
p.addConstraint(MaxSumConstraint(8), ["cpu", "ram", "disk"])
print(p.getSolution())  # une solution valide seulement
```

## Exemple 6 - Domaine force par `InSetConstraint`

```python
from constraint import Problem, InSetConstraint

p = Problem()
p.addVariables(["cours1", "cours2"], [8, 10, 12, 14, 16])
p.addConstraint(InSetConstraint([10, 12, 14]), ["cours1"])
p.addConstraint(InSetConstraint([8, 16]), ["cours2"])
print(p.getSolutions())
```

## Exemple 7 - Exclusion avec `NotInSetConstraint`

```python
from constraint import Problem, NotInSetConstraint

p = Problem()
p.addVariable("salle", ["A101", "A102", "B201", "LAB"])
p.addConstraint(NotInSetConstraint(["LAB"]), ["salle"])
print(p.getSolutions())
```

## Exemple 8 - `getSolution` vs `getSolutions` vs iterateur

```python
from constraint import Problem

p = Problem()
p.addVariables(["x", "y"], [1, 2, 3])
p.addConstraint(lambda x, y: x < y, ("x", "y"))

print("Une solution:", p.getSolution())
print("Toutes:", p.getSolutions())
print("Iterateur:")
for sol in p.getSolutionIter():
    print(sol)
```

## Exemple 9 - Assignation de binomes

```python
from constraint import Problem, AllDifferentConstraint

eleves = ["Alice", "Bob", "Chloe", "Dina"]
roles = ["dev", "test", "doc", "demo"]

p = Problem()
p.addVariables(eleves, roles)
p.addConstraint(AllDifferentConstraint(), eleves)
p.addConstraint(lambda Alice: Alice != "demo", ["Alice"])
p.addConstraint(lambda Bob: Bob in ["dev", "test"], ["Bob"])

for s in p.getSolutions()[:5]:
    print(s)
```

## Exemple 10 - Planning simple (matin/apres-midi)

```python
from constraint import Problem

p = Problem()
p.addVariables(["math", "ia", "bd"], ["LUN_AM", "LUN_PM", "MAR_AM", "MAR_PM"])

# IA apres Math
p.addConstraint(
    lambda math, ia: ["LUN_AM", "LUN_PM", "MAR_AM", "MAR_PM"].index(ia)
    > ["LUN_AM", "LUN_PM", "MAR_AM", "MAR_PM"].index(math),
    ("math", "ia"),
)

# BD pas en LUN_AM
p.addConstraint(lambda bd: bd != "LUN_AM", ["bd"])

# Tous sur des creneaux differents
p.addConstraint(lambda math, ia, bd: len({math, ia, bd}) == 3, ("math", "ia", "bd"))

print(p.getSolutions())
```

## Exemple 11 - Coloriage de graphe

```python
from constraint import Problem

regions = ["A", "B", "C", "D"]
colors = ["rouge", "vert", "bleu"]
edges = [("A", "B"), ("A", "C"), ("B", "C"), ("C", "D")]

p = Problem()
p.addVariables(regions, colors)
for u, v in edges:
    p.addConstraint(lambda x, y: x != y, (u, v))

print(p.getSolution())
```

## Exemple 12 - N-Queens (8 reines)

```python
from constraint import Problem, AllDifferentConstraint

n = 8
p = Problem()

# qi = colonne de la reine sur la ligne i
vars_q = [f"q{i}" for i in range(n)]
p.addVariables(vars_q, range(n))
p.addConstraint(AllDifferentConstraint(), vars_q)  # pas meme colonne

for i in range(n):
    for j in range(i + 1, n):
        p.addConstraint(
            lambda qi, qj, i=i, j=j: abs(qi - qj) != abs(i - j),
            (f"q{i}", f"q{j}"),
        )

print(p.getSolution())
```

## Exemple 13 - Sudoku 4x4 (version courte)

```python
from constraint import Problem, AllDifferentConstraint

grid = [
    [1, 0, 0, 4],
    [0, 0, 1, 0],
    [0, 1, 0, 0],
    [2, 0, 0, 3],
]

p = Problem()
digits = [1, 2, 3, 4]

for i in range(4):
    for j in range(4):
        dom = [grid[i][j]] if grid[i][j] != 0 else digits
        p.addVariable((i, j), dom)

for i in range(4):
    p.addConstraint(AllDifferentConstraint(), [(i, j) for j in range(4)])
for j in range(4):
    p.addConstraint(AllDifferentConstraint(), [(i, j) for i in range(4)])

for bi in [0, 2]:
    for bj in [0, 2]:
        block = [(bi + di, bj + dj) for di in range(2) for dj in range(2)]
        p.addConstraint(AllDifferentConstraint(), block)

sol = p.getSolution()
print(sol)
```

## Exemple 14 - Mini cryptarithme (demo)

```python
from constraint import Problem, AllDifferentConstraint

p = Problem()
letters = ["S", "E", "N", "D", "M", "O", "R", "Y"]
p.addVariables(letters, range(10))
p.addConstraint(AllDifferentConstraint(), letters)

# pas de zero en tete
p.addConstraint(lambda S: S != 0, ["S"])
p.addConstraint(lambda M: M != 0, ["M"])

def valid(S, E, N, D, M, O, R, Y):
    send = 1000 * S + 100 * E + 10 * N + D
    more = 1000 * M + 100 * O + 10 * R + E
    money = 10000 * M + 1000 * O + 100 * N + 10 * E + Y
    return send + more == money

p.addConstraint(valid, letters)
print(p.getSolution())
```

## Exemple 15 - Contrainte "au moins n dans un ensemble"

```python
from constraint import Problem, SomeInSetConstraint

p = Problem()
p.addVariables(["u1", "u2", "u3", "u4"], ["linux", "mac", "windows"])
p.addConstraint(SomeInSetConstraint(["linux"], n=2), ["u1", "u2", "u3", "u4"])
print(p.getSolutions())
```

## 5) Template exam pret a l'emploi

```python
from constraint import Problem, AllDifferentConstraint

def solve_model():
    problem = Problem()

    # 1) Variables + domaines
    # problem.addVariable("X", [1,2,3])
    # problem.addVariables(["A","B","C"], range(10))

    # 2) Contraintes
    # problem.addConstraint(AllDifferentConstraint(), ["A","B","C"])
    # problem.addConstraint(lambda A, B: A < B, ("A", "B"))

    # 3) Resolution
    # return problem.getSolution()
    # return problem.getSolutions()
    pass
```

## 6) Erreurs frequentes (et correction)

- Oublier de passer les variables a `addConstraint`.
  - Mauvais: `problem.addConstraint(lambda a, b: a < b)`
  - Correct: `problem.addConstraint(lambda a, b: a < b, ("a", "b"))`

- Utiliser `getSolutions()` sur un gros probleme sans besoin.
  - Prendre `getSolution()` si une solution suffit.

- Oublier de figer `i` et `j` dans un `lambda` en boucle.
  - Utiliser `lambda x, y, i=i, j=j: ...`

- Domaine trop grand des le debut.
  - Restreindre le plus possible des la modelisation.

## 7) Strategie rapide pour l'exam

1. Ecrire les variables.
2. Donner les domaines minimaux.
3. Ajouter d'abord les contraintes "evidentes".
4. Tester `getSolution()` rapidement.
5. Ajouter les contraintes restantes.
6. Basculer vers `getSolutions()` uniquement si demande explicite.
