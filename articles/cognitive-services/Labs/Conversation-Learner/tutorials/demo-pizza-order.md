---
title: 'Conversation Learner のデモ モデル: ピザの注文 - Microsoft Cognitive Services | Microsoft Docs'
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
ms.openlocfilehash: 3fe11bef6c505771ee1e3f2e12e647eafc7c45d1
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2019
ms.locfileid: "55212445"
---
# <a name="demo-pizza-order"></a>デモ:ピザの注文
このデモでは、次のことを行って 1 回のピザの注文をサポートするピザ注文ボットを紹介します。

- ユーザーの発話からピザのトッピングを認識します
- トッピングのインベントリを管理して、適切に応答します
- 以前の注文を記憶して、同じピザの再注文の時間を短縮します

## <a name="video"></a>ビデオ

[![ピザのデモのプレビュー](https://aka.ms/cl_Tutorial_v3_DemoPizzaOrder_Preview)](https://aka.ms/cl_Tutorial_v3_DemoPizzaOrder)

## <a name="requirements"></a>必要条件
このチュートリアルでは、ピザ注文ボットが実行されている必要があります。

    npm run demo-pizza

### <a name="open-the-demo"></a>デモを開く

Web UI の [モデル] 一覧で、[TutorialDemo Pizza Order] (TutorialDemo ピザの注文) をクリックします。 

## <a name="entities"></a>エンティティ

モデルには、3 つのエンティティが含まれています。

- "Toppings" では、ユーザー指定のトッピングがある場合に蓄積されます。
- "OutofStock" では、選択したトッピングの在庫がないことがユーザーに通知されます
- "LastToppings" には、以前の注文でのトッピングの履歴が含まれます

![](../media/tutorial_pizza_entities.PNG)

### <a name="actions"></a>Actions

モデルには、ユーザーにトッピングの選択を尋ねたり、トッピングを累積したりする、一連のアクションが含まれます。

2 つの API 呼び出しも用意されています。

- "FinalizeOrder" では、注文に対応する処理が行われます
- "UseLastToppings" では、過去のトッピング情報が処理されます

![](../media/tutorial_pizza_actions.PNG)

### <a name="training-dialogs"></a>トレーニング会話

モデルには複数のトレーニング会話が含まれます。

![](../media/tutorial_pizza_dialogs.PNG)

別のトレーニング会話を作成して、もう少しモデルをトレーニングしてみましょう。

1. 左側のパネルで、[Train Dialogs]\(トレーニング会話\) をクリックしてから、[New Train Dialog]\(新しいトレーニング会話\) ボタンをクリックします。
2. [Type your message...]\(メッセージを入力...\) と表示されているチャット パネルに、「Order a pizza with cheese」(チーズのピザを注文する) と入力します
    - "cheese" (チーズ) という単語が、エンティティ抽出機能によって抽出されました。
3. [Score Actions]\(アクションのスコア付け\) ボタンをクリックします。
4. "You have cheese on your pizza" (ピザにチーズをトッピングします) という応答を選択します。
5. "Would you like anything else?" (他にありますか?) という応答を選択します。
6. [Type your message...]\(メッセージを入力...\) と表示されているチャット パネルに、「add mushrooms and peppers」(マッシュルームとペッパーを追加) と入力します
7. [Score Actions]\(アクションのスコア付け\) ボタンをクリックします。
8. "You have cheese, mushrooms and peppers on your pizza" (ピザにチーズ、マッシュルーム、ペッパーをトッピングします) という応答を選択します。
9. "Would you like anything else?" (他にありますか?) という応答を選択します。
10. [Type your message...]\(メッセージを入力...\) と表示されているチャット パネルに、「remove peppers and add sausage」(ペッパーをやめてソーセージを追加) と入力します
11. [Score Actions]\(アクションのスコア付け\) ボタンをクリックします。
12. "You have cheese, mushrooms and sausage on your pizza" (ピザにチーズ、マッシュルーム、ソーセージをトッピングします) という応答を選択します。
13. "Would you like anything else?" (他にありますか?) という応答を選択します。
14. [Type your message...]\(メッセージを入力...\) と表示されているチャット パネルで、「add yam」(サツマイモを追加) と入力します
15. [Score Actions]\(アクションのスコア付け\) ボタンをクリックします。
    - "yam" (サツマイモ) という値は、サポートされているどの具材とも一致しなかったため、エンティティ検出コールバック コードによって "OutofStock" に追加されました。
16. "OutOfStock" (在庫なし) という応答を選択します
17. "Would you like anything else?" (他にありますか?) という応答を選択します。
18. [Type your message...]\(メッセージを入力...\) と表示されているチャット パネルに、「no」(いいえ) と入力します
    - "no" は、どの種類の意図としてもマークされません。 代わりに、現在のコンテキストに基づいて関連するアクションを選択します。
19. [Score Actions]\(アクションのスコア付け\) ボタンをクリックします。
20. "FinalizeOrder" (注文確定) という応答を選択します
    - このアクションを選択すると、客の現在のトッピングが "LastToppings" エンティティに保存され、FinalizeOrder コールバック コードによって "Toppings" エンティティが削除されます。
21. [Type your message...]\(メッセージを入力...\) と表示されているチャット パネルに、「order another」(別の注文) と入力します
22. [Score Actions]\(アクションのスコア付け\) ボタンをクリックします。
23. "Would you like cheese, mushrooms and sausage?" (チーズ、マッシュルーム、ソーセージをトッピングしますか?) という応答を選択します。
    - このアクションは、"LastToppings" エンティティが設定されているため利用可能になります。
24. [Type your message...]\(メッセージを入力...\) と表示されているチャット パネルに、「yes」(はい) と入力します
25. [Score Actions]\(アクションのスコア付け\) ボタンをクリックします。
26. "UseLastToppings" (最後のトッピングを使用) という応答を選択します
27. "You have cheese, mushrooms and sausage on your pizza" (ピザにチーズ、マッシュルーム、ソーセージをトッピングします) という応答を選択します。
28. "Would you like anything else?" (他にありますか?) という応答を選択します。

![](../media/tutorial_pizza_callbackcode.PNG)

![](../media/tutorial_pizza_apicalls.PNG)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Conversation Learner ボットのデプロイ](../deploy-to-bf.md)
