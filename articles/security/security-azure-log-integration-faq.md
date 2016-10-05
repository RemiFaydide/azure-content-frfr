<properties
   pageTitle="FAQ de l’intégration des journaux Azure | Microsoft Azure"
   description="Ce FAQ répond à des questions concernant l’intégration des journaux Azure."
   services="security"
   documentationCenter="na"
   authors="TomShinder"
   manager="MBaldwin"
   editor="TerryLanfear"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/23/2016"
   ms.author="TomSh"/>

# FAQ de l’intégration des journaux Azure

Ce forum aux questions (FAQ) répond aux questions sur l’intégration des journaux Azure, un service qui vous permet d’intégrer des journaux bruts de vos ressources Azure dans vos systèmes SIEM (Security Information and Event Management) locaux. Cette intégration offre un tableau de bord unifié pour toutes vos ressources, en local ou dans le cloud, pour vous permettre d’agréger, de mettre en corrélation, d’analyser et d’alerter en cas d’événements de sécurité associés à vos applications.

## Comment puis-je voir les comptes de stockage desquels l’intégration des journaux Azure extrait des journaux de machines virtuelles Azure ?

Exécutez la commande **azlog source list**.

## Comment puis-je mettre à jour la configuration du proxy ?

Si vos paramètres de proxy ne permettent pas un accès direct au stockage Azure, ouvrez le fichier **AZLOG. EXE. CONFIGURATION** dans **c:\\Program Files\\Microsoft Azure Log Integration**. Mettez à jour le fichier de manière à y inclure la section **defaultProxy** avec l’adresse de proxy de votre organisation. Une fois la mise à jour effectuée, arrêtez et démarrez le service à l’aide des commandes **net stop azlog** et **net start azlog**.

    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
      <system.net>
        <connectionManagement>
          <add address="*" maxconnection="400" />
        </connectionManagement>
        <defaultProxy>
          <proxy usesystemdefault="true"
          proxyaddress=http://127.0.0.1:8888
          bypassonlocal="true" />
        </defaultProxy>
      </system.net>
      <system.diagnostics>
        <performanceCounters filemappingsize="20971520" />
      </system.diagnostics>   

## Comment puis-je consulter les informations d’abonnement dans des événements Windows ?

Ajoutez le **subscriptionid** à le nom convivial lors de l’ajout de la source.

    Azlog source add <sourcefriendlyname>.<subscription id> <StorageName> <StorageKey>  

L’XML de l’événement a des métadonnées comme indiqué ci-dessous, notamment l’ID d’abonnement.

![XML de l’événement][1]

## messages d'erreur

### Lors de l’exécution de la commande **azlog createazureid**, pourquoi le message d’erreur suivant est-il généré ?

Error:

  *Échec de l’application AAD - Locataire 72f988bf-86f1-41af-91ab-2d7cd011db37 - Raison = « Interdit » - Message = « Privilèges insuffisants pour terminer l’opération ». (Failed to create AAD Application - Tenant 72f988bf-86f1-41af-91ab-2d7cd011db37 - Reason = ’Forbidden’ - Message = ’Insufficient privileges to complete the operation.’)*

**Azlog createazureid** tente de créer un principal du service dans tous les locataires Azure AD pour les abonnements auxquels la connexion Azure a accès. Si votre connexion Azure n'a que le rôle d’Invité dans ce locataire Azure AD, la commande échoue avec « Privilèges insuffisants pour terminer l’opération ». Demander à l’administrateur du locataire d’ajouter votre compte en tant qu’utilisateur dans le locataire.

### Lors de l’exécution de la commande **azlog authorize**, pourquoi le message d’erreur suivant est-il généré ?

Error:

  *Avertissement à la création d’attribution de rôle : AuthorizationFailed : le client janedo@microsoft.com' avec l’ID d’objet 'fe9e03e4-4dad-4328-910f-fd24a9660bd2' n’est pas autorisé à effectuer l’action « Microsoft.Authorization/roleAssignments/write » sur '/ subscriptions/70d95299-d689-4c97-b971-0d8ff0000000'.*

La commande **Azlog authorize** attribue le rôle de lecteur au principal du service Azure AD (créé avec **Azlog createazureid**) dans les abonnements fournis. Si la connexion Azure n’a pas le rôle Coadministrateur ou un Propriétaire de l’abonnement, elle échoue avec le message d’erreur « Échec de l’autorisation ». Le contrôle d’accès en fonction du rôle (RBAC) d’Azure du Coadministrateur ou du Propriétaire est nécessaire pour effectuer cette action.

## Où puis-je trouver la définition des propriétés dans le journal d’audit ?

Consultez l'article :

- [Opérations d’audit avec Resource Manager](../resource-group-audit.md)
- [Liste des événements de gestion dans un abonnement dans une API REST Azure Insights (en Anglais)](https://msdn.microsoft.com/library/azure/dn931934.aspx)

## Où puis-je trouver plus d’informations sur les alertes de l’Azure Security Center ?

Consultez la rubrique [Gestion et résolution des alertes de sécurité dans le Centre de sécurité Azure](../security-center/security-center-managing-and-responding-alerts.md).

## Comment puis-je modifier les données collectées avec les diagnostics de la machine virtuelle ?

Consultez la page [Utiliser PowerShell pour activer Azure Diagnostics sur une machine virtuelle exécutant Windows](../virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md) pour plus d’informations sur la manière d’obtenir, modifier et définir les diagnostics Azure dans une configuration Windows *(WAD)*. Voici un exemple :

### Obtention de la configuration de WAD

    -AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient
    $publicsettings = (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient).PublicSettings
    $encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
    $xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
    Write-Host $xmlconfig

    $xmlconfig | Out-File -Encoding utf8 -FilePath "d:\WADConfig.xml"

### Modification de la configuration de WAD

L’exemple suivant est une configuration où seuls l’ID d’événement 4624 et ID d’événement 4625 sont collectés à partir du journal des événements de sécurité. Des événements Microsoft Antimalware sont collectés dans le journal des événements système. Consultez [Consommation d’événements](https://msdn.microsoft.com/library/windows/desktop/dd996910(v=vs.85) pour plus d’informations sur l’utilisation d’expressions XPath.

    <WindowsEventLog scheduledTransferPeriod="PT1M">
        <DataSource name="Security!*[System[(EventID=4624 or EventID=4625)]]" />
        <DataSource name="System!*[System[Provider[@Name='Microsoft Antimalware']]]"/>
    </WindowsEventLog>

### Définition de la configuration de WAD

    $diagnosticsconfig_path = "d:\WADConfig.xml"
    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName log3121 -StorageAccountKey <storage key>

Après avoir apporté les modifications, vérifiez le compte de stockage pour vous assurer que les événements corrects sont collectés.

Si vous avez des questions sur l’intégration des journaux Azure, envoyez un e-mail à [AzSIEMteam@microsoft.com](mailto:AzSIEMteam@microsoft.com)

<!--Image references-->
[1]: ./media/security-azure-log-integration-faq/event-xml.png

<!---HONumber=AcomDC_0921_2016-->