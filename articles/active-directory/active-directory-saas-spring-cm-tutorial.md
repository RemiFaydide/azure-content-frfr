<properties 
    pageTitle="Didacticiel : Intégration d’Azure AD à SpringCM | Microsoft Azure" 
    description="Découvrez comment utiliser SpringCM avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore." 
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

#Didacticiel : Intégration d’Azure AD à SpringCM
  
L’objectif de ce didacticiel est de montrer comment configurer l’authentification unique entre Azure AD et SpringCM.
  
Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un abonnement SpringCM pour lequel l’authentification unique est activée
  
À l’issue de ce didacticiel, les utilisateurs Azure AD que vous avez affectés à SpringCM pourront s’authentifier de manière unique à l’aide du panneau d’accès AAD.

1.  Activation de l’intégration d’applications pour SpringCM
2.  Configuration de l’authentification unique
3.  Configuration de l'approvisionnement des utilisateurs
4.  Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-spring-cm-tutorial/IC797044.png "Scénario")

##Activation de l’intégration d’applications pour SpringCM
  
Cette section décrit l’activation de l’intégration d’applications pour SpringCM.

###Pour activer l’intégration d’applications pour SpringCM, procédez comme suit :

1.  Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.

    ![Active Directory](./media/active-directory-saas-spring-cm-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications](./media/active-directory-saas-spring-cm-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Ajouter l’application](./media/active-directory-saas-spring-cm-tutorial/IC749321.png "Ajouter l’application")

5.  Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-spring-cm-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6.  Dans la **zone de recherche**, entrez **SpringCM**.

    ![Galerie d’applications](./media/active-directory-saas-spring-cm-tutorial/IC797045.png "Galerie d’applications")

7.  Dans le volet des résultats, sélectionnez **SpringCM**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![SpringCM](./media/active-directory-saas-spring-cm-tutorial/IC797046.png "SpringCM")

##Configuration de l’authentification unique
  
Cette section explique comment permettre aux utilisateurs de s’authentifier sur SpringCM avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.

###Pour configurer l’authentification unique, procédez comme suit :

1.  Dans la page d’intégration d’applications **SpringCM** du portail Azure Classic, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-spring-cm-tutorial/IC797047.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs se connectent à SpringCM**, sélectionnez **Authentification unique avec Microsoft Azure AD**, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-spring-cm-tutorial/IC797048.png "Configurer l’authentification unique")

3.  Dans la zone de texte **URL de connexion de SpringCM** de la page **Configurer l’URL de l’application**, tapez l’URL utilisée par vos utilisateurs pour se connecter à votre application SpringCM, puis cliquez sur **Suivant**.

    L’URL de l’application correspond à celle de votre client SpringCM (par exemple, *https://na11.springcm.com/atlas/SSO/SSOEndpoint.ashx?aid=16826*) :

    ![Configurer l’URL de l’application](./media/active-directory-saas-spring-cm-tutorial/IC797049.png "Configurer l’URL de l’application")

4.  Dans la page **Configurer l’authentification unique sur SpringCM**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat en local sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-spring-cm-tutorial/IC797050.png "Configurer l’authentification unique")

5.  Dans une autre fenêtre de navigateur web, connectez-vous au site de votre entreprise **SpringCM** en tant qu’administrateur.

6.  Dans le menu en haut, cliquez sur **GO TO**, sur **Preferences** puis, dans la section **Account Preferences**, cliquez sur **SAML SSO**.

    ![SAML SSO](./media/active-directory-saas-spring-cm-tutorial/IC797051.png "SAML SSO")

7.  Dans la section Identity Provider Configuration, procédez comme suit :

    ![Identity Provider Configuration](./media/active-directory-saas-spring-cm-tutorial/IC797052.png "Identity Provider Configuration")

    1.  Pour charger votre certificat Azure AD téléchargé, cliquez sur **Select Issuer Certificate** ou **Change Issuer Certificate**.
    2.  Dans la page **Configurer l’authentification unique sur SpringCM** du portail Azure Classic, copiez la valeur **URL de l’émetteur**, puis collez-la dans la zone de texte **Issuer** (Émetteur).
    3.  Dans la page **Configurer l’authentification unique sur SpringCM** du portail Azure Classic, copiez la valeur **URL du service d’authentification unique** et collez-la dans la zone de texte **Point de terminaison initié du fournisseur de services**.
    4.  Dans **SAML activé**, sélectionnez **Activer**.
    5.  Cliquez sur **Save**.

8.  Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-spring-cm-tutorial/IC797053.png "Configure Single SignOn")

##Configuration de l'approvisionnement des utilisateurs
  
Pour se connecter à SpringCM, les utilisateurs d’Azure AD doivent être approvisionnés dans SpringCM. Dans le cas de SpringCM, l’approvisionnement est une tâche manuelle.

>[AZURE.NOTE] Pour plus d’informations, consultez [Créer et modifier un utilisateur SpringCM](http://knowledge.springcm.com/create-and-edit-a-springcm-user).

###Pour approvisionner un compte d’utilisateur dans SpringCM, procédez comme suit :

1.  Connectez-vous à votre site d’entreprise **SpringCM** en tant qu’administrateur.

2.  Cliquez sur **GOTO** puis sur**Address Book**.

    ![Create User](./media/active-directory-saas-spring-cm-tutorial/IC797054.png "Créer un utilisateur")

3.  Cliquez sur **Create User**.

4.  Sélectionnez un rôle d’utilisateur dans **User Role**.

5.  Sélectionnez**Send Activation Email**.

6.  Indiquez le prénom, le nom et l’adresse de messagerie du compte Azure AD valide que vous souhaitez approvisionner dans les zones de texte correspondantes.

7.  Ajoutez l’utilisateur à un groupe de sécurité dans **Security group**.

8.  Cliquez sur **Save**.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel outil ou API de création de compte d’utilisateur, fourni par SpringCM, pour approvisionner des comptes utilisateur AAD.

##Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

###Pour affecter des utilisateurs à SpringCM, procédez comme suit :

1.  Dans le portail Azure Classic, créez un compte de test.

2.  Dans la page d’intégration d’applications **SpringCM**, cliquez sur **Affecter des utilisateurs**.

    ![Affecter des utilisateurs](./media/active-directory-saas-spring-cm-tutorial/IC797055.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.

    ![Oui](./media/active-directory-saas-spring-cm-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d’informations sur le volet d’accès, consultez [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0921_2016-->