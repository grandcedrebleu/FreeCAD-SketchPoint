# FreeCAD SketchPoint — 0.1.1 (version d'essai)

Positionner numériquement un point dans une esquisse en cours d'édition.

**Statut : version 0.1.1 d’essai. Le déplacement relatif et le raccourci de la version précédente ont été confirmés par l’utilisateur. Le nouveau contrôle des dépendances reste à tester dans FreeCAD.**

## Fonctionnement

- Absolu : X et Y depuis l'origine locale de l'esquisse.
- Relatif : ΔX et ΔY depuis la position du point à l'ouverture de la fenêtre, selon les mêmes axes locaux.
- Saisie en millimètres, affichage de la cible avant validation.
- Points isolés, extrémités de segments, centres de cercles, centres et extrémités d'arcs de cercle.
- Origine, géométries externes et autres types de courbes refusés.
- Aucune contrainte ajoutée ou supprimée. Les éléments liés peuvent bouger avec le point.
- Retour par transaction à l'état initial en cas d'erreur du solveur ou d'écart à la cible supérieur à 0,000001 mm.
- Annulation du déplacement réussi avec Ctrl+Z.
- Aucun magnétisme dans cette version.

## Installation de la version d'essai

1. Télécharger `SketchPoint.FCMacro` depuis sa page GitHub avec le bouton **Download raw file**.
2. Dans FreeCAD, ouvrir **Macro → Macros…** et relever le dossier des macros utilisateur.
3. Enregistrer le fichier dans ce dossier, en conservant l'extension `.FCMacro`.
4. Créer ou ouvrir une esquisse de test, passer en édition et sélectionner un seul point.
5. Dans **Macro → Macros…**, choisir SketchPoint et cliquer sur **Exécuter**.
6. Choisir le mode, saisir les deux valeurs et valider.

Une valeur relative nulle conserve la coordonnée correspondante. En absolu, laisser la valeur préremplie conserve cette coordonnée.

### Bouton dans une barre d'outils

Après validation du lancement par le menu Macro :
ouvrir **Outils → Personnaliser… → Macros**, sélectionner le fichier et créer sa commande.
Dans l'onglet **Barres d'outils**, choisir Sketcher, créer une barre SketchPoint et y ajouter cette commande depuis la catégorie Macros.
Les intitulés exacts peuvent varier avec la traduction et la version.

## Validation à réaliser dans FreeCAD

Travailler sur un document de test et relever la version complète de FreeCAD ainsi que tout message de la vue Rapport.

| Essai | Résultat attendu |
|---|---|
| Point libre (20,15), absolu (25,12) | Point en (25,12) |
| Point libre (20,15), relatif (+5,-3) | Point en (25,12) |
| Relatif (+5,0) | Y inchangé |
| Coordonnées négatives et décimales | Cible atteinte à la tolérance indiquée |
| Esquisse inclinée ou attachée | Déplacement dans son repère local |
| Extrémité de segment / centre de cercle / arc | Point sélectionné correctement identifié |
| Point bloqué par contraintes | Message et géométrie initiale restaurée |
| Segment horizontal avec points liés | Contraintes conservées ; déplacement des éléments liés possible |
| Annuler dans la fenêtre | Document inchangé |
| Ctrl+Z après réussite, puis rétablir | Géométrie initiale, puis déplacement retrouvé |
| Origine, arête, géométrie externe ou sélection multiple | Refus explicite |
| Relancer plusieurs fois | Nouvelle position utilisée comme base |

## Référence technique

Interface Python consultée :
https://github.com/FreeCAD/FreeCAD/blob/main/src/Mod/Sketcher/App/SketchObjectPyImp.cpp

La macro utilise `getGeoVertexIndex`, `getPoint`, `moveGeometries([(geo, pos)], cible, False)` et `solve`.
Cette lecture du code amont ne remplace pas les tests dans la version installée.

## Historique

### 0.1.0 — version d'essai
Première implémentation du positionnement absolu et relatif, fenêtre française, contrôle de cible et transaction d'annulation. Tests d'exécution FreeCAD restant à réaliser.

### 0.1.1 — version d'essai
- Remplace exec_() par exec().
- Après déplacement, inspecte l'esquisse et ses objets dépendants : erreurs de validité et objets encore à recalculer sont signalés dans une fenêtre et la console.
- Le déplacement est conservé, annulable avec Ctrl+Z. Les erreurs signalées peuvent être préexistantes ; aucune attribution automatique au déplacement.
- Syntaxe Python vérifiée ; comportement FreeCAD à valider sur une extrusion avec contour ouvert, un modèle sain et un modèle déjà en erreur.
