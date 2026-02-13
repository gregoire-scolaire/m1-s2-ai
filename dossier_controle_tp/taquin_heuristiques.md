# Taquin - heuristiques utiles

## Etat but (3x3)
```text
1 2 3
4 5 6
7 8 0
```

## H1: tuiles mal placees
Compte le nombre de tuiles qui ne sont pas a la bonne position (sans compter 0).

### Avantages
- Tres simple
- Rapide a calculer

### Limites
- Peu informative: ne mesure pas la distance exacte des deplacements restants

## H2: distance de Manhattan
Pour chaque tuile, calcule:
`|ligne_actuelle - ligne_but| + |col_actuelle - col_but|`
Puis somme sur toutes les tuiles (hors 0).

### Avantages
- Plus informative que H1
- Admissible pour le taquin

### Consequence pratique
A* + H2 explore en general moins de noeuds que A* + H1.

## Pourquoi H2 domine souvent H1
H1: "faux ou vrai" (mauvaise place ou bonne place)
H2: "combien de mouvements minimum restent" tuile par tuile.
