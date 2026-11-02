# Documentation technique du dépôt COBOL

## 1. Objet du dépôt

Le dépôt `AltecaGuillaumeG/test` est une collection de programmes COBOL de démonstration conçus pour illustrer les mécanismes fondamentaux du langage et les pratiques de mise en œuvre dans des cas d'usage très différents.

Le point clé est que le dépôt n'est pas un système applicatif unique ; c'est une galerie d'exemples de code COBOL, chacun focalisé sur un concept spécifique :

- traitement des chaînes de caractères,
- structures de données et tableaux,
- calculs arithmétiques et comptables,
- conditions et états nommés,
- manipulation de dates,
- logique scientifique avec nombres flottants,
- interactions I/O utilisateur.

Le dépôt est entièrement en COBOL, ce qui en fait un référentiel pédagogique très homogène.

---

## 2. Architecture du dépôt

La structure est de type "mini-programmes indépendants" à la racine du dépôt. Chaque fichier `.CBL` est un programme autonome avec :

- une division `IDENTIFICATION`,
- une `DATA DIVISION`,
- une `PROCEDURE DIVISION`.

Les programmes partagent un même modèle d'organisation, mais diffèrent fortement par leur logique métier et les constructions COBOL qu'ils exploitent.

L'ensemble des programmes est lié par une même philosophie :

1. définir des structures de données explicites,
2. manipuler des valeurs via `MOVE`, `INITIALIZE`, `STRING`, `INSPECT`,
3. calculer ou filtrer des valeurs via `IF`, `PERFORM`, `COMPUTE`,
4. afficher le résultat via `DISPLAY`.

Cela fait du dépôt un bon ensemble d'exemples de référence pour apprendre la programmation COBOL sur des cas concrets et faciles à isoler.

---

## 3. Évolution logique des exercices

Les programmes du dépôt peuvent être lus comme une progression didactique :

- `HELLO.CBL` : base sur les chaînes et les intrinsics,
- `STRINGIT.CBL` : manipulation de données textuelles avancée,
- `INVCALC.CBL` : logique métier avec calculs monétaires,
- `ATTRACT.CBL` : usage des flottants et calcul scientifique,
- autres fichiers complémentaires : conditions, dates, formatages, états nommés.

Les programmes sont donc liés par des notions transverses, même s'ils sont conçus comme des sujets distincts.

---

## 4. Vue détaillée des programmes

### 4.1. `HELLO.CBL` – introduction au traitement textuel

#### Rôle
Ce programme est l'exemple le plus simple et le plus pédagogique du dépôt. Il montre la construction d'un message de type `Hello, <nom> !` à partir d'une donnée saisie par l'utilisateur.

#### Structure technique

- `WS-PROMPT` : message d'invitation,
- `WS-FRIEND` : valeur entrée par l'utilisateur,
- `WS-GREETING` : préfixe du message,
- `WS-EXCLAMATION-POINT` : point d'exclamation,
- `WS-MESSAGE` : zone de sortie finale,
- `WS-TRAILING-SPACES` : compteur de blancs de fin de chaîne.

#### Logique

1. `DISPLAY WS-PROMPT` affiche l'invite.
2. `ACCEPT WS-FRIEND` récupère la saisie utilisateur.
3. `INSPECT FUNCTION REVERSE(WS-FRIEND) TALLYING WS-TRAILING-SPACES FOR LEADING SPACES` détecte les espaces en fin de chaîne.
4. `STRING ... INTO WS-MESSAGE` concatène :
   - le texte `Hello, `,
   - le nom sans espaces de fin,
   - `!`.
5. `DISPLAY WS-MESSAGE` affiche le message final.

#### Intérêt technique
C'est un bon exemple de :

- `PIC X(...)` pour le texte,
- `FUNCTION REVERSE`,
- `INSPECT ... FOR LEADING SPACES`,
- travail sur des zones de données de taille fixe,
- construction d'un message final via `STRING`.

#### Lien avec les autres programmes
Ce programme sert de base pour la logique de manipulation de chaînes retrouvée ensuite dans `STRINGIT.CBL`. La différence est que `HELLO.CBL` traite un cas concret et simple, tandis que `STRINGIT.CBL` approfondit des manipulations plus structurées.

---

### 4.2. `STRINGIT.CBL` – manipulation avancée des chaînes

#### Rôle
C'est le programme de référence pour les opérations sur chaînes de caractères en COBOL. Il montre comment assembler, découper et formater des textes selon des contraintes précises.

#### Structure technique

- `WS-NAME` est une zone composite contenant :
  - `WS-FAMILY-NAME`,
  - `WS-GIVEN-NAME`,
  - `WS-MIDDLE-NAME`.
- `WS-NAMES-IN-OUTPUT-ORDER` regroupe les champs dans l'ordre d'affichage final.
- `WS-NAME-LENGTHS` contient les longueurs calculées pour chaque nom.
- `WS-OUTPUT-AREA` sert à la concaténation finale.

#### Logique
Le programme exécute quatre exemples successifs :

1. `MOVE` direct entre zones de données.
2. `INSPECT ... TALLYING ... FOR CHARACTERS BEFORE INITIAL SPACE` pour calculer la longueur effective d'un mot.
3. `STRING` pour concaténer `Prénom + Espace + Deuxième prénom + Espace + Nom` dans une zone de sortie.
4. `STRING` combiné à `reference modification` pour produire un résultat de taille maîtrisée.

#### Points techniques les plus importants

- `WS-GIVEN-NAME(1:WS-GIVEN-NAME-LENGTH)` : référence partielle d'un élément de donnée.
- `INITIALIZE WS-NAME-LENGTHS` : remise à zéro des counters.
- `DISPLAY '<' WS-OUTPUT-AREA(1:WS-OUTPUT-LENGTH) '>'` : affichage d'une sous-zone de sortie.
- `DELIMITED BY SPACE` et `DELIMITED BY SIZE` : mécanismes de découpe et d'insertion.

#### Intérêt de ce programme
C'est le meilleur exemple du dépôt pour illustrer les concepts suivants :

- stockage de données structurées,
- décodage de chaînes,
- logique de concaténation explicite,
- contrôle précis de la longueur des données,
- formatage d'affichage.

#### Lien avec `HELLO.CBL`
`HELLO.CBL` montre la base de la chaîne. `STRINGIT.CBL` applique la même logique en mode plus formalisé sur plusieurs champs structurés. Les deux sont donc directement liés : l'un est le cas simple, l'autre la version technique et généralisée.

---

### 4.3. `INVCALC.CBL` – logique métier et calcul comptable

#### Rôle
Ce programme est un exemple de traitement comptable de type facture. Il illustre le plus clairement le COBOL "application métier".

#### Structure technique

Le programme définit :

- des taux de taxe,
- des variables cumulatives,
- un enregistrement `INVOICE`,
- des sous-structures de ligne d'article : `INV-LINE`,
- des champs de sortie formatés (`INV-TOTAL-AMOUNT-FORMATTED`).

Quelques éléments importants :

- `INV-LINE OCCURS 1 TO 100 DEPENDING ON INV-LINE-ITEM-COUNT`
- `INV-LINE-UNIT-PRICE PIC S9(05)V99 COMP-3`
- `INV-LINE-QUANTITY PIC S9(05) COMP-3`
- `TAXABLE-ITEM` / `NONTAXABLE-ITEM` via `88` levels

#### Logique métier

1. Initialisation des données de facture.
2. Création de trois lignes d'articles avec quantité, prix et statut taxable.
3. Calcul cumulé :
   - `CUMULATIVE-PRICE-BEFORE-TAX`
   - `LINE-WORKING-TOTAL`
   - `LINE-WORKING-TAX`
   - `CUMULATIVE-PRICE-WITH-TAX`
   - `CUMULATIVE-SALES-TAX`
4. Vérification de la validité des données via conditions.
5. Impression détaillée de la facture.

#### Points techniques importants

- `PERFORM WITH TEST BEFORE VARYING WORKING-INDEX ... UNTIL ...`
- `MULTIPLY ... BY ... GIVING ...`
- `ADD ... TO ...`
- `IF TAXABLE-ITEM(WORKING-INDEX)`
- `INITIALIZE INVOICE REPLACING ALPHANUMERIC DATA BY SPACES NUMERIC DATA BY ZEROES`

#### Intérêt du programme
Il montre que COBOL est particulièrement adapté à :

- la modélisation de données de type facture,
- les calculs arithmétiques sur données monétaires,
- les traitements par lots,
- la présentation de résultats sous forme structurée et lisible.

#### Lien avec les autres programmes
`INVCALC.CBL` est le prolongement naturel de `STRINGIT.CBL` vers des données métier structurées. En effet, dans les deux programmes, on voit la même logique :

- structurer les données,
- calculer sur des champs précis,
- formater une sortie finale.

La différence est que `STRINGIT.CBL` manipule des textes, tandis que `INVCALC.CBL` manipule des données financières et comptables.

---

### 4.4. `ATTRACT.CBL` – calcul scientifique et flottants

#### Rôle
Ce programme sort du cadre habituel du COBOL de gestion. Il démontre qu'un programme COBOL peut aussi traiter des calculs scientifiques en utilisant des nombres flottants.

#### Structure technique

Le code utilise :

- `COMP-2` pour les variables flottantes,
- `Body occurs 2 indexed by BodyIx` pour représenter deux corps,
- `mass`, `vx`, `vy`, `px`, `py` pour les attributs physiques,
- `G` comme constante gravitationnelle.

#### Logique

1. Demande des paramètres pour deux corps.
2. Stocke leurs masses et coordonnées.
3. Calcule :
   - `dx = px(1) - px(2)`
   - `dy = py(1) - py(2)`
   - `d = FUNCTION SQRT((dx * dx) + (dy * dy))`
4. Détermine la force d'attraction :
   - `f = (G * mass(1) * mass(2)) / (d * d)`
5. Calcule une direction avec `COS` / `SIN` et affiche les composantes `fx` et `fy`.

#### Points techniques importants

- `COMP-2` pour les nombres à virgule flottante,
- `indexed by BodyIx` pour manipuler un tableau de 2 éléments,
- `FUNCTION SQRT`, `FUNCTION COS`, `FUNCTION SIN`, `FUNCTION ATAN`,
- `ACCEPT mass(BodyIx)` pour lire des valeurs utilisateur.

#### Intérêt de ce programme
Il montre que COBOL n'est pas limité aux applications de gestion. La différence avec `INVCALC.CBL` est nette :

- `INVCALC.CBL` est orienté métier et calculs décimaux,
- `ATTRACT.CBL` est orienté mathématique et flottants.

#### Lien avec le dépôt
Ce programme complète la palette des exemples. Il illustre le fait que le dépôt n'est pas seulement un recueil de méthodes de gestion, mais aussi une démonstration de la polyvalence du langage COBOL.

---

## 5. Fichiers complémentaires et leur rôle dans le corpus

Le dépôt contient plusieurs autres fichiers qui renforcent le corpus d'exemples. Ils ont chacun un rôle spécifique, souvent dans des domaines plus ciblés :

- `DATE1.CBL`, `DATE2.CBL` : manipulation des dates et formatage des calendriers,
- `IFEVAL.CBL` : exemples de structures conditionnelles avancées,
- `COND88.CBL` : conditions nommées et états logiques basés sur `88` levels,
- `BRAKES.CBL` : logique de contrôle, probablement orientée système/logique,
- `HEX2TEXT.CBL` : conversion hexadécimal / texte,
- `MOVEME.CBL` : manipulation de données et déplacement entre zones,
- `NOTBOOL.CBL` : exemple de négation logique, conditions booléennes,
- `REFORMER.CBL` : transformations de chaînes ou reformattage,
- `CPSEQFR.CBL`, `CPSEQVR.CBL` : traitement de séquences, probablement comparant l'ordre ou la séquence de valeurs,
- `COND88.CBL` : cas d'école sur les états conditionnels.

Ces fichiers ne sont pas des applications complètes ; ce sont des briques de démonstration dont la fonction est de montrer qu'un même langage peut adresser de nombreux problèmes de logique et de mise en forme.

---

## 6. Liens structurels entre les programmes

Les programmes du dépôt sont liés par plusieurs niveaux de cohérence :

### 6.1. Même structure de base
Tous les programmes respectent le même squelette :

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. ...
DATA DIVISION.
WORKING-STORAGE SECTION.
PROCEDURE DIVISION.
```

### 6.2. Même logique de développement
Ils utilisent les mêmes paradigmes de programmation COBOL :

- souplesse de lecture par blocs de données,
- descriptions explicites des champs,
- calculs conditionnels,
- affichage final explicite.

### 6.3. Progression pédagogique
Le dépôt est conçu comme un parcours d'apprentissage :

- base : chaînes, variables, affichage,
- intermédiaire : structures, tableaux, conditions,
- avancé : calculs monétaires et scientifiques,
- démonstration : cas métier et calculs spécialisés.

### 6.4. Unités d'apprentissage indépendantes
Chaque programme est autonome, ce qui permet de l'étudier seul sans dépendre d'un autre fichier. Néanmoins, les programmes se complètent mutuellement et forment un corpus cohérent de référence.

---

## 7. Mécanismes COBOL récurrents dans le dépôt

### 7.1. `PIC` et qualification des données
Le dépôt montre une grande variété de zones de données :

- `PIC X(...)` pour les chaînes,
- `PIC 9(...)` pour les chiffres,
- `PIC S9(...)` pour les valeurs signées,
- `PIC ... COMP-3` pour données compactées,
- `PIC ... COMP-2` pour flottants.

### 7.2. `88` levels et états nommés
Le mécanisme `88` est central dans les programmes de type facture. Il permet de définir des états logiques sur une variable, ce qui rend le code plus lisible et évite les comparaisons littérales répétées.

### 7.3. `PERFORM` et boucles structurées
Le dépôt montre le fonctionnement de `PERFORM` en plusieurs formes :

- `PERFORM ... UNTIL ...`
- `PERFORM WITH TEST BEFORE VARYING ...`
- `PERFORM` sur sections de code.

### 7.4. `STRING`, `INSPECT`, et `MOVE`
Ces opérations sont répétées dans plusieurs exemples. Elles sont la base du traitement textuel en COBOL et représentent la technique la plus utilisée dans la collection.

### 7.5. Sortie console et écriture de résultats
Les programmes utilisent systématiquement `DISPLAY` pour rendre les résultats visibles. Cela correspond au paradigme historique COBOL, où les traitements de données s'effectuent souvent via estándar d'entrée/sortie textuelle.

---

## 8. Analyse synthétique des relations entre fichiers

### Enchaînement naturel des compétences

- `HELLO.CBL` apprend à lire et reformater un texte simple.
- `STRINGIT.CBL` formalise le traitement de chaînes avec plusieurs zones et concaténations.
- `INVCALC.CBL` applique ces techniques à des données structurées et monétaires.
- `ATTRACT.CBL` montre que la même logique peut servir à des calculs scientifiques à virgule flottante.
- Les autres fichiers approfondissent des cas particuliers ou des sous-thèmes.

### Conclusion sur la cohérence du dépôt
Le dépôt est cohérent parce que chaque programme est une variation sur un même thème : orchestrer des données structurées, les transformer, les calculer et les afficher. La différence réside dans la nature des données et le type d'opération effectuée.

---

## 9. Conclusion technique

Le dépôt `AltecaGuillaumeG/test` est un corpus pédagogique de programmes COBOL entièrement orienté démonstration. Son intérêt technique est qu'il ne se limite pas à un seul sujet de programmation ; il couvre plusieurs dimensions du langage :

- gestion de chaînes,
- manipulation de données structurées,
- calcul arithmétique,
- conditions et états nommés,
- tableaux, boucles et itérations,
- calculs monétaires,
- calculs scientifiques,
- I/O homme-machine via la console.

Le point central est la forte cohérence entre les programmes : chacun illustre un mécanisme COBOL précis, mais tous partageant la même philosophie de modélisation du problème sous forme de données explicites et de flux de calculs structurés.

C'est donc un dépôt très comme un ensemble de "micro-cas d'utilisation COBOL" conçu pour enseigner la logique de programmation du langage à travers des exemples simples, autonomes et directement exploitables.

---

## 10. Fichiers principaux à retenir

- `HELLO.CBL` : base du texte et des chaînes
- `STRINGIT.CBL` : traitement avancé des chaînes
- `INVCALC.CBL` : logique métier et monétaire
- `ATTRACT.CBL` : calcul scientifique et flottants
- `COND88.CBL` : états logiques nommés
- `DATE1.CBL`, `DATE2.CBL` : gestion des dates
- `IFEVAL.CBL` : conditions et séquencement
- `HEX2TEXT.CBL`, `MOVEME.CBL`, `NOTBOOL.CBL`, `REFORMER.CBL` : exemples complémentaires

Le dépôt fonctionne comme un kit d'apprentissage structuré en modules thématiques, où chaque fichier est à la fois autonome et complémentaire des autres.
