---
title: Créer des flux de travail avec le connecteur Azure IoT Central dans Microsoft Flow | Microsoft Docs
description: Utilisez le connecteur IoT Central dans Microsoft Flow pour déclencher des flux de travail, et créer, mettre à jour et supprimer des appareils dans les flux de travail.
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 02/20/2019
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: 555fe54174c9e13319af676cab3a5d3dcfaf2fe5
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57770247"
---
# <a name="build-workflows-with-the-iot-central-connector-in-microsoft-flow"></a>Créer des flux de travail avec le connecteur IoT Central dans Microsoft Flow

*Cette rubrique s’applique aux créateurs et aux administrateurs.*

Utilisez Microsoft Flow pour automatiser les flux de travail entre les nombreux services et applications sur lesquels s’appuient les utilisateurs métier. Avec le connecteur IoT Central dans Microsoft Flow, vous pouvez déclencher des workflows quand une règle est déclenchée dans IoT Central. Dans un flux de travail déclenché par IoT Central ou toute autre application, vous pouvez utiliser les actions dans le connecteur IoT Central pour créer un appareil, mettre à jour les propriétés et les paramètres d’un appareil, ou supprimer un appareil. Consultez [ces modèles Microsoft Flow](https://aka.ms/iotcentralflowtemplates) qui connectent IoT Central à d’autres services, comme les notifications mobiles et Microsoft Teams.

## <a name="prerequisites"></a>Conditions préalables

- Application avec paiement à l'utilisation
- Un compte Microsoft personnel, scolaire ou professionnel pour se connecter à Flow ([En savoir plus sur les plans Microsoft Flow](https://aka.ms/microsoftflowplans))

## <a name="trigger-a-workflow"></a>Déclencher un flux de travail

Cette section vous montre comment déclencher une notification mobile dans l’application mobile Flow lorsqu’une règle est déclenchée dans IoT Central.

1. Commencez par [créer une règle dans IoT Central](howto-create-telemetry-rules.md). Après avoir enregistré les conditions de règle, sélectionnez le **Microsoft Flow action** comme une nouvelle action. Un nouvel onglet ou une nouvelle fenêtre doit s’ouvrir dans votre navigateur, vous redirigeant dans Microsoft Flow.

    ![Créer une action Microsoft Flow](media/howto-add-microsoft-flow/createflowaction.png)

1. Connectez-vous à Microsoft Flow. Il n’est pas nécessaire d’utiliser le même compte que celui que vous utilisez dans IoT Central. Vous accédez à une page qui donne une vue d’ensemble montrant un connecteur IoT Central qui se connecte à une action personnalisée.

1. Se connecter au connecteur IoT Central et sélectionnez **continuer**. Vous accédez au concepteur Microsoft Flow pour créer votre flux de travail. Le flux de travail comporte un déclencheur IoT Central où votre application et votre règle sont déjà renseignées.

1. Choisissez **Nouvelle étape** et **Ajouter une action**. À ce stade, vous pouvez ajouter n’importe quelle action de votre choix à votre flux de travail. Par exemple, nous allons envoyer une notification mobile. Recherchez **notification**, puis choisissez **Notifications - M’envoyer une notification mobile**.

1. Dans l’action, renseignez le champ Texte avec ce que votre notification doit indiquer. Vous pouvez inclure du *contenu dynamique* provenant de votre règle IoT Central, en passant à votre notification des informations importantes, comme le nom de l’appareil et l’horodatage.

    > [!NOTE]
    > Sélectionnez le **plus** texte dans la fenêtre de contenu dynamique pour obtenir les valeurs de mesure et de la propriété qui a déclenché la règle.

    ![Édition d’une action dans Flow avec le volet dynamique ouvert](./media/howto-add-microsoft-flow/flowdynamicpane.png)

1. Lorsque vous avez terminé votre opération d’édition, sélectionnez **enregistrer**. Vous êtes alors dirigé vers la page qui donne une vue d’ensemble de votre flux de travail. Vous pouvez voir ici l’historique des exécutions et le partager avec d’autres collègues.

    > [!NOTE]
    > Si vous voulez que d’autres utilisateurs de votre application IoT Central modifient cette règle, vous devez la partager avec eux dans Microsoft Flow. Ajoutez leurs comptes Microsoft Flow comme propriétaires dans votre flux de travail.

1. Si vous revenez à votre application IoT Central, vous voyez que cette règle a une action Microsoft Flow sous la zone Actions.

Vous pouvez toujours commencer à créer un flux de travail avec le connecteur IoT Central dans Microsoft Flow. Vous pouvez ensuite choisir l’application IoT Central et la règle à laquelle se connecter.

## <a name="create-a-device-in-a-workflow"></a>Créer un appareil dans un flux de travail

Cette section vous montre comment créer un appareil dans IoT Central quand l’utilisateur appuie sur un bouton d’un appareil mobile avec l’application mobile Microsoft Flow. Vous pouvez utiliser cette action dans Flow pour l’intégration de systèmes ERP comme Dynamics à IoT Central en créant un appareil quand un appareil est ajouté ailleurs.

1. Commencez par créer un flux de travail vide dans Microsoft Flow.

1. Recherchez **Bouton Flow pour mobile** comme déclencheur.

1. Dans ce déclencheur, ajoutez une entrée de texte nommée **Device name** (Nom de l’appareil). Changez le texte de la description en **Enter the device name of your new device** (Entrez le nom d’appareil de votre nouvel appareil).

1. Ajoutez une nouvelle action. Recherchez l’action **Azure IoT Central - Créer un appareil**.

1. Choisissez votre application, puis choisissez un modèle d’appareil pour créer un appareil dans les listes déroulantes. Vous voyez que l’action se développe pour afficher toutes les propriétés et les paramètres de l’appareil.

1. Sélectionnez le champ Nom de l’appareil. Dans le volet Contenu dynamique, choisissez **Nom de l’appareil**. Cette valeur est transmise à partir de l’entrée de l’utilisateur entre dans l’application mobile et est le nom de votre nouvel appareil dans IoT Central. Dans cet exemple, le seul champ obligatoire est le nom de l’appareil, indiqué par un astérisque rouge. Un autre modèle d’appareil peut avoir plusieurs champs obligatoires qui doivent être renseignés pour pouvoir créer un appareil.

    ![Volet de l’action de création dynamique d’un appareil dans Flow](./media/howto-add-microsoft-flow/flowcreatedevice.png)

1. (Facultatif) Renseignez les autres champs comme vous le souhaitez pour la création des appareils.

1. Pour terminer, enregistrez votre flux de travail.

1. Essayez votre flux de travail dans l’application mobile Microsoft Flow. Accédez à l’onglet **Boutons** dans l’application. Vous devez voir votre flux de travail Bouton -> Créer un appareil. Entrez le nom de votre nouvel appareil et regardez-le apparaître dans IoT Central !

    ![Capture d’écran de la création d’un appareil mobile dans Flow](./media/howto-add-microsoft-flow/flowmobilescreenshot.png)

## <a name="update-a-device-in-a-workflow"></a>Mettre à jour un appareil dans un flux de travail

Cette section vous montre comment mettre à jour les paramètres et les propriétés d’un appareil dans IoT Central quand l’utilisateur appuie sur un bouton d’un appareil mobile avec l’application mobile Microsoft Flow.

1. Commencez par créer un flux de travail vide dans Microsoft Flow.

1. Recherchez **Bouton Flow pour mobile** comme déclencheur.

1. Dans ce déclencheur, ajoutez une entrée de texte comme **Location** (Emplacement) qui correspond à un paramètre ou à une propriété à modifier. Changez le texte de la description.

1. Ajoutez une nouvelle action. Recherchez l’action **Azure IoT Central - Mettre à jour un appareil**.

1. Choisissez votre application dans la liste déroulante. Vous avez maintenant besoin d’un ID de l’appareil existant que vous voulez mettre à jour. Vous pouvez vous procurer l’ID de l’appareil IoT Central à partir de **Device Explorer**.

    ![ID d’appareil dans l’Explorateur d’appareils d’IoT Central](./media/howto-add-microsoft-flow/iotcdeviceid.png)

1. Vous pouvez mettre à jour le nom de l’appareil. Pour mettre à jour des propriétés et des paramètres de l’appareil, vous devez sélectionner le modèle d’appareil de l’appareil que vous voulez mettre à jour dans la liste déroulante **Modèle d’appareil**. La vignette de l’action se développe pour montrer tous les paramètres et propriétés que vous pouvez mettre à jour.

    ![Flux de travail de mise à jour d’appareil dans Flow](./media/howto-add-microsoft-flow/flowupdatedevice.png)

1. Sélectionnez les propriétés et les paramètres que vous voulez mettre à jour. Dans le volet Contenu dynamique, sélectionnez l’entrée correspondante dans le déclencheur. Dans cet exemple, la valeur de Location (Emplacement) est propagée vers le bas pour mettre à jour la propriété Location de l’appareil.

1. Pour terminer, enregistrez votre flux de travail.

1. Essayez votre flux de travail dans l’application mobile Microsoft Flow. Accédez à l’onglet **Boutons** dans l’application. Vous devez voir votre flux de travail Bouton -> Mettre à jour un appareil. Renseignez les entrées : vous voyez alors que l’appareil est mis à jour dans IoT Central !

## <a name="delete-a-device-in-a-workflow"></a>Supprimer un appareil dans un flux de travail

Vous pouvez supprimer un appareil en utilisant son ID d’appareil avec l’action **Azure IoT Central - Supprimer un appareil**. Voici un exemple de flux de travail qui supprime un appareil quand l’utilisateur appuie sur un bouton dans l’application mobile Microsoft Flow.

   ![Flux de travail de suppression d’appareil dans Flow](./media/howto-add-microsoft-flow/flowdeletedevice.png)

## <a name="troubleshooting"></a>Résolution de problèmes

Si vous rencontrez des problèmes pour créer une connexion au connecteur Azure IoT Central, voici quelques conseils pour vous aider.

1. Les comptes personnels Microsoft (comme les domaines @hotmail.com, @live.com ou @outlook.com) ne sont pas pris en charge pour l’instant. Vous devez utiliser un Azure Active Directory (AD) ou compte scolaire.

2. Pour utiliser le connecteur IoT Central dans Microsoft Flow, vous devez vous être connecté au moins une fois à l’application IoT Central. Sinon, l’application n’apparaît pas dans les listes déroulantes Application.

3. Si vous recevez une erreur lors de l’utilisation d’un compte Azure AD, essayez d’ouvrir Windows PowerShell et exécutez les applets de commande suivante en tant qu’administrateur.

    ``` PowerShell
    Install-Module AzureAD
    Connect-AzureAD
    New-AzureADServicePrincipal -AppId 9edfcdd9-0bc5-4bd4-b287-c3afc716aac7 -DisplayName "Azure IoT Central"
    ```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris comment utiliser Microsoft Flow pour générer des flux de travail, l’étape suivante suggérée consiste à [gérer les appareils](howto-manage-devices.md).

