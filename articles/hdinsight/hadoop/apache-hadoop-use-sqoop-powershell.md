---
title: Exécuter des tâches Apache Sqoop avec PowerShell et Azure HDInsight
description: Découvrez comment utiliser Azure PowerShell à partir d’un poste de travail pour exécuter des commandes Apache Sqoop import et export entre un cluster Apache Hadoop et une base de données SQL Azure.
ms.reviewer: jasonh
services: hdinsight
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: hrasheed
ms.openlocfilehash: 2d44db934832bed9a2339b38bc26c5bd42dfb042
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58359940"
---
# <a name="run-apache-sqoop-jobs-by-using-azure-powershell-for-apache-hadoop-in-hdinsight"></a>Exécuter des tâches Apache Sqoop avec Azure PowerShell pour Apache Hadoop dans HDInsight
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Découvrez comment utiliser Azure PowerShell pour exécuter des tâches Apache Sqoop dans Azure HDInsight permettant d’effectuer des importations/exportations entre un cluster HDInsight et une base de données SQL Azure ou SQL Server.

> [!NOTE]  
> Même si vous pouvez utiliser les procédures décrites dans cet article avec un cluster HDInsight Windows ou Linux, ces dernières fonctionnent uniquement à partir d’un client Windows. Pour choisir d’autres méthodes, utilisez le sélecteur d’onglet en haut de cet article. 

## <a name="prerequisites"></a>Conditions préalables 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

* Une station de travail sur laquelle est installé Azure PowerShell.
* Un cluster Apache Hadoop dans HDInsight. Pour plus d’informations, consultez [Créer un cluster et une base de données SQL](hdinsight-use-sqoop.md#create-cluster-and-sql-database).

## <a name="run-apache-sqoop-by-using-powershell"></a>Exécuter Apache Sqoop avec PowerShell
Le script PowerShell suivant prétraite le fichier source, puis l’exporte dans une base de données SQL Azure :

    $resourceGroupName = "<AzureResourceGroupName>"
    $hdinsightClusterName = "<HDInsightClusterName>"

    $httpUserName = "admin"
    $httpPassword = "<Password>"

    $defaultStorageAccountName = $hdinsightClusterName + "store"
    $defaultBlobContainerName = $hdinsightClusterName


    $sqlDatabaseServerName = $hdinsightClusterName + "dbserver"
    $sqlDatabaseName = $hdinsightClusterName + "db"
    $sqlDatabaseLogin = "sqluser"
    $sqlDatabasePassword = "<Password>"

    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzContext}
    catch{Connect-AzAccount}
    #endregion

    #region - pre-process the source file

    Write-Host "`nPreprocessing the source file ..." -ForegroundColor Green

    # This procedure creates a new file with $destBlobName
    $sourceBlobName = "example/data/sample.log"
    $destBlobName = "tutorials/usesqoop/data/sample.log"

    # Define the connection string
    $defaultStorageAccountKey = (Get-AzStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName)[0].Value
    $storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$defaultStorageAccountName;AccountKey=$defaultStorageAccountKey"

    # Create block blob objects referencing the source and destination blob.
    $storageAccount = Get-AzStorageAccount -ResourceGroupName $ResourceGroupName -Name $defaultStorageAccountName
    $storageContainer = ($storageAccount |Get-AzStorageContainer -Name $defaultBlobContainerName).CloudBlobContainer
    $sourceBlob = $storageContainer.GetBlockBlobReference($sourceBlobName)
    $destBlob = $storageContainer.GetBlockBlobReference($destBlobName)

    # Define a MemoryStream and a StreamReader for reading from the source file
    $stream = New-Object System.IO.MemoryStream
    $stream = $sourceBlob.OpenRead()
    $sReader = New-Object System.IO.StreamReader($stream)

    # Define a MemoryStream and a StreamWriter for writing into the destination file
    $memStream = New-Object System.IO.MemoryStream
    $writeStream = New-Object System.IO.StreamWriter $memStream

    # Pre-process the source blob
    $exString = "java.lang.Exception:"
    while(-Not $sReader.EndOfStream){
        $line = $sReader.ReadLine()
        $split = $line.Split(" ")

        # remove the "java.lang.Exception" from the first element of the array
        # for example: java.lang.Exception: 2012-02-03 19:11:02 SampleClass8 [WARN] problem finding id 153454612
        if ($split[0] -eq $exString){
            #create a new ArrayList to remove $split[0]
            $newArray = [System.Collections.ArrayList] $split
            $newArray.Remove($exString)

            # update $split and $line
            $split = $newArray
            $line = $newArray -join(" ")
        }

        # remove the lines that has less than 7 elements
        if ($split.count -ge 7){
            write-host $line
            $writeStream.WriteLine($line)
        }
    }

    # Write to the destination blob
    $writeStream.Flush()
    $memStream.Seek(0, "Begin")
    $destBlob.UploadFromStream($memStream)

    #endregion

    #region - export the log file from the cluster to the SQL database

    Write-Host "Exporting the log file ..." -ForegroundColor Green

    $pw = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
    $httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$pw)

    # Connection string for Azure SQL Database.
    # Comment if using SQL Server
    $connectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$sqlDatabaseName"
    # Connection string for SQL Server.
    # Uncomment if using SQL Server.
    #$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName;user=$sqlDatabaseLogin;password=$sqlDatabasePassword;database=$sqlDatabaseName"

    $tableName_log4j = "log4jlogs"
    $exportDir_log4j = "/tutorials/usesqoop/data"
    $sqljdbcdriver = "/user/oozie/share/lib/sqoop/sqljdbc41.jar"

    # Submit a Sqoop job
    $sqoopDef = New-AzHDInsightSqoopJobDefinition `
        -Command "export --connect $connectionString --table $tableName_log4j --export-dir $exportDir_log4j --input-fields-terminated-by \0x20 -m 1" `
        -Files $sqljdbcdriver

    $sqoopJob = Start-AzHDInsightJob `
                    -ClusterName $hdinsightClusterName `
                    -HttpCredential $httpCredential `
                    -JobDefinition $sqoopDef #-Debug -Verbose

    Wait-AzHDInsightJob `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -HttpCredential $httpCredential `
        -JobId $sqoopJob.JobId

    Write-Host "Standard Error" -BackgroundColor Green
    Get-AzHDInsightJobOutput -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -DefaultContainer $defaultBlobContainerName -HttpCredential $httpCredential -JobId $sqoopJob.JobId -DisplayOutputType StandardError
    Write-Host "Standard Output" -BackgroundColor Green
    Get-AzHDInsightJobOutput -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -DefaultContainer $defaultBlobContainerName -HttpCredential $httpCredential -JobId $sqoopJob.JobId -DisplayOutputType StandardOutput
    #endregion

## <a name="limitations"></a>Limites
HDInsight basé sur Linux présente les limitations suivantes :

* Exportation en bloc : le connecteur Sqoop utilisé pour exporter des données vers Microsoft SQL Server ou Azure SQL Database ne prend pas en charge actuellement les insertions en bloc.

* Traitement par lot : lorsque vous utilisez le commutateur `-batch` pour procéder à des insertions, Sqoop effectue plusieurs insertions plutôt qu’un traitement par lot des opérations d’insertion. 

## <a name="next-steps"></a>Étapes suivantes
Vous maîtrisez à présent l'utilisation de Sqoop. Pour plus d'informations, consultez les rubriques suivantes :

* [Utiliser Apache Oozie avec HDInsight](../hdinsight-use-oozie.md) : Utilisez l’action Sqoop dans un workflow Oozie.
* [Chargez des données dans HDInsight](../hdinsight-upload-data.md) : découvrez d’autres méthodes pour charger des données dans HDInsight ou le stockage Blob Azure.

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html
