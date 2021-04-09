---
title: マッピング データ フローの集計変換
description: Azure Data Factory でマッピング データ フローの集計変換を使用して、大規模なデータ集計を行う方法について説明します。
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/14/2020
ms.openlocfilehash: 71f5488b1f689e8892155b013730bcbb3c8e0e35
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90531927"
---
# <a name="aggregate-transformation-in-mapping-data-flow"></a>マッピング データ フローの集計変換

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

集計変換では、データ ストリームに含まれる列の集計を定義します。 式ビルダーを使用して、既存の列または計算列によってグループ化される、SUM、MIN、MAX、COUNT などのさまざまな種類の集計を定義できます。

## <a name="group-by"></a>グループ化

集計で句ごとのグループ化として使用するために、既存の列を選択するか、新しい計算列を作成します。 既存の列を使用するには、ドロップダウンから列を選択します。 新しい計算列を作成するには、句をポイントし、 **[計算列]** をクリックします。 これにより、[データ フローの式ビルダー](concepts-data-flow-expression-builder.md)が開きます。 計算列を作成したら、 **[Name as]\(名前\)** フィールドに出力列の名前を入力します。 Group By 句を追加するには、既存の句にカーソルを合わせ、プラス アイコンをクリックします。

![集計変換のグループ化の設定](media/data-flow/agg.png "集計変換のグループ化の設定")

集計変換では、句ごとのグループ化は省略可能です。

## <a name="aggregate-columns"></a>列を集計する

**[集計]** タブに移動して、集計式を作成します。 既存の列を集計で上書きするか、新しいフィールドを新しい名前で作成します。 集計式は、列名セレクターの右側のボックスに入力されます。 式を編集するには、テキスト ボックスをクリックして式ビルダーを開きます。 集計列をさらに追加するには、列リストの上にある **[追加]** をクリックするか、既存の集計列の横にあるプラス記号のアイコンをクリックします。 **[列の追加]** または **[列パターンの追加]** のいずれかを選択します。 各集計式には、少なくとも 1 つの集計関数が含まれている必要があります。

![集計の設定](media/data-flow/aggregate-columns.png "集計の設定")

> [!NOTE]
> デバッグ モードでは、式ビルダーで集計関数を使用したデータのプレビューを生成することはできません。 集計変換のデータのプレビューを表示するには、式ビルダーを終了し、[データ のプレビュー] タブで確認します。

### <a name="column-patterns"></a>列パターン

一連の列に同じ集計を適用するには、[列パターン](concepts-data-flow-column-pattern.md)を使用します。 入力スキーマからの列は既定では削除されるため、これは、その多くを保持する場合に役立ちます。 `first()` などのヒューリスティックを使用して、集計を通じて入力列を保持します。

## <a name="reconnect-rows-and-columns"></a>行と列の再接続

集計変換は、SQL 集計の SELECT クエリと似ています。 Group By 句または集計関数に含まれていない列は、集計変換の出力にフローしません。 集計された出力に他の列を含める場合は、次のいずれかの方法を実行します。

* その追加の列を含めるには、`last()` や `first()` などの集計関数を使用します。
* [自己結合パターン](https://mssqldude.wordpress.com/2018/12/20/adf-data-flows-self-join/)を使用して、列を出力ストリームに再結合します。

## <a name="removing-duplicate-rows"></a>重複した行の削除

集計変換の一般的な使用方法は、ソース データ内の重複したエントリを削除または特定することです。 このプロセスは重複除去と呼ばれます。 グループ化キーのセットに基づいて、選択したヒューリスティックを使用して、どの重複行を保持するかを決定します。 一般的なヒューリスティックは、`first()`、`last()`、`max()`、および `min()` です。 グループ化列を除くすべての列にルールを適用するには、[列パターン](concepts-data-flow-column-pattern.md)を使用します。

![Deduplication (重複除去)](media/data-flow/agg-dedupe.png "重複除去")

上の例では、列 `ProductID` および `Name` がグループ化に使用されています。 これらの 2 つの列の 2 つの行に同じ値がある場合、それらは重複していると見なされます。 この集計変換では、一致した最初の行の値が保持され、それ以外の値はすべて削除されます。 列パターン構文を使用すると、名前が `ProductID` および `Name` ではないすべての列が既存の列名にマップされ、最初に一致した行の値が指定されます。 出力スキーマは、入力スキーマと同じです。

データ検証のシナリオでは、`count()` 関数を使用して、存在している重複の数をカウントできます。

## <a name="data-flow-script"></a>データ フローのスクリプト

### <a name="syntax"></a>構文

```
<incomingStream>
    aggregate(
           groupBy(
                <groupByColumnName> = <groupByExpression1>,
                <groupByExpression2>
               ),
           <aggregateColumn1> = <aggregateExpression1>,
           <aggregateColumn2> = <aggregateExpression2>,
           each(
                match(matchExpression),
                <metadataColumn1> = <metadataExpression1>,
                <metadataColumn2> = <metadataExpression2>
               )
          ) ~> <aggregateTransformationName>
```

### <a name="example"></a>例

次の例では、受信ストリーム `MoviesYear` を受け取り、列 `year` で行をグループ化します。 この変換では、列 `Rating` の平均に評価される集計列 `avgrating` が作成されます。 この集計変換には `AvgComedyRatingsByYear` という名前が付けられます。

Data Factory UX では、この変換は次の図のようになります。

![グループ化の例](media/data-flow/agg-script1.png "グループ化の例")

![集計の例](media/data-flow/agg-script2.png "集計の例")

この変換のデータ フロー スクリプトは、次のスニペットに含まれています。

```
MoviesYear aggregate(
                groupBy(year),
                avgrating = avg(toInteger(Rating))
            ) ~> AvgComedyRatingByYear
```

![集計データ フロー スクリプト](media/data-flow/aggdfs1.png "集計データ フロー スクリプト")

```MoviesYear```:年とタイトルの列を定義する派生列 ```AvgComedyRatingByYear```: 年別にグループ化されたコメディの平均評価の集計変換 ```avgrating```: 集計値を保持するために作成される新しい列の名前

```
MoviesYear aggregate(groupBy(year),
    avgrating = avg(toInteger(Rating))) ~> AvgComedyRatingByYear
```

## <a name="next-steps"></a>次の手順

* [ウィンドウ変換](data-flow-window.md)を使用してウィンドウベースの集計を定義する
