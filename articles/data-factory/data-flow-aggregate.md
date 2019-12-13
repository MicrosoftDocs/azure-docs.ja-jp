---
title: マッピング データ フローの集計変換
description: Azure Data Factory でマッピング データ フローの集計変換を使用して、大規模なデータ集計を行う方法について説明します。
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/15/2019
ms.openlocfilehash: 74b96bf2cac0de7c57e496c637f2e3ef549eb61f
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930455"
---
# <a name="aggregate-transformation-in-mapping-data-flow"></a>マッピング データ フローの集計変換 

集計変換では、データ ストリームに含まれる列の集計を定義します。 式ビルダーを使用して、既存の列または計算列によってグループ化される、SUM、MIN、MAX、COUNT などのさまざまな種類の集計を定義できます。

## <a name="group-by"></a>グループ化

集計で句ごとのグループ化として使用するために、既存の列を選択するか、新しい計算列を作成します。 既存の列を使用するには、ドロップダウンから列を選択します。 新しい計算列を作成するには、句をポイントし、 **[計算列]** をクリックします。 これにより、[データ フローの式ビルダー](concepts-data-flow-expression-builder.md)が開きます。 計算列を作成したら、 **[Name as]\(名前\)** フィールドに出力列の名前を入力します。 Group By 句を追加するには、既存の句にカーソルを合わせ、プラス アイコンをクリックします。

![集計変換のグループ化の設定](media/data-flow/agg.png "集計変換のグループ化の設定")

集計変換では、句ごとのグループ化は省略可能です。

## <a name="aggregate-column"></a>列を集計する 

**[集計]** タブに移動して、集計式を作成します。 既存の列を集計で上書きするか、新しいフィールドを新しい名前で作成します。 集計式は、列名セレクターの右側のボックスに入力されます。 式を編集するには、テキスト ボックスをクリックして式ビルダーを開きます。 別の集計を追加するには、既存の式の上にカーソルを合わせ、プラス アイコンをクリックすると、新しい集計列または[列パターン](concepts-data-flow-column-pattern.md)が作成されます。

各集計式には、少なくとも 1 つの集計関数が含まれている必要があります。

![集計変換の集計設定](media/data-flow/agg2.png "集計変換の集計設定")


> [!NOTE]
> デバッグ モードでは、式ビルダーで集計関数を使用したデータのプレビューを生成することはできません。 集計変換のデータのプレビューを表示するには、式ビルダーを終了し、[データ のプレビュー] タブで確認します。

## <a name="reconnect-rows-and-columns"></a>行と列の再接続

集計変換は、SQL 集計の SELECT クエリと似ています。 Group By 句または集計関数に含まれていない列は、集計変換の出力にフローしません。 集計された出力に他の列を含める場合は、次のいずれかの方法を実行します。

* その追加の列を含めるには、`last()` や `first()` などの集計関数を使用します。
* [自己結合パターン](https://mssqldude.wordpress.com/2018/12/20/adf-data-flows-self-join/)を使用して、列を出力ストリームに再結合します。

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

## <a name="next-steps"></a>次の手順

* [ウィンドウ変換](data-flow-window.md)を使用してウィンドウベースの集計を定義する
