<properties 
    pageTitle="Didacticiel : Intégration d’Azure AD à Sprinklr | Microsoft Azure" 
    description="Découvrez comment utiliser Sprinklr avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore !" 
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

#Didacticiel : Intégration d’Azure AD à Sprinklr
  
L’objectif de ce didacticiel est de montrer comment intégrer Azure et Sprinklr.  
Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un locataire Sprinklr
  
À l’issue de ce didacticiel, les utilisateurs d’Azure AD que vous avez affectés à Sprinklr pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Sprinklr (connexion initiée par le fournisseur du service) ou à l’aide de la [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1.  Activation de l’intégration d’applications pour Sprinklr
2.  Configuration de l'authentification unique
3.  Configuration de l'approvisionnement des utilisateurs
4.  Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-sprinklr-tutorial/IC782900.png "Scénario")

##Activation de l’intégration d’applications pour Sprinklr
  
Cette section décrit l’activation de l’intégration d’applications pour Sprinklr.

###Pour activer l’intégration d’applications pour Sprinklr, procédez comme suit :

1.  Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.

    ![Active Directory](./media/active-directory-saas-sprinklr-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications](./media/active-directory-saas-sprinklr-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Ajouter l’application](./media/active-directory-saas-sprinklr-tutorial/IC749321.png "Ajouter l’application")

5.  Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-sprinklr-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6.  Dans la **zone de recherche**, entrez **Sprinklr**.

    ![Galerie d’applications](./media/active-directory-saas-sprinklr-tutorial/IC782901.png "Galerie d’applications")

7.  Dans le volet des résultats, sélectionnez **Sprinklr**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Sprinklr](./media/active-directory-saas-sprinklr-tutorial/IC782902.png "Sprinklr")

##Configuration de l'authentification unique
  
Cette section explique comment permettre aux utilisateurs de s’authentifier sur Sprinklr avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous devez créer un fichier de certificat codé en base 64.  
Si cette procédure ne vous est pas familière, consultez [Conversion d’un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

###Pour configurer l’authentification unique, procédez comme suit :

1.  Dans la page d’intégration d’applications **Sprinklr** du portail Azure Classic, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-sprinklr-tutorial/IC782903.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs se connectent à Sprinklr**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-sprinklr-tutorial/IC782904.png "Configurer l’authentification unique")

3.  Dans la zone de texte **URL de connexion à Sprinklr** de la page **Configuration l’URL de l’application**, tapez votre URL au format « *https://\<nom\_locataire>.Sprinklr.com* », puis cliquez sur **Suivant**.

    ![Configurer l’URL de l’application](./media/active-directory-saas-sprinklr-tutorial/IC782905.png "Configurer l’URL de l’application")

4.  Dans la page **Configurer l’authentification unique sur Sprinklr**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-sprinklr-tutorial/IC782906.png "Configurer l’authentification unique")

5.  Dans une autre fenêtre de navigateur web, connectez-vous au site de votre entreprise Sprinklr en tant qu’administrateur.

6.  Accédez à **Administration > Settings**.

    ![Administration](./media/active-directory-saas-sprinklr-tutorial/IC782907.png "Administration")

7.  Accédez à **Manager Partner > Single Sign** dans le volet gauche.

    ![Manage Partner](./media/active-directory-saas-sprinklr-tutorial/IC782908.png "Manage Partner")

8.  Cliquez sur **+Add Single Sign Ons**.

    ![Single Sign-Ons](./media/active-directory-saas-sprinklr-tutorial/IC782909.png "Single Sign-Ons")

9.  Dans la page **Single Sign on**, procédez comme suit :

    ![Single Sign-Ons](./media/active-directory-saas-sprinklr-tutorial/IC782910.png "Single Sign-Ons")

    1.  Dans la zone de texte **Name**, indiquez le nom de votre configuration (par exemple, *WAADSSOTest*).
    2.  Sélectionnez **Enabled**.
    3.  Sélectionnez **Use new SSO Certificate**.
    4.  Créez un fichier **codé en base 64** à partir du certificat téléchargé.

        >[AZURE.TIP] Pour plus d’informations, consultez [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

    5.  Ouvrez votre certificat codé en base 64 dans le Bloc-notes, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **Identity Provider Certificate**.
    6.  Dans le portail Azure Classic, dans la page de boîte de dialogue **Configurer l’authentification unique sur Sprinklr**, copiez la valeur **ID du fournisseur d’identité** et collez-la dans la zone de texte **ID d’entité**.
    7.  Dans le portail Azure Classic, dans la page de boîte de dialogue **Configurer l’authentification unique sur Sprinklr**, copiez la valeur **URL de connexion distante** et collez-la dans la zone de texte **Identity Provider Login URL** (URL de connexion du fournisseur d’identité).
    8.  Dans le portail Azure Classic, dans la page de boîte de dialogue **Configurer l’authentification unique sur Sprinklr**, copiez la valeur **URL de déconnexion distante** et collez-la dans la zone de texte **Identity Provider Logout URL** (URL de déconnexion du fournisseur d’identité).
    9.  Dans **SAML User ID Type**, sélectionnez **Assertion contains User’s sprinklr.com username**.
    10. Dans **SAML User ID Location**, sélectionnez **User ID is in the Name Identifier element of the Subject statement**.
    11. Cliquez sur **Save**.

        ![SAML](./media/active-directory-saas-sprinklr-tutorial/IC782911.png "SAML")

10. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-sprinklr-tutorial/IC782912.png "Configurer l’authentification unique")

##Configuration de l'approvisionnement des utilisateurs
  
Pour que les utilisateurs AAD puissent se connecter, leur accès doit être approvisionné dans l’application Sprinklr.  
Cette section décrit comment créer des comptes d’utilisateur AAD dans Sprinklr.

###Pour approvisionner un compte d’utilisateur dans SpringCM, procédez comme suit :

1.  Connectez-vous à votre site d’entreprise Sprinklr en tant qu’administrateur.

2.  Accédez à **Administration > Settings**.

    ![Administration](./media/active-directory-saas-sprinklr-tutorial/IC782907.png "Administration")

3.  Accédez à **Manage Client > Users** dans le volet gauche.

    ![Paramètres](./media/active-directory-saas-sprinklr-tutorial/IC782914.png "Paramètres")

4.  Cliquez sur **Add User**.

    ![Paramètres](./media/active-directory-saas-sprinklr-tutorial/IC782915.png "Paramètres")

5.  Dans la page **Edit User**, procédez comme suit :

    ![Edit user](./media/active-directory-saas-sprinklr-tutorial/IC782916.png "Edit user")

    1.  Dans les zones de texte **Email**, **First Name** et **Last Name**, saisissez les informations du compte utilisateur Azure AD que vous souhaitez approvisionner.
    2.  Sélectionnez **Password Disabled**.
    3.  Sélectionnez une langue dans **Language**.
    4.  Sélectionnez un type d’utilisateur dans **User Type**.
    5.  Cliquez sur **Update**.

    >[AZURE.IMPORTANT] Décochez **Password Disabled** pour permettre à un utilisateur de se connecter par le biais d’un fournisseur d’identité.

6.  Accédez à **Role**, puis procédez comme suit :

    ![Partner Roles](./media/active-directory-saas-sprinklr-tutorial/IC782917.png "Partner Roles")

    1.  Dans la liste **Global**, sélectionnez **ALL\_Permissions**.
    2.  Cliquez sur **Update**.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel outil ou API de création de compte utilisateur, fourni par Sprinklr, pour approvisionner des comptes utilisateur AAD.

##Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

###Pour affecter des utilisateurs à Sprinklr, procédez comme suit :

1.  Dans le portail Azure Classic, créez un compte de test.

2.  Dans la page d’intégration d’applications **Sprinklr**, cliquez sur **Affecter des utilisateurs**.

    ![Affecter des utilisateurs](./media/active-directory-saas-sprinklr-tutorial/IC782918.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.

    ![Oui](./media/active-directory-saas-sprinklr-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0921_2016-->