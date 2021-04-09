---
title: Azure Stream Analytics を使用したリアルタイム IoT データ ストリームの処理
description: IoT センサー タグと、Stream Analytics によるデータ ストリームとリアルタイムのデータ処理
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: how-to
ms.date: 11/26/2019
ms.openlocfilehash: a438401ff93c20d8759e6128936c3626bd3de484
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98012684"
---
# <a name="process-real-time-iot-data-streams-with-azure-stream-analytics"></a>Azure Stream Analytics を使用したリアルタイム IoT データ ストリームの処理

この記事では、モノのインターネット (IoT) デバイスからデータを収集するストリーム処理ロジックの作成方法について学習します。 実際のモノのインターネット (IoT) ユース ケースを使用して、迅速で経済的なソリューションを構築する方法を紹介します。

## <a name="prerequisites"></a>前提条件

* 無料の [Azure サブスクリプション](https://azure.microsoft.com/pricing/free-trial/)を作成する。
* [GitHub](https://aka.ms/azure-stream-analytics-get-started-iot) からサンプル クエリとデータ ファイルをダウンロードする。

## <a name="scenario"></a>シナリオ

Contoso は工業オートメーションの領域で活動する会社で、自社の製造工程を完全に自動化しています。 この工場の機械には、リアルタイムでデータのストリームを生成することのできるセンサーがあります。 このシナリオにおいて、生産現場マネージャーは、センサー データからリアルタイムの詳細情報を取得し、パターンを見つけて、それらに対処したいと考えています。 センサー データに対して Stream Analytics クエリ言語 (SAQL) を使用して、データの受信ストリームから興味深いパターンを検出できます。

この例では、データは Texas Instruments 社のセンサー タグ デバイスから生成されます。 データのペイロードは JSON 形式で、次のようになります。

```json
{
    "time": "2016-01-26T20:47:53.0000000",  
    "dspl": "sensorE",  
    "temp": 123,  
    "hmdt": 34  
}  
```

実際のシナリオでは、何百ものこのようなセンサーがストリームとしてイベントを生成することになります。 ゲートウェイ デバイスがコードを実行し、これらのイベントを [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) または [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) にプッシュできれば理想的です。 Stream Analytics ジョブでそれらのイベントを Event Hubs から取り込み、そのストリームに対してリアルタイム分析クエリを実行することになると思われます。 結果はその後、いずれかの[サポートされている出力](stream-analytics-define-outputs.md)に送信することになります。

このガイドでは使いやすさを考えて、現実のセンサー タグ デバイスからキャプチャしたサンプル データ ファイルを用意しています。 このサンプル データに対してクエリを実行し、結果を確認できます。 以降のチュートリアルでは、各自のジョブを入力と出力に関連付け、それらを Azure サービスにデプロイする方法を学習します。

## <a name="create-a-stream-analytics-job"></a>Stream Analytics のジョブの作成

1. [Azure portal](https://portal.azure.com) の左側のナビゲーション メニューで **[+ リソースの作成]** を選択します。 次に、**[分析]** から **[Stream Analytics ジョブ]** を選択します。
   
    ![Create a new Stream Analytics job](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-02.png)

1. 一意のジョブ名を入力し、サブスクリプションがジョブに適したものであることを確認します。 新しいリソース グループを作成するか、サブスクリプションから既存のリソース グループを選択します。

1. ジョブの場所を選択します。 リソース グループとすべてのリソースに同じ場所を使用すると、処理速度が向上し、コストが削減されます。 構成が完了したら、**[作成]** を選択します。
   
    ![新しい Stream Analytics ジョブの作成](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03.png)

## <a name="create-an-azure-stream-analytics-query"></a>Azure Stream Analytics クエリの作成
ジョブを作成した後の次の手順は、クエリを記述することです。 入力または出力をジョブに接続しなくても、サンプル データに対してクエリをテストできます。

GitHub から [HelloWorldASA-InputStream.json](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/GettingStarted/HelloWorldASA-InputStream.json
) をダウンロードします。 次に、Azure portal で Azure Stream Analytics ジョブに移動します。

左側のメニューで、**[ジョブ トポロジ]** の下にある **[クエリ]** を選択します。 次に、**[サンプル入力のアップロード]** を選択します｡ `HelloWorldASA-InputStream.json` ファイルをアップロードし、**[OK]** をクリックします。

![Stream Analytics ダッシュボードのクエリ タイル](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-05.png)

データのプレビューが **入力プレビュー** テーブルに自動的に設定されることに注意してください。

![サンプルの入力データのプレビュー](./media/stream-analytics-get-started-with-iot-devices/input-preview.png)

### <a name="query-archive-your-raw-data"></a>クエリ: 生データのアーカイブ

クエリの最も単純な形式は、すべての入力データを指定された出力にアーカイブするパススルー クエリです。 このクエリは、新しい Azure Stream Analytics ジョブで設定された既定のクエリです。

```sql
SELECT 
    *
INTO
    Output
FROM
    InputStream
```

**[クエリのテスト]** を選択し、**[テスト結果]** の表で結果を確認します。

![Stream Analytics クエリのテスト結果](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-07.png)

### <a name="query-filter-the-data-based-on-a-condition"></a>クエリ: 条件に基づいたデータのフィルター処理

条件に基づいて結果をフィルター処理しましょう。 "sensorA" から取得したイベントの結果のみを表示したいとします。

```sql
SELECT 
    time,
    dspl AS SensorName,
    temp AS Temperature,
    hmdt AS Humidity
INTO
   Output
FROM
    InputStream
WHERE dspl='sensorA'
```

クエリをエディターに貼り付け、**[クエリのテスト]** を選択して結果を確認ます。

![データ ストリームのフィルタリング](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-08.png)

### <a name="query-alert-to-trigger-a-business-workflow"></a>クエリ: ビジネス ワークフローをトリガーするアラート

クエリについて、もう少し詳しく説明します。 あらゆる種類のセンサーを対象に、30 秒間隔で平均温度を監視し、平均温度が 100 度を超える場合にのみ結果を表示するのであれば、

```sql
SELECT 
    System.Timestamp AS OutputTime,
    dspl AS SensorName,
    Avg(temp) AS AvgTemperature
INTO
   Output
FROM
    InputStream TIMESTAMP BY time
GROUP BY TumblingWindow(second,30),dspl
HAVING Avg(temp)>100
```

![30 秒間隔のフィルター クエリ](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-10.png)

ご覧のように、結果に含まれるのは 245 行のみで、平均温度が 100 度を超えるセンサーの名前が一覧表示されます。 このクエリでは、センサー名である **dspl** 別に、30 秒の **タンブリング ウィンドウ** でイベントのストリームをグループ化しています。 一時的なクエリでは、時間の進み方を指定する必要があります。 ここでは、一時的な計算すべてに時間を関連付けるため、**TIMESTAMP BY** 句を使用して **OUTPUTTIME** 列を指定しました。 詳細については、[時間管理](/stream-analytics-query/time-management-azure-stream-analytics)と[ウィンドウ関数](/stream-analytics-query/windowing-azure-stream-analytics)に関するページを参照してください。

### <a name="query-detect-absence-of-events"></a>クエリ: イベントがないことを検出する

入力イベントがないことを検出するためのクエリは、どのように記述すればよいのでしょうか。 センサーが最後にデータを送信してから 5 秒間イベントを送信しなかったタイミングを見つけましょう。

```sql
SELECT 
    t1.time,
    t1.dspl AS SensorName
INTO
   Output
FROM
    InputStream t1 TIMESTAMP BY time
LEFT OUTER JOIN InputStream t2 TIMESTAMP BY time
ON
    t1.dspl=t2.dspl AND
    DATEDIFF(second,t1,t2) BETWEEN 1 and 5
WHERE t2.dspl IS NULL
```

![Detect absence of events](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-11.png)

ここでは、同じデータ ストリームに対して **LEFT OUTER** JOIN を使用しています (自己結合)。 **INNER** JOIN では、一致が見つかった場合にのみ結果が返されます。  これに対して、**LEFT OUTER** JOIN では、結合の左側のイベントに一致するデータがない場合、その右側の列がすべて NULL となった行が返されます。 この手法は、イベントの欠落を見つけるためにきわめて便利です。 詳細については、[JOIN](/stream-analytics-query/join-azure-stream-analytics) に関するページを参照してください。

## <a name="conclusion"></a>まとめ

この記事の目的は、Stream Analytics クエリ言語を使ったさまざまなクエリを記述し、その結果をブラウザーで確認する方法を紹介することです。 ただし、これはほんの導入部に過ぎません。 Stream Analytics は多様な入出力に対応していることに加え、Azure Machine Learning の関数も利用できることから、データ ストリームを分析するうえで強力な手段となっています。 クエリの作成方法の詳細については、[一般的なクエリ パターン](stream-analytics-stream-analytics-query-patterns.md)に関する記事を参照してください。