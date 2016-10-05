<properties 
	pageTitle="Lecture du contenu | Microsoft Azure" 
	description="Cette rubrique répertorie les lecteurs existants que vous pouvez utiliser pour lire votre contenu." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/19/2016" 
	ms.author="juliako"/>


#Lecture de votre contenu à l’aide des lecteurs existants

Azure Media Services prend en charge de nombreux formats de diffusion en continu populaires, tels que la diffusion en continu lisse, la diffusion en continu HTTP et MPEG-Dash. Cette rubrique vous oriente vers les lecteurs existants que vous pouvez utiliser pour tester votre flux de données.

>[AZURE.NOTE]Pour lire du contenu empaqueté ou chiffré dynamiquement, veillez à obtenir au moins une unité de diffusion en continu pour le point de terminaison de diffusion en continu à partir duquel vous prévoyez de remettre votre contenu. Pour plus d’informations sur la mise à l’échelle des unités de diffusion en continu, voir : [Mise à l’échelle des unités de diffusion en continu](media-services-portal-manage-streaming-endpoints.md).

###Lecteur de contenu Media Services sur le portail Azure Classic

Le **portail Azure** propose un lecteur de contenu que vous pouvez utiliser pour tester votre vidéo.

Cliquez sur la vidéo de votre choix (assurez-vous qu’elle a été [publiée](media-services-portal-publish.md)), puis sur le bouton **Lire** situé en bas du portail.

Certaines considérations s’appliquent :

- Le **lecteur de contenu de Media Services** assure la lecture depuis le point de terminaison de diffusion en continu par défaut. Si vous souhaitez lire à partir d’un autre point de terminaison de diffusion en continu que celui par défaut, utilisez un autre lecteur, Par exemple, le [Lecteur multimédia Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html).


![AMSPlayer][AMSPlayer]

###Azure Media Player

Utilisez le [Lecteur multimédia Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html) pour lire votre contenu (protégé ou non) dans l'un des formats suivants :

- Smooth Streaming
- MPEG DASH
- HLS
- MP4 progressif


###Flash Player

####Chiffrement AES avec jeton

[http://aestoken.azurewebsites.net](http://aestoken.azurewebsites.net)

###Lecteurs Silverlight

####Analyse

[http://smf.cloudapp.net/healthmonitor](http://smf.cloudapp.net/healthmonitor)

####PlayReady avec jeton

[http://sltoken.azurewebsites.net](http://sltoken.azurewebsites.net)

### Lecteurs DASH

[http://dashplayer.azurewebsites.net](http://dashplayer.azurewebsites.net)

[http://dashif.org](http://dashif.org)

###Autres

Pour tester les URL HLS, vous pouvez également utiliser :

- **Safari** sur un appareil iOS ou
- **3ivx HLS Player** sous Windows.

##Développement de lecteurs vidéo

Pour plus d’informations sur le développement de vos propres lecteurs, consultez la page [Développement de lecteurs vidéo](media-services-develop-video-players.md)




##Parcours d’apprentissage de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


[AMSPlayer]: ./media/media-services-playback-content-with-existing-players/media-services-portal-player.png

<!---HONumber=AcomDC_0921_2016-->