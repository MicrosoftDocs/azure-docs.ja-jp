---
title: "イベント ハブ レシーバーを使用した Azure Stream Analytics のデバッグ | Microsoft Docs"
description: "Stream Analytics ジョブで Event Hubs のコンシューマー グループの使用を検討するためのクエリのベスト プラクティス。"
keywords: "イベント ハブの制約, コンシューマー グループ"
services: stream-analytics
documentationcenter: 
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: samacha
ms.openlocfilehash: ede3137de92e251f4ad020bc1ce3f041918242b2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="debug-azure-stream-analytics-with-event-hub-receivers"></a>イベント ハブ レシーバーを使用した Azure Stream Analytics のデバッグ

Azure Stream Analytics で Azure Event Hubs を使用して、ジョブからデータを取り込んだり出力したりできます。 Event Hubs を使用する際のベスト プラクティスは、複数のコンシューマー グループを使用して、ジョブの拡張性を確保することです。 その理由の 1 つとしては、特定の入力に関する Stream Analytics ジョブのリーダーの数が、1 つのコンシューマー グループ内のリーダーの数に影響することが挙げられます。 レシーバーの正確な数は、スケールアウト トポロジ ロジックの内部実装の詳細に基づいて決まります。 レシーバーの数は外部に公開されません。 リーダーの数は、ジョブのスタート時かジョブのアップグレード中に変更される可能性があります。

> [!NOTE]
> ジョブのアップグレード中にリーダーの数が変更されると、一時的な警告が監査ログに書き込まれます。 Stream Analytics ジョブは、これらの一時的な問題から自動的に回復します。

## <a name="number-of-readers-per-partition-exceeds-event-hubs-limit-of-five"></a>パーティションあたりのリーダーの数が Event Hubs の上限 (5 つ) を上回る局面

パーティションあたりのリーダーの数が Event Hubs の上限である 5 つを上回るシナリオとしては、次のものがあります。

* 複数の SELECT ステートメント: **同じ**イベント ハブ入力を参照する複数の SELECT ステートメントを使用する場合、各 SELECT ステートメントによって新しいレシーバーが作成されます。
* UNION: UNION を使用する場合、**同じ**イベント ハブとコンシューマー グループを参照する複数の入力を使用できます。
* SELF JOIN: SELF JOIN 操作を使用する場合、**同じ**イベント ハブを複数回参照できます。

## <a name="solution"></a>解決策

次のベスト プラクティスは、パーティションあたりのリーダーの数が 5 つという Event Hubs の制限を超えるシナリオに対処するのに役立ちます。

### <a name="split-your-query-into-multiple-steps-by-using-a-with-clause"></a>WITH 句を使用してクエリを複数のステップに分割する

WITH 句を使用すると、クエリ内で FROM によって参照できる一時的な名前付き結果セットを指定できます。 WITH 句は、単一の SELECT ステートメントの実行スコープで定義します。

たとえば、次のクエリの代わりに、

```
SELECT foo 
INTO output1
FROM inputEventHub

SELECT bar
INTO output2
FROM inputEventHub 
…
```

次のクエリを使用します。

```
WITH input (
   SELECT * FROM inputEventHub
) as data

SELECT foo
INTO output1
FROM data

SELECT bar
INTO output2
FROM data
…
```

### <a name="ensure-that-inputs-bind-to-different-consumer-groups"></a>入力が異なるコンシューマー グループにバインドされるようにする

3 つ以上の入力が同じ Event Hubs コンシューマー グループに接続されるクエリでは、別のコンシューマー グループを作成します。 そのためには、追加の Stream Analytics 入力を作成する必要があります。


## <a name="get-help"></a>問い合わせ
さらにサポートが必要な場合は、[Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)を参照してください。

## <a name="next-steps"></a>次のステップ
* [Stream Analytics の概要](stream-analytics-introduction.md)
* [Stream Analytics の使用](stream-analytics-real-time-fraud-detection.md)
* [Stream Analytics ジョブのスケール設定](stream-analytics-scale-jobs.md)
* [Stream Analytics クエリ言語リファレンス](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Stream Analytics 管理 REST API リファレンス](https://msdn.microsoft.com/library/azure/dn835031.aspx)
