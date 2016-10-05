<properties
    pageTitle="Meilleures pratiques pour Azure RemoteApp | Microsoft Azure"
    description="Meilleures pratiques pour la configuration et l'utilisation d’Azure RemoteApp"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />

# Meilleures pratiques pour la configuration et l'utilisation d'Azure RemoteApp

> [AZURE.IMPORTANT]
Azure RemoteApp n’est plus disponible. Pour plus d’informations, lisez [l’annonce](https://go.microsoft.com/fwlink/?linkid=821148).

Les informations suivantes peuvent vous aider à configurer et utiliser efficacement Azure RemoteApp.

## Connectivité


- Utilisez toujours la dernière version du client. L'utilisation de clients plus anciens peut entraîner des problèmes de connectivité et d'autres problèmes d'utilisation. L'activation des mises à jour automatiques des applications pour votre appareil garantit que la dernière version du client est toujours installée.
- Utilisez toujours la connexion Internet la plus stable et la plus fiable dont vous disposez.
- Utilisez uniquement des connexions proxy prises en charge pour bénéficier de performances de connectivité optimales. Le proxy SOCKS n'est pas pris en charge.

## Applications


- Enregistrez et fermez les applications RemoteApp quand vous n'en avez plus besoin. Sinon, vous pourriez perdre des données.
- Validez les applications personnalisées avant de les utiliser dans Azure RemoteApp. Cela implique de vérifier qu'elles fonctionnent sur une plateforme multisession et qu'elles ne consomment pas de ressources inutiles, telles que la mémoire et le processeur, qui pourraient manquer à un autre utilisateur de la même collection. Pour plus d'informations, téléchargez et lisez les [Meilleures pratiques de compatibilité des applications pour les Services Bureau à distance](http://www.dabcc.com/resources/Application%20Compatibility%20Best%20Practices%20for%20Remote%20Desktop%20Services.pdf).

## Configuration et gestion


- Assurez-vous que vos images de modèle sont toujours à jour en installant les mises à jour logicielles et autres correctifs critiques, si nécessaire. De cette façon, comme Azure RemoteApp s'adapte automatiquement à votre capacité, chaque instance est corrigée.
- Assurez-vous que votre déploiement de services ADFS est sécurisé et fiable. Sinon, les authentifications client peuvent échouer, empêchant alors les utilisateurs d'accéder à Azure RemoteApp.
- Configurez des images de modèle avec les applications, les rôles ou les fonctionnalités installés de sorte qu'elles soient sans état. Elles ne doivent pas dépendre d'une instance de machine virtuelle dans un service RemoteApp en état persistant.
	- Stockez toutes les données utilisateur dans les profils utilisateur ou d'autres emplacements de stockage externes au service, tels que des partages de fichiers locaux ou OneDrive.
	- Stockez les données partagées dans des emplacements de stockage externes au service, tels que les partages de fichiers locaux ou OneDrive.
	- Configurez les paramètres du système dans l'image de modèle plutôt que dans les machines virtuelles d'un service.
	- Désactivez les mises à jour logicielles automatiques pour les applications publiées. Appliquez-les plutôt manuellement à l'image de modèle et testez-les avant d'effectuer le déploiement à partir du modèle.

<!---HONumber=AcomDC_0921_2016-->