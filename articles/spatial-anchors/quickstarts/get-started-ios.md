---
title: Démarrage rapide - Créer une application iOS avec Azure Spatial Anchors | Microsoft Docs
description: Dans ce démarrage rapide, vous allez apprendre à générer une application iOS à l’aide de Spatial Anchors.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: bb64e2b633957ca92636a03333be40aa275dfd25
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56870242"
---
# <a name="quickstart-create-an-ios-app-with-azure-spatial-anchors-in-either-swift-or-objective-c"></a>Démarrage rapide : Créer une application iOS avec Azure Spatial Anchors, en Swift ou Objective-C

Ce démarrage rapide explique comment créer une application iOS à l’aide d’[Azure Spatial Anchors](../overview.md) en Swift ou Objective-C. Azure Spatial Anchors est un service de développement multiplateforme qui vous permet de créer des expériences de réalité mixte en utilisant des objets qui conservent leur emplacement d’un appareil à un autre au fil du temps. Quand vous aurez terminé, vous aurez une application iOS ARKit capable d’enregistrer et de rappeler une ancre spatiale.

Vous découvrirez comment effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer un compte Spatial Anchors
> * Configurer l’identificateur et la clé du compte Spatial Anchors
> * Déployer et exécuter sur un appareil iOS

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

Pour suivre ce guide de démarrage rapide, veillez à avoir :

- Une machine macOS prête pour le développement dotée de <a href="https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12" target="_blank">Xcode 10+</a> et de <a href="https://cocoapods.org" target="_blank">CocoaPods</a>.
- Un appareil iOS <a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">compatible ARKit</a> prêt pour le développement.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Ouvrir l’exemple de projet

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Installez les pods nécessaires à l’aide de CocoaPods :

# <a name="swifttabopenproject-swift"></a>[Swift](#tab/openproject-swift)

Accédez à `iOS/Swift/`.

```bash
cd ./iOS/Swift/
```

# <a name="objective-ctabopenproject-objc"></a>[Objective-C](#tab/openproject-objc)

Accédez à `iOS/Objective-C/`.

```bash
cd ./iOS/Objective-C/
```

***

Exécutez `pod install --repo-update` pour installer les CocoaPods pour le projet.

Ouvrez maintenant `.xcworkspace` dans Xcode.

# <a name="swifttabopenproject-swift"></a>[Swift](#tab/openproject-swift)

```bash
open ./SampleSwift.xcworkspace
```

# <a name="objective-ctabopenproject-objc"></a>[Objective-C](#tab/openproject-objc)

```bash
open ./SampleObjC.xcworkspace
```

***

## <a name="configure-account-identifier-and-key"></a>Configurer l’identificateur et la clé du compte

L’étape suivante consiste à utiliser l’identificateur et la clé du compte enregistrés précédemment au moment de configurer la ressource Spatial Anchors pour configurer l’application.

# <a name="swifttabopenproject-swift"></a>[Swift](#tab/openproject-swift)

Ouvrez `iOS/Swift/SampleSwift/ViewController.swift`.

Localisez le champ `SpatialAnchorsAccountKey` et remplacez `Set me` par la clé du compte.

Localisez le champ `SpatialAnchorsAccountId` et remplacez `Set me` par l’identificateur du compte.

# <a name="objective-ctabopenproject-objc"></a>[Objective-C](#tab/openproject-objc)

Ouvrez `iOS/Objective-C/SampleObjC/ViewController.m`.

Localisez le champ `SpatialAnchorsAccountKey` et remplacez `Set me` par la clé du compte.

Localisez le champ `SpatialAnchorsAccountId` et remplacez `Set me` par l’identificateur du compte.

***

## <a name="deploy-the-app-to-your-ios-device"></a>Déployer l’application sur votre appareil iOS

Connectez l’appareil iOS au Mac et définissez le **schéma actif** sur votre appareil iOS.

![Sélectionner l’appareil](./media/get-started-ios/select-device.png)

Sélectionnez **Build and then run the current scheme**.

![Déployer et exécuter](./media/get-started-ios/deploy-run.png)

> [!NOTE]
> Si l’erreur `library not found for -lPods-SampleObjC` s’affiche, vous avez probablement ouvert le fichier `.xcodeproj` à la place de `.xcworkspace`. Ouvrez `.xcworkspace` et réessayez.

Dans Xcode, arrêtez l’application en appuyant sur **Stop**.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Tutoriel : Partager Spatial Anchors sur des appareils](../tutorials/tutorial-share-anchors-across-devices.md)
