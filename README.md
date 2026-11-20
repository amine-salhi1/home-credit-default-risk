# Prédiction du risque de défaut de crédit (Home Credit)

Notebook unique : `credit.ipynb`.

Prédire si un client aura des difficultés de remboursement (307 511 demandes, 8,07 % de défauts), à partir de sa demande et de son historique de crédit (bureau de crédit externe, échéances de remboursement). Modèle : **LightGBM**, métrique : **ROC-AUC**.

## Démarche

1. **Baseline** : table principale + ratios métier (crédit/revenu, mensualité/revenu, ancienneté d'emploi/âge), correction de la valeur aberrante `DAYS_EMPLOYED = 365243`.
2. **Agrégation multi-tables** : une ligne par client (`groupby` + `agg`), puis jointure à gauche : nombre de crédits, dettes, retards et sous-paiements.
3. **Évaluation rigoureuse** : validation croisée stratifiée à 5 plis (early stopping sur une partie du train), comparaison appariée baseline / avancé, importance des variables par gain.

## Résultats

| Modèle | AUC, séparation 80/20 | AUC out-of-fold, validation croisée 5 plis | Écart-type entre plis |
|---|---|---|---|
| Baseline (table principale) | 0,7603 | 0,7575 | 0,0043 |
| + historique de crédit et de remboursement | **0,7690** | **0,7672** | 0,0028 |

- L'agrégation des tables d'historique apporte **+0,0098 ± 0,0017 d'AUC**, un gain **positif sur les 5 plis**.
- La validation croisée donne des scores un peu plus bas que la séparation unique, dont l'early stopping utilisait le jeu de validation : l'estimation initiale était légèrement optimiste.
- Importance par gain : les scores externes (`EXT_SOURCE_1/2/3`) dominent ; `ORGANIZATION_TYPE`, 1re en nombre de coupes, recule à la 3e place. Meilleure variable construite : le taux de retards de paiement (`INSTALL_LATE_PAYMENT_MEAN`).

## Lancer

Placer les CSV dans `data/` (voir `data/README.md`), `pip install -r requirements.txt`, puis exécuter `credit.ipynb`.
