---
title: "データが回答できる質問をする - データ サイエンスの問題 - Azure | Microsoft Docs"
description: "初心者向けデータ サイエンス ビデオ 3 では、鋭いデータ サイエンスの質問を考案する方法について説明します。 分類の質問と回帰の質問の比較も含まれます。"
keywords: "データ サイエンスの問題,データ サイエンスの質問,質問の考案,回帰の質問,分類の質問,シャープな質問"
services: machine-learning
documentationcenter: na
author: cjgronlund
manager: jhubbard
editor: cjgronlund
ms.assetid: 5b9501e3-9964-417a-8ffc-8913103da77b
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/09/2017
ms.author: cgronlun;garye
translationtype: Human Translation
ms.sourcegitcommit: b167b88d227bf239fd5d3676e48e04436f140c34
ms.openlocfilehash: 3c476cf99ebfe78f5681019fc055d1a07b61ef02


---
# <a name="ask-a-question-you-can-answer-with-data"></a>データで回答できる質問をする
## <a name="video-3-data-science-for-beginners-series"></a>ビデオ 3: 初心者向けデータ サイエンス シリーズ
初心者向けデータ サイエンス ビデオ 3 では、データ サイエンスの問題を質問として考案する方法について説明します。 このビデオには、分類アルゴリズムと回帰アルゴリズムの質問の比較が含まれます。

このシリーズを最大限に活用するには、このビデオをすべて視聴してください。 [ビデオの一覧に移動する](#other-videos-in-this-series)

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Data-science-for-beginners-Ask-a-question-you-can-answer-with-data/player]
>
>

## <a name="other-videos-in-this-series"></a>このシリーズの他のビデオ
*"初心者向けデータ サイエンス"* は、データ サイエンスについて簡単に説明した&5; 本の短いビデオです。

* ビデオ 1: [データ サイエンスが回答する 5 つの質問](machine-learning-data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 分 14 秒)*
* ビデオ 2: [データ サイエンス用のデータの準備はお済みですか?](machine-learning-data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 分 56 秒)*
* ビデオ 3: データで回答できる質問をする
* ビデオ 4: [単純なモデルで回答を予測する](machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 分 42 秒)*
* ビデオ 5: [他のユーザーの成果物をコピーしてデータ サイエンスを実行する](machine-learning-data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 分 18 秒)*

## <a name="transcript-ask-a-question-you-can-answer-with-data"></a>トランスクリプト: データで回答できる質問をする
"初心者向けデータ サイエンス" シリーズの&3; 番目のビデオへようこそ。  

このビデオでは、データで回答できる質問を考えるためのヒントが得られます。

このシリーズの前の 2 本のビデオ、「データ サイエンスが回答できる 5 つの質問」と「データ サイエンス用のデータの準備はお済みですか?」を視聴すると、このビデオをより効果的に活用できます。

## <a name="ask-a-sharp-question"></a>シャープな質問をする
これまで、データ サイエンスが、名前 (カテゴリまたはラベルとも呼ばれます) と数値を使用して質問に対する回答を予測するプロセスであることを説明してきました。 ただし、どのような質問でも可能なわけではありません。"*シャープな質問*" である必要があります。

あいまいな質問には、名前や数値で答える必要がないからです。 シャープな質問であることが必須です。

魔法のランプと、どんな質問にも誠実に答えてくれる精霊を見つけたと想像してください。 ただし、この精霊はいたずら好きなので、その場をやり過ごせるようなあいまいで紛らわしい回答をしようとします。 こちらが知りたいことを答えずにいられないくらい隙のない質問で精霊を釘付けにする必要があります。

"私の株の価格はどうなりますか" のようなあいまいな質問をした場合、精霊は "価格は変動します" と答えるでしょう。 誠実な回答ですが、何の役にも立ちません。

"私の株の売却価格は来週どうなりますか" のようなシャープな質問をした場合、ランプの精は具体的な回答を返して売却価格を予測せざるを得ません。

## <a name="examples-of-your-answer-target-data"></a>回答の例: ターゲット データ
質問を考えたら、データの中に回答の例が用意されているかどうかを確認します。

質問が "私の株の売却価格は来週どうなりますか" の場合、 データに株価の履歴が含まれているかどうかを確認する必要があります。

質問が "全車両の中でどの車が最初に故障しますか" の場合は、 データにこれまでの障害に関する情報が含まれているかどうかを確認する必要があります。

![ターゲット データ - 回答の例。 データ サイエンスの質問を考案する。](./media/machine-learning-data-science-for-beginners-ask-a-question-you-can-answer-with-data/machine-learning-data-science-target-data.png)

これらの回答の例をターゲットと呼びます。 ターゲットとは、将来のデータ ポイントについて予測する対象のことで、カテゴリであることも数値であることもあります。

ターゲット データを取得していない場合は、取得する必要があります。 ターゲットなしで質問に回答することはできません。

## <a name="reformulate-your-question"></a>質問を考え直す
質問を言い換えてみることで、より意味のある結果が得られることがあります。

"これはデータ ポイント A かデータ ポイント B か" という質問では、 何かのカテゴリ (または名前やラベル) を予測します。 この質問に回答するには、"*分類アルゴリズム*" を使用します。

"どのくらいの量か"  または "どのくらいの数か" という質問では、 数量を予測します。 この質問に回答するには、"*回帰アルゴリズム*" を使用します。

これらの質問をどのように変換できるかを確認するには、"新聞のどの記事がこの読者にとって最も興味深いか" という質問に注目します。 これは、さまざまな可能性から&1; つの選択肢の予測を求める質問です。 つまり "A、B、C、D のどれか" は、分類アルゴリズムを使用します。

ただし、この質問は、"このリストの各記事はこの読者にとってどのくらい興味深いか" と言い換えると回答が簡単になります。 これで各記事に数値のスコアを付けることができるようになります。スコアの点数が最も高い記事を特定するのは簡単です。 これは、分類に関する質問を回帰的な質問つまり "どの程度か" に言い換えたことになります。

![質問を考え直す。 分類の質問と回帰の質問。](./media/machine-learning-data-science-for-beginners-ask-a-question-you-can-answer-with-data/machine-learning-data-science-classification-question-vs-regression-question.png)

質問方法は、どのアルゴリズムなら回答が得られるかを知るための手掛かりになります。

新聞記事の例に見られるように、特定のアルゴリズム ファミリが密接に関連していることがわかります。 質問を作り直すと、最も役に立つ回答を提供するアルゴリズムを使用することができます。

ただし、重要なのは、シャープな質問、つまりデータで回答できる質問をすることです。 回答するうえで適切なデータが用意されていることも確認してください。

ここでは、データによって回答可能な質問をするための基本原則について説明してきました。

Microsoft Azure Machine Learning の "初心者向けデータ サイエンス" の他のビデオも必ずご覧ください。

## <a name="next-steps"></a>次のステップ
* [Machine Learning Studio で初めてのデータ サイエンス実験を実行してみる](machine-learning-create-experiment.md)
* [Microsoft Azure での Machine Learning の概要を学習する](machine-learning-what-is-machine-learning.md)



<!--HONumber=Feb17_HO2-->


