---
title: Azure Data Factory でサポートされるコンピューティング環境 | Microsoft Docs
description: Azure Data Factory パイプライン (Azure HDInsight など) でデータの変換または処理に使うことができるコンピューティング環境について説明します。
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 6877a7e8-1a58-4cfb-bbd3-252ac72e4145
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 0e0a249c53c90d3d8d03dcdb5fbb4f11f31c54df
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2019
ms.locfileid: "57545164"
---
# <a name="compute-environments-supported-by-azure-data-factory"></a>Azure Data Factory でサポートされるコンピューティング環境
> [!NOTE]
> この記事は、Azure Data Factory のバージョン 1 に適用されます。 現在のバージョンの Data Factory サービスを使用している場合は、[コンピューティングのリンクされたサービス](../compute-linked-services.md)に関するページを参照してください。

この記事では、データの処理または変換に利用できるコンピューティング環境について説明します。 また、こうしたコンピューティング環境を Azure データ ファクトリに関連付けるリンクされたサービスを構成するときに、Data Factory でサポートされるさまざまな構成 (オンデマンドと Bring Your Own) に関する詳細も提供します。

次の表は、Data Factory でサポートされているコンピューティング環境と、その環境で実行できるアクティビティの一覧です。 

| Compute 環境                      | Activities                               |
| ---------------------------------------- | ---------------------------------------- |
| [オンデマンド Azure HDInsight クラスター](#azure-hdinsight-on-demand-linked-service)または[独自の HDInsight クラスター](#azure-hdinsight-linked-service) | [DotNet](data-factory-use-custom-activities.md)、[Hive](data-factory-hive-activity.md)、[Pig](data-factory-pig-activity.md)、[MapReduce](data-factory-map-reduce.md)、[Hadoop Streaming](data-factory-hadoop-streaming-activity.md) |
| [Azure Batch](#azure-batch-linked-service) | [DotNet](data-factory-use-custom-activities.md) |
| [Azure Machine Learning](#azure-machine-learning-linked-service) | [Machine Learning アクティビティ:バッチ実行とリソースの更新](data-factory-azure-ml-batch-execution-activity.md) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics-linked-service) | [Data Lake Analytics U-SQL](data-factory-usql-activity.md) |
| [Azure SQL](#azure-sql-linked-service)、[Azure SQL Data Warehouse](#azure-sql-data-warehouse-linked-service)、[SQL Server](#sql-server-linked-service) | [ストアド プロシージャ アクティビティ](data-factory-stored-proc-activity.md) |

## <a name="supported-hdinsight-versions-in-azure-data-factory"></a>Data Factory でサポートされる HDInsight バージョン
Azure HDInsight では、いつでもデプロイできる Hadoop クラスター バージョンが複数サポートされます。 サポートされる各バージョンにより、特定のバージョンの Hortonworks Data Platform (HDP) ディストリビューションと、そのディストリビューションの一連のコンポーネントが作成されます。 

Microsoft では、HDInsight のサポートされるバージョンの一覧を、最新の Hadoop エコシステム コンポーネントと修正プログラムで更新します。 詳細については、「[サポートされる HDInsight のバージョン](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions)」を参照してください。

> [!IMPORTANT]
> Linux ベース HDInsight バージョン 3.3 は、2017 年 7 月 31 日に終了しました。 Data Factory バージョン 1 オンデマンド HDInsight のリンクされたサービスのお客様には、2017 年 12 月 15 日まで、HDInsight の新しいバージョンをテストしアップグレードするために提供されます。 Windows ベースの HDInsight は、2018 年 7 月 31 日に終了します。
>
> 

### <a name="after-the-retirement-date"></a>提供終了日以降 

2017 年 12 月 15 日以降:

- Data Factory バージョン 1 のオンデマンド HDInsight のリンクされたサービスを使用して、Linux ベースの HDInsight バージョン 3.3 以前のクラスターを作成できなくなります。 
- 既存の Data Factory バージョン 1 のオンデマンド HDInsight のリンクされたサービスの JSON 定義で、[**osType** や **Version** のプロパティ](https://docs.microsoft.com/azure/data-factory/v1/data-factory-compute-linked-services#azure-hdinsight-on-demand-linked-service)が明示的に指定されていない場合、既定値は **Version=3.1, osType=Windows** から **Version=\<最新の HDI 既定バージョン\>(https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning), osType=Linux** に変更されます。

2018 年 7月 31 日以降:

- Data Factory バージョン 1 のオンデマンド HDInsight のリンクされたサービスを使用して、任意のバージョンの Windows ベースの HDInsight クラスターを作成できなくなります。 

### <a name="recommended-actions"></a>推奨アクション 

- 最新の Hadoop エコシステム コンポーネントと修正プログラムを確実に使用可能にするには、影響を受ける Data Factory バージョン 1 オンデマンド HDInsight のリンクされたサービス定義の [**osType** および **Version** プロパティ](https://docs.microsoft.com/azure/data-factory/v1/data-factory-compute-linked-services#azure-hdinsight-on-demand-linked-service)を、新しい Linux ベースの HDInsight バージョン (HDInsight 3.6) に更新します。 
- 2017 年 12 月 15 日より前に、影響を受けるリンクされたサービスを参照する Data Factory バージョン 1 Hive、Pig、MapReduce、および Hadoop ストリーミング アクティビティをテストします。 これが新しい **osType** と **Version** の既定値 (**Version=3.6**、**osType=Linux**) またはアップグレード後の明示的な HDInsight バージョンおよび OS タイプと互換性があることを確認します。 
  互換性の詳細については、「[Windows ベースの HDInsight クラスターから Linux ベースのクラスターへの移行](https://docs.microsoft.com/azure/hdinsight/hdinsight-migrate-from-windows-to-linux)」および「[HDInsight で使用可能な Hadoop コンポーネントとバージョンとは](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#hortonworks-release-notes-associated-with-hdinsight-versions)」を参照してください。 
- Data Factory バージョン 1 オンデマンド HDInsight のリンクされたサービスを引き続き使用して、Windows ベースの HDInsight クラスターを作成するには、2017 年 12 月 15 日より前に **osType** を **Windows** に明示的に設定します。 2018 年 7 月 31日より前に、Linux ベースの HDInsight クラスターに移行することをお勧めします。 
- オンデマンド HDInsight のリンクされたサービスを使用して、Data Factory バージョン 1 DotNet カスタム アクティビティを実行している場合は、Azure Batch のリンクされたサービスを使用するように DotNet カスタム アクティビティ JSON 定義を更新します。 詳細については、[Data Factory パイプラインでのカスタム アクティビティの使用](https://docs.microsoft.com/azure/data-factory/v1/data-factory-use-custom-activities)に関する記事をご覧ください。 

> [!Note]
> Data Factory バージョン 1 で既存の Bring Your Own クラスター HDInsight のリンクされたデバイスを使用する場合、または Azure Data Factory で Bring Your Own デバイスおよびオンデマンド HDInsight のリンクされたサービスを使用する場合、操作は不要です。 こうしたシナリオでは、HDInsight クラスターの最新バージョンのサポート ポリシーが既に強制されています。 
>
> 


## <a name="on-demand-compute-environment"></a>オンデマンドのコンピューティング環境
オンデマンド構成では、Data Factory サービスによってコンピューティング環境が完全に管理されます。 コンピューティング環境は、データを処理するためのジョブが送信される前に Data Factory により自動的に作成されます。 ジョブが完了すると、コンピューティング環境は Data Factory によって削除されます。 

オンデマンドのコンピューティング環境に対して、リンクされたサービスを作成できます。 そのリンクされたサービスを使用してコンピューティング環境を構成し、ジョブ実行、クラスター管理、およびブートストラップ アクションの詳細設定を制御します。

> [!NOTE]
> 現時点では、オンデマンド構成は HDInsight クラスターにのみ対応しています。
> 

## <a name="azure-hdinsight-on-demand-linked-service"></a>Azure HDInsight のオンデマンドのリンクされたサービス
Data Factory では、データを処理するための Windows ベースまたは Linux ベースのオンデマンド HDInsight クラスターを自動的に作成できます。 クラスターは、そのクラスターに関連付けられているストレージ アカウントと同じリージョンに作成されます。 クラスターを作成するには、JSON **linkedServiceName** プロパティを使用します。

オンデマンド HDInsight のリンクされたサービスに関する次の "*重要な*" 点に注意してください。

* オンデマンド HDInsight クラスターは Azure サブスクリプションには表示されません。 Data Factory サービスがユーザーに代わってオンデマンド HDInsight クラスターを管理します。
* オンデマンド HDInsight クラスターで実行されるジョブのログは、HDInsight クラスターに関連付けられているストレージ アカウントにコピーされます。 こうしたログにアクセスするには、Azure Portal で **[アクティビティ実行の詳細]** ウィンドウに移動します。 詳細については、[パイプラインの監視と管理](data-factory-monitor-manage-pipelines.md)に関するページをご覧ください。
* HDInsight クラスターが稼動し、ジョブを実行している時間に対してのみ課金されます。

> [!IMPORTANT]
> オンデマンド HDInsight クラスターをプロビジョニングするには、通常、"*20 分*" 以上かかります。
>
> 

### <a name="example"></a>例
次の JSON は、Linux ベースのオンデマンド HDInsight のリンクされたサービスを定義します。 Data Factory がデータ スライスを処理するとき、"*Linux ベース*" の HDInsight クラスターが自動的に作成されます。 

```json
{
    "name": "HDInsightOnDemandLinkedService",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "version": "3.6",
            "osType": "Linux",
            "clusterSize": 1,
            "timeToLive": "00:05:00",            
            "linkedServiceName": "AzureStorageLinkedService"
        }
    }
}
```

> [!IMPORTANT]
> HDInsight クラスターは、JSON **linkedServiceName** で指定した Azure Blob Storage に "*既定のコンテナー*" を作成します。 クラスターが削除されても、HDInsight はこのコンテナーを意図的に削除しません。 オンデマンド HDInsight のリンクされたサービスでは、既存のライブ クラスター (**timeToLive**) がある場合を除き、スライスの処理が必要になるたびに HDInsight クラスターが作成されます。 クラスターは、処理が終了すると削除されます。 
>
> 処理されるスライスが多いほど、Blob Storage 内のコンテナーも増えます。 ジョブのトラブルシューティング用コンテナーが不要な場合は、コンテナーを削除してストレージ コストを削減できます。 これらのコンテナーの名前は、`adf<your Data Factory name>-<linked service name>-<date and time>` 形式になります。 [Microsoft Storage Explorer](https://storageexplorer.com/) などのツールを使用して、Blob Storage のコンテナーを削除できます。
>
> 

### <a name="properties"></a>Properties
| プロパティ                     | 説明                              | 必須 |
| ---------------------------- | ---------------------------------------- | -------- |
| type                         | type プロパティを **HDInsightOnDemand** に設定します。 | はい      |
| clusterSize                  | クラスターの worker とデータ ノードの数です。 HDInsight クラスターは、このプロパティで指定した worker ノードの数に加え、2 つのヘッド ノードを使用して作成されます。 ノードのサイズは Standard_D3 (4 コア) です。 4 worker ノード クラスターのコアは 24 個になります (worker ノード用に 4\*4 = 16 個のコアと、ヘッド ノード用に 2\*4 = 8 個のコア)。 Standard_D3 レベルの詳細については、[HDInsight での Linux ベースの Hadoop クラスターの作成](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)に関するページをご覧ください。 | はい      |
| timeToLive                   | オンデマンド HDInsight クラスターに許可されるアイドル時間です。 他のアクティブなジョブがクラスターにない場合、アクティビティの実行が完了したときに、オンデマンド HDInsight クラスターが起動状態を維持する時間を指定します。<br /><br />たとえば、アクティビティ実行に 6 分かかるときに **timeToLive** が 5 分に設定されている場合、アクティビティの実行の 6 分間の処理の後、クラスターが起動状態を 5 分間維持します。 別のアクティビティの実行が 6 分の時間枠で実行される場合、それは同じクラスターで処理されます。<br /><br />オンデマンド HDInsight クラスターの作成は高額な作業です (時間がかかる場合もあります)。 この設定を必要に応じて使用し、オンデマンド HDInsight クラスターを再利用することで、データ ファクトリのパフォーマンスを改善します。<br /><br />**timeToLive** 値を **0** に設定した場合、クラスターは、アクティビティの実行の完了直後に削除されます。 一方、高い値を設定すると、クラスターでは不必要にアイドル状態が維持され、コストの上昇を招きます。 重要なのは、ニーズに合わせて適切な値を設定することです。<br /><br />**timeToLive** 値が適切に設定されている場合、複数のパイプラインでオンデマンド HDInsight クラスターのインスタンスを共有できます。 | はい      |
| version                      | HDInsight クラスターのバージョン。 許可されている HDInsight バージョンについては、「[サポートされる HDInsight のバージョン](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#supported-hdinsight-versions)」を参照してください。 この値が指定されていない場合は、[最新 HDI の既定のバージョン](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning)が使用されます。 | いいえ        |
| linkedServiceName            | データを保存し、処理するためにオンデマンド クラスターで使用される Azure Storage のリンクされたサービスです。 HDInsight クラスターは、このストレージ アカウントと同じリージョンに作成されます。<p>現時点では、Azure Data Lake Store をストレージとして使用するオンデマンド HDInsight クラスターを作成することはできません。 HDInsight 処理の結果データを Data Lake Store に保存する必要がある場合は、コピー アクティビティを使用して、Blob Storage から Data Lake Store にデータをコピーします。 </p> | はい      |
| additionalLinkedServiceNames | HDInsight のリンクされたサービスの追加ストレージ アカウントを指定します。 ストレージ アカウントは、Data Factory によって自動的に登録されます。 こうしたストレージ アカウントは、HDInsight クラスターと同じリージョンに存在する必要があります。 HDInsight クラスターは、**linkedServiceName** プロパティで指定されているストレージ アカウントと同じリージョンで作成されます。 | いいえ        |
| osType                       | オペレーティング システムの種類。 使用可能な値は **Linux** と **Windows** です。 この値を指定しない場合は、**Linux** が使用されます。  <br /><br />Linux ベースの HDInsight クラスターを使用することを強くお勧めします。 Windows 向けの HDInsight の提供終了日は 2018 年 7 月 31 日です。 | いいえ        |
| hcatalogLinkedServiceName    | HCatalog データベースを指定する Azure SQL のリンクされたサービスの名前。 オンデマンド HDInsight クラスターは、SQL データベースを metastore として使用して作成されます。 | いいえ        |

#### <a name="example-linkedservicenames-json"></a>例:LinkedServiceNames JSON

```json
"additionalLinkedServiceNames": [
    "otherLinkedServiceName1",
    "otherLinkedServiceName2"
  ]
```

### <a name="advanced-properties"></a>詳細なプロパティ
オンデマンド HDInsight クラスターを詳細に構成するには、次のプロパティを指定できます。

| プロパティ               | 説明                              | 必須 |
| :--------------------- | :--------------------------------------- | :------- |
| coreConfiguration      | 作成する HDInsight クラスターにコア構成パラメーター (core-site.xml) を指定します。 | いいえ        |
| hBaseConfiguration     | HDInsight クラスターに HBase 構成パラメーター (hbase-site.xml) を指定します。 | いいえ        |
| hdfsConfiguration      | HDInsight クラスターに HDFS 構成パラメーター (hdfs-site.xml) を指定します。 | いいえ        |
| hiveConfiguration      | HDInsight クラスターに Hive 構成パラメーター (hive-site.xml) を指定します。 | いいえ        |
| mapReduceConfiguration | HDInsight クラスターに MapReduce 構成パラメーター (mapred-site.xml) を指定します。 | いいえ        |
| oozieConfiguration     | HDInsight クラスターに Oozie 構成パラメーター (oozie-site.xml) を指定します。 | いいえ        |
| stormConfiguration     | HDInsight クラスターに Storm 構成パラメーター (storm-site.xml) を指定します。 | いいえ        |
| yarnConfiguration      | HDInsight クラスターに YARN 構成パラメーター (yarn-site.xml) を指定します。 | いいえ        |

#### <a name="example-on-demand-hdinsight-cluster-configuration-with-advanced-properties"></a>例:オンデマンド HDInsight クラスター構成と詳細なプロパティ

```json
{
  "name": " HDInsightOnDemandLinkedService",
  "properties": {
    "type": "HDInsightOnDemand",
    "typeProperties": {
      "version": "3.6",
      "osType": "Linux",
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
ヘッド ノード、データ ノード、および ZooKeeper ノードのサイズを指定するには、次のプロパティを使用します。 

| プロパティ          | 説明                              | 必須 |
| :---------------- | :--------------------------------------- | :------- |
| headNodeSize      | ヘッド ノードのサイズを設定します。 既定値は **Standard_D3** です。 詳細については、「[ノード サイズの指定](#specify-node-sizes)」を参照してください。 | いいえ        |
| dataNodeSize      | データ ノードのサイズを設定します。 既定値は **Standard_D3** です。 | いいえ        |
| zookeeperNodeSize | ZooKeeper ノードのサイズを設定します。 既定値は **Standard_D3** です。 | いいえ        |

#### <a name="specify-node-sizes"></a>ノード サイズの指定
前のセクションで説明したプロパティに対して指定する必要がある文字列値については、[仮想マシンのサイズ](../../virtual-machines/linux/sizes.md)に関するページをご覧ください。 値は、[仮想マシンのサイズ](../../virtual-machines/linux/sizes.md)に関するページで参照されているコマンドレットおよび API に準拠している必要があります。 Large (既定値) データ ノードのサイズは 7 GB のメモリ容量です。 これはシナリオによっては不十分な場合があります。 

D4 サイズのヘッド ノードと worker ノードを作成する場合は、**headNodeSize** プロパティと **dataNodeSize** プロパティの値として **Standard_D4** を指定します。 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

こうしたプロパティに不適切な値を設定すると、次のメッセージが表示される場合があります。

  クラスターを作成できませんでした。 例外:クラスター作成操作を完了できません。 コード '400' で操作が失敗しました。 取り残されたクラスターの状態:'Error'。 メッセージ:'PreClusterCreationValidationFailure'。 
  
このメッセージが表示された場合は、[仮想マシンのサイズ](../../virtual-machines/linux/sizes.md)に関するページの表に記載されているコマンドレットと API 名を使用していることを確認してください。  

> [!NOTE]
> 現時点では、Data Factory は、Data Lake Store をプライマリ ストアとして使用する HDInsight クラスターをサポートしていません。 HDInsight クラスターのプライマリ ストアとして Azure Storage を使用してください。 
>
> 


## <a name="bring-your-own-compute-environment"></a>Bring Your Own のコンピューティング環境
既存のコンピューティング環境を、リンクされたサービスとして Data Factory に登録できます。 コンピューティング環境はユーザーにより管理されます。 Data Factory サービスは、コンピューティング環境を使用してアクティビティを実行します。

この種類の構成は次のコンピューティング環境でサポートされています。

* Azure HDInsight
* Azure Batch
* Azure Machine Learning
* Azure Data Lake Analytics
* Azure SQL Database、Azure SQL Data Warehouse、SQL Server

## <a name="azure-hdinsight-linked-service"></a>Azure HDInsight のリンクされたサービス
HDInsight のリンクされたサービスを作成し、独自の HDInsight クラスターを Data Factory に登録できます。

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

### <a name="properties"></a>Properties
| プロパティ          | 説明                              | 必須 |
| ----------------- | ---------------------------------------- | -------- |
| type              | type プロパティを **HDInsight** に設定します。 | はい      |
| clusterUri        | HDInsight クラスターの URI です。        | はい      |
| username          | 既存の HDInsight クラスターへの接続に使用するユーザー アカウントの名前です。 | はい      |
| password          | ユーザー アカウントのパスワードです。   | はい      |
| linkedServiceName | HDInsight クラスターで使用される Blob Storage を参照するストレージのリンクされたサービスの名前です。 <p>現在、Data Lake Store のリンクされたサービスをこのプロパティに指定することはできません。 HDInsight クラスターが Data Lake Store にアクセスできる場合、Hive または Pig スクリプトから Data Lake Store のデータにアクセスできます。 </p> | はい      |

## <a name="azure-batch-linked-service"></a>Azure Batch のリンクされたサービス
Batch のリンクされたサービスを作成し、仮想マシン (VM) の Batch プールをデータ ファクトリに登録できます。 Microsoft .NET カスタム アクティビティを実行するには、Batch または HDInsight のいずれかを使用します。

Batch サービスを初めて利用する場合:

* [Azure Batch の基本](../../batch/batch-technical-overview.md)についてご確認ください。
* [New-AzureBatchAccount](https://msdn.microsoft.com/library/mt125880.aspx) コマンドレットについてご確認ください。 このコマンドレットを使用して、Batch アカウントを作成します。 また、[Azure Portal](../../batch/batch-account-create-portal.md) を使用して、Batch アカウントを作成することもできます。 このコマンドレットの使用の詳細については、[PowerShell を使用した Batch アカウントの管理](https://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx)に関するページをご覧ください。
* [New-AzureBatchPool](https://msdn.microsoft.com/library/mt125936.aspx) コマンドレットについてご確認ください。 このコマンドレットを使用して、Batch プールを作成します。

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

**accountName** プロパティについては、**.\<region name\>** をバッチ アカウントの名前に追加します。 例: 

```json
"accountName": "mybatchaccount.eastus"
```

また、**batchUri** エンドポイントを指定することもできます。 例: 

```json
"accountName": "adfteam",
"batchUri": "https://eastus.batch.azure.com",
```

### <a name="properties"></a>Properties
| プロパティ          | 説明                              | 必須 |
| ----------------- | ---------------------------------------- | -------- |
| type              | type プロパティを **AzureBatch** に設定します。 | はい      |
| accountName       | Batch アカウントの名前。         | はい      |
| accessKey         | Batch アカウントのアクセス キー。  | はい      |
| poolName          | VM のプールの名前。    | はい      |
| linkedServiceName | この Batch のリンクされたサービスに関連付けられている、ストレージのリンクされたサービスの名前。 このリンクされたサービスは、アクティビティの実行およびアクティビティの実行ログの保存に必要なステージング ファイルに使用されます。 | はい      |

## <a name="azure-machine-learning-linked-service"></a>Azure Machine Learning のリンクされたサービス
Machine Learning のリンクされたサービスを作成して、Machine Learning のバッチ スコアリング エンドポイントをデータ ファクトリに登録できます。

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

### <a name="properties"></a>Properties
| プロパティ   | 説明                              | 必須 |
| ---------- | ---------------------------------------- | -------- |
| type       | type プロパティを **AzureML** に設定します。 | はい      |
| mlEndpoint | バッチ スコアリング URL です。                   | はい      |
| apiKey     | 公開されたワークスペース モデルの API です。     | はい      |

## <a name="azure-data-lake-analytics-linked-service"></a>Azure Data Lake Analytics リンク サービス
Data Lake Analytics のリンクされたサービスを作成して、Data Lake Analytics コンピューティング サービスを Azure データ ファクトリにリンクできます。 パイプラインの Data Lake Analytics U-SQL アクティビティは、このリンク サービスを参照します。 

次の表で、JSON 定義で使用される一般的なプロパティについて説明します。

| プロパティ                 | 説明                              | 必須                                 |
| ------------------------ | ---------------------------------------- | ---------------------------------------- |
| type                 | type プロパティを **AzureDataLakeAnalytics** に設定します。 | はい                                      |
| accountName          | Data Lake Analytics アカウント名です。  | はい                                      |
| dataLakeAnalyticsUri | Data Lake Analytics URI です。           | いいえ                                        |
| subscriptionId       | Azure サブスクリプション ID です。                    | いいえ <br /><br />(指定されていない場合は、データ ファクトリのサブスクリプションが使用されます)。 |
| resourceGroupName    | Azure リソース グループ名です。                | いいえ <br /><br /> (指定されていない場合は、データ ファクトリのリソース グループが使用されます)。 |

### <a name="authentication-options"></a>認証オプション
Data Lake Analytics のリンクされたサービスについては、サービス プリンシパルまたはユーザー資格情報のいずれかを使用した認証を選択できます。

#### <a name="service-principal-authentication-recommended"></a>サービス プリンシパル認証 (推奨)
サービス プリンシパル認証を使用するには、Azure Active Directory (Azure AD) でアプリケーション エンティティを登録します。 その後、Data Lake Store へのアクセス権を Azure AD に付与します。 詳細な手順については、「[サービス間認証](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md)」を参照してください。 次の値を記録しておきます。リンクされたサービスを定義するときに使います。
* アプリケーション ID
* アプリケーション キー 
* テナント ID

次のプロパティを指定して、サービス プリンシパル認証を使います。

| プロパティ                | 説明                              | 必須 |
| :---------------------- | :--------------------------------------- | :------- |
| servicePrincipalId  | アプリケーションのクライアント ID です。     | はい      |
| servicePrincipalKey | アプリケーションのキーです。           | はい      |
| tenant              | アプリケーションが配置されているテナントの情報 (ドメイン名またはテナント ID)。 この情報を取得するには、Azure Portal の右上隅にマウス ポインターを移動します。 | はい      |

**例:サービス プリンシパルの認証**
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

#### <a name="user-credential-authentication"></a>ユーザー資格情報認証
Data Lake Analytics のユーザー資格情報認証については、次のプロパティを指定します。

| プロパティ          | 説明                              | 必須 |
| :---------------- | :--------------------------------------- | :------- |
| authorization | Data Factory エディターで、**[承認する]** を選択します。 自動生成された認可 URL をこのプロパティに割り当てる資格情報を入力します。 | はい      |
| sessionId     | OAuth 認可セッションからの OAuth セッション ID です。 各セッション ID は一意であり、1 回のみ使うことができます。 Data Factory エディターを使用すると、この設定が自動的に生成されます。 | はい      |

**例:ユーザー資格情報認証**
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
**[承認する]** を選択して生成した認可コードは、設定した期間が過ぎると有効期限が切れます。 

認証トークンの有効期限が切れると、次のエラー メッセージが表示される場合があります。 

  資格情報の操作エラー: invalid_grant - AADSTS70002:Error validating credentials. (資格情報の検証中にエラーが発生しました。) AADSTS70008:指定されたアクセス権の付与は期限が切れているか、失効しています。 トレース ID: d18629e8-af88-43c5-88e3-d8419eb1fca1 相関 ID: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 タイムスタンプ:2015-12-15 21:09:31Z

次の表は、ユーザー アカウントの種類ごとに有効期限を示しています。 

| ユーザー タイプ                                | 有効期限                            |
| :--------------------------------------- | :--------------------------------------- |
| Azure AD で管理 "*されない*" ユーザー アカウント (Hotmail、Live など) | 12 時間。                                 |
| Azure AD で管理 "*される*" ユーザー アカウント | スライスの最後の実行から 14 日後。 <br /><br />OAuth ベースのリンクされたサービスに基づくスライスが 14 日ごとに少なくとも 1 回実行される場合は 90 日。 |

このエラーを回避または解決するには、トークンの有効期限が切れたときに、**[承認する]** ボタンを選択して再承認します。 その後、リンクされたサービスを再デプロイします。 次のコードを使って、**sessionId** および **authorization** プロパティの値をプログラムで生成することもできます。

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

このコード例で使用されている Data Factory クラスの詳細については、次を参照してください。
* [AzureDataLakeStoreLinkedService クラス](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)
* [AzureDataLakeAnalyticsLinkedService クラス](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)
* [AuthorizationSessionGetResponse クラス](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx)

**WindowsFormsWebAuthenticationDialog** クラスの Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll に参照を追加します。 

## <a name="azure-sql-linked-service"></a>Azure SQL のリンクされたサービス
SQL のリンクされたサービスを作成し、[ストアド プロシージャ アクティビティ](data-factory-stored-proc-activity.md)で使用して、Data Factory パイプラインからストアド プロシージャを起動できます。 詳細については、[Azure SQL コネクタ](data-factory-azure-sql-connector.md#linked-service-properties)に関するページをご覧ください。

## <a name="azure-sql-data-warehouse-linked-service"></a>Azure SQL Data Warehouse のリンクされたサービス
SQL Data Warehouse のリンクされたサービスを作成し、[ストアド プロシージャ アクティビティ](data-factory-stored-proc-activity.md)で使用して、Data Factory パイプラインからストアド プロシージャを起動できます。 詳細については、[Azure SQL Data Warehouse コネクタ](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties)に関する記事をご覧ください。

## <a name="sql-server-linked-service"></a>SQL Server のリンクされたサービス
SQL Server のリンクされたサービスを作成し、[ストアド プロシージャ アクティビティ](data-factory-stored-proc-activity.md) で使用して、Data Factory パイプラインからストアド プロシージャを起動できます。 詳細については、[SQL Server コネクタ](data-factory-sqlserver-connector.md#linked-service-properties)に関する記事を参照してください。

