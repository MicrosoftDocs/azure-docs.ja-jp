---
title: 'ML Studio (classic): 初心者向けデータ サイエンス解説 - Azure'
description: 初心者向けデータ サイエンス解説では、「データ サイエンスが回答する 5 つの質問」から始まる 5 つのショート ビデオで基本的な概念を説明します。 Azure Machine Learning。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.custom: seodec18
ms.date: 03/22/2019
ms.openlocfilehash: e9bbadc95be3479da82d91e9a4a4f712fbc32c57
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100520393"
---
# <a name="data-science-for-beginners-video-1-the-5-questions-data-science-answers"></a>初心者向けデータ サイエンス解説ビデオ 1:データ サイエンスが回答する 5 つの質問

トップ データ サイエンティストによる "*初心者向けデータ サイエンス*" の 5 本の短いビデオでは、データ サイエンスの概要を簡単に説明します。 基本的な内容を扱っているビデオですが、データ サイエンスの実行に関心を持っている場合や、データ サイエンティストと共に作業する場合に便利です。

この最初のビデオでは、データ サイエンスが回答できる質問の種類について説明します。 このシリーズを最大限に活用するには、このビデオをすべて視聴してください。 [ビデオの一覧に移動する](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Shows/SupervisionNotRequired/8/player]
>
>

## <a name="other-videos-in-this-series"></a>このシリーズの他のビデオ
*"初心者向けデータ サイエンス"* は、約 25 分でデータ サイエンスを簡単に説明します。 ぜひ 5 つのビデオをすべてご覧ください。

* ビデオ 1: データ サイエンスが回答する 5 つの質問
* ビデオ 2: [データ サイエンス用のデータの準備はお済みですか?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 分 56 秒)*
* ビデオ 3: [データで回答できる質問をする](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 分 17 秒)*
* ビデオ 4: [単純なモデルで回答を予測する](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 分 42 秒)*
* ビデオ 5: [他のユーザーの成果物をコピーしてデータ サイエンスを実行する](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 分 18 秒)*

## <a name="transcript-the-5-questions-data-science-answers"></a>トランスクリプト: データ サイエンスが回答する 5 つの質問
こんにちは。 ビデオ シリーズ *"初心者向けデータ サイエンス"* へようこそ。

データ サイエンスと聞いて怖じ気づく方もいるかもしれないため、ここでは数式やコンピューター プログラミングの専門用語を使用せずにその基礎を紹介します。

最初のこのビデオでは、"データ サイエンスが回答する 5 つの質問" について説明します。

データ サイエンスでは、質問に対する回答を予測するために、数値と名前 (カテゴリやラベルとも呼ばれます) を使用します。

意外かもしれませんが、 *"データ サイエンスが回答する質問は次の 5 つのみです"* 。

* A か B か
* 異常か
* どのくらいの量または数か
* どのような構成か
* 次に何をすべきか

各質問には、アルゴリズムと呼ばれる機械学習メソッドの別個のファミリが回答します。

アルゴリズムをレシピ、データを食材として考えると理解しやすくなります。 アルゴリズムによって、回答を得るためにデータを組み合わせて混ぜる方法が決まります。 コンピューターはミキサーのようなものです。 コンピューターは人の代わりにアルゴリズムの面倒な処理のほとんどを極めて高速に実行します。

## <a name="question-1-is-this-a-or-b-uses-classification-algorithms"></a>"質問 1:A か B か" は分類アルゴリズムを使用する
"質問 1: A か B か" から始めましょう。A か B か

![分類アルゴリズム:A か B か](./media/data-science-for-beginners-the-5-questions-data-science-answers/classification-algorithms.png)

このアルゴリズムのファミリは、2 クラス分類と呼ばれます。

2 つの解答が考えられる質問に役立ちます。

次に例を示します。

* 次の 1,000 マイルでこのタイヤが故障するか、しないか
* 集客が多いのは5 ドルのクーポンと 25% の割引のどちらか

この質問は、"A、B、C、D のどれか" などのように、3 つ以上の選択肢を含むように言い換えることもできます。  これは多クラス分類と呼ばれ、回答が数個 (または数千個) 考えられる場合に有用です。 多クラス分類では、最も可能性の高いものが選択されます。

## <a name="question-2-is-this-weird-uses-anomaly-detection-algorithms"></a>"質問 2:異常か" は異常検出アルゴリズムを使用する
データ サイエンスが回答できる次の質問は、"異常か" です。異常か この質問は、異常検出と呼ばれるアルゴリズムのファミリによって回答されます。

![異常検出アルゴリズム:異常か](./media/data-science-for-beginners-the-5-questions-data-science-answers/anomaly-detection-algorithms.png)

クレジット カードをお持ちの方は、異常検出のメリットを既に享受しています。 クレジット カード会社では、不正行為の疑いがある場合にアラートを生成できるように、利用者の購買パターンを分析しています。 "異常" な請求とは、通常購入しない店舗での購入や異常に高価なアイテムの購入などです。

この質問は、さまざまな場面で利用できます。 次に例を示します。

* 圧力計の付いた車を所有していて、圧力計の測定値が正常かどうかを知りたい場合
* インターネットを監視していて、インターネットから出力されたメッセージが典型的なものかどうかを知りたい場合

異常検出では、予期しない、または異常なイベントや動作にフラグが設定されます。 これが、問題のある場所を見つける手掛かりになります。

## <a name="question-3-how-much-or-how-many-uses-regression-algorithms"></a>"質問 3:どのくらいの量または数か" は、回帰アルゴリズムを使用します。
機械学習では、"どのくらいの量または数か" への回答も予測できます。 この質問に回答するアルゴリズム ファミリを回帰と呼びます。

![回帰アルゴリズム:どのくらいの量または数か](./media/data-science-for-beginners-the-5-questions-data-science-answers/regression-algorithms.png)

回帰アルゴリズムでは、次のような数値の予測を行います。

* 次の火曜日の気温は何度か  
* 第 4 四半期の売上はいくらか

回帰アルゴリズムは、数値で回答できる質問に答えるときに役立ちます。

## <a name="question-4-how-is-this-organized-uses-clustering-algorithms"></a>"質問 4:どのような構成か" はクラスタリング アルゴリズムを使用する
最後の 2 つはやや高度な質問です。

データ セットの構造を理解したいとき、"どのような構成か?" と質問します。 この質問には、結果が既に明らかな例はありません。

データの構造を探り出す方法は数多くあります。 その 1 つがクラスタリングです。 クラスタリングは、簡単に解釈できるように、データを分離して自然な "塊" にします。 クラスタリングには、1 つの正しい回答はありません。

![クラスタリング アルゴリズム:どのような構成か](./media/data-science-for-beginners-the-5-questions-data-science-answers/clustering-algorithms.png)

クラスタリングの質問の一般的な例を次に示します。

* どの視聴者が同じ種類の映画を好むか
* どのプリンター モデルが同じように故障するか

データの構成を把握することで、行動や出来事について理解を深め、予測できるようになります。  

## <a name="question-5-what-should-i-do-now-uses-reinforcement-learning-algorithms"></a>"質問 5:次に何をすべきか" は強化学習アルゴリズムを使用する
最後の質問 "次に何をすべきか" では、 強化学習と呼ばれるアルゴリズムのファミリを使用します。

強化学習は、ラットと人間の脳が飴と鞭にどのように反応するかによって考え出されました。 このアルゴリズムは、結果から学習して次のアクションを決定します。

通常、強化学習は、人間の指図なしで小さい決定を数多く行う必要がある自動化されたシステムに適しています。

![強化学習アルゴリズム:次に何をすべきか](./media/data-science-for-beginners-the-5-questions-data-science-answers/reinforcement-learning-algorithms.png)

回答される質問は常に、(通常、コンピューターまたはロボットが) どのようなアクションを実行すべきかについてです。 次に例をいくつか示します。

* 家庭用の温度管理システムの場合、温度を調節するかそのままの状態にしておくか  
* 自動運転車の場合、黄色でブレーキをかけるかアクセルを踏むか  
* ロボット掃除機の場合、掃除を続けるか、充電ステーションに戻るか

強化学習アルゴリズムは、そのままの状態のデータを収集して、試行錯誤から学習します。

以上がデータ サイエンスで回答できる 5 つの質問です。

## <a name="next-steps"></a>次のステップ
* [Machine Learning Studio (クラシック) で最初のデータ サイエンス実験を試行する](create-experiment.md)
* [Microsoft Azure での Machine Learning の概要を学習する](../overview-what-is-azure-ml.md)