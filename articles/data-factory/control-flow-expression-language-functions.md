---
title: Azure Data Factory の式と関数
description: この記事では、Data Factory エンティティの作成で使用できる式と関数に関する情報を提供します。
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/25/2019
ms.openlocfilehash: e89cb847bcd5d0137354c07fe97148bcbeca2714
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104786296"
---
# <a name="expressions-and-functions-in-azure-data-factory"></a>Azure Data Factory の式と関数

> [!div class="op_single_selector" title1="使用している Data Factory サービスのバージョンを選択してください:"]
> * [Version 1](v1/data-factory-functions-variables.md)
> * [現在のバージョン](control-flow-expression-language-functions.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory によってサポートされている式と関数に関する詳細情報を提供します。 

## <a name="expressions"></a>式

定義内の JSON 値には、実行時に評価されるリテラルまたは式を指定できます。 次に例を示します。  
  
```json
"name": "value"
```

 or  
  
```json
"name": "@pipeline().parameters.password"
```

式は、JSON string 値内の任意の場所で使うことができ、常に別の JSON 値になります。 JSON 値が式である場合は、アットマーク (\@) を削除することによって式の本体が抽出されます。 \@ で始まるリテラル文字列が必要な場合は、\@\@ を使用してその文字列をエスケープする必要があります。 式の評価方法の例を次に示します。  
  
|JSON 値|結果|  
|----------------|------------|  
|"parameters"|文字 "parameters" が返されます。|  
|"parameters [1]"|文字 "parameters[1]" が返されます。|  
|"\@\@"|'\@' を含む 1 文字の文字列が返されます。|  
|" \@"|' \@' を含む 2 文字の文字列が返されます。|  
  
 また、式が `@{ ... }` にラップされる *文字列補間* と呼ばれる機能を使用して、式を文字列の内部に指定することもできます。 例: `"name" : "First Name: @{pipeline().parameters.firstName} Last Name: @{pipeline().parameters.lastName}"`  
  
 文字列補間を使用すると、結果は常に文字列になります。 `myNumber` を `42` として、`myString` を `foo` として定義したとします。  
  
|JSON 値|結果|  
|----------------|------------|  
|"\@pipeline().parameters.myString"| `foo` が文字列として返されます。|  
|"\@{pipeline().parameters.myString}"| `foo` が文字列として返されます。|  
|"\@pipeline().parameters.myNumber"| `42` が "*数値*" として返されます。|  
|"\@{pipeline().parameters.myNumber}"| `42` が "*string*" として返されます。|  
|"Answer is: @{pipeline().parameters.myNumber}"| string `Answer is: 42` が返されます。|  
|"\@concat('Answer is: ', string(pipeline().parameters.myNumber))"| string `Answer is: 42` が返されます。|  
|"Answer is: \@\@{pipeline().parameters.myNumber}"| string `Answer is: @{pipeline().parameters.myNumber}` が返されます。|  

## <a name="examples"></a>例

### <a name="complex-expression-example"></a>複合式の例
次の例は、アクティビティの出力の詳細サブフィールドを参照する複雑な例を示しています。 サブフィールドと評価されるパイプライン パラメーターを参照するには、ドット (.) 演算子ではなく、[] 構文を使用します (subfield1 と subfield2 の場合と同様)

`@activity('*activityName*').output.*subfield1*.*subfield2*[pipeline().parameters.*subfield3*].*subfield4*`

### <a name="dynamic-content-editor"></a>動的コンテンツ エディター

編集が完了すると、動的コンテンツ エディターによってコンテンツ内の文字が自動的にエスケープされます。 たとえば、コンテンツ エディター内の次のコンテンツは、2 つの式関数を使用する文字列補間です。 

```json
{ 
  "type": "@{if(equals(1, 2), 'Blob', 'Table' )}",
  "name": "@{toUpper('myData')}"
}
```

動的コンテンツ エディターは、上記のコンテンツを式 `"{ \n  \"type\": \"@{if(equals(1, 2), 'Blob', 'Table' )}\",\n  \"name\": \"@{toUpper('myData')}\"\n}"` に変換します。 この式の結果は、次に示す JSON 形式の文字列になります。

```json
{
  "type": "Table",
  "name": "MYDATA"
}
```

### <a name="a-dataset-with-a-parameter"></a>パラメーターを備えたデータセット
次の例では、BlobDataset は **path** という名前のパラメーターを受け取ります。 その値は、`dataset().path` という式を使用して **folderPath** プロパティの値を設定するために使用されます。 

```json
{
    "name": "BlobDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "@dataset().path"
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "path": {
                "type": "String"
            }
        }
    }
}
```

### <a name="a-pipeline-with-a-parameter"></a>パラメーターを備えたパイプライン
次の例では、パイプラインは **inputPath** パラメーターと **outputPath** パラメーターを受け取ります。 パラメーター化された BLOB データセットの **path** は、これらのパラメーターの値を使用して設定されます。 ここで使用する構文は `pipeline().parameters.parametername` です。 

```json
{
    "name": "Adfv2QuickStartPipeline",
    "properties": {
        "activities": [
            {
                "name": "CopyFromBlobToBlob",
                "type": "Copy",
                "inputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.inputPath"
                        },
                        "type": "DatasetReference"
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.outputPath"
                        },
                        "type": "DatasetReference"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                }
            }
        ],
        "parameters": {
            "inputPath": {
                "type": "String"
            },
            "outputPath": {
                "type": "String"
            }
        }
    }
}
```
### <a name="tutorial"></a>チュートリアル
この[チュートリアル](https://azure.microsoft.com/mediahandler/files/resourcefiles/azure-data-factory-passing-parameters/Azure%20data%20Factory-Whitepaper-PassingParameters.pdf)では、パイプラインとアクティビティ間、およびアクティビティ間でパラメーターを渡す方法について説明します。

  
## <a name="functions"></a>関数

式の中で関数を呼び出すことができます。 以降のセクションでは、式で使用できる関数に関する情報を提供します。  

## <a name="string-functions"></a>文字列関数  

文字列を処理するには、以下の文字列関数および一部の[コレクション関数](#collection-functions)も使用できます。
文字列関数は文字列でのみ機能します。

| 文字列関数 | タスク |
| --------------- | ---- |
| [concat](control-flow-expression-language-functions.md#concat) | 2 つ以上の文字列を結合し、結合された文字列を返します。 |
| [endsWith](control-flow-expression-language-functions.md#endswith) | 文字列が指定された部分文字列で終わっているかどうかを調べます。 |
| [guid](control-flow-expression-language-functions.md#guid) | 文字列としてグローバル一意識別子 (GUID) を生成します。 |
| [indexOf](control-flow-expression-language-functions.md#indexof) | 部分文字列の開始位置を返します。 |
| [lastIndexOf](control-flow-expression-language-functions.md#lastindexof) | 部分文字列の最後の出現箇所の開始位置を返します。 |
| [replace](control-flow-expression-language-functions.md#replace) | 部分文字列を指定した文字列で置換し、更新された文字列を返します。 |
| [split](control-flow-expression-language-functions.md#split) | 元の文字列で指定された区切り文字に基づいたより大きい文字列から、コンマで区切られた部分文字列を含む配列を返します。 |
| [startsWith](control-flow-expression-language-functions.md#startswith) | 文字列が特定の部分文字列で始まっているかどうかを調べます。 |
| [substring](control-flow-expression-language-functions.md#substring) | 文字列から、指定された位置から始まる文字を返します。 |
| [toLower](control-flow-expression-language-functions.md#toLower) | 小文字の形式で文字列を返します。 |
| [toUpper](control-flow-expression-language-functions.md#toUpper) | 大文字の形式で文字列を返します。 |
| [trim](control-flow-expression-language-functions.md#trim) | 文字列から先頭と末尾の空白を削除し、更新された文字列を返します。 |

## <a name="collection-functions"></a>コレクション関数

コレクション (通常は配列や文字列、場合によってはディクショナリ) を操作するには、以下のコレクション関数を使用できます。

| コレクション関数 | タスク |
| ------------------- | ---- |
| [contains](control-flow-expression-language-functions.md#contains) | コレクションに特定の項目があるかどうかを確認します。 |
| [empty](control-flow-expression-language-functions.md#empty) | コレクションが空かどうかを調べます。 |
| [first](control-flow-expression-language-functions.md#first) | コレクションから最初の項目を返します。 |
| [intersection](control-flow-expression-language-functions.md#intersection) | 指定したコレクションすべてに共通する項目 "*のみ*" を含むコレクションを返します。 |
| [join](control-flow-expression-language-functions.md#join) | 配列の "*すべて*" の項目を含み、指定された区切り記号で各項目が区切られた、文字列を返します。 |
| [last](control-flow-expression-language-functions.md#last) | コレクションから最後の項目を返します。 |
| [length](control-flow-expression-language-functions.md#length) | 文字列または配列内の項目の数を返します。 |
| [skip](control-flow-expression-language-functions.md#skip) | コレクションの先頭から項目を削除し、"*他のすべて*" の項目を返します。 |
| [take](control-flow-expression-language-functions.md#take) | コレクションの先頭から項目を返します。 |
| [union](control-flow-expression-language-functions.md#union) | 指定した複数のコレクションの "*すべての*" 項目を含む 1 つのコレクションを返します。 | 

## <a name="logical-functions"></a>論理関数  

これらの関数は条件の内部で役立ち、任意の種類のロジックを評価するために使用できます。  
  
| 論理比較関数 | タスク |
| --------------------------- | ---- |
| [and](control-flow-expression-language-functions.md#and) | すべての式が true かどうかを調べます。 |
| [equals](control-flow-expression-language-functions.md#equals) | 両方の値が等しいかどうかを調べます。 |
| [greater](control-flow-expression-language-functions.md#greater) | 1 番目の値が 2 番目の値より大きいかどうかを調べます。 |
| [greaterOrEquals](control-flow-expression-language-functions.md#greaterOrEquals) | 1 番目の値が 2 番目の値以上かどうかを調べます。 |
| [if](control-flow-expression-language-functions.md#if) | 式が true か false かを調べます。 結果に基づき、指定された値を返します。 |
| [less](control-flow-expression-language-functions.md#less) | 1 番目の値が 2 番目の値より小さいかどうかを調べます。 |
| [lessOrEquals](control-flow-expression-language-functions.md#lessOrEquals) | 1 番目の値が 2 番目の値以下かどうかを調べます。 |
| [not](control-flow-expression-language-functions.md#not) | 式が false かどうかを調べます。 |
| [or](control-flow-expression-language-functions.md#or) | 少なくとも 1 つの式が true かどうかを調べます。 |
  
## <a name="conversion-functions"></a>変換関数  

 これらの関数は、言語の各ネイティブ型の間の変換に使われます。  
-   string
-   整数 (integer)
-   float
-   boolean
-   arrays
-   dictionaries

| 変換関数 | タスク |
| ------------------- | ---- |
| [array](control-flow-expression-language-functions.md#array) | 指定した 1 つの入力から配列を返します。 複数の入力の場合は、[createArray](control-flow-expression-language-functions.md#createArray) をご覧ください。 |
| [base64](control-flow-expression-language-functions.md#base64) | 文字列の base64 エンコード バージョンを返します。 |
| [base64ToBinary](control-flow-expression-language-functions.md#base64ToBinary) | base64 エンコード文字列のバイナリ バージョンを返します。 |
| [base64ToString](control-flow-expression-language-functions.md#base64ToString) | base64 エンコード文字列の文字列バージョンを返します。 |
| [[バイナリ]](control-flow-expression-language-functions.md#binary) | 入力値のバイナリ バージョンを返します。 |
| [bool](control-flow-expression-language-functions.md#bool) | 入力値のブール値バージョンを返します。 |
| [coalesce](control-flow-expression-language-functions.md#coalesce) | 1 つまたは複数のパラメーターから、最初の null 以外の値を返します。 |
| [createArray](control-flow-expression-language-functions.md#createArray) | 複数の入力から配列を作成して返します。 |
| [dataUri](control-flow-expression-language-functions.md#dataUri) | 入力値のデータ URI を返します。 |
| [dataUriToBinary](control-flow-expression-language-functions.md#dataUriToBinary) | データ URI のバイナリ バージョンを返します。 |
| [dataUriToString](control-flow-expression-language-functions.md#dataUriToString) | データ URI の文字列バージョンを返します。 |
| [decodeBase64](control-flow-expression-language-functions.md#decodeBase64) | base64 エンコード文字列の文字列バージョンを返します。 |
| [decodeDataUri](control-flow-expression-language-functions.md#decodeDataUri) | データ URI のバイナリ バージョンを返します。 |
| [decodeUriComponent](control-flow-expression-language-functions.md#decodeUriComponent) | エスケープ文字をデコード バージョンに置き換えた文字列を返します。 |
| [encodeUriComponent](control-flow-expression-language-functions.md#encodeUriComponent) | URL の安全でない文字をエスケープ文字に置き換えた文字列を返します。 |
| [float](control-flow-expression-language-functions.md#float) | 入力値の浮動小数点数を返します。 |
| [int](control-flow-expression-language-functions.md#int) | 文字列の整数バージョンを返します。 |
| [json](control-flow-expression-language-functions.md#json) | 文字列または XML に対する JSON (JavaScript Object Notation) 型の値またはオブジェクトを返します。 |
| [string](control-flow-expression-language-functions.md#string) | 入力値の文字列バージョンを返します。 |
| [uriComponent](control-flow-expression-language-functions.md#uriComponent) | URL の安全でない文字がエスケープ文字に置き換えられた、入力値の URI エンコード バージョンを返します。 |
| [uriComponentToBinary](control-flow-expression-language-functions.md#uriComponentToBinary) | URI エンコード文字列のバイナリ バージョンを返します。 |
| [uriComponentToString](control-flow-expression-language-functions.md#uriComponentToString) | URI エンコード文字列の文字列バージョンを返します。 |
| [xml](control-flow-expression-language-functions.md#xml) | 文字列の XML バージョンを返します。 |
| [xpath](control-flow-expression-language-functions.md#xpath) | XML で XPath (XML Path Language) 式と一致するノードまたは値を調べて、一致するノードまたは値を返します。 |

## <a name="math-functions"></a>算術関数  
 これらの関数は、**integer** 型および **float** 型の値に使うことができます。  

| 算術関数 | タスク |
| ------------- | ---- |
| [add](control-flow-expression-language-functions.md#add) | 2 つの数値を加算した結果を返します。 |
| [div](control-flow-expression-language-functions.md#div) | 2 つの数値を除算した結果を返します。 |
| [max](control-flow-expression-language-functions.md#max) | 数値のセットまたは配列から最大の値を返します。 |
| [min](control-flow-expression-language-functions.md#min) | 数値のセットまたは配列から最小の値を返します。 |
| [mod](control-flow-expression-language-functions.md#mod) | 2 つの数値を除算した剰余を返します。 |
| [mul](control-flow-expression-language-functions.md#mul) | 2 つの数値を乗算した積を返します。 |
| [rand](control-flow-expression-language-functions.md#rand) | 指定された範囲からランダムな整数を返します。 |
| [range](control-flow-expression-language-functions.md#range) | 指定した整数から始まる整数の配列を返します。 |
| [sub](control-flow-expression-language-functions.md#sub) | 1 番目の数値から 2 番目の数値を減算して、結果を返します。 |
  
## <a name="date-functions"></a>データ関数  

| 日付または時刻の関数 | タスク |
| --------------------- | ---- |
| [addDays](control-flow-expression-language-functions.md#addDays) | タイムスタンプに日数を加算します。 |
| [addHours](control-flow-expression-language-functions.md#addHours) | タイムスタンプに時間数を加算します。 |
| [addMinutes](control-flow-expression-language-functions.md#addMinutes) | タイムスタンプに分数を加算します。 |
| [addSeconds](control-flow-expression-language-functions.md#addSeconds) | タイムスタンプに秒数を加算します。 |
| [addToTime](control-flow-expression-language-functions.md#addToTime) | タイムスタンプに時間単位数を加算します。 [getFutureTime](control-flow-expression-language-functions.md#getFutureTime) もご覧ください。 |
| [convertFromUtc](control-flow-expression-language-functions.md#convertFromUtc) | タイムスタンプを協定世界時 (UTC) からターゲット タイム ゾーンに変換します。 |
| [convertTimeZone](control-flow-expression-language-functions.md#convertTimeZone) | タイムスタンプをソース タイム ゾーンからターゲット タイム ゾーンに変換します。 |
| [convertToUtc](control-flow-expression-language-functions.md#convertToUtc) | タイムスタンプをソース タイム ゾーンから協定世界時 (UTC) に変換します。 |
| [dayOfMonth](control-flow-expression-language-functions.md#dayOfMonth) | タイムスタンプから月コンポーネントの日付を返します。 |
| [dayOfWeek](control-flow-expression-language-functions.md#dayOfWeek) | タイムスタンプから曜日を返します。 |
| [dayOfYear](control-flow-expression-language-functions.md#dayOfYear) | タイムスタンプから年の何日目かを返します。 |
| [formatDateTime](control-flow-expression-language-functions.md#formatDateTime) | 任意の形式でタイムスタンプを文字列として返します。 |
| [getFutureTime](control-flow-expression-language-functions.md#getFutureTime) | 現在のタイムスタンプに指定した時刻単位を加えて返します。 [addToTime](control-flow-expression-language-functions.md#addToTime) もご覧ください。 |
| [getPastTime](control-flow-expression-language-functions.md#getPastTime) | 現在のタイムスタンプから指定した時刻単位を引いて返します。 [subtractFromTime](control-flow-expression-language-functions.md#subtractFromTime) もご覧ください。 |
| [startOfDay](control-flow-expression-language-functions.md#startOfDay) | タイムスタンプの日の開始日時を返します。 |
| [startOfHour](control-flow-expression-language-functions.md#startOfHour) | タイムスタンプの時刻の開始を返します。 |
| [startOfMonth](control-flow-expression-language-functions.md#startOfMonth) | タイムスタンプの月の開始を返します。 |
| [subtractFromTime](control-flow-expression-language-functions.md#subtractFromTime) | タイムスタンプから時間単位数を減算します。 [getPastTime](control-flow-expression-language-functions.md#getPastTime) もご覧ください。 |
| [ticks](control-flow-expression-language-functions.md#ticks) | 指定したタイムスタンプの `ticks` プロパティの値を返します。 |
| [utcNow](control-flow-expression-language-functions.md#utcNow) | 現在のタイムスタンプを文字列として返します。 |

## <a name="function-reference"></a>関数リファレンス

このセクションでは、使用可能なすべての関数をアルファベット順に示します。

<a name="add"></a>

### <a name="add"></a>add

2 つの数値を加算した結果を返します。

```
add(<summand_1>, <summand_2>)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*summand_1*>、<*summand_2*> | はい | 整数、浮動小数点数、または混合 | 加算する数値 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | -----| ----------- |
| <*result-sum*> | 整数または浮動小数点数 | 指定した数値を加算した結果 |
||||

*例*

この例は、指定した数値を加算します。

```
add(1, 1.5)
```

返される結果: `2.5`

<a name="addDays"></a>

### <a name="adddays"></a>addDays

タイムスタンプに日数を加算します。

```
addDays('<timestamp>', <days>, '<format>'?)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | はい | String | タイムスタンプを含む文字列。 |
| <*days*> | はい | Integer | 追加する正または負の日数 |
| <*format*> | いいえ | String | [単一の書式指定子](/dotnet/standard/base-types/standard-date-and-time-format-strings)または[カスタム書式パターン](/dotnet/standard/base-types/custom-date-and-time-format-strings)。 timestamp の既定の形式は ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK) です。これは、[ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) に準拠し、タイム ゾーン情報を保持します。 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | タイムスタンプに指定した日数を加えた値  |
||||

*例 1*

この例は、指定したタイムスタンプに 10 日を加算します。

```
addDays('2018-03-15T13:00:00Z', 10)
```

返される結果: `"2018-03-25T00:00:0000000Z"`

*例 2*

この例は、指定したタイムスタンプから 5 日を減算します。

```
addDays('2018-03-15T00:00:00Z', -5)
```

返される結果: `"2018-03-10T00:00:0000000Z"`

<a name="addHours"></a>

### <a name="addhours"></a>addHours

タイムスタンプに時間数を加算します。

```
addHours('<timestamp>', <hours>, '<format>'?)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | はい | String | タイムスタンプを含む文字列。 |
| <*hours*> | はい | Integer | 追加する正または負の時間数 |
| <*format*> | いいえ | String | [単一の書式指定子](/dotnet/standard/base-types/standard-date-and-time-format-strings)または[カスタム書式パターン](/dotnet/standard/base-types/custom-date-and-time-format-strings)。 timestamp の既定の形式は ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK) です。これは、[ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) に準拠し、タイム ゾーン情報を保持します。 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | タイムスタンプに指定した時間数を加えた値  |
||||

*例 1*

この例は、指定したタイムスタンプに 10 時間を加算します。

```
addHours('2018-03-15T00:00:00Z', 10)
```

返される結果: `"2018-03-15T10:00:0000000Z"`

*例 2*

この例は、指定したタイムスタンプから 5 時間を減算します。

```
addHours('2018-03-15T15:00:00Z', -5)
```

返される結果: `"2018-03-15T10:00:0000000Z"`

<a name="addMinutes"></a>

### <a name="addminutes"></a>addMinutes

タイムスタンプに分数を加算します。

```
addMinutes('<timestamp>', <minutes>, '<format>'?)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | はい | String | タイムスタンプを含む文字列。 |
| <*minutes*> | はい | Integer | 追加する正または負の分数 |
| <*format*> | いいえ | String | [単一の書式指定子](/dotnet/standard/base-types/standard-date-and-time-format-strings)または[カスタム書式パターン](/dotnet/standard/base-types/custom-date-and-time-format-strings)。 timestamp の既定の形式は ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK) です。これは、[ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) に準拠し、タイム ゾーン情報を保持します。 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | タイムスタンプに指定した分数を加えた値 |
||||

*例 1*

この例は、指定したタイムスタンプに 10 分を加算します。

```
addMinutes('2018-03-15T00:10:00Z', 10)
```

返される結果: `"2018-03-15T00:20:00.0000000Z"`

*例 2*

この例は、指定したタイムスタンプから 5 分を減算します。

```
addMinutes('2018-03-15T00:20:00Z', -5)
```

返される結果: `"2018-03-15T00:15:00.0000000Z"`

<a name="addSeconds"></a>

### <a name="addseconds"></a>addSeconds

タイムスタンプに秒数を加算します。

```
addSeconds('<timestamp>', <seconds>, '<format>'?)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | はい | String | タイムスタンプを含む文字列。 |
| <*seconds*> | はい | Integer | 追加する正または負の秒数 |
| <*format*> | いいえ | String | [単一の書式指定子](/dotnet/standard/base-types/standard-date-and-time-format-strings)または[カスタム書式パターン](/dotnet/standard/base-types/custom-date-and-time-format-strings)。 timestamp の既定の形式は ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK) です。これは、[ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) に準拠し、タイム ゾーン情報を保持します。 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | タイムスタンプに指定した秒数を加えた値  |
||||

*例 1*

この例は、指定したタイムスタンプに 10 秒を加算します。

```
addSeconds('2018-03-15T00:00:00Z', 10)
```

返される結果: `"2018-03-15T00:00:10.0000000Z"`

*例 2*

この例は、指定したタイムスタンプから 5 秒を減算します。

```
addSeconds('2018-03-15T00:00:30Z', -5)
```

返される結果: `"2018-03-15T00:00:25.0000000Z"`

<a name="addToTime"></a>

### <a name="addtotime"></a>addToTime

タイムスタンプに時間単位数を加算します。
[getFutureTime()](#getFutureTime) もご覧ください。

```
addToTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | はい | String | タイムスタンプを含む文字列。 |
| <*interval*> | はい | Integer | 追加する指定した時間単位の数 |
| <*timeUnit*> | はい | String | *間隔* と共に使用する時間単位:"Second"、"Minute"、"Hour"、"Day"、"Week"、"Month"、"Year" |
| <*format*> | いいえ | String | [単一の書式指定子](/dotnet/standard/base-types/standard-date-and-time-format-strings)または[カスタム書式パターン](/dotnet/standard/base-types/custom-date-and-time-format-strings)。 timestamp の既定の形式は ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK) です。これは、[ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) に準拠し、タイム ゾーン情報を保持します。 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | タイムスタンプに指定した時間単位数を加えた値  |
||||

*例 1*

この例は、指定したタイムスタンプに 1 日を加算します。

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day')
```

返される結果: `"2018-01-02T00:00:00.0000000Z"`

*例 2*

この例は、指定したタイムスタンプに 1 日を加算します。

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day', 'D')
```

省略可能な "D" 形式を使用して返される結果: `"Tuesday, January 2, 2018"`

<a name="and"></a>

### <a name="and"></a>and

両方の式が true であるかどうかを確認します。
両方の式が true の場合は true を返し、少なくとも 1 つの式が false の場合は false を返します。

```
and(<expression1>, <expression2>)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*expression1*>, <*expression2*> | はい | Boolean | 調べる式 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | -----| ----------- |
| true または false | Boolean | 両方の式が true の場合は true を返します。 少なくとも 1 つの式が false の場合は false を返します。 |
||||

*例 1*

これらの例では、指定されたブール値が両方とも true であるかどうかを確認します。

```
and(true, true)
and(false, true)
and(false, false)
```

次の結果を返します。

* 1 番目の例:両方の式が true なので、`true` を返します。
* 2 番目の例:片方の式が false なので、`false` を返します。
* 3 番目の例:両方の式が false なので、`false` を返します。

*例 2*

これらの例では、指定された式が両方とも true であるかどうかを確認します。

```
and(equals(1, 1), equals(2, 2))
and(equals(1, 1), equals(1, 2))
and(equals(1, 2), equals(1, 3))
```

次の結果を返します。

* 1 番目の例:両方の式が true なので、`true` を返します。
* 2 番目の例:片方の式が false なので、`false` を返します。
* 3 番目の例:両方の式が false なので、`false` を返します。

<a name="array"></a>

### <a name="array"></a>array

指定した 1 つの入力から配列を返します。
複数の入力の場合は、[createArray()](#createArray) をご覧ください。

```
array('<value>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | はい | String | 配列を作成するための文字列 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| [<*value*>] | Array | 指定した 1 つの入力を含む配列 |
||||

*例*

この例は、"hello" という文字列から配列を作成します。

```
array('hello')
```

返される結果: `["hello"]`

<a name="base64"></a>

### <a name="base64"></a>base64

文字列の base64 エンコード バージョンを返します。

```
base64('<value>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | はい | String | 入力文字列 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*base64-string*> | String | 入力文字列の base64 エンコード バージョン |
||||

*例*

この例は、"hello" という文字列を base64 エンコード文字列に変換します。

```
base64('hello')
```

返される結果: `"aGVsbG8="`

<a name="base64ToBinary"></a>

### <a name="base64tobinary"></a>base64ToBinary

base64 エンコード文字列のバイナリ バージョンを返します。

```
base64ToBinary('<value>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | はい | String | 変換する base64 エンコード文字列 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*binary-for-base64-string*> | String | base64 エンコード文字列のバイナリ バージョン |
||||

*例*

この例は、"aGVsbG8=" という base64 エンコード文字列をバイナリ文字列に変換します。

```
base64ToBinary('aGVsbG8=')
```

返される結果:

`"0110000101000111010101100111001101100010010001110011100000111101"`

<a name="base64ToString"></a>

### <a name="base64tostring"></a>base64ToString

base64 エンコード文字列の文字列バージョンを返し、実質的に base64 の文字列をデコードします。
[decodeBase64()](#decodeBase64) ではなく、この関数を使用してください。
どちらの関数も機能は同じですが、`base64ToString()` をお勧めします。

```
base64ToString('<value>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | はい | String | デコードする base64 エンコード文字列 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*decoded-base64-string*> | String | base64 エンコード文字列の文字列バージョン。 |
||||

*例*

この例は、"aGVsbG8=" という base64 エンコード文字列を単なる文字列に変換します。

```
base64ToString('aGVsbG8=')
```

返される結果: `"hello"`

<a name="binary"></a>

### <a name="binary"></a>binary

文字列のバイナリ バージョンを返します。

```
binary('<value>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | はい | String | 変換する文字列 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*binary-for-input-value*> | String | 指定した文字列のバイナリ バージョン |
||||

*例*

この例は、"hello" という文字列をバイナリ文字列に変換します。

```
binary('hello')
```

返される結果:

`"0110100001100101011011000110110001101111"`

<a name="bool"></a>

### <a name="bool"></a>[bool]

値のブール値バージョンを返します。

```
bool(<value>)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | はい | Any | 変換する値 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| true または false | Boolean | 指定した値のブール値バージョン |
||||

*例*

これらの例は、指定した値をブール値に変換します。

```
bool(1)
bool(0)
```

次の結果を返します。

* 1 番目の例: `true`
* 2 番目の例: `false`

<a name="coalesce"></a>

### <a name="coalesce"></a>coalesce

1 つまたは複数のパラメーターから、最初の null 以外の値を返します。
空の文字列、空の配列、空のオブジェクトは null ではありません。

```
coalesce(<object_1>, <object_2>, ...)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*object_1*>, <*object_2*>, ... | はい | 任意、型が混在してもかまいません | null かどうか調べる 1 つまたは複数の項目 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*first-non-null-item*> | Any | null ではない最初の項目または値。 すべてのパラメーターが null の場合、この関数は null を返します。 |
||||

*例*

これらの例は、指定した値から最初の null 以外の値を返し、すべての値が null のときは null を返します。

```
coalesce(null, true, false)
coalesce(null, 'hello', 'world')
coalesce(null, null, null)
```

次の結果を返します。

* 1 番目の例: `true`
* 2 番目の例: `"hello"`
* 3 番目の例: `null`

<a name="concat"></a>

### <a name="concat"></a>concat

2 つ以上の文字列を結合し、結合された文字列を返します。

```
concat('<text1>', '<text2>', ...)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*text1*>, <*text2*>, ... | はい | String | 結合する少なくとも 2 つの文字列 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*text1text2...* > | String | 入力文字列を結合して作成された文字列 |
||||

*例*

この例は、文字列 "Hello" と "World" を結合します。

```
concat('Hello', 'World')
```

返される結果: `"HelloWorld"`

<a name="contains"></a>

### <a name="contains"></a>contains

コレクションに特定の項目があるかどうかを確認します。
項目が見つかった場合は true を返し、見つからない場合は false を返します。
この関数は、大文字と小文字を区別します。

```
contains('<collection>', '<value>')
contains([<collection>], '<value>')
```

具体的には、この関数は次のコレクション型で動作します。

* "*文字列*" からの "*部分文字列*" の検索
* "*配列*" からの "*値*" の検索
* "*ディクショナリ*" からの "*キー*" の検索

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*collection*> | はい | 文字列、配列、ディクショナリ | 調べるコレクション |
| <*value*> | はい | それぞれ文字列、配列、ディクショナリ | 検索する項目 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| true または false | Boolean | 項目が見つかった場合は true を返します。 見つからなかった場合は false を返します。 |
||||

*例 1*

この例は、文字列 "hello world" で部分文字列 "world" を調べて、true を返します。

```
contains('hello world', 'world')
```

*例 2*

この例は、文字列 "hello world" で部分文字列 "universe" を調べて、false を返します。

```
contains('hello world', 'universe')
```

<a name="convertFromUtc"></a>

### <a name="convertfromutc"></a>convertFromUtc

タイムスタンプを協定世界時 (UTC) からターゲット タイム ゾーンに変換します。

```
convertFromUtc('<timestamp>', '<destinationTimeZone>', '<format>'?)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | はい | String | タイムスタンプを含む文字列。 |
| <*destinationTimeZone*> | はい | String | ターゲット タイム ゾーンの名前。 タイム ゾーン名については、「[マイクロソフトのタイム ゾーンのインデックス値](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values)」を参照してください。ただし、タイム ゾーン名から句読点を削除することが必要な場合があります。 |
| <*format*> | いいえ | String | [単一の書式指定子](/dotnet/standard/base-types/standard-date-and-time-format-strings)または[カスタム書式パターン](/dotnet/standard/base-types/custom-date-and-time-format-strings)。 timestamp の既定の形式は ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK) です。これは、[ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) に準拠し、タイム ゾーン情報を保持します。 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*converted-timestamp*> | String | ターゲット タイム ゾーンに変換されたタイムスタンプ |
||||

*例 1*

この例は、タイムスタンプを指定したタイム ゾーンに変換します。

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time')
```

返される結果: `"2018-01-01T00:00:00Z"`

*例 2*

この例は、タイムスタンプを指定したタイム ゾーンに変換して形式を設定します。

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time', 'D')
```

返される結果: `"Monday, January 1, 2018"`

<a name="convertTimeZone"></a>

### <a name="converttimezone"></a>convertTimeZone

タイムスタンプをソース タイム ゾーンからターゲット タイム ゾーンに変換します。

```
convertTimeZone('<timestamp>', '<sourceTimeZone>', '<destinationTimeZone>', '<format>'?)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | はい | String | タイムスタンプを含む文字列。 |
| <*sourceTimeZone*> | はい | String | ソース タイム ゾーンの名前。 タイム ゾーン名については、「[マイクロソフトのタイム ゾーンのインデックス値](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values)」を参照してください。ただし、タイム ゾーン名から句読点を削除することが必要な場合があります。 |
| <*destinationTimeZone*> | はい | String | ターゲット タイム ゾーンの名前。 タイム ゾーン名については、「[マイクロソフトのタイム ゾーンのインデックス値](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values)」を参照してください。ただし、タイム ゾーン名から句読点を削除することが必要な場合があります。 |
| <*format*> | いいえ | String | [単一の書式指定子](/dotnet/standard/base-types/standard-date-and-time-format-strings)または[カスタム書式パターン](/dotnet/standard/base-types/custom-date-and-time-format-strings)。 timestamp の既定の形式は ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK) です。これは、[ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) に準拠し、タイム ゾーン情報を保持します。 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*converted-timestamp*> | String | ターゲット タイム ゾーンに変換されたタイムスタンプ |
||||

*例 1*

この例は、ソース タイム ゾーンをターゲット タイム ゾーンに変換します。

```
convertTimeZone('2018-01-01T08:00:00.0000000Z', 'UTC', 'Pacific Standard Time')
```

返される結果: `"2018-01-01T00:00:00.0000000"`

*例 2*

この例は、タイム ゾーンを指定したタイム ゾーンに変換して形式を設定します。

```
convertTimeZone('2018-01-01T08:00:00.0000000Z', 'UTC', 'Pacific Standard Time', 'D')
```

返される結果: `"Monday, January 1, 2018"`

<a name="convertToUtc"></a>

### <a name="converttoutc"></a>convertToUtc

タイムスタンプをソース タイム ゾーンから協定世界時 (UTC) に変換します。

```
convertToUtc('<timestamp>', '<sourceTimeZone>', '<format>'?)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | はい | String | タイムスタンプを含む文字列。 |
| <*sourceTimeZone*> | はい | String | ソース タイム ゾーンの名前。 タイム ゾーン名については、「[マイクロソフトのタイム ゾーンのインデックス値](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values)」を参照してください。ただし、タイム ゾーン名から句読点を削除することが必要な場合があります。 |
| <*format*> | いいえ | String | [単一の書式指定子](/dotnet/standard/base-types/standard-date-and-time-format-strings)または[カスタム書式パターン](/dotnet/standard/base-types/custom-date-and-time-format-strings)。 timestamp の既定の形式は ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK) です。これは、[ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) に準拠し、タイム ゾーン情報を保持します。 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*converted-timestamp*> | String | UTC に変換されたタイムスタンプ |
||||

*例 1*

この例は、タイムスタンプを UTC に変換します。

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time')
```

返される結果: `"2018-01-01T08:00:00.0000000Z"`

*例 2*

この例は、タイムスタンプを UTC に変換します。

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time', 'D')
```

返される結果: `"Monday, January 1, 2018"`

<a name="createArray"></a>

### <a name="createarray"></a>createArray

複数の入力から配列を作成して返します。
単一入力の配列については、[array()](#array) をご覧ください。

```
createArray('<object1>', '<object2>', ...)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*object1*>, <*object2*>, ... | はい | 任意、ただし混在していてはなりません | 配列を作成する少なくとも 2 つの項目 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| [<*object1*>, <*object2*>, ...] | Array | すべての入力項目から作成された配列 |
||||

*例*

この例は、以下の入力から配列を作成します。

```
createArray('h', 'e', 'l', 'l', 'o')
```

返される結果: `["h", "e", "l", "l", "o"]`

<a name="dataUri"></a>

### <a name="datauri"></a>dataUri

文字列のデータ URI (Uniform Resource Identifier) を返します。

```
dataUri('<value>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | はい | String | 変換する文字列 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*data-uri*> | String | 入力文字列に対するデータ URI |
||||

*例*

この例は、"hello" という文字列に対する URI を作成します。

```
dataUri('hello')
```

返される結果: `"data:text/plain;charset=utf-8;base64,aGVsbG8="`

<a name="dataUriToBinary"></a>

### <a name="datauritobinary"></a>dataUriToBinary

データ URI (Uniform Resource Identifier) のバイナリ バージョンを返します。
[decodeDataUri()](#decodeDataUri) ではなく、この関数を使用してください。
どちらの関数も機能は同じですが、`dataUriBinary()` をお勧めします。

```
dataUriToBinary('<value>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | はい | String | 変換するデータ URI |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*binary-for-data-uri*> | String | データ URI のバイナリ バージョン |
||||

*例*

この例は、次のデータ URI のバイナリ バージョンを作成します。

```
dataUriToBinary('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

返される結果:

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="dataUriToString"></a>

### <a name="datauritostring"></a>dataUriToString

データ URI (Uniform Resource Identifier) の文字列バージョンを返します。

```
dataUriToString('<value>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | はい | String | 変換するデータ URI |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*string-for-data-uri*> | String | データ URI の文字列バージョン |
||||

*例*

この例は、次のデータ URI の文字列を作成します。

```
dataUriToString('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

返される結果: `"hello"`

<a name="dayOfMonth"></a>

### <a name="dayofmonth"></a>dayOfMonth

タイムスタンプから月の日付を返します。

```
dayOfMonth('<timestamp>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | はい | String | タイムスタンプを含む文字列。 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*day-of-month*> | Integer | 指定したタイムスタンプの月の日付 |
||||

*例*

この例は、次のタイムスタンプから月の日付を返します。

```
dayOfMonth('2018-03-15T13:27:36Z')
```

返される結果: `15`

<a name="dayOfWeek"></a>

### <a name="dayofweek"></a>dayOfWeek

タイムスタンプから曜日を返します。

```
dayOfWeek('<timestamp>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | はい | String | タイムスタンプを含む文字列。 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*day-of-week*> | Integer | 指定したタイムスタンプの曜日。日曜日は 0、月曜日は 1、などとなります。 |
||||

*例*

この例は、次のタイムスタンプから曜日を返します。

```
dayOfWeek('2018-03-15T13:27:36Z')
```

返される結果: `3`

<a name="dayOfYear"></a>

### <a name="dayofyear"></a>dayOfYear

タイムスタンプから年の何日目かを返します。

```
dayOfYear('<timestamp>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | はい | String | タイムスタンプを含む文字列。 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*day-of-year*> | Integer | 指定したタイムスタンプの年初からの通算日数 |
||||

*例*

この例は、次のタイムスタンプから年の何日目かを返します。

```
dayOfYear('2018-03-15T13:27:36Z')
```

返される結果: `74`

<a name="decodeBase64"></a>

### <a name="decodebase64"></a>decodeBase64

base64 エンコード文字列の文字列バージョンを返し、実質的に base64 の文字列をデコードします。
`decodeBase64()` ではなく、[base64ToString()](#base64ToString) を使うようにしてください。
どちらの関数も機能は同じですが、`base64ToString()` をお勧めします。

```
decodeBase64('<value>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | はい | String | デコードする base64 エンコード文字列 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*decoded-base64-string*> | String | base64 エンコード文字列の文字列バージョン。 |
||||

*例*

この例は、base64 エンコード文字列の文字列を作成します。

```
decodeBase64('aGVsbG8=')
```

返される結果: `"hello"`

<a name="decodeDataUri"></a>

### <a name="decodedatauri"></a>decodeDataUri

データ URI (Uniform Resource Identifier) のバイナリ バージョンを返します。
`decodeDataUri()` ではなく、[dataUriToBinary()](#dataUriToBinary) を使うようにしてください。
どちらの関数も機能は同じですが、`dataUriToBinary()` をお勧めします。

```
decodeDataUri('<value>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | はい | String | デコードするデータ URI 文字列 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*binary-for-data-uri*> | String | データ URI 文字列のバイナリ バージョン |
||||

*例*

この例は、次のデータ URI のバイナリ バージョンを返します。

```
decodeDataUri('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

返される結果:

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="decodeUriComponent"></a>

### <a name="decodeuricomponent"></a>decodeUriComponent

エスケープ文字をデコード バージョンに置き換えた文字列を返します。

```
decodeUriComponent('<value>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | はい | String | エスケープ文字をデコードする文字列 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*decoded-uri*> | String | エスケープ文字がデコードされた更新後の文字列 |
||||

*例*

この例は、次の文字列内のエスケープ文字をデコード バージョンに置き換えます。

```
decodeUriComponent('http%3A%2F%2Fcontoso.com')
```

返される結果: `"https://contoso.com"`

<a name="div"></a>

### <a name="div"></a>div

2 つの数値を除算した結果の整数値を返します。
結果の残りの部分を取得するには、[mod()](#mod) をご覧ください。

```
div(<dividend>, <divisor>)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*dividend*> | はい | 整数または浮動小数点数 | *divisor* によって除算される値。 |
| <*divisor*> | はい | 整数または浮動小数点数 | *dividend* を除算する値。0 にすることはできません。 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*quotient-result*> | Integer | 1 番目の数値を 2 番目の数値で除算した結果の整数値 |
||||

*例*

どちらの例も、1 番目の数値を 2 番目の数値で除算します。

```
div(10, 5)
div(11, 5)
```

返される結果: `2`

<a name="encodeUriComponent"></a>

### <a name="encodeuricomponent"></a>encodeUriComponent

URL の安全でない文字がエスケープ文字に置き換えられた、文字列の URI (Uniform Resource Identifier) エンコード バージョンを返します。
`encodeUriComponent()` ではなく、[uriComponent()](#uriComponent) を使うようにしてください。
どちらの関数も機能は同じですが、`uriComponent()` をお勧めします。

```
encodeUriComponent('<value>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | はい | String | URI エンコード形式に変換する文字列 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*encoded-uri*> | String | エスケープ文字が使われている URI エンコード文字列 |
||||

*例*

この例は、次の文字列の URI エンコード バージョンを作成します。

```
encodeUriComponent('https://contoso.com')
```

返される結果: `"http%3A%2F%2Fcontoso.com"`

<a name="empty"></a>

### <a name="empty"></a>empty

コレクションが空かどうかを調べます。
コレクションが空の場合は true を返し、空でない場合は false を返します。

```
empty('<collection>')
empty([<collection>])
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*collection*> | はい | 文字列、配列、オブジェクト | 調べるコレクション |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| true または false | Boolean | コレクションが空の場合は true を返します。 空でない場合は false を返します。 |
||||

*例*

これらの例は、指定したコレクションが空かどうかを調べます。

```
empty('')
empty('abc')
```

次の結果を返します。

* 1 番目の例:空の文字列を渡しているので、関数は `true` を返します。
* 2 番目の例:文字列 "abc" を渡しているので、関数は `false` を返します。

<a name="endswith"></a>

### <a name="endswith"></a>endsWith

文字列が特定の部分文字列で終わっているかどうかを調べます。
部分文字列が見つかった場合は true を返し、見つからない場合は false を返します。
この関数は、大文字と小文字を区別しません。

```
endsWith('<text>', '<searchText>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*text*> | はい | String | 調べる文字列。 |
| <*searchText*> | はい | String | 検索する末尾の部分文字列 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| true または false  | Boolean | 末尾の部分文字列が見つかった場合は true を返します。 見つからなかった場合は false を返します。 |
||||

*例 1*

この例は、文字列 "hello world" が文字列 "world" で終わっているかどうかを調べます。

```
endsWith('hello world', 'world')
```

返される結果: `true`

*例 2*

この例は、文字列 "hello world" が文字列 "universe" で終わっているかどうかを調べます。

```
endsWith('hello world', 'universe')
```

返される結果: `false`

<a name="equals"></a>

### <a name="equals"></a>equals

両方の値、式、またはオブジェクトが等しいかどうかを調べます。
両方が等しい場合は true を返し、等しくない場合は false を返します。

```
equals('<object1>', '<object2>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*object1*>, <*object2*> | はい | 各種 | 比較する値、式、またはオブジェクト |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| true または false | Boolean | 両方が等しい場合は true を返します。 等しくない場合は false を返します。 |
||||

*例*

これらの例は、指定した入力が等しいかどうかを調べます。

```
equals(true, 1)
equals('abc', 'abcd')
```

次の結果を返します。

* 1 番目の例:両方の値が等しいので、関数は `true` を返します。
* 2 番目の例:両方の値が等しくないので、関数は `false` を返します。

<a name="first"></a>

### <a name="first"></a>first

文字列または配列から最初の項目を返します。

```
first('<collection>')
first([<collection>])
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*collection*> | はい | 文字列、配列 | 最初の項目を検索するコレクション |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*first-collection-item*> | Any | コレクション内の最初の項目 |
||||

*例*

これらの例は、以下のコレクション内の最初の項目を検索します。

```
first('hello')
first(createArray(0, 1, 2))
```

次の結果を返します。

* 1 番目の例: `"h"`
* 2 番目の例: `0`

<a name="float"></a>

### <a name="float"></a>float

浮動小数点数の文字列バージョンを実際の浮動小数点数に変換します。

```
float('<value>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | はい | String | 変換する有効な浮動小数点数を含む文字列 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*float-value*> | Float | 指定した文字列の浮動小数点数 |
||||

*例*

この例は、次の浮動小数点数の文字列バージョンを作成します。

```
float('10.333')
```

返される結果: `10.333`

<a name="formatDateTime"></a>

### <a name="formatdatetime"></a>formatDateTime

指定した形式でタイムスタンプを返します。

```
formatDateTime('<timestamp>', '<format>'?)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | はい | String | タイムスタンプを含む文字列。 |
| <*format*> | いいえ | String | [単一の書式指定子](/dotnet/standard/base-types/standard-date-and-time-format-strings)または[カスタム書式パターン](/dotnet/standard/base-types/custom-date-and-time-format-strings)。 timestamp の既定の形式は ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK) です。これは、[ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) に準拠し、タイム ゾーン情報を保持します。 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*reformatted-timestamp*> | String | 指定した形式に更新されたタイムスタンプ。 |
||||

*例*

この例は、タイムスタンプを指定した形式に変換します。

```
formatDateTime('03/15/2018 12:00:00', 'yyyy-MM-ddTHH:mm:ss')
```

返される結果: `"2018-03-15T12:00:00"`

<a name="getFutureTime"></a>

### <a name="getfuturetime"></a>getFutureTime

現在のタイムスタンプに指定した時刻単位を加えて返します。

```
getFutureTime(<interval>, <timeUnit>, <format>?)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*interval*> | はい | Integer | 追加する指定した時間単位の数 |
| <*timeUnit*> | はい | String | *間隔* と共に使用する時間単位:"Second"、"Minute"、"Hour"、"Day"、"Week"、"Month"、"Year" |
| <*format*> | いいえ | String | [単一の書式指定子](/dotnet/standard/base-types/standard-date-and-time-format-strings)または[カスタム書式パターン](/dotnet/standard/base-types/custom-date-and-time-format-strings)。 timestamp の既定の形式は ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK) です。これは、[ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) に準拠し、タイム ゾーン情報を保持します。 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | 現在のタイムスタンプに指定した時間単位数を加えた値 |
||||

*例 1*

現在のタイムスタンプが "2018-03-01T00:00:00.0000000Z" であるものとします。
この例は、そのタイムスタンプに 5 日を加算します。

```
getFutureTime(5, 'Day')
```

返される結果: `"2018-03-06T00:00:00.0000000Z"`

*例 2*

現在のタイムスタンプが "2018-03-01T00:00:00.0000000Z" であるものとします。
この例は、5 日を加算して、結果を "D" 形式に変換します。

```
getFutureTime(5, 'Day', 'D')
```

返される結果: `"Tuesday, March 6, 2018"`

<a name="getPastTime"></a>

### <a name="getpasttime"></a>getPastTime

現在のタイムスタンプから指定した時刻単位を引いて返します。

```
getPastTime(<interval>, <timeUnit>, <format>?)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*interval*> | はい | Integer | 減算する指定した時間単位の数 |
| <*timeUnit*> | はい | String | *間隔* と共に使用する時間単位:"Second"、"Minute"、"Hour"、"Day"、"Week"、"Month"、"Year" |
| <*format*> | いいえ | String | [単一の書式指定子](/dotnet/standard/base-types/standard-date-and-time-format-strings)または[カスタム書式パターン](/dotnet/standard/base-types/custom-date-and-time-format-strings)。 timestamp の既定の形式は ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK) です。これは、[ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) に準拠し、タイム ゾーン情報を保持します。 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | 現在のタイムスタンプから指定した時間単位数を引いた値 |
||||

*例 1*

現在のタイムスタンプが "2018-02-01T00:00:00.0000000Z" であるものとします。
この例は、そのタイムスタンプから 5 日を減算します。

```
getPastTime(5, 'Day')
```

返される結果: `"2018-01-27T00:00:00.0000000Z"`

*例 2*

現在のタイムスタンプが "2018-02-01T00:00:00.0000000Z" であるものとします。
この例は、5 日を減算して、結果を "D" 形式に変換します。

```
getPastTime(5, 'Day', 'D')
```

返される結果: `"Saturday, January 27, 2018"`

<a name="greater"></a>

### <a name="greater"></a>greater

1 番目の値が 2 番目の値より大きいかどうかを調べます。
1 番目の値の方が大きい場合は true を返し、大きくない場合は false を返します。

```
greater(<value>, <compareTo>)
greater('<value>', '<compareTo>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | はい | 整数、浮動小数点数、混合 | 2 番目の値より大きいかどうかを調べる 1 番目の値 |
| <*compareTo*> | はい | それぞれ整数、浮動小数点数、混合 | 比較する値 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| true または false | Boolean | 1 番目の値が 2 番目の値より大きい場合は true を返します。 1 番目の値が 2 番目の値以下の場合は false を返します。 |
||||

*例*

これらの例は、1 番目の値が 2 番目の値より大きいかどうかを調べます。

```
greater(10, 5)
greater('apple', 'banana')
```

次の結果を返します。

* 1 番目の例: `true`
* 2 番目の例: `false`

<a name="greaterOrEquals"></a>

### <a name="greaterorequals"></a>greaterOrEquals

1 番目の値が 2 番目の値以上かどうかを調べます。
1 番目の値が大きいか等しい場合は true を返し、小さい場合は false を返します。

```
greaterOrEquals(<value>, <compareTo>)
greaterOrEquals('<value>', '<compareTo>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | はい | 整数、浮動小数点数、混合 | 2 番目の値以上かどうかを調べる 1 番目の値。 |
| <*compareTo*> | はい | それぞれ整数、浮動小数点数、混合 | 比較する値 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| true または false | Boolean | 1 番目の値が 2 番目の値より大きいか等しい場合は true を返します。 1 番目の値が 2 番目より小さい場合は false を返します。 |
||||

*例*

これらの例は、1 番目の値が 2 番目の値以上かどうかを調べます。

```
greaterOrEquals(5, 5)
greaterOrEquals('apple', 'banana')
```

次の結果を返します。

* 1 番目の例: `true`
* 2 番目の例: `false`

<a name="guid"></a>

### <a name="guid"></a>guid

グローバル一意識別子 (GUID) を文字列として生成します (例: "c2ecc88d-88c8-4096-912c-d6f2e2b138ce")。

```
guid()
```

既定の "D" 形式 (ハイフンで区切られた 32 桁) 以外の GUID 形式を指定することもできます。

```
guid('<format>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*format*> | いいえ | String | 返される GUID の単一の[形式指定子](/dotnet/api/system.guid.tostring)。 規定の形式は "D" ですが、"N"、"D"、"B"、"P"、"X" も指定できます。 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*GUID-value*> | String | ランダムに生成された GUID |
||||

*例*

この例は同じ GUID を生成しますが、32 桁で、ハイフンによって区切られており、かっこで囲まれています。

```
guid('P')
```

返される結果: `"(c2ecc88d-88c8-4096-912c-d6f2e2b138ce)"`

<a name="if"></a>

### <a name="if"></a>if

式が true か false かを調べます。
結果に基づき、指定された値を返します。

```
if(<expression>, <valueIfTrue>, <valueIfFalse>)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*expression*> | はい | Boolean | 調べる式。 |
| <*valueIfTrue*> | はい | Any | 式が true の場合に返す値 |
| <*valueIfFalse*> | はい | Any | 式が false の場合に返す値 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*specified-return-value*> | Any | 式が true か false かに基づいて返すように指定された値 |
||||

*例*

この例は、指定した式が true を返すため `"yes"` を返します。
それ以外の場合、この例は `"no"` を返します。

```
if(equals(1, 1), 'yes', 'no')
```

<a name="indexof"></a>

### <a name="indexof"></a>indexOf

部分文字列の開始位置またはインデックス値を返します。
この関数は大文字と小文字を区別せず、インデックスは値 0 から始まります。

```
indexOf('<text>', '<searchText>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*text*> | はい | String | 検索する部分文字列を含む文字列 |
| <*searchText*> | はい | String | 検索する部分文字列 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*index-value*>| Integer | 指定した部分文字列の開始位置またはインデックス値。 <p>文字列が見つからない場合は、値 -1 を返します。 |
||||

*例*

この例は、文字列 "hello world" 内で部分文字列 "world" の開始インデックス値を検索します。

```
indexOf('hello world', 'world')
```

返される結果: `6`

<a name="int"></a>

### <a name="int"></a>INT

文字列の整数バージョンを返します。

```
int('<value>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | はい | String | 変換する文字列 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*integer-result*> | Integer | 指定した文字列の整数バージョン |
||||

*例*

この例は、文字列 "10" の整数バージョンを作成します。

```
int('10')
```

返される結果: `10`

<a name="json"></a>

### <a name="json"></a>json

文字列または XML に対する JSON (JavaScript Object Notation) 型の値またはオブジェクトを返します。

```
json('<value>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | はい | 文字列、XML | 変換する文字列または XML |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*JSON-result*> | JSON ネイティブの型またはオブジェクト | 指定した文字列または XML に対する JSON ネイティブの型の値またはオブジェクト。 文字列が null の場合、この関数は空のオブジェクトを返します。 |
||||

*例 1*

この例は、次の文字列を JSON 値に変換します。

```
json('[1, 2, 3]')
```

返される結果: `[1, 2, 3]`

*例 2*

この例は、次の文字列を JSON に変換します。

```
json('{"fullName": "Sophia Owen"}')
```

返される結果:

```
{
  "fullName": "Sophia Owen"
}
```

*例 3*

この例は、次の XML を JSON に変換します。

```
json(xml('<?xml version="1.0"?> <root> <person id='1'> <name>Sophia Owen</name> <occupation>Engineer</occupation> </person> </root>'))
```

返される結果:

```json
{
   "?xml": { "@version": "1.0" },
   "root": {
      "person": [ {
         "@id": "1",
         "name": "Sophia Owen",
         "occupation": "Engineer"
      } ]
   }
}
```

<a name="intersection"></a>

### <a name="intersection"></a>intersection

指定したコレクションすべてに共通する項目 "*のみ*" を含むコレクションを返します。
結果に含まれるためには、この関数に渡されるすべてのコレクションに項目が含まれる必要があります。
1 つまたは複数の項目が同じ名前である場合は、その名前を持つ最後の項目が結果に含まれます。

```
intersection([<collection1>], [<collection2>], ...)
intersection('<collection1>', '<collection2>', ...)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*collection1*>, <*collection2*>, ... | はい | 配列またはオブジェクト、両方ともは不可 | 共通項目 "*のみ*" を抽出するコレクション |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*common-items*> | それぞれ、配列またはオブジェクト | 指定したコレクションすべてに共通する項目のみを含むコレクション |
||||

*例*

この例は、次のすべての配列に共通する項目を検索します。

```
intersection(createArray(1, 2, 3), createArray(101, 2, 1, 10), createArray(6, 8, 1, 2))
```

そして、それらの項目 `[1, 2]` "*のみ*" を含む配列を返します。

<a name="join"></a>

### <a name="join"></a>join

配列のすべての項目を含み、各文字が "*区切り記号*" で区切られた文字列を返します。

```
join([<collection>], '<delimiter>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*collection*> | はい | Array | 結合する項目を含む配列 |
| <*delimiter*> | はい | String | 結果の文字列内の各文字の間に挿入される区切り記号 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*char1*><*delimiter*><*char2*><*delimiter*>... | String | 指定した配列内のすべての項目から作成された結果の文字列 |
||||

*例*

この例は、指定した文字で区切られた、次の配列のすべての項目を含む文字列を作成します。

```
join(createArray('a', 'b', 'c'), '.')
```

返される結果: `"a.b.c"`

<a name="last"></a>

### <a name="last"></a>last

コレクションから最後の項目を返します。

```
last('<collection>')
last([<collection>])
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*collection*> | はい | 文字列、配列 | 最後の項目を検索するコレクション |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*last-collection-item*> | それぞれ文字列、配列 | コレクション内の最後の項目 |
||||

*例*

これらの例は、以下のコレクション内の最後の項目を検索します。

```
last('abcd')
last(createArray(0, 1, 2, 3))
```

次の結果を返します。

* 1 番目の例: `"d"`
* 2 番目の例: `3`

<a name="lastindexof"></a>

### <a name="lastindexof"></a>lastIndexOf

部分文字列の最後の出現箇所の開始位置またはインデックス値を返します。
この関数は大文字と小文字を区別せず、インデックスは値 0 から始まります。

```
lastIndexOf('<text>', '<searchText>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*text*> | はい | String | 検索する部分文字列を含む文字列 |
| <*searchText*> | はい | String | 検索する部分文字列 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*ending-index-value*> | Integer | 指定された部分文字列の最後の出現箇所の開始位置またはインデックス値。 <p>文字列が見つからない場合は、値 -1 を返します。 |
||||

*例*

この例は、文字列 "hello world" 内の部分文字列 "world" の最後の出現箇所の開始インデックス値を検索します。

```
lastIndexOf('hello world', 'world')
```

返される結果: `6`

<a name="length"></a>

### <a name="length"></a>length

コレクション内の項目の数を返します。

```
length('<collection>')
length([<collection>])
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*collection*> | はい | 文字列、配列 | 項目を数えるコレクション |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*length-or-count*> | Integer | コレクション内の項目数 |
||||

*例*

これらの例は、次のコレクション内の項目の数をカウントします。

```
length('abcd')
length(createArray(0, 1, 2, 3))
```

返される結果: `4`

<a name="less"></a>

### <a name="less"></a>less

1 番目の値が 2 番目の値より小さいかどうかを調べます。
1 番目の値の方が小さい場合は true を返し、小さくないい場合は false を返します。

```
less(<value>, <compareTo>)
less('<value>', '<compareTo>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | はい | 整数、浮動小数点数、混合 | 2 番目の値より小さいかどうかを調べる 1 番目の値 |
| <*compareTo*> | はい | それぞれ整数、浮動小数点数、混合 | 比較する項目 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| true または false | Boolean | 1 番目の値の方が 2 番目より小さい場合は true を返します。 1 番目の値が 2 番目の値以上の場合は false を返します。 |
||||

*例*

これらの例は、1 番目の値が 2 番目の値より小さいかどうかを調べます。

```
less(5, 10)
less('banana', 'apple')
```

次の結果を返します。

* 1 番目の例: `true`
* 2 番目の例: `false`

<a name="lessOrEquals"></a>

### <a name="lessorequals"></a>lessOrEquals

1 番目の値が 2 番目の値以下かどうかを調べます。
1 番目の値が小さいか等しい場合は true を返し、大きい場合は false を返します。

```
lessOrEquals(<value>, <compareTo>)
lessOrEquals('<value>', '<compareTo>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | はい | 整数、浮動小数点数、混合 | 2 番目の値以下かどうかを調べる 1 番目の値。 |
| <*compareTo*> | はい | それぞれ整数、浮動小数点数、混合 | 比較する項目 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| true または false  | Boolean | 1 番目の値が 2 番目の値より小さいか等しい場合は true を返します。 1 番目の値が 2 番目の値より大きい場合は false を返します。 |
||||

*例*

これらの例は、1 番目の値が 2 番目の値以下かどうかを調べます。

```
lessOrEquals(10, 10)
lessOrEquals('apply', 'apple')
```

次の結果を返します。

* 1 番目の例: `true`
* 2 番目の例: `false`

<a name="max"></a>

### <a name="max"></a>max

両端を含む数値のリストまたは配列から最大の値を返します。

```
max(<number1>, <number2>, ...)
max([<number1>, <number2>, ...])
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*number1*>, <*number2*>, ... | はい | 整数、浮動小数点数、または両方 | 最大値を取得する数値のセット |
| [<*number1*>, <*number2*>, ...] | はい | 配列 - 整数、浮動小数点数、または両方 | 最大値を取得する数値の配列 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*max-value*> | 整数または浮動小数点数 | 指定した配列内または数値セット内で最大の値 |
||||

*例*

これらの例は、数値のセットと配列から最大値を取得します。

```
max(1, 2, 3)
max(createArray(1, 2, 3))
```

返される結果: `3`

<a name="min"></a>

### <a name="min"></a>min

数値のセットまたは配列から最小の値を返します。

```
min(<number1>, <number2>, ...)
min([<number1>, <number2>, ...])
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*number1*>, <*number2*>, ... | はい | 整数、浮動小数点数、または両方 | 最小値を取得する数値のセット |
| [<*number1*>, <*number2*>, ...] | はい | 配列 - 整数、浮動小数点数、または両方 | 最小値を取得する数値の配列 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*min-value*> | 整数または浮動小数点数 | 指定した数値セット内または配列内で最小の値 |
||||

*例*

これらの例は、数値のセットと配列で最小の値を取得します。

```
min(1, 2, 3)
min(createArray(1, 2, 3))
```

返される結果: `1`

<a name="mod"></a>

### <a name="mod"></a>mod

2 つの数値を除算した剰余を返します。
整数の結果を取得するには、[div()](#div) をご覧ください。

```
mod(<dividend>, <divisor>)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*dividend*> | はい | 整数または浮動小数点数 | *divisor* によって除算される値。 |
| <*divisor*> | はい | 整数または浮動小数点数 | *dividend* を除算する値。0 にすることはできません。 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*modulo-result*> | 整数または浮動小数点数 | 1 番目の数値を 2 番目の数値で除算した剰余 |
||||

*例*

この例は、1 番目の数値を 2 番目の数値で除算します。

```
mod(3, 2)
```

返される結果: `1`

<a name="mul"></a>

### <a name="mul"></a>mul

2 つの数値を乗算した積を返します。

```
mul(<multiplicand1>, <multiplicand2>)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*multiplicand1*> | はい | 整数または浮動小数点数 | *multiplicand2* と乗算する値 |
| <*multiplicand2*> | はい | 整数または浮動小数点数 | *multiplicand1* と乗算する値 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*product-result*> | 整数または浮動小数点数 | 1 番目の数値と 2 番目の数値を乗算した積 |
||||

*例*

これらの例は、1 番目の数値と 2 番目の数値を乗算します。

```
mul(1, 2)
mul(1.5, 2)
```

次の結果を返します。

* 1 番目の例: `2`
* 2 番目の例: `3`

<a name="not"></a>

### <a name="not"></a>not

式が false かどうかを調べます。
式が false の場合は true を返し、true の場合は false を返します。

```json
not(<expression>)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*expression*> | はい | Boolean | 調べる式。 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| true または false | Boolean | 式が false の場合は true を返します。 式が true の場合は false を返します。 |
||||

*例 1*

これらの例は、指定した式が false かどうかを調べます。

```json
not(false)
not(true)
```

次の結果を返します。

* 1 番目の例:式が false なので、関数は `true` を返します。
* 2 番目の例:式が true なので、関数は `false` を返します。

*例 2*

これらの例は、指定した式が false かどうかを調べます。

```json
not(equals(1, 2))
not(equals(1, 1))
```

次の結果を返します。

* 1 番目の例:式が false なので、関数は `true` を返します。
* 2 番目の例:式が true なので、関数は `false` を返します。

<a name="or"></a>

### <a name="or"></a>or

少なくとも 1 つの式が true かどうかを調べます。
少なくとも 1 つの式が true の場合は true を返し、両方が false の場合は false を返します。

```
or(<expression1>, <expression2>)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*expression1*>, <*expression2*> | はい | Boolean | 調べる式 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| true または false | Boolean | 少なくとも 1 つの式が true の場合は true を返します。 両方の式が false の場合は false を返します。 |
||||

*例 1*

これらの例は、少なくとも 1 つの式が true かどうかを調べます。

```json
or(true, false)
or(false, false)
```

次の結果を返します。

* 1 番目の例:少なくとも 1 つの式が true なので、関数は `true` を返します。
* 2 番目の例:両方の式が false なので、関数は `false` を返します。

*例 2*

これらの例は、少なくとも 1 つの式が true かどうかを調べます。

```json
or(equals(1, 1), equals(1, 2))
or(equals(1, 2), equals(1, 3))
```

次の結果を返します。

* 1 番目の例:少なくとも 1 つの式が true なので、関数は `true` を返します。
* 2 番目の例:両方の式が false なので、関数は `false` を返します。

<a name="rand"></a>

### <a name="rand"></a>rand

指定した範囲からランダムな整数を返します。開始側の端のみを含みます。

```
rand(<minValue>, <maxValue>)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*minValue*> | はい | Integer | 範囲に含まれる最小の整数 |
| <*maxValue*> | はい | Integer | 関数が返すことのできる範囲内で最も大きい整数の次の整数 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*random-result*> | Integer | 指定した範囲から返されるランダムな整数 |
||||

*例*

この例は、指定した範囲 (最大値は除きます) からランダムな整数を取得します。

```
rand(1, 5)
```

結果として次のいずれかの整数が返ります: `1`、`2`、`3`、`4`

<a name="range"></a>

### <a name="range"></a>range

指定した整数から始まる整数の配列を返します。

```
range(<startIndex>, <count>)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*startIndex*> | はい | Integer | 最初の項目として配列を開始する整数値 |
| <*count*> | はい | Integer | 配列内の整数の数 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| [<*range-result*>] | Array | 指定したインデックスから始まる整数の配列 |
||||

*例*

この例は、指定したインデックスから始まり、指定された数の整数を含む、整数の配列を作成します。

```
range(1, 4)
```

返される結果: `[1, 2, 3, 4]`

<a name="replace"></a>

### <a name="replace"></a>replace

部分文字列を指定した文字列で置換し、結果の文字列を返します。 この関数は、大文字と小文字を区別します。

```
replace('<text>', '<oldText>', '<newText>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*text*> | はい | String | 置換する部分文字列を含む文字列 |
| <*oldText*> | はい | String | 置換前の部分文字列 |
| <*newText*> | はい | String | 置換後の文字列 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*updated-text*> | String | 部分文字列を置換した後の更新された文字列 <p>部分文字列が見つからない場合は、元の文字列を返します。 |
||||

*例*

この例は、文字列 "the old string" で部分文字列 "old" を検索し、"old" を "new" に置き換えます。

```
replace('the old string', 'old', 'new')
```

返される結果: `"the new string"`

<a name="skip"></a>

### <a name="skip"></a>skip

コレクションの先頭から項目を削除し、"*他のすべて*" の項目を返します。

```
skip([<collection>], <count>)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*collection*> | はい | Array | 項目を削除するコレクション |
| <*count*> | はい | Integer | 先頭から削除する項目の数を示す正の整数 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| [<*updated-collection*>] | Array | 指定した項目を削除した後の更新されたコレクション |
||||

*例*

この例は、指定した配列の先頭から 1 つの項目 (番号 0) を削除します。

```
skip(createArray(0, 1, 2, 3), 1)
```

そして、残りの項目を含む配列 `[1,2,3]` を返します。

<a name="split"></a>

### <a name="split"></a>split

元の文字列で指定された区切り文字に基づいて、コンマで区切られた部分文字列を含む配列を返します。

```
split('<text>', '<delimiter>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*text*> | はい | String | 元の文字列で指定された区切り記号に基づいて部分文字列に分割する文字列 |
| <*delimiter*> | はい | String | 区切り記号として使用する、元の文字列内の文字 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| [<*substring1*>,<*substring2*>,...] | Array | コンマで区切られた、元の文字列からの部分文字列を含む配列 |
||||

*例*

この例では、区切り記号として指定した文字に基づいて指定された文字列からの部分文字列を含む配列を作成します。

```
split('a_b_c', '_')
```

返される配列の結果: `["a","b","c"]`

<a name="startOfDay"></a>

### <a name="startofday"></a>startOfDay

タイムスタンプの日の開始日時を返します。

```
startOfDay('<timestamp>', '<format>'?)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | はい | String | タイムスタンプを含む文字列。 |
| <*format*> | いいえ | String | [単一の書式指定子](/dotnet/standard/base-types/standard-date-and-time-format-strings)または[カスタム書式パターン](/dotnet/standard/base-types/custom-date-and-time-format-strings)。 timestamp の既定の形式は ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK) です。これは、[ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) に準拠し、タイム ゾーン情報を保持します。 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | 指定したタイムスタンプの日の午前 0 時を表す日時文字列 |
||||

*例*

この例は、次のタイムスタンプの日の開始を取得します。

```
startOfDay('2018-03-15T13:30:30Z')
```

返される結果: `"2018-03-15T00:00:00.0000000Z"`

<a name="startOfHour"></a>

### <a name="startofhour"></a>startOfHour

タイムスタンプの時刻の開始を返します。

```
startOfHour('<timestamp>', '<format>'?)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | はい | String | タイムスタンプを含む文字列。 |
| <*format*> | いいえ | String | [単一の書式指定子](/dotnet/standard/base-types/standard-date-and-time-format-strings)または[カスタム書式パターン](/dotnet/standard/base-types/custom-date-and-time-format-strings)。 timestamp の既定の形式は ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK) です。これは、[ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) に準拠し、タイム ゾーン情報を保持します。 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | 指定したタイムスタンプの時刻の 0 分を表す日時文字列 |
||||

*例*

この例は、次のタイムスタンプの時刻の開始を取得します。

```
startOfHour('2018-03-15T13:30:30Z')
```

返される結果: `"2018-03-15T13:00:00.0000000Z"`

<a name="startOfMonth"></a>

### <a name="startofmonth"></a>startOfMonth

タイムスタンプの月の開始を返します。

```
startOfMonth('<timestamp>', '<format>'?)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | はい | String | タイムスタンプを含む文字列。 |
| <*format*> | いいえ | String | [単一の書式指定子](/dotnet/standard/base-types/standard-date-and-time-format-strings)または[カスタム書式パターン](/dotnet/standard/base-types/custom-date-and-time-format-strings)。 timestamp の既定の形式は ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK) です。これは、[ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) に準拠し、タイム ゾーン情報を保持します。 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | 指定したタイムスタンプの月の開始日の午前 0 時を表す文字列 |
||||

*例*

この例は、次のタイムスタンプの月の開始を返します。

```
startOfMonth('2018-03-15T13:30:30Z')
```

返される結果: `"2018-03-01T00:00:00.0000000Z"`

<a name="startswith"></a>

### <a name="startswith"></a>startsWith

文字列が特定の部分文字列で始まっているかどうかを調べます。
部分文字列が見つかった場合は true を返し、見つからない場合は false を返します。
この関数は、大文字と小文字を区別しません。

```
startsWith('<text>', '<searchText>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*text*> | はい | String | 調べる文字列。 |
| <*searchText*> | はい | String | 検索する開始文字列 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| true または false  | Boolean | 先頭の部分文字列が見つかった場合は true を返します。 見つからなかった場合は false を返します。 |
||||

*例 1*

この例は、文字列 "hello world" が部分文字列 "hello" で始まっているかどうかを調べます。

```
startsWith('hello world', 'hello')
```

返される結果: `true`

*例 2*

この例は、文字列 "hello world" が部分文字列 "greetings" で始まっているかどうかを調べます。

```
startsWith('hello world', 'greetings')
```

返される結果: `false`

<a name="string"></a>

### <a name="string"></a>string

値の文字列バージョンを返します。

```
string(<value>)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | はい | Any | 変換する値 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*string-value*> | String | 指定した値の文字列バージョン |
||||

*例 1*

この例は、次の数値の文字列バージョンを作成します。

```
string(10)
```

返される結果: `"10"`

*例 2*

この例は、指定した JSON オブジェクトの文字列を作成します。二重引用符 (") のエスケープ文字として、バックスラッシュ文字 (\\) を使います。

```
string( { "name": "Sophie Owen" } )
```

返される結果: `"{ \\"name\\": \\"Sophie Owen\\" }"`

<a name="sub"></a>

### <a name="sub"></a>sub

1 番目の数値から 2 番目の数値を減算して、結果を返します。

```
sub(<minuend>, <subtrahend>)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*minuend*> | はい | 整数または浮動小数点数 | *subtrahend* を引く数値 |
| <*subtrahend*> | はい | 整数または浮動小数点数 | *minuend* から引く数値 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*result*> | 整数または浮動小数点数 | 1 番目の数値から 2 番目の数値を減算した結果 |
||||

*例*

この例は、1 番目の数値から 2 番目の数値を減算します。

```
sub(10.3, .3)
```

返される結果: `10`

<a name="substring"></a>

### <a name="substring"></a>substring

文字列から、指定された位置またはインデックスから始まる文字を返します。
インデックス値は 0 から始まります。

```
substring('<text>', <startIndex>, <length>)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*text*> | はい | String | 文字を取得する文字列 |
| <*startIndex*> | はい | Integer | 開始位置またはインデックスの値として使用する 0 以上の正の数 |
| <*length*> | はい | Integer | 取得する部分文字列の文字数を示す正の値 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*substring-result*> | String | ソース文字列の指定したインデックス位置から始まる、指定した文字数を含む部分文字列 |
||||

*例*

この例は、指定した文字列のインデックス値 6 から始まる 5 文字を含む部分文字列を作成します。

```
substring('hello world', 6, 5)
```

返される結果: `"world"`

<a name="subtractFromTime"></a>

### <a name="subtractfromtime"></a>subtractFromTime

タイムスタンプから時間単位数を減算します。
[getPastTime](#getPastTime) もご覧ください。

```
subtractFromTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | はい | String | タイムスタンプを含む文字列。 |
| <*interval*> | はい | Integer | 減算する指定した時間単位の数 |
| <*timeUnit*> | はい | String | *間隔* と共に使用する時間単位:"Second"、"Minute"、"Hour"、"Day"、"Week"、"Month"、"Year" |
| <*format*> | いいえ | String | [単一の書式指定子](/dotnet/standard/base-types/standard-date-and-time-format-strings)または[カスタム書式パターン](/dotnet/standard/base-types/custom-date-and-time-format-strings)。 timestamp の既定の形式は ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK) です。これは、[ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) に準拠し、タイム ゾーン情報を保持します。 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | タイムスタンプから指定した時間単位数を引いた値 |
||||

*例 1*

この例は、次のタイムスタンプから 1 日を減算します。

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day')
```

返される結果: `"2018-01-01T00:00:00:0000000Z"`

*例 2*

この例は、次のタイムスタンプから 1 日を減算します。

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day', 'D')
```

省略可能な "D" 形式を使用して返される結果: `"Monday, January, 1, 2018"`

<a name="take"></a>

### <a name="take"></a>take

コレクションの先頭から項目を返します。

```
take('<collection>', <count>)
take([<collection>], <count>)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*collection*> | はい | 文字列、配列 | 項目を取得するコレクション |
| <*count*> | はい | Integer | 先頭から取得する項目の数を示す正の整数 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*subset*> または [<*subset*>] | それぞれ文字列、配列 | 元のコレクションの先頭から取得された指定個数の項目を含む文字列または配列 |
||||

*例*

これらの例は、次のコレクションの先頭から指定した数の項目を取得します。

```
take('abcde', 3)
take(createArray(0, 1, 2, 3, 4), 3)
```

次の結果を返します。

* 1 番目の例: `"abc"`
* 2 番目の例: `[0, 1, 2]`

<a name="ticks"></a>

### <a name="ticks"></a>ticks

指定したタイムスタンプの `ticks` プロパティの値を返します。
"*ティック*" は 100 ナノ秒の間隔です。

```
ticks('<timestamp>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | はい | String | タイムスタンプの文字列 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*ticks-number*> | Integer | 指定したタイムスタンプからのティック数 |
||||

<a name="toLower"></a>

### <a name="tolower"></a>toLower

小文字の形式で文字列を返します。 文字列内の文字に小文字バージョンがない場合、その文字は返される文字列に変更されないまま残ります。

```
toLower('<text>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*text*> | はい | String | 小文字形式で返される文字列 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*lowercase-text*> | String | 元の文字列の小文字形式 |
||||

*例*

この例は、次の文字列を小文字に変換します。

```
toLower('Hello World')
```

返される結果: `"hello world"`

<a name="toUpper"></a>

### <a name="toupper"></a>toUpper

大文字の形式で文字列を返します。 文字列内の文字に大文字バージョンがない場合、その文字は返される文字列に変更されないまま残ります。

```
toUpper('<text>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*text*> | はい | String | 大文字形式で返される文字列 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*uppercase-text*> | String | 元の文字列の大文字形式 |
||||

*例*

この例は、次の文字列を大文字に変換します。

```
toUpper('Hello World')
```

返される結果: `"HELLO WORLD"`

<a name="trim"></a>

### <a name="trim"></a>trim

文字列から先頭と末尾の空白を削除し、更新された文字列を返します。

```
trim('<text>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*text*> | はい | String | 先頭と末尾に削除する空白を含む文字列 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*updatedText*> | String | 先頭または末尾の空白が削除された、元の文字列の更新バージョン |
||||

*例*

この例は、文字列 " Hello World  " から先頭と末尾の空白を削除します。

```
trim(' Hello World  ')
```

返される結果: `"Hello World"`

<a name="union"></a>

### <a name="union"></a>union

指定した複数のコレクションの "*すべての*" 項目を含む 1 つのコレクションを返します。
この関数に渡されるいずれかのコレクションに含まれる項目は、結果にも含まれます。 1 つまたは複数の項目が同じ名前である場合は、その名前を持つ最後の項目が結果に含まれます。

```
union('<collection1>', '<collection2>', ...)
union([<collection1>], [<collection2>], ...)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*collection1*>, <*collection2*>, ...  | はい | 配列またはオブジェクト、両方ともは不可 | "*すべての*" 項目を取得するコレクション |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*updatedCollection*> | それぞれ、配列またはオブジェクト | 指定したコレクションのすべての項目を含むコレクション。重複はありません。 |
||||

*例*

この例は、以下のコレクションから "*すべての*" 項目を取得します。

```
union(createArray(1, 2, 3), createArray(1, 2, 10, 101))
```

返される結果: `[1, 2, 3, 10, 101]`

<a name="uriComponent"></a>

### <a name="uricomponent"></a>uriComponent

URL の安全でない文字がエスケープ文字に置き換えられた、文字列の URI (Uniform Resource Identifier) エンコード バージョンを返します。
[encodeUriComponent()](#encodeUriComponent) ではなく、この関数を使用してください。
どちらの関数も機能は同じですが、`uriComponent()` をお勧めします。

```
uriComponent('<value>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | はい | String | URI エンコード形式に変換する文字列 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*encoded-uri*> | String | エスケープ文字が使われている URI エンコード文字列 |
||||

*例*

この例は、次の文字列の URI エンコード バージョンを作成します。

```
uriComponent('https://contoso.com')
```

返される結果: `"http%3A%2F%2Fcontoso.com"`

<a name="uriComponentToBinary"></a>

### <a name="uricomponenttobinary"></a>uriComponentToBinary

URI (Uniform Resource Identifier) コンポーネントのバイナリ バージョンを返します。

```
uriComponentToBinary('<value>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | はい | String | 変換する URI エンコード文字列 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*binary-for-encoded-uri*> | String | URI エンコード文字列のバイナリ バージョン バイナリ コンテンツは base64 でエンコードされ、`$content` によって表されます。 |
||||

*例*

この例は、次の URI エンコード文字列のバイナリ バージョンを作成します。

```
uriComponentToBinary('http%3A%2F%2Fcontoso.com')
```

返される結果:

`"001000100110100001110100011101000111000000100101001100
11010000010010010100110010010001100010010100110010010001
10011000110110111101101110011101000110111101110011011011
110010111001100011011011110110110100100010"`

<a name="uriComponentToString"></a>

### <a name="uricomponenttostring"></a>uriComponentToString

URI (Uniform Resource Identifier) エンコード文字列の文字列バージョンを返します。実質的に、URI エンコード文字列をデコードします。

```
uriComponentToString('<value>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | はい | String | デコードする URI エンコード文字列 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*decoded-uri*> | String | URI エンコード文字列のデコード バージョン |
||||

*例*

この例は、次の URI エンコード文字列のデコードされた文字列バージョンを作成します。

```
uriComponentToString('http%3A%2F%2Fcontoso.com')
```

返される結果: `"https://contoso.com"`

<a name="utcNow"></a>

### <a name="utcnow"></a>utcNow

現在のタイムスタンプを返します。

```
utcNow('<format>')
```

必要に応じて、<*format*> パラメーターで異なる形式を指定できます。

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*format*> | いいえ | String | [単一の書式指定子](/dotnet/standard/base-types/standard-date-and-time-format-strings)または[カスタム書式パターン](/dotnet/standard/base-types/custom-date-and-time-format-strings)。 timestamp の既定の形式は ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK) です。これは、[ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) に準拠し、タイム ゾーン情報を保持します。 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*current-timestamp*> | String | 現在の日付と時刻 |
||||

*例 1*

現在の日時が 2018 年 4 月 15 日午後 1 時 0 分 0 秒であるものとします。
この例は、現在のタイムスタンプを取得します。

```
utcNow()
```

返される結果: `"2018-04-15T13:00:00.0000000Z"`

*例 2*

現在の日時が 2018 年 4 月 15 日午後 1 時 0 分 0 秒であるものとします。
この例は、オプションの "D" 形式を使って現在のタイムスタンプを取得します。

```
utcNow('D')
```

返される結果: `"Sunday, April 15, 2018"`

<a name="xml"></a>

### <a name="xml"></a>xml

JSON オブジェクトを含む文字列の XML バージョンを返します。

```
xml('<value>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | はい | String | 変換する JSON オブジェクトを含む文字列 <p>JSON オブジェクトのルート プロパティは 1 つに限る必要があり、配列にはできません。 <br>二重引用符 (") のエスケープ文字としてはバックスラッシュ文字 (\\) を使います。 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*xml-version*> | Object | 指定した文字列または JSON オブジェクトのエンコードされた XML |
||||

*例 1*

この例は、JSON オブジェクトを含む次の文字列の XML バージョンを作成します。

`xml(json('{ \"name\": \"Sophia Owen\" }'))`

返される結果の XML:

```xml
<name>Sophia Owen</name>
```

*例 2*

次のような JSON オブジェクトがあるものとします。

```json
{
  "person": {
    "name": "Sophia Owen",
    "city": "Seattle"
  }
}
```

この例は、この JSON オブジェクトを含む文字列の XML を作成します。

`xml(json('{\"person\": {\"name\": \"Sophia Owen\", \"city\": \"Seattle\"}}'))`

返される結果の XML:

```xml
<person>
  <name>Sophia Owen</name>
  <city>Seattle</city>
<person>
```

<a name="xpath"></a>

### <a name="xpath"></a>xpath

XML で XPath (XML Path Language) 式と一致するノードまたは値を調べて、一致するノードまたは値を返します。 XPath 式または単に "XPath" は、XML コンテンツ内のノードまたは計算値を選択できるように、XML ドキュメント構造内の移動を補助します。

```
xpath('<xml>', '<xpath>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*xml*> | はい | Any | XPath 式の値に一致するノードまたは値を検索する XML 文字列 |
| <*xpath*> | はい | Any | 一致する XML ノードまたは値の検索に使用する XPath 式 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*xml-node*> | XML | 1 つのノードだけが指定した XPath 式と一致するときの XML ノード |
| <*value*> | Any | 1 つの値だけが指定した XPath 式と一致するときの XML ノードの値 |
| [<*xml-node1*>, <*xml-node2*>, ...] </br>または </br>[<*value1*>, <*value2*>, ...] | Array | 指定した XPath 式と一致する XML ノードまたは値の配列 |
||||

*例 1*

例 1 に続き、この例では `<count></count>` ノードと一致するノードを検索し、`sum()` 関数でそれらのノードの値を追加します。

`xpath(xml(parameters('items')), 'sum(/produce/item/count)')`

返される結果: `30`

*例 2*

この例の式はどちらも、名前空間を含む XML が格納されている指定した引数で、`<location></location>` ノードと一致するノードを検索します。 式では、二重引用符 (") のエスケープ文字としてバックスラッシュ文字 (\\) を使います。

* *式 1*

  `xpath(xml(body('Http')), '/*[name()=\"file\"]/*[name()=\"location\"]')`

* *式 2*

  `xpath(xml(body('Http')), '/*[local-name()=\"file\" and namespace-uri()=\"http://contoso.com\"]/*[local-name()=\"location\"]')`

引数は次のとおりです。

* XML ドキュメントの名前空間 `xmlns="http://contoso.com"` を含む次の XML:

  ```xml
  <?xml version="1.0"?> <file xmlns="http://contoso.com"> <location>Paris</location> </file>
  ```

* どちらかの XPath 式:

  * `/*[name()=\"file\"]/*[name()=\"location\"]`

  * `/*[local-name()=\"file\" and namespace-uri()=\"http://contoso.com\"]/*[local-name()=\"location\"]`

`<location></location>` ノードと一致する結果のノード:

```xml
<location xmlns="https://contoso.com">Paris</location>
```

*例 3*

例 3 に続き、この例では `<location></location>` ノードの値を検索します。

`xpath(xml(body('Http')), 'string(/*[name()=\"file\"]/*[name()=\"location\"])')`

返される結果: `"Paris"`

## <a name="next-steps"></a>次のステップ
式で使用できるシステム変数の一覧については、「[システム変数](control-flow-system-variables.md)」を参照してください。
