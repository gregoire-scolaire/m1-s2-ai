# Checklist controle TP (sans internet)

## Avant le controle
1. Verifier que Python 3 lance correctement.
2. Ouvrir tous les notebooks `_corriger.ipynb` une fois.
3. Tester une execution rapide des cellules clefs.
4. Verifier la presence de `pandas`, `tabulate` (si utilises).
5. Preparer le dossier `dossier_controle_tp` localement.

## Pendant le controle
1. Identifier d'abord le type de probleme (non informe, informe, SAT, CSP).
2. Reutiliser un squelette stable (Node, etat hashable, boucle principale).
3. Ajouter la metrique demandee (temps, noeuds explores, longueur chemin).
4. Verifier les cas limites (pas de solution, limite atteinte).
5. Documenter en 2-3 phrases la logique et les choix d'algorithme.

## Erreurs a eviter
- Oublier `visited`
- Modifier l'etat original par reference
- Confondre BFS (file) et DFS (pile)
- Oublier `g+h` dans A*
- Ignorer les contraintes de blocs 3x3 en Sudoku
