---
title: 信頼度スコア - QnA Maker
titleSuffix: Azure Cognitive Services
description: 信頼度スコアは、回答が特定のユーザー クエリに最適である信頼度を示します。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 06/17/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: c14c607e4c563bbeeaff02b2c2478cc4b4d96ee5
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165137"
---
# <a name="confidence-score-of-a-qna-maker-knowledge-base"></a>QnA Maker ナレッジ ベースの信頼度スコア
ユーザー クエリがナレッジ ベースに対して一致すると、QnA Maker は、信頼度スコアと共に該当する回答を返します。 このスコアは、回答が特定のユーザー クエリに最適である信頼度を示します。 

信頼スコアは 0 から 100 の範囲の数値です。 100 点は完全一致であるの可能性が高いのに対して、0 点は一致する回答が見つからなかったことを意味します｡ スコアが高得点であるほど、回答の信頼度は高くなります。 与えられた 1 つのクエリに対して、複数の回答が返されることがあります｡ その場合、回答は信頼度スコアが高い順に返されます。

次の例では、1 つの QnA エンティティと 2 つの質問があります｡ 


![QnA のペア例](../media/qnamaker-concepts-confidencescore/ranker-example-qna.png)

上記の例では、ユーザー クエリのさまざまな種類に応じて以下のスコア範囲サンプルのようなスコアを予想できます｡


![ランカーのスコア範囲](../media/qnamaker-concepts-confidencescore/ranker-score-range.png)


次の表は、特定のスコアに関連付けられる標準的な信頼度を示します。

|スコアの値|スコアの意味|サンプル クエリ|
|--|--|--|
|90 - 100|ユーザー クエリと KB の質問がほぼ完全に一致|"My changes aren't updated in KB after publish"|
|70 以上|高信頼度 - 通常､ユーザーのクエリに対する完全な答えとなる良い回答|"I published my KB but it's not updated"|
|50 - 70|中程度の信頼度 - 通常､ユーザー クエリの主な意図に答えるかなり良い回答|"Should I save my updates before I publish my KB?"|
|30 - 50|低信頼度 - 通常、ユーザーの意図の一部に答える回答。|" What does the save and train do?"|
|30以下|非常に低い信頼度 - 通常ユーザーのクエリに答えていないが､一部､一致する単語や語句がある回答 |" Where can I add synonyms to my KB"|
|0|一致なし｡回答は返されません。|"How much does the service cost"|

## <a name="choose-a-score-threshold"></a>スコアのしきい値を選択します。
上記の表は、たいていの KB で予想されるスコアをしています｡ ただし、各 KB はそれぞれに異なり、さまざまな種類の単語や意図､目的があるため､自分に最も合うしきい値をテストし､選ぶことをお勧めします｡ 既定では、しきい値は 0 に設定されているため、考えられるすべての回答が返されます。 たいていの KB に合う､推奨されるしきい値は **50** です。

しきい値を選択するさいは､Accuracy (精度) と Coverage (カバレッジ) のバランスを考慮し､自分の要件に基づいて、しきい値を調整してください。

- **Accuracy**(精度または正確さ) が重要な場合は､しきい値を大きくします。 そうすることで､回答が返されるたびに､信頼度の高いケースがずっと多くなり､ユーザーが求めている回答になる可能性がずっと高くなります｡探している可能性が高くなります。 このケースでは、最終的に回答を得られない質問が多くなる可能性があります｡ *例:* しきい値を **70** にした場合、｢what is save and train?｣などの曖昧な例が見逃される可能性があります｡

- **Coverage**(カバレッジまたはリコール)が重要で､ユーザーの質問に対して部分的な関係しかなくても､できるだけ多くの質問に答えるようにしたい場合は､しきい値を小さくします｡ このことは､ユーザーの実際のクエリに対する答えにはなっていないものの､他の多少関係のある回答が得られるケースが増える可能性があることを意味します｡ *例:* しきい値を **30** にすると、"どこで KB を編集できますか?" などのクエリに回答する場合があります。

> [!NOTE]
> QnA Maker の新しいバージョンにはスコアリング ロジックの機能強化が含まれ、しきい値に影響を与える可能性があります。 サービスを更新するときは常に、テストを行い、必要に応じてしきい値を調整してください。 お使いの QnA サービスのバージョンは、[こちら](https://www.qnamaker.ai/UserSettings)で確認できます。最新の更新プログラムの入手方法については、[こちら](../How-To/troubleshooting-runtime.md)をご覧ください。

## <a name="set-threshold"></a>しきい値の設定 

しきい値のスコアは、[GenerateAnswer API JSON 本文](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration)のプロパティとして設定します。 つまり、GenerateAnswer を呼び出すたびに設定します。 

ボット フレームワークからは、[C#](../how-to/metadata-generateanswer-usage.md?#use-qna-maker-with-a-bot-in-c) または [Node.js](../how-to/metadata-generateanswer-usage.md?#use-qna-maker-with-a-bot-in-nodejs) で options オブジェクトの一部としてスコアを設定します。

## <a name="improve-confidence-scores"></a>信頼度スコアの向上
ユーザー クエリに対する特定の応答の信頼度スコアを上げるには、その応答に対する代替質問として、ユーザー クエリをナレッジ ベースに追加できます。 大文字と小文字が区別される[単語変更](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/alterations/replace)を使用して、KB のキーワードにシノニムを追加することもできます。


## <a name="similar-confidence-scores"></a>類似する信頼度スコア
複数の応答の信頼度スコアが似ている場合は、クエリが一般的すぎるために、複数の回答で信頼度が等しくなっている可能性があります。 QnA の構造を改善して、すべての QnA エンティティが明確な意図を持つようにすることを試してください。


## <a name="confidence-score-differences"></a>信頼度スコアの違い
コンテンツが同じ場合でも、ナレッジ ベースのテスト バージョンと公開バージョンの間で、回答の信頼度スコアが、無視できるほどですが、変化する場合があります。 これは、ナレッジ ベースのテスト バージョンと公開バージョンのコンテンツが、異なる Azure Search インデックスに配置されるためです。 ナレッジ ベースを公開すると、ナレッジ ベースの質問と回答コンテンツが、Azure Search のテスト インデックスから実稼働インデックスへ移動されます。 [公開](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base)のしくみを確認してください。

異なるリージョンにナレッジ ベースがある場合は、各リージョンが独自の Azure Search インデックスを使用しています。 異なるインデックスが使用されるため、スコアはまったく同じにはなりません。 


## <a name="no-match-found"></a>一致が見つからない
ランク付けによって適切な一致が見つからない場合は、0.0 または "None" の信頼度スコアが返され、既定の応答は [No good match found in the KB]\(KB で適切な一致が見つかりませんでした\) になります。 エンドポイントを呼び出すボットまたはアプリケーションのコードで、この[既定の応答](#change-default-answer)を上書きできます。 別の方法として、Azure に上書き応答を設定することもできます。この場合は、特定の QnA Maker サービスにデプロイされたすべてのナレッジ ベースの既定値が変更されます。

## <a name="change-default-answer"></a>既定の回答の変更

1. [Azure Portal](https://portal.azure.com) に移動し、作成した QnA Maker サービスを表すリソース グループに移動します。

2. **[App Service]** をクリックして開きます。

    ![Azure portal で QnA Maker の App Service にアクセスする](../media/qnamaker-concepts-confidencescore/set-default-response.png)

3. **[アプリケーション設定]** をクリックし、**DefaultAnswer** フィールドを目的の既定の応答に編集します。 **[Save]** をクリックします。

    ![[アプリケーション設定] を選択し、QnA Maker の DefaultAnswer を編集します。](../media/qnamaker-concepts-confidencescore/change-response.png)

4. App Service を再起動します。

    ![DefaultAnswer の変更後、QnA Maker の App Service を再起動する](../media/qnamaker-faq/qnamaker-appservice-restart.png)


## <a name="next-steps"></a>次の手順
> [!div class="nextstepaction"]
> [サポートされるデータ ソース](./data-sources-supported.md)

