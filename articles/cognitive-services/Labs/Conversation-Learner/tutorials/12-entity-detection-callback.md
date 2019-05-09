---
title: Conversation Learner モデルでエンティティ検出コールバックを使用する方法 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner モデルでエンティティ検出コールバックを使用する方法について説明します。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 576dc6bd44360f73c4133907233e59e5f51837b1
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2019
ms.locfileid: "55212558"
---
# <a name="how-to-use-entity-detection-callback"></a>エンティティ検出コールバックを使用する方法

このチュートリアルでは、エンティティ検出コールバックを紹介し、エンティティ解決の一般的なパターンを例示します。

## <a name="video"></a>ビデオ

[![エンティティ検出コールバックのチュートリアルのプレビュー](https://aka.ms/cl_Tutorial_v3_EntityDetection_Preview)](https://aka.ms/cl_Tutorial_v3_EntityDetection)

## <a name="requirements"></a>必要条件
このチュートリアルでは、`tutorialEntityDetectionCallback` ボットが実行されている必要があります。

    npm run tutorial-entity-detection

## <a name="details"></a>詳細
エンティティ検出コールバックでは、コードによってエンティティ認識動作とエンティティ操作を変更できます。 一般的なエンティティ検出コールバックの設計パターンを示すことで、この機能を説明します。 たとえば、"big apple" (ビッグ アップル) を "new york" (ニューヨーク) に解決します。

## <a name="steps"></a>手順

### <a name="create-the-model"></a>モデルを作成する

1. Web UI で、[New Model]\(新しいモデル\) をクリックします。
2. [Name]\(名前\) フィールドに「EntityDetectionCallback」と入力して、Enter キーを押します。
3. [Create]\(作成\) ボタンをクリックします。

この例では 3 つのエンティティが必要なので、3 つ作成しましょう。

### <a name="create-the-custom-trained-entity"></a>カスタム トレーニング済みエンティティを作成する

1. 左側のパネルで、[Entities]\(エンティティ\)、[New Entity]\(新しいエンティティ\) ボタンの順にクリックします。
2. [Entity Type]\(エンティティの種類\) で [Custom Trained]\(カスタム トレーニング済み\) を選択します。
3. [Entity Name]\(エンティティ名\) に「City」と入力します。
4. [Create]\(作成\) ボタンをクリックします。

### <a name="create-the-first-programmatic-entity"></a>最初のプログラマティック エンティティを作成する

1. [New Entity]\(新しいエンティティ\) ボタンをクリックします。
2. [Entity Type]\(エンティティの種類\) で [Programmatic]\(プログラマティック\) を選択します
3. [Entity Name]\(エンティティ名\) に「CityUnknown」と入力します。
4. [Create]\(作成\) ボタンをクリックします。

### <a name="create-the-first-programmatic-entity"></a>最初のプログラマティック エンティティを作成する

1. [New Entity]\(新しいエンティティ\) ボタンをクリックします。
2. [Entity Type]\(エンティティの種類\) で [Programmatic]\(プログラマティック\) を選択します
3. [Entity Name]\(エンティティ名\) に「CityResolved」と入力します。
4. [Create]\(作成\) ボタンをクリックします。

次に、3 つのアクションを作成します。

### <a name="action-creation"></a>アクションの作成

1. 左側のパネルで [Actions]\(アクション\) をクリックし、[New Action]\(新しいアクション\) ボタンをクリックします。
2. [Bot's response...]\(ボットの応答...\) フィールドに、「Which city do you want?」(どの市を希望しますか?) と入力します
3. [Expected Entity in User reply...]\(ユーザーの応答で予期されるエンティティ...\) フィールドに「City」と入力します。
4. [Disqualifying Entitles]\(不適格エンティティ\) フィールドに「City」と入力します。
5. [Create]\(作成\) ボタンをクリックします。
6. [New Action]\(新しいアクション\) ボタンをクリックします。
7. [Bot's response...]\(ボットの応答...\) フィールドに、「Which city do you want?」(どの市を希望しますか?) と入力します
8. [Expected Entity in User reply...]\(ユーザーの応答で予期されるエンティティ...\) フィールドに「I don't know this city.」(私はこの市を知りません。) と入力します。
9. [Create]\(作成\) ボタンをクリックします。
10. [New Action]\(新しいアクション\) ボタンをクリックします。
11. [Bot's response...]\(ボットの応答...\) フィールドに、「$CityResolved is very nice.」($CityResolved はすばらしいところです。) と入力します
12. [Create]\(作成\) ボタンをクリックします。

コールバック コードを次に示します。

![](../media/tutorial10_callbackcode.PNG)

### <a name="train-the-model"></a>モデルをトレーニングする

1. 左側のパネルで、[Train Dialogs]\(トレーニング会話\) をクリックしてから、[New Train Dialog]\(新しいトレーニング会話\) ボタンをクリックします。
2. [Type your message...]\(メッセージを入力...\) と表示されているチャット パネルで、「hi」(こんにちは) と入力します
3. [Score Actions]\(アクションのスコア付け\) ボタンをクリックします。
4. 応答 "Which city do you want?" (どの市を希望しますか?) を選択します
5. [Type your message...]\(メッセージを入力...\) と表示されているチャット パネルに、「big apple」(ビッグ アップル) と入力します。
6. [Score Actions]\(アクションのスコア付け\) ボタンをクリックします。
    - ボタンをクリックすると、エンティティ検出コールバックがトリガーされます
    - コールバックのコードで、CityResolved エンティティの値が "new york" に正しく設定されます
7. 応答 "new york is very nice." (ニューヨークはすばらしいところです。) を選択します

このパターンは、ボットの多くのシナリオに一般的なものです。 ユーザーの発話と抽出されたエンティティがビジネス ロジックに提供され、そのロジックによって発話が正規の形式に変換されます。そして、後続の会話のターンのために、プログラマティック エンティティに保存されます。

![](../media/tutorial10_bigapple.PNG)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [セッション コールバック](./13-session-callbacks.md)
