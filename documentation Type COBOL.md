# Documentation technique du dépôt COBOL

## 0. Cartographie des liens entre programmes

Le dépôt peut être vu comme un ensemble de modules pédagogiques qui s’appuient sur une même base syntaxique et conceptuelle, tout en traitant des cas d’usage différents.

Voici la cartographie fonctionnelle des relations entre programmes :

- `HELLO.CBL`
  - point d’entrée pédagogique du dépôt,
  - introduit la lecture utilisateur et le traitement de chaînes,
  - sert de base à `STRINGIT.CBL` pour la logique de concaténation et de formatage.

- `STRINGIT.CBL`
  - enrichit la logique de chaînes avec plusieurs champs, calcul de longueurs, découpage, concaténation et sortie formatée,
  - est le programme de référence pour les manipulations textuelles avancées,
  - prépare le terrain pour les exemples de données structurées métier comme `INVCALC.CBL`.

- `INVCALC.CBL`
  - applique la même logique de structuration des données à un cas métier réel : facture, taxe, lignes d’article, totaux,
  - introduit les structures de tableau dépendantes, les calculs monétaires et les états nommés,
  - est le programme central du dépôt pour la logique de gestion.

- `ATTRACT.CBL`
  - s’écarte du secteur financier pour introduire la logique scientifique et les nombres flottants,
  - montre que COBOL est exploitable dans d’autres domaines que la gestion,
  - complète la palette du dépôt en variant le type de calcul de données.

- Fichiers spécialisés complémentaires
  - `DATE1.CBL`, `DATE2.CBL` : gestion des dates et formatage temporel,
  - `IFEVAL.CBL`, `COND88.CBL` : conditions et états logiques,
  - `HEX2TEXT.CBL`, `MOVEME.CBL`, `REFORMER.CBL` : manipulation de données et reformattage,
  - `NOTBOOL.CBL` : logique booléenne et négation,
  - `CPSEQFR.CBL`, `CPSEQVR.CBL` : séquences et parcours de données,
  - `BRAKES.CBL` : logique de contrôle / décisions appliquées.

Schéma synthétique :

```
HELLO.CBL
   ↓
STRINGIT.CBL
   ↓
INVCALC.CBL
   ↓
(compléments spécialisés : dates, conditions, conversion, logique)

ATTRACT.CBL
   └── parallèle métier / scientifique
```

Cette cartographie montre que le dépôt est construit comme une progression verticale :

- des chaînes simples,
- vers la manipulation structurée,
- vers les calculs métier,
- vers les cas scientifiques et spécialisés.

---

## 1. Contexte général

Le dépôt `AltecaGuillaumeG/test` est un ensemble de programmes COBOL de démonstration, organisés comme des exercices autonomes mais cohérents entre eux. Il ne s’agit pas d’une application unique, mais d’un corpus pédagogique qui illustre les mécanismes fondamentaux du langage COBOL à travers des cas d’usage très variés.

Le projet est entièrement développé en COBOL, avec des fichiers `.CBL` placés à la racine du dépôt. Chaque fichier correspond à un objectif précis : manipulation de texte, calcul monétaire, logique conditionnelle, traitement de dates, opérateurs de comparaison, données à virgule flottante, etc.

La logique globale du dépôt est la suivante :

- définir des données structurées,
- les manipuler avec des instructions COBOL standard,
- calculer ou formater des résultats,
- afficher les résultats sur la console.

Les programmes sont donc liés par un même cadre technique : structure en divisions, données explicites, cycle logique basé sur `MOVE`, `IF`, `PERFORM`, `STRING`, `INSPECT`, `DISPLAY`, `ACCEPT`, etc.

---

## 2. Structure commune des programmes

Tous les fichiers suivent la même structure COBOL :

- `IDENTIFICATION DIVISION` : identification du programme
- `DATA DIVISION` : variables et structures de données
- `PROCEDURE DIVISION` : logique d’exécution

À l’intérieur de la `DATA DIVISION`, on retrouve typiquement :

- `WORKING-STORAGE SECTION`
- variables de type `PIC X`, `PIC 9`, `PIC S9`
- zones composites de données
- tableaux / occurrences
- champs d’aide à l’affichage
- variables numériques avec `COMP`, `COMP-3`, `COMP-2`

Au niveau de la `PROCEDURE DIVISION`, les programmes manipulent :

- des données en mémoire,
- des boucles de traitement,
- des conditions,
- des calculs arithmétiques,
- des opérations de texte,
- des sorties sur console.

---

## 3. Analyse programme par programme

### 3.1. `HELLO.CBL`

#### Objectif technique
Ce programme est le plus simple du dépôt et sert de point d’entrée pédagogique. Il montre comment un programme COBOL lit une information utilisateur, traite une chaîne, puis affiche un résultat formaté.

#### Ce qu’il fait
- affiche un message d’invite,
- lit une valeur saisie par l’utilisateur,
- élimine les espaces de fin de chaîne,
- construit un message du type :
  `Hello, <nom>!`

#### Éléments techniques significatifs
- `DISPLAY` et `ACCEPT` pour l’interaction console
- `PIC X(80)` pour stocker le nom saisi
- `INSPECT FUNCTION REVERSE(...) TALLYING ... FOR LEADING SPACES`
- `STRING ... INTO ...` pour assembler le message final

#### Relation avec les autres programmes
`HELLO.CBL` est le programme de base du dépôt. Il introduit la logique de gestion de données textuelles. Les programmes suivants, notamment `STRINGIT.CBL`, reprennent cette logique mais en la complexifiant avec plusieurs zones et opérations avancées.

---

### 3.2. `STRINGIT.CBL`

#### Objectif technique
Ce programme est le meilleur exemple du dépôt pour illustrer les manipulations avancées de chaînes de caractères en COBOL.

#### Ce qu’il fait
Le programme traite des noms structurés et les reformate dans l’ordre `Prénom - Deuxième prénom - Nom`. Il montre différentes méthodes de transformation d’un texte.

#### Éléments techniques significatifs
- `WS-FAMILY-NAME`, `WS-GIVEN-NAME`, `WS-MIDDLE-NAME`
- `WS-NAME-LENGTHS` pour stocker les longueurs calculées
- `INSPECT ... BEFORE INITIAL SPACE`
- référence partielle comme :
  `WS-GIVEN-NAME(1:WS-GIVEN-NAME-LENGTH)`
- `STRING ... DELIMITED BY SPACE` et `DELIMITED BY SIZE`
- `WS-OUTPUT-AREA(1:WS-OUTPUT-LENGTH)` pour afficher seulement une sous-zone

#### Pourquoi c’est important
Il permet de comprendre :

- comment mesurer la longueur d’un mot sans espaces,
- comment découper une chaîne avec précision,
- comment reconcaténer plusieurs sous-chaînes,
- comment formater une sortie texte propre.

#### Relation avec les autres programmes
`STRINGIT.CBL` est la version technique et généralisée de `HELLO.CBL`. Si `HELLO.CBL` présente un cas simple, `STRINGIT.CBL` montre les mécanismes utilisés dans les vrais traitements de chaîne en COBOL.

---

### 3.3. `INVCALC.CBL`

#### Objectif technique
C’est le programme le plus orienté “métier” du dépôt. Il modélise une facture et calcule les montants hors taxe, avec taxe et total final.

#### Ce qu’il fait
- initialise une structure de facture,
- charge des lignes d’article,
- applique les quantités et prix unitaires,
- calcule la taxe pour les articles taxables,
- additionne les montants,
- affiche le détail de la facture.

#### Éléments techniques significatifs
- `INV-LINE OCCURS 1 TO 100 DEPENDING ON INV-LINE-ITEM-COUNT`
- `PIC S9(07)V99 COMP-3` pour les montants
- `88` levels : `IS-RETURN`, `TAXABLE-ITEM`, `NONTAXABLE-ITEM`
- `PERFORM ... VARYING ... UNTIL ...`
- `MULTIPLY` et `ADD`
- `INITIALIZE ... REPLACING ALPHANUMERIC DATA BY SPACES NUMERIC DATA BY ZEROES`

#### Pourquoi c’est important
Ce programme illustre le COBOL d’application de gestion :
- données structurées,
- montants décimaux,
- calculs cumulés,
- logique de validation,
- impression d’un document de type facture.

#### Relation avec les autres programmes
`INVCALC.CBL` reprend le même principe de structuration des données que `STRINGIT.CBL`, mais sur des données économiques. Le point de lien est la logique de construction de structures, calculs, puis sortie formatée. Le dépôt passe ainsi d’un traitement texte vers un traitement de données métier.

---

### 3.4. `ATTRACT.CBL`

#### Objectif technique
Ce programme montre que COBOL peut aussi traiter des calculs scientifiques, avec des nombres flottants et des formules mathématiques.

#### Ce qu’il fait
- collecte les attributs de deux corps,
- calcule leur distance,
- calcule la force gravitationnelle,
- affiche les composantes selon les axes X et Y.

#### Éléments techniques significatifs
- `COMP-2` pour les variables réelles
- `Body occurs 2 indexed by BodyIx`
- `FUNCTION SQRT`
- `FUNCTION COS`
- `FUNCTION SIN`
- `FUNCTION ATAN`
- `ACCEPT` de valeurs utilisateur

#### Pourquoi c’est important
Ce fichier confirme la polyvalence du langage COBOL : il n’est pas réservé aux applications de gestion. Le dépôt montre donc deux grandes familles de logique :

- traitement de texte / données métier (`HELLO`, `STRINGIT`, `INVCALC`)
- calcul scientifique / flottants (`ATTRACT`)

#### Relation avec les autres programmes
`ATTRACT.CBL` complète le corpus en démontrant que les mêmes principes de structuration et de calcul COBOL peuvent s’appliquer à des problèmes non financiers.

---

## 4. Les programmes complémentaires

Le dépôt contient plusieurs autres programmes, chacun ajoutant un angle de compréhension différent du COBOL.

### `DATE1.CBL` et `DATE2.CBL`
- manipulation des dates,
- conversion et formatage,
- traitement de valeurs temporelles.

### `IFEVAL.CBL`
- logique conditionnelle avancée,
- utilisation de structures `IF` plus complexes.

### `COND88.CBL`
- démonstration de conditions nommées avec `88` levels,
- logique d’états d’une donnée.

### `BRAKES.CBL`
- exemple de logique d’automatisation ou de contrôle,
- probablement orienté logique de décision ou traitement de cas.

### `HEX2TEXT.CBL`
- conversion de données hexadécimales en texte.

### `MOVEME.CBL`
- déplacement et recopie de données entre zones.

### `NOTBOOL.CBL`
- logique booléenne et négation.

### `REFORMER.CBL`
- reformattage, transformation de chaîne.

### `CPSEQFR.CBL` / `CPSEQVR.CBL`
- traitement de séquences, ordre, comparaison ou parcours de valeurs.

Ces fichiers ne sont pas des applications complètes ; ils sont plutôt des modules de démonstration, destinés à illustrer des constructions spécifiques du langage COBOL.

---

## 5. Analyse des liens entre les programmes

Le dépôt est cohérent à plusieurs niveaux.

### 5.1. Progression pédagogique
On peut lire les programmes comme une progression :

1. `HELLO.CBL` : base du traitement texte
2. `STRINGIT.CBL` : traitement textuel avancé
3. `INVCALC.CBL` : données structurées et calculs métier
4. `ATTRACT.CBL` : calcul scientifique / flottants
5. autres exemples spécialisés : dates, conditions, conversion, logique

### 5.2. Même schéma structurel
Tous les programmes sont basés sur une architecture COBOL standard :

- `IDENTIFICATION DIVISION`
- `DATA DIVISION`
- `PROCEDURE DIVISION`

Les différences résident dans la nature des données et la logique de calcul.

### 5.3. Mécanismes transverses
Les programmes utilisent un ensemble commun de mécanismes COBOL :

- `MOVE`
- `IF`
- `PERFORM`
- `DISPLAY`
- `ACCEPT`
- `STRING`
- `INSPECT`
- `INITIALIZE`
- `MULTIPLY`
- `ADD`
- `COMPUTE`
- `88` conditions

Cela montre que le dépôt est construit comme un ensemble de briques syntaxiques réutilisées dans plusieurs contextes.

---

## 6. Ce que le dépôt révèle du langage COBOL

Le dépôt illustre les points forts suivants du COBOL :

- grande clarté de définition des données,
- modélisation très structurée des informations,
- excellente capacité de calcul sur données numériques,
- robustesse du traitement textuel,
- capacité de formater des sorties lisibles,
- support nativement adapté aux applications de gestion,
- possibilité d’utiliser le langage pour des cas scientifiques ou techniques.

L’intérêt du dépôt n’est donc pas seulement de montrer des programmes isolés ; c’est de montrer la polyvalence d’un langage structurée, fiable, et très orienté données.

---

## 7. Conclusion technique

Le dépôt `AltecaGuillaumeG/test` constitue un corpus d’exemples COBOL cohérent et pédagogique. Chaque programme a un rôle défini et un objectif précis :

- `HELLO.CBL` : saisir et formater un texte,
- `STRINGIT.CBL` : manipuler des chaînes de manière structurée,
- `INVCALC.CBL` : calculer des montants et des taxes,
- `ATTRACT.CBL` : travailler avec des nombres flottants en calcul scientifique,
- les autres fichiers : démonstrations de sous-ensembles syntaxiques du langage.

Ces programmes sont liés par la même logique de conception : définir des données, appliquer des opérations, calculer des résultats, puis présenter les informations de manière claire. C’est précisément cela qui fait de ce dépôt une bonne base d’apprentissage du COBOL en tant que langage de programmation structuré et orienté traitement de données.

---

## 8. Fichiers principaux à retenir

- `HELLO.CBL` : base du texte et des chaînes
- `STRINGIT.CBL` : traitement avancé des chaînes
- `INVCALC.CBL` : logique métier et monétaire
- `ATTRACT.CBL` : calcul scientifique et flottants
- `COND88.CBL` : états logiques nommés
- `DATE1.CBL`, `DATE2.CBL` : gestion des dates
- `IFEVAL.CBL` : conditions et séquencement
- `HEX2TEXT.CBL`, `MOVEME.CBL`, `NOTBOOL.CBL`, `REFORMER.CBL` : exemples complémentaires

Le dépôt fonctionne comme un kit d'apprentissage structuré en modules thématiques, où chaque fichier est à la fois autonome et complémentaire des autres.
