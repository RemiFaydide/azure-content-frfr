<properties 
	pageTitle="Configurer l’encodeur FMLE pour envoyer un flux live à débit binaire unique | Microsoft Azure" 
	description="Cette rubrique explique comment configurer l’encodeur Flash Media Live Encoder (FMLE) afin d’envoyer un flux à débit binaire unique à des canaux AMS activés pour l’encodage en temps réel." 
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
	ms.author="juliako;cenkdin;anilmur"/>

#Utiliser l’encodeur FMLE pour envoyer un flux en direct à débit binaire unique

> [AZURE.SELECTOR]
- [FMLE](media-services-configure-fmle-live-encoder.md)
- [Elemental Live](media-services-configure-elemental-live-encoder.md)
- [Tricaster](media-services-configure-tricaster-live-encoder.md)
- [Wirecast](media-services-configure-wirecast-live-encoder.md)

Cette rubrique explique comment configurer l’encodeur [Flash Media Live Encoder](http://www.adobe.com/products/flash-media-encoder.html) (FMLE) afin d’envoyer un flux à débit binaire unique à des canaux AMS activés pour l’encodage en temps réel. Pour plus d’informations, consultez [Utilisation de canaux activés pour effectuer un encodage en temps réel avec Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

Ce didacticiel montre comment gérer Azure Media Services (AMS) avec l’outil Azure Media Services Explorer (AMSE). Cet outil est uniquement compatible avec les PC Windows. Si vous êtes sous Mac ou Linux, utilisez le portail Azure Classic pour créer des [canaux](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) et des [programmes](media-services-portal-creating-live-encoder-enabled-channel.md#create-and-manage-a-program).

Notez que ce didacticiel décrit l’utilisation de AAC. Cependant, FMLE ne prend pas en charge AAC par défaut. Vous devez acheter un plug-in pour l’encodage AAC, comme par exemple, le [plug-in AAC de MainConcept](http://www.mainconcept.com/products/plug-ins/plug-ins-for-adobe/aac-encoder-fmle.html).

##Composants requis

- [Créer un compte Azure Media Services](media-services-create-account.md)
- Assurez-vous qu’il y a un point de terminaison de diffusion en continu en cours d’exécution avec au moins une unité de diffusion en continu allouée. Pour plus d’informations, consultez la rubrique [Gestion des points de terminaison de diffusion en continu dans un compte Media Services](media-services-portal-manage-streaming-endpoints.md).
- Installez la dernière version de l’outil [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer).
- Lancez l’outil et connectez-vous à votre compte AMS.

##Conseils

- Si possible, utilisez une connexion Internet câblée.
- Une bonne règle pour déterminer les besoins en bande passante consiste à doubler les débits binaires de diffusion en continu. Bien qu’il ne s’agisse pas d’une obligation, cela permet de réduire l’impact de l’encombrement du réseau.
- Lors de l’utilisation d’encodeurs logiciels, fermez tous les programmes inutiles.

## Créer un canal

1.  Dans l’outil AMSE, accédez à l’onglet **Live**, puis cliquez avec le bouton droit dans la zone des canaux. Dans le menu qui s’affiche, sélectionnez **Créer un canal...**.

![FMLE](./media/media-services-fmle-live-encoder/media-services-fmle1.png)

2. Spécifiez un nom de canal (le champ Description est facultatif). Sous Paramètres du canal, sélectionnez **Standard** pour l’option Live Encoding, avec le protocole d’entrée défini sur **RTPM**. Vous pouvez laisser tous les autres paramètres inchangés.


Vérifiez que l’option **Démarrer maintenant le nouveau canal** est sélectionnée.

3. Cliquez sur **Créer un canal**. ![FMLE](./media/media-services-fmle-live-encoder/media-services-fmle2.png)

>[AZURE.NOTE] Le démarrage du canal peut prendre jusqu’à 20 minutes.


Pendant le démarrage du canal, vous pouvez [configurer l’encodeur](media-services-configure-fmle-live-encoder.md#configure_fmle_rtmp).

>[AZURE.IMPORTANT] Notez que la facturation commence dès que l’état du canal indique qu’il est prêt à être utilisé. Pour plus d’informations, consultez [États du canal](media-services-manage-live-encoder-enabled-channels.md#states).

##<a id=configure_fmle_rtmp></a>Configurer l’encodeur FMLE

Dans ce didacticiel, les paramètres de sortie ci-dessous sont utilisés. Le reste de cette section décrit la procédure de configuration plus en détail.

**Vidéo** :
 
- Codec : H.264
- Profil : Élevé (niveau 4.0)
- Débit binaire : 5 000 kbit/s
- Image clé : 2 secondes (60 secondes)
- Fréquence d’images : 30
 
**Audio** :

- Codec : AAC (LC)
- Débit binaire : 192 kbit/s
- Taux d’échantillonnage : 44,1 kHz


###Configuration

1. Accédez à l’interface de l’encodeur FMLE sur la machine en cours d’utilisation.

	L’interface correspond à une page principale de paramètres. Veuillez prendre note des paramètres recommandés suivants pour utiliser le streaming à l’aide de FMLE.
	
	- Format : Fréquence d’images H.264 : 30,00
	- Taille d’entrée : 1280 x 720
	- Débit binaire : 5000 Kbit/s (cette valeur peut être ajustée en fonction des limitations du réseau)

	![fmle](./media/media-services-fmle-live-encoder/media-services-fmle3.png)

	Lors de l’utilisation de sources entrelacées, veuillez cocher l’option « Désentrelacement ».

2. Sélectionnez l’icône en forme de clé en regard de Format. Elle doit mener vers les paramètres supplémentaires suivants :

	- Profil : Principal
	- Niveau : 4.0
	- Fréquence d’image clé : 2 secondes
	
	![fmle](./media/media-services-fmle-live-encoder/media-services-fmle4.png)

3. Définissez le paramètre audio important suivant :
	
	- Format : AAC
	- Taux d’échantillonnage : 44100 kHz
	- Débit binaire : 192 kbit/s
	
	![fmle](./media/media-services-fmle-live-encoder/media-services-fmle5.png)

6. Récupérez l’URL d’entrée du canal pour l’affecter au **Point de terminaison RTMP** FMLE.
	
	Revenez à l’outil AMSE et vérifiez l’état d’achèvement du canal. Une fois que l’état est passé de **Démarrage** à **En cours d’exécution**, vous pouvez obtenir l’URL d’entrée.
	  
	Une fois le canal en cours d’exécution, cliquez avec le bouton droit sur le nom du canal, déplacez le pointeur vers le bas pour le placer sur **Copier l’URL entrée dans le Presse-papiers**, puis sélectionnez **URL d’entrée principale**.
	
	![fmle](./media/media-services-fmle-live-encoder/media-services-fmle6.png)

7. Collez ces informations dans le champ **URL FMS** de la section de sortie et attribuez un nom de flux.

	![fmle](./media/media-services-fmle-live-encoder/media-services-fmle7.png)

	Pour une redondance supplémentaire, répétez ces étapes avec l’URL d’entrée secondaire.
8. Sélectionnez **Connecter**.

>[AZURE.IMPORTANT] Avant de cliquer sur **Connecter**, vous **devez** vérifier que le canal est prêt. Veillez également à ne pas laisser le canal à l’état d’exécution sans un flux de contribution d’entrée pendant plus de 15 minutes.

##Tester la lecture
  
1. Accédez à l’outil AMSE et cliquez avec le bouton droit sur le canal à tester. Dans le menu, placez le pointeur sur **Lire l’aperçu** et sélectionnez **avec Azure Media Player**.

	![fmle](./media/media-services-fmle-live-encoder/media-services-fmle8.png)

Si le flux s’affiche dans le lecteur, cela signifie que l’encodeur a été correctement configuré pour se connecter à AMS.

Si vous recevez une erreur, vous devrez réinitialiser le canal et ajuster les paramètres de l’encodeur. Pour obtenir des instructions détaillées, reportez-vous à la rubrique consacrée à la [résolution des problèmes](media-services-troubleshooting-live-streaming.md).

##Créer un programme

1. Une fois que vous avez vérifié que la lecture fonctionne sur le canal, créez un programme. Sous l’onglet **Live** de l’outil AMSE, cliquez avec le bouton droit dans la zone des programmes et sélectionnez **Créer un programme**.

	![fmle](./media/media-services-fmle-live-encoder/media-services-fmle9.png)

2. Nommez le programme et, si nécessaire, ajustez la **longueur de la fenêtre d’archive** (qui est de 4 heures par défaut). Vous pouvez également spécifier un emplacement de stockage ou conserver la valeur par défaut.
3. Cochez la case **Démarrer le programme maintenant**.
4. Cliquez sur **Créer le programme**.
  
	Remarque : la création d’un programme prend moins de temps que la création d’un canal.
 
5. Une fois le programme en cours d’exécution, vérifiez que la lecture fonctionne. Pour ce faire, cliquez avec le bouton droit sur le programme, placez le pointeur sur **Lire le(s) programme(s)**, puis sélectionnez **avec Azure Media Player**.
6. Après confirmation, cliquez à nouveau avec le bouton droit sur le programme et sélectionnez **Copier l’URL de sortie dans le Presse-papiers** (ou obtenez cette information à l’aide de l’option **Informations et paramètres du programme** du menu).

Le flux est maintenant prêt à être incorporé dans un lecteur ou distribué à une audience pour un affichage en direct.


## Résolution de problèmes

Pour obtenir des instructions détaillées, reportez-vous à la rubrique consacrée à la [résolution des problèmes](media-services-troubleshooting-live-streaming.md).


##Parcours d’apprentissage de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0921_2016-->