---
title: チュートリアル:エンドツーエンドの ETL パイプラインを作成して売上の分析情報を導き出す
description: Azure HDInsight を使用して ETL パイプラインを作成し、Spark のオンデマンド クラスターと Power BI を使用して売上データから分析情報を導き出す方法を説明します。
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 09/30/2019
ms.author: hrasheed
ms.openlocfilehash: b9bcaf4b7497e8beba377eb7e47a44a6eb061299
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2019
ms.locfileid: "72178004"
---
# <a name="tutorial-create-an-end-to-end-data-pipeline-to-derive-sales-insights"></a>チュートリアル:エンドツーエンドのデータ パイプラインを作成して売上の分析情報を導き出す

このチュートリアルでは、抽出、変換、読み込み (ETL) 操作を実行するエンドツーエンドのデータ パイプラインを作成します。 このパイプラインでは、データに対してクエリを実行して操作するために、Azure HDInsight 上で稼働している Apache Spark および Apache Hive クラスターを使用します。 また、データ ストレージ用に Data Lake Storage Gen2、視覚化用に Power BI といったテクノロジを使用します。

このデータ パイプラインでは、さまざまな店舗のデータを結合し、不要なデータがあれば削除して新しいデータを追加し、これをすべてストレージに再度読み込んで、ビジネスの分析情報を視覚化します。 ETL パイプラインの詳細については、「[大規模な抽出、変換、および読み込み (ETL)](./hadoop/apache-hadoop-etl-at-scale.md)」を参照してください。

![ETL アーキテクチャ](./media/hdinsight-sales-insights-etl/architecture.png)

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。

このチュートリアルの最後にビジネスの分析情報を視覚化するために、[Power BI Desktop](https://www.microsoft.com/download/details.aspx?id=45331) をダウンロードします。

## <a name="create-resources"></a>リソースを作成する

### <a name="clone-the-repository-with-scripts-and-data"></a>スクリプトとデータを含むリポジトリを複製する

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. 上部のメニュー バーから Azure Cloud Shell を開きます。 Cloud Shell から確認のメッセージが求められた場合は、ファイル共有の作成に使用するサブスクリプションを選択します。

   ![Azure Cloud Shell を開く](./media/hdinsight-sales-insights-etl/hdinsight-sales-insights-etl-click-cloud-shell.png)
1. **[Select environment]\(環境の選択\)** ボックスの一覧から **[Bash]** を選択します。
1. お使いの Azure アカウントにサインインし、サブスクリプションを設定します。 
1. プロジェクト用のリソース グループを設定します。
   1. リソース グループには一意の名前を選択します。
   1. Cloud Shell で以下のコード スニペットを実行して、後の手順で使用する変数を設定します。

       ```azurecli-interactive 
       resourceGroup="<RESOURCE GROUP NAME>"
       subscriptionID="<SUBSCRIPTION ID>"
        
       az account set --subscription $subscriptionID
       az group create --name $resourceGroup --location westus
       ```

1. Cloud Shell で次のコマンドを入力して、[HDInsight sales insights ETL のリポジトリ](https://github.com/Azure-Samples/hdinsight-sales-insights-etl)からこのチュートリアル用のデータとスクリプトをダウンロードします。

    ```azurecli-interactive 
    git clone https://github.com/Azure-Samples/hdinsight-sales-insights-etl.git
    cd hdinsight-sales-insights-etl
    ```

1. シェル プロンプトで「`ls`」と入力して、以下のファイルとディレクトリが作成されていることを確認します。

   ```output
   /salesdata/
   /scripts/
   /templates/
   ```

### <a name="deploy-azure-resources-needed-for-the-pipeline"></a>パイプラインに必要な Azure リソースをデプロイする 

1. `chmod +x scripts/*.sh` スクリプトに対する実行のアクセス許可を追加します。
1. コマンド `./scripts/resources.sh <RESOURCE_GROUP_NAME> <LOCATION>` を使用してこのスクリプトを実行し、次のリソースを Azure にデプロイします。

   1. Azure Blob Storage アカウント。 このアカウントは会社の売上データを保持します。
   2. Azure Data Lake Storage Gen2 アカウント。 このアカウントは、両方の HDInsight クラスター用のストレージ アカウントとして機能します。 HDInsight と Data Lake Storage Gen2 の詳細については、[Azure HDInsight と Data Lake Storage Gen2 の統合](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/)に関するページを参照してください。
   3. ユーザー割り当てマネージド ID。 このアカウントにより、HDInsight クラスターは Data Lake Storage Gen2 アカウントにアクセスできるようになります。
   4. Apache Spark クラスター。 このクラスターは、生データのクリーンアップと変換に使用されます。
   5. Apache Hive Interactive Query クラスター。 このクラスターにより、売上データに対してクエリを実行し、Power BI を使用してそれを視覚化できるようになります。
   6. ネットワーク セキュリティ グループ (NSG) 規則でサポートされている Azure 仮想ネットワーク。 この仮想ネットワークでは、クラスターによる通信とその通信のセキュリティ保護が可能になります。 

クラスターの作成には約 20 分かかることがあります。

`resources.sh` スクリプトには次のコマンドが含まれています。 このコマンドは、Azure Resource Manager テンプレート (`resourcestemplate.json`) を使用して、指定されたリソースを必要な構成で作成します。

```azurecli-interactive 
az group deployment create --name ResourcesDeployment \
    --resource-group $resourceGroup \
    --template-file resourcestemplate.json \
    --parameters "@resourceparameters.json"
```

また、`resources.sh` スクリプトは、次のコマンドを使用して、新しく作成された Blob Storage アカウントに売上データの .csv ファイルをアップロードします。

```
az storage blob upload-batch -d rawdata \
    --account-name <BLOB STORAGE NAME> -s ./ --pattern *.csv
```

クラスターへの SSH アクセスに使用される既定のパスワードは、`Thisisapassword1` です。 このパスワードを変更する場合は、`resourcesparameters.json` ファイルに移動し、`sparksshPassword`、`sparkClusterLoginPassword`、`llapClusterLoginPassword`、`llapsshPassword` パラメーターのパスワードを変更してください。

### <a name="verify-deployment-and-collect-resource-information"></a>デプロイを確認してリソースの情報を収集する

1. デプロイの状態を確認する場合は、Azure portal でそのリソース グループに移動します。 **[設定]** で **[デプロイ]** を選択します。 デプロイの名前 `ResourcesDeployment` を選択します。 ここで、正常にデプロイされたリソースとまだ進行中のリソースを確認できます。
1. デプロイが完了したら、Azure portal、 **[リソース グループ]** 、<RESOURCE_GROUP_NAME> の順に移動します。
1. 売上ファイルを格納するために作成された新しい Azure ストレージ アカウントを見つけます。 ストレージ アカウントの名前は `blob` で始まり、その後にランダムな文字列が含まれます。 以下の手順を実行します。
   1. 後で使用するために、そのストレージ アカウント名を書き留めておきます。
   1. Blob Storage アカウントの名前を選択します。
   1. ポータルの左側で、 **[設定]** の **[アクセス キー]** を選択します。
   1. **[Key1]** ボックスの文字列をコピーし、後で使用できるように保存します。
1. HDInsight クラスター用のストレージとして作成された Data Lake Storage Gen2 アカウントを見つけます。 このアカウントは、Blob Storage アカウントと同じリソース グループ内にありますが、`adlsgen2` で始まります。 以下の手順を実行します。
   1. この Data Lake Storage Gen2 アカウントの名前を書き留めておきます。
   1. Data Lake Storage Gen2 アカウントの名前を選択します。
   1. ポータルの左側で、 **[設定]** の **[アクセス キー]** を選択します。
   1. **[Key1]** ボックスの文字列をコピーし、後で使用できるように保存します。

> [!Note]
> ストレージ アカウントの名前がわかったら、Azure Cloud Shell プロンプトで次のコマンドを使用して、アカウント キーを取得することができます。
> ```azurecli-interactive
> az storage account keys list \
>    --account-name <STORAGE NAME> \
>    --resource-group $rg \
>    --output table
> ```

### <a name="create-a-data-factory"></a>Data Factory の作成

Azure Data Factory とは、Azure Pipelines の自動化に役立つツールです。 これは、これらのタスクを実現する唯一の方法ではありませんが、これらのプロセスを自動化するための優れた方法です。 Azure Data Factory の詳細については、[Azure Data Factory のドキュメント](https://azure.microsoft.com/en-us/services/data-factory/)を参照してください。 

このデータ ファクトリには、次の 2 つのアクティビティを含む 1 つのパイプラインがあります。 

- 1 つ目のアクティビティでは、Azure Blob Storage から Data Lake Storage Gen 2 ストレージ アカウントにデータをコピーして、データ インジェストを模倣します。
- 2 つ目のアクティビティでは、Spark クラスター内でこのデータを変換します。 このスクリプトでは、不要な列を削除することによって、データを変換します。 また、1 回の取引で生じた収益を計算する新しい列が追加されます。

Azure Data Factory パイプラインを設定するには、スクリプト `adf.sh` を実行します。

1. `chmod +x adf.sh` を使用して、ファイルに対する実行アクセス許可を追加します。
1. `./adf.sh` を使用してスクリプトを実行します。 

このスクリプトでは、次のことが行われます。

1. Data Lake Storage Gen2 ストレージ アカウントに対する `Storage Blob Data Contributor` アクセス許可を持つサービス プリンシパルを作成します。
1. [Data Lake Storage Gen2 ファイル システム REST API](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/filesystem/create) に対する POST 要求を承認するための認証トークンを取得します。
1. `sparktransform.py` および `query.hql` ファイルに、Data Lake Storage Gen2 ストレージ アカウントの実際の名前を入力します。
1. Data Lake Storage Gen2 および Blob Storage アカウントのストレージ キーを取得します。
1. 別のリソース デプロイを作成して、Azure Data Factory パイプラインとそれに関連付けられているリンクされたサービスやアクティビティを作成します。 これによって、リンクされたサービスがストレージ アカウントに正しくアクセスできるように、ストレージ キーがパラメーターとしてテンプレート ファイルに渡されます。

Data Factory パイプラインは、次のコマンドを使用してデプロイされます。

```azurecli-interactive
az group deployment create --name ADFDeployment \
    --resource-group $resourceGroup \
    --template-file adftemplate.json \
    --parameters "@adfparameters.json"
```

## <a name="run-the-data-pipeline"></a>データ パイプラインを実行する

### <a name="trigger-the-data-factory-activities"></a>Data Factory のアクティビティをトリガーする

作成した Data Factory パイプラインの最初のアクティビティでは、データを Blob Storage から Data Lake Storage Gen2 に移動します。 2 つ目のアクティビティでは、Spark によるデータの変換を適用し、変換された .csv ファイルを新しい場所に保存します。 パイプライン全体が完了するまでに数分かかる場合があります。

パイプラインをトリガーするには、次のいずれかを実行します。

- PowerShell で次のコマンドを実行して Data Factory パイプラインをトリガーします。 

    ```powershell
    Invoke-AzDataFactoryV2Pipeline -DataFactory $df -PipelineName "CopyPipeline_k8z" 
    Invoke-AzDataFactoryV2Pipeline -DataFactory $df -PipelineName "sparkTransformPipeline"
    ```

- データ ファクトリを開いて **[作成と監視]** を選択します。 コピー パイプラインをトリガーした後、ポータルで Spark パイプラインをトリガーします。 ポータルでのパイプラインのトリガーについては、「[Azure Data Factory を使用して HDInsight でオンデマンドの Apache Hadoop クラスターを作成する](hdinsight-hadoop-create-linux-clusters-adf.md#trigger-a-pipeline)」を参照してください。

パイプラインが実行されたことを確認するには、次のいずれかの手順を実行します。

- ポータルからデータ ファクトリの **[監視]** セクションに移動します。
- Azure Storage Explorer で、Data Lake Storage Gen 2 ストレージ アカウントに移動します。 `files` ファイルシステム、`transformed` フォルダーの順に移動して、その内容をチェックしてパイプラインが成功したかどうかを確認します。

HDInsight を使用してデータを変換する他の方法については、[Jupyter Notebook の使用に関するこの記事](/azure/hdinsight/spark/apache-spark-load-data-run-query)を参照してください。

### <a name="create-a-table-on-the-interactive-query-cluster-to-view-data-on-power-bi"></a>Power BI でデータを表示するためのテーブルを Interactive Query クラスターを作成する

1. SCP を使用して、`query.hql` ファイルを LLAP クラスターにコピーします。

    ```
    scp scripts/query.hql sshuser@<clustername>-ssh.azurehdinsight.net:/home/sshuser/
    ```

2. 次のコマンドを使用して LLAP クラスターに SSH でアクセスし、パスワードを入力します。 `resourcesparameters.json` ファイルを変更していない場合、パスワードは `Thisisapassword1` です。

    ```
    ssh sshuser@<clustername>-ssh.azurehdinsight.net
    ```

3. 次のコマンドを使用して、スクリプトを実行します。

    ```
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f query.hql
    ```

このスクリプトにより、Power BI からアクセスできるマネージド テーブルが Interactive Query クラスター上に作成されます。 

### <a name="create-a-power-bi-dashboard-from-sales-data"></a>売上データから Power BI ダッシュボードを作成する

1. Power BI Desktop を開きます。
1. **[データの取得]** を選択します。
1. **HDInsight Interactive Query クラスター**を検索します。
1. そのクラスターの URI をそこに貼り付けます。 `https://<LLAP CLUSTER NAME>.azurehdinsight.net` 形式にする必要があります。

   データベースの `default` を入力します。
1. クラスターへのアクセスに使用するユーザー名とパスワードを入力します。

データが読み込まれたら、作成したいダッシュボードを試すことができます。 Power BI ダッシュボードの概要については、以下のリンクを参照してください。

* [Power BI デザイナーのダッシュボードの概要](https://docs.microsoft.com/power-bi/service-dashboards)
* [チュートリアル:Power BI サービスの概要](https://docs.microsoft.com/power-bi/service-get-started)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このアプリケーションを今後使用しない場合は、課金されないようにするために、次のコマンドを使用して、すべてのリソースを削除してください。

```azurecli-interactive 
az group delete -n $resourceGroup
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [大規模な抽出、変換、および読み込み (ETL)](./hadoop/apache-hadoop-etl-at-scale.md)
