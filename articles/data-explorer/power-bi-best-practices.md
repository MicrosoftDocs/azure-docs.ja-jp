---
title: Power BI を使用して Azure Data Explorer データのクエリと視覚化を行う場合のベスト プラクティス
description: この記事では、Power BI を使用して Azure Data Explorer データのクエリと視覚化を行う場合のベスト プラクティスについて説明します。
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/26/2019
ms.openlocfilehash: e6767c1e03b074f43993e449ca81af951c579090
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/03/2019
ms.locfileid: "71937317"
---
# <a name="best-practices-for-using-power-bi-to-query-and-visualize-azure-data-explorer-data"></a>Power BI を使用して Azure Data Explorer データのクエリと視覚化を行う場合のベスト プラクティス

Azure Data Explorer は、ログと利用統計情報データのための高速で拡張性に優れたデータ探索サービスです。 [Power BI](https://docs.microsoft.com/power-bi/) はビジネス分析ソリューションであり、データを視覚化して、組織全体で結果を共有することができます。 Azure Data Explorer には、Power BI のデータに接続するオプションが 3 つ用意されています。 [組み込みのコネクタ](power-bi-connector.md)を使用するか、[Azure Data Explorer のクエリを Power BI にインポートする](power-bi-imported-query.md)か、[SQL クエリ](power-bi-sql-query.md)を使用します。 この記事では、Power BI を使用した Azure Data Explorer データのクエリと視覚化に関するヒントを提供します。 

## <a name="best-practices-for-using-power-bi"></a>Power BI の使用に関するベスト プラクティス 

テラバイト単位の新しい生データを使用する場合は、Power BI のダッシュボードとレポートを迅速に処理し、最新の状態に保つために、次のガイドラインに従ってください。

* **旅行の荷物は軽く** - レポートに必要なデータのみを Power BI に取り込みます。 詳細な対話型分析を行うには、Kusto クエリ言語を使用したアドホック探索用に最適化された [Azure Data Explorer Web UI](web-query-data.md) を使用します。

* **複合モデル** - [複合モデル](https://docs.microsoft.com/power-bi/desktop-composite-models)を使用して、トップレベルのダッシュボードの集計データとフィルター処理された運用の生データを組み合わせます。 生データを使用するタイミングと、集計ビューを使用するタイミングを明確に定義できます。 

* **Import モードと DirectQuery モード** - 小規模なデータ セットの操作には **Import** モードを使用します。 大規模で頻繁に更新されるデータ セットには **DirectQuery** モードを使用します。 たとえば、ディメンション テーブルは、サイズが小さく、頻繁に変更されないため、**Import** モードを使用して作成します。 予想されるデータ更新の速度に応じて、更新間隔を設定します。 ファクト テーブルは、サイズが大きく、生データが含まれているため、**DirectQuery** モードを使用して作成します。 これらのテーブルを使用し、Power BI の [drillthrough](https://docs.microsoft.com/power-bi/desktop-drillthrough) を使用してフィルター処理されたデータを表示します。

* **並列性** - Azure Data Explorer は、直線的にスケーラブルなデータ プラットフォームです。そのため、次のようにエンドツーエンド フローの並列性を高めることで、ダッシュボード レンダリングのパフォーマンスを向上させることができます。

   * [Power BI で DirectQuery 内のコンカレント接続](https://docs.microsoft.com/power-bi/desktop-directquery-about#maximum-number-of-connections-option-for-directquery)の数を増やします。

   * [弱い整合性を使用して並列性を向上](/azure/kusto/concepts/queryconsistency)させます。 これは、データの鮮度に影響する可能性があります。

* **効率的なスライサー** - [同期スライサー](https://docs.microsoft.com/power-bi/visuals/power-bi-visualization-slicers#sync-and-use-slicers-on-other-pages)を使用すると、準備が整う前にレポートにデータが読み込まれなくなります。 データ セットを構築し、すべてのビジュアルを配置し、すべてのスライサーをマークした後、同期スライサーを選択して必要なデータのみを読み込むことができます。

* **フィルターを使用する** - できるだけ多くの Power BI フィルターを使用し、Azure Data Explorer の検索を関連するデータ シャードに集中させます。

* **効率的なビジュアル** - データに対して最もパフォーマンスの高いビジュアルを選択します。

## <a name="tips-for-using-the-azure-data-explorer-connector-for-power-bi-to-query-data"></a>Power BI 用 Azure Data Explorer コネクタを使用してデータのクエリを実行するためのヒント

次のセクションでは、Power BI に Kusto クエリ言語を使用するためのヒントと手法について説明します。 [Power BI 用 Azure Data Explorer コネクタ](power-bi-connector.md)を使用してデータを視覚化する

### <a name="complex-queries-in-power-bi"></a>Power BI での複雑なクエリ

複雑なクエリは、Kusto でのほうが Power Query よりも簡単に表現できます。 これらは [Kusto 関数](/azure/kusto/query/functions)として実装し、Power BI で呼び出す必要があります。 この方法は、Kusto クエリの `let` ステートメントと共に **DirectQuery** を使用する場合に必要です。 Power BI によって 2 つのクエリが結合され、`join` 演算子と共に `let` ステートメントを使用できないため、構文エラーが発生する可能性があります。 したがって、結合の各部分を Kusto 関数として保存し、Power BI でこれら 2 つの関数を結合できるようにします。

### <a name="how-to-simulate-a-relative-data-time-operator"></a>相対日付/時刻演算子をシミュレートする方法

Power BI には、`ago()` などの "*相対*" 日付/時刻演算子が含まれていません。
`ago()` をシミュレートするには、`DateTime.FixedLocalNow()` および `#duration` Power BI 関数の組み合わせを使用します。

このクエリではなく、`ago()` 演算子を使用します。

```kusto
    StormEvents | where StartTime > (now()-5d)
    StormEvents | where StartTime > ago(5d)
``` 

次の同等のクエリを使用します。

```powerquery-m
let
    Source = Kusto.Contents("help", "Samples", "StormEvents", []),
    #"Filtered Rows" = Table.SelectRows(Source, each [StartTime] > (DateTime.FixedLocalNow()-#duration(5,0,0,0)))
in
    #"Filtered Rows"
```

### <a name="reaching-kusto-query-limits"></a>Kusto クエリの制限に達する 

Kusto クエリからは、既定で最大 500,000 行または 64 MB が返されます。詳細については[クエリの制限](/azure/kusto/concepts/querylimits)に関する記事を参照してください。 これらの既定値をオーバーライドするには、**Azure Data Explorer (Kusto)** 接続ウィンドウで **[詳細オプション]** を使用します。

![[詳細オプション]](media/power-bi-best-practices/advanced-options.png)

これらのオプションでは、クエリと共に [SET ステートメント](/azure/kusto/query/setstatement)が発行され、既定のクエリ制限が変更されます。

  * **[クエリ結果のレコード数を制限する]** で `set truncationmaxrecords` が生成されます。
  * **[クエリ結果のデータ サイズを制限する (バイト単位)]** で `set truncationmaxsize` が生成されます。
  * **[結果セットの切り詰めを無効にする]** で `set notruncation` が生成されます。

### <a name="using-query-parameters"></a>クエリ パラメーターの使用

[クエリ パラメーター](/azure/kusto/query/queryparametersstatement)を使用して、クエリを動的に変更できます。 

#### <a name="using-a-query-parameter-in-the-connection-details"></a>接続の詳細でのクエリ パラメーターの使用

クエリ パラメーターを使用して、クエリの情報をフィルター処理し、クエリのパフォーマンスを最適化します。
 
**[クエリの編集]** ウィンドウの **[ホーム]**  >  **[詳細エディター]**

1. クエリの次のセクションを探します。

    ```powerquery-m
    Source = Kusto.Contents("<Cluster>", "<Database>", "<Query>", [])
    ```
   
   例:

    ```powerquery-m
    Source = Kusto.Contents("Help", "Samples", "StormEvents | where State == 'ALABAMA' | take 100", [])
    ```

1. クエリの関連部分を実際のパラメーターに置き換えます。 クエリを複数の部分に分割し、パラメーターと共にアンパサンド (&) を使用して再度連結します。

   たとえば、前述のクエリでは、`State == 'ALABAMA'` の部分を使用し、`State == '` と `'` のように分割します。それらの間に `State` パラメーターを配置します。
   
    ```kusto
    "StormEvents | where State == '" & State & "' | take 100"
    ```

1. クエリに引用符が含まれている場合は、それらを適切にエンコードします。 たとえば、次のクエリがあります。 

   ```kusto
   "StormEvents | where State == "ALABAMA" | take 100" 
   ```

   これは、次のように 2 つの引用符を使用して**詳細エディター**に表示されます。

   ```kusto
    "StormEvents | where State == ""ALABAMA"" | take 100"
   ```

   次のクエリでは、3 つの引用符に置き換えられます。

   ```kusto
   "StormEvents | where State == """ & State & """ | take 100"
   ```

#### <a name="use-a-query-parameter-in-the-query-steps"></a>クエリ ステップでクエリ パラメーターを使用する

クエリ パラメーターは、それをサポートする任意のクエリ ステップで使用できます。 たとえば、パラメーターの値に基づいて結果をフィルター処理します。

![パラメーターを使用して結果をフィルター処理する](media/power-bi-best-practices/filter-using-parameter.png)

### <a name="dont-use-power-bi-data-refresh-scheduler-to-issue-control-commands-to-kusto"></a>Kusto に制御コマンドを発行するために Power BI データ更新スケジューラを使用しない

Power BI には、データ ソースに対して定期的にクエリを発行できるデータ更新スケジューラが用意されています。 Power BI では、すべてのクエリが読み取り専用であると想定されているため、このメカニズムを使用して Kusto に対して制御コマンドをスケジュールすることはできません。

### <a name="power-bi-can-send-only-short-lt2000-characters-queries-to-kusto"></a>Power BI からは、短い (&lt;2000 文字の) クエリのみを Kusto に送信できます。

Power BI でクエリを実行すると、次のエラーが発生します。 _"DataSource.Error:Web.Contents は ... からコンテンツを取得できませんでした"_ 。このクエリは 2000 文字を超えている可能性があります。 Power BI では、取得する URI の一部としてクエリをエンコードする HTTP GET 要求を発行して、Kusto に対してクエリを実行するために **PowerQuery** を使用します。 そのため、Power BI によって発行された Kusto クエリは、要求 URI の最大長 (2000 文字から小さなオフセットを引いたもの) に制限されます。 回避策として、Kusto で[ストアド関数](/azure/kusto/query/schema-entities/stored-functions)を定義し、Power BI でその関数をクエリに使用する必要があります。

## <a name="next-steps"></a>次の手順

[Power BI 用 Azure Data Explorer コネクタを使用してデータを視覚化する](power-bi-connector.md)




