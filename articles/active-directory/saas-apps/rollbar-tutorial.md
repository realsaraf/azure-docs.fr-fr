---
title: 'Tutoriel : Intégration d’Azure Active Directory à OpsGenie | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Rollbar.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 57537e54-9388-4272-a610-805ce45a451f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/04/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ebedddf5e62e41bd0ef2d4c426df448a81a1e2d
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57837216"
---
# <a name="tutorial-azure-active-directory-integration-with-rollbar"></a>Tutoriel : Intégration d’Azure Active Directory à Rollbar

Dans ce didacticiel, vous allez apprendre à intégrer Rollbar à Azure Active Directory (Azure AD).

L’intégration de Rollbar dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Rollbar.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Rollbar (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration d’Azure AD à Rollbar, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Rollbar pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Rollbar depuis la galerie
1. Configuration et test de l’authentification unique Azure AD

## <a name="adding-rollbar-from-the-gallery"></a>Ajout de Rollbar depuis la galerie
Pour configurer l’intégration de Rollbar avec Azure AD, vous devez ajouter Rollbar, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Rollbar à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

1. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
1. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

1. Dans la zone de recherche, tapez **Rollbar**, sélectionnez **Rollbar** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Rollbar dans la liste des résultats](./media/rollbar-tutorial/tutorial_rollbar_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Rollbar avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Rollbar équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur Rollbar associé doit être établie.

Dans Rollbar, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l'authentification unique Azure AD avec Rollbar, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
1. **[Créer utilisateur de test Rollbar](#create-a-rollbar-test-user)** pour avoir un équivalent de Britta Simon dans Rollbar lié à la représentation Azure AD associée.
1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
1. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Rollbar.

**Pour configurer l'authentification unique Azure AD avec Rollbar, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **Rollbar**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

1. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/rollbar-tutorial/tutorial_rollbar_samlbase.png)

1. Dans la section **Domaines et URL Rollbar**, suivez les étapes ci-dessous si vous souhaitez configurer l’application en mode initié par **IDP** :

    ![Informations d’authentification unique dans Domaine et URL Rollbar](./media/rollbar-tutorial/tutorial_rollbar_url.png)

    a. Dans la zone de texte **Identificateur**, tapez l’URL : `https://saml.rollbar.com`

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://rollbar.com/<accountname>/saml/sso/azure/`

1. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de service**, cochez **Afficher les paramètres d’URL avancés**, puis effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL Rollbar](./media/rollbar-tutorial/tutorial_rollbar_url1.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://rollbar.com/<accountname>/saml/login/azure/`
     
    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de réponse et l’URL de connexion réelles. Pour obtenir ces valeurs, contactez [l’équipe de support technique Rollbar](mailto:support@rollbar.com). 

1. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/rollbar-tutorial/tutorial_rollbar_certificate.png) 

1. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/rollbar-tutorial/tutorial_general_400.png)
    
1. Dans une autre fenêtre de navigateur web, connectez-vous au site de votre entreprise Rollbar en tant qu’administrateur.

1. Cliquez sur les **paramètres du profil** dans l’angle supérieur droit puis cliquez sur les **paramètres de nom du compte**.
    
    ![Configuration](./media/rollbar-tutorial/general.png)

1. Cliquez sur **Fournisseur d’identité** sous SÉCURITÉ.

    ![Configuration](./media/rollbar-tutorial/configure1.png)

1. Dans la section **Fournisseur d’identité SAML**, procédez comme suit :
    
    ![Configuration](./media/rollbar-tutorial/configure2.png)

    a. Sélectionnez **AZURE** dans la liste déroulante **Fournisseur d’identité SAML**.

    b. Ouvrez votre fichier de métadonnées dans le Bloc-notes, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **Métadonnées SAML**.

    c. Cliquez sur **Enregistrer**.

1. Après avoir cliqué sur le bouton Enregistrer, l’écran se présente comme suit :
    
    ![Configuration](./media/rollbar-tutorial/configure3.png)
    > [!NOTE] 
    > Pour exécuter l’étape suivante, vous devez d’abord vous ajouter vous-même comme utilisateur dans l’application Rollbar dans Azure.
    a. Si vous voulez que tous les utilisateurs s’authentifient via Azure, cliquez sur **log in via your identity provider (Se connecter via votre fournisseur d’identité)** pour vous authentifier une nouvelle fois via Azure.  

    b.  Une fois que vous êtes revenu sur l’écran, sélectionnez la case à cocher **Demander la connexion via un fournisseur d’identité SAML**.

    b. Cliquez sur **Enregistrer**.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Pour en savoir plus sur la fonctionnalité de documentation incorporée, accédez à : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/rollbar-tutorial/create_aaduser_01.png)

1. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/rollbar-tutorial/create_aaduser_02.png)

1. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/rollbar-tutorial/create_aaduser_03.png)

1. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/rollbar-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-a-rollbar-test-user"></a>Créer un utilisateur de test Rollbar

Pour se connecter à Rollbar, les utilisateurs d’Azure AD doivent être configurés dans Rollbar. Dans le cas de Rollbar, le provisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous au site d’entreprise Rollbar en tant qu’administrateur.

1. Cliquez sur les **paramètres du profil** dans l’angle supérieur droit puis cliquez sur les **paramètres de nom du compte**.

    ![Utilisateur](./media/rollbar-tutorial/general.png)

1. Cliquez sur **Utilisateurs**.
    
    ![Ajouter un employé](./media/rollbar-tutorial/user1.png)

1. Cliquez sur **Inviter des membres d’équipe**.

    ![Inviter des personnes](./media/rollbar-tutorial/user2.png)

1. Dans la zone de texte, entrez le nom d’utilisateur, par exemple **brittasimon\@contoso.com** puis cliquez sur **ajouter/inviter**.

    ![Inviter des personnes](./media/rollbar-tutorial/user3.png)

1. L’utilisateur reçoit une invitation et, après l’avoir acceptée, il est créé dans le système.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Rollbar.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à Rollbar, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

1. Dans la liste des applications, sélectionnez **Rollbar**.

    ![Lien Rollbar dans la liste des applications](./media/rollbar-tutorial/tutorial_rollbar_app.png)  

1. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

1. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

1. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

1. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Rollbar dans le volet d’accès, vous devez être connecté automatiquement à votre application Rollbar.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/rollbar-tutorial/tutorial_general_01.png
[2]: ./media/rollbar-tutorial/tutorial_general_02.png
[3]: ./media/rollbar-tutorial/tutorial_general_03.png
[4]: ./media/rollbar-tutorial/tutorial_general_04.png

[100]: ./media/rollbar-tutorial/tutorial_general_100.png

[200]: ./media/rollbar-tutorial/tutorial_general_200.png
[201]: ./media/rollbar-tutorial/tutorial_general_201.png
[202]: ./media/rollbar-tutorial/tutorial_general_202.png
[203]: ./media/rollbar-tutorial/tutorial_general_203.png

