---
title: Azure Data Explorer で重複データを処理する
description: このトピックでは、Azure Data Explorer を使用するときに重複データを処理するさまざまな方法を説明します。
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/19/2018
ms.openlocfilehash: 8f55b6dfb7b5bc9eda675aca4ed80a66b8a25a7f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59045783"
---
# <a name="handle-duplicate-data-in-azure-data-explorer"></a>Azure Data Explorer で重複データを処理する

クラウドにデータを送信するデバイスでは、データのキャッシュがローカルに保持されます。 データのサイズにもよりますが、ローカルのキャッシュにデータが保存される期間は数日、ときには数か月にも及びます。 不具合が発生したデバイスがキャッシュに保存されたデータを再送信し、分析用データベースにデータの重複が発生した場合でも、分析用データベースに問題が起こらないようにする必要があります。 このトピックでは、そのようなシナリオでデータの重複に対応するためのベスト プラクティスの概要を紹介します。

データの重複に関する最善の対応策は、重複の発生を予防することです。 可能であれば、データ パイプラインの早い段階で問題を修正しましょう。そのようにすれば、データ パイプラインでデータが移動することによるコストを節約できるうえに、システムに取り込まれた重複データへの対応にリソースが消費される事態を回避できます。 これに対して、ソース システムを変更できない状況では、このシナリオに対応する方法が多岐にわたります。

## <a name="understand-the-impact-of-duplicate-data"></a>データの重複の影響を把握する

重複したデータの割合を確認します。 重複したデータの割合が明らかになったら、問題の範囲とビジネスに対する影響を分析し、適切な対応策を選択します。

重複したデータの割合を特定するためのクエリの例は次のとおりです。

```kusto
let _sample = 0.01; // 1% sampling
let _data =
DeviceEventsAll
| where EventDateTime between (datetime('10-01-2018 10:00') .. datetime('10-10-2018 10:00'));
let _totalRecords = toscalar(_data | count);
_data
| where rand()<= _sample
| summarize recordsCount=count() by hash(DeviceId) + hash(EventId) + hash(StationId)  // Use all dimensions that make row unique. Combining hashes can be improved
| summarize duplicateRecords=countif(recordsCount  > 1)
| extend duplicate_percentage = (duplicateRecords / _sample) / _totalRecords  
```

## <a name="solutions-for-handling-duplicate-data"></a>データの重複への対応策

### <a name="solution-1-dont-remove-duplicate-data"></a>対応策 #1:重複したデータを削除しない

ビジネス上の要件と、データの重複をどの程度まで許容できるかを把握しましょう。 データセットによっては、重複したデータが一定の割合存在していても問題ありません。 データの重複があっても大きな影響がなければ、その存在を無視してもよいのです。 重複したデータを削除しないことのメリットは、取り込みのプロセスやクエリのパフォーマンスに追加のオーバーヘッドが発生しないという点にあります。

### <a name="solution-2-handle-duplicate-rows-during-query"></a>対応策 #2:クエリの最中に行の重複を処理する

クエリの最中にフィルター処理を通じてデータ内の行の重複を除去するという方法もあります。 集合関数 [`arg_max()`](/azure/kusto/query/arg-max-aggfunction) を使えば、重複したレコードを除去し、タイムスタンプ (またはそれ以外の列) に基づいて最新のレコードを取得できます。 この方法を使用するメリットは、クエリを実行している間に重複を除去できるため、取り込みが早くなることです。 それに加えて、(重複した分も含めた) レコードすべてが監査とトラブルシューティングに利用できます。 `arg_max` 関数を使用する方法のデメリットとしては、クエリの実行時間が伸びることと、データにクエリを実行するたびに CPU に負荷がかかることが挙げられます。 クエリの対象となるデータの量によっては、この方法だとうまく機能しなかったり、メモリの消費量が多くなったりすることがあります。その場合には、別の方法に切り替える必要があります。

次の例は、一意のレコードを特定する一連の列を対象に、最後に取り込まれたレコードを取得するクエリです。

```kusto
DeviceEventsAll
| where EventDateTime > ago(90d)
| summarize hint.strategy=shuffle arg_max(EventDateTime, *) by DeviceId, EventId, StationId
```

このクエリは、テーブルに対して直接実行する代わりに、関数の中に配置することもできます。

```kusto
.create function DeviceEventsView
{
DeviceEventsAll
| where EventDateTime > ago(90d)
| summarize arg_max(EventDateTime, *) by DeviceId, EventId, StationId
}
```

### <a name="solution-3-filter-duplicates-during-the-ingestion-process"></a>対応策 #3:取り込みプロセスの最中にフィルター処理により重複を除去する

取り込みプロセスの最中にフィルター処理を使って重複を除去するという方法もあります。 Kusto テーブルへの取り込みの際に、重複したデータが無視されます。 データはステージング テーブルに取り込まれ、重複した行が削除された後、別のテーブルにコピーされます。 この方法のメリットは、1 つ前の方法よりもクエリのパフォーマンスが劇的に高まるという点です。 デメリットとしては、取り込みにかかる時間が増えるほか、データのストレージに追加のコストが発生することが挙げられます。

次の例では、この方法を説明します。

1. 同じスキーマのテーブルをもう 1 つ作成します。

    ```kusto
    .create table DeviceEventsUnique (EventDateTime: datetime, DeviceId: int, EventId: int, StationId: int)
    ```

1. 新しいレコードと以前に取り込まれたレコードとをアンチ結合することによって重複したレコードを除去する関数を作成します。

    ```kusto
    .create function RemoveDuplicateDeviceEvents()
    {
    DeviceEventsAll
    | join hint.strategy=broadcast kind = anti
        (
        DeviceEventsUnique
        | where EventDateTime > ago(7d)   // filter the data for certain time frame
        | limit 1000000   //set some limitations (few million records) to avoid choking-up the system during outage recovery

        ) on DeviceId, EventId, StationId
    }
    ```

    > [!NOTE]
    > 結合は CPU バインドな処理であるため、システムにかかる負荷が増大します。

1. `DeviceEventsUnique` テーブルに[更新ポリシー](/azure/kusto/management/update-policy) を設定します。 この更新ポリシーは、新しいデータが `DeviceEventsAll` テーブルに取り込まれる際に有効になります。 新しい[エクステント](/azure/kusto/management/extents-overview)が作成されると、Kusto エンジンにより関数が自動的に実行されます。 この処理は、新しく作成されたデータのみが対象になります。 次のコマンドは、ソース テーブル (`DeviceEventsAll`)、宛先テーブル (`DeviceEventsUnique`)、関数 `RemoveDuplicatesDeviceEvents` を 1 つにまとめて更新ポリシーを作成するものです。

    ```kusto
    .alter table DeviceEventsUnique policy update
    @'[{"IsEnabled": true, "Source": "DeviceEventsAll", "Query": "RemoveDuplicateDeviceEvents()", "IsTransactional": true, "PropagateIngestionProperties": true}]'
    ```

    > [!NOTE]
    > 更新ポリシーを使用すると、取り込みの最中にデータのフィルター処理が発生するほか、その後データの取り込みが 2 回 (`DeviceEventsAll` テーブルに 1 回、`DeviceEventsUnique` テーブルに 1 回) 行われることになるので、取り込みにかかる時間が長くなります。

1. (省略可能) データのコピーがいくつも保存されないように、`DeviceEventsAll` テーブルのデータ保有期間の値をこれまでよりも低いものに設定します。 日数はデータの量と、トラブルシューティングに備えてデータを保持しておきたい期間の長さに応じて選択してください。 保有期間を `0d` 日に設定すると、データがストレージにアップロードされなくなるため、COGS を抑えると共にパフォーマンスを改善できます。

    ```kusto
    .alter-merge table DeviceEventsAll policy retention softdelete = 1d
    ```

## <a name="summary"></a>まとめ

データの重複に対処する方法には、さまざまなものがあります。 アカウントの価格とパフォーマンスを考慮しつつ、各種の選択肢を慎重に検討し、ビジネスに最も適した方法を判断するようにしてください。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure Data Explorer のクエリを記述する](write-queries.md)
