---
title: Créer des alertes de performances avec Azure Monitor pour conteneurs | Microsoft Docs
description: Cet article explique comment créer des alertes Azure personnalisées basées sur des requêtes de journal relatives à l’utilisation de la mémoire et de l’UC à partir d’Azure Monitor pour conteneurs.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/26/2019
ms.author: magoedte
ms.openlocfilehash: e6fdb0d57a44578647c1f16dc76c557296f20ddb
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2019
ms.locfileid: "56886764"
---
# <a name="how-to-set-up-alerts-for-performance-problems-in-azure-monitor-for-containers"></a>Comment configurer des alertes pour les problèmes de performances dans Azure Monitor pour les conteneurs
Azure Monitor pour les conteneurs supervise les performances des charges de travail de conteneur déployées sur Azure Container Instances ou sur des clusters Kubernetes managés hébergés sur Azure Kubernetes Service (AKS). 

Cet article décrit comment activer la génération d’alertes pour les situations suivantes :

* Lors de la mémoire utilisation du processeur et sur les nœuds du cluster ou dépasse le seuil défini.
* Lorsque l’utilisation du processeur ou mémoire sur les conteneurs au sein d’un contrôleur de dépasse le seuil défini par rapport à la limite définie sur la ressource correspondante.

Pour avertir lors de l’utilisation du processeur ou mémoire est élevé pour un cluster ou un contrôleur, vous créez une règle d’alerte mesure métrique qui repose sur les requêtes de journal fournis. Les requêtes comparer une valeur datetime jusqu'à présent à l’aide de l’opérateur maintenant et revient à une heure. Toutes les dates stockées par Azure Monitor pour les conteneurs sont au format UTC.

Avant de commencer, si vous n’êtes pas familiarisé avec les alertes dans Azure Monitor, consultez [Vue d’ensemble des alertes dans Microsoft Azure](../platform/alerts-overview.md). Pour en savoir plus sur les alertes utilisant des requêtes de journal, voir [Alertes de journal dans Azure Monitor](../platform/alerts-unified-log.md).

## <a name="resource-utilization-log-search-queries"></a>Requêtes de recherche de journal l’utilisation des ressources
Les requêtes dans cette section sont fournis pour prendre en charge chaque scénario d’alerte. Les requêtes sont requis pour l’étape 7 sous la [créer alerte](#create-alert-rule) section ci-dessous.  

La requête suivante calcule l’utilisation moyenne de l’UC en tant que moyenne de l’utilisation par l’UC de nœuds membre à chaque minute.  

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'cpuCapacityNanoCores';
let usageCounterName = 'cpuUsageNanoCores';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
// cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
| distinct ClusterName, Computer
| join hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime
  | where TimeGenerated >= startDateTime
  | where ObjectName == 'K8SNode'
  | where CounterName == capacityCounterName
  | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
  | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer
| join kind=inner hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime + trendBinSize
  | where TimeGenerated >= startDateTime - trendBinSize
  | where ObjectName == 'K8SNode'
  | where CounterName == usageCounterName
  | project Computer, UsageValue = CounterValue, TimeGenerated
) on Computer
| where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
| project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
```

La requête suivante calcule l’utilisation moyenne de la mémoire en tant que moyenne de l’utilisation par la mémoire de nœuds membre à chaque minute.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'memoryCapacityBytes';
let usageCounterName = 'memoryRssBytes';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
// cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
| distinct ClusterName, Computer
| join hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime
  | where TimeGenerated >= startDateTime
  | where ObjectName == 'K8SNode'
  | where CounterName == capacityCounterName
  | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
  | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer
| join kind=inner hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime + trendBinSize
  | where TimeGenerated >= startDateTime - trendBinSize
  | where ObjectName == 'K8SNode'
  | where CounterName == usageCounterName
  | project Computer, UsageValue = CounterValue, TimeGenerated
) on Computer
| where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
| project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
```
>[!IMPORTANT]
>Requêtes ci-dessous contiennent des valeurs de chaîne d’espace réservé pour le nom de votre cluster et de contrôleur - < your-cluster-name > et < your-contrôleur-name >. Remplacez les espaces réservés par des valeurs spécifiques à votre environnement avant de définir des alertes. 


La requête suivante calcule l’utilisation moyenne du processeur de tous les conteneurs dans un contrôleur comme une moyenne de l’utilisation du processeur de chaque instance de conteneur dans un contrôleur de chaque minute sous la forme d’un pourcentage de la limite configurée pour un conteneur.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'cpuLimitNanoCores';
let usageCounterName = 'cpuUsageNanoCores';
let clusterName = '<your-cluster-name>';
let controllerName = '<your-controller-name>';
KubePodInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where ClusterName == clusterName
| where ControllerName == controllerName
| extend InstanceName = strcat(ClusterId, '/', ContainerName),
         ContainerName = strcat(controllerName, '/', tostring(split(ContainerName, '/')[1]))
| distinct Computer, InstanceName, ContainerName
| join hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ObjectName == 'K8SContainer'
    | where CounterName == capacityCounterName
    | summarize LimitValue = max(CounterValue) by Computer, InstanceName, bin(TimeGenerated, trendBinSize)
    | project Computer, InstanceName, LimitStartTime = TimeGenerated, LimitEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer, InstanceName
| join kind=inner hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime + trendBinSize
    | where TimeGenerated >= startDateTime - trendBinSize
    | where ObjectName == 'K8SContainer'
    | where CounterName == usageCounterName
    | project Computer, InstanceName, UsageValue = CounterValue, TimeGenerated
) on Computer, InstanceName
| where TimeGenerated >= LimitStartTime and TimeGenerated < LimitEndTime
| project Computer, ContainerName, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize) , ContainerName
```

La requête suivante calcule l’utilisation moyenne de la mémoire de tous les conteneurs dans un contrôleur en moyenne d’utilisation de la mémoire de chaque instance de conteneur dans un contrôleur de chaque minute sous la forme d’un pourcentage de la limite configurée pour un conteneur.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'memoryLimitBytes';
let usageCounterName = 'memoryRssBytes';
let clusterName = '<your-cluster-name>';
let controllerName = '<your-controller-name>';
KubePodInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where ClusterName == clusterName
| where ControllerName == controllerName
| extend InstanceName = strcat(ClusterId, '/', ContainerName),
         ContainerName = strcat(controllerName, '/', tostring(split(ContainerName, '/')[1]))
| distinct Computer, InstanceName, ContainerName
| join hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ObjectName == 'K8SContainer'
    | where CounterName == capacityCounterName
    | summarize LimitValue = max(CounterValue) by Computer, InstanceName, bin(TimeGenerated, trendBinSize)
    | project Computer, InstanceName, LimitStartTime = TimeGenerated, LimitEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer, InstanceName
| join kind=inner hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime + trendBinSize
    | where TimeGenerated >= startDateTime - trendBinSize
    | where ObjectName == 'K8SContainer'
    | where CounterName == usageCounterName
    | project Computer, InstanceName, UsageValue = CounterValue, TimeGenerated
) on Computer, InstanceName
| where TimeGenerated >= LimitStartTime and TimeGenerated < LimitEndTime
| project Computer, ContainerName, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize) , ContainerName
```

## <a name="create-alert-rule"></a>Créer une règle d’alerte
Procédez comme suit pour créer une alerte de journal dans Azure Monitor à l’aide de l’une des règles de recherche de journal fournis précédemment.  

>[!NOTE]
>La procédure ci-dessous vous oblige à passer à nouveau les API d’alertes journal décrit dans [préférence de basculer les API pour les alertes de journal](../platform/alerts-log-api-switch.md) si vous créez une règle d’alerte pour l’utilisation des ressources de conteneur. 
>

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Dans le volet de gauche du portail Azure, sélectionnez **Moniteur**. Sous la section **Insights**, sélectionnez **Conteneurs**.    
3. Sous l’onglet **Clusters supervisés**, sélectionnez un cluster dans la liste en cliquant sur le nom du cluster.
4. Dans le volet de gauche, dans la section **Supervision**, sélectionnez **Journaux** pour ouvrir la page de journaux d’Azure Monitor, qui est utilisée pour écrire et exécuter des requêtes Azure Log Analytics.
5. Dans la page **Journaux**, cliquez sur **+ Nouvelle règle d’alerte**.
6. Dans la section **Condition**, cliquez sur la condition de journal personnalisée prédéfinie **Chaque fois que la recherche dans les journaux est <logic undefined>**. Le type de signal **Recherche dans les journaux personnalisée** est automatiquement sélectionné pour nous parce que nous avons commencé à créer une règle d’alerte directement à partir de la page de journaux d’Azure Monitor.  
7. Collez un de le [requêtes](#resource-utilization-log-search-queries) fourni précédemment dans le **requête de recherche** champ. 

8. Configurez l’alerte avec les informations suivantes :

    a. Dans la liste déroulante **Basé sur**, sélectionnez **Mesure des métriques**. Une mesure des métriques créera une alerte pour chaque objet de la requête dont la valeur dépasse notre seuil spécifié.  
    b. Pour **Condition**, sélectionnez **Supérieur à**, puis entrez **75** comme **Seuil** de ligne de base initiale, ou entrez une valeur correspondant à vos critères.  
    c. Dans la section **Déclencher l’alerte selon**, sélectionnez **Violations consécutives**, puis, dans la liste déroulante, sélectionnez **Supérieur à** et entrez la valeur **2**.  
    d. Si vous configurez une alerte pour l’UC du conteneur ou l’utilisation de la mémoire, sous **agréger sur** sélectionnez **ContainerName** dans la liste déroulante.  
    e. Dans la section **Évaluées sur la base de**, modifiez la valeur de **Période** en la définissant sur 60 minutes. La règle s’exécute toutes les cinq minutes et renvoie les enregistrements qui ont été créés au cours de la dernière heure. Paramétrer la période de temps sur une durée plus longue compense la latence potentielle des données et garantit que la requête retourne des données pour éviter un faux positif où l’alerte ne se déclenche jamais. 

9. Cliquez sur **Terminé** pour terminer la règle d’alerte.
10. Fournissez un nom pour votre alerte dans le champ **Nom de la règle d’alerte**. Entrez une **Description** détaillant les spécificités de l’alerte, puis sélectionnez une gravité appropriée parmi les options fournies.
11. Pour activer immédiatement la règle d’alerte lors de la création, acceptez la valeur par défaut pour l’option **Activer la règle lors de sa création**.
12. Pour l’étape finale, vous spécifiez un **Groupe d’actions** existant ou en créez un, ce qui garantit que les mêmes actions sont entreprises chaque fois qu’une alerte est déclenchée et peuvent être utilisées pour chaque règle que vous définissez. Configurez en fonction de la façon dont vos opérations informatiques ou de DevOps gèrent les incidents. 
13. Cliquez sur **Créer une règle d’alerte** pour terminer la règle d’alerte. Son exécution démarre immédiatement.

## <a name="next-steps"></a>Étapes suivantes

* Examinez certains [exemples de requête de journal](container-insights-analyze.md#search-logs-to-analyze-data) pour en savoir plus sur les requêtes prédéfinies, ou des exemples à évaluer et à utiliser ou personnaliser pour d’autres scénarios d’alerte. 
* Pour continuer à apprendre à utiliser Azure Monitor et superviser d’autres aspects de votre cluster AKS, voir [Afficher l’intégrité d’Azure Kubernetes Service](container-insights-analyze.md).
