---
title: "\"Hello World\" Conversation Learner モデルを作成する方法 - Microsoft Cognitive Services | Microsoft Docs"
titleSuffix: Azure
description: "\"Hello World\" Conversation Learner モデルを作成する方法について説明します。"
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: b2c43ad2475ab75d251e57fca6009eb1fa2e1f00
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2019
ms.locfileid: "55225297"
---
# <a name="how-to-create-a-hello-world-model-with-conversation-learner"></a>Conversation Learner で "Hello World" モデルを作成する方法

このチュートリアルでは、Conversation Learner でのアクションの作成、対話形式でのボットの学習、エンド ユーザーによる記録された会話の修正など、その基本的な操作方法について説明します。

## <a name="video"></a>ビデオ

[![Hello World チュートリアルのプレビュー](https://aka.ms/cl_Tutorial_v3_HelloWorld_Preview)](https://aka.ms/cl_tutorial_v3_helloworld)


## <a name="requirements"></a>必要条件
LUIS オーサリング キーを含む `.env` ファイルの作成など、セットアップ手順がまだ完了していない場合は、先にすべて済ませておいてください。  詳細については、[クイック スタート](../quickstart.md)を参照してください。

このチュートリアルでは、汎用チュートリアル ボットが実行されている必要があります。

    npm run tutorial-general

## <a name="steps"></a>手順

Web UI のホーム ページから始めます。

### <a name="create-the-model"></a>モデルを作成する
1. [New Model]\(新しいモデル\) ボタンをクリックします。
2. [Name]\(名前\) フィールドに「Hello World」と入力します。
3. [Create]\(作成\) ボタンをクリックします。

作成したモデルのビューが表示されます。

### <a name="create-an-action"></a>アクションを作成する
1. 左側のパネルで [Actions]\(アクション\) をクリックし、[New Action]\(新しいアクション\) ボタンをクリックします。
    - アクションは、Conversation Learner からユーザーに返されるテキスト メッセージ、API 呼び出し、カードのいずれかになります。
2. [Bot's response...]\(ボットの応答...\) フィールドに、「Hello」と入力します。
    - これは、ボットから返されることになる応答です。
3. [Create]\(作成\) ボタンをクリックします。

ボットで実行できる最初のアクション、つまりテキスト応答を返すアクションを作成しました。

### <a name="train-dialogs"></a>トレーニング会話
ここでは、ユーザーの発話に応答する方法についてモデルをトレーニングします。

#### <a name="first-training-dialog"></a>1 つ目のトレーニング会話

1. 左側のパネルで、[Train Dialogs]\(トレーニング会話\) をクリックしてから、[New Train Dialog]\(新しいトレーニング会話\) ボタンをクリックします。
2. 「Hi」と入力して、Enter キーを押します。
    - これは、会話の最初にユーザーが言う可能性があることの例です。
3. [Score Actions]\(アクションのスコア付け\) ボタンをクリックします。
4. "Hello" を選択します。
    - この会話の例での完全なターンを 1 つ完了しました。 
5. ユーザー応答として「Goodbye」と入力します。
6. [Score Actions]\(アクションのスコア付け\) ボタンをクリックします。
7. [+ Action]\(+ アクション\) ボタンをクリックします。
8. 「Goodbye!」と [Bot's response...]\(ボットの応答...\) フィールドに入力し、[Create]\(作成\) ボタンをクリックします。
    - 作成したばかりのアクションでボットが応答したことに注意してください。
9. [Save]\(保存\) ボタンをクリックします。 
    - 終了して、このトレーニング会話が保存されます。

1 つのエンティティと 2 つのアクションを含む 1 つのトレーニング会話をモデルに作成しました。

#### <a name="second-training-dialog"></a>2 つ目のトレーニング会話
さらにもう 1 つトレーニングを実行し、ボットが応答するようすを見てみましょう。

1. [New Train Dialog]\(新しいトレーニング会話\) ボタンをクリックします。
2. 「Hi」と入力します
    - これは 1 つ目の会話に似ているので、ボットから良好なスコアを得られることが予想されます。
3. [Score Actions]\(アクションのスコア付け\) ボタンをクリックします。
    - ポジションとスコアはまだ正確さが不足している可能性があり、さらにトレーニングすることが必要な場合があります。
4. "Hello" (こんにちは) を選択します。
5. 「bye」(じゃあね) とユーザー応答を入力します。
6. [Score Actions]\(アクションのスコア付け\) ボタンをクリックします。
7. "Goodbye!" (さようなら) を選択します
8. [Save]\(保存\) ボタンをクリックします。

### <a name="log-dialogs"></a>会話記録
ここでは、自分または実際のユーザーがボットと行った会話をテスト、表示、修正します。

#### <a name="test-the-model-as-an-end-user"></a>エンド ユーザーとしてモデルをテストする
1. 左側のパネルで、[Log Dialogs]\(会話記録\) をクリックしてから、[New Log Dialog]\(新しい会話記録\) ボタンをクリックします。
2. 「hello there」(やぁ) と入力します。
3. 少し待つと、ボットが "Hello" と自動的に応答するはずです
4. 「byebye」(バイバイ) と入力します
5. 少し待つと、再びボットが "Hello" (こんにちは) と自動的に応答するはずです。
6. [Done Testing]\(テストの完了\) ボタンをクリックします。

#### <a name="view-and-correct-a-user-conversation"></a>ユーザーの会話を表示して修正する
会話記録を使用すると、ユーザーがボットと行った会話の一覧を見ることができます。 それらを編集して、ボットの応答を修正し、トレーニング会話としてやり取りを保存することもできます。 その手順を次に示します。
1. グリッドで、会話の記録をクリックします。
2. ボットの最後のアクションをクリックします (例:"Hello" (こんにちは))。
3. "Goodbye!" (さようなら) を選択して ボットを修正します。
4. [Save As Train Dialog]\(トレーニング会話として保存\) ボタンをクリックします。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [トレーニングの概要](./02-intro-to-training.md)
