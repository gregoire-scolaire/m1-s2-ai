# Algorithmes de recherche - fiche complete

## 1. BFS (Breadth-First Search)

### Principe
BFS explore l'arbre niveau par niveau (profondeur croissante).

### Quand l'utiliser
- Cout uniforme des actions (chaque action vaut 1)
- Besoin du plus court chemin en nombre d'etapes

### Structures de donnees
- Frontiere: file FIFO (`deque`)
- Ensemble visite: `set`

### Pseudo-code
```text
mettre la racine dans la file
while file non vide:
    extraire en tete
    si objectif: retourner
    pour chaque successeur non visite:
        ajouter en queue
```

### Proprietes
- Complet: oui (si espace fini)
- Optimal: oui si couts unitaires
- Complexites: temps O(b^d), memoire O(b^d)

## 2. DFS (Depth-First Search)

### Principe
DFS plonge dans une branche jusqu'au bout avant de revenir en arriere.

### Quand l'utiliser
- Memoire limitee
- On cherche rapidement une solution, pas forcement la meilleure

### Structures de donnees
- Frontiere: pile LIFO
- Ensemble visite: `set`

### Proprietes
- Complet: pas toujours (sans borne de profondeur)
- Optimal: non
- Complexites: temps O(b^m), memoire O(bm)

## 3. Dijkstra

### Principe
Algorithme de plus court chemin pour graphes a couts positifs.
Il expand le noeud au plus petit cout cumule `g(n)`.

### Cas d'usage
- Couts non uniformes
- Pas d'heuristique fiable

### Lien avec A*
Dijkstra est un cas particulier de A* avec `h(n)=0`.

### Proprietes
- Complet: oui (couts positifs)
- Optimal: oui

## 4. A* (A-star)

### Principe
Selectionne le noeud minimisant:
`f(n) = g(n) + h(n)`
- `g(n)`: cout deja paye
- `h(n)`: estimation du cout restant

### Conditions pour l'optimalite
- `h` admissible (ne surestime jamais)
- idealement consistante

### Avantages
- Souvent beaucoup moins d'explorations que BFS/Dijkstra

### Pieges classiques
- Oublier de memoriser le meilleur cout `g` pour un etat
- Utiliser une heuristique non admissible (perte d'optimalite)
- Oublier les doublons dans la frontiere

## 5. Tableau recapitulatif

| Algo | Optimal | Memoire | Besoin heuristique |
|---|---|---|---|
| BFS | Oui (cout unitaire) | Forte | Non |
| DFS | Non | Faible | Non |
| Dijkstra | Oui | Moyenne/Forte | Non |
| A* | Oui (si h admissible) | Moyenne/Forte | Oui |
