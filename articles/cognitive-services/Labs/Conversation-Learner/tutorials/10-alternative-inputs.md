---
title: Conversation Learner で代替入力を使用する方法 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner で代替入力を使用する方法について説明します。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 10335f9c74b9033b303c960a77af136cc80d75bb
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58094366"
---
# <a name="how-to-use-alternative-inputs"></a>代替入力を使用する方法

このチュートリアルでは、学習インターフェイスでユーザーの発話に対する代替入力フィールドを使用する方法について説明します。

## <a name="video"></a>ビデオ

[![代替入力のチュートリアルのプレビュー](https://aka.ms/cl_Tutorial_v3_AlternativeInputs_Preview)](https://aka.ms/cl_Tutorial_v3_AlternativeInputs)

## <a name="requirements"></a>必要条件
このチュートリアルでは、general tutorial ボットが実行されている必要があります。

    npm run tutorial-general

## <a name="details"></a>詳細
代替入力とは、トレーニング会話の特定の時点でユーザーが話した可能性のある、意味論的には同じで代わりに使用できる発話のことです。 このような代替入力を使用すると、発話のバリエーションを簡潔に指定することができま、各バリエーションを個別のトレーニング会話で処理する必要はありません。

## <a name="steps"></a>手順

### <a name="create-the-model"></a>モデルを作成する

1. Web UI で、[New Model]\(新しいモデル\) をクリックします。
2. [Name]\(名前\) フィールドに「AlternativeInputs」と入力して、Enter キーを押します。
3. [Create]\(作成\) ボタンをクリックします。

### <a name="entity-creation"></a>エンティティの作成

1. 左側のパネルで、[Entities]\(エンティティ\)、[New Entity]\(新しいエンティティ\) ボタンの順にクリックします。
2. [Entity Type]\(エンティティの種類\) で [Custom Trained]\(カスタム トレーニング済み\) を選択します。
3. [Entity Name]\(エンティティ名\) に「city」と入力します。
4. [Create]\(作成\) ボタンをクリックします。

![](../media/T10_actions.png)

次に、3 つのアクションを作成しましょう。

### <a name="create-the-first-action"></a>1 つ目のアクションを作成する

1. 左側のパネルで [Actions]\(アクション\) をクリックし、[New Action]\(新しいアクション\) ボタンをクリックします。
2. [Bot's response...]\(ボットの応答...\) フィールドに、「Which city?」(どの市ですか?) と入力します
3. [Expected Entity in User reply...]\(ユーザーの応答で予期されるエンティティ...\) フィールドに「city」と入力します。
4. [Disqualifying Entitles]\(不適格エンティティ\) フィールドに「city」と入力します。
5. [Create]\(作成\) ボタンをクリックします。

![](../media/T10_action_create_1.png)

### <a name="create-the-second-action"></a>2 つ目のアクションを作成する

1. 左側のパネルで [Actions]\(アクション\) をクリックし、[New Action]\(新しいアクション\) ボタンをクリックします。
2. [Bot's response...]\(ボットの応答...\) フィールドに、「The weather in $city is probably sunny.」($city の天気はたぶん晴れです。) と入力します
3. [Create]\(作成\) ボタンをクリックします。

![](../media/T10_action_create_2.png)

### <a name="create-the-third-action"></a>3 つ目のアクションを作成する

1. 左側のパネルで [Actions]\(アクション\) をクリックし、[New Action]\(新しいアクション\) ボタンをクリックします。
2. [Bot's response...]\(ボットの応答...\) フィールドに、「Try asking for the weather.」(天気を尋ねてみてください。) と入力します
3. [Disqualifying Entitles]\(不適格エンティティ\) フィールドに「city」と入力します。
4. [Create]\(作成\) ボタンをクリックします。

![](../media/T10_action_create_3.png)

これでアクションが 3 つできました。

![](../media/T10_actions.png)

### <a name="train-the-model"></a>モデルをトレーニングする

1. 左側のパネルで、[Train Dialogs]\(トレーニング会話\) をクリックしてから、[New Train Dialog]\(新しいトレーニング会話\) ボタンをクリックします。
2. [Type your message...]\(メッセージを入力...\) と表示されているチャット パネルに、「What's the weather?」(どんな天気ですか?) と入力します
3. [Score Actions]\(アクションのスコア付け\) ボタンをクリックします。
4. 応答 "Which city?" を選択します
5. [Type your message...]\(メッセージを入力...\) と表示されているチャット パネルで、「Denver」(デンバー) と入力します
6. [Score Actions]\(アクションのスコア付け\) ボタンをクリックします。
7. 応答 "The weather in Denver is probably sunny.」(デンバーの天気はたぶん晴れです。) を選択します
8. [Submit Changes]\(変更の送信\) ボタンをクリックします。

![](../media/T10_training_1.png)

別のトレーニング会話を作成して、もっとモデルをトレーニングしてみましょう。

### <a name="second-model-train-dialog"></a>2 つ目のモデル トレーニング会話

1. 左側のパネルで、[Train Dialogs]\(トレーニング会話\) をクリックしてから、[New Train Dialog]\(新しいトレーニング会話\) ボタンをクリックします。
2. [Type your message...]\(メッセージを入力...\) と表示されているチャット パネルに、「What can you do?」(あなたは何ができますか?) と入力します
3. [Score Actions]\(アクションのスコア付け\) ボタンをクリックします。
4. 応答 "Try asking for the weather." (天気を尋ねてみてください。) を選択します
5. [Type your message...]\(メッセージを入力...\) と表示されているチャット パネルに、「What's the weather in Seattle?」(シアトルはどんな天気ですか?) と入力します
6. "Seattle" をクリックし、エンティティの一覧から "city" をクリックします。
7. [Score Actions]\(アクションのスコア付け\) ボタンをクリックします。
8. 応答 "The weather in Seattle is probably sunny.」(シアトルの天気はたぶん晴れです。) を選択します
9. [Submit Changes]\(変更の送信\) ボタンをクリックします。

![](../media/T10_training_2.png)

### <a name="third-model-train-dialog-using-alternative-input"></a>代替入力を使用する 3 つ目のモデル トレーニング会話

1. 左側のパネルで、[Train Dialogs]\(トレーニング会話\) をクリックしてから、[New Train Dialog]\(新しいトレーニング会話\) ボタンをクリックします。
2. [Type your message...]\(メッセージを入力...\) と表示されているチャット パネルで、「help」(ヘルプ) と入力します。
3. [Score Actions]\(アクションのスコア付け\) ボタンをクリックします。
    - モデルは最善のオプションかはっきりしないので、既定で最も高いパーセンタイルが選択されます。
4. [Abandon Teaching]\(学習の破棄\) ボタンをクリックして、[Confirm]\(確定\) ボタンをクリックします。

![](../media/T10_training_3.png)

代替入力を使用して、システムをよりよく調整しましょう。 学習させながら、または後で、代替入力を追加できます。

1. 左側のパネルで、[Train Dialogs]\(トレーニング会話\) をクリックしてから、"What can you do?" (あなたは何ができますか?) を [Train Dialogs]\(トレーニング会話\) の一覧から選択します。
1. "What can you do?" (あなたは何ができますか?) という発話をチャット パネルでクリックします。
1. [Add alternative input...]\(代替入力の追加...\) フィールドに「help」(ヘルプ) と入力して Enter キーを押します。
1. [Save Changes]\(変更の保存\) ボタンをクリックします。

![](../media/T10_training_4.png)

Houston を処理するための別の代替入力を追加してみましょう。

1. "What's the weather in Seattle?" (シアトルはどんな天気ですか?) という発話をチャット パネルでクリックします。
1. [Add alternative input...]\(代替入力の追加...\) フィールドに「forecast for Houston」(ヒューストンの天気予報) と入力して Enter キーを押します。
   - エラー メッセージが表示され、エンティティの値が同じというだけでなく、代替入力は意味論的に同等で、元の発話と同じエンティティを含む必要があることが強調されます。 同じエンティティが存在することが必要です。
1. "Houston" をクリックし、エンティティの一覧から "city" を選択します。
1. [Add alternative input...]\(代替入力の追加...\) フィールドに「forecast for Seattle」(シアトルの天気予報) と入力して Enter キーを押します。
1. "Seattle" をクリックし、エンティティの一覧から "city" を選択します。
1. [Save Changes]\(変更の保存\) ボタンをクリックします。
1. [Save Edit]\(編集の保存\) ボタンをクリックします。

![](../media/T10_training_5.png)

### <a name="testing-the-model"></a>モデルをテストする

1. 左側のパネルで、[Log Dialogs]\(会話記録\) をクリックしてから、[New Log Dialog]\(新しい会話記録\) をクリックします。
2. [Type your message...]\(メッセージを入力...\) と表示されているチャット パネルに、「help me」(助けてください) と入力します。
3. [Type your message...]\(メッセージを入力...\) と表示されているチャット パネルに、「forecast for Denver」(デンバーの天気予報) と入力します。

![](../media/T10_logdialog.png)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [会話記録](./11-log-dialogs.md)
