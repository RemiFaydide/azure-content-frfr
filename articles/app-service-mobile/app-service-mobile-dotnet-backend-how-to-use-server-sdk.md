<properties
	pageTitle="Utiliser le Kit de développement logiciel (SDK) de serveur principal .NET pour Azure Mobile Apps | Azure App Service"
	description="Découvrez comment utiliser le Kit de développement logiciel (SDK) de serveur principal .NET pour Azure App Service Mobile Apps."
	keywords="app service, azure app service, application mobile, service mobile, mise à l’échelle, évolutif, déploiement d’application, déploiement d’application azure"
	services="app-service\mobile"
	documentationCenter=""
	authors="ggailey777"
	manager="erikre"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="08/02/2016"
	ms.author="glenga"/>

# Utiliser le Kit de développement logiciel (SDK) de serveur principal .NET pour Azure Mobile Apps

[AZURE.INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

Cette rubrique montre comment utiliser le Kit de développement logiciel (SDK) de serveur principal .NET dans les scénarios Azure App Service Mobile Apps. Le Kit de développement logiciel (SDK) Azure Mobile Apps vous permet de travailler avec des clients mobiles à partir de votre application ASP.NET.

>[AZURE.TIP] Le [Kit de développement logiciel (SDK) serveur .NET pour Azure Mobile Apps][2] est libre de droits sur GitHub. Le référentiel contient l’ensemble du code source, notamment la suite complète de tests unitaires du Kit de développement logiciel (SDK) serveur, et quelques exemples de projets.

## Documentation de référence

La documentation de référence du Kit de développement logiciel (SDK) serveur se trouve ici : [Référence .NET Azure Mobile Apps][1].

## <a name="create-app"></a>Comment : créer un serveur principal d’une application Mobile .NET

Si vous démarrez un nouveau projet, vous pouvez créer une application App Service à l’aide du [portail Azure] ou de Visual Studio. Vous pouvez exécuter l’application App Service en local ou publier le projet sur votre application mobile App Service hébergée sur le cloud.

Si vous ajoutez des fonctionnalités mobiles à un projet existant, consultez la section [Télécharger et initialiser le Kit de développement logiciel (SDK)](#install-sdk).

### Création d’un serveur principal .NET à l’aide du portail Azure

Pour créer un backend mobile App Service, suivez le [didacticiel de démarrage rapide][3] ou procédez comme suit :

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

Dans le panneau _Prise en main_, sous **Créer une table API**, sélectionnez **C#** en tant que **langue du serveur principal**. Cliquez sur **Télécharger**, extrayez les fichiers projets compressés sur votre ordinateur local, puis ouvrez la solution dans Visual Studio.

### Créer un serveur principal .NET à l’aide de Visual Studio 2013 et de Visual Studio 2015

Pour créer un projet Azure Mobile Apps dans Visual Studio, installez le [Kit de développement logiciel (SDK) Azure pour .NET][4] \(version 2.9.0 ou ultérieure). Une fois que vous avez installé le SDK, créez une application ASP.NET en procédant comme suit :

1. Ouvrez la boîte de dialogue **Nouveau projet** (dans le menu *Fichier* > **Nouveau** > **Projet...**).
2. Développez **Modèles** > **Visual C#**, puis sélectionnez **Web**.
3. Sélectionnez **Application web ASP.NET**.
4. Renseignez le nom du projet. Cliquez ensuite sur **OK**.
5. Sous _Modèles ASP.NET 4.5.2_, sélectionnez **Application mobile Azure**. Activez la case à cocher **Hôte dans le cloud** afin de créer un backend mobile dans le cloud pour la publication de ce projet.
6. Cliquez sur **OK**.

## <a name="install-sdk"></a>Télécharger et initialiser le Kit de développement logiciel (SDK)

Le Kit de développement logiciel (SDK) est disponible sur [NuGet.org]. Ce package inclut les fonctionnalités de base requises pour utiliser le Kit de développement logiciel (SDK). Pour initialiser le Kit de développement logiciel (SDK), vous devez effectuer certaines actions sur l’objet **HttpConfiguration**.

### Installer le Kit de développement logiciel (SDK)

Pour installer le Kit de développement logiciel (SDK), cliquez sur le projet de serveur dans Visual Studio, sélectionnez **Gérer les packages NuGet**, recherchez le package [Microsoft.Azure.Mobile.Server], puis cliquez sur **Installer**.

###<a name="server-project-setup"></a> Initialiser le projet de serveur

Un projet de serveur principal .NET est initialisé de la même façon que les autres projets ASP.NET, en incluant une classe de démarrage OWIN. Assurez-vous que vous avez référencé le package NuGet `Microsoft.Owin.Host.SystemWeb`. Pour ajouter cette classe dans Visual Studio, cliquez avec le bouton droit sur votre projet de serveur et sélectionnez **Ajouter** > **Nouvel élément**, puis **web** > **Général** > **Classe de démarrage OWIN**. Une classe est générée avec l’attribut suivant :

    [assembly: OwinStartup(typeof(YourServiceName.YourStartupClassName))]

Dans la méthode `Configuration()` de votre classe de démarrage OWIN, utilisez un objet **HttpConfiguration** pour configurer l’environnement Azure Mobile Apps. L’exemple suivant initialise le projet de serveur sans fonctionnalités supplémentaires :

	// in OWIN startup class
	public void Configuration(IAppBuilder app)
	{
	    HttpConfiguration config = new HttpConfiguration();

	    new MobileAppConfiguration()
	        // no added features
	        .ApplyTo(config);

	    app.UseWebApi(config);
	}

Pour activer des fonctionnalités spécifiques, vous devez appeler les méthodes d’extension sur l’objet **MobileAppConfiguration** avant d’appeler **ApplyTo**. Par exemple, le code suivant ajoute les itinéraires par défaut à tous les contrôleurs d’API avec l’attribut `[MobileAppController]` lors de l’initialisation :

	new MobileAppConfiguration()
	    .MapApiControllers()
	    .ApplyTo(config);

Le démarrage rapide du serveur à partir du portail Azure appelle **UseDefaultConfiguration()**. Cette configuration s’apparente à celle-ci :

		new MobileAppConfiguration()
			.AddMobileAppHomeController()             // from the Home package
			.MapApiControllers()
			.AddTables(                               // from the Tables package
				new MobileAppTableConfiguration()
					.MapTableControllers()
					.AddEntityFramework()             // from the Entity package
				)
			.AddPushNotifications()                   // from the Notifications package
			.MapLegacyCrossDomainController()         // from the CrossDomain package
			.ApplyTo(config);

Les méthodes d’extension utilisées sont les suivantes :

* `AddMobileAppHomeController()` fournit la page d’accueil Azure Mobile Apps par défaut.
* `MapApiControllers()` fournit des fonctionnalités d’API personnalisées pour les contrôleurs WebAPI assortis de l’attribut `[MobileAppController]`.
* `AddTables()` fournit un mappage des points de terminaison `/tables` aux contrôleurs de table.
* `AddTablesWithEntityFramework()` est un raccourci pour le mappage des points de terminaison `/tables` utilisant des contrôleurs basés contrôleurs basés sur Entity Framework.
* `AddPushNotifications()` fournit une méthode simple pour l’inscription d’appareils au service Notification Hubs.
* `MapLegacyCrossDomainController()` fournit des en-têtes CORS standard pour le développement local.

### Extensions de Kit de développement logiciel (SDK)

Les packages d’extension NuGet suivants fournissent différentes fonctionnalités mobiles que votre application peut utiliser. Les extensions s’activent à l’aide de l’objet **MobileAppConfiguration** lors de l’initialisation.

- [Microsoft.Azure.Mobile.Server.Quickstart] prend en charge la configuration de base de Mobile Apps. S’ajoute à la configuration en appelant la méthode d’extension **UseDefaultConfiguration** pendant l’initialisation. Cette extension comprend les extensions suivantes : packages Notifications, Authentication, Entity, Tables, Cross-domain et Home. Ce package est utilisé par le didacticiel de démarrage rapide de Mobile Apps disponible sur le portail Azure.

- [Microsoft.Azure.Mobile.Server.Home](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Home/) implémente la page par défaut *cette application mobile est opérationnelle* pour la racine du site web. S’ajoute à la configuration en appelant la méthode d’extension **AddMobileAppHomeController**.

- [Microsoft.Azure.Mobile.Server.Tables](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Tables/) Inclut des classes pour l’utilisation des données et configure le pipeline de données. S’ajoute à la configuration en appelant la méthode d’extension **AddTables**.

- [Microsoft.Azure.Mobile.Server.Entity](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Entity/) permet à Entity Framework d’accéder aux données de la base de données SQL. S’ajoute à la configuration en appelant la méthode d’extension **AddTablesWithEntityFramework**.

- [Microsoft.Azure.Mobile.Server.Authentication] Active l’authentification et configure l’intergiciel OWIN utilisé pour valider les jetons. Il s’ajoute à la configuration en appelant les méthodes d’extension **AddAppServiceAuthentication** et **IAppBuilder**.**UseAppServiceAuthentication**.

- [Microsoft.Azure.Mobile.Server.Notifications] active les notifications Push et définit un point de terminaison d’inscription Push. S’ajoute à la configuration en appelant la méthode d’extension **AddPushNotifications**.

- [Microsoft.Azure.Mobile.Server.CrossDomain](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.CrossDomain/) crée un contrôleur qui fournit des données aux navigateurs web hérités à partir de votre application mobile. S’ajoute à la configuration en appelant la méthode d’extension **MapLegacyCrossDomainController**.

- [Microsoft.Azure.Mobile.Server.Login] fournit la méthode AppServiceLoginHandler.CreateToken(), qui est une méthode statique utilisée pendant les scénarios d’authentification personnalisés.

## <a name="publish-server-project"></a>Publier le projet de serveur

Cette section vous explique comment publier votre projet de serveur principal .NET à partir de Visual Studio. Vous pouvez également déployer votre projet de serveur principal en utilisant l’une des autres méthodes décrites dans la [Documentation sur le déploiement d’Azure App Service](../app-service-web/web-sites-deploy.md).

1. Avec Visual Studio, développez le projet pour restaurer des packages NuGet.

2. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet, puis cliquez sur **Publier**. La première fois que vous publiez un projet, vous devez définir un profil de publication. Si vous disposez déjà d’un profil, vous pouvez le sélectionner et cliquer sur **Publier**.

2. Si vous êtes invité à sélectionner une cible de publication, cliquez sur **Microsoft Azure App Service** > **Suivant**, puis (si nécessaire) connectez-vous avec vos informations d’identification Azure. Visual Studio récupère vos paramètres de publication depuis Azure et les stocke en sécurité.

	![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-1.png)

3. Choisissez votre **Abonnement**, sélectionnez **Type de ressource** dans **Affichage**, développez **Application mobile** et cliquez sur votre backend Application mobile, puis cliquez sur **OK**.

	![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-2.png)

4. Vérifiez les informations de profil de publication, puis cliquez sur **Publier**.

	![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-3.png)

	Une fois le backend Mobile App publié, une page vous indique que l’opération a réussi.

	![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-success.png)

##<a name="define-table-controller"></a> Définir un contrôleur de table

Définissez un contrôleur de table pour exposer une table SQL aux clients mobiles. La configuration d’un contrôleur de table requiert trois étapes :

1. Créer une classe d’objet de transfert de données (DTO).
2. Configurer une référence de table dans la classe DbContext Mobile.
3. Créer un contrôleur de table.

Un objet de transfert de données (DTO) est un objet C# simple qui hérite de `EntityData`. Par exemple :

    public class TodoItem : EntityData
    {
        public string Text { get; set; }
        public bool Complete {get; set;}
    }

Le DTO est utilisé pour définir la table au sein de la base de données SQL. Pour créer l’entrée de base de données, ajoutez une propriété `DbSet<>` à la classe DbContext que vous utilisez. Dans le modèle de projet par défaut pour Azure Mobile Apps, la classe DbContext est appelée `Models\MobileServiceContext.cs` :

    public class MobileServiceContext : DbContext
    {
        private const string connectionStringName = "Name=MS_TableConnectionString";

        public MobileServiceContext() : base(connectionStringName)
        {

        }

        public DbSet<TodoItem> TodoItems { get; set; }

        protected override void OnModelCreating(DbModelBuilder modelBuilder)
        {
            modelBuilder.Conventions.Add(
                new AttributeToColumnAnnotationConvention<TableColumnAttribute, string>(
                    "ServiceColumnTable", (property, attributes) => attributes.Single().ColumnType.ToString()));
        }
    }

Si vous avez installé le Kit de développement logiciel (SDK) Azure, vous pouvez maintenant créer un modèle de contrôleur de table comme suit :

1. Cliquez avec le bouton droit sur le dossier Contrôleurs et sélectionnez **Ajouter** > **Contrôleur...**.
2. Sélectionnez l’option **Contrôleur de tables dans les applications mobiles Azure**, puis cliquez sur **Ajouter**.
3. Dans la boîte de dialogue **Ajouter un contrôleur** :
    * Dans la liste déroulante **Classe de modèle**, sélectionnez votre nouveau DTO.
    * Dans la liste déroulante **DbContext**, sélectionnez la classe DbContext Mobile Service.
    * Le nom du contrôleur est créé pour vous.
4. Cliquez sur **Add**.

Le projet de serveur de démarrage rapide contient un exemple de classe **TodoItemController** simple.

### Ajuster la taille de pagination des tables

Par défaut, Azure Mobile Apps retourne 50 enregistrements par demande. La pagination permet de s’assurer que le client n’occupe pas son thread d’interface utilisateur ni le serveur pendant trop longtemps et optimise son expérience utilisateur. Pour modifier la taille de pagination des tables, augmentez la « taille de requête autorisée » côté serveur et la taille de page côté client. La « taille de requête autorisée » côté serveur peut être ajustée à l’aide de l’attribut `EnableQuery` :

    [EnableQuery(PageSize = 500)]

Vérifiez que la valeur de PageSize est supérieure ou égale à la taille demandée par le client. Reportez-vous aux procédures de la documentation destinée au client pour savoir comment modifier la taille de pagination pour le client.

## Définir un contrôleur d’API personnalisé

Le contrôleur d’API personnalisé fournit les fonctionnalités de base au serveur principal de votre application mobile en exposant un point de terminaison. Vous pouvez enregistrer un contrôleur d’API mobile spécifique à l’aide de l’attribut [MobileAppController]. L’attribut `MobileAppController`enregistre l’itinéraire, définit le sérialiseur JSON Mobile Apps et active la [vérification de version du client](app-service-mobile-client-and-server-versioning.md).

1. Dans Visual Studio, cliquez avec le bouton droit sur le dossier Contrôleurs, puis cliquez sur **Ajouter** > **Contrôleur**, sélectionnez **web API 2 Controller&mdash;Empty** et cliquez sur **Ajouter**.

2. Spécifiez un **nom de contrôleur**, tel que `CustomController`, puis cliquez sur **Ajouter**.

3. Dans le nouveau fichier de classe de contrôleur, ajoutez l’instruction using suivante :

		using Microsoft.Azure.Mobile.Server.Config;

4. Appliquez l’attribut **[MobileAppController]** à la définition de classe du contrôleur d’API, comme indiqué dans l’exemple suivant :

		[MobileAppController]
		public class CustomController : ApiController
		{
		      //...
		}

4. Dans le fichier App\_Start/Startup.MobileApp.cs, ajoutez un appel à la méthode d’extension **MapApiControllers**, comme dans l’exemple suivant :

		new MobileAppConfiguration()
		    .MapApiControllers()
		    .ApplyTo(config);

Vous pouvez également utiliser la méthode d’extension `UseDefaultConfiguration()` au lieu de `MapApiControllers()`. Tous les contrôleurs auxquels **MobileAppControllerAttribute** n’est pas appliqué restent accessibles aux clients, mais ils peuvent ne pas être utilisés correctement par les clients à l’aide d’un Kit de développement logiciel (SDK) client d’application mobile.

## Utiliser l’authentification

Azure Mobile Apps fait appel à l’authentification/autorisation App Service pour sécuriser votre backend mobile. Cette section vous explique comment effectuer les tâches suivantes liées à l’authentification dans votre projet de serveur principal .NET :

+ [Ajouter l’authentification à un projet de serveur](#add-auth)
+ [Utiliser l’authentification personnalisée pour votre application](#custom-auth)
+ [Récupérer des informations utilisateur authentifiées](#user-info)
+ [Limiter l’accès aux données pour les utilisateurs autorisés](#authorize)

### <a name="add-auth"></a>Ajouter l’authentification à un projet de serveur

Vous pouvez ajouter l’authentification à votre projet de serveur en étendant l’objet **MobileAppConfiguration** et en configurant l’intergiciel OWIN. Lorsque vous installez le package [Microsoft.Azure.Mobile.Server.Quickstart] et appelez la méthode d’extension **UseDefaultConfiguration**, vous pouvez passer directement à l’étape 3.

1. Dans Visual Studio, installez le package [Microsoft.Azure.Mobile.Server.Authentication].

2. Dans le fichier de projet Startup.cs, ajoutez la ligne de code suivante au début de la méthode **Configuration** :

		app.UseAppServiceAuthentication(config);

	Ce composant intergiciel (middleware) OWIN valide les jetons émis par la passerelle App Service associée.

3. Ajoutez l’attribut `[Authorize]` à tous les contrôleurs ou méthodes nécessitant une authentification.

Pour découvrir comment authentifier les clients auprès de votre serveur principal Mobile Apps, consultez la page [Ajouter une authentification à votre application](app-service-mobile-ios-get-started-users.md).

### <a name="custom-auth"></a>Utiliser l’authentification personnalisée pour votre application

Si vous ne souhaitez pas utiliser l’un des fournisseurs d’authentification/d’autorisation App Service, vous pouvez implémenter votre propre système de connexion. Pour faciliter la génération de jetons d’authentification, installez le package [Microsoft.Azure.Mobile.Server.Login]. Fournissez votre propre code pour valider les informations d’identification utilisateur. Par exemple, vous pouvez définir des mots de passe salés et hachés dans une base de données. Dans l’exemple ci-dessous, la méthode `isValidAssertion()` (définie ailleurs) est chargée de ces vérifications.

L’authentification personnalisée est exposée en créant une classe ApiController et en exposant les actions `register` et `login`. Le client doit utiliser une interface utilisateur personnalisée pour collecter les informations auprès de l’utilisateur. Les informations sont ensuite envoyées à l’API avec un appel HTTP POST standard. Une fois que le serveur a validé l’assertion, un jeton est émis à l’aide de la méthode `AppServiceLoginHandler.CreateToken()`. La classe ApiController **ne doit pas** utiliser l’attribut `[MobileAppController]`.

Un exemple d’action `login` :

		public IHttpActionResult Post([FromBody] JObject assertion)
		{
			if (isValidAssertion(assertion)) // user-defined function, checks against a database
			{
				JwtSecurityToken token = AppServiceLoginHandler.CreateToken(new Claim[] { new Claim(JwtRegisteredClaimNames.Sub, assertion["username"]) },
					mySigningKey,
					myAppURL,
					myAppURL,
					TimeSpan.FromHours(24) );
				return Ok(new LoginResult()
				{
					AuthenticationToken = token.RawData,
					User = new LoginResultUser() { UserId = userName.ToString() }
				});
			}
			else // user assertion was not valid
			{
				return this.Request.CreateUnauthorizedResponse();
			}
		}

Dans l’exemple précédent, LoginResult et LoginResultUser sont des objets sérialisables qui exposent les propriétés requises. Le client attend que les réponses de connexion soient renvoyées en tant qu’objets JSON de la forme suivante :

		{
			"authenticationToken": "<token>",
			"user": {
				"userId": "<userId>"
			}
		}

La méthode `AppServiceLoginHandler.CreateToken()` inclut un paramètre _audience_ et un paramètre _émetteur_. Ces deux paramètres sont définis sur l’URL de la racine de votre application à l’aide du schéma HTTPS. De la même manière, vous devez définir _secretKey_ en tant que clé de signature de votre application. Ne distribuez pas la clé de signature dans un client, car elle peut être utilisée pour générer des clés et usurper l’identité d’utilisateurs. Hébergé dans App Service, vous pouvez obtenir la clé de signature en faisant référence à la variable d’environnement _WEBSITE\_AUTH\_SIGNING\_KEY_. Si vous en avez besoin dans un contexte de débogage local, suivez les instructions de la section [Débogage local avec authentification](#local-debug) afin de récupérer la clé et de la stocker en tant que paramètre d’application.

Le jeton émis peut également inclure d’autres revendications et une date d’expiration. Le jeton émis doit inclure au minimum une revendication d’objet (**sub**).

Vous pouvez prendre en charge la méthode cliente `loginAsync()` standard en surchargeant l’itinéraire d’authentification. Si le client appelle `client.loginAsync('custom');` pour se connecter, l’itinéraire doit être `/.auth/login/custom`. Vous pouvez définir l’itinéraire du contrôleur d’authentification personnalisée à l’aide de `MapHttpRoute()` :

    config.Routes.MapHttpRoute("custom", ".auth/login/custom", new { controller = "CustomAuth" });

>[AZURE.TIP] L’utilisation de l’approche `loginAsync()` permet de s’assurer que le jeton d’authentification est joint à chaque appel supplémentaire au service.

###<a name="user-info"></a>Récupérer des informations utilisateur authentifiées

Lorsqu’un utilisateur est authentifié par App Service, vous pouvez accéder à l’ID utilisateur affecté et à d’autres informations dans votre code de serveur principal .NET. Les informations utilisateur peuvent être utilisées pour prendre des décisions d’autorisation sur le backend. Le code suivant récupère l’ID utilisateur associé à une demande :

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

Le SID est dérivé de l’ID utilisateur spécifique au fournisseur et est statique pour un utilisateur donné et un fournisseur de connexion. Le SID a la valeur null pour les jetons d’authentification non valides.

App Service vous permet également de demander des revendications spécifiques à votre fournisseur de connexion. Chaque fournisseur d’identité peut fournir des informations supplémentaires à l’aide du Kit de développement logiciel (SDK) du fournisseur d’identité. Par exemple, vous pouvez utiliser l’API Graph Facebook pour les informations relatives aux amis. Vous pouvez spécifier des revendications qui sont demandées dans le panneau du fournisseur au sein du portail Azure. Certaines revendications nécessitent la définition de paramètres supplémentaires auprès du fournisseur d’identité.

Le code suivant appelle la méthode d’extension **GetAppServiceIdentityAsync** pour obtenir les informations d'identification de connexion, qui incluent l'accès au jeton nécessaire pour effectuer des requêtes par rapport à l’API Graph Facebook :

    // Get the credentials for the logged-in user.
    var credentials =
        await this.User
        .GetAppServiceIdentityAsync<FacebookCredentials>(this.Request);

    if (credentials.Provider == "Facebook")
    {
        // Create a query string with the Facebook access token.
        var fbRequestUrl = "https://graph.facebook.com/me/feed?access_token="
            + credentials.AccessToken;

        // Create an HttpClient request.
        var client = new System.Net.Http.HttpClient();

        // Request the current user info from Facebook.
        var resp = await client.GetAsync(fbRequestUrl);
        resp.EnsureSuccessStatusCode();

        // Do something here with the Facebook user information.
        var fbInfo = await resp.Content.ReadAsStringAsync();
    }

Ajoutez une instruction using pour `System.Security.Principal` afin de fournir la méthode d’extension **GetAppServiceIdentityAsync**.

### <a name="authorize"></a>Limiter l’accès aux données pour les utilisateurs autorisés

Dans la section précédente, nous vous avons montré comment récupérer l’identificateur d’utilisateur d’un utilisateur authentifié. Vous pouvez restreindre l’accès aux données et à d’autres ressources en fonction de cette valeur. Par exemple, l’ajout d’une colonne userId à des tables et le filtrage des résultats de la requête par l’ID utilisateur constituent un moyen simple de limiter les données renvoyées aux utilisateurs autorisés. Le code suivant retourne des lignes de données uniquement lorsque le SID correspond à la valeur dans la colonne UserId de la table TodoItem :

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

    // Only return data rows that belong to the current user.
    return Query().Where(t => t.UserId == sid);

La méthode `Query()` renvoie un paramètre `IQueryable` qui peut être manipulé par LINQ pour gérer le filtrage.

## Ajouter des notifications Push à un projet de serveur

Ajoutez des notifications Push à votre projet de serveur en étendant l’objet **MobileAppConfiguration** et en créant un client Notification Hubs.

1. Dans Visual Studio, cliquez avec le bouton droit sur le projet de serveur, puis cliquez sur **Gérer les packages NuGet**, recherchez `Microsoft.Azure.Mobile.Server.Notifications` et cliquez sur **Installer**.

2. Répétez cette étape pour installer le package `Microsoft.Azure.NotificationHubs`, qui inclut la bibliothèque cliente Notification Hubs.

3. Dans le fichier App\_Start/Startup.MobileApp.cs, ajoutez un appel de la méthode d’extension **AddPushNotifications()** pendant l’initialisation :

		new MobileAppConfiguration()
			// other features...
			.AddPushNotifications()
			.ApplyTo(config);

4. Ajoutez le code suivant pour créer un client Notification Hubs :

        // Get the settings for the server project.
        HttpConfiguration config = this.Configuration;
        MobileAppSettingsDictionary settings =
            config.GetMobileAppSettingsProvider().GetMobileAppSettings();

        // Get the Notification Hubs credentials for the Mobile App.
        string notificationHubName = settings.NotificationHubName;
        string notificationHubConnection = settings
            .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // Create a new Notification Hub client.
        NotificationHubClient hub = NotificationHubClient
            .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

Vous pouvez maintenant utiliser le client Notification Hubs pour envoyer des notifications Push aux appareils inscrits. Pour plus d’informations, consultez [Ajout de notifications Push à votre application](app-service-mobile-ios-get-started-push.md). Pour en savoir plus sur Notification Hubs, consultez l’article [Vue d’ensemble de Notification Hubs](../notification-hubs/notification-hubs-push-notification-overview.md).

##<a name="tags"></a>Activer les notifications Push ciblées à l’aide de balises

Notification Hubs vous permet d’envoyer des notifications ciblées vers des enregistrements spécifiques à l’aide de balises. Plusieurs balises sont créées automatiquement :

* L’ID d’installation identifie un périphérique spécifique.
* L’ID utilisateur, basé sur le SID authentifié, identifie un utilisateur spécifique.

L’ID d’installation est accessible à partir de la propriété **installationId** sur le **MobileServiceClient**. L’exemple suivant montre comment utiliser un ID d’installation pour ajouter une balise à une installation spécifique dans Notification Hubs :

	hub.PatchInstallation("my-installation-id", new[]
	{
	    new PartialUpdateOperation
	    {
	        Operation = UpdateOperationType.Add,
	        Path = "/tags",
	        Value = "{my-tag}"
	    }
	});

Toutes les balises fournies par le client pendant l’inscription aux notifications Push sont ignorées par le backend pendant la création de l’installation. Pour permettre à un client d’ajouter des balises à l’installation, vous devez créer une API personnalisée qui ajoute des balises à l’aide du modèle précédent.

Pour obtenir un exemple, consultez [Client-added push notification tags][5] \(Balises de notification Push ajoutées au client) dans l’exemple de démarrage rapide complet d’App Service Mobile Apps.

##<a name="push-user"></a>Envoyer des notifications Push à un utilisateur authentifié

Quand un utilisateur authentifié s’inscrit aux notifications Push, une balise avec l’ID d’utilisateur est automatiquement ajoutée à l’inscription. Grâce à cette balise, vous pouvez envoyer des notifications Push à tous les appareils inscrits par cette personne. Le code suivant permet d’obtenir le SID de l’utilisateur qui émet la demande et d’envoyer un modèle de notification Push à chaque inscription d’appareil pour cette personne :

    // Get the current user SID and create a tag for the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;
    string userTag = "_UserId:" + sid;

    // Build a dictionary for the template with the item message text.
    var notification = new Dictionary<string, string> { { "message", item.Text } };

    // Send a template notification to the user ID.
    await hub.SendTemplateNotificationAsync(notification, userTag);

Quand vous vous inscrivez à des notifications Push à partir d’un client authentifié, assurez-vous au préalable que l’authentification est bien terminée. Pour plus d’informations, consultez [Envoi de notifications Push aux utilisateurs][6] dans l’exemple de démarrage rapide complet d’App Service Mobile Apps pour le serveur principal .NET.

## Déboguer et dépanner le Kit de développement logiciel (SDK) serveur .NET

Azure App Service fournit plusieurs techniques de débogage et de résolution des problèmes pour les applications ASP.NET.

- [Surveiller les applications web dans Microsoft Azure App Service](../app-service-web/web-sites-monitor.md)
- [Activer la journalisation des diagnostics pour les applications web dans Azure App Service](../app-service-web/web-sites-enable-diagnostic-log.md)
- [Dépanner un service Azure App dans Visual Studio](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md)

### Journalisation

Vous pouvez écrire dans les journaux de diagnostics App Service à l’aide du traçage ASP.NET standard. Avant de pouvoir écrire dans les journaux, vous devez activer les diagnostics de votre serveur principal d’application mobile.

Pour activer les diagnostics et écrire dans les journaux :

1. Suivez les étapes indiquées dans [Activer des diagnostics](../app-service-web/web-sites-enable-diagnostic-log.md#enablediag).

2. Ajoutez l’instruction using suivante dans votre fichier de code :

		using System.Web.Http.Tracing;

3. Créez un writer de suivi pour écrire à partir du serveur principal .NET dans les journaux de diagnostic, comme ceci :

		ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
		traceWriter.Info("Hello, World");

4. Publiez à nouveau votre projet de serveur et accédez au serveur principal d’application mobile pour exécuter le chemin d’accès du code avec la journalisation.

5. Téléchargez et évaluez les fichiers journaux, comme décrit dans [Télécharger des journaux](../app-service-web/web-sites-enable-diagnostic-log.md#download).

### <a name="local-debug"></a>Débogage local avec authentification

Vous pouvez exécuter localement votre application afin de tester les modifications avant de les publier dans le cloud. Pour la plupart des backends Azure Mobile Apps, appuyez sur *F5* lorsque vous êtes dans Visual Studio. Toutefois, certains points spécifiques sont à prendre en compte en lien avec l’authentification.

Vous devez disposer d’une application mobile basée sur le cloud avec l’authentification/l’autorisation App Service configurées, et votre client doit posséder le point de terminaison du cloud spécifié en tant qu’hôte secondaire de connexion. Consultez la documentation associée à votre plateforme cliente pour connaître la procédure appropriée.

Assurez-vous que [Microsoft.Azure.Mobile.Server.Authentication] est installé sur votre backend mobile. Ensuite, dans la classe de démarrage OWIN de votre application, ajoutez les éléments suivants, après que `MobileAppConfiguration` a été appliquée à votre `HttpConfiguration` :

		app.UseAppServiceAuthentication(new AppServiceAuthenticationOptions()
		{
			SigningKey = ConfigurationManager.AppSettings["authSigningKey"],
			ValidAudiences = new[] { ConfigurationManager.AppSettings["authAudience"] },
			ValidIssuers = new[] { ConfigurationManager.AppSettings["authIssuer"] },
			TokenHandler = config.GetAppServiceTokenHandler()
		});

Dans l’exemple précédent, vous devez configurer les paramètres d’application _authAudience_ et _authIssuer_ de votre fichier web.config sur l’URL de la racine de votre application à l’aide du schéma HTTPS. De la même manière, vous devez définir _authSigningKey_ en tant que valeur de clé de signature de votre application. Pour obtenir la clé de signature :

1. Accédez à votre application dans le [portail Azure].
2. Cliquez sur **Outils** > **Kudu** > **Accéder**.
3. Dans le site de gestion Kudu, cliquez sur **Environment** (Environnement).
4. Recherchez la valeur de _WEBSITE\_AUTH\_SIGNING\_KEY_.

Utilisez la clé de signature pour le paramètre _authSigningKey_ dans votre configuration d’application locale. Votre backend mobile est désormais équipé de manière appropriée pour valider lors d’une exécution locale les jetons obtenus par le client à partir du point de terminaison basé sur le cloud.

[1]: https://msdn.microsoft.com/library/azure/dn961176.aspx
[2]: https://github.com/Azure/azure-mobile-apps-net-server
[3]: app-service-mobile-ios-get-started.md
[4]: https://azure.microsoft.com/downloads/
[5]: https://github.com/Azure-Samples/app-service-mobile-dotnet-backend-quickstart/blob/master/README.md#client-added-push-notification-tags
[6]: https://github.com/Azure-Samples/app-service-mobile-dotnet-backend-quickstart/blob/master/README.md#push-to-users
[portail Azure]: https://portal.azure.com
[NuGet.org]: http://www.nuget.org/
[Microsoft.Azure.Mobile.Server]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/
[Microsoft.Azure.Mobile.Server.Quickstart]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Quickstart/
[Microsoft.Azure.Mobile.Server.Authentication]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Authentication/
[Microsoft.Azure.Mobile.Server.Login]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Login/
[Microsoft.Azure.Mobile.Server.Notifications]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Notifications/
[MapHttpAttributeRoutes]: https://msdn.microsoft.com/library/dn479134(v=vs.118).aspx

<!---HONumber=AcomDC_0921_2016-->