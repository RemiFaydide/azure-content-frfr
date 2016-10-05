<properties
	pageTitle="Serveur MFA avec Windows Server 2012 R2 AD FS | Microsoft Azure"
	description="Cet article explique la prise en main d’Azure Multi-Factor Authentication et d’AD FS dans Windows Server 2012 R2."
	services="multi-factor-authentication"
	documentationCenter=""
	authors="kgremban"
	manager="femila"
	editor="curtland"/>

<tags
	ms.service="multi-factor-authentication"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="09/22/2016"
	ms.author="kgremban"/>


# Sécuriser les ressources cloud et locales à l’aide du serveur Azure Multi-Factor Authentication avec AD FS dans Windows Server 2012 R2

Si votre organisation utilise Active Directory Federation Services (AD FS) et que vous souhaitez sécuriser vos ressources de cloud ou locales, vous pouvez déployer et configurer le serveur Azure Multi-Factor Authentication pour l’intégrer avec AD FS. Cette configuration active l’authentification multifacteur pour les points de terminaison de valeur élevée.

Cet article traite de l’utilisation du serveur Azure Multi-Factor Authentication avec AD FS dans Windows Server 2012 R2. Pour plus d’informations, consultez l’article qui explique comment [sécuriser les ressources de cloud et locales à l’aide du serveur Azure Multi-Factor Authentication avec AD FS 2.0](multi-factor-authentication-get-started-adfs-adfs2.md).

## Sécuriser Windows Server 2012 R2 AD FS avec le serveur Azure Multi-Factor Authentication

Lorsque vous installez le serveur Azure Multi-Factor Authentication, vous pouvez choisir l’une des options suivantes :

- Installer le serveur Azure Multi-Factor Authentication localement sur le même serveur qu’AD FS
- Installer l’adaptateur Azure Multi-Factor Authentication localement sur le serveur AD FS, puis installer le serveur Multi-Factor Authentication sur un autre ordinateur

Avant de commencer, tenez compte des informations suivantes :

- Vous n’êtes pas obligé d’installer le serveur Azure Multi-Factor Authentication sur votre serveur AD FS. Toutefois, vous devez installer l’adaptateur Multi-Factor Authentication pour AD FS sur un serveur Windows Server 2012 R2 exécutant AD FS. Vous pouvez installer le serveur sur un autre ordinateur, tant qu’il s’agit d’une version prise en charge, et installer l’adaptateur AD FS séparément sur votre serveur de fédération AD FS. Consultez les procédures suivantes pour savoir comment installer l’adaptateur séparément.
- Lors de la conception de l’adaptateur AD FS du serveur Multi-Factor Authentication, il était prévu qu’AD FS soit capable de transférer le nom de la partie de confiance à l’adaptateur pour pouvoir l’utiliser en tant que nom d’application. Mais cela n’a finalement pas été le cas. Si votre entreprise utilise la messagerie texte ou les méthodes d’authentification d’application mobile, les chaînes définies dans les paramètres de la société contiennent un espace réservé <$*application\_name*$>. Cet espace réservé n’est pas automatiquement remplacé lorsque vous utilisez l’adaptateur AD FS. Nous vous recommandons de supprimer l’espace réservé dans les chaînes appropriées lorsque vous sécurisez AD FS.

- Le compte que vous utilisez pour vous connecter doit disposer des droits d’utilisateur pour créer des groupes de sécurité dans votre service Active Directory.

- L’assistant d’installation de l’adaptateur d’authentification multifacteur AD FS crée un groupe de sécurité appelé PhoneFactor Admins dans votre instance d’Active Directory, puis ajoute le compte de service AD FS de votre service de fédération à ce groupe. Nous vous recommandons de vérifier sur votre contrôleur de domaine que le groupe PhoneFactor Admins est bien créé et que le compte de service AD FS est membre de ce groupe. Si nécessaire, ajoutez manuellement le compte de service AD FS au groupe PhoneFactor Admins sur votre contrôleur de domaine.
- Pour plus d’informations sur l’installation du Kit de développement logiciel (SDK) du service Web avec le portail de l’utilisateur, consultez l’article sur le [déploiement du portail de l’utilisateur pour le serveur Azure Multi-Factor Authentication.](multi-factor-authentication-get-started-portal.md)


### Installer le serveur Azure Multi-Factor Authentication localement sur le serveur AD FS

1. Téléchargez et installez le serveur Azure Multi-Factor Authentication sur votre serveur de fédération AD FS. Pour plus d’informations concernant l’installation, consultez l’article sur la [prise en main du serveur Azure Multi-Factor Authentication](multi-factor-authentication-get-started-server.md).
2. Dans la console de gestion du serveur Azure Multi-Factor Authentication, cliquez sur l’icône **AD FS**, puis sélectionnez les options **Autoriser l’inscription utilisateur** et **Autoriser les utilisateurs à sélectionner la méthode**.
3. Sélectionnez les autres options que vous souhaitez définir pour votre entreprise.
4. Cliquez sur **Installer adaptateur AD FS**.
<center>![Cloud](./media/multi-factor-authentication-get-started-adfs-w2k12/server.png)</center>
5. Si l’ordinateur est joint à un domaine et que la configuration d’Active Directory pour sécuriser la communication entre l’adaptateur AD FS et le service Azure Multi-Factor Authentication est incomplète, la fenêtre **Active Directory** s’affichera. Cliquez sur **Suivant** pour terminer automatiquement cette configuration ou cochez la case **Passer la configuration automatique d’Active Directory et configurer les paramètres manuellement**, puis cliquez sur **Suivant**.
6. Si l’ordinateur n’est pas joint à un domaine et que la configuration du groupe local pour sécuriser la communication entre l’adaptateur AD FS et le service Azure Multi-Factor Authentication est incomplète, la fenêtre **Groupe local** s’affichera. Cliquez sur **Suivant** pour terminer automatiquement cette configuration ou cochez la case **Passer la configuration automatique du groupe local et configurer les paramètres manuellement**, puis cliquez sur **Suivant**.
7. Dans l’assistant d’installation, cliquez sur **Suivant**. Le serveur Azure Multi-Factor Authentication crée le groupe PhoneFactor Admins et ajoute le compte de service AD FS au groupe PhoneFactor Admins.
<center>![Cloud](./media/multi-factor-authentication-get-started-adfs-w2k12/adapter.png)</center>
8. Sur la page **Launch Installer** (Lancer le programme d’installation), cliquez sur **Suivant**.
9. Dans le programme d’installation de l’adaptateur d’authentification multifacteur AD FS, cliquez sur **Suivant**.
10. Une fois l’installation terminée, cliquez sur **Fermer**.
11. Lorsque l’adaptateur a été installé, vous devez l’enregistrer dans AD FS. Ouvrez Windows PowerShell et exécutez la commande suivante :<br> `C:\Program Files\Multi-Factor Authentication Server\Register-MultiFactorAuthenticationAdfsAdapter.ps1`
   <center>![Cloud](./media/multi-factor-authentication-get-started-adfs-w2k12/pshell.png)</center>
12. Modifiez la stratégie d’authentification globale d’AD FS pour utiliser votre nouvel adaptateur enregistré. Dans la console de gestion AD FS, accédez au nœud **Stratégies d’authentification**. Dans la section **Authentification multifacteur**, cliquez sur le lien **Modifier** en regard de la section **Paramètres globaux**. Dans la fenêtre **Modifier la stratégie d’authentification globale**, sélectionnez **Authentification multifacteur** comme méthode d’authentification supplémentaire, puis cliquez sur **OK**. L'adaptateur est enregistré en tant que WindowsAzureMultiFactorAuthentication. Vous devez redémarrer le service AD FS pour que l’enregistrement soit pris en compte.

<center>![Cloud](./media/multi-factor-authentication-get-started-adfs-w2k12/global.png)</center>

À ce stade, le serveur d’authentification multifacteur est configuré comme fournisseur d’authentification supplémentaire à utiliser avec AD FS.

## Installer une version autonome de l’adaptateur AD FS à l’aide du SDK du service Web
1. Installez le SDK du service Web sur le serveur qui exécute le serveur Multi-Factor Authentication.
2. Copiez les fichiers suivants à partir du répertoire \\Program Files\\Multi-Factor Authentication Server sur le serveur sur lequel vous prévoyez d’installer l’adaptateur AD FS :
  - MultiFactorAuthenticationAdfsAdapterSetup64.msi
  - Register-MultiFactorAuthenticationAdfsAdapter.ps1
  - Unregister-MultiFactorAuthenticationAdfsAdapter.ps1
  - MultiFactorAuthenticationAdfsAdapter.config
3. Exécutez le fichier d’installation MultiFactorAuthenticationAdfsAdapterSetup64.msi.
4. Dans le programme d’installation de l’adaptateur d’authentification multifacteur AD FS, cliquez sur **Suivant** pour lancer l’installation.
5. Une fois l’installation terminée, cliquez sur **Fermer**.

## Modifier le fichier MultiFactorAuthenticationAdfsAdapter.config

Procédez comme suit pour modifier le fichier MultiFactorAuthenticationAdfsAdapter.config :

1. Définissez le nœud **UseWebServiceSdk** sur la valeur **true**.
2. Définissez le champ **WebServiceSdkUrl** sur l’URL du Kit de développement logiciel (SDK) du service web Multi-Factor Authentication. Par exemple : **https://contoso.com/&lt;certificatename&gt;/MultiFactorAuthWebServicesSdk/PfWsSdk.asmx** où certificatename correspond au nom de votre certificat.
3. Modifiez le script Register-MultiFactorAuthenticationAdfsAdapter.ps1 en ajoutant *-ConfigurationFilePath &lt;chemin&gt;* à la fin de la commande `Register-AdfsAuthenticationProvider`, où *&lt;chemin&gt;* correspond au chemin d’accès complet au fichier MultiFactorAuthenticationAdfsAdapter.config.

### Configurer le Kit de développement logiciel (SDK) du service web avec un nom d’utilisateur et un mot de passe

Deux options s’offrent à vous pour configurer le Kit de développement logiciel (SDK) du service web : Soit avec un nom d’utilisateur et un mot de passe, soit avec un certificat client. Procédez comme suit pour la première option ou ignorez cette section si vous choisissez la deuxième option.

1. Définissez le champ **WebServiceSdkUsername** sur un compte membre du groupe de sécurité PhoneFactor Admins. Utilisez le format &lt;domaine&gt;&#92;&lt;nom d’utilisateur&gt;.
2. Définissez le champ **WebServiceSdkPassword** sur le mot de passe du compte approprié.

### Configurer le Kit de développement logiciel (SDK) du service web avec un certificat client

Si vous ne souhaitez pas utiliser de nom d’utilisateur et de mot de passe, procédez comme suit pour configurer le Kit de développement logiciel (SDK) du service web avec un certificat client.

1. Obtenez un certificat client auprès d’une autorité de certification pour le serveur qui exécute le Kit de développement logiciel (SDK) du service web. Découvrez comment [obtenir des certificats clients](https://technet.microsoft.com/library/cc770328.aspx).
2. Importez le certificat client dans le magasin de certificats personnels de l’ordinateur local sur le serveur qui exécute le Kit de développement logiciel (SDK) du service web. Remarque : assurez-vous que le certificat public de l’autorité de certification se trouve dans le magasin des certificats racines approuvés.
3. Exportez les clés publiques et privées du certificat client vers un fichier .pfx.
4. Exportez la clé publique au format Base64 vers un fichier .cer.
5. Dans le Gestionnaire de serveur, vérifiez que la fonctionnalité Serveur Web (IIS)\\Serveur Web\\Sécurité\\Authentification par mappage de certificat client IIS est installée. Si elle n’est pas installée, sélectionnez **Ajouter des rôles et fonctionnalités** pour ajouter cette fonctionnalité.
6. Dans le Gestionnaire IIS, double-cliquez sur **Éditeur de configuration** dans le site web qui contient le répertoire virtuel du Kit de développement logiciel (SDK) du service web. Remarque : il est primordial d’effectuer cette opération au niveau du site web et non au niveau du répertoire virtuel.
7. Accédez à la section **system.webServer/security/authentication/iisClientCertificateMappingAuthentication**.
8. Définissez **enabled** sur **true**.
9. Définissez **oneToOneCertificateMappingsEnabled** sur **true**.
10. Cliquez sur le bouton **...** en regard de **oneToOneMappings**, puis cliquez sur le lien **Ajouter**.
11. Ouvrez le fichier .cer au format Base64 que vous avez exporté précédemment. Supprimez *-----BEGIN CERTIFICATE-----*, *-----END CERTIFICATE-----*, ainsi que tous les sauts de ligne. Copiez la chaîne résultante.
12. Définissez **certificate** sur la chaîne copiée à l’étape précédente.
13. Définissez **enabled** sur **true**.
14. Définissez **userName** sur un compte membre du groupe de sécurité PhoneFactor Admins. Utilisez le format &lt;domaine&gt;&#92;&lt;nom d’utilisateur&gt;.
15. Définissez le mot de passe sur le mot de passe du compte approprié, puis fermez l’Éditeur de configuration.
16. Cliquez sur le lien **Appliquer**.
17. Dans le répertoire virtuel du Kit de développement logiciel (SDK) du service web, double-cliquez sur **Authentification**.
18. Vérifiez que **Emprunt d’identité ASP.NET** et **Authentification de base** sont définis sur **Activé** et que tous les autres éléments sont définis sur **Désactivé**.
19. Dans le répertoire virtuel du Kit de développement logiciel (SDK) du service web, double-cliquez sur **Paramètres SSL**.
20. Définissez **Certificats clients** sur **Accepter**, puis cliquez sur **Appliquer**.
21. Copiez le fichier .pfx exporté précédemment sur le serveur qui exécute l’adaptateur AD FS.
22. Importez le fichier .pfx dans le magasin de certificats personnels de l’ordinateur local.
23. Cliquez avec le bouton droit et sélectionnez **Gérer les clés privées**, puis accordez l’accès en lecture au compte que vous avez utilisé pour vous connecter au service AD FS.
24. Ouvrez le certificat client et copiez l’empreinte à partir de l’onglet **Détails**.
25. Dans le fichier MultiFactorAuthenticationAdfsAdapter.config, définissez le champ **WebServiceSdkCertificateThumbprint** sur la chaîne copiée à l’étape précédente.


Enfin, pour enregistrer l’adaptateur, exécutez le script \\Program Files\\Multi-Factor Authentication Server\\Register-MultiFactorAuthenticationAdfsAdapter.ps1 dans PowerShell. L'adaptateur est enregistré en tant que WindowsAzureMultiFactorAuthentication. Vous devez redémarrer le service AD FS pour que l’enregistrement soit pris en compte.

## Rubriques connexes

Pour la résolution des problèmes, consultez le [Forum Aux Questions d’Azure Multi-Factor Authentication](multi-factor-authentication-faq.md)

<!---HONumber=AcomDC_0928_2016-->