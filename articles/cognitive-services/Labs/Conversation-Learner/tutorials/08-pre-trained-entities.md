---
title: Conversation Learner モデルに事前トレーニング済みエンティティを追加する方法 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner モデルに事前トレーニング済みエンティティを追加する方法について説明します。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: dade442cb04f79ef75e65d6eb29128b105e72b40
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2019
ms.locfileid: "59565412"
---
# <a name="how-to-add-pre-trained-entities"></a>事前トレーニング済みエンティティを追加する方法
このチュートリアルでは、Conversation Learner モデルに事前トレーニング済みエンティティを追加する方法を説明します。

## <a name="video"></a>ビデオ

[![事前トレーニング済みエンティティのチュートリアルのプレビュー](https://aka.ms/cl_Tutorial_v3_PreTrainedEntities_Preview)](https://aka.ms/cl_Tutorial_v3_PreTrainedEntities)

## <a name="requirements"></a>必要条件
このチュートリアルでは、汎用チュートリアル ボットが実行されている必要があります。

    npm run tutorial-general

## <a name="details"></a>詳細

事前トレーニング済みエンティティでは、数値、日付、金額などの一般的なエンティティの種類が認識されます。  "何もしなくても" 動作し、カスタム エンティティとは異なり、トレーニングする必要はなく、動作を変更することはできません。  既定では、事前トレーニング済みエンティティは複数値であり、エンティティの識別されたすべてのインスタンスが蓄積されます。

## <a name="steps"></a>手順

Web UI のホーム ページから始めます。

### <a name="create-the-model"></a>モデルを作成する

1. **[新しいモデル]** を選択します。
2. **[名前]** に「**PretrainedEntities**」と入力します。
3. **作成** を選択します。

### <a name="entity-creation"></a>エンティティの作成

1. 左側のパネルで **[エンティティ]** を選択し、**[新しいエンティティ]** を選択します。
2. **[エンティティ型]** で **[Pre-Trained/datetimeV2]\(事前トレーニング済み/datetimeV2\)** を選択します。
3. エンティティの 1 つまたは複数の値の蓄積を有効にするには、**[複数値]** をオンにします。 [Pre-Trained Entities]\(事前トレーニング済みエンティティ\) を負の値にすることができない点に注意してください。
4. **作成** を選択します。

![](../media/T08_entity_create.png)

1. 左側のパネルで **[アクション]** を選択し、**[新しいアクション]** を選択します。
2. **[Bot's response]\(ボットの応答\)** に「**The date is $builtin-datetimev2**」(日付は $builtin-datetimev2 です) と入力します
3. **作成** を選択します。

![](../media/T08_action_create_1.png)

### <a name="create-the-second-action"></a>2 つ目のアクションを作成する

1. 左側のパネルで **[アクション]** を選択し、**[新しいアクション]** を選択します。
2. **[Bot's response]\(ボットの応答\)** に「**What's the date?**」(日付を教えて) と入力します。事前トレーニング済みエンティティは、すべての発話に対して既定で認識されるため、**必須エンティティ**にすることはできません。
3. **[Disqualifying Entities]\(不適格エンティティ\)** に「**builtin-datetimev2**」と入力します。
4. **作成** を選択します。

![](../media/T08_action_create_2.png)

### <a name="train-the-model"></a>モデルをトレーニングする

1. 左側のパネルで、**[Train Dialogs]\(トレーニング会話\)** を選択し、**[New Train Dialog]\(新しいトレーニング会話\)** を選択します。
2. 左側のチャット パネルでユーザーの発話に「**hello**」(こんにちは) と入力します。
3. **[Score Action]\(アクションのスコア付け\)** を選択します。
4. [アクション] リストから **[What's the date?]\(日付を教えて\)** を選択します。
5. 左側のチャット パネルでユーザーの発話に「**today**」(今日) と入力します。
    - **today (今日)** の発話が、LUIS の事前トレーニング済みモデルによって自動的に認識されます。
    - 事前トレーニング済みエンティティの値をマウスでポイントすると、LUIS によって提供される追加のデータが表示されます。

![](../media/T08_training.png)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [エンティティ リゾルバー](./09-entity-resolvers.md)
