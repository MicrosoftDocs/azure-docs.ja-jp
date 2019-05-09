---
title: Conversation Learner モデルで分岐操作と元に戻す操作を使用する方法 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner モデルで分岐操作と元に戻す操作を使用する方法について説明します。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: cca7b42c83be3ca428509ea48c387a1c35ddb35a
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2019
ms.locfileid: "55206732"
---
# <a name="how-to-use-branching-and-undo-operations"></a>分岐操作と元に戻す操作の使用方法
このチュートリアルでは、元に戻す操作と分岐処理について取り上げます。


## <a name="details"></a>詳細
### <a name="undo"></a>元に戻す
開発者は、ユーザーの最後の入力またはアクションの選択を "元に戻す" ことができます。 "元に戻す" 操作を実行すると、実際にはバックグラウンドで新しい会話が作成され、直前のステップまで再生されます。  つまり、会話の API 呼び出しとエンティティ検出コールバックが再び呼び出されます。

### <a name="branch"></a>[Branch]\(ブランチ\)
既存のトレーニング会話と同じ方法で開始される新しいトレーニング会話を作成します。これにより、会話のターンを手動で再入力する手間を省くことができます。 "分岐" 操作を実行すると、バックグラウンドで新しい会話が作成され、選択されたステップまで既存のトレーニング会話が再生されます。  つまり、会話の API 呼び出しとエンティティ検出コールバックが再び呼び出されます。


## <a name="requirements"></a>必要条件
このチュートリアルでは、ピザの注文を受け付けるボットが実行されている必要があります。

    npm run demo-pizza

### <a name="open-or-import-the-demo"></a>デモを開くかインポートする

ピザ注文チュートリアルを既に行っている場合は、Web UI の一覧からそのモデルを開くだけです。 それ以外の場合は、[Import Tutorials]\(チュートリアルのインポート\) をクリックして、"Demo-PizzaOrder" という名前のモデルを選択する必要があります。

## <a name="undo"></a>元に戻す

`Undo` 機能の動作方法の例を次に示します。

### <a name="training-dialogs"></a>トレーニング会話
1. 左側のパネルで、[Train Dialogs]\(トレーニング会話\) をクリックしてから、[`New Train Dialog`]\(新しいトレーニング会話\) ボタンをクリックします。
2. 「Order a pizza」(ピザを注文する) と入力します。
3. `Score Actions` ボタンをクリックします。
4. "What would you like on your pizza?" (ピザに何をのせますか?) をクリックして選択します
5. 「anything」(何でも) と入力します。
6. `Undo` ボタンをクリックします。
    - 最後の入力が削除されて、ボットの最後の応答 "What would you like on your pizza?" (ピザに何をのせますか?) になります

## <a name="branch"></a>[Branch]\(ブランチ\)

このデモでは、既存のトレーニング会話を開き、それから分岐することによって新しいトレーニング会話を作成します。

1. 左側のパネルで、[Train Dialogs]\(トレーニング会話\) をクリックします。
2. グリッドに "new order" (新しい注文) で始まるトレーニングが 1 つだけ表示されることに注意してください。
3. グリッドで "new order" (新しい注文) をクリックして、既存のトレーニング会話を開きます。
4. 会話の最後の "no" をクリックします。
5. [Branch]\(分岐\) アイコンをクリックします (次の図で赤い円で囲まれた部分)。
    - ![](../media/tutorial15_branch.PNG)
    - "no" より前にあるトレーニング会話全体が、新しいトレーニング会話にコピーされます。
    - そのため、先行するターンを再入力する手間なく、その場所から新しい会話の "分岐" を検討することができます。
6. 「yes」と入力して Enter キーを押します。
7. `Score Actions` ボタンをクリックします。
    - この時点で、ボットによって自動的に応答が選択されますが、気に入らないのでそれを変更します。
8. ボットの最後の応答をクリックします。
    - これにより、異なる応答を選択できるようになります。
9. "UseLastToppings" (最後のトッピングを使用) を選択します。
10. `Score Actions` ボタンをクリックします。
    - やはり、ボットによって自動的に応答が選択されます。 "You have sausage, cheese and mushrooms on your pizza." (ソーセージ、チーズ、マッシュルームをピザにのせてあります。) になります。 
    - 今度の応答は気に入ったので、そのままにします。
11. `Score Actions` ボタンをクリックします。
    - 再びボットによって自動的に応答が選択され、"Would you like anything else?" (他にありますか?) になります
12. 「no」を入力します。
13. `Save Branch` ボタンをクリックします。
14. グリッドに "new order" で始まるトレーニングが 2 つ表示されるようになったことに注意してください。
    - そのうちの 1 つは、分岐に使用したものです。
    - もう 1 つは、保存したばかりの分岐バージョンです。
    - それぞれをクリックして、これらの予測を確認します。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [バージョン管理とタグ付け](./18-version-tag.md)
