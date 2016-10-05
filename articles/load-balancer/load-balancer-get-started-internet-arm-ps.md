<properties
   pageTitle="Créer un équilibreur de charge accessible sur Internet dans Resource Manager à l’aide de PowerShell | Microsoft Azure"
   description="Découvrir comment créer un équilibreur de charge accessible sur Internet dans Resource Manager à l’aide de PowerShell"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags
  ms.service="load-balancer"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="na"
  ms.workload="infrastructure-services"
  ms.date="08/31/2016"
  ms.author="sewhee" />

# <a name="get-started"></a>Création d’un équilibreur de charge accessible sur Internet dans Resource Manager à l’aide de PowerShell

[AZURE.INCLUDE [load-balancer-get-started-internet-arm-selectors-include.md](../../includes/load-balancer-get-started-internet-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)] Cet article traite du modèle de déploiement de Resource Manager. Vous pouvez également [découvrir comment créer un équilibreur de charge accessible sur Internet à l’aide du modèle de déploiement classique](load-balancer-get-started-internet-classic-cli.md).

[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## Déploiement de la solution à l’aide d’Azure PowerShell

La procédure suivante explique comment créer un équilibreur de charge accessible sur Internet à l’aide d’Azure Resource Manager avec PowerShell. Avec Azure Resource Manager, toutes les ressources sont créées et configurées individuellement, puis rassemblées pour en créer une unique.

Vous devez créer et configurer les objets suivants pour déployer un équilibreur de charge :

- Configuration d’adresses IP frontales : contient les adresses IP publiques (PIP) pour le trafic réseau entrant.
- Pool d’adresses principales : contient des interfaces réseau (NIC) pour que les machines virtuelles puissent recevoir le trafic réseau de l’équilibreur de charge.
- Règles d’équilibrage de charge : contient des règles qui mappent un port public situé sur l’équilibreur de charge à un port du pool d’adresses principales.
- Règles NAT entrantes : contient des règles qui mappent un port public situé sur l’équilibreur de charge vers le port d’une machine virtuelle spécifique située dans le pool d’adresses principales.
- Sondes : contient les sondes d’intégrité utilisées pour vérifier la disponibilité des instances de machines virtuelles du pool d’adresses principales.

Pour plus d’informations, consultez [Prise en charge d’un équilibreur de charge par Azure Resource Manager](load-balancer-arm.md).

## Configurer PowerShell pour utiliser Resource Manager

Assurez-vous que vous disposez de la dernière version de production du module Azure Resource Manager pour PowerShell :

1. Connectez-vous à Azure.

        Login-AzureRmAccount

    À l’invite, entrez vos informations d’identification.

2. Vérifiez les abonnements associés au compte.

        Get-AzureRmSubscription

3. Parmi vos abonnements Azure, choisissez celui que vous souhaitez utiliser.

        Select-AzureRmSubscription -SubscriptionId 'GUID of subscription'

4. Créez un groupe de ressources. (Ignorez cette étape si vous utilisez un groupe de ressources existant.)

        New-AzureRmResourceGroup -Name NRP-RG -location "West US"

## Créez un réseau virtuel et une adresse IP publique pour le pool d’adresses IP frontales

1. Créez un sous-réseau et un réseau virtuel.

        $backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
        New-AzureRmvirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet

2. Créez une ressource d’adresse IP publique Azure nommée **PublicIP** utilisable par un pool d’adresses IP frontales avec le nom DNS **loadbalancernrp.westus.cloudapp.azure.com**. La commande suivante utilise le type d’allocation statique.

        $publicIP = New-AzureRmPublicIpAddress -Name PublicIp -ResourceGroupName NRP-RG -Location 'West US' –AllocationMethod Static -DomainNameLabel loadbalancernrp

    >[AZURE.IMPORTANT] L’équilibreur de charge utilise l’étiquette du domaine de l’adresse IP publique en tant que préfixe du nom de domaine complet (FQDN). Cet usage diffère du modèle de déploiement classique qui utilise le service cloud en tant que nom de domaine complet de l’équilibreur de charge. Dans cet exemple, le nom de domaine complet est **loadbalancernrp.westus.cloudapp.azure.com**.

## Créer un pool d’adresses IP frontales et un pool d’adresses principales

1. Créez un pool d’adresses IP frontales nommé **LB-Frontend** qui utilise la ressource **PublicIp**.

        $frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name LB-Frontend -PublicIpAddress $publicIP

2. Créez un pool d’adresses principales nommé **LB-backend**.

        $beaddresspool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name LB-backend

## Créer des règles NAT, une règle d’équilibreur de charge, une sonde et un équilibreur de charge

Cet exemple crée les éléments suivants :

- Une règle NAT pour transférer l’ensemble du trafic sur le port 3441 vers le port 3389
- Une règle NAT pour transférer l’ensemble du trafic sur le port 3442 vers le port 3389
- Une règle de sonde qui vérifie l’intégrité dans une page nommée **HealthProbe.aspx**
- Une règle d’équilibreur de charge pour équilibrer tout le trafic entrant sur le port 80 vers le port 80 des adresses du pool principal
- Un équilibreur de charge qui utilise tous les objets ci-dessus

Suivez ces étapes :

1. Créez les règles NAT.

        $inboundNATRule1= New-AzureRmLoadBalancerInboundNatRuleConfig -Name RDP1 -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

        $inboundNATRule2= New-AzureRmLoadBalancerInboundNatRuleConfig -Name RDP2 -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389

2. Créer une sonde d’intégrité. Il existe deux façons de configurer une sonde :

    Sonde HTTP

        $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name HealthProbe -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

    Sonde TCP

        $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name HealthProbe -Protocol Tcp -Port 80 -IntervalInSeconds 15 -ProbeCount 2

3. Créez une règle d’équilibreur de charge.

        $lbrule = New-AzureRmLoadBalancerRuleConfig -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool  $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80

4. Créez l’équilibrage de charge en utilisant les objets créés précédemment.

        $NRPLB = New-AzureRmLoadBalancer -ResourceGroupName NRP-RG -Name NRP-LB -Location 'West US' -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe

## Créer des cartes réseau

Créez des interfaces réseau (ou modifiez des interfaces existantes), puis associez-les à des règles NAT, des règles d’équilibreur de charge et des sondes :

1. Obtenez le réseau virtuel et le sous-réseau du réseau virtuel où les cartes réseau doivent être créées.

        $vnet = Get-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG
        $backendSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet

2. Créez une carte réseau nommée **lb-nic1-be**, puis associez-la à la première règle NAT et au premier (et unique) pool d’adresses principales.

        $backendnic1= New-AzureRmNetworkInterface -ResourceGroupName NRP-RG -Name lb-nic1-be -Location 'West US' -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]

3. Créez une carte réseau nommée **lb-nic2-be**, puis associez-la à la deuxième règle NAT et au premier (et unique) pool d’adresses principales.

        $backendnic2= New-AzureRmNetworkInterface -ResourceGroupName NRP-RG -Name lb-nic2-be -Location 'West US' -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]

4. Vérifiez les cartes réseau.

        $backendnic1

    Sortie attendue :

        Name                 : lb-nic1-be
        ResourceGroupName    : NRP-RG
        Location             : westus
        Id                   : /subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
        Etag                 : W/"d448256a-e1df-413a-9103-a137e07276d1"
        ResourceGuid         : 896cac4f-152a-40b9-b079-3e2201a5906e
        ProvisioningState    : Succeeded
        Tags                 :
        VirtualMachine       : null
        IpConfigurations     : [
                            {
                            "Name": "ipconfig1",
                            "Etag": "W/"d448256a-e1df-413a-9103-a137e07276d1"",
                            "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/ipconfig1",
                            "PrivateIpAddress": "10.0.2.6",
                            "PrivateIpAllocationMethod": "Static",
                            "Subnet": {
                                "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/virtualNetworks/NRPVNet/subnets/LB-Subnet-BE"
                            },
                            "ProvisioningState": "Succeeded",
                            "PrivateIpAddressVersion": "IPv4",
                            "PublicIpAddress": {
                                "Id": null
                            },
                            "LoadBalancerBackendAddressPools": [
                                {
                                "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/backendAddressPools/LB-backend"
                                }
                            ],
                            "LoadBalancerInboundNatRules": [
                                {
                                "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/inboundNatRules/RDP1"
                                }
                            ],
                            "Primary": true,
                            "ApplicationGatewayBackendAddressPools": []
                            }
                        ]
        DnsSettings          : {
                            "DnsServers": [],
                            "AppliedDnsServers": [],
                            "InternalDomainNameSuffix": "prcwibzcuvie5hnxav0yjks2cd.dx.internal.cloudapp.net"
                        }
        EnableIPForwarding   : False
        NetworkSecurityGroup : null
        Primary              : 

5. Utilisez l'applet de commande `Add-AzureRmVMNetworkInterface` pour affecter les cartes réseau aux différentes machines virtuelles.

## Créer une machine virtuelle

Pour obtenir des instructions concernant la création d’une machine virtuelle et l’affectation d’une carte réseau, reportez-vous à l’option 5 de la rubrique [Création et préconfiguration d’une machine virtuelle Windows avec Resource Manager et Azure PowerShell](../virtual-machines/virtual-machines-windows-create-powershell.md#Example).

## Ajouter l’interface réseau à l’équilibrage de charge

1. Récupérez l’équilibrage de charge d’Azure.

    Charger la ressource d'équilibrage de charge dans une variable (si vous ne l'avez pas encore fait). La variable est appelée **$lb**. Utilisez les mêmes noms que ceux de la ressource d’équilibreur de charge que vous avez créée précédemment.

        $lb= get-azurermloadbalancer –name NRP-LB -resourcegroupname NRP-RG

2. Chargez la configuration du serveur principal dans une variable.

        $backend=Get-AzureRmLoadBalancerBackendAddressPoolConfig -name backendpool1 -LoadBalancer $lb

3. Chargez l’interface réseau déjà créée dans une variable. Le nom de la variable est **$nic**. Le nom de l’interface réseau est le même que celui de l’exemple précédent.

        $nic =get-azurermnetworkinterface –name lb-nic1-be -resourcegroupname NRP-RG

4. Modifiez la configuration du serveur principal sur l’interface réseau.

        $nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$backend

5. Enregistrer l'objet d'interface réseau.

        Set-AzureRmNetworkInterface -NetworkInterface $nic

    Une fois l’interface réseau ajoutée au pool principal d’équilibreurs de charge, elle commence à recevoir le trafic réseau selon les règles d’équilibrage de charge pour cette ressource d’équilibreur de charge.

## Mettre à jour un équilibreur de charge existant

1. À l’aide de l’équilibreur de charge de l’exemple précédent, attribuez un objet d’équilibreur de charge à la variable **$slb** à l’aide de `Get-AzureLoadBalancer`.

        $slb = get-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

2. Dans l’exemple suivant, vous allez ajouter une nouvelle règle NAT entrante à un équilibreur de charge existant, en utilisant le port 81 dans le pool frontal et le port 8181 pour le pool principal.

        $slb | Add-AzureRmLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol TCP

3. Enregistrez la nouvelle configuration à l’aide de `Set-AzureLoadBalancer`.

        $slb | Set-AzureRmLoadBalancer

## Supprimer un équilibreur de charge

Utilisez la commande `Remove-AzureLoadBalancer` pour supprimer un équilibreur de charge créé précédemment appelé **NRP-LB** dans un groupe de ressources appelé **NRP-RG**.

    Remove-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

>[AZURE.NOTE] Vous pouvez utiliser le commutateur facultatif **-Force** pour éviter l’invite relative à la suppression.

## Étapes suivantes

[Prise en main de la configuration d’un équilibrage de charge interne](load-balancer-get-started-ilb-arm-ps.md)

[Configuration d'un mode de distribution d'équilibrage de charge](load-balancer-distribution-mode.md)

[Configuration des paramètres du délai d’expiration TCP inactif pour votre équilibrage de charge](load-balancer-tcp-idle-timeout.md)

<!----HONumber=AcomDC_0921_2016-->