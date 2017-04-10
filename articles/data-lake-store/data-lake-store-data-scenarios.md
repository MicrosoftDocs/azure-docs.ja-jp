---
title: "Data Lake Store に関するデータ シナリオ | Microsoft Docs"
description: "Data Lake Store でのデータの取り込み、処理、ダウンロード、視覚化に使用するさまざまなシナリオとツールについて説明します"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 37409a71-a563-4bb7-bc46-2cbd426a2ece
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/17/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 4792b3f602c416457d8a0ec14c2939f704a002f6
ms.lasthandoff: 03/25/2017


---
# <a name="using-azure-data-lake-store-for-big-data-requirements"></a>ビッグ データの要件に対する Azure Data Lake Store の使用
ビッグ データの処理には主に 4 つの段階があります。

* データ ストアへの大量のデータの取り込み (リアルタイムまたは一括)
* データの処理
* データのダウンロード
* データの視覚化

この記事では、Azure Data Lake Store に関してこれらの段階について説明し、お客様のビッグ データのニーズを満たすために使用できるオプションとツールを確認します。

## <a name="ingest-data-into-data-lake-store"></a>Data Lake Store へのデータの取り込み
このセクションでは、さまざまなデータ ソースと、そのデータを Data Lake Store アカウントに取り込む各種方法について説明します。

![Data Lake Store へのデータの取り込み](./media/data-lake-store-data-scenarios/ingest-data.png "Data Lake Store へのデータの取り込み")

### <a name="ad-hoc-data"></a>アドホック データ
これは、ビッグ データ アプリケーションのプロトタイプ作成に使用される小規模なデータ セットを表します。 アドホック データの取り込み方法は、データ ソースに応じてさまざまです。

| データ ソース | 取り込みに使用するツール |
| --- | --- |
| ローカル コンピューター |<ul> <li>[Azure ポータル](/data-lake-store-get-started-portal.md)</li> <li>[Azure PowerShell](data-lake-store-get-started-powershell.md)</li> <li>[Azure クロスプラットフォーム CLI](data-lake-store-get-started-cli.md)</li> <li>[Data Lake Tools for Visual Studio](../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md#upload-source-data-files) </li></ul> |
| Azure Storage BLOB |<ul> <li>[Azure Data Factory](../data-factory/data-factory-azure-datalake-connector.md#example-copy-data-from-azure-blob-to-azure-data-lake-store)</li> <li>[AdlCopy ツール](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[HDInsight クラスター上で実行されている DistCp](data-lake-store-copy-data-wasb-distcp.md)</li> </ul> |

### <a name="streamed-data"></a>ストリーミングされたデータ
これは、アプリケーション、デバイス、センサーなどのさまざまなソースによって生成されるデータを表します。Data Lake Store へのこのデータの取り込みには各種ツールを使用できます。 これらのツールでは通常、イベントごとにリアルタイムでデータを取り込み、処理します。その後、さらにデータを処理できるように、Data Lake Store にイベントを一括で書き込みます。

使用できるツールは次のとおりです。

* [Azure Stream Analytics](../stream-analytics/stream-analytics-data-lake-output.md)。イベント ハブに取り込まれたイベントは、Azure Data Lake Store 出力を使用して Azure Data Lake に書き込むことができます。
* [Azure HDInsight Storm](../hdinsight/hdinsight-storm-write-data-lake-store.md)。Storm クラスターから Data Lake Store に直接データを書き込むことができます。
* [EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md)。イベント ハブからイベントを受け取り、[Data Lake Store .NET SDK](data-lake-store-get-started-net-sdk.md) を使用して Data Lake Store に書き込むことができます。

### <a name="relational-data"></a>リレーショナル データ
リレーショナル データベースのデータもソースとして扱うことができます。 一定の期間を経て、リレーショナル データベースには大量のデータが収集されます。ビッグ データのパイプラインを介して処理すると、これらのデータから重要な知見が得られます。 このようなデータを Data Lake Store に移動するには、次のツールを使用できます。

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Azure Data Factory](../data-factory/data-factory-data-movement-activities.md)

### <a name="web-server-log-data-upload-using-custom-applications"></a>Web サーバー ログ データ (カスタム アプリケーションを使用したアップロード)
Web サーバー ログ データの分析は、ビッグ データ アプリケーションの一般的な使用例で、大量のログ ファイルを Data Lake Store にアップロードすることを必要とするため、この種類のデータセットは特に呼び出されます。 このようなデータをアップロードするための独自のスクリプトまたはアプリケーションを記述するには、次のツールのいずれかを使用できます。

* [Azure クロスプラットフォーム CLI](data-lake-store-get-started-cli.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [Azure Data Lake Store .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Azure Data Factory](../data-factory/data-factory-data-movement-activities.md)

Web サーバー ログ データをアップロードする場合、または他の種類のデータ (ソーシャル センチメント データなど) をアップロードする場合には、独自のカスタム スクリプトやカスタム アプリケーションを記述することをお勧めします。 これにより、データをアップロードするコンポーネントをより大規模なビッグ データ アプリケーションの一部として含める柔軟性が得られるためです。 このコードは、スクリプトまたは単純なコマンド ライン ユーティリティの形をとる場合もあれば、ビッグ データの処理をビジネス アプリケーションまたはビジネス ソリューションに統合するために使用される場合もあります。

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Azure HDInsight クラスターに関連付けられたデータ
ほとんどの種類の HDInsight クラスター (Hadoop、HBase、Storm) では、データ ストレージ リポジトリとして Data Lake Store がサポートされています。 HDInsight クラスターは Azure Storage Blob (WASB) のデータにアクセスします。 パフォーマンスを向上させるために、クラスターに関連付けられた Data Lake Store アカウントに WASB のデータをコピーできます。 データのコピーには次のツールを使用できます。

* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)
* [AdlCopy サービス](data-lake-store-copy-data-azure-storage-blob.md)
* [Azure Data Factory](../data-factory/data-factory-azure-datalake-connector.md#example-copy-data-from-azure-blob-to-azure-data-lake-store)

### <a name="data-stored-in-on-premise-or-iaas-hadoop-clusters"></a>オンプレミスまたは IaaS Hadoop クラスターに格納されているデータ
大量のデータが既存の Hadoop クラスターの HDFS を使用するコンピューターにローカルに格納されている場合があります。 Hadoop クラスターは、オンプレミスのデプロイ内にある場合も、Azure の IaaS クラスター内にある場合もあります。 このようなデータを 1 回限りまたは定期的に Azure Data Lake Store にコピーする要件が存在します。 これを実現するために、さまざまな方法を使用できます。 以下の表に、それぞれの方法とそのトレードオフを示します。

| アプローチ | 詳細 | 長所 | 考慮事項 |
| --- | --- | --- | --- |
| Azure Data Factory (ADF) を使用して Hadoop クラスターから Azure Data Lake Store にデータを直接コピーする。 |[ADF ではデータ ソースとして HDFS をサポートしている。](../data-factory/data-factory-hdfs-connector.md) |ADF では、HDFS が最初からサポートされており、ファースト クラスのエンド ツー エンドの管理と監視が提供される。 |Data Management Gateway をオンプレミスまたは IaaS クラスターにデプロイする必要がある。 |
| Hadoop からデータをファイルとしてエクスポートする。 次に、適切なメカニズムを使用してファイルを Azure Data Lake Store にコピーする。 |次のいずれかを使用してファイルを Azure Data Lake Store にコピーできる。 <ul><li>[Azure PowerShell (Windows OS)](data-lake-store-get-started-powershell.md)</li><li>[Azure クロスプラットフォーム CLI (Windows OS 以外)](data-lake-store-get-started-cli.md)</li><li>Data Lake Store SDK を使用するカスタム アプリ</li></ul> |手軽に開始できる。 カスタマイズしたアップロードを行うことができる。 |複数のテクノロジを含む複数ステップのプロセス。 カスタマイズされたツールという性質上、時間の経過と共に管理と監視が困難になる。 |
| Distcp を使用して、Hadoop から Azure Storage にデータをコピーする。 次に、適切なメカニズムを使用してデータを Azure Storage から Azure Data Lake Store にコピーする。 |次のいずれかを使用してデータを Azure Storage から Data Lake Store にコピーできる。 <ul><li>[Azure Data Factory](../data-factory/data-factory-data-movement-activities.md)</li><li>[AdlCopy ツール](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[HDInsight クラスター上で実行されている Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)</li></ul> |オープン ソースのツールを使用できる。 |複数のテクノロジを含む複数ステップのプロセス。 |

### <a name="really-large-datasets"></a>非常に大規模なデータセット
数 TB に及ぶデータセットをアップロードする場合、上記の方法では速度が遅く、コストがかかることがあります。 このような場合は、次のオプションを使用できます。

* **Azure ExpressRoute の使用**。 Azure ExpressRoute を使用すると、Azure データ センターとお客様のオンプレミスのインフラストラクチャとの間でプライベート接続を作成できます。 これにより、大量のデータを転送するための信頼性の高いオプションが提供されます。 詳細については、 [Azure ExpressRoute のドキュメント](../expressroute/expressroute-introduction.md)をご覧ください。
* **データの "オフライン" アップロード**。 何らかの理由で Azure ExpressRoute が使用できない場合は、 [Azure Import/Export サービス](../storage/storage-import-export-service.md) を利用して、データが格納されたハード ディスク ドライブを Azure データ センターに発送してください。 データはまず Azure Storage BLOB にアップロードされます。 その後、[Azure Data Factory](../data-factory/data-factory-azure-datalake-connector.md#example-copy-data-from-azure-blob-to-azure-data-lake-store) または [AdlCopy ツール](data-lake-store-copy-data-azure-storage-blob.md)を使って、Azure Storage BLOB から Data Lake Store にデータをコピーできます。

  > [!NOTE]
  > Import/Export サービスを利用する場合、Azure データ センターに送るディスク上のファイル サイズは 195 GB 以下である必要があります。
  >
  >

## <a name="process-data-stored-in-data-lake-store"></a>Data Lake Store に格納されているデータの処理
Data Lake Store でデータが利用できるようになったら、サポートされているビッグ データ アプリケーションを使用して、そのデータの分析を実行できます。 現在、Data Lake Store に格納されたデータに対してデータ分析ジョブを実行するには、Azure HDInsight と Azure Data Lake Analytics が使用できます。

![Data Lake Store 内のデータの分析](./media/data-lake-store-data-scenarios/analyze-data.png "Data Lake Store 内のデータの分析")

次の例を参考にしてください。

* [Data Lake Store をストレージとして使用して HDInsight クラスターを作成する](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Data Lake Store で Azure Data Lake Analytics を使用する](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

## <a name="download-data-from-data-lake-store"></a>Data Lake Store からのデータのダウンロード
次のようなシナリオでは、Azure Data Lake Store からデータをダウンロードしたり、移動したりすることもできます。

* 既存のデータ処理パイプラインとのインターフェイスとなる他のリポジトリにデータを移動する。 たとえば、Data Lake Store から Azure SQL Database またはオンプレミスの SQL Server にデータを移動できます。
* アプリケーション プロトタイプの作成中に IDE 環境で処理するために、ローカル コンピューターにデータをダウンロードする。

![Data Lake Store からのデータの出力](./media/data-lake-store-data-scenarios/egress-data.png "Data Lake Store からのデータの出力")

このような場合、次のオプションのいずれかを使用できます。

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Azure Data Factory](../data-factory/data-factory-data-movement-activities.md)
* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)

次の方法を使用して、Data Lake Store からデータをダウンロードするための独自のスクリプトやアプリケーションを記述することもできます。

* [Azure クロスプラットフォーム CLI](data-lake-store-get-started-cli.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [Azure Data Lake Store .NET SDK](data-lake-store-get-started-net-sdk.md)

## <a name="visualize-data-in-data-lake-store"></a>Data Lake Store 内のデータの視覚化
複数のサービスを組み合わせて使用することで、Data Lake Store に格納されたデータを視覚的に表現することができます。

![Data Lake Store 内のデータの視覚化](./media/data-lake-store-data-scenarios/visualize-data.png "Data Lake Store 内のデータの視覚化")

* まず、[Azure Data Factory を使って、Data Lake Store から Azure SQL Data Warehouse にデータを移動](../data-factory/data-factory-data-movement-activities.md#supported-data-stores-and-formats)します。
* その後、 [Power BI を Azure SQL Data Warehouse と統合](../sql-data-warehouse/sql-data-warehouse-integrate-power-bi.md) して、データを視覚的に表現することができます。

