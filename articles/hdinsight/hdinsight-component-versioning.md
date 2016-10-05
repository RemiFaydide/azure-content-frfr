<properties
	pageTitle="Quels sont les différents composants disponibles avec un cluster HDInsight ? | Microsoft Azure"
	description="HDInsight prend en charge plusieurs composants et versions de cluster Hadoop pouvant être déployés. Découvrez les versions de distributions prises en charge Hadoop et HortonWorks Data Platform (HDP)."
	services="hdinsight"
	editor="cgronlun"
	manager="jhubbard"
	authors="mumian"
	tags="azure-portal"
	documentationCenter=""/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/21/2016"
	ms.author="jgao"/>


# Quels sont les différents composants Hadoop disponibles avec HDInsight ?

Découvrez les différents niveaux de service proposés par HDInsight, ainsi que les versions des différents composants Hadoop fournis avec HDInsight.

## HDInsight Standard et HDInsight Premium

Azure HDInsight propose deux catégories d’offres de cloud Big Data : **Standard** et **Premium**. Le tableau sous la section répertorie les fonctionnalités disponibles **uniquement** dans le cadre de l’édition Premium. Les fonctionnalités qui ne sont pas explicitement indiquées dans ce tableau sont disponibles dans le cadre de l’édition Standard.

>[AZURE.NOTE] L’offre HDInsight Premium est actuellement en version préliminaire, disponible uniquement pour les clients Linux.

| Fonctionnalité HDInsight Premium | Description |
|--------------|---------------|
| Microsoft R Server (version préliminaire) | Microsoft R Server est la plateforme d’analyse d’entreprise la plus largement déployée pour le langage R évolutif. Le langage R prend en charge une variété de statistiques de Big Data, de modélisation prédictive et de capacités Machine Learning. Dans le cadre de l’édition Premium de HDInsight, vous pouvez maintenant créer un cluster HDInsight avec R Server prêt à être utilisé avec des modèles et des jeux de données volumineux. Cette nouvelle fonctionnalité fournit aux scientifiques de données et aux statisticiens une interface R familière capable d’évoluer sur demande via HDInsight, sans les tâches de configuration du cluster et de maintenance. <br> <br>Pour plus d’informations, consultez la section [Prise en main de R Server sur HDInsight](hdinsight-hadoop-r-server-get-started.md).

### Types de cluster pris en charge avec l’édition Premium

Le tableau suivant répertorie le type de cluster HDInsight et la matrice de support Premium.

| Type de cluster | Standard | Premium |
|--------------|---------------|--------------|
| Hadoop | Oui | Oui |
| Spark | Oui | Oui |
| HBase | Oui | Non |
| Storm | Oui | Non |

Ce tableau sera mis à jour au fur et à mesure que des types de cluster supplémentaires sont inclus dans HDInsight Premium.

### Tarifs et contrat SLA

Pour plus d’informations sur la tarification et le contrat SLA pour l’édition Premium de HDInsight, consultez [Tarification HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## Composants Hadoop disponibles avec différentes versions de HDInsight

Azure HDInsight prend en charge plusieurs versions de cluster Hadoop qui peuvent être déployées à tout moment. Le choix d'une version crée une version spécifique de la distribution de la plateforme de données Hortonworks (HDP) et un ensemble de composants qui sont contenus dans cette distribution. Les versions des composants associées aux versions de cluster HDInsight sont répertoriées dans le tableau suivant. Notez que la version de cluster par défaut actuellement utilisée par Azure HDInsight est la version 3.4, et, à la date du 14/09/2016, elle est basée sur HDP 2.4.

> [AZURE.NOTE] La version par défaut du service peut changer sans préavis. Nous vous recommandons de spécifier la version lorsque vous créez des clusters à l’aide du Kit de développement logiciel (SDK) .NET/Azure PowerShell et Azure CLI, si vous êtes dépendant d’une version.

Composant|HDInsight Version 3.4 (par défaut) | HDInsight version 3.3 | HDInsight Version 3.2 |HDInsight version 3.1 |HDInsight Version 3,0|
---|---|---|---|---|---
Hortonworks Data Platform|2\.4|2\.3|2\.2|2\.1.7|2\.0|
Apache Hadoop et YARN|2\.7.1|2\.7.1|2\.6.0|2\.4.0|2\.2.0|
Apache Tez|0\.7.0|0\.7.0 | 0\.5.2|0\.4.0||
Apache Pig|0\.15.0|0\.15.0|0\.14.0|0\.12.1|0\.12.0|
Apache Hive et HCatalog|1\.2.1|1\.2.1|0\.14.0|0\.13.1|0\.12.0|
Apache HBase |1\.1.2|1\.1.1|0\.98.4|0\.98.0||
Apache Sqoop|1\.4.6|1\.4.6|1\.4.5|1\.4.4|1\.4.4|1\.4.3
Apache Oozie|4\.2.0|4\.2.0|4\.1.0|4\.0.0|4\.0.0|
Apache Zookeeper|3\.4.6|3\.4.6|3\.4.6|3\.4.5|3\.4.5|
Apache Storm|0\.10.0|0\.10.0|0\.9.3|0\.9.1||
Apache Mahout|0\.9.0+|0\.9.0+|0\.9.0|0\.9.0||
Apache Phoenix|4\.4.0|4\.4.0|4\.2.0|4\.0.0.2.1.7.0-2162||
Apache Spark|1\.6.0 (Linux uniquement)|1\.5.2 (Linux uniquement/version expérimentale)|1\.3.1 (Windows uniquement)|||


**Obtention d’informations sur les versions actuelles des composants**

Les versions de composant associées aux versions de cluster HDInsight sont susceptibles de changer dans les futures mises à jour de HDInsight. Pour déterminer quels sont les composants disponibles et vérifier quelles versions sont utilisées pour un cluster, vous pouvez utiliser l’API REST Ambari. La commande **GetComponentInformation** permet de récupérer des informations sur un composant du service. Pour des détails, consultez la [documentation Ambari][ambari-docs]. Vous pouvez également obtenir ces informations en vous connectant à un cluster à l’aide du Bureau à distance et en examinant directement le contenu du répertoire « C:\\apps\\dist ».


**Notes de publication**

Pour d’autres notes de publication sur les dernières versions de HDInsight, consultez la page [Notes de publication de HDInsight](hdinsight-release-notes.md).


## Versions de HDInsight prises en charge
Le tableau suivant répertorie les versions de HDInsight actuellement disponibles, les versions correspondantes de la plateforme de données Hortonworks utilisées par ces dernières, ainsi que leurs dates de lancement. La date d’expiration de la prise en charge et la date d’obsolescence sont également indiquées. Notez les points suivants :

* Seuls les clusters à haute disponibilité avec deux nœuds principaux sont déployés par défaut pour les clusters HDInsight 2.1 et versions ultérieures. Ils ne sont pas disponibles pour les clusters HDInsight 1.6.
* Une fois que la prise en charge a expiré pour une version particulière, elle n’est plus disponible par l’intermédiaire du portail Azure. Le tableau suivant indique quelles versions sont disponibles sur le portail Azure Classic. Les versions des clusters resteront disponibles avec le paramètre `Version` de la commande [New-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619331.aspx) Windows PowerShell et le Kit de développement logiciel (SDK) .NET jusqu’à leur date d’obsolescence.

Version de HDInsight|Version de HDP|SYSTÈME D’EXPLOITATION DE LA MACHINE VIRTUELLE|Haute disponibilité|Date de lancement|Disponible sur le portail Azure|Date d'expiration du support|Date d'obsolescence
---|---|---|---|---|---|---|---
HDI 3.4|HDP 2.4|Ubuntu 14.0.4 LTS|Oui|29/03/2016|Oui||
HDI 3.3|HDP 2.3|Ubuntu 14.0.4 LTS ou Windows Server 2012R2|Oui|02/12/2015|Oui|27/06/2016|31/07/2017
HDI 3.2|HDP 2.2|Ubuntu 12.04 LTS ou Windows Server 2012R2|Oui|2/18/2015|Oui|03/01/2016|04/01/2017
HDI 3,1|HDP 2,1|Windows Server 2012 R2|Oui|6/24/2014|Non|18/05/2015|30/06/2016
HDI 3,0|HDP 2,0|Windows Server 2012 R2|Oui|02/11/2014|Non|09/17/2014|06/30/2015
HDI 2,1|HDP 1,3|Windows Server 2012 R2|Oui|10/28/2013|Non|05/12/2014|05/31/2015
HDI 1.6|HDP 1.1||Non|10/28/2013|Non|04/26/2014|05/31/2015

**Déploiement des clusters autres que les clusters par défaut**

### Contrat de niveau de service pour les versions de cluster HDInsight

Le contrat de niveau de service est défini en termes de « fenêtre de support ». Une fenêtre de support désigne la durée pendant laquelle une version de cluster HDInsight est prise en charge par le support technique et le service clientèle Microsoft. Un cluster HDInsight est en dehors de la fenêtre de support si la **Date d'expiration du support** correspondant à la version de ce cluster est postérieure à la date actuelle. Le tableau ci-dessus contient une liste des versions de cluster HDInsight prises en charge. La date d'expiration du support pour une version donnée de HDInsight (lorsqu'une version X+1 plus récente est disponible) est calculée comme suit, la date la plus tardive prévalant :

- Formule 1 : ajoutez 180 jours à la date de lancement du cluster HDInsight version X.
- Formule 2 : ajoutez 90 jours à la date à laquelle le cluster HDInsight version X+1 (la version qui suit la version X) est mis à disposition dans le portail.

La **Date d’obsolescence** est la date après laquelle la version du cluster ne peut plus être créée sur HDInsight.

> [AZURE.NOTE] Les clusters HDInsight sous Windows (y compris les versions 2.1, 3.0, 3.1, 3.2 et 3.3) s’exécutent sur la 4e famille de systèmes d’exploitation invités d’Azure, qui utilise la version 64 bits de Windows Server 2012 R2 et prend en charge .NET Framework 4.0, 4.5, 4.5.1 et 4.5.2.

## Notes de publication de Hortonworks associées aux versions de HDInsight##

* Le cluster HDInsight version 3.4 utilise une distribution Hadoop basée sur [Hortonworks Data Platform 2.4](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.4.0/bk_HDP_RelNotes/content/ch_relnotes_v240.html). Il s’agit du cluster Hadoop **par défaut** créé lors de l’utilisation du portail.



* Le cluster HDInsight version 3.3 utilise une distribution Hadoop basée sur [Hortonworks Data Platform 2.3](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.3.0/bk_HDP_RelNotes/content/ch_relnotes_v230.html).
	* Les notes de publication d’Apache Storm sont disponibles [ici](https://storm.apache.org/2015/11/05/storm0100-released.html).
	* Les notes de publication d’Apache Hive sont disponibles [ici](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12332384&styleName=Text&projectId=12310843).

* Le cluster HDInsight version 3.2 utilise une distribution Hadoop basée sur [Hortonworks Data Platform 2.2][hdp-2-2].

	* Notes de publication pour les composants spécifiques d’Apache - [Hive 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310843&version=12326450), [Pig 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310730&version=12326954), [HBase 0.98.4](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310753&version=12326810), [Phoenix 4.2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12315120&version=12327581), [M/R 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310941&version=12327180), [HDFS 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310942&version=12327181), [YARN 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12313722&version=12327197), [Common](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310240&version=12327179), [Tez 0.5.2](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314426&version=12328742), [Ambari 2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12312020&version=12327486), [Storm 0.9.3](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314820&version=12327112), [Oozie 4.1.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12324960&projectId=12311620).


* Le cluster HDInsight version 3.1 utilise une distribution Hadoop basée sur [Hortonworks Data Platform 2.1.7][hdp-2-1-7]. Les clusters HDInsight 3.1 créés avant le 07/11/2014 étaient basés sur [Hortonworks Data Platform 2.1.1][hdp-2-1-1].

* Le cluster HDInsight version 3.0 utilise une distribution Hadoop basée sur [Hortonworks Data Platform 2.0][hdp-2-0-8].

* Le cluster HDInsight version 2.1 utilise une distribution Hadoop basée sur [Hortonworks Data Platform 1.3][hdp-1-3-0].

* Le cluster HDInsight version 1.6 utilise une distribution Hadoop basée sur [Hortonworks Data Platform 1.1][hdp-1-1-0].


[image-hdi-versioning-versionscreen]: ./media/hdinsight-component-versioning/hdi-versioning-version-screen.png

[wa-forums]: http://azure.microsoft.com/support/forums/

[connect-excel-with-hive-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md

[hdp-2-2]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.2.0/HDP_2.2.0_Release_Notes_20141202_version/index.html

[hdp-2-1-7]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html

[ambari-docs]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

[zookeeper]: http://zookeeper.apache.org/

<!---HONumber=AcomDC_0921_2016-->