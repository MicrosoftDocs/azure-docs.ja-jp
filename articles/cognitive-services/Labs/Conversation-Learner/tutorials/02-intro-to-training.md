---
title: Conversation Learner モデルのトレーニングの概要 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner による以前のトレーニングの分岐や編集など、モデルをトレーニングする方法について説明します。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 1e6dea521207ec00efe948a879c705bd28a75d57
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2019
ms.locfileid: "55213195"
---
# <a name="introduction-to-training"></a>トレーニングの概要

このチュートリアルでは、モデルのトレーニングの基本、以前のトレーニングに基づく新しいトレーニングの分岐、ボットの応答を変更するための編集について説明します。

## <a name="video"></a>ビデオ

[![トレーニングの概要のチュートリアルのプレビュー](https://aka.ms/cl_Tutorial_v3_IntroTraining_Preview)](https://aka.ms/cl_Tutorial_v3_IntroTraining)

## <a name="requirements"></a>必要条件
このチュートリアルでは、general tutorial ボットが実行されている必要があります。

    npm run tutorial-general

## <a name="details"></a>詳細

- アクション:ユーザー入力に対するボットの応答。
- トレーニング:ユーザー入力に応答するようにボットを教える方法。
- 分岐:始まりは元のトレーニング会話と同じだが、途中で異なる方向になる新しいトレーニング会話を作成するための、保存されたトレーニング会話内でのユーザー入力の変更。

## <a name="steps"></a>手順

### <a name="create-a-new-model"></a>新しいモデルを作成する

1. Web UI で、[新しいモデル] をクリックします。
2. [Name]\(名前\) に「Inspire Bot」と入力します。 続けて [作成] をクリックします。

### <a name="create-an-action"></a>アクションを作成する

1. 左側のパネルで [Actions]\(アクション\) をクリックし、[New Action]\(新しいアクション\) ボタンをクリックします。
2. [Bot's response]\(ボットの応答\) フィールドに、「Hi! Would you like to be inspired today?」(こんにちは、今日はインスピレーションを得たいですか?) と入力します。
    - 他のすべてのフィールドとチェック ボックスは既定の設定のままにします。
3. [作成] をクリックします。

### <a name="first-training-and-creating-another-action-while-training"></a>最初のトレーニングと、トレーニング中の別のアクションの作成

1. 左側のパネルで、[Train Dialogs]\(トレーニング会話\) をクリックしてから、[New Train Dialog]\(新しいトレーニング会話\) ボタンをクリックします。
2. [Type your message...]\(メッセージを入力...\) と表示されているチャット パネルで、「hello」(こんにちは) と入力します。 
    - これは、ユーザー側の会話をシミュレートします。
3. [Score Actions]\(アクションのスコア付け\) をクリックします。
4. 応答 "Hi! Would you like to be inspired today?」(こんにちは、今日はインスピレーションを得たいですか?) と入力します。
5. ユーザーとして、「yes」と応答します。
6. [Score Actions]\(アクションのスコア付け\) をクリックします。
7. [+ Action]\(+ アクション\) ボタンをクリックします。 
    - これで、お馴染みの [Create an Action]\(アクションの作成\) ダイアログ ボックスに移動します。
8. ボットの応答に、「You're awesome!」(あなたはすばらしい) と入力します。
9. [作成] をクリックします。
10. ボットがすぐに応答することに注意してください。
11. [Save]\(保存\) ボタンをクリックします。

### <a name="branch-a-second-training-off-of-the-first-training"></a>最初のトレーニングから 2 つ目のトレーニングを分岐する
1. 最初のトレーニングがまとめられているグリッドの行をクリックします。 
    - これにより、既存のトレーニングを表示したり編集したりできます。
2. "Yes" というユーザー応答をクリックします。 
    - これにより、編集コントロールが表示されます。
3. 分岐アイコンをクリックします。 
    - 新しい会話用の別のユーザー入力を求めるメッセージが表示されます。
4. 「no」と入力して、Enter キーを押すか、[Create]\(作成\) ボタンをクリックします。 
    - この時点で、トレーニング会話の新しいインスタンスが作成されます。元の会話は変わらずそのままです。
5. [Score Actions]\(アクションのスコア付け\) をクリックします。
6. 直前に表示されたボットの正しくない応答をクリックします。
7. [+ Action]\(+ アクション\) ボタンをクリックします 
    - ボットが応答するための新しいアクションを作成することができます。
8. ボットの応答として、「No problem! Have a great day!」(問題ありません。よい 1 日を。) と入力します。
9. Create をクリックしてください。
10. ボットがすぐに応答することに注意してください。
11. [Save]\(保存\) ボタンをクリックします。

### <a name="test-the-trainings"></a>トレーニングをテストする
1. 左側のパネルで、[Log Dialogs]\(会話記録\) をクリックしてから、[New Log Dialog]\(新しい会話記録\) をクリックします。
2. 「hi」(こんにちは) とメッセージを入力します。 
3. ボットがトレーニングした方法で自動的に応答することに注意してください。
4. 「Yes」(はい) とユーザー応答を入力します。
5. ボットの応答に注意してください、最初のトレーニングが動作していることを示します。
6. [Session Timeout]\(セッション タイムアウト\) ボタンをクリックします。 これは、Conversation Learner に、行われたばかりの会話ターンを無視して、もう一度始めるように伝えます。
7. 「hi」(こんにちは) とメッセージを入力します。 
8. ボットがトレーニングした方法で自動的に応答することに注意してください。
9. 「no」(いいえ) とユーザー応答を入力します。
10. ボットの応答に注意してください、2 番目のトレーニングが動作していることを示します。
11. [Done Testing]\(テストの完了\) ボタンをクリックします。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [待機アクションと非待機アクション](./03-wait-vs-nonwait-actions.md)
