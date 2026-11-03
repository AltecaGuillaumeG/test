# Documentation d’architecture technique — dépôt COBOL

## 1. Cartographie des liens entre programmes

Le dépôt rassemble des programmes COBOL autonomes, organisés par thèmes : traitement de texte, données métier, dates, conditions, représentation des données et fichiers séquentiels. La cohérence est principalement pédagogique et technique : les sources illustrent des mécanismes COBOL dans des contextes variés. Le code ne montre pas de chaîne d’exécution reliant tous les programmes.

### Dépendances explicites

Deux programmes appellent `HEX2TEXT` avec `CALL ... USING` et une zone de données compatible avec sa `LINKAGE SECTION` :

```text
MOVEME ─────┐
            ├── CALL 'HEX2TEXT' ──> HEX2TEXT
REFORMER ───┘
```

Les autres relations sont thématiques, et non des appels entre programmes :

```text
HELLO ──(traitement de texte, du simple au composé)──> STRINGIT
STRINGIT ──(données structurées et formatage)────────> REFORMER
INVCALC ──(calculs de gestion)────────────────────────> exemples métier
DATE1 / DATE2 ──(date et heure)       IFEVAL / COND88 / NOTBOOL ──(conditions)
CPSEQFR / CPSEQVR ──(fichiers séquentiels)  ATTRACT / BRAKES ──(calculs scientifiques)
MOVEME / REFORMER ──(représentation/conversion)───────> HEX2TEXT
```

`HELLO`, `STRINGIT`, `INVCALC` et `ATTRACT` ne se référencent pas directement dans le code. Leur ordre peut servir de lecture pédagogique, mais il ne constitue pas un flux applicatif. `HEX2TEXT` est le seul programme dont l’interface de sous-programme est visible ; les autres exemples sont lancés séparément.

### Niveaux fonctionnels

- **Fondamentaux** : `HELLO` (interaction et chaîne), `MOVEME` (affectations et représentation mémoire), `IFEVAL` et `COND88` (conditions).
- **Intermédiaires** : `STRINGIT` (transformation de texte), `DATE1` et `DATE2` (dates/heure), `CPSEQFR` et `CPSEQVR` (entrées-sorties séquentielles).
- **Spécialisés** : `INVCALC` (calcul de facture), `REFORMER` (conversion de format d’enregistrement), `HEX2TEXT` (conversion en caractères hexadécimaux), `NOTBOOL` (conventions booléennes), `BRAKES` et `ATTRACT` (calculs à virgule flottante).

Ces niveaux sont une classification documentaire fondée sur le sujet des programmes, pas une hiérarchie de dépendances.

## 2. Analyse détaillée par fichier

### `ATTRACT.CBL` — programme `ATTRACT`

- **Objectif et logique :** saisit les attributs de deux corps, calcule leur distance et une force gravitationnelle, puis affiche des valeurs et composantes selon X/Y. Démonstration scientifique à nombres flottants, explicitement présentée comme exemple.
- **Données clés :** tableau `Body OCCURS 2` (masse, vitesses et positions), index `BodyIx`, constantes et intermédiaires `G`, `dx`, `dy`, `d`, `f`, `theta`, `fx`, `fy`, tous en `COMP-2`.
- **Mécanismes :** `PERFORM ... VARYING`, paragraphes `SolicitAttributesOfBody`, `VerifyAttributesOfBody`, `ComputeAttraction`, `ACCEPT`, `COMPUTE`, fonctions `SQRT`, `ATAN`, `COS`, `SIN`, conditions et `DISPLAY`.
- **Singularité / relation :** seul exemple de calcul gravitationnel interactif. Il est autonome et ne partage pas de module de calcul avec `BRAKES`. Le commentaire du code signale que le calcul directionnel est une approximation erronée pour la démonstration ; il ne faut donc pas interpréter `fx` et `fy` comme une décomposition scientifique validée.

### `BRAKES.CBL` — programme `BRAKES`

- **Objectif et logique :** calcule l’élévation de température des freins d’un camion à partir de valeurs constantes et de l’énergie potentielle perdue ; calcul d’ingénierie.
- **Données clés :** masse des freins `m`, chaleur spécifique `sh`, masse du camion `w`, dénivelé `d`, accélération `a`, puis `Mgh`, `mc` et `deltaT-Celsius`, en `COMP-2`.
- **Mécanismes :** `COMPUTE`, `MOVE`, édition d’affichage par `PIC Z,ZZ9.99`, `DISPLAY`, `GOBACK`.
- **Singularité / relation :** contrairement à `ATTRACT`, les données sont codées en dur et il n’y a pas de saisie console. Le lien avec `ATTRACT` est le thème du calcul scientifique, pas une dépendance de code.

### `COND88.CBL` — programme `COND88`

- **Objectif et logique :** exemples de noms de condition de niveau 88 appliqués à des indicateurs, catégories et plages d’âge.
- **Données clés :** conditions `SIMPLE-88`, `SIMPLE-88-WITH-FALSE`, `CATEGORY-A`, `CATEGORY-B` et plages `PERSON-IS-A-...` définies sur `PERSON-AGE`.
- **Mécanismes :** `SET ... TO TRUE/FALSE`, `IF`, `NOT`, `EVALUATE TRUE`, `MOVE`. Le commentaire précise que le programme n’émet pas de sortie.
- **Singularité / relation :** illustration dédiée aux niveaux 88 ; les mêmes conditions nommées apparaissent concrètement dans `INVCALC`, `REFORMER` et d’autres fichiers.

### `CPSEQFR.CBL` — programme `CPSEQFR`

- **Objectif et logique :** lit un fichier séquentiel à enregistrements fixes de 40 caractères, inverse les zones de 10 et 30 caractères dans l’enregistrement de sortie, compte les enregistrements traités.
- **Données clés :** fichiers `INFILE` / `OUTFILE`, `INPUT-RECORD`, `OUTPUT-RECORD`, statuts fichier avec conditions 88 et `RECORD-COUNT`.
- **Mécanismes :** `SELECT`, `FILE-CONTROL`, `FD`, `OPEN`, `READ`, `WRITE`, `CLOSE`, `FILE STATUS`, `PERFORM UNTIL`, `MOVE`, `ADD`, `GO TO` pour les sorties d’erreur.
- **Singularité / relation :** exemple d’E/S de fichier à longueur fixe ; complémentaire de `CPSEQVR`, qui déclare des enregistrements variables. Les noms de fichiers externes attendus sont `INFILE1` et `OUTFILE1`.

### `CPSEQVR.CBL` — programme `CPSEQVR`

- **Objectif et logique :** lit des enregistrements de longueur variable de `VARFILE1`, ajoute un champ de cinq caractères `XXXXX`, écrit le résultat dans `VARFILE2` et compte les enregistrements.
- **Données clés :** `IN-RECLEN`, `OUT-RECLEN`, `FIELD-COUNT`, `RECORD-AREA` et `RECORD-FIELD OCCURS 1 TO 10 DEPENDING ON FIELD-COUNT`.
- **Mécanismes :** déclarations de fichiers en mode `V`, longueur définie par `DEPENDING ON`, `READ`, `WRITE ... FROM`, calculs de longueur et boucle `PERFORM UNTIL END-OF-INPUT`.
- **Singularité / relation :** variante à enregistrements variables de `CPSEQFR`. Les deux fichiers utilisent des statuts de fichiers et traitent des données externes ; aucun jeu de données ni script de lancement n’est présent parmi les sources COBOL examinées.

### `DATE1.CBL` — programme `DATE1`

- **Objectif et logique :** obtient la date et l’heure système avec `FUNCTION CURRENT-DATE` et produit plusieurs présentations, dont un horodatage de type NCSA, une date longue et des formats abrégés US/Europe.
- **Données clés :** groupe `CURRENT-DATE-DATA`, `NCSA-TIMESTAMP`, zones de date abrégée, table de mois redéfinie avec `REDEFINES` / `OCCURS`, et table des suffixes ordinaux.
- **Mécanismes :** `MOVE`, `MOVE CORRESPONDING`, `DIVIDE ... REMAINDER`, `STRING`, références qualifiées et `DISPLAY`.
- **Singularité / relation :** exploite la fonction intrinsèque `CURRENT-DATE`, tandis que `DATE2` illustre les formes de `ACCEPT ... FROM` pour obtenir les données temporelles. Les deux programmes sont indépendants.

### `DATE2.CBL` — programme `DATE2`

- **Objectif et logique :** récupère date, jour de l’année, jour de semaine et heure via les formes `ACCEPT ... FROM DATE`, `DAY`, `DAY-OF-WEEK` et `TIME`, puis construit des formats lisibles.
- **Données clés :** groupes pour les formats de date/heure, tables redéfinies de noms de mois et de jours, table des suffixes ordinaux et `FULL-DATE`.
- **Mécanismes :** `ACCEPT`, `STRING`, `DIVIDE ... REMAINDER`, `MOVE`, références qualifiées et `DISPLAY`.
- **Singularité / relation :** met en regard plusieurs sources conceptuelles de date/heure et des présentations textuelles. Complément thématique de `DATE1`, sans appel entre les deux.

### `HELLO.CBL` — programme `HELLO`

- **Objectif et logique :** demande un nom à l’utilisateur, retire les espaces de fin et affiche un message de salutation.
- **Données clés :** `WS-FRIEND`, `WS-GREETING`, `WS-EXCLAMATION-POINT`, `WS-MESSAGE` et compteur `WS-TRAILING-SPACES`.
- **Mécanismes :** `DISPLAY`, `ACCEPT`, `INSPECT FUNCTION REVERSE(...) TALLYING ... FOR LEADING SPACES`, puis `STRING` avec limites de longueur.
- **Singularité / relation :** exemple interactif minimal de manipulation de chaîne. `STRINGIT` explore d’autres transformations de texte, mais n’est pas appelé par `HELLO`.

### `HEX2TEXT.CBL` — programme `HEX2TEXT`

- **Objectif et logique :** sous-programme qui transforme chaque octet d’une zone en deux caractères hexadécimaux.
- **Données clés :** interface `LS-HEX2TEXT-CONVERSION` en `LINKAGE SECTION` (`LS-ORIGINAL-LENGTH`, `LS-ORIGINAL-VALUE`, `LS-RESULT`), table `HEXSTR` et variables de division/calcul.
- **Mécanismes :** `PROCEDURE DIVISION USING`, boucle `PERFORM VARYING`, `REDEFINES` pour accéder à l’octet, `DIVIDE ... REMAINDER`, calculs d’indices et `MOVE` par référence modifiée.
- **Singularité / relation :** module réutilisable explicitement appelé par `MOVEME` et `REFORMER`. L’interface commune est une zone de données de longueur, valeur d’origine et résultat ; les deux appelants en déclarent une structure correspondante.

### `IFEVAL.CBL` — identifiant déclaré `COND88`

- **Objectif et logique :** exemples de comparaison alphanumérique et numérique, contrôle de validité numérique, protection d’une division et conditions combinées.
- **Données clés :** `ALPHA-1`, `ALPHA-2`, `NUMERIC-1`, `NUMERIC-2` et `RESULT-OF-COMPARE`.
- **Mécanismes :** `IF/ELSE`, `EVALUATE TRUE`, `EVALUATE TRUE ALSO TRUE`, tests `IS NUMERIC`, comparaisons, `DIVIDE`, `SUBTRACT` et références modifiées.
- **Singularité / relation :** le commentaire indique que le code sert de référence et n’émet pas de sortie. Point d’attention vérifiable : le nom de fichier est `IFEVAL.CBL`, mais son `PROGRAM-ID` est `COND88`, comme celui de `COND88.CBL`; cela peut compliquer la compilation ou l’identification selon l’outil utilisé. Le code n’appelle aucun autre programme.

### `INVCALC.CBL` — programme `INVCALC`

- **Objectif et logique :** initialise des données de facture d’exemple, calcule les montants avant/après taxe et la taxe, puis affiche le récapitulatif et les lignes. Logique de gestion monétaire.
- **Données clés :** groupe `INVOICE`, lignes `INV-LINE OCCURS 1 TO 100 DEPENDING ON INV-LINE-ITEM-COUNT`, prix et cumuls en `COMP-3`, taux `SALES-TAX-RATE`, indicateurs 88 `IS-RETURN`, `TAXABLE-ITEM` et `NONTAXABLE-ITEM`, zones de présentation.
- **Mécanismes :** `INITIALIZE ... REPLACING`, `PERFORM ... VARYING`, tests `IS NUMERIC`, `MULTIPLY`, `ADD`, `IF`, noms de condition 88 et formatage par `PIC` édités. Les paragraphes `INVALID-INVOICE-DATA` et `PRINT-INVOICE-DETAILS` sont internes au programme.
- **Singularité / relation :** exemple le plus riche de structure de données métier et de calcul décimal. Il est autonome et ne partage pas de service avec `STRINGIT`. À noter dans le code : pour la troisième ligne, l’instruction de sélection taxable vise `TAXABLE-ITEM(1)` plutôt que l’index de la ligne, ce qui semble être une incohérence d’indexation.

### `MOVEME.CBL` — programme `MOVEME`

- **Objectif et logique :** démontre les effets de `MOVE` sur des champs alphanumériques, binaires, décimaux compactés, flottants et numériques édités ; compare aussi `INITIALIZE` et `MOVE SPACES`.
- **Données clés :** zones `COMP`, `COMP-3`, `COMP-1`, `COMP-2`, champs d’affichage et `WS-GROUP-ITEM` contenant une condition 88 et une table dépendante.
- **Mécanismes :** nombreux `MOVE`, `INITIALIZE`, `LENGTH OF`, `DISPLAY` et appels `CALL 'HEX2TEXT' USING WS-HEX2TEXT-CONVERSION`.
- **Singularité / relation :** fournit une observation hexadécimale des représentations mémoire, transmise au module `HEX2TEXT`. Les résultats binaires peuvent dépendre de la plateforme et de sa représentation des données, comme le signalent les commentaires du programme.

### `NOTBOOL.CBL` — programme `NOTBOOL`

- **Objectif et logique :** compare différentes conventions pour représenter des valeurs booléennes : caractères, valeurs numériques et noms de condition de niveau 88.
- **Données clés :** indicateurs `EX2-FLAG` à `EX7-FLAG`, valeurs conventionnelles `T/F`, `Y/N`, `1/0` et conditions 88.
- **Mécanismes :** `MOVE`, `IF/ELSE`, `EVALUATE`, `SET ... TO TRUE/FALSE`, `COMPUTE`. Une section BIT est laissée en commentaire et signale une incompatibilité avec GnuCOBOL.
- **Singularité / relation :** n’émet pas de sortie selon son commentaire ; sert de référence sur les conventions booléennes. Proche de `COND88` par sujet, sans dépendance de code.

### `REFORMER.CBL` — programme `REFORMER`

- **Objectif et logique :** transforme un enregistrement d’exemple reçu d’un partenaire en format interne : date, SKU, indicateur taxable et données numériques.
- **Données clés :** `INPUT-RECORD`, `OUTPUT-RECORD`, table de correspondance des préfixes SKU redéfinie, indicateur 88 `TAXABLE-ITEM`, zones temporaires de date et structure d’appel `HEX2TEXT`.
- **Mécanismes :** `UNSTRING`, `FUNCTION CURRENT-DATE`, `SEARCH`, `STRING`, `IF/ELSE`, `SET` sur 88, `MOVE`, `PERFORM VARYING`, référence modifiée et appel `HEX2TEXT` pour afficher l’enregistrement en hexadécimal.
- **Singularité / relation :** exemple d’intégration de plusieurs techniques autour d’un enregistrement métier. Il appelle explicitement `HEX2TEXT`, comme `MOVEME`. Le programme charge les données d’entrée dans le code ; il ne lit pas de fichier externe.

### `STRINGIT.CBL` — programme `STRINGIT`

- **Objectif et logique :** montre plusieurs façons de réordonner et reformater prénom, deuxième prénom et nom.
- **Données clés :** zones de nom `WS-NAME`, groupe de sortie, longueurs calculées et `WS-OUTPUT-AREA`.
- **Mécanismes :** `MOVE`, `INSPECT ... TALLYING ... FOR CHARACTERS BEFORE INITIAL SPACE`, `STRING`, `INITIALIZE`, `ADD` et références modifiées.
- **Singularité / relation :** compare l’assemblage de zones complètes aux sous-chaînes calculées. Complément pédagogique de `HELLO` et techniques de texte également présentes dans `REFORMER`, sans lien d’exécution direct.

## 3. Fichiers principaux à retenir

- `HEX2TEXT.CBL` : sous-programme de conversion hexadécimale, partagé par `MOVEME` et `REFORMER`.
- `MOVEME.CBL` : démonstration des mouvements de données et de leur représentation.
- `REFORMER.CBL` : transformation d’un enregistrement métier et exemple d’appel à `HEX2TEXT`.
- `INVCALC.CBL` : calcul structuré d’une facture et de ses taxes.
- `HELLO.CBL` et `STRINGIT.CBL` : exemples progressifs de saisie et de manipulation textuelle.
- `CPSEQFR.CBL` et `CPSEQVR.CBL` : traitement de fichiers séquentiels fixes et variables.
- `DATE1.CBL` et `DATE2.CBL` : obtention et formatage de dates/heures.
- `COND88.CBL`, `IFEVAL.CBL` et `NOTBOOL.CBL` : conditions, comparaisons et conventions booléennes.
- `BRAKES.CBL` et `ATTRACT.CBL` : calculs d’ingénierie/scientifiques à virgule flottante.

## Synthèse architecturale

Le dépôt est un corpus de programmes COBOL distincts, majoritairement démonstratifs, dont la cohérence réside dans l’exploration de structures de données, de traitements et de formats de sortie. La réutilisation logicielle est limitée mais concrète autour du sous-programme `HEX2TEXT`; les autres rapprochements décrivent des thèmes communs et ne doivent pas être lus comme une architecture d’application intégrée. Les exemples couvrent ainsi des données textuelles et métier, des calculs numériques, des conditions et des E/S séquentielles sans orchestrateur commun visible dans les fichiers étudiés.
