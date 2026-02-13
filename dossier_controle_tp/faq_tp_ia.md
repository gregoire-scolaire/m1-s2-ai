# FAQ TP IA

## Recherche (BFS/DFS/Dijkstra/A*)

Q: Pourquoi mon algo boucle ?
R: Tu revisites des etats. Utilise un `set` de visites avec etats hashables.

Q: Pourquoi A* retourne un chemin non optimal ?
R: Verifie que ton heuristique est admissible et que tu geres bien `best_g`.

Q: Comment rendre un etat hashable ?
R: Convertis `list[list[int]]` en `tuple[tuple[int]]`.

Q: Pourquoi DFS echoue souvent sur le taquin ?
R: Il peut partir tres profond dans une mauvaise branche.

## Sudoku SAT/CSP

Q: SAT ou CSP en controle ?
R: Prends la methode que tu maitrises le mieux; garde un backtracking de secours.

Q: Pourquoi dupliquer les grilles avant benchmark ?
R: Pour comparer les solveurs sur la meme entree sans effets de bord.

Q: Mon solveur ne marche pas sans internet.
R: Entraine-toi avec le fallback local (backtracking) et prepare un environnement preinstalle.
