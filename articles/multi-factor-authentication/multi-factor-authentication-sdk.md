<properties 
	pageTitle="Intégration de vos identités locales avec Azure Active Directory"
	description="Cette page décrit Azure AD Connect et vous explique pourquoi l’utiliser."
	services="multi-factor-authentication"
	documentationCenter=""
	authors="kgremban"
	manager="femila"
	editor="curtand"/>

<tags
	ms.service="multi-factor-authentication"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/04/2016"
	ms.author="kgremban"/>

# Création de Multi-Factor Authentication dans des applications personnalisées (SDK)

> [AZURE.IMPORTANT]  Si vous souhaitez télécharger le Kit de développement logiciel (SDK), vous devrez créer un fournisseur d’authentification multifacteur Azure, même si vous disposez de licences EMS, AAD Premium ou Azure MFA. Si vous créez un fournisseur d’authentification multifacteur Azure à cet effet et que vous possédez déjà des licences, vous devez créer le fournisseur à l’aide du modèle **Par utilisateur activé** et lier le fournisseur au répertoire qui contient les licences EMS, Azure AD Premium ou Azure MFA. Cela garantit que vous ne serez pas facturé à moins d’avoir plus d’utilisateurs uniques utilisant le Kit de développement logiciel (SDK) que de licences possédées.

Le Kit de développement logiciel (SDK) Azure Multi-Factor Authentication vous permet de vérifier les appels téléphoniques et les messages texte directement dans les processus de connexion et de transaction des applications de votre locataire Azure AD.

Le Kit de développement logiciel (SDK) Multi-Factor Authentication est disponible pour C#, Visual Basic (.NET), Java, Perl, PHP et Ruby. Le SDK fournit un wrapper fin autour de l'authentification multifacteur. Il inclut tout le nécessaire pour écrire votre code, y compris des fichiers de code source commentés, des fichiers d'exemples et un fichier Lisez-moi détaillé. Chaque SDK inclut également un certificat et une clé privée pour chiffrer les transactions spécifiques à votre fournisseur d'authentification multifacteur. Tant que vous disposez d'un fournisseur, vous pouvez télécharger le SDK dans toutes les langues et tous formats de votre choix.

La structure des API dans le Kit de développement logiciel (SDK) Multi-Factor Authentication est assez simple. Vous passez un simple appel de fonction vers une API avec les paramètres d'option multifacteur, notamment le mode de vérification et les données utilisateur comme le numéro de téléphone à appeler ou le code PIN à valider. Les API traduisent l'appel de fonction en demandes de services Web au service d'authentification multifacteur Azure basé sur le cloud. Tous les appels doivent inclure une référence au certificat privé compris dans chaque SDK.

Comme les API n'ont pas accès aux utilisateurs enregistrés dans Azure Active Directory, vous devez fournir les informations utilisateur, notamment les numéros de téléphone et les codes PIN, dans un fichier ou une base de données. En outre, les API ne fournissent pas de fonctionnalités d'inscription ou de gestion des utilisateurs, et vous devez donc créer ces processus dans votre application.






## Téléchargement du SDK Azure Multi-Factor Authentication

Le téléchargement du SDK Azure Multi-Factor Authentication nécessite un [fournisseur Azure Multi-Factor Auth](multi-factor-authentication-get-started-auth-provider.md). Cela requiert un abonnement Azure complet, même si vous possédez des licences Azure MFA, Azure AD Premium ou Enterprise Mobility Suite. Pour télécharger le Kit de développement logiciel (SDK), vous devez accéder au portail de gestion multi-facteur soit en gérant directement le fournisseur Multi-Factor Auth, soit en cliquant sur le lien **Accéder au portail** sur la page des paramètres du MFA.


### Pour télécharger le SDK Azure Multi-Factor Authentication à partir du portail Azure


1. Connectez-vous au portail Azure en tant qu’administrateur.
2. Sélectionnez à gauche Active Directory.
3. En haut de la page Active Directory, cliquez sur **Fournisseurs d'authentification multifacteur**
4. Cliquez sur **Gérer** au bas de la page
5. Une nouvelle page apparaît. En bas à gauche, cliquez sur SDK.
<center>![Download](./media/multi-factor-authentication-sdk/download.png)</center>
6. Sélectionnez la langue souhaitée, puis cliquez sur l'un des liens de téléchargement associés.
7. Enregistrez le fichier téléchargé.



### Pour télécharger le SDK Azure Multi-Factor Authentication à l'aide des paramètres du service


1. Connectez-vous au portail Azure en tant qu’administrateur.
2. Sélectionnez à gauche Active Directory.
3. Double-cliquez sur votre instance d'Azure AD.
4. En haut de la page, cliquez sur **Configurer**
5. Sous Authentification multifacteur, sélectionnez **Gérer les paramètres du service** ![Télécharger](./media/multi-factor-authentication-sdk/download2.png)
6. Au bas de la page Paramètres du service, cliquez sur **Accéder au portail**. ![Télécharger](./media/multi-factor-authentication-sdk/download3a.png)
7. Une nouvelle page apparaît. En bas à gauche, cliquez sur SDK.
8. Sélectionnez la langue souhaitée, puis cliquez sur l'un des liens de téléchargement associés.
9. Enregistrez le fichier téléchargé.

## Contenu du SDK Azure Multi-Factor Authentication
Le SDK contient les éléments suivants :

- **LISEZ-MOI**. Explique comment utiliser les API Multi-Factor Authentication dans une application nouvelle ou existante.
- **Fichier(s) source** pour l'authentification multifacteur
- **Certificat client** que vous utilisez pour communiquer avec le service d'authentification multifacteur
- **Clé privée** pour le certificat
- **Résultats des appels** Une liste des codes de résultats d’appels. Pour ouvrir ce fichier, utilisez une application avec mise en forme du texte, comme WordPad. Utilisez les codes de résultats d’appels pour tester et dépanner l'implémentation de l'authentification multifacteur dans votre application. Ce ne sont pas des codes d'état de l'authentification.
- **Exemples.** Exemple de code pour une implémentation fonctionnelle de base de l'authentification multifacteur.


>[AZURE.WARNING]Le certificat client est un certificat privé unique généré spécialement pour vous. Ne partagez pas ou n’égarez pas ce fichier. Il garantit la sécurité de vos communications avec le service Multi-Factor Authentication.

## Exemple de code : Vérification de téléphone en mode standard

Cet exemple de code montre comment utiliser les API du SDK Azure Multi-Factor Authentication pour ajouter la vérification de l'appel vocal en mode standard à votre application. Le mode standard est un appel téléphonique auquel l'utilisateur répond en appuyant sur la touche #.

Cet exemple utilise le SDK Azure Multi-Factor Authentication C# .NET 2.0 dans une application ASP.NET de base avec une logique côté serveur C#, mais le processus est très similaire pour des implémentations simples dans d'autres langues. Comme le SDK inclut les fichiers sources et non les fichiers exécutables, vous pouvez générer les fichiers et y fait référence ou les inclure directement dans votre application.

>[AZURE.NOTE]Lorsque vous implémentez l'authentification multifacteur, utilisez les facteurs supplémentaires comme la vérification secondaire ou tertiaire pour compléter votre méthode d'authentification principale. Ces méthodes ne sont pas conçues pour être utilisées comme méthode d'authentification principale.

### Vue d'ensemble d’un exemple de code
Cet exemple de code pour une application de démonstration Web très simple utilise un appel téléphonique auquel l’utilisateur répond en appuyant sur la touche #. Ce facteur d'appel téléphonique est appelé mode standard dans l'authentification multifacteur.

Le code côté client n'inclut aucun élément spécifique à l'authentification multifacteur. Comme les facteurs d'authentification supplémentaires sont indépendants de l'authentification principale, vous pouvez les ajouter sans modifier l'interface d'ouverture de session existante. Les API du SDK Multi-Factor vous permettent de personnaliser l'expérience utilisateur, mais peut-être qu’aucune modification ne sera nécessaire.

Le code côté serveur ajoute l'authentification en mode standard à l'étape 2. Il crée un objet PfAuthParams avec les paramètres requis pour la vérification en mode standard : nom d'utilisateur, numéro de téléphone, mode, et chemin d'accès au certificat client (CertFilePath), requis pour chaque appel. Pour obtenir une démonstration de tous les paramètres dans PfAuthParams, consultez l'exemple de fichier du Kit de développement logiciel (SDK).

Ensuite, le code passe l'objet PfAuthParams à la fonction pf\_authenticate(). La valeur de retour indique si l'authentification a réussi ou échoué. Les paramètres de sortie, callStatus et errorID, contiennent des informations supplémentaires sur le résultat de l’appel. Les codes de résultats d’appel sont documentés dans le fichier de résultats d'appel du Kit de développement logiciel (SDK).

Cette implémentation minimale peut être écrite en quelques lignes. Toutefois, dans le code de production, vous pouvez inclure une gestion plus sophistiquée des erreurs, un autre code de base de données et une expérience utilisateur améliorée.

### Code du client Web

Voici le code du client Web d’une page de démonstration.


	<%@ Page Language="C#" AutoEventWireup="true" CodeFile="Default.aspx.cs" Inherits="_Default" %>

	<!DOCTYPE html>

	<html xmlns="http://www.w3.org/1999/xhtml">
	<head runat="server">
	<title>Multi-Factor Authentication Demo</title>
	</head>
	<body>
	<h1>Azure Multi-Factor Authentication Demo</h1>
	<form id="form1" runat="server">

	<div style="width:auto; float:left">
	Username:&nbsp;<br />
	Password:&nbsp;<br />
	</div>

	<div">
	<asp:TextBox id="username" runat="server" width="100px"/><br />
	<asp:Textbox id="password" runat="server" width="100px" TextMode="password" /><br />
	</div>

	<asp:Button id="btnSubmit" runat="server" Text="Log in" onClick="btnSubmit_Click"/>

	<p><asp:Label ID="lblResult" runat="server"></asp:Label></p>

	</form>
	</body>
	</html>


### Code côté serveur

Dans le code côté serveur suivant, l'authentification multifacteur est configurée et exécutée à l'étape 2. Le mode standard (MODE\_STANDARD) est un appel téléphonique auquel l'utilisateur répond en appuyant sur la touche #.

	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Web;
	using System.Web.UI;
	using System.Web.UI.WebControls;

	public partial class _Default : System.Web.UI.Page
	{
	    protected void Page_Load(object sender, EventArgs e)
	    {
	    }

	    protected void btnSubmit_Click(object sender, EventArgs e)
	    {
	        // Step 1: Validate the username and password
	        if (username.Text != "Contoso" || password.Text != "password")
	        {
	            lblResult.ForeColor = System.Drawing.Color.Red;
	            lblResult.Text = "Username or password incorrect.";
	        }
	        else
	        {
	            // Step 2: Perform multi-factor authentication

	            // Add call details from the user database.
	            PfAuthParams pfAuthParams = new PfAuthParams();
	            pfAuthParams.Username = username.Text;
	            pfAuthParams.Phone = "9134884271";
	            pfAuthParams.Mode = pf_auth.MODE_STANDARD;

	            // Specify a client certificate
	            // NOTE: This file contains the private key for the client
	            // certificate. It must be stored with appropriate file
	            // permissions.
	            pfAuthParams.CertFilePath = "c:\\cert_key.p12";

	            // Perform phone-based authentication
	            int callStatus;
	            int errorId;

	            if(pf_auth.pf_authenticate(pfAuthParams, out callStatus, out errorId))
	            {
	                lblResult.ForeColor = System.Drawing.Color.Green;
	                lblResult.Text = "Multi-Factor Authentication succeeded.";
	            }
	            else
	            {
	                lblResult.ForeColor = System.Drawing.Color.Red;
	                lblResult.Text = " Multi-Factor Authentication failed.";
	            }
	        }

	    }
	}

<!---HONumber=AcomDC_0921_2016-->