---
title: Conversation Learner モデルで API 呼び出しを使用する方法 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner モデルで API 呼び出しを使用する方法について説明します。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: aba3c2eb925370704ea52364891502a7a09cc9ec
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2019
ms.locfileid: "55212541"
---
# <a name="how-to-add-api-calls-to-a-conversation-learner-model"></a>Conversation Learner モデルに API 呼び出しを追加する方法

このこのチュートリアルでは、モデルに API 呼び出しを追加する方法を示します。 API 呼び出しは、ユーザーが定義してボット内に記述する関数であり、Conversation Learner でそれらを呼び出すことができます。

## <a name="video"></a>ビデオ

[![API 呼び出しのチュートリアルのプレビュー](https://aka.ms/cl_Tutorial_v3_APICalls_Preview)](https://aka.ms/cl_Tutorial_v3_APICalls)

## <a name="requirements"></a>必要条件
このチュートリアルでは、"tutorialAPICalls.ts" ボットが実行されている必要があります。

    npm run tutorial-api-calls

## <a name="details"></a>詳細

- API 呼び出しは、エンティティを読み取って操作できます。
- API 呼び出しは、メモリ マネージャー オブジェクトにアクセスできます。
- API 呼び出しでは、引数を受け取ることができます。

### <a name="open-the-demo"></a>デモを開く

Web UI で、[Import Tutorials]\(チュートリアルのインポート\) をクリックし、"Tutorial-14-APICalls" という名前のモデルを選択します。

### <a name="entities"></a>エンティティ

モデルでは、`number` という名前の 1 つのエンティティが定義されています。

![](../media/tutorial12_entities.PNG)

### <a name="api-calls"></a>API 呼び出し
API 呼び出しのコードは、`C:\<installedpath>\src\demos\tutorialAPICalls.ts` ファイルで定義されています。

![](../media/tutorial12_apicalls.PNG)

- `RandomGreeting` コールバックからは、`greeting` 配列で定義されたランダムなあいさつが返されます。
- `Multiply` コールバックでは、それを呼び出したアクションによって渡された 2 つの数値が乗算されて、UI に表示できる結果が返されます。
    - 1 番目の引数はメモリ マネージャーであることに注意してください。 
    - API コールバックでは複数の入力を受け取ることができることに注意してください (この例では `num1string` と `num2string`)。
- `ClearEntities` コールバックでは、ユーザーが別の数値を入力できるように number エンティティがクリアされます。 
    - API 呼び出しがエンティティをどのように操作できるかを示しています。

### <a name="actions"></a>Actions
4 つのアクションを作成してあります。 そのうちの 3 つは "非待機" API アクションであり、4 つ目は他のチュートリアルで見たものと同じようなことをユーザーに質問する "テキスト" アクションです。 それぞれがどのように作成されているかを見るには、次のようにします。
1. 左側のパネルで [Actions]\(アクション\) をクリックした後、グリッドに一覧表示される 4 つのアクションのいずれかをクリックします。
2. ポップアップ表示されるフォームで各フィールドの値を確認します。
3. API フィールドの横にある [`Refresh`]\(更新\) ボタンに注意してください。
    - ボットを停止し、UI ページが表示されている間に API を変更した場合は、[`Refresh`]\(更新\) ボタンをクリックして、最新の変更を表示できます。

![](../media/tutorial12_actions.PNG)

#### <a name="clearentities-multiply-and-randomgreeting"></a>ClearEntities、Multiply、RandomGreeting
これら 3 つのアクションの種類はすべて API です。 いずれも API コールバック関数に依存して何らかの処理を実行し、ユーザーに対して表示される値を返す可能性があります。

#### <a name="what-number-do-you-want-to-multiply-by-12"></a>"What number do you want to multiply by 12" (どのような値を 12 と乗算しますか)
これは "テキスト" アクションであり、ユーザーに質問するだけです。 このアクションでは、どの API コールバックとも実際に対話が行われることはなく、ユーザーに対して応答として数値を入力することが求められます。この数値は、エンティティのメモリに格納された後、API コールバックの 1 つを使用する "Multiply" アクションによって使用できます。


### <a name="train-dialog"></a>会話をトレーニングする

"トレーニング会話" を見ていきましょう。

1. 左側のパネルで [`Train Dialogs`]\(トレーニング会話\) をクリックした後、[`New Train Dialog`]\(新しいトレーニング会話\) ボタンをクリックします。
2. 「hello」と入力します。
3. `Score Actions` ボタンをクリックします。
4. [`RandomGreeting`] を選択します。 
    - これにより、Random Greeting API 呼び出しが実行されます。
    - ここでは、ユーザーの応答を待機しません。
5. `What number to do you want to multiply by 12?` を選択します
6. 任意の数値だけを 1 つ入力します。
    - 数値に `number` エンティティとして自動的にラベルが付けられたことに注意してください。
7. `Score Actions` ボタンをクリックします。
8. `Multiply` アクションを選択します。
    - 12 の乗算の結果に注意してください。
    - `number` に入力した値がメモリにまだ含まれることに注意してください。
9. `ClearEntities` アクションを選択します。
    - `number` エンティティの値がメモリからクリアされたことに注意してください。
10. `Save` ボタンをクリックします。

これで、API コールバックの登録方法、それらの共通パターン、引数と関連する値とエンティティを定義する方法の説明は終わりです。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [カード (パート 1)](./15-cards.md)
