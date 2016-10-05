<properties
	pageTitle="Présentation d’Azure Key Vault | Microsoft Azure"
	description="Azure Key Vault permet de protéger les clés de chiffrement et les secrets utilisés par les services et les applications cloud. En utilisant Azure Key Vault, les clients peuvent chiffrer les clés et secrets (tels que les clés d’authentification, les clés de compte de stockage, les clés de chiffrement de données, les fichiers .PFX et les mots de passe) à l’aide de clés protégées par des modules de sécurité matériels (HSM)."
	services="key-vault"
	documentationCenter=""
	authors="cabailey"
	manager="mbaldwin"
	tags="azure-resource-manager"/>

<tags
	ms.service="key-vault"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="09/27/2016"
	ms.author="cabailey"/>



# Qu’est-ce qu’Azure Key Vault ?

Azure Key Vault est disponible dans la plupart des régions. Pour plus d’informations, consultez la [page de tarification de Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).

## Introduction

Azure Key Vault permet de protéger les clés de chiffrement et les secrets utilisés par les services et les applications cloud. En utilisant Key Vault, vous pouvez chiffrer les clés et les secrets (tels que les clés d’authentification, les clés de compte de stockage, les clés de chiffrement de données, les fichiers PFX et les mots de passe) à l’aide de clés protégées par des modules de sécurité matériels (HSM). Pour une meilleure garantie, vous pouvez importer ou générer des clés HSM. Dans ce cas, Microsoft traite vos clés dans des modules de sécurité matériels validés selon la norme « FIPS 140-2 Level 2 » (matériel et microprogramme).

Key Vault rationalise le processus de gestion de clés et vous permet de garder le contrôle des clés qui accèdent à vos données et les chiffrent. Les développeurs peuvent créer des clés pour le développement et le test en quelques minutes, puis les migrer en toute transparence en clés de production. Les administrateurs de sécurité peuvent accorder (et annuler) les autorisations sur les clés, si nécessaire.

Utilisez le tableau suivant afin de mieux comprendre comment Key Vault peut permettre de répondre aux besoins des développeurs et des administrateurs de sécurité.





| Rôle | Définition du problème | Résolu par Azure Key Vault |
| ------------- |-------------|-----|
| Développeur d’une application Azure | « Je souhaite écrire une application pour Azure qui utilise des clés de signature et de chiffrement, mais je veux que ces clés soient externes à mon application afin que la solution soit adaptée à une application répartie au niveau géographique. <br/><br/>Je souhaite également protéger ces clés et secrets, sans avoir à écrire le code moi-même, et je veux qu’ils soient faciles à utiliser pour moi à partir de mes applications, avec des performances optimales. » | √ Les clés sont stockées dans un coffre et appelées par un URI, si nécessaire.<br/><br/> √ Les clés sont protégées par Azure, à l’aide d’algorithmes standard, des longueurs de clé et des modules de sécurité matériels (HSM).<br/><br/> √ Les clés sont traitées dans des modules de sécurité matériels situés dans les mêmes centres de données Azure que les applications. Cela garantit une meilleure fiabilité et une latence plus faible que si les clés résidaient dans un emplacement séparé, par exemple localement.|
| Développeur de logiciels en tant que service (SaaS) |« Je ne veux pas prendre la responsabilité des clés et secrets de mes clients. <br/><br/>Je souhaite que les clients possèdent et gèrent leurs clés pour pouvoir me concentrer sur ce que je fais le mieux, à savoir fournir les principales fonctionnalités du logiciel ». | √ Les clients peuvent importer leurs propres clés dans Azure et les gérer. Lorsqu’une application SaaS doit effectuer des opérations de chiffrement à l’aide des clés de leurs clients, Key Vault s’en charge à sa place. L’application ne voit pas les clés des clients.|
| Responsable de la sécurité | « Je veux être sûr que nos applications sont conformes à la norme FIPS 140-2HSM de niveau 2 pour la gestion sécurisée des clés. <br/><br/>Je souhaite m’assurer que mon organisation contrôle le cycle de vie d’une clé et peut surveiller son utilisation. <br/><br/>Et bien que nous utilisions plusieurs ressources et services Azure, je souhaite gérer les clés à partir d’un emplacement unique dans Azure. » |√ Les modules de sécurité matériels sont certifiés FIPS 140-2 de niveau 2.<br/><br/>√ Key Vault est conçu de sorte que Microsoft ne voit ni n’extrait vos clés.<br/><br/>√ L’utilisation de la clé est assortie d’une journalisation quasiment en temps réel.<br/><br/>√ Le coffre fournit une interface unique, indépendamment du nombre de coffres dont vous disposez dans Azure, des régions qui sont prises en charge et des applications qui les utilisent. |


Toute personne disposant d’un abonnement Azure peut créer et utiliser des coffres de clés. Bien que Key Vault procure des avantages aux développeurs et aux administrateurs de sécurité, il peut être implémenté et géré par l’administrateur d’une organisation qui gère les autres services Azure pour cette dernière. Par exemple, cet administrateur peut se connecter avec un abonnement Azure, créer un coffre pour l’organisation dans lequel stocker les clés et avoir la responsabilité de tâches opérationnelles, telles que :

+ créer ou importer une clé ou un secret ;
+ supprimer ou effacer une clé ou un secret ;
+ Autoriser des utilisateurs ou des applications à accéder au coffre de clés, afin qu’ils puissent gérer ou utiliser les clés et les clés secrètes
+ configurer l’utilisation de la clé (par exemple, signer ou chiffrer) ;
+ surveiller l’utilisation de clés.

Cet administrateur peut ensuite fournir aux développeurs des URI à appeler à partir de leurs applications et fournir à leur administrateur de sécurité des informations de journalisation sur l’utilisation des clés.

   ![Vue d’ensemble Azure Key Vault][1]

Les développeurs peuvent également gérer les clés directement à l’aide d’API. Pour plus d’informations, consultez le [guide du développeur de Key Vault](key-vault-developers-guide.md).

## Étapes suivantes

Pour un didacticiel de prise en main d’un administrateur, consultez la page [Prise en main d’Azure Key Vault](key-vault-get-started.md).

Pour plus d’informations sur l’utilisation de la journalisation du coffre de clés, consultez [journalisation d’Azure Key Vault](key-vault-logging.md).

Pour plus d’informations sur l’utilisation des clés et des secrets avec Azure Key Vault, consultez la page [About Keys, Secrets, and Certificates](https://msdn.microsoft.com/library/azure/dn903623(v=azure.1).aspx) (À propos des clés, des secrets et des certificats).


<!--Image references-->
[1]: ./media/key-vault-whatis/AzureKeyVault_overview.png

<!---HONumber=AcomDC_0928_2016-->