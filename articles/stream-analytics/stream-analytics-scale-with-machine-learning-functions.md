---
title: Azure Stream Analytics での Machine Learning 関数のスケーリング
description: この記事では、パーティション分割とストリーミング ユニットを構成することにより、Machine Learning 関数を使用する Stream Analytics ジョブをスケーリングする方法について説明します。
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 3d478c2421066c8347622f9064c479bb8255b112
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621744"
---
# <a name="scale-your-stream-analytics-job-with-azure-machine-learning-studio-functions"></a>Azure Machine Learning Studio 関数を使用した Stream Analytics ジョブのスケーリング

この記事では、Azure Machine Learning 関数を使用する Azure Stream Analytics ジョブを効率的にスケーリングする方法について説明します。 通常の Stream Analytics ジョブをスケーリングする方法の詳細については、「 [ストリーム データ処理スループット向上のための Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)」を参照してください。

## <a name="what-is-an-azure-machine-learning-function-in-stream-analytics"></a>Stream Analytics の Azure Machine Learning 関数とは

Stream Analytics の Machine Learning 関数は、Stream Analytics クエリ言語の通常の関数呼び出しのように使用できます。 ただし、この関数呼び出しは、バックグラウンドでは実際には Azure Machine Learning Web サービス要求です。

複数の行を同じ Web サービス API 呼び出しで "バッチ処理" することで、Machine Learning Web サービス要求のスループットを上げることができます。 このグループ化は、ミニバッチと呼ばれます。 詳しくは、[Azure Machine Learning Studio Web サービス](../machine-learning/studio/consume-web-services.md)に関する記事をご覧ください。 Stream Analytics での Azure Machine Learning Studio のサポートはプレビュー段階です。

## <a name="configure-a-stream-analytics-job-with-machine-learning-functions"></a>Machine Learning 関数を使用した Stream Analytics ジョブの構成

Stream Analytics ジョブで使用される Machine Learning 関数を構成するには、2 つのパラメーターがあります。

* Machine Learning 関数呼び出しのバッチ サイズ。
* Stream Analytics ジョブに対してプロビジョニングするストリーミング ユニット (SU) の数。

SU の適切な値を決定するには、Stream Analytics ジョブの待機時間または各 SU のスループットのどちらを最適化するかを決めます。 適切にパーティション分割された Stream Analytics クエリのスループットを向上させるために、いつでもジョブに SU を追加できます。 SU を追加すると、ジョブの実行コストが増加します。

Stream Analytics ジョブの待機時間の "*許容範囲*" を決定します。 バッチ サイズを大きくすると、Azure Machine Learning service 要求の待機時間と、Stream Analytics ジョブの待機時間が増加します。

バッチ サイズを増やすと、Stream Analytics ジョブでは**同じ数**の Machine Learning Web サービス要求で**より多くのイベント**を処理できます。 通常、Machine Learning Web サービスの待機時間の増加は、バッチ サイズの増加に比例します。 

特定の状況において Machine Learning Web サービスの最もコスト効率のよいバッチ サイズを検討することが重要です。 Web サービス要求の既定のバッチ サイズは、1000 です。 この既定のサイズは、[Stream Analytics REST API](https://docs.microsoft.com/previous-versions/azure/mt653706(v=azure.100) "Stream Analytics REST API") または [Stream Analytics 用 PowerShell クライアント](stream-analytics-monitor-and-manage-jobs-use-powershell.md)を使って変更できます。

バッチ サイズを決定したら、関数で 1 秒間に処理する必要があるイベントの数に基づいてストリーミング ユニット (SU) の数を設定できます。 ストリーミング ユニットの詳細については、[Stream Analytics でのジョブのスケーリング](stream-analytics-scale-jobs.md)に関する記事をご覧ください。

6 SU ごとに、Machine Learning Web サービスへの 20 個のコンカレント接続が取得されます。 一方、1 SU ジョブと 3 SU ジョブでは、20 個のコンカレント接続が取得されます。  

アプリケーションで 1 秒間に 200,000 個のイベントが生成され、バッチ サイズが 1000 の場合、結果の Web サービスの待機時間は 200 ミリ秒です。 このレートは、各接続で 1 秒あたり 5 つの要求が Machine Learning Web サービスに対して行われることを意味します。 接続が 20 個の場合、Stream Analytics ジョブにより 200 ミリ秒で 20,000 イベント、つまり 1 秒あたり 100,000 イベントを処理できます。

1 秒あたり 200,000 イベントを処理するには Stream Analytics ジョブで 40 個のコンカレント接続が必要なため、12 SU となります。 次の図には、Stream Analytics ジョブから Machine Learning Web サービス エンドポイントへの要求を示しています。Learning Web サービスへのコンカレント接続は 6 SU ごとに最大で 20 個あります。

![Machine Learning 関数を使用した Stream Analytics のスケーリング - 2 つのジョブの例](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "Machine Learning 関数を使用した Stream Analytics のスケーリング - 2 つのジョブの例")

一般的に、バッチ サイズが ***B*** で、バッチ サイズ B での Web サービスの待機時間が ***L*** (ミリ秒単位) の場合、***N*** SU での Stream Analytics ジョブのスループットは次のようになります。

![Machine Learning 関数を使用した Stream Analytics のスケーリング - 数式](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "Machine Learning 関数を使用した Stream Analytics のスケーリング - 数式")

Machine Learning Web サービスで、"最大同時呼び出し数" を構成することもできます。 このパラメーターを最大値 (現在は 200) に設定することをお勧めします。

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

1 秒間に 10,000 ツイートの速度でツイートのセンチメント分析を行う Stream Analytics ジョブを作成するために必要な構成を調べてみましょう。

この Stream Analytics ジョブでは、1 SU でトラフィックを処理できるでしょうか。 ジョブは、既定のバッチ サイズである 1,000 を使用して、入力に対応できます。 センチメント分析 Machine Learning Web サービスの既定の待機時間では (バッチ サイズが既定の 1,000 の場合)、待機時間は 1 秒以下です。

通常、Stream Analytics ジョブの**全体的な** (エンド ツー エンドの) 待機時間は数秒です。 この Stream Analytics ジョブ、*特に* Machine Learning 関数呼び出しについて詳しく説明します。 バッチ サイズが 1,000 の場合、10,000 イベントのスループットでは、Web サービスに対する要求は約 10 個になります。 1 SU でも、この入力トラフィックに対応できる十分なコンカレント接続数があります。

イベント入力速度が 100 倍に増加した場合、Stream Analytics ジョブは 1 秒あたり 1,000,000 ツイートを処理する必要があります。 スケールの拡大を実現する 2 つのオプションがあります。

1. バッチ サイズを増やします。
2. 入力ストリームをパーティション分割して、イベントを並列処理します。

最初のオプションでは、ジョブの**待機時間**が増加します。

2 番目のオプションでは、より多くの SU をプロビジョニングして、同時 Machine Learning Web サービス要求の数を増やす必要があります。 SU の値を大きくすると、ジョブの**コスト**が増加します。

各バッチ サイズについて、次の待機時間の測定値を使用するスケーリングを調べてみましょう。

| Latency | バッチ サイズ |
| --- | --- |
| 200 ミリ秒 | 1,000 イベント バッチ以下 |
| 250 ミリ秒 | 5,000 イベント バッチ |
| 300 ミリ秒 | 10,000 イベント バッチ |
| 500 ミリ秒 | 25,000 イベント バッチ |

1. 最初のオプションの使用 (より多くの SU はプロビジョニング**されません**)。 バッチ サイズを **25,000** に増やすことができます。 この方法でバッチ サイズを増やすと、Machine Learning Web サービスへの 20 個のコンカレント接続で 1,000,000 イベントを処理できます (待機時間は 1 回の呼び出しあたり 500 ミリ秒)。 そのため、Machine Learning Web サービス要求に対するセンチメント関数要求により、Stream Analytics ジョブの追加の待機時間は **200 ミリ秒**から **500 ミリ秒**に増加します。 ただし、バッチ サイズを無限に増やすことは**できない**ことに注意してください。Machine Learning Web サービスでは要求のペイロード サイズが 4 MB である必要があり、それより小さい Web サービス要求は操作が 100 秒を超えるとタイムアウトするためです。
1. 2 番目のオプションを使用して、バッチ サイズを 1,000 のままにし、Web サービスの待ち時間が 200 ミリ秒、Web サービスへのコンカレント接続が各 20 個の場合、1,000 * 20 * 5 イベントで 1 秒あたり 100,000 イベントを処理できます。 そのため、1 秒あたり 1,000,000 イベントを処理するには、60 SU が必要です。 最初のオプションよりも Web サービス バッチ要求の数が多くなるため、コストが増加します。

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

通常、Machine Learning 関数に設定するバッチ サイズは、Stream Analytics ジョブの各 "プル" から返されるイベントの数で割り切れません。 その場合、Machine Learning Web サービスは "部分的な" バッチで呼び出されます。 部分的なバッチを使うと、プルの間でのイベントの結合においてジョブ待機時間オーバーヘッドの増加を防ぐことができます。

## <a name="new-function-related-monitoring-metrics"></a>新しい関数関連の監視メトリック
Stream Analytics ジョブの [監視] 領域には、3 つの関数関連のメトリックが追加されました。 次の図に示すように、 **[関数要求]** 、 **[関数イベント]** 、 **[失敗した関数要求]** です。

![Machine Learning 関数を使用した Stream Analytics のスケーリング - 指標](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "Machine Learning 関数を使用した Stream Analytics のスケーリング - 指標")

定義は次のとおりです。

**[関数要求]** :関数要求の数。

**[関数イベント]** :関数要求のイベントの数。

**[FAILED FUNCTION REQUESTS]\(失敗した関数要求\)** :失敗した関数要求の数。

## <a name="key-takeaways"></a>重要なポイント

Machine Learning 関数で Stream Analytics ジョブをスケーリングするには、次の要因を検討します。

1. 入力イベントの速度。
2. 実行中の Stream Analytics ジョブの許容待機時間 (および Machine Learning Web サービス要求のバッチ サイズ)。
3. プロビジョニング済みの Stream Analytics SU および Machine Learning Web サービス要求の数 (関数関連の追加コスト)。

完全にパーティション分割された Stream Analytics クエリを例として使用しました。 もっと複雑なクエリが必要な場合は、[Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)を活用してください。Stream Analytics チームから追加のサポートを受けることができます。

## <a name="next-steps"></a>次の手順
Stream Analytics の詳細については、以下を参照してください。

* [Azure Stream Analytics の使用](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](https://msdn.microsoft.com/library/azure/dn835031.aspx)
