---
title: 'Conversation Learner のデモ モデル: パスワードのリセット - Microsoft Cognitive Services | Microsoft Docs'
titleSuffix: Azure
description: Conversation Learner のデモ モデルを作成する方法について説明します。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 6436193dadb5933074fdce861a31672a0f4849de
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2019
ms.locfileid: "55227337"
---
# <a name="demo-password-reset"></a>デモ:パスワード リセット
このチュートリアルでは、Conversation Learner を利用したパスワードのリセットに役立つ簡単なテクニカル サポート ボットを紹介します。 ボットのモデルでは、ドメイン外クラスを含め、簡単ではない会話の流れとマルチ ターン シーケンスを学習することができます。 コードまたはエンティティを使用しないでタスクを実行できます。

## <a name="video"></a>ビデオ

[![パスワードのデモのプレビュー](https://aka.ms/cl_Tutorial_v3_DemoPassword_Preview)](https://aka.ms/cl_Tutorial_v3_DemoPassword)

## <a name="requirements"></a>必要条件
このチュートリアルでは、パスワードのリセット ボットが実行されている必要があります。

    npm run demo-password

### <a name="open-the-demo"></a>デモを開く

Web UI の [モデル] 一覧で、[Tutorial Demo Password Reset] (チュートリアル デモ: パスワードのリセット) をクリックします。 

### <a name="actions"></a>Actions

モデルには、パスワードの一般的な問題の解決を手助けするアクションのセットが含まれています。

![](../media/tutorial_pw_reset_actions.PNG)

### <a name="training-dialogs"></a>トレーニング会話

モデルには、ドメイン外クラスのトレーニングのデモなど、複数のトレーニング会話も含まれています。 たとえば、道案内情報を要求するユーザーなどです。 サンプル ボットはデモのために少しトレーニングされており、"can't help with that" (それについてはお役に立てません) とだけ応答します。 既存のトレーニング会話のリストは、左側のパネルの [Train Dialogs]\(トレーニング会話\) の下にあります。

![](../media/tutorial_pw_reset_entities.PNG)

1. 左側のパネルで、[Train Dialogs]\(トレーニング会話\) をクリックしてから、[New Train Dialog]\(新しいトレーニング会話\) ボタンをクリックします。
2. [Type your message...]\(メッセージを入力...\) と表示されているチャット パネルに、「i lost my password」(パスワードをなくしました) と入力します。
3. [Score Actions]\(アクションのスコア付け\) ボタンをクリックします。
4. [Is that for your local account or Microsoft account?]\(それはローカル アカウントですか、Microsoft アカウントですか?\) という応答を選択します。
5. [Type your message...]\(メッセージを入力...\) と表示されているチャット パネルに、「local account please」(ローカル アカウントです) と入力します
6. [Score Actions]\(アクションのスコア付け\) ボタンをクリックします。
7. 応答 [Which version of Windows do you have?]\(Windows のバージョンは何ですか?\) を選択します。
8. [Type your message...]\(メッセージを入力...\) と表示されているチャット パネルに、「windows xp」と入力します
9. [Score Actions]\(アクションのスコア付け\) ボタンをクリックします。
10. [+Action]\(+ アクション\) ボタンをクリックします。
11. [Bot's response...]\(ボットの応答...\) フィールドに、「SOLUTION:How to reset password on Windows XP」(解決策: Windows XP でパスワードをリセットする方法) と入力します。
12. [Create]\(作成\) ボタンをクリックします。

### <a name="training-dialogs-for-out-of-domain-scenarios"></a>ドメイン外のシナリオのトレーニング会話

1. 左側のパネルで、[Train Dialogs]\(トレーニング会話\) をクリックしてから、既存の "toy stores" (おもちゃ屋) トレーニング会話をクリックします。
2. チャット パネルで、"toy stores" (おもちゃ屋) 発話をクリックします。
3. [Add alternative input...]\(代替入力の追加...\) フィールドに「web search」(Web 検索) と入力して Enter キーを押します。
4. [Add alternative input...]\(代替入力の追加...\) フィールドに「flight booking」(飛行機の予約) と入力して Enter キーを押します。
5. [Save Changes]\(変更の保存\) ボタンをクリックします。
6. [Save Edit]\(編集の保存\) ボタンをクリックします。
7. 左側のパネルで、[Log Dialogs]\(会話記録\) をクリックしてから、[New Log Dialog]\(新しい会話記録\) ボタンをクリックします。
8. [Type your message...]\(メッセージを入力...\) と表示されているチャット パネルに、「i can't find my password」(パスワードが見つかりません) と入力します
9. [Type your message...]\(メッセージを入力...\) と表示されているチャット パネルに、「Microsoft account」(Microsoft アカウント) と入力します
10. [Type your message...]\(メッセージを入力...\) と表示されているチャット パネルで、「thanks」(ありがとう) と入力します
11. [Done Testing]\(テストの完了\) ボタンをクリックします。
12. グリッド ビューで "i can't find my password" (パスワードが見つかりません) という会話記録をクリックします。
13. チャット パネルで、誤って表示された "Solution:How to reset a Microsoft Account Password" (解決策: Microsoft アカウントのパスワードをリセットする方法) という応答をクリックします。
14. [+Action]\(+ アクション\) ボタンをクリックします。
15. [Bot's response...]\(ボットの応答...\) フィールドに、「You are welcome」(どう致しまして) と入力します
16. [Create]\(作成\) ボタンをクリックします。
17. [Save As Train Dialog]\(トレーニング会話として保存\) ボタンをクリックします。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [デモ - ピザの注文](./demo-pizza-order.md)
