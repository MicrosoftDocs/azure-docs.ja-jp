---
title: Azure Stream Analytics 用のイベント順序ポリシーの構成
description: この記事では、Stream Analytics でイベント順序設定を構成する方法について説明します。
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: how-to
ms.date: 08/06/2020
ms.openlocfilehash: f7ec6f32b48a93a29210311c7ba6747eb2e2d066
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98014297"
---
# <a name="configuring-event-ordering-policies-for-azure-stream-analytics"></a>Azure Stream Analytics 用のイベント順序ポリシーの構成

この記事では、Azure Stream Analytics で遅延到着と順不同のイベント ポリシーを設定し、使用する方法について説明します。 クエリで [TIMESTAMP BY](/stream-analytics-query/timestamp-by-azure-stream-analytics) 句を使用する場合のみ、これらのポリシーが適用されます。また、これらはクラウド入力ソースにのみ適用されます。

## <a name="event-time-and-arrival-time"></a>イベント時間と到着時間

Stream Analytics ジョブは、*イベント時間* または *到着時間* のいずれかに基づいてイベントを処理できます。 **イベント/アプリケーション時間** は、イベント ペイロードに含まれるタイムスタンプです (イベントが生成された時間)。 **到着時刻** は、イベントが入力ソース (イベント ハブ/IoT ハブ/Blob ストレージ) に到達した時間のタイムスタンプです。 

既定では、Stream Analytics は *到着時間* でイベントを処理しますが、クエリで [TIMESTAMP BY](/stream-analytics-query/timestamp-by-azure-stream-analytics) 節を使用することによって、*イベント時間* でイベントを処理することもできます。 遅延着信と順不同のポリシーは、イベント時間でイベントを処理する場合にのみ適用できます。 これらの設定を構成するときに、自分のシナリオの待機時間と正確性の要件を考慮に入れてください。 

## <a name="what-is-late-arrival-policy"></a>遅延到着ポリシーとは

さまざまな理由によりイベントが遅れて到着することがあります。 たとえば、40 秒遅れて到着したイベントのイベント時間が 00:10:00 である場合、到着時間は 00:10:40 になります。 遅延到着ポリシーを 15 秒に設定した場合、15 秒よりも遅く到着するイベントは、削除されるか (Stream Analytics によっては処理されない)、そのイベント時間が調整されます。 上記の例では、イベントは 40 秒遅れて (ポリシー設定よりも遅れて) 到着したため、イベント時間は遅延到着ポリシーの最大値に合わせて 00:10:25 (到着時間 - 遅延到着ポリシーの値) に調整されます。 既定の遅延到着ポリシーは 5 秒です。

## <a name="what-is-out-of-order-policy"></a>順不同ポリシーとは 

イベントは、順不同で到着する場合もあります。 イベント時間を遅延到着ポリシーに基づいて調整した後、順不同のイベントを自動的に削除または調整することもできます。 このポリシーを 8 秒に設定した場合、順不同であるものの、8 秒以内到着したイベントは、イベント時間で並べ替えられます。 設定した時間を超えたイベントは、削除されるか、順不同ポリシーの最大値に調整されます。 既定の順不同ポリシーは 0 秒です。 

## <a name="adjust-or-drop-events"></a>イベントの調整または削除

イベントが遅れて、または順不同で到着した場合、構成したポリシーに基づいて、そのようなイベントを削除する (Stream Analytics によっては処理されない) か、そのイベント時間を調整することができます。

実行中のこれらのポリシーの例を見てみましょう。
<br> **遅延到着ポリシー:** 15 秒
<br> **順不同ポリシー:** 8 秒

| イベント番号 | イベント時間 | 到着時間 | System.Timestamp | 説明 |
| --- | --- | --- | --- | --- |
| **1** | 00:10:00  | 00:10:40  | 00:10:25  | イベントは許容範囲レベルを超えて遅れて到着しました。 そのためイベント時間は、遅延到着の最大許容値に調整されます。  |
| **2** | 00:10:30 | 00:10:41  | 00:10:30  | イベントは遅れて到着しましたが、許容範囲レベル内です。 そのためイベント時間は調整されません。  |
| **3** | 00:10:42 | 00:10:42 | 00:10:42 | イベントは、時間どおりに到達しました。 調整する必要はありません。  |
| **4** | 00:10:38  | 00:10:43  | 00:10:38 | 正しくない順序であるものの、許容値の 8 秒以内で到着しました。 そのためイベント時間は調整されません。 分析のために、このイベントは先行するイベント番号 4 として考慮されます。  |
| **5** | 00:10:35 | 00:10:45  | 00:10:37 | 正しくない順序で、かつ許容値の 8 秒を超えて到着しました。 そのためイベント時間は、順不同の最大許容値に調整されます。 |

## <a name="can-these-settings-delay-output-of-my-job"></a>これらの設定でジョブの出力を遅らせることができますか 

はい。 既定では、順不同ポリシーはゼロ (00 分 00 秒) に設定されています。 既定値を変更する場合、ジョブの最初の出力はこの値 (以上) で遅延されます。 

入力パーティションの 1 つがイベントを受信しない場合、出力が遅延到着ポリシーの値によって遅延されることを予測する必要があります。 その理由については、以下の InputPartition エラーのセクションを参照してください。 

## <a name="i-see-lateinputevents-messages-in-my-activity-log"></a>イベント ログに LateInputEvents メッセージが表示されます

イベントが遅延して到着し、構成に従って削除または調整されることを通知するために、これらのメッセージが表示されます。 遅延到着ポリシーを適切に構成した場合は、これらのメッセージを無視できます。 

このメッセージの例は以下のとおりです。 <br>
<code>
{"message Time":"2019-02-04 17:11:52Z","error":null,
"message":"First Occurred: 02/04/2019 17:11:48 | Resource Name: ASAjob | Message: Source 'ASAjob' had 24 data errors of kind 'LateInputEvent' between processing times '2019-02-04T17:10:49.7250696Z' and '2019-02-04T17:11:48.7563961Z'. Input event with application timestamp '2019-02-04T17:05:51.6050000' and arrival time '2019-02-04T17:10:44.3090000' was sent later than configured tolerance.","type":"DiagnosticMessage","correlation ID":"49efa148-4asd-4fe0-869d-a40ba4d7ef3b"} 
</code>

## <a name="i-see-inputpartitionnotprogressing-in-my-activity-log"></a>アクティビティ ログに InputPartitionNotProgressing が表示されます

入力ソース (イベント ハブ/IoT ハブ) に、複数のパーティションが含まれている可能性があります。 Azure Stream Analytics は、組み合わされたすべてのパーティションが少なくとも時刻 t1 になった後にのみ、タイムスタンプ t1 の出力を生成します。 たとえば、2 つのパーティションがあるイベント ハブ パーティションから読み取りを行うクエリがあるとします。 一方のパーティション (P1) には、時刻 t1 までのイベントがあります。 もう一方のパーティション (P2) には、時刻 t1 + x までのイベントがあります。 その場合、出力は時刻 t1 までに生成されます。 ただし、明示的な Partition by PartitionId 句がある場合は、両方のパーティションが個別に進行します。 

同じ入力ストリームの複数のパーティションが組み合わされる場合、すべてのパーティションが新しいデータを待機する最大時間が、到着遅延許容期間になります。 イベント ハブに 1 つのパーティションがあるか、IoT ハブが入力を受信しない場合、そのパーティションのタイムラインは、遅延到着許容範囲のしきい値に到達するまで進みません。 これにより、遅延到着許容範囲のしきい値まで出力が遅延されます。 このような場合、次のようなメッセージが表示されることがあります。
<br><code>
{"message Time":"2/3/2019 8:54:16 PM UTC","message":"Input Partition [2] does not have additional data for more than [5] minute(s). Partition will not progress until either events arrive or late arrival threshold is met.","type":"InputPartitionNotProgressing","correlation ID":"2328d411-52c7-4100-ba01-1e860c757fc2"} 
</code><br><br>
このメッセージは、入力内の少なくとも 1 つのパーティションが空で、遅延到着しきい値で出力を遅延させることを通知するものです。 この問題を解決するには、以下のいずれかを実行することをお勧めします。  
1. イベント ハブ/IoT ハブのすべてのパーティションが入力を受け取るようにする。 
2. クエリで Partition by PartitionId 句を使用する。 

## <a name="why-do-i-see-a-delay-of-5-seconds-even-when-my-late-arrival-policy-is-set-to-0"></a>遅延到着ポリシーが 0 に設定されていても 5 秒の遅延があるのはなぜですか?
これは、入力をまったく受け取ったことがない入力パーティションが存在する場合に発生します。 この動作を検証するため、パーティションごとの入力メトリックを確認できます。 

パーティションに、構成されている遅延到着のしきい値を超えるデータがない場合は、イベントの順序に関する考慮事項のセクションで説明しているように、Stream Analytics により、アプリケーションのタイムスタンプが進められます。 このために、推定到着時間が必要です。 パーティションにどのようなデータも存在したことがない場合、Stream Analytics では、到着時刻は *ローカル時刻- 5 秒* であると推定します。 このパーティションには、どのようなデータも存在したことがないため、5 秒の透かしの遅延が表示される可能性があります。  

## <a name="next-steps"></a>次のステップ
* [時間の処理に関する考慮事項](stream-analytics-time-handling.md)
* [Stream Analytics で使用できるメトリック](./stream-analytics-monitoring.md#metrics-available-for-stream-analytics)