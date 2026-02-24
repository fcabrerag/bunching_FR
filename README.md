# Bus Bunching:
Bonjour, ce dépôt a pour but de présenter mon travail de mémoire de maîtrise sur le phénomène des «Bus Bunching». Il vise à illustrer certaines étapes de l'analyse, de la visualisation, du nettoyage et du traitement des données.

Je n'ai pas utilisé l'IA pour ecrire( je ne suis pas francais(je vis en France depuis 3 ans). En consequant, vous pouvez trouver des fautes(je suis vraiment desolé). Mon code date de l'année 2021/2022 , le temps de gloire de stackoverflow et l'apprentissage de code de manière autodidacte. Cette 2026 , je suis en train d'optimiser le code . Donc, je n'ai envie de utiliser les modèles du langage LLM ( chatGPT / Claude). Cette approche me permettra d'apprendre tellement... Malgré la vittesse. C'est parti !


Ce qui suit explique le contenu de chaque section :

1. Contexte et jeu de données : Présente une brève description du problème étudié, ainsi qu’une description du jeu de données.
2. Nettoyage et traitement des données manquantes : Expose les problèmes rencontrés dans le jeu de données et propose des stratégies de résolution permettant d’obtenir un jeu de données cohérent.

## Qu'est-ce que le "Bus Bunching"?

Le phénomène de regroupement de bus(Bus Bunching) est le principal problème affectant la régularité du réseau de bus urbains. Un regroupement de bus se définit comme l'intervalle de temps durant lequel un bus circule aux côtés d'au moins un autre bus de la même ligne, séparés par un intervalle inférieur à une certaine fraction de l'intervalle prévu. Cet intervalle correspond à la différence de temps entre deux bus empruntant le même itinéraire lorsqu'ils atteignent le même point, et représente également un écart dans leurs temps de parcours.

## 1. Contexte et jeu de données:

Les données historiques utilisées proviennent du système de géolocalisation automatique des véhicules (AVL) de la ligne de bus « T201 » à Santiago, au Chili. Elles correspondent aux trajets effectués par cette ligne au cours du mois d'août 2018.


## 1.2- Description des variables:
Les variables utilisées dans les exemples sont décrites ci-dessous:

| Nom de la variable | Description |
| --- | --- |
| patente | identifiant du bus. Elle est composée de quatre lettres suivies de deux chiffres ou de deux lettres suivies de quatre chiffres, selon l'âge du bus(plaque d'immatriculation).|
| fechamuestreo | date de collecte des données de géolocalisation. |
| latitud | distance, mesurée en degrés, entre un parallèle et l'équateur. Un signe négatif indique l'hémisphère sud.|
| longitud | arc de cercle mesuré entre le méridien de Greenwich et le méridien passant par le point considéré. Elle est mesurée en degrés et ses valeurs sont comprises entre 0° et 180°.|
| num_paradero | variable numérique indiquant l'arrêt que le bus vient de traverser.|
| grupo | variable numérique indiquant le numéro du trajet effectué ce jour-là par un bus donné.|
| dist30s | distance parcourue par le bus en 30 secondes.|
| dist_total | cela correspond à la distance parcourue par le bus jusqu'au point de son trajet où il se trouve(distance parcourue cumulée).|

*** Un voyage en bus est constitué d'un ensemble d'enregistrements (groupe de motifs).

## 2.-Nettoyage et traitement des données manquantes:

## Problèmes constatés dans le jeu de données:

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

La fréquence d'affichage des données n'est pas uniforme sur l'ensemble des lignes de bus. Dans certains cas, l'intervalle entre les valeurs de données dépasse 30 secondes (l'intervalle défini entre chaque ligne GPS). Un algorithme est appliqué pour générer de nouvelles lignes de données GPS à la fréquence appropriée. Les nouvelles variables de latitude et de longitude sont calculées par interpolation linéaire, ce qui détermine les valeurs intermédiaires entre les points de données avec une marge d'erreur minimale. La figure « a » montre les données avant interpolation linéaire (entre 16h18 et 16h20) ; aucune donnée n'est affichée sur le panneau d'affichage du bus FLXL-95. La figure « b » montre les données correctes après interpolation linéaire.

![](https://github.com/fcabrerag/bunching_FR/blob/7dfd9837c3790a012cd7e4b25a73f3629ad33f4e/images/data_interpolation.png)

## 3.-Calcul du "Bunching Bus":

### 3.1- Construction d'une structure de données de fenêtre:

Pour le calcul du bunching, on utilise une structure de données basée sur des fenêtres temporelles d’une durée de 30 secondes.
Chaque fenêtre possède un identifiant unique appelé n_ventana.

Une journée est divisée en fenêtres consécutives de 30 secondes, à partir de 05:00:00 jusqu’à 23:59:59, qui correspondent respectivement à l’heure de début et de fin du service quotidien de bus.

Chaque fenêtre a la structure suivante :

| début | fin | n_ventana|
| --- | --- | --- |
|2018-08-01 05:00:00 | 2018-08-01 05:00:29| 1|

Chaque enregistrement du dataset doit être associé à un numéro de fenêtre en fonction de la valeur de sa variable "fechamuestreo".

Par exemple, si le bus immatriculé "WB-1026" possède la valeur suivante pour la variable "fechamuestreo" : 2018-08-01 05:00:00

Alors il doit être associé à n_ventana = 1.


**Quel problème ai-je rencontré ?**

En 2021, lors de la rédaction de ma thèse, j’ai mis en place une structure de données basée sur des fenêtres temporelles, puis j’ai déterminé à quelle fenêtre appartenait chaque enregistrement du jeu de données.

**Cependant, chaque exécution prenait environ une minute uniquement pour identifier la fenêtre correspondant à l’ensemble des enregistrements d’une seule journée.**

Il y avait 6 mois des données . Donc, ce n'etait pas très rapide....


Je vous invite à consulter le code afin que vous puissiez voir le problème en détail:

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/drive/10zqlI_yS6ojk6_hWcgGzOo87rVh3PBfn)
