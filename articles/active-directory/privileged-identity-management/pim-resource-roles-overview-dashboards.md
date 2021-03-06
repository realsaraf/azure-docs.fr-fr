---
title: Un tableau de bord de ressource permet d’effectuer une révision d’accès dans PIM - Azure | Microsoft Docs
description: Explique comment utiliser un tableau de bord de ressources pour effectuer une révision d’accès dans Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 03/30/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 89abf15731bd125737e7c18ab45782820a856b38
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58012684"
---
# <a name="use-a-resource-dashboard-to-perform-an-access-review-in-pim"></a>Un tableau de bord de ressource permet d’effectuer une révision d’accès dans PIM

Il est possible d’utiliser un tableau de bord de ressources pour effectuer une révision d’accès dans Privileged Identity Management (PIM) pour les ressources Azure. Le tableau de bord Vue Administration se compose de trois éléments principaux :

- une représentation graphique des activations de rôle des ressources ;
- deux graphiques montrant la distribution des attributions de rôle par type d’attribution ;
- une zone de données se rapportant aux nouvelles attributions de rôle.

![Capture d’écran du tableau de bord Vue Administration montrant les graphiques](media/azure-pim-resource-rbac/rbac-overview-top.png)

![Capture d’écran du tableau de bord Vue Administration montrant les listes de données](media/azure-pim-resource-rbac/role-settings.png)

La représentation graphique des activations de rôle des ressources couvre les sept derniers jours. Ces données, limitées à la ressource sélectionnée, présentent les activations des rôles les plus courants (propriétaire, contributeur, administrateur de l’accès utilisateur) et tous rôles confondus.

À droite du graphique des activations, deux graphiques montrent la distribution des attributions de rôle par type d’attribution, pour les utilisateurs et les groupes. Vous pouvez passer des valeurs aux pourcentages (et vice versa) en sélectionnant une section du graphique.

Sous les graphiques apparaissent le nombre d’utilisateurs et de groupes ayant reçu de nouvelles attributions de rôle au cours des 30 derniers jours ainsi que la liste des rôles triée par nombre total d’attributions (dans l’ordre décroissant).

## <a name="next-steps"></a>Étapes suivantes

- [Démarrer une révision d’accès des rôles de ressources Azure dans PIM](pim-resource-roles-start-access-review.md) 
