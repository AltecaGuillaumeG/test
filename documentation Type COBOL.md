# Documentation du projet COBOL

## 1. Présentation générale

Ce dépôt, nommé `AltecaGuillaumeG/test`, contient une série de programmes COBOL de démonstration destinés à illustrer les concepts de base de la programmation COBOL ainsi que certaines constructions avancées utilisées dans les applications de type métier et scientifique.

Le dépôt est entièrement composé en COBOL (`100% COBOL`), ce qui confirme qu'il s'agit d'un projet pédagogique orienté apprentissage du langage.

La description du dépôt est la suivante :

> document pour test IA

Le but principal du projet est de présenter des exemples de code COBOL variés pour montrer :

- la structure des programmes COBOL,
- la gestion des données et des formats d'affichage,
- les instructions de calcul et de traitement conditionnel,
- la manipulation de chaînes de caractères,
- la gestion des dates et des montants,
- l'usage de tableaux, de conditions, et d'instructions d'entrée/sortie.

---

## 2. Contexte du dépôt

Le dépôt contient plusieurs fichiers `.CBL` représentant des mini-programmes indépendants, chacun ayant pour objectif de démontrer un aspect particulier du langage COBOL.

Les programmes présents à la racine incluent notamment :

- `HELLO.CBL`
- `STRINGIT.CBL`
- `INVCALC.CBL`
- `ATTRACT.CBL`
- `DATE1.CBL`
- `DATE2.CBL`
- `IFEVAL.CBL`
- `COND88.CBL`
- `BRAKES.CBL`
- `HEX2TEXT.CBL`
- `MOVEME.CBL`
- `NOTBOOL.CBL`
- `REFORMER.CBL`
- `CPSEQFR.CBL`
- `CPSEQVR.CBL`

Le dépôt a donc une logique de bibliothèque d'exemples pédagogiques plutôt qu'un système applicatif complet.

---

## 3. Structure typique d'un programme COBOL

Les fichiers du dépôt suivent le schéma habituel de COBOL :

1. `IDENTIFICATION DIVISION`
   - identification du programme,
   - nom du programme (`PROGRAM-ID`).

2. `DATA DIVISION`
   - définition des variables,
   - zones de travail (`WORKING-STORAGE SECTION`),
   - fichiers, structures, tableaux, données numériques et alphabétiques.

3. `PROCEDURE DIVISION`
   - logique exécutive du programme,
   - traitement, calculs, affichage, validation, boucles, conditionnels.

Cette organisation est visible dans tous les programmes, notamment dans `HELLO.CBL` et `INVCALC.CBL`.

---

## 4. Analyse des programmes principaux

### 4.1. `HELLO.CBL`

Ce programme est un exemple de base centré sur la saisie d'un nom et son affichage sous forme de message de bienvenue.

Fonctionnalités principales :

- affichage d'un message d'invite,
- saisie d'une valeur via `ACCEPT`,
- suppression des espaces en fin de chaîne,
- utilisation de `INSPECT` et `REVERSE`,
- construction d'une chaîne finale avec `STRING`.

Exemple de logique :

- le programme demande à l'utilisateur d'entrer un nom ;
- il identifie le nombre d'espaces à la fin de la chaîne ;
- il construit un message du type : `Hello, Jean!`.

Ce programme illustre les notions suivantes :

- `PIC X(...)` pour les chaînes de caractères,
- `FUNCTION REVERSE(...)`,
- `INSPECT ... TALLYING ... FOR LEADING SPACES`,
- `STRING ... INTO ...`,
- `DISPLAY` et `ACCEPT`.

---

### 4.2. `STRINGIT.CBL`

Ce programme démontre la manipulation avancée des données textuelles.

Il traite notamment :

- `MOVE` entre zones de données,
- `INSPECT` pour calculer la longueur d'une chaîne,
- `reference modification` comme `WS-GIVEN-NAME(1:WS-GIVEN-NAME-LENGTH)`,
- `STRING` pour concaténer des éléments,
- `UNSTRING` et autres techniques de micro-traitement de texte (présentes dans les thèmes du fichier et des exemples associés).

Il montre comment assembler des noms dans l'ordre `Prénom - Deuxième prénom - Nom`. Le code met en œuvre plusieurs approches :

- déplacement simple de données,
- calcul de longueur avant espace,
- concaténation avec espace,
- construction d'une zone de sortie à partir de sous-chaînes.

Ce programme est très utile pour comprendre la gestion des chaînes et la logique de formatage en COBOL.

---

### 4.3. `INVCALC.CBL`

Ce programme est un exemple de logique de traitement comptable et de facturation.

Il met en œuvre :

- des tableaux de lignes de facture,
- des montants monétaires en `COMP-3`,
- des données conditionnelles avec `88` levels,
- le calcul du total avant taxe,
- le calcul de la taxe de vente,
- le calcul du total final,
- l'affichage d'une facture formatée.

Les concepts qu'il illustre sont :

- `PIC S9(07)V99 COMP-3` pour les valeurs monétaires,
- `OCCURS` pour les tableaux de lignes,
- `88` conditions nommées (`IS-RETURN`, `TAXABLE-ITEM`, `NONTAXABLE-ITEM`),
- `PERFORM ... VARYING`,
- `MULTIPLY`, `ADD`, `MOVE`, `IF`, `ELSE`, `END-IF`.

C'est un bon exemple de logique métier en COBOL et correspond à des cas typiques d'applications de comptabilité ou de gestion.

---

### 4.4. `ATTRACT.CBL`

Ce programme est un exemple plus scientifique, bien que le langage COBOL soit surtout associé aux applications de gestion.

Il calcule l'attraction gravitationnelle entre deux corps dans l'espace en utilisant des données à virgule flottante (`COMP-2`).

Il expose :

- l'usage de données flottantes,
- la lecture de paramètres utilisateur via `ACCEPT`,
- le calcul de distance et de force,
- l'affichage des résultats,
- l'utilisation de fonctions mathématiques `SQRT`, `COS`, `SIN`, `ATAN`.

Même si ce programme est davantage démonstratif que pratique, il montre que COBOL peut être utilisé au-delà du domaine purement financier.

---

## 5. Concepts COBOL mis en évidence dans le dépôt

### 5.1. Types et formats de données

Les variables utilisent des descriptions de type comme :

- `PIC X(...)` : caractères alphanumériques,
- `PIC 9(...)` : chiffres,
- `PIC S9(...)` : nombres signés,
- `PIC ... COMP-3` : données numériques compactées,
- `PIC ... COMP-2` : nombres à virgule flottante.

Exemples observés :

- `PIC X(80) VALUE SPACES`
- `PIC S9(07)V99 COMP-3`
- `PIC SV9(5) COMP-3`
- `PIC X(10)`
- `PIC 9.`

### 5.2. Conditions nommées

L'instruction `88` permet de définir des états logiques à partir d'une variable.

Exemples :

- `88 IS-RETURN VALUE 'R'.`
- `88 TAXABLE-ITEM VALUE 'T'.`

Cela permet d'écrire un code plus lisible que de comparer des valeurs brutes.

### 5.3. Boucles et exécution conditionnelle

Les programmes utilisent :

- `IF ... END-IF`,
- `EVALUATE` (présent dans d'autres exemples du dépôt),
- `PERFORM ... VARYING`,
- `PERFORM` de sections et sous-routines,
- `GOBACK` pour terminer un programme.

### 5.4. Traitement de texte

Les exemples montrent la force de COBOL pour manipuler des chaînes :

- découpage,
- concaténation,
- conversion de valeurs,
- retrait d'espaces,
- référence partielle à une sous-zone (`A(1:10)`).

### 5.5. Calculs numériques

Les programmes utilisent largement :

- `ADD`,
- `SUBTRACT`,
- `MULTIPLY`,
- `DIVIDE`,
- `COMPUTE`,
- `MOVE`,
- `INITIALIZE`.

---

## 6. Points forts pédagogiques du dépôt

Le dépôt est particulièrement utile pour :

- apprendre la syntaxe COBOL de manière progressive,
- comprendre l'organisation en divisions,
- découvrir les types de variables et les formats d'affichage,
- étudier les traitements de chaîne et de valeurs numériques,
- se familiariser avec les programmes de type application métier.

Les exemples sont simples, en petits morceaux, et permettent de consolider les bases sans introduire une complexité excessive.

---

## 7. Exemple de compilation et d'exécution

Sous GNU COBOL, un programme peut généralement être compilé avec une commande du type :

```bash
cobc -x HELLO.CBL -o HELLO
./HELLO
```

Pour un autre programme :

```bash
cobc -x INVCALC.CBL -o INVCALC
./INVCALC
```

Les détails exacts peuvent varier selon l'environnement, mais le principe reste identique : compilation puis exécution du binaire généré.

---

## 8. Conclusion

Le dépôt `AltecaGuillaumeG/test` constitue un recueil d'exemples COBOL orientés pédagogie. Il couvre un large éventail de notions essentielles, allant des bases de la langue aux structures métiers les plus courantes : manipulation de chaînes, calculs, conditions, tableaux, dates, monnaies et affichage utilisateur.

L'ensemble est cohérent avec un objectif d'apprentissage et de démonstration de la puissance de COBOL dans plusieurs contextes, notamment la gestion commerciale et la manipulation de données structurées.

Cette documentation offre une vue synthétique du projet et constitue une base de référence pour comprendre les exemples présents dans le dépôt.

---

## 9. Fichiers principaux du dépôt

- `HELLO.CBL` : salutation et traitement de chaînes
- `STRINGIT.CBL` : concaténation, référence partielle, inspection de chaînes
- `INVCALC.CBL` : calcul d'une facture avec taxes
- `ATTRACT.CBL` : calcul scientifique avec flottants
- `DATE1.CBL`, `DATE2.CBL` : manipulation des dates
- `COND88.CBL`, `IFEVAL.CBL` : conditions et structures de contrôle
- `BRAKES.CBL`, `HEX2TEXT.CBL`, `MOVEME.CBL`, `NOTBOOL.CBL`, `REFORMER.CBL` : exemples complémentaires de logique COBOL

---

Document généré dans le cadre de l'analyse du dépôt COBOL `AltecaGuillaumeG/test`.
