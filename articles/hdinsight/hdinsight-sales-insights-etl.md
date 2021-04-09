---
title: チュートリアル:Azure HDInsight でエンドツーエンドの ETL パイプラインを作成して売上の分析情報を導き出す
description: Azure HDInsight を使用して ETL パイプラインを作成し、Spark のオンデマンド クラスターと Power BI を使用して売上データから分析情報を導き出す方法を説明します。
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive, devx-track-azurecli
ms.date: 04/15/2020
ms.openlocfilehash: 32c331b10fcc73b5449588595370f8f69ef9893c
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104872049"
---
# <a name="tutorial-create-an-end-to-end-data-pipeline-to-derive-sales-insights-in-azure-hdinsight"></a>チュートリアル:Azure HDInsight でエンドツーエンドのデータ パイプラインを作成して売上の分析情報を導き出す

このチュートリアルでは、抽出、変換、読み込み (ETL) 操作を実行するエンドツーエンドのデータ パイプラインを作成します。 このパイプラインでは、データに対してクエリを実行して操作するために、Azure HDInsight 上で稼働している [Apache Spark](./spark/apache-spark-overview.md) および Apache Hive クラスターを使用します。 また、データ ストレージ用に Data Lake Storage Gen2、視覚化用に Power BI といったテクノロジを使用します。

このデータ パイプラインでは、さまざまな店舗のデータを結合し、不要なデータがあれば削除して新しいデータを追加し、これをすべてストレージに再度読み込んで、ビジネスの分析情報を視覚化します。 ETL パイプラインの詳細については、「[大規模な抽出、変換、および読み込み (ETL)](./hadoop/apache-hadoop-etl-at-scale.md)」を参照してください。

:::image type="content" source="./media/hdinsight-sales-insights-etl/architecture.png" alt-text="ETL アーキテクチャ" border="false":::

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

* Azure CLI - バージョン 2.2.0 以上。 「[Azure CLI のインストール](/cli/azure/install-azure-cli)」を参照してください。

* jq。コマンド ライン JSON プロセッサです。  [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/)に関するページを参照してください。

* [Azure の組み込みロールである所有者](../role-based-access-control/built-in-roles.md)のメンバー。

* PowerShell を使用して Data Factory パイプラインをトリガーしている場合は、[AZ モジュール](/powershell/azure/)が必要になります。

* [Power BI Desktop](https://aka.ms/pbiSingleInstaller)。このチュートリアルの最後に、ビジネスの分析情報を視覚化する目的で使用します。

## <a name="create-resources"></a>リソースを作成する

### <a name="clone-the-repository-with-scripts-and-data"></a>スクリプトとデータを含むリポジトリを複製する

1. Azure サブスクリプションにログインします。 Azure Cloud Shell を使用する予定の場合は、コード ブロックの右上隅で **[使ってみる]** を選択します。 それ以外の場合は、次のコマンドを入力します。

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

1. Azure の[所有者](../role-based-access-control/built-in-roles.md)ロールのメンバーであることを確認します。 `user@contoso.com` を、ご使用のアカウントに置き換え、次のコマンドを入力します。

    ```azurecli
    az role assignment list \
    --assignee "user@contoso.com" \
    --role "Owner"
    ```

    レコードが返されない方は、メンバーではないため、このチュートリアルの手順を実行することはできません。

1. [HDInsight sales insights ETL のリポジトリ](https://github.com/Azure-Samples/hdinsight-sales-insights-etl)からこのチュートリアル用のデータとスクリプトをダウンロードします。 次のコマンドを入力します。

    ```bash
    git clone https://github.com/Azure-Samples/hdinsight-sales-insights-etl.git
    cd hdinsight-sales-insights-etl
    ```

1. `salesdata scripts templates` が作成されたことを確認します。 この確認には次のコマンドを使用します。

   ```bash
   ls
   ```

### <a name="deploy-azure-resources-needed-for-the-pipeline"></a>パイプラインに必要な Azure リソースをデプロイする

1. 次のコマンドを入力して、すべてのスクリプトの実行アクセス許可を追加します。

    ```bash
    chmod +x scripts/*.sh
    ````

1. リソース グループの変数を設定します。 `RESOURCE_GROUP_NAME` を既存のリソース グループまたは新しいリソース グループの名前に置き換えてから、コマンドを入力します。

    ```bash
    resourceGroup="RESOURCE_GROUP_NAME"
    ```

1. スクリプトを実行します。 `LOCATION` を適切な値に置き換え、次のコマンドを入力します。

    ```bash
    ./scripts/resources.sh $resourceGroup LOCATION
    ```

    指定するリージョンがわからない場合は、[az account list-locations](/cli/azure/account#az-account-list-locations) コマンドで、サブスクリプションに対してサポートされているリージョンの一覧を取得できます。

    このコマンドによって次のリソースがデプロイされます。

    * Azure Blob Storage アカウント。 このアカウントは会社の売上データを保持します。
    * Azure Data Lake Storage Gen2 アカウント。 このアカウントは、両方の HDInsight クラスター用のストレージ アカウントとして機能します。 HDInsight と Data Lake Storage Gen2 の詳細については、[Azure HDInsight と Data Lake Storage Gen2 の統合](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/)に関するページを参照してください。
    * ユーザー割り当てマネージド ID。 このアカウントにより、HDInsight クラスターは Data Lake Storage Gen2 アカウントにアクセスできるようになります。
    * Apache Spark クラスター。 このクラスターは、生データのクリーンアップと変換に使用されます。
    * Apache Hive [Interactive Query](./interactive-query/apache-interactive-query-get-started.md) クラスター。 このクラスターにより、売上データに対してクエリを実行し、Power BI を使用してそれを視覚化できるようになります。
    * ネットワーク セキュリティ グループ (NSG) 規則でサポートされている Azure 仮想ネットワーク。 この仮想ネットワークでは、クラスターによる通信とその通信のセキュリティ保護が可能になります。

クラスターの作成には約 20 分かかることがあります。

クラスターへの SSH アクセスに使用される既定のパスワードは、`Thisisapassword1` です。 このパスワードを変更する場合は、`./templates/resourcesparameters_remainder.json` ファイルに移動し、`sparksshPassword`、`sparkClusterLoginPassword`、`llapClusterLoginPassword`、`llapsshPassword` パラメーターのパスワードを変更してください。

### <a name="verify-deployment-and-collect-resource-information"></a>デプロイを確認してリソースの情報を収集する

1. デプロイの状態を確認する場合は、Azure portal でそのリソース グループに移動します。 **[設定]** の **[デプロイ]** を選択し、デプロイを選択します。 ここで、正常にデプロイされたリソースとまだ進行中のリソースを確認できます。

1. クラスターの名前を表示するには、次のコマンドを入力します。

    ```bash
    sparkClusterName=$(cat resourcesoutputs_remainder.json | jq -r '.properties.outputs.sparkClusterName.value')
    llapClusterName=$(cat resourcesoutputs_remainder.json | jq -r '.properties.outputs.llapClusterName.value')

    echo "Spark Cluster" $sparkClusterName
    echo "LLAP cluster" $llapClusterName
    ```

1. Azure Storage アカウントとアクセス キーを表示するには、次のコマンドを入力します。

    ```azurecli
    blobStorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.blobStorageName.value')

    blobKey=$(az storage account keys list \
        --account-name $blobStorageName \
        --resource-group $resourceGroup \
        --query [0].value -o tsv)

    echo $blobStorageName
    echo $blobKey
    ```

1. Data Lake Storage Gen2 アカウントとアクセス キーを表示するには、次のコマンドを入力します。

    ```azurecli
    ADLSGen2StorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.adlsGen2StorageName.value')

    adlsKey=$(az storage account keys list \
        --account-name $ADLSGen2StorageName \
        --resource-group $resourceGroup \
        --query [0].value -o tsv)

    echo $ADLSGen2StorageName
    echo $adlsKey
    ```

### <a name="create-a-data-factory"></a>Data Factory の作成

Azure Data Factory とは、Azure Pipelines の自動化に役立つツールです。 これは、これらのタスクを実現する唯一の方法ではありませんが、これらのプロセスを自動化するための優れた方法です。 Azure Data Factory の詳細については、[Azure Data Factory のドキュメント](https://azure.microsoft.com/services/data-factory/)を参照してください。

このデータ ファクトリには、次の 2 つのアクティビティを含む 1 つのパイプラインがあります。

* 1 つ目のアクティビティでは、Azure Blob Storage から Data Lake Storage Gen 2 ストレージ アカウントにデータをコピーして、データ インジェストを模倣します。
* 2 つ目のアクティビティでは、Spark クラスター内でこのデータを変換します。 このスクリプトでは、不要な列を削除することによって、データを変換します。 また、1 回の取引で生じた収益を計算する新しい列が追加されます。

Azure Data Factory パイプラインを設定するには、次のコマンドを実行します。  現在のディレクトリは `hdinsight-sales-insights-etl` のままにする必要があります。

```bash
blobStorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.blobStorageName.value')
ADLSGen2StorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.adlsGen2StorageName.value')

./scripts/adf.sh $resourceGroup $ADLSGen2StorageName $blobStorageName
```

このスクリプトでは、次のことが行われます。

1. Data Lake Storage Gen2 ストレージ アカウントに対する `Storage Blob Data Contributor` アクセス許可を持つサービス プリンシパルを作成します。
1. [Data Lake Storage Gen2 ファイル システム REST API](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/filesystem/create) に対する POST 要求を承認するための認証トークンを取得します。
1. `sparktransform.py` および `query.hql` ファイルに、Data Lake Storage Gen2 ストレージ アカウントの実際の名前を入力します。
1. Data Lake Storage Gen2 および Blob Storage アカウントのストレージ キーを取得します。
1. 別のリソース デプロイを作成して、Azure Data Factory パイプラインとそれに関連付けられているリンクされたサービスやアクティビティを作成します。 これによって、リンクされたサービスがストレージ アカウントに正しくアクセスできるように、ストレージ キーがパラメーターとしてテンプレート ファイルに渡されます。

## <a name="run-the-data-pipeline"></a>データ パイプラインを実行する

### <a name="trigger-the-data-factory-activities"></a>Data Factory のアクティビティをトリガーする

作成した Data Factory パイプラインの最初のアクティビティでは、データを Blob Storage から Data Lake Storage Gen2 に移動します。 2 つ目のアクティビティでは、Spark によるデータの変換を適用し、変換された .csv ファイルを新しい場所に保存します。 パイプライン全体が完了するまでに数分かかる場合があります。

Data Factory の名前を取得するには、次のコマンドを入力します。

```azurecli
cat resourcesoutputs_adf.json | jq -r '.properties.outputs.factoryName.value'
```

パイプラインをトリガーするには、次のいずれかを実行します。

* PowerShell で Data Factory パイプラインをトリガーします。 `RESOURCEGROUP` と `DataFactoryName` を適切な値に置き換えてから、次のコマンドを実行します。

    ```powershell
    # If you have multiple subscriptions, set the one to use
    # Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

    $resourceGroup="RESOURCEGROUP"
    $dataFactory="DataFactoryName"

    $pipeline =Invoke-AzDataFactoryV2Pipeline `
        -ResourceGroupName $resourceGroup `
        -DataFactory $dataFactory `
        -PipelineName "IngestAndTransform"

    Get-AzDataFactoryV2PipelineRun `
        -ResourceGroupName $resourceGroup  `
        -DataFactoryName $dataFactory `
        -PipelineRunId $pipeline
    ```

    必要に応じて `Get-AzDataFactoryV2PipelineRun` を再実行し、進行状況を監視します。

    または

* データ ファクトリを開いて **[作成と監視]** を選択します。 `IngestAndTransform` パイプラインをポータルからトリガーします。 ポータルでのパイプラインのトリガーについては、「[Azure Data Factory を使用して HDInsight でオンデマンドの Apache Hadoop クラスターを作成する](hdinsight-hadoop-create-linux-clusters-adf.md#trigger-a-pipeline)」を参照してください。

パイプラインが実行されたことを確認するには、次のいずれかの手順を実行します。

* ポータルからデータ ファクトリの **[監視]** セクションに移動します。
* Azure Storage Explorer で、Data Lake Storage Gen 2 ストレージ アカウントに移動します。 `files` ファイルシステム、`transformed` フォルダーの順に移動して、その内容をチェックしてパイプラインが成功したかどうかを確認します。

HDInsight を使用してデータを変換する他の方法については、[Jupyter Notebook の使用に関するこの記事](/azure/hdinsight/spark/apache-spark-load-data-run-query)を参照してください。

### <a name="create-a-table-on-the-interactive-query-cluster-to-view-data-on-power-bi"></a>Power BI でデータを表示するためのテーブルを Interactive Query クラスターを作成する

1. SCP を使用して、`query.hql` ファイルを LLAP クラスターにコピーします。 次のコマンドを入力します。

    ```bash
    llapClusterName=$(cat resourcesoutputs_remainder.json | jq -r '.properties.outputs.llapClusterName.value')
    scp scripts/query.hql sshuser@$llapClusterName-ssh.azurehdinsight.net:/home/sshuser/
    ```

    リマインダー:既定のパスワードは `Thisisapassword1`です。

1. SSH を使用して LLAP クラスターにアクセスします。 次のコマンドを入力します。

    ```bash
    ssh sshuser@$llapClusterName-ssh.azurehdinsight.net
    ```

1. 次のコマンドを使用して、スクリプトを実行します。

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f query.hql
    ```

    このスクリプトにより、Power BI からアクセスできるマネージド テーブルが Interactive Query クラスター上に作成されます。

### <a name="create-a-power-bi-dashboard-from-sales-data"></a>売上データから Power BI ダッシュボードを作成する

1. Power BI Desktop を開きます。

1. メニューから **[データの取得]**  >  **[その他]**  >  **[Azure]**  >  **[HDInsight 対話型クエリ]** の順に移動します。

1. **[接続]** を選択します。

1. **[HDInsight 対話型クエリ]** ダイアログから次の手順を実行します。
    1. **[サーバー]** ボックスに、LLAP クラスターの名前を `https://LLAPCLUSTERNAME.azurehdinsight.net` 形式で入力します。
    1. **[データベース]** ボックスに「`default`」と入力します。
    1. **[OK]** を選択します。

1. **[AzureHive]** ダイアログから次の手順を実行します。
    1. **[ユーザー名]** ボックスに「`admin`」と入力します。
    1. **[パスワード]** ボックスに「`Thisisapassword1`」と入力します。
    1. **[接続]** を選択します。

1. **[ナビゲーター]** から `sales` または `sales_raw` を選択して、データをプレビューします。 データが読み込まれたら、作成したいダッシュボードを試すことができます。 Power BI ダッシュボードの概要については、以下のリンクを参照してください。

* [Power BI デザイナーのダッシュボードの概要](https://docs.microsoft.com/power-bi/service-dashboards)
* [チュートリアル:Power BI サービスの概要](https://docs.microsoft.com/power-bi/service-get-started)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このアプリケーションを今後使用しない場合は、課金されないようにするために、次のコマンドを使用して、すべてのリソースを削除してください。

1. リソース グループを削除するために、次のコマンドを入力します。

    ```azurecli
    az group delete -n $resourceGroup
    ```

1. サービス プリンシパルを削除するために、次のコマンドを入力します。

    ```azurecli
    servicePrincipal=$(cat serviceprincipal.json | jq -r '.name')
    az ad sp delete --id $servicePrincipal
    ```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [大規模な抽出、変換、および読み込み (ETL)](./hadoop/apache-hadoop-etl-at-scale.md)
