<properties 
	pageTitle="FAQ Cache Redis Azure | Microsoft Azure" 
	description="Découvrez les réponses aux questions les plus fréquentes, les modèles et les meilleures pratiques pour Cache Redis Azure." 
	services="redis-cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/21/2016" 
	ms.author="sdanie"/>

# Forum aux questions sur le Cache Redis Azure

Découvrez les réponses aux questions les plus fréquentes, les modèles et les meilleures pratiques pour Cache Redis Azure.


## Que dois-je faire si je n’ai pas trouvé de réponse à ma question ici ?

Si votre question n’est pas répertoriée ici, faites-le nous savoir et nous vous aiderons à trouver une réponse.

-	Vous pouvez publier une question dans le [thread](#comments) à la fin de cette FAQ et collaborer avec l’équipe Azure Cache et d’autres membres de la communauté en cas de question sur cet article.
-	Pour atteindre un public plus large, vous pouvez publier une question sur le [Forum Azure Cache MSDN](https://social.msdn.microsoft.com/forums/azure/home?forum=azurecache) et collaborer avec l’équipe Azure Cache et d’autres membres de la Communauté.
-	Si vous souhaitez effectuer une demande de fonctionnalité, vous pouvez envoyer vos demandes et idées à [Azure Redis Cache User Voice](https://feedback.azure.com/forums/169382-cache).
-	Vous pouvez également nous envoyer un e-mail par le biais de [Azure Cache External Feedback](mailto:azurecache@microsoft.com).

## Principes fondamentaux du Cache Redis Azure

Les Forums aux questions de cette section couvrent des principes fondamentaux du Cache Redis Azure.

-    [Présentation du Cache Redis Azure](#what-is-azure-redis-cache)
-    [Comment commencer avec Cache Redis Azure ?](#how-can-i-get-started-with-azure-redis-cache)

Les Forums aux questions suivants couvrent les concepts de base, et les questions sur le Cache Redis Azure sont traitées dans une des autres sections des Forums aux questions.

-	[Que propose Cache Redis et quelle taille dois-je utiliser ?](#what-redis-cache-offering-and-size-should-i-use)
-	[Quels clients de cache Redis puis-je utiliser ?](#what-redis-cache-clients-can-i-use)
-	[Existe-t-il un émulateur local pour le Cache Redis Azure ?](#is-there-a-local-emulator-for-azure-redis-cache)
-	[Comment surveiller l’intégrité et les performances de mon cache ?](#how-do-i-monitor-the-health-and-performance-of-my-cache)



## Forum aux questions sur la planification

-	[Que propose Cache Redis et quelle taille dois-je utiliser ?](#what-redis-cache-offering-and-size-should-i-use)
-	[Performance du Cache Redis Azure](#azure-redis-cache-performance)
-	[Dans quelle région dois-je placer mon cache ?](#in-what-region-should-i-locate-my-cache)
-	[Combien me coûte le Cache Redis Azure ?](#how-am-i-billed-for-azure-redis-cache)



## Forum aux questions sur le développement

-	[En quoi consistent les options de configuration StackExchange.Redis ?](#what-do-the-stackexchangeredis-configuration-options-do)
-	[Quels clients de cache Redis puis-je utiliser ?](#what-redis-cache-clients-can-i-use)
-	[Existe-t-il un émulateur local pour le Cache Redis Azure ?](#is-there-a-local-emulator-for-azure-redis-cache)
-	[Comment exécuter des commandes Redis ?](#how-can-i-run-redis-commands)
-	[Pourquoi est-ce que le Cache Redis Azure n’a pas de référence de bibliothèque de classes MSDN comme certains autres services Azure ?](#why-doesnt-azure-redis-cache-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services)
-	[Puis-je utiliser le Cache Redis Azure comme un cache de session PHP ?](#can-i-use-azure-redis-cache-as-a-php-session-cache)


## Forum aux questions sur la sécurité

-	[Quand dois-je activer le port non-SSL pour la connexion à Redis ?](#when-should-i-enable-the-non-ssl-port-for-connecting-to-redis)


## Forum aux questions sur la production

-	[Quelles sont les meilleures pratiques en matière de production ?](#what-are-some-production-best-practices)
-	[Quels sont les points à prendre en compte en ce qui concerne les commandes Redis les plus courantes ?](#what-are-some-of-the-considerations-whfr-FRing-common-redis-commands)
-	[Comment puis-je évaluer et tester les performances de mon cache ?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
-	[Informations importantes sur la croissance du pool de threads](#important-details-about-threadpool-growth)
-	[Activation de Garbage Collection sur le serveur pour obtenir un meilleur début sur le client lors de l'utilisation de StackExchange.Redis](#enable-server-gc-to-get-more-throughput-on-the-client-whfr-FRing-stackexchangeredis)


## Forum aux questions sur la surveillance et le dépannage

Le Forum aux questions de cette section couvre des questions courantes liées à la surveillance et au dépannage. Pour plus d’informations sur la surveillance et le dépannage de vos instances de Cache Redis Azure, consultez [Surveillance du cache Redis Azure](cache-how-to-monitor.md) et [Résolution des problèmes du cache Redis Azure](cache-how-to-troubleshoot.md).

-	[Comment surveiller l’intégrité et les performances de mon cache ?](#how-do-i-monitor-the-health-and-performance-of-my-cache)
-	[Mes paramètres de compte de stockage de diagnostic de cache ont été modifiés, que s’est-il passé ?](#my-cache-diagnostics-storage-account-settings-changed-what-happened)
-	[Pourquoi le diagnostic est-il activée pour certains nouveaux caches mais pas pour d’autres ?](#why-is-diagnostics-enabled-for-some-new-caches-but-not-others)
-	[Pourquoi est-ce que je reçois des erreurs d’expiration du délai ?](#why-am-i-seeing-timeouts)
-	[Pourquoi mon client a-t-il été déconnecté du cache ?](#why-was-my-client-disconnected-from-the-cache)


## Forum aux questions sur les offres de cache précédentes

-	[Quelle est l'offre Azure Cache qui me convient ?](#which-azure-cache-offering-is-right-for-me)


### Présentation du Cache Redis Azure

Le Cache Redis Azure se base sur le [cache Redis](http://redis.io) open source connu. Il vous permet d’accéder à un cache Redis sécurisé et dédié, géré par Microsoft et accessible depuis une application dans Azure. Pour une présentation plus détaillée, consultez la page produit [Cache Redis Azure](https://azure.microsoft.com/services/cache/) sur Azure.com.


### Comment commencer avec Cache Redis Azure ?

Il existe plusieurs façons de démarrer avec Cache Redis Azure.

-    Vous pouvez consulter un de nos didacticiels disponibles pour [.NET](cache-dotnet-how-to-use-azure-redis-cache.md), [ASP.NET](cache-web-app-howto.md), [Java](cache-java-get-started.md), [Node.js](cache-nodejs-get-started.md), et [Python](cache-python-get-started.md).
-    Vous pouvez regarder [Comment créer des applications haute performance à l’aide de Cache Redis Microsoft Azure](https://azure.microsoft.com/documentation/videos/how-to-build-high-performance-apps-using-microsoft-azure-cache/).
-    Vous pouvez consulter la documentation client pour les clients qui correspondent au langage de développement de votre projet pour voir comment utiliser Redis. Il existe de nombreux clients Redis qui peuvent être utilisés avec le Cache Redis Azure. Pour obtenir la liste des clients Redis, consultez [http://redis.io/clients](http://redis.io/clients).


Si vous n’avez pas encore de compte Azure, vous pouvez :

-    [Ouvrir un compte Azure gratuitement](/pricing/free-trial/?WT.mc_id=redis_cache_hero). Vous obtenez des crédits que vous pouvez utiliser pour essayer des services Azure payants. Même après que les crédits sont épuisés, vous pouvez conserver le compte et utiliser les services et fonctionnalités Azure gratuits.
-    [Activez les avantages d’abonnement Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero). Votre abonnement MSDN vous donne droit chaque mois à des crédits dont vous pouvez vous servir pour les services Azure payants.


<a name="cache-size"></a>
### Que propose Cache Redis et quelle taille dois-je utiliser ?
Chaque offre Cache Redis Azure propose différents niveaux de **taille**, de **bande passante**, de **haute disponibilité** et de **contrat SLA**.

Voici quelques considérations relatives au choix d’une offre de Cache :

-	**Mémoire** : les niveaux De base et Standard offrent respectivement 250 Mo et 53 Go. Le niveau Premium offre jusqu’à 530 Go, voire plus [sur demande](mailto:wapteams@microsoft.com?subject=Redis%20Cache%20quota%20increase). Pour plus d'informations, consultez [Tarification - Cache Redis Azure](https://azure.microsoft.com/pricing/details/cache/).
-	**Performances réseau** : si vous avez une charge de travail qui nécessite un débit élevé, le niveau Premium offre davantage de bande passante par rapport au niveau Standard ou De base. En outre, à chaque niveau les caches de taille supérieure offrent davantage de bande passante en raison de la machine virtuelle sous-jacente qui héberge le cache. Pour plus d’informations, consultez le [tableau suivant](#cache-performance).
-	**Débit** : le niveau Premium offre le débit maximal disponible. Si le client ou le serveur de cache atteint la limite de bande passante, vous recevez des erreurs d’expiration du côté client. Pour plus d’informations, consultez le tableau suivant.
-	**Haute disponibilité/contrat SLA** : le cache Redis Azure garantit la disponibilité d’un cache Standard/Premium à au moins 99,9 % du temps. Pour en savoir plus sur notre contrat SLA, consultez [Tarification - Cache Redis Azure](https://azure.microsoft.com/support/legal/sla/cache/v1_0/). Le contrat SLA couvre uniquement la connectivité aux points de terminaison du Cache. Le contrat SLA ne couvre pas la protection contre la perte de données. Nous vous recommandons d’utiliser la fonctionnalité de persistance des données Redis disponible au niveau Premium pour accroître la résilience contre la perte de données.
-	**Persistance des données Redis** : le niveau Premium vous permet de conserver les données du cache dans un compte de stockage Azure. Dans un cache De base/Standard, toutes les données sont stockées uniquement dans la mémoire. En cas de problème lié à l’infrastructure sous-jacente, il existe un risque de perte de données. Nous vous recommandons d’utiliser la fonctionnalité de persistance des données Redis disponible au niveau Premium pour accroître la résilience contre la perte de données. Le Cache Redis Azure propose des options RDB et AOF (à venir) de persistance Redis. Pour plus d’informations, consultez [Comment configurer la persistance pour un cache Redis Azure Premium](cache-how-to-premium-persistence.md).
-	**Cluster Redis** : pour créer des caches de plus de 53 Go ou partager des données parmi plusieurs nœuds Redis, vous pouvez utiliser le clustering Redis disponible au niveau Premium. Chaque nœud se compose d’une paire de caches principal/réplica pour la haute disponibilité. Pour plus d’informations, consultez [Comment configurer le clustering Redis pour un cache Redis Azure Premium](cache-how-to-premium-clustering.md).
-	**Amélioration de l’isolement réseau et de la sécurité** : le déploiement d’Azure Virtual Network offre une sécurité et un isolement renforcés pour votre cache Redis Azure, ainsi que des sous-réseaux, des stratégies de contrôle d’accès et d’autres fonctionnalités permettant de restreindre davantage l’accès. Pour plus d’informations, consultez [Comment configurer la prise en charge des réseaux virtuels pour un cache Redis Azure Premium](cache-how-to-premium-vnet.md).
-	**Configurer Redis** : aux niveaux Standard et Premium, vous pouvez configurer Redis pour les notifications d’espace de clés.
-	**Nombre maximal de connexions client**: le niveau Premium offre le nombre maximal de clients pouvant se connecter à Redis, avec un nombre de connexions plus élevé pour les caches de taille supérieure. [Pour plus d’informations, consultez la page sur la tarification](https://azure.microsoft.com/pricing/details/cache/).
-	**Noyau dédié pour le serveur Redis** : au niveau Premium, toutes les tailles de cache ont un noyau dédié pour Redis. Aux niveaux De base/Standard, les tailles C1 et supérieures ont un noyau dédié pour le serveur Redis.
-	**Redis étant monothread**, le fait d’avoir plus de deux cœurs n’offre aucun avantage supplémentaire, mais les machines virtuelles de grande taille ont généralement plus de bande passante que les plus petites. Si le client ou le serveur de cache atteint la limite de bande passante, vous recevez des erreurs d’expiration du côté client.
-	**Améliorations des performances** : les caches au niveau Premium sont déployés sur du matériel qui dispose de processeurs plus rapides et offrent de meilleures performances par rapport au niveau De base ou Standard. Les caches de niveau Premium ont un débit supérieur et des latences moindres.

<a name="cache-performance"></a>
### Performance du Cache Redis Azure

Le tableau suivant présente les valeurs maximales de bande passante observées lors de tests exécutés sur différentes tailles de caches Standard et Premium à l’aide de `redis-benchmark.exe` à partir d’une machine virtuelle IaaS sur le point de terminaison du Cache Redis Azure. Notez que ces valeurs ne sont pas garanties et qu’il n’y a pas de contrat SLA pour ces chiffres, mais ils sont à peu près normaux. Vous devez tester la charge de votre application pour déterminer la taille de cache adaptée.

De ce tableau, nous pouvons tirer les conclusions suivantes.

-	Le débit des caches de taille identique est plus élevé dans le niveau Premium que dans le niveau Standard. Par exemple, pour un cache de 6 Go, le débit de P1 est de 140 000 demandes par seconde (RPS), contre 49 000 dans le cas de C3.
-	Avec le clustering Redis, le débit augmente de façon linéaire à mesure que vous augmentez le nombre de partitions (nœuds) dans le cluster. Par exemple, si vous créez un cluster P4 de 10 partitions, le débit disponible est alors de 250 000 * 10 = 2,5 millions de demandes par seconde.
-	Pour les tailles de clé supérieures, le débit du niveau Premium est plus élevé que celui du niveau Standard.

| Niveau tarifaire | Taille | Cœurs d’unité centrale | Bande passante disponible | Taille de clé de 1 Ko |
|--------------------------|--------|-----------|--------------------------------------------------------|------------------------------------------|
| **Tailles de cache Standard** | | | **Mégabits par seconde (Mbit/s) / mégaoctets par seconde (Mo/s)** | **Demandes par seconde (RPS)** |
| C0 | 250 Mo | Partagé | 5 / 0,625 | 600 |
| C1 | 1 Go | 1 | 100 / 12,5 | 12 200 |
| C2 | 2,5 Go | 2 | 200 / 25 | 24 000 |
| C3 | 6 Go | 4 | 400 / 50 | 49 000 |
| C4 | 13 Go | 2 | 500 / 62,5 | 61 000 |
| C5 | 26 Go | 4 | 1 000 / 125 | 115 000 |
| C6 | 53 Go | 8 | 2 000 / 250 | 150 000 |
| **Tailles de cache Premium** | | **Cœurs de processeur par partition** | | **Demandes par seconde (RPS), par partition** |
| P1 | 6 Go | 2 | 1 000 / 125 | 140 000 |
| P2 | 13 Go | 4 | 2 000 / 250 | 220 000 |
| P3 | 26 Go | 4 | 2 000 / 250 | 220 000 |
| P4 | 53 Go | 8 | 4 000 / 500 | 250 000 |


Pour obtenir des instructions sur le téléchargement des outils Redis comme `redis-benchmark.exe`, consultez la section [Comment exécuter des commandes Redis ?](#cache-commands).

<a name="cache-region"></a>
### Dans quelle région dois-je placer mon cache ?

Pour optimiser les performances et la latence, placez votre Cache Redis Azure dans la même région que celle de votre application de client de cache.

<a name="cache-billing"></a>
### Combien me coûte le Cache Redis Azure ?

La tarification du cache Redis Azure est disponible [ici](https://azure.microsoft.com/pricing/details/cache/). La page Tarifs appliqués répertorie les tarifs appliqués à un taux horaire. Les caches sont facturés à la minute, de la création du cache jusqu’à sa suppression. Aucune option ne vous permet d’arrêter ou de suspendre la facturation d'un cache.


<a name="cache-configuration"></a>
### En quoi consistent les options de configuration StackExchange.Redis ?

StackExchange.Redis présente de nombreuses options. Cette section présente certains des paramètres les plus courants. Pour plus d’informations sur les options de StackExchange.Redis, consultez la page [Configuration StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md).

ConfigurationOptions|Description|Recommandation
---|---|---
AbortOnConnectFail|Lorsque la valeur est True, la connexion n’est pas rétablie après une panne réseau.|La valeur False laisse StackExchange.Redis se reconnecter automatiquement .
ConnectRetry|Nombre de tentatives de connexion pendant la connexion initiale.| Reportez-vous aux notes suivantes. |
ConnectTimeout|Délai d’expiration en millisecondes pour les opérations de connexion.| Reportez-vous aux notes suivantes. |

Dans la plupart des cas, les valeurs par défaut du client sont suffisantes. Vous pouvez affiner les options en fonction de votre charge de travail.

-	**Nouvelle tentatives**
	-	Pour ConnectRetry et ConnectTimeout, la recommandation générale consiste à échouer rapidement, puis à réessayer. Cela dépend de votre charge de travail et de combien de temps en moyenne votre client prend pour émettre une commande Redis et recevoir une réponse.
	-	Laissez StackExchange.Redis se reconnecter automatiquement au lieu de vérifier l’état de la connexion et de vous reconnecter vous-même. **Évitez d’utiliser la propriété ConnectionMultiplexer.IsConnected**.
	-	Effet boule de neige : vous pouvez parfois rencontrer un problème, vous réessayez, mais le problème ne fait que grossir et n’est jamais résolu. Dans ce cas, utilisez un algorithme de nouvelle tentative exponentiel, comme décrit dans l’article [Conseils généraux sur les nouvelles tentatives](best-practices-retry-general.md), publié par le groupe Microsoft Patterns & Practices.
-	**Valeurs de délai d’expiration**
	-	Réfléchissez à votre charge de travail et définissez les valeurs en conséquence. Si vous stockez des valeurs élevées, définissez le délai sur une valeur plus élevée.
	-	Définissez `AbortOnConnectFail` sur False et laissez StackExchange.Redis se reconnecter pour vous.
	-	Utilisez une seule instance de ConnectionMultiplexer pour l’application. Vous pouvez utiliser une LazyConnection pour créer une instance unique qui est renvoyée par une propriété Connection, comme indiqué dans [Connexion au cache avec la classe ConnectionMultiplexer](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).
	-	Définissez la propriété `ConnectionMultiplexer.ClientName` sur un nom d’instance unique pour faciliter le diagnostic.
	-	Utilisez plusieurs instances `ConnectionMultiplexer` pour les charges de travail personnalisées.
	-	Vous pouvez suivre ce modèle si vous savez que la charge de votre application est variable. Par exemple :
	-	Vous pouvez avoir un multiplexeur pour gérer les clés de grande taille.
	-	Vous pouvez avoir un multiplexeur pour gérer les clés de petite taille.
	-	Vous pouvez définir différentes valeurs pour l’expiration du délai et la logique des nouvelles tentatives pour chaque ConnectionMultiplexer que vous utilisez.
	-	Définissez la propriété `ClientName` de chaque multiplexeur pour faciliter le diagnostic.
	-	Cela permet d’obtenir une latence optimisée par `ConnectionMultiplexer`.

### Quels clients de cache Redis puis-je utiliser ?

L’un des grands avantages de Redis est qu’il existe de nombreux clients prenant en charge de nombreux langages de développement différents. Pour obtenir la liste actuelle des clients, consultez la page des [clients Redis](http://redis.io/clients). Pour des didacticiels qui couvrent plusieurs langues et clients, consultez [Utilisation du Cache Redis Azure](cache-dotnet-how-to-use-azure-redis-cache.md), puis cliquez sur la langue souhaitée à partir du sélecteur de langue en haut de l’article.

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

<a name="cache-emulator"></a>
### Existe-t-il un émulateur local pour le Cache Redis Azure ?

Il n’existe aucun émulateur local pour le Cache Redis Azure, mais vous pouvez exécuter la version MSOpenTech de redis-server.exe à partir des [outils de ligne de commande Redis](https://github.com/MSOpenTech/redis/releases/) sur votre ordinateur local et vous y connecter pour obtenir un résultat similaire à celui d’un émulateur de cache local, comme illustré dans l’exemple suivant.


	private static Lazy<ConnectionMultiplexer>
  		lazyConnection = new Lazy<ConnectionMultiplexer>
	    (() =>
	    {
		    // Connect to a locally running instance of Redis to simulate a local cache emulator experience.
		    return ConnectionMultiplexer.Connect("127.0.0.1:6379");
	    });
	
	    public static ConnectionMultiplexer Connection
	    {
		    get
		    {
			    return lazyConnection.Value;
		    }
	    }


Vous pouvez éventuellement configurer un fichier [redis.conf](http://redis.io/topics/config) correspondant mieux aux [paramètres de cache par défaut](cache-configure.md#default-redis-server-configuration) de votre Cache Redis Azure en ligne le cas échéant.

<a name="cache-commands"></a>
### Comment exécuter des commandes Redis ?

Vous pouvez utiliser les commandes répertoriées dans [Commandes Redis](http://redis.io/commands#), à l’exception de celles répertoriées dans [Commandes Redis non prises en charge dans le Cache Redis Azure](cache-configure.md#redis-commands-not-supported-in-azure-redis-cache). Vous disposez de plusieurs options pour exécuter des commandes Redis.

-	Si vous avez un cache Standard ou Premium, vous pouvez exécuter les commandes Redis à l’aide de la [console Redis](cache-configure.md#redis-console). Elle offre un moyen sécurisé d'exécuter des commandes Redis dans le portail Azure.
-	Vous pouvez aussi utiliser les outils en ligne de commande Redis. Pour ce faire, effectuez les étapes suivantes.
-	Téléchargez les [outils en ligne de commande Redis](https://github.com/MSOpenTech/redis/releases/).
-	Connectez-vous au cache avec `redis-cli.exe`. Transmettez le point de terminaison du cache à l’aide du commutateur -h et la clé à l’aide de -a, comme indiqué dans l’exemple suivant.
-	`redis-cli -h <your cache="" name="">
.redis.cache.windows.net -a <key>
  `
  -	Notez que les outils Redis en ligne de commande ne fonctionnent pas avec le port SSL, mais vous pouvez utiliser un utilitaire comme `stunnel` pour connecter en toute sécurité les outils au port SSL en suivant les instructions du billet de blog [Annonce du fournisseur d’état de session ASP.NET pour la version préliminaire de Redis](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx).

<a name="cache-reference"></a>
### Pourquoi est-ce que le Cache Redis Azure n’a pas de référence de bibliothèque de classes MSDN comme certains autres services Azure ?

Le Cache Redis Microsoft Azure est basé sur le Cache Redis open source connu et est accessible par un large éventail de [clients Redis](http://redis.io/clients) disponibles pour nombreux langages de programmation. Chaque client possède sa propre API qui effectue des appels à l’instance de cache Redis à l’aide de [commandes Redis](http://redis.io/commands).

Étant donné que chaque client est différent, il n’y a pas de référence centralisée au sujet des classes sur MSDN. Chaque client a sa propre documentation de référence. En plus de la documentation de référence, il existe plusieurs didacticiels qui montrent comment débuter avec le Cache Redis Azure avec plusieurs langues et clients. Pour accéder à ces didacticiels, consultez [Utilisation du Cache Redis Azure](cache-dotnet-how-to-use-azure-redis-cache.md), puis cliquez sur la langue souhaitée à partir du sélecteur de langue en haut de l’article.


### Puis-je utiliser le Cache Redis Azure comme un cache de session PHP ?

Oui, pour utiliser le Cache Redis Azure comme un cache de session PHP, pointez la chaîne de connexion vers votre instance de Cache Redis Azure dans `session.save_path`.

>[AZURE.IMPORTANT] Lorsque vous utilisez le Cache Redis Azure comme un cache de session PHP, vous devez coder l’URL de la clé de sécurité utilisée pour se connecter au cache, comme illustré dans l’exemple suivant.
>
>`session.save_path = "tcp://mycache.redis.cache.windows.net:6379?auth=<url encoded primary or secondary key here>";`
>
>Si la clé n’est pas codée par URL, vous pouvez recevoir une exception semblable à ce qui suit : `Failed to parse session.save_path`

Pour plus d’informations sur l’utilisation du Cache Redis comme un cache de session PHP avec le client PhpRedis, consultez [Gestionnaire PHP Session](https://github.com/phpredis/phpredis#php-session-handler).



<a name="cache-ssl"></a>
### Quand dois-je activer le port non-SSL pour la connexion à Redis ?

Le serveur Redis ne prend pas en charge SSL dès le départ, contrairement au Cache Redis Azure. Si vous vous connectez à un Cache Redis Azure et que votre client est compatible SSL, par exemple StackExchange.Redis, vous devez utiliser SSL.

Notez que le port non-SSL est désactivé par défaut pour les nouvelles instances Cache Redis Azure. Si votre client ne prend pas en charge SSL, vous devez activer le port non-SSL en suivant les instructions de la section [Ports d’accès](cache-configure.md#access-ports) de l’article [Configuration d’un cache dans le Cache Redis Azure](cache-configure.md).

Les outils Redis, comme `redis-cli`, ne fonctionnent pas avec le port SSL, mais vous pouvez utiliser un utilitaire comme `stunnel` pour connecter en toute sécurité les outils au port SSL en suivant les instructions du billet de blog [Annonce du fournisseur d’état de session ASP.NET pour la version préliminaire de Redis](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx).

Pour obtenir des instructions sur le téléchargement des outils Redis, consultez la section [Comment exécuter des commandes Redis ?](#cache-commands).



### Quelles sont les meilleures pratiques en matière de production ?

-	[Meilleures pratiques StackExchange.Redis](#stackexchangeredis-best-practices)
-	[Configuration et concepts](#configuration-and-concepts)
-	[Tests de performances](#performance-testing)

#### Meilleures pratiques StackExchange.Redis

-	Définissez `AbortConnect` sur false, puis laissez le ConnectionMultiplexer se reconnecter automatiquement. [Vous trouverez plus d’informations ici](https://gist.github.com/JonCole/36ba6f60c274e89014dd#file-se-redis-setabortconnecttofalse-md).
-	Réutilisez le ConnectionMultiplexer, ne créez pas de nouvelle instance pour chaque requête. Le modèle `Lazy<ConnectionMultiplexer>` [illustré ici](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache) est fortement recommandé.
-	Redis fonctionne de manière optimale avec des valeurs plus petites et il est donc recommandé de fractionner les données plus volumineuses dans plusieurs clés. Dans [cette discussion Redis](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ), 100 ko est considéré comme « volumineux ». Listez [cet article](https://gist.github.com/JonCole/db0e90bedeb3fc4823c2#large-requestresponse-size) illustrant un exemple de problème pouvant être provoqué par des valeurs élevées.
-	Configurez vos [paramètres ThreadPool](#important-details-about-threadpool-growth) pour éviter les délais d’attente.
-	Utilisez au moins la valeur par défaut connectTimeout de 5 secondes. Cette valeur laisse à StackExchange.Redis suffisamment de temps pour rétablir la connexion en cas de blocage du réseau.
-	Gardez à l’esprit les coûts de performances associés aux différentes opérations que vous exécutez. Par exemple, la commande `KEYS` est une opération O(n) et doit être évitée. Le [site redis.io](http://redis.io/commands/) fournit plus d’informations sur la complexité de temps de chaque opération prise en charge. Cliquez sur chaque commande pour afficher la complexité de chaque opération.

#### Configuration et concepts

-	Utilisez le niveau Standard ou Premium pour les systèmes de production. Le niveau De base est un système à nœud unique, sans réplication des données ni contrat SLA. En outre, utilisez au moins un cache C1. Les caches C0 s’appliquent tout particulièrement aux scénarios de développement/test simples.
-	N’oubliez pas que Redis est un magasin de données **In-Memory**. Lisez [cet article](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) pour identifier les scénarios où une perte de données peut se produire.
-	Développez votre système de telle sorte qu’il puisse gérer les problèmes de connexion [liés à une mise à jour corrective et à un basculement](https://gist.github.com/JonCole/317fe03805d5802e31cfa37e646e419d#file-azureredis-patchingexplained-md).

#### Tests de performances

-	Commencez par utiliser `redis-benchmark.exe` pour avoir une idée du débit possible avant d’effectuer vos propres tests de performances. Notez que redis-benchmark ne prend pas en charge SSL et vous devez [activer le port non SSL via le portail Azure](cache-configure.md#access-ports) avant d’exécuter le test. Par exemple, consultez la rubrique [Comment puis-je évaluer et tester les performances de mon cache ?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
-	La machine virtuelle cliente utilisée pour le test doit figurer dans la même région que votre instance de cache Redis.
-	Nous recommandons d’utiliser des machines virtuelles Dv2 pour votre client car elles disposent d’un matériel plus performant et fournissent de meilleurs résultats.
-	Assurez-vous que la machine virtuelle cliente que vous choisissez possède au moins autant de puissance de calcul et de bande passante que le cache que vous testez.
-	Activez VRSS sur la machine cliente si vous êtes sous Windows. [Vous trouverez plus d’informations ici](https://technet.microsoft.com/library/dn383582.aspx).
-	Les instances Redis de niveau Premium offriront une meilleure latence de réseau et un débit supérieur car elles s’exécutent sur un matériel plus performant en termes de processeur et de réseau.

<a name="cache-redis-commands"></a>
### Quels sont les points à prendre en compte en ce qui concerne les commandes Redis les plus courantes ?

-	Vous ne devez pas exécuter certaines commandes Redis qui prennent un certain temps sans comprendre l’impact qu’ont ces commandes.
	-	Par exemple, n’exécutez pas la commande [KEYS](http://redis.io/commands/keys) en production, car elle peut prendre beaucoup de temps à renvoyer une réponse en fonction du nombre de clés. Redis est un serveur à thread unique qui traite une commande à la fois. Si vous avez émis d’autres commandes après KEYS, elles ne sont pas traitées tant que Redis traite la commande KEYS. Le [site redis.io](http://redis.io/commands/) fournit plus d’informations sur la complexité de temps de chaque opération prise en charge. Cliquez sur chaque commande pour afficher la complexité de chaque opération.
-	Taille des clés : dois-je utiliser des petites clés/valeurs ou de grandes clés/valeurs ? En général, cela dépend du scénario. Si votre scénario requiert des clés plus longues, vous pouvez ajuster les valeurs ConnectionTimeout et les valeurs des nouvelles tentatives et adapter votre logique de nouvelles tentatives. Du point de vue du serveur Redis, les plus petites valeurs sont celles qui présentent les meilleures performances.
-	Cela ne veut pas dire que vous ne pouvez pas stocker de plus grandes valeurs dans Redis. Vous devez simplement être au fait de ces informations. Les temps de latence seront plus élevés. Si vous avez un jeu de données plus grand et un autre plus petit, vous pouvez utiliser plusieurs instances ConnectionMultiplexer, chacune configurée avec un ensemble différent de valeurs de délai d’expiration et de nouvelles tentatives, comme décrit dans la section précédente [En quoi consistent les options de configuration StackExchange.Redis](#cache-configuration).



<a name="cache-benchmarking"></a>
### Comment puis-je évaluer et tester les performances de mon cache ?

-	[Activez les diagnostics du cache](cache-how-to-monitor.md#enable-cache-diagnostics) afin de pouvoir [surveiller](cache-how-to-monitor.md) l’intégrité de votre cache. Vous pouvez afficher les mesures dans le portail Azure, et [les télécharger et les analyser](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) à l’aide des outils de votre choix.
-	Vous pouvez utiliser redis-benchmark.exe pour tester la charge de votre serveur Redis.
-	Assurez-vous que le client de test de la charge et le cache Redis se trouvent dans la même région.
-	Utilisez redis-cli.exe et surveillez le cache à l’aide de la commande INFO.
-	Si votre charge provoque une fragmentation élevée de la mémoire, vous devez augmenter la taille de votre cache.
-	Pour obtenir des instructions sur le téléchargement des outils Redis, consultez la section [Comment exécuter des commandes Redis ?](#cache-commands).

Voici un exemple d'utilisation de redis-benchmark.exe. Pour obtenir des résultats précis, exécutez cette commande à partir d’une machine virtuelle située dans la même région que votre cache.

-	Test de requêtes SET en pipeline à l’aide d’une charge utile de 1 ko

    redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t SET -n 1000000 -d 1024 -P 50
	
-	Testez des requêtes GET en pipeline à l’aide d’une charge utile de 1 ko. REMARQUE : Exécutez le jeu SET indiqué ci-dessus pour remplir le cache
	
    redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t GET -n 1000000 -d 1024 -P 50

<a name="threadpool"></a>
### Informations importantes sur la croissance du pool de threads

Le pool de threads CLR comporte deux types de threads : « Worker » et « I/O Completion Port » (IOCP).

-	Les threads Worker sont utilisés notamment pour le traitement des méthodes `Task.Run(…)` ou `ThreadPool.QueueUserWorkItem(…)`. Ces threads sont également utilisés par différents composants dans le CLR lorsqu’une tâche doit être effectuée sur un thread en arrière-plan.
-	Les threads IOCP sont utilisés dans le cas d’E/S asynchrones (lecture à partir du réseau, par exemple).

Le pool de threads fournit de nouveaux threads Worker ou IOCP à la demande (sans aucune limitation) jusqu’à ce qu’il atteigne le paramètre « Minimum » pour chaque type de thread. Par défaut, le nombre minimal de threads est défini sur le nombre de processeurs d’un système.

Une fois que le nombre de threads existants (occupés) atteint le nombre « minimal » de threads, le pool de threads limite le taux d’injection des nouveaux threads à hauteur d’un thread toutes les 500 millisecondes. Autrement dit, si votre système reçoit une rafale de tâches nécessitant un thread IOCP, la tâche sera traitée très rapidement. Toutefois, si le nombre de tâches est supérieur au paramètre « Minimum » configuré, le traitement de certaines tâches sera retardé car le ThreadPool attendra l’une ou l’autre des conditions suivantes :

1. Un thread existant se libère pour traiter la tâche.
1. Aucun thread existant ne se libère pendant 500 ms, auquel cas un nouveau thread est créé.

En fait, cela signifie que lorsque le nombre de threads occupés est supérieur au nombre minimum de threads, vous devrez probablement subir un délai de 500 ms avant que le trafic réseau ne soit traité par l’application. Il est également important de noter que lorsqu’un thread existant reste inactif pendant plus de 15 secondes (d’après mes souvenirs), il sera nettoyé et ce cycle de croissance et de diminution peut se répéter.

Observons cet exemple de message d’erreur de StackExchange.Redis (build 1.0.450 ou version ultérieure) ; vous voyez que les statistiques de pool de threads s’affichent désormais (voir les détails IOCP et WORKER ci-dessous).

	System.TimeoutException: Timeout performing GET MyKey, inst: 2, mgr: Inactive, 
	queue: 6, qu: 0, qs: 6, qc: 0, wr: 0, wq: 0, in: 0, ar: 0, 
	IOCP: (Busy=6,Free=994,Min=4,Max=1000), 
	WORKER: (Busy=3,Free=997,Min=4,Max=1000)

Dans l’exemple ci-dessus, vous pouvez voir que 6 threads sont occupés pour le thread IOCP et que le système est configuré pour autoriser au minimum 4 threads. Dans ce cas, le client subirait probablement deux retards de 500 ms car 6 > 4.

Notez que StackExchange.Redis peut atteindre les délais d’expiration si la croissance des threads IOCP ou WORKER est limitée.

### Recommandation

Par conséquent, nous recommandons fortement aux clients de définir la valeur de configuration minimale pour les threads IOCP et WORKER sur une valeur supérieure à la valeur par défaut. Il est impossible de recommander une valeur unique car une valeur parfaitement adaptée à une application sera certainement trop élevée ou trop faible pour une autre application. Ce paramètre peut également avoir un impact sur les performances d’autres composants d’applications complexes, ce qui signifie que chaque client doit ajuster ce paramètre en fonction de ses besoins spécifiques. Le mieux est de commencer à 200 ou 300, puis de tester et d’ajuster cette valeur en fonction des besoins.

Configuration de ce paramètre :

-	Dans ASP.NET, utilisez le [paramètre de configuration « minIoThreads »][] sous l’élément de configuration `<processModel>` dans le fichier web.config. Si vous travaillez à l’intérieur de sites Web Azure, ce paramètre n’est pas exposé via les options de configuration. Vous pouvez toutefois le définir par programmation (voir ci-dessous) à partir de votre méthode Application\_Start dans global.asax.cs.

> **Remarque importante :** la valeur spécifiée dans cet élément de configuration est un paramètre applicable *par cœur*. Par exemple, si vous utilisez un ordinateur 4 cœurs et que vous souhaitez définir votre paramètre minIOThreads sur 200 au moment de l’exécution, vous utilisez `<processModel minIoThreads="50"/>`.

-	En dehors d’ASP.NET, utilisez l’API [ThreadPool.SetMinThreads(...)](https://msdn.microsoft.com/library/system.threading.threadpool.setminthreads.aspx).

<a name="server-gc"></a>
### Activation de Garbage Collection sur le serveur pour obtenir un meilleur début sur le client lors de l'utilisation de StackExchange.Redis

L'activation de Garbage Collection sur le serveur peut optimiser le client, fournir des performances et un débit optimaux lors de l'utilisation de StackExchange.Redis. Pour plus d'informations sur Garbage Collection du serveur et son activation, consultez les articles suivants.

-	[Activation de Garbage Collection sur le serveur](https://msdn.microsoft.com/library/ms229357.aspx)
-	[Principes de base du Garbage Collection](https://msdn.microsoft.com/library/ee787088.aspx)
-	[Garbage Collection et performances](https://msdn.microsoft.com/library/ee851764.aspx)







<a name="cache-monitor"></a>
### Comment surveiller l’intégrité et les performances de mon cache ?

Les instances du cache Redis Microsoft Azure peuvent être surveillées dans le [portail Azure](https://portal.azure.com). Vous pouvez afficher les mesures, épingler des graphiques de mesure au Tableau d’accueil, personnaliser la plage de date et d’heure des graphiques de surveillance, ajouter et supprimer des mesures dans les graphiques et définir des alertes lorsque certaines conditions sont remplies. Pour plus d’informations, voir [Surveillance du cache Redis Azure](cache-how-to-monitor.md).

La section **Support + dépannage** du panneau **Paramètres** du cache Redis contient également plusieurs outils pour surveiller et dépanner vos caches.

-	**Dépannage** fournit des informations sur les problèmes courants et les stratégies de résolutions associées.
-	**Journaux d’audit** fournit des informations sur les actions exécutées sur votre cache. Vous pouvez également utiliser le filtrage pour développer cette vue afin d’inclure d’autres ressources.
-	**Resource Health** surveille vos ressources et vous indique si elles s’exécutent comme prévu. Pour plus d’informations sur le service Azure Resource Health, consultez [Vue d’ensemble d’Azure Resource Health](../resource-health/resource-health-overview.md).
-	**Nouvelle demande de support** fournit des options pour ouvrir une demande de support pour votre cache.

Ces outils vous permettent de surveiller l’intégrité de vos instances Cache Redis Azure et vous aident à gérer vos applications de mise en cache. Pour plus d’informations, consultez la section [Paramètres de support et dépannage](cache-configure.md#support-amp-troubleshooting-settings).

### Mes paramètres de compte de stockage de diagnostic de cache ont été modifiés, que s’est-il passé ?

Les caches de la même région et du même abonnement partagent les mêmes paramètres de stockage de diagnostics. Et, si la configuration est modifiée (diagnostics activés/désactivés ou changement du compte de stockage), elle s’applique à tous les caches de l’abonnement qui se trouvent dans cette région. Si les paramètres de diagnostic pour votre cache ont été modifiés, vérifiez si les paramètres de diagnostic pour un autre cache dans le même abonnement et la même région ont été modifiés. Pour vérifier si c’est le cas, vous pouvez afficher les journaux d’audit pour votre cache pour un événement `Write DiagnosticSettings`. Pour plus d’informations sur l’utilisation des journaux d’audit, consultez les rubriques [Affichage des événements et des journaux d’audit](../azure-portal/insights-debugging-with-events.md) et [Opérations d’audit avec Resource Manager](../resource-group-audit.md). Pour plus d’informations sur la surveillance des événements du Cache Redis Azure, consultez [Opérations et alertes](cache-how-to-monitor.md#operations-and-alerts).

### Pourquoi le diagnostic est-il activée pour certains nouveaux caches mais pas pour d’autres ?

Les caches de la même région et du même abonnement partagent les mêmes paramètres de stockage de diagnostics. Si vous créez un nouveau cache dans la même région et dans le même abonnement en tant qu’autre cache avec activation des diagnostics, les diagnostics sont activés sur le nouveau cache avec les mêmes paramètres.


<a name="cache-timeouts"></a>
### Pourquoi est-ce que je reçois des erreurs d’expiration du délai ?

L’expiration du délai se produit dans le client que vous utilisez pour communiquer avec Redis. La plupart du temps, le serveur Redis n’expire pas. Lorsqu’une commande est envoyée au serveur Redis, elle est placée dans la file d’attente, puis le serveur Redis la récupère et l’exécute. Toutefois, le client peut expirer pendant ce processus. Si c’est le cas, une exception est levée côté appelant. Pour plus d’informations sur la résolution des problèmes de délai d’attente, consultez les rubriques [Résolution des problèmes côté client](cache-how-to-troubleshoot.md#client-side-troubleshooting) et [Exceptions au délai d’expiration de StackExchange.Redis](cache-how-to-troubleshoot.md#stackexchangeredis-timeout-exceptions).


<a name="cache-disconnect"></a>
### Pourquoi mon client a-t-il été déconnecté du cache ?

Voici quelques raisons pour lesquelles la déconnexion du cache peut se produire :

-	Causes côté client
	-	L’application cliente a été redéployée.
	-	L’application cliente a effectué une opération de mise à l’échelle.
		-	Dans le cas de Cloud Services ou de Web Apps, cela peut être dû à la mise à l’échelle automatique.
	-	La couche réseau côté client a été modifiée.
	-	Des erreurs temporaires se sont produites dans le client ou dans les nœuds du réseau entre le client et le serveur.
	-	Les limites de seuil de bande passante ont été atteintes.
	-	Les opérations côté processeur ont pris trop de temps.
-	Causes côté serveur
	-	Dans l’offre de cache Standard, le service Cache Redis Azure a créé un basculement du nœud principal vers le nœud secondaire.
	-	Azure appliquait un correctif à l’instance où le cache était déployé
		-	Il peut s’agir de mises à jour du serveur Redis ou d’une maintenance générale de la machine virtuelle.







### Quelle est l'offre Azure Cache qui me convient ?

>[AZURE.IMPORTANT]Conformément à ce qui a été [annoncé](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/) l’année dernière, le service de cache géré Azure et le service In-Role Cache Azure seront supprimés le 30 novembre 2016. Nous vous recommandons d’utiliser [Cache Redis Azure](https://azure.microsoft.com/services/cache/). Pour obtenir des informations sur la migration, consultez [Migrer un Service de cache géré vers le Cache Redis Azure](cache-migrate-to-redis.md).

### Cache Redis Azure
Le Cache Redis Azure fait l’objet d’une disponibilité générale dans des tailles pouvant aller jusqu’à 53 Go et son contrat SLA de disponibilité atteint 99,9 %. Le nouveau [niveau Premium](cache-premium-tier-intro.md) propose des tailles pouvant aller jusqu’à 530 Go et prend en charge le clustering, les réseaux virtuels et la persistance, avec un contrat SLA de 99,9 %.

Le Cache Redis Azure permet aux clients d’utiliser un cache Redis sécurisé et dédié, géré par Microsoft. Avec cette offre, vous pouvez exploiter toutes les fonctionnalités et l’écosystème fournis par Redis, ainsi que la fiabilité des services d’hébergement et de surveillance Microsoft.

À la différence des caches classiques qui traitent uniquement des paires clé-valeur, Redis est réputé pour ses types de données hautement performants. Redis prend également en charge l’exécution d’opérations atomiques sur ces types, comme : l’ajout à une chaîne, l’incrémentation de la valeur d’un hachage, la transmission de type push vers une liste, le calcul d’une intersection, union et différence ensemblistes ou l’obtention du membre dont le classement est le plus élevé dans un ensemble trié. Parmi les autres fonctionnalités, citons notamment la prise en charge des transactions, pub/sub, la création de scripts Lua, les clés d’une durée de vie limitée et les paramètres de configuration permettant à Redis de se comporter davantage comme un cache classique.

Un autre aspect clé du succès de Redis est son écosystème open source intègre et vif construit tout autour. Ce succès se reflète dans la diversité des clients Redis disponibles dans plusieurs langues. Cet avantage permet une utilisation par presque toutes les charges de travail qui peuvent être développées dans Azure.

Pour plus d’informations sur la prise en main du cache Redis Azure, consultez [Comment utiliser le cache Redis Azure](cache-dotnet-how-to-use-azure-redis-cache.md) et [Redis Cache documentation](https://azure.microsoft.com/documentation/services/redis-cache/).

### Service de cache géré
[Le Service de cache géré ne sera plus opérationnel à partir du 30 novembre 2016.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)

### In-Role Cache
[In-Role Cache ne sera plus opérationnel à partir du 30 novembre 2016.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)





[paramètre de configuration « minIoThreads »]: https://msdn.microsoft.com/library/vstudio/7w2sway1(v=vs.100).aspx

<!---HONumber=AcomDC_0921_2016-->