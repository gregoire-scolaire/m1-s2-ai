# Guide complet - CSP (Constraint Satisfaction Problems)

## 1) Definition courte

Un CSP est defini par:
- un ensemble de variables `X = {X1, X2, ...}`,
- un domaine pour chaque variable `Di`,
- un ensemble de contraintes `C` entre variables.

Objectif: trouver une affectation complete qui respecte toutes les contraintes.

Forme canonique:
- CSP = `(X, D, C)`

## 2) Vocabulaire a connaitre

- Variable: ce qu'on cherche a affecter.
- Domaine: valeurs possibles de la variable.
- Contrainte unaire: porte sur 1 variable.
- Contrainte binaire: porte sur 2 variables.
- Contrainte n-aire: porte sur n variables.
- Affectation partielle: certaines variables seulement.
- Affectation complete: toutes les variables ont une valeur.
- Consistante: aucune contrainte violee.

## 3) Methode de modelisation (tres importante)

1. Lire l'enonce et lister les objets a decider.
2. Transformer ces objets en variables.
3. Definir un domaine minimal pour chaque variable.
4. Ecrire les contraintes en phrases simples.
5. Traduire ces phrases en contraintes formelles/code.
6. Tester sur un mini cas.
7. Raffiner les domaines/contraintes si trop de solutions.

Regle d'or:
- Bon modele = domaines petits + contraintes claires.

## 4) Resolution CSP (idee algo)

## Backtracking

Principe:
- choisir une variable non affectee,
- tester une valeur,
- verifier la coherence locale,
- continuer recursivement,
- revenir en arriere si blocage.

Pseudo-code:

```text
BACKTRACK(assignation):
  if complete(assignation): return assignation
  X <- choisir_variable_non_assignee()
  for v in ordre_valeurs(X):
      if coherent(X=v, assignation):
          ajouter X=v
          result <- BACKTRACK(assignation)
          if result != echec: return result
          retirer X=v
  return echec
```

## Propagation

- Forward checking: apres affectation, on retire les valeurs impossibles des voisins.
- Arc consistency (AC-3): maintient coherence des arcs (binaire) pour couper l'espace de recherche.

## Heuristiques classiques

- MRV (Minimum Remaining Values): choisir la variable avec le plus petit domaine restant.
- Degree heuristic: en cas d'egalite, choisir la variable liee au plus de contraintes.
- LCV (Least Constraining Value): tester d'abord la valeur qui bloque le moins les autres.

## 5) Exemples CSP (conceptuels)

## Exemple A - Coloriage de carte

- Variables: regions.
- Domaines: couleurs.
- Contraintes: regions adjacentes de couleurs differentes.

## Exemple B - Emploi du temps

- Variables: cours.
- Domaines: creneaux horaires.
- Contraintes:
  - prof non double-booke,
  - salle unique par creneau,
  - prerequis temporels (cours A avant B).

## Exemple C - Affectation de projets

- Variables: etudiants.
- Domaines: sujets.
- Contraintes:
  - un sujet par etudiant,
  - capacite max par sujet,
  - exclusions de preference.

## Exemple D - Sudoku

- Variables: cases `(i, j)`.
- Domaines: `{1..9}` ou singleton si pre-rempli.
- Contraintes:
  - allDifferent sur chaque ligne,
  - allDifferent sur chaque colonne,
  - allDifferent sur chaque bloc 3x3.

## 6) Exemples CSP codes (python-constraint)

## Exemple 1 - CSP minimal

```python
from constraint import Problem

p = Problem()
p.addVariables(["x", "y"], [1, 2, 3, 4])
p.addConstraint(lambda x, y: x < y, ("x", "y"))
print(p.getSolutions())
```

## Exemple 2 - AllDifferent sur 4 variables

```python
from constraint import Problem, AllDifferentConstraint

p = Problem()
p.addVariables(["a", "b", "c", "d"], [1, 2, 3, 4])
p.addConstraint(AllDifferentConstraint(), ["a", "b", "c", "d"])
print(p.getSolutions())
```

## Exemple 3 - Sommes et inegalites

```python
from constraint import Problem, ExactSumConstraint

p = Problem()
p.addVariables(["p1", "p2", "p3"], range(6))
p.addConstraint(ExactSumConstraint(8), ["p1", "p2", "p3"])
p.addConstraint(lambda p1, p2: p1 >= p2, ("p1", "p2"))
print(p.getSolutions())
```

## Exemple 4 - Graphe non oriente

```python
from constraint import Problem

nodes = ["n1", "n2", "n3", "n4", "n5"]
edges = [("n1", "n2"), ("n1", "n3"), ("n2", "n4"), ("n3", "n4"), ("n4", "n5")]
colors = ["R", "V", "B"]

p = Problem()
p.addVariables(nodes, colors)
for u, v in edges:
    p.addConstraint(lambda cu, cv: cu != cv, (u, v))

print(p.getSolution())
```

## Exemple 5 - 4-Queens

```python
from constraint import Problem, AllDifferentConstraint

n = 4
p = Problem()
q = [f"q{i}" for i in range(n)]
p.addVariables(q, range(n))
p.addConstraint(AllDifferentConstraint(), q)

for i in range(n):
    for j in range(i + 1, n):
        p.addConstraint(lambda qi, qj, i=i, j=j: abs(qi - qj) != abs(i - j), (q[i], q[j]))

print(p.getSolutions())
```

## Exemple 6 - Mini planning avec ressources

```python
from constraint import Problem, AllDifferentConstraint

p = Problem()
p.addVariables(["taskA", "taskB", "taskC"], ["J1_AM", "J1_PM", "J2_AM"])
p.addConstraint(AllDifferentConstraint(), ["taskA", "taskB", "taskC"])

# taskB apres taskA
order = {"J1_AM": 0, "J1_PM": 1, "J2_AM": 2}
p.addConstraint(lambda a, b: order[b] > order[a], ("taskA", "taskB"))
print(p.getSolutions())
```

## Exemple 7 - Sudoku 9x9 (structure)

```python
from constraint import Problem, AllDifferentConstraint

def solve_sudoku_csp(grid):
    p = Problem()

    # variables + domaines
    for i in range(9):
        for j in range(9):
            dom = [grid[i][j]] if grid[i][j] != 0 else list(range(1, 10))
            p.addVariable((i, j), dom)

    # lignes
    for i in range(9):
        p.addConstraint(AllDifferentConstraint(), [(i, j) for j in range(9)])

    # colonnes
    for j in range(9):
        p.addConstraint(AllDifferentConstraint(), [(i, j) for i in range(9)])

    # blocs 3x3
    for bi in range(0, 9, 3):
        for bj in range(0, 9, 3):
            block = [(bi + di, bj + dj) for di in range(3) for dj in range(3)]
            p.addConstraint(AllDifferentConstraint(), block)

    sol = p.getSolution()
    if not sol:
        return None

    out = [[0] * 9 for _ in range(9)]
    for i in range(9):
        for j in range(9):
            out[i][j] = sol[(i, j)]
    return out
```

## Exemple 8 - Affectation equipes/projets

```python
from constraint import Problem

etudiants = ["e1", "e2", "e3", "e4"]
projets = ["P1", "P2", "P3"]

p = Problem()
p.addVariables(etudiants, projets)

# capacite max 2 sur chaque projet
p.addConstraint(lambda e1, e2, e3, e4: [e1, e2, e3, e4].count("P1") <= 2, etudiants)
p.addConstraint(lambda e1, e2, e3, e4: [e1, e2, e3, e4].count("P2") <= 2, etudiants)
p.addConstraint(lambda e1, e2, e3, e4: [e1, e2, e3, e4].count("P3") <= 2, etudiants)

# preferences
p.addConstraint(lambda e1: e1 != "P3", ["e1"])
p.addConstraint(lambda e4: e4 in ["P2", "P3"], ["e4"])

print(p.getSolutions())
```

## Exemple 9 - Horaires avec indisponibilites

```python
from constraint import Problem, NotInSetConstraint

p = Problem()
slots = ["LUN_8", "LUN_10", "MAR_8", "MAR_10"]
p.addVariables(["algo", "reseau", "ia"], slots)

p.addConstraint(NotInSetConstraint(["LUN_8"]), ["ia"])  # IA indispo lundi 8
p.addConstraint(lambda algo, reseau, ia: len({algo, reseau, ia}) == 3, ("algo", "reseau", "ia"))
print(p.getSolutions())
```

## Exemple 10 - Chiffres distincts + equation

```python
from constraint import Problem, AllDifferentConstraint

p = Problem()
p.addVariables(["a", "b", "c"], range(10))
p.addConstraint(AllDifferentConstraint(), ["a", "b", "c"])
p.addConstraint(lambda a, b, c: a + b == c, ("a", "b", "c"))
p.addConstraint(lambda a: a != 0, ["a"])
print(p.getSolutions())
```

## 7) SAT vs CSP (resume utile exam)

- SAT:
  - modelisation en logique propositionnelle (CNF),
  - solveurs tres optimises (Glucose, etc.),
  - encodage parfois long.

- CSP:
  - modelisation plus naturelle (variables/domaines/contraintes),
  - tres pedagogique et lisible,
  - souvent plus rapide a ecrire en controle.

En pratique:
- si enonce proche Sudoku/planning/affectation, CSP est souvent le plus direct.

## 8) Check-list de rendu (anti-points perdus)

1. Les variables representent bien les bonnes entites.
2. Les domaines sont corrects et restreints.
3. Toutes les contraintes de l'enonce sont traduites.
4. Les noms de variables dans les `lambda` matchent bien les tuples passes.
5. Tu verifies au moins un test minimal.
6. Tu sais expliquer ton modele en 4 phrases claires.

## 9) Erreurs classiques en exam

- Confondre objet du monde reel et variable CSP.
- Domaines trop larges.
- Oublier une contrainte de non-collision.
- Mauvaise capture de variable de boucle dans `lambda`.
- Appeler `getSolutions()` alors qu'une seule solution etait demandee.

## 10) Mini template de redaction theorique

Si on te demande la modelisation "papier":

1. Variables:
   - `Xi` = ...
2. Domaines:
   - `D(Xi)` = ...
3. Contraintes:
   - `C1`: ...
   - `C2`: ...
4. Methode de resolution:
   - backtracking + (optionnel) propagation/heuristique.

Cette structure simple suffit souvent pour une reponse propre et complete.

## 11) Banque d'exemples supplementaires

Pour t'entrainer avec beaucoup plus de snippets Python, voir:
- `exam/EXEMPLES_PYTHON_CSP.md` (60 exemples, Python pur + python-constraint)
