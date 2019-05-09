---
title: Conversation Learner モデルでセッション コールバックを使用する方法 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner モデルでセッション コールバックを使用する方法について説明します。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 246b87e46029c2bf4d7361540939181b3b209acc
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2019
ms.locfileid: "55219133"
---
# <a name="how-to-use-session-callbacks-with-a-conversation-learner-model"></a>Conversation Learner モデルでセッション コールバックを使用する方法

このチュートリアルでは、セッション、セッションの処理方法、Conversation Learner の onSessionStart および onSessionEnd コールバックについて説明します。

## <a name="video"></a>ビデオ

[![セッション コールバックのチュートリアル プレビュー](https://aka.ms/cl_Tutorial_v3_SessionCallbacks_Preview)](https://aka.ms/cl_Tutorial_v3_SessionCallbacks)

## <a name="requirements"></a>必要条件
このチュートリアルでは、"tutorialSessionCallbacks" ボットが実行されている必要があります。

    npm run tutorial-session-callbacks

## <a name="details"></a>詳細
このチュートリアルでは、セッションの概念とセッションの既定の処理、さらに、その動作を上書きする方法について取り上げています。

Conversation Learner では、セッションは 1 つの中断されないボットとの対話型交換を表します。 セッションは、複数のターンを持つことができますが、30 分を超えた場合は非アクティブと見なしてプログラムで終了できます。  この既定のセッション タイムアウト期間の変更については、ヘルプの "制限" に関するページを参照してください。

この長期の非アクティブ状態は、ボットが新しいセッションを作成し、再帰型ニューラル ネットワークを初期状態にリセットするためです。 既定では、すべてのエンティティ値がクリアされます。 エンティティの値をクリアするこの既定の動作は、次のようにして変更できます。

### <a name="load-the-demo-model"></a>デモ モデルを読み込む

Web UI で、[Import Tutorials]\(チュートリアルのインポート\) をクリックし、"Tutorial-13-SessionCallbacks" という名前のモデルを選択します。

### <a name="code-for-the-callbacks"></a>コールバックのコード

このモデルの 2 つのコールバックのサンプル コードは、`c:\<installedpath>\src\demos\tutorialSessionCallbacks.ts` にあります。

![](../media/tutorial11_code.PNG)

- OnSessionStartCallback: このメソッドは BotName エンティティを設定します。
- OnSessionEndCallback: 保持する内容を指定できます。 この場合、ユーザーの名前と電話番号を除く、すべてのエンティティがクリアされます。

各コールバックは省略可能です。

### <a name="actions"></a>Actions

このモデルでは、4 つのアクションが定義されています。 既存のアクションは、[Actions]\(アクション\) のグリッド ビューに表示されます。

![](../media/tutorial11_actions.PNG)

### <a name="creating-an-end-session-action-for-callback-invocation"></a>終了セッション アクションを作成する (コールバック呼び出しの場合)

1. 左側のパネルで [Actions]\(アクション\) をクリックし、[New Action]\(新しいアクション\) ボタンをクリックします。
2. [Entity Type]\(エンティティの種類\) で [ENDSESSION]\(セッション終了\) を選択します。
3. [Data...]\(データ...\) フィールドに「Done」(完了) と入力します
4. [Create]\(作成\) ボタンをクリックします。

### <a name="edit-an-existing-action"></a>既存のアクションを編集する

1. グリッド ビューから "So, $UserName, you are in $UserLocation" (それでは、$UserName、あなたは $UserLocation にいるのですね) アクションを選択します。
2. [Wait for Response]\(応答を待機する\) チェック ボックスをオフにします。
3. [Save]\(保存\) ボタンをクリックします。

### <a name="chaining-actions"></a>アクションを変更する

1. 左側のパネルで、[Train Dialogs]\(トレーニング会話\) をクリックしてから、[New Train Dialog]\(新しいトレーニング会話\) ボタンをクリックします。
2. [Type your message...]\(メッセージを入力...\) と表示されているチャット パネルに、「hi」(こんにちは) と入力します。
3. [Score Actions]\(アクションのスコア付け\) ボタンをクリックします。
4. 応答 "Hi, I'm Botty. What's your name?" (こんにちは、私は Botty です。あなたは誰ですか) を選択します。
5. [Type your message...]\(メッセージを入力...\) と表示されているチャット パネルに、「Lars」と入力します
6. 応答 "Hi Lars. What is your phone number?" (こんにちは Lars。電話番号は?) を選択します。
7. [Type your message...]\(メッセージを入力...\) と表示されているチャット パネルに、「555-555-5555」と入力します
8. [Score Actions]\(アクションのスコア付け\) ボタンをクリックします。
9. 応答 "Can you tell Botty your location, Lars?" (Botty にあなたの場所を教えてくれますか、Lars?) を選択します。
10. [Type your message...]\(メッセージを入力...\) と表示されているチャット パネルに、「Seattle」と入力します
11. [Score Actions]\(アクションのスコア付け\) ボタンをクリックします。
12. 応答 "So, Lars, you are in Seattle" (それでは、Lars、あなたは Seattle にいるのですね) を選択します
13. 応答 "Done" (完了) を選択します
14. [Save]\(保存\) ボタンをクリックします。

### <a name="testing-the-model"></a>モデルをテストする

1. 左側のパネルで、[Log Dialogs]\(会話記録\) をクリックしてから、[New Log Dialog]\(新しい会話記録\) ボタンをクリックします。
2. [Type your message...]\(メッセージを入力...\) と表示されているチャット パネルで、「hi」(こんにちは) と入力します
3. [Type your message...]\(メッセージを入力...\) と表示されているチャット パネルに、「Lars」と入力します
4. [Type your message...]\(メッセージを入力...\) と表示されているチャット パネルに、「555-555-5555」と入力します
5. [Type your message...]\(メッセージを入力...\) と表示されているチャット パネルに、「Seattle」と入力します
    - この時点で、場所を除くすべてのエンティティ値が保持されているはずです。
6. [Type your message...]\(メッセージを入力...\) と表示されているチャット パネルに、「hello」(こんにちは) と入力します
7. [Type your message...]\(メッセージを入力...\) と表示されているチャット パネルに、「Detroit」と入力します
8. [Session Timeout]\(セッション タイムアウト\) ボタンをクリックします。
    - このボタンをクリックすると、長い非アクティブ状態に対するボットの応答が実行されます
9. [OK] ボタンをクリックします。
10. [Done Testing]\(テストの完了\) ボタンをクリックします。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [API 呼び出し](./14-api-calls.md)
