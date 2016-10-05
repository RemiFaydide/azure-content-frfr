<properties
   pageTitle="Azure Service Fabric sur Linux | Microsoft Azure"
   description="Les clusters Service Fabric prenant en charge Linux et Java, vous pouvez déployer et héberger des applications Service Fabric écrites en Java sur Linux."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="Java"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/14/2016"
   ms.author="SubramaR"/>

# Service Fabric sur Linux

La version d’évaluation sera mise à la disposition générale le 26 septembre comme annoncé [dans ce billet de blog](https://azure.microsoft.com/blog/service-fabric-on-linux-support-available-this-month/). La version d’évaluation de Service Fabric sur Linux vous permet de créer, déployer et gérer des applications à haut niveau de disponibilité et d’extensibilité dans l’environnement Linux de la même manière que sur Windows. De plus, les infrastructures générales de Service Fabric (Reliable Services et Reliable Actors) sont désormais disponibles dans Java sur Linux.

> [AZURE.VIDEO service-fabric-linux-preview]

## Systèmes d’exploitation et langages de programmation pris en charge

La version préliminaire limitée vous permet de créer des clusters de développement à boîtier unique ainsi que des clusters de plusieurs machines Azure exécutant Ubuntu Server 16.04.

Vous pouvez créer des [services exécutables invités](service-fabric-deploy-existing-app.md) dans tous les langages ou infrastructures. Vous pouvez également utiliser Java ou C# pour créer des services basés sur les infrastructures Reliable Services et Reliable Actors, en plus de l’orchestration de conteneurs Docker.

>[AZURE.NOTE] Les infrastructures Reliable Collections ne sont pas encore prises en charge dans Linux.

Service Fabric sur Linux est conceptuellement équivalent à Service Fabric sur Windows (à l’exception des caractéristiques du système d’exploitation et de la prise en charge du langage de programmation). Par conséquent, la plupart de notre [documentation existante](http://aka.ms/servicefabricdocs) s’applique pour vous permettre de vous familiariser avec la technologie.

## Étapes suivantes

Se familiariser avec les infrastructures de programmation [Reliable Actors](service-fabric-reliable-actors-introduction.md) et [Reliable Services](service-fabric-reliable-services-introduction.md).

<!---HONumber=AcomDC_0921_2016-->