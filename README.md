# Validation croisée, résidus studentisés, distance de Cook (implémentation "à la main")

Modèles linéaires - Implémentation manuelle en R de la validation croisée leave-one-out, des points leviers, de la distance de Cook et des résidus studentisés par validation croisée, sans utiliser les fonctions R haut-niveau correspondantes, puis comparaison des résultats et des performances avec les fonctions natives de R.

## Le projet

Il se déroule en deux parties, appliquées à un jeu de données réel volumineux : les consommations électriques de **6291 entreprises françaises**, relevées toutes les 30 minutes pendant deux semaines par des compteurs Linky (672 variables).

### Partie A — Validation croisée leave-one-out

* Construction du modèle linéaire `y = Xβ + ε` : les variables explicatives sont les relevés de la première semaine, la variable à expliquer `y` est la consommation totale du lundi de la deuxième semaine
* **Méthode 1 (naïve)** : calcul des résidus de prévision `ε̂(i)` en ajustant **6291 modèles distincts**, chacun privé d'une observation — extrêmement coûteux (~3945 secondes d'exécution)
* **Méthode 2 (formule fermée)** : calcul des mêmes résidus via la relation `ε̂(i) = ε̂i / (1 - hii)`, à partir des résidus et des leviers (`hatvalues`) du seul modèle global — résultat strictement identique, en temps quasi instantané
* Discussion critique de la pertinence du modèle (résidus non homogènes, sensibilité aux entreprises aux habitudes de consommation atypiques)

### Partie B — Implémentation "à la main"

Sans utiliser `lm` ni les fonctions dédiées de R, implémentation directe à partir de la matrice de projection `Px = X(XᵀX)⁻¹Xᵀ` :

* **Matrice hat et leviers `hii`** : calcul par produits matriciels (`solve`, `crossprod`), comparé à `hatvalues()`
* **Valeurs ajustées et résidus d'estimation** : déduits de `Px`, comparés à `res$fitted.values` et `res$residuals`
* **Résidus par validation croisée** : réutilisation de la formule fermée de la partie A
* **Distance de Cook** : implémentation via la formule reposant sur les leviers et résidus du modèle global (évitant de recalculer *n* modèles), comparée à `cooks.distance()`
* **Résidus studentisés par validation croisée `t*ᵢ`** : démonstration mathématique complète permettant d'exprimer `σ̂(i)` sans ajuster *n* modèles séparés, implémentation et comparaison avec `rstudent()`

Pour chaque quantité, les résultats de l'implémentation manuelle sont validés par comparaison stricte (`all(round(...) == round(...))`) avec les fonctions R correspondantes, et les temps d'exécution sont systématiquement mesurés (`system.time`).

## Résultats principaux

* Les formules fermées (leviers, résidus de prévision, distance de Cook, résidus studentisés) donnent des résultats **rigoureusement identiques** aux fonctions R optimisées, mais évitent le calcul de *n* modèles séparés
* Le modèle linéaire simple montre ses limites : hétérogénéité des résidus, présence de nombreux points leviers et d'au moins une observation très influente (distance de Cook > 1)

## Structure du projet

* `TP3_BIS_Gambardello_Clara.pdf` : rapport complet avec code, résultats et graphiques
* `Sujet_TP3_BIS.pdf` : énoncé du TP

## Lancer l'analyse

1. Se procurer le jeu de données `smart278co.Rdata` (données de consommation électrique, non fourni dans ce dépôt pour des raisons de taille/licence)
2. Adapter le chemin de chargement du fichier dans le script
3. Exécuter le code R (fourni dans le rapport) dans RStudio

## Auteure

Clara GAMBARDELLO
Master 1 Modélisation Statistique - Modèles Linéaires (2024/2025)
