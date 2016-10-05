<properties
   pageTitle="Surveiller et gérer des clusters HDInsight à l'aide de l'API REST d'Apache Ambari | Microsoft Azure"
   description="Découvrez comment utiliser Ambari pour gérer et surveiller des clusters HDInsight Linux. Dans ce document, vous allez apprendre à utiliser l'API REST d'Ambari incluse avec les clusters HDInsight."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/20/2016"
   ms.author="larryfr"/>

#Gérer des clusters HDInsight à l'aide de l'API REST d'Ambari

[AZURE.INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Apache Ambari simplifie la gestion et la surveillance d'un cluster Hadoop en fournissant une interface utilisateur web et une API REST faciles à utiliser. Ambari est inclus dans les clusters HDInsight sous Linux et sert à surveiller le cluster et à apporter des modifications de configuration. Dans ce document, vous allez apprendre les bases de l’utilisation de l’API REST Ampari en effectuant des tâches courantes à l’aide de cURL.

##Composants requis

* [cURL](http://curl.haxx.se/) : cURL est un utilitaire multiplateforme utilisable pour travailler en ligne de commande avec les API REST. Dans ce document, il est utilisé pour communiquer avec l'API REST d'Ambari.
* [jq](https://stedolan.github.io/jq/) : jq est un utilitaire de ligne de commande multiplateforme conçu pour travailler avec des documents JSON. Dans ce document, il est utilisé pour analyser les documents JSON renvoyés par l'API REST d'Ambari.
* [Interface de ligne de commande Azure](../xplat-cli-install.md) : utilitaire de ligne de commande multiplateforme conçu pour travailler avec des services Azure.

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

##<a id="whatis"></a>Présentation d'Ambari

[Apache Ambari](http://ambari.apache.org) simplifie la gestion de Hadoop en fournissant une interface utilisateur web conviviale qui peut être utilisée pour approvisionner, gérer et surveiller les clusters Hadoop. Les développeurs peuvent intégrer ces fonctionnalités dans leurs applications à l’aide des [API REST Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

Ambari est fourni par défaut avec les clusters HDInsight Linux.

##API REST

L'URI de base pour l'API REST d'Ambari sur HDInsight est https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME, où __CLUSTERNAME__ est le nom de votre cluster.

> [AZURE.IMPORTANT] Bien que le nom du cluster dans la partie du nom de domaine complet (FQDN) de l’URI (CLUSTERNAME.azurehdinsight.net) ne respecte pas la casse, les autres occurrences dans l’URI respectent la casse. Par exemple, si le nom de votre cluster est MyCluster, les éléments suivants sont des URI valides :
>
> `https://mycluster.azurehdinsight.net/api/v1/clusters/MyCluster` `https://MyCluster.azurehdinsight.net/api/v1/clusters/MyCluster`
>
> Les URI suivantes renvoient une erreur, car la deuxième occurrence du nom n’utilise pas la casse correcte.
>
> `https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster` `https://MyCluster.azurehdinsight.net/api/v1/clusters/mycluster`

Une connexion à Ambari sur HDInsight requiert HTTPS. Lors de l’authentification de la connexion, vous devez utiliser le nom du compte d’administrateur (la valeur par défaut est __admin__) et le mot de passe que vous avez fournis au moment de la création du cluster.

L’exemple suivant utilise cURL pour effectuer une demande GET sur l’API REST. Remplacez __PASSWORD__ par le mot de passe de l’administrateur du cluster. Remplacez __CLUSTERNAME__ par le nom du cluster :

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME"
    
La réponse est un document JSON qui commence avec des informations similaires à ce qui suit :

    {
    "href" : "http://10.0.0.10:8080/api/v1/clusters/CLUSTERNAME",
    "Clusters" : {
        "cluster_id" : 2,
        "cluster_name" : "CLUSTERNAME",
        "health_report" : {
        "Host/stale_config" : 0,
        "Host/maintenance_state" : 0,
        "Host/host_state/HEALTHY" : 7,
        "Host/host_state/UNHEALTHY" : 0,
        "Host/host_state/HEARTBEAT_LOST" : 0,
        "Host/host_state/INIT" : 0,
        "Host/host_status/HEALTHY" : 7,
        "Host/host_status/UNHEALTHY" : 0,
        "Host/host_status/UNKNOWN" : 0,
        "Host/host_status/ALERT" : 0

Dans la mesure où il s’agit de JSON, il est plus facile d’utiliser un analyseur JSON pour utiliser les données. Par exemple, l’exemple suivant utilise jq pour afficher uniquement l’élément `health_report`.

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME" | jq '.Clusters.health_report'

##Exemple : Obtenir le nom de domaine complet de nœuds de cluster

Lorsque vous travaillez avec HDInsight, vous pouvez avoir besoin de connaître le nom de domaine complet (FQDN) d'un nœud de cluster. Vous pouvez facilement récupérer le nom de domaine complet des différents nœuds du cluster avec les commandes suivantes :

* __Nœuds principaux__ : `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'`
* __Nœuds de travail__ : `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/DATANODE" | jq '.host_components[].HostRoles.host_name'`
* __Nœuds Zookeeper__ : `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq '.host_components[].HostRoles.host_name'`

Veuillez noter que chaque exemple suit le même modèle :

1. Interroger un élément dont nous savons qu’il s’exécute sur ces nœuds.
2. Récupérer les éléments `host_name`, qui contiennent le FQDN pour ces nœuds.

L'élément `host_components` du document renvoyé contient plusieurs éléments. `.host_components[]`, puis la spécification d'un chemin d'accès dans l'élément, s'exécutera en boucle sur chaque élément et extraira la valeur du chemin d'accès spécifique. Si vous ne voulez qu'une seule valeur, par exemple la première entrée de nom de domaine complet, vous pouvez renvoyer les éléments sous forme de collection, puis sélectionner une entrée spécifique :

    jq '[.host_components[].HostRoles.host_name][0]'

Renvoie le premier FQDN de la collection.

##Exemple : Récupérer le compte de stockage et le conteneur par défaut

Lorsque vous créez un cluster HDInsight, vous devez utiliser un compte Azure Storage et un conteneur d'objets blob comme stockage par défaut du cluster. Vous pouvez utiliser Ambari pour récupérer ces informations après avoir créé le cluster. Par exemple, si vous souhaitez écrire des données par programme, directement dans le conteneur.

L'instruction suivante récupérera l'URI WASB du stockage par défaut du cluster :

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
    
> [AZURE.NOTE] Renvoie la première configuration appliquée au serveur (`service_config_version=1`), qui contient ces informations. Si vous récupérez une valeur qui a été modifiée après la création du cluster, vous devrez peut-être lister les versions de configuration et récupérer la plus récente.

Renvoie une valeur similaire à la suivante, où __CONTAINER__ est le conteneur par défaut et __ACCOUNTNAME__ est le nom de compte de stockage Azure :

    wasbs://CONTAINER@ACCOUNTNAME.blob.core.windows.net

Vous pouvez ensuite utiliser ces informations avec [Azure CLI](../xplat-cli-install.md) pour télécharger des données à partir du conteneur.

1. Récupérez le groupe de ressources du compte de stockage. Remplacez __ACCOUNTNAME__ par le nom du compte de stockage récupéré à partir d'Ambari :

        azure storage account list --json | jq '.[] | select(.name=="ACCOUNTNAME").resourceGroup'
    
    Renvoie le nom du groupe de ressources du compte.
    
    > [AZURE.NOTE] Si cette commande ne renvoie aucun résultat, vous devrez peut-être modifier l'interface de ligne de commande Azure pour la faire passer en mode Azure Resource Manager et réexécuter la commande. Pour passer en mode Azure Resource Manager, utilisez la commande suivante :
    >
    > `azure config mode arm`
    
2. Récupérez la clé du compte de stockage. Remplacez __GROUPNAME__ par le groupe de ressources de l'étape précédente. Remplacez __ACCOUNTNAME__ par le nom du compte de stockage :

        azure storage account keys list -g GROUPNAME ACCOUNTNAME --json | jq '.storageAccountKeys.key1'

    Cet exemple renvoie la clé primaire du compte.
    
3. Utilisez la commande télécharger pour stocker un fichier dans le conteneur :

        azure storage blob upload -a ACCOUNTNAME -k ACCOUNTKEY -f FILEPATH --container __CONTAINER__ -b BLOBPATH
        
    Remplacez __ACCOUNTNAME__ par le nom du compte de stockage. Remplacez __ACCOUNTKEY__ par la clé récupérée précédemment. __FILEPATH__ est le chemin d'accès du fichier à télécharger, tandis que __BLOBPATH__ est le chemin d'accès dans le conteneur.

    Par exemple, si vous souhaitez que le fichier s'affiche dans HDInsight à l'adresse wasbs://example/data/filename.txt, alors __BLOBPATH__ serait `example/data/filename.txt`.

##Exemple : mise à jour de la configuration Ambari

1. Obtenir la configuration actuelle, qu’Ambari enregistre comme « configuration souhaitée » :

        curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME?fields=Clusters/desired_configs"
        
    Cet exemple renvoie un document JSON avec la configuration actuelle (identifiée par la valeur _tag_) pour les composants installés sur le cluster. L’exemple suivant est un extrait des données renvoyées à partir d’un type de cluster Spark.
    
        "spark-metrics-properties" : {
            "tag" : "INITIAL",
            "user" : "admin",
            "version" : 1
        },
        "spark-thrift-fairscheduler" : {
            "tag" : "INITIAL",
            "user" : "admin",
            "version" : 1
        },
        "spark-thrift-sparkconf" : {
            "tag" : "INITIAL",
            "user" : "admin",
            "version" : 1
        }

    Dans cette liste, vous devez copier le nom du composant (par exemple, __spark\_thrift\_sparkconf__ et la valeur __tag__).
    
2. Récupérez la configuration du composant et de la balise à l’aide de la commande suivante. Remplacez __spark-thrift-sparkconf__ et __INITIAL__ par le composant et la balise dont vous souhaitez récupérer la configuration.

        curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations?type=spark-thrift-sparkconf&tag=INITIAL" | jq --arg newtag $(echo version$(date +%s%N)) '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
    
    Curl récupère le document JSON, puis jq est utilisé pour apporter des modifications aux données afin de créer un modèle. Le modèle est ensuite utilisé pour ajouter/modifier des valeurs de configuration. Plus précisément, il effectue les opérations suivantes :
    
    * Il crée une valeur unique qui contient la chaîne « version » et la date, qui est stockée dans __newtag__.
    * Il crée un document racine pour la nouvelle configuration souhaitée.
    * Il récupère le contenu du tableau `.items[]` et l’ajoute à l’élément __desired\_config__.
    * Il supprime les éléments __href__, __version__ et __Config__, car ils ne sont pas nécessaires à l’envoi d’une nouvelle configuration.
    * Il ajoute un nouvel élément __tag__ et définit sa valeur sur __version#################__. La partie numérique est basée sur la date actuelle. Chaque configuration doit avoir une balise unique.
    
    Enfin, les données sont enregistrées dans le document __newconfig.json__. La structure du document doit se présenter comme suit :
    
        {
            "Clusters": {
                "desired_config": {
                "tag": "version1459260185774265400",
                "type": "spark-thrift-sparkconf",
                "properties": {
                    ....
                 },
                 "properties_attributes": {
                     ....
                 }
            }
        }

3. Ouvrez le document __newconfig.json__ et modifiez/ajoutez des valeurs dans l’objet __properties__. Dans l’exemple suivant, la valeur de __spark.yarn.am.memory__ passe de __1g__ à __3g__ et un nouvel élément est ajouté pour __spark.kryoserializer.buffer.max__ avec une valeur de __256m__.

        "spark.yarn.am.memory": "3g",
        "spark.kyroserializer.buffer.max": "256m",

    Enregistrez le fichier une fois que vous avez apporté des modifications.

4. Utilisez la commande suivante pour envoyer la configuration mise à jour à Ambari.

        cat newconfig.json | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME"
        
    Cette commande dirige le contenu du fichier __newconfig.json__ vers la requête curl, qui l’envoie au cluster en tant que nouvelle configuration souhaitée. La demande cURL renvoie un document JSON. L’élément __versionTag__ de ce document doit correspondre à la version que vous avez envoyée et l’objet __configs__ contient les modifications de configuration que vous avez demandées.

###Exemple : redémarrer un composant de service

À ce stade, si vous regardez l’interface web d’Ambari, le service Spark indique qu’il doit être redémarré pour que la nouvelle configuration prenne effet. Procédez comme suit pour redémarrer le service.

1. Utilisez ce qui suit pour activer le mode de maintenance du service Spark :

        echo '{"RequestInfo": {"context": "turning on maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}' | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK"

    Cette commande permet d’envoyer un document JSON au serveur (contenu dans l’instruction `echo`), ce qui active le mode maintenance. Vous pouvez vérifier que le service est désormais en mode maintenance à l’aide de la demande suivante :
    
        curl -u admin:PASSWORD -H "X-Requested-By: ambari" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK" | jq .ServiceInfo.maintenance_state
        
    Cette commande retourne une valeur de `"ON"`.

3. Ensuite, utilisez ce qui suit pour désactiver le service :

        echo '{"RequestInfo": {"context" :"Stopping the Spark service"}, "Body": {"ServiceInfo": {"state": "INSTALLED"}}}' | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK"
        
    Cette commande renvoie une réponse similaire à ce qui suit.
    
        {
            "href" : "http://10.0.0.18:8080/api/v1/clusters/CLUSTERNAME/requests/29",
            "Requests" : {
                "id" : 29,
                "status" : "Accepted"
            }
        }
    
    La valeur `href` retournée par cet URI à l’aide de l’adresse IP interne du nœud du cluster. Pour l’utiliser depuis l’extérieur du cluster, remplacez la partie « 10.0.0.18:8080 » par le nom de domaine complet du cluster. Par exemple, la commande suivante récupère l’état de la demande.
    
        curl -u admin:PASSWORD -H "X-Requested-By: ambari" "https://CLUSTERNAME/api/v1/clusters/CLUSTERNAME/requests/29" | jq .Requests.request_status
    
    Si cette commande renvoie une valeur de `"COMPLETED"`, alors la requête est terminée.

4. Une fois la demande précédente terminée, utilisez ce qui suit pour démarrer le service.

        echo '{"RequestInfo": {"context" :"Restarting the Spark service"}, "Body": {"ServiceInfo": {"state": "STARTED"}}}' | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK"

    Une fois que le service redémarre, il s’agit des nouveaux paramètres de configuration.

5. Enfin, utilisez les éléments suivants pour désactiver le mode de maintenance.

        echo '{"RequestInfo": {"context": "turning off maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}' | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK"

##Étapes suivantes

Pour obtenir une référence complète de l'API REST, consultez la page [Référence V1 de l'API d'Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

> [AZURE.NOTE] Certaines fonctionnalités d'Ambari, telles que l'ajout ou la suppression d'hôtes du cluster, ou l'ajout de nouveaux services, sont désactivées, puisqu'il est géré par le service cloud HDInsight.

<!---HONumber=AcomDC_0921_2016-->