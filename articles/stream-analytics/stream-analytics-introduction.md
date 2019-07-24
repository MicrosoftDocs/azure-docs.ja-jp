---
title: Azure Stream Analytics の概要
description: モノのインターネット (IoT) からのストリーミング データをリアルタイムで分析するのに役立つ管理されたサービスである Stream Analytics について説明します。
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: overview
ms.custom: seodec18
ms.date: 12/07/2018
ms.openlocfilehash: 09f402f81700b53eb9e4a95e36545ef02850660a
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/12/2019
ms.locfileid: "59524482"
---
# <a name="what-is-azure-stream-analytics"></a>Azure Stream Analytics とは

Azure Stream Analytics は、デバイスからの大量のデータ ストリームを分析することができるイベント処理エンジンです。 受信データは、デバイス、センサー、Web サイト、ソーシャル メディア フィード、アプリケーションなどから得ることができます。 また、データ ストリームから情報を抽出し、パターンや関係を把握することもできます。 これらのパターンを使用して、アラートの作成、レポート ツールへの情報のフィード、後で使用するための情報の保存など、別のアクションをダウンストリームでトリガーすることができます。

Azure Stream Analytics を使用できるいくつかの例を次に示します。 

* デバイス テレメトリに基づくリアルタイム分析および IoT (モノのインターネット) センサー フュージョン
* Web ログ/クリックストリーム分析
* フリート マネジメントや無人車両用の地理空間分析
* 高価な資産のリモート監視と予測メンテナンス
* 販売時点管理データのリアルタイム分析による在庫管理や異常検出

## <a name="how-does-stream-analytics-work"></a>Stream Analytics の動作

Azure Stream Analytics の出発点となるのは、ストリーミング データのソースです。ソースは、Azure Event Hubs や Azure IoT Hub に取り込まれるほか、Azure Blob Storage などのデータ ストアから取り込まれます。 ストリームを分析するには、データのストリーム配信元の入力ソースを指定する Stream Analytics ジョブを作成します。 データやパターン、関係をどのように探し出すかを定義する変換クエリもこのジョブで指定します。 変換クエリでは、特定の期間にわたるストリーミング データに対して簡単にフィルター処理、並べ替え、集計、および結合を行うために、SQL クエリ言語が使用されます。 ジョブを実行するときには、イベント順序のオプションを調整したり、集計操作を実行するときの時間枠の期間を調整したりすることができます。

受信データを分析した後は、変換済みデータの出力を指定します。これにより、分析した情報に応じて、処理の内容を制御することができます。 たとえば、次のようなアクションを実行することができます。

* 監視対象キューにデータを送信し、ダウンストリームでアラートまたはカスタム ワークフローをトリガーする。
* Power BI ダッシュボードにデータを送信してリアルタイムに視覚化する。
* データを他の Azure Storage サービスに保存して、履歴データに基づいて機械学習モデルをトレーニングしたり、バッチ分析を実行したりできます。

次の画像は、Stream Analytics のパイプラインを示したものです。Stream Analytics ジョブでは、入出力されるすべてのデータを使用できるほか、一部のデータを選んで使用することもできます。 この画像は、Stream Analytics に対してデータがどのように送信され、分析され、その後他のアクション (保存、プレゼンテーションなど) に送信されるかを示しています。

![Stream Analytics 導入のパイプライン](./media/stream-analytics-introduction/stream-analytics-intro-pipeline.png)

## <a name="key-capabilities-and-benefits"></a>主要な機能と利点

Azure Stream Analytics は、あらゆるジョブの規模に対応するスケーラビリティ、使いやすさ、柔軟性、信頼性を実現する設計になっています。 複数の Azure リージョンにまたがって使用できます。 次の画像は、Azure Stream Analytics の主な機能を示しています。

![Stream Analytics の主な機能](./media/stream-analytics-introduction/stream-analytics-key-capabilities.png)

## <a name="ease-of-getting-started"></a>簡単に始められる

Azure Stream Analytics は簡単に始めることができます。 ほんの数回のクリックでさまざまなソースやシンクに接続し、エンド ツー エンドのパイプラインを作成することができます。 ストリーミング データ インジェストに関して、Stream Analytics は、[Azure Event Hubs](/azure/event-hubs/) や [Azure IoT Hub](/azure/iot-hub/) に接続することができます。 また、[Azure Blob Storage](/azure/storage/storage-introduction) サービスに接続して、履歴データを取り込むこともできます。 イベント ハブからのデータは、他のデータ ソースや処理エンジンと結び付けることが可能です。 ジョブの入力には、参照データ (静的なデータ、緩やかに変化するデータ) を含めることもでき、その参照データにストリーミング データを結合することで、ルックアップ操作を実行することができます。

Stream Analytics は、[Azure BLOB](/azure/storage/storage-introduction)、[Azure SQL Database](/azure/sql-database/)、[Azure Data Lake Store](/azure/data-lake-store/)、[Azure Cosmos DB](/azure/cosmos-db/introduction) など、さまざまなストレージ システムにジョブの出力をルーティングすることができます。 保存後、Azure HDInsight を使ってバッチ分析を実行したり、出力を別のサービス (イベント ハブなど) に送信して利用したり、出力を [Power BI](https://docs.microsoft.com/power-bi/) に送信して、Power BI のストリーミング API を使ってリアルタイムに視覚化したりすることができます。

## <a name="programmer-productivity"></a>プログラマの生産性

Azure Stream Analytics に使用されているシンプルな SQL ベースのクエリ言語は、移動中のデータを分析するための強力な時間制約によって拡張されています。 ジョブの変換を定義するには、単純な SQL コンストラクトで複雑なテンポラル クエリおよび分析を作成することができる、シンプルな宣言型の [Stream Analytics クエリ言語](https://msdn.microsoft.com/library/azure/dn834998.aspx)を使用します。 Stream Analytics のクエリ言語は、SQL 言語との一貫性が確保されているため、SQL 言語の知識があればすぐにジョブを作成することができます。 ジョブの作成には、Azure PowerShell、[Stream Analytics Visual Studio ツール](stream-analytics-tools-for-visual-studio-install.md)、Azure Resource Manager テンプレートなどの開発者ツールを使用することもできます。 開発者ツールを使用することで、変換クエリをオフラインで開発したり、[CI/CD パイプライン](stream-analytics-tools-for-visual-studio-cicd.md)を使用して Azure にジョブを送ったりすることができます。 

Stream Analytics クエリ言語には、ストリーミング データを分析したり処理したりするための関数が豊富に用意されています。 このクエリ言語では、単純なデータ操作と集計のための関数から、複雑な地理空間関数までサポートされます。 クエリはポータルで編集した後、ライブ ストリームから抽出されたサンプル データを使ってテストすることができます。

クエリ言語の機能は、別の関数を定義したり呼び出したりすることで拡張することができます。 Azure Machine Learning サービスで関数の呼び出しを定義することにより、Azure Machine Learning のソリューションを最大限に活かすことができます。また、JavaScript のユーザー定義関数 (UDF) を組み込んで、Stream Analytics のクエリ内で複雑な計算を行うこともできます。

## <a name="fully-managed"></a>フル マネージド 

Azure Stream Analytics は、Azure のフル マネージド サーバーレス (PaaS) プランです。 つまり、目的のジョブを実行するために、自分でハードウェアをプロビジョニングしたりクラスターを管理したりする必要はありません。 ジョブは、Azure Stream Analytics によって完全に管理され、クラウドにおける複雑なコンピューティング クラスターの設定や、ジョブを実行するために必要なパフォーマンス チューニングは Azure Stream Analytics によって行われます。 Azure Event Hubs や Azure IoT Hub と統合することで、接続されたデバイス、クリックストリーム、およびログ ファイルから 1 秒間に数百万ものイベントを取り込むことができます。 イベント ハブのパーティション機能を使用すると、計算を論理上のステップに分割できます。スケーラビリティを高めるために、分割した各ステップは、さらに分割できるようになっています。

## <a name="run-in-the-cloud-on-in-the-intelligent-edge"></a>クラウドまたはインテリジェント エッジで実行

Azure Stream Analytics は、大規模な分析の場合はクラウドで実行できます。また、非常に低遅延の分析の場合はインテリジェント エッジで実行できます。
Azure Stream Analytics では、クラウドとインテリジェント エッジの両方で同じクエリ言語を使用するため、開発者はストリーム処理用の真にハイブリッドなアーキテクチャを構築できます。

## <a name="low-total-cost-of-ownership"></a>総保有コストが低い

クラウド サービスである Stream Analytics は、コストに最適化されています。 初期費用はかかりません。課金の対象となるのは、[利用ストリーミング ユニット](stream-analytics-streaming-unit-consumption.md)と処理したデータの量だけです。 コミットメントやクラスターのプロビジョニングは不要です。 実際のビジネス ニーズに応じてストリーミング ジョブをスケールアップしたりスケールダウンしたりすることができます。 

## <a name="mission-critical-ready"></a>ミッション クリティカル対応
Azure Stream Analytics は世界中の複数のリージョンで使用でき、信頼性、セキュリティ、コンプライアンスの要件をサポートすることで、ミッション クリティカルなワークロードを実行するように設計されています。
### <a name="reliability"></a>信頼性
厳密には、Azure Stream Analytics で保証されるイベントの処理は 1 回、イベントの配信は 1 回以上となります。そのため、イベントが失われることはありません。 [イベント配信の保証](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics)に関するページで説明されているように、選択した出力が提供される厳密に 1 回だけの処理が保証されます。
Azure Stream Analytics には、イベントの配信に失敗した場合のために、組み込みの回復機能が備わっています。 また、Stream Analytics では、ジョブの状態を保持するために組み込みのチェックポイント機能が提供され、反復可能な結果が提供されます。

Stream Analytics はマネージド サービスとして、分レベルで 99.9% の可用性のイベント処理が証されます。 詳細については、「[Stream Analytics の SLA](https://azure.microsoft.com/support/legal/sla/stream-analytics/v1_0/)」を参照してください。 

### <a name="security"></a>セキュリティ
セキュリティに関しては、Azure Stream Analytics では、すべての着信および発信の通信が暗号化され、TLS 1.2 がサポートされています。 組み込みのチェックポイントも暗号化されます。 Stream Analytics では、すべての処理はメモリ内で実行されるため、受信データが格納されることはありません。 

### <a name="compliance"></a>コンプライアンス
「[Overview of Microsoft Azure compliance (Microsoft Azure のコンプライアンスの概要)](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)」で説明されているように、Azure Stream Analytics は複数のコンプライアンス認定に従っています。 

## <a name="performance"></a>パフォーマンス

Stream Analytics は、1 秒間に何百万というイベントを処理できるため、短い待ち時間で結果を配信することができます。
大規模でリアルタイムの複雑なイベント処理アプリケーションに対応するために、スケールアップまたはスケールアウトを行うことができます。 Stream Analytics は、パーティション分割によるパフォーマンスをサポートしています。そのため、複雑なクエリを並列化し、複数のストリーミング ノードで実行することができます。
Azure Stream Analytics の基盤となっている [Trill](https://github.com/Microsoft/Trill) は、Microsoft Research と共同で開発された、高パフォーマンスのメモリ内ストリーミング分析エンジンです。 

## <a name="next-steps"></a>次の手順

Azure Stream Analytics の概要は以上です。 次はさらに踏み込んで、初めての Stream Analytics ジョブを作成してみましょう。

* [Azure Portal を使用して Stream Analytics ジョブを作成する](stream-analytics-quick-create-portal.md)。
* [Azure PowerShell を使用して Stream Analytics ジョブを作成する](stream-analytics-quick-create-powershell.md)。
* [Visual Studio を使用して Stream Analytics ジョブを作成する](stream-analytics-quick-create-vs.md)

