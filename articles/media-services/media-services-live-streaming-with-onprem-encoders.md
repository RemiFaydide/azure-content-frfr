<properties 
	pageTitle="Vidéo en flux continu avec des encodeurs locaux qui créent des flux à vitesses de transmission multiples | Microsoft Azure" 
	description="Cette rubrique explique comment configurer un canal qui reçoit un flux dynamique à débit binaire multiple en provenance d’un encodeur local. Le flux peut ensuite être distribué aux applications de lecture clientes via un ou plusieurs points de terminaison de diffusion en continu à l’aide d’un des protocoles de diffusion en continu adaptatifs suivants : HLS, Smooth Stream, MPEG DASH et HDS." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ne" 
	ms.topic="article" 
	ms.date="09/19/2016" 
	ms.author="cenkdin;juliako"/>

#Vidéo en flux continu avec des encodeurs locaux qui créent des flux à vitesses de transmission multiples

##Vue d'ensemble

Dans Azure Media Services, un **canal** représente un pipeline de traitement du contenu vidéo en flux continu. Un **canal** reçoit des flux d’entrée live de l’une des deux manières suivantes :


- Un encodeur live local envoie au canal un paquet **RTMP** ou **Smooth Streaming** (MP4 fragmenté) à débit binaire multiple qui n’est pas activé pour effectuer un encodage live avec AMS. Les flux reçus transitent par les **canaux** sans traitement supplémentaire. Cette méthode est appelée **pass-through**. Vous pouvez utiliser les encodeurs dynamiques suivants qui produisent un flux Smooth Streaming à débit binaire multiple : Elemental, Envivio, Cisco. Les encodeurs dynamiques suivants produisent un flux au format RTMP : Adobe Flash Live, Telestream Wirecast et transcodeurs Tricaster. Un encodeur live peut également envoyer un flux à débit binaire unique vers un canal qui n’est pas activé pour le Live Encoding, mais ce n’est pas recommandé. Lorsqu’il y est invité, Media Services fournit le flux aux clients.

	>[AZURE.NOTE] L’utilisation d’une méthode pass-through est le moyen le plus économique de diffuser une vidéo en flux continu.
	
- Un encodeur dynamique envoie un flux à vitesse de transmission unique vers le canal activé pour effectuer un encodage en direct avec Media Services dans l’un des formats suivants : RTP (MPEG-TS), RTMP ou Smooth Streaming (MP4 fragmenté). Le canal procède ensuite à l’encodage en temps réel du flux à débit binaire unique entrant en flux vidéo à débit binaire multiple (adaptatif). Lorsqu’il y est invité, Media Services fournit le flux aux clients.

À compter de la version de Media Services 2.10, lorsque vous créez un canal, vous pouvez spécifier la façon dont vous souhaitez qu’il reçoive le flux d’entrée. Vous pouvez également indiquer si vous souhaitez ou non que le canal effectue un encodage en temps réel de votre flux. Deux options s'offrent à vous :

- **Aucun** : indiquez cette valeur si vous envisagez d’utiliser un encodeur live local qui produira des flux à débit binaire multiple (un flux pass-through). Le cas échéant, le flux entrant est transmis à la sortie sans encodage. Il s’agit du comportement d’un canal avant la version 2.10. Cette rubrique fournit des détails sur l’utilisation des canaux de ce type.

- **Standard** : choisissez cette valeur si vous envisagez d’utiliser Media Services pour encoder votre flux live à débit binaire unique en flux à débit binaire multiple. N'oubliez pas qu'il existe un impact sur la facturation pour le codage en direct et que laisser un canal d'encodage en temps réel dans l'état « Actif » occasionne des frais de facturation. Il est recommandé d'arrêter immédiatement vos canaux en cours d'exécution une fois votre événement de diffusion en continu en temps réel terminé pour éviter des frais horaires supplémentaires. Lorsqu’il y est invité, Media Services fournit le flux aux clients.

>[AZURE.NOTE]Cette rubrique décrit les attributs des canaux qui ne sont pas activés pour effectuer un encodage live (type d’encodage **Aucun**). Pour obtenir des informations sur l’utilisation des canaux qui sont activés pour effectuer l’encodage live, consultez [Comment effectuer une diffusion de vidéo en flux continu à l’aide d’Azure Media Services pour créer des flux à vitesses de transmission multiples](media-services-manage-live-encoder-enabled-channels.md).

Le diagramme suivant illustre un workflow de vidéo en flux continu utilisant un encodeur live local pour produire des flux à débit binaire multiple au format MP4 fragmenté (Smooth Streaming) ou RMTP.

![Flux de travail live][live-overview]

Cette rubrique couvre les sujets suivants :

- [Scénario courant de vidéo en flux continu](media-services-live-streaming-with-onprem-encoders.md#scenario)
- [Description d’un canal et de ses composants associés](media-services-live-streaming-with-onprem-encoders.md#channel)
- [Considérations](media-services-live-streaming-with-onprem-encoders.md#considerations)

##<a id="scenario"></a>Scénario courant de vidéo en flux continu
Les étapes suivantes décrivent les tâches impliquées dans la création d’applications courantes de vidéo en flux continu.

1. Connectez une caméra vidéo à un ordinateur. Lancez et configurez un encodeur live local qui produit un flux à débit binaire multiple au format MP4 fragmenté (Smooth Streaming) ou RTMP. Pour plus d’informations, voir [Prise en charge RTMP et encodeurs live dans Azure Media Services](http://go.microsoft.com/fwlink/?LinkId=532824).
	
	Cette étape peut également être effectuée après la création du canal.

1. Créez et démarrez un canal.
1. Récupérez l’URL de réception du canal.

	L’URL de réception est utilisée par l’encodeur dynamique pour envoyer le flux au canal.
1. Récupérez l’URL d’aperçu du canal.

	Utilisez cette URL pour vérifier que votre canal reçoit correctement le flux dynamique.

3. Créez un programme.

	Lors de l’utilisation du portail Azure Classic, la création d’un programme crée également un élément multimédia.

	Lors de l’utilisation du Kit de développement logiciel (SDK) .NET ou de REST, vous devez créer une ressource et préciser son utilisation lors de la création d’un programme.
1. Publiez la ressource associée au programme.

	Assurez-vous d'avoir au moins une unité réservée de diffusion en continu pour le point de terminaison de diffusion en continu à partir duquel vous prévoyez de diffuser votre contenu.
1. Démarrez le programme dès que vous êtes prêt à lancer la diffusion en continu et l’archivage.
2. Un signal peut éventuellement être envoyé à l’encodeur dynamique pour qu’il démarre une publicité. La publicité est insérée dans le flux de sortie.
1. Arrêtez le programme chaque fois que vous voulez arrêter la diffusion et archiver l’événement.
1. Supprimez le programme (et éventuellement la ressource).

##<a id="channel"></a>Description d’un canal et de ses composants associés

###<a id="channel_input"></a>Configurations de l’entrée (réception) des canaux

####<a id="ingest_protocols"></a>Protocole de diffusion en continu de réception

Media Services prend en charge la réception des flux live à l’aide des protocoles de diffusion en continu suivants :

- **MP4 fragmenté à débit binaire multiple**
 
- **RTMP à débit binaire multiple**

	Lorsque le protocole de diffusion en continu de réception **RTMP** est sélectionné, deux points de terminaison de réception (entrée) sont créés pour le canal :
	
	**URL principale** : spécifie l’URL complète du point de terminaison de réception RTMP principal du canal.

	**URL secondaire** : spécifie l’URL complète du point de terminaison de réception RTMP secondaire du canal.


	Utilisez l’URL secondaire si vous désirez améliorer la durabilité et la tolérance de panne de votre flux de réception, ainsi que le basculement et la tolérance de panne de l’encodeur, tout spécialement dans les scénarios suivants.

	- Double envoi d’encodeur unique vers des URL principales et secondaires :
	
		L’objectif principal de ce scénario est d’améliorer la résilience des fluctuations et gigues du réseau. Certains codeurs RTMP ne gèrent pas correctement les déconnexions du réseau. En cas de déconnexion du réseau, un encodeur peut arrêter le codage et ne plus envoyer les données en mémoire tampon lorsque la reconnexion se produit, ce qui provoque parfois des discontinuités et des pertes de données. La déconnexion du réseau peut être due à un problème de réseau ou une maintenance côté Azure. Les URL principales et secondaires réduisent les problèmes de réseau et fournissent également un processus de mise à niveau contrôlé. Chaque fois qu’une déconnexion réseau planifiée se produit, Media Services gère la déconnexion principale et secondaire et décale la déconnexion entre les deux, ce qui permet aux encodeurs d’avoir suffisamment de temps pour envoyer des données, puis se reconnecter. L’ordre de la déconnexion peut être aléatoire, mais il y aura toujours un délai entre la déconnexion principale et la déconnexion secondaire. Dans ce scénario, l’encodeur est toujours le point de défaillance unique.
	 
	- Plusieurs encodeurs, chaque encodeur envoyant les données vers un point dédié :
		
		Ce scénario met en place une redondance d’encodeurs et d’ingestion. Dans ce scénario, l’encodeur 1 envoie les données à l’URL principale et l’encodeur 2 envoie les données à l’URL secondaire. Si un encodeur tombe en panne, l’autre peut toujours continuer à envoyer les données. La redondance des données peut toujours être maintenue car Media Services ne déconnecte pas les URL principale et secondaire en même temps. Ce scénario part du principe que les encodeurs sont synchronisés et envoient exactement les mêmes données.
 
	- Double envoi de plusieurs encodeurs vers des URL principales et secondaires :
	
		Dans ce scénario, les deux encodeurs envoient des données aux URL principales et secondaires. Cela améliore la fiabilité et la tolérance de panne, ainsi que la redondance des données. Ce scénario peut tolérer une panne des deux encodeurs, ainsi que des déconnexions, même si l’un des encodeurs cesse de fonctionner. Ce scénario part du principe que les encodeurs sont synchronisés et envoient exactement les mêmes données.

Pour plus d’informations sur les encodeurs live RTMP, consultez la page [Prise en charge RTMP et encodeurs live dans Azure Media Services](http://go.microsoft.com/fwlink/?LinkId=532824).

Les considérations suivantes s'appliquent :

- Assurez-vous que vous disposez d’une connectivité Internet libre suffisante pour envoyer des données aux points de réception.
- L’utilisation d’une URL de réception secondaire nécessite de la bande passante supplémentaire.
- Le flux à débit binaire multiple entrant peut présenter un maximum de 10 niveaux de qualité vidéo (également appelés couches) et un maximum de 5 pistes audio.
- Le débit binaire moyen le plus élevé pour les niveaux ou couches de qualité vidéo doit être inférieur à 10 Mbit/s.
- La somme des débits binaires moyens de tous les flux vidéo et audio doit être inférieure à 25 Mbit/s.
- Vous ne pouvez pas modifier le protocole d’entrée pendant l’exécution du canal ou de ses programmes associés. Si vous avez besoin d’autres protocoles, vous devez créer des canaux distincts pour chaque protocole d’entrée.
- Vous pouvez recevoir un flux à débit binaire unique dans votre canal, mais comme le flux de données n’est pas traité par le canal, les applications clientes recevront également un flux à débit binaire unique (cette option n’est pas recommandée).

####URL (points de terminaison) de réception 

Un canal fournit un point de terminaison d’entrée (URL de réception) que vous spécifiez dans l’encodeur live pour que ce dernier puisse transmettre les flux à vos canaux.

Vous pouvez obtenir les URL de réception lors de la création du canal. Pour les obtenir, il n’est pas nécessaire que le canal soit à l’état **En cours d’exécution**. Lorsque vous êtes prêt à commencer l’envoi de données dans le canal, ce dernier doit être à l’état **En cours d’exécution**. Une fois que le canal commence à recevoir les données, vous pouvez prévisualiser votre flux via l’URL d’aperçu.

Vous avez la possibilité de recevoir un flux live au format MP4 fragmenté (Smooth Streaming) via une connexion SSL. Pour assurer la réception via SSL, veillez à mettre à jour l’URL de réception pour HTTPS. Il n’est pas possible de recevoir un flux RTMP via SSL pour le moment.

####<a id="keyframe_interval"></a>Intervalle d’image clé

Lorsque vous utilisez un encodeur live local pour générer un flux à débit binaire multiple, l’intervalle d’image clé spécifie la durée du GOP (tel qu’il est utilisé par cet encodeur externe). Une fois ce flux entrant reçu par le canal, vous pouvez distribuer votre flux live aux applications de lecture clientes dans un des formats suivants : Smooth Streaming, DASH et HLS. Lors de la diffusion en continu, TLS est toujours empaquetée de façon dynamique. Par défaut, Media Services calcule automatiquement le coefficient d’empaquetage de segment HLS (fragments par segment) en fonction de l’intervalle d’image clé, également appelé groupe d’images (GOP), reçu de l’encodeur live.

Le tableau suivant montre le mode de calcul de la durée du segment :

Intervalle d’image clé|Coefficient d’empaquetage de segment HLS (FragmentsPerSegment)|Exemple
---|---|---
Inférieur ou égal à 3 secondes|3|Si la valeur de KeyFrameInterval (ou groupe d’images) est de 2 secondes, le coefficient d’empaquetage de segment HLS est égal à 3, ce qui a pour effet de créer un segment HLS de 6 secondes.
3 à 5 secondes|2:1|Si la valeur de KeyFrameInterval (ou groupe d’images) est de 4 secondes, le coefficient d’empaquetage de segment HLS est égal à 2 pour 1, ce qui a pour effet de créer un segment HLS de 8 secondes.
Supérieur à 5 secondes|1:1|Si la valeur de KeyFrameInterval (ou groupe d’images) est de 6 secondes, le coefficient d’empaquetage de segment HLS est égal à 1, ce qui a pour effet de créer un segment HLS de 6 secondes.


Vous pouvez modifier le coefficient de fragments par segment en configurant la sortie du canal et le paramètre FragmentsPerSegment sur ChannelOutputHls.

Vous pouvez également modifier la valeur d’intervalle d’image clé en définissant la propriété KeyFrameInterval sur ChannelInput.

Si vous définissez explicitement la valeur KeyFrameInterval, le coefficient d’empaquetage de segment HLS FragmentsPerSegment est calculé à l’aide des règles décrites ci-dessus.

Si vous définissez explicitement les valeurs KeyFrameInterval et FragmentsPerSegment, Media Services utilise les valeurs que vous avez définies.


####Adresses IP autorisées

Vous pouvez définir les adresses IP autorisées à publier du contenu vidéo sur ce canal. Les adresses IP autorisées peuvent être spécifiées en tant qu’adresses IP uniques (par exemple, 10.0.0.1), une plage d’adresses IP utilisant une adresse IP et un masque de sous-réseau CIDR (par exemple, 10.0.0.1/22), ou une plage d’adresses IP utilisant une adresse IP et un masque de sous-réseau décimal séparé par des points (par exemple, 10.0.0.1[255.255.252.0]).

Si aucune adresse IP n’est spécifiée et qu’il n’existe pas de définition de règle, alors aucune adresse IP ne sera autorisée. Pour autoriser toutes les adresses IP, créez une règle et définissez la valeur 0.0.0.0/0.

###Aperçu du canal 

####URL d’aperçu

Les canaux fournissent un point de terminaison d’aperçu (URL d’aperçu) permettant de prévisualiser et de valider le flux avant de lui appliquer un traitement supplémentaire et de le distribuer.

Vous pouvez obtenir l’URL d’aperçu lors de la création du canal. Pour obtenir l’URL, il n’est pas nécessaire que le canal soit à l’état **En cours d’exécution**.

Une fois que le canal commence à recevoir les données, vous pouvez prévisualiser votre flux.

Notez que pour le moment, le flux d’aperçu ne peut être distribué qu’au format MP4 fragmenté (Smooth Streaming), quel que soit le type d’entrée spécifié. Vous pouvez utiliser le lecteur [http://smf.cloudapp.net/healthmonitor](http://smf.cloudapp.net/healthmonitor) pour tester la diffusion au format Smooth Streaming. Vous pouvez également utiliser un lecteur hébergé dans le portail Azure Classic pour afficher votre flux.


####Adresses IP autorisées

Vous pouvez définir les adresses IP autorisées à se connecter au point de terminaison d’aperçu. Si aucune adresse IP n’est spécifiée, alors toutes les adresses IP seront autorisées. Les adresses IP autorisées peuvent être spécifiées en tant qu’adresses IP uniques (par exemple, 10.0.0.1), une plage d’adresses IP utilisant une adresse IP et un masque de sous-réseau CIDR (par exemple, 10.0.0.1/22), ou une plage d’adresses IP utilisant une adresse IP et un masque de sous-réseau décimal séparé par des points (par exemple, 10.0.0.1[255.255.252.0]).

###Sortie du canal

Pour plus d’informations, consultez la section [Configuration de l’intervalle d’image clé](#keyframe_interval).


###Programmes du canal

Un canal est associé à des programmes vous permettant de contrôler la publication et le stockage des segments dans un flux dynamique. Les canaux gèrent des programmes. La relation entre canal et programme est très similaire au contenu multimédia traditionnel où un canal a un flux de contenu constant et un programme est limité à un événement minuté sur ce canal.

Vous pouvez spécifier le nombre d’heures pendant lesquelles vous souhaitez conserver le contenu enregistré pour le programme en définissant la durée de la **fenêtre d’archivage**. Cette valeur peut être comprise entre 5 minutes et 25 heures. La durée de la fenêtre d’archivage détermine également la plage maximale de temps dans laquelle les clients peuvent effectuer des recherches en arrière à partir de la position dynamique actuelle. Les programmes peuvent durer davantage que le laps de temps spécifié, mais le contenu qui se situe en dehors de la longueur de fenêtre est ignoré en permanence. La valeur de cette propriété détermine également la longueur maximale que les manifestes de client peuvent atteindre.

Chaque programme est associé à une ressource qui stocke le contenu diffusé en continu. Un élément multimédia est mappé à un conteneur d’objets blob dans le compte de stockage Azure et les fichiers de l’élément multimédia sont stockés en tant qu’objets blob dans ce conteneur. Pour publier le programme afin que vos clients puissent visionner le flux, vous devez créer un localisateur OnDemand pour la ressource associée. Le fait de posséder ce localisateur vous permettra de générer une URL de diffusion en continu que vous pourrez fournir à vos clients.

Un canal prend en charge jusqu’à trois programmes exécutés simultanément, ce qui rend possible la création de plusieurs archives du même flux entrant. Cela vous permet de publier et d’archiver différentes parties d’un événement en fonction des besoins. Par exemple, imaginez que vous devez archiver 6 heures d’un programme, mais diffuser uniquement les 10 dernières minutes. Pour ce faire, vous devez créer deux programmes exécutés simultanément. Un programme est configuré pour archiver 6 heures de l’événement, mais il n’est pas publié. L’autre programme est configuré pour archiver pendant 10 minutes et il est publié.

Vous ne devez pas réutiliser de programmes existants pour de nouveaux événements. Au lieu de cela, créez et démarrez un nouveau programme pour chaque événement, tel que décrit dans la section Programmation d’applications de vidéo en flux continu.

Démarrez le programme dès que vous êtes prêt à lancer la diffusion en continu et l’archivage. Arrêtez le programme chaque fois que vous voulez arrêter la diffusion et archiver l’événement.

Pour supprimer du contenu archivé, arrêtez et supprimez le programme, puis supprimez l’élément multimédia associé. Un élément multimédia ne peut pas être supprimé s’il est utilisé par un programme ; le programme doit d’abord être supprimé.

Même après l’arrêt et la suppression du programme, les utilisateurs pourront lire votre contenu archivé en tant que vidéo à la demande tant que vous n’aurez pas supprimé l’élément multimédia.

Si vous souhaitez conserver le contenu archivé sans qu’il soit disponible pour la diffusion, supprimez le localisateur de diffusion en continu.

##<a id="states"></a>États du canal et mappage des états au mode de facturation 

État actuel d’un canal. Les valeurs possibles incluent :

- **Arrêté**. C’est l’état initial du canal après sa création. Dans cet état, les propriétés du canal peuvent être mises à jour, mais la diffusion en continu n’est pas autorisée.
- **Démarrage en cours**. Le canal est en cours de démarrage. Aucune mise à jour ou diffusion en continu n’est autorisée durant cet état. Si une erreur se produit, le canal retourne à l’état Arrêté.
- **Exécution en cours**. Le canal est capable de traiter des flux dynamiques.
- **En cours d’arrêt**. Le canal est en cours d’arrêt. Aucune mise à jour ou diffusion en continu n’est autorisée durant cet état.
- **Suppression en cours**. Le canal est en cours de suppression. Aucune mise à jour ou diffusion en continu n’est autorisée durant cet état.

Le tableau suivant montre comment les états du canal sont mappés au mode de facturation.
 
État du canal|Indicateurs de l’interface utilisateur du portail|Facturation ?
---|---|---|---
Starting|Starting|Aucun (état transitoire)
Exécution|Prêt (aucun programme en cours d'exécution)<p>ou<p> Diffusion en continu (au moins un programme en cours d'exécution)|Oui
En cours d’arrêt|En cours d’arrêt|Aucun (état transitoire)
Arrêté|Arrêté|Non

##<a id="cc_and_ads"></a>Sous-titrage codé et insertion de publicités 

Le tableau suivant présente les normes de sous-titrage et d’insertion de publicités prises en charge.

Standard|Remarques
---|---
CEA-708 et EIA-608 (708/608)|CEA-708 et EIA-608 sont des normes de sous-titrage codé pour les États-Unis et le Canada.<p><p>Actuellement, le sous-titrage est uniquement pris en charge s’il est inclus dans le flux d’entrée encodé. Vous devez utiliser un encodeur multimédia live capable d’insérer des sous-titres 608 ou 708 dans le flux encodé qui est envoyé à Media Services. Media Services distribuera le contenu avec les sous-titres insérés à vos utilisateurs.
TTML dans ismt (pistes textuelles Smooth Streaming)|L’empaquetage dynamique de Media Services permet à vos clients de diffuser en continu du contenu dans un des formats suivants : MPEG DASH, HLS ou Smooth Streaming. Toutefois, si votre flux est au format MP4 fragmenté (Smooth Streaming) avec des sous-titres dans un fichier .ismt (pistes textuelles Smooth Streaming), vous serez seulement en mesure de distribuer le flux aux clients Smooth Streaming.
SCTE-35|Système de signalisation numérique utilisé pour signaler l’insertion de publicités. Les récepteurs en aval utilisent le signal pour ajouter les publicités au flux pendant le temps alloué. SCTE-35 doit être envoyé comme piste partiellement allouée dans le flux d’entrée.<p><p>Notez qu’actuellement, le seul format de flux d’entrée pris en charge transmettant les signaux publicitaires est le format MP4 fragmenté (Smooth Streaming), qui est aussi le seul format de sortie compatible.


##<a id="Considerations"></a>Considérations

Lorsque vous utilisez un encodeur live local pour envoyer un flux à débit binaire multiple dans un canal, les contraintes suivantes s’appliquent :

- Assurez-vous que vous disposez d’une connectivité Internet libre suffisante pour envoyer des données aux points de réception.
- Le flux à débit binaire multiple entrant peut présenter un maximum de 10 niveaux de qualité vidéo (10 couches) et un maximum de 5 pistes audio.
- Le débit binaire moyen le plus élevé pour les niveaux ou couches de qualité vidéo doit être inférieur à 10 Mbit/s.
- La somme des débits binaires moyens de tous les flux vidéo et audio doit être inférieure à 25 Mbit/s.
- Vous ne pouvez pas modifier le protocole d’entrée pendant l’exécution du canal ou de ses programmes associés. Si vous avez besoin d’autres protocoles, vous devez créer des canaux distincts pour chaque protocole d’entrée.


Autres considérations liées à l’utilisation des canaux et des composants associés :

- Chaque fois que vous reconfigurez l’encodeur live, appelez la méthode de réinitialisation **Reset** sur le canal. Avant de réinitialiser le canal, vous devez arrêter le programme. Une fois le canal réinitialisé, redémarrez le programme.
- Un canal peut être arrêté uniquement lorsqu’il est en cours d’exécution et que tous les programmes du canal ont été arrêtés.
- Par défaut, vous pouvez seulement ajouter 5 canaux à votre compte Media Services. Pour plus d’informations, voir [Quotas et limitations](media-services-quotas-and-limitations.md).
- Vous ne pouvez pas modifier le protocole d’entrée pendant l’exécution du canal ou de ses programmes associés. Si vous avez besoin d’autres protocoles, vous devez créer des canaux distincts pour chaque protocole d’entrée.
- Vous êtes facturé uniquement lorsque votre canal est à l’état **En cours d’exécution**. Pour plus d’informations, reportez-vous à [cette](media-services-live-streaming-with-onprem-encoders.md#states) section.

##Création de canaux recevant un flux continu à débit binaire multiple provenant d’encodeurs locaux

Pour plus d’informations sur les encodeurs en direct locaux, consultez [Utilisation d’encodeurs tiers en direct avec Azure Media Services](https://azure.microsoft.com/blog/azure-media-services-rtmp-support-and-live-encoders/).

Choisissez **Portail**, **.NET**, **API REST** pour voir comment créer et gérer des canaux et des programmes.

[AZURE.INCLUDE [media-services-selector-manage-channels](../../includes/media-services-selector-manage-channels.md)]



##Parcours d’apprentissage de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



##Rubriques connexes

[Spécification d’ingestion en direct au format MP4 fragmenté Azure Media Services](media-services-fmp4-live-ingest-overview.md)

[Diffusion d’événements en direct en continu avec Azure Media Services](media-services-overview.md)

[Concepts Azure Media Services](media-services-concepts.md)

[live-overview]: ./media/media-services-manage-channels-overview/media-services-live-streaming-current.png

<!---HONumber=AcomDC_0921_2016-->