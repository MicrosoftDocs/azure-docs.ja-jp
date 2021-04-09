---
title: クイック スタート:Azure PowerShell を使用した Azure HDInsight での Apache Spark クラスターの作成
description: このクイック スタートでは、Azure PowerShell を使って、Azure HDInsight に Apache Spark クラスターを作成し、単純な Spark SQL クエリを実行する方法を示します。
ms.service: hdinsight
ms.topic: quickstart
ms.date: 06/12/2019
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 0d4a8df4cb6ba47b7772e2ab3b3247d36622dcb6
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104865963"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-powershell"></a>クイック スタート:PowerShell を使用して Azure HDInsight 内に Apache Spark クラスターを作成する

このクイックスタートでは、Azure PowerShell を使用して Azure HDInsight に Apache Spark クラスターを作成します。 その後、Jupyter Notebook を作成し、それを使用して、Apache Hive のテーブルに対する Spark SQL クエリを実行します。 Azure HDInsight は、全範囲に対応した、オープンソースのエンタープライズ向けマネージド分析サービスです。 Azure HDInsight の Apache Spark フレームワークにより、メモリ内処理を使用した、高速のデータ分析とクラスター コンピューティングが可能になります。 Jupyter Notebook を使用すると、データを対話的に操作したり、コードと Markdown テキストとを結合したり、簡単な視覚化を行ったりすることができます。

[概要:Azure HDInsight の Apache Spark](apache-spark-overview.md) | [Apache Spark](https://spark.apache.org/) | [Apache Hive](https://hive.apache.org/) | [Jupyter Notebook](https://jupyter.org/)

複数のクラスターを一緒に使用している場合は、仮想ネットワークを作成する必要があります。また、Spark クラスターを使用している場合は、Hive Warehouse Connector を使用することもできます。 詳細については、「[Azure HDInsight 用の仮想ネットワークを計画する](../hdinsight-plan-virtual-network-deployment.md)」と「[Hive Warehouse Connector を使用して Apache Spark と Apache Hive を統合する](../interactive-query/apache-hive-warehouse-connector.md)」を参照してください。

## <a name="prerequisite"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- [PowerShell Az モジュール](/powershell/azure/install-az-ps)。

## <a name="create-an-apache-spark-cluster-in-hdinsight"></a>HDInsight での Apache Spark クラスターの作成

> [!IMPORTANT]  
> HDInsight クラスターの料金は、そのクラスターを使用しているかどうかに関係なく、分単位で課金されます。 使用後は、クラスターを必ず削除してください。 詳しくは、この記事の「[リソースのクリーンアップ](#clean-up-resources)」をご覧ください。

HDInsight クラスターの作成には、次の Azure オブジェクトとリソースの作成が含まれます。

- Azure リソース グループ。 Azure リソース グループは、Azure リソースのコンテナーです。
- Azure Storage アカウントまたは Azure Data Lake Storage。  各 HDInsight クラスターには、依存データ ストレージが必要です。 このクイックスタートでは、Azure Storage Blob をクラスター記憶域として使用するクラスターを作成します。 Data Lake Storage Gen2 の使用法の詳細については、「[クイック スタート:HDInsight のクラスターを設定する](../hdinsight-hadoop-provision-linux-clusters.md)」をご覧ください。
- HDInsight の各種クラスター。  このクイック スタートでは、Spark 2.3 クラスターを作成します。

リソースを作成するには、PowerShell スクリプトを使います。 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

PowerShell スクリプトを実行すると、次の値の入力を求められます。

|パラメーター|値|
|------|------|
|Azure リソース グループ名 | リソース グループの一意名を指定します。|
|場所| Azure リージョンを指定します (例: "米国中部")。 |
|既定のストレージ アカウント名 | ストレージ アカウントに一意の名前を指定してください。 |
|クラスター名 | HDInsight クラスターの一意の名前を指定します。|
|クラスター ログイン資格情報 | このクイック スタートでは後で、このアカウントを使ってクラスター ダッシュボードに接続します。|
|SSH ユーザー資格情報 | SSH クライアントを使って、HDInsight のクラスターとのリモート コマンド ライン セッションを作成できます。|

1. 次のコード ブロックの右上隅にある **[使ってみる]** を選択して [Azure Cloud Shell](../../cloud-shell/overview.md) を開き、説明に従って Azure に接続します。

2. 次の PowerShell スクリプトをコピーして Cloud Shell に貼り付けます。

    ```azurepowershell-interactive
    ### Create a Spark 2.3 cluster in Azure HDInsight

    # Default cluster size (# of worker nodes), version, and type
    $clusterSizeInNodes = "1"
    $clusterVersion = "3.6"
    $clusterType = "Spark"

    # Create the resource group
    $resourceGroupName = Read-Host -Prompt "Enter the resource group name"
    $location = Read-Host -Prompt "Enter the Azure region to create resources in, such as 'Central US'"
    $defaultStorageAccountName = Read-Host -Prompt "Enter the default storage account name"

    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create an Azure storage account and container
    # Note: Storage account kind BlobStorage can only be used as secondary storage for HDInsight clusters.
    New-AzStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $defaultStorageAccountName `
        -Location $location `
        -SkuName Standard_LRS `
        -Kind StorageV2 `
        -EnableHttpsTrafficOnly 1

    $defaultStorageAccountKey = (Get-AzStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName)[0].Value

    $defaultStorageContext = New-AzStorageContext `
                                    -StorageAccountName $defaultStorageAccountName `
                                    -StorageAccountKey $defaultStorageAccountKey

    # Create a Spark 2.3 cluster
    $clusterName = Read-Host -Prompt "Enter the name of the HDInsight cluster"

    # Cluster login is used to secure HTTPS services hosted on the cluster
    $httpCredential = Get-Credential -Message "Enter Cluster login credentials" -UserName "admin"

    # SSH user is used to remotely connect to the cluster using SSH clients
    $sshCredentials = Get-Credential -Message "Enter SSH user credentials" -UserName "sshuser"

    # Set the storage container name to the cluster name
    $defaultBlobContainerName = $clusterName

    # Create a blob container. This holds the default data store for the cluster.
    New-AzStorageContainer `
        -Name $clusterName `
        -Context $defaultStorageContext

    $sparkConfig = New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"
    $sparkConfig.Add("spark", "2.3")

    # Create the cluster in HDInsight
    New-AzHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $clusterName `
        -Location $location `
        -ClusterSizeInNodes $clusterSizeInNodes `
        -ClusterType $clusterType `
        -OSType "Linux" `
        -Version $clusterVersion `
        -ComponentVersion $sparkConfig `
        -HttpCredential $httpCredential `
        -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultStorageContainer $clusterName `
        -SshCredential $sshCredentials

    Get-AzHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $clusterName
    ```

   クラスターの作成には約 20 分かかります。 次のセッションに進む前に、クラスターを作成する必要があります。

HDInsight クラスターを作成する際に問題が発生した場合は、適切なアクセス許可がない可能性があります。 詳細については、「[アクセス制御の要件](../hdinsight-hadoop-customize-cluster-linux.md#access-control)」を参照してください。

## <a name="create-a-jupyter-notebook"></a>Jupyter Notebook の作成

[Jupyter Notebook](https://jupyter.org/) は、さまざまなプログラミング言語をサポートする対話型のノートブック環境です。 ノートブックを使うと、データと対話し、Markdown テキストとコードを組み合わせて、簡単な視覚化を実行できます。

1. [Azure portal](https://portal.azure.com) で、 **[HDInsight クラスター]** を探して選択します。
   
   :::image type="content" source="./media/apache-spark-jupyter-spark-sql-use-powershell/azure-portal-search-hdinsight-cluster.png" alt-text="スクリーンショットは、HDInsight を検索している Azure portal を示しています。" border="true":::
   
1. 作成したクラスターを一覧から選択します。
   
   :::image type="content" source="./media/apache-spark-jupyter-spark-sql-use-powershell/azure-portal-open-hdinsight-cluster.png" alt-text="スクリーンショットは、作成したクラスターを使用した HDInsight クラスターを示しています。" border="true":::
   
1. クラスターの **[概要]** ページで、 **[クラスター ダッシュボード]** を選択し、 **[Jupyter Notebook]** を選択します。 入力を求められたら、クラスターのログイン資格情報を入力します。

   :::image type="content" source="./media/apache-spark-jupyter-spark-sql-use-powershell/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png " alt-text="Jupyter Notebook を開いて対話型の Spark SQL クエリを実行する" border="true":::

1. **[新規]**  >  **[PySpark]** を選んで、ノートブックを作成します。

   :::image type="content" source="./media/apache-spark-jupyter-spark-sql-use-powershell/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png " alt-text="Jupyter Notebook を作成して対話型の Spark SQL クエリを実行する" border="true":::

   Untitled(Untitled.pynb) という名前の新しい Notebook が作成されて開かれます。

## <a name="run-apache-spark-sql-statements"></a>Apache Spark SQL ステートメントを実行する

SQL (構造化照会言語) は、データ照会とデータ定義のための言語として最も一般的かつ広く使用されています。 Spark SQL を Apache Spark の拡張機能として導入することで、使い慣れた SQL 構文を使って構造化データを扱うことができます。

1. カーネルの準備ができていることを確認します。 Notebook のカーネル名の横に白丸が表示されたら、カーネルの準備ができています。 黒丸は、カーネルがビジー状態であることを示します。

    :::image type="content" source="./media/apache-spark-jupyter-spark-sql/jupyter-spark-kernel-status.png " alt-text="カーネルの状態" border="true":::

    Notebook を初めて起動すると、カーネルがバックグラウンドでいくつかのタスクを実行します。 カーネルの準備ができるまで待ちます。 
1. 次のコードを空のセルに貼り付け、**Shift + Enter** キーを押してコードを実行します。 コマンドを実行すると、クラスター上の Hive テーブルが一覧表示されます。

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    HDInsight の Spark クラスターで Jupyter Notebook を使用すると、Spark SQL を使用して Hive クエリを実行するために使用できるプリセット `sqlContext` が手に入ります。 `%%sql` により、プリセット `sqlContext` を使用して Hive クエリを実行するよう Jupyter Notebook に指示します。 クエリは、すべての HDInsight クラスターに既定で付属する Hive テーブル (**hivesampletable**) から先頭の 10 行を取得します。 結果が得られるまで約 30 秒かかります。 出力は次のようになります。

    :::image type="content" source="./media/apache-spark-jupyter-spark-sql-use-powershell/hdinsight-spark-get-started-hive-query.png " alt-text="HDInsight の Spark における Apache Hive クエリ" border="true":::

    Jupyter でクエリを実行するたびに、Web ブラウザー ウィンドウのタイトルに **[(ビジー)]** ステータスと Notebook のタイトルが表示されます。 また、右上隅にある **PySpark** というテキストの横に塗りつぶされた円も表示されます。

1. 別のクエリを実行して、`hivesampletable` のデータを確認します。

    ```PySpark
    %%sql
    SELECT * FROM hivesampletable LIMIT 10
    ```

    画面が更新され、クエリ出力が表示されます。

    :::image type="content" source="./media/apache-spark-jupyter-spark-sql-use-powershell/hdinsight-spark-get-started-hive-query-output.png " alt-text="HDInsight での Hive クエリの出力" border="true":::

1. ノートブックの **[File]\(ファイル\)** メニューの **[Close and Halt]\(閉じて停止\)** を選びます。 Notebook をシャットダウンすると、クラスターのリソースが解放されます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

HDInsight はデータを Azure Storage または Azure Data Lake Storage に格納するので、クラスターが使われていないときは、クラスターを安全に削除できます。 また、HDInsight クラスターは、使用していない場合でも課金されます。 クラスターの料金は Storage の料金の何倍にもなるため、クラスターを使用しない場合は削除するのが経済的にも合理的です。 「[次のステップ](#next-steps)」で示されているチュートリアルにすぐに取り掛かる場合は、クラスターをそのままにしてもかまいません。

Azure portal に戻り、 **[削除]** を選びます。

:::image type="content" source="./media/apache-spark-jupyter-spark-sql-use-powershell/hdinsight-azure-portal-delete-cluster.png " alt-text="Azure portal で HDInsight クラスターを削除する" border="true":::

リソース グループ名を選び、リソース グループ ページを開いて、 **[リソース グループの削除]** を選ぶこともできます。 リソース グループを削除すると、HDInsight クラスターと既定のストレージ アカウントの両方が削除されます。

### <a name="piecemeal-clean-up-with-powershell-az-module"></a>PowerShell Az モジュールを使用した段階的な部分クリーンアップ

```powershell
# Removes the specified HDInsight cluster from the current subscription.
Remove-AzHDInsightCluster `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $clusterName

# Removes the specified storage container.
Remove-AzStorageContainer `
    -Name $clusterName `
    -Context $defaultStorageContext

# Removes a Storage account from Azure.
Remove-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName

# Removes a resource group.
Remove-AzResourceGroup `
    -Name $resourceGroupName
```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、HDInsight に Apache Spark クラスターを作成し、基本的な Spark SQL クエリを実行する方法を学習しました。 HDInsight クラスターを使用してサンプル データに対話型のクエリを実行する方法については、次のチュートリアルに進みます。

> [!div class="nextstepaction"]
> [Apache Spark への対話型クエリの実行](./apache-spark-load-data-run-query.md)