<properties 
	pageTitle="Comment créer un ILB ASE à l’aide des modèles Azure Resource Manager | Microsoft Azure" 
	description="Découvrez comment créer un ASE d’équilibrage de charge interne à l’aide des modèles Azure Resource Manager." 
	services="app-service" 
	documentationCenter="" 
	authors="stefsch" 
	manager="nirma" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/21/2016" 
	ms.author="stefsch"/>

# Comment créer un ILB ASE à l’aide des modèles Azure Resource Manager

## Vue d'ensemble ##
Les environnements App Service peuvent être créés avec une adresse interne de réseau virtuel au lieu d’une adresse IP virtuelle publique. Cette adresse interne est fournie par un composant Azure appelé « équilibreur de charge interne » (ILB). Vous pouvez créer un ILB ASE à l’aide du portail Azure. Il peut également être créé de manière automatisée par le biais des modèles Azure Resource Manager. Cet article décrit les étapes et la syntaxe nécessaires à la création d’un ILB ASE à l’aide des modèles Azure Resource Manager.

La création automatisée d’un ILB ASE comporte trois étapes :
1. Tout d’abord, l’ASE de base est créé dans un réseau virtuel à l’aide d’une adresse d’équilibreur de charge interne au lieu d’une adresse IP virtuelle publique. Dans le cadre de cette étape, un nom de domaine racine est affecté à l’ILB ASE.
2. Une fois l’ILB ASE créé, un certificat SSL est téléchargé.
3. Le certificat SSL téléchargé est explicitement affecté à l’ILB ASE en tant que certificat SSL « par défaut ». Ce certificat SSL servira à des applications de l’ILB ASE pour le trafic SSL lorsque celles-ci sont gérées à l’aide d’un domaine racine commun affecté à l’ASE (par exemple, https://someapp.mycustomrootcomain.com).

## Création de l’ILB ASE de base ##
Un exemple de modèle Azure Resource Manager et de son fichier de paramètres associés, sont disponibles sur GitHub [ici][quickstartilbasecreate].

La plupart des paramètres du fichier *azuredeploy.parameters.json* sont communs à la création d’ASE ILB, ainsi que d’ASE liés à une adresse IP virtuelle publique. La liste ci-dessous répertorie les paramètres spécifiques, ou qui sont uniques, lors de la création d’un ILB ASE :


- *interalLoadBalancingMode* : dans la plupart des cas, définissez ce paramètre sur 3, ce qui signifie que le trafic HTTP/HTTPS sur les ports 80/443 ainsi que les ports de canaux de contrôle/données écoutés par le service FTP sur l’ASE seront liés à une adresse interne du réseau virtuel allouée à l’ILB. Si cette propriété a la valeur 2, seuls les ports relatifs au service FTP (canaux de contrôle et de données) seront liés à une adresse d’ILB, tandis que le trafic HTTP/HTTPS restera sur l’adresse IP virtuelle publique.
-  *dnsSuffix* : ce paramètre définit le domaine racine par défaut qui sera affecté à l’ASE. Dans la version publique d’Azure App Service, le domaine racine par défaut pour toutes les applications web est *azurewebsites.net*. Cependant, étant donné qu’un ILB ASE est interne au réseau virtuel d’un client, il n’est pas pertinent d’utiliser le domaine racine par défaut du service public. Au lieu de cela, un ILB ASE doit avoir un domaine racine par défaut approprié pour une utilisation au sein du réseau virtuel interne d’une société. Par exemple, une société fictive Contoso Corporation peut utiliser le domaine racine par défaut *internal-contoso.com* pour les applications qui sont destinées à être résolues et accessibles uniquement au sein du réseau virtuel de Contoso.
-  *ipSslAddressCount* : ce paramètre est automatiquement défini par défaut sur la valeur 0 dans le fichier *azuredeploy.json*, car les ILB ASE disposent d’une seule adresse d’ILB. Il n’existe aucune adresse IP SSL explicite pour un ILB ASE. Par conséquent, le pool d’adresses IP SSL pour un ILB ASE doit être défini sur zéro, sinon une erreur d’approvisionnement se produit.

Une fois le fichier *azuredeploy.parameters.json* renseigné pour un ILB ASE, ce dernier peut ensuite être créé à l’aide de l’extrait de code Powershell suivant. Modifiez les chemins d’accès de fichiers pour qu’ils correspondent aux emplacements où se trouvent les fichiers de modèle Azure Resource Manager sur votre ordinateur. Pensez également à indiquer vos propres valeurs pour le nom de déploiement d’Azure Resource Manager et le nom de groupe de ressources.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"
    
    New-AzureRmResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

Une fois le modèle Azure Resource Manager soumis, la création de l’ILB ASE prendra quelques heures. Une fois la création terminée, l’ASE ILB s’affichera dans le portail dans la liste des environnements App Service pour l’abonnement qui a déclenché le déploiement.

## Téléchargement et configuration du certificat SSL « par défaut » ##

Une fois l’ILB ASE créé, un certificat SSL doit être associé à l’ASE en tant que certificat SSL « par défaut » utilisé pour établir des connexions SSL aux applications. Poursuivons avec l’exemple de la société fictive Contoso Corporation. Si le suffixe DNS par défaut de l’ASE est *internal-contoso.com*, une connexion à *https://some-random-app.internal-contoso.com* nécessite un certificat SSL valide pour **.internal-contoso.com*.

Il existe plusieurs façons d’obtenir un certificat SSL valide, parmi lesquelles les autorités de certification internes, l’achat d’un certificat auprès d’un émetteur externe et l’utilisation d’un certificat auto-signé. Quelle que soit la source du certificat SSL, les attributs de certificat suivants doivent être configurés correctement :

- *Objet* : cet attribut doit être défini sur **.votre-domaine-racine-ici.com*
- *Autre nom de l’objet* : cet attribut doit inclure à la fois **.votre-domaine-racine-ici.com* et **.scm.votre-domaine-racine-ici.com*. La deuxième entrée est nécessaire, car les connexions SSL sur le site SCM/Kudu associé à chaque application seront établies à l’aide d’une adresse sous la forme *votre-nom-application.scm.votre-domaine-racine-ici.com*.

Une fois le certificat SSL valide obtenu, deux étapes préparatoires supplémentaires sont nécessaires. Le certificat SSL doit être converti/enregistré au format de fichier .pfx. N’oubliez pas que le fichier .pfx doit inclure tous les certificats intermédiaires et racine et être sécurisé avec un mot de passe.

Le fichier .pfx obtenu doit ensuite être converti en une chaîne au format Base64, car le certificat SSL sera téléchargé à l’aide d’un modèle Azure Resource Manager. Étant donné que les modèles Azure Resource Manager sont des fichiers texte, le fichier .pfx doit être converti en une chaîne au format Base64 afin d’être inclus en tant que paramètre du modèle.

L’extrait de code Powershell ci-dessous montre un exemple de génération d’un certificat auto-signé, d’exportation du certificat en tant que fichier .pfx, de conversion du fichier .pfx au format Base64 de la chaîne codée, puis d’enregistrement de la chaîne codée en Base64 dans un fichier distinct. Le code Powershell pour l’encodage en Base64 est une adaptation issue du [Blog Powershell Scripts][examplebase64encoding].

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

    $certThumbprint = "cert:\localMachine\my" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.pfx"
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     
    
    $fileContentBytes = get-content -encoding byte $fileName
    $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
    $fileContentEncoded | set-content ($fileName + ".b64")
    
Une fois le certificat SSL généré et converti en chaîne codée en Base64, vous pouvez utiliser l’exemple de modèle Azure Resource Manager sur GitHub pour [configurer le certificat SSL par défaut][configuringDefaultSSLCertificate].

Les paramètres du fichier *azuredeploy.parameters.json* sont répertoriés ci-dessous :

- *appServiceEnvironmentName* : nom de l’ILB ASE configuré.
- *existingAseLocation* : chaîne de texte contenant la région Azure où l’ILB ASE a été déployé. Par exemple : « Centre-Sud des États-Unis ».
- *pfxBlobString* : représentation sous forme de chaîne codée en Base64 du fichier .pfx. À l’aide de l’extrait de code ci-dessus, copiez la chaîne contenue dans « exportedcert.pfx.b64 » et collez-la en tant que valeur de l’attribut *pfxBlobString*.
- *password* : mot de passe utilisé pour sécuriser le fichier .pfx.
- *certificateThumbprint* : empreinte numérique du certificat. Si vous récupérez cette valeur à partir de Powershell (par exemple, *$certificate.Thumbprint* dans l’extrait de code précédent), vous pouvez utiliser la valeur telle quelle. Toutefois, si vous copiez la valeur à partir de la boîte de dialogue de certificat Windows, n’oubliez pas de retirer les espaces superflus. La valeur *certificateThumbprint* doit se présenter sous la forme suivante : AF3143EB61D43F6727842115BB7F17BBCECAECAE.
- *certificateName* : identificateur de chaîne convivial de votre choix permettant d’identifier le certificat. Ce nom fait partie de l’identificateur Azure Resource Manager unique pour l’entité *Microsoft.Web/certificates* qui représente le certificat SSL. Le nom **doit** se terminer par le suffixe suivant : \_nomdevotreASE\_InternalLoadBalancingASE. Ce suffixe permet au portail d’indiquer que le certificat est utilisé pour sécuriser un ASE avec équilibrage de charge interne.


Un exemple abrégé de fichier *azuredeploy.parameters.json* est présenté ci-dessous :


    {
         "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json",
         "contentVersion": "1.0.0.0",
         "parameters": {
              "appServiceEnvironmentName": {
                   "value": "yourASENameHere"
              },
              "existingAseLocation": {
                   "value": "East US 2"
              },
              "pfxBlobString": {
                   "value": "MIIKcAIBAz...snip...snip...pkCAgfQ"
              },
              "password": {
                   "value": "PASSWORDGOESHERE"
              },
              "certificateThumbprint": {
                   "value": "AF3143EB61D43F6727842115BB7F17BBCECAECAE"
              },
              "certificateName": {
                   "value": "DefaultCertificateFor_yourASENameHere_InternalLoadBalancingASE"
              }
         }
    }

Une fois le fichier *azuredeploy.parameters.json* renseigné, le certificat SSL par défaut peut être configuré à l’aide de l’extrait de code Powershell suivant. Modifiez les chemins d’accès de fichiers pour qu’ils correspondent aux emplacements où se trouvent les fichiers de modèle Azure Resource Manager sur votre ordinateur. Pensez également à indiquer vos propres valeurs pour le nom de déploiement d’Azure Resource Manager et le nom de groupe de ressources.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"
    
    New-AzureRmResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

Une fois le modèle Azure Resource Manager soumis, cela prendra environ quarante minutes par serveur frontal de l’ASE pour que la modification soit appliquée. Par exemple, avec un ASE par défaut utilisant deux serveurs frontaux, l’application du modèle prendra environ une heure et vingt minutes. Lorsque le modèle est en cours d’exécution, l’ASE ne peut pas être mis à l’échelle.

Une fois le modèle terminé, les applications sur l’ILB ASE sont accessibles via le protocole HTTPS et les connexions sont sécurisées à l’aide du certificat SSL par défaut. Le certificat SSL par défaut est utilisé lorsque les applications sur l’ILB ASE sont gérées à l’aide d’une combinaison du nom de l’application et du nom d’hôte par défaut. Par exemple, *https://mycustomapp.internal-contoso.com* utiliserait le certificat SSL par défaut pour **.internal-contoso.com*.

Toutefois, tout comme les applications qui s’exécutent sur le service public mutualisé, les développeurs peuvent également configurer des noms d’hôte personnalisés pour les applications individuelles, puis configurer des liaisons de certificat SNI SSL uniques pour ces applications individuelles.


## Prise en main

Pour prendre en main les environnements App Service, consultez [Présentation de l'environnement App Service](app-service-app-service-environment-intro.md).

Tous les articles et procédures concernant les environnements App Service sont disponibles dans le [fichier Lisez-moi des environnements App Service](../app-service/app-service-app-service-environments-readme.md).

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[quickstartilbasecreate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-create/
[examplebase64encoding]: http://powershellscripts.blogspot.com/2007/02/base64-encode-file.html
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl/
 

<!---HONumber=AcomDC_0921_2016-->