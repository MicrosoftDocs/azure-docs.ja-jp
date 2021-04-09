---
title: マッピング データ フローでのデータ変換を解析する
description: 列の埋め込みドキュメントを解析します
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: 4db9503ea84ae13148a89a03048c73399413e5cc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101710194"
---
# <a name="parse-transformation-in-mapping-data-flow"></a>マッピング データ フローでの変換を解析する

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

変換解析を使用して、データ内のドキュメント形式の列を解析します。 現在サポートされている解析可能な埋め込みドキュメントの種類は、JSON と区切りテキストです。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWykdO]

## <a name="configuration"></a>構成

変換解析の構成パネルで、最初に、インラインで解析する列に含まれているデータの種類を選択します。 変換解析には、次の構成設定も含まれています。

![解析の設定](media/data-flow/data-flow-parse-1.png "[解析]")

### <a name="column"></a>列

派生列や集計と同様に、ここで、ドロップダウン ピッカーから選択して既存の列を変更できます。 または、ここに新しい列の名前を入力することもできます。 ADF により、解析されたソース データがこの列に格納されます。

### <a name="expression"></a>Expression

式ビルダーを使用して、解析対象のソースを設定します。 これは、解析する自己完結型データが含まれたソース列を選択するだけで簡単に行うことができます。または、解析するための複雑な式を作成することもできます。

### <a name="output-column-type"></a>出力列の種類

ここでは、1 つの列に書き込まれる、解析からのターゲット出力スキーマを構成します。

![解析例](media/data-flow/data-flow-parse-2.png "解析例")

この例では、受信フィールド "jsonString" の解析を定義しています。これはプレーンテキストですが、JSON 構造体として書式設定されています。 次のスキーマを使用して、解析結果を JSON として "json" という名前の新しい列に格納します。

```(trade as boolean, customers as string[])```

[検査] タブと [データのプレビュー] を参照して、出力が適切にマッピングされていることを確認します。

## <a name="examples"></a>使用例

```
source(output(
        name as string,
        location as string,
        satellites as string[],
        goods as (trade as boolean, customers as string[], orders as (orderId as string, orderTotal as double, shipped as (orderItems as (itemName as string, itemQty as string)[]))[])
    ),
    allowSchemaDrift: true,
    validateSchema: false,
    ignoreNoFilesFound: false,
    documentForm: 'documentPerLine') ~> JsonSource
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false,
    ignoreNoFilesFound: false) ~> CsvSource
JsonSource derive(jsonString = toString(goods)) ~> StringifyJson
StringifyJson parse(json = jsonString ? (trade as boolean,
        customers as string[]),
    format: 'json',
    documentForm: 'arrayOfDocuments') ~> ParseJson
CsvSource derive(csvString = 'Id|name|year\n\'1\'|\'test1\'|\'1999\'') ~> CsvString
CsvString parse(csv = csvString ? (id as integer,
        name as string,
        year as string),
    format: 'delimited',
    columnNamesAsHeader: true,
    columnDelimiter: '|',
    nullValue: '',
    documentForm: 'documentPerLine') ~> ParseCsv
ParseJson select(mapColumn(
        jsonString,
        json
    ),
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> KeepStringAndParsedJson
ParseCsv select(mapColumn(
        csvString,
        csv
    ),
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> KeepStringAndParsedCsv
```

## <a name="data-flow-script"></a>データ フローのスクリプト

### <a name="syntax"></a>構文

### <a name="examples"></a>例

```
parse(json = jsonString ? (trade as boolean,
                                customers as string[]),
                format: 'json',
                documentForm: 'singleDocument') ~> ParseJson

parse(csv = csvString ? (id as integer,
                                name as string,
                                year as string),
                format: 'delimited',
                columnNamesAsHeader: true,
                columnDelimiter: '|',
                nullValue: '',
                documentForm: 'documentPerLine') ~> ParseCsv
```    

## <a name="next-steps"></a>次のステップ

* [フラット化変換](data-flow-flatten.md)を使用して、行を列にピボットします。
* [派生列変換](data-flow-derived-column.md)を使用して、列を行にピボットします。
