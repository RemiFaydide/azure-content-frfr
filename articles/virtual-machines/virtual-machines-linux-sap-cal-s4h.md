<properties 
pageTitle="Déployer S/4 HANA ou BW/4 HANA sur Microsoft Azure | Microsoft Azure" 
description="Déployer S/4 HANA ou BW/4 HANA sur Microsoft Azure" 
services="virtual-machines-linux" 
documentationCenter="" 
authors="hermanndms" 
manager="timlt" 
editor="" 
tags="azure-resource-manager" 
  keywords=""/>
<tags  
  ms.service="virtual-machines-linux" 
  ms.devlang="na" 
  ms.topic="article" 
  ms.tgt_pltfrm="vm-linux" 
  ms.workload="infrastructure-services" 
  ms.date="09/15/2016" 
  ms.author="hermannd"/>


# Déployer S/4 HANA ou BW/4 HANA sur Microsoft Azure 

Cet article explique comment déployer S/4 HANA sur Microsoft Azure via SAP Cloud Appliance Library 3.0. Les captures d’écran montrent le processus étape par étape. Le déploiement d’autres solutions basées sur SAP HANA comme BW/4 HANA fonctionne de la même façon en termes de processus. Il suffit de sélectionner une autre solution.

Pour démarrer avec SAP Cloud Appliance Library (SAP CAL), accédez à [cette page](https://cal.sap.com/). Il existe un blog SAP sur le nouveau [SAP Cloud Appliance Library 3.0](http://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience).


Les captures d’écran suivantes présentent la procédure étape par étape pour déployer S/4 HANA sur Microsoft Azure. Le processus fonctionne de la même façon pour les autres solutions telles que BW/4 HANA.


![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic-1b.jpg)

La première image montre toutes les solutions basées sur SAP CAL HANA qui sont disponibles sur Microsoft Azure. À titre d’exemple, le processus a été appliqué à l’« édition locale SAP S/4 HANA » (la solution présentée en bas de la capture d’écran).

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic-2.jpg)

Tout d’abord, il est nécessaire de créer un compte SAP CAL. Il existe actuellement deux possibilités pour Azure - Azure standard et Azure en Chine continentale qui est géré par notre partenaire 21Vianet.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic3b.jpg)

Il faut ensuite entrer l’ID de l’abonnement Azure qui se trouve sur le portail Azure (voir également plus bas comment l’obtenir). Ensuite, un certificat de gestion Azure doit être téléchargé.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic6b.jpg)

Dans le nouveau portail Azure, l’élément « Abonnements » se trouve sur le côté gauche. Cliquez dessus pour afficher tous les abonnements actifs pour votre utilisateur.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic7b.jpg)

Sélectionnez l’un des abonnements puis choisissez « Certificats de gestion » afin de découvrir le nouveau concept qui utilise des « principaux du service » pour le nouveau modèle Azure Resource Manager. SAP CAL n’étant pas encore adapté pour ce nouveau modèle, il vous faut toujours le modèle « classique » et l’ancien portail pour travailler avec les certificats de gestion.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic4b.jpg)

Ici, vous pouvez voir l’ancien portail Azure. Le téléchargement d’un certificat de gestion donne à SAP CAL les autorisations nécessaires à la création de machines virtuelles au sein d’un abonnement client. Dans l’onglet « ABONNEMENTS » se trouve l’ID d’abonnement qui doit être entré dans le portail SAP CAL.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic5.jpg)

Dans le deuxième onglet, il est ensuite possible de charger le certificat de gestion précédemment téléchargé depuis SAP CAL.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic8.jpg)

Une petite boîte de dialogue s’affiche pour sélectionner le fichier de certificat téléchargé.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic9.jpg)

Une fois que le certificat a été chargé, la connexion entre SAP CAL et l’abonnement Azure du client peut être testée au sein de SAP CAL. Un message indiquant que la connexion est valide doit apparaître.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic10.jpg)

Après avoir configuré un compte, il faut sélectionner une solution devant être déployée et créer une instance. Avec le mode « de base », c’est un jeu d’enfant. Entrez un nom d’instance, choisissez une région Azure et définissez un mot de passe principal pour la solution.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic11.jpg)

Après un délai variable en fonction de la taille et de la complexité de la solution (SAP CAL en donne une estimation), celle-ci apparaît comme « active » et prête à être utilisée. C’est très simple.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic12.jpg)

En examinant certains détails de la solution, il est possible de voir quel genre de machines virtuelles ont été déployées. Dans ce cas, trois machines virtuelles Azure de tailles et objectifs variables ont été créées.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic13.jpg)

Sur le portail Azure, les machines virtuelles sont repérables car elles commencent par le nom d’instance qui a été spécifié dans SAP CAL.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic14b.jpg)

Il est à présent possible de se connecter à la solution via le bouton de connexion dans le portail SAP CAL. La petite boîte de dialogue contient un lien vers un guide d’utilisation donnant toutes les informations d’identification par défaut permettant de travailler avec la solution.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic15.jpg)

Une autre option consiste à se connecter à la machine virtuelle Windows cliente et de commencer la SAP GUI préconfigurée.

<!---HONumber=AcomDC_0921_2016-->