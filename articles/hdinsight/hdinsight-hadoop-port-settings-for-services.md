<properties
pageTitle="Ports utilisés par HDInsight | Azure"
description="Liste des ports utilisés par les services Hadoop sur HDInsight."
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="jhubbard"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="09/13/2016"
ms.author="larryfr"/>

# Ports et URI utilisés par HDInsight

Ce document fournit une liste des ports utilisés par les services Hadoop exécutés sur des clusters HDInsight sous Linux. Il fournit également des informations sur les ports utilisés pour se connecter au cluster à l’aide de SSH.

## Ports publics et ports non publics

Les clusters HDInsight sous Linux exposent uniquement trois ports publiquement sur Internet, à savoir les ports 22, 23 et 443. Ceux-ci sont utilisés pour accéder en toute sécurité au cluster à l’aide de SSH et des services exposés sur le protocole HTTPS sécurisé.

En interne, HDInsight est implémenté par plusieurs machines virtuelles Azure (les nœuds dans le cluster) exécutées sur un réseau virtuel Azure. Depuis le réseau virtuel, vous pouvez accéder aux ports non exposés sur Internet. Par exemple, si vous vous connectez à l’un des nœuds principaux à l’aide de SSH, à partir du nœud principal, vous pouvez ensuite accéder directement aux services s’exécutant sur les nœuds du cluster.

> [AZURE.IMPORTANT] Lorsque vous créez un cluster HDInsight, si vous ne spécifiez pas un réseau virtuel Azure en tant qu’option de configuration, un réseau est créé. Toutefois, vous ne pouvez pas joindre d’autres machines (comme les autres machines virtuelles Azure ou votre ordinateur de développement client) à ce réseau virtuel créé automatiquement.

Pour joindre des ordinateurs supplémentaires au réseau virtuel, vous devez d’abord créer le réseau virtuel, puis le spécifier lors de la création de votre cluster HDInsight. Pour plus d’informations, consultez [Étendre les capacités de HDInsight en utilisant un réseau virtuel Azure](hdinsight-extend-hadoop-virtual-network.md)

## Ports publics

Tous les nœuds dans un cluster HDInsight se trouvent dans un réseau virtuel Azure et ne sont pas directement accessibles depuis Internet. Une passerelle publique fournit l’accès à Internet pour les ports suivants, qui sont communs à tous les types de cluster HDInsight.

| de diffusion en continu | Port | Protocole | Description |
| ---- | ---------- | -------- | ----------- | ----------- |
| sshd | 22 | SSH | Connecte les clients à sshd sur le nœud principal primaire. Consultez la page [Utilisation de SSH avec HDInsight Linux](hdinsight-hadoop-linux-use-ssh-windows.md) |
| sshd | 22 | SSH | Connecte les clients à sshd sur le nœud de périmètre (HDInsight Premium uniquement). Consultez la page [Commencer à utiliser R Server sur HDInsight](hdinsight-hadoop-r-server-get-started.md) |
| sshd | 23 | SSH | Connecte les clients à sshd sur le nœud principal secondaire. Consultez la page [Utilisation de SSH avec HDInsight Linux](hdinsight-hadoop-linux-use-ssh-windows.md) |
| Ambari | 443 | HTTPS | Interface utilisateur web d’Ambari. Consultez [Gérer des clusters HDInsight à l’aide de l’interface utilisateur web d’Ambari](hdinsight-hadoop-manage-ambari.md) |
| Ambari | 443 | HTTPS | API Ambari REST. Consultez la page [Gérer des clusters HDInsight à l’aide de l’API REST d’Ambari](hdinsight-hadoop-manage-ambari-rest-api.md) |
| WebHCat, | 443 | HTTPS | API REST HCatalog. Consultez les pages [Utilisation de Hive avec Curl](hdinsight-hadoop-use-Pig-curl.md), [Utilisation de Pig avec Curl](hdinsight-hadoop-use-Pig-curl.md), [Utilisation de MapReduce avec Curl](hdinsight-hadoop-use-mapreduce-curl.md) |
| HiveServer2 | 443 | ODBC | Se connecte à Hive à l’aide de ODBC. Consultez la page [Connexion d’Excel à HDInsight à l’aide du pilote ODBC Microsoft](hdinsight-connect-excel-hive-odbc-driver.md). |
| HiveServer2 | 443 | JDBC | Se connecte à Hive à l’aide de JDBC. Consultez la page [Se connecter à Hive sur HDInsight à l’aide du pilote JDBC Hive](hdinsight-connect-hive-jdbc-driver.md) |

Les éléments suivants sont disponibles pour les types de clusters spécifiques :

| de diffusion en continu | Port | Protocole |Type de cluster | Description |
| ------------ | ---- |  ----------- | --- | ----------- |
| Stargate | 443 | HTTPS | HBase | API REST HBase. Consultez la page [Prise en main de HBase](hdinsight-hbase-tutorial-get-started-linux.md) |
| Livy | 443 | HTTPS | Spark | API REST Spark. Consultez la page [Envoi de travaux Spark à distance à l’aide de Livy](hdinsight-apache-spark-livy-rest-interface.md) |
| Storm | 443 | HTTPS | Storm | Interface utilisateur web de Storm. Consultez la page [Déploiement et gestion des topologies Storm sur HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)

### Authentification

Tous les services exposés publiquement sur Internet doivent être authentifiés :

| Port | Informations d'identification |
| ---- | ----------- |
| 22 ou 23 | Les informations d’identification utilisateur SSH spécifiées lors de la création du cluster |
| 443 | Le nom de connexion (par défaut : admin) et le mot de passe qui ont été définis lors de la création du cluster |

## Ports non publics

> [AZURE.NOTE] Certains services sont disponibles uniquement sur certains types de clusters. Par exemple, HBase est disponible uniquement sur les clusters de type HBase.

### Ports HDFS

| de diffusion en continu | Nœud(s) | Port | Protocole | Description |
| ------- | ------- | ---- | -------- | ----------- | 
| Interface utilisateur web de NameNode | Nœuds principaux | 30070 | HTTPS | Interface utilisateur pour afficher l’état actuel |
| Service de métadonnées NameNode | nœuds principaux | 8020 | IPC | Métadonnées du système de fichiers 
| DataNode | Tous les nœuds de travail | 30075 | HTTPS | Interface utilisateur web pour afficher l’état, les journaux, etc. |
| DataNode | Tous les nœuds de travail | 30010 | &nbsp; | Transfert de données |
| DataNode | Tous les nœuds de travail | 30020 | IPC | Opérations sur les métadonnées |
| NameNode secondaire | Nœuds principaux | 50090 | HTTP | Point de contrôle pour les métadonnées NameNode |

### Ports YARN

| de diffusion en continu | Nœud(s) | Port | Protocole | Description |
| ------- | ------- | ---- | -------- | ----------- |
| Interface utilisateur web de Resource Manager | Nœuds principaux | 8088 | HTTP | Interface utilisateur web pour Resource Manager |
| Interface utilisateur web de Resource Manager | Nœuds principaux | 8090 | HTTPS | Interface utilisateur web pour Resource Manager |
| Interface d’administration de Resource Manager | nœuds principaux | 8141 | IPC | Pour les envois d’application (Hive, serveur Hive, Pig, etc.) |
| Scheduler Resource Manager | nœuds principaux | 8030 | HTTP | Interface d’administration |
| Interface d’application Resource Manager | nœuds principaux | 8050 | HTTP |Adresse de l’interface du gestionnaire d’applications |
| NodeManager | Tous les nœuds de travail | 30050 | &nbsp; | L’adresse du gestionnaire de conteneurs |
| Interface utilisateur web de NodeManager | Tous les nœuds de travail | 30060 | HTTP | Interface de Resource Manager |
| Adresse de Timeline | Nœuds principaux | 10200 | RPC | Le service RPC du service de Timeline. |
| Interface utilisateur web de Timeline | Nœuds principaux | 8181 | HTTP | L’interface utilisateur web du service Timeline |

### Ports Hive

| de diffusion en continu | Nœud(s) | Port | Protocole | Description |
| ------- | ------- | ---- | -------- | ----------- |
| HiveServer2 | Nœuds principaux | 10001 | Thrift | Service pour se connecter par programme à Hive (Thrift/JDBC) |
| HiveServer | Nœuds principaux | 10000 | Thrift | Service pour se connecter par programme à Hive (Thrift/JDBC) |
| Metastore Hive | Nœuds principaux | 9083 | Thrift | Service de connexion par programme aux métadonnées Hive (Thrift/JDBC) |

### Ports WebHCat

| Service | Nœud(s) | Port | Protocole | Description |
| ------- | ------- | ---- | -------- | ----------- |
| Serveur WebHCat | Nœuds principaux | 30111 | HTTP | API web sur HCatalog et d’autres services Hadoop |

### Ports MapReduce

| de diffusion en continu | Nœud(s) | Port | Protocole | Description |
| ------- | ------- | ---- | -------- | ----------- |
| JobHistory | Nœuds principaux | 19888 | HTTP | Interface utilisateur web de MapReduce JobHistory |
| JobHistory | Nœuds principaux | 10020 | &nbsp; | Serveur MapReduce JobHistory |
| ShuffleHandler | &nbsp; | 13562 | &nbsp; | Transfère les sorties Map intermédiaires aux raccords de réduction qui le demandent |

### Oozie

| de diffusion en continu | Nœud(s) | Port | Protocole | Description |
| ------- | ------- | ---- | -------- | ----------- |
| Serveur Oozie | Nœuds principaux | 11000 | HTTP | URL du service Oozie |
| Serveur Oozie | Nœuds principaux | 11001 | HTTP | Port pour l’administration Oozie |

### Mesures d’Ambari

| de diffusion en continu | Nœud(s) | Port | Protocole | Description |
| ------- | ------- | ---- | -------- | ----------- |
| TimeLine (historique d’application) | Nœuds principaux | 6188 | HTTP | L’interface utilisateur web du service TimeLine |
| TimeLine (historique d’application) | Nœuds principaux | 30200 | RPC | L’interface utilisateur web du service TimeLine |

### Ports HBase

| de diffusion en continu | Nœud(s) | Port | Protocole | Description |
| ------- | ------- | ---- | -------- | ----------- |
| HMaster | Nœuds principaux | 16000 | &nbsp; | &nbsp; |
| Interface utilisateur web d’informations sur HMaster | Nœuds principaux | 16010 | HTTP | Le port de l’interface utilisateur web principale de HBase Master |
| Serveur de la région | Tous les nœuds de travail | 16020 | &nbsp; | &nbsp; |
| &nbsp; | &nbsp; | 2181 | &nbsp; | Le port que les clients utilisent pour se connecter à ZooKeeper |

### Ports Kafka

| de diffusion en continu | Nœud(s) | Port | Protocole | Description |
| ------- | ------- | ---- | -------- | ----------- |
| Service Broker | Nœuds de travail | 9092 | [Protocole Kafka](http://kafka.apache.org/protocol.html) | Utilisé pour la communication client |
| &nbsp; | Nœuds Zookeeper | 2181 | &nbsp; | Le port que les clients utilisent pour se connecter à ZooKeeper |

<!---HONumber=AcomDC_0921_2016-->