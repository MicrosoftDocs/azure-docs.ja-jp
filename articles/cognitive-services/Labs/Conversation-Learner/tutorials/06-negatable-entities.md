---
title: Conversation Learner モデルで否定可能エンティティを使用する方法 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner モデルで否定可能エンティティを使用する方法について説明します。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 78dc759632c4fc3116a59ea1e5bc0b93200bca45
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58168189"
---
# <a name="how-to-use-negatable-entities-with-a-conversation-learner-model"></a>Conversation Learner モデルで否定可能エンティティを使用する方法

このチュートリアルでは、エンティティの "否定可能" プロパティのデモンストレーションを行います。

## <a name="video"></a>ビデオ

[![否定可能エンティティのチュートリアルのプレビュー](https://aka.ms/cl_Tutorial_v3_NegatableEntities_Preview)](https://aka.ms/cl_Tutorial_v3_NegatableEntities)

## <a name="requirements"></a>必要条件
このチュートリアルでは、汎用チュートリアル ボットが実行されている必要があります。

    npm run tutorial-general

## <a name="details"></a>詳細
エンティティの "否定可能" プロパティを使用すると、エンティティの標準 (正) と負の両方のインスタンスにラベルを付け、正と負のモデルに基づいて学習させ、既存のエンティティの値をクリアできます。 Conversation Leaner では、"否定可能" プロパティ セットを持つエンティティは否定可能エンティティと呼ばれます。

## <a name="steps"></a>手順

Web UI のホーム ページから始めます。

### <a name="create-the-model"></a>モデルを作成する

1. **[新しいモデル]** を選択します。
2. **[名前]** に「**NegatableEntity**」と入力します。
3. **作成**を選択します。

### <a name="entity-creation"></a>エンティティの作成

1. 左側のパネルで **[エンティティ]** を選択し、**[新しいエンティティ]** を選択します。
2. **[エンティティ型]** で **[Custom Trained]\(カスタム トレーニング済み\)** を選択します。
3. **[エンティティ名]** に「**name**」と入力します。
4. **[Negatable]\(負の値を許可する\)** をオンにすると、ユーザーはエンティティの値を指定することや、何かがエンティティ値 "*ではない*" と言うことができるようになり、一致するエンティティ値を削除できるようになります。
5. **作成**を選択します。

![](../media/T06_entity_create.png)

### <a name="create-the-first-action"></a>1 つ目のアクションを作成する

1. 左側のパネルで **[アクション]** を選択し、**[新しいアクション]** を選択します。
2. 「**I don't know your name.**」(あなたの名前を知りません。) と **[Bot's Response]\(ボットの応答\)** に入力します。
3. **[Disqualifying Entities]\(不適格エンティティ\)** に「**name**」(名前) と入力します。
4. **作成**を選択します。

![](../media/T06_action_create_1.png)

### <a name="create-the-second-action"></a>2 つ目のアクションを作成する

1. 左側のパネルで **[アクション]** を選択し、**[新しいアクション]** を選択します。
2. 「**I know your name.It is $name.**」(あなたの名前を知っています。$name です。) と **[Bot's Response]\(ボットの応答\)** に入力します。
3. **作成**を選択します。

> [!NOTE]
> **name** エンティティは、応答の発話内の参照によって**必須エンティティ**として自動的に追加されました。

これでアクションが 2 つできました。

![](../media/T06_action_create_2.png)

### <a name="train-the-model"></a>モデルをトレーニングする

1. 左側のパネルで、**[Train Dialogs]\(トレーニング会話\)** を選択し、**[New Train Dialog]\(新しいトレーニング会話\)** を選択します。
2. 左側のチャット パネルでユーザーの発話に「**hello**」(こんにちは) と入力します。
3. **[Score Action]\(アクションのスコア付け\)** を選択します。
4. **[I don't know your name.]\(あなたの名前を知りません。** \) を [アクション] リストから選択します。 制約に基づく唯一の有効なアクションであるため、パーセンタイルは 100% です。
5. 左側のチャット パネルでユーザーの発話に「**My name is Frank**」(私の名前は Frank です) と入力します。
6. **[Frank]**、**[+name]\(+ 名前\)** の順に選択します。 負の値にすることができるエンティティには 2 つのインスタンスがあります。(+) プラスは値を追加または上書きします。(-) マイナスは値を削除します。
7. **[Score Action]\(アクションのスコア付け\)** を選択します。 **name** エンティティはモデルのメモリ内で **Frank** と定義されているので、"**I know your name.It is $name**" (あなたの名前を知っています。$name です) アクションが使用可能になります。
8. **[I know your name.It is $name.]\(あなたの名前を知っています。$name です。\)** を [アクション] リストから選択します。
9. 「**My name is not Frank.**」(私の名前は Frank ではありません。) と 左側のチャット パネルでユーザーの発話に入力します。
10. **[Frank]**、**[-name]\(- 名前\)** の順に選択し、**name** エンティティから値を削除します。
11. **[Score Action]\(アクションのスコア付け\)** を選択します。
12. **[I don't know your name.]\(あなたの名前を知りません。** \) を [アクション] リストから選択します。
13. 「**My name is Susan.**」(私の名前は Susan です。) と 左側のチャット パネルでユーザーの 3 つ目の発話に入力します。
14. **[Susan]**、**[+name]\(+ 名前\)** の順に選択します 

![](../media/T06_training.png)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [複数値エンティティ](./07-multi-value-entities.md)
