---
title: マッピング データ フローの条件分割変換
description: Azure Data Factory マッピング データ フローの条件分割変換を使用してデータを複数のストリームに分割する
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/21/2020
ms.openlocfilehash: eece6f97e82f3800d4f59ac1849b34c2a1e4635b
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/22/2020
ms.locfileid: "83800081"
---
# <a name="conditional-split-transformation-in-mapping-data-flow"></a>マッピング データ フローの条件分割変換

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

条件分割変換では、データ行を、一致条件に応じて異なるストリームにルーティングします。 条件分割変換は、プログラミング言語の CASE 決定構造と似ています。 この変換では式を評価し、その結果に基づいて、データ行を指定されたストリームに送ります。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4wKCX]

## <a name="configuration"></a>構成

**[分割]** 設定により、データの行の送り先が、最初に一致したストリームと、一致した全ストリームのどちらになるかが決まります。

データ フローの式ビルダーを使用して、分割条件の式を入力します。 新しい条件を追加する場合は、既存の行にあるプラス記号アイコンをクリックします。 いずれの条件にも一致しない行用に、既定のストリームを追加することもできます。

![条件分割](media/data-flow/conditionalsplit1.png "条件分割のオプション")

## <a name="data-flow-script"></a>データ フローのスクリプト

### <a name="syntax"></a>構文

```
<incomingStream>
    split(
        <conditionalExpression1>
        <conditionalExpression2>
        ...
        disjoint: {true | false}
    ) ~> <splitTx>@(stream1, stream2, ..., <defaultStream>)
```

### <a name="example"></a>例

以下の例は、受信ストリーム `CleanData` を受け取る `SplitByYear` という名前の条件分割変換です。 この変換では、`year < 1960` と `year > 1980` という 2 つの分割条件が設定されています。 最初に一致した条件にデータを送るので、`disjoint` は false に設定しています。 最初の条件に一致した行はすべて、出力ストリーム `moviesBefore1960` に送られます。 残りの行のうち 2 番目の条件に一致したものはすべて、出力ストリーム `moviesAFter1980` に送られます。 その他のすべての行は、既定のストリーム `AllOtherMovies` に送られます。

Data Factory UX では、この変換は次の図のようになります。

![条件分割](media/data-flow/conditionalsplit1.png "条件分割のオプション")

この変換のデータ フロー スクリプトは、次のスニペットに含まれています。

```
CleanData
    split(
        year < 1960,
        year > 1980,
        disjoint: false
    ) ~> SplitByYear@(moviesBefore1960, moviesAfter1980, AllOtherMovies)
```

## <a name="next-steps"></a>次のステップ

条件分割と合わせて使用する一般的なデータ フロー変換には、[結合変換](data-flow-join.md)、[参照変換](data-flow-lookup.md)、[選択変換](data-flow-select.md)があります
