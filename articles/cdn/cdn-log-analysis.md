---
title: Analyse des modèles d’utilisation CDN Azure | Microsoft Docs
description: Cet article décrit les différents types de rapports d’analyse disponibles pour les produits Azure CDN.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 95e18b3c-b987-46c2-baa8-a27a029e3076
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: magattus
ms.openlocfilehash: ef713c954d6eab05259547a277db12a1e9036bcf
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2019
ms.locfileid: "56650544"
---
# <a name="analyze-azure-cdn-usage-patterns"></a>Analyse des modèles d’utilisation CDN Azure

Après avoir activé le CDN pour votre application, vous pouvez surveiller l’utilisation du CDN, vérifier l’intégrité de votre application et corriger les éventuels problèmes. Azure CDN fournit ces fonctionnalités sous les formes suivantes : 

## <a name="core-analytics-via-azure-diagnostic-logs"></a>Analytique principale via les journaux de diagnostic Azure

L’analytique principale est disponible pour les points de terminaison CDN à tous les niveaux tarifaires. Les journaux de diagnostic Azure autoriser analytique core à exporter vers le stockage Azure, concentrateurs d’événements ou journaux Azure Monitor. Journaux d’Azure Monitor offre une solution avec des graphiques configurables par l’utilisateur et personnalisables. Pour plus d’informations sur les journaux de diagnostic Azure, consultez [Journaux de diagnostic Azure](cdn-azure-diagnostic-logs.md).

## <a name="verizon-core-reports"></a>Rapports principaux de Verizon

En tant qu’utilisateur d’Azure CDN avec un profil **CDN Azure Standard fourni par Verizon** ou **CDN Azure Premium fourni par Verizon**, vous pouvez afficher les rapports principaux de Verizon dans le portail supplémentaire Verizon. Les rapports principaux de Verizon sont accessibles par le biais de l’option **Gérer** du portail Azure. Ils offrent toute une variété de graphiques et de vues. Pour plus d’informations, consultez [Rapports principaux de Verizon](cdn-analyze-usage-patterns.md).

## <a name="verizon-custom-reports"></a>Rapports personnalisés de Verizon

En tant qu’utilisateur d’Azure CDN avec un profil **CDN Azure Standard fourni par Verizon** ou **CDN Azure Premium fourni par Verizon**, vous pouvez afficher les rapports personnalisés de Verizon dans le portail supplémentaire Verizon. Les rapports personnalisés de Verizon sont accessibles par le biais de l’option **Gérer** du portail Azure. La page des rapports personnalisés de Verizon indique le nombre d’accès ou les données transférées pour chaque CName de périphérie appartenant à un profil Azure CDN. Les données peuvent être groupées par code de réponse HTTP ou état du cache sur n’importe quelle période. Pour plus d’informations, consultez [Rapports personnalisés de Verizon](cdn-verizon-custom-reports.md).

## <a name="azure-cdn-premium-from-verizon-reports"></a>Rapports CDN Azure Premium fourni par Verizon

Avec **Azure CDN Premium de Verizon**, vous pouvez également accéder aux rapports suivants :
   * [Rapports HTTP avancés](cdn-advanced-http-reports.md)
   * [Statistiques en temps réel](cdn-real-time-stats.md)
   * [Performances de nœuds Edge](cdn-edge-performance.md)

