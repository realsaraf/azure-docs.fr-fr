---
title: Dépannage d’Azure Monitor for containers | Microsoft Docs
description: Cet article vous permet de dépanner Azure Monitor for containers et de résoudre certains problèmes.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/30/2018
ms.author: magoedte
ms.openlocfilehash: abf833cc054bfac0581506f75259e357f0ab1b38
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2019
ms.locfileid: "56985748"
---
# <a name="troubleshooting-azure-monitor-for-containers"></a>Dépannage d’Azure Monitor for containers

Lorsque vous configurez la surveillance de votre cluster Azure Kubernetes Service (AKS) avec Azure Monitor for containers, il se peut que vous rencontriez un problème empêchant la collecte de données ou la récupération de l’état. Cet article décrit en détail certains problèmes courants et indique la façon de les résoudre.

## <a name="azure-monitor-for-containers-is-enabled-but-not-reporting-any-information"></a>Azure Monitor for containers est activé, mais aucune information n’est renvoyée
Si Azure Monitor pour les conteneurs est correctement configurée et activée, mais vous ne pouvez pas afficher les informations d’état ou sans résultats sont retournés à partir d’une requête de journal, vous diagnostiquez le problème en procédant comme suit : 

1. Vérifiez l’état de l’agent en exécutant la commande : 

    `kubectl get ds omsagent --namespace=kube-system`

    La sortie doit ressembler à la suivante, qui indique que l’agent a été correctement déployé :

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```  
2. Vérifiez l’état du déploiement avec la version de l’agent *06072018* (ou ultérieure) à l’aide de cette commande :

    `kubectl get deployment omsagent-rs -n=kube-system`

    La sortie doit ressembler à l’exemple suivant, qui indique que l’agent a été correctement déployé :

    ```
    User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
    NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
    omsagent   1         1         1            1            3h
    ```

3. Vérifiez l’état du pod pour confirmer qu’il est bien en cours d’exécution à l’aide de la commande : `kubectl get pods --namespace=kube-system`

    La sortie doit ressembler à l’exemple suivant, avec l’état *En cours d’exécution* pour l’agent OMS :

    ```
    User@aksuser:~$ kubectl get pods --namespace=kube-system 
    NAME                                READY     STATUS    RESTARTS   AGE 
    aks-ssh-139866255-5n7k5             1/1       Running   0          8d 
    azure-vote-back-4149398501-7skz0    1/1       Running   0          22d 
    azure-vote-front-3826909965-30n62   1/1       Running   0          22d 
    omsagent-484hw                      1/1       Running   0          1d 
    omsagent-fkq7g                      1/1       Running   0          1d 
    ```

4. Vérifiez les journaux de l’agent. Quand l’agent conteneurisé est déployé, il effectue une vérification rapide en exécutant les commandes OMI, puis indique la version de l’agent et du fournisseur. 

5. Pour vérifier que l’agent a bien été intégré, exécutez la commande : `kubectl logs omsagent-484hw --namespace=kube-system`

    Le statut doit ressembler à l’exemple suivant :

    ```
    User@aksuser:~$ kubectl logs omsagent-484hw --namespace=kube-system
    :
    :
    instance of Container_HostInventory
    {
        [Key] InstanceID=3a4407a5-d840-4c59-b2f0-8d42e07298c2
        Computer=aks-nodepool1-39773055-0
        DockerVersion=1.13.1
        OperatingSystem=Ubuntu 16.04.3 LTS
        Volume=local
        Network=bridge host macvlan null overlay
        NodeRole=Not Orchestrated
        OrchestratorType=Kubernetes
    }
    Primary Workspace: b438b4f6-912a-46d5-9cb1-b44069212abc    Status: Onboarded(OMSAgent Running)
    omi 1.4.2.2
    omsagent 1.6.0.23
    docker-cimprov 1.0.0.31
    ```

## <a name="error-messages"></a>messages d'erreur

Le tableau ci-dessous récapitule les erreurs connues que vous pouvez rencontrer lors de l’utilisation d’Azure Monitor pour conteneurs.

| messages d'erreur  | Action |  
| ---- | --- |  
| Message d’erreur `No data for selected filters`  | L’établissement de la supervision du flux de données pour les nouveaux clusters peut prendre un certain temps. Patientez au moins 10 à 15 minutes avant l’affichage des données de votre cluster. |   
| Message d’erreur `Error retrieving data` | Bien que le cluster Azure Kubernetes Service soit configuré pour la supervision de l’intégrité et des performances, une connexion est établie entre le cluster et l’espace de travail Azure Log Analytics. Un espace de travail Log Analytics est utilisé pour stocker toutes les données de supervision de votre cluster. Cette erreur peut se produire lorsque votre espace de travail Log Analytics a été supprimé ou perdu. Vérifiez si votre espace de travail est disponible en consultant [Gérer l’accès](../../azure-monitor/platform/manage-access.md?toc=/azure/azure-monitor/toc.json#view-workspace-details). Si l’espace de travail est manquant, vous devez réintégrer votre cluster à Azure Monitor pour conteneurs. Pour réintégrer, vous devez [désactiver](container-insights-optout.md) la supervision du cluster et [réactiver](container-insights-onboard.md?toc=%2fazure%2fmonitoring%2ftoc.json#enable-monitoring-for-a-new-cluster) Azure Monitor pour conteneurs. |  
| `Error retrieving data` après l’ajout d’Azure Monitor pour conteneurs à l’aide de la commande az aks cli | Lors de l’intégration à l’aide de `az aks cli`, Azure Monitor pour conteneurs peut, dans de très rares cas, ne pas être correctement intégré. Vérifiez si la solution est intégrée. Pour cela, accédez à votre espace de travail Log Analytics et voyez si la solution est disponible en sélectionnant **Solutions** dans le volet de gauche. Pour résoudre ce problème, vous devez redéployer la solution en suivant les instructions de [déploiement d’Azure Monitor pour conteneurs](container-insights-onboard.md?toc=%2fazure%2fmonitoring%2ftoc.json) |  

Pour vous aider à diagnostiquer le problème, nous vous fournissons un script de résolution des problèmes disponible [ici](https://github.com/Microsoft/OMS-docker/tree/ci_feature_prod/Troubleshoot#troubleshooting-script).  

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez activer la fonctionnalité de supervision pour collecter des métriques d’intégrité pour les nœuds et pods du cluster AKS et les consulter dans le Portail Azure. Pour savoir comment utiliser Azure Monitor pour les conteneurs, consultez l’article [Connaître l’état d’Azure Kubernetes Service](container-insights-analyze.md).