<properties
   pageTitle="Spécification de points de terminaison de service Service Fabric | Microsoft Azure"
   description="Comment décrire les ressources du point de terminaison dans un manifeste de service, y compris comment configurer des points de terminaison HTTPS"
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/14/2016"
   ms.author="subramar"/>

# Spécifier des ressources dans un manifeste de service

## Vue d'ensemble

Le manifeste de service met les ressources à la disposition du service à déclarer/modifier sans changer le code compilé. Azure Service Fabric prend en charge la configuration des ressources des points de terminaison du service. L’accès aux ressources spécifiées dans le manifeste de service peut être contrôlé par le biais de la valeur SecurityGroup dans le manifeste de l’application. La déclaration des ressources permet de les modifier au moment du déploiement. Ainsi, le service n’a pas besoin d’introduire un nouveau mécanisme de configuration. La définition de schéma pour le fichier ServiceManifest.xml est installée avec le Kit de développement logiciel (SDK) Service Fabric et les outils sous *C:\\Program Files\\Microsoft SDKs\\Service Fabric\\schemas\\ServiceFabricServiceModel.xsd*.

## Points de terminaison

Lorsqu’une ressource de point de terminaison est définie dans le manifeste de service, Service Fabric alloue les ports de la plage de ports d’application réservés lorsqu’un port n’est pas spécifié de manière explicite. Examinez, par exemple, le point de terminaison *ServiceEndpoint1* spécifié dans l’extrait de code du manifeste fourni après ce paragraphe. En outre, les services peuvent également demander un port spécifique d’une ressource. Les réplicas de service exécutés sur des nœuds de cluster différents peuvent être alloués à des numéros de ports différents, tandis que les réplicas d’un service exécutés sur le même nœud partagent le port. Les réplicas de service peuvent alors utiliser ces ports pour la réplication et pour écouter les demandes du client.

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
    <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
    <Endpoint Name="ServiceEndpoint3" Protocol="https"/>
  </Endpoints>
</Resources>
```

Reportez-vous à [Configuration de services Reliable Services avec état](service-fabric-reliable-services-configuration.md) pour en savoir plus sur le référencement des points de terminaison à partir du fichier des paramètres de package de configuration (settings.xml).

## Exemple : spécification d’un point de terminaison HTTP pour votre service

Le manifeste de service suivant définit une seule ressource de point de terminaison TCP et deux ressources de point de terminaison HTTP dans l’élément &lt;Ressources&gt;.

Les points de terminaison HTTP sont automatiquement répertoriés dans la liste de contrôle d’accès par Service Fabric.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Stateful1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         This name must match the string used in the RegisterServiceType call in Program.cs. -->
    <StatefulServiceType ServiceTypeName="Stateful1Type" HasPersistedState="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>Stateful1.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Config package is the contents of the Config directoy under PackageRoot that contains an
       independently updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port number on which to
           listen. Note that if your service is partitioned, this port is shared with
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
      <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
      <Endpoint Name="ServiceEndpoint3" Protocol="https"/>

      <!-- This endpoint is used by the replicator for replicating the state of your service.
           This endpoint is configured through the ReplicatorSettings config section in the Settings.xml
           file under the ConfigPackage. -->
      <Endpoint Name="ReplicatorEndpoint" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## Exemple : spécification d’un point de terminaison HTTPS pour votre service

Le protocole HTTPS assure l'authentification du serveur et est également utilisé pour le chiffrement des communications client-serveur. Pour activer HTTPS sur votre service Service Fabric, spécifiez le protocole dans la section *Ressources -> Points de terminaison -> Point de terminaison* du manifeste de service, comme indiqué plus haut pour le point de terminaison *ServiceEndpoint3*.

>[AZURE.NOTE] Il est impossible de modifier le protocole d’un service lors de la mise à niveau de l’application sans que cela ne soit considéré comme une modification avec rupture.


Voici un exemple ApplicationManifest à définir pour le protocole HTTPS. Vous devez fournir l’empreinte numérique de votre certificat. EndpointRef est une référence à EndpointResource dans ServiceManifest, pour lequel vous définissez le protocole HTTPS. Vous pouvez ajouter plusieurs EndpointCertificate.

```
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="Application1Type"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="2" />
    <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
    <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion
       should match the Name and Version attributes of the ServiceManifest element defined in the
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <EndpointBindingPolicy CertificateRef="TestCert1" EndpointRef="ServiceEndpoint3"/>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types when an instance of this
         application type is created. You can also create one or more instances of service type by using the
         Service Fabric PowerShell module.

         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Stateful1">
      <StatefulService ServiceTypeName="Stateful1Type" TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]" MinReplicaSetSize="[Stateful1_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[Stateful1_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
      </StatefulService>
    </Service>
  </DefaultServices>
  <Certificates>
    <EndpointCertificate Name="TestCert1" X509FindValue="FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF F0" X509StoreName="MY" />  
  </Certificates>
</ApplicationManifest>
```

<!---HONumber=AcomDC_0921_2016-->