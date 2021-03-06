---
title: Valider l’inscription auprès d’Azure pour Azure Stack | Microsoft Docs
description: Utilisez Azure Stack Readiness Checker pour valider l’inscription auprès d’Azure.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/23/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 03/23/2019
ms.openlocfilehash: a777fc1d9052eb58bbebd319fe6cc7f42a09cb9a
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/25/2019
ms.locfileid: "58403610"
---
# <a name="validate-azure-registration"></a>Valider l’inscription auprès d’Azure

Utilisez l’outil Azure Stack Readiness Checker (**AzsReadinessChecker**) pour vérifier que votre abonnement Azure est prêt à être utilisé avec Azure Stack. Validez l’inscription avant de commencer un déploiement d’Azure Stack. L’outil Readiness Checker valide ce qui suit :

- Le type d’abonnement Azure que vous utilisez est pris en charge. Les abonnements doivent être de type Fournisseur de services cloud (CSP) ou Contrat Entreprise (EA).
- Le compte que vous utilisez pour inscrire votre abonnement auprès d’Azure peut se connecter à Azure et correspond à un propriétaire de l’abonnement.

Pour plus d’informations sur l’inscription d’Azure Stack, consultez [Inscrire Azure Stack auprès d’Azure](azure-stack-registration.md).

## <a name="get-the-readiness-checker-tool"></a>Obtenir l’outil Readiness Checker

Téléchargez la dernière version d’**AzsReadinessChecker** sur le site [PowerShell Gallery](https://aka.ms/AzsReadinessChecker).  

## <a name="prerequisites"></a>Prérequis

Les prérequis suivants sont obligatoires :

**Ordinateur sur lequel l’outil est exécuté :**

- Windows 10 ou Windows Server 2016, avec connectivité à Internet.
- PowerShell 5.1 ou ultérieur. Pour vérifier votre version, exécutez l’applet de commande PowerShell suivante, puis examinez les versions **Major** et **Minor** :  

  ```powershell
  $PSVersionTable.PSVersion
  ```

- [PowerShell configuré pour Azure Stack](azure-stack-powershell-install.md).
- Dernière version de [Microsoft Azure Stack Readiness Checker](https://aka.ms/AzsReadinessChecker).  

**Environnement Azure Active Directory :** 

- Identifiez le nom d’utilisateur et le mot de passe d’un compte associé à un propriétaire de l’abonnement Azure que vous allez utiliser avec Azure Stack.  
- Identifiez l’ID d’abonnement pour l’abonnement Azure à utiliser.
- Identifiez l’environnement **AzureEnvironment** que vous allez utiliser. Les valeurs prises en charge pour le paramètre du nom d’environnement sont **AzureCloud**, **AzureChinaCloud** ou **AzureUSGovernment** selon l’abonnement Azure que vous utilisez.

## <a name="steps-to-validate-azure-registration"></a>Étapes pour valider l’inscription auprès d’Azure

1. Sur un ordinateur qui répond aux prérequis, ouvrez une invite PowerShell avec privilège élevé, puis exécutez la commande suivante pour installer **AzsReadinessChecker** :

   ```powershell
   Install-Module Microsoft.AzureStack.ReadinessChecker -Force
   ```

2. À l’invite PowerShell, exécutez la commande suivante pour définir `$registrationCredential` comme le compte associé au propriétaire de l’abonnement. Remplacez `subscriptionowner@contoso.onmicrosoft.com` par votre compte et le nom de votre locataire :

   ```powershell
   $registrationCredential = Get-Credential subscriptionowner@contoso.onmicrosoft.com -Message "Enter Credentials for Subscription Owner"
   ```

   > [!NOTE]
   > En tant que fournisseur de services cloud, quand vous utilisez un abonnement à des services partagés ou avec des droits d’utilisation interne, vous devez fournir les informations d’identification d’un utilisateur de cette instance AAD. En règle générale, celles-ci sont similaires à `subscriptionowner@iurcontoso.onmicrosoft.com`. Cet utilisateur doit avoir les informations d’identification appropriées, comme décrit dans l’étape précédente.

3. À l’invite PowerShell, exécutez ce qui suit pour définir `$subscriptionID` comme l’abonnement Azure à utiliser. Remplacez `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` par votre propre ID d’abonnement :

   ```powershell
   $subscriptionID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
   ```

4. À l’invite PowerShell, exécutez la commande suivante pour démarrer la validation de votre abonnement :

   - Spécifiez la valeur pour `AzureEnvironment` avec **AzureCloud**, **AzureGermanCloud** ou **AzureChinaCloud**.  
   - Indiquez l’administrateur et le nom du locataire Azure Active Directory.

   ```powershell
   Invoke-AzsRegistrationValidation -RegistrationAccount $registrationCredential -AzureEnvironment AzureCloud -RegistrationSubscriptionID $subscriptionID
   ```

5. Au terme de l’exécution de l’outil, passez en revue la sortie. Vérifiez que l’état est correct pour la connexion et les conditions d’inscription. Un résultat de validation réussie s’affiche, semblable à l’exemple suivant :

   ```shell
   Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
   Checking Registration Requirements: OK
   Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
   Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
   Invoke-AzsRegistrationValidation Completed
   ```

## <a name="report-and-log-file"></a>Rapport et fichier journal

Chaque fois qu’une validation s’exécute, les résultats sont journalisés dans **AzsReadinessChecker.log** et **AzsReadinessCheckerReport.json**. L’emplacement de ces fichiers est indiqué avec les résultats de la validation dans PowerShell.

Ces fichiers peuvent vous aider à partager l’état de validation avant de déployer Azure Stack ou à enquêter sur les problèmes de validation. Les deux fichiers conservent les résultats des vérifications de validation postérieures. Le rapport fournit à votre équipe de déploiement la confirmation de la configuration de l’identité. Le fichier journal peut aider l’équipe de déploiement ou de support à enquêter sur les problèmes de validation.

Par défaut, les deux fichiers sont écrits à l’emplacement **C:\Users\<nom_utilisateur>\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json**.  

- Utilisez le paramètre **-OutputPath** ***&lt;chemin&gt;*** à la fin de la ligne de commande d’exécution pour spécifier un emplacement de rapport différent.
- Utilisez le paramètre **-CleanReport** à la fin de la commande d’exécution pour effacer les informations sur les exécutions précédentes de l’outil du fichier **AzsReadinessCheckerReport.json**.

Pour plus d’informations, consultez [Rapport de validation Azure Stack](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Échec de validation

En cas d’échec de vérification de la validation, des détails sont fournis dans la fenêtre PowerShell. L’outil journalise également des informations dans le fichier **AzsReadinessChecker.log**.

Les exemples suivants donnent des conseils sur la façon de résoudre les échecs de validation courants :

### <a name="user-must-be-an-owner-of-the-subscription"></a>L’utilisateur doit être un propriétaire de l’abonnement.

```shell
Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
Checking Registration Requirements: Fail
Error Details for registration account admin@contoso.onmicrosoft.com:
The user admin@contoso.onmicrosoft.com is role(s) Reader for subscription 3f961d1c-d1fb-40c3-99ba-44524b56df2d. User must be an owner of the subscription to be used for registration.
Additional help URL https://aka.ms/AzsRemediateRegistration

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsRegistrationValidation Completed
```

**Cause** : Le compte n’est pas celui d’un administrateur de l’abonnement Azure.

**Résolution** : Utilisez le compte d’un administrateur de l’abonnement Azure auquel l’utilisation du déploiement d’Azure Stack sera facturée.

### <a name="expired-or-temporary-password"></a>Mot de passe temporaire ou ayant expiré

```shell
Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
Checking Registration Requirements: Fail
Error Details for registration account admin@contoso.onmicrosoft.com:
Checking Registration failed with: Retrieving TenantId for subscription [subscription ID] using account admin@contoso.onmicrosoft.com failed with AADSTS50055: Force Change Password.
Trace ID: [Trace ID]
Correlation ID: [Correlation ID]
Timestamp: 2018-10-22 11:16:56Z: The remote server returned an error: (401) Unauthorized.

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsRegistrationValidation Completed
```

**Cause** : Le compte ne peut pas se connecter, car le mot de passe est temporaire ou a expiré.

**Résolution** : Dans PowerShell, exécutez la commande suivante, puis suivez les invites pour réinitialiser le mot de passe.

```powershell
Login-AzureRMAccount
```

Vous pouvez également vous connecter au [portail Azure](https://portal.azure.com) en tant que propriétaire du compte et l’utilisateur sera obligé de changer de mot de passe.

### <a name="unknown-user-type"></a>Type d’utilisateur inconnu  

```shell
Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
Checking Registration Requirements: Fail
Error Details for registration account admin@contoso.onmicrosoft.com:
Checking Registration failed with: Retrieving TenantId for subscription <subscription ID> using <account> failed with unknown_user_type: Unknown User Type

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsRegistrationValidation Completed
```

**Cause** : Le compte ne peut pas se connecter à l’environnement Azure Active Directory spécifié. Dans cet exemple, **AzureChinaCloud** est spécifié comme **AzureEnvironment**.  

**Résolution** : Vérifiez que le compte est valide pour l’environnement Azure spécifié. Dans PowerShell, exécutez la commande suivante pour vérifier que le compte est valide pour un environnement spécifique :

```powershell
Login-AzureRmAccount -EnvironmentName AzureChinaCloud
```

## <a name="next-steps"></a>Étapes suivantes

- [Valider l’identité Azure](azure-stack-validate-identity.md)
- [Afficher le rapport de préparation](azure-stack-validation-report.md)
- [Considérations générales relatives à l’intégration de Microsoft Azure Stack](azure-stack-datacenter-integration.md)
