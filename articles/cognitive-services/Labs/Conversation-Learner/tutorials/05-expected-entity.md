---
title: Conversation Learner アクションの "予期されるエンティティ" プロパティを使用する方法 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner モデルの "予期されるエンティティ" プロパティを使用する方法について説明します。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 01d991cff9b7f7a66740f86e537833ffe4e862c7
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2019
ms.locfileid: "55226270"
---
# <a name="how-to-use-the-expected-entity-property-of-actions"></a>アクションの "予期されるエンティティ" プロパティを使用する方法

このチュートリアルでは、アクションの "予期されるエンティティ" プロパティについて説明します。

## <a name="video"></a>ビデオ

[![予期されるエンティティのチュートリアルのプレビュー](https://aka.ms/cl_Tutorial_v3_ExpectedEntity_Preview)](https://aka.ms/cl_Tutorial_v3_ExpectedEntity)

## <a name="requirements"></a>必要条件
このチュートリアルでは、general tutorial ボットが実行されている必要があります。

    npm run tutorial-general

## <a name="details"></a>詳細
アクションの "予期されるエンティティ" プロパティは、このアクションに対するユーザーの応答をエンティティに保存するために使用されます。

アクションの "予期されるエンティティ" プロパティにエンティティを追加すると、システムで次のことが行われます。

1. 最初に、機械学習ベースのエンティティ抽出モデルを使用して、エンティティの照合が試みられます
2. エンティティが見つからない場合、ヒューリスティックに基づいてユーザーの発話全体が $entity に割り当てられます
3. `EntityDetectionCallback` が呼び出されて、アクションの選択に進みます。

## <a name="steps"></a>手順

### <a name="create-the-model"></a>モデルを作成する

1. Web UI で、[New Model]\(新しいモデル\) をクリックします。
2. [Name]\(名前\) フィールドに「ExpectedEntities」と入力して、Enter キーを押します。
3. [Create]\(作成\) ボタンをクリックします。

### <a name="entity-creation"></a>エンティティの作成

1. 左側のパネルで、[Entities]\(エンティティ\)、[New Entity]\(新しいエンティティ\) ボタンの順にクリックします。
2. [Entity Type]\(エンティティの種類\) で [Custom Trained]\(カスタム トレーニング済み\) を選択します。
3. [Entity Name]\(エンティティ名\) に「name」と入力します。
4. [Create]\(作成\) ボタンをクリックします。

> [!NOTE]
> "カスタム トレーニング済み" のエンティティの種類とは、他の種類のエンティティとは異なり、このエンティティをトレーニングできることを意味します。

![](../media/tutorial4_entities.PNG)

### <a name="create-the-first-action"></a>1 つ目のアクションを作成する

1. 左側のパネルで [Actions]\(アクション\) をクリックし、[New Action]\(新しいアクション\) ボタンをクリックします。
2. [Bot's response...]\(ボットの応答...\) フィールドに、「What's your name?」(お名前は?) と入力します
3. [Expected Entities]\(予期されるエンティティ\) フィールドに、「name」と入力します。
4. [Create]\(作成\) ボタンをクリックします。

> [!NOTE]
> このアクションを選択した場合、ユーザーの応答から検出されて抽出されたエンティティは、"name" エンティティに保存されます。 エンティティが検出されない場合は、応答全体がこのエンティティに保存されます。

### <a name="create-the-second-action"></a>2 つ目のアクションを作成する

1. 左側のパネルで [Actions]\(アクション\) をクリックし、[New Action]\(新しいアクション\) ボタンをクリックします。
2. [Bot's response...]\(ボットの応答...\) フィールドに、「Hi $name!」(こんにちは $name!) と入力します。
3. [Create]\(作成\) ボタンをクリックします。

> [!NOTE]
> "name" エンティティは、応答内の参照によって "必須エンティティ" として自動的に追加されました。

これでアクションが 2 つできました。

![](../media/tutorial4_actions.PNG)

### <a name="train-the-model"></a>モデルをトレーニングする

1. 左側のパネルで、[Train Dialogs]\(トレーニング会話\) をクリックしてから、[New Train Dialog]\(新しいトレーニング会話\) ボタンをクリックします。
2. [Type your message...]\(メッセージを入力...\) と表示されているチャット パネルに、「hi」(こんにちは) と入力します。
    - これは、ユーザー側の会話をシミュレートします。
3. [Score Actions]\(アクションのスコア付け\) ボタンをクリックします。
4. 応答 "What's your name?" (お名前は?) を選択します
    - "Hi $name!" (こんにちは $name!) という応答は、その時点でモデルのメモリにおいて "name" エンティティが定義されている必要があるため、選択できません。
5. [Type your message...]\(メッセージを入力...\) と表示されているチャット パネルに、「Frank」と入力します。
    - エンティティとして応答を保存するために以前に設定したヒューリスティックに基づき、"Frank" がエンティティとして強調表示されます。
6. [Score Actions]\(アクションのスコア付け\) ボタンをクリックします。
    - モデルのメモリ内で "name" エンティティが "Frank" として定義されるようになったので、アクションとして "Hi $name!" アクションを選択できます。
7. 応答 "Hi $name!" を選択します
8. [Save]\(保存\) ボタンをクリックします。

代替入力を追加して、モデルをさらにトレーニングします。

1. [Add alternative input...]\(代替入力の追加...\) フィールドに「I'm Jose」と入力します。
    - モデルで名前がエンティティとして認識されないので、エンティティの値としてテキスト ブロック全体が選択されます。
2. "I'm Jose" という語句をクリックし、ごみ箱アイコンをクリックしてもかまいません。
3. "Jose" をクリックし、エンティティの一覧から "name" をクリックします。
4. [Score Action]\(アクションのスコア付け\) をクリックします。
5. 応答 "Hi Frank!" を選択します
6. [Save]\(保存\) ボタンをクリックします。

![](../media/tutorial4_dialogs.PNG)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [否定可能エンティティ](./06-negatable-entities.md)
