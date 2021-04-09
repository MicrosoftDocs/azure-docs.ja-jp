---
title: アプリをトレーニングする - LUIS
titleSuffix: Azure Cognitive Services
description: トレーニングは、自然言語の理解を向上させるために、Language Understanding (LUIS) アプリ バージョンを教育するプロセスです。 エンティティ、意図、発話の追加、編集、ラベル付け、削除など、モデルを更新した後に、LUIS アプリをトレーニングします。
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 11/15/2019
ms.openlocfilehash: 47b006932aace3149dd94e136e334c1b6e5bfcef
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98762691"
---
# <a name="train-your-active-version-of-the-luis-app"></a>LUIS アプリのアクティブなバージョンをトレーニングする

トレーニングは、自然言語の理解を向上させるために、Language Understanding (LUIS) アプリを教えるプロセスです。 エンティティ、意図、発話の追加、編集、ラベル付け、削除など、モデルを更新した後に、LUIS アプリをトレーニングします。

アプリのトレーニングと[テスト](luis-concept-test.md)は反復処理です。 LUIS アプリをトレーニングした後で、サンプルの発話を使用して、意図とエンティティが正しく認識されるかどうかをテストします。 正しく認識されない場合は、もう一度 LUIS アプリを更新し、トレーニングおよびテストを行います。

トレーニングは、LUIS ポータルでのアクティブなバージョンに適用されます。

## <a name="how-to-train-interactively"></a>対話的にトレーニングする方法

[LUIS ポータル](https://www.luis.ai)で反復的なプロセスを開始するには、まず、LUIS アプリを少なくとも 1 回トレーニングする必要があります。 すべての意図に少なくとも 1 つの発話があることをトレーニングの前に確認してください。

1. **[My Apps]\(マイ アプリ\)** ページでご自身のアプリの名前を選択して、アプリにアクセスします。

1. アプリで、上部パネルの **[トレーニング]** を選択します。

1. トレーニングが完了したら、ブラウザーの上部に通知が表示されます。

## <a name="training-date-and-time"></a>日付と時刻をトレーニングする

トレーニングの日付と時刻は GMT + 2 です。

## <a name="train-with-all-data"></a>すべてのデータでトレーニングする

トレーニングでは、ネガティブ サンプリングのごく一部を使用します。 代わりにポータルまたは API を使用すれば、使用可能なすべてのデータを使用できます。 

### <a name="using-the-luis-portal"></a>LUIS ポータルを使用する

[LUIS ポータル](https://www.luis.ai/)にログインし、お使いのアプリをクリックします。 画面の上部にある **[管理]** を選択し、 **[設定]** を選択して、 **[決定論的トレーニングを使用する]** オプションを有効または無効にします。 無効にすると、利用可能なすべてのデータがトレーニングで使用されます。

![非決定論的トレーニングを有効または無効にするためのボタン](./media/non-determinstic-training.png)

### <a name="using-the-version-settings-api"></a>バージョン設定 API の使用

`UseAllTrainingData` を true に設定してこの機能を無効にし、[バージョン設定 API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) を使用します。

## <a name="unnecessary-training"></a>不要なトレーニング

ひとつ変更するたびにトレーニングする必要はありません。 トレーニングは変更のグループをモデルに適用した後で行う必要があり、次に行うステップはテストまたは発行です。 テストまたは発行を行う必要がない場合、トレーニングする必要はありません。

## <a name="training-with-the-rest-apis"></a>REST API によるトレーニング

LUIS ポータルでのトレーニングは、**[トレーニング]** ボタンをクリックする 1 ステップだけです。 REST API によるトレーニングは、2 ステップのプロセスです。 最初に、HTTP POST で[トレーニングを要求](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45)します。 次に、HTTP Get で[トレーニングの状態](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46)を要求します。

トレーニングが完了したことを知るには、すべてのモデルが正常にトレーニングされるまで状態をポーリングする必要があります。

## <a name="next-steps"></a>次の手順

* [対話型テスト](luis-interactive-test.md)
* [バッチ テスト](luis-how-to-batch-test.md)
