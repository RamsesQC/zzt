# Glossaire

## Base Compare Layer

Couche de référence utilisée pour les comparaisons. Elle correspond généralement à l’état actuel du réseau (sens de circulation existants et déviateurs ou filtres modaux actuellement en place sur le territoire).

Elle peut également être remplacée par toute autre configuration de référence afin de comparer différents scénarios entre eux.

# AB Street - Ajout de fonctionnalités

## 1. Show Before Edits

Actuellement, la fonctionnalité **« Show Before Edits »** permet uniquement d’afficher le réseau sans les modifications apportées.

Cette couche non modifiée est également utilisée comme **Base Compare Layer** pour certaines analyses, notamment :

- **Predict Impact**
- **Impact on One Destination**

Il faudrait modifier cette fonctionnalité afin qu’elle permette d’enregistrer plusieurs configurations possibles de LTN (Low Traffic Neighborhood).

L’utilisateur devrait pouvoir :

- enregistrer les différentes configurations ;
- les nommer ;
- les gérer ;
- basculer rapidement entre les configurations enregistrées ;
- changer la **Base Compare Layer** ;
- effectuer des analyses comparatives entre deux configurations sélectionnées, par exemple avec **Impact on One Destination**.

Les configurations enregistrées sont des instantanés de l’état modifiable du projet, et non de nouvelles couches cartographiques. La couche OSM d’origine doit rester distincte et continuer à être utilisée par **« Show Before Edits »**. Pour les comparaisons, l’utilisateur peut choisir une configuration enregistrée comme référence ; par défaut, la référence reste la configuration OSM d’origine.

Première étape : enregistrer une configuration nommée, recharger le projet et restaurer cette configuration. Après l’enregistrement, l’utilisateur continue à modifier la configuration active. Restaurer un instantané remplace l’état modifiable actif ; l’application demande confirmation avant ce remplacement. Les noms en double sont refusés dans cette première étape ; le renommage et la suppression pourront être ajoutés ultérieurement.

Dans **« Route »** et **« Impact on One Destination »**, la comparaison oppose la configuration active à la référence sélectionnée. Le déplacement des marqueurs **B** ou **X** met à jour l’analyse, comme aujourd’hui. La référence peut être l’OSM d’origine ou n’importe quelle configuration enregistrée, afin de comparer les itérations d’un plan routier.

## 2. Reclassify Road

Renommer l’option actuelle **« Reclassify Main Road »** en **« Reclassify Road »**.

Ajouter une fonctionnalité supplémentaire à **« Reclassify Road »** :

- Lorsqu’un segment de rue est sélectionné avec cet outil, sa classification doit pouvoir alterner.
- Au premier clic, le segment peut passer de **Local Road** à **Main Road**, selon son état.
- Au deuxième clic sur un même segment, plutôt que de revenir à son état initial, celui-ci doit recevoir la classification **Alley** (ruelle).

Une ruelle est une voie qui ne peut pas être utilisée comme itinéraire de transit. Dans le routage de transit, elle devrait se comporter comme une rue piétonne, même si les véhicules peuvent encore y accéder localement.

## 3. Onglet « Impact on One Destination »

Modifier l’analyse **« Impact on One Destination »** afin de comparer :

- la configuration active ;
- la **Base Compare Layer**, qui peut être l’OSM d’origine ou toute configuration enregistrée.

Actuellement, l’outil d’analyse affiche uniquement le temps de déplacement et la distance supplémentaires nécessaires pour sortir d’une destination.

Il faudrait également réaliser le même type d’analyse pour les déplacements visant à entrer dans une destination, c’est-à-dire effectuer l’analyse inverse.

Le déplacement du marqueur **X** doit recalculer la comparaison pour la référence sélectionnée.

## 4. Nouvel onglet d’analyse

Ajouter une nouvelle analyse dans un onglet de la barre supérieure.

Cet onglet permettrait de :

- calculer le temps moyen supplémentaire de déplacement entre deux configurations ;
- présenter les différences moyennes de temps de parcours pour l’ensemble du secteur étudié ;
- fournir des résultats distincts pour les déplacements d’entrée et de sortie ;
- permettre à l’utilisateur de choisir l’échelle d’analyse ;
- permettre à l’utilisateur de choisir les types de trajets à considérer, notamment :
  - l’ensemble du secteur ;
  - les rues locales seulement ;
  - les destinations sélectionnées ;
  - toute autre catégorie adaptée au scénario étudié.

L’analyse devrait permettre d’obtenir des résultats adaptés à chaque scénario étudié.

## 5. Ajouter une fonctionnalité de prise de notes

Ajouter une fonctionnalité permettant de prendre des notes sur des éléments.
