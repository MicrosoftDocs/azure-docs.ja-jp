---
title: "モデルをトレーニングするためのデータの収集: Machine Learning Recommendations API | Microsoft Docs"
description: "Azure Machine Learning Recommendations - モデルをトレーニングするためのデータの収集"
services: cognitive-services
documentationcenter: 
author: luiscabrer
manager: jhubbard
editor: cgronlun
ms.assetid: 4040d2b7-bd79-4946-ad83-5a31529151f8
ms.service: cognitive-services
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2016
ms.author: luisca
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: c4b748b601be52b8a27540595f99dbbb3a7cf2ef


---
# <a name="collecting-data-to-train-your-model"></a>モデルをトレーニングするためのデータの収集
Recommendations API は、特定のユーザーにどの項目を勧めればよいかを調べるために、過去のトランザクションから学習します。

モデルを作成した後、トレーニングを行う前に、カタログ ファイルと使用状況データという 2 つの情報を提供する必要があります。

> [クイック スタート ガイド](cognitive-services-recommendations-quick-start.md)をまだ完了していない場合は、完了しておくことをお勧めします。
> 
> 

## <a name="catalog-data"></a>カタログ データ
### <a name="catalog-file-format"></a>カタログ ファイルの形式
カタログ ファイルには、顧客に提供している項目についての情報が含まれています。
カタログ データは、次の形式に従う必要があります。

* 特徴なし - `<Item Id>,<Item Name>,<Item Category>[,<Description>]`
* 特徴あり - `<Item Id>,<Item Name>,<Item Category>,[<Description>],<Features list>`

#### <a name="sample-rows-in-a-catalog-file"></a>カタログ ファイルのサンプル行
特徴なし:

    AAA04294,Office Language Pack Online DwnLd,Office
    AAA04303,Minecraft Download Game,Games
    C9F00168,Kiruna Flip Cover,Accessories

特徴あり:

    AAA04294,Office Language Pack Online DwnLd,Office,, softwaretype=productivity, compatibility=Windows
    BAB04303,Minecraft DwnLd,Games, softwaretype=gaming,, compatibility=iOS, agegroup=all
    C9F00168,Kiruna Flip Cover,Accessories, compatibility=lumia,, hardwaretype=mobile

#### <a name="format-details"></a>形式の詳細
| 名前 | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| 項目 ID |はい |[A-z]、[a-z]、[0-9]、[_] (アンダースコア)、[-] (ダッシュ) <br> 最大長: 50 |項目の一意識別子 |
| Item Name |はい |任意の英数字<br>  最大長: 255 |項目名。 |
| Item Category |はい |任意の英数字 <br>  最大長: 255 |この項目が属しているカテゴリ (例: 料理本、ドラマ...)。空にすることができます。 |
| Description |いいえ。ただし特徴が存在する場合を除きます (しかし、空にすることはできます) |任意の英数字 <br>  最大長: 4000 |この項目の説明。 |
| Features list |なし |任意の英数字 <br>  最大長: 4000、特徴の最大数: 20 |モデルの推奨事項を強化するために使用できる "特徴名=特徴値" のコンマ区切りの一覧。 |

#### <a name="uploading-a-catalog-file"></a>カタログ ファイルのアップロード
カタログ ファイルのアップロードについては、 [API リファレンス](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f316efeda5650db055a3e1) を参照してください。  

カタログ ファイルの内容は要求本文として渡す必要があることに注意してください。

複数の呼び出しで同じモデルに複数のカタログ ファイルがアップロードされた場合は、新しいカタログ項目のみを挿入します。 既存の項目は、元の値でそのまま残ります。 このメソッドを使用して、カタログ データを更新することはできません。

> 注: ファイルの最大サイズは、200 MB です。
> カタログ内でサポートされる項目の最大数は 100,000 個です。
> 
> 

## <a name="why-add-features-to-the-catalog"></a>カタログに特徴を追加する理由
推奨エンジンは、どの項目が顧客に好まれたり購入されたりする可能性があるかを示す統計モデルを作成します。 今まで扱ったことがない新製品がある場合は、共起だけでモデルを作成することはできません。 たとえば、店で新たに "子供用バイオリン" の販売を始めるとします。以前にそのバイオリンを販売したことがないため、そのバイオリンと一緒に他のどの項目を推奨すればよいかがわかりません。

ただし、エンジンがそのバイオリンに関する情報 (楽器であること、7 ～ 10 歳の子供用であること、高価なバイオリンではないことなど) を認識している場合、エンジンは同じような特徴を持つ他の製品から学習できます。 たとえば、過去にバイオリンを販売したことがあり、通常、バイオリンの購入者はクラシック音楽の CD や譜面台を購入する傾向にあります。  新しいバイオリンがあまり使用されていなくても、システムは、特徴どおしのこうしたつながりを見つけ出して、特徴に基づいて推奨事項を提供することができます。

特徴は、カタログ データの一部としてインポートされた後、その順位 (またはモデル内での特徴の重要度) は、順位のビルドが実行されたときに関連付けられます。 使用状況データと項目の種類のパターンに従って特徴の順位を変更できます。 しかし、使用状況と項目の一貫性を保つため、順位付けの変動は小さくなければなりません。 特徴の順位は負以外の数値です。 数値 0 は、特徴が順位付けされていないことを意味します (最初の順位付けのビルドが完了する前にこの API を呼び出すと、これが発生します)。 順位付けの日付をスコアの鮮度と言います。

### <a name="features-are-categorical"></a>特徴はカテゴリ
これは、カテゴリのような特徴を作成する必要があるという意味です。 たとえば、"price=9.34" はカテゴリのような特徴ではありません。 一方、"priceRange=Under5Dollars" のような特徴はカテゴリです。 よくある別の間違いは、項目の名前を特徴として使用することです。 このようにすると項目の名前が一意になるため、カテゴリを表さなくなります。 特徴は、項目のカテゴリを表すようにしてください。

### <a name="how-manywhich-features-should-i-use"></a>使用する特徴の種類と数
推奨ビルドは最終的には、最大 20 個の特徴を持つモデルのビルドをサポートします。 20 個を超える特徴をカタログ内の項目に割り当てることもできますが、順位付けのビルドを実行して上位の特徴のみを選択する必要があります  (ランクが 2.0 以上の特徴は非常に優れています)。 

### <a name="when-are-features-actually-used"></a>特徴を実際に使用する場合
特徴は、トランザクション情報のみに基づいて推奨を提供するのに十分なトランザクション データがない場合に、モデルによって使用されます。 したがって、特徴が最も大きな影響を及ぼすのは、"コールド項目" (ほとんどトランザクションがない項目) です。 すべての項目に十分なトランザクション情報がある場合は、特徴を使用してモデルを強化する必要がないこともあります。

### <a name="using-product-features"></a>製品の特徴の使用
ビルドの一部として特徴を使用するには、次の手順を実行します。

1. カタログをアップロードするときに、カタログに特徴が含まれていることを確認します。
2. 順位付けのビルドをトリガーします。 これで、特徴の重要度/順位の分析が行われます。
3. 次のようにビルド パラメーターを設定して、推奨ビルドをトリガーします。useFeaturesInModel を true、allowColdItemPlacement を true に設定し、modelingFeatureList にはモデルの強化に使用する特徴のコンマ区切り一覧を設定します。 詳細については、[Recommendations ビルド タイプのパラメーターに関するページ](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3d0)をご覧ください。

## <a name="usage-data"></a>使用状況データ
使用状況ファイルには、それらの項目がどのように使用されているかやビジネスのトランザクションに関する情報が含まれています。

#### <a name="usage-format-details"></a>使用状況の形式の詳細
使用状況ファイルは CSV (コンマ区切り値) ファイルで、使用状況ファイルの各行はユーザーと項目の相互作用を表します。 各行の形式は次のようになります。<br>
`<User Id>,<Item Id>,<Time>,[<Event>]`

| 名前 | 必須 | 型 | Description |
| --- | --- | --- | --- |
| ユーザー ID |あり |[A-z]、[a-z]、[0-9]、[_] (アンダースコア)、[-] (ダッシュ) <br>  最大長: 255 |ユーザーの一意識別子。 |
| 項目 ID |はい |[A-z]、[a-z]、[0-9]、[_] (アンダースコア)、[-] (ダッシュ) <br> 最大長: 50 |項目の一意識別子 |
| Time |はい |YYYY/MM/DDTHH:MM:SS 形式の日付 (例: 2013/06/20T10:00:00) |データの時間。 |
| イベント |なし |次のいずれか:<br>• Click<br>• RecommendationClick<br>•    AddShopCart<br>• RemoveShopCart<br>• Purchase |トランザクションの種類。 |

#### <a name="sample-rows-in-a-usage-file"></a>使用状況ファイルのサンプル行
    00037FFEA61FCA16,288186200,2015/08/04T11:02:52,Purchase
    0003BFFDD4C2148C,297833400,2015/08/04T11:02:50,Purchase
    0003BFFDD4C2118D,297833300,2015/08/04T11:02:40,Purchase
    00030000D16C4237,297833300,2015/08/04T11:02:37,Purchase
    0003BFFDD4C20B63,297833400,2015/08/04T11:02:12,Purchase
    00037FFEC8567FB8,297833400,2015/08/04T11:02:04,Purchase

#### <a name="uploading-a-usage-file"></a>使用状況ファイルのアップロード
使用状況ファイルのアップロードについては、 [API リファレンス](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f316efeda5650db055a3e2) を参照してください。
使用状況ファイルの内容を HTTP 呼び出しの本文として渡す必要があることに注意してください。

> 注:
> 
> ファイルの最大サイズは 200 MB です。 複数の使用状況ファイルをアップロードすることができます。
> 
> 使用状況データをモデルに追加する前に、カタログ ファイルをアップロードする必要があります。 トレーニング段階では、カタログ ファイル内の項目のみが使用されます。 その他の項目はすべて無視されます。
> 
> 

## <a name="how-much-data-do-you-need"></a>必要なデータ量
モデルの品質は、データの品質と量に大きく依存します。
システムは、ユーザーがさまざまな商品をいつ購入したか (これを共起と呼びます) を学習します。 FBT ビルドでは、同じトランザクションで購入された商品を把握することも重要となります。 

1 つの目安として、トランザクションが 20 個以上あれば、ほとんどの商品が含まれています。したがって、カタログに 10,000 個の商品が含まれている場合は、少なくともその 20 倍の数のトランザクション (約 200,000 個のトランザクション) を保持することをお勧めします。 繰り返しますが、これはあくまで目安です。 実際のデータでテストする必要があります。

モデルを作成したら、 [オフライン評価](cognitive-services-recommendations-buildtypes.md) を実行して、モデルがどの程度正常に機能する可能性があるかを確認できます。




<!--HONumber=Nov16_HO3-->


