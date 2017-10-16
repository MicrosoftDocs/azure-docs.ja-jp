---
title: "Azure Data Factory でサポートされるコンピューティング環境 | Microsoft Docs"
description: "Azure Data Factory パイプライン (Azure HDInsight など) でデータの変換または処理に使うことができるコンピューティング環境について説明します。"
services: data-factory
documentationcenter: 
author: shengcmsft
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/10/2017
ms.author: shengc
ms.openlocfilehash: 07d702e34e1574161a64af9a4724a66879a0ba05
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="compute-environments-supported-by-azure-data-factory"></a>Azure Data Factory でサポートされるコンピューティング環境
この記事では、データの処理または変換に利用できるさまざまなコンピューティング環境について説明します。 これらのコンピューティング環境を Azure Data Factory にリンクする「リンクされたサービス」の構成時に Data Factory でサポートされるさまざまな構成 (オンデマンドと Bring Your Own の比較) に関する詳細も提供します。

次の表は、Data Factory でサポートされているコンピューティング環境と、その環境で実行できるアクティビティの一覧です。 

| Compute 環境                      | アクティビティ                               |
| ---------------------------------------- | ---------------------------------------- |
| [On-demand HDInsight クラスター](#azure-hdinsight-on-demand-linked-service)または[独自の HDInsight クラスター](#azure-hdinsight-linked-service) | [Hive](transform-data-using-hadoop-hive.md)、[Pig](transform-data-using-hadoop-pig.md)、[Spark](transform-data-using-spark.md)、[MapReduce](transform-data-using-hadoop-map-reduce.md)、[Hadoop Streaming](transform-data-using-hadoop-streaming.md) |
| [Azure Batch](#azure-batch-linked-service) | [カスタム](transform-data-using-dotnet-custom-activity.md) |
| [Azure Machine Learning](#azure-machine-learning-linked-service) | [Machine Learning アクティビティ: バッチ実行とリソース更新](transform-data-using-machine-learning.md) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics-linked-service) | [Data Lake Analytics U-SQL](transform-data-using-data-lake-analytics.md) |
| [Azure SQL](#azure-sql-linked-service)、[Azure SQL Data Warehouse](#azure-sql-data-warehouse-linked-service)、[SQL Server](#sql-server-linked-service) | [ストアド プロシージャ](transform-data-using-stored-procedure.md) |

>  

## <a name="on-demand-compute-environment"></a>オンデマンドのコンピューティング環境
この種類の構成では、コンピューティング環境は Azure Data Factory サービスにより完全管理されます。 データを処理するためのジョブが送信される前に Data Factory サービスにより自動的に作成され、ジョブの完了時に削除されます。 オンデマンドのコンピューティング環境のために「リンクされたサービス」を作成し、構成し、ジョブ実行、クラスター管理、ブートストラップ アクションの詳細設定を制御できます。

> [!NOTE]
> オンデマンド構成は現在のところ、Azure HDInsight クラスターにのみ対応しています。
>
> 

## <a name="azure-hdinsight-on-demand-linked-service"></a>Azure HDInsight のオンデマンドのリンクされたサービス
Azure Data Factory サービスは、データを処理するためのオンデマンド HDInsight クラスターを自動的に作成します。 このクラスターはクラスターに関連付けられているストレージ アカウント (JSON の linkedServiceName プロパティ) と同じリージョンで作成されます。 ストレージ アカウントは、汎用の標準的な Azure ストレージ アカウントである必要があります。 

オンデマンド HDInsight のリンクされたサービスに関する次の **重要な** 点に注意してください。

* オンデマンド HDInsight クラスターは Azure サブスクリプションのもとで作成されます。 クラスターが稼働している場合、Azure ポータルでクラスターを確認できます。 
* オンデマンド HDInsight クラスターで実行されるジョブのログは HDInsight クラスターに関連付けられているストレージ アカウントにコピーされます。 リンクされたサービス定義で定義されている ClusterUserName、clusterPassword、clusterSshUserName、clusterSshPassword は、クラスターのライフサイクル中に詳細なトラブルシューティングのためにクラスターにログインするときに使用されます。 
* HDInsight クラスターが稼動し、ジョブを実行している時間に対してのみ課金されます。

> [!IMPORTANT]
> オンデマンドで Azure HDInsight クラスターをプロビジョニングするには一般的に **20 分**以上かかります。
>
> 

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
> 実行するアクティビティが多いほど、Azure BLOB ストレージ内のコンテナーも増えます。 ジョブのトラブルシューティングのためにコンテナーが必要ない場合、コンテナーを削除してストレージ コストを削減できます。 これらのコンテナーの名前は、`adf**yourdatafactoryname**-**linkedservicename**-datetimestamp` 形式になります。 Azure BLOB ストレージ内のコンテナーを削除するには、 [Microsoft ストレージ エクスプローラー](http://storageexplorer.com/) などのツールを使用します。
>
> 

### <a name="properties"></a>プロパティ
| プロパティ                     | 説明                              | 必須 |
| ---------------------------- | ---------------------------------------- | -------- |
| type                         | type プロパティは **HDInsightOnDemand**に設定されます。 | あり      |
| clusterType                  | 作成する HDInsight クラスターの種類。 許可される値は "hadoop" および "spark" です。 指定しない場合は、既定値の hadoop が使用されます。 | いいえ       |
| clusterSize                  | クラスター内の worker/データ ノードの数です。 このプロパティで指定した worker ノード数と共に 2 つのヘッド ノードを使用して HDInsight クラスターが作成されます。 ノードのサイズは Standard_D3 (4 コア) であるため、4 worker ノード クラスターのコアは 24 個になります (worker ノード用に 4\*4 = 16 個のコアと、ヘッド ノード用に 2\*4 = 8 個のコア)。 詳細については、[Hadoop、Spark、Kafka などの HDInsight クラスターのセットアップ](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)に関する記事を参照してください。 | あり      |
| timetolive                   | オンデマンド HDInsight クラスターに許可されるアイドル時間です。 他のアクティブなジョブがクラスターにない場合、アクティビティ実行の完了後にオンデマンド HDInsight クラスターが起動状態を維持する時間を指定します。 最小許容値は 5 分 (00:05:00) です。<br/><br/>たとえば、アクティビティ実行に 6 分かかるときに timetolive が 5 分に設定されている場合、アクティビティ実行の 6 分間の処理の後、クラスターが起動状態を 5 分間維持します。 別のアクティビティ実行が 6 分の時間枠で実行される場合、それは同じクラスターで処理されます。<br/><br/>オンデマンド HDInsight クラスターの作成は高額な作業であり (時間もかかることがあります)、オンデマンド HDInsight クラスターを再利用し、Data Factory のパフォーマンスを改善する必要がある場合にこの設定を利用します。<br/><br/>timetolive 値を 0 に設定した場合、アクティビティ実行の完了直後にクラスターが削除されます。 ただし、高い値を設定した場合、クラスターは、何らかのトラブルシューティングの目的でログオンできるようにアイドル状態を維持できますが、その結果コストが高くなる可能性があります。 そのため、ニーズに合わせて適切な値を設定することが重要です。<br/><br/>timetolive プロパティ値が適切に設定されている場合、複数のパイプラインでオンデマンド HDInsight クラスターのインスタンスを共有できます。 | あり      |
| version                      | HDInsight クラスターのバージョン。 指定しない場合、現在の HDInsight 定義の既定バージョンを使用します。 | いいえ       |
| 既定のコンテナー            | データを保存し、処理するためにオンデマンド クラスターで使用される Azure Storage のリンクされたサービスです。 HDInsight クラスターは、この Azure Storage アカウントと同じリージョンに作成されます。 Azure HDInsight には、サポートされる各 Azure リージョンで使用できるコアの合計数に制限があります。 必要な clusterSize を満たせるだけ十分なコア クォータが、その Azure リージョンに存在することを確認してください。 詳細については、[Hadoop、Spark、Kafka などの HDInsight クラスターのセットアップ](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)に関する記事を参照してください<p>現時点では、Azure Data Lake Store をストレージとして使用するオンデマンド HDInsight クラスターを作成することはできません。 HDInsight 処理の結果データを Azure Data Lake Store に保存する必要がある場合は、コピー アクティビティを使用して、Azure Blob Storage から Azure Data Lake Store にデータをコピーします。 </p> | あり      |
| hostSubscriptionId           | HDInsight クラスターを作成するために使用する Azure サブスクリプション ID です。 指定されていない場合は、Azure のログイン コンテキストのサブスクリプション ID を使用します。 | いいえ       |
| clusterResourceGroup         | HDInsight クラスターは、このリソース グループに作成されます。 | あり      |
| sparkVersion                 | クラスターの種類が "Spark" の場合の Spark のバージョンです | いいえ       |
| additionalLinkedServiceNames | Data Factory サービスがあなたの代わりに登録できるように、HDInsight の「リンクされたサービス」の追加ストレージ アカウントを指定します。 これらのストレージ アカウントは、linkedServiceName で指定されたストレージ アカウントと同じリージョンに作成されている HDInsight クラスターと同じリージョンにある必要があります。 | いいえ       |
| osType                       | オペレーティング システムの種類。 使用可能な値: Linux と Windows (HDInsight 3.3 の場合のみ)。 既定値は Linux です。 | いいえ       |
| hcatalogLinkedServiceName    | HCatalog データベースを指す Azure SQL のリンクされたサービスの名前。 オンデマンド HDInsight クラスターは、Azure SQL データベースを metastore として使用して作成されます。 | いいえ       |
| connectVia                   | この HDInsight リンク サービスにアクティビティをディスパッチするために使用される統合ランタイムです。 オンデマンド HDInsight リンク サービスの場合、Azure 統合ランタイムだけをサポートします。 指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 | いいえ       |

> [!IMPORTANT]
> HDInsight は、デプロイできる Hadoop クラスター バージョンを複数サポートしています。 各バージョンを選択すると、特定のバージョンの Hortonworks Data Platform (HDP) ディストリビューションと、そのディストリビューションに含まれるコンポーネントが作成されます。 HDInsight のサポートされるバージョンの一覧を常に更新して、最新の Hadoop エコシステム コンポーネントと修正プログラムを提供しています。 HDInsight のサポートされているバージョンを確実に使用するために、[HDInsight のサポートされているバージョンと OS の種類](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions)に関する最新情報を常に参照してください。 
>
> 

#### <a name="additionallinkedservicenames-json-example"></a>additionalLinkedServiceNames JSON の例

```json
"additionalLinkedServiceNames": [
    "otherLinkedServiceName1": {
        "referenceName": "AzureStorageLinkedService",
        "type": "LinkedServiceReference"
    },
    "otherLinkedServiceName2": {
        "referenceName": "AzureStorageLinkedService",
        "type": "LinkedServiceReference"
    }
]
```

### <a name="service-principal-authentication"></a>サービス プリンシパルの認証

オンデマンドの HDInsight リンク サービスでは、ユーザーに代わって HDInsight クラスターを作成するためにサービス プリンシパル認証が必要になります。 サービス プリンシパルの認証を使用するには、Azure Active Directory (Azure AD) でアプリケーション エンティティを登録し、サブスクリプションの、または HDInsight クラスターが作成されるリソース グループの**共同作成者**のロールを付与します。 詳細な手順については、[ポータルを使用した、リソースにアクセスできる Azure Active Directory アプリケーションとサービス プリンシパルの作成](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-create-service-principal-portal)に関する記事を参照してください。 次の値を記録しておきます。リンクされたサービスを定義するときに使います。

- アプリケーション ID
- アプリケーション キー 
- テナント ID

次のプロパティを指定して、サービス プリンシパル認証を使います。

| プロパティ                | 説明                              | 必須 |
| :---------------------- | :--------------------------------------- | :------- |
| **servicePrincipalId**  | アプリケーションのクライアント ID を取得します。     | あり      |
| **servicePrincipalKey** | アプリケーションのキーを取得します。           | あり      |
| **tenant**              | アプリケーションが存在するテナントの情報 (ドメイン名またはテナント ID) を指定します。 Azure Portal の右上隅をマウスでポイントすることにより取得できます。 | あり      |

### <a name="advanced-properties"></a>高度なプロパティ

次のプロパティを指定し、オンデマンド HDInsight クラスターを詳細に設定することもできます。

| プロパティ               | 説明                              | 必須 |
| :--------------------- | :--------------------------------------- | :------- |
| coreConfiguration      | 作成する HDInsight クラスターに core 構成パラメーター (core-site.xml と同じ) を指定します。 | なし       |
| hBaseConfiguration     | HDInsight クラスターに HBase 構成パラメーター (hbase-site.xml) を指定します。 | なし       |
| hdfsConfiguration      | HDInsight クラスターに HDFS 構成パラメーター (hdfs-site.xml) を指定します。 | なし       |
| hiveConfiguration      | HDInsight クラスターに hive 構成パラメーター (hive-site.xml) を指定します。 | なし       |
| mapReduceConfiguration | HDInsight クラスターに MapReduce 構成パラメーター (mapred-site.xml) を指定します。 | なし       |
| oozieConfiguration     | HDInsight クラスターに Oozie 構成パラメーター (oozie-site.xml) を指定します。 | なし       |
| stormConfiguration     | HDInsight クラスターに Storm 構成パラメーター (storm-site.xml) を指定します。 | なし       |
| yarnConfiguration      | HDInsight クラスターに Yarn 構成パラメーター (yarn-site.xml) を指定します。 | なし       |

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
          "additionalLinkedServiceNames": [
              "otherLinkedServiceName1": {
                  "referenceName": "datafeeds1",
                  "type": "LinkedServiceReference"
              },
              "otherLinkedServiceName2": {
                  "referenceName": "datafeeds2",
                  "type": "LinkedServiceReference"
              }
          ]}
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
| headNodeSize      | ヘッド ノードのサイズを指定します。 既定値は Standard_D3 です。 詳細については、「**ノードのサイズの指定**」をご覧ください。 | いいえ       |
| dataNodeSize      | データ ノードのサイズを指定します。 既定値は Standard_D3 です。 | なし       |
| zookeeperNodeSize | Zookeeper ノードのサイズを指定します。 既定値は Standard_D3 です。 | なし       |

#### <a name="specifying-node-sizes"></a>ノードのサイズの指定
前のセクションで説明したプロパティで指定する必要がある文字列値については、[仮想マシンのサイズ](../virtual-machines/linux/sizes.md)に関する記事を参照してください。 値は、この記事に記載されている**コマンドレットと API** に準拠する必要があります。 この記事に示すように、Large (既定値) サイズのデータ ノードのメモリ容量は 7 GB ですが、シナリオによってはこれでは不十分な場合があります。 

D4 サイズのヘッド ノードとワーカー ノードを作成する場合は、headNodeSize プロパティと dataNodeSize プロパティの値として **Standard_D4** を指定します。 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

これらのプロパティに間違った値を指定すると、次のエラーが発生します。**エラー:** クラスターを作成できませんでした。 例外: クラスター作成操作を完了できません。 コード '400' で操作が失敗しました。 取り残されたクラスターの状態: 'Error'。 メッセージ: 'PreClusterCreationValidationFailure'。" というエラー メッセージが表示される場合があります。 このエラーが発生した場合は、[仮想マシンのサイズ](../virtual-machines/linux/sizes.md)に関する記事の表に記載されている**コマンドレットと API** の名前を使用していることを確認してください。        

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

### <a name="properties"></a>プロパティ
| プロパティ          | 説明                              | 必須 |
| ----------------- | ---------------------------------------- | -------- |
| type              | type プロパティは **HDInsight**に設定されます。 | はい      |
| clusterUri        | HDInsight クラスターの URI です。        | はい      |
| username          | 既存の HDInsight クラスターに接続するために使用されるユーザーの名前を指定します。 | はい      |
| パスワード          | ユーザー アカウントのパスワードを指定します。   | はい      |
| linkedServiceName | HDInsight クラスターで使われる Azure Blob Storage を参照する Azure Storage のリンクされたサービスの名前です。 <p>現在は、Azure Data Lake Store のリンクされたサービスをこのプロパティに指定することはできません。 HDInsight クラスターが Data Lake Store にアクセスできる場合、Hive/Pig スクリプトから Azure Data Lake Store 内のデータにアクセスできます。 </p> | あり      |
| connectVia        | このリンク サービスにアクティビティをディスパッチするために使用される統合ランタイムです。 Azure 統合ランタイムまたは自己ホスト型統合ランタイムを使用することができます。 指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 | いいえ       |

> [!IMPORTANT]
> HDInsight は、デプロイできる Hadoop クラスター バージョンを複数サポートしています。 各バージョンを選択すると、特定のバージョンの Hortonworks Data Platform (HDP) ディストリビューションと、そのディストリビューションに含まれるコンポーネントが作成されます。 HDInsight のサポートされるバージョンの一覧を常に更新して、最新の Hadoop エコシステム コンポーネントと修正プログラムを提供しています。 HDInsight のサポートされているバージョンを確実に使用するために、[HDInsight のサポートされているバージョンと OS の種類](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions)に関する最新情報を常に参照してください。 
>

## <a name="azure-batch-linked-service"></a>Azure Batch のリンクされたサービス

Azure Batch の「リンクされたサービス」を作成し、仮想マシン (VM) の Batch プールを Data Factory に登録できます。 Azure Batch を使用してカスタム アクティビティを実行することができます。

Azure Batch サービスを初めて利用する場合は、次のトピックをご覧ください。

* [Azure Batch の基本](../batch/batch-technical-overview.md) 」をご覧ください。
* Azure Batch アカウントは、[New-AzureRmBatchAccount](/powershell/module/azurerm.batch/New-AzureRmBatchAccount?view=azurermps-4.3.1) コマンドレットまたは [Azure Portal](../batch/batch-account-create-portal.md) を使用して作成します。 このコマンドレットの使用方法の詳細については、 [PowerShell を使用した Azure Batch アカウントの管理](http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) に関するトピックをご覧ください。
* [New-AzureBatchPool](/powershell/module/azurerm.batch/New-AzureBatchPool?view=azurermps-4.3.1) コマンドレット。

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


### <a name="properties"></a>プロパティ
| プロパティ          | 説明                              | 必須 |
| ----------------- | ---------------------------------------- | -------- |
| type              | type プロパティは **AzureBatch**に設定されます。 | はい      |
| .<リージョン名       | Azure Batch アカウントの名前です。         | はい      |
| accessKey         | Azure Batch アカウントのアクセス キーです。  | あり      |
| batchUri          | https://*batchaccountname.region*.batch.azure.com の形式の Azure Batch アカウントへの URL です。 | あり      |
| poolName          | 仮想マシンのプールの名前です。    | はい      |
| 既定のコンテナー | この Azure Batch の「リンクされたサービス」に関連付けられている Azure Storage の「リンクされたサービス」の名前です。 この「リンクされたサービス」はアクティビティの実行に必要なファイルのステージングに利用されます。 | あり      |
| connectVia        | このリンク サービスにアクティビティをディスパッチするために使用される統合ランタイムです。 Azure 統合ランタイムまたは自己ホスト型統合ランタイムを使用することができます。 指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 | いいえ       |

## <a name="azure-machine-learning-linked-service"></a>Azure Machine Learning のリンクされたサービス
Azure Machine Learning の「リンクされたサービス」を作成し、Machine Learning のバッチ スコアリング エンドポイントを Data Factory に登録します。

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

### <a name="properties"></a>プロパティ
| プロパティ               | 説明                              | 必須                                 |
| ---------------------- | ---------------------------------------- | ---------------------------------------- |
| type                   | type プロパティは **AzureML**に設定されます。 | はい                                      |
| mlEndpoint             | バッチ スコアリング URL です。                   | はい                                      |
| apiKey                 | 公開されたワークスペース モデルの API です。     | あり                                      |
| updateResourceEndpoint | トレーニング済みモデル ファイルを使用した予測 Web サービスの更新に使用される Azure ML Web サービス エンドポイントの更新リソース URL です | いいえ                                       |
| servicePrincipalId     | アプリケーションのクライアント ID を取得します。     | UpdateResourceEndpoint が指定されている場合は必須です |
| servicePrincipalKey    | アプリケーションのキーを取得します。           | UpdateResourceEndpoint が指定されている場合は必須です |
| テナント                 | アプリケーションが存在するテナントの情報 (ドメイン名またはテナント ID) を指定します。 Azure Portal の右上隅をマウスでポイントすることにより取得できます。 | UpdateResourceEndpoint が指定されている場合は必須です |
| connectVia             | このリンク サービスにアクティビティをディスパッチするために使用される統合ランタイムです。 Azure 統合ランタイムまたは自己ホスト型統合ランタイムを使用することができます。 指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 | いいえ                                       |

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
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="properties"></a>プロパティ

| プロパティ             | 説明                              | 必須                                 |
| -------------------- | ---------------------------------------- | ---------------------------------------- |
| type                 | type プロパティは **AzureDataLakeAnalytics**に設定する必要があります。 | はい                                      |
| accountName          | Azure Data Lake Analytics アカウント名。  | はい                                      |
| dataLakeAnalyticsUri | Azure Data Lake Analytics URI。           | いいえ                                       |
| subscriptionId       | Azure サブスクリプション ID                    | いいえ (指定されていない場合、Data Factory のサブスクリプションが使用されます)。 |
| resourceGroupName    | Azure リソース グループ名                | いいえ (指定されていない場合は Data Factory のリソース グループが使用されます)。 |
| servicePrincipalId   | アプリケーションのクライアント ID を取得します。     | はい                                      |
| servicePrincipalKey  | アプリケーションのキーを取得します。           | はい                                      |
| テナント               | アプリケーションが存在するテナントの情報 (ドメイン名またはテナント ID) を指定します。 Azure Portal の右上隅をマウスでポイントすることにより取得できます。 | あり                                      |
| connectVia           | このリンク サービスにアクティビティをディスパッチするために使用される統合ランタイムです。 Azure 統合ランタイムまたは自己ホスト型統合ランタイムを使用することができます。 指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 | いいえ                                       |



## <a name="azure-sql-database-linked-service"></a>Azure SQL Database のリンクされたサービス
Azure SQL のリンクされたサービスを作成し、 [ストアド プロシージャ アクティビティ](transform-data-using-stored-procedure.md) で使用して、Data Factory パイプラインからストアド プロシージャを起動します。 このリンクされたサービスの詳細については、 [Azure SQL コネクタ](connector-azure-sql-database.md#linked-service-properties) に関する記事を参照してください。

## <a name="azure-sql-data-warehouse-linked-service"></a>Azure SQL Data Warehouse のリンクされたサービス
Azure SQL Data Warehouse のリンクされたサービスを作成し、 [ストアド プロシージャ アクティビティ](transform-data-using-stored-procedure.md) で使用して、Data Factory パイプラインからストアド プロシージャを起動します。 このリンクされたサービスの詳細については、 [Azure SQL Data Warehouse コネクタ](connector-azure-sql-data-warehouse.md#linked-service-properties) に関する記事をご覧ください。

## <a name="sql-server-linked-service"></a>SQL Server のリンクされたサービス
SQL Server のリンクされたサービスを作成し、 [ストアド プロシージャ アクティビティ](transform-data-using-stored-procedure.md) で使用して、Data Factory パイプラインからストアド プロシージャを起動します。 このリンクされたサービスの詳細については、 [SQL Server コネクタ](connector-sql-server.md#linked-service-properties) に関する記事をご覧ください。

## <a name="azure-data-factory---naming-rules"></a>Azure Data Factory - 名前付け規則
次の表に、Data Factory アーティファクトの名前付け規則を示します。

| 名前                             | 名前の一意性                          | 検証チェック                        |
| :------------------------------- | :--------------------------------------- | :--------------------------------------- |
| Data Factory                     | Microsoft Azure 全体で一意です。 名前の大文字と小文字を区別されません。つまり、`MyDF` と `mydf` は同じデータ ファクトリを表します。 | <ul><li>各データ ファクトリは、厳密に 1 つの Azure サブスクリプションに関連付けられます。</li><li>オブジェクト名は英文字または数字で始まり、英文字、数字、ダッシュ (-) 文字のみを含めることができます。</li><li>すべてのダッシュ (-) 文字は、その直前または直後に文字または数字が使用されている必要があります。 連続するダッシュ文字はコンテナー名では使用できません。</li><li>名前は 3 ～ 63 文字の長さにすることができます。</li></ul> |
| リンクされたサービス/テーブル/パイプライン | データ ファクトリ内で一意です。 名前の大文字と小文字は区別されません。 | <ul><li>テーブル名の最大文字数: 260。</li><li>オブジェクト名は、文字、数字、アンダー スコア (_) のいずれかで始める必要があります。</li><li>次の文字は使用できません: “.”, “+”, “?”, “/”, “<”, ”>”,”*”,”%”,”&”,”:”,”\\"</li></ul> |
| [リソース グループ]                   | Microsoft Azure 全体で一意です。 名前の大文字と小文字は区別されません。 | <ul><li>最大文字数: 1,000。</li><li>名前には、文字、数字、"-"、"_"、"," および "." を文字を含めることができます。</li></ul> |

## <a name="next-steps"></a>次のステップ
Azure Data Factory でサポートされる変換アクティビティの一覧については、[データの変換](transform-data.md)に関する記事を参照してください。