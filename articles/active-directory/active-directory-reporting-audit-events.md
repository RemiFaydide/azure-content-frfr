<properties
   pageTitle="Événements de rapport d’audit d’Azure Active Directory | Microsoft Azure"
   description="Événements audités disponibles pour l'affichage et le téléchargement à partir d'Azure Active Directory"
   services="active-directory"
   documentationCenter=""
   authors="dhanyahk"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/19/2016"
   ms.author="dhanyahk"/>

# Événements de rapport d’audit d’Azure Active Directory

*Cette documentation fait partie du guide [Azure Active Directory Reporting Guide](active-directory-reporting-guide.md).*

Le rapport d'audit d'Azure Active Directory permet aux clients d'identifier les actions privilégiées qui se sont produites dans leur Azure Active Directory. Ces actions privilégiées incluent les modifications d'élévation (par exemple la création de rôle ou les réinitialisations de mot de passe), la modification des configurations de stratégie (par exemple les stratégies de mot de passe) ou bien les modifications apportées à la configuration de répertoire (par exemple les modifications apportées aux paramètres de fédération de domaine). Les rapports fournissent l’enregistrement d’audit pour le nom d’événement, l’acteur qui a effectué l’action, la ressource cible affectée par la modification, ainsi que la date et l’heure (UTC). Les clients sont en mesure de récupérer la liste des événements d’audit pour leur annuaire Azure Active Directory via le [portail Azure](https://portal.azure.com/), comme décrit dans [Afficher vos journaux d’audit](active-directory-reporting-azure-portal.md).


## Liste d'événements de rapport d'audit
<!--- audit event descriptions should be in the past tense --->

Événements | Description de l'événement
------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------
**Événements liés aux utilisateurs** |
Ajouter un utilisateur | Ajout d'un utilisateur au répertoire
Supprimer l'utilisateur | Suppression de l'utilisateur du répertoire
Définir les propriétés de licence | Définition des propriétés de la licence pour un utilisateur du répertoire
Réinitialiser le mot de passe de l'utilisateur | Réinitialisation du mot de passe pour un utilisateur du répertoire
Modifier le mot de passe de l'utilisateur | Modification du mot de passe pour un utilisateur du répertoire
Modifier la licence de l'utilisateur | Modification de la licence attribuée à un utilisateur du répertoire Pour savoir quelles licences ont été mises à jour, examinez les propriétés [Mettre à jour l’utilisateur](#update-user-attributes) ci-dessous.
Mettre à jour l'utilisateur | Mise à jour d'un utilisateur dans le répertoire [Voir ci-dessous](#update-user-attributes) pour les attributs qui peuvent être mis à jour.
Définir le mot de passe utilisateur | Définition de la propriété qui force un utilisateur à modifier son mot de passe lors de la connexion
Mettre à jour les informations d’identification de l’utilisateur | Modification du mot de passe par l’utilisateur.  
**Événements liés aux groupes** |
Ajouter un groupe | Création d’un groupe dans l’annuaire.
Mettre à jour un groupe | Mise à jour d’un groupe dans l’annuaire. Pour savoir quelles propriétés de groupe ont été mises à jour, reportez-vous à [Propriétés de groupe auditées](#update-group-attributes) dans la section ci-dessous.
Supprimer un groupe | Suppression d’un groupe de l’annuaire.
Ajouter un membre à un groupe | Ajout d’un membre à un groupe dans l’annuaire.
Supprimer un membre d’un groupe | Suppression d’un membre d’un groupe dans l’annuaire.
CreateGroupSettings | Création de paramètres de groupe.
UpdateGroupSettings | Mise à jour de paramètres de groupe. Pour savoir quels paramètres de groupe ont été mis à jour, reportez-vous à [Propriétés de groupe auditées](#update-group-attributes) dans la section ci-dessous.
DeleteGroupSettings | Suppression de paramètres de groupe.
SetGroupLicense | Définition d’une licence de groupe.
SetGroupManagedBy | Définition d’un groupe à gérer par un utilisateur.
AddGroupMember | Ajout d’un membre à un groupe.
RemoveGroupMember | Supprimer un membre d’un groupe
AddGroupOwner | Ajout d’un propriétaire à un groupe.
RemoveGroupOwner | Suppression d’un propriétaire de groupe.
**Événements liés aux applications** |
Ajouter un principal du service | Ajout d'un principal du service dans le répertoire
Supprimer le principal du service | Suppression d'un principal du service du répertoire
Ajouter les informations d'identification du principal du service | Ajout des informations d'identification à un principal du service
Supprimer les informations d'identification du principal du service | Suppression des informations d'identification d'un principal du service
Ajouter l'entrée de délégation | Création d’un [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permissiongrant-entity) dans l’annuaire.
Définir l'entrée de délégation | Mise à jour d’un [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permissiongrant-entity) dans l’annuaire.
Supprimer l'entrée de délégation | Suppression d’un [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permissiongrant-entity) dans l’annuaire.
**Événements liés aux rôles** |
Ajouter un membre au rôle | Ajout d'un utilisateur à un rôle de répertoire
Supprimer un membre du rôle | Suppression d'un utilisateur d'un rôle de répertoire
Définir les informations de contact d'entreprise | Définition des préférences de contact au niveau de l'entreprise Cela inclut les adresses de messagerie pour le marketing, ainsi que les notifications techniques à propos de Microsoft Online Services.
Ajouter l'entrée de délégation | Création d’un [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#OAuth2PermissionGrantEntity) dans l’annuaire.
Définir l'entrée de délégation | Mise à jour d’un [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#OAuth2PermissionGrantEntity) dans l’annuaire.
Supprimer l'entrée de délégation | Suppression d’un [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#OAuth2PermissionGrantEntity) dans l’annuaire.
AddSevicePrincipalOwner | Ajout d’un propriétaire à un principal du service.
RemoveSevicePrincipalOwner | Suppression d’un propriétaire de principal du service.
AddApplication | Ajout d’une application.
UpdateApplication | Mise à jour d’une application. Pour savoir quels paramètres d’application ont été mis à jour, reportez-vous à [Propriétés de groupe auditées](#update-application-attributes) dans la section ci-dessous.
DeleteApplication | Suppression d’une application.
RestoreApplication|Restauration d’une application.
AddApplicationOwner | 	Ajout d’un propriétaire à une application.
RemoveApplicationOwner | 	Suppression d’un propriétaire d’application.
**Événements liés aux rôles** |
Ajouter un membre au rôle | Ajout d'un utilisateur à un rôle de répertoire
Supprimer un membre du rôle | Suppression d'un utilisateur d'un rôle de répertoire
AddRoleDefinition | Ajout d’une définition de rôle.
UpdateRoleDefinition | Mise à jour d’une définition de rôle. Pour savoir quels paramètres de rôle ont été mis à jour, reportez-vous à [Propriétés de définition de rôle auditées](#update-role-definition-attributes) dans la section ci-dessous.
DeleteRoleDefinition | Suppression d’une définition de rôle.
AddRoleAssignmentToRoleDefinition | Ajout d’une affectation de rôle à une définition de rôle.
RemoveRoleAssignmentFromRoleDefinition | Suppression d’une affectation de rôle d’une définition de rôle.
AddRoleFromTemplate | Ajout d’un rôle à partir d’un modèle.
UpdateRole | Mise à jour d’un rôle.
AddRoleScopeMemberToRole | Ajout d’un membre inclus dans une étendue à un rôle.
RemoveRoleScopedMemberFromRole | Suppression d’un membre inclus dans une étendue d’un rôle.
**Événements d’appareil (tous les nouveaux événements)** |
AddDevice | Ajout d’un appareil.
UpdateDevice | Mise à jour d’un appareil. Pour savoir quelles propriétés d’appareil ont été mises à jour, reportez-vous à [Propriétés d’appareil auditées](#update-device-attributes) dans la section ci-dessous.
DeleteDevice | Suppression d’un appareil.
AddDeviceConfiguration | Ajout d’une configuration d’appareil.
UpdateDeviceConfiguration | Mise à jour d’une configuration d’appareil. Pour savoir quelles propriétés de configuration ont été mises à jour, reportez-vous à [Propriétés de configuration auditées](#update-device-configuration-attributes) dans la section ci-dessous.
DeleteDeviceConfiguration | Suppression d’une configuration d’appareil.
AddRegisteredOwner | Ajout d’un propriétaire enregistré à un appareil.
AddRegisteredUsers | Ajout d’utilisateurs enregistrés à un appareil.
RemoveRegisteredOwner | Suppression du propriétaire enregistré d’un appareil.
RemoveRegisteredUsers | Suppression d’utilisateurs enregistrés d’un appareil.
RemoveDeviceCredentials | Suppression des informations d’identification d’un appareil.
**Événements B2B** |
Lot d’invitations chargé | Un administrateur a chargé un fichier contenant des invitations à envoyer aux utilisateurs partenaires.
Lot d’invitations traité. | Un fichier contenant des invitations à envoyer aux utilisateurs partenaires a été traité.
Invitation d’un utilisateur externe. | Un utilisateur externe a été invité au répertoire.
Utilisation de l’invitation d’un utilisateur externe. | Un utilisateur externe a utilisé son invitation au répertoire.
Ajout d’un utilisateur externe à un groupe. | Un utilisateur externe a été affecté à un groupe dans le répertoire.
Affectation d’un utilisateur externe à une application. | Un utilisateur externe a reçu un accès direct à une application.
Création d’un locataire viral. | Un nouveau locataire a été créé par l’utilisation d’une invitation dans Azure AD.
Création d’un utilisateur viral. | Un utilisateur a été créé par l’utilisation d’une invitation dans un locataire existant dans Azure AD.
**Unités administratives (tous les nouveaux événements)** |
AddAdministrativeUnit | Ajout d’une unité administrative.
UpdateAdministrativeUnit| 	Mise à jour d’une unité administrative. Pour savoir quelles propriétés d’unité administrative ont été mises à jour, reportez-vous à [Propriétés d’unité administrative auditées](#update-administrative-unit-attributes) dans la section ci-dessous.
DeleteAdministrativeUnit| 	Suppression d’une unité administrative.
AddMemberToAdministrativeUnit| 	Ajout d’un membre à une unité administrative.
RemoveMemberFromAdministrativeUnit| 	Suppression d’un membre d’une unité administrative.
**Événements liés aux répertoires** |
Ajouter un partenaire à l'entreprise | Ajout d'un partenaire au répertoire
Supprimer le partenaire de l'entreprise | Suppression d'un partenaire du répertoire
DemotePartner | Rétrogradation d’un partenaire.
Ajouter le domaine à l'entreprise | Ajout d'un domaine au répertoire
Supprimer le domaine de l'entreprise | Suppression d'un domaine du répertoire
Mettre à jour le domaine | Mise à jour d'un domaine dans le répertoire Pour savoir quelles propriétés de domaine ont été mises à jour, reportez-vous à [Propriétés de domaine auditées](#update-domain-attributes) dans la section ci-dessous.
Définir l'authentification de domaine | Modification du paramètre de domaine par défaut de l'entreprise.
Définir les informations de contact d'entreprise | Définition des préférences de contact au niveau de l'entreprise Cela inclut les adresses de messagerie pour le marketing, ainsi que les notifications techniques à propos de Microsoft Online Services.
Définir les paramètres de fédération du domaine | Mise à jour des paramètres de fédération d'un domaine
Vérifier le domaine | Vérification d'un domaine dans le répertoire
Vérifier le domaine vérifié par courrier électronique | Vérification d'un domaine dans le répertoire à l'aide de la vérification par courrier électronique.
Définir l'indicateur DirSyncEnabled de l'entreprise | Définition de la propriété qui active un répertoire pour Azure AD Sync
Définir la stratégie de mot de passe | Définition des contraintes de longueur et de caractères des mots de passe utilisateur
Définir les informations de l'entreprise | Mise à jour des informations au niveau de l'entreprise Consultez l'applet de commande Powershell [Get-msolcompanyinformation permet](https://msdn.microsoft.com/library/azure/dn194126.aspx) pour obtenir plus de détails.
SetCompanyAllowedDataLocation | 	Définition d’un emplacement de données autorisé pour l’entreprise.
SetCompanyDirSyncEnabled | Définition de l’indicateur DirSyncEnabled de l’entreprise.
SetCompanyDirSyncFeature | 	Définition de la fonctionnalité DirSync.
SetCompanyInformation | Définition des informations de l’entreprise.
SetCompanyMultiNationalEnabled | 	Définition de l’activation de la fonctionnalité multinationale.
SetDirectoryFeatureOnTenant | 	Définition de la fonctionnalité d’annuaire sur un client.
SetTenantLicenseProperties | Définition des propriétés d’une licence client.
CreateCompanySettings | Création de paramètres d’entreprise.
UpdateCompanySettings | Mise à jour de paramètres d’entreprise. Pour savoir quels paramètres d’entreprise ont été mis à jour, reportez-vous à [Propriétés d’entreprise auditées](#update-company-attributes) dans la section ci-dessous.
DeleteCompanySettings | Suppression de paramètres d’entreprise.
SetAccidentalDeletionThreshold | 	Définition d’un seuil de suppression accidentelle
SetRightsManagementProperties | Définition de propriétés Rights Management.
PurgeRightsManagementProperties | Vidage de propriétés Rights Management.
UpdateExternalSecrets | Mise à jour de clés secrètes externes.
**Événements de stratégie (tous les nouveaux événements)** |
AddPolicy | 	Ajout d’une stratégie.
UpdatePolicy |	Mise à jour d’une stratégie.
DeletePolicy |	Suppression d’une stratégie.
AddDefaultPolicyApplication |	Ajout d’une stratégie à une application.
AddDefaultPolicyServicePrincipal |	Ajout d’une stratégie à un principal du service.
RemoveDefaultPolicyApplication |	Suppression d’une stratégie d’application.
RemoveDefaultPolicyServicePrincipal |	Suppression d’une stratégie de principal du service.
RemovePolicyCredentials |	Suppression des informations d’identification d’une stratégie.

## Rétention des rapports d’audit
Les événements du rapport d’audit d’Azure AD sont conservés pendant 180 jours. Pour plus d’informations sur la rétention des rapports, consultez la page [Stratégies de rétention des rapports Azure Active Directory](active-directory-reporting-retention.md).

Pour les clients intéressés par le stockage de leurs événements d’audit pour des périodes de rétention plus longues, l’API de création de rapports peut être utilisée pour extraire régulièrement des événements d’audit dans un magasin de données distinct. Consultez la rubrique [Prise en main de l’API de création de rapports](active-directory-reporting-api-getting-started.md) pour en savoir plus.

## Propriétés incluses dans chaque événement d'audit

Propriété | Description
------------- | --------------------------------------------------------------
Date et heure | Date et heure auxquelles l’audit s’est produit.
Acteur | L'utilisateur ou le principal du service qui a effectué l'action
Action | L'action qui a été effectuée
Cible | L'utilisateur ou le principal du service pour qui l'action a été effectuée


## Attributs de « Mettre à jour l’utilisateur »
L'événement d'audit « Mettre à jour l'utilisateur » inclut des informations supplémentaires sur les attributs utilisateur qui ont été mis à jour. Pour chaque attribut, la valeur précédente et la nouvelle valeur sont toutes les deux incluses.

Attribut | Description
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
AccountEnabled | L’utilisateur peut se connecter.
AssignedLicense | Toutes les licences attribuées à l'utilisateur
AssignedPlan | Plans de service résultant des licences attribuées à l'utilisateur
LicenseAssignmentDetail | Plus d'informations sur les licences attribuées à l'utilisateur Par exemple, si la licence basée sur le groupe a été impliquée, cela inclut le groupe qui a accordé ladite licence.
Mobile | Téléphone mobile de l'utilisateur
OtherMail | Adresse de messagerie alternative de l'utilisateur
OtherMobile | Téléphone mobile alternatif de l'utilisateur
StrongAuthenticationMethod | Liste des méthodes de vérification configurées par l'utilisateur pour l'authentification multi-facteur, comme l'appel vocal, le SMS ou la vérification du code à partir d'une application mobile.
StrongAuthenticationRequirement | Dans le cas où l'authentification multi-facteur est appliquée, activée ou désactivée pour cet utilisateur
StrongAuthenticationUserDetails | Numéro de téléphone de l'utilisateur, numéro de téléphone alternatif et adresse de messagerie utilisés pour la vérification de l'authentification multi-facteur et de la réinitialisation du mot de passe.
StrongAuthenticationPhoneAppDetail | Détail des applications de téléphone inscrites pour effectuer l’authentification à deux facteurs (2FA).
TelephoneNumber | Numéro de téléphone de l'utilisateur
AlternativeSecurityId | ID de sécurité alternatif pour l’objet.
CreationType |Méthode de création de l’utilisateur (par invitation ou virale).
InviteTicket |Liste de tickets d’invitation pour l’utilisateur.
InviteReplyUrl |Liste d’URL de réponse pour l’acceptation d’invitations.
InviteResources |Liste de ressources auxquelles l’utilisateur a été invité.
LastDirSyncTime |Dernière mise à jour de l’objet suite à une synchronisation à partir de l’annuaire faisant autorité (client, local).
MSExchRemoteRecipientType |Mappage vers les types de destinataires MSO. Pour plus d’informations sur les types de destinataires, reportez-vous à [l’article sur la propriété Recipient.Type] https://msdn.microsoft.com/library/microsoft.office.interop.outlook.recipient.type.aspx.
PreferredDataLocation |Emplacement préféré pour les données d’utilisateur, de groupe, de contact, de dossier public ou d’appareil.
ProxyAddresses |Adresse permettant à un objet de destinataire Exchange Server d’être reconnu dans un système de messagerie étranger.
StsRefreshTokensValidFrom |Transmet les informations de révocation des jetons d’actualisation : tous les jetons d’actualisation STS émis avant ce moment sont considérés comme expirés.
UserPrincipalName |UPN, qui est un nom de connexion de type Internet pour un utilisateur.
UserState |État de l’utilisateur : PendingApproval/PendingAcceptance/Accepted/PendingVerification (Approbation en cours/Acceptation en cours/Accepté/Vérification en attente).
UserStateChangedOn |Horodatage de la dernière modification de l’attribut UserState. Permet de déclencher des workflows de cycle de vie.
UserType |Type d’utilisateur. Membre (0), invité (1), viral (2).

## Attributs de mise à jour d’un groupe
Attribut | Description
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
Classification | Classification d’un groupe unifié (HBI, MBI, etc.).
Description | Phrases descriptives explicites concernant l’objet.  
DisplayName |Nom d’affichage d’un objet.
DirSyncEnabled |Indique si la synchronisation se produit à partir d’un annuaire faisant autorité (client, local).
GroupLicenseAssignment |Attribution de licence d’un groupe.
GroupType |Type de groupe. Unifié (0).
IsMembershipRuleLocked |Indique que la propriété MembershipRule est définie par le service de gestion des groupes en libre-service et ne peut pas être modifiée par les utilisateurs. S’applique uniquement aux groupes dont l’attribut GroupType inclut la propriété GroupType.DynamicMembership.
IsPublic |Indicateur spécifiant si le groupe est public/privé.
LastDirSyncTime |Dernière mise à jour de l’objet suite à une synchronisation à partir de l’annuaire faisant autorité (client, local).
Mail |Adresse de messagerie principale
MailEnabled |Indique si ce groupe est doté d’une fonctionnalité de messagerie.
MailNickname |Moniker d’un objet de carnet d’adresses, généralement la partie de l’adresse électronique précédant le symbole « @ ».   
MembershipRule |Chaîne spécifiant les critères utilisés par le service de gestion des groupes en libre-service pour déterminer quels membres doivent appartenir à ce groupe. Voir également IsMembershipRuleLocked. S’applique uniquement aux groupes dont l’attribut GroupType inclut la propriété GroupType.DynamicMembership.
MembershipRuleProcessingState| Valeur enum définie par le service de gestion des groupes en libre-service spécifiant l’état du traitement de l’appartenance pour ce groupe. S’applique uniquement aux groupes dont l’attribut GroupType inclut la propriété GroupType.DynamicMembership.
ProxyAddresses |Adresse permettant à un objet de destinataire Exchange Server d’être reconnu dans un système de messagerie étranger.
RenewedDateTime |Horodatage du renouvellement le plus récent d’un groupe.   
SecurityEnabled |Indique si l’appartenance au groupe peut influencer les décisions d’autorisation.
WellKnownObject |Qualifie un objet d’annuaire selon un ensemble prédéfini d’objets.

## Attributs de mise à jour d’un appareil
Attribut | Description
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
AccountEnabled | Indique si un principal de sécurité peut s’authentifier.
CloudAccountEnabled | Indique si un principal de sécurité peut s’authentifier. Écrit par Intune lorsque l’appareil est masterisé en local.
CloudDeviceOSType | Type de l’appareil basé sur le système d’exploitation (par exemple, Windows RT ou iOS). Lorsqu’il est défini par un service cloud (tel qu’Intune), cet attribut fait autorité dans l’annuaire pour l’attribut DeviceOSType.
CloudDeviceOSVersion | Version du système d’exploitation. Lorsqu’il est défini par un service cloud (tel qu’Intune), cet attribut fait autorité dans l’annuaire pour l’attribut DeviceOSVersion.
CloudDisplayName | Valeur de l’attribut LDAP displayName. Lorsqu’il est défini par un service cloud (tel qu’Intune), cet attribut fait autorité dans l’annuaire pour l’attribut displayName.
CloudCreated |Indique si l’objet a été créé par des services cloud.
CompliantUntil | Indique jusqu’à quand l’appareil est jugé conforme.
DeviceMetadata |Métadonnées personnalisées de l’appareil.
DeviceObjectVersion | Cet attribut est utilisé pour identifier la version de schéma de l’appareil.
DeviceOSType |Type de l’appareil basé sur le système d’exploitation (par exemple, Windows RT ou iOS). Écrit par le service d’inscription et destiné à être mis à jour par le service de gestion des données de référence ou le service de gestion allégé STS.
DeviceOSVersion |Version du système d’exploitation. Écrit par le service d’inscription et destiné à être mis à jour par le service de gestion des données de référence ou le service de gestion allégé STS.
DevicePhysicalIds |Attribut à valeurs multiples destiné à stocker les identificateurs de l’appareil physique. Ceux-ci peuvent inclure des ID de BIOS, des empreintes de TPM, des ID matériels spécifiques, etc.
DirSyncEnabled | Indique si la synchronisation se produit à partir d’un annuaire faisant autorité (client, local).
DisplayName |Nom d’affichage d’un objet.
IsCompliant | Cet attribut est utilisé pour gérer l’état de l’appareil lié à la gestion des périphériques mobiles.
IsManaged |Cet attribut est utilisé pour indiquer que l’appareil est géré par un système MDM cloud.
LastDirSyncTime |Dernière mise à jour de l’objet suite à une synchronisation à partir de l’annuaire faisant autorité (client, local).

## Attributs de mise à jour d’une configuration d’appareil
Attribut | Description
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
MaximumRegistrationInactivityPeriod |Nombre maximal de jours pendant lequel un appareil peut être inactif avant que son retrait ne soit envisagé.
RegistrationQuota |Stratégie utilisée pour limiter le nombre d’inscriptions d’appareil autorisées pour un même utilisateur.

## Attributs de mise à jour d’une configuration de principal du service
Attribut | Description
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
AccountEnabled |Indique si un principal de sécurité peut s’authentifier.
AppPrincipalId | Identité externe, définie par une application, pour un principal de sécurité.
DisplayName |Nom d’affichage d’un objet.
ServicePrincipalName | Nom de principal du service qui inclut un nom spécifiant une valeur de classe d’application et une autorité contenant au moins nomhôte[:port] ou un nom spécifiant un identificateur pour le principal du service.

## Attributs de mise à jour d’une application
Attribut | Description
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
AppAddress |Ensemble d’adresses (URL de redirection) qui sont affectées à un principal du service.
AppId |ID de l’application.   
AppIdentifierUri | URI de l’application, qui identifie l’application. Il s’agit généralement de l’URL d’accès à l’application.
AppLogoUrl |URL de l’image du logo de l’application stockée dans un CDN.
AvailableToOtherTenants | Si cet attribut est défini sur true, l’application est une application mutualisée (c’est-à-dire qu’elle peut être utilisée par d’autres clients).
DisplayName | Nom d’affichage d’une application.
Entitlement |Liste des droits de l’application.
ExternalUserAccountDelegationsAllowed |Indicateur spécifiant si application de ressources est approuvée et peut créer des entrées de délégation pour des comptes d’utilisateur externes.
GroupMembershipClaims |Stratégie liée aux revendications d’appartenance à un groupe.
PublicClient | Défini sur true si le client ne protège pas les données (c’est-à-dire un client non confidentiel dans OAuth2.0)
RecordConsentConditions | Types de conditions de consentement, telles que définies par les termes du contrat : Aucune (0), SilentConsentForPartnerManagedApp (1). Cette valeur est exposée dans le schéma de l’API Graph et peut seulement être définie/modifiée jeu par les administrateurs de client.
RequiredResourceAccess |Contenu XML d’une valeur de la propriété RequiredResourceAccess.
WebApp |S’il est défini sur true, cet attribut indique que cette application est une application web.
WwwHomepage |Page web principale.

## Attributs de mise à jour d’un rôle
Attribut | Description
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
AppAddress |Ensemble d’adresses (URL de redirection) qui sont affectées à un principal du service.
BelongsToFirstLoginObjectSet |S’il est défini sur true, cet attribut indique que cet objet appartient au jeu d’objets requis pour activer la connexion du premier administrateur d’un nouveau client.
Builtin |Indique si la durée de vie d’un objet est régie par le système.
Description | Phrases descriptives explicites concernant l’objet.  
DisplayName |Nom d’affichage d’un objet.
MailNickname | Moniker d’un objet de carnet d’adresses, généralement la partie de l’adresse électronique précédant le symbole « @ ».
RoleDisabled |Indique si le rôle doit être ignoré dans le cadre des contrôles d’accès.
RoleTemplateId | Identité du modèle de rôle.
ServiceInfo |Informations d’approvisionnement spécifiques du service qui peuvent être consommées par MOAC ou d’autres instances de service (de type identique ou différent).
TaskSetScopeReference |Identifie un ensemble de tâches et un ensemble d’étendues associées à un rôle ou un modèle de rôle.
ValidationError |Informations publiées par un service fédéré décrivant une erreur non temporaire et propre au service concernant les propriétés ou le lien d’une action d’administrateur d’objet à résoudre.
WellKnownObject |Qualifie un objet d’annuaire selon un ensemble prédéfini d’objets.

## Attributs de mise à jour d’une définition de rôle
Attribut | Description
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
AssignableScopes |Ensemble d’étendues d’autorisation qui peuvent être référencées lors de l’affectation de cette définition de rôle à un principal de sécurité.
DisplayName |Nom d’affichage d’un objet.
GrantedPermissions |Autorisations accordées par cette définition de rôle.


## Attributs de mise à jour d’une unité administrative
Attribut | Description
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
Description |	Cette propriété est mise à jour lorsque vous modifiez la description d’une unité administrative.
DisplayName |	Cette propriété est mise à jour lorsque vous modifiez le nom d’une unité administrative.

## Attributs de mise à jour de paramètres d’entreprise
Attribut | Description
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
AllowedDataLocation | Emplacement dans lequel l’approvisionnement des utilisateurs de l’entreprise est autorisé.
AuthorizedServiceInstance | Noms des instances de service dans lesquelles un plan peut être déployé.
DirSyncEnabled |Indique si la synchronisation se produit à partir d’un annuaire faisant autorité (client, local).
DirSyncStatus |Indique si la synchronisation des objets de carnet d’adresses dans le contexte de ce locataire se produit à partir d’un annuaire faisant autorité (client, local) ; extension de la propriété DirSyncEnabled sur les objets d’entreprise.
DirSyncFeatures |Indicateur binaire permettant de suivre l’ensemble de fonctionnalités DirSync activées et désactivées pour le client.
DirectoryFeatures |Fonctionnalités d’annuaire activées ou désactivées.
DirSyncConfiguration |Contient toutes les configurations DirSync propres au client actuel.
DisplayName |Nom d’affichage d’un objet.
IsMnc |Indicateur booléen défini sur true si l’entreprise est activée pour la fonctionnalité d’entreprise multinationale.
ObjectSettings | Ensemble de paramètres applicables à l’étendue de l’objet.
PartnerCommerceUrl |URL du site de commerce du partenaire.
PartnerHelpUrl |URL du site d’aide du partenaire.
PartnerSupportEmail | URL de la messagerie électronique de support du partenaire.
PartnerSupportTelephone |URL du téléphone de support du partenaire.
PartnerSupportUrl |URL du site de support du partenaire.
StrongAuthenticationDetails |Détails relatifs à l’authentification forte.
StrongAuthenticationPolicy |Stratégie d’authentification forte de l’entreprise.
TechnicalNotificationMail |Adresse de messagerie pour la notification de problèmes techniques relatifs à une entreprise.
TelephoneNumber |Numéros de téléphone conformes à la recommandation E.123 de l’UIT.
TenantType |Type d’un client. Si cette valeur n’est pas spécifiée, le client est une entreprise. Sinon, les valeurs possibles sont : MicrosoftSupport (0), SyndicatePartner (1), BreadthPartner (2) BreadthPartnerDelegatedAdmin (3) ResellerPartnerDelegatedAdmin (4) ValueAddedResellerPartnerDelegatedAdmin (5).
VerifiedDomain |Ensemble de noms de domaine DNS liés à une entreprise.

## Attributs de mise à jour de domaine
Attribut | Description
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
Fonctionnalités |Indicateurs binaires décrivant les fonctionnalités du domaine, le cas échéant.
Default |Indique si le domaine est la valeur par défaut (par exemple, le suffixe par défaut UserPrincipalName lorsqu’un administrateur crée un utilisateur dans MOAC).
Initial |Indique si le domaine est le domaine initial de l’entreprise, tel qu’alloué par OCP. Le domaine initial est un sous-domaine unique d’un domaine Microsoft Online, par exemple e.g.contoso3.microsoftonline.com.
LiveType |Type de l’espace de noms Windows Live correspondant, le cas échéant.
Nom | Identificateur du point de terminaison.
PasswordNotificationWindowDays |Nombre de jours avant l’expiration un mot de passe notifié à l’utilisateur.
PasswordValidityPeriodDays | Nombre de jours pendant lequel un mot de passe est valide ; passé cette période, il doit être modifié.

Les enregistrements d'audit sont un contrôle requis pour de nombreuses réglementations de conformité. Nous conseillons aux clients utilisant le rapport d’audit Azure Active Directory dans le cadre de leurs réglementations de conformité d’envoyer une copie de cette rubrique d’aide avec la copie de leur rapport d’audit exporté afin d’expliquer les détails du rapport. Si vous souhaitez comprendre les réglementations de conformité qui sont actuellement respectées par Azure, dirigez-vous vers la [page de conformité](https://azure.microsoft.com/support/trust-center/compliance/) de Microsoft Azure Trust Center.

<!---HONumber=AcomDC_0921_2016-->