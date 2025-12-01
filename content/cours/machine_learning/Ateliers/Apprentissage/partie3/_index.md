+++
title = "Partie 3"
weight = 120
+++

## Passons à la phase d'apprentissage

Le problème que nous devons résoudre consiste à déterminer si, lors d'un combat, un Pokémon a de grandes chances de gagner.

Comme nous disposons de données d'apprentissage, nous sommes dans un cas de Machine Learning dit "supervisé" : la machine apprend en fonction de ce qu'on lui fournit en entrée.

Dans ce type de situation, deux grandes familles d'algorithmes s'offrent à nous : ceux dédiés à la **classification** et ceux dédiés à la **régression**. La classification regroupe les prédictions, tandis que la régression renvoie une valeur numérique.

Dans notre cas, nous devons prédire un pourcentage de victoire. C'est donc naturellement vers les algorithmes de régression présentés dans le chapitre *Principaux algorithmes du machine learning* que nous nous tournons :

- **La régression linéaire**
- **Les arbres de décisions**
- **Les forêts aléatoires**

### Découpage des observations en jeu d'apprentissage et jeu de tests

La première étape consiste à répartir les observations en un jeu d'apprentissage (train) et un jeu de tests (test). Pour cela, nous allons utiliser Scikit-Learn et préparer un script Python qui charge les données, supprime les valeurs manquantes et isole les features utiles.

![alt text](image-4.png)

```python
import pandas as pnd

# Chargement du dataset (séparateur 	)
dataset = pnd.read_csv("datas/dataset.csv", delimiter='	')

# Suppression des lignes contenant des valeurs manquantes
dataset = dataset.dropna(axis=0, how='any')
```

Nous extrayons ensuite les features (données **X**) :

```python
# POINTS_ATTAQUE;POINTS_DEFFENCE;POINTS_ATTAQUE_SPECIALE;
# POINT_DEFENSE_SPECIALE;POINTS_VITESSE;NOMBRE_GENERATIONS
X = dataset.iloc[:, 5:12].values
```

La variable à prédire (**Y**) correspond à la colonne `POURCENTAGE_DE_VICTOIRE` :

```python
# 17e colonne
y = dataset.iloc[:, 17].values
```

Autrement dit, nous apprenons à la machine à estimer le pourcentage de victoire d'un Pokémon à partir de ses points d'attaque (standards et spéciaux), de sa vitesse, de ses points de vie, de ses points de défense (standards et spéciaux) et de son nombre de générations. Nous définissons ainsi les **variables prédictives (X)** et la **variable à prédire (Y)**.

Nous découpions ensuite chaque variable en un jeu d'apprentissage (`X_APPRENTISSAGE`, `Y_APPRENTISSAGE`) et un jeu de validation (`X_VALIDATION`, `Y_VALIDATION`) à l'aide de `train_test_split` :

```python
from sklearn.model_selection import train_test_split

X_APPRENTISSAGE, X_VALIDATION, Y_APPRENTISSAGE, Y_VALIDATION = train_test_split(
    X,
    y,
    test_size=0.2,   # 20 % des observations pour le test
    random_state=0,
)
```

#### Jeu d'observations complet (extrait de 10 observations)

| NOM | POINTS_DE_VIE | NIVEAU_ATTAQUE | [...] | VITESSE | POURCENTAGE_DE_VICTOIRE |
|-----|---------------|----------------|-------|---------|-------------------------|
| Bulbizarre | 45 | 49 | [...] | 45 | 0.2781954887218045 |
| Herbizarre | 60 | 62 | [...] | 60 | 0.38016528925619836 |
| Florizarre | 80 | 82 | [...] | 80 | 0.6742424242424242 |
| Mega-Florizarre | 80 | 100 | [...] | 80 | 0.56 |
| Salameche | 39 | 52 | [...] | 65 | 0.49107142857142855 |
| Reptincel | 58 | 64 | [...] | 80 | 0.5423728813559322 |
| Dracaufeu | 78 | 84 | [...] | 100 | 0.8646616541353384 |
| Mega-Dracaufeu X | 78 | 130 | [...] | 100 | 0.8561151079136691 |
| Mega-Dracaufeu Y | 78 | 104 | [...] | 100 | 0.8444444444444444 |
| Carapuce | 44 | 48 | [...] | 43 | 0.1623931623931624 |

#### Découpage en variables explicatives (X) et variable expliquée (Y)

| X | | | | | Y |
|---|---|---|---|---|---|
| NOM | POINTS_DE_VIE | NIVEAU_ATTAQUE | [...] | VITESSE | POURCENTAGE_DE_VICTOIRE |
| Bulbizarre | 45 | 49 | [...] | 45 | 0.2781954887218045 |

#### Jeu d'apprentissage (80 %)

| X_APPRENTISSAGE | | | | | Y_APPRENTISSAGE |
|-----------------|---|---|---|---|-----------------|
| NOM | POINTS_DE_VIE | NIVEAU_ATTAQUE | [...] | VITESSE | POURCENTAGE_DE_VICTOIRE |
| Bulbizarre | 45 | 49 | [...] | 45 | 0.2781954887218045 |
| Herbizarre | 60 | 62 | [...] | 60 | 0.38016528925619836 |
| Florizarre | 80 | 82 | [...] | 80 | 0.6742424242424242 |
| Mega-Florizarre | 80 | 100 | [...] | 80 | 0.56 |
| Salameche | 39 | 52 | [...] | 65 | 0.49107142857142855 |
| Reptincel | 58 | 64 | [...] | 80 | 0.5423728813559322 |
| Dracaufeu | 78 | 84 | [...] | 100 | 0.8646616541353384 |
| Mega-Dracaufeu X | 78 | 130 | [...] | 100 | 0.8561151079136691 |

#### Jeu de validation (20 %)

| X_VALIDATION | | | | | Y_VALIDATION |
|--------------|---|---|---|---|--------------|
| NOM | POINTS_DE_VIE | NIVEAU_ATTAQUE | [...] | VITESSE | POURCENTAGE_DE_VICTOIRE |
| Mega-Dracaufeu Y | 78 | 104 | [...] | 100 | 0.8444444444444444 |
| Carapuce | 44 | 48 | [...] | 43 | 0.1623931623931624 |

### Algorithme de régression linéaire

Comme vu dans *Des statistiques pour comprendre les données*, la régression linéaire modélise la relation entre des variables prédictives et une variable cible. Dans notre cas :

- points de vie,
- niveaux d'attaque/défense (standards et spéciaux),
- vitesse,
- génération

permettent de prédire le **pourcentage de victoire**.

```python
from sklearn.metrics import r2_score
from sklearn.linear_model import LinearRegression

algorithme = LinearRegression()
algorithme.fit(X_APPRENTISSAGE, Y_APPRENTISSAGE)
predictions = algorithme.predict(X_VALIDATION)
precision = r2_score(Y_VALIDATION, predictions)

print(">> ----------- REGRESSION LINEAIRE -----------")
print(">> Precision = " + str(precision))
print("------------------------------------------")
```

```
>> ----------- REGRESSION LINEAIRE -----------
>> Precision = 0.9043488485570964
```

### L'arbre de décision appliqué à la régression

Attention à utiliser `DecisionTreeRegressor` plutôt que `DecisionTreeClassifier` :

```python
from sklearn.tree import DecisionTreeRegressor

algorithme = DecisionTreeRegressor()
algorithme.fit(X_APPRENTISSAGE, Y_APPRENTISSAGE)
predictions = algorithme.predict(X_VALIDATION)
precision = r2_score(Y_VALIDATION, predictions)

print(">> ----------- ARBRE DE DECISIONS -----------")
print(">> Precision = " + str(precision))
print("------------------------------------------")
```

```
>> ----------- ARBRE DE DECISIONS -----------
>> Precision = 0.8812980947158535
```

### La random forest

```python
from sklearn.ensemble import RandomForestRegressor

algorithme = RandomForestRegressor()
algorithme.fit(X_APPRENTISSAGE, Y_APPRENTISSAGE)
predictions = algorithme.predict(X_VALIDATION)
precision = r2_score(Y_VALIDATION, predictions)

print(">> ----------- FORETS ALEATOIRES -----------")
print(">> Precision = " + str(precision))
print("------------------------------------------")
```

```
>> ----------- FORETS ALEATOIRES -----------
>> Precision = 0.9328013851258918
```

Les algorithmes sont ici utilisés avec leurs paramètres par défaut. En les optimisant, on peut espérer de meilleures performances.

### Sauvegarde du modèle

Pour réutiliser un modèle sans relancer l'entraînement, nous pouvons le sérialiser avec `joblib` :

```python
import joblib

fichier = "modele/modele_pokemon.mod"
joblib.dump(algorithme, fichier)
```

---

## Phénomènes de surapprentissage et de sous-apprentissage

Nous voulons un modèle généralisable, c'est-à-dire capable de produire de bonnes prédictions sur des données inconnues.

- **Surapprentissage (overfitting)** : l'algorithme est trop adapté aux données d'apprentissage et se comporte mal sur les données de test.
- **Sous-apprentissage (underfitting)** : l'algorithme n'arrive pas à apprendre les liens entre variables et produit des prédictions faibles.

D'où l'intérêt de mesurer les scores sur les deux ensembles :

```python
predictions = algorithme.predict(X_VALIDATION)
precision_apprentissage = algorithme.score(X_APPRENTISSAGE, Y_APPRENTISSAGE)
precision_validation = r2_score(Y_VALIDATION, predictions)
```

Les réseaux de neurones sont particulièrement sensibles à ces phénomènes lorsqu'on multiplie les itérations.

---

## Utiliser le modèle dans une application

Nous pouvons maintenant créer un notebook `partie3_predictions.ipynb` qui charge le modèle et prédit le vainqueur d'un combat entre deux Pokémon issus du Pokédex.

```python
import csv
import joblib
```

### Recherche des informations d'un Pokémon

```python
def recherche_informations_pokemon(numero, pokedex):
    infos = []
    for pokemon in pokedex:
        if int(pokemon[0]) == numero:
            infos = [
                pokemon[0],  # Numéro
                pokemon[1],  # Nom
                pokemon[4],  # Points de vie
                pokemon[5],  # Attaque
                pokemon[6],  # Défense
                pokemon[7],  # Attaque spéciale
                pokemon[8],  # Défense spéciale
                pokemon[9],  # Vitesse
                pokemon[10], # Génération
            ]
            break
    return infos
```

| INFORMATION | INDICE DANS LE POKEDEX |
|-------------|------------------------|
| Numéro | 0 |
| Nom | 1 |
| Points de vie | 4 |
| Niveau d'attaque | 5 |
| Niveau de défense | 6 |
| Niveau d'attaque spéciale | 7 |
| Niveau de défense spéciale | 8 |
| Vitesse | 9 |
| Génération | 10 |

### Fonction de prédiction d'un combat

```python
def prediction(numero_pokemon1, numero_pokemon2, pokedex):
    pokemon1 = recherche_informations_pokemon(numero_pokemon1, pokedex)
    pokemon2 = recherche_informations_pokemon(numero_pokemon2, pokedex)

    modele = joblib.load("modele/modele_pokemon.mod")

    prediction_pokemon_1 = modele.predict([
        [
            pokemon1[2],
            pokemon1[3],
            pokemon1[4],
            pokemon1[5],
            pokemon1[6],
            pokemon1[7],
            pokemon1[8],
        ]
    ])

    prediction_pokemon_2 = modele.predict([
        [
            pokemon2[2],
            pokemon2[3],
            pokemon2[4],
            pokemon2[5],
            pokemon2[6],
            pokemon2[7],
            pokemon2[8],
        ]
    ])

    print(
        "COMBAT OPPOSANT : ("
        + str(numero_pokemon1)
        + ") "
        + pokemon1[1]
        + " vs ("
        + str(numero_pokemon2)
        + ") "
        + pokemon2[1]
    )
    print(" " + pokemon1[1] + " : " + str(prediction_pokemon_1[0]))
    print(" " + pokemon2[1] + " : " + str(prediction_pokemon_2[0]))
    print("")

    if prediction_pokemon_1 > prediction_pokemon_2:
        print(pokemon1[1].upper() + " EST LE VAINQUEUR !")
    else:
        print(pokemon2[1].upper() + " EST LE VAINQUEUR !")
```

### Chargement du Pokédex et exécution

```python
with open("datas/pokedex.csv", newline="") as csvfile:
    pokedex = csv.reader(csvfile)
    next(pokedex)  # Ignore l'en-tête
    prediction(368, 598, pokedex)
```

```
COMBAT OPPOSANT : (368) Mangriff vs (598) Crapustule
 Mangriff : 0.7008019808073136
 Crapustule : 0.5924562022360302

MANGRIFF EST LE VAINQUEUR !
```

---

## Fin du cas d'étude

Ce cas d'étude nous a permis :

1. de préparer des données pour un problème de Machine Learning supervisé ;
2. d'analyser les variables importantes et de formuler des hypothèses ;
3. de tester différents algorithmes de régression et de retenir un modèle fiable ;
4. d'intégrer ce modèle dans une application pour prédire le vainqueur d'un combat de Pokémon.

Retenez que la préparation et l'analyse des données constituent **la phase la plus importante** d'un projet de Machine Learning. Des données bien préparées garantissent de bonnes prédictions, et la vigilance face au surapprentissage comme au sous-apprentissage reste indispensable.
