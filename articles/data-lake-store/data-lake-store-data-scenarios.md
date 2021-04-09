---
title: Data Lake Storage Gen1 に関するデータ シナリオ | Microsoft Docs
description: Data Lake Storage Gen1 (旧称 Azure Data Lake Store) でのデータの取り込み、処理、ダウンロード、視覚化に使用するさまざまなシナリオとツールについて説明します。
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: twooley
ms.openlocfilehash: 0bd895b0c19293a199b2a9b135915b7dc45b9e5e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98702499"
---
# <a name="using-azure-data-lake-storage-gen1-for-big-data-requirements"></a>Data Lake Storage Gen1 を使用してビッグ データの要件に対応する

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

ビッグ データの処理には主に 4 つの段階があります。

* データ ストアへの大量のデータの取り込み (リアルタイムまたは一括)
* データの処理
* データのダウンロード
* データの視覚化

この記事では、Azure Data Lake Storage Gen1 に関してこれらの段階について説明し、お客様のビッグ データのニーズを満たすために使用できるオプションとツールを確認します。

## <a name="ingest-data-into-data-lake-storage-gen1"></a>Data Lake Storage Gen1 にデータを取り込む
このセクションでは、さまざまなデータ ソースと、そのデータを Data Lake Storage Gen1 アカウントに取り込む各種方法について説明します。

![Data Lake Storage Gen1 にデータを取り込む](./media/data-lake-store-data-scenarios/ingest-data.png "Data Lake Storage Gen1 にデータを取り込む")

### <a name="ad-hoc-data"></a>アドホック データ
これは、ビッグ データ アプリケーションのプロトタイプ作成に使用される小規模なデータ セットを表します。 アドホック データの取り込み方法は、データ ソースに応じてさまざまです。

| Data Source | 取り込みに使用するツール |
| --- | --- |
| ローカル コンピューター |<ul> <li>[Azure Portal](data-lake-store-get-started-portal.md)</li> <li>[Azure PowerShell](data-lake-store-get-started-powershell.md)</li> <li>[Azure CLI](data-lake-store-get-started-cli-2.0.md)</li> <li>[Data Lake Tools for Visual Studio](../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md) </li></ul> |
| Azure Storage BLOB |<ul> <li>[Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)</li> <li>[AdlCopy ツール](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[HDInsight クラスター上で実行されている DistCp](data-lake-store-copy-data-wasb-distcp.md)</li> </ul> |

### <a name="streamed-data"></a>ストリーミングされたデータ
これは、アプリケーション、デバイス、センサーなどのさまざまなソースによって生成されるデータを表します。Data Lake Storage Gen1 へのこのデータの取り込みには各種ツールを使用できます。 これらのツールでは通常、イベントごとにリアルタイムでデータを取り込み、処理します。その後、さらにデータを処理できるように、Data Lake Storage Gen1 にイベントを一括で書き込みます。

使用できるツールは次のとおりです。

* [Azure Stream Analytics](../stream-analytics/stream-analytics-define-outputs.md)。Event Hubs に取り込まれたイベントは、Azure Data Lake Storage Gen1 出力を使用して Azure Data Lake Storage Gen1 に書き込むことができます。
* [Azure HDInsight Storm](../hdinsight/storm/apache-storm-write-data-lake-store.md)。Storm クラスターから Data Lake Storage Gen1 に直接データを書き込むことができます。
* [EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)。Event Hubs からイベントを受け取り、[Data Lake Storage Gen1 .NET SDK](data-lake-store-get-started-net-sdk.md) を使用して Data Lake Storage Gen1 に書き込むことができます。

### <a name="relational-data"></a>リレーショナル データ
リレーショナル データベースのデータもソースとして扱うことができます。 一定の期間を経て、リレーショナル データベースには大量のデータが収集されます。ビッグ データのパイプラインを介して処理すると、これらのデータから重要な知見が得られます。 このようなデータを Data Lake Storage Gen1 に移動する場合は、次のツールを使用できます。

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Azure Data Factory](../data-factory/copy-activity-overview.md)

### <a name="web-server-log-data-upload-using-custom-applications"></a>Web サーバー ログ データ (カスタム アプリケーションを使用したアップロード)
Web サーバー ログ データの分析は、ビッグ データ アプリケーションの一般的な使用例で、大量のログ ファイルを Data Lake Storage Gen1 にアップロードすることを必要とするため、この種類のデータセットが特に呼び出されます。 このようなデータをアップロードするための独自のスクリプトまたはアプリケーションを記述するには、次のツールのいずれかを使用できます。

* [Azure CLI](data-lake-store-get-started-cli-2.0.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [Azure Data Lake Storage Gen1 .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Azure Data Factory](../data-factory/copy-activity-overview.md)

Web サーバー ログ データをアップロードする場合、または他の種類のデータ (ソーシャル センチメント データなど) をアップロードする場合には、独自のカスタム スクリプトやカスタム アプリケーションを記述することをお勧めします。 これにより、データをアップロードするコンポーネントをより大規模なビッグ データ アプリケーションの一部として含める柔軟性が得られるためです。 このコードは、スクリプトまたは単純なコマンド ライン ユーティリティの形をとる場合もあれば、ビッグ データの処理をビジネス アプリケーションまたはビジネス ソリューションに統合するために使用される場合もあります。

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Azure HDInsight クラスターに関連付けられたデータ
ほとんどの種類の HDInsight クラスター (Hadoop、HBase、Storm) では、データ ストレージ リポジトリとして Data Lake Storage Gen1 がサポートされています。 HDInsight クラスターは Azure Storage Blob (WASB) のデータにアクセスします。 パフォーマンスを向上させるために、クラスターに関連付けられた Data Lake Storage Gen1 アカウントに WASB のデータをコピーできます。 データのコピーには次のツールを使用できます。

* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)
* [AdlCopy サービス](data-lake-store-copy-data-azure-storage-blob.md)
* [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>オンプレミスまたは IaaS Hadoop クラスターに格納されているデータ
大量のデータが既存の Hadoop クラスターの HDFS を使用するコンピューターにローカルに格納されている場合があります。 Hadoop クラスターは、オンプレミスのデプロイ内にある場合も、Azure の IaaS クラスター内にある場合もあります。 このようなデータを 1 回限りまたは定期的に Azure Data Lake Storage Gen1 にコピーする要件が存在します。 これを実現するために、さまざまな方法を使用できます。 以下の表に、それぞれの方法とそのトレードオフを示します。

| アプローチ | 詳細 | 長所 | 考慮事項 |
| --- | --- | --- | --- |
| Azure Data Factory (ADF) を使用して Hadoop クラスターから Azure Data Lake Storage Gen1 にデータを直接コピーする。 |[ADF ではデータ ソースとして HDFS をサポートしている。](../data-factory/connector-hdfs.md) |ADF では、HDFS が最初からサポートされており、ファースト クラスのエンド ツー エンドの管理と監視が提供される。 |Data Management Gateway をオンプレミスまたは IaaS クラスターにデプロイする必要がある。 |
| Hadoop からデータをファイルとしてエクスポートする。 次に、適切なメカニズムを使用してファイルを Azure Data Lake Storage Gen1 にコピーする。 |次のいずれかを使用してファイルを Azure Data Lake Storage Gen1 にコピーできます。 <ul><li>[Azure PowerShell (Windows OS)](data-lake-store-get-started-powershell.md)</li><li>[Azure CLI](data-lake-store-get-started-cli-2.0.md)</li><li>Data Lake Storage Gen1 SDK を使用するカスタム アプリ</li></ul> |手軽に開始できる。 カスタマイズしたアップロードを行うことができる。 |複数のテクノロジを含む複数ステップのプロセス。 カスタマイズされたツールという性質上、時間の経過と共に管理と監視が困難になる。 |
| Distcp を使用して、Hadoop から Azure Storage にデータをコピーする。 次に、適切なメカニズムを使用してデータを Azure Storage から Data Lake Storage Gen1 にコピーする。 |次のいずれかを使用してデータを Azure Storage から Data Lake Storage Gen1 にコピーできます。 <ul><li>[Azure Data Factory](../data-factory/copy-activity-overview.md)</li><li>[AdlCopy ツール](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[HDInsight クラスター上で実行されている Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)</li></ul> |オープン ソースのツールを使用できる。 |複数のテクノロジを含む複数ステップのプロセス。 |

### <a name="really-large-datasets"></a>非常に大規模なデータセット
数 TB に及ぶデータセットをアップロードする場合、上記の方法では速度が遅く、コストがかかることがあります。 このような場合は、次のオプションを使用できます。

* **Azure ExpressRoute の使用**。 Azure ExpressRoute を使用すると、Azure データ センターとお客様のオンプレミスのインフラストラクチャとの間でプライベート接続を作成できます。 これにより、大量のデータを転送するための信頼性の高いオプションが提供されます。 詳細については、 [Azure ExpressRoute のドキュメント](../expressroute/expressroute-introduction.md)をご覧ください。
* **データの "オフライン" アップロード**。 何らかの理由で Azure ExpressRoute が使用できない場合は、 [Azure Import/Export サービス](../import-export/storage-import-export-service.md) を利用して、データが格納されたハード ディスク ドライブを Azure データ センターに発送してください。 データはまず Azure Storage BLOB にアップロードされます。 その後、[Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) または [AdlCopy ツール](data-lake-store-copy-data-azure-storage-blob.md)を使って、Azure Storage Blob から Data Lake Storage Gen1 にデータをコピーできます。

  > [!NOTE]
  > Import/Export サービスを利用する場合、Azure データ センターに送るディスク上のファイル サイズは 195 GB 以下である必要があります。
  >
  >

## <a name="process-data-stored-in-data-lake-storage-gen1"></a>Data Lake Storage Gen1 に格納されているデータを処理する
Data Lake Storage Gen1 でデータが利用できるようになったら、サポートされているビッグ データ アプリケーションを使用して、そのデータの分析を実行できます。 現在、Data Lake Storage Gen1 に格納されたデータに対してデータ分析ジョブを実行する場合は、Azure HDInsight と Azure Data Lake Analytics を使用できます。

![Data Lake Storage Gen1 のデータを分析する](./media/data-lake-store-data-scenarios/analyze-data.png "Data Lake Storage Gen1 でのデータの分析")

次の例を参考にしてください。

* [Data Lake Storage Gen1 をストレージとして使用して HDInsight クラスターを作成する](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Data Lake Storage Gen1 で Azure Data Lake Analytics を使用する](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

## <a name="download-data-from-data-lake-storage-gen1"></a>Data Lake Storage Gen1 からデータをダウンロードする
次のようなシナリオでは、Azure Data Lake Storage Gen1 からデータをダウンロードしたり、移動したりすることもできます。

* 既存のデータ処理パイプラインとのインターフェイスとなる他のリポジトリにデータを移動する。 たとえば、Data Lake Storage Gen1 から Azure SQL Database または SQL Server にデータを移動できます。
* アプリケーション プロトタイプの作成中に IDE 環境で処理するために、ローカル コンピューターにデータをダウンロードする。

![Data Lake Storage Gen1 からデータを送信する](./media/data-lake-store-data-scenarios/egress-data.png "Data Lake Storage Gen1 からデータを送信する")

このような場合、次のオプションのいずれかを使用できます。

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Azure Data Factory](../data-factory/copy-activity-overview.md)
* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)

次の方法を使用して、Data Lake Storage Gen1 からデータをダウンロードするための独自のスクリプトやアプリケーションを記述することもできます。

* [Azure CLI](data-lake-store-get-started-cli-2.0.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [Azure Data Lake Storage Gen1 .NET SDK](data-lake-store-get-started-net-sdk.md)

## <a name="visualize-data-in-data-lake-storage-gen1"></a>Data Lake Storage Gen1 のデータを視覚化する
複数のサービスを組み合わせて使用することで、Data Lake Storage Gen1 に格納されたデータを視覚的に表現することができます。

![Data Lake Storage Gen1 のデータを視覚化する](./media/data-lake-store-data-scenarios/visualize-data.png "Data Lake Storage Gen1 のデータを視覚化する")

* まず、[Azure Data Factory を使用して、Data Lake Storage Gen1 から Azure Synapse Analytics にデータを移動](../data-factory/copy-activity-overview.md)することができます。
* その後、[Power BI を Azure Synapse Analytics と統合](/power-bi/connect-data/service-azure-sql-data-warehouse-with-direct-connect)して、データを視覚的に表現できます。