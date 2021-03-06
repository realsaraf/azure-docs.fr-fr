---
title: JavaScript et versions des contrats de pages pour les flux d’utilisateurs dans Azure Active Directory B2C | Microsoft Docs
description: Découvrez comment activer JavaScript et utiliser des versions de contrats de pages pour personnaliser un flux d’utilisateurs dans Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 5102755c9e830f43fa92e8546e5125960e0a2f9a
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/25/2019
ms.locfileid: "58401557"
---
# <a name="about-using-javascript-and-page-contract-versions-in-a-user-flow"></a>Informations sur JavaScript et les versions des contrats de pages dans un flux d’utilisateurs

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Azure AD B2C fournit un ensemble de contenu packagé (HTML, CSS et JavaScript) pour les éléments d’interface utilisateur des flux d’utilisateurs. Si vous prévoyez d’activer du code [JavaScript](javascript-samples.md) côté client dans vos flux d’utilisateurs, vérifiez que les éléments sur lesquels il s’appuie sont immuables. Dans le cas contraire, la moindre modification pourrait provoquer un comportement inattendu sur vos pages de flux d’utilisateurs. Pour éviter ces problèmes, vous pouvez imposer l’utilisation d’un contrat de pages pour un flux d’utilisateurs et spécifier une version du contrat de pages. Toutes les définitions de contenu sur lesquelles se basent votre code JavaScript seront ainsi immuables. Même si vous ne souhaitez pas activer JavaScript pour un flux d’utilisateurs, vous pouvez spécifier une version du contrat pour vos pages de flux d’utilisateurs.

> [!NOTE]
> Cet article traite de JavaScript pour les flux d’utilisateurs, mais vous pouvez également utiliser JavaScript et sélectionner des versions de contrats de pages avec des [stratégies personnalisées](page-contract.md).

## <a name="enable-javascript"></a>Activer JavaScript

Dans les propriétés du flux d’utilisateurs, vous pouvez activer JavaScript, ce qui impose également l’utilisation d’un contrat de pages. Vous pouvez alors définir la version du contrat de pages (voir la section suivante).

![Paramètre Activer JavaScript](media/user-flow-javascript-overview/javascript-settings.PNG)

## <a name="specify-a-page-contract-version"></a>Spécifier une version du contrat de pages

Que vous activiez ou non JavaScript dans les propriétés de votre flux d’utilisateurs, vous pouvez spécifier une version du contrat pour vos pages de flux d’utilisateurs. Ouvrez le flux d’utilisateur et sélectionnez **Mises en page**. Sous **Nom de la mise en page**, sélectionner une page de flux d’utilisateurs et choisissez **Version du contrat de pages**.

![Paramètre Activer JavaScript](media/user-flow-javascript-overview/page-contract-version.PNG)

## <a name="next-steps"></a>Étapes suivantes
Voir [Exemples JavaScript dans Azure Active Directory B2C](javascript-samples.md).
