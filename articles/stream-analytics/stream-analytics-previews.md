---
title: Azure Stream Analytics のプレビュー機能
description: この記事では、現在プレビュー段階にある Azure Stream Analytics の機能を示します。
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/05/2019
ms.openlocfilehash: 08430f3eee858cdb6c9a7fbdfe11bd4c00ef148d
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58630399"
---
# <a name="azure-stream-analytics-preview-features"></a>Azure Stream Analytics のプレビュー機能

この記事は、現在プレビュー段階にある Azure Stream Analytics のすべての機能をまとめたものです。 プレビュー機能を運用環境で使用することはお勧めしません。

## <a name="public-previews"></a>パブリック プレビュー

以下の機能はパブリック プレビュー段階です。 これらの機能は現在でも利用できますが、運用環境では使用しないでください。

### <a name="anomaly-detection"></a>異常検出

Azure Stream Analytics では、"*スパイク*" と "*ディップ*" の検出、および双方向、ゆっくりした増加、ゆっくりした減少の傾向の検出のサポートを備えた、新しい機械学習モデルが導入されています。 詳細については、「[Azure Stream Analytics での異常検出](stream-analytics-machine-learning-anomaly-detection.md)」を参照してください。

### <a name="sql-database-reference-data"></a>SQL Database 参照データ

Azure Stream Analytics では、参照データ入力のソースとして Azure SQL Database がサポートされています。 Stream Analytics ツールを使用して、Azure portal および Visual Studio で Stream Analytics ジョブに対する参照データとして SQL Database を使用できます。 詳細については、[SQL Database からの参照データの Azure Stream Analytics ジョブでの使用](sql-reference-data.md)に関するページを参照してください。

### <a name="integration-with-azure-machine-learning"></a>Azure Machine Learning との統合

Machine Learning (ML) 関数を使用して Stream Analytics のジョブをスケーリングできます。 Stream Analytics ジョブで ML 関数を使用する方法について詳しくは、「[Azure Machine Learning 関数を使用した Stream Analytics ジョブのスケーリング](stream-analytics-scale-with-machine-learning-functions.md)」をご覧ください。 現実のシナリオについては、「[Azure Stream Analytics と Azure Machine Learning を使用した感情分析の実行](stream-analytics-machine-learning-integration-tutorial.md)」を確認してください。

### <a name="javascript-user-defined-aggregate"></a>JavaScript ユーザー定義集計

Azure Stream Analytics は JavaScript で記述されたユーザー定義集計 (UDA) をサポートしています。これを使用すると複雑なステートフルのビジネス ロジックを実装できます。 UDA の使用方法については、「[Azure Stream Analytics の JavaScript ユーザー定義集計](stream-analytics-javascript-user-defined-aggregates.md)」をご覧ください。 

### <a name="live-data-testing-in-visual-studio"></a>Visual Studio でのライブ データ テスト

Visual Studio Tools for Azure Stream Analytics ではローカル テスト機能が強化され、イベント ハブや IoT ハブなどのクラウド ソースからのライブ イベント ストリームに対してクエリをテストすることができます。 方法については、「[Visual Studio の Azure Stream Analytics ツールを使用してライブ データをローカルにテストする](stream-analytics-live-data-local-testing.md)」をご覧ください。

### <a name="net-user-defined-functions-on-iot-edge"></a>IoT Edge での .NET ユーザー定義関数

.NET Standard のユーザー定義関数を使用すると、ストリーミング パイプラインの一部として .NET Standard のコードを実行できます。 簡単な C# クラスを作成したり、完全なプロジェクトやライブラリをインポートしたりできます。 Visual Studio では完全な作成およびデバッグ エクスペリエンスがサポートされています。 詳しくは、「[Azure Stream Analytics Edge ジョブの .NET Standard ユーザー定義関数の開発](stream-analytics-edge-csharp-udf-methods.md)」をご覧ください。

## <a name="private-previews"></a>プライベート プレビュー

以下の機能は、プライベート プレビュー段階です。

### <a name="c-custom-deserializer-for-azure-stream-analytics-on-iot-edge"></a>Azure Stream Analytics on IoT Edge 用の C# カスタム デシリアライザー

開発者は、C# でカスタム デシリアライザーを実装して、Azure Stream Analytics で受信したイベントを逆シリアル化できるようになりました。 逆シリアル化できる形式としては、Parquet、Protobuf、XML、または任意のバイナリ形式などがあります。

### <a name="visual-studio-code-for-azure-stream-analytics"></a>Azure Stream Analytics 用の Visual Studio Code

Visual Studio Code で Azure Stream Analytics のジョブを作成できます。 ツールのプライベート プレビュー機能にアクセスするには、*ASAToolsfeedback\@microsoft.com* にお問い合わせください。

## <a name="next-steps"></a>次の手順

* [Azure Stream Analytics での 8 つの新機能](https://azure.microsoft.com/blog/eight-new-features-in-azure-stream-analytics/)

* [Azure Stream Analytics で利用可能になった 4 つの新機能](https://azure.microsoft.com/blog/4-new-features-now-available-in-azure-stream-analytics/)
