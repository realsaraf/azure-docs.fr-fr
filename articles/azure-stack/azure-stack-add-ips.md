---
title: Ajouter des adresses IP publiques dans Azure Stack | Microsoft Docs
description: Apprenez à ajouter plus d’adresses IP publiques dans Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2019
ms.author: jeffgilb
ms.reviewer: scottnap
ms.lastreviewed: 02/28/2019
ms.openlocfilehash: 09805719262f0a1d30f3b38af4b5209667d25e5a
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/01/2019
ms.locfileid: "57195368"
---
# <a name="add-public-ip-addresses"></a>Ajouter des adresses IP publiques
*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*  

Apprenez à ajouter plus d’adresses IP publiques dans Azure Stack.  Dans cet article, nous parlons d’adresses IP publiques pour les adresses externes, mais dans Azure Stack, il faut comprendre ajouter des blocs d’adresses IP à votre réseau externe.  Que ce réseau externe soit routable sur l’Internet public ou sur un intranet et utilise un espace d’adresses n’est pas important dans cet article.  La procédure est la même. 

## <a name="add-a-public-ip-address-pool"></a>Ajouter un pool d’adresses IP publiques
Vous pouvez ajouter des adresses IP publiques à votre système Azure Stack n’importe quand après le déploiement initial dudit système. Découvrez comment [Afficher la consommation d’adresses IP publiques](azure-stack-viewing-public-ip-address-consumption.md) pour voir l’utilisation actuelle et la disponibilité d’adresses IP publiques sur Azure Stack.

À un niveau élevé, le processus d’ajout d’un nouveau bloc d’adresses IP publiques dans Azure Stack ressemble à ceci :

 ![Ajouter des flux IP](media/azure-stack-add-ips/flow.PNG)

## <a name="obtain-the-address-block-from-your-provider"></a>Obtenir le bloc d’adresses de votre fournisseur
Vous devez en premier lieu obtenir le bloc d’adresses que vous souhaitez ajouter à Azure Stack.  En fonction d’où vous obtenez votre bloc d’adresses, vous devez réfléchir au délai et le comparer à votre taux de consommation des adresses IP publiques dans Azure Stack.  

> [!IMPORTANT]
> Azure Stack accepte n’importe quel bloc d’adresses que vous fournissez, tant qu’il s’agit d’un bloc valide qui ne chevauche pas une plage d’adresses existante dans Azure Stack.  Veillez à obtenir un bloc d’adresses valide routable et ne chevauchant pas le réseau externe auquel Azure Stack est connecté.  Une fois que vous avez ajouté la plage à Azure Stack, vous ne pouvez plus la retirer.

## <a name="add-the-ip-address-range-to-azure-stack"></a>Ajouter la plage d’adresses IP à Azure Stack

1. Dans un navigateur Internet, accédez au tableau de bord du portail administrateur.  Pour cet exemple, nous allons utiliser https://adminportal.local.azurestack.external.  
2.  Connectez-vous au portail d’administration Azure Stack en tant qu’opérateur cloud.
3.  Dans le tableau de bord par défaut, recherchez la liste Gestion des régions et sélectionnez la région à gérer, par exemple, local.
4.  Trouvez la vignette Fournisseurs de ressources et cliquez sur Fournisseur de ressources réseau.
5.  Cliquez sur la vignette Utilisation des pools IP publics.
6.  Cliquez sur le bouton Ajouter un pool IP.
7.  Donnez un nom au pool IP.  Le nom que vous choisissez vous permet d’identifier facilement le pool IP. Vous pouvez donc lui donner le nom que vous voulez.  Il est conseillé de donner le même nom que la plage d’adresses, mais cela n’est pas obligatoire.
8.   Entrez le bloc d’adresses que vous voulez ajouter dans la notation CIDR.  Par exemple :  192.168.203.0/24
9.  Lorsque vous fournissez une plage CIDR valide dans le champ Plage d’adresses (bloc CIDR), les champs Adresse IP de début, Adresse IP de fin et Adresses IP disponibles sont automatiquement renseignés.  Ils sont en lecture seule et générés automatiquement. Vous ne pouvez donc pas les modifier sans modifier la valeur dans le champ Plage d’adresses.
10. Après avoir vérifié les informations sur le panneau et confirmé que tout est correct, cliquez sur OK pour valider les modifications et ajouter la plage d’adresses à Azure Stack.


## <a name="next-steps"></a>Étapes suivantes 
[Examiner les actions de nœud d’unité d’échelle](azure-stack-node-actions.md) 
