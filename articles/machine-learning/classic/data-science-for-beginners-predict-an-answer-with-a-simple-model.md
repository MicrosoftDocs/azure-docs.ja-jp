---
title: 'ML Studio (classic): 回帰モデルで回答を予測する - Azure'
description: 初心者向けデータ サイエン ビデオ 4 で、単純な回帰モデルを作成して価格を予測する方法。 線形回帰とターゲット データが含まれます。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.custom: seodec18
ms.date: 03/22/2019
ms.openlocfilehash: c6d78b10b98abe33744673459b3d31d8941aac89
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100520410"
---
# <a name="predict-an-answer-with-a-simple-model"></a>単純なモデルで回答を予測する


## <a name="video-4-data-science-for-beginners-series"></a>ビデオ 4: 初心者向けデータ サイエンス シリーズ
初心者向けデータ サイエン ビデオ 4 では、単純な回帰モデルを作成してダイヤモンドの価格を予測する方法について説明します。 ターゲット データを使用して回帰モデルを引き出します。

このシリーズを最大限に活用するには、このビデオをすべて視聴してください。 [ビデオの一覧に移動する](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/data-science-for-beginners-series-predict-an-answer-with-a-simple-model/player]
>
>

## <a name="other-videos-in-this-series"></a>このシリーズの他のビデオ
*"初心者向けデータ サイエンス"* は、データ サイエンスについて簡単に説明した 5 本の短いビデオです。

* ビデオ 1: [データ サイエンスが回答する 5 つの質問](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 分 14 秒)*
* ビデオ 2: [データ サイエンス用のデータの準備はお済みですか?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 分 56 秒)*
* ビデオ 3: [データで回答できる質問をする](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 分 17 秒)*
* ビデオ 4: 単純なモデルで回答を予測する
* ビデオ 5: [他のユーザーの成果物をコピーしてデータ サイエンスを実行する](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 分 18 秒)*

## <a name="transcript-predict-an-answer-with-a-simple-model"></a>トランスクリプト: 単純なモデルで回答を予測する
"初心者向けデータ サイエンス" シリーズの 4 番目のビデオへようこそ。 ここでは、単純なモデルを作成して予測を行います。

*"モデル"* とは、データについての単純化されたストーリーです。 何が言いたいかを説明します。

## <a name="collect-relevant-accurate-connected-enough-data"></a>関連性があり、正確で、連続している、十分なデータを収集する
私がダイヤモンドを購入しようとしているとします。 1\.35 カラットのダイヤモンドが付いた祖母の指輪があり、その料金を知りたいと思っています。 メモ帳とペンを持って宝石店に行き、ケース内のすべてのダイヤモンドの価格と重さ (カラット単位) を書き留めます。 最初のダイヤモンドは、1.01 カラットで 7,366 ドルです。

店内の他のすべてのダイヤモンドに対してこれを行います。

![ダイヤモンドのデータの列](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/diamond-data.png)

リストが 2 列になっていることに注目してください。 各列は、重さ (カラット単位) と価格という異なる属性を持ち、各行は 1 つのダイヤモンドを表す 1 つのデータ ポイントです。

実際に、これで小さなデータ セット、つまり 1 つのテーブルができたことになります。 このテーブルが品質の条件を満たしていることに注目してください。

* データに **関連性がある** : 重さは間違いなく価格に関連しています。
* **正確である** : 書き留めた価格をダブルチェックしました。
* **連続している** : 各列にはどこにも空白がありません。
* さらに、ご覧のとおり、質問に回答するのに **十分な** データがあります。

## <a name="ask-a-sharp-question"></a>シャープな質問をする
ここで、厳しい質問を投げかけます。"1.35 カラットのダイヤモンドを購入するにはいくらかかるか?"

作成したリストには 1.35 カラットのダイヤモンドはありません。そこで、残りのデータを使用して、質問に対する回答を得ます。

## <a name="plot-the-existing-data"></a>既存のデータをプロットする
最初に、重さを図に表すために水平方向の数直線を描きます。これを軸と呼びます。 重さの範囲は 0 ～ 2 であるため、この範囲を網羅する線を引き、0.5 カラットごとに目盛りを描きます。

次に、価格を記録するために垂直方向の軸を描いて、水平方向の重さの軸につなげます。 これはドル単位で記録します。 座標軸のセットができました。

![重さと価格の軸](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/weight-and-price-axes.png)

このデータを取得し、 *"散布図"* に変換します。 数値のデータ セットを視覚化するにはこの方法がお勧めです。

最初のデータ ポイントでは、1.01 カラットの位置で垂直軸の見当を付けてください。 次に、7,366 ドルの位置で水平軸の見当を付けてください。 それらが交差する場所に点を描きます。 これが 1 番目のダイヤモンドを表します。

リストの各ダイヤモンドで同じ作業を行います。 すべて終わると、点の集まりが得られます。点はそれぞれダイヤモンドを表します。

!["散布図"](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/scatter-plot.png)

## <a name="draw-the-model-through-the-data-points"></a>データ ポイントからモデルを描く
ここで点を見ながら目を細めると、点の集まりがぼんやりとした太い線に見えてきます。 マーカーを使って、これを通る直線を描画できます。

直線を描画することで、"*モデル*" が作成されました。 これは、現実の世界を把握し、極度に単純化されたマンガ版を作成したと考えてください。 マンガは正しくありません。直線はすべてのデータ ポイントを通っているわけではありません。 ただし、この簡略化は便利です。

![線形回帰直線](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/linear-regression-line.png)

直線から外れた点があってもかまいません。 データ サイエンティストの説明では、この直線がモデルです。また、各点には、"*ノイズ*" または "*分散*" が付随しています。 基になる理想的な関係が存在し、一方、ノイズや不確実性を含むザラザラした現実世界があります。

"*どの程度か*" という質問に回答しようとしているため、これを "*回帰*" と呼びます。 また、直線を使用しているため、これは *"線形回帰"* です。

## <a name="use-the-model-to-find-the-answer"></a>モデルを使用して回答を見つける
これでモデルが用意されたため、質問を出します。1.35 カラットのダイヤモンドはいくらかかるか?

この質問に回答するには、1.35 カラットに見当を付けて垂直方向に直線を描画します。 モデルの線と交差する位置で、水平方向の線をドルの軸に対して見当を付けます。 ちょうど 10,000 に当たります。 できました! これが答えです。1.35 カラットのダイヤモンドは約 10,000 ドルします。

![モデル上で回答を見つける](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/find-the-answer.png)

## <a name="create-a-confidence-interval"></a>信頼区間を作成する
この予測がどの程度の精度なのかという疑問を抱くのは当然です。 1\.35 カラットのダイヤモンドが 10,000 ドルに非常に近いのか、はるかに高いのか (または安いのか) を知っておくと便利です。 この答えを出すには、ほとんどの点を囲むように回帰直線の周りに枠を描画してみましょう。 この枠は "*信頼区間*" と呼ばれます。「価格はこの枠内に収まる」ということをかなりの自信を持って言うことができます。その理由は、過去のほとんどの価格がその中に収まっているからです。 1\.35 カラットの直線が枠の上端および下端と交差する点から水平な直線を 2 本描画できます。

!["信頼区間"](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/confidence-interval.png)

これで信頼区間について言及できるようになりました。1.35 カラットのダイヤモンドの価格は約 10,000 ドルであると自信を持って言うことができます。ただし、下限として 8,000 ドル、上限として 12,000 ドルになる可能性があります。

## <a name="were-done-with-no-math-or-computers"></a>数学やコンピューターなしで実行できる
データ サイエンティストが有料で行うことを、ここでは描画のみで実行しました。

* データで回答できる質問をする
* "*線形回帰*" を使用して "*モデル*" を構築する
* "*予測*" と "*信頼区間*" を作成する

数学やコンピューターを使用せずにこれを実行しました。

次のような情報を得ていた場合は、

* ダイヤモンドのカット
* 色の違い (ダイヤモンドがどのくらい白に近いか)
* ダイヤモンドの含有物の数

列を増やします。 その場合は、数学が役立ちます。 3 列以上ある場合、紙に点を描画するのは困難です。 数学によって、線または面をデータと非常にうまく適合させることができます。

また、ダイヤモンドがほんのわずかではなく、2,000 個または 200 万個あった場合は、コンピューターを使用してこの処理をより高速に実行できます。

ここでは、線形回帰を実行する方法について説明し、データを使用して予測しました。

Microsoft Azure Machine Learning Studio (クラシック) の "初心者向けデータ サイエンス" の他のビデオもご覧ください。

## <a name="next-steps"></a>次のステップ
* [Machine Learning Studio (クラシック) で最初のデータ サイエンス実験を試行する](create-experiment.md)
* [Microsoft Azure での Machine Learning の概要を学習する](../overview-what-is-azure-ml.md)