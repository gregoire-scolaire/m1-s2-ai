# Sudoku - SAT et CSP

## 1. Modelisation SAT

### Variables
`X(i,j,k)` signifie: "la case (i,j) contient la valeur k".

### Contraintes
1. Chaque case a au moins une valeur.
2. Chaque case a au plus une valeur.
3. Chaque ligne contient 1..9.
4. Chaque colonne contient 1..9.
5. Chaque bloc 3x3 contient 1..9.
6. Les valeurs deja remplies sont forcees.

### Resolution
On donne la CNF a un solveur SAT (ex: Glucose3).

## 2. Modelisation CSP

### Variables
Chaque case est une variable.

### Domaines
- Case vide -> {1..9}
- Case pre-remplie -> {valeur fixee}

### Contraintes
`AllDifferent` sur:
- chaque ligne
- chaque colonne
- chaque bloc 3x3

## 3. SAT vs CSP

### SAT
- Tres performant avec solveurs modernes
- Necessite un encodage logique precis

### CSP
- Plus lisible pedagogiquement
- Tres naturel pour exprimer des contraintes

## 4. Plan B hors-ligne
Si bibliotheques non disponibles:
- utiliser un backtracking pur Python
- plus lent, mais autonome sans internet
