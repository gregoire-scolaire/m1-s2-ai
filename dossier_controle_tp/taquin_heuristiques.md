# Taquin - heuristiques utiles

## Qu'est-ce qu'une heuristique ?
Une heuristique `h(n)` est une fonction qui estime le cout restant entre un etat `n` et l'objectif.
Elle sert a guider la recherche informee (surtout A*) vers les etats les plus prometteurs.

### Pourquoi c'est utile
- Sans heuristique, A* devient Dijkstra (`h(n)=0`) et explore plus.
- Avec une bonne heuristique, on reduit le nombre de noeuds explores.

### Deux proprietes importantes
- Admissible: ne surestime jamais le vrai cout restant.
- Consistante: pour chaque transition `n -> n'`, `h(n) <= c(n,n') + h(n')`.

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

## Conseils examen
- Si on te demande une heuristique simple: commence par H1.
- Si on te demande de meilleures performances: utilise H2 (Manhattan).
- Si on te demande la justification theorique: mentionne "admissible" et "consistante".
