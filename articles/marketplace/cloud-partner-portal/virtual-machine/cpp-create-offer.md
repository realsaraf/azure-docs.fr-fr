---
title: Créer une offre de machine virtuelle dans la Place de marché Microsoft Azure | Microsoft Docs
description: Répertorie les étapes requises pour créer une offre de machine virtuelle pour la Place de marché Microsoft Azure.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: a3124ca2f163291d266fba77255d2ee98d30fd23
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57888806"
---
# <a name="create-virtual-machine-offer"></a>Créer une offre de machine virtuelle

Cette section répertorie les étapes requises pour créer une requête d’offre de machine virtuelle pour la Place de marché Microsoft Azure.  Les offres apparaissent sous la forme d’une entité distincte dans la Place de marché Microsoft Azure et sont associées à un ou plusieurs références SKU.  Une offre de machine virtuelle est composée des regroupements de ressources et de services de prise en charge suivants : 

![Ressources d’une offre de machine virtuelle](./media/publishvm_002.png)

où :

|  **Groupe de ressources**   |  **Description**  |
|  ---------------   |  ---------------  |
|    Références (SKU)            |  Plus petite unité achetable d’une offre. Plusieurs références SKU peuvent être associées à une offre (classe de produits) afin de différencier les fonctionnalités prises en charge, les types d’images de machine virtuelle et les modèles de facturation. |
|  Marketplace       | Contient des ressources et des spécifications marketing, juridique et de gestion des prospects.  <ul><li> Les ressources marketing incluent le nom, la description et les logos de l’offre</li> <li> Les ressources juridiques incluent une politique de confidentialité, des conditions d’utilisation et d’autres documents juridiques</li>  <li> La stratégie de gestion des prospects vous permet de spécifier comment gérer les prospects à partir du portail de l’utilisateur final de la Place de marché Microsoft Azure.</li> </ul> |
| Support            | Contient les informations de contact et de stratégie du support |
| Version d’évaluation         | Définit les ressources qui permettent aux utilisateurs finaux de tester votre offre avant de l’acheter |
|  |  |


## <a name="new-offer-form"></a>Formulaire de nouvelle offre

Une fois connecté dans le [Portail Cloud Partner](https://cloudpartner.azure.com/), cliquez sur l’élément **+ Nouvelle offre** sur la barre de menus de gauche. Dans le menu qui s’affiche, cliquez sur **Machines virtuelles** pour afficher le formulaire **Nouvelle offre** et démarrer le processus de définition des ressources d’une nouvelle offre de machine virtuelle. 
<!-- not all publishers see corevm or azure apps test, you need to be whitelisted to see them. we should hide those in these images. -->

![Sélection de l’interface utilisateur de nouvelle offre de machine virtuelle](./media/publishvm_003.png)

> [!WARNING]
> Si l’option **Machines virtuelles** n’apparaît pas ou n’est pas activée, votre compte n’est pas autorisé à créer ce type d’offre.  Vérifiez que vous remplissez toutes les [conditions préalables](./cpp-prerequisites.md) de ce type d’offre, notamment l’inscription d’un compte de développeur.


## <a name="next-steps"></a>Étapes suivantes

Les rubriques suivantes de cette section présentent les onglets de la page **Nouvelle offre** (pour un type d’offre de machine virtuelle).  Chaque article explique comment utiliser l’onglet associé pour définir les groupes de ressources et les services de prise en charge de votre nouvelle offre de machine virtuelle.

- [Onglet des paramètres de l’offre](./cpp-offer-settings-tab.md)
- [Onglet des références SKU](./cpp-skus-tab.md)
- [Onglet de version d'évaluation](./cpp-test-drive-tab.md)
- [Onglet de la Place de marché](./cpp-marketplace-tab.md)
- [Onglet du support](./cpp-support-tab.md)
