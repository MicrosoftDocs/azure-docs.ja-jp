---
title: Conversation Learner を他のボット構築技術と共に使用する方法 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner を他のボット構築技術と共に使用する方法について説明します。
services: cognitive-services
author: mattm
manager: larsliden
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 07/13/2018
ms.author: v-jaswel
ms.openlocfilehash: b90fc42aa56bfc813ec464670336dea75cff0f0e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58117440"
---
# <a name="how-to-use-conversation-learner-with-other-bot-building-technologies"></a>Conversation Learner を他のボット構築技術と共に使用する方法

このチュートリアルでは、Conversation Learner を他のボット構築技術と共に使用する方法と、これらの技術間でメモリ (または状態) を共有する方法について説明します。 

## <a name="video"></a>ビデオ

[![ハイブリッド ボットのチュートリアルのプレビュー](https://aka.ms/cl_Tutorial_v3_Hybrid_Applications_Preview)](https://aka.ms/cl_Tutorial_v3_Hybrid_Applications)

## <a name="requirements"></a>必要条件
このチュートリアルでは、[Log Dialog]\(会話記録\) の Web UI ではなく、ボット エミュレーターを使用して会話記録を作成する必要があります。 Bot Framework Emulator のセットアップについて詳しくは、[こちら](https://docs.microsoft.com/azure/bot-service/bot-service-debug-emulator?view=azure-bot-service-4.0)をご覧ください。 

このチュートリアルでは、hybrid tutorial ボットが実行されている必要があります。

    npm run tutorial-hybrid

## <a name="details"></a>詳細

Conversation Learner が制御権を持っている間、Conversation Learner セッションに関連するすべての状態が、Conversation Learner のメモリ マネージャーに格納される必要があります。 これが必要なのは、機械学習は状態を使用して会話をどのように進めるかを決定するからです。 セッションの開始時に呼び出される OnSessionStartCallback で、外部状態を Conversation Learner に渡すことができます。 内部状態は、セッションの終了時に OnSessionEndCallback によって返すことができます。

Conversation Learner は、いくつかの初期状態を受け取って値を返す関数呼び出しのようなものと考えることができます。

この例では、2 つの異なるシステムを使用して hybrid ボットを作成します。
1. Conversation Learner モデル <br/>
    Conversation Learner モデルを使用して、ボットの次のアクションを現在のセッションに基づいて決定します。ボットのこの部分は 1 つの初期状態 `isOpen` (ストアが開いているか閉まっているかを示す) を受け取り、別の状態 `purchaseItem` (ユーザーが購入するアイテムの名前) を返します。

2. テキスト マッチング <br />
    単純に、受信したテキストを特定の文字列と照合し、応答します。ボットのこの部分は、ボットの他のストレージ メカニズムを管理し、CL セッションの開始に責任を持ちます。 具体的には、3 つの変数 `usingConversationLearner`、`storeIsOpen`、および `purchaseItem` を管理します。

まず、このデモで使用されているモデルを見てみましょう。

### <a name="open-the-demo"></a>デモを開く

Web UI で、[Import Tutorials]\(チュートリアルのインポート\) をクリックし、"Tutorial-16-HybridBot" という名前のモデルを選択します。

## <a name="entities"></a>エンティティ

エンティティ ページを開き、2 つのエンティティ `isOpen` と `purchaseItem` に注目します。

これらのエンティティの使われ方を理解するために、ファイル `C:\<installedpath>\src\demos\tutorialHybrid.ts` を開き、コールバックを見てみます。

`OnSessionStartCallback` のコードは、`storeIsOpen` の値を BotBuilder 会話ストレージから `isOpen` エンティティの値としてコピーし、Conversation Learner が利用できるようにします。 次のコードをご覧ください。

![](../media/tutorial17_sessionstart.PNG)

同様に、`OnSessionEndCallback` のコードは、(学習済みのアクティビティによってセッションが終了したのであり、単なるタイムアウトではない場合) エンティティ `purchaseItem` の値を BotBuilder ストレージ `purchaseItem` にコピーします。 次のコードをご覧ください。

![](../media/tutorial17_sessionend.PNG)

では、アクションを見てみましょう。

## <a name="actions"></a>Actions

モデルには 4 つのアクションがあります。

アクションで意図されているルールは次のとおりです。

- `isOpen` エンティティが設定されている場合、ボットは「何をお求めですか?」と尋ね、 そのアイテムを `puchaseItem` スロットに格納します。
- `isOpen` が設定されていない場合、ボットは「申し訳ありません。閉店中です」と答えます。
- 他の 2 つのアクションは `END_SESSION` タイプです。
- END_SESSION アクションは、会話が完了したことを ConversationLearner に知らせます。

### <a name="overall-bot-logic"></a>ボット全体のロジック

まず、ボット状態の `usingConversationLearner` フラグが設定されている場合、制御を Conversation Learner に渡します。 そうでない場合、他のメカニズムに制御を渡します。  この例では単純なテキスト マッチングを示していますが、これは LUIS、QnA maker のような他のボット技術のどれであっても、また Conversation Learner の別のインスタンスであっても構いません。

ユーザーがストアを開いたり閉じたりする方法が必要なので、"open store" および "close store" との文字列比較を実行し、"storeIsOpen" フラグを設定します。

次に、Conversation Learner モデルへの制御の譲渡をトリガーする方法が必要です。 "shop" という文字列にマッチしたら、次のことを行います。
- ボットのメモリで `usingConversationLearner` フラグを設定します。
- Conversation Learner モデルに対して "StartSession" メソッドを呼び出します。  これによって "onSessionStartCallback" をトリガーし、これが `isOpen` エンティティの値を初期化します。

次を参照してください。

![](../media/tutorial17_useConversationLearner.PNG)

最後の購入アイテムを表示する "history" とのテキスト マッチングも行います。
最後に、何か他の文字列が入力された場合、利用可能なユーザー コマンドを表示します。

## <a name="train-dialog"></a>会話をトレーニングする

このチュートリアルでは、モデルはあらかじめトレーニングされています。  完全なボットをテストし、セッション開始とセッション終了のコールバックの効果を実際に確認します。

## <a name="testing-the-bot"></a>ボットのテスト

Conversation Learner モデルによって処理された内容しか表示できないため、単一 Conversation Leaner モデルのボットと異なり、Conversation Learner UI でこれをテストすることはできません。

### <a name="install-the-bot-framework-emulator"></a>Bot Framework エミュレーターのインストール

- [https://github.com/Microsoft/BotFramework-Emulator](https://github.com/Microsoft/BotFramework-Emulator) に移動します。
- エミュレーターをダウンロードしてインストールします。

### <a name="configure-the-emulator"></a>エミュレーターの構成

- エミュレーターを開き、URL のターゲットが、ボットを実行しているのと同じポートであることを確認します。 例: `http://localhost:3978/api/messages`

### <a name="test"></a>テスト 

#### <a name="scenario-1-store-is-closed"></a>シナリオ 1:ストアが閉まっている
1. 「shop」と入力します。 これはテキスト マッチングによって処理され、Conversation Learner モデルに制御を渡します。
2. 「hello」と入力します。  `isOpen` 値が設定されていないため、ボットは「申し訳ありません。閉店中です」と答えてセッションを終了します。

#### <a name="scenario-2-store-is-open"></a>シナリオ 2:ストアが開いている
1. 「open store」と入力します。  これは `isOpen` を true に設定します。
1. 「shop」と入力します。
1. 「hello」と入力します。  `isOpen` 値が true に設定されているため、ボットは「何をお求めですか?」と答えます。
1. 「chair」と入力します。 'chair' が CL メモリにエンティティ `purchaseItem` として保存されます。 セッション終了コールバックが呼び出され、コールバックはこの値を会話ストアにコピーします。
1. 「history」と入力します。  最後の `purchaseItem` が chair だったので、ボットは「お客様は椅子を購入しました」と答えます。

## <a name="conclusion"></a>まとめ

以上の学習内容を応用して、Conversation Learner を他の任意のボット構築技術と組み合わせることができるはずです。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [ブランチと "元に戻す"](./17-branch-undo.md)
