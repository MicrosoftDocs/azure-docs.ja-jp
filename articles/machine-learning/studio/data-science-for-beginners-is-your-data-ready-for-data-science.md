---
title: データの評価
titleSuffix: Azure Machine Learning Studio
description: データ サイエンスを準備するためにデータが満たす必要のある 4 つの条件。 このビデオでは、基本的なデータの評価を支援する具体的な例を説明しています。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.custom: seodec18
ms.date: 03/22/2019
ms.openlocfilehash: 1d9475b49b3ef8a2e672e1ce505a7d94d19cb14f
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67875319"
---
# <a name="is-your-data-ready-for-data-science"></a>データ サイエンス用のデータの準備はお済みですか?
## <a name="video-2-data-science-for-beginners-series"></a>ビデオ 2: 初心者向けデータ サイエンス シリーズ
データを評価して、データ サイエンスに備えるための基本的な条件を満たしていることを確認する方法について説明します。

このシリーズを最大限に活用するには、このビデオをすべて視聴してください。 [ビデオの一覧に移動する](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Shows/SupervisionNotRequired/9/player]
>
>

## <a name="other-videos-in-this-series"></a>このシリーズの他のビデオ
*"初心者向けデータ サイエンス"* は、データ サイエンスについて簡単に説明した 5 本の短いビデオです。

* ビデオ 1: [データ サイエンスが回答する 5 つの質問](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 分 14 秒)*
* ビデオ 2: データ サイエンス用のデータの準備はお済みですか?
* ビデオ 3: [データで回答できる質問をする](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 分 17 秒)*
* ビデオ 4: [単純なモデルで回答を予測する](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 分 42 秒)*
* ビデオ 5: [他のユーザーの成果物をコピーしてデータ サイエンスを実行する](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 分 18 秒)*

## <a name="transcript-is-your-data-ready-for-data-science"></a>トランスクリプト: データ サイエンス用のデータの準備はお済みですか?
"データ サイエンス用のデータの準備はお済みですか?" へようこそ。 これは、"*初心者向けデータ サイエンス*" シリーズの 2 番目のビデオです。  

データ サイエンスから必要な回答を得るには、高品質の原材料を提供して操作できるようにする必要があります。 ピザを作るのと同じで、最初の材料が良いほど最終的な製品も良くなります。 

## <a name="criteria-for-data"></a>データの条件
データ サイエンスには、次のような、まとめて取得する必要のある特定の材料があります。

* 関連性がある
* 接続中
* 正確である
* 操作できるだけの十分な量がある

## <a name="is-your-data-relevant"></a>データに関連性がありますか?
そのため、最初の材料として、関連性のあるデータが必要です。

![関連性のあるデータと関連性のないデータの比較 - データの評価](./media/data-science-for-beginners-is-your-data-ready-for-data-science/relevant-and-irrelevant-data.png)

左側の表には、ボストンのバーの外でテストされた 7 名の人びとの血中アルコール濃度、レッド ソックスの最後のゲームでの打率、および最寄りのコンビニエンス ストアでの牛乳の価格が表示されています。

これは申し分なく正当なデータです。 唯一の問題は、関連性がないことです。 これらの数値の間に明確な関係はありません。 誰かに現在の牛乳の価格とレッド ソックスの打率を教えられても、彼らの血中アルコール濃度を推測できる方法はありません。

次は右側の表をご覧ください。 今度は、各人の体重を測定すると共に、彼らが口にした飲み物の数を数えました。  各行の数値は相互に関連しています。 体重と飲んだマルガリータの数を伝えれば、血中アルコール濃度を推測できるでしょう。

## <a name="do-you-have-connected-data"></a>データは連続していますか?
次の材料は、連続しているデータです。

![連続しているデータと連続していないデータの比較 - データの条件、データの準備](./media/data-science-for-beginners-is-your-data-ready-for-data-science/connected-vs-disconnected-data.png)

ここにハンバーガーの品質に関連するデータとして、グリルの温度、パテの重さ、地元の食品雑誌での評価があります。 ただし左側の表内の空白に注目してください。

ほとんどのデータ セットで、一部の値が抜けています。 このような抜けはよくあることであり、回避する方法はあります。 ただし、あまりにも抜けが多いと、データがスイスのチーズのような状態になります。

左側の表を見ると、不足しているデータが数多くあり、グリルの温度とパテの重さがどのような種類の関係にあるのかを考え付くのは困難です。 この例は、連続していないデータを示しています。

一方、右側の表は、すべて揃っている、連続したデータの例です。

## <a name="is-your-data-accurate"></a>データは正確ですか?
次の材料は正確性です。 ここに、当てるべき 4 つの的があります。

![正確なデータと不正確なデータの比較 - データの条件](./media/data-science-for-beginners-is-your-data-ready-for-data-science/inaccurate-vs-accurate-data.png)

右上の的をご覧ください。 的の中心の周囲には緊密なグループが存在します。 もちろん、これは正確です。 奇妙なことに、データ サイエンスの言語では、その真下の的に対するパフォーマンスも正確であると見なされます。

これらの矢の中心を正確に描くと、それが的の中心に非常に近いことがわかります。 矢はターゲットの周囲に散らばっているため、不正確なように見えますが、的を中心にしているため、正確であると見なされます。

次に左上のターゲットについて考えてみましょう。 ここでは、矢は非常に近くにまとまって当たり、緊密なグループができています。 これは明瞭ですが、中心が的の中心から外れているため正確ではありません。 左下の的にある矢は不正確であり、かつ不明瞭です。 この射手はもっと練習が必要ですね。

## <a name="do-you-have-enough-data-to-work-with"></a>使用するデータは十分ありますか?
最後に、材料 #4 は十分なデータです。

![分析用のデータは十分ありますか? データの評価](./media/data-science-for-beginners-is-your-data-ready-for-data-science/barely-enough-data.png)

表内の各データ ポイントを絵画の筆のひと塗りだと考えてください。 まだその一部しかないと、絵画ははっきりせず、それが何であるかを当てることは困難な場合があります。

筆で数回塗ると、絵画が少し鮮明になります。

何回か塗りを重ねるとやっと、ある程度大まかな判断をするのに十分なだけ見えるようになります。 私が訪問したいと思っている場所でしょうか? 明るく、水もきれいですね。そう、ここは私が休暇を過ごす場所です。

データを追加すると、画像が明確になり、より緻密に判断することができます。 さて、左側の岸にホテルが 3 軒見えるようになりました。 一番手前のホテルの建築的な特徴に気が付くはずです。 眺めがよいので 3 階に泊まることさえ選択できるでしょう。

関連性があり、連続していて、正確で、かつ十分なデータがあれば、ある程度高品質なデータ サイエンスを実行するために必要なすべての材料が揃っています。

Microsoft Azure Machine Learning Studio の "*初心者向けデータ サイエンス*" の他の 4 つのビデオも必ずご覧ください。

## <a name="next-steps"></a>次の手順
* [Machine Learning Studio で初めてのデータ サイエンス実験を実行してみる](create-experiment.md)
* [Microsoft Azure での Machine Learning の概要を学習する](/azure/machine-learning/preview/overview-what-is-azure-ml)
