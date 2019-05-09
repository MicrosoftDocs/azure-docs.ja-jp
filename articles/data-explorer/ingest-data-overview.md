---
title: Azure データ エクスプローラーでのデータ インジェスト
description: Azure データ エクスプローラーでデータを取り込む (読み込む) さまざまな方法について説明します
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/18/2019
ms.openlocfilehash: 6c9cb7282ba9d99ea643bbd023d876b8fbe3904c
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/05/2019
ms.locfileid: "59045596"
---
# <a name="azure-data-explorer-data-ingestion"></a>Azure データ エクスプローラーでのデータ インジェスト

データ インジェストとは、Azure データ エクスプローラーで 1 つまたは複数のソースからデータ レコードを読み込んでテーブルを作成または更新するプロセスのことです。 取り込まれたデータは、クエリに使用できるようになります。 次の図は、データ インジェストを含む、Azure Data Explorer での作業のエンド ツー エンドのフローを示したものです。

![Data flow](media/ingest-data-overview/data-flow.png)

データ インジェストを担当する Azure データ エクスプローラーのデータ管理サービスでは、次の機能が提供されます。

1. **データのプル**: 外部ソース (Event Hubs) からデータをプルするか、または Azure キューからインジェスト要求を読み取ります。

1. **バッチ処理**: 同じデータベースおよびテーブルに送られるデータをバッチ化して、インジェストのスループットを最適化します。

1. **検証**:必要に応じて、事前検証と形式変換を行います。

1. **データ操作**: スキーマの照合と、データの整理、インデックス付け、エンコード、圧縮を行います。

1. **インジェスト フローでの永続性ポイント**: エンジンでのインジェストの負荷を管理し、一時的な障害時の再試行を処理します。

1. **データ取り込みのコミット**: データをクエリで使用できるようにします。

## <a name="ingestion-methods"></a>インジェストの方法

Azure データ エクスプローラーでは複数のインジェスト方法がサポートされており、それぞれに固有のターゲット シナリオ、利点、欠点があります。 Azure Data Explorer では、一般的なサービスに対するパイプラインとコネクタ、SDK を使用するプログラムでのインジェスト、および探索のためのエンジンへの直接アクセスが提供されています。

### <a name="ingestion-using-pipelines-connectors-and-plugins"></a>パイプライン、コネクタ、およびプラグインを使用したインジェスト

現在、Azure Data Explorer は以下をサポートしています。

* Event Grid パイプライン。Azure portal 内の管理ウィザードを使用して管理できます。 詳細については、[Azure Data Explorer への Azure BLOB の取り込み](ingest-data-event-grid.md)に関するページを参照してください。

* イベント ハブ パイプライン。Azure portal 内の管理ウィザードを使用して管理できます。 詳細については、[イベント ハブから Azure Data Explorer へのデータの取り込み](ingest-data-event-hub.md)に関するページを参照してください。

* Logstash プラグイン。[Logstash から Azure Data Explorer へのデータの取り込み](ingest-data-logstash.md)に関するページを参照してください。

* Kafka コネクタ。[Kafka から Azure Data Explorer へのデータの取り込み](ingest-data-kafka.md)に関するページを参照してください。

### <a name="ingestion-using-integration-services"></a>統合サービスを使用した取り込み

* Azure Data Factory (ADF) は、Azure の分析ワークロード用のフル マネージド データ統合サービスで、[サポートされているデータ ストアと形式](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats)を使用して Azure Data Explorer との間でデータのコピーを行います。 詳細については、「[Azure Data Factory を使用して Azure Data Explorer をコピー先またはコピー元としてデータをコピーする](/azure/data-factory/connector-azure-data-explorer)」を参照してください。

### <a name="programmatic-ingestion"></a>プログラムによるインジェスト

Azure データ エクスプローラーで提供されている SDK を使用して、クエリとデータ インジェストを行うことができます。 プログラムによるインジェストは、インジェスト プロセスの最中および後のストレージ トランザクションを最小限に抑えることによって、インジェスト コスト (COG) を削減するように最適化されています。

**使用可能な SDK とオープン ソース プロジェクト**:

Kusto では、データの取り込みとクエリに使用できるクライアント SDK が提供されています。

* [Python SDK](/azure/kusto/api/python/kusto-python-client-library)

* [.NET SDK](/azure/kusto/api/netfx/about-the-sdk)

* [Java SDK](/azure/kusto/api/java/kusto-java-client-library)

* [Node SDK](/azure/kusto/api/node/kusto-node-client-library)

* [REST API](/azure/kusto/api/netfx/kusto-ingest-client-rest)

**プログラムによるインジェストの手法**:

* Azure Data Explorer のデータ管理サービスを介したデータの取り込み (高スループットで信頼性の高いインジェスト):

    [**バッチ インジェスト**](/azure/kusto/api/netfx/kusto-ingest-queued-ingest-sample) (SDK によって提供): クライアントは Azure Blob Storage にデータをアップロードし (Azure データ エクスプローラーのデータ管理サービスで指定)、通知を Azure キューに投稿します。 バッチ インジェストは、大量で信頼性が高く低コストのデータ インジェストに推奨される手法です。

* Azure データ エクスプローラー エンジンへのデータの直接取り込み (探索とプロトタイプ作成に最適)。

  * **インライン インジェスト**: 帯域内データを含む制御コマンド (.ingest inline) は、アドホックなテストを対象としています。

  * **クエリからの取り込み**: クエリ結果を指す制御コマンド (.set、.set-or-append、.set-or-replace) は、レポートまたは小さい一時テーブルを生成するために使用されます。

  * **ストレージからの取り込み**: 外部に格納されたデータ (例: Azure Blob Storage) を使用する制御コマンド (.ingest into) では、データを効率的に一括インジェストできます。

**さまざまな方法の待機時間**:

| 方法 | Latency |
| --- | --- |
| **インライン インジェスト** | 即時 |
| **クエリからの取り込み** | クエリ時間 + 処理時間 |
| **ストレージからの取り込み** | ダウンロード時間 + 処理時間 |
| **キューによるインジェスト** | バッチ処理時間 + 処理時間 |
| |

処理時間はデータ サイズに依存し、数秒未満です。 バッチ処理時間の既定値は 5 分です。

## <a name="choosing-the-most-appropriate-ingestion-method"></a>最も適切なインジェスト方法の選択

データの取り込みを始める前に、次の点を確認する必要があります。

* データはどこに存在するか? 
* データはどのような形式で、それは変更できるか? 
* クエリする必要があるフィールドは何か? 
* 予想されるデータ量と増加速度は? 
* 予想されるイベントの種類の数 (テーブルの数に反映される)? 
* イベントのスキーマはどれくらいの頻度で変更されるか? 
* データを生成するノードの数? 
* ソース OS は何か? 
* 待機時間の要件? 
* 既存のマネージド インジェスト パイプラインの 1 つを使用できるか? 

Event Hub などのメッセージング サービスに基づく既存のインフラストラクチャがある組織では、おそらく、コネクタを使用するのが最適なソリューションです。 キューによるインジェストは、大量のデータに適しています。

## <a name="supported-data-formats"></a>サポートされるデータ形式

クエリからの取り込み以外のすべてのインジェスト方法では、Azure Data Explorer が解析できるようにデータを書式設定します。 以下のデータ形式がサポートされます。

* CSV、TSV、PSV、SCSV、SOH
* JSON (行区切り、複数行)、Avro
* ZIP、GZIP 

> [!NOTE]
> データが取り込まれるときに、対象のテーブル列に基づいてデータ型が推論されます。 レコードが不完全な場合、またはフィールドを必要なデータ型として解析できない場合は、対応するテーブル列に null 値が設定されます。

## <a name="ingestion-recommendations-and-limitations"></a>インジェストの推奨事項と制限事項

* 取り込まれたデータの効果的なアイテム保持ポリシーは、データベースのアイテム保持ポリシーから派生します。 詳しくは、「[Retention policy](/azure/kusto/concepts/retentionpolicy)」(アイテム保持ポリシー) をご覧ください。 データを取り込むには、**テーブル取り込み者**または**データベース取り込み者**のアクセス許可が必要です。
* インジェストでは、5 GB の最大ファイル サイズがサポートされます。 100 MB から 1 GB の間のファイルを取り込むことをお勧めします。

## <a name="schema-mapping"></a>スキーマ マッピング

スキーマ マッピングは、ソースのデータ フィールドをターゲットのテーブル列にバインドするのに役立ちます。

* [CSV マッピング](/azure/kusto/management/mappings?branch=master#csv-mapping) (省略可能) は、すべての序数ベースの形式で動作します。 取り込みコマンドのパラメーターを使用して実行すること、または[テーブルで事前に作成](/azure/kusto/management/tables?branch=master#create-ingestion-mapping)して取り込みコマンドのパラメーターから参照することができます。
* [JSON マッピング](/azure/kusto/management/mappings?branch=master#json-mapping) (必須) と [Avro マッピング](/azure/kusto/management/mappings?branch=master#avro-mapping) (必須) は、取り込みコマンドのパラメーターを使用して実行できます。 また、[テーブルで事前に作成](/azure/kusto/management/tables#create-ingestion-mapping)して取り込みコマンドのパラメーターから参照することもできます。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [イベント ハブから Azure Data Explorer にデータを取り込む](ingest-data-event-hub.md)

> [!div class="nextstepaction"]
> [Event Grid サブスクリプションを使用して Azure Data Explorer にデータを取り込む](ingest-data-event-grid.md)

> [!div class="nextstepaction"]
> [Kafka から Azure Data Explorer にデータを取り込む](ingest-data-kafka.md)

> [!div class="nextstepaction"]
> [Azure Data Explorer の Python ライブラリを使用してデータを取り込む](python-ingest-data.md)

> [!div class="nextstepaction"]
> [Azure Data Explorer の Node ライブラリを使用してデータを取り込む](node-ingest-data.md)

> [!div class="nextstepaction"]
> [Azure Data Explorer .NET Standard SDK (プレビュー) を使用してデータを取り込む](net-standard-ingest-data.md)

> [!div class="nextstepaction"]
> [Logstash から Azure Data Explorer にデータを取り込む](ingest-data-logstash.md)
