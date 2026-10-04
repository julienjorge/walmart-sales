# Walmart — prédire les ventes hebdomadaires

## Le projet

Le service marketing de Walmart veut estimer les ventes hebdomadaires de ses magasins à
partir d'indicateurs économiques et du calendrier, et comprendre quelles variables les
influencent.

Nous avons construit une régression linéaire, puis une version régularisée par Lasso pour
limiter le surapprentissage (un modèle qui colle trop à ses données d'entraînement et
généralise mal).

## Les données

Un fichier fourni par Jedha, dérivé d'une compétition Kaggle : 150 lignes, chacune décrivant
un magasin sur une semaine. La cible est le montant des ventes de la semaine. Les variables
explicatives sont le numéro du magasin, la date, un drapeau de semaine fériée, la
température, le prix du carburant, l'indice des prix (CPI) et le taux de chômage. Toutes les
colonnes sauf le magasin ont des valeurs manquantes.

## Comment ça marche

1. **Exploration** : valeurs manquantes, distribution des ventes, ventes par magasin et au
   fil du temps, variables économiques, corrélations.
2. **Preprocessing** : suppression des lignes sans cible ou sans date, variables année /
   mois / jour, filtrage des valeurs aberrantes à trois écarts-types, découpage train / test
   (80 / 20), puis imputation, standardisation et encodage du magasin en indicatrices.
3. **Modélisation** : régression linéaire de référence, lecture des coefficients, puis Lasso
   réglé par validation croisée.
4. **Conclusion** : résultats et limites.

Deux choix importants :

- **Garder le magasin comme variable.** C'est ce qui explique l'essentiel du niveau des
  ventes ; le risque de surapprentissage lié à ses 18 indicatrices est traité par le Lasso.
- **Supprimer les lignes sans date plutôt qu'imputer.** Le mois capte le pic de fin
  d'année ; inventer un mois aurait dégradé la seule variable temporelle utile.

## Les résultats

Le R² mesure la part de la variation des ventes expliquée par le modèle, entre 0 et 1.

| Modèle | R² entraînement | R² test |
|---|---|---|
| Régression linéaire | 0,973 | 0,940 |
| Lasso (alpha = 1 000) | 0,971 | 0,956 |

Le Lasso généralise mieux et écarte deux variables : le drapeau férié et l'année. Le
magasin domine la prédiction ; le mois est la seule autre variable de poids.

## Exécuter le projet

```
walmart/
├── 01-Walmart_sales.ipynb            notebook complet, avec ses sorties
├── data/raw/Walmart_Store_sales.csv  données d'origine
└── README.md
```

Python 3.13 avec pandas, plotly, scikit-learn et Jupyter. Aucune configuration : ouvrir le
notebook depuis la racine du dépôt et exécuter toutes les cellules.

## Limites

- **Très peu de données** : 113 lignes exploitées, dont 23 pour le test.
- **Fuite légère** : le filtrage à trois écarts-types a été calculé avant le découpage
  train / test. Les cinq lignes retirées sont très loin du seuil, l'effet est négligeable.
- **Un modèle qui connaît ses magasins** : il ne peut rien dire d'un magasin jamais vu.
- **Pas de prévision dans le temps** : le découpage est aléatoire, pas chronologique.
