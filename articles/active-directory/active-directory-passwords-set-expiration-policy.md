<properties
	pageTitle="Définir des stratégies d’expiration de mot de passe dans Azure Active Directory | Microsoft Azure"
	description="Découvrir comment vérifier les stratégies d’expiration et modifier l’expiration du mot de passe utilisateur isolément ou en bloc pour les mots de passe Azure Active Directory"
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/12/2016"
	ms.author="curtand"/>


# Définir des stratégies d’expiration de mot de passe dans Azure Active Directory

> [AZURE.IMPORTANT] **Rencontrez-vous des problèmes de connexion ?** Dans ce cas, [voici comment vous pouvez modifier et réinitialiser votre mot de passe](active-directory-passwords-update-your-own-password.md).

En tant qu’administrateur global d’un service cloud Microsoft, vous pouvez utiliser le Module Microsoft Azure Active Directory pour Windows PowerShell afin de configurer des mots de passe utilisateur qui n’expirent pas. Vous pouvez également utiliser des applets de commande Windows PowerShell pour supprimer la configuration de non-expiration, ou voir quels mots de passe utilisateur sont configurés pour ne pas expirer. Cet article fournit une aide pour des services cloud, tels que Microsoft Intune et Office 365, qui s’appuient sur Microsoft Azure Active Directory pour les services d’annuaire et d’identité.

  > [AZURE.NOTE] Seuls les mots de passe de comptes d’utilisateurs non synchronisés via une synchronisation d’annuaires peuvent être configurés pour ne pas expirer. Pour plus d’informations sur la synchronisation d’annuaires, voir la liste de rubriques dans [Plan de la synchronisation d’annuaires](https://msdn.microsoft.com/library/azure/hh967642.aspx).

Pour utiliser des applets de commande Windows PowerShell, vous devez commencer par les installer.

## Que voulez-vous faire ?

- [Vérifier la stratégie d’expiration d’un mot de passe](#how-to-check-expiration-policy-for-a-password)

- [Définir un mot de passe pour qu’il expire](#set-a-password-to-expire)

- [Définir un mot de passe pour qu’il n’expire pas](#set-a-password-to-never-expire)

## Vérifier la stratégie d’expiration pour un mot de passe

1.  Connectez-vous à Windows PowerShell à l’aide de vos informations d’identification d’administrateur de la société.

2.  Effectuez l’une des actions suivantes :

	- Pour voir si le mot de passe d’un utilisateur donné est défini pour ne jamais expirer, exécutez l’applet de commande suivante en utilisant le nom d’utilisateur principal (UPN) (par exemple, aprilr@contoso.onmicrosoft.com) ou l’identifiant utilisateur de l’utilisateur à vérifier : `Get-MSOLUser -UserPrincipalName <user ID> | Select PasswordNeverExpires`

	- Pour afficher le paramètre « Le mot de passe n’expire jamais » pour tous les utilisateurs, exécutez l’applet de commande suivante : `Get-MSOLUser | Select UserPrincipalName, PasswordNeverExpires`

## Définir un mot de passe pour qu’il expire

1.  Connectez-vous à Windows PowerShell à l’aide de vos informations d’identification d’administrateur de la société.

2.  Effectuez l’une des actions suivantes :

	- Pour définir le mot de passe d’un utilisateur afin qu’il expire, exécutez l’applet de commande suivante en utilisant le nom d’utilisateur principal (UPN) ou l’identifiant utilisateur de l’utilisateur : `Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires $false`

	- Pour définir les mots de passe de tous les utilisateurs de l’organisation afin qu’ils expirent, utilisez l’applet de commande suivante : `Get-MSOLUser | Set-MsolUser -PasswordNeverExpires $false`

## Définir un mot de passe pour qu’il n’expire jamais

1. Connectez-vous à Windows PowerShell à l’aide de vos informations d’identification d’administrateur de la société.

2.  Effectuez l’une des actions suivantes :

	- Pour définir le mot de passe d’un utilisateur afin qu’il n’expire jamais, exécutez l’applet de commande suivante en utilisant le nom d’utilisateur principal (UPN) ou l’identifiant d’utilisateur de l’utilisateur : `Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires $true`

	- Pour définir les mots de passe de tous les utilisateurs de l’organisation afin qu’ils n’expirent jamais, utilisez l’applet de commande suivante : `Get-MSOLUser | Set-MsolUser -PasswordNeverExpires $true`

## Étapes suivantes

* **Rencontrez-vous des problèmes de connexion ?** Dans ce cas, [voici comment vous pouvez modifier et réinitialiser votre mot de passe](active-directory-passwords-update-your-own-password.md).

<!---HONumber=AcomDC_0921_2016-->