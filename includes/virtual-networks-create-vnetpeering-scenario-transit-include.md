## Chaînage de services : Transit via des réseaux virtuels homologués

Les itinéraires système permettent la circulation automatique du trafic lié au déploiement, mais il existe des cas où il peut s’avérer utile d’acheminer les paquets au moyen d’une appliance virtuelle. Dans ce scénario, un abonnement comprend les deux réseaux virtuels, HubVNet et VNet1, comme illustré dans le schéma ci-dessous. Vous déployez Network Virtual Appliance (NVA) dans VNet HubVNet. Après avoir établi une homologation de réseaux virtuels entre HubVNet et VNet1, vous pouvez configurer des itinéraires définis par l’utilisateur et spécifier le prochain saut vers NVA dans HubVNet.

![Transit NVA](./media/virtual-networks-create-vnetpeering-scenario-transit-include/figure01.PNG)

> [AZURE.NOTE] Pour une question de simplicité, nous supposons que tous les réseaux virtuels se trouvent dans le même abonnement, mais cela fonctionne également dans les cas impliquant plusieurs abonnements.

La propriété clé pour activer le routage de transit est le paramètre Autoriser le trafic transféré. Ce paramètre permet d’accepter et d’envoyer le trafic depuis/vers NVA dans les réseaux virtuels homologués.

<!---HONumber=AcomDC_0928_2016-->