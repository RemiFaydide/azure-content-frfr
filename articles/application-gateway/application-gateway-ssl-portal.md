<properties
   pageTitle="Configurer une passerelle d’application pour le déchargement SSL en utilisant le portail | Microsoft Azure"
   description="Cette page fournit des instructions pour la création d’une passerelle Application Gateway pour le déchargement SSL à l’aide du portail"
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/09/2016"
   ms.author="gwallace"/>

# Configurer une passerelle d’application pour le déchargement SSL en utilisant le portail

> [AZURE.SELECTOR]
-[Azure portal](application-gateway-ssl-portal.md)
-[Azure Resource Manager PowerShell](application-gateway-ssl-arm.md)
-[Azure Classic PowerShell](application-gateway-ssl.md)

Il est possible de configurer une passerelle Azure Application Gateway pour terminer la session SSL (Secure Sockets Layer) au niveau de la passerelle afin d’éviter les tâches de déchiffrement SSL coûteuses au niveau de la batterie de serveurs web. Le déchargement SSL simplifie aussi la configuration de serveur principal et la gestion de l’application web.

## Scénario

Le scénario suivant passe par la configuration du déchargement SSL sur une passerelle d’application existante. Le scénario suppose que vous avez déjà suivi la procédure de [Création d’une passerelle Application Gateway](application-gateway-create-gateway-portal.md).

## Avant de commencer

Pour configurer le déchargement SSL avec une passerelle Application Gateway, un certificat est requis. Ce certificat est chargé sur la passerelle d’application et utilisé pour chiffrer et déchiffrer le trafic envoyé via SSL. Le certificat doit être partagé au format Personal Information Exchange (.pfx). Ce format de fichier permet d’exporter la clé privée requise par la passerelle d’application pour effectuer le chiffrement et le déchiffrement du trafic.

## Ajouter un écouteur HTTPS

L’écouteur HTTPS recherche le trafic en fonction de sa configuration et aide à acheminer le trafic vers les pools principaux.

### Étape 1

Accédez au portail Azure et sélectionnez une passerelle d’application existante.

![Panneau de vue d’ensemble de passerelle d’application][1]

### Étape 2

Cliquez sur Écouteurs et cliquez sur le bouton Ajouter pour ajouter un écouteur.

### Étape 3

Remplissez les informations requises pour l’écouteur, et téléchargez le certificat .pfx. Lorsque vous avez terminé, cliquez sur OK.

**Nom** - il s’agit d’un nom convivial pour l’écouteur.

**Configuration IP frontale** - il s’agit de la configuration d’IP frontale utilisée pour l’écouteur.

**Port frontal (nom/port)** - un nom convivial pour le port utilisé sur le serveur frontal de la passerelle Application Gateway et le port utilisé.

**Protocole** - un commutateur qui détermine si https ou http est utilisé pour le serveur frontal.

**Certificat (nom/mot de passe)** - si le déchargement SSL est utilisé, un certificat .pfx est requis pour ce paramètre, et un nom convivial et un mot de passe sont requis.

![ajouter panneau d’écouteur][2]

## Créer une règle et l’associer à l’écouteur

L’écouteur a été créé. Il est temps de créer une règle pour gérer le trafic de l’écouteur.

### Étape 1 :

Cliquez sur **Règles** dans la passerelle Application Gateway, puis cliquez sur Ajouter.

![Panneau de règles Application Gateway][3]

### Étape 2 :

Sur le panneau**Ajouter une règle de base**, tapez le nom convivial de la règle et choisissez l’écouteur créé à l’étape précédente. Choisissez le pool principal approprié et la configuration http souhaitée, puis cliquez sur **OK**

![fenêtre de paramètres https][4]

Les paramètres sont maintenant enregistrés pour la passerelle Application Gateway. Le processus d’enregistrement pour ces paramètres peut prendre un certain temps avant leur disponibilité sur le portail ou via PowerShell. Une fois enregistrée, la passerelle Application Gateway gère le chiffrement et le déchiffrement du trafic. Tout le trafic entre la passerelle Application Gateway et les serveurs web principaux est géré via http. Toute communication lancée vers le client via le protocole https sera renvoyée chiffrée au client.

## Étapes suivantes

Pour savoir comment configurer une sonde d’intégrité personnalisée avec Azure Application Gateway, consultez [Créer une sonde d’intégrité personnalisée](application-gateway-create-gateway-portal.md).

[1]: ./media/application-gateway-ssl-portal/figure1.png
[2]: ./media/application-gateway-ssl-portal/figure2.png
[3]: ./media/application-gateway-ssl-portal/figure3.png
[4]: ./media/application-gateway-ssl-portal/figure4.png

<!---HONumber=AcomDC_0921_2016-->