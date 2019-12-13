---
title: Azure Data Factory でサポートされるコンピューティング環境
description: Azure Data Factory パイプライン (Azure HDInsight など) でデータの変換または処理に使うことができるコンピューティング環境について説明します。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/10/2019
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.openlocfilehash: af4f069e9021a301a77ccefa300ad86afb955fcf
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74927043"
---
# <a name="compute-environments-supported-by-azure-data-factory"></a>Azure Data Factory でサポートされるコンピューティング環境
この記事では、データの処理または変換に利用できるさまざまなコンピューティング環境について説明します。 これらのコンピューティング環境を Azure Data Factory にリンクする「リンクされたサービス」の構成時に Data Factory でサポートされるさまざまな構成 (オンデマンドと Bring Your Own の比較) に関する詳細も提供します。

次の表は、Data Factory でサポートされているコンピューティング環境と、その環境で実行できるアクティビティの一覧です。 

| Compute 環境                                          | activities                                                   |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [On-demand HDInsight クラスター](#azure-hdinsight-on-demand-linked-service)または[独自の HDInsight クラスター](#azure-hdinsight-linked-service) | [Hive](transform-data-using-hadoop-hive.md)、[Pig](transform-data-using-hadoop-pig.md)、[Spark](transform-data-using-spark.md)、[MapReduce](transform-data-using-hadoop-map-reduce.md)、[Hadoop Streaming](transform-data-using-hadoop-streaming.md) |
| [Azure Batch](#azure-batch-linked-service)                   | [カスタム](transform-data-using-dotnet-custom-activity.md)     |
| [Azure Machine Learning Studio](#azure-machine-learning-studio-linked-service) | [Machine Learning アクティビティ:バッチ実行とリソースの更新](transform-data-using-machine-learning.md) |
| [Azure Machine Learning](#azure-machine-learning-linked-service) | [Azure Machine Learning 実行パイプライン](transform-data-machine-learning-service.md) |
| [Azure Machine Learning](#azure-machine-learning-linked-service) | [Azure Machine Learning 実行パイプライン](transform-data-machine-learning-service.md) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics-linked-service) | [Data Lake Analytics U-SQL](transform-data-using-data-lake-analytics.md) |
| [Azure SQL](#azure-sql-database-linked-service)、[Azure SQL Data Warehouse](#azure-sql-data-warehouse-linked-service)、[SQL Server](#sql-server-linked-service) | [ストアド プロシージャ](transform-data-using-stored-procedure.md) |
| [Azure Databricks](#azure-databricks-linked-service)         | [Notebook](transform-data-databricks-notebook.md)、[Jar](transform-data-databricks-jar.md)、[Python](transform-data-databricks-python.md) |
| [Azure 関数](#azure-function-linked-service)         | [Azure 関数アクティビティ](control-flow-azure-function-activity.md)
>  

## <a name="on-demand-hdinsight-compute-environment"></a>オンデマンドの HDInsight コンピューティング環境
この種類の構成では、コンピューティング環境は Azure Data Factory サービスにより完全管理されます。 データを処理するためのジョブが送信される前に Data Factory サービスにより自動的に作成され、ジョブの完了時に削除されます。 オンデマンドのコンピューティング環境のために「リンクされたサービス」を作成し、構成し、ジョブ実行、クラスター管理、ブートストラップ アクションの詳細設定を制御できます。

> [!NOTE]
> オンデマンド構成は現在のところ、Azure HDInsight クラスターにのみ対応しています。 Azure Databricks は、ジョブ クラスターを使用したオンデマンド ジョブもサポートしています。詳細については、「[Azure Databricks のリンクされたサービス](#azure-databricks-linked-service)」を参照してください。

## <a name="azure-hdinsight-on-demand-linked-service"></a>Azure HDInsight のオンデマンドのリンクされたサービス
Azure Data Factory サービスは、データを処理するためのオンデマンド HDInsight クラスターを自動的に作成します。 このクラスターはクラスターに関連付けられているストレージ アカウント (JSON の linkedServiceName プロパティ) と同じリージョンで作成されます。 ストレージ アカウントは、汎用の標準的な Azure ストレージ アカウントである必要があります。 

オンデマンド HDInsight のリンクされたサービスに関する次の **重要な** 点に注意してください。

* オンデマンド HDInsight クラスターは Azure サブスクリプションのもとで作成されます。 クラスターが稼働している場合、Azure ポータルでクラスターを確認できます。 
* オンデマンド HDInsight クラスターで実行されるジョブのログは HDInsight クラスターに関連付けられているストレージ アカウントにコピーされます。 リンクされたサービス定義で定義されている ClusterUserName、clusterPassword、clusterSshUserName、clusterSshPassword は、クラスターのライフサイクル中に詳細なトラブルシューティングのためにクラスターにログインするときに使用されます。 
* HDInsight クラスターが稼動し、ジョブを実行している時間に対してのみ課金されます。
* HDInsight のオンデマンドのリンクされたサービスでは、**スクリプト操作**を使用できません。  

> [!IMPORTANT]
> オンデマンドで Azure HDInsight クラスターをプロビジョニングするには一般的に **20 分**以上かかります。

### <a name="example"></a>例
次の JSON は、Linux ベースのオンデマンド HDInsight のリンクされたサービスを定義します。 Data Factory サービスは、必要なアクティビティを処理するために、**Linux ベースの** HDInsight クラスターを自動的に作成します。 

```json
{
  "name": "HDInsightOnDemandLinkedService",
  "properties": {
    "type": "HDInsightOnDemand",
    "typeProperties": {
      "clusterType": "hadoop",
      "clusterSize": 1,
      "timeToLive": "00:15:00",
      "hostSubscriptionId": "<subscription ID>",
      "servicePrincipalId": "<service principal ID>",
      "servicePrincipalKey": {
        "value": "<service principal key>",
        "type": "SecureString"
      },
      "tenant": "<tenent id>",
      "clusterResourceGroup": "<resource group name>",
      "version": "3.6",
      "osType": "Linux",
      "linkedServiceName": {
        "referenceName": "AzureStorageLinkedService",
        "type": "LinkedServiceReference"
      }
    },
    "connectVia": {
      "referenceName": "<name of Integration Runtime>",
      "type": "IntegrationRuntimeReference"
    }
  }
}
```

> [!IMPORTANT]
> HDInsight クラスターは、JSON (**linkedServiceName**) で指定した BLOB ストレージに**既定のコンテナー**を作成します。 クラスターを削除しても、HDInsight はこのコンテナーを削除しません。 この動作は仕様です。 オンデマンド HDInsight のリンクされたサービスでは、既存のライブ クラスター (**timeToLive**) がある場合を除き、スライスを処理する必要があるたびに HDInsight クラスターが作成され、処理が終了すると削除されます。 
>
> 実行するアクティビティが多いほど、Azure BLOB ストレージ内のコンテナーも増えます。 ジョブのトラブルシューティングのためにコンテナーが必要ない場合、コンテナーを削除してストレージ コストを削減できます。 これらのコンテナーの名前は、`adf**yourdatafactoryname**-**linkedservicename**-datetimestamp` 形式になります。 Azure BLOB ストレージ内のコンテナーを削除するには、 [Microsoft ストレージ エクスプローラー](https://storageexplorer.com/) などのツールを使用します。
>
> 

### <a name="properties"></a>properties
| プロパティ                     | 説明                              | 必須 |
| ---------------------------- | ---------------------------------------- | -------- |
| type                         | type プロパティは **HDInsightOnDemand**に設定されます。 | はい      |
| clusterSize                  | クラスター内の worker/データ ノードの数です。 このプロパティで指定した worker ノード数と共に 2 つのヘッド ノードを使用して HDInsight クラスターが作成されます。 ノードのサイズは Standard_D3 (4 コア) であるため、4 worker ノード クラスターのコアは 24 個になります (worker ノード用に 4\*4 = 16 個のコアと、ヘッド ノード用に 2\*4 = 8 個のコア)。 詳細については、[Hadoop、Spark、Kafka などの HDInsight クラスターのセットアップ](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)に関する記事を参照してください。 | はい      |
| linkedServiceName            | データを保存し、処理するためにオンデマンド クラスターで使用される Azure Storage のリンクされたサービスです。 HDInsight クラスターは、この Azure Storage アカウントと同じリージョンに作成されます。 Azure HDInsight には、サポートされる各 Azure リージョンで使用できるコアの合計数に制限があります。 必要な clusterSize を満たせるだけ十分なコア クォータが、その Azure リージョンに存在することを確認してください。 詳細については、[Hadoop、Spark、Kafka などの HDInsight クラスターのセットアップ](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)に関する記事を参照してください<p>現時点では、Azure Data Lake Store をストレージとして使用するオンデマンド HDInsight クラスターを作成することはできません。 HDInsight 処理の結果データを Azure Data Lake Store に保存する必要がある場合は、コピー アクティビティを使用して、Azure Blob Storage から Azure Data Lake Store にデータをコピーします。 </p> | はい      |
| clusterResourceGroup         | HDInsight クラスターは、このリソース グループに作成されます。 | はい      |
| timetolive                   | オンデマンド HDInsight クラスターに許可されるアイドル時間です。 他のアクティブなジョブがクラスターにない場合、アクティビティ実行の完了後にオンデマンド HDInsight クラスターが起動状態を維持する時間を指定します。 最小許容値は 5 分 (00:05:00) です。<br/><br/>たとえば、アクティビティ実行に 6 分かかるときに timetolive が 5 分に設定されている場合、アクティビティ実行の 6 分間の処理の後、クラスターが起動状態を 5 分間維持します。 別のアクティビティ実行が 6 分の時間枠で実行される場合、それは同じクラスターで処理されます。<br/><br/>オンデマンド HDInsight クラスターの作成は高額な作業であり (時間もかかることがあります)、オンデマンド HDInsight クラスターを再利用し、Data Factory のパフォーマンスを改善する必要がある場合にこの設定を利用します。<br/><br/>timetolive 値を 0 に設定した場合、アクティビティ実行の完了直後にクラスターが削除されます。 ただし、高い値を設定した場合、クラスターは、何らかのトラブルシューティングの目的でログオンできるようにアイドル状態を維持できますが、その結果コストが高くなる可能性があります。 そのため、ニーズに合わせて適切な値を設定することが重要です。<br/><br/>timetolive プロパティ値が適切に設定されている場合、複数のパイプラインでオンデマンド HDInsight クラスターのインスタンスを共有できます。 | はい      |
| clusterType                  | 作成する HDInsight クラスターの種類。 許可される値は "hadoop" および "spark" です。 指定しない場合は、既定値の hadoop が使用されます。 Enterprise セキュリティ パッケージ対応のクラスターをオンデマンドで作成することはできません。代わりに[既存のクラスターを使用するか、自分のコンピューティングを持ち込んでください](#azure-hdinsight-linked-service)。 | いいえ       |
| version                      | HDInsight クラスターのバージョン。 指定しない場合、現在の HDInsight 定義の既定バージョンを使用します。 | いいえ       |
| hostSubscriptionId           | HDInsight クラスターを作成するために使用する Azure サブスクリプション ID です。 指定されていない場合は、Azure のログイン コンテキストのサブスクリプション ID を使用します。 | いいえ       |
| clusterNamePrefix           | HDI クラスター名のプレフィックス (クラスター名の末尾にタイムスタンプが自動的に付加されます)| いいえ       |
| sparkVersion                 | クラスターの種類が "Spark" の場合の Spark のバージョンです | いいえ       |
| additionalLinkedServiceNames | Data Factory サービスがあなたの代わりに登録できるように、HDInsight の「リンクされたサービス」の追加ストレージ アカウントを指定します。 これらのストレージ アカウントは、linkedServiceName で指定されたストレージ アカウントと同じリージョンに作成されている HDInsight クラスターと同じリージョンにある必要があります。 | いいえ       |
| osType                       | オペレーティング システムの種類。 使用できる値は、以下のとおりです。Linux と Windows (HDInsight 3.3 の場合のみ)。 既定値は Linux です。 | いいえ       |
| hcatalogLinkedServiceName    | HCatalog データベースを指す Azure SQL のリンクされたサービスの名前。 オンデマンド HDInsight クラスターは、Azure SQL データベースを metastore として使用して作成されます。 | いいえ       |
| connectVia                   | この HDInsight リンク サービスにアクティビティをディスパッチするために使用される統合ランタイムです。 オンデマンド HDInsight リンク サービスの場合、Azure 統合ランタイムだけをサポートします。 指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 | いいえ       |
| clusterUserName                   | クラスターにアクセスするユーザー名。 | いいえ       |
| clusterPassword                   | クラスターにアクセスするセキュリティで保護された文字列の種類のパスワード。 | いいえ       |
| clusterSshUserName         | クラスターのノードにリモートで接続する SSH のユーザー名 (Linux の場合)。 | いいえ       |
| clusterSshPassword         | クラスターのノードにリモートで接続する SSH のセキュリティで保護された文字列の種類のパスワード (Linux の場合)。 | いいえ       |
| scriptActions | オンデマンド クラスター作成中に [HDInsight クラスターのカスタマイズ](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux)のスクリプトを指定します。 <br />現在、Azure Data Factory のユーザー インターフェイス作成ツールは 1 つのスクリプト操作のみの指定をサポートしていますが、JSON でこの制限に対応できます (JSON で複数のスクリプト操作を指定できます)。 | いいえ |


> [!IMPORTANT]
> HDInsight は、デプロイできる Hadoop クラスター バージョンを複数サポートしています。 各バージョンを選択すると、特定のバージョンの Hortonworks Data Platform (HDP) ディストリビューションと、そのディストリビューションに含まれるコンポーネントが作成されます。 HDInsight のサポートされるバージョンの一覧を常に更新して、最新の Hadoop エコシステム コンポーネントと修正プログラムを提供しています。 HDInsight のサポートされているバージョンを確実に使用するために、[HDInsight のサポートされているバージョンと OS の種類](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions)に関する最新情報を常に参照してください。 
>
> [!IMPORTANT]
> 現在、HDInsight のリンクされたサービスは、HBase、Interactive Query (Hive LLAP)、Storm をサポートしていません。 
>
> 

#### <a name="additionallinkedservicenames-json-example"></a>additionalLinkedServiceNames JSON の例

```json
"additionalLinkedServiceNames": [{
    "referenceName": "MyStorageLinkedService2",
    "type": "LinkedServiceReference"          
}]
```

### <a name="service-principal-authentication"></a>サービス プリンシパルの認証

オンデマンドの HDInsight リンク サービスでは、ユーザーに代わって HDInsight クラスターを作成するためにサービス プリンシパル認証が必要になります。 サービス プリンシパルの認証を使用するには、Azure Active Directory (Azure AD) でアプリケーション エンティティを登録し、サブスクリプションの、または HDInsight クラスターが作成されるリソース グループの**共同作成者**のロールを付与します。 詳細な手順については、[ポータルを使用した、リソースにアクセスできる Azure Active Directory アプリケーションとサービス プリンシパルの作成](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)に関する記事を参照してください。 次の値を記録しておきます。リンクされたサービスを定義するときに使います。

- アプリケーション ID
- アプリケーション キー 
- テナント ID

次のプロパティを指定して、サービス プリンシパル認証を使います。

| プロパティ                | 説明                              | 必須 |
| :---------------------- | :--------------------------------------- | :------- |
| **servicePrincipalId**  | アプリケーションのクライアント ID を取得します。     | はい      |
| **servicePrincipalKey** | アプリケーションのキーを取得します。           | はい      |
| **tenant**              | アプリケーションが存在するテナントの情報 (ドメイン名またはテナント ID) を指定します。 Azure Portal の右上隅をマウスでポイントすることにより取得できます。 | はい      |

### <a name="advanced-properties"></a>高度なプロパティ

次のプロパティを指定し、オンデマンド HDInsight クラスターを詳細に設定することもできます。

| プロパティ               | 説明                              | 必須 |
| :--------------------- | :--------------------------------------- | :------- |
| coreConfiguration      | 作成する HDInsight クラスターに core 構成パラメーター (core-site.xml と同じ) を指定します。 | いいえ       |
| hBaseConfiguration     | HDInsight クラスターに HBase 構成パラメーター (hbase-site.xml) を指定します。 | いいえ       |
| hdfsConfiguration      | HDInsight クラスターに HDFS 構成パラメーター (hdfs-site.xml) を指定します。 | いいえ       |
| hiveConfiguration      | HDInsight クラスターに hive 構成パラメーター (hive-site.xml) を指定します。 | いいえ       |
| mapReduceConfiguration | HDInsight クラスターに MapReduce 構成パラメーター (mapred-site.xml) を指定します。 | いいえ       |
| oozieConfiguration     | HDInsight クラスターに Oozie 構成パラメーター (oozie-site.xml) を指定します。 | いいえ       |
| stormConfiguration     | HDInsight クラスターに Storm 構成パラメーター (storm-site.xml) を指定します。 | いいえ       |
| yarnConfiguration      | HDInsight クラスターに Yarn 構成パラメーター (yarn-site.xml) を指定します。 | いいえ       |

#### <a name="example--on-demand-hdinsight-cluster-configuration-with-advanced-properties"></a>例 – オンデマンド HDInsight クラスターと詳細なプロパティ

```json
{
    "name": " HDInsightOnDemandLinkedService",
    "properties": {
      "type": "HDInsightOnDemand",
      "typeProperties": {
          "clusterSize": 16,
          "timeToLive": "01:30:00",
          "hostSubscriptionId": "<subscription ID>",
          "servicePrincipalId": "<service principal ID>",
          "servicePrincipalKey": {
            "value": "<service principal key>",
            "type": "SecureString"
          },
          "tenant": "<tenent id>",
          "clusterResourceGroup": "<resource group name>",
          "version": "3.6",
          "osType": "Linux",
          "linkedServiceName": {
              "referenceName": "AzureStorageLinkedService",
              "type": "LinkedServiceReference"
            },
            "coreConfiguration": {
                "templeton.mapper.memory.mb": "5000"
            },
            "hiveConfiguration": {
                "templeton.mapper.memory.mb": "5000"
            },
            "mapReduceConfiguration": {
                "mapreduce.reduce.java.opts": "-Xmx4000m",
                "mapreduce.map.java.opts": "-Xmx4000m",
                "mapreduce.map.memory.mb": "5000",
                "mapreduce.reduce.memory.mb": "5000",
                "mapreduce.job.reduce.slowstart.completedmaps": "0.8"
            },
            "yarnConfiguration": {
                "yarn.app.mapreduce.am.resource.mb": "5000",
                "mapreduce.map.memory.mb": "5000"
            },
            "additionalLinkedServiceNames": [{
                "referenceName": "MyStorageLinkedService2",
                "type": "LinkedServiceReference"          
            }]
        }
    },
      "connectVia": {
      "referenceName": "<name of Integration Runtime>",
      "type": "IntegrationRuntimeReference"
    }
}
```

### <a name="node-sizes"></a>ノードのサイズ
次のプロパティを使用して、ヘッド ノード、データ ノード、Zookeeper ノードのサイズを指定できます。 

| プロパティ          | 説明                              | 必須 |
| :---------------- | :--------------------------------------- | :------- |
| headNodeSize      | ヘッド ノードのサイズを指定します。 既定値はStandard_D3 です。 詳細については、「**ノードのサイズの指定**」をご覧ください。 | いいえ       |
| dataNodeSize      | データ ノードのサイズを指定します。 既定値はStandard_D3 です。 | いいえ       |
| zookeeperNodeSize | Zookeeper ノードのサイズを指定します。 既定値はStandard_D3 です。 | いいえ       |

#### <a name="specifying-node-sizes"></a>ノードのサイズの指定
前のセクションで説明したプロパティで指定する必要がある文字列値については、[仮想マシンのサイズ](../virtual-machines/linux/sizes.md)に関する記事を参照してください。 値は、この記事に記載されている**コマンドレットと API** に準拠する必要があります。 この記事に示すように、Large (既定値) サイズのデータ ノードのメモリ容量は 7 GB ですが、シナリオによってはこれでは不十分な場合があります。 

D4 サイズのヘッド ノードとワーカー ノードを作成する場合は、headNodeSize プロパティと dataNodeSize プロパティの値として **Standard_D4** を指定します。 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

これらのプロパティに間違った値を指定すると、次のエラーが発生します。**エラー:** クラスターを作成できませんでした。 例外:クラスター作成操作を完了できません。 コード '400' で操作が失敗しました。 取り残されたクラスターの状態:'Error'。 メッセージ:'PreClusterCreationValidationFailure'。 このエラーが発生した場合は、[仮想マシンのサイズ](../virtual-machines/linux/sizes.md)に関する記事の表に記載されている**コマンドレットと API** の名前を使用していることを確認してください。        

## <a name="bring-your-own-compute-environment"></a>Bring Your Own のコンピューティング環境
この種類の構成では、既存のコンピューティング環境を Data Factory の「リンクされたサービス」として登録できます。 このコンピューティング環境はユーザーにより管理され、Data Factory サービスをこの環境を利用し、アクティビティを実行します。

この種類の構成は次のコンピューティング環境でサポートされています。

* Azure HDInsight
* Azure Batch
* Azure Machine Learning
* Azure Data Lake Analytics
* Azure SQL DB、Azure SQL DW、SQL Server

## <a name="azure-hdinsight-linked-service"></a>Azure HDInsight のリンクされたサービス
Azure HDInsight の「リンクされたサービス」を作成し、独自の HDInsight クラスターを Data Factory に登録できます。

### <a name="example"></a>例

```json
{
    "name": "HDInsightLinkedService",
    "properties": {
      "type": "HDInsight",
      "typeProperties": {
        "clusterUri": " https://<hdinsightclustername>.azurehdinsight.net/",
        "userName": "username",
        "password": {
            "value": "passwordvalue",
            "type": "SecureString"
          },
        "linkedServiceName": {
              "referenceName": "AzureStorageLinkedService",
              "type": "LinkedServiceReference"
        }
      },
      "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
      }
    }
  }
```

### <a name="properties"></a>properties
| プロパティ          | 説明                                                  | 必須 |
| ----------------- | ------------------------------------------------------------ | -------- |
| type              | type プロパティは **HDInsight**に設定されます。            | はい      |
| clusterUri        | HDInsight クラスターの URI です。                            | はい      |
| username          | 既存の HDInsight クラスターに接続するために使用されるユーザーの名前を指定します。 | はい      |
| password          | ユーザー アカウントのパスワードを指定します。                       | はい      |
| linkedServiceName | HDInsight クラスターで使われる Azure Blob Storage を参照する Azure Storage のリンクされたサービスの名前です。 <p>現在は、Azure Data Lake Store のリンクされたサービスをこのプロパティに指定することはできません。 HDInsight クラスターが Data Lake Store にアクセスできる場合、Hive/Pig スクリプトから Azure Data Lake Store 内のデータにアクセスできます。 </p> | はい      |
| isEspEnabled      | HDInsight クラスターが [Enterprise セキュリティ パッケージ](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-architecture)対応の場合は、'*true*' を指定します。 既定値は '*false*' です。 | いいえ       |
| connectVia        | このリンク サービスにアクティビティをディスパッチするために使用される統合ランタイムです。 Azure 統合ランタイムまたは自己ホスト型統合ランタイムを使用することができます。 指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 <br />Enterprise セキュリティ パッケージ (ESP) 対応の HDInsight クラスターには、クラスターへの通信経路があるセルフホステッド統合ランタイムを使用するか、ESP HDInsight クラスターと同じ仮想ネットワーク内にデプロイする必要があります。 | いいえ       |

> [!IMPORTANT]
> HDInsight は、デプロイできる Hadoop クラスター バージョンを複数サポートしています。 各バージョンを選択すると、特定のバージョンの Hortonworks Data Platform (HDP) ディストリビューションと、そのディストリビューションに含まれるコンポーネントが作成されます。 HDInsight のサポートされるバージョンの一覧を常に更新して、最新の Hadoop エコシステム コンポーネントと修正プログラムを提供しています。 HDInsight のサポートされているバージョンを確実に使用するために、[HDInsight のサポートされているバージョンと OS の種類](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions)に関する最新情報を常に参照してください。 
>
> [!IMPORTANT]
> 現在、HDInsight のリンクされたサービスは、HBase、Interactive Query (Hive LLAP)、Storm をサポートしていません。 
>
> 

## <a name="azure-batch-linked-service"></a>Azure Batch のリンクされたサービス

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure Batch の「リンクされたサービス」を作成し、仮想マシン (VM) の Batch プールを Data Factory に登録できます。 Azure Batch を使用してカスタム アクティビティを実行することができます。

Azure Batch サービスを初めて利用する場合は、次の記事をご覧ください。

* [Azure Batch の基本](../batch/batch-technical-overview.md) 」をご覧ください。
* Azure Batch アカウントの作成方法については、[New-AzBatchAccount](/powershell/module/az.batch/New-azBatchAccount) コマンドレットをご覧ください。Azure portal を使用した Azure Batch アカウントの作成方法については、[Azure portal](../batch/batch-account-create-portal.md) をご覧ください。 このコマンドレットの使用方法の詳細については、[PowerShell を使用した Azure Batch アカウントの管理](https://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx)に関する記事をご覧ください。
* Azure Batch プールの作成方法については、[New-AzBatchPool](/powershell/module/az.batch/New-AzBatchPool) コマンドレットをご覧ください。

### <a name="example"></a>例

```json
{
    "name": "AzureBatchLinkedService",
    "properties": {
      "type": "AzureBatch",
      "typeProperties": {
        "accountName": "batchaccount",
        "accessKey": {
          "type": "SecureString",
          "value": "access key"
        },
        "batchUri": "https://batchaccount.region.batch.azure.com",
        "poolName": "poolname",
        "linkedServiceName": {
          "referenceName": "StorageLinkedService",
          "type": "LinkedServiceReference"
        }
      },
      "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
      }
    }
  }
```


### <a name="properties"></a>properties
| プロパティ          | 説明                              | 必須 |
| ----------------- | ---------------------------------------- | -------- |
| type              | type プロパティは **AzureBatch**に設定されます。 | はい      |
| accountName       | Azure Batch アカウントの名前です。         | はい      |
| accessKey         | Azure Batch アカウントのアクセス キーです。  | はい      |
| batchUri          | https://*batchaccountname.region*.batch.azure.com の形式の Azure Batch アカウントへの URL です。 | はい      |
| poolName          | 仮想マシンのプールの名前です。    | はい      |
| linkedServiceName | この Azure Batch の「リンクされたサービス」に関連付けられている Azure Storage の「リンクされたサービス」の名前です。 この「リンクされたサービス」はアクティビティの実行に必要なファイルのステージングに利用されます。 | はい      |
| connectVia        | このリンク サービスにアクティビティをディスパッチするために使用される統合ランタイムです。 Azure 統合ランタイムまたは自己ホスト型統合ランタイムを使用することができます。 指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 | いいえ       |

## <a name="azure-machine-learning-studio-linked-service"></a>Azure Machine Learning Studio のリンクされたサービス
Azure Machine Learning Studio の「リンクされたサービス」を作成し、Machine Learning のバッチ スコアリング エンドポイントを Data Factory に登録します。

### <a name="example"></a>例

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
      "type": "AzureML",
      "typeProperties": {
        "mlEndpoint": "https://[batch scoring endpoint]/jobs",
        "apiKey": {
            "type": "SecureString",
            "value": "access key"
        }
     },
     "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
      }
    }
}
```

### <a name="properties"></a>properties
| プロパティ               | 説明                              | 必須                                 |
| ---------------------- | ---------------------------------------- | ---------------------------------------- |
| 種類                   | type プロパティは次の値に設定されます。**AzureML**。 | はい                                      |
| mlEndpoint             | バッチ スコアリング URL です。                   | はい                                      |
| apiKey                 | 公開されたワークスペース モデルの API です。     | はい                                      |
| updateResourceEndpoint | トレーニング済みモデル ファイルを使用した予測 Web サービスの更新に使用される Azure Machine Learning Web サービス エンドポイントの更新リソース URL です | いいえ                                       |
| servicePrincipalId     | アプリケーションのクライアント ID を取得します。     | UpdateResourceEndpoint が指定されている場合は必須です |
| servicePrincipalKey    | アプリケーションのキーを取得します。           | UpdateResourceEndpoint が指定されている場合は必須です |
| tenant                 | アプリケーションが存在するテナントの情報 (ドメイン名またはテナント ID) を指定します。 Azure Portal の右上隅をマウスでポイントすることにより取得できます。 | UpdateResourceEndpoint が指定されている場合は必須です |
| connectVia             | このリンク サービスにアクティビティをディスパッチするために使用される統合ランタイムです。 Azure 統合ランタイムまたは自己ホスト型統合ランタイムを使用することができます。 指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 | いいえ                                       |

## <a name="azure-machine-learning-linked-service"></a>Azure Machine Learning のリンクされたサービス
Azure Machine Learning のリンクされたサービスを作成して、Azure Machine Learning ワークスペースをデータ ファクトリに接続します。

> [!NOTE]
> 現在、Azure Machine Learning のリンクされたサービスでは、サービス プリンシパル認証のみがサポートされています。

### <a name="example"></a>例

```json
{
    "name": "AzureMLServiceLinkedService",
    "properties": {
        "type": "AzureMLService",
        "typeProperties": {
            "subscriptionId": "subscriptionId",
            "resourceGroupName": "resourceGroupName",
            "mlWorkspaceName": "mlWorkspaceName",
            "servicePrincipalId": "service principal id",
            "servicePrincipalKey": {
                "value": "service principal key",
                "type": "SecureString"
            },
            "tenant": "tenant ID"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime?",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="properties"></a>properties
| プロパティ               | 説明                              | 必須                                 |
| ---------------------- | ---------------------------------------- | ---------------------------------------- |
| 種類                   | type プロパティは次の値に設定されます。**AzureMLService**。 | はい                                      |
| subscriptionId         | Azure サブスクリプション ID              | はい                                      |
| resourceGroupName      | 名前 | はい                                      |
| mlWorkspaceName        | Azure Machine Learning ワークスペースの名前 | はい  |
| servicePrincipalId     | アプリケーションのクライアント ID を取得します。     | いいえ |
| servicePrincipalKey    | アプリケーションのキーを取得します。           | いいえ |
| tenant                 | アプリケーションが存在するテナントの情報 (ドメイン名またはテナント ID) を指定します。 Azure Portal の右上隅をマウスでポイントすることにより取得できます。 | UpdateResourceEndpoint が指定されている場合は必須です | いいえ |
| connectVia             | このリンク サービスにアクティビティをディスパッチするために使用される統合ランタイムです。 Azure 統合ランタイムまたは自己ホスト型統合ランタイムを使用することができます。 指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 | いいえ |    

## <a name="azure-data-lake-analytics-linked-service"></a>Azure Data Lake Analytics リンク サービス
**Azure Data Lake Analytics** リンク サービスを作成して、Azure Data Lake Analytics コンピューティング サービスを Azure Data Factory にリンクします。 パイプラインの Data Lake Analytics U-SQL アクティビティは、このリンク サービスを参照します。 

### <a name="example"></a>例

```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "azuredatalakeanalytics URI",
            "servicePrincipalId": "service principal id",
            "servicePrincipalKey": {
                "value": "service principal key",
                "type": "SecureString"
            },
            "tenant": "tenant ID",
            "subscriptionId": "<optional, subscription ID of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="properties"></a>properties

| プロパティ             | 説明                              | 必須                                 |
| -------------------- | ---------------------------------------- | ---------------------------------------- |
| type                 | type プロパティは次の値に設定されます。**AzureDataLakeAnalytics**。 | はい                                      |
| accountName          | Azure Data Lake Analytics アカウント名。  | はい                                      |
| dataLakeAnalyticsUri | Azure Data Lake Analytics URI。           | いいえ                                       |
| subscriptionId       | Azure サブスクリプション ID                    | いいえ                                       |
| resourceGroupName    | Azure リソース グループ名                | いいえ                                       |
| servicePrincipalId   | アプリケーションのクライアント ID を取得します。     | はい                                      |
| servicePrincipalKey  | アプリケーションのキーを取得します。           | はい                                      |
| tenant               | アプリケーションが存在するテナントの情報 (ドメイン名またはテナント ID) を指定します。 Azure Portal の右上隅をマウスでポイントすることにより取得できます。 | はい                                      |
| connectVia           | このリンク サービスにアクティビティをディスパッチするために使用される統合ランタイムです。 Azure 統合ランタイムまたは自己ホスト型統合ランタイムを使用することができます。 指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 | いいえ                                       |



## <a name="azure-databricks-linked-service"></a>Azure Databricks のリンクされたサービス
**Azure Databricks のリンクされたサービス**を作成して、Databricks ワークロード (ノートブック、jar、phthon) の実行に使用する Databricks ワークスペースを登録できます。 
> [!IMPORTANT]
> Databricks のリンクされたサービスでは、[インスタンス プール](https://aka.ms/instance-pools)がサポートされています。 

### <a name="example---using-new-job-cluster-in-databricks"></a>例 - Databricks で新しいジョブ クラスターの使用

```json
{
    "name": "AzureDatabricks_LS",
    "properties": {
        "type": "AzureDatabricks",
        "typeProperties": {
            "domain": "https://eastus.azuredatabricks.net",
            "newClusterNodeType": "Standard_D3_v2",
            "newClusterNumOfWorker": "1:10",
            "newClusterVersion": "4.0.x-scala2.11",
            "accessToken": {
                "type": "SecureString",
                "value": "dapif33c9c721144c3a790b35000b57f7124f"
            }
        }
    }
}

```

### <a name="example---using-existing-interactive-cluster-in-databricks"></a>例 - Databricks で対話型の既存クラスターの使用

```json
{
    "name": " AzureDataBricksLinedService",
    "properties": {
      "type": " AzureDatabricks",
      "typeProperties": {
        "domain": "https://westeurope.azuredatabricks.net",
        "accessToken": {
            "type": "SecureString", 
            "value": "dapif33c9c72344c3a790b35000b57f7124f"
          },
        "existingClusterId": "{clusterId}"
        }
}

```

### <a name="properties"></a>properties

| プロパティ             | 説明                              | 必須                                 |
| -------------------- | ---------------------------------------- | ---------------------------------------- |
| 名前                 | リンクされたサービスの名前               | はい   |
| type                 | type プロパティは次の値に設定されます。**Azure Databricks**。 | はい                                      |
| domain               | Databricks ワークスペースのリージョンに基づき Azure リージョンを指定します。 例: https://eastus.azuredatabricks.net | はい                                 |
| accessToken          | Data Factory の Azure Databricks の認証にはアクセス トークンが必要です。 アクセス トークンは、Databricks ワークスペースから生成する必要があります。 アクセス トークンを見つける詳細な手順については、[こちら](https://docs.azuredatabricks.net/api/latest/authentication.html#generate-token)を参照してください。  | はい                                       |
| existingClusterId    | このすべてのジョブを実行する既存のクラスターのクラスター ID。 これは作成済みの対話型クラスターでなければなりません。 応答が停止した場合は、クラスターの手動再起動が必要になることがあります。 Databricks では、信頼性を高めるために新しいクラスターでジョブを実行することをお勧めします。 対話型クラスターのクラスター ID は Databricks ワークスペース -> クラスター -> 対話型クラスター名 -> 構成 -> タグで見つけることができます。 [詳細](https://docs.databricks.com/user-guide/clusters/tags.html) | いいえ 
| instancePoolId    | Databricks ワークスペース内の既存のプールのインスタンス プール ID。  | いいえ  |
| newClusterVersion    | クラスターの Spark バージョン。 Databricks にジョブ クラスターを作成します。 | いいえ  |
| newClusterNumOfWorker| このクラスターに属する worker ノードの数。 1 つのクラスターには 1 つの Spark ドライバーと num_workers Executor、全体では num_workers + 1 Spark ノードになります。 文字列は Int32 で設定されます。たとえば、"1" は、numOfWorker が 1 であること、"1:10" は、最小が 1 で最大が 10 の自動スケーリングを意味します。  | いいえ                |
| newClusterNodeType   | このフィールドは、単一の値を通じて使用されるリソースをこのクラスターのそれぞれの Spark ノードにエンコードします。 たとえば、Spark ノードはメモリまたはコンピューティング集約型ワークロード用にプロビジョニングされ、最適化されます。 このフィールドは、新しいクラスターに必須です。                | いいえ               |
| newClusterSparkConf  | 省略可能なユーザー指定の Spark 構成キーと値のペアのセット。 ユーザーは、余分な JVM オプションの文字列をドライバーと Executor にそれぞれ spark.driver.extraJavaOptions および spark.executor.extraJavaOptions を介して渡すこともできます。 | いいえ  |
| newClusterInitScripts| 新しいクラスター用のユーザーが定義した省略可能な初期化スクリプトのセット。 Init スクリプト DBFS への DBFS パスを指定します。 | いいえ  |


## <a name="azure-sql-database-linked-service"></a>Azure SQL Database のリンクされたサービス
Azure SQL のリンクされたサービスを作成し、 [ストアド プロシージャ アクティビティ](transform-data-using-stored-procedure.md) で使用して、Data Factory パイプラインからストアド プロシージャを起動します。 このリンクされたサービスの詳細については、 [Azure SQL コネクタ](connector-azure-sql-database.md#linked-service-properties) に関する記事を参照してください。

## <a name="azure-sql-data-warehouse-linked-service"></a>Azure SQL Data Warehouse のリンクされたサービス
Azure SQL Data Warehouse のリンクされたサービスを作成し、 [ストアド プロシージャ アクティビティ](transform-data-using-stored-procedure.md) で使用して、Data Factory パイプラインからストアド プロシージャを起動します。 このリンクされたサービスの詳細については、 [Azure SQL Data Warehouse コネクタ](connector-azure-sql-data-warehouse.md#linked-service-properties) に関する記事をご覧ください。

## <a name="sql-server-linked-service"></a>SQL Server のリンクされたサービス
SQL Server のリンクされたサービスを作成し、 [ストアド プロシージャ アクティビティ](transform-data-using-stored-procedure.md) で使用して、Data Factory パイプラインからストアド プロシージャを起動します。 このリンクされたサービスの詳細については、 [SQL Server コネクタ](connector-sql-server.md#linked-service-properties) に関する記事をご覧ください。

## <a name="azure-function-linked-service"></a>Azure 関数のリンクされたサービス
Azure 関数のリンクされたサービスを作成し、それを [Azure 関数アクティビティ](control-flow-azure-function-activity.md)と共に使用して、Data Factory パイプラインで Azure 関数を実行します。 Azure 関数の戻り値の型は、有効な `JObject` である必要があります。 ([JArray](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm) は `JObject` では "*ない*" ことに留意してください。)`JObject`以外の戻り値の型が失敗し、ユーザー エラー*応答コンテンツは有効な JObject ではない*が発生します。

| **プロパティ** | **説明** | **必須** |
| --- | --- | --- |
| type   | type プロパティは、次のように設定する必要があります:**AzureFunction** | はい |
| function app url | Azure 関数アプリの URL。 形式は `https://<accountname>.azurewebsites.net` です。 この URL は、Azure portal で関数アプリを表示した際に **URL** セクションに表示される値です  | はい |
| function key | Azure 関数のアクセス キーです。 それぞれの関数の **[管理]** セクションをクリックし、**ファンクション キー**または**ホスト キー**をコピーします。 詳しくは、次の記事をご覧ください:[Azure Functions の HTTP トリガーとバインド](../azure-functions/functions-bindings-http-webhook.md#authorization-keys) | はい |
|   |   |   |

## <a name="next-steps"></a>次の手順
Azure Data Factory でサポートされる変換アクティビティの一覧については、[データの変換](transform-data.md)に関する記事を参照してください。
