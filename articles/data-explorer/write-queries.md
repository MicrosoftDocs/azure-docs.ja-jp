---
title: Azure データ エクスプローラーのクエリを記述する
description: このガイドでは、Azure データ エクスプローラーで基本クエリとより高度なクエリを実行する方法について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/07/2019
ms.openlocfilehash: b1a7e64cf6b85b517bc027d6541d63c9be729734
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59274631"
---
# <a name="write-queries-for-azure-data-explorer"></a>Azure データ エクスプローラーのクエリを記述する

この記事では、Azure のデータ エクスプローラーのクエリ言語を使用し、最も一般的な演算子を含む基本的なクエリを実行する方法について説明します。 この言語のより高度な機能についても一部説明します。

## <a name="prerequisites"></a>前提条件

この記事では、2 つの方法のいずれかでクエリを実行できます。

- 学習の補助目的で設定した Azure データ エクスプローラー *ヘルプ クラスター*で。
    Azure Active Directory に登録されている組織の電子メール アカウントで[クラスターにサインインします](https://dataexplorer.azure.com/clusters/help/databases/samples)。

- StormEvents サンプル データが含まれる独自のクラスターで。 詳細については、「[クイック スタート:Azure Data Explorer クラスターとデータベースを作成する](create-cluster-database-portal.md)」、および「[Azure のデータ エクスプローラーにサンプル データを取り込む](ingest-sample-data.md)」を参照してください。

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="overview-of-the-query-language"></a>クエリ言語の概要

Azure のデータ エクスプローラーのクエリ言語は、データを処理し、結果を返す、読み取り専用の要求です。 要求は、構文を読みやすく、作りやすく、自動化しやすくするように設計されたデータフロー モデルを利用してプレーンテキストで述べられます。 データベース、テーブル、列など、SQL に似た階層で整理されたスキーマ エンティティがクエリで使用されます。

クエリは一連のクエリ ステートメントで構成されます。ステートメントはセミコロン (`;`) で区切られ、少なくとも 1 つのステートメントを表形式にします。表形式のステートメントでは、生成されたデータが列と行からなる表のようなメッシュで整理されます。 クエリの表形式ステートメントからクエリの結果が生成されます。

表形式ステートメントの構文では、ある表形式クエリ演算子から別の表形式クエリ演算子に表形式データが流れます。データ源 (データベースのテーブルやデータを生成する演算子など) から始まり、パイプ (`|`) 区切り記号でまとめられている一連のデータ変換演算子を通過します。

たとえば、次のクエリにはステートメントが 1 つありますが、それは表形式ステートメントです。 このステートメントは `StormEvents` という名前のテーブルの参照から始まっています (このテーブルをホストするデータベースはここでは接続情報の一部として暗に示されるだけです)。 そのテーブルのデータ (行) が `StartTime` 列の値でフィルター処理され、さらに `State` 列の値でフィルター処理されます。 "最後まで残った" 行の数がクエリにより返されます。

**\[**[**クリックするとクエリが実行されます**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVWws1VISSxJLQGyNYwMDMx1DQ11DQw1FRLzUpBU2aArMgIpQjGvJFXB1lZByc3HP8jTxVFJQQEkm5xfmlcCAHoR9euCAAAA)**\]**

```Kusto
StormEvents
| where StartTime >= datetime(2007-11-01) and StartTime < datetime(2007-12-01)
| where State == "FLORIDA"  
| count
```

このケースでは、結果は次のようになります。

|Count|
|-----|
|   23|
| |

詳細については、「[Query language reference](https://aka.ms/kustolangref)」 (クエリ言語参照) を参照してください。

## <a name="most-common-operators"></a>最も一般的な演算子

このセクションで取り上げる演算子は、Azure データ エクスプローラーのクエリを理解するための基本となります。 記述するクエリの多くには、ここで説明する演算子がいくつか含まれます。

ヘルプ クラスターでクエリを実行するには、各クエリの上にある **[クリックするとクエリが実行されます]** を選択してください。

独自のクラスターでクエリを実行するには:

1. 各クエリを Web ベース クエリ アプリケーションをコピーし、クエリを選択するか、クエリにカーソルを置きます。

1. アプリケーションの上部にある **[実行]** を選択します。

### <a name="count"></a>count

[**count**](https://docs.microsoft.com/azure/kusto/query/countoperator):テーブルの行数が返されます。

次のクエリでは、StormEvents テーブルの行数が返されます。

**\[**[**クリックするとクエリが実行されます**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVqhRSM4vzSsBALU2eHsTAAAA)**\]**

```Kusto
StormEvents | count
```

### <a name="take"></a>take

[**take**](https://docs.microsoft.com/azure/kusto/query/takeoperator):最大で指定のデータ行数まで返されます。

次のクエリでは、StormEvents テーブルから 5 行が返されます。 *limit* というキーワードは *take* のエイリアスです。

**\[**[**クリックするとクエリが実行されます**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVqhRKEnMTlUwBQDEz2b8FAAAAA%3d%3d)**\]**

```Kusto
StormEvents | take 5
```

> [!TIP]
> ソース データが整理されていない限り、どのレコードが返されるかはわかりません。

### <a name="project"></a>project

[**project**](https://docs.microsoft.com/azure/kusto/query/projectoperator):列のサブセットが選択されます。

次のクエリでは、特定の列セットが返されます。

**\[**[**クリックするとクエリが実行されます**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUShJzE5VMAWxCorys1KTSxSCSxKLSkIyc1N1FFzzUiAMoFgJiA%2fSFlJZAGS6JOYmpqcGFOUXpBaVVAKlCjKL81NS%2fRKLihJLMstSAY%2buIINnAAAA)**\]**

```Kusto
StormEvents
| take 5
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

### <a name="where"></a>各値の説明:

[**where**](https://docs.microsoft.com/azure/kusto/query/whereoperator):述語の条件を満たす行のサブセットにテーブルをフィルター処理します。

次のクエリでは、`EventType` と `State` でデータが絞り込まれます。

**\[**[**クリックするとクエリが実行されます**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAEWMPQvCMBCGd8H%2fcFuWro4dBOvHkgoJOB%2fm0KjJhetRKfjjNe3g9n49r1OW1I2UdVivPvC%2bkxDM3k%2bFoG3B7F%2fMwQDmAE5Rl%2fCydceTPfjemsopPgk2VRXhB121TkV9TNRAl8MiZrz53zeww4Q3OgsXEp1%2bVYkDB7IoghpH%2bgI9OH8WnwAAAA%3d%3d)**\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| take 5
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

### <a name="sort"></a>sort

[**sort**](https://docs.microsoft.com/azure/kusto/query/sortoperator):入力テーブルの行を 1 つ以上の列の順序で並べ替えます。

次のクエリでは、`DamageProperty` の降順でデータが並べ替えられます。

**\[**[**クリックするとクエリが実行されます**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2NPQvCMBCGd8H%2fcFuXrI4dBOvHEoUGnM%2fm0KjphctRKfjjNe0guL0fvM%2fbKktsBuo1LxdveN1ICCbvxkRQ11Btn8y%2bAuw9tIo6h%2bd1uz%2fYnTvaquwyi8JlhA1GvNJJOJHoCJ5yV2rFB8GqqCR8p04LSdSFSAaa3s9iopvfu%2fnDfasUMnuyKIIaBvoAtvGMsb4AAAA%3d)**\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| sort by DamageProperty desc
| take 5
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

> [!NOTE]
> 演算の順序が重要です。 `sort by` の前に `take 5` を置いてみてください。 違う結果が出ますか。

### <a name="top"></a>top

[**top**](https://docs.microsoft.com/azure/kusto/query/topoperator):指定の列で並べ替えられたレコードが先頭から  *N*  個まで返されます。

次のクエリでは上と同じ結果が返されますが、演算子が 1 つ少なくなっています。

**\[**[**クリックするとクエリが実行されます**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2NOwvCMBSFd8H%2fcLcsWR07CNbHkgoJOMfmohGTG24vlYA%2fXtsOgtt5cL5jhTi1I2YZ1qs3vO7ICLN3tSA0Daj9kygo8DmAFS9LeNna48kcXGfUtBMqsIFrhZ1P%2foZnpoIsFQIO%2fdQXpgf2MgFYXEyooc1hETNU%2f071H%2bRblThQQOOZvcQRP1rSng21AAAA)**\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| top 5 by DamageProperty desc
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

### <a name="extend"></a>extend

[**extend**](https://docs.microsoft.com/azure/kusto/query/extendoperator):派生列が計算されます。

次のクエリでは、すべての行の値を計算することで、新しい列が作成されます。

**\[**[**クリックするとクエリが実行されます**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2OvQ7CMAyEdyTewVuWMDJ2QGr5WQJSKzGHxoIiEkeuKVTi4WmooBKbfXeffaUQ%2b6LDIO189oLHBRnhs1d9RMgyUOsbkVNgg4NSrIzicVVud2ZT7Y1KnFCEJZx6yK23ZzwwRWTpwWFbJx%2bfggOf39lKQwEyKIKrGo%2bwSEdZ0pyCkemKtUyi%2fib1j9ZjDz311H9%2fBys2LTk0lhPT4RvwA3pn6AAAAA%3d%3d)**\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| top 5 by DamageProperty desc
| extend Duration = EndTime - StartTime
| project StartTime, EndTime, Duration, State, EventType, DamageProperty, EpisodeNarrative
```

式には一般的な演算子 (+、-、*、/、%) をすべて含めることができます。また、各種の便利な関数を呼び出すことができます。

### <a name="summarize"></a>summarize

[**summarize**](https://docs.microsoft.com/azure/kusto/query/summarizeoperator):行のグループが集計されます。

次のクエリでは、`State` を基準にイベント数が返されます。

**\[**[**クリックするとクエリが実行されます**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIBYnFJ%2beX5pUo2CqAaQ1NhaRKheCSxJJUAB%2fedDI3AAAA)**\]**

```Kusto
StormEvents
| summarize event_count = count() by State
```

**summarize** 演算子では、同じ値を持つ行が **by** 句でグループ化され、集計関数 (**count** など) が使用され、各グループが 1 つの行に組み合わされます。 そのため、このケースでは、州ごとに行が 1 つ与えられ、その州の行数用に列が 1 つ与えられます。

集計関数にはさまざまなものがあります。1 つの **summarize** 演算子でいくつかの集計関数を使用し、複数の列を計算できます。 たとえば、各州で発生した嵐の数とその一意の番号を取得し、**top** を使用して嵐の影響を最も受けた州を取得できます。

**\[**[**クリックするとクエリが実行されます**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlUIBkk455fmlSjYKiSDaA1NHYWQyoJU%2fzSwXDFQPAUiAdYPktJUSKoE6kwsSQUZVpJfoGAKEYGblZJanAwAgbFb73QAAAA%3d)**\]**

```Kusto
StormEvents
| summarize StormCount = count(), TypeOfStorms = dcount(EventType) by State
| top 5 by StormCount desc
```

**summarize** 演算の結果には以下が含まれます。

- **by** で指定される各列

- 計算された式ごとに 1 つの列

- by 値の組み合わせごとに 1 つの行

### <a name="render"></a>render

[**render**](https://docs.microsoft.com/azure/kusto/query/renderoperator):結果がグラフィックで表現され、出力されます。

次のクエリでは、縦棒グラフが表示されます。

**\[**[**クリックするとクエリが実行されます**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWMsQ7CQAxDdyT%2bIWMrdSgbSxmQ2Nj6Aei4Ru0hkqA0VwTi49uUBRZL9rPdmiidJmQbt5sPjJkoaHojoGeXKJmtWbUoK6DUQQNh6osj9onPwUq4vqC1YLjORc2Dpef2OaD%2bPcEBdvu6dvZQuWG077b6LTlV5A4VotwzcRyC2gxU6ktSqQAAAA%3d%3d)**\]**

```Kusto
StormEvents
| summarize event_count=count(), mid = avg(BeginLat) by State
| sort by mid
| where event_count > 1800
| project State, event_count
| render columnchart
```

次のクエリでは、簡単な時間グラフが表示されます。

**\[**[**クリックするとクエリが実行されます**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIBYnFJ%2beX5pXYgkkNTYWkSoWkzDyN4JLEopKQzNxUHQXDFE2QtqLUvJTUIoUSoFhyBlASAAyXWQJWAAAA)**\]**

```Kusto
StormEvents
| summarize event_count=count() by bin(StartTime, 1d)
| render timechart
```

次のクエリでは、「StartTime % 1d」でイベントが数えられ、それが時間にまとめられ、時間グラフが表示されます。

**\[**[**クリックするとクエリが実行されます**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEADWNQQqDMBRE90LvMBtBwY0HcNkT2L2k8UuEJh9%2bfqSWHt4k4GZghpk3s7L450FB46P5g75KYYXjJJiwfZilm9WIvnZPaDGuGDC6vnRj8t7I%2fiNQ2S%2bWU9CpatfjfVZKLbLo7WGiLZnkGxJoxlqX%2bRf81ZbyiAAAAA%3d%3d)**\]**

```Kusto
StormEvents
| extend hour = floor(StartTime % 1d , 1h)
| summarize event_count=count() by hour
| sort by hour asc
| render timechart
```

次のクエリでは、連日の嵐が時間グラフで比較されます。

**\[**[**クリックするとクエリが実行されます**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEACWPSwvCMBCE74L%2fYSgIFXrpD%2bihaKzxkUBTXyeputKCbSCmvvDHm9TL7gwzsN8qq03DHtTa%2b3DwBb0stRdUujMJrjetTQhlS2OLuiGMEF8QIa7GvvusyJBPLaFuEQbZZjWDnGHN9nwigyhYp1wwt7c8z7jgqZM7riZSKC6cFjIv5pimS1n4SLAdFixX7OCMzFkmRdAfundNU5r6QyAPejzrrrVJP8MxTu8eN%2fqT%2bL5xL5CBdcjnyrH%2fALPTSKnkAAAA)**\]**

```Kusto
StormEvents
| extend hour= floor( StartTime % 1d , 1h)
| where State in ("GULF OF MEXICO","MAINE","VIRGINIA","WISCONSIN","NORTH DAKOTA","NEW JERSEY","OREGON")
| summarize event_count=count() by hour, State
| render timechart
```

> [!NOTE]
> **render** 演算子は、エンジンに含まれるというより、むしろクライアント側の機能です。 使いやすいように言語に統合されています。 この Web アプリケーションはオプションとして棒グラフ、円柱グラフ、円グラフ、時間グラフ、折れ線グラフに対応しています。 

## <a name="scalar-operators"></a>スカラー演算子

このセクションでは、最も重要なスカラー演算子をいくつか取り上げます。

### <a name="bin"></a>bin()

[**bin()**](https://docs.microsoft.com/azure/kusto/query/binfunction):値を切り捨てて、指定された bin サイズの倍数である整数にします。

次のクエリでは、1 日のバケット サイズで数が算出されます。

**\[**[**クリックするとクエリが実行されます**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVWwU0hJLEktATI1jAwMzHUNjHQNTTQVEvNSkBTZYCoyMtQEGVdcmpubWJRZlaqQCrIiPjm%2fNK9EwVYBTGtoKiRVKiRl5mnAjdJRMEzRBABIhjnmkwAAAA%3d%3d)**\]**

```Kusto
StormEvents
| where StartTime > datetime(2007-02-14) and StartTime < datetime(2007-02-21)
| summarize event_count = count() by bin(StartTime, 1d)
```

### <a name="case"></a>case()

[**case()**](https://docs.microsoft.com/azure/kusto/query/casefunction):述語の一覧が評価され、述語が満たされた最初の結果式か最後の **else** 式が返されます。 この演算子を使用し、データを分類またはグループ化できます。

次のクエリでは、新しい列 `deaths_bucket` が返され、死亡者が数でグループ化されます。

**\[**[**クリックするとクエリが実行されます**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAGWOwQrCQAxE74X%2bQ9hTCwX14FFBaK9e%2bgGS7gZdbFrYZEXFj7dbqgfNbfJmhml1DNzcaFDJsxdIZMbgnwSOUC8Cu%2fQq6lnUPpDVEroHtIpKKUB3pcEt7lMX7ZV0ClkUgiLPYLqlaQ%2fbdQWmx3AmU%2f2gTUJMzkf%2bYwkJY99%2fiDmuDqac545Bv3MAxb4Bic1Oy88AAAA%3d)**\]**

```Kusto
StormEvents
| summarize deaths = sum(DeathsDirect) by State
| extend deaths_bucket = case (
    deaths > 50, "large",
    deaths > 10, "medium",
    deaths > 0, "small",
    "N/A")
| sort by State asc
```

### <a name="extract"></a>extract()

[**extract()**](https://docs.microsoft.com/azure/kusto/query/extractfunction):テキスト文字列から正規表現との一致を取得します。

次のクエリでは、トレースから特定の属性値が抽出されます。

**\[**[**クリックするとクエリが実行されます**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAE2OwQrCMBBE74X%2bw9BTojHYagSVHJRevXkrHqJdpVBbSVew4McbFYungeXtvKmJsetzxw4WZQh2x5og9t6daIWOfdVcJIpkY1OFrc0U8rt3XLWNTbOZnhultU4UfoD5A4zRmVkovInDOo6%2bojh6gh5MTTmQwR0uQckiGb5FMZ0s9WEsQ3uo%2fixSccT9jdqz8ORqKTECV1cSaSdfq2k6L8oAAAA%3d)**\]**

```Kusto
let MyData = datatable (Trace: string) ["A=1, B=2, Duration=123.45,...", "A=1, B=5, Duration=55.256, ..."];
MyData
| extend Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s)
```

このクエリでは **let** ステートメントが使用されますが、このステートメントにより名前 (この場合、`MyData`) が式にバインドされます。 **let** ステートメントが現れる残りのスコープについては (グローバル スコープまたは関数本体のスコープ)、この名前を使用してそのバインドされている値を参照できます。

### <a name="parsejson"></a>parse_json()

[**parse_json()**](https://docs.microsoft.com/azure/kusto/query/parsejsonfunction):文字列が JSON 値として解釈され、値が dynamic として返されます。 複合 JSON オブジェクトの複数の要素を抽出する必要がある場合、**extractjson()** 関数の使用より優れています。

次のクエリでは、配列から JSON 要素が抽出されます。

**\[**[**クリックするとクエリが実行されます**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAHWPQQuCQBCF74L%2fYdmLBSJ6EGKjU17r1E0kJh1C2XZlHc0w%2f3ur1s1O896bB%2fONRGKnVwIE7MAKOwhuEtnmYiBHwRoypbpvXSf1Bl60BqjUiot04B3IFrmIol0Q%2bpPLdauIi3iyj9KWojCcNfRWx7NuqEiw48KaMRu9bO86y3HXeTPsCVXBzvg8amlpajANXqtGq4VmO5VqoyvM6dsKfkhpmAUzkf9nM9OtLi3reg79ar788AEVX8GkOAEAAA%3d%3d)**\]**

```Kusto
let MyData = datatable (Trace: string)
['{"duration":[{"value":118.0,"valcount":5.0,"min":100.0,"max":150.0,"stdDev":0.0}]}'];
MyData
| extend NewCol = parse_json(Trace)
| project NewCol.duration[0].value, NewCol.duration[0].valcount, NewCol.duration[0].min, NewCol.duration[0].max, NewCol.duration[0].stdDev
```

次のクエリでは、JSON 要素が抽出されます。

**\[**[**クリックするとクエリが実行されます**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAE2OwQqCQBCG74LvsOzFBBE9CLHRKa916hYRkw6RbLuyO5pRvXvrGtZpvn9m4P8kEts%2bSiBga1a7QXCWyBZ7AxUKZslc1SVmh%2bjJe5AdcpHnyzRLxlTpThEXxRhvV%2bVOWeYZBseFZ0t1iT0XLryj4yoMprIweDEcCFXNdnjfaOnaWzAWT43VamqPx6fW6AYr%2bn6l3iH5S95hXjiLH8Mw82TxAQvJEB%2fsAAAA)**\]**

```Kusto
let MyData = datatable (Trace: string) ['{"value":118.0,"valcount":5.0,"min":100.0,"max":150.0,"stdDev":0.0}'];
MyData
| extend NewCol = parse_json(Trace)
| project NewCol.value, NewCol.valcount, NewCol.min, NewCol.max, NewCol.stdDev
```

次のクエリでは、dynamic データ型の JSON 要素が抽出されます。

**\[**[**クリックするとクエリが実行されます**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAD2NMQvCMBBG90D%2bw5GphVLSoSARt65ubuJwJjdU0lZiWlrU%2f25MotO9x8H7LHk4bh16hAOYcDxeLUFxcqhJgdlGHHpdcnbOWDzFgnYmoZpmV8tK6GkePTmh2q8N%2fRg%2bUkbGNXAb%2beFNR4tQQd7lZc9ZGuXsBXc33Uh7iJN1jFdZcvunIf5HXCvOEqf2BwXmDCnKAAAA)**\]**

```Kusto
let MyData = datatable (Trace: dynamic)
[dynamic({"value":118.0,"counter":5.0,"min":100.0,"max":150.0,"stdDev":0.0})];
MyData
| project Trace.value, Trace.counter, Trace.min, Trace.max, Trace.stdDev
```

### <a name="ago"></a>ago()

[**ago()**](https://docs.microsoft.com/azure/kusto/query/agofunction):現在の UTC 時刻から指定された期間を減算します。

次のクエリでは、過去 12 時間のデータが返されます。

**\[**[**クリックするとクエリが実行されます**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA1WOQQ6CQAxF9yTc4S8hQcmQuNSNR4ALTKQyJDAlnSIuPLwzJGrctM3v+7+t684R7qMEhW6MafQUMJAnsUoIdl4mQm/VVrC+h0Z6shFOINZAIc/qOql24KIEL8nIAuWYohC6sfQB9yjtPtPA8SrhmGeLjF7RjTO1Gu+cIdYPVHjeisOpLyukKTbjYml5piuvXknwIU1lGlPm2Qvzg55L+u+b9udIyOZI6LfHZf/YNK58Ay2HrbAEAQAA)**\]**

```Kusto
//The first two lines generate sample data, and the last line uses
//the ago() operator to get records for last 12 hours.
print TimeStamp= range(now(-5d), now(), 1h), SomeCounter = range(1,121)
| mv-expand TimeStamp, SomeCounter
| where TimeStamp > ago(12h)
```

### <a name="startofweek"></a>startofweek()

[**startofweek()**](https://docs.microsoft.com/azure/kusto/query/startofweekfunction):日付を含む週の始まりが返されます。オフセットが指定されている場合、そのオフセットでシフトされます。

次のクエリでは、さまざまなオフセットで週の始まりが返されます。

**\[**[**クリックするとクエリが実行されます**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEACtKzEtPVchPSytOLVFIK8rPVdA1VCjJVzBUKC5JLVAw5OWqUSgoys9KTS5RKE9NzQ4uSSwqUbAFygLp%2fDSQkEZefrmGpg7UEE0dCA0AdE3lv1kAAAA%3d)**\]**

```Kusto
range offset from -1 to 1 step 1
| project weekStart = startofweek(now(), offset),offset
```

このクエリでは **range** 演算子が使用されます。この演算子では、値の単一列テーブルが生成されます。 [**startofday()**](https://docs.microsoft.com/azure/kusto/query/startofdayfunction)、[**startofweek()**](https://docs.microsoft.com/azure/kusto/query/startofweekfunction)、[**startofyear()**](https://docs.microsoft.com/azure/kusto/query/startofyearfunction))、[**startofmonth()**](https://docs.microsoft.com/azure/kusto/query/startofmonthfunction)、[**endofday()**](https://docs.microsoft.com/azure/kusto/query/endofdayfunction)、[**endofweek()**](https://docs.microsoft.com/azure/kusto/query/endofweekfunction)、[**endofmonth()**](https://docs.microsoft.com/azure/kusto/query/endofmonthfunction)、[**endofyear()**](https://docs.microsoft.com/azure/kusto/query/endofyearfunction) も参照してください。

### <a name="between"></a>between()

[**between()**](https://docs.microsoft.com/azure/kusto/query/betweenoperator):両端を含む範囲内に入っている値が一致として出力されます。

次のクエリでは、指定のデータ範囲でデータが絞り込まれます。

**\[**[**クリックするとクエリが実行されます**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVVISi0pT03NU9BISSxJLQGKaBgZGJjrApGRuaaCnp4ChrixgaYmyKTk%2fNK8EgBluyagXgAAAA%3d%3d)**\]**

```Kusto
StormEvents
| where StartTime between (datetime(2007-07-27) .. datetime(2007-07-30))
| count
```

次のクエリでも指定のデータ範囲でデータが絞り込まれますが、少しばかり変化を加え、開始日から 3 日間 (`3d`) と指定しています。

**\[**[**クリックするとクエリが実行されます**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVVISi0pT03NU9BISSxJLQGKaBgZGJjrApGRuaaCnp6CcYomSF9yfmleCQCGAqjRTAAAAA%3d%3d)**\]**

```Kusto
StormEvents
| where StartTime between (datetime(2007-07-27) .. 3d)
| count
```

## <a name="tabular-operators"></a>テーブル演算子

Kusto にはさまざまなテーブル演算子があります。この記事の他のセクションでそのいくつかを取り上げます。 ここでは **parse** を取り上げます。 

### <a name="parse"></a>parse

[**parse**](https://docs.microsoft.com/azure/kusto/query/parseoperator):文字列式が評価され、その値が 1 つまたは複数の計算列に解析されます。 解析には、simple (既定)、regex、relaxed という 3 つの方法があります。

次のクエリでは、既定の simple 解析を利用し、トレースが解析され、関連値が抽出されます。 この式 (StringConstant と呼ばれる) は正規文字列値であり、一致は厳密です。拡張列が必須の型に一致する必要があります。

**\[**[**クリックするとクエリが実行されます**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAN2UTU%2fDMAyG75X6H6xcxlCkpRlsUNQjN6gQ2wnEoevMFsiaKk2HJvHjabqvlI91l11QLrH12vETW5Zo4H411kmKEME0MdWZSISz2yVmpvaHhdEim3V979n3OrU%2fhFgZ8boaSZHiI0pMiipEY6FKnWKcLDB6EDlKkeEoneO0lKgpGGUSWYcUER9SKOw1LhcT1BHvU5AqfR%2bLKpbxXjDscRYMgF2FFyxkwRMFvX7ngCLXuBSqLO5%2bT9S%2ftrJuh54OI7g8iMFaMdhxGOy0GJz9i25w%2fjdG0IoRHNWNNe1ph2pwEKNlqI7HsEPley83vrfZCL73CXmiq%2fr32wA%2bhJnDOZAGEQHXBNIEIq4VSpXNbAIXkbjAO8UOmuz4bWoXlrhWWO0vqyA2%2bAcw2f7B1rORd60calat3jA1TRbq1A6NxsC%2bLdCoCuj3p74AKTs4pmcFAAA%3d)**\]**

```Kusto
let MyTrace = datatable (EventTrace:string)
[
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00Z, releaseTime=02/17/2016 08:40:00Z, previousLockTime=02/17/2016 08:39:00Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:00Z)'
];
MyTrace
| parse EventTrace with * "resourceName=" resourceName ", totalSlices=" totalSlices:long * "sliceNumber=" sliceNumber:long * "lockTime=" lockTime ", releaseTime=" releaseTime:date "," * "previousLockTime=" previouLockTime:date ")" *  
| project resourceName ,totalSlices , sliceNumber , lockTime , releaseTime , previouLockTime
```

次のクエリでは、`kind = regex` を利用し、トレースが解析され、関連値が抽出されます。 StringConstant は正規表現にすることができます。

**\[**[**クリックするとクエリが実行されます**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAN2UQU%2fCQBCF7036HyZ7gWKRbVHQmgY9eNPGCCcoh9KOsLK0ZLtFMf54l6LQBgUuXEyTTbP7pt3vvclwlPC47IkgRHAhCqR6Rhyher%2fAWOb7TioFi8eGrg10rZLvO%2bAlkr0su5yF%2bIwcg1SVCEyTTIToBTN0n9gcOYuxG04wyjgKE2QiA56XpK7dNiFdvXrZbITCtZsm8CSc9piqpXbDajdsarWAXjkX1KFW3wSx%2fs8exVzggiVZ%2bvD7h5rXK5lRMU%2bHYV3uxaAHMehxGPS0GDb9F2nY9t8Y1kEM66g01rSnbarWXowDTXU8xqqpdG14o2vfE0HXPmEeCHX%2fKYsjNR8EjvEdtqMB3picAKme1zrGIKh%2f3NX7w5pLoEgLt6SM56c1PzpTq6oqYpIitMOTeAxAlKb6c3Wjs3GBbAzJJUV8UjQjP91BJztuOGryKbHvGwQgxxbJK4ayTFKKBbahQCkA2DX7C29veJJmBQAA)**\]**

```Kusto
let MyTrace = datatable (EventTrace:string)
[
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00Z, releaseTime=02/17/2016 08:40:00Z, previousLockTime=02/17/2016 08:39:00Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:00Z)'
];
MyTrace
| parse kind = regex EventTrace with "(.*?)[a-zA-Z]*=" resourceName @", totalSlices=\s*\d+\s*.*?sliceNumber=" sliceNumber:long  ".*?(previous)?lockTime=" lockTime ".*?releaseTime=" releaseTime ".*?previousLockTime=" previousLockTime:date "\\)"  
| project resourceName , sliceNumber , lockTime , releaseTime , previousLockTime
```

次のクエリでは、`kind = relaxed` を利用し、トレースが解析され、関連値が抽出されます。 StringConstant は正規文字列値であり、一致は厳密ではありません。拡張列は必須の型に部分的に一致すれば一致と見なされます。

**\[**[**クリックするとクエリが実行されます**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAN2US0%2fCQBDH7036HSZ7wZpN2BYFrenRGzZG4KLxUNoRVpYu2W5REj%2b83fKw9QE1kYvppTOZx%2f%2b3MxmBGm5WQxXFCAEkkS6%2bsUA4uV5iqku%2fn2nF04ljWw%2b21Sr9PoRS86fVQPAY71BglBUpCjOZqxjDaI7BLV%2bg4CkO4ikmuUBFQUsdiTIlC7wehcz8hvl8jCrwOhSEjGdDXuQyr%2b322h5zu8Au%2fDPmM%2feeglr32ROxULjkMs%2f63xfqXJowp0WPh%2bGe78VgBzFYMwx2XAyP%2fYtpeN7PGO5BDLfRNNa0x12q7l6MA0vVHMMslW09XtnW5iLY1hssIlXon%2fE0CYom0SsmQP6IMxz1%2b7%2b7AnXQdX6TNXMIvHA9hVMgNYEEqiaQuj5StXwh04kpUNVLqup3ETsCsoMxpavSSdXyi7NrIohJ%2foJDtoRbzybcMeFQjkjJZ4x1nYVWtEPtleHjjaGmCujnVu%2fWU75tHgYAAA%3d%3d)**\]**

```Kusto
let MyTrace = datatable (EventTrace:string)
[
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00Z, releaseTime=02/17/2016 08:40:00Z, previousLockTime=02/17/2016 08:39:00Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:00Z)'
];
MyTrace
| parse kind=relaxed "Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=NULL, sliceNumber=23, lockTime=02/17/2016 08:40:01, releaseTime=NULL, previousLockTime=02/17/2016 08:39:01)" with * "resourceName=" resourceName ", totalSlices=" totalSlices:long * "sliceNumber=" sliceNumber:long * "lockTime=" lockTime ", releaseTime=" releaseTime:date "," * "previousLockTime=" previousLockTime:date ")" *  
| project resourceName ,totalSlices , sliceNumber , lockTime , releaseTime , previousLockTime
```

## <a name="time-series-analysis"></a>時系列分析

### <a name="make-series"></a>make-series

[**make-series**](https://docs.microsoft.com/azure/kusto/query/make-seriesoperator): [summarize](https://docs.microsoft.com/azure/kusto/query/summarizeoperator) など、行のグループが集計されますが、by 値の組み合わせごとに (時) 系列ベクトルが生成されます。

次のクエリでは、1 日あたりの嵐の数に対して一連の時系列が返されます。 このクエリでは、州ごとに 3 か月という期間が考慮されます。足りない値域は定数 0 で埋められます。

**\[**[**クリックするとクエリが実行されます**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUchNzE7VLU4tykwtVsizTc4vzSvR0FRISU1LLM0psTVQyM9TCC5JLCoJycxNVcjMUyhKzEtP1UhJLEktAYpoGBkYmOsaGAKRpo4CmqixrjFI1DBFUyGpEmRKSSoAazsM0n0AAAA%3d)**\]**

```Kusto
StormEvents
| make-series n=count() default=0 on StartTime in range(datetime(2007-01-01), datetime(2007-03-31), 1d) by State
```

一連の (時) 系列を作成したら、系列関数を適用し、異常な形状や季節のパターンなどを検出できます。

次のクエリでは、特定の日に嵐が最も多く発生した上位 3 州が抽出されます。

**\[**[**クリックするとクエリが実行されます**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2OsQoCMRBEe8F%2f2DIBAzmvsLrSLzj7EC%2brBs3mSPbkBD%2feLDYibPVmZmdGziUdn0hct5s3JH9HU7FErEDDlBdipSHgxS8PHixkgpF94VNMCJGgeLqiCp6RG1F7aw%2fGdu30Dv5ob3qhXdBwfskXRmnElZECfDtdbbgq0qJwnqEX76%2fmyCW%2ftkV1Ek9pWSwgNdOt7foAJIuybs8AAAA%3d)**\]**

```Kusto
StormEvents
| make-series n=count() default=0 on StartTime in range(datetime(2007-01-01), datetime(2007-03-31), 1d) by State
| extend series_stats(n)
| top 3 by series_stats_n_max desc
| render timechart
```

詳細については、[系列関数](https://docs.microsoft.com/azure/kusto/query/scalarfunctions#series-processing-functions)の完全一覧をご覧ください。

## <a name="advanced-aggregations"></a>高度な集計

この記事の前半では、**count** や **summarize** など、基本的な集計を取り上げました。 このセクションでは、より高度なオプションを紹介します。

### <a name="top-nested"></a>top-nested

[**top-nested**](https://docs.microsoft.com/azure/kusto/query/topnestedoperator):上位の結果が階層型で生成されます。各レベルは前のレベル値に基づくドリルダウンになります。

この演算子は、ダッシュボード視覚化シナリオや、質問に対して答える必要がある場合に便利です。たとえば、"(何らかの集計を使用して) K1 の上位 N の値を見つけて、その値のそれぞれに対して (別の集計を使用して) K2 の上位 M の値を見つけて、..." のような質問です。

次のクエリでは、最上位を `State`、その次を `Sources` として階層型テーブルが返されます。

**\[**[**クリックするとクエリが実行されます**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjJL9DNSy0uSU1RMFLIT1MILkksSVVIqlQoLs3VcEpNz8zzSSzR1OHlQlJoDFaYX1qUTEilIUila16KT35yYklmfh6GcgDrXwk5jgAAAA%3d%3d)**\]**

```Kusto
StormEvents
| top-nested 2 of State by sum(BeginLat),
top-nested 3 of Source by sum(BeginLat),
top-nested 1 of EndLocation by sum(BeginLat)
```

### <a name="pivot-plugin"></a>pivot() プラグイン

[**pivot() plugin**](https://docs.microsoft.com/azure/kusto/query/pivotplugin):入力テーブルのある列の一意の値を出力テーブルの複数の列に変えるという方法でテーブルを回転させます。 この演算子では、最終的な出力の残りの列値で集計が必要になる箇所で集計が実行されます。

次のクエリではフィルターが適用され、行が回転して列になります。

**\[**[**クリックするとクエリが実行されます**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSgoys9KTS5RCC5JLEnVUQBLhFQWpILkyjNSi1IhMgrFJYlFJcXlmSUZCkqOPkoIabgOhYzEYgWl8My8FLBsalliTilIZ0FmWX6JBtgUTQDlv21NfQAAAA%3d%3d)**\]**

```Kusto
StormEvents
| project State, EventType
| where State startswith "AL"
| where EventType has "Wind"
| evaluate pivot(State)
```

### <a name="dcount"></a>dcount()

[**dcount()**](https://docs.microsoft.com/azure/kusto/query/dcount-aggfunction):グループ内のある式の個別値の数が見積もられ、返されます。 すべての値を数える際に [**count()**](https://docs.microsoft.com/azure/kusto/query/countoperator) を使用します。

次のクエリでは、個別の `Source` が `State` を基準に数えられます。

**\[**[**クリックするとクエリが実行されます**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlUIzi8tSk4tVrBVSEnOL80r0YAIaCokVSoElySWpAIAFKgSBDoAAAA%3d)**\]**

```Kusto
StormEvents
| summarize Sources = dcount(Source) by State
```

### <a name="dcountif"></a>dcountif()

[**dcountif()**](https://docs.microsoft.com/azure/kusto/query/dcountif-aggfunction):評価の結果、述語が真であるとされた行の式の個別値の数が見積もられ、返されます。

次のクエリでは、`DamageProperty < 5000` となっている `Source` の個別値が数えられます。

**\[**[**クリックするとクエリが実行されます**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVqhRKEnMTlUwNDDg5apRKC7NzU0syqxKVQjOLy1KTi1WsFVISc4vzSvJTNOACOkouCTmJqanBhTlF6QWlVQq2CiYGhgYaCokVSoElySWpAIAuk%2fTX14AAAA%3d)**\]**

```Kusto
StormEvents 
| take 100
| summarize Sources = dcountif(Source, DamageProperty < 5000) by State
```

### <a name="dcounthll"></a>dcount_hll()

[**dcount_hll()**](https://docs.microsoft.com/azure/kusto/query/dcount-hllfunction):( [**hll**](https://docs.microsoft.com/azure/kusto/query/hll-aggfunction)  または  [**hll_merge**](https://docs.microsoft.com/azure/kusto/query/hll-merge-aggfunction) によって生成される) HyperLogLog 結果から  **dcount**  が計算されます。

次のクエリでは、HLL アルゴリズムを使用してカウントが生成されます。

**\[**[**クリックするとクエリが実行されます**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlXIyMkJSi1WsAUxNFwScxPTUwOK8gtSi0oqNRWSKhWSMvM0gksSi0pCMnNTdQwNcjUx9PumFqWnpkCMiM8FcTQgpoKVFhTlZ6UmlyikJOeX5pXEg6yB69EEAKm9wyCXAAAA)**\]**

```Kusto
StormEvents
| summarize hllRes = hll(DamageProperty) by bin(StartTime,10m)
| summarize hllMerged = hll_merge(hllRes)
| project dcount_hll(hllMerged)
```

### <a name="argmax"></a>arg_max()

[**arg_max()**](https://docs.microsoft.com/azure/kusto/query/arg-max-aggfunction):グループ内で式を最大化する行を見つけ、別の式の値を返します (* の場合、式全体が返されます)。

次のクエリでは、各州で最後に洪水が報告された時刻が返されます。

**\[**[**クリックするとクエリが実行されます**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQDzQyoLUhVsbRWU3HLy81OUQLLFpbm5iUWZVakKiUXp8bmJFRrBJYlFJSGZuak6ClqaCkmVCkCBklSQ2oKi%2fKzU5BKIgI4CkkLXvBQoA2YNAHO1S0OFAAAA)**\]**

```Kusto
StormEvents
| where EventType == "Flood"
| summarize arg_max(StartTime, *) by State
| project State, StartTime, EndTime, EventType
```

### <a name="makeset"></a>makeset()

[**makeset()**](https://docs.microsoft.com/azure/kusto/query/makeset-aggfunction):グループ内である式により受け取られる個別値セットを dynamic (JSON) 配列で返します。

次のクエリでは、各州より洪水が報告された時刻がすべて返され、個別値セットから配列が作成されます。

**\[**[**クリックするとクエリが実行されます**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWLQQ6CQBAE7yb8ocNJE76wR3mA8IEFOxF1mM3siIHweAVPHqsq1bianCeOnovDiveNRuzczokIAWX9VL2WW80vkWjDQuzuwqTmGQESH8z0Y%2bPRvB2EJ3QzvuTcvmR6Z%2b8%2fUf3NH6ZkMFeAAAAA)**\]**

```Kusto
StormEvents
| where EventType == "Flood"
| summarize FloodReports = makeset(StartTime) by State
| project State, FloodReports
```

### <a name="mv-expand"></a>mv-expand

[**mv-expand**](https://docs.microsoft.com/azure/kusto/query/mvexpandoperator):コレクション内の各値に別個の行が与えられるように、動的に型指定された列から複数値のコレクションが展開されます。 展開された行内のその他の列はすべて複製されます。 makelist の反対になります。

次のクエリでは、1 つのセットを作成し、それを利用して **mv-expand** 機能を実行するという方法でサンプル データが生成されます。

**\[**[**クリックするとクエリが実行されます**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWOQQ6CQAxF9yTcoWGliTcws1MPIFygyk9EKTPpVBTj4Z2BjSz%2f738v7WF06r1vD2xcp%2bCoNq9yHDFYLIsvvW5Q0JybKYCco2omqnyNTxHW7oPFckbwajFZhB%2bIsE1trNZ0gi1dpuRmQ%2baC%2bjuuthS7Fbwvi%2f%2bP8lpGvAMP7Wr3A6BceSu7AAAA)**\]**

```Kusto
let FloodDataSet = StormEvents
| where EventType == "Flood"
| summarize FloodReports = makeset(StartTime) by State
| project State, FloodReports;
FloodDataSet
| mv-expand FloodReports
```

### <a name="percentiles"></a>percentiles()

[**percentiles()**](https://docs.microsoft.com/azure/kusto/query/percentiles-aggfunction):式により定義される人口について、指定の [**最も近いランクのパーセンタイル**](https://docs.microsoft.com/azure/kusto/query/percentiles-aggfunction) に対する見積もりを返します。 精度は、パーセンタイル リージョンの人口密度によって異なります。  [**summarize**](https://docs.microsoft.com/azure/kusto/query/summarizeoperator) 内の集計というコンテキストでのみ使用できます。

次のクエリでは、嵐の継続時間に対してパーセンタイルが計算されます。

**\[**[**クリックするとクエリが実行されます**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUUitKEnNS1FIKS1KLMnMz1OwVXDNSwnJzE1V0FUILkksKgGxQQrLM1KLUhHq7BQMirEI2ygYZ4CEi0tzcxOLMqtSFQpSi5KBlmXmpBZrwJTpKJjqKBgZACkgtgBiS1NNAEC7XiaYAAAA)**\]**

```Kusto
StormEvents
| extend duration = EndTime - StartTime
| where duration > 0s
| where duration < 3h
| summarize percentiles(duration, 5, 20, 50, 80, 95)
```

次のクエリでは、嵐の継続時間に対するパーセンタイルが州別に計算され、5 分 (`5m`) という値域でデータが正規化されます。

**\[**[**クリックするとクエリが実行されます**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG1NSwrCMBTcC95hli1EKEpBQd31BHUvafOgAZNI8uIPD28SEBVcDDMM8%2bnZedNdyHKYz56gG5NVUNFL1s5ih86qgzaEBXqWnrPOwetEnj65PZrwx95iNWU7RGOk1w8C5avj6KLlNF64qjHcMWhbvXsCralFPmT6rZ%2fJj2lAnyh8pwWWTaKEdcKmLYul%2fgLODFs%2b4AAAAA%3d%3d)**\]**

```Kusto
StormEvents
| extend duration = EndTime - StartTime
| where duration > 0s
| where duration < 3h
| summarize event_count = count() by bin(duration, 5m), State
| summarize percentiles(duration, 5, 20, 50, 80, 95) by State
```

### <a name="cross-dataset"></a>データセットを超える

このセクションでは、より複雑なクエリを作成したり、複数のテーブル間でデータを結合したり、すべてのデータベースやクラスターにわたってクエリを実行したりできます。

### <a name="let"></a>let

[**let**](https://docs.microsoft.com/azure/kusto/query/letstatement):モジュール性と再利用性が向上します。 **let** ステートメントでは、複雑になる可能性がある式を複数の部分に分割し、それぞれに名前を関連付け、そのような部品を組み立てることができます。 **let** ステートメントは、ユーザー定義の関数やビュー (結果が新しいテーブルのように見えるテーブルの式) を作成する目的にも使用できます。 **let** ステートメントでバインドされる式は、スカラー型、表型、またはユーザー定義関数 (ラムダ) になります。

次の例では表型の変数が作成され、それが後続の式で使用されます。

**\[**[**クリックするとクエリが実行されます**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAMtJLVHwyUzPKMnLzEsPLskvyi1WsOXlArNcy1LzSop5uWoUyjNSi1IVwPyQyoJUBVtbBSW4LiVrXq4coDGOZYk5iXnJGakkGQPXBTIGzSUgPVn5mXkKGmhmayrk5ykElySWpIKUpGQWl2TmJZdARACul3kY0gAAAA%3d%3d)**\]**

```Kusto
let LightningStorms =
StormEvents
| where EventType == "Lightning";
let AvalancheStorms =
StormEvents
| where EventType == "Avalanche";
LightningStorms
| join (AvalancheStorms) on State
| distinct State
```

### <a name="join"></a>join

[**join**](https://docs.microsoft.com/azure/kusto/query/joinoperator):各テーブルの指定の列で値を照合することで、2 つのテーブルの行を結合し、新しいテーブルを形成します。 Kusto は、**fullouter**、 **inner**、 **innerunique**、 **leftanti**、 **leftantisemi**、**leftouter**、 **leftsemi**、 **rightanti**、 **rightantisemi**、 **rightouter**、 **rightsemi** というすべての結合型に対応しています。

次の例では、内部結合を使用して 2 つのテーブルが結合されます。

**\[**[**クリックするとクエリが実行されます**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA8tJLVGIULBVSEksAcKknFQN79RKq+KSosy8dB2FsMSc0lRDq5z8vHRNXq5oXi4FIFBPVNcx1IGyk9R1jJDYxjB2srqOCS9XrDUvVw7Qhkj8Nhih2wA0ydAAySgjZI4xnJMCtMQAYkuEQo1CVn5mnkJ2Zl6KbWZeXmoR0Nb8PAWgZQAFPLdO5AAAAA==)**\]**

```Kusto
let X = datatable(Key:string, Value1:long)
[
    'a',1,
    'b',2,
    'b',3,
    'c',4
];
let Y = datatable(Key:string, Value2:long)
[
    'b',10,
    'c',20,
    'c',30,
    'd',40
];
X 
| join kind=inner Y on Key
```

> [!TIP]
> 結合の前に入力テーブルの行と列の数を減らすには、**where** と **project** を使用します。 一方のテーブルがもう一方よりも常に小さい場合は、それを結合の左側 (パイプされる側) として使います。 一致した場合に結合する列は、同じ名前を持つ必要があります。 いずれかのテーブルで列の名前を変更する必要がある場合は、**project** 演算子を使用します。

### <a name="serialize"></a>serialize

[**serialize**](https://docs.microsoft.com/azure/kusto/query/serializeoperator):**row_number()** のようなシリアル化されたデータを必要とする関数を使用できるように、行セットがシリアル化されます。

データがシリアル化されているため、次のクエリは成功します。

**\[**[**クリックするとクエリが実行されます**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIzi%2fNK9HQVEiqVAguSSxJBcumFmUm5gBlQZzUipLUvBSFovzy%2bLzS3KTUIgVbJI6GJgB4pV4NWgAAAA%3d%3d)**\]**

```Kusto
StormEvents
| summarize count() by State
| serialize
| extend row_number = row_number()
```

行セットは、それが **sort**、**top**、**range** 演算子の結果であり、任意で後ろに **project**、**project-away**、**extend**、**where**、**parse**、**mv-expand**、**take** 演算子が続く場合も、シリアル化されていると見なされます。

**\[**[**クリックするとクエリが実行されます**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIzi%2fNK9HQVEiqVAguSSxJBcvmF5XABRQSi5NBgqkVJal5KQpF%2beXxeaW5SalFCrZIHA1NAEGimf5iAAAA)**\]**

```Kusto
StormEvents
| summarize count() by State
| sort by State asc
| extend row_number = row_number()
```

### <a name="cross-database-and-cross-cluster-queries"></a>複数のデータベースに対するクエリと複数のクラスターに対するクエリ

[複数のデータベースに対するクエリと複数のクラスターに対するクエリ](https://docs.microsoft.com/azure/kusto/query/cross-cluster-or-database-queries):`database("MyDatabase").MyTable` として参照すれば、同じクラスター上のデータベースにクエリを実行できます。 `cluster("MyCluster").database("MyDatabase").MyTable` として参照すれば、リモート クラスター上のデータベースにクエリを実行できます。

次のクエリはあるクラスターから呼び出され、`MyCluster` クラスターのデータを問い合わせます。 このクエリを実行するには、独自のクラスター名とデータベース名を使用します。

```Kusto
cluster("MyCluster").database("Wiki").PageViews
| where Views < 2000
| take 1000;
```

### <a name="user-analytics"></a>ユーザー分析

このセクションには、Kusto ではいかに簡単にユーザーの行動を分析できるかということを示す要素とクエリが含まれています。

### <a name="activitycountsmetrics-plugin"></a>activity_counts_metrics プラグイン

[**activity_counts_metrics プラグイン**](https://docs.microsoft.com/azure/kusto/query/activity-counts-metrics-plugin):有用なアクティビティ指標が計算されます (合計カウント値、個別カウント値、新しい値の個別カウント、集計個別カウント)。 指標は時間枠ごとに計算され、比較され、前のすべての時間枠と共に集計されます。

次のクエリでは、毎日のアクティビティ カウントを計算することでユーザー導入が分析されます。

**\[**[**クリックするとクエリが実行されます**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAJXSPQvCMBAG4L3Q%2f5CtFlLoFyiVDn4M6mqdREpsggTaKs1VEfzxXm0LDiEimcJz3CW8VwogClgDKWcgQFZiEvrB1PNnnh%2b4c9sqsUDUXMPxyA9Z8%2bsjDfhwz0hKsBzPuRSTgxLNlicKGllfKMmwBw6sbsnY0bWto205C4cS3Rso2tpgO4MtDbbSWvixzGD6eb1ttBYZev42%2fbzI8L%2fe9n9b3NkJQ8xs60XEnZUt1hBWgLxLeObFta1B5ZXAKAs1BPuVKO03iXb7gp36tXDfExVB%2f2ICAAA%3d)**\]**

```Kusto
let start=datetime(2017-08-01);
let end=datetime(2017-08-04);
let window=1d;
let T = datatable(UserId:string, Timestamp:datetime)
[
'A', datetime(2017-08-01),
'D', datetime(2017-08-01),
'J', datetime(2017-08-01),
'B', datetime(2017-08-01),
'C', datetime(2017-08-02),
'T', datetime(2017-08-02),
'J', datetime(2017-08-02),
'H', datetime(2017-08-03),
'T', datetime(2017-08-03),
'T', datetime(2017-08-03),
'J', datetime(2017-08-03),
'B', datetime(2017-08-03),
'S', datetime(2017-08-03),
'S', datetime(2017-08-04),
];
T
| evaluate activity_counts_metrics(UserId, Timestamp, start, end,
window)
```

### <a name="activityengagement-plugin"></a>activity_engagement プラグイン

[**activity_engagement プラグイン**](https://docs.microsoft.com/azure/kusto/query/activity-engagement-plugin):変化するタイムライン ウィンドウを対象に、ID 列に基づいてアクティビティ エンゲージメント率が計算されます。 **activity_engagement プラグイン**は、DAU、WAU、MAU (毎日、毎週、毎月のアクティブ ユーザー) の計算に利用できます。

次のクエリでは、7 日間の移動ウィンドウを対象に、あるアプリケーションを毎日使用するユーザーの合計とそのアプリケーションを週単位で使用するユーザーの合計の比率が返されます。

**\[**[**クリックするとクエリが実行されます**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG1RQWrDMBC8G%2fyHvUVOHGy1lByKD6GBviDkUIoR1tpVsS0jr0MCeXxXiigpVAiBVjOzM6uigHcc0SlCcGrUdgCtSIFtYZnRgWrInA0ZnNOkR4J6JuUIKo9CMgOKp1LutqXknb1GDI76P8RzQHCXDqHW6gqt43ZRkeydNxNOIHWa3AAv5Ctei2xvx06IQNtGTlZInT0AHQN9BpFt5EO59kHmKvQVUUivX8q1y3L4c9%2fIks%2bt5LoMwsMZLxMrgtHVXcb7pOuEthWemEFvBkPARL%2fSpCjgTfXN0vuBHvbH4rQ%2fsikyNjg6q37xL3GsV47cqQ4HHEl8rIxefeZhNHmMmIehsB2dp8nunnZy9hsbiriDWuqTWqpfxdBsLb2ZGzhm8y%2f6b2i%2bWO8HLFcMGe8BAAA%3d)**\]**

```Kusto
// Generate random data of user activities
let _start = datetime(2017-01-01);
let _end = datetime(2017-01-31);
range _day from _start to _end step 1d
| extend d = tolong((_day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+100*r-1), 1)
| mv-expand id=_users to typeof(long) limit 1000000
// Calculate DAU/WAU ratio
| evaluate activity_engagement(['id'], _day, _start, _end, 1d, 7d)
| project _day, Dau_Wau=activity_ratio*100
| render timechart
```

> [!TIP]
> DAU/MAU を計算するとき、エンド データと移動ウィンドウ期間 (OuterActivityWindow) を変更します。

### <a name="activitymetrics-plugin"></a>activity_metrics プラグイン

[**activity_metrics プラグイン**](https://docs.microsoft.com/azure/kusto/query/activity-metrics-plugin):現在の期間と前の期間に基づいて、有用なアクティビティ指標 (個別カウント値、新しい値の個別カウント、保有率、解約率) を計算し、比較します。

次のクエリでは、指定のデータセットに対して解約率と保有率が計算されます。

**\[**[**クリックするとクエリが実行されます**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG2SwW7CMAyG70i8g2%2bk0KoNE%2bIwscsOe4hpqqLGQFjaVKkLVNrDLw7RxjRyqBTr%2fz%2f3t1OW8IYdekUIXnXataAVKXB7GAf0oBoyZ0MGh%2fnMIkE9kPIEO1YhmRbFupLbopJFtc6ekwY7%2fV%2bxKZ4kK0KXA0Kt1QR7H9olIrmbbyDsQer57AvwSlxhFjnruoMQ0VYkT1ZKnd0JfRByBpGt5F255iDDLvYVCaSXm2rpsxz%2b3FfrKnwLGeoygtszXvtABKN3Nwz%2fJ009ur1gYwbWtIZAVvGw53JEn%2fK9PJwSi3rvTthQlOWBPp%2bVJbwq24yWN3FB%2fLQTeAwByLgOeD8x0lnZkRVpL1PdInnTDOJ9YfTiI0%2fE24DyONIctvpB0x94zfBlSJBDcxz97509PgDCM%2bAMzTEgvwEO44wSMAIAAA%3d%3d)**\]**

```Kusto
// Generate random data of user activities
let _start = datetime(2017-01-02);
let _end = datetime(2017-05-31);
range _day from _start to _end step 1d
| extend d = tolong((_day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+200*r-1), 1)
| mv-expand id=_users to typeof(long) limit 1000000
| where _day > datetime(2017-01-02)
| project _day, id
// Calculate weekly retention rate
| evaluate activity_metrics(['id'], _day, _start, _end, 7d)
| project _day, retention_rate*100, churn_rate*100
| render timechart
```

### <a name="newactivitymetrics-plugin"></a>new_activity_metrics プラグイン

[**new_activity_metrics プラグイン**](https://docs.microsoft.com/azure/kusto/query/new-activity-metrics-plugin):新しいユーザーの集合に対して有用なアクティビティ指標 (個別カウント値、新しい値の個別カウント、保有率、解約率) を計算し、比較します。 このプラグインの概念は  [**activity_metrics plugin**](https://docs.microsoft.com/azure/kusto/query/activity-metrics-plugin) に似ていますが、新しいユーザーに焦点が置かれます。

次のクエリでは、新しいユーザーの集合 (最初の週に現れたユーザー) を対象に、週単位の保有率と解約率が計算されます。

**\[**[**クリックするとクエリが実行されます**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG1Ry27DIBC8W%2fI%2f7C04wbJJFeVQ5VapP9BbVVnIrGMaGyy8eVjqxxcwh1QqBx7LzCwzVBW8o0EnCcFJo%2bwISpIE28F1RgeyJX3TpHHOswEJmpmkIzgFFJIeke1rcSzrQ1mL4jVh0Kj%2fEC8R4bucEd7kAp3z3ZIg2ZU2E04gVJ79AD4oVIIU2cGaM2OBVSZKUQlVPOGcxwUHrNiJp3ITbMyn2JUlHbU91FtXcPhz3u1rP5fC10UUHm%2f4mLwiaHVaZcIzaZnQdiwQCxj0qAlEHUeeVRV8yAuCNcMC1CN02s0Ed8QLtLa33igbpK9M0skRCd3q4CaHa%2fgBg%2fcmJb40%2ft7pdmafG602XzxExpN3HsPicFQ8z1IcQWhy9htbisk2EU92XZ1vZkhb04Sv5tD2V7fufwFYtolnAgIAAA%3d%3d)**\]**

```Kusto
// Generate random data of user activities
let _start = datetime(2017-05-01);
let _end = datetime(2017-05-31);
range Day from _start to _end step 1d
| extend d = tolong((Day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+200*r-1), 1)
| mv-expand id=_users to typeof(long) limit 1000000
// Take only the first week cohort (last parameter)
| evaluate new_activity_metrics(['id'], Day, _start, _end, 7d, _start)
| project from_Day, to_Day, retention_rate, churn_rate
```

### <a name="sessioncount-plugin"></a>session_count プラグイン

[**session_count プラグイン**](https://docs.microsoft.com/azure/kusto/query/session-count-plugin):あるタイムラインを対象に ID 列に基づいてセッションの数が計算されます。

次のクエリでは、セッション数が返されます。 セッションは、あるユーザー ID が 100 個の時間帯の時間枠の中で少なくとも 1 回現れる場合 (そのとき、セッションの遡りウィンドウは 41 個の時間帯) にアクティブと見なされます。

**\[**[**クリックするとクエリが実行されます**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWPQYvCQAyF74X%2bh3dZUCjYgfUgMkcP3r2XoZPqaM3INK4u7I%2ffzOwiNQRC8pKPl5EEnXfiYJEcHwmHcKUxMGFI8QoDidhoYBK6wdTVD%2bgpxB5dd6FvPSuzcwyMS2BvAzMlLP5gez%2fDrNt%2fCN4Z1iwRua5Kk2GPE6WZkY%2bMsRZt1m4pnqmXl9qouK2r1Qo75cUB5RlPQ%2bAgoWDzpPj%2bcuPdCWGiaVKp6%2bOdZbH3zYxmNFuNUhp8mmU%2bTWpWv8or%2fckl%2bQXutT48NwEAAA%3d%3d)**\]**

```Kusto
let _data = range Timeline from 1 to 9999 step 1
| extend __key = 1
| join kind=inner (range Id from 1 to 50 step 1 | extend __key=1) on __key
| where Timeline % Id == 0
| project Timeline, Id;
// End of data definition
_data
| evaluate session_count(Id, Timeline, 1, 10000, 100, 41)
| render linechart
```

### <a name="funnelsequence-plugin"></a>funnel_sequence プラグイン

[**funnel_sequence プラグイン**](https://docs.microsoft.com/azure/kusto/query/funnel-sequence-plugin):ある一連の状態を経たユーザーの個別カウントが計算されます。その連続につながった、あるいはその連続が後に続いた前の状態と次の状態の分布が表示されます。

次のクエリでは、2007 年にすべての竜巻の前後で発生したイベントが表示されます。

**\[**[**クリックするとクエリが実行されます**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAGWOPYvCQBCG%2b0D%2bw3QmEEmieIqNVQrBRgxYiMhcdqKLyWzcnQiCP95V70DuYIrh5Xk%2f0hRWxpw1H8EwbMTYtrgSiwMnKNqJrtw8DNIU1vkcticUOGHXETv4ptpYgtJYRmWAnrbFGx39QbEWsv%2fIj7YwuHsZmx6FoO6ZqTk4uvTEFUVFp51RtFSJH4hWSt1SAsqj4r9olGXTYZb7i5Mw%2bJRnvzLkKhl%2fTXzAq668dc%2bAG2Orq2g3%2bBk22MfxA23MLGQQAQAA)**\]**

```Kusto
// Looking on StormEvents statistics:
// Q1: What happens before Tornado event?
// Q2: What happens after Tornado event?
StormEvents
| evaluate funnel_sequence(EpisodeId, StartTime, datetime(2007-01-01), datetime(2008-01-01), 1d,365d, EventType, dynamic(['Tornado']))
```

### <a name="funnelsequencecompletion-plugin"></a>funnel_sequence_completion プラグイン

[**funnel_sequence_completion プラグイン**](https://docs.microsoft.com/azure/kusto/query/funnel-sequence-completion-plugin):異なる期間内で完了したシーケンス ステップのフィルターが計算されます。

次のクエリでは、1 時間、4 時間、1 日 (`[1h, 4h, 1d]`) という "全体" 時間を対象に、 `Hail -> Tornado -> Thunderstorm -> Wind` という順序の完了したフィルターが確認されます。

**\[**[**クリックするとクエリが実行されます**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA12QTYvCMBCG74L/YW6tkIV2XT9g8SjsnlvwICKhM9JAOqlJqrj4402CW0RIIB/PPLwzmjwcnZfWwwZQevKqo/yzKFYfRRnW7Hs60ZEhxjdi/UZcFaO5VuqPAjhfLvD/w9F5IG7iM95YdqrJ99mPVDoTkNXGskSTju3ASNZ5Y7t43wVhdhj9PVll0L1aylbAV9glJqyKldsLsXfTyR3oIvUQAsNpYCY95jg2puuDUhnOt71yBukXBVRxCnVoTjwnIlLX4rUzAUlf3/pEPYViDDd7AOyqowFQAQAA)**\]**

```Kusto
let _start = datetime(2007-01-01);
let _end = datetime(2008-01-01);
let _windowSize = 365d;
let _sequence = dynamic(['Hail', 'Tornado', 'Thunderstorm', 'Wind']);
let _periods = dynamic([1h, 4h, 1d]);
StormEvents
| evaluate funnel_sequence_completion(EpisodeId, StartTime, _start, _end, _windowSize, EventType, _sequence, _periods)
```

## <a name="functions"></a>Functions

このセクションでは、[**関数**](https://docs.microsoft.com/azure/kusto/query/functions)を扱います。サーバー上に保管される再利用可能なクエリです。 関数はクエリや他の関数で呼び出すことができます (再帰関数はサポートされていません)。

> [!NOTE]
> ヘルプ センターで関数を作成することはできません。読み取り専用です。 この部分には独自のテスト クラスターを使用します。

次の例では、引数として州名 (`MyState`) を受け取る関数が作成されます。

```Kusto
.create function with (folder="Demo")
MyFunction (MyState: string)
{
StormEvents
| where State =~ MyState
}
```

次の例では、テキサス州のデータを取得する関数が呼び出されます。

```Kusto
MyFunction ("Texas")
| summarize count()
```

次の例では、最初の手順で作成された関数が削除されます。

```Kusto
.drop function MyFunction
```

## <a name="next-steps"></a>次の手順

[Kusto クエリ言語リファレンス](https://aka.ms/kustolangref)
