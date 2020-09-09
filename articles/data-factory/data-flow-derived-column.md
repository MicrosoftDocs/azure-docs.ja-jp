---
title: マッピング データ フローの派生列変換
description: Azure Data Factory で、マッピング データ フローの派生列変換を使用して、大規模にデータを変換する方法について説明します。
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/15/2019
ms.openlocfilehash: 38ec2d4619f47bf9fc4d1815cb6e9990cef72dcf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "81606501"
---
# <a name="derived-column-transformation-in-mapping-data-flow"></a>マッピング データ フローの派生列変換

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

データ フロー内に新しい列を生成したり、既存のフィールドを変更したりするには、派生列変換を使用します。

## <a name="derived-column-settings"></a>派生列変換の設定

既存の列を上書きするには、列のドロップダウンからそれを選択します。 それ以外の場合は、列選択フィールドをテキスト ボックスとして使用して、新しい列の名前を入力します。 派生列の式を作成するには、[式の入力] ボックスをクリックして [Data Flow 式ビルダー](concepts-data-flow-expression-builder.md)を開きます。

![派生列変換の設定](media/data-flow/dc1.png "派生列変換の設定")

派生列を追加するには、既存の派生列にカーソルを合わせ、プラス アイコンをクリックします。 **[列の追加]** または **[列パターンの追加]** のいずれかを選択します。 列名がソースからの変数の場合は、列パターンが役に立つ可能性があります。 詳細については、「[列パターン](concepts-data-flow-column-pattern.md)」を参照してください。

![新しい派生列の選択](media/data-flow/columnpattern.png "新しい派生列の選択")

## <a name="build-schemas-in-output-schema-pane"></a>[Output Schema]\(出力スキーマ\) ウィンドウにスキーマを作成する

変更している列と自分のスキーマに追加している列は、[出力スキーマ] ペインに一覧表示されます。 ここでは、単純なデータ構造と複雑なデータ構造を対話形式で作成できます。 フィールドを追加するには、 **[列の追加]** を選択します。 階層を構築するには、 **[Add subcolumn]\(サブ列の追加\)** を選択します。

![サブ列の追加](media/data-flow/addsubcolumn.png "サブ列の追加")

データ フローでの複合型の処理の詳細については、[マッピング データ フローでの JSON の処理](format-json.md#mapping-data-flow-properties)に関する記事を参照してください。

![複合列の追加](media/data-flow/complexcolumn.png "列の追加")

## <a name="data-flow-script"></a>データ フローのスクリプト

### <a name="syntax"></a>構文

```
<incomingStream>
    derive(
           <columnName1> = <expression1>,
           <columnName2> = <expression2>,
           each(
                match(matchExpression),
                <metadataColumn1> = <metadataExpression1>,
                <metadataColumn2> = <metadataExpression2>
               )
          ) ~> <deriveTransformationName>
```

### <a name="example"></a>例

次の例は、受信ストリーム `MoviesYear` を受け取り 2 つの派生列を作成する、`CleanData` という名前の派生列です。 最初の派生列は、列 `Rating` を整数型の Rating の値で置き換えます。 2 番目の派生列は、名前が 'movies' で始まる各列に一致するパターンです。 一致した列ごとに、一致する列の値と等しい列 `movie` を、'movie_' というプレフィックスを付けて作成します。 

Data Factory UX では、この変換は次の図のようになります。

![派生の例](media/data-flow/derive-script1.png "派生の例")

この変換のデータ フロー スクリプトは、次のスニペットに含まれています。

```
MoviesYear derive(
                Rating = toInteger(Rating),
                each(
                    match(startsWith(name,'movies')),
                    'movie' = 'movie_' + toString($$)
                )
            ) ~> CleanData
```

## <a name="next-steps"></a>次のステップ

- 「[Mapping Data Flow の式言語](data-flow-expression-functions.md)」で詳細を確認します。
