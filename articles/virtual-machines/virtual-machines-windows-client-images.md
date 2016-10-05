<properties
   pageTitle="L’utilisation d’images d’un client Windows pour les scénarios de développement / test | Microsoft Azure"
   description="Comment utiliser les avantages de l’abonnement Visual Studio pour déployer Windows 7/8/10 dans Azure pour des scénarios de développement / de test"
   services="virtual-machines-windowse"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="08/31/2016"
   ms.author="iainfou"/>

# Utilisation d’un client Windows dans Azure pour les scénarios de développement/test

Vous pouvez utiliser Windows 7, Windows 8 ou Windows 10 dans Azure pour des scénarios de développement / de test à condition de disposer d'un abonnement Visual Studio (anciennement MSDN) approprié. Cet article décrit les conditions d’admissibilité pour les clients Windows en cours d’exécution dans Azure et l’utilisation des images de galerie Azure.


## Admissibilité à un abonnement
Les abonnés Visual Studio actifs (les personnes qui ont acquis une licence d’abonnement Visual Studio) peuvent utiliser un client Windows à des fins de développement et de tests. Un client Windows peut être utilisé sur votre propre matériel et vos machines virtuelles Azure en cours d’exécution dans n’importe quel type d’abonnement Azure. Il ne peut pas être déployé ou utilisé dans Azure pour la production normale ou utilisé par des personnes qui ne sont pas des abonnés Visual Studio actifs.

Pour votre commodité, certaines images Windows 10 disponibles sont disponibles dans la galerie Azure sous [eligible dev/test offers](#eligible-offers). Les abonnés Visual Studio dans n’importe quel type d’offre peuvent également [préparer et créer correctement](virtual-machines-windows-prepare-for-upload-vhd-image.md) une image 64 bits de Windows 7, Windows 8 ou Windows 10, puis la [charger dans Azure](virtual-machines-windows-upload-image.md). L’utilisation reste limitée au développement/test par les abonnés Visual Studio actifs.


## Offres admissibles
Le tableau suivant décrit en détail les ID d’offres admissibles pour le déploiement de Windows 10 par le biais de la galerie Azure. Les images Windows 10 sont uniquement visibles par les offres suivantes. Les abonnés Visual Studio qui doivent exécuter le client Windows dans un autre type d’offre doivent [préparer et créer correctement](virtual-machines-windows-prepare-for-upload-vhd-image.md) une image 64 bits de Windows 7, Windows 8 ou Windows 10, [puis la charger dans Azure](virtual-machines-windows-upload-image.md).

| Nom de l’offre | Numéro de l’offre | Images de client disponibles |
|:-----------|:------------:|:-----------------------:|
| [Pay-As-You-Go Dev/Test](https://azure.microsoft.com/offers/ms-azr-0023p/) | 0023P | Windows 10 |
| [Abonnés Visual Studio Enterprise (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/) | 0029P | Windows 10 |
| [Abonnés Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/) | 0059P | Windows 10 |
| [Abonnés Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/) | 0060P | Windows 10 |
| [Visual Studio Premium avec MSDN (avantage)](https://azure.microsoft.com/offers/ms-azr-0061p/) | 0061P | Windows 10 |
| [Abonnés Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) | 0063P | Windows 10 |
| [Abonnés Visual Studio Enterprise (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/) | 0064P | Windows 10 |
| [Enterprise Dev/Test](https://azure.microsoft.com/ofers/ms-azr-0148p/) | 0148P | Windows 10 |


## Vérification de votre abonnement Azure
Si vous ne connaissez pas votre ID d’offre, vous pouvez l’obtenir dans le portail Azure ou le portail de compte.

L’ID d’offre d’abonnement est indiquée dans le panneau « Abonnements » dans le portail Azure :

![Détails de l’ID de l’offre dans le portail Azure](./media/virtual-machines-windows-client-images/offer_id_azure_portal.png)

Vous pouvez également voir l’ID d’offre dans [l’onglet « Abonnements »](http://account.windowsazure.com/Subscriptions) du portail de compte Azure :

![Détails de l’ID de l’offre dans le portail de compte Azure](./media/virtual-machines-windows-client-images/offer_id_azure_account_portal.png)


## Étapes suivantes
Vous pouvez désormais déployer vos machines virtuelles à l’aide de [PowerShell](virtual-machines-windows-ps-create.md), de [modèles Resource Manager](virtual-machines-windows-ps-template.md) ou de [Visual Studio](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

<!---HONumber=AcomDC_0921_2016-->