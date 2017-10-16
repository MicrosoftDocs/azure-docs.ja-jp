---
title: "Azure Data Factory でサポートされるコンピューティング環境 | Microsoft Docs"
description: "Azure Data Factory パイプライン (Azure HDInsight など) でデータの変換または処理に使うことができるコンピューティング環境について説明します。"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: 6877a7e8-1a58-4cfb-bbd3-252ac72e4145
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/25/2017
ms.author: shlo
robots: noindex
ms.openlocfilehash: bccdfdc486f5e79f034c5a940e42c5f230283a50
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="compute-environments-supported-by-azure-data-factory"></a>Azure Data Factory でサポートされるコンピューティング環境
> [!NOTE]
> この記事は、一般公開 (GA) されている Data Factory のバージョン 1 に適用されます。 プレビュー段階にある Data Factory サービスのバージョン 2 を使用している場合は、[V2 での Compute linked services](../compute-linked-services.md) を参照してください。

この記事では、データの処理または変換に利用できるさまざまなコンピューティング環境について説明します。 これらのコンピューティング環境を Azure Data Factory にリンクする「リンクされたサービス」の構成時に Data Factory でサポートされるさまざまな構成 (オンデマンドと Bring Your Own の比較) に関する詳細も提供します。

次の表は、Data Factory でサポートされているコンピューティング環境と、その環境で実行できるアクティビティの一覧です。 

| Compute 環境 | アクティビティ |
| --- | --- |
| [On-demand HDInsight クラスター](#azure-hdinsight-on-demand-linked-service)または[独自の HDInsight クラスター](#azure-hdinsight-linked-service) |[DotNet](data-factory-use-custom-activities.md)、[Hive](data-factory-hive-activity.md)、[Pig](data-factory-pig-activity.md)、[MapReduce](data-factory-map-reduce.md)、[Hadoop Streaming](data-factory-hadoop-streaming-activity.md) |
| [Azure Batch](#azure-batch-linked-service) |[DotNet](data-factory-use-custom-activities.md) |
| [Azure Machine Learning](#azure-machine-learning-linked-service) |[Machine Learning アクティビティ: バッチ実行とリソース更新](data-factory-azure-ml-batch-execution-activity.md) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics-linked-service) |[Data Lake Analytics U-SQL](data-factory-usql-activity.md) |
| [Azure SQL](#azure-sql-linked-service)、[Azure SQL Data Warehouse](#azure-sql-data-warehouse-linked-service)、[SQL Server](#sql-server-linked-service) |[ストアド プロシージャ](data-factory-stored-proc-activity.md) |

## <a name="supported-hdinsight-versions-in-azure-data-factory"></a>Azure Data Factory でサポートされる HDInsight バージョン
Azure HDInsight は、いつでもデプロイできる Hadoop クラスター バージョンを複数サポートしています。 各バージョンを選択すると、特定のバージョンの Hortonworks Data Platform (HDP) ディストリビューションと、そのディストリビューションに含まれるコンポーネントが作成されます。 Microsoft では、HDInsight のサポートされるバージョンの一覧を常に更新して、最新の Hadoop エコシステム コンポーネントと修正プログラムを提供しています。 HDInsight 3.2 は、2017 年 4 月 1 日より非推奨となっています。 詳細については、「[サポートされる HDInsight のバージョン](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions)」を参照してください。

これは、HDInsight 3.2 クラスターに対してアクティビティを実行している既存の Azure Data Factory に影響します。 次のセクションのガイドラインに従って、影響を受ける Data Factory を更新することをお勧めします。

### <a name="for-linked-services-pointing-to-your-own-hdinsight-clusters"></a>独自の HDInsight クラスターを指すリンクされたサービス
* **独自の HDInsight 3.2 以下のクラスターを指す HDInsight のリンクされたサービス**

  Azure Data Factory は、独自の HDInsight クラスターに対するジョブを HDI 3.1 から[最新のサポートされる HDInsight バージョン](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions)に送信することをサポートしています。 ただし、HDInsight 3.2 クラスターは、「[サポートされる HDInsight バージョン](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions)」に記載されている非推奨ポリシーに基づいて、2017 年 4 月 1 日以降は作成できなくなっています。  

  **推奨事項:** 
  * 「[HDInsight の各バージョンで使用できる Hadoop コンポーネント](../../hdinsight/hdinsight-component-versioning.md#hadoop-components-available-with-different-hdinsight-versions)」と「[HDInsight バージョンに対応する Hortonworks リリース ノート](../../hdinsight/hdinsight-component-versioning.md#hortonworks-release-notes-associated-with-hdinsight-versions)」に記載されている情報に従ってテストを実行して、このリンクされたサービスを参照するアクティビティの[最新のサポートされる HDInsight のバージョン](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions)に対する互換性を確認します。
  * HDInsight 3.2 クラスターを[最新のサポートされる HDInsight のバージョン](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions)にアップグレードして、最新の Hadoop エコシステム コンポーネントと修正プログラムを取得します。 

* **独自の HDInsight 3.3 以上のクラスターを指す HDInsight のリンクされたサービス**

  Azure Data Factory は、独自の HDInsight クラスターに対するジョブを HDI 3.1 から[最新のサポートされる HDInsight バージョン](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions)に送信することをサポートしています。 
  
  **推奨事項:** 
  * Data Factory の観点から見て必要なアクションはありません。 ただし、HDInsight のバージョンが古い場合は、[最新のサポートされる HDInsight のバージョン](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions)にアップグレードして、最新の Hadoop エコシステム コンポーネントと修正プログラムを取得することをお勧めします。

### <a name="for-hdinsight-on-demand-linked-services"></a>HDInsight のオンデマンドのリンクされたサービス
* **バージョン 3.2 以下が HDInsight のオンデマンドのリンクされたサービスの JSON 定義に指定されている:**
  
  Azure Data Factory は、**2017 年 5 月 15 日**から、バージョン 3.3 以降のオンデマンド HDInsight クラスターの作成をサポートします。 また、既存のオンデマンド HDInsight 3.2 のリンクされたサービスに対するサポートの終了は、**2017 年 7 月 15 日**まで延長されます。  

  **推奨事項:** 
  * 「[HDInsight の各バージョンで使用できる Hadoop コンポーネント](../../hdinsight/hdinsight-component-versioning.md#hadoop-components-available-with-different-hdinsight-versions)」と「[HDInsight バージョンに対応する Hortonworks リリース ノート](../../hdinsight/hdinsight-component-versioning.md#hortonworks-release-notes-associated-with-hdinsight-versions)」に記載されている情報に従ってテストを実行して、このリンクされたサービスを参照するアクティビティの[最新のサポートされる HDInsight のバージョン](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions)に対する互換性を確認します。
  * **2017 年 7 月 15 日**より前に、オンデマンドの HDI のリンクされたサービスの JSON 定義に含まれる Version プロパティを[最新のサポートされる HDInsight のバージョン](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions)に更新して、最新の Hadoop エコシステム コンポーネントと修正プログラムを取得します。 JSON 定義の詳細については、「[Azure HDInsight のオンデマンドのリンクされたサービス](#azure-hdinsight-on-demand-linked-service)」を参照してください。 

* **オンデマンドの HDInsight のリンクされたサービスに指定されていないバージョン:**
  
  Azure Data Factory は、**2017 年 5 月 15 日**から、バージョン 3.3 以降のオンデマンド HDInsight クラスターの作成をサポートします。 また、既存のオンデマンド HDInsight 3.2 のリンクされたサービスに対するサポートの終了は、**2017 年 7 月 15 日**まで延長されます。 

  **2017 年 7 月 15 日**までは、空白のままにした場合の Version プロパティと osType プロパティの既定値は次のようになります。 

  | プロパティ | 既定値 | 必須 |
  | --- | --- | --- |
  バージョン   | Windows クラスターでは HDI 3.1、Linux クラスターでは HDI 3.2| いいえ
  osType | 既定値は Windows | いいえ

  **2017 年 7 月 15 日**より後は、空白のままにした場合の Version プロパティと osType プロパティの既定値は次のようになります。

  | プロパティ | 既定値 | 必須 |
  | --- | --- | --- |
  バージョン   | Windows クラスターでは HDI 3.3、Linux クラスターでは 3.5    | いいえ
  osType | 既定値は Linux | いいえ

  **推奨事項:** 
  * **2017 年 7 月 15 日**までは、「[HDInsight の各バージョンで使用できる Hadoop コンポーネント](../../hdinsight/hdinsight-component-versioning.md#hadoop-components-available-with-different-hdinsight-versions)」と「[HDInsight バージョンに対応する Hortonworks リリース ノート](../../hdinsight/hdinsight-component-versioning.md#hortonworks-release-notes-associated-with-hdinsight-versions)」に記載されている情報に従ってテストを実行して、このリンクされたサービスを参照するアクティビティの[最新のサポートされる HDInsight のバージョン](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions)に対する互換性を確認してください。  
  * **2017 年 7 月 15 日**より後は、既定の設定をオーバーライドする場合は、osType とバージョンの値を必ず明示的に指定してください。 

>[!Note]
>現時点では、Azure Data Factory は、Azure Data Lake Store をプライマリ ストアとして使用する HDInsight クラスターはサポートしていません。 HDInsight クラスターのプライマリ ストアとして Azure Storage を使用します。 
>  
>  

## <a name="on-demand-compute-environment"></a>オンデマンドのコンピューティング環境
この種類の構成では、コンピューティング環境は Azure Data Factory サービスにより完全管理されます。 データを処理するためのジョブが送信される前に Data Factory サービスにより自動的に作成され、ジョブの完了時に削除されます。 オンデマンドのコンピューティング環境のために「リンクされたサービス」を作成し、構成し、ジョブ実行、クラスター管理、ブートストラップ アクションの詳細設定を制御できます。

> [!NOTE]
> オンデマンド構成は現在のところ、Azure HDInsight クラスターにのみ対応しています。
> 
> 

## <a name="azure-hdinsight-on-demand-linked-service"></a>Azure HDInsight のオンデマンドのリンクされたサービス
Azure Data Factory サービスは、データを処理するための Windows/Linux ベースのオンデマンド HDInsight クラスターを自動的に作成します。 このクラスターはクラスターに関連付けられているストレージ アカウント (JSON の linkedServiceName プロパティ) と同じリージョンで作成されます。

オンデマンド HDInsight のリンクされたサービスに関する次の **重要な** 点に注意してください。

* 作成したオンデマンド HDInsight クラスターは Azure サブスクリプションには表示されません。 Azure Data Factory サービスがユーザーに代わってオンデマンド HDInsight クラスターを管理します。
* オンデマンド HDInsight クラスターで実行されるジョブのログは HDInsight クラスターに関連付けられているストレージ アカウントにコピーされます。 これらのログには Azure ポータルの **[アクティビティ実行の詳細]** ブレードからアクセスできます。 詳細については、「 [パイプラインの監視と管理](data-factory-monitor-manage-pipelines.md) 」という記事を参照してください。
* HDInsight クラスターが稼動し、ジョブを実行している時間に対してのみ課金されます。

> [!IMPORTANT]
> オンデマンドで Azure HDInsight クラスターをプロビジョニングするには一般的に **20 分**以上かかります。
> 
> 

### <a name="example"></a>例
次の JSON は、Linux ベースのオンデマンド HDInsight のリンクされたサービスを定義します。 Data Factory サービスは、データ スライスを処理するときに、 **Linux ベースの** HDInsight クラスターを自動的に作成します。 

```json
{
    "name": "HDInsightOnDemandLinkedService",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "version": "3.5",
            "clusterSize": 1,
            "timeToLive": "00:05:00",
            "osType": "Linux",
            "linkedServiceName": "AzureStorageLinkedService"
        }
    }
}
```

Windows ベースの HDInsight クラスターを使用するには、**osType** を **windows** に設定するか、このプロパティを使用しません (既定値が windows です)。  

> [!IMPORTANT]
> HDInsight クラスターは、JSON (**linkedServiceName**) で指定した BLOB ストレージに**既定のコンテナー**を作成します。 クラスターを削除しても、HDInsight はこのコンテナーを削除しません。 この動作は仕様です。 オンデマンド HDInsight のリンクされたサービスでは、既存のライブ クラスター (**timeToLive**) がある場合を除き、スライスを処理する必要があるたびに HDInsight クラスターが作成され、処理が終了すると削除されます。 
> 
> 処理されるスライスが多いほど、Azure BLOB ストレージ内のコンテナーも増えます。 ジョブのトラブルシューティングのためにコンテナーが必要ない場合、コンテナーを削除してストレージ コストを削減できます。 これらのコンテナーの名前は、`adf**yourdatafactoryname**-**linkedservicename**-datetimestamp` 形式になります。 Azure BLOB ストレージ内のコンテナーを削除するには、 [Microsoft ストレージ エクスプローラー](http://storageexplorer.com/) などのツールを使用します。
> 
> 

### <a name="properties"></a>プロパティ
| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| type |type プロパティは **HDInsightOnDemand**に設定されます。 |はい |
| clusterSize |クラスター内の worker/データ ノードの数です。 このプロパティで指定した worker ノード数と共に 2 つのヘッド ノードを使用して HDInsight クラスターが作成されます。 ノードのサイズは Standard_D3 (4 コア) であるため、4 worker ノード クラスターのコアは 24 個になります (worker ノード用に 4\*4 = 16 個のコアと、ヘッド ノード用に 2\*4 = 8 個のコア)。 Standard_D3 レベルの詳細については、「[HDInsight での Linux ベースの Hadoop クラスターの作成](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)」を参照してください。 |はい |
| timetolive |オンデマンド HDInsight クラスターに許可されるアイドル時間です。 他のアクティブなジョブがクラスターにない場合、アクティビティ実行の完了後にオンデマンド HDInsight クラスターが起動状態を維持する時間を指定します。<br/><br/>たとえば、アクティビティ実行に 6 分かかるときに timetolive が 5 分に設定されている場合、アクティビティ実行の 6 分間の処理の後、クラスターが起動状態を 5 分間維持します。 別のアクティビティ実行が 6 分の時間枠で実行される場合、それは同じクラスターで処理されます。<br/><br/>オンデマンド HDInsight クラスターの作成は高額な作業であり (時間もかかることがあります)、オンデマンド HDInsight クラスターを再利用し、Data Factory のパフォーマンスを改善する必要がある場合にこの設定を利用します。<br/><br/>timetolive 値を 0 に設定した場合、アクティビティ実行の完了直後にクラスターが削除されます。 一方、高い値を設定した場合、クラスターは不必要にアイドル状態を維持し、コストの上昇を招きます。 そのため、ニーズに合わせて適切な値を設定することが重要です。<br/><br/>timetolive プロパティ値が適切に設定されている場合、複数のパイプラインでオンデマンド HDInsight クラスターのインスタンスを共有できます。  |あり |
| version |HDInsight クラスターのバージョン。 既定値は、Windows クラスターでは 3.1、Linux クラスターでは 3.2 です。 |なし |
| 既定のコンテナー | データを保存し、処理するためにオンデマンド クラスターで使用される Azure Storage のリンクされたサービスです。 HDInsight クラスターは、この Azure Storage アカウントと同じリージョンに作成されます。<p>現時点では、Azure Data Lake Store をストレージとして使用するオンデマンド HDInsight クラスターを作成することはできません。 HDInsight 処理の結果データを Azure Data Lake Store に保存する必要がある場合は、コピー アクティビティを使用して、Azure Blob Storage から Azure Data Lake Store にデータをコピーします。 </p>  | はい |
| additionalLinkedServiceNames |Data Factory サービスがあなたの代わりに登録できるように、HDInsight の「リンクされたサービス」の追加ストレージ アカウントを指定します。 これらのストレージ アカウントは、linkedServiceName で指定されたストレージ アカウントと同じリージョンに作成されている HDInsight クラスターと同じリージョンにある必要があります。 |いいえ |
| osType |オペレーティング システムの種類。 使用可能な値:  Windows (既定値) および Linux |なし |
| hcatalogLinkedServiceName |HCatalog データベースを指す Azure SQL のリンクされたサービスの名前。 オンデマンド HDInsight クラスターは、Azure SQL データベースを metastore として使用して作成されます。 |いいえ |

#### <a name="additionallinkedservicenames-json-example"></a>additionalLinkedServiceNames JSON の例

```json
"additionalLinkedServiceNames": [
    "otherLinkedServiceName1",
    "otherLinkedServiceName2"
  ]
```

### <a name="advanced-properties"></a>高度なプロパティ
次のプロパティを指定し、オンデマンド HDInsight クラスターを詳細に設定することもできます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| coreConfiguration |作成する HDInsight クラスターに core 構成パラメーター (core-site.xml と同じ) を指定します。 |なし |
| hBaseConfiguration |HDInsight クラスターに HBase 構成パラメーター (hbase-site.xml) を指定します。 |なし |
| hdfsConfiguration |HDInsight クラスターに HDFS 構成パラメーター (hdfs-site.xml) を指定します。 |なし |
| hiveConfiguration |HDInsight クラスターに hive 構成パラメーター (hive-site.xml) を指定します。 |なし |
| mapReduceConfiguration |HDInsight クラスターに MapReduce 構成パラメーター (mapred-site.xml) を指定します。 |なし |
| oozieConfiguration |HDInsight クラスターに Oozie 構成パラメーター (oozie-site.xml) を指定します。 |なし |
| stormConfiguration |HDInsight クラスターに Storm 構成パラメーター (storm-site.xml) を指定します。 |なし |
| yarnConfiguration |HDInsight クラスターに Yarn 構成パラメーター (yarn-site.xml) を指定します。 |なし |

#### <a name="example--on-demand-hdinsight-cluster-configuration-with-advanced-properties"></a>例 – オンデマンド HDInsight クラスターと詳細なプロパティ

```json
{
  "name": " HDInsightOnDemandLinkedService",
  "properties": {
    "type": "HDInsightOnDemand",
    "typeProperties": {
      "clusterSize": 16,
      "timeToLive": "01:30:00",
      "linkedServiceName": "adfods1",
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
        "datafeeds",
        "adobedatafeed"
      ]
    }
  }
}
```

### <a name="node-sizes"></a>ノードのサイズ
次のプロパティを使用して、ヘッド ノード、データ ノード、Zookeeper ノードのサイズを指定できます。 

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| headNodeSize |ヘッド ノードのサイズを指定します。 既定値は Standard_D3 です。 詳細については、「**ノードのサイズの指定**」をご覧ください。 |いいえ |
| dataNodeSize |データ ノードのサイズを指定します。 既定値は Standard_D3 です。 |なし |
| zookeeperNodeSize |Zookeeper ノードのサイズを指定します。 既定値は Standard_D3 です。 |なし |

#### <a name="specifying-node-sizes"></a>ノードのサイズの指定
前のセクションで説明したプロパティで指定する必要がある文字列値については、[仮想マシンのサイズ](../../virtual-machines/linux/sizes.md)に関する記事を参照してください。 値は、この記事に記載されている**コマンドレットと API** に準拠する必要があります。 この記事に示すように、Large (既定値) サイズのデータ ノードのメモリ容量は 7 GB ですが、シナリオによってはこれでは不十分な場合があります。 

D4 サイズのヘッド ノードとワーカー ノードを作成する場合は、headNodeSize プロパティと dataNodeSize プロパティの値として **Standard_D4** を指定します。 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

これらのプロパティに間違った値を指定すると、次のエラーが発生します。**エラー:** クラスターを作成できませんでした。 例外: クラスター作成操作を完了できません。 コード '400' で操作が失敗しました。 取り残されたクラスターの状態: 'Error'。 メッセージ: 'PreClusterCreationValidationFailure'。" というエラー メッセージが表示される場合があります。 このエラーが発生した場合は、[仮想マシンのサイズ](../../virtual-machines/linux/sizes.md)に関する記事の表に記載されている**コマンドレットと API** の名前を使用していることを確認してください。  

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
      "userName": "admin",
      "password": "<password>",
      "linkedServiceName": "MyHDInsightStoragelinkedService"
    }
  }
}
```

### <a name="properties"></a>プロパティ
| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| type |type プロパティは **HDInsight**に設定されます。 |はい |
| clusterUri |HDInsight クラスターの URI です。 |はい |
| username |既存の HDInsight クラスターに接続するために使用されるユーザーの名前を指定します。 |はい |
| パスワード |ユーザー アカウントのパスワードを指定します。 |はい |
| linkedServiceName | HDInsight クラスターで使われる Azure Blob Storage を参照する Azure Storage のリンクされたサービスの名前です。 <p>現在は、Azure Data Lake Store のリンクされたサービスをこのプロパティに指定することはできません。 HDInsight クラスターが Data Lake Store にアクセスできる場合、Hive/Pig スクリプトから Azure Data Lake Store 内のデータにアクセスできます。 </p>  |あり |

## <a name="azure-batch-linked-service"></a>Azure Batch の「リンクされたサービス」
Azure Batch の「リンクされたサービス」を作成し、仮想マシン (VM) の Batch プールを Data Factory に登録できます。 Azure Batch と Azure HDInsight のいずれかを利用し、.NET カスタム アクティビティを実行できます。

Azure Batch サービスを初めて利用する場合は、次のトピックをご覧ください。

* [Azure Batch の基本](../../batch/batch-technical-overview.md) 」をご覧ください。
* Azure Batch アカウントは、[New-AzureBatchAccount](https://msdn.microsoft.com/library/mt125880.aspx) コマンドレットまたは [Azure Portal](../../batch/batch-account-create-portal.md) を使用して作成します。 このコマンドレットの使用方法の詳細については、 [PowerShell を使用した Azure Batch アカウントの管理](http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) に関するトピックをご覧ください。
* [New-AzureBatchPool](https://msdn.microsoft.com/library/mt125936.aspx) コマンドレット。

### <a name="example"></a>例

```json
{
  "name": "AzureBatchLinkedService",
  "properties": {
    "type": "AzureBatch",
    "typeProperties": {
      "accountName": "<Azure Batch account name>",
      "accessKey": "<Azure Batch account key>",
      "poolName": "<Azure Batch pool name>",
      "linkedServiceName": "<Specify associated storage linked service reference here>"
    }
  }
}
```

**accountName** プロパティの Batch アカウントの名前に「**.\<リージョン名\>**」を追加します。 例:

```json
"accountName": "mybatchaccount.eastus"
```

もう 1 つの選択肢は、次の例に示すように batchUri エンドポイントを指定することです。

```json
"accountName": "adfteam",
"batchUri": "https://eastus.batch.azure.com",
```

### <a name="properties"></a>プロパティ
| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| type |type プロパティは **AzureBatch**に設定されます。 |はい |
| .<リージョン名 |Azure Batch アカウントの名前です。 |はい |
| accessKey |Azure Batch アカウントのアクセス キーです。 |はい |
| poolName |仮想マシンのプールの名前です。 |はい |
| 既定のコンテナー |この Azure Batch の「リンクされたサービス」に関連付けられている Azure Storage の「リンクされたサービス」の名前です。 この「リンクされたサービス」はアクティビティの実行に必要なファイルのステージングとアクティビティ実行ログの保存に利用されます。 |はい |

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
      "apiKey": "<apikey>"
    }
  }
}
```

### <a name="properties"></a>プロパティ
| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| type |type プロパティは **AzureML**に設定されます。 |はい |
| mlEndpoint |バッチ スコアリング URL です。 |はい |
| apiKey |公開されたワークスペース モデルの API です。 |はい |

## <a name="azure-data-lake-analytics-linked-service"></a>Azure Data Lake Analytics リンク サービス
**Azure Data Lake Analytics** リンク サービスを作成して、Azure Data Lake Analytics コンピューティング サービスを Azure Data Factory にリンクします。 パイプラインの Data Lake Analytics U-SQL アクティビティは、このリンク サービスを参照します。 

次の表では、JSON 定義で使用される一般的なプロパティを説明しています。 サービス プリンシパル認証とユーザー資格情報認証のいずれかをさらに選ぶことができます。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| **type** |type プロパティは **AzureDataLakeAnalytics**に設定する必要があります。 |あり |
| **accountName** |Azure Data Lake Analytics アカウント名。 |あり |
| **dataLakeAnalyticsUri** |Azure Data Lake Analytics URI。 |いいえ |
| **subscriptionId** |Azure サブスクリプション ID |いいえ (指定されていない場合、Data Factory のサブスクリプションが使用されます)。 |
| **resourceGroupName** |Azure リソース グループ名 |いいえ (指定されていない場合は Data Factory のリソース グループが使用されます)。 |

### <a name="service-principal-authentication-recommended"></a>サービス プリンシパル認証 (推奨)
サービス プリンシパル認証を使うには、Azure Active Directory (Azure AD) でアプリケーション エンティティを登録して、Data Lake Store へのアクセス権を付与します。 詳細な手順については、「[サービス間認証](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md)」を参照してください。 次の値を記録しておきます。リンクされたサービスを定義するときに使います。
* アプリケーション ID
* アプリケーション キー 
* テナント ID

次のプロパティを指定して、サービス プリンシパル認証を使います。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| **servicePrincipalId** | アプリケーションのクライアント ID を取得します。 | あり |
| **servicePrincipalKey** | アプリケーションのキーを取得します。 | あり |
| **tenant** | アプリケーションが存在するテナントの情報 (ドメイン名またはテナント ID) を指定します。 Azure Portal の右上隅をマウスでポイントすることにより取得できます。 | あり |

**例: サービス プリンシパル認証**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

### <a name="user-credential-authentication"></a>ユーザー資格情報認証
また、次のプロパティを指定することで、Data Lake Analytics のユーザー資格情報認証を使用することもできます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| **authorization** | Data Factory エディターで **[承認する]** をクリックし、資格情報を入力すると、自動生成された承認 URL がこのプロパティに割り当てられます。 | あり |
| **sessionId** | OAuth 承認セッションからの OAuth セッション ID です。 各セッション ID は一意であり、1 回のみ使うことができます。 Data Factory エディターを使うと、この設定が自動的に生成されます。 | あり |

**例: ユーザー資格情報認証**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
            "authorization": "<authcode>",
            "sessionId": "<session ID>", 
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

#### <a name="token-expiration"></a>トークンの有効期限
**[認証]** ボタンを使用して生成した認証コードは、いずれ有効期限が切れます。 さまざまな種類のユーザー アカウントの有効期限については、次の表を参照してください。 認証**トークンの有効期限が切れる**と、次のエラー メッセージが表示される場合があります: 資格情報の操作エラー: invalid_grant - AADSTS70002: 資格情報の検証中にエラーが発生しました。 AADSTS70008: 指定されたアクセス権の付与は期限が切れているか、失効しています。 トレース ID: d18629e8-af88-43c5-88e3-d8419eb1fca1 相関 ID: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 タイムスタンプ: 2015-12-15 21:09:31Z"

| ユーザー タイプ | 有効期限 |
|:--- |:--- |
| Azure Active Directory で管理されていないユーザー アカウント (@hotmail.com、@live.com など)。 |12 時間 |
| Azure Active Directory (AAD) で管理されているユーザー アカウント |スライスの最後の実行から 14 日後。 <br/><br/>OAuth ベースのリンクされたサービスに基づいて、少なくとも 14 日間に 1 回スライスが実行する場合、90 日です。 |

このエラーを回避または解決するには、**トークンの有効期限が切れた**ときに、**[承認する]** ボタンを使用して再承認し、リンクされたサービスを再デプロイします。 次のようにコードを使用して、**sessionId** と **authorization** のプロパティ値をプログラムで生成することもできます。

```csharp
if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
    linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
{
    AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

    WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
    string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

    AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
    if (azureDataLakeStoreProperties != null)
    {
        azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeStoreProperties.Authorization = authorization;
    }

    AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
    if (azureDataLakeAnalyticsProperties != null)
    {
        azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeAnalyticsProperties.Authorization = authorization;
    }
}
```

コードで使用する Data Factory クラスの詳細については、「[AzureDataLakeStoreLinkedService Class](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)」、「[AzureDataLakeAnalyticsLinkedService クラス](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)」、および「[AuthorizationSessionGetResponse クラス](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx)」をご覧ください。 WindowsFormsWebAuthenticationDialog クラスの Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll に参照を追加します。 

## <a name="azure-sql-linked-service"></a>Azure SQL のリンクされたサービス
Azure SQL のリンクされたサービスを作成し、 [ストアド プロシージャ アクティビティ](data-factory-stored-proc-activity.md) で使用して、Data Factory パイプラインからストアド プロシージャを起動します。 このリンクされたサービスの詳細については、 [Azure SQL コネクタ](data-factory-azure-sql-connector.md#linked-service-properties) に関する記事を参照してください。

## <a name="azure-sql-data-warehouse-linked-service"></a>Azure SQL Data Warehouse のリンクされたサービス
Azure SQL Data Warehouse のリンクされたサービスを作成し、 [ストアド プロシージャ アクティビティ](data-factory-stored-proc-activity.md) で使用して、Data Factory パイプラインからストアド プロシージャを起動します。 このリンクされたサービスの詳細については、 [Azure SQL Data Warehouse コネクタ](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties) に関する記事をご覧ください。

## <a name="sql-server-linked-service"></a>SQL Server のリンクされたサービス
SQL Server のリンクされたサービスを作成し、 [ストアド プロシージャ アクティビティ](data-factory-stored-proc-activity.md) で使用して、Data Factory パイプラインからストアド プロシージャを起動します。 このリンクされたサービスの詳細については、 [SQL Server コネクタ](data-factory-sqlserver-connector.md#linked-service-properties) に関する記事をご覧ください。

