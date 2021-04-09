---
title: Azure でリアルタイムおよびストリーム処理ソリューションを選択する
description: Azure でアプリケーションを構築する目的で、正しいリアルタイム分析とストリーミング処理技術を選択する方法について説明します。
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: 4c10a91971357001723adcb783253c9867cf6d87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98019059"
---
# <a name="choose-a-real-time-analytics-and-streaming-processing-technology-on-azure"></a>Azure でリアルタイム分析とストリーミング処理技術を選択する

Azure では、リアルタイム分析とストリーミング処理のためのサービスがいくつか用意されています。 この記事では、自分のアプリケーションに最適な技術を決定するために必要な情報が提供されます。

## <a name="when-to-use-azure-stream-analytics"></a>Azure Stream Analytics を使用する状況

Azure Stream Analytics は、Azure でのストリーム分析に推奨されるサービスです。 幅広いシナリオに対応しています。それには以下が含まれますが、それらに限定されません。

* データ視覚化のためのダッシュボード
* 一時的および空間的なパターンまたは異常からのリアルタイム [アラート](stream-analytics-set-up-alerts.md)
* Extract Transform Load (ETL)
* [イベント ソーシング パターン](/azure/architecture/patterns/event-sourcing)
* [IoT Edge](stream-analytics-edge.md)

アプリケーションに Azure Stream Analytics ジョブを追加することが、既に知っている言語を使用し、Azure でストリーミング分析を実行する最も簡単な方法です。 Azure Stream Analytics はジョブ サービスです。そのため、クラスターの管理に時間を費やす必要がありません。また、ジョブ レベルの 99.9% SLA により、ダウンタイムを心配する必要がありません。 課金もジョブ レベルで行われることで、スタートアップ コストが低くなりますが (1 ストリーミング ユニット)、スケーラブルです (最大 192 ストリーミング ユニット)。 クラスターを実行し、保守管理するより、少ない Stream Analytics ジョブを実行するほうがコスト効果がずっと高くなります。

Azure Stream Analytics には、面倒な設定なし使える機能が豊富に用意されています。 追記の設定なしで、次の機能をすぐに活用できます。

* [ウィンドウ集計](stream-analytics-window-functions.md)、一時的な結合、一時的な分析関数など、組み込みの一時的演算子。
* ネイティブ Azure [入力](stream-analytics-add-inputs.md)および[出力](stream-analytics-define-outputs.md)アダプター
* ジオフェンシングのための地理空間参照データとの結合を含む、[参照データ](stream-analytics-use-reference-data.md) (ルックアップ テーブルとも呼ばれています) の緩やかな変化に対応。
* [異常検出](stream-analytics-machine-learning-anomaly-detection.md)など、統合ソリューション
* 同じクエリで複数の時間枠
* 任意の順序で一時的演算子を複数作成する機能。
* Event Hubs に到着した入力から Event Hubs に到着する出力までのエンドツーエンドの待ち時間を 100 ミリ秒以下 (Event Hubs との間のネットワーク遅延を含む) に維持した高いスループット。

## <a name="when-to-use-other-technologies"></a>他の技術を使用する状況

### <a name="you-want-to-write-udfs-udas-and-custom-deserializers-in-a-language-other-than-javascript-or-c"></a>JavaScript または C# 以外の言語で UDF、UDA、カスタム デシリアライザーを記述する

Azure Stream Analytics では、クラウド ジョブの場合は JavaScript の、IoT Edge ジョブの場合で C# のユーザー定義関数 (UDF) またはユーザー定義集計 (UDA) がサポートされています。 C# ユーザー定義デシリアライザーもサポートされます。 Java や Python など、他の言語でデシリアライザー、UDF、UDA を実装する場合、Spark Structured Streaming を使用できます。 任意のストリーミング処理を実行する目的で、独自の仮想マシンで Event Hubs **EventProcessorHost** を実行することもできます。

### <a name="your-solution-is-in-a-multi-cloud-or-on-premises-environment"></a>ソリューションがマルチクラウドまたはオンプレミス環境にある

Azure Stream Analytics は Microsoft の独占技術であり、Azure でのみ利用できます。 クラウドまたはオンプレミスをまたいでソリューションを移植する必要がある場合、Spark Structured Streaming や Storm など、オープンソース技術を検討してください。

## <a name="next-steps"></a>次の手順

* [Azure portal を使用して Stream Analytics ジョブを作成する](stream-analytics-quick-create-portal.md)
* [Azure PowerShell を使用して Stream Analytics ジョブを作成する](stream-analytics-quick-create-powershell.md)
* [Visual Studio を使用して Stream Analytics ジョブを作成する](stream-analytics-quick-create-vs.md)
* [Visual Studio Code を使用して Stream Analytics ジョブを作成する](quick-create-visual-studio-code.md)
