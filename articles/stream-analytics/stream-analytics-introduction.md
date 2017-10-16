---
title: "Stream Analytics の概要 | Microsoft Docs"
description: "モノのインターネット (IoT) からのストリーミング データをリアルタイムで分析するのに役立つ管理されたサービスである Stream Analytics について説明します。"
keywords: "サービスとしての分析、管理されたサービス、ストリーム プロセス、Stream Analytics、Stream Analytics とは"
services: stream-analytics
documentationcenter: 
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 613c9b01-d103-46e0-b0ca-0839fee94ca8
ms.service: stream-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 08/08/2017
ms.author: samacha
ms.openlocfilehash: 6bb70ba7588b8c6e9261fca097403e7fccb041eb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-stream-analytics"></a>Stream Analytics とは

Azure Stream Analytics は、完全に管理されたイベント処理エンジンであり、ストリーミング データを対象としたリアルタイム分析の計算を設定することができます。 対象となるデータは、デバイス、センサー、Web サイト、ソーシャル メディア フィード、アプリケーション、インフラストラクチャ システムなどから得ることができます。 

## <a name="what-can-i-do-with-stream-analytics"></a>Stream Analytics を使ってできること

Stream Analytics を使うと、デバイスやプロセスから流れてくる大量のデータを観察し、データ ストリームから情報を抽出して、パターンや傾向、関係を調べることができます。 データの内容に応じて、アプリケーションのタスクを実行することができます。 そのようなタスクの例としては、アラートの生成やオートメーション ワークフローの開始、レポート ツール (Power BI など) への情報のフィード、将来調査に使用するデータの保存などが考えられます。 

次に例を示します。

* 金融サービス会社が個人に合わせて提供する株式取引のリアルタイム分析とアラート。
* トランザクション データを観察することによって不正行為をリアルタイムに検出。 
* データと ID の保護サービス。
* 対象物 (モノのインターネット、IoT) に埋め込まれたセンサーやアクチュエータから生成されたデータの分析。
* Web クリックストリーム分析。
* 顧客関係管理 (CRM) アプリケーション (特定の期間のカスタマー エクスペリエンスが低下したときにアラートを生成するなど)。

## <a name="how-does-stream-analytics-work"></a>Stream Analytics の動作

この図は、Stream Analytics パイプラインでデータが取り込まれ、分析されて、表示または処理のために送信されるようすを示したものです。 

![Stream Analytics パイプライン](./media/stream-analytics-introduction/stream_analytics_intro_pipeline.png)

Stream Analytics の原点となるのは、ストリーミング データのソースです。 Azure には、Azure イベント ハブまたは IoT Hub を使用してデバイスからデータを取り込むことができます。 Azure Blob Storage などのデータ ストアからデータを取り込むこともできます。 

ストリームを観察するには、データをどこから取り込むかを指定する Stream Analytics "*ジョブ*" を作成します。 このジョブでは、"*変換*" (データやパターン、関係をどのように探し出すか) も指定します。 このタスクに関して、Stream Analytics は SQL に似たクエリ言語をサポートしており、特定の期間のストリーミング データに対してフィルター選択、並べ替え、集計、結合の処理を適用することができます。

また、変換したデータの出力先もこのジョブで指定します。 これにより、分析した情報に応じて、処理の内容を制御することができます。 たとえば、分析結果に応じて次の処理を行うことが考えられます。

* デバイスの設定を変更するコマンドを送信する。 
* キューにデータを送信する。この場合、キューを監視しているプロセスによって、検出した内容に応じた処理が実行されます。 
* Power BI ダッシュボードにデータを送信してレポートを作成する。
* ストレージ (Data Lake Store、SQL Server データベース、Azure Blob Storage、Azure Table Storage など) にデータを送信する。

実行中のジョブを監視して、1 秒あたりに処理するイベントの数を調整することができます。 また、トラブルシューティング用の診断ログをジョブから生成することもできます。

## <a name="key-capabilities-and-benefits"></a>主要な機能と利点

Stream Analytics は、あらゆるジョブの規模に対応するスケーラビリティ、使いやすさ、柔軟性、経済性を実現する設計になっています。

### <a name="connectivity-to-many-inputs-and-outputs"></a>さまざまな入力と出力への接続

Stream Analytics は、ストリーム データの取り込みに関しては [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) と [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) に、履歴データの取り込みに関しては [Azure Blob Storage サービス](https://docs.microsoft.com/azure/storage/storage-introduction#blob-storage-accounts)に直接接続されます。 イベント ハブからデータを取得する場合は、Stream Analytics を他のデータ ソースや処理エンジンと結び付けることが可能です。

ジョブの入力に参照データ (静的なデータや変化の速度が遅いデータ) を含めることもできます。 この参照データにストリーミング データを結合すれば、データベースのクエリと同様のルックアップ操作を行うことができます。

Stream Analytics ジョブの出力は、さまざまな方向にルーティングすることができます。 書き込み先に使用できるストレージとして、Azure Storage の BLOB やテーブル、Azure SQL DB、Azure Data Lake Store、Azure Cosmos DB などがあります。 そこに格納されたデータは、Azure HDInsight で一括分析することができます。 出力結果をイベント ハブや Azure Service Bus のトピック、キューなどのサービスに送って、別の処理に投入することもできます。 出力結果を Power BI に送れば、データを視覚化することができます。

### <a name="ease-of-use"></a>使いやすさ

変換の定義には、宣言型の単純な [Stream Analytics クエリ言語](https://msdn.microsoft.com/library/azure/dn834998.aspx)を使用します。プログラミングを一切行うことなく高度な分析を作成することができます。 ストリーミング データがクエリ言語の入力となります。 このデータに対して、フィルター選択や並べ替え、値の集計、計算の実行、データの結合 (ストリーム内での結合、または参照データとの結合) を行ったり、地理空間関数を使用したりすることができます。 クエリはポータルで IntelliSense や構文チェックを使って編集でき、ライブ ストリームから抽出できるサンプル データを使ってテストすることができます。

### <a name="extensible-query-language"></a>拡張可能なクエリ言語

クエリ言語の機能は、別の関数を定義したり呼び出したりすることで拡張することができます。 Azure Machine Learning サービスで関数の呼び出しを定義すれば、Azure Machine Learning のソリューションを最大限に活かすことができます。 JavaScript のユーザー定義関数 (UDF) を組み込んで、Stream Analytics のクエリ内で複雑な計算を行うこともできます。

### <a name="scalability"></a>拡張性

Stream Analytics では、1 秒あたり最大 1 GB の入力データを処理することができます。 [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) や [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) と統合することで、接続されたデバイス、クリックストリーム、およびログ ファイルから 1 秒間に数百万ものイベントを取り込むことができます。 イベント ハブのパーティション機能を使用すると、計算を論理上のステップに分割できます。スケーラビリティを高めるために、分割した各ステップは、さらに分割できるようになっています。

### <a name="low-cost"></a>低コスト

クラウド サービスである Stream Analytics は、低コストで運用できるように最適化されています。 料金は、ストリーミング ユニットの使用量とシステムで処理されるデータ量に基づく従量課金制です。 使用量は、処理されたイベントのボリュームと、Stream Analytics ジョブを処理するためにクラスター内にプロビジョニングされたコンピューティング能力の量に基づいて算出されます。

### <a name="reliability-quick-recovery-and-repeatability"></a>高い信頼性、迅速な回復、再現性

Stream Analytics は、クラウド内で管理されるサービスであるため、データの損失を効果的に防止するための対策とビジネス継続性を備えています。 障害が発生した場合は、組み込みの回復機能が作動します。 また、内部に状態を保持できる特性を通じて、繰り返し可能な処理結果を提供できます。これを利用して、イベントをアーカイブしておき、後で処理に再適用して常に同じ結果を取得することができます。 ユーザーは、根本原因の分析や what if 分析などを実行するときに、これを使用して時間をさかのぼり、計算処理を調査できます。

## <a name="next-steps"></a>次のステップ

* [IoT デバイスのデータを使って実際に入力とクエリを試す](stream-analytics-get-started-with-azure-stream-analytics-to-process-data-from-iot-devices.md)。
* 電話のメタデータを分析して不正行為を見つける[エンドツーエンドの Stream Analytics ソリューション](stream-analytics-real-time-fraud-detection.md)を構築する。
* Stream Analytics で使用される SQL に似たクエリ言語と、[ウィンドウ関数](stream-analytics-window-functions.md)に見られるような独特の概念について理解を深める。
* [Stream Analytics ジョブをスケーリング](stream-analytics-scale-jobs.md)する方法について調べる。 
* [Stream Analytics と Azure Machine Learning を統合](stream-analytics-machine-learning-integration-tutorial.md)する方法について調べる。
* [Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)で Stream Analytics に関する質問の回答を見つける。

