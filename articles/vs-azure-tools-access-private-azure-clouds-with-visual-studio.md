<properties 
   pageTitle="Accès à des clouds Azure privés avec Visual Studio | Microsoft Azure"
   description="Découvrez comment accéder aux ressources de cloud privé à l'aide de Visual Studio."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# Accès à des clouds privés Azure avec Visual Studio

##Vue d'ensemble

Par défaut, Visual Studio prend en charge les points de terminaison REST du cloud public Azure. Cela peut être un problème, cependant, si vous utilisez Visual Studio avec un cloud privé Azure. Vous pouvez utiliser des certificats pour configurer Visual Studio afin d’accéder aux points de terminaison REST du cloud privé Azure. Vous pouvez obtenir ces certificats au moyen de votre fichier de paramètres de publication Azure.

## Pour accéder à un cloud privé Azure dans Visual Studio

1. Dans le [portail classique Azure](http://go.microsoft.com/fwlink/?LinkID=213885) du cloud privé, téléchargez votre fichier de paramètres de publication, ou contactez votre administrateur pour obtenir un fichier de paramètres de publication. Sur la version publique d'Azure, le lien de téléchargement est [https://manage.windowsazure.com/publishsettings/](https://manage.windowsazure.com/publishsettings/). (Le fichier que vous téléchargez doit avoir une extension .publishsettings.)

1. Dans l’**Explorateur de serveurs** de Visual Studio, sélectionnez le nœud **Azure** et, dans le menu contextuel, sélectionnez la commande **Gérer les abonnements**.

    ![Commande Gérer les abonnements](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790778.png)

1. Dans la boîte de dialogue **Gérer les abonnements Microsoft Azure**, sélectionnez l’onglet **Certificats**, puis sélectionnez le bouton **Importer**.

    ![Importation de certificats Azure](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790779.png)

1. Dans la boîte de dialogue **Importer des abonnements Microsoft Azure**, accédez au dossier où vous avez enregistré le fichier de paramètres de publication, sélectionnez le fichier, puis sélectionnez le bouton **Importer**. Cette opération importe les certificats du fichier de paramètres de publication dans Visual Studio. Vous devez maintenant être en mesure d'interagir avec vos ressources de cloud privé.

    ![Importation de paramètres de publication](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790780.png)

## Étapes suivantes

[Publication d’un service cloud Azure depuis Visual Studio](https://msdn.microsoft.com/library/azure/ee460772.aspx)

[Téléchargement et importation des paramètres de publication et des informations d’abonnement](https://msdn.microsoft.com/library/dn385850(v=nav.70).aspx)

<!---HONumber=AcomDC_0817_2016-->