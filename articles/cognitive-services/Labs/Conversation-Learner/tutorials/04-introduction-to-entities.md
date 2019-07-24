---
title: Conversation Learner モデルでエンティティを使用する方法 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner モデルでエンティティを使用する方法について説明します。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: a42a2ec36d9ce4fb9c139dfddcde0fe0c188c888
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2019
ms.locfileid: "55210354"
---
# <a name="introduction-to-entities"></a>エンティティの概要

このチュートリアルでは、エンティティ、不適格エンティティ、必須エンティティと、Conversation Learner 内でのこれらの使用法を紹介します。

## <a name="video"></a>ビデオ

[![エンティティ チュートリアルのプレビューの概要](https://aka.ms/cl_Tutorial_v3_IntroEntities_Preview)](https://aka.ms/cl_Tutorial_v3_IntroEntities)

## <a name="requirements"></a>必要条件

このチュートリアルでは、汎用チュートリアル ボットが実行されている必要があります。

    npm run tutorial-general

## <a name="details"></a>詳細

エンティティは、ユーザーの発話からの抽出、またはカスタム コードによる割り当てを通じて、ボットがそのタスクを実行するために必要となる情報を取得します。 また、エンティティ自体も、「必須」または「不適格」として明示的に分類されることによって、アクションの使用可能性を制約できます。

- 必須のエンティティは、アクションを使用可能にするために、モデルのメモリ内に存在する必要があります。
- 不適格エンティティは、アクションを利用可能にするために、モデルのメモリ内に "*存在してはなりません*"。

このチュートリアルでは、主にカスタム エンティティを取り上げます。 事前トレーニング済みのエンティティ、複数値エンティティ、負の値のエンティティ、プログラマティック エンティティについては、他のチュートリアルで取り上げています。

## <a name="steps"></a>手順

### <a name="create-the-model"></a>モデルを作成する

1. Web UI で、[新しいモデル] をクリックします。
2. [名前] フィールドに、「IntroToEntities」と入力し、Enter キーを押します。
3. [作成] ボタンをクリックします。

### <a name="entity-creation"></a>エンティティの作成

1. 左側のパネルで、[Entities]\(エンティティ\)、[New Entity]\(新しいエンティティ\) ボタンの順にクリックします。
2. [Entity Type]\(エンティティの種類\) で [Custom Trained]\(カスタム トレーニング済み\) を選択します。
3. [Entity Name]\(エンティティ名\) に「city」と入力します。
4. [作成] ボタンをクリックします。

> [!NOTE]
> "カスタム トレーニング済み" のエンティティ型とは、他の種類のエンティティとは異なり、このエンティティをトレーニングできることを意味します。

### <a name="create-the-actions"></a>アクションの作成

1. 左側のパネルで [アクション] をクリックし、[新しいアクション] をクリックします。
2. [Bot's response...]\(ボットの応答...\) フィールドに、「I don't know what city you want.」と入力します。
3. [Disqualifying Entitles]\(不適格エンティティ\) フィールドに「city」と入力します。
4. [作成] ボタンをクリックします。

> [!NOTE]
> "city" エンティティを "不適格エンティティ" に追加すると、"city" エンティティがボットのメモリで定義されている場合、ボットからこのアクションは不適格と見なされます。

次に、2 番目のアクションを作成します。

1. 左側のパネルで [アクション] をクリックし、[新しいアクション] をクリックします。
2. [Bot's response...]\(ボットの応答...\) フィールドに、「The weather in the $city is probably sunny.」と入力します。
3. [作成] ボタンをクリックします。

> [!NOTE]
> "city" エンティティは、応答の参照によって必須エンティティの一覧に自動的に追加されています。

![](../media/tutorial3_actions.PNG)

### <a name="train-the-model"></a>モデルのトレーニング

1. 左側のパネルで、[Train Dialogs]\(トレーニング会話\) をクリックしてから、[New Train Dialog]\(新しいトレーニング会話\) ボタンをクリックします。
2. [Type your message...]\(メッセージを入力...\) と表示されているチャット ウィンドウで、「hello」と入力します。
    - これは、ユーザー側の会話をシミュレートします。
3. [Score Actions]\(アクションのスコア付け\) ボタンをクリックします。
4. 応答 "I don't know what city you want" を選択します。
5. ユーザーとして、"Seattle" で応答します。
6. [Score Actions]\(アクションのスコア付け\) ボタンをクリックします。
7. 応答 "Weather in $city is probably sunny." を選択します。
8. [保存] ボタンをクリックします。

![](../media/tutorial3_entities.PNG)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [予期されるエンティティ](./05-expected-entity.md)
