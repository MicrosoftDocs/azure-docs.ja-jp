---
title: Azure Stream Analytics の JavaScript ユーザー定義集計
description: この記事では、Azure Stream Analytics の JavaScript ユーザー定義集計で高度なクエリ機構を実行する方法を説明します。
author: rodrigoaatmicrosoft
ms.author: rodrigoa
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/28/2017
ms.custom: devx-track-js
ms.openlocfilehash: e9695a268d1c6ec43a737afd36536e4ec2a41a41
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98012480"
---
# <a name="azure-stream-analytics-javascript-user-defined-aggregates"></a>Azure Stream Analytics の JavaScript ユーザー定義集計
 
Azure Stream Analytics は JavaScript で記述されたユーザー定義集計 (UDA) をサポートしています。これを使用すると複雑なステートフルのビジネス ロジックを実装できます。 UDA 内では、状態のデータ構造、状態の蓄積、状態の処分および集計結果の計算を完全にコントロールできます。 この記事では、2 つの異なる JavaScript UDA インターフェイスと UDA の作成手順、および Stream Analytics クエリのウィンドウ ベースの操作を介して UDA を使用する方法について説明します。

## <a name="javascript-user-defined-aggregates"></a>JavaScript ユーザー定義集計

ユーザー定義集計は、タイム ウィンドウの仕様を利用して、そのウィンドウ内のイベントを集計し単一の結果値を生成するのに使用されます。 現在、Stream Analytics がサポートしている UDA インターフェイスには、AccumulateOnly と AccumulateDeaccumulate の 2 種類があります。 どちらの種類の UDA も、タンブリング、ホッピング、スライディング、およびセッションの各ウィンドウで使用できます。 AccumulateDeaccumulate UDA をホッピング、スライディング、およびセッションの各ウィンドウと共に使用する場合は、AccumulateOnly UDA に比べて良いパフォーマンスを発揮します。 使用するアルゴリズムに基づいて 2 種類のいずれかを選択します。

### <a name="accumulateonly-aggregates"></a>AccumulateOnly 集計

AccumulateOnly 集計が蓄積できるのは、その状態にとって新しいイベントだけです。このアルゴリズムでは値の処分はできません。 この集計の種類は、状態の値からのイベント情報の処分を実装できない場合に選択します。 AccumulatOnly 集計用の JavaScript テンプレートを次に示します。

```JavaScript
// Sample UDA which state can only be accumulated.
function main() {
    this.init = function () {
        this.state = 0;
    }

    this.accumulate = function (value, timestamp) {
        this.state += value;
    }

    this.computeResult = function () {
        return this.state;
    }
}
```

### <a name="accumulatedeaccumulate-aggregates"></a>AccumulateDeaccumulate 集計

AccumulateDeaccumulate 集計を使用すると、前に蓄積した値を状態から処分できます。たとえば、イベント値の一覧からキーと値のペアを削除したり、sum 集計の状態から値を減算したりできます。 AccumulateDeaccumulate 集計用の JavaScript テンプレートを次に示します。

```JavaScript
// Sample UDA which state can be accumulated and deaccumulated.
function main() {
    this.init = function () {
        this.state = 0;
    }

    this.accumulate = function (value, timestamp) {
        this.state += value;
    }

    this.deaccumulate = function (value, timestamp) {
        this.state -= value;
    }

    this.deaccumulateState = function (otherState){
        this.state -= otherState.state;
    }

    this.computeResult = function () {
        return this.state;
    }
}
```

## <a name="uda---javascript-function-declaration"></a>UDA - JavaScript 関数の宣言

各 JavaScript UDA は、関数オブジェクトの宣言によって定義されます。 UDA 定義内の主要な要素を次に示します。

### <a name="function-alias"></a>関数のエイリアス

関数のエイリアスは、UDA 識別子です。 Stream Analytics クエリ内で呼び出されると、UDA エイリアスは常にプレフィックス "uda." が付いて プレフィックスは含まれません。

### <a name="function-type"></a>関数の型

UDA については、関数の型は **Javascript UDA** である必要があります。

### <a name="output-type"></a>出力の種類

Stream Analytics ジョブでサポートされる特定の型です。クエリ内で型を処理する場合は "Any" です。

### <a name="function-name"></a>関数名

この関数オブジェクトの名前です。 関数名は UDA エイリアスと一致している必要があります。

### <a name="method---init"></a>メソッド - init()

Init() メソッドは集計の状態を初期化します。 このメソッドはウィンドウの開始時に呼び出されます。

### <a name="method--accumulate"></a>メソッド - accumulate()

accumulate() メソッドは、以前の状態とイベントの現在の値に基づいて UDA の状態を計算します。 このメソッドは、イベントがタイム ウィンドウ (TUMBLINGWINDOW、HOPPINGWINDOW、SLIDINGWINDOW、または SESSIONWINDOW) に入る時に呼び出されます。

### <a name="method--deaccumulate"></a>メソッド - deaccumulate()

deaccumulate() メソッドは、以前の状態とイベントの現在の値に基づいて状態を再計算します。 このメソッドは、イベントが SLIDINGWINDOW または SESSIONWINDOW を離れるときに呼び出されます。

### <a name="method--deaccumulatestate"></a>メソッド – deaccumulateState()

deaccumulateState() メソッドは、以前の状態とホップの状態に基づいて状態を再計算します。 このメソッドは、一連のイベントが HOPPINGWINDOW を離れるときに呼び出されます。

### <a name="method--computeresult"></a>メソッド – computeResult()

computeResult() メソッドは、現在の状態に基づいて集計の結果を返します。 このメソッドは、タイム ウィンドウ (TUMBLINGWINDOW、HOPPINGWINDOW、SLIDINGWINDOW、または SESSIONWINDOW) の最後に呼び出されます。

## <a name="javascript-uda-supported-input-and-output-data-types"></a>JavaScript UDA がサポートする入力と出力のデータ型
JavaScript UDA のデータ型については、[JavaScript UDF の統合](stream-analytics-javascript-user-defined-functions.md)に関するページの「**Stream Analytics と JavaScript の型変換**」のセクションをご覧ください。

## <a name="adding-a-javascript-uda-from-the-azure-portal"></a>Azure Portal から JavaScript UDA を追加する

以下に、ポータルから UDA を作成するプロセスについて説明します。 ここで使用する例では、時間加重平均を計算します。

以下の手順に従って、既存の ASA ジョブに JavaScript UDA を作成しましょう。

1. Azure Portal にログオンし、既存の Stream Analytics ジョブを見つけます。
1. 次に **[ジョブ トポロジ]** で関数リンクをクリックします。
1. **[追加]** アイコンをクリックして新しい関数を追加します。
1. [新しい関数] ビューで、関数の種類として **[JavaScript UDA]** を選択すると、既定の UDA テンプレートがエディターに表示されます。
1. UDA エイリアスとして "TWA" を入力し、関数の実装を次のように変更します。

    ```JavaScript
    // Sample UDA which calculate Time-Weighted Average of incoming values.
    function main() {
        this.init = function () {
            this.totalValue = 0.0;
            this.totalWeight = 0.0;
        }

        this.accumulate = function (value, timestamp) {
            this.totalValue += value.level * value.weight;
            this.totalWeight += value.weight;

        }

        // Uncomment below for AccumulateDeaccumulate implementation
        /*
        this.deaccumulate = function (value, timestamp) {
            this.totalValue -= value.level * value.weight;
            this.totalWeight -= value.weight;
        }

        this.deaccumulateState = function (otherState){
            this.state -= otherState.state;
            this.totalValue -= otherState.totalValue;
            this.totalWeight -= otherState.totalWeight;
        }
        */

        this.computeResult = function () {
            if(this.totalValue == 0) {
                result = 0;
            }
            else {
                result = this.totalValue/this.totalWeight;
            }
            return result;
        }
    }
    ```

1. [保存] ボタンをクリックすると、関数リストに UDA が表示されます。

1. 新しい関数 "TWA" をクリックして関数の定義を確認できます。

## <a name="calling-javascript-uda-in-asa-query"></a>ASA クエリで JavaScript UDA を呼び出す

Azure portal でジョブを開き、クエリを編集して必須プレフィックス "uda." を持つ TWA() 関数を呼び出します。 次に例を示します。

```SQL
WITH value AS
(
    SELECT
    NoiseLevelDB as level,
    DurationSecond as weight
FROM
    [YourInputAlias] TIMESTAMP BY EntryTime
)
SELECT
    System.Timestamp as ts,
    uda.TWA(value) as NoseDoseTWA
FROM value
GROUP BY TumblingWindow(minute, 5)
```

## <a name="testing-query-with-uda"></a>UDA でクエリをテストする

ローカル JSON ファイルを下記の内容で作成して Stream Analytics ジョブにアップロードし、上記のクエリをテストします。

```JSON
[
  {"EntryTime": "2017-06-10T05:01:00-07:00", "NoiseLevelDB": 80, "DurationSecond": 22.0},
  {"EntryTime": "2017-06-10T05:02:00-07:00", "NoiseLevelDB": 81, "DurationSecond": 37.8},
  {"EntryTime": "2017-06-10T05:02:00-07:00", "NoiseLevelDB": 85, "DurationSecond": 26.3},
  {"EntryTime": "2017-06-10T05:03:00-07:00", "NoiseLevelDB": 95, "DurationSecond": 13.7},
  {"EntryTime": "2017-06-10T05:03:00-07:00", "NoiseLevelDB": 88, "DurationSecond": 10.3},
  {"EntryTime": "2017-06-10T05:05:00-07:00", "NoiseLevelDB": 103, "DurationSecond": 5.5},
  {"EntryTime": "2017-06-10T05:06:00-07:00", "NoiseLevelDB": 99, "DurationSecond": 23.0},
  {"EntryTime": "2017-06-10T05:07:00-07:00", "NoiseLevelDB": 108, "DurationSecond": 1.76},
  {"EntryTime": "2017-06-10T05:07:00-07:00", "NoiseLevelDB": 79, "DurationSecond": 17.9},
  {"EntryTime": "2017-06-10T05:08:00-07:00", "NoiseLevelDB": 83, "DurationSecond": 27.1},
  {"EntryTime": "2017-06-10T05:09:00-07:00", "NoiseLevelDB": 91, "DurationSecond": 17.1},
  {"EntryTime": "2017-06-10T05:09:00-07:00", "NoiseLevelDB": 115, "DurationSecond": 7.9},
  {"EntryTime": "2017-06-10T05:09:00-07:00", "NoiseLevelDB": 80, "DurationSecond": 28.3},
  {"EntryTime": "2017-06-10T05:10:00-07:00", "NoiseLevelDB": 55, "DurationSecond": 18.2},
  {"EntryTime": "2017-06-10T05:10:00-07:00", "NoiseLevelDB": 93, "DurationSecond": 25.8},
  {"EntryTime": "2017-06-10T05:11:00-07:00", "NoiseLevelDB": 83, "DurationSecond": 11.4},
  {"EntryTime": "2017-06-10T05:12:00-07:00", "NoiseLevelDB": 89, "DurationSecond": 7.9},
  {"EntryTime": "2017-06-10T05:15:00-07:00", "NoiseLevelDB": 112, "DurationSecond": 3.7},
  {"EntryTime": "2017-06-10T05:15:00-07:00", "NoiseLevelDB": 93, "DurationSecond": 9.7},
  {"EntryTime": "2017-06-10T05:18:00-07:00", "NoiseLevelDB": 96, "DurationSecond": 3.7},
  {"EntryTime": "2017-06-10T05:20:00-07:00", "NoiseLevelDB": 108, "DurationSecond": 0.99},
  {"EntryTime": "2017-06-10T05:20:00-07:00", "NoiseLevelDB": 113, "DurationSecond": 25.1},
  {"EntryTime": "2017-06-10T05:22:00-07:00", "NoiseLevelDB": 110, "DurationSecond": 5.3}
]
```

## <a name="get-help"></a>ヘルプの参照

更にヘルプが必要な場合は、[Azure Stream Analytics に関する Microsoft Q&A 質問ページ](/answers/topics/azure-stream-analytics.html)を参照してください。

## <a name="next-steps"></a>次のステップ

* [Azure Stream Analytics の概要](stream-analytics-introduction.md)
* [Azure Stream Analytics の使用](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics クエリ言語リファレンス](/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics の管理 REST API リファレンス](/rest/api/streamanalytics/)