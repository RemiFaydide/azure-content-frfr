<properties
	pageTitle="Installation de MongoDB sur une machine virtuelle Windows | Microsoft Azure"
	description="Apprenez à installer MongoDB sur une machine virtuelle Azure créée avec le modèle de déploiement classique exécutant Windows Server."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="iainfoulds"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/20/2016"
	ms.author="iainfou"/>

#Installation de MongoDB sur une machine virtuelle Windows

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

[MongoDB][MongoDB] est une base de données NoSQL open-source qui offre des performances élevées. Cet article vous guide dans la création d’une nouvelle machine virtuelle Windows Server à l’aide du [portail Azure Classic][AzurePortal], la création et l’association d’un disque de données à la machine virtuelle, puis l’installation et la configuration de MongoDB. Si vous souhaitez utiliser une machine virtuelle existante dans Azure, vous pouvez passer directement à [l’installation et la configuration de MongoDB](#install-and-run-mongo-on-win2k8-vm).


## Création d’une machine virtuelle exécutant Windows Server

Pour créer une machine virtuelle, procédez comme suit :

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-windowsvm.md)]

> [AZURE.NOTE] Vous pouvez ajouter un point de terminaison pour MongoDB lors de la création de la machine virtuelle et le configurer comme suit : attribuez-lui le nom **Mongo**, utilisez **TCP**comme protocole et attribuez aux ports publics et privés la valeur **27017**.

## Association d’un disque de données
Pour fournir un stockage pour la machine virtuelle, associez un disque de données, puis initialisez-le de façon à ce qu'il puisse être utilisé par Windows. Il est possible d’associer un disque existant, si vous avez déjà des données à utiliser, ou un disque vide.

[AZURE.INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-windows-linux.md)]

Pour obtenir des instructions sur l’initialisation du disque, consultez la page « Initialisation d’un nouveau disque de données dans Windows Server », sous [Attachement d’un disque de données à une machine virtuelle Windows](virtual-machines-windows-classic-attach-disk.md).

## Installation et exécution de MongoDB sur la machine virtuelle

[AZURE.INCLUDE [install-and-run-mongo-on-win2k8-vm](../../includes/install-and-run-mongo-on-win2k8-vm.md)]

## Résumé
Ce didacticiel vous a montré comment créer une machine virtuelle exécutant Windows Server, vous y connecter à distance et y associer un disque de données. Vous avez également appris à installer et à configurer MongoDB sur la machine virtuelle Windows. Vous pouvez maintenant accéder à MongoDB sur la machine virtuelle Windows en suivant les rubriques avancées dans la [Documentation MongoDB][MongoDocs].

[MongoDocs]: http://docs.mongodb.org/manual/
[MongoDB]: http://www.mongodb.org/
[AzurePortal]: http://manage.windowsazure.com

<!---HONumber=AcomDC_0720_2016-->