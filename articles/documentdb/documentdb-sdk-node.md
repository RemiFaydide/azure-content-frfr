<properties 
	pageTitle="API et Kit de développement logiciel (SDK) Node.js de DocumentDB | Microsoft Azure" 
	description="Découvrez l'API et le Kit de développement logiciel (SDK) Node.js, y compris les dates de lancement, les dates de suppression et les modifications apportées entre chaque version du Kit de développement logiciel (SDK) Node.js DocumentDB." 
	services="documentdb" 
	documentationCenter="nodejs" 
	authors="rnagpal" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="08/09/2016" 
	ms.author="rnagpal"/>

# API DocumentDB et Kits de développement logiciel (SDK)

> [AZURE.SELECTOR]
- [.NET](documentdb-sdk-dotnet.md)
- [Node.JS](documentdb-sdk-node.md)
- [Java](documentdb-sdk-java.md)
- [Python](documentdb-sdk-python.md)
- [REST](https://go.microsoft.com/fwlink/?LinkId=402413)
- [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)

##API et Kit de développement logiciel (SDK) DocumentDB Node.js

<table>
<tr><td>**Téléchargement du Kit de développement logiciel (SDK)**</td><td>[NPM](https://www.npmjs.com/package/documentdb)</td></tr>
<tr><td>**Documentation de l'API**</td><td>[Documentation de référence de l'API Node.js](http://azure.github.io/azure-documentdb-node/DocumentClient.html)</td></tr>
<tr><td>**Instructions d’installation du Kit de développement logiciel (SDK)**</td><td>[Instructions d'installation](http://azure.github.io/azure-documentdb-node/)</td></tr>
<tr><td>**Contribution au Kit de développement logiciel (SDK)**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-node/tree/master/source)</td></tr>
<tr><td>**Exemples**</td><td>[Exemples de codes Node.js](documentdb-nodejs-samples.md)</td></tr>
<tr><td>**Didacticiel de prise en main**</td><td>[Prise en main du SDK Node.js](documentdb-nodejs-get-started.md)</td></tr>
<tr><td>** Didacticiel d’application web **</td><td>[Créer une application web Node.js à l'aide de DocumentDB](documentdb-nodejs-application.md)</td></tr>
<tr><td>**Plateforme actuellement prise en charge**</td><td>[Node.js v0.10](https://nodejs.org/en/blog/release/v0.10.0/)<br/>[Node.js v0.12](https://nodejs.org/en/blog/release/v0.12.0/)<br/>[Node.js v4.2.0](https://nodejs.org/en/blog/release/v4.2.0/)</td></tr>
</table></br>

##Notes de publication

###<a name="1.9.0"/>1.9.0</a>

- Ajout de la prise en charge d’une stratégie de nouvelle tentative pour les requêtes limitées. (Les requêtes limitées reçoivent une exception de taux de requête excessif, code d’erreur 429.) Par défaut, DocumentDB accepte neuf nouvelles tentatives pour chaque requête lorsque le code d’erreur 429 est retourné, conformément au temps retryAfter spécifié dans l’en-tête de réponse. Il est désormais possible de définir un intervalle fixe de nouvelle tentative dans la propriété RetryOptions sur l’objet ConnectionPolicy, si vous souhaitez ignorer le temps retryAfter retourné par le serveur entre chaque nouvelle tentative. DocumentDB attend maintenant au maximum 30 secondes pour chaque requête limitée (quel que soit le nombre de nouvelles tentatives) et renvoie la réponse avec un code d’erreur 429. Cette durée peut également être remplacée dans la propriété RetryOptions sur l’objet ConnectionPolicy.

- DocumentDB retourne maintenant x-ms-throttle-retry-count and x-ms-throttle-retry-wait-time-ms comme en-têtes de réponse dans chaque requête afin d’indiquer le nombre limite de nouvelles tentatives et le cumul de temps d’attente observé par la requête entre les nouvelles tentatives.

- La classe RetryOptions a été ajoutée pour exposer la propriété RetryOptions sur la classe ConnectionPolicy, qui peut être utilisée pour substituer certaines des options de nouvelle tentative par défaut.

###<a name="1.8.0"/>1.8.0</a>

 - Ajout de la prise en charge des comptes de base de données de plusieurs régions.

###<a name="1.7.0"/>1.7.0</a>

- Ajout de la fonctionnalité de durée de vie (TTL) pour les documents.

###<a name="1.6.0"/>1.6.0</a>

- Implémentation des [collections partitionnées](documentdb-partition-data.md) et des [niveaux de performances définis par l’utilisateur](documentdb-performance-levels.md).

###<a name="1.5.6"/>1.5.6</a>

- Résolution du bogue RangePartitionResolver.resolveForRead là où les liens n’étaient pas renvoyés en raison d’une concaténation incorrecte des résultats.

###<a name="1.5.5"/>1.5.5</a>

- Résout hashParitionResolver resolveForRead() : levait une exception si aucune clé de partition n’était fournie, au lieu de renvoyer une liste de tous les liens enregistrés.

###<a name="1.5.4"/>1.5.4</a>

- Résolution du problème [n° 100](https://github.com/Azure/azure-documentdb-node/issues/100) : Agent HTTPS dédié : éviter de modifier l’agent global pour DocumentDB. Utilisez un agent dédié pour toutes les demandes de la bibliothèque.

###<a name="1.5.3"/>1.5.3</a>

- Résolution du problème [n° 81](https://github.com/Azure/azure-documentdb-node/issues/81) : gestion correcte des tirets dans les ID de média.

###<a name="1.5.2"/>1.5.2</a>

- Résolution du problème [n° 95](https://github.com/Azure/azure-documentdb-node/issues/95) : avertissement de fuite de l’écouteur EventEmitter.

###<a name="1.5.1"/>1.5.1</a>

- Résolution du problème [n° 92](https://github.com/Azure/azure-documentdb-node/issues/90) : dossier Hash renommé en hash pour les systèmes respectant la casse.

### <a name="1.5.0"/>1.5.0</a>

- Implémentation de la prise en charge du partitionnement via l’ajout de programmes de résolution de partitions de hachage et de plage.

### <a name="1.4.0"/>1.4.0</a>

- Implémentation de l’opération Upsert. Nouvelles méthodes upsertXXX sur documentClient.

### <a name="1.3.0"/>1.3.0</a>

- Ignorée pour aligner le numéro de version avec les autres Kits de développement logiciel (SDK).

### <a name="1.2.2"/>1.2.2</a>

- Fractionnement du wrapper des promesses Q dans un nouveau dépôt.
- Mise à jour du fichier de package pour le Registre npm.

### <a name="1.2.1"/>1.2.1</a>

- Implémentation du routage basé sur l’ID.
- Résolution du problème [n° 49](https://github.com/Azure/azure-documentdb-node/issues/49) - propriété actuelle en conflit avec la méthode current().

### <a name="1.2.0"/>1.2.0</a>

- Ajout de la prise en charge de l’index géospatial.
- Validation de la propriété ID pour toutes les ressources. Les ID des ressources ne peuvent pas contenir les caractères ?, /, #, &#47;&#47; ou se terminer par un espace.
- Ajout du nouvel en-tête « progression de la transformation de l’index » à ResourceResponse.

### <a name="1.1.0"/>1.1.0</a>

- Implémente la stratégie d’indexation V2.

### <a name="1.0.3"/>1.0.3</a>

- Problème [n° 40](https://github.com/Azure/azure-documentdb-node/issues/40) : implémentation des configurations eslint et grunt dans le Kit de développement logiciel (SDK) principal et de promesse.

### <a name="1.0.2"/>1.0.2</a>

- Problème [#45](https://github.com/Azure/azure-documentdb-node/issues/45) - Le wrapper de promesses n’inclut pas d’en-tête avec erreur.

### <a name="1.0.1"/>1.0.1</a>

- Implémentation de la possibilité de créer une requête pour les conflits en ajoutant readConflicts, readConflictAsync et queryConflicts.
- Mise à jour de la documentation de l’API.
- Problème [n° 41](https://github.com/Azure/azure-documentdb-node/issues/41) : Erreur client.createDocumentAsync.

### <a name="1.0.0"/>1.0.0</a>

- Kit de développement logiciel (SDK) GA

## Dates de lancement et de suppression
Microsoft fournira une notification au moins **12 mois** avant le retrait d’un Kit de développement logiciel (SDK) pour faciliter la transition vers une version plus récente/prise en charge.

Les nouvelles fonctionnalités et fonctions, et les optimisations sont uniquement ajoutées au Kit SDK actuel. Par conséquent, il est recommandé de toujours passer à la dernière version du SDK dès que possible.

Toute requête à DocumentDB utilisant un Kit SDK supprimé est rejetée par le service.

> [AZURE.WARNING]
Toutes les versions du Kit de développement logiciel (SDK) Azure DocumentDB pour Node.js antérieures à la version **1.0.0** seront supprimées le **29 février 2016**.

<br/>

| Version | Date de lancement | Date de suppression 
| ---	  | ---	         | ---
| [1\.9.0](#1.9.0) | 7 juillet 2016 |--- 
| [1\.8.0](#1.8.0) | 14 juin 2016 |--- 
| [1\.7.0](#1.7.0) | 26 avril 2016 |--- 
| [1\.6.0](#1.6.0) | 29 mars 2016 |--- 
| [1\.5.6](#1.5.6) | 8 mars 2016 |--- 
| [1\.5.5](#1.5.5) | 2 février 2016 |--- 
| [1\.5.4](#1.5.4) | 1er février 2016 |--- 
| [1\.5.2](#1.5.2) | 26 janvier 2016 |--- 
| [1\.5.2](#1.5.2) | 22 janvier 2016 |--- 
| [1\.5.1](#1.5.1) | 4 janvier 2016 |--- 
| [1\.5.0](#1.5.0) | 31 décembre 2015 |--- 
| [1\.4.0](#1.4.0) | 6 octobre 2015 |--- 
| [1\.3.0](#1.3.0) | 6 octobre 2015 |--- 
| [1\.2.2](#1.2.2) | 10 septembre 2015 |--- 
| [1\.2.1](#1.2.1) | 15 août 2015 |--- 
| [1\.2.0](#1.2.0) | 5 août 2015 |--- 
| [1\.1.0](#1.1.0) | 9 juillet 2015 |--- 
| [1\.0.3](#1.0.3) | 4 juin 2015 |--- 
| [1\.0.2](#1.0.2) | 23 mai 2015 |--- 
| [1\.0.1](#1.0.1) | 15 mai 2015 |--- 
| [1\.0.0](#1.0.0) | 8 avril 2015 |--- 
| 0.9.4-version préliminaire | 6 avril 2015 | 29 février 2016 
| 0.9.3-version préliminaire | 14 janvier 2015 | 29 février 2016 
| 0.9.2-version préliminaire | 18 décembre 2014 | 29 février 2016 
| 0.9.1-version préliminaire | 22 août 2014 | 29 février 2016 
| 0.9.0-version préliminaire | 21 août 2014 | 29 février 2016


## Forum Aux Questions
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## Voir aussi

Pour en savoir plus sur DocumentDB, consultez la page du service [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/).

<!---HONumber=AcomDC_0810_2016-->