---
title: Conversation Learner モデルで複数値エンティティを使用する方法 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner モデルで複数値エンティティを使用する方法について説明します。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 07550ed9c78aa584e55403d225eef4bcff33b057
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58167424"
---
# <a name="how-to-use-multi-value-entities-with-a-conversation-learner-model"></a>Conversation Learner モデルで複数値エンティティを使用する方法
このチュートリアルでは、エンティティの複数値プロパティを示します。

## <a name="video"></a>ビデオ

[![複数値エンティティのチュートリアルのプレビュー](https://aka.ms/cl_Tutorial_v3_MultiValued_Preview)](https://aka.ms/cl_Tutorial_v3_MultiValued)

## <a name="requirements"></a>必要条件
このチュートリアルでは、汎用チュートリアル ボットが実行されている必要があります。

    npm run tutorial-general

## <a name="details"></a>詳細
複数値エンティティでは、1 つの値が格納されるのではなく、一覧に値が累積されます。  これらのエンティティは、ユーザーが複数の値を指定できる場合に便利です。 たとえば、ピザのトッピングなどです。

複数値としてマークされたエンティティでは、エンティティの認識された各インスタンスが、ボットのメモリ内のリストに追加されます。 それ以降に認識された値は、上書きではなく、エンティティの値に追加されます。

## <a name="steps"></a>手順

Web UI のホーム ページから始めます。

### <a name="create-the-model"></a>モデルを作成する

1. **[新しいモデル]** を選択します。
2. **[名前]** に、「**MultiValueEntities**」と入力します。
3. **作成**を選択します。

### <a name="entity-creation"></a>エンティティの作成

1. 左側のパネルで **[エンティティ]** を選択し、**[新しいエンティティ]** を選択します。
2. **[エンティティ型]** で **[Custom Trained]\(カスタム トレーニング済み\)** を選択します。
3. **[エンティティ名]** に、「**toppings**」(トッピング) と入力します。
4. エンティティの 1 つまたは複数の値の蓄積を有効にするには、**[複数値]** をオンにします。
5. **[Negatable]\(否定可能\)** チェック ボックスをオンにします。
6. **作成**を選択します。

![](../media/T07_entity_create.png)

### <a name="create-the-first-action"></a>1 つ目のアクションを作成する

1. 左側のパネルで **[アクション]** を選択し、**[新しいアクション]** を選択します。
2. **[Bot's response]\(ボットの応答\)** に「**Here are your toppings: $toppings**」(選択したトッピング: $toppings) と入力します先頭のドル記号は、エンティティ参照を示します。
3. **作成**を選択します。

![](../media/T07_action_create_1.png)

### <a name="create-the-second-action"></a>2 つ目のアクションを作成する

1. 左側のパネルで **[アクション]** を選択し、**[新しいアクション]** を選択します。
2. **[Bot's response]\(ボットの応答\)** に「**What toppings would you like?**」(何をトッピングしますか?) と入力します。
3. **[Disqualifying Entities]\(不適格エンティティ\)** に「**toppings**」(トッピング) と入力します。
4. **作成**を選択します。

これでアクションが 2 つできました。

![](../media/T07_action_create_2.png)

### <a name="train-the-model"></a>モデルをトレーニングする

1. 左側のパネルで、**[Train Dialogs]\(トレーニング会話\)** を選択し、**[New Train Dialog]\(新しいトレーニング会話\)** を選択します。
2. 左側のチャット パネルでユーザーの発話に「**hi**」(やあ) と入力します。
3. **[Score Action]\(アクションのスコア付け\)** を選択します。
4. [アクション] リストから **[What toppings would you like?]\(何をトッピングしますか?\)** を選択します。 制約に基づく唯一の有効なアクションであるため、パーセンタイルは 100% です。
5. 左側のチャット パネルでユーザーの発話に「**cheese and mushrooms**」(チーズとマッシュルーム) と入力します。
6. **[cheese]\(チーズ\)**、**[+toppings]\(+ トッピング\)** の順に選択します。
7. **[mushrooms]\(マッシュルーム\)**、**[+toppings]\(+ トッピング\)** の順に選択します。
8. **[Score Action]\(アクションのスコア付け\)** を選択します。
9. [アクション] リストから **[Here are your toppings: $toppings]\(選択したトッピング: $toppings\)** を選択します。
10. 左側のチャット パネルでユーザーの発話に「**add pepper**」(ペッパーを追加して) と入力します。
11. **[pepper]\(ペッパー\)**、**[+toppings]\(+ トッピング\)** の順に選択します。
12. **[Score Action]\(アクションのスコア付け\)** を選択します。
13. [アクション] リストから **[Here are your toppings: $toppings]\(選択したトッピング: $toppings\)** を選択します。
14. 左側のチャット パネルでユーザーの 3 つ目の発話に「**remove cheese**」(チーズを削除して) と入力します。
15. **[cheese]\(チーズ\)**、**[-toppings]\(- トッピング\)** の順に選択します。
16. **[Score Action]\(アクションのスコア付け\)** を選択します。
17. [アクション] リストから **[Here are your toppings: $toppings]\(選択したトッピング: $toppings\)** を選択します。

![](../media/T07_training.png)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [事前トレーニング済みエンティティ](./08-pre-trained-entities.md)
