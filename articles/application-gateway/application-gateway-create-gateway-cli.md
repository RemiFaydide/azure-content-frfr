<properties
   pageTitle="Créer une passerelle Application Gateway à l’aide de l’interface de ligne de commande Azure de Resource Manager | Microsoft Azure"
   description="Découvrez comment créer une passerelle Application Gateway à l’aide de l’interface de ligne de commande Azure dans Resource Manager"
   services="application-gateway"
   documentationCenter="na"
   authors="georgewallace"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/09/2016"
   ms.author="gwallace" />

# Créer une passerelle Application Gateway à l’aide de l’interface de ligne de commande Azure

La passerelle Azure Application Gateway est un équilibreur de charge de couche 7. Elle assure l’exécution des requêtes HTTP de basculement et de routage des performances entre serveurs locaux ou dans le cloud. Une passerelle Application Gateway offre les fonctionnalités de livraison d’applications suivantes : équilibrage de charge HTTP, affinité de session basée sur les cookies et déchargement SSL (Secure Sockets Layer), sondes d’intégrité personnalisées et prise en charge de plusieurs sites.

> [AZURE.SELECTOR]
- [Portail Azure](application-gateway-create-gateway-portal.md)
- [Commandes PowerShell pour Azure Resource Manager](application-gateway-create-gateway-arm.md)
- [Azure Classic PowerShell](application-gateway-create-gateway.md)
- [Modèle Azure Resource Manager](application-gateway-create-gateway-arm-template.md)
- [Interface de ligne de commande Azure](application-gateway-create-gateway-cli.md)

## Condition préalable : installer l’interface de ligne de commande Azure

Pour effectuer les étapes indiquées dans cet article, vous devez [installer l’interface de ligne de commande Azure pour Mac, Linux et Windows (Azure CLI)](../xplat-cli-install.md) et [ouvrir une session sur Azure](../xplat-cli-connect.md).

> [AZURE.NOTE] Si vous n’avez pas de compte Azure, vous devez vous en procurer un. Inscrivez-vous à un [essai gratuit ici](../active-directory/sign-up-organization.md).

## Scénario

Dans ce scénario, vous allez apprendre à créer une passerelle Application Gateway à l’aide du portail Azure.

Ce scénario va :

- créer une passerelle Application Gateway moyenne avec deux instances ;
- créer un réseau virtuel nommé AdatumAppGatewayVNET avec un bloc CIDR réservé de 10.0.0.0/16 ;
- créer un sous-réseau appelé Appgatewaysubnet qui utilise 10.0.0.0/28 comme bloc CIDR ;
- configurer un certificat pour le déchargement SSL.

![Exemple de scénario][scenario]

>[AZURE.NOTE] La configuration supplémentaire de la passerelle Application Gateway, y compris les sondes d’intégrité personnalisées, les adresses de pool principal et les règles supplémentaires sont configurées après avoir configuré la passerelle Application Gateway et non lors du déploiement initial.

## Avant de commencer

La passerelle Application Gateway Azure requiert son propre sous-réseau. Lorsque vous créez un réseau virtuel, assurez-vous que vous laissez suffisamment d’espace d’adresse pour disposer de plusieurs sous-réseaux. Une fois que vous avez déployé une passerelle Application Gateway sur un sous-réseau, seules les passerelles Application Gateway supplémentaires peuvent être ajoutées au sous-réseau.

## Connexion à Azure

Ouvrez **l’invite de commandes Microsoft Azure** et connectez-vous.

    azure login

Une fois que vous avez tapé l’exemple précédent, un code est fourni. Accédez à https://aka.ms/devicelogin dans un navigateur pour continuer le processus de connexion.

![Invite de commande affichant le code de l’appareil][1]

Dans le navigateur, entrez le code que vous avez reçu. Vous êtes redirigé vers une page de connexion.

![Page de navigateur pour la saisie du code][2]

Une fois que vous êtes connecté, fermez le navigateur pour poursuivre le scénario.

![Connexion réussie][3]

## Basculer en mode Resource Manager

    azure config mode arm

## Créer le groupe de ressources

Avant de créer la passerelle Application Gateway, un groupe de ressources est créé pour contenir cette dernière. Voici la commande.

    azure group create -n AdatumAppGatewayRG -l eastus

## Créez un réseau virtuel

Une fois le groupe de ressources créé, un réseau virtuel est créé pour la passerelle Application Gateway. Dans l’exemple suivant, l’espace d’adressage était 10.0.0.0/16 tel que défini dans les notes du scénario précédent.

    azure network vnet create -n AdatumAppGatewayVNET -a 10.0.0.0/16 -g AdatumAppGatewayRG -l eastus

## Créer un sous-réseau

Une fois le réseau virtuel créé, un sous-réseau est ajouté pour la passerelle Application Gateway. Si vous envisagez d’utiliser la passerelle Application Gateway avec une application web hébergée dans le même réseau virtuel que la passerelle Application Gateway, veillez à laisser suffisamment de place pour un autre sous-réseau.

    azure network vnet subnet create -g AdatumAppGatewayRG -n Appgatewaysubnet -v AdatumAppGatewayVNET -a 10.0.0.0/28 

## Créer la passerelle Application Gateway

Une fois le réseau virtuel et le sous-réseau créés, les conditions préalables pour la passerelle Application Gateway sont remplies. En outre, un certificat .pfx exporté précédemment et le mot de passe de ce dernier sont requis pour l’étape suivante. Les adresses IP utilisées pour le backend sont celles de votre serveur principal. Ces valeurs peuvent être des adresses IP privées dans le réseau virtuel, des adresses IP publiques ou des noms de domaine complets pour vos serveurs principaux.

    azure network application-gateway create -n AdatumAppGateway -l eastus -g AdatumAppGatewayRG -e AdatumAppGatewayVNET -m Appgatewaysubnet -r 134.170.185.46,134.170.188.221,134.170.185.50 -y c:\AdatumAppGateway\adatumcert.pfx -x P@ssw0rd -z 2 -a Standard_Medium -w Basic -j 443 -f Enabled -o 80 -i http -b https -u Standard



Cet exemple crée une passerelle Application Gateway de base avec les paramètres par défaut pour l’écouteur, le pool principal, les paramètres http principaux et les règles. Cela configure également le déchargement SSL. Vous pouvez modifier ces paramètres en fonction de votre déploiement une fois l’approvisionnement réussi. Si vous avez déjà défini votre application web avec le pool principal aux étapes précédentes, une fois la création effective, l’équilibrage de charge commence.

## Étapes suivantes

Apprenez à créer des sondes d’intégrité personnalisées en vous rendant sur [Créer une sonde d’intégrité personnalisée](application-gateway-create-probe-portal.md)

Découvrez comment configurer le déchargement SSL et éviter à vos serveurs web le déchiffrement SSL coûteux en vous rendant sur [Configurer le déchargement SSL](application-gateway-ssl-arm.md)

<!--Image references-->

[scenario]: ./media/application-gateway-create-gateway-cli/scenario.png
[1]: ./media/application-gateway-create-gateway-cli/figure1.png
[2]: ./media/application-gateway-create-gateway-cli/figure2.png
[3]: ./media/application-gateway-create-gateway-cli/figure3.png

<!---HONumber=AcomDC_0921_2016-->