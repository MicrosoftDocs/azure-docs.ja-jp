---
title: アプリのトレーニング
titleSuffix: Language Understanding - Azure Cognitive Services
description: トレーニングは、自然言語の理解を向上させるために、Language Understanding (LUIS) アプリ バージョンを教育するプロセスです。 エンティティ、意図、発話の追加、編集、ラベル付け、削除など、モデルを更新した後に、LUIS アプリをトレーニングします。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 04/07/2019
ms.author: diberry
ms.openlocfilehash: ba0db22437961a33b0b415ec7cb60ad3df12821c
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2019
ms.locfileid: "59267057"
---
# <a name="train-your-active-version-of-the-luis-app"></a>LUIS アプリのアクティブなバージョンをトレーニングする 

トレーニングは、自然言語の理解を向上させるために、Language Understanding (LUIS) アプリを教えるプロセスです。 エンティティ、意図、発話の追加、編集、ラベル付け、削除など、モデルを更新した後に、LUIS アプリをトレーニングします。 

<!--
When you train a LUIS app by example, LUIS generalizes from the examples you have labeled, and it learns to recognize the relevant intents and entities. This teaches LUIS to improve classification accuracy in the future. -->

アプリのトレーニングと[テスト](luis-concept-test.md)は反復処理です。 LUIS アプリをトレーニングした後で、サンプルの発話を使用して、意図とエンティティが正しく認識されるかどうかをテストします。 正しく認識されない場合は、もう一度 LUIS アプリを更新し、トレーニングおよびテストを行います。 

トレーニングは、LUIS ポータルでのアクティブなバージョンに適用されます。 

## <a name="how-to-train-interactively"></a>対話的にトレーニングする方法

[LUIS ポータル](https://www.luis.ai)で反復的なプロセスを開始するには、まず、LUIS アプリを少なくとも 1 回トレーニングする必要があります。 すべての意図に少なくとも 1 つの発話があることをトレーニングの前に確認してください。

1. **My Apps** ページでアプリの名前を選択してアプリにアクセスします。 

2. アプリで、上部パネルの **[トレーニング]** を選択します。 

3. トレーニングが完了したら、ブラウザーの上部に緑のバーが表示されます。

<!-- The following note refers to what might cause the error message "Training failed: FewLabels for model: <ModelName>" -->

>[!NOTE]
>アプリの 1 つまたは複数の意図に、発話の例が含まれていない場合は、アプリのトレーニングを行うことはできません。 すべての意図に発話を追加します。 詳細については、「[Add example utterances](luis-how-to-add-example-utterances.md)」(発話の追加) を参照してください。

## <a name="training-date-and-time"></a>日付と時刻をトレーニングする

トレーニングの日付と時刻は GMT + 2 です。 

## <a name="train-with-all-data"></a>すべてのデータでトレーニングする

トレーニングでは、ネガティブ サンプリングのごく一部を使用します。 少数のネガティブ サンプリングではなく、すべてのデータを使用したい場合は、`UseAllTrainingData` を True に設定した状態で[バージョン設定 API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) を使用し、この機能をオフにします。 

## <a name="unnecessary-training"></a>不要なトレーニング

ひとつ変更するたびにトレーニングする必要はありません。 トレーニングは変更のグループをモデルに適用した後で行う必要があり、次に行うステップはテストまたは発行です。 テストまたは発行を行う必要がない場合、トレーニングする必要はありません。 

## <a name="training-with-the-rest-apis"></a>REST API によるトレーニング

LUIS ポータルでのトレーニングは、**[トレーニング]** ボタンをクリックする 1 ステップだけです。 REST API によるトレーニングは、2 ステップのプロセスです。 最初に、HTTP POST で[トレーニングを要求](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45)します。 次に、HTTP Get で[トレーニングの状態](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46)を要求します。 

トレーニングが完了したことを知るには、すべてのモデルが正常にトレーニングされるまで状態をポーリングする必要があります。 

## <a name="next-steps"></a>次の手順

* [LUIS で推奨される発話にラベルを付ける](luis-how-to-review-endpoint-utterances.md) 
* [LUIS アプリのパフォーマンスを向上させるフィーチャーを使用する](luis-how-to-add-features.md) 
