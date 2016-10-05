<properties 
   pageTitle="Utilisation des fonctions fenêtre U-SQL dans les travaux Analytique Data Lake Azure | Azure" 
   description="Apprenez à utiliser les fonctions de fenêtre U-SQL. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="05/16/2016"
   ms.author="edmaca"/>


# Utilisation des fonctions U-SQL dans les travaux Analytique Data Lake Azure  

Les fonctions de fenêtre ont été intégrées à la norme SQL ISO/ANSI en 2003. U-SQL adopte un sous-ensemble de fonctions Windows comme défini par la norme ANSI SQL Standard.

Les fonctions de fenêtre sont utilisées pour faire des calculs dans les ensembles de lignes nommées *fenêtres*. Les fenêtres sont définies par la clause OVER. Les fonctions de fenêtre peuvent résoudre certains scénarios clés de façon très efficace.

Ce guide de formation utilise deux jeux de données exemple pour vous guider dans un exemple de scénario dans lequel vous pouvez appliquer des fonctions de fenêtre. Pour plus d’informations, consultez [Référence U-SQL](http://go.microsoft.com/fwlink/p/?LinkId=691348).

Les fonctions de fenêtre sont classées dans les catégories :

- [Fonctions de rapport d’agrégation](#reporting-aggregation-functions), telles que SUM ou AVG
- [Fonctions de classement](#ranking-functions), par exemple DENSE\_RANK, ROW\_NUMBER, NTILE et RANK
- Les [fonctions analytiques](#analytic-functions) telles que la distribution cumulative, les centiles ou les accès à des données depuis une ligne précédente dans le même ensemble de résultats sans utilisation d’une jointure automatique

**Configuration requise :**

- Examinez les deux didacticiels suivants :

    - [Prise en main des outils Azure Data Lake Tools pour Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
    - [Prise en main de l’utilisation U-SQL pour les travaux d’Analytique Data Lake Azure](data-lake-analytics-u-sql-get-started.md).
- Créer un compte d’analytique Data Lake comme indiqué dans [Prise en main des outils de l’utilisation Data Lake Azure Tools pour Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Créer un projet Visual Studio U-SQL, comme indiqué dans [prise en main de l’utilisation de U-SQL pour les travaux Analytique Azure Data Lake](data-lake-analytics-u-sql-get-started.md).

## Exemples de jeux de données

Ce didacticiel utilise deux jeux de données :

- QueryLog

    QueryLog présente une liste de ce que les personnes ont recherché dans le moteur de recherche. Chaque journal des requêtes inclut :
    
        - Query - What the user was searching for.
        - Latency - How fast the query came back to the user in milliseconds.
        - Vertical - What kind of content the user was interested in (Web links, Images, Videos).
    
    Copiez et collez le script suivant dans votre projet U-SQL pour former l’ensemble de lignes QueryLog :
    
        @querylog = 
            SELECT * FROM ( VALUES
                ("Banana"  , 300, "Image" ),
                ("Cherry"  , 300, "Image" ),
                ("Durian"  , 500, "Image" ),
                ("Apple"   , 100, "Web"   ),
                ("Fig"     , 200, "Web"   ),
                ("Papaya"  , 200, "Web"   ),
                ("Avocado" , 300, "Web"   ),
                ("Cherry"  , 400, "Web"   ),
                ("Durian"  , 500, "Web"   ) )
            AS T(Query,Latency,Vertical);
    
    Dans la pratique, les données sont probablement stockées dans un fichier de données. Vous accéderez à ces données dans un fichier délimité par des tabulations en utilisant le code suivant :
    
        @querylog = 
        EXTRACT 
            Query    string, 
            Latency  int, 
            Vertical string
        FROM "/Samples/QueryLog.tsv"
        USING Extractors.Tsv();

- Employés

    Le jeu de données Employé inclut les champs suivants :
   
        - EmpID - Employee ID.
        - EmpName  Employee name.
        - DeptName - Department name. 
        - DeptID - Deparment ID.
        - Salary - Employee salary.

    Copiez et collez le script suivant dans votre projet U-SQL pour former un ensemble de lignes Employés :

        @employees = 
            SELECT * FROM ( VALUES
                (1, "Noah",   "Engineering", 100, 10000),
                (2, "Sophia", "Engineering", 100, 20000),
                (3, "Liam",   "Engineering", 100, 30000),
                (4, "Emma",   "HR",          200, 10000),
                (5, "Jacob",  "HR",          200, 10000),
                (6, "Olivia", "HR",          200, 10000),
                (7, "Mason",  "Executive",   300, 50000),
                (8, "Ava",    "Marketing",   400, 15000),
                (9, "Ethan",  "Marketing",   400, 10000) )
            AS T(EmpID, EmpName, DeptName, DeptID, Salary);
    
    L’instruction qui suit illustre la création de l’ensemble de lignes en l’extrayant d’un fichier de données.
    
        @employees = 
        EXTRACT 
            EmpID    int, 
            EmpName  string, 
            DeptName string, 
            DeptID   int, 
            Salary   int
        FROM "/Samples/Employees.tsv"
        USING Extractors.Tsv();

Lorsque vous testez les exemples dans le didacticiel, vous devez inclure les définitions de l’ensemble de lignes. U-SQL exige que vous définissiez uniquement les ensembles de lignes utilisés. Certains exemples n’ont besoin que d’un ensemble de lignes.

Vous devez également ajouter l’instruction suivante pour la sortie de l’ensemble de lignes de résultat vers un fichier de données :

    OUTPUT @result TO "/wfresult.csv" 
        USING Outputters.Csv();
 
 La plupart des exemples utilisent la variable appelée **@result** pour les résultats.

## Comparer les fonctions de fenêtre au regroupement

Les concepts de fenêtrage et de regroupement sont proches, mais cependant différents. Il peut être utile de connaître cette relation.

### Utiliser l’agrégation et le regroupement

La requête suivante utilise l’agrégation pour calculer le salaire total de tous les employés :

    @result = 
        SELECT 
            SUM(Salary) AS TotalSalary
        FROM @employees;
    
>[AZURE.NOTE] Pour obtenir des instructions pour le test et la vérification, consultez [Prise en main de l’utilisation de U-SQL pour les travaux Analytique Data Lake Azure](data-lake-analytics-u-sql-get-started.md).

Le résultat est une seule ligne avec une seule colonne. 165 000 $ est la somme de la valeur Salaire correspondant à la table entière.

|TotalSalary
|-----------
|165000

>[AZURE.NOTE] Si vous êtes novice en matière de fonctions de fenêtres, il peut être utile de rappeler les nombres dans les sorties.

L’instruction suivante utilise la clause GROUP BY pour calculer le salaire total correspondant à chaque service :

    @result=
        SELECT DeptName, SUM(Salary) AS SalaryByDept
        FROM @employees
        GROUP BY DeptName;

Les résultats sont :

|DeptName|SalaryByDept
|--------|------------
|Ingénierie|60000
|HR|30000
|Responsable|50000
|Marketing|25000

La somme de la colonne SalaryByDept est 165 000 $, qui correspond à la quantité dans le dernier script.
 
Dans ces deux cas, le nombre de lignes de sortie est inférieur à celui des lignes d’entrée :
 
- Sans GROUP BY, l’agrégation réduit toutes les lignes dans une seule ligne.
- Avec GROUP BY, il y a N lignes de sortie, N étant le nombre de valeurs distinctes apparaissant dans les données, dans ce cas, la sortie contiendra 4 lignes.

###  Utiliser une fonction de fenêtre

La clause OVER dans l’exemple suivant est vide. Définit la « fenêtre » qui inclura toutes les lignes. Dans cet exemple, la somme SUM est appliquée à la clause OVER qu’elle précède.

Vous pouvez lire cette requête comme suit : « La somme des salaires sur une fenêtre contenant toutes les lignes ».

    @result=
        SELECT
            EmpName,
            SUM(Salary) OVER( ) AS SalaryAllDepts
        FROM @employees;

Contrairement à GROUP BY, il y a autant de lignes de sortie que de lignes d’entrée :

|EmpName|TotalAllDepts
|-------|--------------------
|Noah|165000
|Sophia|165000
|Liam|165000
|Emma|165000
|Jacob|165000
|Olivia|165000
|Mason|165000
|Ava|165000
|Ethan|165000


La valeur 165000 (le total de tous les salaires) est placée dans chaque ligne de sortie. Ce total est calculé à partir de la « fenêtre » de toutes les lignes, et inclut donc tous les salaires.

Les exemples qui suivent montrent comment affiner la « fenêtre » pour répertorier tous les employés, le service et le salaire total pour le service. PARTITION BY est ajouté à la clause OVER.

    @result=
    SELECT
        EmpName, DeptName,
        SUM(Salary) OVER( PARTITION BY DeptName ) AS SalaryByDept
    FROM @employees;

Les résultats sont :

|EmpName|DeptName|SalaryByDep
|-------|--------|-------------------
|Noah|Ingénierie|60000
|Sophia|Ingénierie|60000
|Liam|Ingénierie|60000
|Mason|Responsable|50000
|Emma|HR|30000
|Jacob|HR|30000
|Olivia|HR|30000
|Ava|Marketing|25000
|Ethan|Marketing|25000

Là encore, il existe le même nombre de lignes en entrée qu’en sortie. Toutefois, chaque ligne comporte le salaire total d’un service correspondant.




## Fonctions d’agrégation de rapport

Les fonctions de fenêtre prennent également en charge les agrégats suivants :

- COUNT
- SUM
- MIN
- MAX
- MOY
- STDEV
- VAR

La syntaxe :

    <AggregateFunction>( [DISTINCT] <expression>) [<OVER_clause>]

Remarque :

- Par défaut, les fonctions agrégées, excepté COUNT, ignorent les valeurs nulles.
- Lorsque les fonctions d’agrégation sont spécifiées avec la clause OVER, la clause ORDER BY n’est pas autorisée dans la clause OVER.

### Utiliser SUM

L’exemple suivant ajoute le total du salaire par service pour chaque ligne d’entrée :
 
    @result=
        SELECT 
            *,
            SUM(Salary) OVER( PARTITION BY DeptName ) AS TotalByDept
        FROM @employees;

Voici la sortie :

|EmpID|EmpName|DeptName|DeptID|Salaire|TotalByDept
|-----|-------|--------|------|------|-----------
|1|Noah|Ingénierie|100|10000|60000
|2|Sophia|Ingénierie|100|20000|60000
|3|Liam|Ingénierie|100|30000|60000
|7|Mason|Responsable|300|50000|50000
|4|Emma|HR|200|10000|30000
|5|Jacob|HR|200|10000|30000
|6|Olivia|HR|200|10000|30000
|8|Ava|Marketing|400|15000|25000
|9|Ethan|Marketing|400|10000|25000

### NOMBRE d’utilisations

L’exemple suivant ajoute un champ supplémentaire à chaque ligne pour afficher le nombre total d’employés de chaque service.

    @result =
        SELECT *, 
            COUNT(*) OVER(PARTITION BY DeptName) AS CountByDept 
        FROM @employees;

Résultat :

|EmpID|EmpName|DeptName|DeptID|Salaire|CountByDept
|-----|-------|--------|------|------|-----------
|1|Noah|Ingénierie|100|10000|3
|2|Sophia|Ingénierie|100|20000|3
|3|Liam|Ingénierie|100|30000|3
|7|Mason|Responsable|300|50000|1
|4|Emma|HR|200|10000|3
|5|Jacob|HR|200|10000|3
|6|Olivia|HR|200|10000|3
|8|Ava|Marketing|400|15000|2
|9|Ethan|Marketing|400|10000|2


### Utilisez MIN et MAX

L’exemple suivant ajoute un champ supplémentaire à chaque ligne pour afficher le salaire minimum pratiqué dans chaque service.

    @result =
        SELECT 
            *,
            MIN(Salary) OVER( PARTITION BY DeptName ) AS MinSalary
        FROM @employees;

Résultats :

|EmpID|EmpName|DeptName|DeptID|Salaire|MinSalary
|-----|-------|--------|------|-------------|----------------
|1|Noah|Ingénierie|100|10000|10000
|2|Sophia|Ingénierie|100|20000|10000
|3|Liam|Ingénierie|100|30000|10000
|7|Mason|Responsable|300|50000|50000
|4|Emma|HR|200|10000|10000
|5|Jacob|HR|200|10000|10000
|6|Olivia|HR|200|10000|10000
|8|Ava|Marketing|400|15000|10000
|9|Ethan|Marketing|400|10000|10000

Remplacez MIN par MAX, puis faites un essai.


## Fonctions de classement

Les fonctions de classement renvoient une valeur de classement (longue) pour chaque ligne de chaque partition comme défini par les clauses PARTITION BY et OVER. L’ordre du rang est contrôlé par ORDER BY dans la clause OVER.

Vous trouverez plus loin les fonctions de classement prises en charge :

- RANK
- DENSE\_RANK
- NTILE
- ROW\_NUMBER

**Syntaxe :**

	[ RANK() | DENSE_RANK() | ROW_NUMBER() | NTILE(<numgroups>) ]
	    OVER (
	        [PARTITION BY <identifier, > …[n]]
	        [ORDER BY <identifier, > …[n] [ASC|DESC]] 
	) AS <alias>

- La clause ORDER BY est facultative pour les fonctions de classement. Si ORDER BY est spécifié, il détermine l’ordre de classement. Si ORDER BY n’est pas spécifié, U-SQL affecte des valeurs en fonction de l’ordre, dans lequel il lit l’enregistrement. Il en résulte une valeur non déterminante de nombre de ligne, de classement ou de classement sans vide dans le cas où ordre par clause n’est pas spécifié.
- NTILE exige une expression qui s’évalue en entier positif. Ce nombre indique le nombre de groupes en lequel chaque partition doit être divisée. Cet identificateur est utilisé uniquement avec la fonction de classement de NTILE.

Pour plus d’informations sur la clause OVER, consultez [Référence U-SQL]().

ROW\_NUMBER, RANK et DENSE\_RANK affectent des numéros de lignes dans une fenêtre. Plutôt qu’aborder les sujets séparément, il est plus intuitif de voir comment ils répondent à la même entrée.

    @result =
    SELECT 
        *,
        ROW_NUMBER() OVER (PARTITION BY Vertical ORDER BY Latency) AS RowNumber,
        RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS Rank, 
        DENSE_RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS DenseRank 
    FROM @querylog;
        
Notez que les clauses OVER sont identiques. Résultat :

|Interroger|Latence : int|Vertical|RowNumber|Rank|DenseRank
|-----|-----------|--------|--------------|---------|--------------
|Banane|300|Image|1|1|1
|Cerise|300|Image|2|1|1
|Durion|500|Image|3|3|2
|Pomme|100|Web|1|1|1
|Figue|200|Web|2|2|2
|Papaye|200|Web|3|2|2
|Figue|300|Web|4|4|3
|Cerise|400|Web|5|5|4
|Durion|500|Web|6|6|5

### ROW\_NUMBER

Dans chaque fenêtre (Vertical, Image ou Web), le nombre de lignes est incrémenté de 1 classé par latence.

![Fonction fenêtre ROW\_NUMBER de fenêtre U-SQL](./media/data-lake-analytics-use-windowing-functions/u-sql-windowing-function-row-number-result.png)

### RANK

Différent de ROW\_NUMBER(), RANK() prend en compte la valeur de latence spécifiée dans la clause ORDER BY de la fenêtre.

RANK commence par (1,1,3), car les deux premières valeurs de latence sont les mêmes. Puis la valeur suivante est 3, car la valeur de latence est passée à 500. L’objectif clé étant que même si les valeurs en double se voient appliquer le même classement, le nombre RANK « passe » à la valeur ROW\_NUMBER suivante. Vous pouvez voir ce schéma se répéter avec la suite (2,2,4) dans le Web vertical.

![Fonction fenêtre RANK U-SQL](./media/data-lake-analytics-use-windowing-functions/u-sql-windowing-function-rank-result.png)

### DENSE\_RANK
	
DENSE\_RANK est similaire à RANK, sauf qu’il ne « passe » pas au ROW\_NUMBER suivant, au lieu de ça, il passe au numéro suivant dans la séquence. Notez les séquences: (1,1,2) et (2,2,3) dans l’exemple.

![Fonction fenêtre DENSE\_RANK U-SQL](./media/data-lake-analytics-use-windowing-functions/u-sql-windowing-function-dense-rank-result.png)

### Remarques

- Si ORDER BY n’est pas spécifié, la fonction de classement est appliquée à l’ensemble de lignes sans respecter un ordre. Cela débouche sur un comportement non déterminant sur la façon dont la fonction de classement est appliquée
- Il n’existe aucune garantie que les lignes retournées par une requête à l’aide de ROW\_NUMBER respecteront exactement le même ordre avec chaque exécution, sauf si les conditions suivantes sont remplies.

	- Les valeurs de la colonne partitionnée sont uniques.
	- Les valeurs des colonnes ORDER BY sont uniques.
	- Les combinaisons de valeurs de la colonne de partition et les colonnes ORDER BY sont uniques.

### NTILE

NTILE distribue les lignes d’une partition ordonnée dans un nombre spécifié de groupes. Les groupes sont numérotés à partir de un.


L’exemple qui suit fractionne l’ensemble de lignes dans chaque partition (vertical) en 4 groupes dans l’ordre de latence de la requête et renvoie le numéro de groupe de chaque ligne.

La verticale de l’Image comporte 3 lignes, et donc, est composée de 3 groupes.

La verticale Web comporte 6 lignes, les deux lignes supplémentaires sont réparties entre les deux premiers groupes. C’est pour cette raison qu’il existe 2 lignes dans le groupe 1 et le groupe 2 et seulement 1 ligne dans les groupes 3 et 4.

    @result =
        SELECT 
            *,
            NTILE(4) OVER(PARTITION BY Vertical ORDER BY Latency) AS Quartile   
        FROM @querylog;
		
Résultats :

|Interroger|Latence|Vertical|Quartile
|-----|-----------|--------|-------------
|Banane|300|Image|1
|Cerise|300|Image|2
|Durion|500|Image|3
|Pomme|100|Web|1
|Figue|200|Web|1
|Papaye|200|Web|2
|Figue|300|Web|2
|Cerise|400|Web|3
|Durion|500|Web|4

NTILE prend un paramètre (« numgroups »). Numgroups est un entier positif ou une expression constante longue qui spécifie le nombre de groupes en lequel chaque partition doit être divisée.

- Si le nombre de lignes présentes dans la partition est divisible par numgroups, alors les groupes auront tous une taille égale.
- Si le nombre de lignes d’une partition n’est pas divisible par numgroups, les groupes auront deux tailles différentes à cause d’un membre. Les groupes plus grands viennent avant les plus petits dans l’ordre spécifié par la clause OVER.

Par exemple :

- 100 lignes divisées en 4 groupes : [25, 25, 25, 25]
- 102 lignes divisées en 4 groupes : [26, 26, 25, 25]


### Enregistrements Top N par Partition via RANK, DENSE\_RANK ou ROW\_NUMBER

Nombre d’utilisateurs souhaitent sélectionner uniquement n premières (TOP) lignes par groupe. Cela n’est pas possible avec la clause GROUP BY traditionnelle.

Vous avez vu l’exemple suivant au début de la section des fonctions de classement. Elle n’affiche pas les N premiers enregistrement de chaque partition :

    @result =
    SELECT 
        *,
        ROW_NUMBER() OVER (PARTITION BY Vertical ORDER BY Latency) AS RowNumber,
        RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS Rank,
        DENSE_RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS DenseRank
    FROM @querylog;

Résultats :

|Interroger|Latence|Vertical|Rank|DenseRank|RowNumber
|-----|-----------|--------|---------|--------------|--------------
|Banane|300|Image|1|1|1
|Cerise|300|Image|1|1|2
|Durion|500|Image|3|2|3
|Pomme|100|Web|1|1|1
|Figue|200|Web|2|2|2
|Papaye|200|Web|2|2|3
|Figue|300|Web|4|3|4
|Cerise|400|Web|5|4|5
|Durion|500|Web|6|5|6

### TOP N avec DENSE RANK

L’exemple suivant retourne les 3 premiers enregistrements de chaque groupe sans espace dans la numérotation de classement séquentiel de chaque partition de fenêtrage.

    @result =
    SELECT 
        *,
        DENSE_RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS DenseRank
    FROM @querylog;
    
    @result = 
        SELECT *
        FROM @result
        WHERE DenseRank <= 3;

Résultats :

|Interroger|Latence|Vertical|DenseRank
|-----|-----------|--------|--------------
|Banane|300|Image|1
|Cerise|300|Image|1
|Durion|500|Image|2
|Pomme|100|Web|1
|Figue|200|Web|2
|Papaye|200|Web|2
|Figue|300|Web|3

### TOP N avec RANK

    @result =
        SELECT 
            *,
            RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS Rank
        FROM @querylog;
    
    @result = 
        SELECT *
        FROM @result
        WHERE Rank <= 3;

Résultats :

|Interroger|Latence|Vertical|Rank
|-----|-----------|--------|---------
|Banane|300|Image|1
|Cerise|300|Image|1
|Durion|500|Image|3
|Pomme|100|Web|1
|Figue|200|Web|2
|Papaye|200|Web|2


### TOP N avec ROW\_NUMBER

    @result =
        SELECT 
            *,
            ROW_NUMBER() OVER (PARTITION BY Vertical ORDER BY Latency) AS RowNumber
        FROM @querylog;
    
    @result = 
        SELECT *
        FROM @result
        WHERE RowNumber <= 3;

Résultats :
    
|Interroger|Latence|Vertical|RowNumber
|-----|-----------|--------|--------------
|Banane|300|Image|1
|Cerise|300|Image|2
|Durion|500|Image|3
|Pomme|100|Web|1
|Figue|200|Web|2
|Papaye|200|Web|3

### Affecter un numéro de ligne unique global

Il est souvent utile d’affecter un numéro unique à chaque ligne. C’est facile (et plus efficace que l’utilisation d’un réducteur) avec les fonctions de classement.

    @result =
        SELECT 
            *,
            ROW_NUMBER() OVER () AS RowNumber
        FROM @querylog;

<!-- ################################################### -->
## Fonctions analytiques

Les fonctions analytiques sont utilisées pour comprendre les distributions de valeurs dans les fenêtres. Le scénario le plus courant pour l’utilisation de fonctions analytiques est le calcul de centiles.

**Prise en charge des fonctions de fenêtre analytique**

- CUME\_DIST
- PERCENT\_RANK
- PERCENTILE\_CONT
- PERCENTILE\_DISC

### CUME\_DIST  

CUME\_DIST calcule la position relative d’une valeur spécifiée dans un groupe de valeurs. Elle calcule le pourcentage de requêtes qui ont une latence inférieure ou égale à la latence de la requête actuelle dans la même verticale. Pour une ligne R, en supposant l’utilisation d’un ordre croissant, le CUME\_DIST de R correspond au nombre de lignes avec des valeurs inférieures ou égales à la valeur de R, divisé par le nombre de lignes évaluées dans le jeu de résultats de la partition ou de la requête. CUME\_DIST renvoie des nombres dans la plage 0 < x < = 1.

**Syntaxe**

    CUME_DIST() 
        OVER (
            [PARTITION BY <identifier, > …[n]]
            ORDER BY <identifier, > …[n] [ASC|DESC] 
    ) AS <alias>

L’exemple suivant utilise la fonction CUME\_DIST pour calculer le centile de latence pour chaque requête au sein d’une verticale.

    @result=
        SELECT 
            *,
            CUME_DIST() OVER(PARTITION BY Vertical ORDER BY Latency) AS CumeDist
        FROM @querylog;

Résultats :
    
|Interroger|Latence|Vertical|CumeDist
|-----|-----------|--------|---------------
|Durion|500|Image|1
|Banane|300|Image|0\.666666666666667
|Cerise|300|Image|0\.666666666666667
|Durion|500|Web|1
|Cerise|400|Web|0\.833333333333333
|Figue|300|Web|0\.666666666666667
|Figue|200|Web|0\.5
|Papaye|200|Web|0\.5
|Pomme|100|Web|0\.166666666666667

Il existe 6 lignes dans la partition dont la clé de partition est « Web » (4 lignes et vers le bas) :

- Il existe des 6 lignes avec une valeur égale ou inférieure à 500, donc le CUME\_DIST est égal à 6/6 = 1
- Il existe 5 lignes avec une valeur égale ou inférieure à 400, et donc CUME\_DIST est égal à 5/6 = 0,83.
- Il existe 4 lignes avec une valeur égale ou inférieure à 300, et CUME\_DIST est donc égal à 4/6 = 0,66
- Il existe 3 lignes avec une valeur égale ou inférieure à 200, et donc CUME\_DIST est égal à 3/6 = 0,5. Il existe deux lignes avec la même valeur de latence.
- Il existe 1 ligne avec une valeur égale ou inférieure à 100, et donc CUME\_DIST est égal à 1/6 = 0,16.


**Notes d’utilisation :**

- Les valeurs de lien sont toujours évaluées à la même valeur de distribution cumulative.
- Les valeurs NULL sont traitées comme les valeurs les plus basses possible.
- Vous devez spécifier la clause ORDER BY pour calculer CUME\_DIST.
- CUME\_DIST s’apparente à la fonction PERCENT\_RANK

Remarque : La clause ORDER BY n’est pas autorisée si l’instruction SELECT n’est pas suivie de OUTPUT. Ainsi, la clause ORDER BY dans l’instruction OUTPUT détermine l’ordre d’affichage de l’ensemble de lignes qui en résulte.


### PERCENT\_RANK

PERCENT\_RANK calcule le classement relatif d’une ligne dans un groupe de lignes. PERCENT\_RANK est utilisé pour évaluer la position relative d’une valeur dans un ensemble de lignes ou une partition. La plage de valeurs retournée par PERCENT\_RANK est supérieure à 0 et inférieure ou égale à 1. Contrairement à CUME\_DIST, la première ligne de PERCENT\_RANK est toujours 0.
	
**Syntaxe**

    PERCENT_RANK() 
        OVER (
            [PARTITION BY <identifier, > …[n]]
            ORDER BY <identifier, > …[n] [ASC|DESC] 
        ) AS <alias>

**Remarques**

- La première ligne de n’importe quel jeu a une valeur PERCENT\_RANK de 0.
- Les valeurs NULL sont traitées comme les valeurs les plus basses possible.
- Vous devez spécifier la clause ORDER BY pour calculer PERCENT\_RANK.
- CUME\_DIST s’apparente à la fonction PERCENT\_RANK


L’exemple suivant utilise la fonction PERCENT\_RANK. pour calculer le centile de latence pour chaque requête au sein d’une verticale.

La clause PARTITION BY est spécifiée pour partitionner les lignes du jeu de résultats défini par la verticale. La clause ORDER BY dans la clause OVER met les lignes dans l’ordre dans chaque partition.

La valeur retournée par la fonction PERCENT\_RANK représente le classement de la latence des requêtes dans un vertical sous forme de pourcentage.


    @result=
        SELECT 
            *,
            PERCENT_RANK() OVER(PARTITION BY Vertical ORDER BY Latency) AS PercentRank
        FROM @querylog;

Résultats :

|Interroger|Latence : int|Vertical|PercentRank
|-----|-----------|--------|------------------
|Banane|300|Image|0
|Cerise|300|Image|0
|Durion|500|Image|1
|Pomme|100|Web|0
|Figue|200|Web|0\.2
|Papaye|200|Web|0\.2
|Figue|300|Web|0,6
|Cerise|400|Web|0,8
|Durion|500|Web|1

### PERCENTILE\_CONT et PERCENTILE\_DISC

Ces deux fonctions calculent un centile basé sur une distribution continue ou discrète des valeurs de colonne.

**Syntaxe**

    [PERCENTILE_CONT | PERCENTILE_DISC] \( numeric_literal ) 
        WITHIN GROUP ( ORDER BY <identifier> [ ASC | DESC ] )
        OVER ( [ PARTITION BY <identifier,>…[n] ] ) AS <alias>

**numeric\_literal** -centile à calculer. La valeur doit être comprise entre 0,0 et 1,0.

WITHIN GROUP (ORDER BY <identificateur> [ASC | DESC]) - spécifie une liste de valeurs numériques à trier et pour lesquelles calculer le centile. Un seul identificateur de colonne est autorisé. L’expression doit correspondre à un type numérique. Les autres types de données ne sont pas autorisés. L’ordre de tri par défaut est croissant.

OVER ([PARTITION BY <identificateur,>... [n] ] ) - divise l’ensemble de lignes d’entrée en fonction de la clé de partition à laquelle la fonction centile est appliquée. Pour plus d’informations, voir la section RANKING du présent document. Remarque : Toutes les valeurs null présentes dans le jeu de données sont ignorées.

**PERCENTILE\_CONT** calcule un centile en fonction d’une distribution continue ou discrète de la valeur de colonne. Le résultat est interpolé et peut ne pas être égal à une des valeurs spécifiques de la colonne.

**PERCENTILE\_DISC** calcule le centile selon une répartition discrète des valeurs de colonne. Le résultat est égal à une valeur spécifique de la colonne. En d’autres termes, PERCENTILE\_DISC, à l’inverse de PERCENTILE\_CONT, retourne toujours une valeur réelle (entrée d’origine).

Vous pouvez voir comment les deux fonctionnent dans l’exemple ci-dessous, qui essaie de trouver la valeur médiane (centile = 0,50) de latence dans chaque Verticale

    @result = 
        SELECT 
            Vertical, 
            Query,
            PERCENTILE_CONT(0.5) 
                WITHIN GROUP (ORDER BY Latency)
                OVER ( PARTITION BY Vertical ) AS PercentileCont50,
            PERCENTILE_DISC(0.5) 
                WITHIN GROUP (ORDER BY Latency) 
                OVER ( PARTITION BY Vertical ) AS PercentileDisc50 
        
        FROM @querylog;

Résultats :

|Interroger|Latence : int|Vertical|PercentileCont50|PercentilDisc50
|-----|-----------|--------|-------------------|----------------
|Banane|300|Image|300|300
|Cerise|300|Image|300|300
|Durion|500|Image|300|300
|Pomme|100|Web|250|200
|Figue|200|Web|250|200
|Papaye|200|Web|250|200
|Figue|300|Web|250|200
|Cerise|400|Web|250|200
|Durion|500|Web|250|200


Pour PERCENTILE\_CONT, comme les valeurs peuvent être interpolées, la valeur médiane pour le web est 250 même si aucune requête dans le site web vertical a une latence de 250 seulement.

PERCENTILE\_DISC n’interpole pas les valeurs, et par conséquent, la valeur médiane pour le Web est de 200 - c’est-à-dire une réelle valeur trouvée dans les lignes d’entrée.











## Voir aussi

- [Vue d'ensemble de Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
- [Prise en main de Data Lake Analytics à l'aide du portail Azure](data-lake-analytics-get-started-portal.md)
- [Prise en main de Data Lake Analytics à l'aide d'Azure PowerShell](data-lake-analytics-get-started-powershell.md)
- [Développer des scripts de U-SQL à l’aide d’outils Data Lake Tools pour Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
- [Utilisation des didacticiels interactifs d’Analytique Data Lake Azure](data-lake-analytics-use-interactive-tutorials.md)
- [Analyser les journaux du site Web à l’aide de l’analytique Data Lake Azure](data-lake-analytics-analyze-weblogs.md)
- [Prise en main du langage U-SQL Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md)
- [Gestion d'Azure Data Lake Analytics à l'aide du portail Azure](data-lake-analytics-manage-use-portal.md)
- [Gestion d'Azure Data Lake Analytics à l'aide d'Azure PowerShell](data-lake-analytics-manage-use-powershell.md)
- [Surveiller et résoudre les problèmes des tâches Azure Data Lake Analytics à l’aide du portail Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

<!---HONumber=AcomDC_0914_2016-->