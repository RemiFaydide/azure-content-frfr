<properties 
   pageTitle="Vue d’ensemble d’Azure Automation DSC | Microsoft Azure" 
   description="Vue d'ensemble de la configuration d'état souhaité (DSC) Azure Automation, les termes s'y rapportant et les problèmes connus" 
   services="automation" 
   documentationCenter="dev-center-name" 
   authors="coreyp-at-msft" 
   manager="stevenka" 
   editor="tysonn"
   keywords="PowerShell DSC, Desired State Configuration, configuration d'état souhaité PowerShell DSC Azure"/>

<tags
   ms.service="automation"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="powershell"
   ms.workload="TBD" 
   ms.date="05/10/2016"
   ms.author="magoedte;coreyp"/>

# Vue d'ensemble d'Azure Automation DSC #

##Qu'est-ce qu'Azure Automation DSC ?##
Il peut être long et fastidieux de déployer et maintenir l’état souhaité de vos serveurs et ressources d’application, sans parler du risque d’erreur que cela implique. Avec Azure Automation Desired State Configuration (DSC), vous pouvez déployer de manière cohérente, surveiller en toute confiance et mettre à jour automatiquement l’état souhaité de toutes vos ressources informatiques à partir du cloud. Basé sur PowerShell DSC, Automation DSC peut aligner la configuration de l’ordinateur sur un état spécifique à travers l’ensemble des ordinateurs physiques et des machines virtuelles sous Windows ou Linux, aussi bien dans le cloud que sur site. Vous pouvez bénéficier de services informatiques livrés en continu et contrôlés de manière cohérente tout en gérant en toute simplicité les modifications rapides intervenant dans votre environnement informatique hybride et hétérogène.

Azure Automation DSC s'appuie sur les concepts de base introduits dans PowerShell DSC pour fournir une expérience de gestion des configurations encore plus facile. Azure Automation DSC apporte la même couche de gestion à la [configuration d'état souhaité PowerShell](https://msdn.microsoft.com/powershell/dsc/overview) que celle proposée aujourd’hui par Azure Automation pour l’écriture de scripts PowerShell.

Azure Automation DSC vous permet de [créer et de gérer des configurations d'état souhaité PowerShell](https://technet.microsoft.com/library/dn249918.aspx), d'importer des [ressources DSC](https://technet.microsoft.com/library/dn282125.aspx) et de générer des configurations de nœuds DSC (documents MOF), le tout dans le cloud. Ces éléments DSC seront placés sur le [server collecteur DSC](https://technet.microsoft.com/library/dn249913.aspx) Azure Automation afin que les nœuds cibles (tels que les machines virtuelles et physiques) dans le cloud ou localement puissent les choisir, se conformer à l'état qu'elle spécifie et retourner à Azure Automation un rapport attestant de leur conformité à l'état souhaité.

Lire de la documentation vous enchante moyennement ? Jetez un œil à la vidéo ci-dessous, publiée en mai 2015 à l’occasion de l’annonce d’Azure Automation DSC. **Remarque :** bien que les concepts et le cycle de vie abordés dans cette vidéo soient corrects, Azure Automation DSC a beaucoup progressé depuis l’enregistrement de cette vidéo. Désormais disponible au public, il dispose d’une interface utilisateur plus étendue dans le portail Azure et prend en charge des fonctionnalités supplémentaires.

> [AZURE.VIDEO microsoft-ignite-2015-heterogeneous-configuration-management-using-microsoft-azure-automation]



## Conditions d'utilisation d'Azure Automation DSC ##
### Configuration ###
PowerShell DSC a introduit un nouveau concept appelé « configurations ». Les configurations vous permettent de définir, via la syntaxe PowerShell, l'état souhaité pour votre environnement. Pour configurer votre environnement à l'aide de DSC, commencez par définir un bloc de script Windows PowerShell en utilisant le mot clé « configuration », suivi d'un identificateur, puis d'accolades ({}) pour délimiter le bloc.

![texte de remplacement](./media/automation-dsc-overview/AADSC_1.png)

Dans le bloc de configuration, vous pouvez définir des blocs de configuration de nœuds qui spécifient la configuration souhaitée pour un ensemble de nœuds (machines) de votre environnement qui doivent être configurés exactement de la même manière. De cette façon, une configuration de nœuds représente un « rôle » qu'un ou plusieurs nœuds doivent adopter. Un bloc de configuration de nœuds commence par le mot clé « node ». Faites suivre ce mot clé du nom du rôle, qui peut être une variable ou une expression. Après le nom du rôle, utilisez des accolades {} pour délimiter le bloc de configuration de nœuds.

![texte de remplacement](./media/automation-dsc-overview/AADSC_2.png)
 
Dans le bloc de configuration de nœuds, vous pouvez définir des blocs de ressources pour configurer les ressources DSC spécifiques. Un bloc de ressources commence par le nom de la ressource, suivi de l'identificateur que vous souhaitez spécifier pour ce bloc, puis d'accolades {} pour délimiter le bloc.

![texte de remplacement](./media/automation-dsc-overview/AADSC_3.png)

Pour plus d'informations sur le mot clé « configuration », consultez le blog suivant : [Understanding Configuration Keyword in Desired State Configuration](http://blogs.msdn.com/b/powershell/archive/2013/11/05/understanding-configuration-keyword-in-desired-state-configuration.aspx "Présentation du mot clé « configuration » dans la configuration d'état souhaité")

L'exécution (compilation) d'une configuration DSC produit une ou plusieurs configurations de nœuds DSC (documents MOF), qui sont les nœuds DSC devant être en conformité avec l'état souhaité.

Azure Automation DSC vous permet d'importer, de créer et de compiler des configurations DSC dans Azure Automation, de la même façon que les Runbooks peuvent être importés, créés et démarrés dans Azure Automation.

>[AZURE.IMPORTANT] Une configuration ne doit contenir qu'un seul bloc de configuration, et doit porter le même nom que la configuration dans Azure Automation DSC.


###Configuration de nœuds###

Lors de la compilation d'une configuration DSC, une ou plusieurs configurations de nœuds sont générées, en fonction des blocs Node de la configuration. Une configuration de nœud est semblable à un document « MOF » ou « document de configuration » (si ces termes PS DSC vous sont familiers) et représente un « rôle », par exemple de serveur web ou de travail, dont un ou plusieurs nœuds doivent adopter l’état souhaité ou vérifier leur conformité à cet état. Les noms de configurations de nœuds dans Azure Automation DSC prennent la forme « nom\_configuration.nom\_bloc\_de\_la\_configuration\_du\_nœud ».

Les nœuds PS DSC sont informés des configurations de nœuds qu'ils doivent adopter via des méthodes Push ou Pull DSC. Azure Automation DSC repose sur la méthode Pull DSC, où les nœuds demandent des configurations de nœuds qu'ils doivent appliquer à partir du serveur collecteur Azure Automation DSC. Étant donné que les nœuds effectuent la demande à Azure Automation DSC, ils peuvent se trouver derrière des pare-feu, avoir tous les ports entrants fermés, etc. Ils nécessitent simplement un accès sortant à Internet, directement ou via un proxy.


###Nœud###

Un nœud DSC est une machine dont la configuration est gérée par DSC. Il peut s’agir d’une machine virtuelle Azure Windows ou Linux, d’une machine virtuelle locale, d’un hôte physique ou d’une machine hébergée dans un autre cloud public. Les nœuds adoptent des configurations de nœuds pour devenir conformes et assurer leur conformité avec l’état souhaité qu’ils définissent. Ils peuvent également envoyer à un serveur de rapports un rapport sur leur état de configuration et sur leur conformité avec l’état souhaité.

Azure Automation DSC facilite l’intégration de nœuds en vue de leur gestion par Azure Automation DSC, et permet de changer la configuration de nœud affectée à chaque nœud côté serveur, de sorte que la prochaine fois qu’un nœud recherchera des instructions sur le serveur, il adoptera un rôle différent et modifiera sa configuration et son état de conformité sur lesquels sera basé le rapport.


###Ressource###
Les ressources DSC créent des blocs que vous pouvez utiliser pour définir une configuration d'état souhaité (DSC) Windows PowerShell. DSC est fourni avec un ensemble de ressources intégrées, telles que des fichiers et des dossiers, des fonctionnalités et des rôles de serveur, des paramètres de registre, des variables d’environnement, ainsi que des services et des processus. Pour connaître la liste complète des ressources DSC intégrées et savoir comment les utiliser, consultez [Ressources de configuration d'état souhaité Windows PowerShell intégrées](https://technet.microsoft.com/library/dn249921.aspx).

Il est également possible d'importer des ressources DSC dans le cadre des modules PowerShell pour étendre l'ensemble de ressources DSC intégrées. Les ressources autres que les ressources par défaut seront extraites par les nœuds DSC à partir du serveur collecteur DSC, si la configuration de nœud que le nœud est censé adopter contient des références à ces ressources. Pour savoir comment créer des ressources personnalisées, consultez [Créer des ressources de configuration d'état souhaité Windows PowerShell personnalisées](https://technet.microsoft.com/library/dn249927.aspx).

Azure Automation DSC est fourni avec exactement les mêmes ressources DSC intégrées que PS DSC. Des ressources supplémentaires peuvent être ajoutées à Azure Automation DSC en important dans Azure Automation des modules PowerShell contenant les ressources.


###Tâche de compilation###
Dans Azure Automation DSC, une tâche de compilation est une instance de compilation d'une configuration destinée à créer une ou plusieurs configurations de nœuds. Elles sont semblables aux tâches de Runbook Azure Automation, sauf qu'elles n'effectuent en fait aucune tâche excepté créer des configurations de nœuds. Toutes les configurations de nœuds créées par une tâche de compilation sont automatiquement placées sur le serveur collecteur Azure Automation DSC, et remplacent les versions précédentes des configurations de nœuds, si elles existaient pour cette configuration. Le nom d’une configuration de nœud produite par une tâche de compilation se présente sous la forme « nom\_configuration.nom\_bloc\_de\_la\_configuration\_du\_nœud ». Par exemple, la compilation de la configuration ci-dessous produirait une configuration de nœud unique appelée « MyConfiguration.webserver »


![texte de remplacement](./media/automation-dsc-overview/AADSC_5.png)


>[AZURE.NOTE] À l'instar des Runbooks, les configurations peuvent être publiées. Cela n'est pas lié au placement des éléments DSC sur le serveur collecteur Azure Automation DSC. Suite à une tâche de compilation, les éléments DSC sont placés sur le serveur collecteur Azure Automation DSC. Pour plus d'informations sur la « publication » dans Azure Automation, consultez [Publication d'un Runbook](https://msdn.microsoft.com/library/dn903765.aspx).


##Cycle de vie d’Azure Automation DSC##
Passer d’un compte Automation vide à un ensemble géré de nœuds correctement configurés implique un ensemble de processus permettant de définir des configurations, de convertir ces configurations en configurations de nœud et d’intégrer ces nœuds à Azure Automation DSC et à ces configurations de nœud. Le diagramme suivant illustre le cycle de vie d’Azure Automation DSC :

![texte de remplacement](./media/automation-dsc-overview/DSCLifecycle.png)


L’image suivante illustre le processus détaillé du cycle de vie de DSC. Elle décrit les différentes approches utilisées pour importer une configuration et l’appliquer à des nœuds dans Azure Automation, présente les composants requis pour permettre à un ordinateur local de prendre en charge DSC, et illustre les interactions entre les différents composants.

![Architecture de DSC](./media/automation-dsc-overview/dsc-architecture.png)

##Pièges/Problèmes connus :##

- Lors d’une mise à niveau vers WMF 5 RTM, si l’ordinateur est déjà inscrit en tant que nœud dans Azure Automation DSC, vous devez annuler son inscription dans Azure Automation DSC et l’enregistrer à nouveau après avoir effectué la mise à niveau.

- Azure Automation DSC ne prend pas en charge les configurations DSC partielles ou composites pour le moment. Il est toutefois possible d’importer et utiliser les ressources DSC composites dans des configurations DSC d’Azure Automation exactement comme dans une instance PowerShell locale, ce qui permet d’en réutiliser la configuration.

- La dernière version de WMF 5 doit être installée pour que l'agent PowerShell DSC pour Windows puisse communiquer avec Azure Automation. La dernière version de l’agent PowerShell DSC pour Linux doit être installée pour que Linux puisse communiquer avec Azure Automation.

- Le serveur collecteur PowerShell DSC traditionnel s'attend à ce que des fichiers ZIP de modules soient placés sur le serveur collecteur au format **NomModule\_Version.zip**. Azure Automation s'attend à ce que des modules PowerShell soient importés avec des noms sous la forme **NomModule.zip**. Pour plus d'informations sur le format de module d'intégration nécessaire pour importer le module dans Azure Automation, consultez [ce billet de blog](https://azure.microsoft.com/blog/2014/12/15/authoring-integration-modules-for-azure-automation/).

- Les modules PowerShell importés dans Azure Automation ne peuvent pas contenir de fichiers .doc ou .docx. Certains modules PowerShell contenant des ressources DSC contiennent ces fichiers à des fins d'aide. Ces fichiers doivent être supprimés des modules avant l'importation de ces derniers dans Azure Automation.

- Quand un nœud est d’abord enregistré avec un compte Azure Automation, ou que le nœud est modifié pour être mappé à une autre configuration de nœud côté serveur, son état sera « Conforme », même s’il n’est pas réellement conforme à la configuration de nœud à laquelle il est maintenant mappé. Dès que le nœud a effectué sa première collecte et qu’il a envoyé son premier rapport, une fois que l’inscription a eu lieu ou qu’un mappage de configuration de nœud a changé, l’état du nœud peut être approuvé.

- Lorsqu’une machine virtuelle Microsoft Azure est intégrée en vue d’être gérée par Azure Automation DSC, une heure peut être nécessaire avant que la machine virtuelle apparaisse en tant que nœud DSC dans Azure Automation. Cela est dû à l'installation de Windows Management Framework 5.0 sur la machine virtuelle par l'extension Azure VM DSC, nécessaire à l’intégration de la machine virtuelle dans Azure Automation DSC.

- Une fois inscrit, chaque nœud négocie automatiquement un certificat unique pour l'authentification qui expire après un an. À ce stade, le protocole d'inscription DSC PowerShell ne peut pas renouveler automatiquement les certificats lorsqu'ils approchent de l'expiration, donc vous devez réinscrire les nœuds après une année. Avant la réinscription, assurez-vous que chaque nœud exécute Windows Management Framework 5.0 RTM. Si le certificat d'authentification d'un nœud expire et si le nœud n'est pas réinscrit, le nœud ne pourra pas communiquer avec Azure Automation et sera marqué « Ne répond pas ». La réinscription s'effectue de la même façon que l'inscription initiale du nœud. La réinscription effectuée dans un délai de 90 jours ou moins à partir de l'heure d'expiration du certificat, ou à tout moment après le délai d'expiration du certificat, entraîne la génération et l'utilisation d'un nouveau certificat.

- Lors d’une mise à niveau vers WMF 5 RTM, si l’ordinateur est déjà inscrit en tant que nœud dans Azure Automation DSC, vous devez annuler son inscription dans Azure Automation DSC et l’enregistrer à nouveau après avoir effectué la mise à niveau. Avant la réinscription, supprimez le fichier $env:windir\\system32\\configuration\\DSCEngineCache.mof.

- Les applets de commande PowerShell DSC risquent de ne pas fonctionner si WMF 5 RTM est installé au-dessus de WMF 5 Production Preview. Pour résoudre ce problème, exécutez la commande suivante dans une session PowerShell avec élévation de privilèges (exécuter en tant qu’administrateur) : `mofcomp $env:windir\system32\wbem\DscCoreConfProv.mof`
 

##Articles connexes##

- [Gestion de machines avec Azure Automation DSC](../automation/automation-dsc-onboarding.md)
- [Compilation de configurations dans Azure Automation DSC](../automation/automation-dsc-compile.md)
- [Applets de commande Azure Automation DSC](https://msdn.microsoft.com/library/mt244122.aspx)
- [Tarification d’Azure Automation DSC](https://azure.microsoft.com/pricing/details/automation/)
- [Déploiement continu sur les machines virtuelles IaaS à l’aide d'Azure Automation DSC et Chocolatey](automation-dsc-cd-chocolatey.md)

<!---HONumber=AcomDC_0921_2016-->