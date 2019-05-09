---
title: Azure Stream Analytics での Machine Learning 関数のスケーリング
description: この記事では、パーティション分割とストリーミング ユニットを構成することにより、Machine Learning 関数を使用する Stream Analytics ジョブをスケーリングする方法について説明します。
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/28/2017
ms.openlocfilehash: f11034a4970e3fb95333310af82a6b2a2551f1eb
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59257282"
---
# <a name="scale-your-stream-analytics-job-with-azure-machine-learning-functions"></a>Azure Machine Learning 関数を使用した Stream Analytics ジョブのスケーリング
Stream Analytics ジョブを設定し、そのジョブにサンプル データを供給することは簡単です。 同じジョブを大量のデータに対して実行する必要がある場合は、どうしたらよいのでしょうか。 それには、Stream Analytics ジョブをスケールできるように構成する方法を把握する必要があります。 このドキュメントでは、Machine Learning 関数を使用した Stream Analytics ジョブのスケーリングの特別な側面について説明します。 通常の Stream Analytics ジョブをスケーリングする方法の詳細については、「 [ストリーム データ処理スループット向上のための Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)」を参照してください。

## <a name="what-is-an-azure-machine-learning-function-in-stream-analytics"></a>Stream Analytics の Azure Machine Learning 関数とは
Stream Analytics の Machine Learning 関数は、Stream Analytics クエリ言語の通常の関数呼び出しのように使用できます。 ただし、この関数呼び出しは、バックグラウンドでは実際には Azure Machine Learning Web サービス要求です。 Machine Learning Web サービスでは、同じ Web サービス API 呼び出しで複数の行の "バッチ処理" をサポートしています。このバッチ処理はミニ バッチと呼ばれ、全体的なスループットを向上させます。 詳しくは、[Azure Machine Learning Web サービス](../machine-learning/studio/consume-web-services.md)に関する記事をご覧ください。

## <a name="configure-a-stream-analytics-job-with-machine-learning-functions"></a>Machine Learning 関数を使用した Stream Analytics ジョブの構成
Stream Analytics ジョブの Machine Learning 関数を構成するときに検討が必要なパラメーターが 2 つあります。Machine Learning 関数呼び出しのバッチ サイズと、Stream Analytics ジョブのためにプロビジョニングされたストリーミング ユニット (SU) です。 SU の適切な値を決定するには、最初に、待ち時間とスループット、つまり、Stream Analytics ジョブの待ち時間と各 SU のスループットを決定する必要があります。 完全にパーティション分割された Stream Analytics クエリのスループットを向上させるために、いつでもジョブに SU を追加できますが、SU を追加すると、ジョブの実行コストが増加します。

そのため、Stream Analytics ジョブの実行の待機時間の*許容範囲*を決定することが重要です。 Azure Machine Learning サービス要求を実行することによる追加の待機時間は、当然のことながらバッチ サイズに応じて増加します。それにより、Stream Analytics ジョブの待機時間が長くなります。 一方、バッチ サイズを増やすと、Stream Analytics ジョブは "*同じ数の Machine Learning Web サービス要求でより多くのイベント*" を処理できます。 多くの場合、Machine Learning Web サービスの待機時間はバッチ サイズの増加にほぼ比例して増加します。そのため、特定の状況で最もコスト効率が高い Machine Learning Web サービスのバッチ サイズを考えることが重要です。 Web サービス要求の既定のバッチ サイズは 1,000 です。変更するには、[Stream Analytics REST API](https://msdn.microsoft.com/library/mt653706.aspx "Stream Analytics REST API") または [Stream Analytics 用の PowerShell クライアント](stream-analytics-monitor-and-manage-jobs-use-powershell.md "Stream Analytics 用の PowerShell クライアント")を使用します。

バッチ サイズを決定したら、関数が 1 秒あたりに処理する必要があるイベントの数に基づいてストリーミング ユニット (SU) の数を決定できます。 ストリーミング ユニットの詳細については、[Stream Analytics でのジョブのスケーリング](stream-analytics-scale-jobs.md)に関する記事をご覧ください。

通常、Machine Learning Web サービスへのコンカレント接続は 6 SU ごとに 20 個あります。ただし、1 SU のジョブや 3 SU のジョブでもコンカレント接続は 20 個になります。  たとえば、データ入力速度が 1 秒あたり 200,000 イベントで、バッチ サイズが既定値の 1,000 のままの場合、1,000 イベント ミニ バッチでの Web サービスの待機時間は 200 ミリ秒になります。 つまり、各接続で 1 秒あたり 5 つの要求を Machine Learning Web サービスに対して行うことができます。 接続が 20 個の場合、200 ミリ秒で 20,000 イベント、つまり 1 秒あたり 100,000 イベントを処理できます。 したがって、1 秒あたり 200,000 イベントを処理するには 40 個のコンカレント接続が必要なため、12 SU となります。 次の図には、Stream Analytics ジョブから Machine Learning Web サービス エンドポイントへの要求を示しています。Learning Web サービスへのコンカレント接続は 6 SU ごとに最大で 20 個あります。

![Machine Learning 関数を使用した Stream Analytics のスケーリング - 2 つのジョブの例](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "Machine Learning 関数を使用した Stream Analytics のスケーリング - 2 つのジョブの例")

一般的に、バッチ サイズが ***B*** で、バッチ サイズ B での Web サービスの待機時間が ***L*** (ミリ秒単位) の場合、***N*** SU での Stream Analytics ジョブのスループットは次のようになります。

![Machine Learning 関数を使用した Stream Analytics のスケーリング - 数式](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "Machine Learning 関数を使用した Stream Analytics のスケーリング - 数式")

他に検討が必要な値は、Machine Learning Web サービス側の "最大同時呼び出し数" です。これは最大値 (現在は 200) に設定することをお勧めします。

この設定の詳細については、[Machine Learning Web サービスのスケーリング](../machine-learning/studio/scaling-webservice.md)に関する記事を参照してください。

## <a name="example--sentiment-analysis"></a>例 – センチメント分析
次の例には、「 [チュートリアル: Stream Analytics と Machine Learning を使用したセンチメント分析の実行](stream-analytics-machine-learning-integration-tutorial.md)」で説明しているセンチメント分析 Machine Learning 関数を使用した Stream Analytics ジョブが含まれています。

次の例に示すように、クエリは完全にパーティション分割されたシンプルなクエリで、その後に **sentiment** 関数が続いています。

```SQL
    WITH subquery AS (
        SELECT text, sentiment(text) as result from input
    )

    Select text, result.[Score]
    Into output
    From subquery
```
次のシナリオについて検討します。ツイートのセンチメント分析 (イベント) を実行する Stream Analytics ジョブを作成する必要があります。スループットは、1 秒あたり 10,000 ツイートです。 この Stream Analytics ジョブでは、1 SU でトラフィックを処理できるでしょうか。 既定のバッチ サイズである 1,000 を使用して、入力に対応できる必要があります。 さらに、追加された Machine Learning 関数によって生成される待機時間は 1 秒以内である必要があります。これは、一般的なセンチメント分析 Machine Learning Web サービスの既定の待機時間です (バッチ サイズが既定の 1,000 の場合)。 通常、Stream Analytics ジョブの**全体的な** (エンド ツー エンドの) 待機時間は数秒です。 この Stream Analytics ジョブ、*特に* Machine Learning 関数呼び出しについて詳しく説明します。 バッチ サイズが 1,000 の場合、10,000 イベントのスループットでは、Web サービスに対する要求は約 10 個になります。 1 SU でも、この入力トラフィックに対応できる十分なコンカレント接続数があります。

イベント入力速度が 100 倍に増加した場合、Stream Analytics ジョブは 1 秒あたり 1,000,000 ツイートを処理する必要があります。 スケールの拡大を実現する 2 つのオプションがあります。

1. バッチ サイズを増やす
2. 入力ストリームをパーティション分割して、イベントを並列処理する

最初のオプションでは、ジョブの**待機時間**が増加します。

2 番目のオプションでは、より多くの SU をプロビジョニングして、生成される同時 Machine Learning Web サービス要求の数を増やす必要があります。 そのため、ジョブ **コスト**が増加します。

センチメント分析 Machine Learning Web サービスの待機時間は、1,000 イベント バッチ以下の場合は 200 ミリ秒、5,000 イベント バッチの場合は 250 ミリ秒、10,000 イベント バッチの場合は 300 ミリ秒、25,000 イベント バッチの場合は 500 ミリ秒と仮定しています。

1. 最初のオプションの使用 (より多くの SU はプロビジョニング**されません**)。 バッチ サイズを **25,000** に増やすことができます。 そうすると、Machine Learning Web サービスへの 20 個のコンカレント接続で 1,000,000 イベントを処理できます (待機時間は 1 回の呼び出しあたり 500 ミリ秒)。 そのため、Machine Learning Web サービス要求に対するセンチメント関数要求により、Stream Analytics ジョブの追加の待機時間は **200 ミリ秒**から **500 ミリ秒**に増加します。 ただし、バッチ サイズを無限に増やすことは**できない**ことに注意してください。Machine Learning Web サービスでは要求のペイロード サイズが 4 MB である必要があり、それより小さい Web サービス要求は操作が 100 秒を超えるとタイムアウトするためです。
2. 2 番目のオプションを使用して、バッチ サイズを 1,000 のままにし、Web サービスの待ち時間が 200 ミリ秒、Web サービスへのコンカレント接続が各 20 個の場合、1,000 * 20 * 5 イベントで 1 秒あたり 100,000 イベントを処理できます。 そのため、1 秒あたり 1,000,000 イベントを処理するには、60 SU が必要です。 最初のオプションよりも Web サービス バッチ要求の数が多くなるため、コストが増加します。

異なる SU とバッチ サイズでの Stream Analytics ジョブのスループット (1 秒あたりのイベント数) を以下の表に示します。

| バッチ サイズ (ML の待機時間) | 500 (200 ミリ秒) | 1,000 (200 ミリ秒) | 5,000 (250 ミリ秒) | 10,000 (300 ミリ秒) | 25,000 (500 ミリ秒) |
| --- | --- | --- | --- | --- | --- |
| **1 SU** |2,500 |5,000 |20,000 |30,000 |50,000 |
| **3 SU** |2,500 |5,000 |20,000 |30,000 |50,000 |
| **6 SU** |2,500 |5,000 |20,000 |30,000 |50,000 |
| **12 SU** |5,000 |10,000 |40,000 |60,000 |100,000 |
| **18 SU** |7,500 |15,000 |60,000 |90,000 |150,000 |
| **24 SU** |10,000 |20,000 |80,000 |120,000 |200,000 |
| **…** |… |... |... |... |... |
| **60 SU** |25,000 |50,000 |200,000 |300,000 |500,000 |

ここまでの説明で、Stream Analytics の Machine Learning 関数のしくみを十分に理解できたと思います。 また、Stream Analytics ジョブがデータ ソースからデータを "プル" し、各 "プル" が Stream Analytics ジョブで処理されるイベントのバッチを返すことも理解されていることでしょう。 このプル モデルは Machine Learning Web サービス要求にどのような影響を与えるのでしょうか。

通常、Machine Learning 関数に設定するバッチ サイズは、Stream Analytics ジョブの各 "プル" から返されるイベントの数で割り切れません。 その場合、Machine Learning Web サービスは "部分的な" バッチで呼び出されます。 これを行うのは、プルどうしのイベントを結合する際にジョブの追加の待機時間のオーバーヘッドが発生しないようにするためです。

## <a name="new-function-related-monitoring-metrics"></a>新しい関数関連の監視メトリック
Stream Analytics ジョブの [監視] 領域には、3 つの関数関連のメトリックが追加されました。 次の図に示すように、[関数要求]、[関数イベント]、[関数の要求に失敗しました] です。

![Machine Learning 関数を使用した Stream Analytics のスケーリング - 指標](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "Machine Learning 関数を使用した Stream Analytics のスケーリング - 指標")

定義は次のとおりです。

**[関数要求]**:関数要求の数。

**[関数イベント]**:関数要求のイベントの数。

**[FAILED FUNCTION REQUESTS]\(失敗した関数要求\)**:失敗した関数要求の数。

## <a name="key-takeaways"></a>重要なポイント
要点をまとめると、Machine Learning 関数を使用して Stream Analytics ジョブをスケールするには、次の項目を考慮する必要があります。

1. イベント入力速度
2. 実行中の Stream Analytics ジョブの許容待機時間 (および Machine Learning Web サービス要求のバッチ サイズ)
3. プロビジョニング済みの Stream Analytics SU および Machine Learning Web サービス要求の数 (関数関連の追加コスト)

完全にパーティション分割された Stream Analytics クエリを例として使用しました。 もっと複雑なクエリが必要な場合は、[Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)を活用してください。Stream Analytics チームから追加のサポートを受けることができます。

## <a name="next-steps"></a>次の手順
Stream Analytics の詳細については、以下を参照してください。

* [Azure Stream Analytics の使用](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](https://msdn.microsoft.com/library/azure/dn835031.aspx)
