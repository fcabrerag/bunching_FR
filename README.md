## 2.-Nettoyage et traitement des données manquantes:

## Problèmes constatés dans l'ensemble de données:

1.- En réalisant une révision initiale des données, nous pouvons découvrir que des voyages en bus existent dans un délai déterminé qui est hors de l'itinéraire de voyage qui doit réaliser le bus normalement (par exemple : une réparation, un détour par rapport à l'itinéraire, etc). En conséquence, ces données doivent être exclues de l’ensemble de données.

2.- Les données sont une série temporelle avec une périodicité fixe (30 secondes entre chaque enregistrement). Cependant, des intervalles plus longs entre les enregistrements sont observés.



### 2.1- Validation de l'emplacement( Problème 1): 

Comment valider la position d'un bus lors d'un trajet ? Je dispose d'un jeu de données de coordonnées GPS de bus et je souhaite vérifier si certaines conditions définies dans l'énoncé du problème sont remplies.

Par exemple, déterminer si un trajet a lieu dans la zone géographique prédéfinie ou si les coordonnées GPS se situent à l'intérieur des limites d'une ville ou d'un pays.

Il existe différentes approches pour résoudre ce problème. Je souhaite notamment présenter une solution utilisant la bibliothèque Geopandas en Python.

L'une des opérations définies dans Geopandas est « within », qui permet de vérifier si un point GPS se situe à l'intérieur d'un polygone (ce dernier devant être chargé au préalable). Après cette jointure spatiale, les données ne répondant pas à la condition spécifiée peuvent être exclues.

L'image suivante présente les résultats obtenus après l'opération de jointure spatiale. Les trajets de bus incorrects ont ainsi été supprimés de l'ensemble de données.

![](https://github.com/fcabrerag/pro_investigacion/blob/main/imagenes/Rows_GPS_20180803.png)


### 2.2- Interpolation de données(Problème 2): 

L'échantillonnage des données ne présente pas la même fréquence pour tous les trajets de bus. Occasionnellement, les intervalles entre les valeurs des données dépassent 30 secondes (l'intervalle de temps défini entre chaque ligne GPS). Un algorithme a été appliqué pour générer de nouvelles lignes de données GPS avec la fréquence correcte. Les nouvelles variables de latitude et de longitude ont été calculées par interpolation linéaire, qui détermine les valeurs intermédiaires entre deux points de données avec une erreur minimale. L'image ci-dessous compare l'état initial et l'état après correction par l'algorithme.

![](https://github.com/fcabrerag/pro_investigacion/blob/main/imagenes/data_interpolation.png)
