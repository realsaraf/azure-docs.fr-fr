---
title: Sortie d’Azure Stream Analytics dans Azure SQL Database
description: Découvrez comment charger des données de sortie d’Azure Stream Analytics dans SQL Azure et comment améliorer les débits d’écriture.
services: stream-analytics
author: chetanmsft
ms.author: chetanmsft
manager: katiiceva
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 3/18/2019
ms.openlocfilehash: d259fd5fc8c60837c6b6110eb751360227d70836
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58338426"
---
# <a name="azure-stream-analytics-output-to-azure-sql-database"></a>Sortie d’Azure Stream Analytics dans Azure SQL Database

Cet article donne des conseils pour améliorer les performances de débit d’écriture quand vous chargez des données dans SQL Azure Database à partir d’Azure Stream Analytics.

La sortie SQL dans Azure Stream Analytics prend en charge l’option d’écriture en parallèle. Cette option permet les topologies de travaux [massivement parallèles](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs), où plusieurs partitions de sortie sont écrites en parallèle dans la table de destination. Toutefois, l’activation de cette option dans Azure Stream Analytics ne permet pas toujours d’observer une hausse du débit, car le résultat dépend beaucoup de la configuration et du schéma de table de votre base de données SQL Azure. Les index, la clé de clustering, le facteur de remplissage d’index et la compression que vous choisissez ont un impact sur le temps de chargement des tables. Pour plus d’informations sur les moyens d’optimiser votre base de données SQL Azure afin d’améliorer les performances des requêtes et du chargement sur la base de tests d’évaluation internes, consultez les recommandations fournies dans [Optimisation des performances dans Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-performance-guidance). La réorganisation des écritures n’est pas garantie lors de l’écriture en parallèle dans la base de données SQL Azure.

Les configurations présentées ci-après pour chaque service peuvent vous aider à améliorer le débit global de votre solution.

## <a name="azure-stream-analytics"></a>Azure Stream Analytics

- **Héritage du partitionnement** – Cette option de configuration de la sortie SQL permet d’hériter le schéma de partitionnement de l’entrée ou l’étape de requête précédente. L’activation de cette option contribue à améliorer les débits lors de l’écriture dans une table sur disque et l’utilisation d’une topologie de travail [massivement parallèle](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs). Ce partitionnement est déjà automatiquement activé pour beaucoup d’autres [sorties](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#partitions-in-sources-and-sinks). Le verrouillage de table (TABLOCK) est également désactivé pour les insertions en bloc effectuées avec cette option.

> [!NOTE] 
> Quand il y a plus de huit partitions d’entrée, l’héritage du schéma de partitionnement d’entrée n’est pas toujours une option appropriée. Cette limite supérieure a été observée sur une table contenant une seule colonne d’identité et un index cluster. Les résultats observés peuvent varier en fonction du schéma et des index choisis.

- **Taille de lot** – Avec cette option de configuration de la sortie SQL, vous pouvez spécifier la taille de lot maximale dans une sortie SQL d’Azure Stream Analytics en fonction de la nature de votre table de destination/charge de travail. La taille de lot correspond au nombre maximal d’enregistrements qui sont envoyés avec chaque opération d’insertion en bloc. Dans les index cluster columnstore, une taille de lot d’environ [100 000](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-data-loading-guidance) permet d’optimiser la parallélisation, la journalisation minimale et le verrouillage. Dans les tables sur disque, une taille de 10 000 (valeur par défaut) ou moins peut être optimale pour votre solution, car des tailles de lot plus élevées risquent de déclencher une escalade de verrous durant les insertions en bloc.

- **Paramétrage des messages d’entrée** – Si vous avez déjà optimisé les performances à l’aide de l’héritage du partitionnement et de la taille de lot, vous pouvez en plus augmenter le nombre d’événements d’entrée par message dans chaque partition pour améliorer encore le débit d’écriture. Le paramétrage des messages d’entrée permet d’augmenter les tailles de lot dans Azure Stream Analytics jusqu’à la taille de lot spécifié, ce qui améliore le débit. Cela peut être obtenue à l’aide de [compression](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-define-inputs) ou en augmentant la taille des messages d’entrée dans les objets Blob ou EventHub.

## <a name="sql-azure"></a>SQL Azure

- **Table et index partitionnés** – L’utilisation d’une table SQL [partitionnée](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes?view=sql-server-2017) et d’index partitionnés sur la table ayant la même colonne que votre clé de partition (par exemple, PartitionId) peut réduire considérablement les conflits entre partitions durant les opérations d’écriture. Pour utiliser une table partitionnée, vous devez créer une [fonction de partition](https://docs.microsoft.com/sql/t-sql/statements/create-partition-function-transact-sql?view=sql-server-2017) et un [schéma de partition](https://docs.microsoft.com/sql/t-sql/statements/create-partition-scheme-transact-sql?view=sql-server-2017) dans le groupe de fichiers PRIMARY. Cela améliorera également la disponibilité des données existantes quand de nouvelles données sont chargées. La limite des E/S de journal peut être atteinte en fonction du nombre de partitions, mais vous pouvez l’augmenter en mettant à niveau la référence SKU.

- **Éviter les violations de clé unique** – Si vous obtenez [plusieurs messages d’avertissement de violation de clé](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-common-troubleshooting-issues#handle-duplicate-records-in-azure-sql-database-output) dans le journal d’activité Azure Stream Analytics, assurez-vous que votre travail n’est pas impacté par des violations de contrainte unique qui sont susceptibles de se produire dans des scénarios de reprise d’activité. Pour éviter ce problème, définissez l’option [IGNORE\_DUP\_KEY](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-common-troubleshooting-issues#handle-duplicate-records-in-azure-sql-database-output) sur vos index.

## <a name="azure-data-factory-and-in-memory-tables"></a>Azure Data Factory et tables en mémoire

- **Table en mémoire en tant que table temporaire** – [tables en mémoire](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) autoriser pour les chargements de données très haut débit, mais les données doivent tenir dans la mémoire. Les tests d’évaluation montrent que le chargement en masse d’une table en mémoire vers une table sur disque est environ dix fois plus rapide que l’insertion en bloc directe à l’aide d’un seul writer dans la table sur disque ayant une colonne d’identité et un index cluster. Pour améliorer ces performances d’insertion en bloc, créez un [travail de copie avec Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database) qui copie les données de la table en mémoire vers la table sur disque.

## <a name="avoiding-performance-pitfalls"></a>Éviter les pièges de performances
Insertion de données en bloc est beaucoup plus rapide que le chargement des données avec des insertions uniques, car la répétées frais de transfert de données, l’analyse de l’instruction insert, l’instruction en cours d’exécution et l’émission d’un enregistrement de transaction est évité. Au lieu de cela, un chemin d’accès plus efficace est utilisé dans le moteur de stockage pour le flux de données. Le coût d’installation de ce chemin d’accès est cependant beaucoup plus élevé que d’une seule instruction insert dans une table basée sur le disque. Le seuil de rentabilité est généralement environ 100 lignes, au-delà de quels en bloc le chargement est presque toujours plus efficace. 

Si le taux d’événements entrants est faible, il peut facilement créer des tailles de lot inférieure à 100 lignes, ce qui rend bulk insert inefficace et utilise trop d’espace disque. Pour contourner cette limitation, vous pouvez effectuer une des actions suivantes :
* Créer un INSTEAD OF [déclencheur](https://docs.microsoft.com/en-us/sql/t-sql/statements/create-trigger-transact-sql) à utiliser l’instruction insert simple pour chaque ligne.
* Utiliser une table temporaire en mémoire, comme décrit dans la section précédente.

Un autre scénario de ce type se produit lors de l’écriture dans un index columnstore non-cluster (NCCI), où les insertions en bloc plus petits peuvent créer trop de segments, qui peuvent se bloquer l’index. Dans ce cas, la recommandation consiste à utiliser un index Clustered Columnstore à la place.

## <a name="summary"></a>Résumé

En résumé, avec la fonctionnalité de sortie partitionnée dans Azure Stream Analytics pour la sortie SQL, la parallélisation alignée de votre travail avec une table partitionnée dans SQL Azure doit en principe vous apporter des améliorations de débit significatives. L’utilisation d’Azure Data Factory pour orchestrer le déplacement des données d’une table en mémoire vers des tables sur disque contribue aussi à améliorer le débit. Quand cela est possible, l’optimisation de la densité des messages peut également être un facteur majeur de l’amélioration du débit global.
