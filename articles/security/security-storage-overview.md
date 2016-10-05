<properties
   pageTitle="Vue d’ensemble des fonctionnalités de sécurité d’Azure Storage | Microsoft Azure"
   description=" Azure Storage est la solution de stockage cloud pour les applications récentes qui s’appuient sur la durabilité, la disponibilité et l’extensibilité pour répondre aux besoins des clients. Cet article fournit une vue d’ensemble des principales fonctionnalités de sécurité Azure pouvant être utilisées avec Azure Storage. "
   services="security"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/16/2016"
   ms.author="terrylan"/>

# Vue d’ensemble des fonctionnalités de sécurité d’Azure Storage

Azure Storage est la solution de stockage cloud pour les applications récentes qui s’appuient sur la durabilité, la disponibilité et l’extensibilité pour répondre aux besoins des clients. Azure Storage fournit un ensemble complet de fonctionnalités de sécurité :

- Le compte de stockage peut être sécurisé à l’aide du contrôle d’accès en fonction du rôle et d’Azure Active Directory.
- Les données peuvent être sécurisées en transit entre une application et Azure au moyen du chiffrement côté client, de HTTPS ou de SMB 3.0.
- Les données peuvent être définies de façon à être automatiquement chiffrées du moment où elles sont écrites dans Azure Storage en utilisant Storage Service Encryption.
- Les disques de système d’exploitation et de données utilisés par les machines virtuelles peuvent être définis de façon à être chiffrés à l’aide d’Azure Disk Encryption.
- Il est possible d’accorder un accès délégué aux objets de données d’Azure Storage en utilisant des signatures d’accès partagé.
- La méthode d’authentification d’un utilisateur lorsqu’il accède au stockage peut être suivie à l’aide de l’analyse de Storage.

Pour une étude plus détaillée de la sécurité dans le Stockage Azure, consultez le [Guide de sécurité du Stockage Azure](../storage/storage-security-guide.md). Ce guide fournit une présentation approfondie les fonctionnalités de sécurité d’Azure Storage, notamment les clés de compte de stockage, le chiffrement des données en transit et au repos et l’analyse du stockage.

Cet article fournit une vue d’ensemble des fonctionnalités de sécurité Azure pouvant être utilisées avec le Stockage Azure. Les liens renvoient à des articles qui fournissent des informations détaillées complémentaires sur chaque fonctionnalité.

Voici les principales fonctionnalités abordées dans cet article :

- Contrôle d’accès en fonction du rôle
- Accès délégué aux objets de stockage
- Chiffrement en transit
- Chiffrement au repos et chiffrement Service Storage
- Azure Disk Encryption
- Azure Key Vault

## Contrôle d’accès en fonction du rôle

Vous pouvez sécuriser un compte de stockage en utilisant le contrôle d’accès en fonction du rôle (RBAC). Restreindre l’accès en fonction des principes du [besoin de connaître](https://en.wikipedia.org/wiki/Need_to_know) et du [privilège minimum](https://en.wikipedia.org/wiki/Principle_of_least_privilege) est impératif pour les organisations désireuses d’appliquer des stratégies de sécurité pour l’accès aux données. Ces droits d’accès sont octroyés en affectant le rôle RBAC approprié aux groupes et aux applications, dans une étendue donnée. Vous pouvez utiliser les [rôles RBAC intégrés](../active-directory/role-based-access-built-in-roles.md), comme Collaborateur de compte de stockage, pour affecter des privilèges aux utilisateurs.

En savoir plus :

- [Contrôle d’accès en fonction du rôle Azure Active Directory](../active-directory/role-based-access-control-configure.md)

## Accès délégué aux objets de stockage

Une signature d’accès partagé (SAP) fournit un accès délégué aux ressources de votre compte de stockage. La SAP vous permet d’octroyer à un client des autorisations d’accès limité à des objets de votre compte de stockage pendant une période donnée et avec un ensemble défini d’autorisations. Vous pouvez accorder ces autorisations limitées sans partager les clés d’accès de votre compte. La SAP est un URI qui englobe dans ses paramètres de requête toutes les informations nécessaires pour obtenir un accès authentifié à une ressource de stockage. Pour accéder aux ressources de stockage avec la signature d'accès partagé, il suffit au client de transmettre cette dernière à la méthode ou au constructeur approprié.

En savoir plus :

- [Présentation du modèle SAP](../storage/storage-dotnet-shared-access-signature-part-1.md)
- [Créer et utiliser une signature d’accès partagé avec Blob Storage](../storage/storage-dotnet-shared-access-signature-part-2.md)

## Chiffrement en transit
Le chiffrement en transit est un mécanisme de protection des données transmises sur des réseaux. Azure Storage vous permet de sécuriser les données à l’aide de diverses fonctionnalités :

- Le [chiffrement au niveau du transport](../storage/storage-security-guide.md#encryption-in-transit) (HTTPS, par exemple) lorsque vous transférez des données vers ou à partir d’Azure Storage.
- Le [chiffrement câblé](../storage/storage-security-guide.md#using-encryption-during-transit-with-azure-file-shares), par exemple le chiffrement SMB 3.0 pour les partages de fichiers Azure.
- Le [chiffrement côté client](../storage/storage-security-guide.md#using-client-side-encryption-to-secure-data-that-you-send-to-storage), pour chiffrer les données avant leur transfert vers Storage et les déchiffrer après leur transfert à partir de Storage.

En savoir plus sur le chiffrement côté client :

- [Client-Side Encryption for Microsoft Azure Storage (Chiffrement côté client pour Microsoft Azure Storage)](https://blogs.msdn.microsoft.com/windowsazurestorage/2015/04/28/client-side-encryption-for-microsoft-azure-storage-preview/)
- [Cloud security controls series: Encrypting Data in Transit (Série consacrée aux contrôles de sécurité dans le cloud : chiffrement des données en transit)](http://blogs.microsoft.com/cybertrust/2015/08/10/cloud-security-controls-series-encrypting-data-in-transit/)

## Chiffrement au repos

Pour de nombreuses organisations, le [chiffrement des données au repos](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) est une étape obligatoire du processus de gestion de la confidentialité, de la conformité et de la souveraineté des données. Trois fonctionnalités Azure fournissent un chiffrement des données « au repos ».

- [Storage Service Encryption](../storage/storage-security-guide.md#encryption-at-rest) vous permet de demander que le service de stockage chiffre automatiquement les données lors de leur écriture dans Azure Storage.
- [Client-side Encryption](../storage/storage-security-guide.md#client-side-encryption) fournit également la fonctionnalité de chiffrement au repos.
- [Azure Disk Encryption](../storage/storage-security-guide.md#using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines) vous permet de chiffrer les disques de données et de système d’exploitation utilisés par une machine virtuelle IaaS.

En savoir plus sur Storage Service Encryption :

- [Azure Storage Service Encryption](https://azure.microsoft.com/services/storage/) est disponible pour [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/). Pour plus d’informations sur les autres types de stockage Azure, consultez [File](https://azure.microsoft.com/services/storage/files/), [Disk (Premium Storage)](https://azure.microsoft.com/services/storage/premium-storage/), [Table](https://azure.microsoft.com/services/storage/tables/) et [Queue](https://azure.microsoft.com/services/storage/queues/).
- [Azure Storage Service Encryption pour les données au repos](../storage/storage-service-encryption.md)

## Azure Disk Encryption

Azure Disk Encryption pour les machines virtuelles vous permet de répondre aux exigences de conformité et de sécurité des organisations en chiffrant vos disques de machine virtuelle (y compris les disques d’amorçage et disques de données) avec des clés et stratégies que vous pouvez contrôler dans [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

Azure Disk Encryption pour les machines virtuelles est compatible avec les systèmes d’exploitation Linux et Windows. Cette fonctionnalité utilise également le coffre de clés pour vous aider à protéger, gérer et contrôler l’utilisation de vos clés de chiffrement de disque. Toutes les données contenues dans les disques de vos machines virtuelles sont chiffrées au repos à l’aide d’une technologie de chiffrement standard dans vos comptes Azure Storage. La solution Disk Encryption pour Windows est basée sur le [chiffrement de lecteur Microsoft BitLocker](https://technet.microsoft.com/library/cc732774.aspx) et la solution Linux est basée sur le chiffrement [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt).

En savoir plus :

- [Azure Disk Encryption pour des machines virtuelles IaaS Windows et Linux](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)

## Azure Key Vault

Azure Disk Encryption utilise [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) pour assurer le contrôle et la gestion des secrets et clés de chiffrement de disque au sein de votre abonnement au coffre de clés, tout en vous assurant que toutes les données des disques de machines virtuelles sont chiffrées au repos dans votre Stockage Azure. Nous vous recommandons d’utiliser Key Vault pour auditer les clés et l’utilisation de la stratégie.

En savoir plus :

- [Qu’est-ce qu’Azure Key Vault ?](../key-vault/key-vault-whatis.md)
- [Prise en main du coffre de clés Azure](../key-vault/key-vault-get-started.md)

<!---HONumber=AcomDC_0921_2016-->