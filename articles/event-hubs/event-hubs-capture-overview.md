---
title: ストリーミング イベントをキャプチャする - Azure Event Hubs | Microsoft Docs
description: この記事では、Azure Event Hubs でイベントのストリーミングをキャプチャするキャプチャ機能の概要を示します。
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: e53cdeea-8a6a-474e-9f96-59d43c0e8562
ms.service: event-hubs
ms.workload: na
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 4ba3109460616be98b5330ec7175f161a6a3b750
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326177"
---
# <a name="capture-events-through-azure-event-hubs-in-azure-blob-storage-or-azure-data-lake-storage"></a>Azure Event Hubs で Azure Blob Storage または Azure Data Lake Storage にイベントをキャプチャする
Azure Event Hubs を利用すると、Event Hubs のストリーミング データをご自分で選択した [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) または [Azure Data Lake Storage](https://azure.microsoft.com/services/data-lake-store/) アカウントに自動的に配信できます。その際、時間やサイズの間隔を柔軟に指定できます。 Capture の設定は手軽で、実行に伴う管理コストは生じません。また、Event Hubs の[スループット単位](event-hubs-scalability.md#throughput-units)に応じて自動的にスケールします。 Event Hubs Capture はストリーミング データを Azure に読み込む最も簡単な方法であり、これを利用すれば、データのキャプチャではなくデータの処理に注力できるようになります。

Event Hubs Capture を利用すると、リアルタイムおよびバッチベースのパイプラインを同じストリームで処理できます。 すなわち、変化するニーズに合わせて拡大可能なソリューションを構築できます。 将来のリアルタイム処理を視野に入れてバッチベースのシステムを構築している場合も、既存のリアルタイム ソリューションに効率的なコールド パスを追加したいと考えている場合も、Event Hubs Capture ならストリーミング データの操作が容易です。

## <a name="how-event-hubs-capture-works"></a>Event Hubs Capture の仕組み

Event Hubs は分散ログに似た、テレメトリの受信のための持続的バッファーです。 Event Hubs でのスケーリングの鍵となるのは、[パーティション分割されたコンシューマー モデル](event-hubs-scalability.md#partitions)です。 各パーティションは独立したデータのセグメントであり、個別に使用されます。 このデータは、構成可能なリテンション期間に基づいて、所定のタイミングで破棄されます。 そのため、特定のイベント ハブが "いっぱい" になることはありません。

Event Hubs Capture を使用すると、キャプチャされたデータを格納するための独自の Azure Blob Storage アカウントとコンテナー、または Azure Data Lake Store アカウントを指定することができます。 これらのアカウントのリージョンは、イベント ハブと同じであっても、別のリージョンであってもかまわないため、Event Hubs Capture 機能の柔軟性がさらに高まります。

キャプチャされたデータは [Apache Avro][Apache Avro] 形式で書き込まれます。これはコンパクトで高速なバイナリ形式で、インライン スキーマを備えた便利なデータ構造になっています。 この形式は Hadoop エコシステム、Stream Analytics、Azure Data Factory で幅広く使用されています。 Avro の操作の詳細は、この記事の後半に記載してあります。

### <a name="capture-windowing"></a>キャプチャのウィンドウ化

Event Hubs Capture では、キャプチャを制御するウィンドウを設定できます。 このウィンドウは "先に来たものが優先されるポリシー" が適用される最小サイズと時間の構成です。つまり、先に生じたトリガーによってキャプチャ操作が行われます。 15 分/100 MB のキャプチャ ウィンドウを設定してあるときに 1 MB/秒で送信する場合は、サイズのウィンドウが時間のウィンドウよりも先にトリガーとなります。 各パーティションのキャプチャは個別に行われ、完了したブロック BLOB がキャプチャ時に (キャプチャが実行されるタイミングとなったときに) 書き込まれます。 ストレージの名前付け規則は次のとおりです。

```
{Namespace}/{EventHub}/{PartitionId}/{Year}/{Month}/{Day}/{Hour}/{Minute}/{Second}
```

日付の値がゼロ パディングされていることに注意してください。ファイル名の例は次のようになります。

```
https://mystorageaccount.blob.core.windows.net/mycontainer/mynamespace/myeventhub/0/2017/12/08/03/03/17.avro
```

Azure Storage Blob が一時的に利用できなくなった場合、Event Hubs Capture では、イベント ハブで構成されたデータ保持期間の間、データが保持され、ストレージ アカウントが再び利用可能になったら、データがバックフィルされます。

### <a name="scaling-to-throughput-units"></a>スループット単位へのスケーリング

Event Hubs のトラフィックは [スループット単位](event-hubs-scalability.md#throughput-units)で制御されます。 受信の場合、1 スループット単位は最大 1 MB/秒または 1,000 イベント/秒となり、送信の場合はその倍となります。 Standard Event Hubs は、1 から 20 のスループット ユニットで構成できます。クォータの引き上げの[サポート リクエスト][support request] によってさらに購入することもできます。 購入済みのスループット単位を超えた使用分については、調整されます。 Event Hubs Capture は内部 Event Hubs ストレージからデータを直接コピーするため、スループット単位のエグレス クォータを回避でき、他の処理リーダー (Stream Analytics や Spark など) のためにエグレスを節約できます。

構成されると、Event Hubs Capture は最初のイベント送信時に自動的に実行され、そのまま実行を継続します。 ダウンストリーム処理で処理が行われていることを把握しやすいように、Event Hubs はデータがないときは空のファイルを書き込みます。 このプロセスにより、バッチ プロセッサに提供可能な、予測しやすいパターンとマーカーが得られます。

## <a name="setting-up-event-hubs-capture"></a>Event Hubs Capture の設定

[Azure Portal](https://portal.azure.com) または Azure Resource Manager テンプレートを使用して、イベント ハブの作成時に Capture を構成できます。 詳細については、次の記事を参照してください。

- [Azure Portal を使用して Event Hubs Capture を有効にする](event-hubs-capture-enable-through-portal.md)
- [Azure Resource Manager テンプレートを使用して、イベント ハブを含んだ Event Hubs 名前空間を作成して Capture を有効にする](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)


## <a name="exploring-the-captured-files-and-working-with-avro"></a>キャプチャしたファイルの確認と Avro の操作

Event Hubs Capture では、構成された時間枠で指定された Avro 形式のファイルが作成されます。 これらのファイルは、 [Azure ストレージ エクスプローラー][Azure Storage Explorer]などの任意のツールを使用して確認できます。 また、ローカルにダウンロードして操作することができます。

Event Hubs Capture によって生成されたファイルには、次の Avro スキーマがあります。

![Avro スキーマ][3]

Avro ファイルは、Apache の [Avro ツール][Avro Tools] jar を使用することで簡単に調べることができます。また、軽量 SQL 主導のエクスペリエンス向けの jar from Apache. You can also use [Apache Drill][Apache Drill] または [Apache Spark][Apache Spark] を使用して、取り込まれたデータに対する複雑な分散処理を実行することもできます。 

### <a name="use-apache-drill"></a>Apache Drill を使用する

[Apache Drill][Apache Drill] は、任意の場所にある構造化および半構造化データのクエリを実行できる、"ビッグ データ探索のためのオープンソースの SQL クエリ エンジン" です。 このエンジンは、スタンドアロン ノードとして、またはパフォーマンスを向上させるために巨大なクラスターとして実行できます。

Azure Blob Storage へのネイティブ サポートを使用できるため、Avro ファイル内のデータにクエリを実行することが容易になります。次のドキュメントを参照してください。

[Apache Drill: Azure Blob Storage プラグイン][Apache Drill: Azure Blob Storage Plugin]

キャプチャされたファイルに簡単にクエリするには、コンテナーを使用して Apache Drill を有効にした状態で VM を作成および実行して Azure Blob Storage にアクセスできます。

https://github.com/yorek/apache-drill-azure-blob

完全なエンド ツー エンドのサンプルを大規模なストリーミング リポジトリで入手できます。

[大規模なストリーミング: Event Hubs Capture]

### <a name="use-apache-spark"></a>Apache Spark を使用する

[Apache Spark][Apache Spark] は、"大規模なデータ処理のための統合された分析エンジン" です。 さまざまな言語 (SQL を含む) がサポートされ、Azure Blob Storage に容易にアクセスできます。 Azure で Apache Spark を実行するには次の 2 つのオプションがあり、どちらも Azure Blob Storage への容易なアクセスが提供されます。

- [HDInsight: Azure Storage 内のファイルのアドレス指定][HDInsight: Address files in Azure storage]
- [Azure Databricks: Azure Blob Storage][Azure Databricks: Azure Blob Storage]

### <a name="use-avro-tools"></a>Avro ツールを使用する

[Avro ツール][Avro Tools]は、jar パッケージとして入手できます。 この jar ファイルをダウンロードしたら、次のコマンドを実行することによって、特定の Avro ファイルのスキーマを表示できます。

```shell
java -jar avro-tools-1.8.2.jar getschema <name of capture file>
```

このコマンドによって次の情報が返されます。

```json
{

    "type":"record",
    "name":"EventData",
    "namespace":"Microsoft.ServiceBus.Messaging",
    "fields":[
                 {"name":"SequenceNumber","type":"long"},
                 {"name":"Offset","type":"string"},
                 {"name":"EnqueuedTimeUtc","type":"string"},
                 {"name":"SystemProperties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Properties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Body","type":["null","bytes"]}
             ]
}
```

また、Avro ツールを使用してファイルを JSON 形式に変換し、その他の処理を実行することもできます。

より高度な処理を実行するには、お好みのプラットフォーム用の Avro をダウンロードしてインストールしてください。 この記事の執筆時点では、C、C++、C\#、Java、NodeJS、Perl、PHP、Python、Ruby 向けの実装があります。

Apache Avro には、[Java][Java] and [Python][Python] 向けの完全な入門ガイドが用意されています。 [Event Hubs Capture の概要](event-hubs-capture-python.md)に関する記事を読むこともできます。

## <a name="how-event-hubs-capture-is-charged"></a>Event Hubs Capture に対する課金方法

Event Hubs Capture の料金は、スループット単位と同様に時間単位で測定されます。 料金は、その名前空間で購入されたスループット単位の数に正比例します。 スループット単位が増減すると、Event Hubs Capture の測定もそれに応じたパフォーマンスを提供するために調整されます。 測定は連携して行われます。 料金の詳細については、「[Event Hubs の価格](https://azure.microsoft.com/pricing/details/event-hubs/)」をご覧ください。 

エグレス クォータは別途請求されるため、Capture では消費されません。 

## <a name="integration-with-event-grid"></a>Event Grid との統合 

Event Hubs 名前空間をソースとして Azure Event Grid サブスクリプションを作成できます。 以下のチュートリアルでは、イベント ハブをソースとして、また Azure Functions アプリをシンクとして使用して、Event Grid サブスクリプションを作成する方法を示します。[Event Grid と Azure Functions を使用してキャプチャされた Event Hubs データを SQL Data Warehouse に移行する](store-captured-data-data-warehouse.md)

## <a name="next-steps"></a>次の手順

Event Hubs Capture は Azure にデータを取得する最も簡単な方法です。 Azure Data Lake、Azure Data Factory、Azure HDInsight を利用することで、使い慣れたツールとプラットフォームを使用して、必要なスケールでバッチ処理やその他の分析を実行できます。

Event Hubs の詳細については、次のリンク先を参照してください:

* [イベントの送受信の概要](event-hubs-dotnet-framework-getstarted-send.md)
* [Event Hubs の概要][Event Hubs overview]

[Apache Avro]: https://avro.apache.org/
[Apache Drill]: https://drill.apache.org/
[Apache Spark]: https://spark.apache.org/
[support request]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[Azure Storage Explorer]: https://azurestorageexplorer.codeplex.com/
[3]: ./media/event-hubs-capture-overview/event-hubs-capture3.png
[Avro Tools]: https://www-us.apache.org/dist/avro/avro-1.9.0/java/avro-tools-1.9.0.jar
[Java]: https://avro.apache.org/docs/current/gettingstartedjava.html
[Python]: https://avro.apache.org/docs/current/gettingstartedpython.html
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
[HDInsight: Address files in Azure storage]:https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-blob-storage#address-files-in-azure-storage
[Azure Databricks: Azure Blob Storage]:https://docs.databricks.com/spark/latest/data-sources/azure/azure-storage.html
[Apache Drill: Azure Blob Storage Plugin]:https://drill.apache.org/docs/azure-blob-storage-plugin/
[大規模なストリーミング: Event Hubs Capture]: https://github.com/yorek/streaming-at-scale/tree/master/event-hubs-capture
