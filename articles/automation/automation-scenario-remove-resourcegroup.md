<properties
    pageTitle="Automatiser la suppression des groupes de ressources | Microsoft Azure"
    description="Version du workflow PowerShell d’un scénario d’Azure Automation incluant des runbooks pour supprimer tous les groupes de ressources de votre abonnement."
    services="automation"
    documentationCenter=""
    authors="MGoedtel"
    manager="jwhit"
    editor=""
	/>
<tags
    ms.service="automation"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/26/2016"
    ms.author="magoedte"/>

# Scénario Azure Automation - Automatiser la suppression de groupes de ressources

De nombreux clients créent plusieurs groupes de ressources, certains étant dédiés à la gestion des applications de production et d’autres pouvant être des environnements de développement, de test et intermédiaires. Automatiser le déploiement de ces ressources est une chose, mais être capable de désactiver un groupe de ressources d’un simple clic en est une autre. L’utilisation d’Automation pour gérer cette opération constitue un cas d’utilisation idéal et l’occasion de rationaliser une tâche de gestion très courante. Cela s’avère également utile si vous travaillez avec un abonnement Azure présentant une limite de dépense obtenu via une offre spéciale réservée aux membres de MSDN ou du programme Microsoft Partner Network Cloud Essentials, par exemple.

Ce scénario repose sur un runbook PowerShell et est conçu pour supprimer un ou plusieurs groupes de ressources que vous spécifiez de votre abonnement. Le runbook prend en charge le test avant la mise en œuvre de la suppression, ce qui est sa valeur par défaut. De cette façon, vous éviterez de supprimer accidentellement des groupes en étant absolument certain que vous êtes prêt à effectuer cette procédure.

## Obtention du scénario

Ce scénario se compose d’un runbook PowerShell que vous pouvez télécharger depuis [PowerShell Gallery](https://www.powershellgallery.com/packages/Remove-ResourceGroup/1.0/DisplayScript) ou importer directement depuis la [galerie de runbooks](automation-runbook-gallery.md) du portail Azure.<br><br>

Runbook | Description|
----------|------------|
Remove-ResourceGroup | Supprime un ou plusieurs groupes de ressources Azure et les ressources associées de l’abonnement.  
<br> Les paramètres d’entrée suivants sont définis pour ce runbook :

Paramètre | Description|
----------|------------|
NameFilter (obligatoire) | Permet de spécifier un filtre de nom pour limiter les groupes de ressources que vous avez l’intention de supprimer. Vous pouvez transmettre plusieurs valeurs à l’aide d’une liste séparée par des virgules.<br>Le filtre ne respecte pas la casse et établit une correspondance avec tous les groupes de ressources qui contiennent cette chaîne.|
PreviewMode (facultatif) | Exécutez le runbook pour voir quels groupes de ressources seraient supprimés, sans procéder à leur suppression.<br> **true** afin d’éviter la suppression accidentelle de groupes de ressources transmis au runbook.  

## Installation et configuration de ce scénario

### Composants requis

Ce runbook s’authentifie à l’aide du [compte d’identification Azure](automation-sec-configure-azure-runas-account.md).

### Installer et publier des Runbook

Après avoir téléchargé le runbook, vous pouvez l’importer à l’aide de la procédure décrite dans les [procédures d’importation de runbooks](automation-creating-importing-runbook.md#importing-a-runbook-from-a-file-into-Azure-Automation). Publiez le runbook une fois qu’il a été correctement importé dans votre compte Automation.


## Utilisation du runbook

La procédure suivante vous guide à travers l’exécution de ce runbook et vous aide à vous familiariser avec son fonctionnement. Dans cet exemple, nous allons uniquement tester le runbook ; nous ne procéderons pas à la suppression effective du groupe de ressources.

1. À partir du portail Azure, ouvrez votre compte Automation, puis cliquez sur la vignette **Runbooks**.
2. Sélectionnez le runbook **Remove-ResourceGroup** et cliquez sur **Démarrer**.
3. Lorsque vous démarrez le runbook, le panneau **Démarrer le Runbook** s’ouvre. Vous pouvez configurer les valeurs suivantes pour les paramètres disponibles. Entrez le nom d’un ou plusieurs groupes de ressources de votre abonnement à inclure dans le test et dont la suppression accidentelle n’aura aucune incidence.<br> ![Paramètres de Remove-ResouceGroup](media/automation-scenario-remove-resourcegroup/remove-resourcegroup-input-parameters.png)
    
    >[AZURE.NOTE] Assurez-vous que l’option **PreviewMode** est définie sur **true** afin d’éviter de supprimer le ou les groupes sélectionnés. **Notez** que ce runbook ne supprimera pas le groupe de ressources contenant le compte Automation qui exécute ce runbook.

4. Une fois toutes les valeurs des paramètres configurées, cliquez sur **OK**. Le runbook est placé en file d’attente en vue de l’exécution.

Pour visualiser les détails de la tâche du runbook **Remove-ResourceGroup** dans le portail Azure, sélectionnez la vignette **Tâches** du runbook. Le résumé de la tâche affiche les paramètres d’entrée et le flux de sortie, en plus des informations générales sur le travail et des éventuelles exceptions.<br> ![État de la tâche du runbook Remove-ResourceGroup](media/automation-scenario-remove-resourcegroup/remove-resourcegroup-runbook-job-status.png)

La section **Résumé de la tâche** inclut les messages des flux de sortie, des flux d’avertissements et des flux d’erreurs. Pour visualiser les résultats détaillés de l’exécution du Runbook, sélectionnez la vignette **Sortie**.<br> ![Résultats de la sortie du runbook Remove-ResourceGroup](media/automation-scenario-remove-resourcegroup/remove-resourcegroup-runbook-job-output.png)

## Étapes suivantes

- Pour vous familiariser avec la création de votre propre runbook, consultez [Création ou importation d’un runbook dans Azure Automation](automation-creating-importing-runbook.md).
- Pour obtenir des informations sur la prise en main des runbooks de workflow PowerShell, consultez [Mon premier runbook PowerShell Workflow](automation-first-runbook-textual.md).

<!---HONumber=AcomDC_0928_2016-->