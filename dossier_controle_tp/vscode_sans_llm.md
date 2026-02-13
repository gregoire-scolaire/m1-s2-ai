# VS Code utile en controle (sans LLM)

## Extensions recommandees (Jupyter + Python + conda)

1. `ms-python.python` (Python)
Raison: base obligatoire pour interpreter Python, selection du kernel, execution, debug.

2. `ms-python.vscode-pylance` (Pylance)
Raison: auto-completion rapide, verif statique, aide sur types/imports.

3. `ms-python.debugpy` (Python Debugger)
Raison: debug pas-a-pas (breakpoints) pour comprendre vite une erreur.

4. `ms-toolsai.jupyter` (Jupyter)
Raison: execution de notebooks, completions dans cellules, gestion kernels.

5. `ms-toolsai.jupyter-keymap` (Jupyter Keymap)
Raison: raccourcis plus proches de Notebook classique.

6. `ms-toolsai.jupyter-renderers` (Jupyter Renderers)
Raison: rendu propre de sorties riches (dataframes, tableaux, etc.).

7. `ms-python.vscode-python-envs` (Python Environments)
Raison: detection/selection facile des environnements, dont conda.

## Et Anaconda ?
Si ton flux est base sur conda, `Python Environments` suffit souvent.
Tu peux ensuite choisir ton env conda comme kernel du notebook.

## Reglages VS Code utiles pour l'auto-completion (sans IA)

Ajoute ces options dans `settings.json`:

```json
{
  "editor.quickSuggestions": {
    "other": true,
    "comments": false,
    "strings": true
  },
  "editor.suggestOnTriggerCharacters": true,
  "editor.tabCompletion": "on",
  "python.analysis.autoImportCompletions": true,
  "python.analysis.typeCheckingMode": "basic"
}
```

## Aides autorisees (sans LLM) pendant le controle

1. Snippets personnels
- Cree des snippets pour: BFS, DFS, A*, Sudoku backtracking, tableau de comparaison.

2. Raccourcis clavier utiles
- `Shift+Enter`: executer cellule et passer a la suivante.
- `Ctrl+Enter`: executer cellule sans changer.
- `Ctrl+Shift+P`: palette de commandes (kernel, format, restart).

3. Checklist de debug rapide
- Verifier kernel actif.
- Verifier env Python/conda selectionne.
- Relancer toutes les cellules de definitions (classes/fonctions).
- Verifier imports manquants.

4. Fiches locales ouvertes en onglets
- `dossier_controle_tp/algorithmes_recherche.md`
- `dossier_controle_tp/taquin_heuristiques.md`
- `dossier_controle_tp/sat_csp_sudoku.md`
- `dossier_controle_tp/faq_tp_ia.md`

## Commandes d'installation rapides (optionnel)

```bash
code --install-extension ms-python.python
code --install-extension ms-python.vscode-pylance
code --install-extension ms-python.debugpy
code --install-extension ms-toolsai.jupyter
code --install-extension ms-toolsai.jupyter-keymap
code --install-extension ms-toolsai.jupyter-renderers
code --install-extension ms-python.vscode-python-envs
```

## Sources officielles
- VS Code Jupyter notebooks: https://code.visualstudio.com/docs/datascience/jupyter-notebooks
- VS Code Python: https://code.visualstudio.com/docs/languages/python
- Python extension (Marketplace): https://marketplace.visualstudio.com/items?itemName=ms-python.python
- Pylance (Marketplace): https://marketplace.visualstudio.com/items?itemName=ms-python.vscode-pylance
- Python Debugger (Marketplace): https://marketplace.visualstudio.com/items?itemName=ms-python.debugpy
- Jupyter (Marketplace): https://marketplace.visualstudio.com/items?itemName=ms-toolsai.jupyter
- Jupyter Keymap (Marketplace): https://marketplace.visualstudio.com/items?itemName=ms-toolsai.jupyter-keymap
- Jupyter Renderers (Marketplace): https://marketplace.visualstudio.com/items?itemName=ms-toolsai.jupyter-renderers
