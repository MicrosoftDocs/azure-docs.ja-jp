---
title: Azure Monitor ログ クエリでの結合 | Microsoft Docs
description: この記事には、Azure Monitor ログ クエリでの結合の使用に関するレッスンが含まれています。
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: f2880044e48e59d0d5f005f9772cdd0f807f7f29
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75397822"
---
# <a name="joins-in-azure-monitor-log-queries"></a>Azure Monitor ログ クエリでの結合

> [!NOTE]
> このレッスンを完了する前に、「[Azure Monitor ログ分析の使用を開始する](get-started-portal.md)」と [Azure Monitor ログのクエリ](get-started-queries.md)に関するページの内容を完了しておく必要があります。

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

結合を使用すると、同じクエリで、複数のテーブルのデータを分析できます。 これにより、指定された列の値が照合され、2 つのデータ セットの行がマージされます。


```Kusto
SecurityEvent 
| where EventID == 4624     // sign-in events
| project Computer, Account, TargetLogonId, LogonTime=TimeGenerated
| join kind= inner (
    SecurityEvent 
    | where EventID == 4634     // sign-out events
    | project TargetLogonId, LogoffTime=TimeGenerated
) on TargetLogonId
| extend Duration = LogoffTime-LogonTime
| project-away TargetLogonId1 
| top 10 by Duration desc
```

この例では、最初のデータセットでは、すべてのサインイン イベントがフィルター選択されます。 これは、すべてのサインアウト イベントをフィルター選択する、2 番目のデータセットと結合されます。 予想列は _Computer_、_Account_、_TargetLogonId_、および _TimeGenerated_ です。 データセットは、共有列 _TargetLogonId_ で関連付けられています。 相関関係ごとに 1 つのレコードが出力されます。これにはサインイン時間とサインアウト時間の両方が含まれます。

両方のデータセットに同じ名前の列がある場合、右側のデータセットの列にインデックス番号が追加されます。したがって、この例では、結果の _TargetLogonId_ には左側のテーブルの値が、_TargetLogonId1_ には右側のテーブルの値が示されています。 この場合、2 番目の _TargetLogonId1_ 列は、`project-away` 演算子を使用して削除されました。

> [!NOTE]
> パフォーマンスを向上させるには、`project` 演算子を使用して、結合されたデータセットの関連する列のみを保持します。


次の構文を使用して、2 つのデータセットを結合します。結合されたキーの名前は 2 つのテーブルの間で異なります。
```
Table1
| join ( Table2 ) 
on $left.key1 == $right.key2
```

## <a name="lookup-tables"></a>ルックアップ テーブル
結合の一般的な使用法では、結果をよりわかりやすいものに変換するうえで役立つ、`datatable` を使用した値の静的マッピングが使用されています。 たとえば、セキュリティ イベント データを、各イベント ID に対応するイベント名を使用してわかりやすくするには、次を使用します。

```Kusto
let DimTable = datatable(EventID:int, eventName:string)
  [
    4625, "Account activity",
    4688, "Process action",
    4634, "Account activity",
    4658, "The handle to an object was closed",
    4656, "A handle to an object was requested",
    4690, "An attempt was made to duplicate a handle to an object",
    4663, "An attempt was made to access an object",
    5061, "Cryptographic operation",
    5058, "Key file operation"
  ];
SecurityEvent
| join kind = inner
 DimTable on EventID
| summarize count() by eventName
```

![datatable での結合](media/joins/dim-table.png)

## <a name="join-kinds"></a>結合の種類
_kind_ 引数で結合の種類を指定します。 次の表で説明するように、指定されたテーブルのレコード間で、種類ごとに異なる照合が実行されます。

| 結合の種類 | 説明 |
|:---|:---|
| innerunique | これは既定の結合モードです。 最初に左側のテーブルで一致する列の値が検索され、重複する値が削除されます。  次に一意の値のセットが、右側のテーブルと照合されます。 |
| 内部 (inner) | 結果には、両方のテーブルの一致するレコードのみが含まれます。 |
| leftouter | 結果には、左側のテーブルのすべてのレコードと、右側のテーブルの一致するレコードが含まれます。 一致しない出力プロパティには、null 値が含まれます。  |
| leftanti | 結果には、右側との一致が存在しない左側のレコードが含まれます。 結果テーブルには、左側のテーブルの列のみが含まれます。 |
| leftsemi | 結果には、右側との一致が存在する左側のレコードが含まれます。 結果テーブルには、左側のテーブルの列のみが含まれます。 |


## <a name="best-practices"></a>ベスト プラクティス

最適なパフォーマンスが得られるように、次の点を考慮してください。

- 結合に対して評価する必要があるレコードを減らすには、各テーブルで時間フィルターを使用します。
- 結合の前に入力テーブルの行と列の数を減らすには、`where` と `project` を使用します。
- 一方のテーブルがもう一方よりも常に小さい場合は、それを結合の左側として使用します。


## <a name="next-steps"></a>次のステップ
Azure Monitor ログ クエリの使用に関するその他のレッスンを参照してください。

- [文字列操作](string-operations.md)
- [集計関数](aggregations.md)
- [高度な集計](advanced-aggregations.md)
- [JSON とデータ構造](json-data-structures.md)
- [詳細クエリ書き込み](advanced-query-writing.md)
- [グラフ](charts.md)
