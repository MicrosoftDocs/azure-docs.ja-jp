---
title: Azure Monitor ログのテキスト データの解析 | Microsoft Docs
description: データが取り込まれたとき、またデータがクエリで取得されたときに、Azure Monitor レコード内のログ データを解析するためのさまざまなオプションについて説明します。また、それぞれの相対的な利点について比較します。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/04/2018
ms.openlocfilehash: 2e2babd39dc9dadf06c9a3ca3292e021e0c1deaa
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102037185"
---
# <a name="parse-text-data-in-azure-monitor-logs"></a>Azure Monitor ログのテキスト データの解析
Azure Monitor によって収集された一部のログ データでは、1 つのプロパティに複数に分けられた情報が含まれます。 このデータを複数のプロパティに分けて解析すると、クエリで使用しやすくなります。 一般的な例は、複数の値を持つすべてのログ エントリを 1 つのプロパティに集める[カスタム ログ](../agents/data-sources-custom-logs.md)です。 異なる値に個別のプロパティを作成することによって、それぞれで検索したり、集約したりできます。

この記事では、データが取り込まれたとき、またデータがクエリで取得されたときに、Azure Monitor でログ データを解析するためのさまざまなオプションについて説明します。また、それぞれの相対的な利点について比較します。


## <a name="parsing-methods"></a>解析方法
データを収集した場合は取り込み時に、またはクエリを使用してデータを分析した場合はクエリ時にデータを解析できます。 以下に示すように、それぞれの戦略には、それぞれの利点があります。

### <a name="parse-data-at-collection-time"></a>収集時のデータ解析
収集時にデータを解析する場合、テーブルに新しいプロパティを作成する[カスタム フィールド](../logs/custom-fields.md)を構成します。 クエリに解析ロジックを含める必要はありません。テーブルのその他のフィールドと同じようにこれらのプロパティを使用します。

この方法には、次のような長所があります。

- クエリに解析コマンドを含める必要がないため、収集したデータに対してクエリを実行しやすくなります。
- クエリで解析を実行する必要がないため、クエリのパフォーマンスが向上します。
 
この方法には、次のような短所があります。

- 前もって定義する必要があります。 既に収集されたデータを含めることはできません。
- 解析ロジックを変更する場合は、新しいデータにのみ適用されます。
- 解析オプションの数がクエリで使用できるオプションよりも少なくなります。
- データ収集のための待機時間が長くなります。
- エラー処理が難しくなる場合があります。


### <a name="parse-data-at-query-time"></a>クエリ実行時のデータ解析
クエリ実行時にデータを解析する場合、データを複数のフィールドに分けて解析するロジックをクエリに含めます。 実際のテーブル自体は変更されません。

この方法には、次のような長所があります。

- 既に収集されたデータを含め、すべてのデータに適用されます。
- ロジックに加えた変更はすべてのデータにすぐに適用されます。
- 特定のデータ構造に対して事前に定義したロジックを含めることができる柔軟な解析オプション。
 
この方法には、次のような短所があります。

- より複雑なクエリを記述する必要があります。 これは、[テーブルをシミュレートする関数](#use-function-to-simulate-a-table)を使用することによって軽減できます。
- 複数のクエリの解析ロジックをレプリケートする必要があります。 関数を使用して一部のロジックを共有できます。
- 非常に大きなレコード セット (数十億件のレコード) に対して複雑なロジックを実行するときに、オーバーヘッドが発生する可能性があります。

## <a name="parse-data-as-its-collected"></a>収集されたとおりにデータを解析する
収集されたとおりにデータを解析する方法については、[Azure Monitor でのカスタム フィールドの作成](../logs/custom-fields.md)に関するページを参照してください。 この方法では、他のプロパティと同じようにクエリで使用できるテーブル内のカスタム プロパティが作成されます。

## <a name="parse-data-in-query-using-patterns"></a>パターンを使用してクエリでデータを解析する
レコード全体で繰り返されるパターンによって解析するデータを識別できる場合、[Kusto クエリ言語](/azure/kusto/query/)のさまざまな演算子を使用して、1 つまたは複数の新しいプロパティに特定のデータを抽出することができます。

### <a name="simple-text-patterns"></a>単純なテキスト パターン

クエリで [parse](/azure/kusto/query/parseoperator) 演算子を使用して、文字列式から抽出できる 1 つ以上のカスタム プロパティを作成します。 識別するパターンと作成するプロパティの名前を指定します。 これは、_key=value_ のような形式でキー値の文字列を持つデータで特に役立ちます。

次の形式でデータを持つカスタム ログについて考慮します。

```
Time=2018-03-10 01:34:36 Event Code=207 Status=Success Message=Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
Time=2018-03-10 01:33:33 Event Code=208 Status=Warning Message=Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
Time=2018-03-10 01:35:44 Event Code=209 Status=Success Message=Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
Time=2018-03-10 01:38:22 Event Code=302 Status=Error Message=Application could not connect to database
Time=2018-03-10 01:31:34 Event Code=303 Status=Error Message=Application lost connection to database
```

次のクエリは、このデータを個別のプロパティに分けて解析します。 _project_ の行は、_RawData_ (これはカスタム ログからエントリ全体を保持する単一のプロパティです) ではなく、計算されたプロパティのみを返すために追加されています。

```Kusto
MyCustomLog_CL
| parse RawData with * "Time=" EventTime " Event Code=" Code " Status=" Status " Message=" Message
| project EventTime, Code, Status, Message
```

以下は、_AzureActivity_ テーブルで UPN のユーザー名を呼び出す別の例です。

```Kusto
AzureActivity
| parse  Caller with UPNUserPart "@" * 
| where UPNUserPart != "" //Remove non UPN callers (apps, SPNs, etc)
| distinct UPNUserPart, Caller
```


### <a name="regular-expressions"></a>正規表現
データを正規表現で識別できる場合、個々の値を抽出するために、[正規表現を使用する関数](/azure/kusto/query/re2)を使用できます。 次の例では、[extract](/azure/kusto/query/extractfunction) を使用して、_AzureActivity_ レコードから _UPN_ フィールドを呼び出し、個別のユーザーを返します。

```Kusto
AzureActivity
| extend UPNUserPart = extract("([a-z.]*)@", 1, Caller) 
| distinct UPNUserPart, Caller
```

大規模な解析を効率的にするために、Azure Monitor は re2 バージョンの正規表現を使用します。これは、類似しているものの、他の正規表現のバリアントとは異なります。 詳細については、[re2 式の構文](https://aka.ms/kql_re2syntax)に関するページを参照してください。


## <a name="parse-delimited-data-in-a-query"></a>クエリで区切られたデータを解析する
区切られたデータでは、CSV ファイルのコンマのような一般的な文字でフィールドが区切られます。 [split](/azure/kusto/query/splitfunction) 関数を使用し、指定した区切り記号で区切られたデータを解析します。 この関数を [extend](/azure/kusto/query/extendoperator) 演算子と一緒に使用して、データ内のすべてのフィールドを返したり、出力に含める個々のフィールドを指定したりできます。

> [!NOTE]
> split は動的オブジェクトを返すため、演算子とフィルターで使用される文字列などのデータ型に、結果を明示的にキャストする必要があります。

次の CSV 形式でデータを持つカスタム ログについて考慮します。

```
2018-03-10 01:34:36, 207,Success,Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
2018-03-10 01:33:33, 208,Warning,Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
2018-03-10 01:35:44, 209,Success,Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
2018-03-10 01:38:22, 302,Error,Application could not connect to database
2018-03-10 01:31:34, 303,Error,Application lost connection to database
```

次のクエリは、このデータを解析し、2 つの計算されたプロパティで集計します。 最初の行は、_RawData_ プロパティを文字列配列に分割します。 次の各行は、プロパティに名前を付け、適切なデータ型に変換する関数を使用して出力に追加します。

```Kusto
MyCustomCSVLog_CL
| extend CSVFields  = split(RawData, ',')
| extend EventTime  = todatetime(CSVFields[0])
| extend Code       = toint(CSVFields[1]) 
| extend Status     = tostring(CSVFields[2]) 
| extend Message    = tostring(CSVFields[3]) 
| where getyear(EventTime) == 2018
| summarize count() by Status,Code
```

## <a name="parse-predefined-structures-in-a-query"></a>クエリの定義済み構造の解析
データが既知の構造で形式が設定されている場合、定義済みの構造を解析するために、[Kusto クエリ言語](/azure/kusto/query/)のいずれかの関数を使用することができます。

- [JSON](/azure/kusto/query/parsejsonfunction)
- [XML](/azure/kusto/query/parse-xmlfunction)
- [IPv4](/azure/kusto/query/parse-ipv4function)
- [URL](/azure/kusto/query/parseurlfunction)
- [URL query](/azure/kusto/query/parseurlqueryfunction)
- [[ファイル パス]](/azure/kusto/query/parsepathfunction)
- [User agent](/azure/kusto/query/parse-useragentfunction)
- [Version string](/azure/kusto/query/parse-versionfunction)

次の例のクエリは、JSON で構造化された _AzureActivity_ テーブルの _Properties_ フィールドを解析します。 JSON で名前が付けられた個々の値が含まれる _parsedProp_ という名前の動的プロパティに結果を保存します。 これらの値は、クエリ結果をフィルターし、集計するために使用されます。

```Kusto
AzureActivity
| extend parsedProp = parse_json(Properties) 
| where parsedProp.isComplianceCheck == "True" 
| summarize count() by ResourceGroup, tostring(parsedProp.tags.businessowner)
```

これらの解析関数は、プロセッサに負荷をかける場合があります。そのため、クエリが書式設定されたデータの複数のプロパティを使用する場合に限って使用するようにしてください。 それ以外の場合、単純なパターン マッチング処理は、高速になります。

以下の例には、ドメイン コントローラー TGT Preauth タイプの詳細が示されています。 このタイプは、EventData フィールドにのみ存在し、XML 文字列です。必要とされる、このフィールドからのデータは他にありません。 この場合、[parse](/azure/kusto/query/parseoperator) は必要な特定のデータを選択するために使用します。

```Kusto
SecurityEvent
| where EventID == 4768
| parse EventData with * 'PreAuthType">' PreAuthType '</Data>' * 
| summarize count() by PreAuthType
```

## <a name="use-function-to-simulate-a-table"></a>関数を使用してテーブルをシミュレートする
特定のテーブルの同じ解析を実行する複数のクエリが存在することがあります。 この場合、各クエリで解析ロジックをレプリケートせずに、解析されたデータを返す[関数を作成します](../logs/functions.md)。 その他のクエリでは、元のテーブルの代わりに、関数のエイリアスを使用できます。

上記のコンマ区切りのカスタム ログの例を考慮してください。 複数のクエリで解析されたデータを使用するには、次のクエリを使用して関数を作成し、エイリアス _MyCustomCSVLog_ で保存します。

```Kusto
MyCustomCSVLog_CL
| extend CSVFields = split(RawData, ',')
| extend DateTime  = tostring(CSVFields[0])
| extend Code      = toint(CSVFields[1]) 
| extend Status    = tostring(CSVFields[2]) 
| extend Message   = tostring(CSVFields[3]) 
```

これで、次のようなクエリで実際のテーブル名の代わりにエイリアス _MyCustomCSVLog_ を使用できるようになりました。

```Kusto
MyCustomCSVLog
| summarize count() by Status,Code
```


## <a name="next-steps"></a>次のステップ
* [ログ クエリ](./log-query-overview.md)について学習し、データ ソースとソリューションから収集されたデータを分析します。