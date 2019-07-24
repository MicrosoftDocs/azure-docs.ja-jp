---
title: Conversation Learner モデルで待機アクションと非待機アクションを使用する方法 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner モデルで待機アクションと非待機アクションを使用する方法について説明します。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 2869e6c59388adf548e5f239d3a831a5a3f060dd
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2019
ms.locfileid: "55214264"
---
# <a name="wait-and-non-wait-actions"></a>待機アクションと非待機アクション

このチュートリアルでは、Conversation Learner における待機アクションと非待機アクションの違いについて説明します。

## <a name="video"></a>ビデオ

[![待機と非待機のチュートリアルのプレビュー](https://aka.ms/cl_Tutorial_v3_WaitnonWait_Preview)](https://aka.ms/cl_Tutorial_v3_WaitnonWait)

## <a name="requirements"></a>必要条件
このチュートリアルでは、general tutorial ボットが実行されている必要があります。

    npm run tutorial-general

## <a name="details"></a>詳細

- 待機アクション:"待機" アクションを実行したシステムは、その後アクションの実行を停止し、ユーザー入力を待機します。
- 非待機アクション:"非待機" アクションを実行したシステムは、その後すぐに (ユーザー入力を待たずに) 別のアクションを選択します。

## <a name="steps"></a>手順

### <a name="create-a-new-model"></a>新しいモデルを作成する

1. Web UI で、[新しいモデル] をクリックします。
2. [Name]\(名前\) フィールドに「Wait Non-Wait」(待機と非待機) と入力し、Enter キーを押すか、[Create]\(作成\) ボタンをクリックします。

### <a name="create-the-first-two-wait-actions"></a>最初の 2 つの待機アクションを作成する

1. 左側のパネルで [Actions]\(アクション\) をクリックし、[New Action]\(新しいアクション\) ボタンをクリックします。
2. [Bot's response...]\(ボットの応答...\) フィールドに、「What pizza would you like?」(どんなピザにしますか?) と入力します。
    - これは待機アクションなので、[Wait for Response]\(応答を待機する\) チェック ボックスはオンのままにします。
3. [Create]\(作成\) ボタンをクリックします。
4. 以上の手順を繰り返し、別のアクションを "Pizza on the way!" (ピザ配達中!) を ボットの応答として作成します。

### <a name="train-using-those-wait-actions"></a>これらの待機アクションを使用してトレーニングする

1. 左側のパネルで、[Train Dialogs]\(トレーニング会話\) をクリックしてから、[New Train Dialog]\(新しいトレーニング会話\) ボタンをクリックします。
2. [Type your message...]\(メッセージを入力...\) と表示されているチャット パネルに、「Hi」(こんにちは) と入力します。 
    - これは、ユーザー側の会話をシミュレートします。
3. [Score Actions]\(アクションのスコア付け\) ボタンをクリックします。
4. 応答 "What pizza would you like?" (どんなピザにしますか?) を選択します。
5. ユーザーとして、「Margherita」(マルゲリータ) と応答します。
6. [Score Actions]\(アクションのスコア付け\) ボタンをクリックします。
7. 応答 "Pizza on the way!" (ピザ配達中!) を選択します。
8. [Save]\(保存\) ボタンをクリックします。

### <a name="create-a-non-wait-action-while-training"></a>トレーニング中に非待機アクションを作成する
前に行ったようにして非待機アクションを作成できますが、トレーニング セッション内から作成することもできます。
1. [New Train Dialog]\(新しいトレーニング会話\) ボタンをクリックします。
2. ユーザーとして「Hello」(こんにちは) と入力します。
3. [Score Actions]\(アクションのスコア付け\) ボタンをクリックします。
4. [+ Action]\(+ アクション\) ボタンをクリックします。 
    - これで、お馴染みの [Create an Action]\(アクションの作成\) ダイアログ ボックスに移動します。
5. ボットの応答として、「Welcome to Pizza Bot!」(ピザ ボットへようこそ!) と入力します。
6. [Wait for Response]\(応答を待機する\) チェック ボックスをオフにします。
7. [Create]\(作成\) ボタンをクリックします。
    - ボットがすぐに "Welcome to Pizza Bot!" (ピザ ボットへようこそ!) と応答すること、 および再び別のボット応答の入力を求められることに注意してください。 これは、ボットの応答が先ほど作成した非待機アクションであったためです。
9. 応答 "What pizza would you like?" (どんなピザにしますか?) を選択します。
10. ユーザーとして、「Margherita」(マルゲリータ) と応答します。
11. [Score Actions]\(アクションのスコア付け\) ボタンをクリックします。
12. 応答 "Pizza on the way!" (ピザ配達中!) を選択します。
13. [Save]\(保存\) ボタンをクリックします。

> [!NOTE]
> 待機アクションと非待機アクションに関するボット応答のシーケンス。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [エンティティの概要](./04-introduction-to-entities.md)
