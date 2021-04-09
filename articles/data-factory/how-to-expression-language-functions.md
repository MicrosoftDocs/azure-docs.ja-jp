---
title: Azure Data Factory でパラメーターと式を使用する方法
description: このハウツー記事では、Data Factory エンティティの作成で使用できる式と関数に関する情報を提供します。
author: ssabat
ms.author: susabat
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/08/2020
ms.openlocfilehash: 090c738d06b9b8acd53a4c194e97fa3a0515e1d6
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104783389"
---
# <a name="how-to-use-parameters-expressions-and-functions-in-azure-data-factory"></a>Azure Data Factory でパラメーター、式、関数を使用する方法

> [!div class="op_single_selector" title1="使用している Data Factory サービスのバージョンを選択してください:"]
> * [Version 1](v1/data-factory-functions-variables.md)
> * [現在のバージョン](how-to-expression-language-functions.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

このドキュメントでは、Azure Data Factory 内でパラメーター化されたデータ パイプラインを作成する機能を紹介するさまざまな例を使用して、基本的な概念を学習することに主に焦点を当てます。 パラメーター化と動的式は、ADF に追加された注目すべき機能です。これらを使用すると、膨大な時間を節約し、より柔軟な抽出、変換、読み込み (ETL) または 抽出、読み込み、変換 (ELT) ソリューションを実現できます。これにより、ソリューションのメンテナンス コストが大幅に削減され、既存パイプラインへの新機能実装を高速化できるようになります。 これらの利点は、パラメーター化によってハード コーディングの量が最小限に抑えられ、ソリューション内の再利用可能なオブジェクトとプロセスの数が増えるからです。

## <a name="azure-data-factory-ui-and-parameters"></a>Azure Data Factory UI とパラメーター

ADF ユーザー インターフェイスで Azure Data Factory パラメーターを初めて使用する場合は、視覚的な説明として[リンクされたサービス用の Data Factory UI](https://docs.microsoft.com/azure/data-factory/parameterize-linked-services#data-factory-ui) と[パラメーターを備えたメタデータ ドリブン パイプライン用の Data Factory UI](https://docs.microsoft.com/azure/data-factory/how-to-use-trigger-parameterization#data-factory-ui) に関するページをご覧ください。

## <a name="parameter-and-expression-concepts"></a>パラメーターと式の概念 

パラメーターを使用して、パイプライン、データセット、リンクされたサービス、およびデータ フローに外部の値を渡すことができます。 パラメーターは、リソースに渡されると変更できません。 リソースをパラメーター化することで、毎回異なる値を使用してリソースを再利用できます。 パラメーターは、個別に、または式の一部として使用できます。 定義内の JSON 値には、実行時に評価されるリテラルまたは式を指定できます。

次に例を示します。  
  
```json
"name": "value"
```

 or  
  
```json
"name": "@pipeline().parameters.password"
```

式は、JSON string 値内の任意の場所で使うことができ、常に別の JSON 値になります。 ここで、*password* は式のパイプライン パラメーターです。 JSON 値が式である場合は、アットマーク (\@) を削除することによって式の本体が抽出されます。 \@ で始まるリテラル文字列が必要な場合は、\@\@ を使用してその文字列をエスケープする必要があります。 式の評価方法の例を次に示します。  
  
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

## <a name="examples-of-using-parameters-in-expressions"></a>式でのパラメーターの使用例 

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

### <a name="a-dataset-with--parameters"></a>パラメーターを備えたデータセット

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

### <a name="a-pipeline-with--parameters"></a>パラメーターを備えたパイプライン

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

  
## <a name="calling-functions-within-expressions"></a>式の中で関数を呼び出す 

式の中で関数を呼び出すことができます。 以降のセクションでは、式で使用できる関数に関する情報を提供します。  

### <a name="string-functions"></a>文字列関数  

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

### <a name="collection-functions"></a>コレクション関数

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

### <a name="logical-functions"></a>論理関数  

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
  
### <a name="conversion-functions"></a>変換関数  

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

### <a name="math-functions"></a>算術関数  
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
  
### <a name="date-functions"></a>データ関数  

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

## <a name="detailed-examples-for-practice"></a>実践のための詳細な例

### <a name="detailed-azure-data-factory-copy-pipeline-with-parameters"></a>パラメーターを備えた Azure Data Factory コピー パイプラインの詳細 

この[Azure Data Factory コピー パイプラインでのパラメーター受け渡しに関するチュートリアル](https://azure.microsoft.com/mediahandler/files/resourcefiles/azure-data-factory-passing-parameters/Azure%20data%20Factory-Whitepaper-PassingParameters.pdf)では、パイプラインとアクティビティの間、およびアクティビティ間でパラメーターを渡す方法について説明します。

### <a name="detailed--mapping-data-flow-pipeline-with-parameters"></a>パラメーターを備えたマッピング データ フロー パイプラインの詳細 

データ フローでパラメーターを使用する方法の包括的な例については、[パラメーターを備えたマッピング データ フロー](https://docs.microsoft.com/azure/data-factory/parameters-data-flow)に関するページをご覧ください。

### <a name="detailed-metadata-driven-pipeline-with-parameters"></a>パラメーターを備えたメタデータ ドリブン パイプラインの詳細

パラメーターを使用してメタデータ ドリブン パイプラインを設計する方法の詳細については、[パラメーターを備えたメタデータ ドリブン パイプライン](https://docs.microsoft.com/azure/data-factory/how-to-use-trigger-parameterization)に関するページをご覧ください。 これは、パラメーターの一般的なユース ケースです。


## <a name="next-steps"></a>次のステップ
式で使用できるシステム変数の一覧については、「[システム変数](control-flow-system-variables.md)」を参照してください。
