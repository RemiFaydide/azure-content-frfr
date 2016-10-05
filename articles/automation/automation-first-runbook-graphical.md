<properties
    pageTitle="Mon premier Runbook graphique dans Azure Automation | Microsoft Azure"
    description="Ce didacticiel vous familiarise avec la création, le test et la publication d'un Runbook graphique simple."
    services="automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor=""
	keywords="runbook, modèle de runbook, automatisation des runbooks, runbook azure"/>
<tags
    ms.service="automation"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/06/2016"
    ms.author="magoedte;bwren"/>

# Mon premier Runbook graphique

> [AZURE.SELECTOR] - [Graphical](automation-first-runbook-graphical.md) - [PowerShell](automation-first-runbook-textual-PowerShell.md) - [PowerShell Workflow](automation-first-runbook-textual.md)

Ce didacticiel vous familiarise avec la création d’un [Runbook graphique](automation-runbook-types.md#graphical-runbooks) dans Azure Automation. Nous commencerons par un simple Runbook que nous testerons et publierons tout en expliquant comment suivre l'état de la tâche du Runbook. Puis nous modifierons le Runbook pour gérer les ressources Azure, en démarrant dans ce cas une machine virtuelle Azure. Puis nous renforcerons le Runbook en ajoutant des paramètres de Runbook et des liens conditionnels.

## Composants requis

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

-	Abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez [activer vos avantages abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou <a href="/pricing/free-account/" target="_blank">[créer un compte gratuit](https://azure.microsoft.com/free/).
-	Un [compte d’identification Azure](automation-sec-configure-azure-runas-account.md) pour le stockage du Runbook et l’authentification auprès des ressources Azure. Ce compte doit avoir l’autorisation de démarrer et d’arrêter la machine virtuelle.
-	Une machine virtuelle Azure. Nous arrêterons et démarrerons cet ordinateur afin qu'il ne soit pas en production.


## Étape 1 - Création d’un Runbook

Nous allons commencer par créer un Runbook simple qui renvoie le texte *Hello World*.

1.	Dans le portail Azure, ouvrez votre compte Automation. La page du compte Automation vous offre un aperçu rapide des ressources de ce compte. Vous devriez déjà posséder certains éléments multimédias. La plupart de ces éléments représentent les modules automatiquement inclus dans un nouveau compte Automation. Vous devriez également disposer de la ressource d’informations d’identification mentionnée dans les [composants requis](#prerequisites).
2.	Cliquez sur la vignette **Runbooks** pour ouvrir la liste des Runbooks.<br> ![Contrôle des Runbooks](media/automation-first-runbook-graphical/runbooks-control.png)
3.	Créez un Runbook en cliquant sur le bouton **Ajouter un Runbook** puis**Créer un Runbook**.
4.	Nommez le Runbook *MyFirstRunbook-Graphical*.
5.	Dans ce cas précis, nous allons créer un [Runbook graphique](automation-graphical-authoring-intro.md) ; vous devez donc sélectionner **Graphique** dans le champ **Type de Runbook**.<br> ![Nouveau Runbook](media/automation-first-runbook-graphical/create-new-runbook.png)<br>
6.	Cliquez sur **Créer** pour créer le Runbook et ouvrez l’éditeur graphique.

## Étape 2 : Ajout d’activités au Runbook

Le contrôle Bibliothèque à gauche de l’éditeur vous permet de sélectionner des activités à ajouter à votre Runbook. Nous allons ajouter une applet de commande **Write-Output** pour extraire du texte du Runbook.

1.	Dans le contrôle Bibliothèque, cliquez dans la zone de recherche et tapez **Write-Output**. Les résultats de recherche seront affichés ci-dessous. <br> ![Microsoft.PowerShell.Utility](media/automation-first-runbook-graphical/search-powershell-cmdlet-writeoutput.png)
2.	Faites défiler jusqu'au bas de la liste. Vous pouvez cliquer avec le bouton droit sur **Write-Output** et sélectionner **Ajouter au canevas** ou cliquer sur les points de suspension en regard de l’applet de commande, puis sélectionner **Ajouter au canevas**.
3.	Cliquez sur l’activité **Write-Output** sur le canevas. Le panneau du contrôle Configuration qui vous permet de configurer l’activité s’ouvre.
4.	Par défaut, le champ **Étiquette** affiche le nom de l’applet de commande, mais nous pouvons le modifier pour le rendre plus convivial. Remplacez-le par *Write Hello World to output*.
5.	Cliquez sur **Paramètres** pour renseigner les paramètres de l’applet de commande. Certaines applets de commande comportent plusieurs jeux de paramètres, et vous devez sélectionner celle que vous utiliserez. Dans ce cas précis, **Write-Output** ne comporte qu’un seul paramètre défini ; vous n’avez donc pas besoin d’en sélectionner un. <br> ![Propriétés Write-Output](media/automation-first-runbook-graphical/write-output-properties-b.png)
6.	Sélectionnez le paramètre **InputObject**. Il s'agit du paramètre où nous spécifierons le texte à envoyer au flux de sortie.
7.	Dans le menu déroulant **Source de données**, sélectionnez **Expression PowerShell**. Le menu déroulant **Source de données** fournit différentes sources que vous utilisez pour renseigner un paramètre. Vous pouvez utiliser la sortie de ces sources, par exemple une autre activité, un élément multimédia Automation ou une expression PowerShell. Dans notre cas, nous voulons simplement extraire le texte *Hello World*. Nous pouvons utiliser une expression PowerShell et spécifiez une chaîne.
8.	Dans le champ **Expression**, tapez *« Hello World »*, puis cliquez deux fois sur **OK** pour revenir au canevas.<br> ![Expression PowerShell](media/automation-first-runbook-graphical/expression-hello-world.png)
9.	Enregistrez le Runbook en cliquant sur **Enregistrer**.<br> ![Enregistrer un Runbook](media/automation-first-runbook-graphical/runbook-toolbar-save-revised20165.png)

## Étape 3 : Test du Runbook

Avant publier le Runbook pour le rendre disponible en production, nous voulons le tester pour s’assurer qu'il fonctionne correctement. Lorsque vous testez un Runbook, vous exécutez sa version **Brouillon** et affichez sa sortie de manière interactive.

1.	Cliquez sur **Panneau de test** pour ouvrir le panneau Test.<br> ![Volet de test](media/automation-first-runbook-graphical/runbook-toolbar-test-revised20165.png)
2.	Cliquez sur **Démarrer** pour démarrer le test. Ce doit être la seule option activée.
3.	Une [tâche de Runbook](automation-runbook-execution.md) est créée et son état apparaît dans le volet. L’état initial de la tâche est *Mis en file d’attente* pour indiquer que la tâche attend qu’un Runbook Worker du cloud devienne disponible. La tâche prend ensuite l’état *Démarrage en cours* lorsqu’un Worker sélectionne la tâche, puis l’état *En cours d’exécution* lorsque le Runbook commence à s’exécuter.
4.	Lorsque la tâche du Runbook est terminée, sa sortie s'affiche. Dans notre cas, nous devrions voir *Hello World*.<br> ![Hello World](media/automation-first-runbook-graphical/runbook-test-results.png)
5.	Fermez le panneau Test pour revenir au canevas.

## Étape 4 : Publication et démarrage du Runbook

Le Runbook que nous venons de créer est toujours en mode brouillon. Nous devons le publier pour pouvoir l'exécuter en production. Lorsque vous publiez un Runbook, vous écrasez la version publiée existante par la version brouillon. Dans notre cas, nous n'avons pas encore de version publiée car nous venons de créer le Runbook.

1.	Cliquez sur **Publier** pour publier le Runbook, puis sur **Oui** lorsque vous y êtes invité.<br> ![Publish](media/automation-first-runbook-graphical/runbook-toolbar-publish-revised20166.png)
2.	Si vous faites défiler la page vers la gauche pour visualiser le panneau **Runbooks**, celui-ci affichera l’**État de création** **Publié**.
3.	Faites défiler la page vers la droite pour visualiser le panneau **MyFirstRunbook**. Les options de la partie supérieure nous permettent de démarrer le Runbook, de planifier son démarrage à un moment ultérieur ou de créer un [Webhook](automation-webhooks.md) afin de le démarrer par le biais d’un appel HTTP.
4.	Étant donné que nous voulons simplement démarrer le Runbook, cliquez sur **Démarrer**, puis sur **Oui** lorsque vous y êtes invité.<br> ![Démarrer un Runbook](media/automation-first-runbook-graphical/runbook-controls-start-revised20165.png)
5.	Un panneau de tâche est ouvert pour la tâche du Runbook que nous venons de créer. Nous pouvons fermer ce panneau mais, dans ce cas, nous le laisserons ouvert afin de suivre la progression de la tâche.
6.	L’état de la tâche est indiqué dans le champ **Résumé de la tâche** et correspond aux états que nous avons constatés lors du test du Runbook.<br> ![Résumé des tâches](media/automation-first-runbook-graphical/runbook-job-summary.png)
7.	Lorsque le Runbook prend l’état *Terminé*, cliquez sur **Sortie**. Le panneau **Sortie** s’ouvre, affichant *Hello World*.<br> ![Résumé des tâches](media/automation-first-runbook-graphical/runbook-job-output.png)
8.	Fermez le panneau Sortie.
9.	Cliquez sur **Tous les journaux** pour ouvrir le panneau Flux de la tâche du Runbook. Seul le message *Hello World* devrait apparaître dans le flux de sortie, mais d’autres flux peuvent s’afficher pour une tâche de runbook, notamment Mode détaillé et Erreur si le runbook consigne ces informations.<br> ![Résumé des tâches](media/automation-first-runbook-graphical/runbook-job-AllLogs.png)
10.	Fermez le panneau Tous les journaux et le panneau Tâche pour revenir au panneau MyFirstRunbook.
11.	Cliquez sur **Tâches** pour ouvrir le panneau Tâches de ce Runbook. Il répertorie toutes les tâches créées par ce Runbook. Une seule tâche doit apparaître, car nous n’avons exécuté la tâche qu’une seule fois.<br> ![Travaux](media/automation-first-runbook-graphical/runbook-control-jobs.png)
12.	Vous pouvez cliquer sur cette tâche pour ouvrir le volet de la tâche que nous avons consulté au démarrage du Runbook. Cela vous permet de revenir en arrière et d’afficher les détails de toute tâche créée pour un Runbook donné.

## Étape 5 - Créer des ressources de variables

Nous avons testé et publié notre Runbook, mais jusqu'à présent, il ne fait rien d'utile. Nous voulons qu’il gère les ressources Azure. Avant de configurer le Runbook pour l’authentification, nous allons créer une variable qui contiendra l’ID d’abonnement. Nous la référencerons après avoir configuré l’authentification à l’étape 6 ci-dessous. L’ajout d’une référence au contexte de l’abonnement vous permet de gérer facilement plusieurs abonnements. Avant de poursuivre, copiez votre ID d’abonnement depuis l’option Abonnements du panneau de navigation.

1. Dans le panneau Comptes Automation, cliquez sur la vignette **Ressources**. Le panneau **Ressources** s’ouvre.
2. Dans le panneau Ressources, cliquez sur la vignette **Variables**.
3. Dans le panneau Variables, cliquez sur **Ajouter une variable**.<br>![Variable Automation](media/automation-first-runbook-graphical/create-new-subscriptionid-variable.png)
4. Dans la zone **Nom** du panneau Nouvelle variable, entrez **AzureSubscriptionId**, puis dans la zone **Valeur**, entrez votre ID d’abonnement. Conservez la sélection *chaîne* pour **Type** et la valeur par défaut pour le **chiffrement**.
5. Cliquez sur **Créer** pour créer la variable.


## Étape 6 - Ajouter une authentification pour gérer les ressources Azure

Maintenant que nous disposons d’une variable pour contenir l’ID d’abonnement, nous pouvons configurer le Runbook pour l’authentification à l’aide des informations d’identification mentionnées dans les [conditions préalables](#prerequisites). Il suffit pour cela d’ajouter au canevas la **Ressource** de connexion Azure et l’applet de commande **Add-AzureRMAccount**.

1.	Ouvrez l’éditeur graphique en cliquant sur **Modifier** dans le panneau MyFirstRunbook.<br> ![Modifier un Runbook](media/automation-first-runbook-graphical/runbook-controls-edit-revised20165.png)
2.	Étant donné que **Write Hello World to output** ne nous est plus utile, cliquez dessus avec le bouton droit et sélectionnez **Supprimer**.
3.	Dans la bibliothèque, développez **Connexions** et ajoutez **AzureRunAsConnection** au canevas en sélectionnant **Ajouter au canevas**.
4.	Dans le canevas, sélectionnez **AzureRunAsConnection**, puis dans le panneau de contrôle Configuration, tapez **Get Run As Connection** (Obtenir une connexion d’identification) dans la zone de texte **Étiquette**. Il s’agit de la connexion.
5.	Dans la commande Bibliothèque, tapez **Add-AzureRmAccount** dans la zone de recherche.
6.	Ajoutez **Add-AzureRmAccount** au canevas.<br> ![Add-AzureRMAccount](media/automation-first-runbook-graphical/search-powershell-cmdlet-addazurermaccount.png)
7.	Pointez sur **Get Run As Connection** (Obtenir une connexion d’identification) jusqu’à ce qu’un cercle apparaisse au bas de la forme. Cliquez sur le cercle et faites glisser la flèche vers **Add-AzureRmAccount**. La flèche que vous venez de créer est un *lien*. Le runbook démarre avec **Get Run As Connection**. Exécutez **Add-AzureRmAccount**.<br> ![Créer un lien entre des activités](media/automation-first-runbook-graphical/runbook-link-auth-activities.png)
8.	Dans le canevas, sélectionnez **Add-AzureRmAccount** et le type de panneau de contrôle Configuration **Login to Azure** (Connexion à Azure) dans la zone de texte **Étiquette**.
9.	Cliquez sur **Paramètres** pour afficher le panneau Configuration des paramètres d’activité.
10.  **Add-AzureRmAccount** comporte plusieurs jeux de paramètres et vous devez donc en sélectionner un pour fournir les valeurs de paramètre. Cliquez sur **Jeu de paramètres**, puis sélectionnez le jeu de paramètres **ServicePrincipalCertificate**.
11.  Une fois que vous sélectionnez le jeu de paramètres, les paramètres apparaissent dans le panneau Configuration des paramètres d’activité. Cliquez sur **APPLICATIONID**.<br> ![Ajouter des paramètres de compte Azure RM](media/automation-first-runbook-graphical/add-azurermaccount-parameterset.png)
12.  Dans le panneau Valeur du paramètre, sélectionnez **Sortie d’activité** pour **Source de données** et sélectionnez **Get Run As Connection** (Obtenir une connexion d’identification) dans la liste. Puis, dans la zone de texte **Chemin du champ**, saisissez **ApplicationId** et cliquez sur **OK**. Nous allons spécifier le nom de la propriété du chemin du champ car l’activité génère un objet contenant plusieurs propriétés.
13.  Cliquez sur **CERTIFICATETHUMBPRINT** puis, dans le panneau Valeur du paramètre, sélectionnez **Sortie de l’activité** comme **Source de données**. Sélectionnez **Get Run As Connection** (Obtenir une connexion d’identification) dans la liste, puis, dans la zone de texte **Chemin du champ**, entrez **CertificateThumbprint** et cliquez sur **OK**.
14.  Cliquez sur **SERVICEPRINCIPALNAME** puis, dans le panneau Valeur du paramètre, sélectionnez **ConstantValue** comme **source de données**, cliquez sur l’option **True**, puis cliquez sur **OK**.
15.  Cliquez sur **TENANTID** puis, dans le panneau Valeur du paramètre, sélectionnez **Sortie de l’activité** comme **Source de données**. Sélectionnez **Get Run As Connection** (Obtenir une connexion d’identification) dans la liste, puis, dans la zone de texte **Chemin du champ**, entrez **TenantId** et cliquez deux fois sur **OK**.
16.  Dans la commande Bibliothèque, tapez **Set-AzureRmContext** dans la zone de recherche.
17.	 Ajoutez **Set-AzureRmContext** au canevas.
18.	 Dans le canevas, sélectionnez **Set-AzureRmContext**, puis dans le panneau de contrôle Configuration, tapez **Specify Subscription Id** (Spécifier un ID d’abonnement) dans la zone de texte **Étiquette**.
19.	 Cliquez sur **Paramètres** pour afficher le panneau Configuration des paramètres d’activité.
20. **Set-AzureRmContext** comporte plusieurs jeux de paramètres et vous devez donc en sélectionner un pour fournir les valeurs de paramètre. Cliquez sur **Jeu de paramètres**, puis sélectionnez le jeu de paramètres **SubscriptionId**.
21.	 Une fois que vous sélectionnez le jeu de paramètres, les paramètres apparaissent dans le panneau Configuration des paramètres d’activité. Cliquez sur **SubscriptionID**.
22.	 Dans le panneau Valeur du paramètre, sélectionnez **Ressource de variable** comme **Source de données** et sélectionnez **AzureSubscriptionId** dans la liste, puis cliquez à deux reprises sur **OK**.
23.  Pointez sur **Login to Azure** (Connexion à Azure) jusqu’à ce qu’un cercle apparaisse au bas de la forme. Cliquez sur le cercle et faites glisser la flèche vers **Specify Subscription Id** (Spécifier un ID d’abonnement).


À ce stade, votre Runbook doit ressembler à ce qui suit : <br>![Configuration de l’authentification de runbook](media/automation-first-runbook-graphical/runbook-auth-config.png)

## Étape 7 - Ajouter une activité pour démarrer une machine virtuelle

Nous allons maintenant ajouter une activité **Start-AzureRmVM** pour démarrer une machine virtuelle. Vous pouvez choisir n’importe quelle machine virtuelle dans votre abonnement Azure, et pour l’instant, nous allons coder ce nom dans l’applet de commande.

1. Dans la commande Bibliothèque, tapez **Start-AzureRm** dans la zone de recherche.
2. Ajoutez **Start-AzureRmVM** au canevas, puis faites-le glisser sous **Specify Subscription Id** (Spécifier un ID d’abonnement).
3. Pointez sur **Specify Subscription Id** (Spécifier un ID d’abonnement) jusqu’à ce qu’un cercle apparaisse au bas de la forme. Cliquez sur le cercle et faites glisser la flèche vers **Start-AzureRmVM**.
4.	Sélectionnez **Start-AzureRmVM**. Cliquez sur **Paramètres**, puis sur **Jeu de paramètres** afin d’afficher les jeux pour **Start-AzureRmVM**. Sélectionnez le jeu de paramètres **ResourceGroupNameParameterSetName**. Notez que des points d’exclamation apparaissent en regard des paramètres **ResourceGroupName** et **Name**. Ils indiquent que ces paramètres sont obligatoires. Notez également que les deux attendent des valeurs de chaîne.
5.	Sélectionnez **Name**. Sélectionnez **Expression PowerShell** comme **Source de données** et tapez entre guillemets doubles le nom de la machine virtuelle que nous allons démarrer avec ce Runbook. Cliquez sur **OK**.<br>![Valeur du paramètre Name Start-AzureRmVM](media/automation-first-runbook-graphical/runbook-startvm-nameparameter.png)
6.	Sélectionnez **ResourceGroupName**. Utilisez **Expression PowerShell** comme **Source de données** et tapez le nom du groupe de ressources entre guillemets doubles. Cliquez sur **OK**.<br> ![Paramètres Start-AzureRmVM](media/automation-first-runbook-graphical/startazurermvm-params.png)
8.	Cliquez sur le volet de test afin de tester le Runbook.
9.	Cliquez sur **Démarrer** pour démarrer le test. Une fois le test terminé, vérifiez que la machine virtuelle a démarré.

À ce stade, votre Runbook doit ressembler à ce qui suit : <br>![Configuration de l’authentification de runbook](media/automation-first-runbook-graphical/runbook-startvm.png)

## Étape 8 - Ajouter des paramètres d’entrée supplémentaires au Runbook

Le Runbook démarre actuellement la machine virtuelle dans le groupe de ressources spécifié dans l’applet de commande **Start-AzureRmVM**, mais il serait plus utile si nous pouvions spécifier les deux valeurs au démarrage. Nous allons à présent ajouter des paramètres d’entrée au Runbook pour fournir cette fonctionnalité.

1. Ouvrez l’éditeur graphique en cliquant sur **Modifier** dans le volet **MyFirstRunbook**.
2. Cliquez sur **Entrée et sortie**, puis sur **Ajouter une entrée** pour ouvrir le volet Paramètre d’entrée de Runbook.<br> ![Entrée et sortie de Runbook](media/automation-first-runbook-graphical/runbook-toolbar-InputandOutput-revised20165.png)
3. Spécifiez *VMName* dans le champ **Nom**. Conservez la sélection *chaîne* pour le champ **Type**, mais définissez la valeur **Obligatoire** sur *Oui*. Cliquez sur **OK**.
4. Créez un second paramètre d’entrée obligatoire appelé *ResourceGroupName*, puis cliquez sur **OK** pour fermer le panneau **Entrée et sortie**.<br> ![Paramètres d'entrée de Runbook](media/automation-first-runbook-graphical/start-azurermvm-params-outputs.png)
5. Sélectionnez l’activité **Start-AzureRmVM**, puis cliquez sur **Paramètres**.
6. Changez la **Source de données** de **Name** en **Entrée de Runbook**, puis sélectionnez **VMName**.<br>
7. Changez la **Source de données** de **ResourceGroupName** en **Entrée de Runbook**, puis sélectionnez **ResourceGroupName**.<br> ![Paramètres Start-AzureVM](media/automation-first-runbook-graphical/start-azurermvm-params-runbookinput.png)
8. Enregistrez le Runbook et ouvrez le volet de test. Notez que vous pouvez désormais fournir des valeurs pour les deux variables d'entrée qui seront utilisées dans le test.
9. Fermez le volet de test.
10.	Cliquez sur **Publier** pour publier la nouvelle version du Runbook.
11.	Arrêtez la machine virtuelle que vous avez démarrée à l'étape précédente.
12.	Cliquez sur **Démarrer** pour démarrer le Runbook. Entrez les valeurs **VMName** et **ResourceGroupName** pour la machine virtuelle que vous allez démarrer.<br> ![Démarrage du runbook](media/automation-first-runbook-graphical/runbook-start-inputparams.png)
13.	Une fois le Runbook terminé, vérifiez que la machine virtuelle a démarré.

## Étape 9 - Création d’un lien conditionnel

Nous allons maintenant modifier le Runbook afin qu’il tente de démarrer la machine virtuelle uniquement si elle n’est pas déjà démarrée. Pour cela, nous allons ajouter au Runbook une applet de commande **Get-AzureRmVM** qui obtiendra l’état du niveau d’instance de la machine virtuelle. Nous allons ensuite ajouter un module de code Workflow PowerShell appelé **Obtenir l’état** à un extrait de code PowerShell pour déterminer si l’état de la machine virtuelle est en cours d’exécution ou arrêté. Un lien conditionnel à partir du module **Obtenir l’état** exécutera **Start-AzureRmVM** seulement si l’état d’exécution en cours est arrêté. Enfin, nous allons générer un message vous informant si la machine virtuelle a été démarrée avec succès ou non à l’aide de l’applet de commande PowerShell Write-Output.

1. Ouvrez **MyFirstRunbook** dans l’éditeur graphique.
2. Supprimez le lien entre **Specify Subscription Id** (Spécifier un ID d’abonnement) et **Start-AzureRmVM** en cliquant dessus, puis en appuyant sur la touche *Supprimer*.
3. Dans la commande Bibliothèque, tapez **Get-AzureRm** dans la zone de recherche.
4. Ajoutez **Get-AzureRmVM** au canevas.
5. Sélectionnez **Get-AzureRmVM** puis **Jeu de paramètres** afin d’afficher les jeux pour **Get-AzureRmVM**. Sélectionnez le jeu de paramètres **GetVirtualMachineInResourceGroupNameParamSet**. Notez que des points d’exclamation apparaissent en regard des paramètres **ResourceGroupName** et **Name**. Ils indiquent que ces paramètres sont obligatoires. Notez également que les deux attendent des valeurs de chaîne.
6. Pour le paramètre **Name** sous **Source de données**, sélectionnez **Entrée du Runbook** puis **VMName**. Cliquez sur **OK**.
7. Pour le paramètre **ResourceGroupName** sous **Source de données**, sélectionnez **Entrée de Runbook**, puis **ResourceGroupName**. Cliquez sur **OK**.
8. Pour le paramètre **Status** sous **Source de données**, sélectionnez **Valeur constante** puis cliquez sur **True**. Cliquez sur **OK**.
9. Créez un lien entre **Specify Subscription Id** (Spécifier un ID d’abonnement) et **Get-AzureRmVM**.
10. Dans la commande Bibliothèque, développez **Contrôle de Runbook** et ajoutez **Code** au canevas.
11. Créez un lien entre **Get-AzureRmVM** et **Code**.
12. Cliquez sur **Code** et dans le panneau Configuration, changez l’étiquette en **Obtenir l’état**.
13. Sélectionnez le paramètre **Code** pour afficher le panneau **Éditeur de code**.
14. Dans l’éditeur de code, collez l’extrait de code suivant :

     ```
     $StatusesJson = $ActivityOutput['Get-AzureRmVM'].StatusesText 
     $Statuses = ConvertFrom-Json $StatusesJson 
     $StatusOut ="" 
     foreach ($Status in $Statuses){ 
     if($Status.Code -eq "Powerstate/running"){$StatusOut = "running"} 
     elseif ($Status.Code -eq "Powerstate/deallocated") {$StatusOut = "stopped"} 
     } 
     $StatusOut 
     ```

15. Créez un lien entre **Obtenir l’état** et **Start-AzureRmVM**.<br> ![Runbook avec module de code](media/automation-first-runbook-graphical/runbook-startvm-get-status.png)
16. Sélectionnez le lien puis dans le panneau Configuration, changez **Appliquer la condition** en **Oui**. Notez que le lien se transforme en une ligne pointillée indiquant que l’activité cible s’exécutera uniquement si la condition produit la valeur true.
17. Dans la zone **Expression de condition**, tapez *$ActivityOutput[’Get Status’] -eq "Stopped"*. **Start-AzureRmVM** s’exécutera uniquement si la machine virtuelle est arrêtée.
18.	Dans le contrôle Bibliothèque, développez **Applets de commande**, puis **Microsoft.PowerShell.Utility**.
19.	Ajoutez **Write-Output** au canevas à deux reprises.<br> ![Runbook avec Write-Output](media/automation-first-runbook-graphical/runbook-startazurermvm-complete.png)
20. Sur le premier contrôle **Write-Output**, cliquez sur **Paramètres** et changez la valeur de **Étiquette** en *Notify VM Started*.
21. Pour **InputObject**, définissez le champ **Source de données** sur **Expression PowerShell**, puis tapez l’expression *"$VMName démarrée."*.
22. Sur le second contrôle **Write-Output**, cliquez sur **Paramètres** et changez la valeur de **Étiquette** en *Notify VM Start Failed*
23. Pour **InputObject**, changez **Source de données** en **Expression PowerShell** puis tapez l’expression *"$VMName n’a pas pu démarrer."*.
24. Créez un lien entre **Start-AzureRmVM** et **Notify VM Started** ainsi que **Notify VM Start Failed**.
25. Sélectionnez le lien vers **Notify VM Started** et changez **Appliquer la condition** en **True**.
26. Dans la zone **Expression de condition**, tapez *$ActivityOutput[’Start-AzureRmVM’].IsSuccessStatusCode -eq $true*. Ce contrôle Write-Output s’exécutera désormais uniquement si la machine virtuelle a démarré correctement.
27. Sélectionnez le lien vers **Notify VM Started** et changez **Appliquer la condition** en **True**.
28. Dans la zone **Expression de condition**, tapez *$ActivityOutput[’Start-AzureRmVM’].IsSuccessStatusCode -ne $true*. Ce contrôle Write-Output s’exécutera désormais uniquement si la machine virtuelle a démarré correctement.
29.	Enregistrez le Runbook et ouvrez le volet de test.
30.	Démarrez le Runbook avec la machine virtuelle arrêtée, et il devrait démarrer.

## Étapes suivantes

-	Pour en savoir plus sur la création graphique, consultez [Création de graphiques dans Azure Automation](automation-graphical-authoring-intro.md).
-	Pour une prise en main des Runbooks PowerShell, consultez [Mon premier Runbook PowerShell](automation-first-runbook-textual-powershell.md)
-	Pour une prise en main des Runbooks de workflow PowerShell, consultez [Mon premier Runbook PowerShell Workflow](automation-first-runbook-textual.md)

<!---HONumber=AcomDC_0928_2016-->