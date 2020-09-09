---
title: Azure Stream Analytics の概要
description: モノのインターネット (IoT) からのストリーミング データをリアルタイムで分析するのに役立つ管理されたサービスである Stream Analytics について説明します。
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 07/6/2020
ms.openlocfilehash: d62fd0a23a5f5553f27c7a399eb17d06d427a6f3
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2020
ms.locfileid: "86108789"
---
# <a name="what-is-azure-stream-analytics"></a>Azure Stream Analytics とは

Azure Stream Analytics は、複数のソースからの大量の高速ストリーミング データを同時に分析および処理するように設計された、リアルタイムの分析および複合イベント処理エンジンです。 パターンやリレーションシップは、デバイス、センサー、クリックストリーム、ソーシャル メディア フィード、アプリケーションなどのいくつかの入力ソースから抽出された情報内で識別できます。 これらのパターンを使用してアクションを起動し、アラートの作成、レポート作成ツールへの情報のフィード、または後で使用するための変換されたデータの保存などのワークフローを開始できます。 また、Stream Analytics は Azure IoT Edge ランタイムでも使用できます。これにより、IoT デバイス上のデータを処理できるようになります。 

次のシナリオは、Azure Stream Analytics を使用できる場合の例です。

* IoT デバイスからのリアルタイムのテレメトリ ストリームの分析
* Web ログ/クリックストリーム分析
* フリート マネジメントや無人車両用の地理空間分析
* 高価な資産のリモート監視と予測メンテナンス
* 販売時点管理データのリアルタイム分析による在庫管理や異常検出

## <a name="how-does-stream-analytics-work"></a>Stream Analytics の動作

Azure Stream Analytics ジョブは、入力、クエリ、および出力で構成されます。 Stream Analytics は、Azure Event Hubs (Apache Kafka からの Azure Event Hubs を含む)、Azure IoT Hub、または Azure Blob Storage からデータを取り込みます。 SQL クエリ言語に基づくクエリを使用して、ストリーミング データの一定期間にわたるフィルター処理、並べ替え、集計、および結合を容易に行うことができます。 この SQL 言語は、JavaScript および C# のユーザー定義関数 (UDF) で拡張することもできます。 単純な言語コンストラクトや構成を使用して集計操作を実行するときに、イベントの順序付けのオプションや時間枠の期間を簡単に調整できます。

各ジョブには変換されたデータの 1 つまたは複数の出力が含まれるため、分析した情報に応じてどのような処理を実行するかを制御できます。 たとえば、次のように操作できます。

* Azure Functions、Service Bus Topics、Queues などのサービスにデータを送信して、通信またはダウンストリームのカスタム ワークフローをトリガーする。
* リアルタイムのダッシュボード作成のためにデータを Power BI ダッシュボードに送信する。
* 履歴データに基づいて機械学習モデルをトレーニングするか、またはバッチ分析を実行するために、データを他の Azure ストレージ サービス (例: Azure Data Lake、Azure Synapse Analytics など) に格納する。

次の図は、データがどのように Stream Analytics に送信され、分析され、さらに格納やプレゼンテーションなどの他のアクションのために送信されるかを示しています。

![Stream Analytics 導入のパイプライン](./media/stream-analytics-introduction/stream-analytics-e2e-pipeline.png)

## <a name="key-capabilities-and-benefits"></a>主要な機能と利点

Azure Stream Analytics は、あらゆるジョブの規模に対応するスケーラビリティ、使いやすさ、柔軟性、信頼性を実現する設計になっています。 複数の Azure リージョンにまたがって使用できます。 次の図は、Azure Stream Analytics の主な機能を示しています。

![Stream Analytics の主な機能](./media/stream-analytics-introduction/stream-analytics-key-capabilities.png)

## <a name="ease-of-getting-started"></a>簡単に始められる

Azure Stream Analytics は、簡単に始めることができます。 何回かクリックして複数のソースやシンクに接続するだけで、エンド ツー エンドのパイプラインが作成されます。 Stream Analytics は、ストリーミング データ インジェストのためには [Azure Event Hubs](/azure/event-hubs/) や [Azure IoT Hub](/azure/iot-hub/) に、履歴データの取り込みのためには [Azure Blob Storage](/azure/storage/common/storage-introduction) に接続できます。 また、ストリーミング データに結合して参照操作を実行するために使用できる Azure Blob Storage または [SQL Database](stream-analytics-use-reference-data.md#azure-sql-database) からの静的な、またはゆっくり変化する参照データをジョブ入力に含めることもできます。

Stream Analytics は、[Azure Blob Storage](/azure/storage/common/storage-introduction)、[Azure SQL Database](/azure/sql-database/)、[Azure Data Lake Store](/azure/data-lake-store/)、[Azure CosmosDB](/azure/cosmos-db/introduction) などのさまざまなストレージ システムにジョブ出力をルーティングできます。 Azure HDInsight を使用して格納済みの出力に対してバッチ分析を実行したり、消費のための Event Hubs やリアルタイムの視覚化のための [Power BI](https://docs.microsoft.com/power-bi/) などの別のサービスに出力を送信したりできます。

Stream Analytics の出力の完全な一覧については、「[Azure Stream Analytics からの出力を理解する](stream-analytics-define-outputs.md)」を参照してください。

## <a name="programmer-productivity"></a>プログラマの生産性

Azure Stream Analytics では、移動中のデータを分析するための強力な時間的制約によって拡張された単純な SQL ベースのクエリ言語を使用します。 ジョブの変換を定義するには、単純な SQL コンストラクトで複雑なテンポラル クエリおよび分析を作成することができる、シンプルな宣言型の [Stream Analytics クエリ言語](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)を使用します。 Stream Analytics クエリ言語は SQL 言語と整合性があるため、SQL に精通していれば十分にジョブの作成を開始できます。 ジョブはまた、Azure PowerShell、[Stream Analytics Visual Studio ツール](stream-analytics-tools-for-visual-studio-install.md)、the [Stream Analytics Visual Studio Code 拡張機能](quick-create-vs-code.md)、Azure Resource Manager テンプレートなどの開発者ツールを使用して作成することもできます。 開発者ツールを使用することで、変換クエリをオフラインで開発したり、[CI/CD パイプライン](stream-analytics-tools-for-visual-studio-cicd.md)を使用して Azure にジョブを送ったりすることができます。

Stream Analytics クエリ言語には、ストリーミング データを分析および処理するための幅広い関数が用意されています。 このクエリ言語では、シンプルなデータ操作、集計および分析関数、[地理空間機能](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-geospatial-functions)、[パターン マッチング](https://docs.microsoft.com/stream-analytics-query/match-recognize-stream-analytics)、[異常検出](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-machine-learning-anomaly-detection)がサポートされています。 ポータルでクエリを編集し、ライブ ストリームから抽出されたサンプル データを使用してそれをテストできます。

クエリ言語の機能は、別の関数を定義したり呼び出したりすることで拡張することができます。 Azure Machine Learning ソリューションを利用するために Azure 機械学習で関数呼び出しを定義したり、Stream Analytics クエリの一部として複雑な計算を実行するために JavaScript または C# のユーザー定義関数 (UDF) またはユーザー定義集計を統合したりすることができます。

## <a name="fully-managed"></a>フル マネージド

Azure Stream Analytics は、Azure のフル マネージド サーバーレス (PaaS) プランです。 ハードウェアをプロビジョニングしたり、ジョブを実行するためにクラスターを管理したり、OS やソフトウェアを更新したりする必要はありせん。 Azure Stream Analytics によってジョブが完全に管理されるため、お客様はインフラストラクチャではなく、ビジネス ロジックに集中できます。

## <a name="run-in-the-cloud-or-on-the-intelligent-edge"></a>クラウドまたはインテリジェント エッジ上で実行

Azure Stream Analytics は、大規模な分析のためにクラウドで実行するか、または待機時間が極端に短い分析のために IoT Edge で実行できます。 Azure Stream Analytics によってクラウドとエッジの両方で同じツールとクエリ言語が使用されるため、開発者は、ストリーム処理のための真にハイブリッドなアーキテクチャを構築できます。 

## <a name="low-total-cost-of-ownership"></a>低い総保有コスト

クラウド サービスである Stream Analytics は、コストに最適化されています。 関連する初期費用はありません。[消費するストリーミング ユニット](stream-analytics-streaming-unit-consumption.md)に対して支払うだけです。 コミットメントやクラスターのプロビジョニングは必要ないため、ビジネス ニーズに基づいて、ジョブをスケールアップまたはスケールダウンできます。

## <a name="mission-critical-ready"></a>ミッション クリティカル対応

Azure Stream Analytics は世界中の複数のリージョンで使用でき、信頼性、セキュリティ、コンプライアンスの要件をサポートすることで、ミッション クリティカルなワークロードを実行するように設計されています。

### <a name="reliability"></a>[信頼性]

厳密には、Azure Stream Analytics で保証されるイベントの処理は 1 回、イベントの配信は 1 回以上となります。そのため、イベントが失われることはありません。 [イベント配信の保証](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics)に関するページで説明されているように、選択した出力が提供される厳密に 1 回だけの処理が保証されます。

Azure Stream Analytics には、イベントの配信に失敗した場合のために、組み込みの回復機能が備わっています。 Stream Analytics にはまた、ジョブの状態を保持するための組み込みのチェックポイントも用意されており、反復可能な結果が提供されます。

Stream Analytics は、マネージド サービスとして、分レベルの細分性で 99.9% の可用性のイベント処理が保証されます。 詳細については、「[Stream Analytics の SLA](https://azure.microsoft.com/support/legal/sla/stream-analytics/v1_0/)」のページを参照してください。 

### <a name="security"></a>セキュリティ

セキュリティに関しては、Azure Stream Analytics では、すべての着信および発信の通信が暗号化され、TLS 1.2 がサポートされています。 組み込みのチェックポイントも暗号化されます。 Stream Analytics では、すべての処理はメモリ内で実行されるため、受信データが格納されることはありません。

### <a name="compliance"></a>コンプライアンス

「[Overview of Microsoft Azure compliance (Microsoft Azure のコンプライアンスの概要)](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)」で説明されているように、Azure Stream Analytics は複数のコンプライアンス認定に従っています。 

## <a name="performance"></a>パフォーマンス

Stream Analytics は、1 秒間に何百万というイベントを処理できるため、きわめて短い待ち時間で結果を配信することができます。 大規模でリアルタイムの複雑なイベント処理アプリケーションに対応するために、スケールアップまたはスケールアウトを行うことができます。 Stream Analytics は、パーティション分割によってより高いパフォーマンスをサポートしているため、複雑なクエリを並列化し、複数のストリーミング ノードで実行できます。 Azure Stream Analytics の基盤となっている [Trill](https://github.com/Microsoft/Trill) は、Microsoft Research と共同で開発された、高パフォーマンスのメモリ内ストリーミング分析エンジンです。

## <a name="next-steps"></a>次のステップ

Azure Stream Analytics の概要は以上です。 次はさらに踏み込んで、初めての Stream Analytics ジョブを作成してみましょう。

* [Azure Portal を使用して Stream Analytics ジョブを作成する](stream-analytics-quick-create-portal.md)。
* [Azure PowerShell を使用して Stream Analytics ジョブを作成する](stream-analytics-quick-create-powershell.md)。
* [Visual Studio を使用して Stream Analytics ジョブを作成する](stream-analytics-quick-create-vs.md)
* [Visual Studio Code を使用して Stream Analytics ジョブを作成する](quick-create-vs-code.md)
