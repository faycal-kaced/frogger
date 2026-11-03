# 🐸 Frogger — Java / Swing

Une réimplémentation du jeu d'arcade **Frogger** en Java, avec une interface graphique Swing.
Le joueur guide une grenouille qui doit traverser des voies où circulent des voitures, sans se faire écraser.

Le projet propose deux modes de jeu :

- **Mode classique (fini)** : la grenouille part du bas de l'écran et doit atteindre la ligne d'arrivée en haut.
- **Mode infini** : la grenouille reste à l'écran pendant que les voies défilent. Le but est d'aller le plus loin possible. Le score et le temps de survie s'affichent à la fin de la partie.

---

## Sommaire

- [Aperçu](#aperçu)
- [Prérequis](#prérequis)
- [Installation et lancement](#installation-et-lancement)
- [Commandes](#commandes)
- [Choisir le mode de jeu](#choisir-le-mode-de-jeu)
- [Architecture du projet](#architecture-du-projet)
- [Fonctionnement du jeu](#fonctionnement-du-jeu)
- [Paramètres configurables](#paramètres-configurables)
- [Limitations connues et pistes d'amélioration](#limitations-connues-et-pistes-damélioration)
- [Auteur](#auteur)

---

## Aperçu

| Élément | Représentation |
|---|---|
| Grenouille | case **verte** |
| Voiture allant de gauche à droite | cases **noires** |
| Voiture allant de droite à gauche | cases **bleues** |
| Route / fond | **cyan** |

Le plateau mesure par défaut **26 × 20 cases** de 16 pixels, soit une fenêtre d'environ 416 × 320 px.

---

## Prérequis

- **Java JDK 8 ou plus récent** (`javac` et `java` disponibles dans le `PATH`)
- *(Facultatif)* **Eclipse IDE** : le dossier contient déjà les fichiers `.project` et `.classpath`
- *(Seulement pour le mode classique avec l'environnement fourni)* la bibliothèque **`givenEnvironment.jar`**, qui n'est **pas incluse** dans le dépôt (voir ci-dessous)

---

## Installation et lancement

### 1. Cloner le dépôt

```bash
git clone git@github.com:faycal-kaced/frogger.git
cd "frogger/Faycal kaced"
```

> ⚠️ Le dossier source contient un espace (`Faycal kaced`) : pensez aux guillemets dans le terminal.

### 2. À propos de `givenEnvironment.jar`

Dans sa version actuelle, `gameCommons/Main.java` utilise la classe `givenEnvironment.GivenEnvironment`. Elle provient d'une archive `givenEnvironment.jar` (fournie avec l'énoncé du projet) qui n'est **pas versionnée**. Le fichier `.classpath` pointe vers un chemin local Windows (`C:/Users/lenovo/Desktop/workspace/Projet/givenEnvironment.jar`).

Deux possibilités :

- **Vous avez le `.jar`** : placez-le dans le dossier du projet, puis compilez avec `-cp givenEnvironment.jar` (voir l'option A).
- **Vous ne l'avez pas** : utilisez l'environnement écrit dans le projet (`Environment` ou `EnvInf`) en modifiant `Main.java` comme expliqué dans [Choisir le mode de jeu](#choisir-le-mode-de-jeu) (voir l'option B).

### 3. Compiler et lancer

Les sources sont encodées en **ISO-8859-1** (Latin-1). Il faut donc préciser l'encodage pour que les accents des commentaires ne provoquent pas d'erreur.

**Option A : avec `givenEnvironment.jar`**

```bash
# Linux / macOS
javac -encoding ISO-8859-1 -cp givenEnvironment.jar -d bin $(find . -name "*.java")
java -cp "bin:givenEnvironment.jar" gameCommons.Main
```

```bat
:: Windows (PowerShell ou cmd, depuis le dossier "Faycal kaced")
dir /s /b *.java > sources.txt
javac -encoding ISO-8859-1 -cp givenEnvironment.jar -d bin @sources.txt
java -cp "bin;givenEnvironment.jar" gameCommons.Main
```

**Option B : sans le `.jar`** (après avoir modifié `Main.java`)

```bash
javac -encoding ISO-8859-1 -d bin $(find . -name "*.java")
java -cp bin gameCommons.Main
```

### Avec Eclipse

1. `File → Import → General → Existing Projects into Workspace`
2. Sélectionnez le dossier `Faycal kaced`
3. Corrigez le chemin de `givenEnvironment.jar` dans `Properties → Java Build Path → Libraries`, ou retirez-le si vous utilisez l'option B
4. Clic droit sur `gameCommons/Main.java` → `Run As → Java Application`

---

## Commandes

| Touche | Action |
|---|---|
| ⬆️ Flèche haut | Avancer |
| ⬇️ Flèche bas | Reculer |
| ⬅️ Flèche gauche | Aller à gauche |
| ➡️ Flèche droite | Aller à droite |

La partie est **perdue** dès que la grenouille se trouve sur une case occupée par une voiture.

---

## Choisir le mode de jeu

Le mode se choisit dans `gameCommons/Main.java`, en changeant la grenouille et l'environnement instanciés.

### Mode classique (environnement du projet)

```java
import frog.Frog;
import environment.Environment;
// ...
IFrog frog = new Frog(game);
// ...
IEnvironment env = new Environment(game);
```

Dans `Game.update()`, **décommentez** l'appel `testWin();` pour que la victoire soit détectée à l'arrivée en haut de l'écran.

### Mode infini

```java
import frog.FrogInf;
import environment.EnvInf;
// ...
IFrog frog = new FrogInf(game);
// ...
IEnvironment env = new EnvInf(game);
```

Laissez `testWin();` **commenté** : en mode infini, il n'y a pas de ligne d'arrivée. À la fin, l'écran affiche :

```
You've lost... So, so sad. Score : <nombre de voies franchies> Time : <durée> s
```

---

## Architecture du projet

```
Faycal kaced/
├── gameCommons/            # Cœur du jeu
│   ├── Main.java           # Point d'entrée : configuration, création des objets, boucle du Timer
│   ├── Game.java           # État de la partie, mise à jour, tests de victoire et de défaite
│   ├── Case.java           # Coordonnées (absc, ord) d'une case de la grille
│   ├── IFrog.java          # Interface d'une grenouille
│   └── IEnvironment.java   # Interface d'un environnement (isSafe, isWinningPosition, update)
│
├── frog/
│   ├── Frog.java           # Grenouille du mode classique (bornée au plateau)
│   └── FrogInf.java        # Grenouille du mode infini (affichée fixe sur la ligne 2)
│
├── environment/
│   ├── Environment.java    # Environnement classique : une Lane par ligne
│   ├── Lane.java           # Une voie : sens, vitesse, densité, liste de voitures
│   ├── EnvInf.java         # Environnement infini : génère de nouvelles voies au fil de la progression
│   ├── LaneInf.java        # Voie qui défile avec la grenouille
│   └── Car.java            # Voiture : position, longueur aléatoire, déplacement, collisions
│
├── graphicalElements/
│   ├── IFroggerGraphics.java  # Interface de l'affichage
│   ├── FroggerGraphic.java    # Implémentation Swing (JFrame/JPanel, clavier, écran de fin)
│   └── Element.java           # Case colorée à dessiner
│
├── util/
│   └── Direction.java      # Enum : up, down, left, right
│
├── .project / .classpath   # Configuration Eclipse
└── *.class                 # Fichiers compilés (versionnés par erreur, voir plus bas)
```

### Diagramme simplifié

```
               ┌────────────┐
               │    Main    │  Timer Swing (tous les `tempo` ms)
               └─────┬──────┘
                     │ game.update() + graphic.repaint()
               ┌─────▼──────┐
     ┌─────────│    Game    │──────────┐
     │         └─────┬──────┘          │
     ▼               ▼                 ▼
 IFrog          IEnvironment     IFroggerGraphics
 ├─ Frog        ├─ Environment ──► Lane ──► Car      └─ FroggerGraphic
 └─ FrogInf     └─ EnvInf ───────► LaneInf ─► Car
```

Le code s'appuie sur des **interfaces** (`IFrog`, `IEnvironment`, `IFroggerGraphics`). On peut ainsi changer de grenouille, d'environnement ou d'affichage sans toucher à la classe `Game`.

---

## Fonctionnement du jeu

1. **Boucle principale** : un `javax.swing.Timer` appelle `Game.update()` toutes les `tempo` millisecondes (100 ms par défaut).
2. **`Game.update()`** :
   - vide l'affichage,
   - met à jour l'environnement : chaque voie fait avancer ses voitures et peut en ajouter une,
   - dessine la grenouille,
   - vérifie la défaite (et la victoire en mode classique).
3. **Voies (`Lane`)** :
   - chaque voie a un **sens** (gauche → droite ou l'inverse) et une **vitesse** tirés au hasard ;
   - les voitures avancent d'une case tous les `speed` tics d'horloge ;
   - à chaque tic, une voiture peut apparaître en début de voie avec une probabilité égale à la **densité**, si l'entrée est libre ;
   - les voitures sorties de l'écran sont supprimées.
   - La première et la dernière ligne ne contiennent pas de voitures : ce sont les zones de départ et d'arrivée.
4. **Collisions** : `Lane.isSafe(case)` vérifie qu'aucune voiture de la voie ne couvre la case de la grenouille.
5. **Mode infini** : quand la grenouille avance, chaque `LaneInf` décale son ordonnée en sens inverse. La route semble ainsi défiler pendant que la grenouille reste affichée sur la ligne 2. `EnvInf` ajoute de nouvelles voies en haut au fur et à mesure.

---

## Paramètres configurables

Dans `gameCommons/Main.java` :

| Variable | Valeur par défaut | Rôle |
|---|---|---|
| `width` | `26` | Largeur du plateau (en cases) |
| `height` | `20` | Hauteur du plateau (en cases) |
| `tempo` | `100` | Intervalle entre deux tics d'horloge (ms) |
| `minSpeedInTimerLoops` | `3` | Vitesse de base des voitures (nombre de tics entre deux déplacements) |
| `defaultDensity` | `0.2` | Densité de voitures par défaut |

Dans `graphicalElements/FroggerGraphic.java` : `pixelByCase = 16` fixe la taille d'une case à l'écran.

---

## Limitations connues et pistes d'amélioration

Points relevés à la lecture du code :

- **Dépendance externe manquante** : `Main.java` importe `givenEnvironment.GivenEnvironment`, qui n'est pas dans le dépôt. Sans le `.jar`, le projet ne compile pas tel quel. Utiliser `Environment` ou `EnvInf` par défaut rendrait le dépôt autonome.
- **Fichiers `.class` versionnés** : ajouter un `.gitignore` contenant `*.class` et `bin/`.
- **Chemin absolu Windows** dans `.classpath` : à remplacer par un chemin relatif (par exemple un dossier `lib/`).
- **Encodage** : sources en ISO-8859-1 avec fins de ligne CRLF. Les convertir en UTF-8 éviterait les accents illisibles sur GitHub.
- **`Car.coversCase`** : l'abscisse est incrémentée avec `x += i` au lieu de `x + i`. Pour les voitures de longueur 3 et plus, certaines cases ne sont pas testées correctement.
- **Longueur des voitures** : `(int) (Math.random() * 3)` peut renvoyer `0`, ce qui crée des voitures invisibles et sans collision. `1 + (int) (Math.random() * 3)` donnerait des longueurs de 1 à 3.
- **Vitesse des voies** : dans `Lane(game, y, density)`, la vitesse vaut `minSpeedInTimerLoops` ± 2. Avec la valeur par défaut (3), elle va de 1 à 5, mais si `minSpeedInTimerLoops` est inférieur à 3, elle peut tomber à `0` ou devenir négative, ce qui provoquerait une erreur `ArithmeticException` sur `timer % speed`.
- **`removeOldCars`** : supprimer des éléments dans une boucle indexée qui avance saute l'élément suivant. Un `cars.removeIf(c -> !c.appearsInBounds())` serait plus sûr.
- **Duplication** : `Lane` et `LaneInf` sont presque identiques ; une classe mère commune réduirait le code.
- **Choix du mode** : pour changer de mode, il faut aujourd'hui modifier le code. Un argument en ligne de commande (`java gameCommons.Main infini`) ou un menu de démarrage serait plus pratique.

---

## Auteur

**Fayçal Kaced** — [github.com/faycal-kaced](https://github.com/faycal-kaced)

Projet réalisé dans le cadre d'un cours de programmation orientée objet en Java.
