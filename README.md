# DEFT2013 Tâche 2 :

*Bourdere Andreou Nathan - Chupin Yannis*

Retrouvez le notebook au coeur de notre projet sur ce [lien](https://colab.research.google.com/drive/1MnukWfxglrTBuqy65Yd3l0IRLzC3CvEb#scrollTo=lfnRacezr8cU).

## Description de la tâche

Les documents ont été fournis dans deux CSVs. Une ligne désignait un document et pour chaque nous disposons des informations suivantes : 

```
doc_id,titre,type,difficulte,cout,ingredients,recette
```

Prennons l'exemple du premier document situé dans le *train.csv*:

```SQL
recette_221358.xml,
"Feuilleté de saumon et de poireau, sauce aux crevettes",
Plat principal,
Facile,
Moyen,
"- 1 gros pavé de saumon - 100 g de crevettes décortiquées - 2 poireaux moyens - 1 oignon  - 1 pâte feuilletée - 25 cl de crème liquide épaisse - Un peu de vin blanc - 1/2 citron jaune - 1 jaune d'oeuf - Un peu d'huile d'olive  - Une noisette de beurre - Sel, aneth",
"Couper finement le blanc et un peu de vert des poireaux en rondelle. Éplucher et couper l'oignon. Faire chauffer l'huile d'olive et le beurre dans une poêle. Y faire revenir à feu doux les poireaux et l'oignon environ 15 minutes. En fin de cuisson ajouter le vin blanc, un peu d'aneth et du sel. Pendant ce temps faire cuire le saumon dans une poêle huilée. En fin de cuisson le faire griller sur feu vif. Préparer la sauce : mélanger les crevettes, le 1/2 citron jaune, la crème, le sel et l'aneth. Ajouter un peu de sauce à la préparation des poireaux. Préchauffer le four à 200°C (thermostat 6-7.Étaler la pâte feuilletée et la couper en 2. Sur une moitié disposer la préparation au poireaux. Disposer le saumon sur les poireaux. Y verser un peu de sauce sur le dessus (garder le reste de sauce au frigo). Refermer le chausson à l'aide de l'autre moitié de la pâte feuilleté. Bien appuyer sur les bords. Étaler le jaune d’œuf sur le dessus à l'aide d'un pinceau. Enfourner environ 20 minutes. Avant de servir chauffer la sauce et la servir à côté du feuilleté.On peut servir ce feuilleté avec une salade verte."
```

On remarque que le *doc_id* nous permet déjà d'identifier le document (la recette) de manière unique. Ensuite, notre objectif est de classifier des recettes de manière à savoir si c'est **une entrée**, un **plat principal** ou **un dessert**. Le *type* est donc l'information cruciale, l'attribut $y$, que nous devons séparer du reste des attributs pour l'entrainement.

Pour ce qui est du classifieur, les informations pertinentes à son entraînement se trouvent dans **titre**, **ingrédient** et **recette**. Ces informations peuvent être mise en valeur par l'usage d'outils TAL, d'où l'interêt particulier que nous leur portons pour ce projet. 

Cencernant le reste des attributs (*coût* et *difficulté*) nous n'y avons accodré que peu d'importance car ce que nous souhaitions avant tout c'était de perfectionner notre maîtrise des outils TAL. Notons cependant qu'il serait intéressant de les encoder via des one-hot afin de regarder si leur incormoration au modèle le fait gagner en performances. 

```SQL
recette_84191.xml,
Roulé à la confiture de lait,
Dessert,
Moyennement difficile,Bon marché,
"- Pour la garniture: - 1 boîte de lait concentré sucré de 40 cl - 250 g de mascarpone - Pour le biscuit: - 4 oeufs - 50 g de farine - 50 g de fleur de maïs - 125 g de sucre",
"La veille, préparer de la confiture de lait en portant à ébullition au bain marie pendant 3 heures le lait concentré sucré. Mélanger énergiquement les 4 jaunes d'oeufs avec le sucre, la farine, la fécule. Monter les blancs en neige et les incorporer à la préparation précédente. Répandre le tout sur une plaque de cuisson(penser au démoulage facile : silicone, papier cuisson...)et faire cuire juste 15 minutes à 180°C (thermostat 6). A la sortie du four, le rouler sur lui-même dans un linge humide. Mélanger la confiture de lait avec le mascarpone jusqu'à l'obtention d'une pâte lisse. Étaler la crème obtenue sur le biscuit en veillant à ne pas aller jusqu'aux bords puis le rouler à nouveau. Mettre au moins 1 heure au réfrigérateur. Attention aux divers temps de repos/préparation ! Chocolat chaud"
```
Ce second exemple de recette est issu quand à lui de *test.csv*. Cet ensemble de recettes permettra de *valider* nos résultats une fois les classifieurs entraînés. On remarque que format de cet ensemble est le mème que pour l'ensemble précédent. 

## Statistiques corpus
 
### Le corpus, globalement

Le set contient *13861* recettes dont *9981* sont utilisées pour le train, *2495* pour le test et *1388* pour la validation. 
Afin d'analyser le contenu de ces recettes, nous avons **concaténé** le titre, la recette et les ingrédients. 

### Distribution des types de recettes

Dans le set d'entrainement, on peut compter $5802$ Plats principaux $3762$ Desserts et $2909$ Entrées. Respectivements ils représentent $46,52\%$, $30,16\%$ et $23,32\%$ des effectifs totaux de l'ensemble d'entraînement. On remarque alors tout de suite que les *Plats principaux* sont surreprésentés, tandis que les *Entrées* sont sous représentées. Dans ce type de cironstances, on remarque assez vite que le risque pour le modèle est de répondre trop souvent *Plat principal* dans le but d'améliorer l'accuracy globale, au détriment du tax de vrai positifs pour les Entrées. 

Le set de validation quant à lui compte 644 Plats principaux, 407 Desserts et  2909 Entrées. Respectivements ils représentent $16,26\%$, $10,27\%$ et $73,45\%$ des effectifs totaux de l'ensemble de test. On remarque une encore plus grande surreprésentation de Entrées cette fois-ci. 

## Méthodes proposées

En ce qui concerne les méthodes utilisées, nous avons testé à la fois plusieurs *classifieurs* mais aussi plusieurs utilisations des données à notre disposition. 

### Run1: Méthode Aléatoire, notre base

	Description de la méthode:
	- Aucun descripteur utilisé.
	- Associer à une recette un label aléatoire, de manière équiprobable. 

Toutes les méthodes qui suivent, ont été testées avec les classifieurs suivant : 
 - SVC
 - Naïve Bayes
 - Rondom Forest
 - Logistical regression


### Run2: TF-IDF

	Description de la méthode :
	- Le titre, les ingrédients et la recette ont été concaténés et les données utilisées sont celles de la 
### Run3: NOMMETHODE
### Run4: NOMMETHODE (pour aller plus loin)

## Résultats

### Les f1 Scores

| Run      |    SVC   |Naive Bayes| Rand. Forest | Reg. Log. |
| -------- | --------:| --------:| --------:| --------:|
| baseline |     15,2 | | | |
| METH 2   |      6,8 | | | |
| METH 3   |     50,8 | | | |
| METH 4   |     70,2 | | | |

### Les matrices de confusions

### Analyse de résultats
	
	Pistes d'analyse:
	* Combien de documents ont un score de 0 ? de 0.5 ? de 1 ? (Courbe ROC)
	* Y-a-t-il des régularités dans les document bien/mal classifiés ?
	* Où est-ce que l'approche se trompe ? (matrice de confusion)
	* Si votre méthode le permet: quels sont les descripteurs les plus décisifs ?
