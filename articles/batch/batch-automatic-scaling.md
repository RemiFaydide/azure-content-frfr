<properties
	pageTitle="Mettre automatiquement à l’échelle les nœuds de calcul dans un pool Azure Batch | Microsoft Azure"
	description="Activer la mise à l’échelle automatique sur un pool de cloud pour ajuster dynamiquement le nombre de nœuds de calcul dans le pool."
	services="batch"
	documentationCenter=""
	authors="mmacy"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="batch"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="multiple"
	ms.date="07/21/2016"
	ms.author="marsma"/>

# Mettre automatiquement à l’échelle les nœuds de calcul dans un pool Azure Batch

Grâce à la mise à l’échelle automatique, le service Azure Batch peut ajouter ou supprimer de manière dynamique des nœuds de calcul dans un pool en fonction des paramètres que vous définissez. Vous pouvez potentiellement gagner du temps et de l’argent en ajustant automatiquement la quantité de puissance de calcul utilisée par votre application : ajoutez des nœuds lorsque les demandes des tâches de votre travail augmentent et supprimez-les lorsqu’elles diminuent.

Vous activez la mise à l’échelle automatique sur un pool de nœuds de calcul en lui associant une *formule de mise à l’échelle automatique* que vous définissez selon le même principe que la méthode [PoolOperations.EnableAutoScale][net_enableautoscale] dans la bibliothèque [Batch .NET](batch-dotnet-get-started.md). Le service Batch utilise ensuite cette formule pour déterminer le nombre de nœuds de calcul nécessaires à l’exécution de vos charges de travail. Le service Batch répond aux échantillons de données de mesure de service collectés à intervalles réguliers, et ajuste le nombre de nœuds de calcul du pool à un intervalle configurable en fonction de la formule associée.

Vous pouvez activer la mise à l’échelle automatique lors de la création d’un pool ou dans un pool existant. Vous pouvez également modifier une formule existante dans un pool dont la mise à l’échelle automatique est activée. Le service Batch vous permet d’évaluer vos formules avant de les assigner à des pools et de surveiller l’état de l’exécution des mises à l’échelle automatiques.

## Formules de mise à l’échelle automatique

Une formule de mise à l’échelle automatique est une valeur de chaîne qui contient une ou plusieurs instructions, et qui est affectée à un élément de pool [autoScaleFormula][rest_autoscaleformula] \(REST de Batch) ou à la propriété [CloudPool.AutoScaleFormula][net_cloudpool_autoscaleformula] \(.NET de Batch). Une fois affectées à un pool, le service Batch utilise votre formule pour déterminer le nombre de nœuds de calcul cibles d’un pool pour le prochain intervalle de traitement (les intervalles seront décrits en détail dans la suite de cet article). La chaîne de formule ne peut pas dépasser 8 Ko. Elle peut inclure jusqu’à 100 instructions séparées par des points-virgules, et peut comprendre des sauts de ligne et des commentaires.

Les formules de mise à l’échelle automatique reviennent en quelque sorte à utiliser un « langage » de mise à l’échelle Batch. Les instructions de formules sont des expressions de forme libre qui peuvent inclure des variables définies par le service (variables définies par le service Batch) et des variables définies par l’utilisateur (variables que vous définissez). Elles peuvent effectuer différentes opérations sur ces valeurs à l’aide de types, d’opérateurs et de fonctions intégrés. Par exemple, une instruction peut prendre la forme suivante :

`$myNewVariable = function($ServiceDefinedVariable, $myCustomVariable);`

Les formules contiennent généralement plusieurs instructions qui effectuent des opérations sur les valeurs qui sont obtenues dans les instructions précédentes : Par exemple, nous obtenons tout d’abord une valeur pour `variable1`, puis la transmettons à une fonction pour renseigner `variable2` :

```
$variable1 = function1($ServiceDefinedVariable);
$variable2 = function2($OtherServiceDefinedVariable, $variable1);
```

En utilisant ces instructions dans votre formule, vous allez chercher à atteindre un nombre de nœuds de calcul auquel le pool doit être mis à l’échelle, soit le nombre **cible** de **nœuds dédiés**. Ce nombre peut être supérieur, inférieur ou égal au nombre actuel de nœuds dans le pool. Le service Batch évalue la formule de mise à l’échelle automatique d’un pool à un intervalle spécifique (les [intervalles de mise à l’échelle automatique](#automatic-scaling-interval) sont décrits ci-dessous). Il ajuste ensuite le nombre cible de nœuds dans le pool en fonction du nombre spécifié par votre formule de mise à l’échelle automatique au moment de l’évaluation.

Prenons un exemple rapide : cette formule de mise à l’échelle automatique de deux lignes indique que le nombre de nœuds doit être ajusté en fonction du nombre de tâches actives, en respectant un maximum de 10 nœuds de calcul :

```
$averageActiveTaskCount = avg($ActiveTasks.GetSample(TimeInterval_Minute * 15));
$TargetDedicated = min(10, $averageActiveTaskCount);
```

Les sections suivantes de cet article décrivent les différentes entités qui composeront vos formules de mise à l’échelle automatique, à savoir les variables, les opérateurs, les opérations et les fonctions. Vous allez découvrir comment obtenir différentes métriques de ressource et de tâche de calcul au sein de Batch. Vous pouvez utiliser ces métriques pour ajuster intelligemment votre nombre de nœuds de calcul en fonction de l’utilisation des ressources et de l’état des tâches. Vous apprendrez ensuite à construire une formule et à activer la mise à l’échelle automatique dans un pool à l’aide des API REST et .NET de Batch. Nous terminerons par quelques exemples de formule.

> [AZURE.IMPORTANT] Chaque compte Azure Batch est limité à un nombre maximal de cœurs (et donc de nœuds de calcul) utilisables pour le traitement. Le service Batch crée des nœuds uniquement jusqu’à cette limite de cœurs. Par conséquent, il peut ne pas atteindre le nombre cible de nœuds de calcul spécifié par une formule. Consultez [Quotas et limites du service Azure Batch](batch-quota-limit.md) pour obtenir des instructions sur l’affichage et l’augmentation des quotas de votre compte.

## Variables

Vous pouvez utiliser aussi bien des variables **définies par le service** que des variables **définies par l’utilisateur** dans les formules de mise à l’échelle automatique. Les variables définies par le service sont intégrées au service Batch ; certaines sont en lecture-écriture, tandis que d’autres sont en lecture seule. Les variables définies par l’utilisateur sont des variables que *vous* définissez. Dans l’exemple de formule de deux lignes ci-dessus, `$TargetDedicated` est une variable définie par le service et `$averageActiveTaskCount` est une variable définie par l’utilisateur.

Les tableaux ci-dessous montrent des variables en lecture-écriture et en lecture seule définies par le service Batch.

Vous pouvez **obtenir** et **définir** les valeurs de ces variables définies par le service pour gérer le nombre de nœuds de calcul dans un pool :

<table>
  <tr>
    <th>Variables définies par le service<br/>en lecture-écriture</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>$TargetDedicated</td>
    <td>Nombre <b>cible</b> de <b>nœuds de calcul dédiés</b> pour le pool. Il s’agit du nombre de nœuds de calcul auquel le pool doit être mis à l’échelle. Il s’agit d’un nombre «&#160;cible&#160;» dans la mesure où il est possible qu’un pool ne puisse pas atteindre le nombre cible de nœuds. Cela peut se produire si le nombre cible de nœuds est modifié de nouveau par une évaluation de la mise à l’échelle automatique suivante avant que le pool n’ait atteint la cible initiale. Cela peut également se produire si un nœud de compte ou un quota principal Batch est atteint avant que le nombre cible de nœuds ne le soit.</td>
  </tr>
  <tr>
    <td>$NodeDeallocationOption</td>
    <td>Action exécutée lorsque des nœuds de calcul sont supprimés d’un pool. Les valeurs possibles sont les suivantes&#160;:
      <br/>
      <ul>
        <li><p><b>requeue</b>&#160;: arrête immédiatement les tâches et les replace dans la file d’attente des travaux pour qu’elles soient replanifiées.</p></li>
        <li><p><b>terminate</b>&#160;: arrête immédiatement les tâches et les supprime de la file d’attente des travaux.</p></li>
        <li><p><b>taskcompletion</b>&#160;: attend la fin des tâches en cours d’exécution, puis supprime le nœud du pool.</p></li>
        <li><p><b>retaineddata</b>&#160;: attend que toutes les données de tâche locales conservées sur le nœud aient été nettoyées avant de supprimer le nœud du pool.</p></li>
      </ul></td>
   </tr>
</table>

Vous pouvez **obtenir** la valeur des variables définies par le service ci-après pour effectuer des ajustements basés sur les métriques à partir du service Batch :

<table>
  <tr>
    <th>Lecture seule<br/>définies par le service<br/>variables</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>$CPUPercent</td>
    <td>Pourcentage moyen d’utilisation du processeur.</td>
  </tr>
  <tr>
    <td>$WallClockSeconds</td>
    <td>Nombre de secondes consommées.</td>
  </tr>
  <tr>
    <td>$MemoryBytes</td>
    <td>Nombre moyen de mégaoctets utilisés.</td>
  <tr>
    <td>$DiskBytes</td>
    <td>Nombre moyen de gigaoctets utilisés sur les disques locaux.</td>
  </tr>
  <tr>
    <td>$DiskReadBytes</td>
    <td>Nombre d’octets lus.</td>
  </tr>
  <tr>
    <td>$DiskWriteBytes</td>
    <td>Nombre d’octets écrits.</td>
  </tr>
  <tr>
    <td>$DiskReadOps</td>
    <td>Nombre d’opérations de lecture sur disque effectuées.</td>
  </tr>
  <tr>
    <td>$DiskWriteOps</td>
    <td>Nombre d’opérations d’écriture sur disque effectuées.</td>
  </tr>
  <tr>
    <td>$NetworkInBytes</td>
    <td>Nombre d’octets entrants.</td>
  </tr>
  <tr>
    <td>$NetworkOutBytes</td>
    <td>Nombre d’octets sortants.</td>
  </tr>
  <tr>
    <td>$SampleNodeCount</td>
    <td>Nombre de nœuds de calcul.</td>
  </tr>
  <tr>
    <td>$ActiveTasks</td>
    <td>Nombre de tâches à l’état actif.</td>
  </tr>
  <tr>
    <td>$RunningTasks</td>
    <td>Nombre de tâches en cours d’exécution.</td>
  </tr>
  <tr>
    <td>$SucceededTasks</td>
    <td>Nombre de tâches ayant abouti.</td>
  </tr>
  <tr>
    <td>$FailedTasks</td>
    <td>Nombre de tâches ayant échoué.</td>
  </tr>
  <tr>
    <td>$CurrentDedicated</td>
    <td>Nombre actuel de nœuds de calcul dédiés.</td>
  </tr>
</table>

> [AZURE.TIP] Les variables en lecture seule définies par le service qui sont illustrées ci-dessus sont des *objets* qui fournissent diverses méthodes pour accéder aux données qui leur sont associées. Consultez la section [Obtenir des échantillons de données](#getsampledata) ci-dessous pour plus d’informations.

## Types

Ces **types** sont pris en charge dans une formule.

- double
- doubleVec
- doubleVecList
- string
- timestamp : structure composée qui inclut les éléments suivants :

	- year
	- mois (1-12)
	- jour (1-31)
	- jour de la semaine (sous forme de chiffre, par exemple, 1 pour lundi).
	- hour (au format 24 heures, par exemple, 13 signifie 1 PM).
	- minute (00-59)
	- seconde (00-59)
- timeinterval

	- TimeInterval\_Zero
	- TimeInterval\_100ns
	- TimeInterval\_Microsecond
	- TimeInterval\_Millisecond
	- TimeInterval\_Second
	- TimeInterval\_Minute
	- TimeInterval\_Hour
	- TimeInterval\_Day
	- TimeInterval\_Week
	- TimeInterval\_Year

## Opérations

Les **opérations** autorisées sur les types répertoriés ci-dessus sont les suivantes :

| Opération | Opérateurs pris en charge | Type de résultat |
| ------------------------------------- | --------------------- | ------------- |
| double *opérateur* double | +, -, *, / | double |
| double *opérateur* timeinterval | * | timeinterval |
| doubleVec *opérateur* double | +, -, *, / | doubleVec |
| doubleVec *opérateur* doubleVec | +, -, *, / | doubleVec |
| timeinterval *opérateur* double | *, / | timeinterval |
| timeinterval *opérateur* timeinterval | +, - | timeinterval |
| timeinterval *opérateur* timestamp | + | timestamp |
| timestamp *opérateur* timeinterval | + | timestamp |
| timestamp *opérateur* timestamp | - | timeinterval |
| *opérateur*double | -, ! | double |
| *opérateur*timeinterval | - | timeinterval |
| double *opérateur* double | <, <=, ==, >=, >, != | double |
| string *opérateur* string | <, <=, ==, >=, >, != | double |
| timestamp *opérateur* timestamp | <, <=, ==, >=, >, != | double |
| timeinterval *opérateur* timeinterval | <, <=, ==, >=, >, != | double |
| double *opérateur* double | &&, &#124;&#124; | double |

Lorsque vous testez un double avec un opérateur ternaire (`double ? statement1 : statement2`), la valeur différente de zéro est **true**, et zéro est **false**.

## Fonctions

Les **fonctions** prédéfinies disponibles pour la définition d’une formule de mise à l’échelle automatique sont les suivantes :

| Fonction | Type de retour | Description
| --------------------------------- | ------------- | --------- |
| avg(doubleVecList) | double | Retourne la valeur moyenne de toutes les valeurs de l’élément doubleVecList.
| len(doubleVecList) | double | Retourne la longueur du vecteur créé à partir de l’élément doubleVecList.
| lg(double) | double | Retourne la base logarithmique 2 de l’élément double.
| lg(doubleVecList) | doubleVec | Retourne la base logarithmique 2 au niveau composant de l’élément doubleVecList. Un élément vec(double) doit être explicitement transmis pour le paramètre. Dans le cas contraire, la version double lg(double) est prise en compte.
| ln(double) | double | Retourne le logarithme naturel de l’élément double.
| ln(doubleVecList) | doubleVec | Retourne la base logarithmique 2 au niveau composant de l’élément doubleVecList. Un élément vec(double) doit être explicitement transmis pour le paramètre. Dans le cas contraire, la version double lg(double) est prise en compte.
| log(double) | double | Retourne la base logarithmique 10 de l’élément double.
| log(doubleVecList) | doubleVec | Retourne la base logarithmique 10 au niveau composant de l’élément doubleVecList. Un élément vec(double) doit être explicitement transmis pour le paramètre de type double unique. Dans le cas contraire, la version double log(double) est prise en compte.
| max(doubleVecList) | double | Retourne la valeur maximale de l’élément doubleVecList.
| min(doubleVecList) | double | Retourne la valeur minimale de l’élément doubleVecList.
| norm(doubleVecList) | double | Retourne la double norme du vecteur créé à partir de l’élément doubleVecList.
| percentile(doubleVec v, double p) | double | Retourne l’élément de percentile du vecteur v.
| rand() | double | Retourne une valeur aléatoire comprise entre 0,0 et 1,0.
| range(doubleVecList) | double | Retourne la différence entre les valeurs minimale et maximale de l’élément doubleVecList.
| std(doubleVecList) | double | Retourne l’écart type de l’échantillon des valeurs dans l’élément doubleVecList.
| stop() | | Arrête l’évaluation de l’expression de mise à l’échelle automatique.
| sum(doubleVecList) | double | Retourne la somme de tous les composants de l’élément doubleVecList.
| time(string dateTime="") | timestamp | Retourne l’horodatage de l’heure actuelle si aucun paramètre n’est transmis, ou l’horodatage de la chaîne dateTime dans le cas contraire. Les formats dateTime pris en charge sont W3C-DTF et RFC 1123.
| val(doubleVec v, double i) | double | Retourne la valeur de l’élément qui est à l’emplacement i du vecteur v avec un index de départ de zéro.

Certaines des fonctions décrites dans le tableau ci-dessus peuvent accepter une liste en tant qu’argument. La liste séparée par des virgules se compose de n’importe quelle combinaison d’éléments *double* et *doubleVec*. Par exemple :

`doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?`

La valeur *doubleVecList* est convertie en un seul paramètre *doubleVec* avant l’évaluation. Par exemple, si `v = [1,2,3]`, alors appeler `avg(v)` revient à appeler `avg(1,2,3)`. Appeler `avg(v, 7)` équivaut à appeler `avg(1,2,3,7)`.

## <a name="getsampledata"></a>Obtenir des échantillons de données

Les formules de mise à l’échelle automatique agissent sur les données métriques (échantillons) qui sont fournies par le service Batch. Une formule augmente ou réduit la taille du pool en fonction des valeurs obtenues à partir du service. Les variables définies par le service qui sont illustrées ci-dessus sont des objets qui fournissent diverses méthodes pour accéder aux données associées à chaque objet. Par exemple, l’expression ci-après présente une requête visant à obtenir les cinq dernières minutes de l’utilisation du processeur :

`$CPUPercent.GetSample(TimeInterval_Minute * 5)`

<table>
  <tr>
    <th>Méthode</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>GetSample()</td>
    <td><p>La méthode <b>GetSample()</b> renvoie un vecteur d’échantillons de données.
	<p>Un échantillon correspond à 30&#160;secondes de données de métrique. En d’autres termes, des échantillons sont obtenus toutes les 30&#160;secondes, mais comme noté ci-dessus, il existe un délai entre le moment où un échantillon est collecté et le moment où il est disponible pour une formule. Par conséquent, tous les échantillons pour une période donnée ne sont pas forcément disponibles pour évaluation par une formule.
        <ul>
          <li><p><b>doubleVec GetSample(double count)</b>&#160;: spécifie le nombre d’échantillons à obtenir à partir des échantillons collectés les plus récents.</p>
				  <p>GetSample(1) renvoie le dernier échantillon disponible. Pour les métriques comme $CPUPercent, toutefois, cette fonction ne doit pas être utilisée, car il est impossible de savoir <em>quand</em> l’échantillon a été collecté. Il peut s’agir d’un événement récent ou plus ancien en raison de problèmes système. Dans ce cas de figure, il est préférable d’utiliser un intervalle de temps, comme indiqué ci-dessous.</p></li>
          <li><p><b>doubleVec GetSample((timestamp | timeinterval) startTime [, double samplePercent])</b>&#160;: spécifie un délai d’exécution pour la collecte des échantillons de données. Elle spécifie éventuellement le pourcentage d’échantillons qui doivent être disponibles dans le délai d’exécution demandé.</p>
          <p><em>$CPUPercent.GetSample(TimeInterval_Minute * 10)</em> doit renvoyer 20&#160;échantillons si tous les échantillons des dix dernières minutes sont présents dans l’historique CPUPercent. Cependant, si la dernière minute de l’historique n’est pas disponible, seuls 18&#160;échantillons seraient renvoyés, auquel cas&#160;:<br/>
		  &#160;&#160;&#160;&#160;la commande <em>$CPUPercent.GetSample(TimeInterval_Minute * 10, 95)</em> échouerait, car seuls 90&#160;% des échantillons sont disponibles&#160;;<br/>
		  &#160;&#160;&#160;&#160;la commande <em>$CPUPercent.GetSample(TimeInterval_Minute * 10, 80)</em> aboutirait.</p></li>
          <li><p><b>doubleVec GetSample((timestamp | timeinterval) startTime, (timestamp | timeinterval) endTime [, double samplePercent])</b>&#160;: spécifie un délai d’exécution pour la collecte des données avec une heure de début et une heure de fin.</p></li></ul>
		  <p>Comme indiqué ci-dessus, il existe un délai entre le moment où un échantillon est collecté et le moment où il est disponible pour une formule. Cet aspect doit être pris en compte lorsque vous utilisez la méthode <em>GetSample</em>. Reportez-vous à la méthode <em>GetSamplePercent</em> ci-dessous.</td>
  </tr>
  <tr>
    <td>GetSamplePeriod()</td>
    <td>Retourne la période des échantillons considérés dans un jeu de données d’échantillon historiques.</td>
  </tr>
	<tr>
		<td>Count()</td>
		<td>Renvoie le nombre total d’échantillons dans l’historique des métriques.</td>
	</tr>
  <tr>
    <td>HistoryBeginTime()</td>
    <td>Retourne l’horodateur du plus ancien échantillon de données disponible pour la métrique.</td>
  </tr>
  <tr>
    <td>GetSamplePercent()</td>
    <td><p>Retourne le pourcentage d’échantillons disponibles pour un intervalle de temps donné. Par exemple&#160;:</p>
    <p><b>doubleVec GetSamplePercent ((timestamp | timeinterval) startTime [, (timestamp | timeinterval) endTime])</b>
	<p>Comme la méthode GetSample échoue si le pourcentage d’échantillons retourné est inférieur au samplePercent spécifié, vous pouvez utiliser la méthode GetSamplePercent pour procéder d’abord à une vérification. Vous pouvez ensuite effectuer une autre action si des échantillons insuffisants sont présents, sans arrêter l’évaluation de la mise à l’échelle automatique.</p></td>
  </tr>
</table>

### Échantillons, pourcentage d’échantillonnage et méthode *GetSample()*

La principale opération d’une formule de mise à l’échelle automatique vise à obtenir des données métriques des tâches et des ressources, puis à ajuster la taille du pool en fonction de ces données. Par conséquent, il est important de comprendre clairement comment les formules de mise à l’échelle automatique interagissent avec les données de mesure, également appelées « échantillons ».

**Exemples**

Le service Batch prélève régulièrement des *échantillons* des mesures de tâches et de ressources pour les rendre disponibles pour vos formules de mise à l’échelle automatique. Ces échantillons sont enregistrés toutes les 30 secondes par le service Batch. Cependant, un temps de latence entraîne généralement un retard entre l’enregistrement de ces échantillons et leur mise à disposition (en lecture) pour vos formules de mise à l’échelle automatique. En outre, en raison de différents facteurs tels que les problèmes de réseau ou d’autres problèmes d’infrastructure, il arrive que des échantillons ne soient pas enregistrés pendant un intervalle donné. C’est ce que l’on appelle les échantillons « manquants ».

**Pourcentage d’échantillonnage**

Lorsque `samplePercent` est transmis à la méthode `GetSample()` ou que la méthode `GetSamplePercent()` est appelée, le terme « pourcentage » désigne une comparaison entre le nombre total *possible* d’échantillons enregistrés par le service Batch et le nombre d’échantillons effectivement *disponibles* pour la formule de mise à l’échelle automatique.

Prenons l’exemple d’un intervalle de 10 minutes. Comme les échantillons sont enregistrés toutes les 30 secondes, au cours d’un intervalle de 10 minutes, le nombre total maximal d’échantillons enregistrés par Batch devrait être de 20 échantillons (soit 2 par minute). Toutefois, en raison de la latence inhérente au mécanisme de création de rapports, ou d’un quelque autre problème au sein de l’infrastructure Azure, il se peut que 15 échantillons seulement soient disponibles en lecture pour votre formule de mise à l’échelle automatique. Cela signifie que, pour cette période de 10 minutes, seuls **75 pour cent** du nombre total d’échantillons enregistrés sont réellement disponibles pour votre formule.

**Méthode GetSample() et plages d’échantillons**

Vos formules de mise à l’échelle automatique vont agrandir et réduire vos pools : ajout ou suppression de nœuds. Comme les nœuds vous coûtent de l’argent, vous souhaitez vous assurer que vos formules utiliseront une méthode d’analyse intelligente basée sur des données suffisantes. Par conséquent, nous vous recommandons d’utiliser une analyse des types de tendance dans vos formules. Cela augmentera ou réduira vos pools en fonction d’une *plage* d’échantillons collectés.

Pour ce faire, utilisez `GetSample(interval look-back start, interval look-back end)` pour retourner un **vecteur** d’échantillons :

`runningTasksSample = $RunningTasks.GetSample(1 * TimeInterval_Minute, 6 * TimeInterval_Minute);`

Lorsque le service Batch évalue la ligne ci-dessus, il retourne une plage d’échantillons sous la forme d’un vecteur de valeurs : Par exemple :

`runningTasksSample=[1,1,1,1,1,1,1,1,1,1];`

Une fois que vous avez collecté le vecteur d’échantillons, vous pouvez utiliser des fonctions telles que `min()`, `max()` et `avg()` pour dériver des valeurs significatives à partir de la plage collectée.

Pour plus de sécurité, vous pouvez forcer *l’échec* d’une évaluation de formule si le pourcentage d’échantillons disponible pendant une période donnée est inférieur à un certain seuil. Lorsque vous forcez l’échec d’une évaluation de formule, Batch reçoit l’instruction de cesser toute nouvelle évaluation de la formule si le pourcentage d’échantillons spécifié n’est pas disponible, auquel cas la taille du pool ne sera pas modifiée. Pour spécifier un pourcentage d’échantillons à respecter pour que l’évaluation aboutisse, spécifiez ce pourcentage en tant que troisième paramètre de `GetSample()`. Dans l’exemple ci-dessous, une exigence de 75 pour cent d’échantillons est spécifiée :

`runningTasksSample = $RunningTasks.GetSample(60 * TimeInterval_Second, 120 * TimeInterval_Second, 75);`

Il est également important, en raison du délai de disponibilité des échantillons dont nous avons parlé ci-dessus, de toujours spécifier une plage horaire avec une heure de début différée antérieure à une minute. En effet, il faut environ une minute aux échantillons pour se propager dans le système, ce qui signifie que les échantillons situés dans la plage `(0 * TimeInterval_Second, 60 * TimeInterval_Second)` ne seront généralement pas disponibles. Là encore, vous pouvez utiliser le paramètre pourcentage de `GetSample()` pour forcer une exigence de pourcentage d’échantillon particulière.

> [AZURE.IMPORTANT] Nous **vous recommandons vivement** **d’éviter de vous appuyer *uniquement* sur `GetSample(1)` dans vos formules de mise à l’échelle automatique**, car la méthode `GetSample(1)` dit globalement au service Batch : « Donne-moi le dernier échantillon disponible, peu importe depuis combien de temps il est disponible ». Dans la mesure où il s’agit uniquement d’un simple échantillon (potentiellement ancien), il risque de ne pas être représentatif de l’état récent de la tâche ou de la ressource. Si vous utilisez tout de même `GetSample(1)`, veillez à l’intégrer dans une instruction plus générale pour éviter de l’utiliser comme unique point de données sur lequel reposera votre formule.

## Mesures

Vous pouvez utiliser les deux métriques de **ressource** et de **tâche** lorsque vous définissez une formule. Vous ajustez le nombre cible de nœuds dédiés dans le pool en fonction des données métriques que vous obtenez et évaluez. Consultez la section [Variables](#variables) ci-dessus pour plus d’informations sur chaque métrique.

<table>
  <tr>
    <th>Mesure</th>
    <th>Description</th>
  </tr>
  <tr>
    <td><b>Ressource</b></td>
    <td><p>Les <b>métriques de ressource</b> sont basées sur l’utilisation du processeur, de la bande passante et de la mémoire par les nœuds de calcul, ainsi que sur le nombre de nœuds.</p>
		<p> Ces variables définies par le service sont utiles pour effectuer des ajustements en fonction du nombre de nœuds :</p>
    <p><ul>
      <li>$TargetDedicated</li>
			<li>$CurrentDedicated</li>
			<li>$SampleNodeCount</li>
    </ul></p>
    <p>Ces variables définies par le service sont utilisées pour effectuer des ajustements en fonction de l’utilisation des ressources du nœud :</p>
    <p><ul>
      <li>$CPUPercent</li>
      <li>$WallClockSeconds</li>
      <li>$MemoryBytes</li>
      <li>$DiskBytes</li>
      <li>$DiskReadBytes</li>
      <li>$DiskWriteBytes</li>
      <li>$DiskReadOps</li>
      <li>$DiskWriteOps</li>
      <li>$NetworkInBytes</li>
      <li>$NetworkOutBytes</li></ul></p>
  </tr>
  <tr>
    <td><b>Tâche</b></td>
    <td><p>Les <b>métriques de tâche</b> sont basées sur l’état des tâches (Active, En attente et Terminée). Les variables suivantes définies par le service sont utiles pour ajuster la taille du pool en fonction des métriques de tâche :</p>
    <p><ul>
      <li>$ActiveTasks</li>
      <li>$RunningTasks</li>
      <li>$SucceededTasks</li>
			<li>$FailedTasks</li></ul></p>
		</td>
  </tr>
</table>

## Créer une formule de mise à l’échelle

Vous construisez une formule de mise à l’échelle automatique en formulant des instructions qui utilisent les composants ci-dessus, puis en combinant ces instructions dans une formule complète. Par exemple, nous construisons une formule en définissant dans un premier temps les exigences de formule qui vont :

1. Augmenter le nombre cible de nœuds de calcul dans un pool si l’utilisation du processeur est intensive.
2. Réduire le nombre cible de nœuds de calcul dans un pool si l’utilisation du processeur est faible.
3. Toujours réduire le nombre maximal de nœuds à 400.

Pour *augmenter* le nombre de nœuds en cas d’utilisation intensive du processeur, nous définissons l’instruction qui renseigne une variable définie par l’utilisateur ($TotalNodes) en utilisant une valeur équivalente à 110 pour cent du nombre cible actuel de nœuds si l’utilisation moyenne du processeur minimale au cours des 10 dernières minutes a été supérieure à 70 pour cent :

`$TotalNodes = (min($CPUPercent.GetSample(TimeInterval_Minute*10)) > 0.7) ? ($CurrentDedicated * 1.1) : $CurrentDedicated;`

L’instruction suivante définit la même variable sur 90 pour cent du nombre cible actuel de nœuds si l’utilisation moyenne du processeur au cours des 60 dernières minutes était *inférieure à* 20 pour cent. Cela réduit le nombre cible en cas d’utilisation faible du processeur. Notez que cette instruction fait également référence à la variable définie par l’utilisateur *$TotalNodes* à partir de l’instruction ci-dessus.

`$TotalNodes = (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ? ($CurrentDedicated * 0.9) : $TotalNodes;`

Cet exemple définit le nombre cible de nœuds de calcul dédiés sur un **maximum** de 400 :

`$TargetDedicated = min(400, $TotalNodes)`

Voici la formule complète :

```
$TotalNodes = (min($CPUPercent.GetSample(TimeInterval_Minute*10)) > 0.7) ? ($CurrentDedicated * 1.1) : $CurrentDedicated;
$TotalNodes = (avg($CPUPercent.GetSample(TimeInterval_Minute*60)) < 0.2) ? ($CurrentDedicated * 0.9) : $TotalNodes;
$TargetDedicated = min(400, $TotalNodes)
```

> [AZURE.NOTE] Une formule de mise à l’échelle automatique est composée de variables, de types, d’opérations et de fonctions API [Batch REST][rest_api]. Vous utilisez ces éléments dans les chaînes de formule même en cas d’utilisation de la bibliothèque [.NET Batch][net_api].

## Créer un pool en activant la mise à l’échelle automatique sur ce dernier

Pour activer la mise à l’échelle automatique lors de la création d’un pool, utilisez l’une des techniques suivantes :

- [New-AzureBatchPool](https://msdn.microsoft.com/library/azure/mt125936.aspx) : cette applet de commande Azure PowerShell utilise le paramètre AutoScaleFormula pour spécifier la formule de mise à l’échelle automatique.
- [BatchClient.PoolOperations.CreatePool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx) : une fois que cette méthode .NET a été appelée pour créer un pool, vous pouvez définir les propriétés [CloudPool.AutoScaleEnabled](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleenabled.aspx) et [CloudPool.AutoScaleFormula](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleformula.aspx) du pool pour activer la mise à l’échelle automatique.
- [Ajout d’un pool à un compte](https://msdn.microsoft.com/library/azure/dn820174.aspx) : les éléments enableAutoScale et autoScaleFormula sont utilisés dans cette requête d’API REST afin de configurer la mise à l’échelle automatique pour le pool lors de la création de ce dernier.

> [AZURE.IMPORTANT] Si vous créez un pool compatible avec une mise à l’échelle automatique en utilisant l’une des techniques ci-dessus, le paramètre *targetDedicated* du pool ne doit **pas** être spécifié. Notez également que si vous souhaitez redimensionner manuellement un pool compatible avec une mise à l’échelle automatique (par exemple, avec [BatchClient.PoolOperations.ResizePool][net_poolops_resizepool]), vous devez dans un premier temps **désactiver** la mise à l’échelle automatique dans le pool avant de le redimensionner.

L’extrait de code suivant illustre la création d’un pool compatible avec une mise à l’échelle automatique ([CloudPool][net_cloudpool]) à l’aide de la bibliothèque [.NET Batch][net_api]. La formule de mise à l’échelle automatique du pool définit le nombre cible de nœuds sur cinq les lundis, et sur un les autres jours de la semaine. En outre, l’intervalle de mise à l’échelle automatique est défini sur 30 minutes (voir [Intervalle de mise à l’échelle automatique](#automatic-scaling-interval) ci-dessous). Dans cet extrait de code et les autres extraits de cet article, « myBatchClient » est une instance entièrement initialisée de [BatchClient][net_batchclient].

```
CloudPool pool = myBatchClient.PoolOperations.CreatePool("mypool", "3", "small");
pool.AutoScaleEnabled = true;
pool.AutoScaleFormula = "$TargetDedicated = (time().weekday==1?5:1);";
pool.AutoScaleEvaluationInterval = TimeSpan.FromMinutes(30);
pool.Commit();
```

### Intervalle de mise à l’échelle automatique

Par défaut, le service Batch ajuste la taille d’un pool en fonction de sa formule de mise à l’échelle toutes les **15 minutes**. Cet intervalle peut toutefois être configuré à l’aide des propriétés de pool suivantes :

- API REST : [autoScaleEvaluationInterval][rest_autoscaleinterval]
- API .NET : [CloudPool.AutoScaleEvaluationInterval][net_cloudpool_autoscaleevalinterval]

L’intervalle doit être compris entre cinq minutes et 168 heures. Si un intervalle en dehors de cette plage est spécifié, le service Batch renvoie une erreur de demande incorrecte (400).

> [AZURE.NOTE] La mise à l’échelle automatique ne peut pas actuellement répondre aux modifications en moins d’une minute, mais vise plutôt à ajuster progressivement la taille de votre pool pendant l’exécution d’une charge de travail.

## Activer la mise à l’échelle automatique après la création d’un pool

Si vous avez déjà configuré un pool avec un nombre de nœuds de calcul spécifié à l’aide du paramètre *targetDedicated*, vous pouvez mettre à jour ce pool par la suite afin d’y activer la mise à l’échelle automatique. Pour effectuer cette opération, vous pouvez utiliser l’une des méthodes suivantes :

- [BatchClient.PoolOperations.EnableAutoScale][net_enableautoscale] \: cette méthode .NET nécessite l’ID d’un pool existant et la formule de mise à l’échelle automatique à appliquer au pool.
- [Activer la mise à l’échelle automatique dans un pool][rest_enableautoscale] \: cette requête d’API REST nécessite l’ID du pool existant dans l’URI et la formule de mise à l’échelle automatique dans le corps de la requête.

> [AZURE.NOTE] La valeur spécifiée pour le paramètre *targetDedicated* au moment de la création du pool est ignorée lorsque la formule de mise à l’échelle automatique est évaluée.

Cet extrait de code montre l’activation de la mise à l’échelle automatique dans un pool existant à l’aide de la bibliothèque [.NET Batch][net_api]. Notez que l’activation et la mise à jour de la formule sur un pool existant utilisent la même méthode. Par conséquent, cette technique *met à jour* la formule sur le pool spécifié si la mise à l’échelle automatique a déjà été activée. L’extrait de code suppose que « mypool » est l’ID d’un pool existant ([CloudPool][net_cloudpool]).

		 // Define the autoscaling formula. In this snippet, the  formula sets the target number of nodes to 5 on
		 // Mondays, and 1 on every other day of the week
		 string myAutoScaleFormula = "$TargetDedicated = (time().weekday==1?5:1);";

		 // Update the existing pool's autoscaling formula by calling the BatchClient.PoolOperations.EnableAutoScale
		 // method, passing in both the pool's ID and the new formula.
		 myBatchClient.PoolOperations.EnableAutoScale("mypool", myAutoScaleFormula);

## Évaluer la formule de mise à l’échelle automatique

Il est recommandé d’évaluer systématiquement une formule avant de l’utiliser dans votre application. La formule est évaluée par le biais de l’exécution d’une « série de tests » sur un pool existant. Pour cela, utilisez :

- [BatchClient.PoolOperations.EvaluateAutoScale](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.evaluateautoscale.aspx) ou [BatchClient.PoolOperations.EvaluateAutoScaleAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.evaluateautoscaleasync.aspx) : ces méthodes .NET nécessitent l’ID d’un pool existant et la chaîne qui contient la formule de mise à l’échelle automatique. Les résultats de l’appel sont contenus dans une instance de la classe [AutoScaleEvaluation](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscaleevaluation.aspx).
- [Évaluer une formule de mise à l’échelle automatique](https://msdn.microsoft.com/library/azure/dn820183.aspx) : dans cette requête d’API REST, l’ID du pool est spécifié dans l’URI. La formule de mise à l’échelle automatique est spécifiée dans l’élément *autoScaleFormula* du corps de la requête. La réponse de l’opération contient les éventuelles informations d’erreur associées à la formule.

> [AZURE.NOTE] Pour évaluer une formule de mise à l’échelle automatique, vous devez d’abord avoir activé la mise à l’échelle automatique dans le pool à l’aide d’une formule valide.

Dans cet extrait de code utilisant la bibliothèque [.NET Batch][net_api], nous évaluons une formule avant de l’appliquer au pool ([CloudPool][net_cloudpool]).

```
// First obtain a reference to the existing pool
CloudPool pool = myBatchClient.PoolOperations.GetPool("mypool");

// We must ensure that autoscaling is enabled on the pool prior to evaluating a formula
if (pool.AutoScaleEnabled.HasValue && pool.AutoScaleEnabled.Value)
{
	// The formula to evaluate - adjusts target number of nodes based on day of week and time of day
	string myFormula = @"
		$CurTime=time();
		$WorkHours=$CurTime.hour>=8 && $CurTime.hour<18;
		$IsWeekday=$CurTime.weekday>=1 && $CurTime.weekday<=5;
		$IsWorkingWeekdayHour=$WorkHours && $IsWeekday;
		$TargetDedicated=$IsWorkingWeekdayHour?20:10;
	";

	// Perform the autoscale formula evaluation. Note that this does not actually apply the formula to
	// the pool.
	AutoScaleEvaluation eval = client.PoolOperations.EvaluateAutoScale(pool.Id, myFormula);

	if (eval.AutoScaleRun.Error == null)
	{
		// Evaluation success - print the results of the AutoScaleRun. This will display the values of each
		// variable as evaluated by the autoscale formula.
		Console.WriteLine("AutoScaleRun.Results: " + eval.AutoScaleRun.Results);

		// Apply the formula to the pool since it evaluated successfully
		client.PoolOperations.EnableAutoScale(pool.Id, myFormula);
	}
	else
	{
		// Evaluation failed, output the message associated with the error
		Console.WriteLine("AutoScaleRun.Error.Message: " + eval.AutoScaleRun.Error.Message);
	}
}
```

L’évaluation réussie de la formule dans cet extrait de code entraîne une sortie similaire à ce qui suit :

`AutoScaleRun.Results: $TargetDedicated = 10;$NodeDeallocationOption = requeue;$CurTime = 2015 - 08 - 25T20: 08:42.271Z;$IsWeekday = 1;$IsWorkingWeekdayHour = 0;$WorkHours = 0`

## Obtenir des informations sur les exécutions de mise à l’échelle automatique

Vérifiez régulièrement les résultats de l’exécution de la mise à l’échelle automatique pour vous assurer qu’une formule fonctionne comme prévu.

- [CloudPool.AutoScaleRun](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscalerun.aspx) : lorsque vous utilisez la bibliothèque .NET, cette propriété d’un pool fournit une instance de la classe [AutoScaleRun](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.aspx). Cette classe fournit les propriétés suivantes de la dernière exécution de mise à l’échelle automatique :
  - [AutoScaleRun.Error](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.error.aspx)
  - [AutoScaleRun.Results](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.results.aspx)
  - [AutoScaleRun.Timestamp](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.timestamp.aspx)
- [Obtenir des informations sur un pool](https://msdn.microsoft.com/library/dn820165.aspx) : cette requête d’API REST retourne des informations sur le pool, et inclut la dernière exécution de mise à l’échelle automatique.

## <a name="examples"></a>Exemples de formules

Examinons quelques exemples présentant certaines façons d’utiliser des formules pour automatiquement mettre à l’échelle les ressources de calcul dans un pool.

### Exemple 1 : ajustement en fonction du temps

Peut-être souhaitez-vous ajuster la taille du pool en fonction du jour de la semaine et de l’heure de la journée, pour augmenter ou diminuer le nombre de nœuds dans le pool en conséquence :

```
$CurTime=time();
$WorkHours=$CurTime.hour>=8 && $CurTime.hour<18;
$IsWeekday=$CurTime.weekday>=1 && $CurTime.weekday<=5;
$IsWorkingWeekdayHour=$WorkHours && $IsWeekday;
$TargetDedicated=$IsWorkingWeekdayHour?20:10;
```

Cette formule obtient dans un premier temps l’heure actuelle. S’il s’agit d’un jour de la semaine (1 à 5) et des heures de travail (8 heures à 18 heures), la taille du pool cible est définie sur 20 nœuds. Autrement, la taille cible du pool est définie sur 10.

### Exemple 2 : ajustement en fonction de la tâche

Dans cet exemple, la taille du pool est ajustée en fonction du nombre de tâches présentes dans la file d’attente. Notez que les commentaires et les sauts de ligne sont acceptés dans les chaînes de formule.

```
// Get pending tasks for the past 15 minutes.
$Samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
// If we have fewer than 70 percent data points, we use the last sample point, otherwise we use the maximum of
// last sample point and the history average.
$Tasks = $Samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1), avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// If number of pending tasks is not 0, set targetVM to pending tasks, otherwise half of current dedicated.
$TargetVMs = $Tasks > 0? $Tasks:max(0, $TargetDedicated/2);
// The pool size is capped at 20, if target VM value is more than that, set it to 20. This value
// should be adjusted according to your use case.
$TargetDedicated = max(0,min($TargetVMs,20));
// Set node deallocation mode - keep nodes active only until tasks finish
$NodeDeallocationOption = taskcompletion;
```

### Exemple 3 : comptabilisation des tâches parallèles

Il s’agit d’un autre exemple qui ajuste la taille du pool en fonction du nombre de tâches. Cette formule prend également en compte la valeur [MaxTasksPerComputeNode][net_maxtasks] qui a été définie pour le pool. Cela est particulièrement utile dans les situations où [l’exécution parallèle de tâches](batch-parallel-node-tasks.md) a été activée sur votre pool.

```
// Determine whether 70 percent of the samples have been recorded in the past 15 minutes; if not, use last sample
$Samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
$Tasks = $Samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1),avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// Set the number of nodes to add to one-fourth the number of active tasks (the MaxTasksPerComputeNode
// property on this pool is set to 4, adjust this number for your use case)
$Cores = $TargetDedicated * 4;
$ExtraVMs = (($Tasks - $Cores) + 3) / 4;
$TargetVMs = ($TargetDedicated+$ExtraVMs);
// Attempt to grow the number of compute nodes to match the number of active tasks, with a maximum of 3
$TargetDedicated = max(0,min($TargetVMs,3));
// Keep the nodes active until the tasks finish
$NodeDeallocationOption = taskcompletion;
```

### Exemple 4 : définition d’une taille de pool initiale

Cet exemple montre un extrait de code en C# avec une formule de mise à l’échelle automatique qui définit la taille du pool sur un certain nombre de nœuds pour une période initiale. La taille du pool est ensuite ajustée en fonction du nombre de tâches en cours d’exécution et actives une fois la période initiale écoulée.

```
string now = DateTime.UtcNow.ToString("r");
string formula = string.Format(@"

	$TargetDedicated = {1};
	lifespan         = time() - time(""{0}"");
	span             = TimeInterval_Minute * 60;
	startup          = TimeInterval_Minute * 10;
	ratio            = 50;

	$TargetDedicated = (lifespan > startup ? (max($RunningTasks.GetSample(span, ratio), $ActiveTasks.GetSample(span, ratio)) == 0 ? 0 : $TargetDedicated) : {1});
	", now, 4);
```

Formule dans l’extrait de code ci-dessus :

- Définit la taille initiale du pool sur quatre nœuds.
- N’ajuste pas la taille du pool dans les 10 premières minutes de cycle de vie du pool.
- Après 10 minutes, obtient la valeur maximale du nombre de tâches en cours d’exécution et actives au cours des 60 dernières minutes.
  - Si les deux valeurs sont égales à 0 (ce qui indique qu’aucune tâche n’était en cours d’exécution ni active au cours des 60 dernières minutes), la taille du pool est définie sur 0.
  - Si l’une des valeurs est supérieure à zéro, aucune modification n’est apportée.

## Étapes suivantes

* L’article [Optimiser l’utilisation des ressources de calcul Azure Batch avec les tâches de nœud simultanées](batch-parallel-node-tasks.md) contient des informations sur la façon dont vous pouvez effectuer plusieurs tâches simultanément sur les nœuds de calcul de votre pool. En plus de la mise à l’échelle automatique, cette fonctionnalité peut aider à réduire la durée du travail pour certaines charges de travail et vous permettre d’économiser de l’argent.

* Afin d’améliorer encore l’efficacité, assurez-vous que votre application Batch interroge le service Batch de la manière la plus optimale qui soit. Dans [Interroger efficacement le service Azure Batch](batch-efficient-list-queries.md), vous allez apprendre à limiter la quantité de données qui transitent par le réseau lorsque vous interrogez l’état des milliers de nœuds de calcul ou de tâches potentiels.

[net_api]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_batchclient]: http://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_cloudpool_autoscaleformula]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleformula.aspx
[net_cloudpool_autoscaleevalinterval]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval.aspx
[net_enableautoscale]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.enableautoscale.aspx
[net_maxtasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.maxtaskspercomputenode.aspx
[net_poolops_resizepool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.resizepool.aspx

[rest_api]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[rest_autoscaleformula]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[rest_autoscaleinterval]: https://msdn.microsoft.com/fr-FR/library/azure/dn820173.aspx
[rest_enableautoscale]: https://msdn.microsoft.com/library/azure/dn820173.aspx

<!----HONumber=AcomDC_0727_2016-->