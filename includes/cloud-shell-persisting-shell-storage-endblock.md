---
author: cynthn
ms.service: azure
ms.topic: include
ms.date: 11/09/2018
ms.author: cynthn
ms.openlocfilehash: c2ed33aea77b5478e8d17f6bd0213ef3e778b806
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2018
ms.locfileid: "51572076"
---
## <a name="transfer-local-files-to-cloud-shell"></a>Transférer des fichiers locaux vers Cloud Shell
Le répertoire `clouddrive` se synchronise dans le panneau Stockage du portail Azure. Utilisez ce panneau pour échanger des fichiers avec votre partage de fichiers. La mise à jour des fichiers à partir de Cloud Shell se reflète dans l’interface graphique du stockage de fichiers quand vous actualisez le panneau.

### <a name="download-files"></a>Télécharger des fichiers

![Liste de fichiers locaux](../articles/cloud-shell/media/persisting-shell-storage/download.png)
1. Dans le portail Azure, accédez au partage de fichiers monté.
2. Sélectionnez le fichier cible.
3. Cliquez sur le bouton **Télécharger**.

### <a name="upload-files"></a>Charger des fichiers

![Fichiers locaux à charger](../articles/cloud-shell/media/persisting-shell-storage/upload.png)
1. Accédez au partage de fichiers monté.
2. Cliquez sur le bouton **Charger**.
3. Sélectionnez le ou les fichiers que vous souhaitez charger.
4. Confirmez le chargement.

Vous devriez à présent voir les fichiers accessibles dans votre répertoire `clouddrive` dans Cloud Shell.