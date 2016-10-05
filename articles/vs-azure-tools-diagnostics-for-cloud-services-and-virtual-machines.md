<properties
   pageTitle="Configuration de Diagnostics pour les services cloud et les machines virtuelles Azure | Microsoft Azure"
   description="Décrit comment configurer les informations de diagnostic pour le débogage des services cloud Azure et des machines virtuelles dans Visual Studio."
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

# Configuration de Diagnostics pour les services cloud et les machines virtuelles Azure

Lorsque vous devez dépanner un service cloud ou une machine virtuelle Azure, vous pouvez configurer les diagnostics Azure plus facilement à l’aide de Visual Studio. Le service diagnostics Azure capture les données système et les données de journalisation sur les machines virtuelles et instances de machine virtuelle qui exécutent votre service cloud, puis les transfère vers un compte de stockage de votre choix. Pour plus d’informations sur la journalisation des diagnostics dans Azure, consultez [Activer la journalisation des diagnostics pour les applications web dans Azure App Service](./app-service-web/web-sites-enable-diagnostic-log.md).

Cette rubrique explique comment activer et configurer le service diagnostics Azure dans Visual Studio, avant et après le déploiement, ainsi que sur des machines virtuelles Azure. Elle explique également comment sélectionner les types d’informations de diagnostic à collecter et comment les afficher après leur collecte.

Vous pouvez configurer les diagnostics Azure comme suit :

- Vous pouvez modifier les paramètres de configuration des diagnostics via la boîte de dialogue **Configuration des diagnostics** dans Visual Studio. Les paramètres sont enregistrés dans un fichier nommé diagnostics.wadcfgx (diagnostics.wadcfg dans le Kit de développement logiciel (SDK) Azure 2.4 ou les versions antérieures). Sinon, vous pouvez modifier directement le fichier de configuration. Si vous mettez le fichier à jour manuellement, les changements de configuration prennent effet lors du déploiement suivant du service cloud sur Azure, ou lors de son exécution dans l’émulateur.

- Pour modifier les paramètres des diagnostics pour un service cloud ou une machine virtuelle en cours d’exécution, utilisez **Cloud Explorer** ou l’**Explorateur de serveurs** dans Visual Studio.

## Modifications apportées aux diagnostics Azure 2.6

Pour les projets du Kit de développement logiciel (SDK) Azure 2.6 dans Visual Studio, les modifications suivantes ont été apportées. Ces modifications s’appliquent également aux versions ultérieures du Kit de développement logiciel (SDK) Azure.

- L’émulateur local prend désormais en charge les diagnostics. Cela signifie que vous pouvez collecter les données de diagnostic et vérifier que votre application crée les traces appropriées lorsque vous développez et testez dans Visual Studio. La chaîne de connexion `UseDevelopmentStorage=true` permet la collecte des données de diagnostic lorsque vous exécutez votre projet de service cloud dans Visual Studio à l’aide de l’émulateur de stockage Azure. Toutes les données de diagnostic sont collectées dans le compte de stockage (stockage de développement).

- La chaîne de connexion de compte de stockage des diagnostics (Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString) est à nouveau stockée dans le fichier de configuration de service (.cscfg). Dans le Kit de développement logiciel (SDK) Azure 2.5, le compte de stockage des diagnostics a été spécifié dans le fichier diagnostics.wadcfgx.

Il existe des différences notables entre la façon dont la chaîne de connexion fonctionnait dans le Kit de développement logiciel (SDK) Azure 2.4 et les versions antérieures, et celle dont elle fonctionne dans le Kit de développement logiciel (SDK) Azure 2.6 et les versions ultérieures.

- Dans le Kit de développement logiciel (SDK) Azure 2.4 et les versions antérieures, la chaîne de connexion était utilisée comme un runtime par le plug-in des diagnostics pour obtenir les informations du compte de stockage relatives au transfert de journaux de diagnostic.

- Dans le Kit de développement logiciel (SDK) Azure 2.6 et les versions ultérieures, la chaîne de connexion des diagnostics est utilisée par Visual Studio pour configurer l’extension de diagnostics avec les informations de compte de stockage appropriées lors de la publication. La chaîne de connexion permet de définir des comptes de stockage différents pour différentes configurations de service que Visual Studio utilise lors de la publication. Toutefois, étant donné que le plug-in des diagnostics n’est plus disponible (après le Kit de développement logiciel (SDK) Azure 2.5), le fichier .cscfg par lui-même ne peut pas activer l’extension des diagnostics. Vous devez activer l’extension séparément par le biais d’outils tels que Visual Studio ou PowerShell.

- Pour simplifier le processus de configuration de l’extension des diagnostics avec PowerShell, la sortie du package à partir de Visual Studio contient également les fichiers XML de configuration publique pour l’extension des diagnostics pour chaque rôle. Visual Studio utilise la chaîne de connexion des diagnostics pour renseigner les informations de compte de stockage présentes dans la configuration publique. Les fichiers de configuration publique sont créés dans le dossier Extensions et suivent le modèle PaaSDiagnostics.&lt;RoleName>.PubConfig.xml. Tout déploiement basé sur PowerShell peut utiliser ce modèle pour mapper chaque configuration à un rôle.

- La chaîne de connexion dans le fichier .cscfg est également utilisée par le [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040) pour accéder aux données de diagnostic afin de pouvoir les intégrer sous l’onglet **Analyse**. La chaîne de connexion est nécessaire pour configurer le service afin qu’il affiche les données d’analyse détaillées dans le portail.

## Migration de projets vers le Kit de développement logiciel (SDK) Azure 2.6 et les versions ultérieures

Lors d’une migration du Kit de développement logiciel (SDK) Azure 2.5 vers le Kit de développement logiciel (SDK) Azure 2.6 ou les versions ultérieures, si vous avez un compte de stockage des diagnostics spécifié dans le fichier .wadcfgx, il y reste. Pour tirer parti de la souplesse que représente l’utilisation de différents comptes de stockage pour différentes configurations de stockage, vous devez ajouter manuellement la chaîne de connexion à votre projet. Si vous migrez un projet à partir d'Azure SDK 2.4 ou d’une version antérieure vers Azure SDK 2.6, les chaînes de connexion de diagnostic sont conservées. Toutefois, notez les modifications apportées à la manière dont les chaînes de connexion sont traitées dans le Kit de développement logiciel (SDK) Azure 2.6, comme spécifié dans la section précédente.

### Détermination du compte de stockage des diagnostics par Visual Studio

- Si une chaîne de connexion des diagnostics est spécifiée dans le fichier .cscfg, Visual Studio l’utilise pour configurer l’extension des diagnostics lors de la publication et lors de la génération des fichiers xml de configuration publique durant l’empaquetage.

- Si aucune chaîne de connexion des diagnostics n’est spécifiée dans le fichier .cscfg, Visual Studio utilise à nouveau le compte de stockage spécifié dans le fichier .wadcfgx pour configurer l’extension des diagnostics lors de la publication et de la génération des fichiers xml de configuration publique durant l’empaquetage.

- La chaîne de connexion des diagnostics dans le fichier .cscfg est prioritaire sur le compte de stockage dans le fichier .wadcfgx. Si une chaîne de connexion des diagnostics est spécifiée dans le fichier .cscfg, Visual Studio l’utilise et ignore le compte de stockage dans .wadcfgx.

### Quel est le rôle de la case à cocher « Mettre à jour les chaînes de connexion de stockage de développement… » ?

La case à cocher **Mettre à jour les chaînes de connexion de stockage de développement pour les diagnostics et la mise en cache avec les informations d’identification du compte de stockage Microsoft Azure lors de la publication vers Microsoft Azure** offre un moyen pratique pour mettre à jour toute chaîne de connexion de compte de stockage de développement avec le compte de stockage Azure spécifié lors de la publication.

Par exemple, supposons que vous activiez cette case à cocher et que la chaîne de connexion des diagnostics spécifie `UseDevelopmentStorage=true`. Lorsque vous publiez le projet sur Azure, Visual Studio met automatiquement à jour la chaîne de connexion des diagnostics avec le compte de stockage que vous avez spécifié dans l’Assistant Publication. Toutefois, si un compte de stockage réel a été spécifié comme chaîne de connexion des diagnostics, ce compte est utilisé à la place.

## Différences entre les fonctionnalités de diagnostics du Kit de développement logiciel (SDK) Azure 2.4 et les versions antérieures et du Kit de développement logiciel (SDK) Azure 2.5 et les versions ultérieures

Si vous mettez à niveau votre projet du Kit de développement logiciel (SDK) Azure 2.4 vers le Kit de développement logiciel (SDK) Azure 2.5 ou les versions ultérieures, vous devez garder à l’esprit les différences existant entre les fonctionnalités de diagnostics suivantes.

- **Les API de configuration sont déconseillées** : la configuration par programmation des diagnostics est disponible dans le Kit de développement logiciel (SDK) Azure 2.4 ou les versions antérieures, mais déconseillée dans le Kit de développement logiciel (SDK) Azure 2.5 et les versions ultérieures. Si votre configuration des diagnostics est actuellement définie dans un code, vous devez reconfigurer ces paramètres à partir de rien dans le projet migré, afin que les diagnostics continuent à fonctionner. Le nom du fichier de configuration des diagnostics est diagnostics.wadcfg dans le Kit de développement logiciel (SDK) Azure 2.4 et diagnostics.wadcfgx dans le Kit de développement logiciel (SDK) Azure 2.5 et les versions ultérieures.

- **Les diagnostics pour les applications du service cloud peuvent uniquement être configurés au niveau du rôle, pas au niveau de l’instance.**

- **Chaque fois que vous déployez votre application, la configuration des diagnostics est mise à jour** : cela peut occasionner des problèmes de parité si vous modifiez votre configuration des diagnostics à partir de l’Explorateur de serveurs, puis redéployez votre application.

- **Dans le Kit de développement logiciel (SDK) Azure 2.5 et les versions ultérieures, les vidages sur incident sont configurés dans le fichier de configuration des diagnostics, pas dans le code** : si vous avez des vidages sur incident configurés dans le code, vous devez transférer la configuration manuellement du code vers le fichier de configuration, car les vidages sur incident ne sont pas transférés durant la migration vers le Kit de développement logiciel (SDK) Azure 2.6.

## Activation des diagnostics dans les projets de service cloud avant leur déploiement

Dans Visual Studio, vous pouvez choisir de recueillir des données de diagnostic pour des rôles exécutés dans Azure, lorsque vous exécutez le service dans l’émulateur avant le déploiement du service. Toutes les modifications apportées aux paramètres de diagnostic dans Visual Studio sont enregistrées dans le fichier de configuration diagnostics.wadcfgx. Ces paramètres de configuration spécifient le compte de stockage où les données de diagnostic sont enregistrées lorsque vous déployez votre service cloud.

### Activation des diagnostics dans Visual Studio avant le déploiement

1. Dans le menu contextuel du rôle qui vous intéresse, choisissez **Propriétés**, puis l’onglet **Configuration** dans la fenêtre **Propriétés** du rôle.

1. Dans la section **Diagnostics**, vérifiez que la case à cocher **Activer les diagnostics** est activée.

    ![Accès à l’option Activer les diagnostics](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796660.png)

1. Cliquez sur le bouton de sélection (...) pour spécifier le compte de stockage dans lequel vous souhaitez stocker les données de diagnostic. Les données de diagnostic seront stockées à cet emplacement.

    ![Spécifier le compte de stockage à utiliser](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796661.png)

1. Dans la boîte de dialogue **Créer une chaîne de connexion de stockage**, spécifiez si vous souhaitez vous connecter à l’aide de l’émulateur de stockage Azure, d’un abonnement Azure, ou d’informations d’identification entrées manuellement.

    ![Boîte de dialogue Compte de stockage](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796662.png)

  - Si vous choisissez l’option Émulateur de stockage Microsoft Azure, la chaîne de connexion est définie sur UseDevelopmentStorage=true.

  - Si vous choisissez l’option Votre abonnement, vous pouvez choisir l’abonnement Azure que vous souhaitez utiliser et le nom du compte. Vous pouvez choisir le bouton Gérer les comptes pour gérer vos abonnements Azure.

  - Si vous choisissez l’option Informations d’identification entrées manuellement, vous êtes invité à entrer le nom et la clé du compte Azure que vous souhaitez utiliser.

1. Choisissez le bouton **Configurer** pour afficher la boîte de dialogue **onfiguration de diagnostic**. Chaque onglet (à l’exception de **Général** et **Répertoires de journaux**) correspond à une source de données de diagnostic que vous pouvez collecter. L’onglet par défaut, **Général**, offre les options de collecte de données de diagnostic suivantes : **Erreurs uniquement**, **Toutes les informations** et **Plan personnalisé**. L’option par défaut, **Erreurs uniquement**, nécessite le plus petit volume de stockage, car elle ne transfère pas les messages d’avertissement ou de suivi. L’option Toutes les informations transfère le plus grand nombre d’informations. Elle est donc la plus coûteuse en termes de stockage.

    ![Activer les diagnostics Azure et la configuration](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758144.png)

1. Pour cet exemple, sélectionnez l’option **Plan personnalisé** afin de pouvoir personnaliser les données collectées.

1. Le champ **Quota de disque en Mo** spécifie l’espace à allouer aux données de diagnostic dans le compte de stockage. Vous pouvez modifier la valeur par défaut si vous le souhaitez.

1. Sous chaque onglet des données de diagnostic à collecter, activez la case à cocher **Activer le transfert de <type de journal>**. Par exemple, si vous voulez collecter les journaux d’application, sélectionnez la case à cocher **Activer le transfert des journaux d’application** sous l’onglet **Journaux d’application**. Spécifiez également toutes autres informations requises pour chaque type de données de diagnostic. Pour plus d’informations sur la configuration de chaque onglet, consultez la section **Configuration des sources de données de diagnostic**, plus loin dans cette rubrique.

1. Après avoir activé la collecte de toutes les données de diagnostic souhaitées, cliquez sur le bouton **OK**.

1. Exécutez votre projet de service cloud Azure dans Visual Studio comme d’habitude. À mesure que vous utilisez votre application, les informations de journalisation que vous avez sélectionnées sont enregistrées dans le compte de stockage Azure que vous avez spécifié.

## Activation des diagnostics dans des machines virtuelles Azure

Dans Visual Studio, vous pouvez choisir de recueillir des données de diagnostic pour des machines virtuelles Azure.

### Pour activer les diagnostics dans des machines virtuelles Azure

1. Dans l’**Explorateur de serveurs**, sélectionnez le nœud Azure, puis connectez-vous à votre abonnement Azure si ce n’est déjà fait.

1. Développez le nœud **Machines virtuelles**. Vous pouvez sélectionner ou créer une machine virtuelle.

1. Dans le menu contextuel de la machine virtuelle qui vous intéresse, sélectionnez **Configurer**. Cela a pour effet d’afficher la boîte de dialogue de configuration de la machine virtuelle.

    ![Configuration d’une machine virtuelle Azure](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796663.png)

1. Ajoutez l’extension Microsoft Monitoring Agent Diagnostics si elle n’est pas déjà installée. Cette extension vous permet de collecter les données de diagnostic pour la machine virtuelle Azure. Dans la liste Extensions installées, cliquez sur le menu déroulant Sélectionner une extension disponible, puis sur Microsoft Monitoring Agent Diagnostics.

    ![Installation d’une extension de machine virtuelle Azure](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766024.png)

    >[AZURE.NOTE] D’autres extensions des diagnostics sont disponibles pour vos machines virtuelles. Pour plus d’informations, consultez Fonctionnalités et extensions de machine virtuelle Azure.

1. Pour ajouter l’extension et afficher sa boîte de dialogue **Configuration des diagnostics**, cliquez sur le bouton **Ajouter**.

1. Cliquez sur le bouton **Configurer** pour spécifier un compte de stockage, puis cliquez sur **OK**.

    Chaque onglet (à l’exception de **Général** et **Répertoires de journaux**) correspond à une source de données de diagnostic que vous pouvez collecter.

    ![Activer les diagnostics Azure et la configuration](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758144.png)

    L’onglet par défaut, **Général**, offre les options de collecte de données de diagnostic suivantes : **Erreurs uniquement**, **Toutes les informations** et **Plan personnalisé**. L’option par défaut, **Erreurs uniquement**, nécessite le plus petit volume de stockage, car elle ne transfère pas les messages d’avertissement ou de suivi. L’option **Toutes les informations** transfère le plus grand nombre d’informations. Elle est donc la plus coûteuse en termes de stockage.

1. Pour cet exemple, sélectionnez l’option **Plan personnalisé** afin de pouvoir personnaliser les données collectées.

1. Le champ **Quota de disque en Mo** spécifie l’espace à allouer aux données de diagnostic dans le compte de stockage. Vous pouvez modifier la valeur par défaut si vous le souhaitez.

1. Sous chaque onglet des données de diagnostic à collecter, activez la case à cocher **Activer le transfert de <type de journal>**.

    Par exemple, si vous voulez collecter les journaux d’application, sélectionnez la case à cocher **Activer le transfert des journaux d’application** sous l’onglet **Journaux d’application**. Spécifiez également toutes autres informations requises pour chaque type de données de diagnostic. Pour plus d’informations sur la configuration de chaque onglet, consultez la section **Configuration des sources de données de diagnostic**, plus loin dans cette rubrique.

1. Après avoir activé la collecte de toutes les données de diagnostic souhaitées, cliquez sur le bouton **OK**.

1. Enregistrez le projet mis à jour.

    Un message s’affiche dans la fenêtre **Journal des activités Microsoft Azure**, indiquant que la machine virtuelle a été mise à jour.

## Configuration des sources de données de diagnostic

Après avoir activé la collecte des données de diagnostic, vous pouvez sélectionner précisément les sources de données et les informations à collecter. La section suivante répertorie les onglets de la boîte de dialogue **Configuration des diagnostics** et indique la signification de chaque option de configuration.

### Journaux d’application

**Un journal d’application** contient des informations de diagnostic produites par une application web. Si vous voulez capturer les journaux d’application, sélectionnez la case à cocher **Activer le transfert des journaux d’application**. Vous pouvez augmenter ou réduire le nombre de minutes pendant lesquelles les journaux d’application sont transférés vers votre compte de stockage en modifiant la valeur du champ **Période de transfert (min)**. Vous pouvez également modifier la quantité d’informations consignées dans le journal en définissant le niveau de journalisation. Par exemple, vous pouvez sélectionner **Détaillé** pour collecter un maximum d’informations, ou **Critique** pour ne recueillir que les erreurs critiques. Si vous disposez d’un fournisseur de diagnostics spécifique qui génère des journaux d’application, vous pouvez capturer ceux-ci en définissant la valeur du champ **GUID du fournisseur**.

  ![Journaux d’application](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758145.png)

  Pour plus d’informations sur les journaux d’application, consultez [Activer la journalisation des diagnostics pour les applications web dans Azure App Service](./app-service-web/web-sites-enable-diagnostic-log.md).

### Journaux d’événements Windows

Si vous voulez capturer les journaux des événements Windows, sélectionnez la case à cocher **Activer le transfert des journaux des événements Windows**. Vous pouvez augmenter ou réduire le nombre de minutes pendant lesquelles les journaux d’événements sont transférés vers votre compte de stockage en modifiant la valeur du champ **Période de transfert (min)**. Activez les cases à cocher correspondant aux types d’événements que vous voulez suivre.

  ![Journaux d’événements](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796664.png)

Si vous utilisez le Kit de développement logiciel (SDK) Azure 2.6 ou des versions ultérieures et souhaitez spécifier une source de données personnalisée, entrez-la dans la zone de texte **la source de données>**, puis sélectionnez le bouton **Ajouter** en regard de celle-ci. La source de données est ajoutée au fichier diagnostics.cfcfg.

Si vous utilisez le Kit de développement logiciel (SDK) Azure 2.5 et souhaitez spécifier une source de données personnalisée, vous pouvez l’ajouter dans la section `WindowsEventLog` du fichier diagnostics.wadcfgx, comme dans l’exemple suivant.

```
<WindowsEventLog scheduledTransferPeriod="PT1M">
   <DataSource name="Application!*" />
   <DataSource name="CustomDataSource!*" />
</WindowsEventLog>
```
### Compteurs de performances

Les informations d’un compteur de performances peuvent vous aider à localiser des goulets d’étranglement système et à affiner les performances des applications et du système. Pour plus d’informations, consultez [Créer et utiliser des compteurs de performances dans une application Azure](https://msdn.microsoft.com/library/azure/hh411542.aspx). Si vous voulez capturer les compteurs de performances, sélectionnez la case à cocher **Activer le transfert des compteurs de performances**. Vous pouvez augmenter ou réduire le nombre de minutes pendant lesquelles les journaux d’événements sont transférés vers votre compte de stockage en modifiant la valeur du champ **Période de transfert (min)**. Activez les cases à cocher correspondant aux compteurs de performances que vous voulez suivre.

  ![Compteurs de performance](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758147.png)

Pour suivre un compteur de performances non répertorié, entrez-le en utilisant la syntaxe suggérée, puis cliquez sur le bouton **Ajouter**. Le système d’exploitation de la machine virtuelle détermine les compteurs de performances que vous pouvez suivre. Pour plus d’informations sur la syntaxe, consultez [Spécifier le chemin d’un compteur](https://msdn.microsoft.com/library/windows/desktop/aa373193.aspx).

### Journaux d’infrastructure

Si vous voulez capturer des journaux d’infrastructure contenant des informations sur l’infrastructure de diagnostic Azure, ainsi que sur les modules RemoteAccess et RemoteForwarder, sélectionnez la case à cocher **Activer le transfert des journaux d’infrastructure**. Vous pouvez augmenter ou réduire le nombre de minutes pendant lesquelles les journaux sont transférés vers votre compte de stockage en modifiant la valeur du champ Période de transfert (min).

  ![Journaux d’infrastructure de diagnostics](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758148.png)

  Pour plus d’informations, consultez [Recueillir des données de journaux à l’aide des diagnostics Azure](https://msdn.microsoft.com/library/azure/gg433048.aspx).

### Répertoires de journaux

Si vous voulez capturer des répertoires de journaux contenant des données collectées à partir de répertoires de journaux relatifs à des demandes IIS, à des demandes ayant échoué ou à des dossiers que vous choisissez, sélectionnez la case à cocher **Activer le transfert des répertoires de journaux**. Vous pouvez augmenter ou réduire le nombre de minutes pendant lesquelles les journaux sont transférés vers votre compte de stockage en modifiant la valeur du champ **Période de transfert (min)**.

Vous pouvez cocher les cases correspondant aux journaux à collecter, par exemple, **Journaux IIS** et **Journaux des demandes ayant échoué**. Des noms de conteneurs de stockage par défaut sont fournis, que vous pouvez modifier.

Vous pouvez également capturer des journaux de tout dossier. Spécifiez simplement le chemin d’accès dans la section **Journal du répertoire absolu**, puis cliquez sur le bouton **Ajouter le répertoire**. Les journaux seront transférés vers les conteneurs spécifiés.

  ![Répertoires de journaux](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796665.png)

### Journaux de suivi des événements ETW

Si vous utilisez la fonction [Suivi d’événements pour Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803(v=vs.85).aspx) (ETW) et souhaitez capturer les journaux ETW, sélectionnez la case à cocher **Activer le transfert des journaux ETW**. Vous pouvez augmenter ou réduire le nombre de minutes pendant lesquelles les journaux sont transférés vers votre compte de stockage en modifiant la valeur du champ **Période de transfert (min)**.

Les événements sont capturés à partir de sources d’événements et de fichiers manifestes d’événements que vous spécifiez. Pour spécifier une source d’événements, entrez un nom dans la section **Sources d’événements**, puis cliquez sur le bouton **Ajouter une source d’événements**. De même, vous pouvez spécifier un manifeste d’événements dans la section **Manifestes d’événements**, puis cliquer sur le bouton **Ajouter un manifeste d’événements**.

  ![Journaux de suivi des événements ETW](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766025.png)

  Dans ASP.NET, l’infrastructure ETW est prise en charge via des classes dans l’espace de noms [System.Diagnostics.aspx](https://msdn.microsoft.com/library/system.diagnostics(v=vs.110). L’espace de noms Microsoft.WindowsAzure.Diagnostics, qui hérite des classes [System.Diagnostics.aspx](https://msdn.microsoft.com/library/system.diagnostics(v=vs.110) standard et les étend, permet d’utiliser [System.Diagnostics.aspx](https://msdn.microsoft.com/library/system.diagnostics(v=vs.110) comme infrastructure de journalisation dans l’environnement Azure. Pour plus d’informations, consultez [Contrôler la journalisation et le suivi dans Microsoft Azure](https://msdn.microsoft.com/magazine/ff714589.aspx) et [Activation de Diagnostics dans les services cloud et les machines virtuelles Azure](./cloud-services/cloud-services-dotnet-diagnostics.md).

### Vidages sur incident

Si vous voulez capturer des informations sur le moment où se produit un incident d’instance de rôle, sélectionnez la case à cocher **Activer le transfert des vidages sur incident**. (ASP.NET gérant la plupart des exceptions, cela n’est généralement utile que pour les rôles de travail.) Vous pouvez augmenter ou réduire le pourcentage d’espace de stockage dédié aux vidages sur incident en modifiant la valeur **Quota de répertoire (%)**. Vous pouvez modifier le conteneur de stockage dans lequel les vidages sur incident sont stockés et choisir de capturer un vidage **Complet** ou **Mini**.

Les processus actuellement suivis sont répertoriés. Activez les cases à cocher correspondant aux processus que vous voulez capturer. Pour ajouter un processus à la liste, entrez le nom du processus, puis cliquez sur le bouton **Ajouter le processus**.

  ![Vidages sur incident](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766026.png)

  Pour plus d’informations, consultez [Contrôler la journalisation et le suivi dans Microsoft Azure](https://msdn.microsoft.com/magazine/ff714589.aspx) et [Diagnostics Microsoft Azure, partie 4 : composants de journalisation personnalisés et modifications des diagnostics 1.3 Azure](http://justazure.com/microsoft-azure-diagnostics-part-4-custom-logging-components-azure-diagnostics-1-3-changes/).

## Affichage des données de diagnostic

Après avoir collecté les données de diagnostic pour un service cloud ou une machine virtuelle, vous pouvez les afficher.

### Pour afficher les données de diagnostic d’un service cloud

1. Déployez votre service cloud comme d’habitude, puis exécutez-le.

1. Vous pouvez visualiser les données de diagnostic dans un rapport généré par Visual Studio ou dans des tables de votre compte de stockage. Pour afficher les données d’un rapport, ouvrez **Cloud Explorer** ou l’**Explorateur de serveurs**, ouvrez le menu contextuel du nœud pour le rôle qui vous intéresse, puis cliquez sur **Afficher les données de diagnostic**.

    ![Afficher les données de diagnostic](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC748912.png)

    Un rapport contenant les données disponibles s’affiche.

    ![Rapport de diagnostics Microsoft Azure dans Visual Studio](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796666.png)

    Si les données les plus récentes n’apparaissent pas, vous devez peut-être attendre la fin de la période de transfert.

    Sélectionnez le lien **Actualiser** pour mettre immédiatement à jour les données, ou choisissez un intervalle dans la liste déroulante **Actualisation automatique** pour que les données soient mises à jour automatiquement. Pour exporter les données d’erreur, cliquez sur le bouton **Exporter au format CSV** pour créer un fichier de valeurs séparées par des virgules que vous pouvez ouvrir dans une feuille de calcul.

    Dans **Cloud Explorer** ou l’**Explorateur de serveurs**, ouvrez le compte de stockage associé au déploiement.

1. Ouvrez les tables de diagnostics dans la visionneuse de tables, puis étudiez les données que vous avez collectées. Pour les journaux IIS et les journaux personnalisés, vous pouvez ouvrir un conteneur d’objets blob. Trouvez la table ou le conteneur d’objets blob qui contient les données qui vous intéressent en étudiant la table qui suit. Outre les données pour ce fichier journal, les entrées de la table contiennent EventTickCount, DeploymentId, Role et RoleInstance pour vous aider à identifier la machine virtuelle et le rôle qui ont généré les données et à quel moment.

    |Données de diagnostic|Description|Lieu|
    |---|---|---|
    |Journaux d’application|Les journaux générés par votre code au moyen de méthodes d’appel de la classe System.Diagnostics.Trace.|WADLogsTable|
    |Journaux d’événements|Ces données proviennent des journaux d’événements Windows sur les machines virtuelles. Windows stocke les informations dans ces journaux mais les applications et les services les utilisent également pour signaler des erreurs ou enregistrer des informations.|WADWindowsEventLogsTable|
    |Compteurs de performance|Vous pouvez collecter des données sur n’importe quel compteur de performance disponible sur la machine virtuelle. Le système d’exploitation propose des compteurs de performances qui comprennent de nombreuses statistiques, comme l’utilisation de la mémoire et le temps processeur.|WADPerformanceCountersTable|
    |Journaux d’infrastructure|Ces journaux sont générés à partir de l’infrastructure de diagnostics elle-même.|WADDiagnosticInfrastructureLogsTable|
    |Journaux IIS|Ces journaux enregistrent les requêtes web. Si votre service cloud obtient une quantité importante de trafic, ces journaux peuvent être assez longs. Il est donc conseillé de collecter et de stocker ces données uniquement en cas de besoin.|Vous trouverez des journaux de requêtes ayant échoué dans le conteneur d’objets blob sous wad-iis-failedreqlogs, sous un chemin pour ce déploiement, ce rôle et cette instance. Vous trouverez les journaux complets sous wad-iis-logfiles. Les entrées pour chaque fichier se font dans la table WADDirectories.|
    |Vidages sur incident|Ces informations fournissent des images binaires du processus de votre service cloud (généralement, un rôle de travail).|Conteneur d’objets blob wad-crush-dumps|
    |Fichiers journaux personnalisés|Journaux de données que vous avez prédéfinis.|Spécifiez dans le code l’emplacement des fichiers journaux personnalisés dans votre compte de stockage. Par exemple, vous pouvez spécifier un conteneur d’objets blob personnalisé.|

1. Si des données d’un type quelconque sont tronquées, vous pouvez tenter d’augmenter la mémoire tampon pour ce type de données ou de raccourcir l’intervalle entre les transferts de données de la machine virtuelle à votre compte de stockage.

1. (Facultatif) Purgez de temps en temps les données du compte de stockage afin de réduire les coûts de stockage généraux.

1. Lorsque vous effectuez un déploiement complet, le fichier diagnostics.cscfg (.wadcfgx pour le Kit de développement logiciel (SDK) Azure 2.5) est mis à jour dans Azure et votre service cloud sélectionne toutes les modifications apportées à votre configuration de diagnostics. Si, à la place, vous mettez à jour un déploiement existant, le fichier .cscfg n’est pas mis à jour dans Azure. Vous pouvez tout de même modifier les paramètres de diagnostics en suivant les étapes décrites dans la section qui suit. Pour plus d’informations sur l’exécution d’un déploiement complet et la mise à jour d’un déploiement existant, consultez [Assistant Publication d’application Azure](vs-azure-tools-publish-azure-application-wizard.md).

### Pour afficher les données de diagnostic d’une machine virtuelle

1. Dans le menu contextuel de la machine virtuelle, sélectionnez **Afficher les données de diagnostic**.

    ![Afficher les données de diagnostic dans la machine virtuelle Azure](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766027.png)

    Cela a pour effet d’ouvrir la fenêtre **Résumé des diagnostics**.

    ![Résumé des diagnostics de la machine virtuelle Azure](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796667.png)

    Si les données les plus récentes n’apparaissent pas, vous devez peut-être attendre la fin de la période de transfert.

    Sélectionnez le lien **Actualiser** pour mettre immédiatement à jour les données, ou choisissez un intervalle dans la liste déroulante **Actualisation automatique** pour que les données soient mises à jour automatiquement. Pour exporter les données d’erreur, cliquez sur le bouton **Exporter au format CSV** pour créer un fichier de valeurs séparées par des virgules que vous pouvez ouvrir dans une feuille de calcul.

## Configuration des diagnostics de service cloud après déploiement

Si vous recherchez des informations sur un problème dans un service cloud en cours d’exécution, vous pouvez collecter des données que vous n’aviez pas spécifiées avant le déploiement initial du rôle. Dans ce cas, vous pouvez commencer à collecter ces données au moyen des paramètres de l’Explorateur de serveurs. Configurez des diagnostics pour une seule instance ou pour toutes les instances d’un rôle, selon que vous ouvrez la boîte de dialogue Configuration des diagnostics depuis le menu contextuel de l’instance ou du rôle. Si vous configurez le nœud de rôle, les changements s’appliquent à toutes les instances. Si vous configurez le nœud d’instance, les changements s’appliquent uniquement à cette instance.

### Pour configurer les diagnostics pour un service cloud en cours d’exécution

1. Dans l’Explorateur de serveurs, développez le nœud **Cloud Services**, puis développez les nœuds permettant de situer le rôle ou l’instance, ou les deux, que vous recherchez.

    ![Configuration des diagnostics](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC748913.png)

1. Dans le menu contextuel d’un nœud d’instance ou d’un nœud de rôle, choisissez **Mettre à jour les paramètres de diagnostics**, puis choisissez les paramètres de diagnostics que vous souhaitez collecter.

    Pour plus d’informations sur les paramètres de configuration, consultez **Configuration des sources de données de diagnostic** dans cette rubrique. Pour plus d’informations sur la façon d’afficher les données de diagnostic, consultez **Affichage des données de diagnostic** dans cette rubrique.

    Si vous modifiez la collecte de données dans l’**Explorateur de serveurs**, ces modifications continuent à s’appliquer jusqu’au redéploiement complet de votre service cloud. Si vous utilisez les paramètres de publication par défaut, les changements ne sont pas remplacés car le paramètre par défaut indique de mettre à jour le déploiement existant, au lieu d’effectuer un redéploiement complet. Pour vous assurer que les paramètres sont effacés au moment du déploiement, accédez à l’onglet **Paramètres avancés** dans l’Assistant Publication, puis désactivez la case à cocher **Mise à jour du déploiement**. Lorsque vous redéployez après avoir désactivé cette case à cocher, les paramètres reviennent à ceux du fichier .wadcfgx (ou .wadcfg) tels que définis dans l’éditeur Propriétés pour le rôle. Si vous mettez à jour votre déploiement, Azure conserve les anciens paramètres.

## Dépannage de problèmes de service cloud Azure

Si vous rencontrez des problèmes avec vos projets de service cloud, par exemple, un rôle qui reste bloqué à l’état « occupé », qui exécute un cycle en boucle ou qui lève une erreur interne du serveur, il existe des outils et techniques permettant de diagnostiquer et de corriger ces problèmes. Pour des exemples spécifiques de problèmes et solutions courants, ainsi qu’une vue d’ensemble des concepts et outils utilisés pour diagnostiquer et corriger de telles erreurs, consultez [Données de diagnostic de calcul PaaS Azure](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

## Questions et réponses

**Qu’est-ce que la taille de la mémoire tampon et quelle doit-elle être ?**

Sur chaque instance de machine virtuelle, des quotas limitent le nombre de données de diagnostic qu’il est possible de stocker sur le système de fichiers local. De plus, vous pouvez spécifier une taille de mémoire tampon pour chaque type de données de diagnostic disponible. Cette taille de mémoire tampon joue le rôle de quota individuel pour ce type de données. En consultant le bas de la boîte de dialogue, déterminez le quota général et la quantité de mémoire restante. En spécifiant des mémoires tampons plus grandes ou des types de données plus nombreux, vous vous approchez du quota général. Vous pouvez modifier le quota global en modifiant le fichier de configuration diagnostics.wadcfg/.wadcfgx. Les données de diagnostic sont stockées sur le même système de fichiers que les données de votre application. Par conséquent, si votre application utilise beaucoup d’espace disque, évitez d’augmenter le quota de diagnostic général.

**Qu’est-ce que la période de transfert et quelle doit être sa durée ?**

La période de transfert est le temps écoulé entre les captures de données. Après chaque période de transfert, les données sont déplacées du système de fichiers local d’une machine virtuelle aux tables de votre compte de stockage. Si la quantité de données collectées dépasse le quota avant la fin d’une période de transfert, les données plus anciennes sont écartées. Vous pouvez réduire la période de transfert si vous perdez des données parce que vos données dépassent la taille de la mémoire tampon ou le quota général.

**Dans quel fuseau horaire se trouvent les horodatages ?**

Les horodatages sont dans le fuseau horaire local du centre de données qui héberge votre service cloud. Les trois colonnes d’horodateur suivantes dans les tables de journal sont utilisées.

  - **PreciseTimeStamp** est l’horodatage ETW de l’événement. Autrement dit, l’heure de l’événement est enregistrée à partir du client.

  - **TIMESTAMP** est la valeur PreciseTimeStamp arrondie à la limite de fréquence de chargement. Par conséquent, si votre fréquence de téléchargement est de 5 minutes et si l’heure de l’événement est 00:17:12, TIMESTAMP est 00:15:00.

  - **Timestamp** est l’horodateur de création de l’entité dans la table Azure.

**Comment gérer les coûts pendant la collecte d’informations de diagnostics ?**

Les paramètres par défaut (**Niveau de journal** sur **Erreur** et **Période de transfert** sur **1 minute**) sont conçus pour minimiser les coûts. Vos coûts de calcul augmentent lorsque vous collectez plus de données de diagnostic ou diminuez la période de transfert. Ne collectez pas plus de données que nécessaire et n’oubliez pas de désactiver la collecte de données quand vous n’en avez plus besoin. Vous pouvez toujours la réactiver, même en cours d’exécution, comme indiqué dans la section précédente.

**Comment recueillir des journaux de demandes IIS ayant échoué ?**

Par défaut, IIS ne collecte pas les journaux de demandes ayant échoué. Vous pouvez configurer IIS pour les collecter en modifiant le fichier web.config pour votre rôle web.

**Je n’obtiens pas les informations des méthodes RoleEntryPoint comme OnStart. Que se passe-t-il ?**

Les méthodes de RoleEntryPoint sont appelées dans le contexte de WAIISHost.exe, non d’IIS. Par conséquent, les informations de configuration dans web.config qui autorisent le traçage ne s’appliquent pas. Pour résoudre ce problème, ajoutez un fichier .config à votre projet de rôle web et nommez le fichier de manière à ce qu’il corresponde à l’assembly de sortie qui contient le code RoleEntryPoint. Dans le projet de rôle web par défaut, le nom du fichier .config serait WAIISHost.exe.config. Ajoutez ensuite les lignes suivantes à ce fichier :

```
<system.diagnostics>
  <trace>
      <listeners>
          <add name “AzureDiagnostics” type=”Microsoft.WindowsAzure.Diagnostics.DiagnosticMonitorTraceListener”>
              <filter type=”” />
          </add>
      </listeners>
  </trace>
</system.diagnostics>
```

Maintenant, dans la fenêtre **Propriétés**, définissez la propriété **Copier dans le répertoire de sortie** sur **Toujours copier**.

## Étapes suivantes

Pour en savoir plus sur la journalisation des diagnostics dans Azure, consultez [Activation de Diagnostics dans les services cloud et les machines virtuelles Azure](./cloud-services/cloud-services-dotnet-diagnostics.md) et [Activer la journalisation des diagnostics pour les applications web dans Azure App Service](./app-service-web/web-sites-enable-diagnostic-log.md).

<!---HONumber=AcomDC_0817_2016-->