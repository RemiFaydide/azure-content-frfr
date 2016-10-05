<properties
   pageTitle="Proxy inverse de Service Fabric | Microsoft Azure"
   description="Utilisez le proxy inverse de Service Fabric pour assurer les communications avec les microservices depuis l’intérieur et l’extérieur du cluster."
   services="service-fabric"
   documentationCenter=".net"
   authors="BharatNarasimman,vturecek"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="07/26/2016"
   ms.author="vturecek"/>

# Proxy inverse de Service Fabric

Le proxy inverse de Service Fabric est un proxy inverse intégré dans un Service Fabric, qui permet de gérer les microservices du cluster Service Fabric qui expose les points de terminaison HTTP.

## Modèle de communication des microservices

Dans un Service Fabric, les microservices s’exécutent généralement sur un sous-ensemble de machines virtuelles au sein du cluster et peuvent passer d’une machine virtuelle à l’autre, pour des raisons diverses. Ainsi, les points de terminaison associés aux microservices peuvent changer de manière dynamique. Pour les communications avec le microservice, le modèle classique adopté est la boucle de résolution ci-dessous :

1. Résolvez l’emplacement du service via le service de nommage initialement.
2. Connectez-vous au service.
3. Déterminez la cause des échecs de connexion et résolvez à nouveau l’emplacement du service, le cas échéant.

Ce processus implique généralement l’encapsulage des bibliothèques de communications côté client dans une boucle de nouvelle tentative, qui implémente les règles de nouvelle tentative et de résolution de service. Pour en savoir plus sur ce sujet, consultez [Communication avec les services](service-fabric-connect-and-communicate-with-services.md).

### Communication via un proxy inverse de Service Fabric
Le proxy inversé Service Fabric s’exécute sur tous les nœuds du cluster. Il exécute l’ensemble du processus de résolution de service pour un client, puis transmet la requête du client. Par conséquent, les clients en cours d’exécution sur le cluster peuvent utiliser n’importe quelle bibliothèque de communications HTTP côté client pour interagir avec le service cible, via le proxy inversé Service Fabric exécuté en local sur le même nœud.

![Communications internes][1]

## Atteindre les microservices depuis l’extérieur du cluster
Le modèle de communication externe par défaut des microservices est l’option d’**abonnement**, dans laquelle chaque service par défaut n’est pas accessible directement depuis des clients externes. [Azure Load Balancer](../load-balancer/load-balancer-overview.md) représente une limite réseau située entre des microservices et des clients externes, qui effectue la traduction des adresses réseau et transfère les requêtes externes vers des points de terminaison **IP:port** internes. Pour que le point de terminaison d’un microservice soit accessible aux clients externes, Azure Load Balancer doit tout d’abord être configuré pour transférer le trafic vers chaque port utilisé par le service au sein du cluster. En outre, la plupart des microservices (notamment les microservices avec état) ne se trouvent pas sur tous les nœuds du cluster ; ils peuvent se déplacer d’un nœud à un autre lors d’un basculement. Dans ce cas, Azure Load Balancer ne peut pas déterminer efficacement l’emplacement du nœud cible des réplicas auxquels transférer le trafic.

### Atteindre les microservices via le proxy inverse Service Fabric depuis l’extérieur du cluster

Au lieu de configurer les ports de services individuels dans Azure Load Balancer, vous pouvez vous contenter de définir le port du proxy inverse Service Fabric dans Azure Load Balancer. Cela permet aux clients se trouvant en dehors du cluster d’atteindre les services à l’intérieur du cluster via le proxy inverse sans qu’une configuration supplémentaire soit nécessaire.

![Communications externes][0]

>[AZURE.WARNING] La configuration du port du proxy inverse sur l’équilibreur de charge permet aux systèmes se trouvant à l’extérieur du cluster d’atteindre les microservices du cluster exposant le point de terminaison HTTP.


## Format d’URI pour l’adressage des services via le proxy inverse

Le proxy inverse utilise un format d’URI spécifique pour identifier la partition de service vers laquelle une requête entrante doit être transférée :

```
http(s)://<Cluster FQDN | internal IP>:Port/<ServiceInstanceName>/<Suffix path>?PartitionKey=<key>&PartitionKind=<partitionkind>&Timeout=<timeout_in_seconds>
```

 - **http(s) :** le proxy inverse peut être configuré pour accepter le trafic HTTP ou HTTPS. En cas de trafic HTTPS, une terminaison SSL se produit au niveau du proxy inverse. Les requêtes qui sont transférées par le proxy inverse aux services du cluster transitent via le protocole HTTP.
 - **Nom de domaine complet du cluster| internal IP:** For external clients, the reverse proxy can be configured so that it is reachable through the cluster domain (e.g., mycluster.eastus.cloudapp.azure.com). By default the reverse proxy runs on every node, so for internal traffic it can be reached on localhost or on any internal node IP (e.g., 10.0.0.1).
 - **Port :** port spécifié pour le proxy inverse. Exemple : 19008.
 - **ServiceInstanceName :** nom complet de l’instance de service déployée associée au service que vous tentez d’atteindre sans le schéma "fabric:/". Par exemple, pour atteindre le service *fabric:/myapp/myservice/*, vous pouvez utiliser *myapp/myservice*.
 - **Chemin d’accès au suffixe :** chemin d’accès réel à l’URL associée au service auquel vous souhaitez vous connecter. Exemple : *myapi/values/add/3*
 - **PartitionKey :** pour un service partitionné, il s’agit de la clé calculée associée à la partition que vous souhaitez atteindre. Il ne s’agit *pas* du GUID d’ID de la partition. Ce paramètre n’est pas obligatoire pour les services utilisant le schéma de partition de singleton.
 - **PartitionKind :** schéma de partition de service. Il peut s’agir de Named ou Int64Range. Ce paramètre n’est pas obligatoire pour les services utilisant le schéma de partition de singleton.
 - **Timeout :** spécifie le délai d’attente de la requête HTTP créée par le proxy inverse pour le service, pour le compte de la requête du client. La valeur par défaut est de 60 minutes. Paramètre facultatif.

### Exemple d’utilisation

Prenons l’exemple du service **fabric:/MyApp/MyService**, qui ouvre un écouteur HTTP sur l’URL suivante :

```
http://10.0.05:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/
```

Avec les ressources suivantes :

 - `/index.html`
 - `/api/users/<userId>`

Si le service utilise le schéma de partitionnement de singleton, les paramètres de la chaîne de requête *PartitionKey* et *PartitionKind* ne sont pas requis ; le service est accessible via la passerelle en tant que :

 - En externe : `http://mycluster.eastus.cloudapp.azure.com:19008/MyApp/MyService`
 - En interne : `http://localhost:19008/MyApp/MyService`

Si le service utilise le schéma de partitionnement Int64 uniforme, les paramètres de chaîne de requête *PartitionKey* et *PartitionKind* doivent être utilisés pour atteindre une partition du service :

 - En externe : `http://mycluster.eastus.cloudapp.azure.com:19008/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`
 - En interne : `http://localhost:19008/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`

Pour atteindre les ressources exposées par le service, il vous suffit de placer le chemin d’accès à la ressource après le nom du service dans l’URL :

 - En externe : `http://mycluster.eastus.cloudapp.azure.com:19008/MyApp/MyService/index.html?PartitionKey=3&PartitionKind=Int64Range`
 - En interne : `http://localhost:19008/MyApp/MyService/api/users/6?PartitionKey=3&PartitionKind=Int64Range`

La passerelle transmet ensuite ces requêtes à l’URL du service :

 - `http://10.0.05:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/index.html`
 - `http://10.0.05:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/api/users/6`

## Traitement spécial des services de partage de port

Application Gateway tente de résoudre à nouveau une adresse de service, relançant la requête lorsqu’un service est inaccessible. Il s’agit là de l’un des principaux avantages de la passerelle, car le code client n’a pas besoin d’implémenter sa propre boucle de résolution et sa propre résolution de service.

En règle générale, lorsqu’un service est inaccessible, cela signifie que le réplica ou l’instance de service ont été déplacés vers un autre nœud dans le cadre de leur cycle de vie normal. Dans ce cas, la passerelle peut recevoir une erreur de connexion au réseau, qui indique qu’un point de terminaison n’est plus ouvert sur l’adresse résolue à l’origine.

Toutefois, les instances de service ou réplicas peuvent partager un processus hôte, voire un port, lorsqu’ils sont hébergés par un serveur web basé sur http.sys. Cela inclut :

 - [System.Net.HttpListener](https://msdn.microsoft.com/library/system.net.httplistener%28v=vs.110%29.aspx)
 - [ASP.NET Core WebListener](https://docs.asp.net/latest/fundamentals/servers.html#weblistener)
 - [Katana](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.OwinSelfHost/)

Dans cette situation, le serveur web est disponible dans le processus hôte et répond aux requêtes, mais le réplica ou l’instance de service résolue ne sont plus accessibles sur l’hôte, très probablement. Dans ce cas, la passerelle reçoit une réponse HTTP 404 du serveur web. Par conséquent, une réponse HTTP 404 présente deux sens distincts :

 1. L’adresse du service est correcte, mais la ressource demandée par l’utilisateur n’existe pas.
 2. L’adresse du service est incorrecte, et la ressource demandée par l’utilisateur peut exister sur un autre nœud.

Dans le premier cas, il s’agit d’une erreur HTTP 404 normale, considérée comme une erreur de l’utilisateur. Toutefois, dans le deuxième cas, l’utilisateur a demandé une ressource qui n’existe pas, mais la passerelle n’a pas pu la localiser, car le service lui-même a été déplacé. Dans cette situation, la passerelle doit à nouveau résoudre l’adresse, puis réessayer.

La passerelle doit donc faire la distinction entre ces deux cas. Le serveur doit lui fournir une indication lui permettant d’y parvenir.

 - Par défaut, Application Gateway part du principe qu’il s’agit du deuxième cas et tente à nouveau de résoudre le problème, puis de renvoyer la requête.
 - Pour indiquer à Application Gateway qu’il s’agit du premier cas, le service doit renvoyer l’en-tête de réponse HTTP suivant :

`X-ServiceFabric : ResourceNotFound`

Cet en-tête indique une situation HTTP 404 normale, dans laquelle la ressource demandée n’existe pas, et la passerelle ne tente pas de résoudre à nouveau l’adresse du service.

## Installation et configuration
Le proxy inverse de Service Fabric peut être activé pour le cluster via le [modèle Azure Resource Manager](./service-fabric-cluster-creation-via-arm.md).

Une fois que vous disposez du modèle associé au cluster à déployer (à partir d’exemples de modèle ou via la création d’un modèle Resource Manager personnalisé), vous pouvez activer le proxy inverse dans le modèle, en procédant comme suit.

1. Définissez un port pour le proxy inverse, dans la [section des paramètres](../resource-group-authoring-templates.md) du modèle.

    ```json
    "SFReverseProxyPort": {
        "type": "int",
        "defaultValue": 19008,
        "metadata": {
            "description": "Endpoint for Service Fabric Reverse proxy"
        }
    },
    ```
2. Spécifier le port de chaque objet nodetype dans la [section du type de ressource](../resource-group-authoring-templates.md) **Cluster**

    ```json
    {
        "apiVersion": "2016-03-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
       "nodeTypes": [
          {
           ...
           "httpApplicationGatewayEndpointPort": "[parameters('SFReverseProxyPort')]",
           ...
          },
        ...
        ],
        ...
    }
    ```
3. Pour contacter le proxy inverse à partir de l’extérieur du cluster Azure, configurez les **règles Azure Load Balancer** pour le port spécifié à l’étape 1.

    ```json
    {
        "apiVersion": "[variables('lbApiVersion')]",
        "type": "Microsoft.Network/loadBalancers",
        ...
        ...
        "loadBalancingRules": [
            ...
            {
                "name": "LBSFReverseProxyRule",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID0')]"
                    },
                    "backendPort": "[parameters('SFReverseProxyPort')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig0')]"
                    },
                    "frontendPort": "[parameters('SFReverseProxyPort')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[concat(variables('lbID0'),'/probes/SFReverseProxyProbe')]"
                    },
                    "protocol": "tcp"
                }
            }
        ],
        "probes": [
            ...
            {
                "name": "SFReverseProxyProbe",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port":     "[parameters('SFReverseProxyPort')]",
                    "protocol": "tcp"
                }
            }  
        ]
    }
    ```
4. Pour configurer des certificats SSL sur le port du proxy inverse, ajoutez le certificat à la propriété httpApplicationGatewayCertificate dans la [section de type de ressource](../resource-group-authoring-templates.md) du **Cluster**

    ```json
    {
        "apiVersion": "2016-03-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]"
        ],
        "properties": {
            ...
            "httpApplicationGatewayCertificate": {
                "thumbprint": "[parameters('sfReverseProxyCertificateThumbprint')]",
                "x509StoreName": "[parameters('sfReverseProxyCertificateStoreName')]"
            },
            ...
            "clusterState": "Default",
        }
    }
    ```

## Étapes suivantes
 - Pour obtenir un exemple de communication HTTP entre services, consultez cet [exemple de projet sur GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/master/Services/WordCount).

 - [Appels de procédure distante avec Reliable Services à distance](service-fabric-reliable-services-communication-remoting.md)

 - [API Web qui utilise OWIN dans Reliable Services](service-fabric-reliable-services-communication-webapi.md)

 - [Communication WCF à l’aide de Reliable Services](service-fabric-reliable-services-communication-wcf.md)


[0]: ./media/service-fabric-reverseproxy/external-communication.png
[1]: ./media/service-fabric-reverseproxy/internal-communication.png

<!---HONumber=AcomDC_0921_2016-->