# Prédiction du risque de défaut de crédit (Home Credit)

Notebook unique : `credit.ipynb`.

Prédire si un client aura des difficultés de remboursement (307 511 demandes, 8,07 % de défauts), à partir de sa demande et de son historique de crédit (bureau de crédit externe, échéances de remboursement). Modèle : **LightGBM**, métrique : **ROC-AUC**.

## Démarche

1. **Baseline** : table principale + ratios métier (crédit/revenu, mensualité/revenu, ancienneté d'emploi/âge), correction de la valeur aberrante `DAYS_EMPLOYED = 365243`.
2. **Agrégation multi-tables** : une ligne par client (`groupby` + `agg`), puis jointure à gauche : nombre de crédits, dettes, retards et sous-paiements.
3. **Évaluation rigoureuse** : validation croisée stratifiée à 5 plis (early stopping sur une partie du train), comparaison appariée baseline / avancé, importance des variables par gain.

## Résultats (séparation stratifiée 80/20)

| Modèle | ROC-AUC |
|---|---|
| Baseline | 0,7603 |
| + historique de crédit et de remboursement | **0,7690** |

<!-- Ajouter ici le tableau de la partie 3 (validation croisée) une fois le notebook relancé. -->

## Lancer

Placer les CSV dans `data/` (voir `data/README.md`), `pip install -r requirements.txt`, puis exécuter `credit.ipynb`.
