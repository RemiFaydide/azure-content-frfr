<properties
   pageTitle="Hébergement de plusieurs sites sur Application Gateway | Microsoft Azure"
   description="Cette page fournit une vue d’ensemble de la prise en charge de sites multiples pour la passerelle Application Gateway."
   documentationCenter="na"
   services="application-gateway"
   authors="amsriva"
   manager="rossort"
   editor="amsriva"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/07/2016"
   ms.author="amsriva"/>

# Hébergement de plusieurs sites Application Gateway

L’hébergement de plusieurs sites vous permet de configurer plusieurs applications web sur la même instance de passerelle Application Gateway. Cette fonctionnalité vous permet de configurer une topologie plus efficace pour vos déploiements en ajoutant jusqu’à 20 sites web à une passerelle Application Gateway. Chaque site web peut être dirigé vers son propre pool principal. Dans l’exemple suivant, la passerelle Application Gateway gère le trafic pour contoso.com et fabrikam.com avec deux pools de serveurs principaux : ContosoServerPool et FabrikamServerPool.

![imageURLroute](./media/application-gateway-multi-site-overview/multisite.png)

Les demandes adressées à http://contoso.com sont acheminées vers ContosoServerPool et les demandes adressées à http://fabrikam.com sont acheminées vers FabrikamServerPool.

De même, deux sous-domaines du même domaine parent peuvent également être hébergés sur le même déploiement de passerelle Application Gateway. Par exemple, les sous-domaines http://blog.contoso.com et http://app.contoso.com hébergés sur un déploiement de passerelle Application Gateway unique.


## En-têtes d’hôte et Indication du nom du serveur (SNI)
Il existe trois mécanismes pour activer l’hébergement de plusieurs sites dans la même infrastructure.

1. Hébergez plusieurs applications web, chacune sur une adresse IP unique.
2. Utilisez le nom d’hôte pour héberger plusieurs applications web sur la même adresse IP.
3. Utilisez des ports différents pour héberger plusieurs applications web sur la même adresse IP.

Actuellement, une passerelle Application Gateway obtient une adresse IP publique unique sur laquelle elle écoute le trafic. Par conséquent, la prise en charge de plusieurs applications, chacune avec sa propre adresse IP, n’est pas disponible actuellement. La passerelle Application Gateway prend en charge l’hébergement de plusieurs applications écoutant sur des ports différents, mais dans ce scénario, les applications doivent accepter le trafic sur des ports non standard, ce qui est rarement la configuration souhaitée. Application Gateway se base sur des en-têtes d’hôte HTTP 1.1 pour héberger plusieurs sites web sur les mêmes adresse IP et port. Les sites hébergés sur la passerelle Application Gateway peuvent également prendre en charge le déchargement SSL avec l’extension TLS d’indication du nom du serveur (SNI). Ce scénario signifie que le navigateur du client et la batterie de serveurs web principale doivent prendre en charge HTTP/1.1 et l’extension TLS définie dans RFC 6066.


## Élément de configuration d’écouteur

L’élément de configuration HTTPListener existant a été amélioré pour prendre en charge les éléments de nom d’hôte et d’indication du nom du serveur utilisés par la passerelle Application Gateway pour acheminer le trafic vers le pool principal approprié. L’exemple de code suivant est un extrait de l’élément HttpListeners issu du fichier de modèle.

    "httpListeners": [
                {
                    "name": "appGatewayHttpsListener1",
                    "properties": {
                        "FrontendIPConfiguration": {
                            "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendIPConfigurations/DefaultFrontendPublicIP"
                        },
                        "FrontendPort": {
                            "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendPorts/appGatewayFrontendPort443'"
                        },
                        "Protocol": "Https",
                        "SslCertificate": {
                            "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/sslCertificates/appGatewaySslCert1'"
                        },
                        "HostName": "contoso.com",
                        "RequireServerNameIndication": "true"
                    }
                },
                {
                    "name": "appGatewayHttpListener2",
                    "properties": {
                        "FrontendIPConfiguration": {
                            "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendIPConfigurations/appGatewayFrontendIP'"
                        },
                        "FrontendPort": {
                            "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendPorts/appGatewayFrontendPort80'"
                        },
                        "Protocol": "Http",
                        "HostName": "fabrikam.com",
                        "RequireServerNameIndication": "false"
                    }
                }
            ],




Vous pouvez consulter le [modèle Resource Manager avec l’hébergement de sites multiples](https://github.com/Azure/azure-quickstart-templates/blob/master/201-application-gateway-multihosting) pour un déploiement basé sur un modèle de bout en bout.

## Règle de routage

Aucune modification n’est requise dans la règle de routage. La règle de routage « De base » doit continuer à être choisie pour lier l’écouteur de site approprié au pool d’adresses principales correspondant.

	"requestRoutingRules": [
	{
		"name": "<ruleName1>",
		"properties": {
			"RuleType": "Basic",
			"httpListener": {
				"id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/httpListeners/appGatewayHttpsListener1')]"
			},
			"backendAddressPool": {
				"id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/ContosoServerPool')]"
			},
			"backendHttpSettings": {
				"id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
			}
		}

	},
	{
		"name": "<ruleName2>",
		"properties": {
			"RuleType": "Basic",
			"httpListener": {
				"id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/httpListeners/appGatewayHttpListener2')]"
			},
			"backendAddressPool": {
				"id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/FabrikamServerPool')]"
			},
			"backendHttpSettings": {
				"id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
			}
		}

	}
	]
	
## Étapes suivantes

Après avoir étudié l’hébergement de sites multiples, accédez à [Créer une passerelle Application Gateway avec l’hébergement de sites multiples](application-gateway-create-multisite-azureresourcemanager-powershell.md) pour créer une passerelle Application Gateway avec prise en charge de plusieurs applications web.

<!---HONumber=AcomDC_0921_2016-->