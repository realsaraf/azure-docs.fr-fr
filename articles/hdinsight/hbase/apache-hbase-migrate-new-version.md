---
title: Migrer un cluster HBase vers une nouvelle version - Azure HDInsight
description: Comment migrer des clusters HBase vers une nouvelle version.
services: hdinsight
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: ashishth
ms.openlocfilehash: 3b27fe0bec4ec23739e3cff02d6aed667f1d3e1d
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58226825"
---
# <a name="migrate-an-apache-hbase-cluster-to-a-new-version"></a>Effectuer la migration d’un cluster Apache HBase vers une nouvelle version

Les clusters basés sur une tâche, tels que les clusters [Apache Spark](https://spark.apache.org/) et [Apache Hadoop](https://hadoop.apache.org/), sont faciles à mettre à niveau. Consultez [Mettre à niveau le cluster HDInsight](../hdinsight-upgrade-cluster.md) :

1. Sauvegardez les données temporaires (stockées localement).
2. Supprimez le cluster existant.
3. Créez un cluster dans le même sous-réseau de réseau virtuel.
4. Importez les données temporaires.
5. Démarrez des travaux et poursuivez le traitement sur le nouveau cluster.

Pour mettre à niveau un cluster [Apache HBase](https://hbase.apache.org/), quelques étapes supplémentaires sont nécessaires, comme décrit dans cet article.

> [!NOTE]  
> Le temps d’arrêt lors de la mise à niveau doit être minime, de l’ordre de quelques minutes. Ce temps d’arrêt est dû à la procédure pour vider toutes les données en mémoire, puis au temps nécessaire à la configuration et au redémarrage des services sur le nouveau cluster. Vos résultats peuvent varier en fonction du nombre de nœuds, de la quantité de données et d’autres variables.

## <a name="review-apache-hbase-compatibility"></a>Vérifier la compatibilité d’Apache HBase

Avant de mettre à niveau Apache HBase, vérifiez que la version de HBase présente sur le cluster source est compatible avec celle du cluster de destination. Pour plus d’informations, consultez [Composants et versions d’Apache Hadoop disponibles avec HDInsight](../hdinsight-component-versioning.md).

> [!NOTE]  
> Nous vous recommandons vivement d’examiner la matrice de compatibilité de versions dans le [guide sur HBase](https://hbase.apache.org/book.html#upgrading).

Voici un exemple de matrice de compatibilité de versions, où O indique la compatibilité et N signale une incompatibilité potentielle :

| Type de compatibilité | Version principale| Version secondaire | Correctif |
| --- | --- | --- | --- |
| Compatibilité communication client-serveur | N | O | O |
| Compatibilité serveur-serveur | N | O | O |
| Compatibilité format de fichier | N | O | O |
| Compatibilité API client | N | O | O |
| Compatibilité fichier binaire client | N | N | O |
| **Compatibilité API limitée côté serveur** |  |  |  |
| Stable | N | O | O |
| En cours d’évolution | N | N | O |
| Instable | N | N | N |
| Compatibilité dépendance | N | O | O |
| Compatibilité opérationnelle | N | N | O |

> [!NOTE]  
> Les dernières incompatibilités doivent être décrites dans les notes de publication de version HBase.

## <a name="upgrade-with-same-apache-hbase-major-version"></a>Mettre à niveau vers la même version principale d’Apache HBase

Le scénario suivant est destiné à la mise à niveau de HDInsight 3.4 vers 3.6 (les deux sont fournis avec Apache HBase 1.1.2) avec la même version principale HBase. D’autres mises à niveau de version sont similaires, tant qu’il n’y a pas de problème de compatibilité entre les versions sources et de destination.

1. Vérifiez que votre application est compatible avec la nouvelle version, comme indiqué dans les notes de version et la matrice de compatibilité HBase. Testez votre application dans un cluster exécutant la version cible de HDInsight et HBase.

2. [Configurez un nouveau cluster HDInsight de destination](../hdinsight-hadoop-provision-linux-clusters.md) avec le même compte de stockage, mais avec un nom de conteneur différent :

    ![Utiliser le même compte de stockage, mais créer un autre conteneur](./media/apache-hbase-migrate-new-version/same-storage-different-container.png)

3. Videz votre cluster HBase source. Il s’agit du cluster à partir duquel vous effectuez la mise à niveau. HBase écrit les données entrantes dans un magasin en mémoire, appelé _memstore_. Une fois que le memstore atteint une certaine taille, il est vidé sur le disque pour le stockage à long terme dans le compte de stockage du cluster. Lorsque vous supprimez l’ancien cluster, les memstores sont recyclés, ce qui entraîne potentiellement une perte de données. Pour vider manuellement le memstore de chaque table sur le disque, exécutez le script suivant. La version la plus récente de ce script se trouve sur [GitHub](https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/flush_all_tables.sh) Azure.

    ```bash
    #!/bin/bash
    
    #-------------------------------------------------------------------------------#
    # SCRIPT TO FLUSH ALL HBASE TABLES.
    #-------------------------------------------------------------------------------#
    
    LIST_OF_TABLES=/tmp/tables.txt
    HBASE_SCRIPT=/tmp/hbase_script.txt
    TARGET_HOST=$1
    
    usage ()
    {
        if [[ "$1" == "-h" ]] || [[ "$1" == "--help" ]]
        then
            cat << ...
    
    Usage: 
    
    $0 [hostname]
    
    Providing hostname is optional and not required when the script is executed within HDInsight cluster with access to 'hbase shell'.
    
    However hostname should be provided when executing the script as a script-action from HDInsight portal.
    
    For Example:
    
        1.  Executing script inside HDInsight cluster (where 'hbase shell' is 
            accessible):
    
            $0 
    
            [No need to provide hostname]
    
        2.  Executing script from HDinsight Azure portal:
    
            Provide Script URL.
    
            Provide hostname as a parameter (i.e. hn0, hn1 or wn2 etc.).
    ...
            exit
        fi
    }
    
    validate_machine ()
    {
        THIS_HOST=`hostname`
    
        if [[ ! -z "$TARGET_HOST" ]] && [[ $THIS_HOST  != $TARGET_HOST* ]]
        then
            echo "[INFO] This machine '$THIS_HOST' is not the right machine ($TARGET_HOST) to execute the script."
            exit 0
        fi
    }
    
    get_tables_list ()
    {
    hbase shell << ... > $LIST_OF_TABLES 2> /dev/null
        list
        exit
    ...
    }
    
    add_table_for_flush ()
    {
        TABLE_NAME=$1
        echo "[INFO] Adding table '$TABLE_NAME' to flush list..."
        cat << ... >> $HBASE_SCRIPT
            flush '$TABLE_NAME'
    ...
    }
    
    clean_up ()
    {
        rm -f $LIST_OF_TABLES
        rm -f $HBASE_SCRIPT
    }
    
    ########
    # MAIN #
    ########
    
    usage $1
    
    validate_machine
    
    clean_up
    
    get_tables_list
    
    START=false
    
    while read LINE 
    do 
        if [[ $LINE == TABLE ]] 
        then
            START=true
            continue
        elif [[ $LINE == *row*in*seconds ]]
        then
            break
        elif [[ $START == true ]]
        then
            add_table_for_flush $LINE
        fi
    
    done < $LIST_OF_TABLES
    
    cat $HBASE_SCRIPT
    
    hbase shell $HBASE_SCRIPT << ... 2> /dev/null
    exit
    ...
    
    ```
    
4. Arrêtez l’ingestion des données vers l’ancien cluster HBase.
5. Pour vous assurer que toutes les données récentes dans le memstore sont vidées, exécutez à nouveau le script précédent.
6. Connectez-vous à [Apache Ambari](https://ambari.apache.org/) sur l’ancien cluster (https://OLDCLUSTERNAME.azurehdidnsight.net) et interrompez les services HBase. Lorsque vous êtes invité à confirmer que vous souhaitez arrêter les services, cochez la case pour activer le mode de maintenance pour HBase. Pour plus d’informations sur la connexion à Ambari et sur l’utilisation d’Ambari, consultez [Gérer des clusters HDInsight à l’aide de l’interface utilisateur Web d’Ambari](../hdinsight-hadoop-manage-ambari.md).

    ![Dans Ambari, cliquer sur l’onglet Services, puis sur HBase dans le menu de gauche, puis sur Arrêter sous Service Actions (Actions du service)](./media/apache-hbase-migrate-new-version/stop-hbase-services.png)

    ![Cocher la case pour activer le mode de maintenance pour HBase, puis confirmer](./media/apache-hbase-migrate-new-version/turn-on-maintenance-mode.png)

7. Ouvrez une session Ambari sur le nouveau cluster HDInsight. Modifiez le paramètre HDFS `fs.defaultFS` pour pointer vers le nom du conteneur utilisé par le cluster d’origine. Ce paramètre se trouve sous **HDFS > Configurations > Avancé > Advanced core-site (Site principal avancé)**.

    ![Dans Ambari, cliquer sur l’onglet Services, puis sur HDFS dans le menu de gauche, puis sur l’onglet Configurations, puis sur l’onglet Avancé en dessous](./media/apache-hbase-migrate-new-version/hdfs-advanced-settings.png)

    ![Dans Ambari, modifier le nom du conteneur](./media/apache-hbase-migrate-new-version/change-container-name.png)

8. **Si vous n’utilisez pas de clusters HBase avec la fonctionnalité améliorée écrit, ignorez cette étape. Il est nécessaire uniquement pour les clusters HBase avec fonctionnalité améliorée écrit.**
   
   Modifier le chemin d’accès hbase.rootdir pour pointer vers le conteneur du cluster d’origine.

    ![Dans Ambari, modifiez le nom du conteneur pour hbase rootdir](./media/apache-hbase-migrate-new-version/change-container-name-for-hbase-rootdir.png)
    
9. Enregistrez vos modifications.
10. Redémarrez tous les services requis, comme indiqué par Ambari.
11. Pointez votre application vers le nouveau cluster.

    > [!NOTE]  
    > Le DNS statique de votre application est modifié lors de la mise à niveau. Au lieu de coder en dur ce DNS, vous pouvez configurer un CNAME dans les paramètres DNS de votre nom de domaine qui pointe vers le nom du cluster. Une autre option consiste à utiliser un fichier config pour votre application, que vous pouvez mettre à jour sans nouveau déploiement.

12. Lancez l’ingestion des données pour voir si tout fonctionne comme prévu.
13. Si le nouveau cluster vous convient, supprimez le cluster d’origine.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur [Apache HBase](https://hbase.apache.org/) et la mise à niveau de clusters HDInsight, consultez les articles suivants :

* [Mettre à niveau le cluster HDInsight](../hdinsight-upgrade-cluster.md)
* [Superviser et gérer Azure HDInsight à l’aide de l’interface utilisateur web d’Apache Ambari](../hdinsight-hadoop-manage-ambari.md)
* [Composants et versions d’Apache Hadoop](../hdinsight-component-versioning.md)
* [Optimiser les configurations à l’aide d’Apache Ambari](../hdinsight-changing-configs-via-ambari.md#apache-hbase-optimization-with-the-ambari-web-ui)
