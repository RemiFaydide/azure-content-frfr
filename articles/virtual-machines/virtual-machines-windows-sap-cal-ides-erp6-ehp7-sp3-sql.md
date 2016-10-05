<properties 
pageTitle="Déploiement de SAP IDE EHP7 SP3 pour SAP ERP 6.0 sur Microsoft Azure | Microsoft Azure" 
description="Déploiement de SAP IDE EHP7 SP3 pour SAP ERP 6.0 sur Microsoft Azure" 
services="virtual-machines-windows" 
documentationCenter="" 
authors="hermanndms" 
manager="timlt" 
editor="" 
tags="azure-resource-manager" 
keywords=""/>
<tags  
ms.service="virtual-machines-windows" 
ms.devlang="na" 
ms.topic="article" 
ms.tgt_pltfrm="vm-windows" 
ms.workload="infrastructure-services" 
ms.date="09/16/2016" 
ms.author="hermannd"/>


# Déploiement de SAP IDE EHP7 SP3 pour SAP ERP 6.0 sur Microsoft Azure 

Cet article explique comment déployer SAP IDES exécuté avec SQL Server et un système d’exploitation Windows sur Microsoft Azure via SAP Cloud Appliance Library 3.0. Les captures d’écran montrent le processus étape par étape. Le déploiement d’autres solutions de la liste fonctionne de la même façon en termes de processus. Il suffit de sélectionner une autre solution.

Pour démarrer avec SAP Cloud Appliance Library (SAP CAL), accédez à [cette page](https://cal.sap.com/). Il existe un blog SAP sur le nouveau [SAP Cloud Appliance Library 3.0](http://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience).


Les captures d’écran suivantes présentent la procédure étape par étape pour déployer SAP IDES sur Microsoft Azure. Le processus fonctionne de la même façon pour les autres solutions.


![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic1.jpg)

La première image montre toutes les solutions disponibles sur Microsoft Azure. La solution SAP IDES basée sur Windows uniquement disponible sur Azure en surbrillance a été choisie pour le processus.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic2.jpg)

Tout d’abord, il est nécessaire de créer un compte SAP CAL. Il existe actuellement deux possibilités pour Azure - Azure standard et Azure en Chine continentale qui est géré par notre partenaire 21Vianet.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic3.jpg)

Il faut ensuite entrer l’ID de l’abonnement Azure qui se trouve sur le portail Azure (voir également plus bas comment l’obtenir). Ensuite, un certificat de gestion Azure doit être téléchargé.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic6.jpg)

Dans le nouveau portail Azure, l’élément « Abonnements » se trouve sur le côté gauche. Cliquez dessus pour afficher tous les abonnements actifs pour votre utilisateur.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic7.jpg)

Sélectionnez l’un des abonnements puis choisissez « Certificats de gestion » afin de découvrir le nouveau concept qui utilise des « principaux du service » pour le nouveau modèle Azure Resource Manager. SAP CAL n’étant pas encore adapté pour ce nouveau modèle, il vous faut toujours le modèle « classique » et l’ancien portail pour travailler avec les certificats de gestion.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic4.jpg)

Ici, vous pouvez voir l’ancien portail Azure. Le téléchargement d’un certificat de gestion donne à SAP CAL les autorisations nécessaires à la création de machines virtuelles au sein d’un abonnement client. Dans l’onglet « ABONNEMENTS » se trouve l’ID d’abonnement qui doit être entré dans le portail SAP CAL.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic5.jpg)

Dans le deuxième onglet, il est ensuite possible de charger le certificat de gestion précédemment téléchargé depuis SAP CAL.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic8.jpg)

Une petite boîte de dialogue s’affiche pour sélectionner le fichier de certificat téléchargé.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic9.jpg)

Une fois que le certificat a été chargé, la connexion entre SAP CAL et l’abonnement Azure du client peut être testée au sein de SAP CAL. Un message indiquant que la connexion est valide doit apparaître.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic10.jpg)

Après avoir configuré un compte, il faut sélectionner une solution devant être déployée et créer une instance. Avec le mode « de base », c’est un jeu d’enfant. Entrez un nom d’instance, choisissez une région Azure et définissez un mot de passe principal pour la solution.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic11.jpg)

Après un délai variable en fonction de la taille et de la complexité de la solution (SAP CAL en donne une estimation), celle-ci apparaît comme « active » et prête à être utilisée. C’est très simple.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic12.jpg)

En examinant certains détails de la solution, il est possible de voir quel genre de machines virtuelles ont été déployées. Dans ce cas, nous avons une seule machine virtuelle Azure de taille D12 créée par SAP CAL.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic13.jpg)

Sur le portail Azure, les machines virtuelles sont repérables car elles commencent par le nom d’instance qui a été spécifié dans SAP CAL.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic14.jpg)

Il est à présent possible de se connecter à la solution via le bouton de connexion dans le portail SAP CAL. La petite boîte de dialogue contient un lien vers un guide d’utilisation donnant toutes les informations d’identification par défaut permettant de travailler avec la solution. [Voci](https://caldocs.hana.ondemand.com/caldocs/help/Getting_Started_Guide_IDES607MSSQL.pdf) le lien vers le guide de la solution IDES.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic15.jpg)

Une autre option consiste à se connecter à la machine virtuelle Windows et de commencer la SAP GUI préconfigurée.

<!---HONumber=AcomDC_0921_2016-->