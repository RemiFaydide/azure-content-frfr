<properties 
    pageTitle="Didacticiel : Intégration d’Azure AD à Slack | Microsoft Azure" 
    description="Découvrez comment utiliser Slack avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore." 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="09/19/2016" 
    ms.author="jeedes" />

#Didacticiel : Intégration d’Azure AD avec Slack
  
L’objectif de ce didacticiel est de montrer comment intégrer Azure et Slack. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un abonnement Slack pour lequel l’authentification unique est activée
  
À l’issue de ce didacticiel, les utilisateurs d’Azure AD que vous avez affectés à Slack pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Slack (connexion initiée par le fournisseur du service) ou en s’aidant de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).
  
Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1.  Activation de l’intégration d’applications pour Slack
2.  Configuration de l'authentification unique
3.  Configuration de l'approvisionnement des utilisateurs
4.  Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-slack-tutorial/IC794980.png "Scénario")

##Activation de l’intégration d’applications pour Slack
  
Cette section décrit l’activation de l’intégration d’applications pour Slack.

###Pour activer l’intégration d’applications pour Slack, procédez comme suit :

1.  Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.

    ![Active Directory](./media/active-directory-saas-slack-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications](./media/active-directory-saas-slack-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Ajouter l’application](./media/active-directory-saas-slack-tutorial/IC749321.png "Ajouter l’application")

5.  Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-slack-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6.  Dans la **zone de recherche**, entrez **Slack**.

    ![Galerie d’applications](./media/active-directory-saas-slack-tutorial/IC794981.png "Galerie d’applications")

7.  Dans le volet des résultats, sélectionnez **Slack**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Scénario](./media/active-directory-saas-slack-tutorial/IC796925.png "Scénario")

##Configuration de l'authentification unique
  
Cette section explique comment permettre aux utilisateurs de s’authentifier sur Slack avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML. Dans le cadre de cette procédure, vous devez créer un fichier de certificat codé en base 64. Si cette procédure ne vous est pas familière, consultez [Conversion d’un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

###Pour configurer l’authentification unique, procédez comme suit :

1.  Dans la page d’intégration d’applications **Slack** du portail Azure Classic, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-slack-tutorial/IC794982.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs se connectent à Slack**, sélectionnez **Authentification unique avec Microsoft Azure AD**, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-slack-tutorial/IC794983.png "Configurer l’authentification unique")

3.  Dans la zone de texte **URL de connexion à Slack** de la page **Configurer l’URL de l’application**, tapez l’URL de votre client Slack (par exemple, « **https://azuread.slack.com*"), puis cliquez sur **Suivant**.

    ![Configurer l’URL de l’application](./media/active-directory-saas-slack-tutorial/IC794984.png "Configurer l’URL de l’application")

4.  Dans la page **Configurer l’authentification unique sur Slack**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat en local sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-slack-tutorial/IC794985.png "Configurer l’authentification unique")

5.  Dans une autre fenêtre de navigateur web, connectez-vous au site de votre entreprise Slack en tant qu’administrateur.

6.  Accédez à **Microsoft Azure AD > Team Settings**.

    ![Team Settings](./media/active-directory-saas-slack-tutorial/IC794986.png "Team Settings")

7.  Dans la section **Team Settings**, cliquez sur l’onglet **Authentication** puis sur **Change Settings**.

    ![Team Settings](./media/active-directory-saas-slack-tutorial/IC794987.png "Team Settings")

8.  Dans la boîte de dialogue **SAML Authentication Settings**, procédez comme suit :

    ![SAML Settings](./media/active-directory-saas-slack-tutorial/IC794988.png "SAML Settings")

    1.  Dans la page de boîte de dialogue **Configurer l’authentification unique sur Slack** du portail Azure Classic, copiez la valeur **URL SSO SAML** et collez-la dans la zone de texte **Point de terminaison SAML 2.0 (HTTP)**.
    2.  Dans la page de boîte de dialogue **Configurer l’authentification unique sur Slack** du portail Azure Classic, copiez la valeur **URL de l’émetteur**, puis collez-la dans la zone de texte **Émetteur du fournisseur d’identité**.
    3.  Créez un fichier **codé en base 64** à partir du certificat téléchargé.
    
        >[AZURE.TIP] Pour plus d’informations, consultez [Conversion d’un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

    4.  Ouvrez votre certificat codé en base 64 dans le Bloc-notes, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **Certificat public**.
    5.  Décochez la case **Autoriser les utilisateurs à modifier leur adresse de messagerie**.
    6.  Cochez la case **Autoriser les utilisateurs à choisir leur propre nom d’utilisateur**.
    7.  Dans **L’authentification de votre équipe doit être utilisée par**, sélectionnez **C’est facultatif**.
    8.  Cliquez sur **Enregistrer la configuration**.

9.  Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-slack-tutorial/IC794989.png "Configurer l’authentification unique")

##Configuration de l'approvisionnement des utilisateurs
  
Pour se connecter à Slack, les utilisateurs d’Azure AD doivent être approvisionnés dans Slack.
  
Vous n’avez rien à faire pour configurer l’approvisionnement des utilisateurs dans Slack. Lorsqu’un utilisateur tente de se connecter à Slack, un compte Slack est, au besoin, automatiquement créé.

##Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

###Pour affecter des utilisateurs à Slack, procédez comme suit :

1.  Dans le portail Azure Classic, créez un compte de test.

2.  Dans la page d’intégration d’applications **Slack**, cliquez sur **Affecter des utilisateurs**.

    ![Affecter des utilisateurs](./media/active-directory-saas-slack-tutorial/IC794990.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.

    ![Oui](./media/active-directory-saas-slack-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d’informations sur le volet d’accès, consultez [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0921_2016-->