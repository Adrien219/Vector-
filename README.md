# Vector — Game Design Document

## Concept

**Titre du jeu** : Vector

**Genre** : Plateforme 2D, course automatique avec poursuite

**Plateforme** : PC , build Unity

**État du projet** : Alpha jouable, deux niveaux complets, mécaniques principales fonctionnelles

## Auteurs

**BAKALI KABULO ORNELLA** : ornell27   ornell27 ornellabakali27@gmail.com

**MULONGOY LUBABILA ADRIEL** : Adrien219   adriellubabila219@gmail.com

**TSHIBWABWA DIAMBILE DORCAS** : Dorca32   dorcastshibwabwa32@gmail.com


Étudiant en L4 Intelligence Artificielle, dans le cadre du cours de Programmation des Jeux Vidéo dispensé par le professeur Daniel Katual et l'assistant Ortega Kabwe Mulunda, année académique 2025-2026.

**lien vers la vidéo** : https://youtu.be/KKFcHxsrPCY

**introduction**

Vector est un runner 2D dans lequel le joueur incarne un traceur de parkour poursuivi sans relâche par un ennemi. Course automatique, double saut, glissade, pièges dynamiques et collecte de pièces s'enchaînent sur deux niveaux à difficulté croissante. L'objectif est d'atteindre la sortie avant de se faire rattraper.



## Résumé du concept

### Objectif principal

Atteindre la sortie de chaque niveau en évitant les pièges et l'ennemi qui poursuit, tout en collectant un maximum de pièces. La partie se termine par une victoire si la sortie du niveau 2 est atteinte, ou par une défaite en cas de rattrapage par l'ennemi ou de contact avec un piège mortel.

### Boucle de jeu principale

Courir, esquiver ou sauter, collecter, atteindre la sortie, passer au niveau suivant. Cette boucle se répète sur les deux niveaux. Chaque niveau augmente la pression : vitesse accrue, fréquence de pièges supérieure, obstacles mobiles plus rapides.

### Piliers de conception

Le projet repose sur quatre principes directeurs. La tension permanente, d'abord : la poursuite par l'ennemi est constante et rester immobile équivaut à perdre. Les réflexes et le timing ensuite, puisque les pièges dynamiques exigent une lecture rapide et une réaction précise. La récompense du risque, avec des pièces parfois placées en zones dangereuses pour encourager la prise d'initiative au-delà de la simple survie. Enfin, une progression mesurée, où la difficulté monte de façon paramétrique entre les niveaux sans introduire de nouvelles mécaniques afin de rester lisible.

---

## Mécaniques de jeu

### Contrôles

Le joueur dispose de trois actions. La flèche du haut ou la barre d'espace permet de sauter, avec un double saut disponible. La flèche du bas déclenche la glissade, qui réduit temporairement la hauteur du collider pour passer sous certains obstacles. La touche Échap met le jeu en pause ou le relance.

Le déplacement horizontal est automatique : le personnage court en permanence vers la droite à une vitesse fixée par le niveau courant.

### Systèmes de jeu

La course et le saut sont gérés dans `PlayerController`. La vitesse est appliquée chaque frame via le `Rigidbody2D` du joueur. Le double saut est implémenté avec un compteur réinitialisé au contact d'un sol ou d'un mur. La glissade abaisse temporairement le collider via une coroutine.

La poursuite est assurée par `BotPlayer`, qui interpole sa position vers celle du joueur à vitesse constante. Une distance de capture configurable déclenche la défaite si le joueur est rattrapé. Une seconde détection par contact direct couvre les cas où la distance ne se referme pas continûment.

Le score et la collecte reposent sur `Coin` et `ScoreManager`. Les pièces sont des prefabs avec un collider en mode trigger qui, au contact du joueur, incrémentent le score géré par un singleton persistant. Ce singleton survit aux changements de scène et expose des événements auxquels le HUD s'abonne pour se rafraîchir automatiquement, sans interrogation continue.

Trois types de pièges complètent le gameplay. `FallingFloor` est une plate-forme qui s'effondre par gravité après un délai d'avertissement quand le joueur marche dessus. `MovingObstacle` produit un mouvement oscillant configurable pour les haches, pendules ou plates-formes mobiles. `DeadlyTrap` est un trigger mortel destiné aux pics, lasers et zones de mort sous le niveau.

La progression de difficulté est pilotée par `LevelConfig`, posé une fois par niveau, qui définit le numéro de niveau, la vitesse de course du joueur et un multiplicateur appliqué aux obstacles mobiles. La transition d'un niveau à l'autre est gérée par `LevelExit`, qui charge la scène suivante ou bascule vers l'écran de victoire si le niveau courant est le dernier.

Le menu principal propose trois actions : lancer une nouvelle partie, activer ou désactiver les effets visuels liés aux sauts et aux chutes, fermer le jeu. L'état du toggle est persisté via `PlayerPrefs` dans `GameSettings` et est consulté par `PlayerVFX` à chaque effet déclenché.

### Progression

Le jeu compte deux niveaux jouables. Le niveau 1 démarre avec une vitesse de course de 8 unités par seconde, des obstacles à vitesse nominale, une densité de pièges faible reposant principalement sur des plates-formes effondrables et une zone de mort sous le niveau. Le niveau 2 monte la vitesse à 11 unités par seconde, applique un multiplicateur de 1,5 aux obstacles mobiles, augmente la densité de pièges et introduit des obstacles oscillants. L'écran de fin affiche le résultat, le total de pièces collectées et le niveau atteint.

---

## Univers et narration

### context

Le joueur incarne un traceur de parkour traqué par un poursuivant inexorable. Le décor urbain défile sans interruption, parsemé de plates-formes instables et de pièges. Il n'y a pas de cinématique : la narration passe par la pression mécanique de la poursuite et la lecture environnementale.

### Personnages

Le traceur, contrôlé par le joueur, est un protagoniste agile dérivé du sprite Hero Knight Pixel Art déjà présent dans le projet. Ses capacités sont la course, le double saut et la glissade.

Le poursuivant, géré par `BotPlayer`, est un clone visuel du joueur réutilisant le même set d'animations. Son comportement est scripté : il suit en permanence la position du joueur jusqu'au contact.

### Direction artistique

Le style visuel est en 2D Pixel Art avec une palette urbaine sombre. Le décor utilise un défilement parallaxe à plusieurs plans pour donner de la profondeur. Le HUD est une interface minimaliste en TextMeshPro générée à l'exécution, avec un contraste fort et un contour noir pour rester lisible sur fond mouvant. Les écrans de fin emploient un code couleur sémantique : vert pour la victoire, rouge pour la défaite.

### Ambiance sonore

Des points d'ancrage audio sont prévus dans `Coin` pour le son de collecte et dans `PlayerVFX` pour les sons de saut et d'atterrissage. Aucune musique n'est embarquée à ce stade ; les fichiers sont assignables librement via l'inspecteur Unity.

---

## Technique et production

### Stack technique

Le projet est développé sous Unity 2022.3 LTS, compatible Unity 2023.3 LTS. Le langage est C#. L'interface utilisateur repose sur TextMeshPro. La physique utilise Unity Physics 2D et le rendu passe par le Built-in Render Pipeline 2D.

### Outils

Le développement se fait avec Visual Studio, VS Code ou Rider selon les préférences. Le versionnage est assuré par Git. Les assets graphiques proviennent du set Hero Knight Pixel Art et d'un set parkour, déjà fournis dans `Assets/sprites/` et `Assets/Resources/`.

### Architecture logicielle

Le code suit le principe de responsabilité unique. Chaque script encapsule une fonctionnalité bien définie. Le joueur est piloté par `PlayerController`, l'ennemi par `BotPlayer`. Le score et la progression sont centralisés dans `ScoreManager`. Les pièces utilisent `Coin`. L'affichage temps réel passe par `HUDController`. Les pièges sont implémentés dans `FallingFloor`, `MovingObstacle` et `DeadlyTrap`. La progression est gérée par `LevelConfig` et `LevelExit`. Les écrans sont contrôlés par `MainMenuController` et `EndGameController`. Les préférences persistent via `GameSettings`. Les effets visuels sont déclenchés par `PlayerVFX`. La pause et la reprise sont dans `GameController`. Le décor est animé par `Parallax`.

La communication inter-scripts passe par des événements plutôt que par couplage direct, ce qui garantit un découplage propre entre la logique métier et l'affichage.

### Configuration Unity requise

Avant de lancer le projet, quatre tags doivent exister dans Edit, Project Settings, Tags and Layers : `Player`, `Ground`, `Wall` et `Enemy`. Le menu Window, TextMeshPro, Import TMP Essential Resources doit être exécuté une fois pour disposer des ressources nécessaires au HUD. Enfin, dans File, Build Settings, les scènes `Menu.unity`, `Game.unity`, `Level2.unity` et `EndGame.unity` doivent être ajoutées dans cet ordre.

### Mise en place des scènes

La scène `Menu.unity` reçoit un GameObject vide avec le script `MainMenuController`. La scène `Game.unity` accueille un GameObject `HUD` portant `HUDController`, un GameObject `LevelConfig` réglé sur niveau 1 et vitesse 8, un GameObject `LevelExit` placé en fin de parcours et pointant vers `Level2`, ainsi que les pièges et pièces choisis pour le niveau. La scène `Level2.unity` reproduit la même structure avec des paramètres durcis : niveau 2, vitesse 11, multiplicateur d'obstacles 1,5, et un `LevelExit` final dont le champ `Next Level Scene` est laissé vide pour déclencher la victoire. La scène `EndGame.unity` reçoit un GameObject vide avec `EndGameController`.

Le seul prefab à créer manuellement est `Assets/Prefab/Coin.prefab`. Pour le construire, on crée un GameObject vide, on lui ajoute un Sprite Renderer avec une sprite jaune ou ronde, un Circle Collider 2D coché en Is Trigger avec un rayon d'au moins 1,5 pour tolérer les collisions à grande vitesse, et le script `Coin`. On glisse ensuite le GameObject dans `Assets/Prefab/` pour en faire un prefab réutilisable, puis on supprime l'instance de la scène. La duplication en scène se fait avec Ctrl+D ou en maintenant Alt enfoncé pendant le glissement.

### État du projet

Les mécaniques de course, saut et glissade sont stables. Le système de poursuite, la collecte de pièces et le HUD, les trois types de pièges, les transitions de niveaux, le menu principal et l'écran de fin sont également stables. Les effets visuels sont optionnels et nécessitent uniquement de brancher des particules dans l'inspecteur. Les sons disposent de hooks prêts à recevoir des assets audio. Des niveaux supplémentaires pourront être ajoutés ultérieurement en réutilisant la même structure.

### Conformité au cahier des charges

Le projet couvre l'ensemble des fonctionnalités minimales demandées : déplacement automatique du personnage, saut pour éviter les obstacles, ennemi poursuivant, obstacles fixes et pièges dynamiques avec sol qui s'ouvre, collecte de pièces, sortie de niveau déclenchant la transition, deux niveaux jouables avec difficulté progressive, menu principal proposant nouvelle partie, toggle des effets visuels et fermeture du jeu, affichage du score en permanence, écran de fin indiquant le résultat, le total de pièces et le niveau atteint, et règles de jeu (perte par rattrapage ou contact avec un piège, victoire par franchissement de la sortie).

---

##  Installation et Exécution

Vous pouvez tester ce projet de deux manières différentes :

### 1. Pour les testeurs (Lancer le jeu directement)
Si vous voulez simplement tester le gameplay sans installer Unity :
* Rendez-vous dans la section **[Releases]** de ce dépôt.
* Téléchargez le fichier `.zip` de la version la plus récente.
* Décompressez l'archive sur votre ordinateur.
* Double-cliquez sur l'exécutable **`RunnerGame.exe`** pour lancer la partie.

### 2. Pour les développeurs (Ouvrir le projet)
Si vous souhaitez explorer le code source ou modifier les scènes :
1. **Cloner le dépôt** :
   ```bash
   git clone [https://github.com/Adrien219/MySmartDailyApp.git](https://github.com/Adrien219/MySmartDailyApp.git)
---

## Arborescence du projet


Vector-main/
├── Assets/
│   ├── Animations/
│   ├── Hero Knight - Pixel Art/
│   ├── Prefab/
│   ├── Resources/
│   ├── Scenes/
│   ├── sprites/
│   ├── TextMesh Pro/
│   └── (scripts C#)
├── Packages/
├── ProjectSettings/
├── README.md
└── README_SETUP.md

