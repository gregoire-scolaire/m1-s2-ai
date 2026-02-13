# Mega pack - Exemples Python pour CSP

Objectif: avoir une banque de snippets prets a reutiliser pendant la revision et en exam.

## 0) Setup rapide

```python
# Si besoin
# pip install python-constraint

from constraint import Problem, AllDifferentConstraint, ExactSumConstraint
```

## 1) Python pur utile pour CSP (E01 -> E20)

### E01 - Copier une grille 2D proprement

```python
grid_copy = [row[:] for row in grid]
```

### E02 - Parcourir une grille avec indices

```python
for i in range(len(grid)):
    for j in range(len(grid[i])):
        print(i, j, grid[i][j])
```

### E03 - Detecter case vide Sudoku

```python
def find_empty(grid):
    for i in range(9):
        for j in range(9):
            if grid[i][j] == 0:
                return i, j
    return None
```

### E04 - Verifier unicite avec set

```python
def all_unique(values):
    vals = [v for v in values if v != 0]
    return len(vals) == len(set(vals))
```

### E05 - Extraire une ligne

```python
def get_row(grid, i):
    return grid[i]
```

### E06 - Extraire une colonne

```python
def get_col(grid, j):
    return [grid[i][j] for i in range(9)]
```

### E07 - Extraire un bloc 3x3

```python
def get_block(grid, bi, bj):
    # bi,bj: debut du bloc (0,3,6)
    return [grid[i][j] for i in range(bi, bi + 3) for j in range(bj, bj + 3)]
```

### E08 - Verifier validite locale Sudoku

```python
def is_valid(grid, row, col, val):
    if val in get_row(grid, row):
        return False
    if val in get_col(grid, col):
        return False
    bi, bj = (row // 3) * 3, (col // 3) * 3
    if val in get_block(grid, bi, bj):
        return False
    return True
```

### E09 - Backtracking Sudoku minimal

```python
def solve_sudoku_bt(grid):
    pos = find_empty(grid)
    if pos is None:
        return True

    r, c = pos
    for val in range(1, 10):
        if is_valid(grid, r, c, val):
            grid[r][c] = val
            if solve_sudoku_bt(grid):
                return True
            grid[r][c] = 0
    return False
```

### E10 - Compter occurences avec dict

```python
def count_values(values):
    counts = {}
    for v in values:
        counts[v] = counts.get(v, 0) + 1
    return counts
```

### E11 - Trier des variables par taille de domaine (MRV)

```python
variables = ["A", "B", "C"]
domains = {"A": [1, 2], "B": [1, 2, 3, 4], "C": [9]}
ordered = sorted(variables, key=lambda x: len(domains[x]))
print(ordered)  # ['C', 'A', 'B']
```

### E12 - Choisir variable non assignee (MRV)

```python
def select_unassigned_var(assignment, variables, domains):
    unassigned = [v for v in variables if v not in assignment]
    return min(unassigned, key=lambda v: len(domains[v]))
```

### E13 - Verifier contrainte binaire generic

```python
def consistent_pair(x, vx, y, vy, constraints):
    # constraints: dict de fonctions par paire (x,y)
    f = constraints.get((x, y))
    if f:
        return f(vx, vy)
    f = constraints.get((y, x))
    if f:
        return f(vy, vx)
    return True
```

### E14 - Forward checking simple

```python
def forward_check(var, value, domains, neighbors, constraints):
    new_domains = {k: list(v) for k, v in domains.items()}
    new_domains[var] = [value]

    for n in neighbors[var]:
        if n == var:
            continue
        filtered = []
        for nv in new_domains[n]:
            if consistent_pair(var, value, n, nv, constraints):
                filtered.append(nv)
        new_domains[n] = filtered
        if not filtered:
            return None
    return new_domains
```

### E15 - Recup voisins depuis aretes

```python
def build_neighbors(nodes, edges):
    nbrs = {n: set() for n in nodes}
    for a, b in edges:
        nbrs[a].add(b)
        nbrs[b].add(a)
    return nbrs
```

### E16 - Verification AllDifferent

```python
def all_different(*vals):
    return len(vals) == len(set(vals))
```

### E17 - Somme exacte

```python
def exact_sum(vals, total):
    return sum(vals) == total
```

### E18 - Au plus un vrai

```python
def at_most_one(bool_values):
    return sum(1 for b in bool_values if b) <= 1
```

### E19 - Produit cartesien (combinaisons)

```python
from itertools import product

for a, b, c in product([1, 2], [3, 4], [5, 6]):
    print(a, b, c)
```

### E20 - Mesurer temps d'execution

```python
import time

start = time.time()
# ... code solveur ...
elapsed = time.time() - start
print(f"temps: {elapsed:.4f}s")
```

## 2) CSP Python pur (E21 -> E30)

### E21 - Coloriage de graphe en backtracking

```python
nodes = ["A", "B", "C", "D"]
edges = [("A", "B"), ("A", "C"), ("B", "C"), ("C", "D")]
colors = ["R", "G", "B"]

neighbors = build_neighbors(nodes, edges)

def can_color(node, color, assign):
    return all(assign.get(n) != color for n in neighbors[node])

def solve_graph(assign=None):
    if assign is None:
        assign = {}
    if len(assign) == len(nodes):
        return assign

    node = next(n for n in nodes if n not in assign)
    for c in colors:
        if can_color(node, c, assign):
            assign[node] = c
            sol = solve_graph(assign)
            if sol:
                return sol
            del assign[node]
    return None

print(solve_graph())
```

### E22 - N-Queens backtracking

```python
def solve_n_queens(n=8):
    cols = set()
    diag1 = set()  # r-c
    diag2 = set()  # r+c
    pos = [-1] * n

    def bt(r):
        if r == n:
            return True
        for c in range(n):
            if c in cols or (r - c) in diag1 or (r + c) in diag2:
                continue
            cols.add(c)
            diag1.add(r - c)
            diag2.add(r + c)
            pos[r] = c
            if bt(r + 1):
                return True
            cols.remove(c)
            diag1.remove(r - c)
            diag2.remove(r + c)
            pos[r] = -1
        return False

    return pos if bt(0) else None

print(solve_n_queens(8))
```

### E23 - Affichage solution N-Queens

```python
def print_board(pos):
    n = len(pos)
    for r in range(n):
        row = ["."] * n
        row[pos[r]] = "Q"
        print(" ".join(row))

sol = solve_n_queens(8)
if sol:
    print_board(sol)
```

### E24 - Latin square 4x4 (backtracking)

```python
N = 4
grid = [[0] * N for _ in range(N)]

def ok(r, c, v):
    if v in grid[r]:
        return False
    if any(grid[i][c] == v for i in range(N)):
        return False
    return True

def bt(cell=0):
    if cell == N * N:
        return True
    r, c = divmod(cell, N)
    for v in range(1, N + 1):
        if ok(r, c, v):
            grid[r][c] = v
            if bt(cell + 1):
                return True
            grid[r][c] = 0
    return False

print(bt())
for row in grid:
    print(row)
```

### E25 - Topological-like contrainte ordre

```python
# A avant B avant C
order_slots = ["S1", "S2", "S3", "S4"]
pos = {s: i for i, s in enumerate(order_slots)}

def check_order(A, B, C):
    return pos[A] < pos[B] < pos[C]

print(check_order("S1", "S2", "S4"))
print(check_order("S2", "S1", "S4"))
```

### E26 - Capacite max par categorie

```python
def respect_capacity(assign, category, cap):
    return list(assign.values()).count(category) <= cap

assign = {"e1": "P1", "e2": "P1", "e3": "P2"}
print(respect_capacity(assign, "P1", 2))
print(respect_capacity(assign, "P1", 1))
```

### E27 - Verification complete d'une grille Sudoku

```python
def valid_sudoku_grid(grid):
    for i in range(9):
        if not all_unique(get_row(grid, i)):
            return False
        if not all_unique(get_col(grid, i)):
            return False

    for bi in [0, 3, 6]:
        for bj in [0, 3, 6]:
            if not all_unique(get_block(grid, bi, bj)):
                return False
    return True
```

### E28 - Generer paires de contraintes

```python
vars_ = ["x1", "x2", "x3", "x4"]
pairs = []
for i in range(len(vars_)):
    for j in range(i + 1, len(vars_)):
        pairs.append((vars_[i], vars_[j]))
print(pairs)
```

### E29 - Generation domaines avec comprehension

```python
domains = {f"X{i}": list(range(1, 6)) for i in range(1, 8)}
print(domains)
```

### E30 - Filtrer domaines selon contrainte unaire

```python
domains = {"A": [1, 2, 3, 4], "B": [1, 2, 3, 4]}
domains["A"] = [v for v in domains["A"] if v % 2 == 0]
print(domains)  # A garde seulement 2,4
```

## 3) python-constraint (E31 -> E60)

### E31 - Exemple de base

```python
from constraint import Problem

p = Problem()
p.addVariable("a", [1, 2, 3])
p.addVariable("b", [4, 5, 6])
print(p.getSolutions())
```

### E32 - Contrainte fonction simple

```python
p = Problem()
p.addVariables(["a", "b"], [1, 2, 3, 4, 5, 6])
p.addConstraint(lambda a, b: 2 * a == b, ("a", "b"))
print(p.getSolutions())
```

### E33 - AllDifferent a 3 variables

```python
from constraint import AllDifferentConstraint

p = Problem()
p.addVariables(["x", "y", "z"], [1, 2, 3])
p.addConstraint(AllDifferentConstraint(), ["x", "y", "z"])
print(p.getSolutions())
```

### E34 - Somme exacte

```python
from constraint import ExactSumConstraint

p = Problem()
p.addVariables(["p", "q", "r"], [0, 1, 2, 3, 4, 5])
p.addConstraint(ExactSumConstraint(7), ["p", "q", "r"])
print(p.getSolutions())
```

### E35 - getSolution (une seule)

```python
p = Problem()
p.addVariables(["u", "v"], [1, 2, 3])
p.addConstraint(lambda u, v: u < v, ("u", "v"))
print(p.getSolution())
```

### E36 - getSolutionIter

```python
p = Problem()
p.addVariables(["u", "v"], [1, 2, 3])
p.addConstraint(lambda u, v: u != v, ("u", "v"))
for sol in p.getSolutionIter():
    print(sol)
```

### E37 - Contrainte unaire (exclusion)

```python
p = Problem()
p.addVariables(["salle"], ["A101", "A102", "LAB"])
p.addConstraint(lambda salle: salle != "LAB", ["salle"])
print(p.getSolutions())
```

### E38 - Contraintes chainees

```python
p = Problem()
p.addVariables(["A", "B", "C"], [1, 2, 3, 4, 5])
p.addConstraint(lambda A, B: A < B, ("A", "B"))
p.addConstraint(lambda B, C: B < C, ("B", "C"))
print(p.getSolutions())
```

### E39 - Variables tuplees (grille)

```python
p = Problem()
for i in range(2):
    for j in range(2):
        p.addVariable((i, j), [1, 2])

p.addConstraint(lambda a, b: a != b, ((0, 0), (0, 1)))
print(p.getSolutions())
```

### E40 - AllDifferent sur ligne 3 cases

```python
p = Problem()
for j in range(3):
    p.addVariable((0, j), [1, 2, 3])
p.addConstraint(AllDifferentConstraint(), [(0, 0), (0, 1), (0, 2)])
print(p.getSolutions())
```

### E41 - Coloriage de graphe

```python
nodes = ["A", "B", "C", "D"]
edges = [("A", "B"), ("A", "C"), ("B", "C"), ("C", "D")]

p = Problem()
p.addVariables(nodes, ["R", "G", "B"])
for x, y in edges:
    p.addConstraint(lambda cx, cy: cx != cy, (x, y))

print(p.getSolution())
```

### E42 - Planning 3 cours 3 creneaux

```python
slots = ["LUN_AM", "LUN_PM", "MAR_AM"]

p = Problem()
p.addVariables(["math", "ia", "bd"], slots)
p.addConstraint(AllDifferentConstraint(), ["math", "ia", "bd"])
p.addConstraint(lambda math, ia: slots.index(math) < slots.index(ia), ("math", "ia"))

print(p.getSolutions())
```

### E43 - Affectation employe-poste

```python
employees = ["e1", "e2", "e3"]
roles = ["dev", "qa", "ops"]

p = Problem()
p.addVariables(employees, roles)
p.addConstraint(AllDifferentConstraint(), employees)
p.addConstraint(lambda e1: e1 != "ops", ["e1"])
print(p.getSolutions())
```

### E44 - Bin packing mini (somme <= capacite)

```python
from constraint import MaxSumConstraint

p = Problem()
p.addVariables(["a", "b", "c"], [1, 2, 3, 4, 5])
p.addConstraint(MaxSumConstraint(8), ["a", "b", "c"])
print(p.getSolutions())
```

### E45 - MinSumConstraint

```python
from constraint import MinSumConstraint

p = Problem()
p.addVariables(["x", "y", "z"], [0, 1, 2, 3])
p.addConstraint(MinSumConstraint(5), ["x", "y", "z"])
print(p.getSolutions())
```

### E46 - InSetConstraint

```python
from constraint import InSetConstraint

p = Problem()
p.addVariables(["h1", "h2"], [8, 10, 12, 14, 16])
p.addConstraint(InSetConstraint([10, 12]), ["h1"])
print(p.getSolutions())
```

### E47 - NotInSetConstraint

```python
from constraint import NotInSetConstraint

p = Problem()
p.addVariables(["h1", "h2"], [8, 10, 12, 14, 16])
p.addConstraint(NotInSetConstraint([8, 16]), ["h2"])
print(p.getSolutions())
```

### E48 - AllEqualConstraint

```python
from constraint import AllEqualConstraint

p = Problem()
p.addVariables(["x", "y", "z"], [1, 2, 3])
p.addConstraint(AllEqualConstraint(), ["x", "y", "z"])
print(p.getSolutions())  # (1,1,1), (2,2,2), (3,3,3)
```

### E49 - SomeInSetConstraint

```python
from constraint import SomeInSetConstraint

p = Problem()
p.addVariables(["m1", "m2", "m3", "m4"], ["linux", "mac", "win"])
p.addConstraint(SomeInSetConstraint(["linux"], n=2), ["m1", "m2", "m3", "m4"])
print(p.getSolutions())
```

### E50 - SomeNotInSetConstraint

```python
from constraint import SomeNotInSetConstraint

p = Problem()
p.addVariables(["u1", "u2", "u3"], ["A", "B"])
p.addConstraint(SomeNotInSetConstraint(["A"], n=1), ["u1", "u2", "u3"])
print(p.getSolutions())
```

### E51 - Variables numeriques + relation

```python
p = Problem()
p.addVariables(["x", "y", "z"], range(10))
p.addConstraint(lambda x, y, z: x + y == z, ("x", "y", "z"))
p.addConstraint(lambda x: x != 0, ["x"])
print(p.getSolution())
```

### E52 - Chiffres distincts equation

```python
p = Problem()
p.addVariables(["a", "b", "c", "d"], range(10))
p.addConstraint(AllDifferentConstraint(), ["a", "b", "c", "d"])
p.addConstraint(lambda a, b, c, d: 10 * a + b + 10 * c + d == 55, ("a", "b", "c", "d"))
print(p.getSolutions())
```

### E53 - N-Queens avec python-constraint

```python
n = 8
vars_q = [f"q{i}" for i in range(n)]

p = Problem()
p.addVariables(vars_q, range(n))
p.addConstraint(AllDifferentConstraint(), vars_q)

for i in range(n):
    for j in range(i + 1, n):
        p.addConstraint(lambda qi, qj, i=i, j=j: abs(qi - qj) != abs(i - j), (f"q{i}", f"q{j}"))

print(p.getSolution())
```

### E54 - Sudoku 4x4

```python
grid = [
    [1, 0, 0, 4],
    [0, 0, 1, 0],
    [0, 1, 0, 0],
    [2, 0, 0, 3],
]

digits = [1, 2, 3, 4]
p = Problem()

for i in range(4):
    for j in range(4):
        dom = [grid[i][j]] if grid[i][j] else digits
        p.addVariable((i, j), dom)

for i in range(4):
    p.addConstraint(AllDifferentConstraint(), [(i, j) for j in range(4)])
for j in range(4):
    p.addConstraint(AllDifferentConstraint(), [(i, j) for i in range(4)])

for bi in [0, 2]:
    for bj in [0, 2]:
        block = [(bi + di, bj + dj) for di in range(2) for dj in range(2)]
        p.addConstraint(AllDifferentConstraint(), block)

print(p.getSolution())
```

### E55 - Sudoku 9x9 (fonction)

```python
def solve_sudoku_csp(grid):
    p = Problem()

    for i in range(9):
        for j in range(9):
            dom = [grid[i][j]] if grid[i][j] != 0 else list(range(1, 10))
            p.addVariable((i, j), dom)

    for i in range(9):
        p.addConstraint(AllDifferentConstraint(), [(i, j) for j in range(9)])

    for j in range(9):
        p.addConstraint(AllDifferentConstraint(), [(i, j) for i in range(9)])

    for bi in range(0, 9, 3):
        for bj in range(0, 9, 3):
            p.addConstraint(AllDifferentConstraint(), [(bi + di, bj + dj) for di in range(3) for dj in range(3)])

    sol = p.getSolution()
    if not sol:
        return None

    out = [[0] * 9 for _ in range(9)]
    for i in range(9):
        for j in range(9):
            out[i][j] = sol[(i, j)]
    return out
```

### E56 - Capacites par projet (compteur)

```python
students = ["s1", "s2", "s3", "s4"]
projects = ["P1", "P2", "P3"]

p = Problem()
p.addVariables(students, projects)

for pr in projects:
    p.addConstraint(lambda s1, s2, s3, s4, pr=pr: [s1, s2, s3, s4].count(pr) <= 2, students)

print(p.getSolutions())
```

### E57 - Eviter doublon de creneau

```python
p = Problem()
p.addVariables(["cours1", "cours2", "cours3"], ["c1", "c2", "c3", "c4"])
p.addConstraint(AllDifferentConstraint(), ["cours1", "cours2", "cours3"])
print(p.getSolution())
```

### E58 - Contrainte de distance

```python
p = Problem()
p.addVariables(["x", "y"], range(10))
p.addConstraint(lambda x, y: abs(x - y) >= 3, ("x", "y"))
print(p.getSolutions())
```

### E59 - Contrainte modulo

```python
p = Problem()
p.addVariables(["a", "b"], range(1, 20))
p.addConstraint(lambda a, b: (a + b) % 3 == 0, ("a", "b"))
print(p.getSolutions()[:10])
```

### E60 - Pipeline type exam

```python
from constraint import Problem, AllDifferentConstraint

def solve_exam_style():
    p = Problem()

    # 1) Variables + domaines
    p.addVariables(["A", "B", "C"], [1, 2, 3, 4, 5])

    # 2) Contraintes
    p.addConstraint(AllDifferentConstraint(), ["A", "B", "C"])
    p.addConstraint(lambda A, B: A < B, ("A", "B"))
    p.addConstraint(lambda B, C: B + C == 7, ("B", "C"))

    # 3) Resolution
    return p.getSolutions()

print(solve_exam_style())
```

## 4) Mini exercices a faire (sans corrige)

1. Modifier E42 pour imposer `bd` apres `ia`.
2. Modifier E53 pour afficher 3 solutions (pas une seule).
3. Ajouter une contrainte de capacite max 1 sur `P3` dans E56.
4. Transformer E21 pour retourner toutes les colorations.
5. Ajouter verification de grille invalide dans E55.
6. Refaire E22 avec MRV (choix ligne la plus contrainte).

## 5) Checklist anti-erreurs

- Bien passer les variables dans `addConstraint(..., vars)`.
- Si boucle + lambda, figer les indices: `i=i`, `j=j`.
- Utiliser `getSolution()` si une seule solution suffit.
- Restreindre les domaines au max des le debut.
- Tester un petit jeu de donnees avant le cas complet.
