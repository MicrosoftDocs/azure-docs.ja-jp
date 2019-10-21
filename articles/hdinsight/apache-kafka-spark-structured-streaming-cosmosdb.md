---
title: Apache Kafka から Azure Cosmos DB への Apache Spark データ - Azure HDInsight
description: Apache Spark 構造化ストリーミングを使って Apache Kafka からデータを読み込み、そのデータを Azure Cosmos DB に保存する方法を説明します。 この例では、Jupyter Notebook を使用して HDInsight 上で Spark からデータをストリームします。
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: hrasheed
ms.openlocfilehash: 0d8c6929705ab29ced25a847bf7c5a72d57aa49b
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2019
ms.locfileid: "71037286"
---
# <a name="use-apache-spark-structured-streaming-with-apache-kafka-and-azure-cosmos-db"></a>Apache Kafka と Azure Cosmos DB で Apache Spark 構造化ストリーミングを使用する

[Apache Spark](https://spark.apache.org/) [構造化ストリーミング](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html) を使って、Azure HDInsight 上で [Apache Kafka](https://kafka.apache.org/) からデータを読み込み、そのデータを Azure Cosmos DB に保存する方法を説明します。

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) は、グローバル分散型のマルチモデル データベースです。 この例では、SQL API データベース モデルを使用します。 詳細については、「[Azure Cosmos DB の概要](../cosmos-db/introduction.md)」のドキュメントを参照してください。

Spark 構造化ストリーミングは、Spark SQL に組み込まれたストリーミング処理エンジンであり、 静的データに対してバッチ計算と同様にストリーミング計算を表現できるようになります。 構造化ストリーミングの詳細については、Apache.org の「[Structured Streaming Programming Guide](https://spark.apache.org/docs/2.2.0/structured-streaming-programming-guide.html)」(構造化ストリーミングのプログラミング ガイド) をご覧ください。

> [!IMPORTANT]  
> この例では、HDInsight 3.6 上で Spark 2.2 を使用しました。
>
> このドキュメントの手順では、HDInsight の Spark クラスターと HDInsight の Kafka クラスターの両方を含む Azure リソース グループを作成します。 これらのクラスターは両方とも、Spark クラスターが Kafka クラスターと直接通信できるように、Azure Virtual Network 内に配置します。
>
> このドキュメントの手順を完了したら、余分に課金されないようにするためにクラスターは削除してください。

## <a name="create-the-clusters"></a>クラスターの作成

HDInsight の Apache Kafka では、パブリック インターネットを介した Kafka ブローカーへのアクセスは提供されていません。 Kafka と通信するすべてのものは、Kafka クラスター内のノードと同じ Azure 仮想ネットワークに存在している必要があります。 この例では、Kafka クラスターと Spark クラスターの両方を Azure 仮想ネットワーク内に配置します。 次の図に、クラスター間の通信フローを示します。

![Azure 仮想ネットワークにおける Spark クラスターと Kafka クラスターの図](./media/apache-kafka-spark-structured-streaming-cosmosdb/apache-spark-kafka-vnet.png)

> [!NOTE]  
> Kafka サービスは、仮想ネットワーク内の通信に制限されます。 SSH や Ambari など、クラスター上の他のサービスは、インターネット経由でアクセスできます。 HDInsight で使用できるパブリック ポートの詳細については、「[HDInsight で使用されるポートと URI](hdinsight-hadoop-port-settings-for-services.md)」を参照してください。

Azure 仮想ネットワーク、Kafka、および Spark クラスターは手動で作成できますが、Azure Resource Manager テンプレートを使用する方が簡単です。 次の手順に従って、Azure 仮想ネットワーク、Kafka クラスター、および Spark クラスターを Azure サブスクリプションにデプロイします。

1. 次のボタンを使用して Azure にサインインし、Azure Portal でテンプレートを開きます。

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-spark-scala-kafka-cosmosdb%2Fmaster%2Fazuredeploy.json" target="_blank">
    <img src="./media/apache-kafka-spark-structured-streaming-cosmosdb/resource-manager-deploy.png" alt="Deploy to Azure"/>
    </a>

    Azure Resource Manager テンプレートは、このプロジェクトの GitHub リポジトリにあります ([https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb))。

    このテンプレートでは、次のリソースを作成します。

   * HDInsight 3.6 クラスター上の Kafka。

   * HDInsight 3.6 クラスター上の Spark

   * Azure Virtual Network (HDInsight クラスターを含む)

       > [!NOTE]  
       > テンプレートによって作成された仮想ネットワークは、10.0.0.0/16 アドレス空間を使用します。

   * Azure Cosmos DB: SQL API データベース

     > [!IMPORTANT]  
     > この例で使用する構造化ストリーミングのノートブックでは、HDInsight 3.6 上に Spark が必要です。 HDInsight 上で以前のバージョンの Spark を使用している場合は、ノートブックを使用するとエラーを受信します。

2. 以下の情報を使用して、 **[カスタム デプロイ]** セクションに各エントリを入力します。

    ![HDInsight のカスタム デプロイ値](./media/apache-kafka-spark-structured-streaming-cosmosdb/hdi-custom-parameters.png)

    * **サブスクリプション**:Azure サブスクリプションを選択します。

    * **リソース グループ**: グループを作成するか、または既存のグループを選択します。 このグループに HDInsight クラスターが含まれます。

    * **場所**: 地理的に近い場所を選択します。

    * **Cosmos DB アカウント名**: この値が、Cosmos DB アカウントの名前として使用されます。

    * **Base Cluster Name (ベース クラスター名)** : この値は、Spark クラスターと Kafka クラスターのベース名として使用されます。 たとえば、「**myhdi**」と入力すると、__spark-myhdi__ という名前の Spark クラスターと、**kafka-myhdi** という名前の Kafka クラスターが作成されます。

    * **クラスターのバージョン**: HDInsight クラスターのバージョン。

        > [!IMPORTANT]  
        > この例は HDInsight 3.6 でテストされており、その他の種類のクラスターでは機能しないことがあります。

    * **Cluster Login User Name (クラスター ログイン ユーザー名)** : Spark クラスターと Kafka クラスターの管理者のユーザー名。

    * **クラスター ログイン パスワード**: Spark クラスターと Kafka クラスターの管理者のユーザー パスワード。

    * **SSH ユーザー名**: Spark クラスターと Kafka クラスターの作成に使用する SSH ユーザー。

    * **SSH パスワード**: Spark クラスターと Kafka クラスター用の SSH ユーザーのパスワード。

3. **使用条件**を読み、 **[上記の使用条件に同意する]** をオンにします。

4. 最後に、 **[購入]** を選択します。 クラスターの作成には約 20 分かかります。

> [!IMPORTANT]  
> クラスター、仮想ネットワーク、および Cosmos DB アカウントを作成するまで、最大で 45 分間かかる場合があります。

## <a name="create-the-cosmos-db-database-and-collection"></a>Cosmos DB データベースとコレクションを作成する

このドキュメントで使用するプロジェクトでは、データを Cosmos DB に格納します。 コードを実行する前に、お使いの Cosmos DB インスタンス内に、まず _データベース_ と _コレクション_ を作成する必要があります。 ドキュメントのエンドポイントと、Cosmos DB に対する要求の認証に使用される _キー_ も取得する必要があります。 

これを実行する方法の 1 つとして、[Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) を使用します。 次のスクリプトでは、`kafkadata` という名前のデータベースと `kafkacollection` という名前のコレクションを作成します。 その後、プライマリ キーが返されます。

```azurecli
#!/bin/bash

# Replace 'myresourcegroup' with the name of your resource group
resourceGroupName='myresourcegroup'
# Replace 'mycosmosaccount' with the name of your Cosmos DB account name
name='mycosmosaccount'

# WARNING: If you change the databaseName or collectionName
#          then you must update the values in the Jupyter notebook
databaseName='kafkadata'
collectionName='kafkacollection'

# Create the database
az cosmosdb database create --name $name --db-name $databaseName --resource-group $resourceGroupName
# Create the collection
az cosmosdb collection create --collection-name $collectionName --name $name --db-name $databaseName --resource-group $resourceGroupName

# Get the endpoint
az cosmosdb show --name $name --resource-group $resourceGroupName --query documentEndpoint

# Get the primary key
az cosmosdb list-keys --name $name --resource-group $resourceGroupName --query primaryMasterKey
```

ドキュメントのエンドポイントとプライマリ キーの情報は、次のテキストのようになっています。

```text
# endpoint
"https://mycosmosaccount.documents.azure.com:443/"
# key
"YqPXw3RP7TsJoBF5imkYR0QNA02IrreNAlkrUMkL8EW94YHs41bktBhIgWq4pqj6HCGYijQKMRkCTsSaKUO2pw=="
```

> [!IMPORTANT]  
> Jupyter Notebook で必要とされるため、エンドポイントとキーの値を保存します。

## <a name="get-the-apache-kafka-brokers"></a>Apache Kafka ブローカーを取得します

この例のコードは、Kafka クラスターにある Kafka ブローカー ホストに接続します。 2 つの Kafka ブローカー ホストのアドレスを検出するには、以下の PowerShell または Bash の例を使います。

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
$clusterName = Read-Host -Prompt "Enter the Kafka cluster name"
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER" `
    -Credential $creds `
    -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$brokerHosts = $respObj.host_components.HostRoles.host_name[0..1]
($brokerHosts -join ":9092,") + ":9092"
```

> [!NOTE]  
> Bash の例では、`$CLUSTERNAME` に Kafka クラスターの名前に含めることが前提となります。
>
> この例では、[jq](https://stedolan.github.io/jq/) ユーティリティを使って JSON ドキュメントからのデータを解析します。

```bash
curl -u admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER" | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2
```

プロンプトが表示されたら、クラスター ログイン (管理者) アカウントのパスワードを入力します。

出力は次のテキストのようになります。

`wn0-kafka.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net:9092,wn1-kafka.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net:9092`

このドキュメントの以降のセクションで使用するため、この情報を保存してください。

## <a name="get-the-notebooks"></a>ノートブックを取得する

このドキュメントで説明した例のコードは、[https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb) で入手できます。

## <a name="upload-the-notebooks"></a>ノートブックをアップロードする

プロジェクトから HDInsight クラスター上の Spark へノートブックをアップロードするには、以下の手順を使用します。

1. Web ブラウザーで、Spark クラスターの Jupyter Notebook に接続します。 次の URL の`CLUSTERNAME` をお使いの __Spark__ クラスターの名前に置き換えます。

        https://CLUSTERNAME.azurehdinsight.net/jupyter

    プロンプトが表示されたら、クラスターの作成時に使用したログイン (管理者) パスワードを入力します。

2. ページの右上隅の __[アップロード]__ ボタンを使用して、__Stream-taxi-data-to-kafka.ipynb__ ファイルをクラスターにアップロードします。 __[開く]__ を選択して、アップロードを開始します。

3. ノートブックの一覧で __Stream-taxi-data-to-kafka.ipynb__ エントリを検索し、横にある __[アップロード]__ ボタンを選択します。

4. 手順 1. ～ 3. を繰り返して、__Stream-data-from-Kafka-to-Cosmos-DB.ipynb__ ノートブックを読み込みます。

## <a name="load-taxi-data-into-kafka"></a>タクシー データを Kafka に読み込む

ファイルをアップロードした後、__Stream-taxi-data-to-kafka.ipynb__ エントリを選択してノートブックを開きます。 ノートブックの手順に従って Kafka にデータを読み込みます。

## <a name="process-taxi-data-using-spark-structured-streaming"></a>Spark 構造化ストリーミングを使用してタクシー データを処理する

[Jupyter Notebook](https://jupyter.org/) のホーム ページから、__Stream-data-from-Kafka-to-Cosmos-DB.ipynb__ エントリを選択します。 ノートブックの手順に従い、Spark 構造化ストリーミングを使って Kafka から Azure Cosmos DB にデータをストリーミングします。

## <a name="next-steps"></a>次の手順

この記事では、Apache Spark 構造化ストリームの使用方法を説明しました。Apache Spark、Apache Kafka、および Azure Cosmos DB の操作に関する詳細については、以下のドキュメントをご覧ください。

* [Apache kafka を使った Apache Spark ストリーミング (DStream) の使用方法](hdinsight-apache-spark-with-kafka.md)。
* [HDInsight で Jupyter Notebook と Apache Spark を使い始める](spark/apache-spark-jupyter-spark-sql.md)
* [Azure Cosmos DB の概要](../cosmos-db/introduction.md)
