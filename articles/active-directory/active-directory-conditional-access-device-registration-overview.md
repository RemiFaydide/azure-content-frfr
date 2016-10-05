<properties
	pageTitle="Vue d’ensemble du service Azure Active Directory Device Registration | Microsoft Azure"
	description="est la base des scénarios d’accès conditionnel en fonction de l’appareil. Lors de l’inscription d’un appareil, Azure Active Directory Device Registration fournit une identité à l’appareil qui sera utilisée pour authentifier l’appareil lors de la connexion de l’utilisateur."
	services="active-directory"
	keywords="enregistrement de l’appareil, activer l’enregistrement de l’appareil, enregistrement de l’appareil et MDM"
	documentationCenter=""
	authors="femila"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="09/27/2016"
	ms.author="Markvi"/>

# Prise en main du service Azure Active Directory Device Registration

Azure Active Directory Device Registration est la base des scénarios d’accès conditionnel en fonction de l’appareil. Lors de l’inscription d’un appareil, Azure Active Directory Device Registration fournit une identité à l’appareil qui sera utilisée pour authentifier l’appareil lors de la connexion de l’utilisateur. L’appareil authentifié et les attributs de l’appareil peuvent alors être utilisés pour appliquer des stratégies d’accès conditionnel pour les applications qui sont hébergées sur le cloud et localement.

Quand ils sont associés à une solution de gestion des appareils mobiles comme Microsoft Intune, les attributs de l’appareil dans Azure Active Directory sont mis à jour avec des informations supplémentaires sur l’appareil. Cela vous permet de créer des règles d’accès conditionnel qui imposent que l’accès à partir des appareils réponde à vos normes de sécurité et de conformité. Pour plus d’informations sur l’inscription d’appareils dans Microsoft Intune, consultez [Inscrire des appareils pour la gestion dans Intune](https://docs.microsoft.com/intune/deploy-use/enroll-devices-in-microsoft-intune).

## Scénarios permis par Azure Active Directory Device Registration

Azure Active Directory Device Registration prend en charge les appareils iOS, Android et Windows. Les scénarios individuels qui utilisent Azure AD Device Registration peuvent avoir une prise en charge des plateformes et des exigences plus spécifiques. Ces scénarios sont les suivants :

- **Accès conditionnel aux applications hébergées en local** : vous pouvez utiliser des appareils inscrits avec des stratégies d’accès pour les applications qui sont configurées pour utiliser les services AD FS avec Windows Server 2012 R2. Pour plus d’informations sur la configuration d’un accès conditionnel en local, consultez la rubrique [Configuration d'un accès conditionnel en local à l'aide du service d'inscription d'appareils Azure Active Directory](active-directory-conditional-access-on-premises-setup.md).

- **Accès conditionnel pour les applications Office 365 avec Microsoft Intune** : les administrateurs informatiques peuvent configurer des stratégies d’appareil d’accès conditionnel pour sécuriser les ressources d’entreprise, tout en autorisant les travailleurs de l’information à accéder aux services sur les appareils compatibles. Pour plus d’informations, consultez la rubrique [Stratégies d’accès conditionnel basées sur les appareils pour les services Office 365](active-directory-conditional-access-device-policies.md).

##Configuration du service Azure Active Directory Device Registration

Vous devez activer Azure Active Directory Device Registration sur le portail Azure pour que les appareils mobiles puissent détecter le service en recherchant des enregistrements DNS connus. Vous devez configurer le DNS de votre entreprise afin que les appareils Windows 10, Windows 8.1, Windows 7, Android et iOS puissent détecter et utiliser le service. Vous pouvez afficher et activer/désactiver les appareils inscrits via le portail d’administration dans Azure Active Directory.

>[AZURE.NOTE]
 Pour prendre connaissance des dernières informations sur la configuration de l’inscription automatique des appareils, consultez [Configuration de l’inscription automatique auprès d’Azure Active Directory d’appareils Windows joints à un domaine](active-directory-conditional-access-automatic-device-registration-setup.md).

### Activer le service Azure Active Directory Device Registration

1. Connectez-vous au portail Microsoft Azure en tant qu’administrateur.
2. Dans le volet gauche, sélectionnez **Active Directory**.
3. Dans l’onglet **Annuaire**, sélectionnez votre annuaire.
4. Sélectionnez l’onglet **Configurer**.
5. Faites défiler jusqu’à la section intitulée **Appareils**.
6. Sélectionnez **TOUS** pour **LES UTILISATEURS PEUVENT JOINDRE DES APPAREILS À L’ESPACE DE TRAVAIL**.
7. Sélectionnez le nombre maximal d’appareils que vous souhaitez autoriser par utilisateur.

>[AZURE.NOTE]
L’inscription auprès de Microsoft Intune ou de Mobile Device Management pour Office 365 nécessite la jonction d’espace de travail. Si vous avez configuré l’un de ces services, TOUS est sélectionné et le bouton AUCUN est désactivé.

Par défaut, l’authentification à deux facteurs n’est pas activée pour le service. Elle est toutefois recommandée lors de l’inscription d’un appareil.

- Avant de demander une authentification à deux facteurs pour ce service, vous devez configurer un fournisseur d’authentification à deux facteurs dans Azure Active Directory et configurer vos comptes d’utilisateur pour Multi-Factor Authentication. Pour plus d’informations, consultez la page [Ajout de Multi-Factor Authentication à Azure Active Directory](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md).

- Si vous utilisez les services AD FS avec Windows Server 2012 R2, vous devez configurer un module d’authentification à deux facteurs dans ces services. Pour plus d’informations, consultez la page [Utilisation de Multi-Factor Authentication avec les services AD FS (Active Directory Federation Services)](../multi-factor-authentication/multi-factor-authentication-get-started-server.md).

## Configurer la détection du service Azure Active Directory Device Registration
Les appareils Windows 7 et Windows 8.1 détecteront le service Device Registration en associant le nom de compte de l’utilisateur à un nom de serveur d’inscription d’appareils connu.

Vous devez créer un enregistrement DNS CNAME qui pointe vers l’enregistrement A associé à votre service Azure Active Directory Device Registration. L’enregistrement CNAME doit utiliser le préfixe enterpriseregistration connu suivi du suffixe UPN utilisé par les comptes d’utilisateurs au sein de votre organisation. Si votre organisation utilise plusieurs suffixes UPN, plusieurs enregistrements CNAME doivent être créés dans le DNS.

Par exemple, si vous utilisez deux suffixes UPN dans votre organisation nommés @contoso.com et @region.contoso.com, vous devez créer les enregistrements DNS suivants :

| Entrée | Type | Adresse |
|-------------------------------------------|-------|------------------------------------|
| enterpriseregistration.contoso.com | CNAME | enterpriseregistration.windows.net |
| enterpriseregistration.region.contoso.com | CNAME | enterpriseregistration.windows.net |

## Afficher et gérer des objets appareil dans Azure Active Directory
1. Dans le portail d’administration Azure, vous pouvez afficher, bloquer et débloquer des appareils. Un appareil bloqué n’aura plus accès aux applications qui sont configurées de manière à n’autoriser que les appareils inscrits.
2. Connectez-vous au portail Microsoft Azure en tant qu’administrateur.
3. Dans le volet gauche, sélectionnez **Active Directory**.
4. Sélectionnez votre annuaire.
5. Cliquez sur l’onglet **Utilisateurs**. Sélectionnez ensuite un utilisateur pour afficher ses appareils.
6. Cliquez sur l’onglet **Appareils**.
7. Dans le menu déroulant, sélectionnez **Appareils inscrits**.
8. Ici, vous pouvez afficher, bloquer ou débloquer les appareils inscrits des utilisateurs.

## Rubriques supplémentaires

Le service Azure Active Directory Device Registration vous permet d’inscrire vos appareils Windows 7 et Windows 8.1 joints à un domaine. Les rubriques suivantes fournissent des informations supplémentaires sur les conditions préalables et les étapes requises pour configurer l’inscription de l’appareil sur des appareils Windows 7 et Windows 8.1.

- [Inscription automatique auprès d’Azure Active Directory d’appareils Windows joints à un domaine](active-directory-conditional-access-automatic-device-registration.md)
- [Configurer l’inscription automatique des appareils pour les appareils joints à un domaine Windows 7](active-directory-conditional-access-automatic-device-registration-windows7.md)
- [Configurer l’inscription automatique des appareils pour les appareils joints à un domaine Windows 8.1.](active-directory-conditional-access-automatic-device-registration-windows-8-1.md)
- [Inscription automatique auprès d’Azure Active Directory d’appareils Windows 10 joints à un domaine](active-directory-azureadjoin-devices-group-policy.md)

<!---HONumber=AcomDC_0928_2016-->