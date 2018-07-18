Ce tutoriel s’adresse aux contributeurs OSM expérimentés. Les étapes d'édition à l’aide du logiciel JOSM des relations de territoire OSM seront revues.
Les relations de territoire OSM  sont des collections regroupant les contours internes / externes des polygones et leurs attributs.

Ce document a ét réalisé en juillet 2018 dans le cadre du projet OpenCities à Kinshasa. Les niveaux hiérarchiques peuvent varier d’un pays à l’autre. Cependant, la démarche à suivre demeure la même quel que soit le pays.
 A titre de démonstration, nous allons utiliser les données des relations de la commune de Kisenso et ses quartiers.

## Limites des territoires dans OSM

Les polygones de limites administratives dans OpenStreetMap  décrivent les contours externes du territoire. Il est aussi possible d’y ajouter des trous correspondant à des zones d’exclusion à l’intérieur du territoire. Les limites de territoire sont décrites plus en détail dans [Les limites administratives dans OpenStreetMap](Les_limites_administratives_dans_OpenStreetMap.md).

## Requête Overpass – Extraire les données de territoire existantes

**Figure 1: Limites administratives, **
**Commune de Kisenso et de ses quartiers**
![Overpass Extraction territoires](img/Territoires-Commune-Kisenso-et-quartiers.png "Quarties de Kisenso" )
Une requête Overpass est utilisée pour télécharger ces données.

Dans toute requête Overpass, il faut décrire la zone géographique à télécharger. Ce peut être un simple rectangle (la variable bbox permet de définir ce rectangle). Il est aussi possible d’extraire les données d’une zone administrative existante dans la base OSM. À titre d’exemple, nous allons ici extraire les relations boundary pour Kinshasa.  

Deux options existent pour définir ce territoire. Nous allons les présenter toutes les deux avec leurs avantages / désavantages.

La première solution est plus simple à définir mais plus périlleuse lors de l’exécution. Il peut y avoir utilisation excessive de la mémoire amenant parfois un plantage du système. À expérimenter donc avec prudence.


    **Solution 1 – geocodeArea:nom – Définition de zone géographique dans Overpass**
    Instructions avec paramètre {{geocodeArea:Territoire}}                                                                               
    [out:xml][timeout:60]; 
    {{geocodeArea:Kisenso}}; 
    relation["boundary"]; 
    out meta;>; out meta; 

La deuxième solution nécessite de calculer le geocode à l’aide du id de la relation plus le nombre 3600000000. À titre d’exemple,  recherchons Kinshasa dans OSM et sélectionnons *Limite administrative Kisenso, Kinshasa, RD Congo*.  La relation est alors affichée avec le id OSM, soit dans ce cas-ci id=389536. 


    **Solution 2 – (area:noGeocode) - Définition de zone géographique dans Overpass**   
    Calcul du Geocode-overpass en  additionnant les deux nombres 
          3600000000 
    +         389536  (id, relation Kisenso) 
    =     3600389536  (Geocode-overpass pour Kisenso) 
    Instructions Overpass 
    [out:xml][timeout:60]; 
    relation(area:3600389536)["boundary"]; 
    out meta;>; out meta; 

## Édition des limites territoriales à l’aide de l’éditeur JOSM

Pour définir une nouvelle relation de limite administrative, notre objectif est de sélectionner et y ajouter tous les chemins qui forment le contour externe du polygone. Il est aussi possible de créer un trou au milieu de ce polygone pour une zone à exclure du territoire.  Il faut ensuite y ajouter les différentes clés OSM qui décrivent les caractéristiques de ce territoire. 

**Figure 2 : Ajout du style Admin Boundaries **
![Style Admin-Boundaries](img/JOSM-ajout-style-Admin-Boundaries.png)

Pour faciliter l’édition de territoires, nous allons ajouter le style Admin Boundaries.  Sélectionnez F12 / Projection et modèles de Rendu / Onglet couleur. Dans la liste des modèles disponibles à gauche, sélectionnez «Admin boundaries».  
Ce style colorie les polygones, y ajoute une couleur de fond différente pour chaque niveau administratif.

**Figure 3 : Paramètres de style, Admin Boundaries **
![Menu Paramètre de style](img/JOSM-Relation-Mission-partage-segments-avec-territoires-adjacents.png)

Pour  définir les paramètres du style Admin Boundaries, nous repérons le panneau Coloriage à droite, cliquons sur Admin Boundaries avec le bouton droit de la souris et sélectionnons Paramètres de style. Nous sélectionnons Admin_level=8 (pour les quartiers). Les territoires de niveau 8 son coloriés en vert. Lorsque sélectionnés, ils apparaissent en rosé.

Un polygone est un chemin fermé, dont le premier et le dernier point sont connectés.  Par contre, lorsque des polygones adjacent partagent un même segment, nous évitons de créer des doublons de chemins dificiles à voir et éditer. Pour la création des territoires dans OSM, nous coupons à la jonction avec un territoire adjacent. Pour illustrer ces divers segments, nous avons sélectionné en alternance les segments de chemins pour le quartier Mission dans la figure 3 ci-haut. Les chemins sélectionnés apparaissent avec flèche rouge sur-dimensionnée et les territoires correspondant à ces chemins en rosé. Nous allons maintenant voir comment créer et éditer ces territoires.

## JOSM - Création de relation de limite administrative 

**Figure 4 : Création de multipolygone**
![Créer un multipolygone](img/JOSM-créer-multipolygone.png)

Nous sélectionnons successivement les chemins qui tracent le contour externe du polygone. Sur la barre de menu du haut, nous sélectionnons Outils / Créer un multipolygone (Touche de raccourci Ctrl+B). Dans le panneau Attributs / Membres à droite, nous voyons apparaitre la référence au multipolygone. Nous cliquons sur cette référence et sélectionnons le bouton Modifier en dessous pour accéder au panneau de la modification de la relation.

Le panneau d’édition de la relation nous permet de voir la liste des chemins (Voir figure 5). L’outil multipolygone a automatiquement ajouté les rôles outer.  Si on ajoute de nouveaux chemins, il faudra aussi y ajouter ce rôle=outer. À droite, un indicateur visuel sous forme de boucle fermée nous indique rapidement que le polygone est clos (portion du haut de figure 5).  Nous modifions type=boundary et ajoutons les autres clés OSM pour définir ce territoire. Dans ce cas-ci, le quartier Mission contient la cle admin_level=8.

S’il était nécessaire d’ajouter une zone d’exclusion (ie. un trou dans le polygone), nous ajouterions à cette liste de membres les chemins intérieurs avec le rôle=inner.

**Figure 5: Édition de relation de limite administrative**
![Repère : Polygone clos ou ouvert](img/JOSM-Edition-relation-repere-polygone-clos.png)

## Édition / correction des relations de limites administratives

Pour fin d’exemple, nous allons maintenant simuler une erreur avec un polygone ouvert et voir comment détecter de telles erreurs à l’aide du style Admin_boundaries et dans le panneau d’édition de la relation. Pour créer un polygone ouvert, nous déconnectons les nodes joignant deux chemins traçant le polygone pour le quartier Mission.
     
Figure 6 : Polygone ouvert
![Polygone ouvert](img/JOSM-Edition-relation-polygone-ouvert.png)

Lorsque le polygone est ouvert, le style colorie les chemins avec des lignes hachurées et de gros rectangles jaunes nous indiquent quelles nodes ne sont pas connectées.  Après sélection des chemins (polygone non clos), et du menu Créer un multipolygone, cette fonction nous retourne un message d’erreur et la relation n’est pas créée. Il faut donc corriger avant de poursuivre.

Lors de l’édition, diverses actions sont réalisées telles couper, coller, coller divers segments. Il peut nous arriver de briser le polygone d’une relation existante, le laisser ouvert. Dans de tels cas, le style nous permet comme ci-haut de détecter le problème.  De même, le panneau d’édition nous signale lors de l’édition de la relation que le polygone n’est pas clos. La section du bas de la figure 5 illustre comment apparait l’indicateur dans un tel cas avec une simple ligne et des repères rouges là où les chemins ne sont pas connectés.  Si cet indicateur persiste après avoir corrigé le problème, nous sélectionnon s la liste des membres à trier et cliquons à gauche sur le bouton Trier les membres. Si le problème persiste encore, on s’assure de ne pas avoir de doubons de chemins, sinon de doublons de nodes. Dans ce cas-ci, à chaque jonction entre deux chemins que nous n’avons pas de doublons de nodes. Pour joindre les deux noeuds, nous cliquons sur Ctrl-M et on vérifie à nouveau si le problème est réglé.


    **Base OpenStreetMap, Relation, Quartier Mission (admin_level=8)**    |
    <relation id="8432841" visible="true" version="1" changeset="60474930" timestamp="2018-07-06T18:47:14Z" user="PierZen" uid="226516">
    <member type="way" ref="605700586" role="outer"/>
    <member type="way" ref="605700594" role="outer"/>
    <member type="way" ref="605700629" role="outer"/>
    <member type="way" ref="605700603" role="outer"/>
    <member type="way" ref="605700578" role="outer"/>
    <member type="way" ref="605700639" role="outer"/>
    <member type="way" ref="605700635" role="outer"/>
    <member type="way" ref="605700582" role="outer"/>
    <member type="way" ref="605700584" role="outer"/>
    <tag k="admin_level" v="8"/>
    <tag k="boundary" v="administrative"/>
    <tag k="name" v="Mission"/><tag k="type" v="boundary"/>
    </relation>

Pierre Béland
Juillet 2018
