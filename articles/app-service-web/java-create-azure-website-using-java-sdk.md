<properties 
	pageTitle="Création d’une application web dans Azure App Service à l’aide du Kit de développement logiciel (SDK) Azure pour Java" 
	description="Apprenez à créer une application web sur Azure App Service par programme à l’aide du Kit de développement logiciel (SDK) Azure pour Java." 
	tags="azure-classic-portal"
	services="app-service\web" 
	documentationCenter="Java" 
	authors="donntrenton" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="multiple" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="02/25/2016" 
	ms.author="v-donntr"/>


# Création d’une application web dans Azure App Service à l’aide du Kit de développement logiciel (SDK) Azure pour Java

<!-- Azure Active Directory workflow is not yet available on the Azure Portal -->

## Vue d'ensemble

Cette procédure pas à pas vous montre comment créer un Kit de développement logiciel (SDK) Azure pour une application Java qui crée une application web dans [Azure App Service][], puis comment déployer une application vers celle-ci. Elle comprend deux parties :

- La première partie montre comment créer une application Java qui crée une application web.
- La deuxième partie montre comment créer une simple application JSP « Hello World » , puis utiliser un client FTP pour déployer le code sur App Service.


## Configuration requise

### Installations de logiciels

Le code d'application AzureWebDemo dans cet article a été écrit à l'aide du Kit de développement logiciel (SDK) Azure Java 0.7.0, que vous pouvez installer à l'aide de [Web Platform Installer][] \(WebPI). En outre, veillez à utiliser la dernière version de la [boîte à outils Azure pour Eclipse][]. Après avoir installé le Kit de développement logiciel (SDK), mettez à jour les dépendances de votre projet Eclipse en exécutant **Mettre à jour l’index** dans les **référentiels Maven**, puis ajoutez de nouveau la version la plus récente de chaque package dans la fenêtre **Dépendances**. Vous pouvez vérifier la version de votre logiciel installé dans Eclipse en cliquant sur **Aide > Détails de l’installation** ; vous devez avoir au moins les versions suivantes :

- Package pour les bibliothèques Microsoft Azure pour Java 0.7.0.20150309
- Environnement de développement intégré (IDE) Eclipse pour développeurs Java EE 4.4.2.20150219


### Création et configuration de ressources de cloud dans Azure

Avant de commencer cette procédure, vous devez disposer d’un abonnement Azure actif et définir une valeur Active Directory (AD) par défaut sur Azure.


### Création d’un annuaire Active Directory (AD) dans Azure

Si vous ne disposez pas déjà d’un annuaire Active Directory (AD) dans votre abonnement Azure, connectez-vous au [Portail Azure Classic][] avec votre compte Microsoft. Si vous disposez de plusieurs abonnements, cliquez sur **Abonnements** et sélectionnez l’annuaire par défaut pour l’abonnement que vous souhaitez utiliser pour ce projet. Cliquez sur **Appliquer** pour basculer vers cette vue d’abonnement.

1. Sélectionnez **Active Directory** dans le menu à gauche. **Cliquez sur Nouveau > Annuaire > Création personnalisée**.

2. Dans **Ajouter un annuaire**, sélectionnez **Créer un nouvel annuaire**.

3. Dans **Nom**, entrez un nom d’annuaire.

4. Dans **Domaine**, entrez un nom de domaine. Il s’agit d’un nom de domaine de base qui est inclus par défaut avec votre annuaire ; il se présente sous la forme `<domain_name>.onmicrosoft.com`. Vous pouvez définir son nom sur la base du nom d’annuaire ou d’un autre nom de domaine que vous possédez. Par la suite, vous pouvez ajouter un autre nom de domaine que votre organisation utilise déjà.

5. Dans **Pays ou région**, sélectionnez votre paramètre régional.

Pour plus d'informations sur AD, consultez la page [Qu'est-ce qu'un annuaire Azure AD][] ?


### Création d’un certificat de gestion pour Azure

Le Kit de développement logiciel (SDK) Azure pour Java utilise des certificats de gestion pour s’authentifier avec des abonnements Azure. Il s’agit de certificats X.509 v3 vous permettant d’authentifier une application cliente qui utilise l’API Gestion des services pour agir au nom du propriétaire de l’abonnement afin de gérer les ressources d’abonnement.

Le code de cette procédure utilise un certificat auto-signé pour l’authentification auprès d’Azure. Pour cette procédure, vous devez créer un certificat et le télécharger sur le [Portail Azure Classic][] au préalable. Cela implique les étapes suivantes :

- Générer un fichier PFX représentant votre certificat client et l’enregistrer en local
- Générer un certificat de gestion (fichier CER) à partir du fichier PFX
- Télécharger le fichier CER vers votre abonnement Azure
- Convertir le fichier PFX au format JKS, car Java utilise ce format pour s’authentifier à l’aide de certificats
- Écrire le code d’authentification de l’application, qui fait référence au fichier JKS local

Lorsque vous effectuez cette procédure, le certificat CER réside dans votre abonnement Azure et le certificat JKS réside sur votre lecteur local. Pour plus d’informations sur les certificats de gestion, consultez la page [Créer et télécharger un certificat de gestion pour Microsoft Azure][].


#### Création d’un certificat

Pour créer votre propre certificat auto-signé, ouvrez une console de commandes sur votre système d’exploitation et exécutez les commandes suivantes.

> **Remarque :** le JDK doit être installé sur l’ordinateur sur lequel vous exécutez cette commande. En outre, le chemin d’accès vers l’outil keytool dépend de l’emplacement où vous installez le JDK. Pour plus d’informations, consultez la rubrique [Outil de gestion de clés et de certificats (keytool)][] dans la documentation en ligne de Java.

Pour créer le fichier .pfx :

    <java-install-dir>/bin/keytool -genkey -alias <keystore-id>
     -keystore <cert-store-dir>/<cert-file-name>.pfx -storepass <password>
     -validity 3650 -keyalg RSA -keysize 2048 -storetype pkcs12
     -dname "CN=Self Signed Certificate 20141118170652"

Pour créer le fichier .cer :

    <java-install-dir>/bin/keytool -export -alias <keystore-id>
     -storetype pkcs12 -keystore <cert-store-dir>/<cert-file-name>.pfx
     -storepass <password> -rfc -file <cert-store-dir>/<cert-file-name>.cer

où :

- `<java-install-dir>` correspond au chemin d’accès vers le répertoire où vous avez installé Java.
- `<keystore-id>` correspond à l’identificateur d’entrée keystore (par exemple, `AzureRemoteAccess`).
- `<cert-store-dir>` correspond au chemin d’accès vers le répertoire dans lequel vous souhaitez stocker les certificats (par exemple `C:/Certificates`).
- `<cert-file-name>` correspond au nom du fichier de certificat (par exemple `AzureWebDemoCert`).
- `<password>` correspond au mot de passe que vous choisissez pour protéger le certificat ; il doit comporter au moins 6 caractères. Vous avez la possibilité de n’entrer aucun mot de passe, même si cela n’est pas recommandé.
- `<dname>` correspond au nom unique X.500 à associer à l’alias, et est utilisé en tant que champs de l’émetteur et du sujet dans le certificat auto-signé.

Pour plus d’informations, consultez la rubrique [Créer et télécharger un certificat de gestion pour Microsoft Azure][].


#### Téléchargement du certificat

Pour télécharger un certificat auto-signé dans Azure, accédez à la page **Paramètres** dans le Portail Azure Classic, puis cliquez sur l’onglet **Certificats de gestion**. Cliquez sur **Télécharger** en bas de la page et naviguez jusqu’à l’emplacement du fichier CER que vous avez créé.


#### Conversion du fichier PFX au format JKS

Dans l’invite de commandes Windows (en mode d’exécution d’administrateur), accédez avec la commande cd au répertoire contenant les certificats et exécutez la commande suivante, où `<java-install-dir>` correspond au répertoire où vous avez installé Java sur votre ordinateur :

    <java-install-dir>/bin/keytool.exe -importkeystore
     -srckeystore <cert-store-dir>/<cert-file-name>.pfx
     -destkeystore <cert-store-dir>/<cert-file-name>.jks
     -srcstoretype pkcs12 -deststoretype JKS

1. Lorsque vous y êtes invité, entrez le mot de passe keystore de la destination ; ce sera le mot de passe pour le fichier JKS.

2. Lorsque vous y êtes invité, entrez le mot de passe keystore source ; il s’agit du mot de passe que vous avez spécifié pour le fichier PFX.

Les deux mots de passe ne doivent pas être identiques. Vous avez la possibilité de n’entrer aucun mot de passe, même si cela n’est pas recommandé.


## Création d’une application de création d’application web

### Création de l’espace de travail Eclipse et du projet Maven

Dans cette section, vous créez un espace de travail et un projet Maven pour l’application de création d’applications web, nommée AzureWebDemo.

1. Créez un projet Maven. Cliquez sur **Fichier > Nouveau > Projet Maven**. Dans **Nouveau projet Maven**, sélectionnez **Créer un projet simple** et **Utiliser l’emplacement de l’espace de travail par défaut**.

2. Dans la deuxième page de **Nouveau projet Maven**, spécifiez les éléments suivants :

    - ID de groupe : `com.<username>.azure.webdemo`
    - ID d’artefact : AzureWebDemo
    - Version : 0.0.1-SNAPSHOT
    - Packaging : ar
    - Nom : AzureWebDemo

    Cliquez sur **Terminer**.

3. Ouvrez le fichier pom.xml du nouveau projet dans l’Explorateur de projets. Sélectionnez l’onglet **Dépendances**. Comme il s’agit d’un nouveau projet, aucun package n’est encore répertorié.

4. Ouvrez la vue Référentiels Maven. **Cliquez sur Fenêtre > Afficher la vue > Autres > Maven > Référentiels Maven **, puis cliquez sur **OK**. La vue **Référentiels Maven** s’affiche en bas de l’IDE.

5. Ouvrez **Référentiels globaux**, cliquez avec le bouton droit sur le référentiel **central**, puis sélectionnez **Reconstruire l’index**.

    ![][1]
    
    Cette étape peut prendre plusieurs minutes selon la vitesse de votre connexion. Une fois l’index reconstruit, vous devez voir les packages Microsoft Azure dans le référentiel Maven **central**.

6. Dans **Dépendances**, cliquez sur **Ajouter**. Dans **Entrer l’ID de groupe...**, entrez `azure-management`. Sélectionnez les packages pour la gestion de base et la gestion de App Service Web Apps :

        com.microsoft.azure  azure-management
        com.microsoft.azure  azure-management-websites

    > **Remarque :** si vous mettez à jour les dépendances après la publication d’une nouvelle version, il vous faudra ajouter à nouveau chacune des dépendances dans cette liste. Après avoir cliqué sur **Ajouter** et sélectionné chaque dépendance, celle-ci apparaît avec le nouveau numéro de version dans la liste **Dépendances**.

Cliquez sur **OK**. Les packages Azure apparaissent alors dans la liste **Dépendances**.


### Écriture de code Java pour créer une application web en appelant le SDK Azure

Ensuite, écrivez le code qui appelle les API dans le Kit de développement logiciel (SDK) Azure pour Java afin de créer l’application web App Service.

1. Créez une classe Java pour contenir le code de point d’entrée principal. Dans l’Explorateur de projets, cliquez avec le bouton droit sur le nœud du projet et sélectionnez **Nouveau > Classe**.

2. Dans **Nouvelle classe Java**, nommez la classe `WebCreator` et activez la case **public static void main**. Les sélections doivent apparaître comme suit :

    ![][2]

3. Cliquez sur **Terminer**. Le fichier WebCreator.java s’affiche dans l’Explorateur de projets.


### Appel de l’API Azure pour créer une application web App Service


#### Ajout des importations nécessaires

Dans WebCreator.java, ajoutez les importations ci-après ; celles-ci donnent accès aux classes dans les bibliothèques de gestion et permettent ainsi d’utiliser les API Azure :

    // General imports
    import java.net.URI;
    import java.util.ArrayList;
    
    // Imports for Exceptions
    import java.io.IOException;
    import java.net.URISyntaxException;
    import javax.xml.parsers.ParserConfigurationException;
    import com.microsoft.windowsazure.exception.ServiceException;
    import org.xml.sax.SAXException;
    
    // Imports for Azure App Service management configuration
    import com.microsoft.windowsazure.Configuration;
    import com.microsoft.windowsazure.management.configuration.ManagementConfiguration;
    
    // Service management imports for App Service Web Apps creation
    import com.microsoft.windowsazure.management.websites.*;
    import com.microsoft.windowsazure.management.websites.models.*;
    
    // Imports for authentication
    import com.microsoft.windowsazure.core.utils.KeyStoreType;


#### Définition de la classe de point d’entrée principal

Étant donné que l’objectif de l’application AzureWebDemo consiste à créer une application web App Service, nommez la classe principale pour cette application `WebAppCreator`. Cette classe fournit le code de point d’entrée principal qui appelle l’API Gestion de services Azure pour créer l’application web.

Ajoutez les définitions de paramètre suivantes pour l’application web et l’espace web. Vous devez fournir vos propres informations de certificat et d’ID d’abonnement Azure.

    public class WebAppCreator {
    
        // Parameter definitions used for authentication.
        private static String uri = "https://management.core.windows.net/";
        private static String subscriptionId = "<subscription-id>";
        private static String keyStoreLocation = "<certificate-store-path>";
        private static String keyStorePassword = "<certificate-password>";
    
        // Define web app parameter values.
        private static String webAppName = "WebDemoWebApp";
        private static String domainName = ".azurewebsites.net";
        private static String webSpaceName = WebSpaceNames.WESTUSWEBSPACE;
        private static String appServicePlanName = "WebDemoAppServicePlan";

où :

- `<subscription-id>` correspond à l’ID d’abonnement Azure dans lequel vous souhaitez créer la ressource.
- `<certificate-store-path>` correspond au chemin d’accès et au nom de fichier pour le fichier JKS dans votre annuaire local de magasins de certificats. Par exemple, `C:/Certificates/CertificateName.jks` pour Linux et `C:\Certificates\CertificateName.jks` pour Windows.
- `<certificate-password>` correspond au mot de passe que vous avez spécifié lorsque vous avez créé votre certificat JKS.
- `webAppName` peut être n’importe quel nom que vous avez choisi ; cette procédure utilise le nom `WebDemoWebApp`. Le nom de domaine complet est le `webAppName` avec le `domainName` ajouté, de sorte que dans ce cas le domaine complet est `webdemowebapp.azurewebsites.net`.
- `domainName` doit être spécifié comme indiqué ci-dessus.
- `webSpaceName` doit être l’une des valeurs définies dans la classe [WebSpaceNames][].
- `appServicePlanName` doit être spécifié comme indiqué ci-dessus.

> **Remarque :** chaque fois que vous exécutez cette application, vous devez modifier la valeur de `webAppName` et `appServicePlanName` (ou supprimer l’application web sur le portail Azure) avant d’exécuter à nouveau l’application. Sinon, l’exécution échoue, car la même ressource existe déjà sur Azure.


#### Définition de la méthode de création web

Ensuite, définissez une méthode de création de l’application web. Cette méthode, `createWebApp`, spécifie les paramètres de l’application web et l’espace web. Elle crée et configure également le client App Service Web Apps, qui est défini par l’objet [WebSiteManagementClient][]. Le client de gestion est essentiel à la création d’applications web. Il fournit des services web RESTful qui permettent aux applications de gérer des applications web (exécution d’opérations telles que create, update et delete) en appelant l’API Gestion des services.

    private static void createWebApp() throws Exception {

        // Specify configuration settings for the App Service management client.
        Configuration config = ManagementConfiguration.configure(
            new URI(uri),
            subscriptionId,
            keyStoreLocation,  // Path to the JKS file
            keyStorePassword,  // Password for the JKS file
            KeyStoreType.jks   // Flag that you are using a JKS keystore
        );

        // Create the App Service Web Apps management client to call Azure APIs
        // and pass it the App Service management configuration object.
        WebSiteManagementClient webAppManagementClient = WebSiteManagementService.create(config);

        // Create an App Service plan for the web app with the specified parameters.
        WebHostingPlanCreateParameters appServicePlanParams = new WebHostingPlanCreateParameters();
        appServicePlanParams.setName(appServicePlanName);
        appServicePlanParams.setSKU(SkuOptions.Free);
        webAppManagementClient.getWebHostingPlansOperations().create(webSpaceName, appServicePlanParams);

        // Set webspace parameters.
        WebSiteCreateParameters.WebSpaceDetails webSpaceDetails = new WebSiteCreateParameters.WebSpaceDetails();
        webSpaceDetails.setGeoRegion(GeoRegionNames.WESTUS);
        webSpaceDetails.setPlan(WebSpacePlanNames.VIRTUALDEDICATEDPLAN);
        webSpaceDetails.setName(webSpaceName);

        // Set web app parameters.
        // Note that the server farm name takes the Azure App Service plan name.
        WebSiteCreateParameters webAppCreateParameters = new WebSiteCreateParameters();
        webAppCreateParameters.setName(webAppName);
        webAppCreateParameters.setServerFarm(appServicePlanName);
        webAppCreateParameters.setWebSpace(webSpaceDetails);

        // Set usage metrics attributes.
        WebSiteGetUsageMetricsResponse.UsageMetric usageMetric = new WebSiteGetUsageMetricsResponse.UsageMetric();
        usageMetric.setSiteMode(WebSiteMode.Basic);
        usageMetric.setComputeMode(WebSiteComputeMode.Shared);

        // Define the web app object.
        ArrayList<String> fullWebAppName = new ArrayList<String>();
        fullWebAppName.add(webAppName + domainName);
        WebSite webApp = new WebSite();
        webApp.setHostNames(fullWebAppName);

        // Create the web app.
        WebSiteCreateResponse webAppCreateResponse = webAppManagementClient.getWebSitesOperations().create(webSpaceName, webAppCreateParameters);

        // Output the HTTP status code of the response; 200 indicates the request succeeded; 4xx indicates failure.
        System.out.println("----------");
        System.out.println("Web app created - HTTP response " + webAppCreateResponse.getStatusCode() + "\n");

        // Output the name of the web app that this application created.
        String shinyNewWebAppName = webAppCreateResponse.getWebSite().getName();
        System.out.println("----------\n");
        System.out.println("Name of web app created: " + shinyNewWebAppName + "\n");
        System.out.println("----------\n");
    }

Le code génère l’état HTTP de la réponse indiquant la réussite ou l’échec, et en cas de réussite, génère le nom de l’application web créée.


#### Définition de la méthode main()

Fournissez le code de la méthode main() qui appelle createWebApp() pour créer l’application web.

Enfin, appelez `createWebApp` à partir de `main` :

        public static void main(String[] args)
            throws IOException, URISyntaxException, ServiceException,
            ParserConfigurationException, SAXException, Exception {

            // Create web app
            createWebApp();

        }  // end of main()

    }  // end of WebAppCreator class


#### Exécution de l’application et vérification de la création de l’application web

Pour vérifier que votre application s’exécute, cliquez sur **Exécuter > Exécuter**. Au terme de l’exécution de l’application, vous devez voir la sortie suivante dans la console Eclipse :

    ----------
    Web app created - HTTP response 200
    
    ----------
    
    Name of web app created: WebDemoWebApp
    
    ----------

Connectez-vous au Portail Azure Classic et cliquez sur **Applications web**. La nouvelle application web doit apparaître dans la liste des applications web dans les quelques minutes qui suivent.


## Déploiement d’une application sur l’application web

Une fois que vous avez exécuté AzureWebDemo et créé la nouvelle application web, connectez-vous au Portail Classic, cliquez sur **Applications web**, puis sélectionnez **WebDemoWebApp** dans la liste **Applications web**. Dans la page de tableau de bord de l’application web, cliquez sur **Parcourir** (ou cliquez sur l’URL, `webdemowebapp.azurewebsites.net`) pour y accéder. Seule une page d’espace réservé vide s’affiche, car aucun contenu n’a été publié sur l’application web.

Ensuite, vous créez une application « Hello World » et la déployez sur l’application web.


### Création d’une application JSP Hello World

#### Création de l'application

Pour illustrer le déploiement d’une application sur le web, la procédure suivante vous montre comment créer une application Java « Hello World » simple et la télécharger sur l’application web App Service créée par votre application.

1. Cliquez sur **Fichier > Nouveau > Projet Web dynamique**. Nommez-le `JSPHello`. Il est inutile de modifier d’autres paramètres dans cette boîte de dialogue. Cliquez sur **Terminer**.

    ![][3]

2. Dans l’Explorateur de projets, développez le projet **JSPHello** , cliquez avec le bouton droit sur **WebContent**, puis cliquez sur **Nouveau > Fichier JSP**. Dans la boîte de dialogue Nouveau fichier JSP, nommez le nouveau fichier `index.jsp`. Cliquez sur **Next**.

3. Dans la boîte de dialogue **Select JSP Template**, sélectionnez **New JSP File (html)**, puis cliquez sur **Terminer**.

4. Dans index.jsp, ajoutez le code suivant dans les sections des balises `<head>` et `<body>` :

        <head>
          ...
          java.util.Date date = new java.util.Date();
        </head>
    
        <body>
          Hello, the time is <%= date %> 
        </body>


#### Exécution de l’application Hello World dans localhost

Avant d’exécuter cette application, vous devez configurer certaines propriétés.

1. Cliquez avec le bouton droit sur le projet **JSPHello** et sélectionnez **Propriétés**.

2. Dans la boîte de dialogue **Propriétés** : sélectionnez **Chemin d’accès de la génération Java**, sélectionnez l’onglet **Ordre et exportation**, activez la case **Bibliothèque système JRE**, puis cliquez sur **Haut** pour déplacer l’élément vers le haut de la liste.

    ![][4]

3. Également dans la boîte de dialogue **Propriétés**, sélectionnez **Runtimes ciblés**, puis cliquez sur **Nouveau**.

4. Dans la boîte de dialogue **Nouvel environnement d’exécution serveur**, sélectionnez un serveur tel que **Apache Tomcat v7.0**, puis cliquez sur **Suivant**. Dans la boîte de dialogue **Serveur Tomcat**, définissez **Nom** sur `Apache Tomcat v7.0`, et définissez **Répertoire d’installation Tomcat** sur le répertoire dans lequel vous avez installé la version du serveur Tomcat que vous souhaitez utiliser.

    ![][5]

    Cliquez sur **Terminer**.

5. Vous revenez ensuite à la page **Runtimes ciblés** de la boîte de dialogue **Propriétés**. Sélectionnez **Apache Tomcat v7.0**, puis cliquez sur **OK**.

    ![][6]

6. Dans le menu **Exécuter** d’Eclipse, cliquez sur **Exécuter**. Dans la boîte de dialogue **Exécuter en tant que**, sélectionnez **Exécuter sur le serveur**. Dans la boîte de dialogue **Exécuter sur le serveur**, sélectionnez **Serveur Tomcat v7.0** :

    ![][7]

    Cliquez sur **Terminer**.

7. Lorsque l’application s’exécute, la page **JSPHello** doit apparaître dans une fenêtre localhost dans Eclipse (`http://localhost:8080/JSPHello/`), avec le message suivant :

    `Hello World, the time is Tue Mar 24 23:21:10 GMT 2015`


#### Exportation de l’application en tant que fichier WAR

Exportez les fichiers de projet web en tant que fichier d’archive (WAR) web afin de le déployer sur l’application web. Les fichiers de projet web suivants se trouvent dans le dossier WebContent :

    META-INF
    WEB-INF
    index.jsp

1. Cliquez avec le bouton droit sur le dossier WebContent et sélectionnez **Exporter**.

2. Dans la boîte de dialogue **Exporter la sélection**, cliquez sur **Web > WAR**, puis cliquez sur **Suivant**.

3. Dans la boîte de dialogue **Exportation WAR** , sélectionnez le répertoire src dans le projet actuel, et incluez le nom du fichier WAR à la fin. Par exemple :

    `<project-path>/JSPHello/src/JSPHello.war`

Pour plus d’informations sur le déploiement de fichiers WAR, consultez la page [Ajout d’une application à votre site Web Java sur Azure](web-sites-java-add-app.md).


### Déploiement de l’application Hello World à l’aide d’un client FTP

Sélectionnez un client FTP tiers pour publier l’application. Cette procédure décrit deux options : la console Kudu intégrée à Azure ; et FileZilla, un outil populaire présentant une interface utilisateur graphique pratique.

> **Remarque :** le Kit de ressources Azure pour Eclipse prend en charge le déploiement vers les comptes de stockage et les services cloud, mais pas le déploiement sur des applications web pour le moment. Vous pouvez effectuer un déploiement sur des comptes de stockage et des services cloud à l’aide d’un projet de déploiement Azure comme décrit dans [Création d’une application Hello World pour Azure dans Eclipse](http://msdn.microsoft.com/library/azure/hh690944.aspx), mais pas sur des applications web. Utilisez d’autres méthodes telles que FTP ou GitHub pour transférer des fichiers vers votre application web.

> **Remarque :** nous ne recommandons pas d’utiliser le client FTP à partir de l’invite de commandes Windows (l’utilitaire FTP.EXE de ligne de commandes fourni avec Windows). Les clients FTP qui utilisent le mode FTP actif, par exemple FTP.EXE, ne fonctionnent souvent pas sur les pare-feu. Le mode FTP actif spécifie une adresse basée sur le réseau local interne, à laquelle un serveur FTP ne parviendra probablement pas à se connecter.

Pour plus d’informations sur le déploiement vers une application web App Service à l’aide du FTP, consultez les rubriques suivantes :

- [Déployer à l’aide d’un utilitaire FTP](web-sites-deploy.md)


#### Configurer les informations d'identification du déploiement

Assurez-vous que vous avez exécuté l’application **AzureWebDemo** pour créer une application web. Vous allez transférer des fichiers vers cet emplacement.

1. Connectez-vous au Portail Classic et cliquez sur **Applications Web**. Vérifiez que **WebDemoWebApp** apparaît dans la liste des applications web et qu’il est en cours d’exécution. Cliquez sur **WebDemoWebApp** pour ouvrir sa page **Tableau de bord**.

2. Dans la page **Tableau de bord** sous **Aperçu rapide**, cliquez sur **Configurer les informations d’identification du déploiement** (si vous disposez déjà de ces informations, l’option suivante apparaît : **Réinitialisez vos informations d’identification de déploiement**).

    Les informations d'identification du déploiement sont associées à un compte Microsoft. Vous devez spécifier un nom d’utilisateur et un mot de passe que vous pouvez utiliser pour le déploiement à l’aide de Git et de FTP. Vous pouvez utiliser ces informations d’identification pour effectuer un déploiement sur n’importe quelle application web dans tous les abonnements Azure associés à votre compte Microsoft. Fournissez les informations d’identification du déploiement Git et FTP dans la boîte de dialogue, et enregistrez le nom d’utilisateur et le mot de passe en vue d’une utilisation ultérieure.


#### Obtention des informations de connexion FTP

Pour utiliser le FTP pour déployer des fichiers d’application vers l’application web nouvellement créée, vous devez obtenir les informations de connexion. Il existe deux méthodes pour cela. Une façon consiste à visiter la page **Tableau de bord** de l’application web ; l’autre manière consiste à télécharger le profil de publication de l’application web. Le profil de publication est un fichier XML qui fournit des informations telles que le nom d’hôte FTP et les informations d’identification d’ouverture de session pour vos applications web dans Azure App Service. Vous pouvez utiliser ce nom d’utilisateur et ce mot de passe pour effectuer un déploiement sur n’importe quelle application web dans tous les abonnements associés au compte Azure, pas uniquement celui-ci.

Pour obtenir les informations de connexion FTP à partir du panneau de l’application web dans le [portail Azure][] :

1. Sous **Essentials**, recherchez et copiez le **Nom d’hôte FTP**. Il s’agit d’un URI similaire à `ftp://waws-prod-bay-NNN.ftp.azurewebsites.windows.net`.

2. Sous **Essentials**, recherchez et copiez le **Nom d’utilisateur FTP/déploiement**. Il se présente sous la forme *nomappweb\\déploiement-nomutilisateur* ; par exemple `WebDemoWebApp\deployer77`.

Pour obtenir les informations de connexion FTP à partir du profil de publication :

1. Dans le panneau de l’application web, cliquez sur **Obtenir le profil de publication**. Cette action télécharge un fichier .publishsettings sur votre disque local.

2. Ouvrez le fichier .publishsettings dans un éditeur XML ou un éditeur de texte et recherchez l’élément `<publishProfile>` contenant `publishMethod="FTP"`. Il doit se présenter comme suit :

        <publishProfile
            profileName="WebDemoWebApp - FTP"
            publishMethod="FTP"
            publishUrl="ftp://waws-prod-bay-NNN.ftp.azurewebsites.windows.net/site/wwwroot"
            ftpPassiveMode="True"
            userName="WebDemoWebApp\$WebDemoWebApp"
            userPWD="<deployment-password>"
            ...
        </publishProfile>

3. Notez que les paramètres `publishProfile` de l’application web sont mappés vers les paramètres du Gestionnaire de Site FileZilla comme suit :

- `publishUrl` est identique au **Nom d’hôte FTP**, la valeur que vous définissez dans **Hôte**.
- `publishMethod="FTP"` signifie que vous définissez **Protocole** sur **FTP - File Transfer Protocol** et **Chiffrement** sur **Utiliser le FTP simple**.
- `userName` et `userPWD` sont des clés pour les valeurs de nom d’utilisateur et de mot de passe spécifiées lorsque vous réinitialisez les informations d’identification de déploiement. `userName` est identique à **Déploiement / FTP utilisateur**. Elles sont mappées vers **Utilisateur** et **Mot de passe** dans FileZilla.
- `ftpPassiveMode="True"` signifie que le site FTP utilise le transfert FTP passif ; sélectionnez **Passif** sous l’onglet **Paramètres de transfert**.


#### Configuration de l’application web pour héberger une application Java

Avant de publier l’application, vous devez modifier quelques paramètres de configuration afin que l’application web puisse héberger une application Java.

1. Dans le Portail Classic, accédez à la page **Tableau de bord** de l’application web et cliquez sur **Configurer**. Dans la page **Configurer**, spécifiez les paramètres suivants.

2. Dans **Version Java**, la valeur par défaut est **Off** ; sélectionnez la version Java ciblée par votre application cible ; par exemple 1.7.0\_51. Après cela, assurez-vous également que **Conteneur Web** est défini sur une version de Tomcat Server.

3. Dans **Documents par défaut**, ajoutez index.jsp et déplacez-le vers le haut de la liste. (Le fichier par défaut pour les applications web est hostingstart.html.)

4. Cliquez sur **Save**.


#### Publication de votre application à l’aide de Kudu

Un moyen de publier l’application consiste à utiliser la console de débogage Kudu intégrée à Azure. Kudu est réputé comme étant stable et homogène avec App Service Web Apps et Tomcat Server. Vous accédez à la console de l’application web en accédant à une URL sous la forme suivante :

`https://<webappname>.scm.azurewebsites.net/DebugConsole`

1. Pour cette procédure, la console Kudu se trouve à l’adresse suivante ; accédez à cet emplacement :

    `https://webdemowebapp.scm.azurewebsites.net/DebugConsole`

2. Dans le menu principal, sélectionnez **Console de débogage > CMD**.

3. À partir de la ligne de commande de la console, accédez à `/site/wwwroot` (ou cliquez sur `site`, puis sur `wwwroot` dans l’affichage de répertoire en haut de la page) :

    `cd /site/wwwroot`

4. Une fois que vous avez spécifié **Version Java**, Tomcat Server doit créer un répertoire webapps. À partir de la ligne de commande de la console, accédez au répertoire webapps :

    `mkdir webapps`

    `cd webapps`

5. Faites glisser JSPHello.war à partir de `<project-path>/JSPHello/src/` et déposez-le dans l’affichage de répertoire Kudu sous `/site/wwwroot/webapps`. Ne le faites pas glisser vers la zone « Faire glisser pour télécharger et compresser », étant donné que Tomcat le décompressera.

  ![][8]

Au départ, JSPHello.war apparaît dans la zone de répertoire tout seul :

  ![][9]

Après un court délai (probablement moins de 5 minutes), Tomcat Server décompresse le fichier WAR dans un répertoire JSPHello décompressé. Cliquez sur le répertoire ROOT pour voir si index.jsp a été décompressé et copié à cet endroit. Dans ce cas, accédez au répertoire webapps pour voir si le répertoire JSPHello décompressé a été créé. Si vous ne voyez pas ces éléments, attendez et recommencez.

  ![][10]


#### Publication de votre application à l’aide de FileZilla (facultatif)

Un autre outil que vous pouvez utiliser pour publier l’application est FileZilla, un client FTP tiers populaire présentant une interface utilisateur graphique pratique. Vous pouvez télécharger et installer FileZilla à partir de [http://filezilla-project.org/](http://filezilla-project.org/) si vous ne l’avez pas déjà. Pour plus d’informations sur l’utilisation du client, consultez la [Documentation de FileZilla](https://wiki.filezilla-project.org/Documentation) et cette entrée de blog sur les [Clients FTP - 4e partie : FileZilla](http://blogs.msdn.com/b/robert_mcmurray/archive/2008/12/17/ftp-clients-part-4-filezilla.aspx).

1. Dans FileZilla, cliquez sur **Fichier > Gestionnaire de Sites**.
2. Dans la boîte de dialogue **Gestionnaire de Sites**, cliquez sur **Nouveau Site**. Un nouveau site FTP vierge apparaît dans **Sélectionnez une entrée** vous invitant à fournir un nom. Dans le cadre de cette procédure, nommez-le `AzureWebDemo-FTP`.

    Dans l’onglet **Configurer**, spécifiez les paramètres suivants :
    - **Hôte :** Entrez le **Nom d'hôte FTP** que vous avez copié à partir du tableau de bord.
    - **Port :** (Laissez ce champ vide, car il s'agit d'un transfert passif et le serveur déterminera le port à utiliser.)
    - **Protocole :** FTP File Transfer Protocol
    - **Chiffrement :** Utiliser un chiffrement FTP simple
    - **Type d'ouverture de session :** Normal
    - **Utilisateur :** Entrez l’utilisateur FTP / déploiement que vous avez copié à partir du tableau de bord. Il s'agit du nom d'utilisateur FTP complet, qui se présente sous la forme *nomappweb\\nomutilisateur*.
    - **Mot de passe :** Entrez le mot de passe que vous avez spécifié lorsque vous avez défini les informations d'identification de déploiement.

    Sous l’onglet **Paramètres de transfert**, sélectionnez **Passif**.

3. Cliquez sur **Connecter**. Si la connexion aboutit, la console de FileZilla affiche un message `Status: Connected` et émet une commande `LIST` pour répertorier le contenu du répertoire.

4. Dans le volet **Site local**, sélectionnez le répertoire source dans lequel réside le fichier JSPHello.war ; le chemin d’accès est semblable au suivant :

    `<project-path>/JSPHello/src/`

5. Dans le volet **Site distant**, sélectionnez le dossier de destination. Vous allez déployer le fichier WAR dans le répertoire `webapps` sous la racine de l’application web. Accédez à `/site/wwwroot`, cliquez avec le bouton droit sur `wwwroot`, puis sélectionnez **Créer un dossier**. Nommez le répertoire `webapps` et accédez à ce répertoire.

6. Transférez JSPHello.war vers `/site/wwwroot/webapps`. Sélectionnez JSPHello.war dans la liste de fichiers **Local**, cliquez avec le bouton droit dessus et sélectionnez **Télécharger**. Il doit s’afficher dans `/site/wwwroot/webapps`.

7. Une fois que vous avez copié JSPHello.war dans le répertoire webapps, Tomcat Server décompresse automatiquement les fichiers du fichier WAR. Bien que Tomcat Server commence la décompression presque immédiatement, il peut se passer un certain délai (parfois plusieurs heures) avant que les fichiers s’affichent dans le client FTP.


#### Exécution de l’application Hello World dans l’application web

1. Après avoir téléchargé le fichier WAR et vérifié que Tomcat Server a créé un répertoire `JSPHello` décompressé, accédez à `http://webdemowebapp.azurewebsites.net/JSPHello` pour exécuter l’application.

    > **Remarque :** si vous cliquez sur **Parcourir** à partir du Portail Classic, il se peut que la page web par défaut s’affiche avec le message suivant : « Cette application web de type Java a été créée. » Vous devrez peut-être actualiser la page web pour afficher la sortie de l’application au lieu de la page web par défaut.

2. Lorsque l’application s’exécute, une page web s’affiche avec la sortie suivante :

    `Hello World, the time is Tue Mar 24 23:21:10 GMT 2015`


#### Nettoyage des ressources Azure

Cette procédure crée une application web App Service. La ressource vous sera facturée aussi longtemps qu’elle existe. Sauf si vous envisagez de continuer à utiliser l’application web à des fins de test ou de développement, pensez à l’arrêter ou à la supprimer. Une application web qui a été arrêtée continuera à faire l’objet de frais réduits, mais vous pouvez la redémarrer à tout moment. La suppression d’une application web a pour conséquence la suppression de toutes les données que vous avez téléchargées dans celle-ci.

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

  [1]: ./media/java-create-azure-website-using-java-sdk/eclipse-maven-repositories-rebuild-index.png
  [2]: ./media/java-create-azure-website-using-java-sdk/eclipse-new-java-class.png
  [3]: ./media/java-create-azure-website-using-java-sdk/eclipse-new-dynamic-web-project.png
  [4]: ./media/java-create-azure-website-using-java-sdk/eclipse-java-build-path.png
  [5]: ./media/java-create-azure-website-using-java-sdk/eclipse-targeted-runtimes-tomcat-server.png
  [6]: ./media/java-create-azure-website-using-java-sdk/eclipse-targeted-runtimes-properties-page.png
  [7]: ./media/java-create-azure-website-using-java-sdk/eclipse-run-on-server.png
  [8]: ./media/java-create-azure-website-using-java-sdk/kudu-console-drag-drop.png
  [9]: ./media/java-create-azure-website-using-java-sdk/kudu-console-jsphello-war-1.png
  [10]: ./media/java-create-azure-website-using-java-sdk/kudu-console-jsphello-war-2.png
 

[Azure App Service]: http://go.microsoft.com/fwlink/?LinkId=529714
[Web Platform Installer]: http://go.microsoft.com/fwlink/?LinkID=252838
[boîte à outils Azure pour Eclipse]: https://msdn.microsoft.com/library/azure/hh690946.aspx
[Portail Azure Classic]: https://manage.windowsazure.com
[Qu'est-ce qu'un annuaire Azure AD]: http://technet.microsoft.com/library/jj573650.aspx
[Créer et télécharger un certificat de gestion pour Microsoft Azure]: ../cloud-services/cloud-services-certs-create.md
[Outil de gestion de clés et de certificats (keytool)]: http://docs.oracle.com/javase/6/docs/technotes/tools/windows/keytool.html
[WebSiteManagementClient]: http://azure.github.io/azure-sdk-for-java/com/microsoft/azure/management/websites/WebSiteManagementClient.html
[WebSpaceNames]: http://dl.windowsazure.com/javadoc/com/microsoft/windowsazure/management/websites/models/WebSpaceNames.html
[portail Azure]: https://portal.azure.com

<!---HONumber=AcomDC_0302_2016-->