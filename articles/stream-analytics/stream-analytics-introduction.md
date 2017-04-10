---
title: "Stream Analytics の概要 | Microsoft Docs"
description: "モノのインターネット (IoT) からのストリーミング データをリアルタイムで分析するのに役立つ管理されたサービスである Stream Analytics について説明します。"
keywords: "サービスとしての分析、管理されたサービス、ストリーム プロセス、Stream Analytics、Stream Analytics とは"
services: stream-analytics
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 613c9b01-d103-46e0-b0ca-0839fee94ca8
ms.service: stream-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 2b4a10c77ae02ac0e9eeecf6d7d6ade6e4c33115
ms.openlocfilehash: 493a915fb4e3de4cb70324b22beeaae6720a3492
ms.lasthandoff: 01/25/2017


---
# <a name="what-is-stream-analytics"></a>Stream Analytics とは
Azure Stream Analytics は、完全に管理された、コスト効率の高いリアルタイムのイベント処理エンジンです。データから深い洞察を明らかにするうえで役立ちます。 Stream Analytics では、デバイス、センサー、Web サイト、ソーシャル メディア、アプリケーション、インフラストラクチャ システムなどのデータ ストリーミングのリアルタイム分析の計算を簡単に設定できます。

Azure ポータルでわずか数回クリックするだけで、ストリーミング データの入力ソース、ジョブの結果の出力シンク、SQL に似た言語で表現したデータ変換を指定して、Stream Analytics ジョブを作成できます。 Azure ポータルでジョブの規模と速度を監視し調整して、1 秒間に処理するイベント数を数 KB から&1; GB 以上へ簡単に拡張できます。

Stream Analytics で活用されている、時間が重要となる処理向けに高度にチューニングされたストリーミング エンジンの開発と、直感的な仕様記述を実現するための言語の統合は、Microsoft Research が長年にわたって取り組んできた成果です。

## <a name="what-can-i-use-stream-analytics-for"></a>Stream Analytics を使ってできること
今日のネットワークには大量のデータが高速で流れています。 これらのストリーミング データをリアルタイムで処理し、行動の基礎に据えることができれば、組織の効率を大幅に向上し、市場での差別化を図ることができます。 リアルタイムのストリーミング分析を活用するシナリオは、あらゆる業界に見ることができます。たとえば、金融サービス会社が提供する個人向けリアルタイム株式取引の分析と通知、リアルタイムの不正検出、データと ID の保護サービス、モノに埋め込まれたセンサーやアクチュエータで生成されたデータの安全な取り込みと分析 (モノのインターネット (IoT))、Web クリック ストリーム分析、一定期間におけるカスタマー エクスペリエンスが低下した場合にアラートを発行する CRM アプリケーションなどが挙げられます。 企業は、競争の激しい現代のビジネス環境で成功を収めるために、このようなリアルタイムのイベント ストリーム データ分析を実行できる最も柔軟性、信頼性、コスト効率の高い方法を探し求めています。

## <a name="key-capabilities-and-benefits"></a>主要な機能と利点
* **使いやすさ:** Stream Analytics は、変換を記述するための単純な宣言型のクエリ モデルをサポートします。 使いやすさを最大限に高めるために、Stream Analytics は T-SQL に似た言語を使用しているため、お客様がストリーム処理システムの技術的な複雑さに対処する必要がなくなります。 ブラウザー内クエリ エディターで [Stream Analytics クエリ言語](https://msdn.microsoft.com/library/azure/dn834998.aspx) を使用することで、IntelliSense オートコンプリートを使用して、結合、集計、射影、フィルターなどの一般的な操作はもちろん、時間ベース結合、ウィンドウ表示の集計関数、および時間的フィルターの時系列クエリも短時間で簡単に実装できます。 さらに、サンプル データ ファイルに対してブラウザー内のクエリをテストできるため、迅速な反復的開発が可能になります。  
* **拡張性**: Stream Analytics は、最大 1 GB/秒の高いイベント スループットの処理をサポートしています。 [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) や [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) と統合することで、接続されたデバイス、クリックストリーム、およびログ ファイルから&1; 秒間に数百万ものイベントを取り込むことができます。 これを実現するために、Stream Analytics では Event Hubs のパーティション分割機能を使用しています。この機能により、パーティションごとに 1 MB/秒の処理が可能になります。 ユーザーは、計算処理をクエリ定義内の複数の論理的なステップにパーティション分割し、各ステップをさらに分割して、スケーラビリティを向上できます。  
* **高い信頼性、反復可能性、および迅速な回復**: クラウド内で管理されるサービスである Stream Analytics は、データ損失を防止するのに役立ち、障害が生じた場合でも組み込みの回復機能を通じてビジネス継続性を提供します。 また、内部に状態を保持できる特性を通じて、繰り返し可能な処理結果を提供できます。これを利用して、イベントをアーカイブしておき、後で処理に再適用して常に同じ結果を取得することができます。 お客様は、根本原因の分析や what if 分析などを実行するときに、これを使用して時間をさかのぼり、計算処理を調査できます。  
* **低コスト:** Stream Analytics サービスは、クラウド サービスとしてリアルタイム分析ソリューションを低コストで実装し、保守できるように最適化されています。 料金は、ストリーミング ユニットの使用量とシステムで処理されるデータ量に基づく従量課金制です。 使用量は、処理されたイベントのボリュームと、その Stream Analytics ジョブを処理するためにクラスター内にプロビジョニングされたコンピューティング能力の量に基づいて算出されます。  
* **参照データ:** Stream Analytics では、参照データを指定して使用することができます。 参照データには、履歴データか、時間の経過に伴う変化が頻繁にはない単純にストリーミングでないデータを指定できます。 参照データは、システムにより、他の受信イベント ストリームと同様に扱われ、リアルタイムで取り込まれる他のイベント ストリームと結合されて変換が実行されるので、簡単に使用できます。  
* **ユーザー定義関数:** Stream Analytics は Azure Machine Learning と統合されているため、Stream Analytics クエリの構成要素として、Machine Learning サービスの関数呼び出しを定義することができます。 Stream Analytics の機能を拡張して、既存の Azure Machine Learning ソリューションを有効活用することができます。 この点について詳しくは、 [Machine Learning 統合のチュートリアル](stream-analytics-machine-learning-integration-tutorial.md)をご覧ください。
* **接続**: Stream Analytics は、Azure Event Hubs と Azure IoT Hub に直接接続してストリームを取り込み、Azure BLOB サービスに直接接続して履歴データを取り込みます。 結果は、Stream Analytics から Azure Storage Blobs または Tables、Azure SQL DB、Azure Data Lake Store、DocumentDB、Event Hubs、Azure Service Bus Topics または Queues、Power BI に書き込まれます。そこで、視覚化、ワークフローによる処理、[Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) を介したバッチ分析での使用、一連のイベントとしての再処理を実行することができます。 Event Hubs を使用すると、計算処理のストリーミングの性質を失うことなく、複数の Stream Analytics を他のデータ ソースや処理エンジンとまとめて構成できます。  

## <a name="get-help"></a>問い合わせ
さらにサポートが必要な場合は、 [Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>次のステップ
モ ノのインターネットからのデータをストリーム分析する管理サービスである、 Stream Analytics の概要です。 このサービスの詳細については、以下の情報をご覧ください。

* [Azure Stream Analytics の使用](stream-analytics-get-started.md)
* [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](https://msdn.microsoft.com/library/azure/dn835031.aspx)


