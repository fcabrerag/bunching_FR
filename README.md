# Bus Bunching:
Bonjour, ce dépôt a pour but de présenter mon travail de mémoire de maîtrise sur le phénomène des «Bus Bunching». Il vise à illustrer certaines étapes de l'analyse, de la visualisation, du nettoyage et du traitement des données.

Ce qui suit explique le contenu de chaque section :

1. Contexte et jeu de données : Présente une brève description du problème étudié, ainsi qu’une description du jeu de données.
2. Nettoyage et traitement des données manquantes : Expose les problèmes rencontrés dans le jeu de données et propose des stratégies de résolution permettant d’obtenir un jeu de données cohérent.

## 1. Contexte et jeu de données:

Les données historiques utilisées proviennent du système de géolocalisation automatique des véhicules (AVL) de la ligne de bus « T201 » à Santiago, au Chili. Elles correspondent aux trajets effectués par cette ligne au cours du mois d'août 2018.

## Description des variables:

patente: Identifiant du bus. Elle est composée de quatre lettres suivies de deux chiffres ou de deux lettres suivies de quatre chiffres, selon l'âge du bus(Plaque d'immatriculation).
fechamuestreo : Date de collecte des données de géolocalisation.
latitud : Distance, mesurée en degrés, entre un parallèle et l'équateur. Un signe négatif indique l'hémisphère sud.
longitud : Arc de cercle mesuré entre le méridien de Greenwich et le méridien passant par le point considéré. Elle est mesurée en degrés et ses valeurs sont comprises entre 0° et 180°.
num_paradero : Variable numérique indiquant l'arrêt que le bus vient de traverser.
grupo : Variable numérique indiquant le numéro du trajet effectué ce jour-là par un bus donné.
dist30s: distance parcourue par le bus en 30 secondes.
dist_total: Cela correspond à la distance parcourue par le bus jusqu'au point de son trajet où il se trouve(distance parcourue cumulée).

*** Un voyage en bus est constitué d'un ensemble d'enregistrements (groupe de motifs).

## 2.-Nettoyage et traitement des données manquantes:

## Problèmes constatés dans l'ensemble de données:

1.- Lors d'un premier examen des données, nous constatons que certains trajets effectués par un bus (identifié par une plaque d'immatriculation spécifique) se situent en dehors de son itinéraire habituel. Par exemple : lorsqu'un bus est soumis à un contrôle technique ou lorsqu'un conducteur l'utilise à des fins autres que le service de transport.Par conséquent, ces données doivent être exclues de l'ensemble de données.


2.- Les données sont une série temporelle avec une périodicité fixe (30 secondes entre chaque enregistrement). Cependant, des intervalles plus longs entre les enregistrements sont observés.


### 2.1- Validation de l'emplacement( Problème 1): 

Comment valider la position d'un bus lors d'un trajet ? Je dispose d'un jeu de données de coordonnées GPS de bus et je souhaite vérifier si certaines conditions définies dans l'énoncé du problème sont remplies. En exploitant les caractéristiques des données de latitude et de longitude, nous pouvons utiliser des données spatiales. Cela nous permet de transformer la latitude et la longitude en points, ainsi que de charger un polygone de ville et de vérifier si les colonnes du jeu de données (latitude, longitude) se situent à l'intérieur de ce polygone. Dans ce cas précis, nous utiliserons le polygone de la ville de Santiago du Chili téléchargé depuis un site web gouvernemental officiel qui le met à disposition.

Il existe différentes approches pour résoudre ce problème. Je souhaite notamment présenter une solution utilisant la bibliothèque Geopandas en Python.

L'une des opérations définies dans Geopandas est « within », qui permet de vérifier si un point GPS se situe à l'intérieur d'un polygone (ce dernier devant être chargé au préalable). Après cette jointure spatiale, les données ne répondant pas à la condition spécifiée peuvent être exclues.

L'image suivante présente les résultats obtenus après l'opération de jointure spatiale. Les trajets de bus incorrects ont ainsi été supprimés de l'ensemble de données(action corrective).Cet exemple concerne l'ensemble de données correspondant à la journée du 03/08/2018 pour la ligne de bus T201 de la compagnie « Subus » à Santiago, au Chili.

![](https://github.com/user-attachments/assets/5de0f9db-618f-4518-b855-47a0cb5f3606)

Je vous fournis ici le code qui vous permet de visualiser le notebook de code dans Google Colab:

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/drive/1CYkA3KOp8I6KxDLdf6SD3Gf96vM-4MoK)

### 2.2- Interpolation de données(Problème 2): 

L'échantillonnage des données ne présente pas la même fréquence pour tous les trajets de bus. Occasionnellement, les intervalles entre les valeurs des données dépassent 30 secondes (l'intervalle de temps défini entre chaque ligne GPS). Un algorithme a été appliqué pour générer de nouvelles lignes de données GPS avec la fréquence correcte. Les nouvelles variables de latitude et de longitude ont été calculées par interpolation linéaire, qui détermine les valeurs intermédiaires entre deux points de données avec une erreur minimale. L'image ci-dessous compare l'état initial et l'état après correction par l'algorithme.

![](https://github.com/fcabrerag/pro_investigacion/blob/main/imagenes/data_interpolation.png)
