---
title: Conversation Learner モデルでカードを使用する方法 (パート 1) - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner モデルでカードを使用する方法について説明します。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: a2db74e4c5688c0a2a3975ff828caf20c3cf7904
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2019
ms.locfileid: "55222578"
---
# <a name="how-to-use-cards-part-1-of-2"></a>カードを使用する方法 (パート 1/2)

このチュートリアルでは、ご利用のボットに単純なカードを追加して使用する方法について説明します。

> [!NOTE]
> Conversation Learner は現在、ボットが起動されるディレクトリ内に存在する "cards" という名前のディレクトリにカード定義ファイルがあることを想定しています。

## <a name="video"></a>ビデオ

[![カードのチュートリアルのプレビュー](https://aka.ms/cl_Tutorial_v3_Cards_Preview)](https://aka.ms/cl_Tutorial_v3_Cards)

## <a name="requirements"></a>必要条件
このチュートリアルでは、general tutorial ボットが実行されている必要があります。

    npm run tutorial-general

## <a name="details"></a>詳細

カードは、ユーザーが会話の中でオプションを選択できる UI 要素です。 

### <a name="open-the-demo"></a>デモを開く

Web UI で、[Import Tutorials]\(チュートリアルのインポート\) をクリックし、"Tutorial-15-Cards" という名前のモデルを選択します。

### <a name="the-card"></a>カード

カード定義は次の場所にあります:C:\<installedpath\>\src\cards\prompt.json。

この "cards" ディレクトリにカードの定義があることがシステムによって想定されます。

![](../media/tutorial13_prompt.PNG)

> [!NOTE]
> テキスト フィールド内の本文の型 "TextBlock" と "{{question}}" プレースホルダーにご注意ください。
> 2 つの提出ボタンがあり、さらに、それぞれについて提出されるテキストが存在します。

### <a name="actions"></a>Actions

アクションは 3 つ作成してあります。 次に示すように、1 つ目のアクションはカードです。

![](../media/tutorial13_actions.PNG)

カード アクション タイプがどのように作成されているかを見てみましょう。

![](../media/tutorial13_cardaction.PNG)

> [!NOTE]
> カードには、質問の入力、ボタン 1、ボタン 2 という 3 つの異なるパラメーターが含まれています。 これらの要素はカード内のテンプレート参照であり、ここに質問とそれぞれの答えを入力することになります。 エンティティを参照して使用できるほか、テキストとエンティティの組み合わせを参照することもできます。

カードの体裁は、目のアイコンで表示することができます。

### <a name="practicing-creating-card-actions"></a>カードの作成アクションを実践する

1. 左側のパネルで [アクション] をクリックし、[新しいアクション] をクリックします。
2. [Action Type]\(アクションの種類\) で [カード] を選択します。
3. [テンプレート] 一覧から [prompt]\(プロンプト\) を選択します。
4. [question]\(質問\) フィールドに「Go left or right」(左または右に移動) と入力します
5. [button1]\(ボタン 1\) フィールドに「left」(左) と入力します
6. [button2]\(ボタン 2\) フィールドに「right」(右) と入力します
7. [キャンセル] をクリックします。

### <a name="train-dialog-using-an-adaptive-card"></a>アダプティブ カードを使用したトレーニング会話

1. 左側のパネルで、[Train Dialogs]\(トレーニング会話\) をクリックしてから、[New Train Dialog]\(新しいトレーニング会話\) ボタンをクリックします。
2. [Type your message...]\(メッセージを入力...\) と表示されているチャット パネルで、「hi」(こんにちは) と入力します
3. [Score Actions]\(アクションのスコア付け\) ボタンをクリックします。
4. 応答 "prompt: question:Go left or right?" (プロンプト: 質問:左または右に移動?) を選択します
    - 目のアイコンを使用して、カードのプレビューを表示できます
5. チャット パネルに表示されるプロンプトで、[left]\(左\) ボタンをクリックします。
6. [Score Actions]\(アクションのスコア付け\) ボタンをクリックします。
7. 応答 "left" (左) を選択します
8. [保存] ボタンをクリックします。
9. 応答 "prompt: question:Go left or right?" (プロンプト: 質問:左または右に移動?) を選択します
10. チャット パネルに表示されるプロンプトで、[Right]\(右\) ボタンをクリックします。
11. [Score Actions]\(アクションのスコア付け\) ボタンをクリックします。
12. 応答 "Right" (右) を選択します

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [ハイブリッド ボット](./16-hybrid-bots.md)
