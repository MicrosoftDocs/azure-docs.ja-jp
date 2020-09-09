---
title: 意図の追加 - LUIS
titleSuffix: Azure Cognitive Services
description: 意図を LUIS アプリに追加して、同じ意図を持つ質問またはコマンドのグループを特定します。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/17/2020
ms.author: diberry
ms.service: cognitive-services
ms.openlocfilehash: fe1c157d6847366a59739cd5128987127d01da94
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/03/2020
ms.locfileid: "84344426"
---
# <a name="add-intents-to-determine-user-intention-of-utterances"></a>ユーザーの発話意図を判断する意図を追加する

[意図](luis-concept-intent.md)を LUIS アプリに追加して、同じ意図を持つ質問またはコマンドのグループを特定します。

LUIS ポータル上で、上部のナビゲーション バーの **[ビルド]** セクションに移動し、左側のパネルの **[意図]** から意図を管理します。

## <a name="add-an-intent-to-your-app"></a>アプリに意図を追加する

1. [LUIS ポータル](https://www.luis.ai)にサインインし、自分の**サブスクリプション**と**作成リソース**を選択して、その作成リソースに割り当てられているアプリを表示します。
1. **[マイ アプリ]** ページで自分のアプリの名前を選択して、そのアプリを開きます。
1. **[意図]** ページで、 **[+ 作成]** を選択します。
1. **[Create new intent]\(意図の新規作成\)** ダイアログ ボックスで、意図の名前 (例: `ModifyOrder`) を入力し、 **[完了]** を選択します。

    > [!div class="mx-imgBorder"]
    > ![意図を追加する](./media/luis-how-to-add-intents/Addintent-dialogbox.png)

    発行された予測エンドポイントで発話を予測するために、意図には[発話例](luis-concept-utterance.md)が必要になります。

## <a name="add-an-example-utterance"></a>発話の例を追加する

発話の例とは、ユーザーの質問や命令のサンプル テキストです。 この意図を予測するタイミングを Language Understanding (LUIS) に学習させるには、意図に発話の例を追加する必要があります。 LUIS では、15 個から 30 個のサンプル発話がないと意図が理解されません。 発話の例はまとめて追加しないでください。 各発話は、意図に既に含まれているサンプルとの違いに気を付けて選択する必要があります。

1. 意図詳細ページで、意図の名前の下にあるテキスト ボックスに、該当するユーザーから想定される関連する発話 (`Deliver a large cheese pizza` など) を入力します。

    > [!div class="mx-imgBorder"]
    > ![意図の詳細ページのスクリーンショット、発話が強調表示されています](./media/luis-how-to-add-intents/add-new-utterance-to-intent.png)

    LUIS では、すべての発話が小文字に変換され、ハイフンなどの[トークン](luis-language-support.md#tokenization)の前後にはスペースが追加されます。

<a name="#intent-prediction-discrepancy-errors"></a>

## <a name="intent-prediction-errors"></a>意図予測のエラー

意図に対してトレーニングされたアプリ上で発話が予測されない場合には、意図予測のエラーが判定されます。

1. 発話予測のエラーを見つけて修正するには、[不適切] および [不明確] の **[フィルター]** オプションを使用します。

    > [!div class="mx-imgBorder"]
    > ![発話予測のエラーを見つけて修正するには、[フィルター] オプションを使用します。](./media/luis-how-to-add-intents/find-intent-prediction-errors.png)

1. [意図] の詳細ページ上にスコア値を表示するには、 **[ビュー]** オプション メニューから **[Show details intent scores]\(詳細な意図のスコアを表示する\)** を選択します。

    フィルターとビューが適用され、エラーを含む発話の例が存在する場合は、発話の例のリストにそれらの発話と問題が表示されます。

各行には、発話の例の現在のトレーニングの予測スコア、最も近いライバルのスコア、これらの 2 つのスコアの差が表示されます。

### <a name="fixing-intents"></a>意図の修正

意図予測のエラーを修正する方法を学習するには、[概要ダッシュボード](luis-how-to-use-dashboard.md)を使用します。 概要ダッシュボードには、アクティブなバージョンの最後のトレーニングに関する分析が表示され、モデルを修正するための最上位の提案が示されます。

## <a name="using-the-contextual-toolbar"></a>コンテキスト ツールバーの使用

コンテキスト ツール バーに他のアクションがあります。

* 発話の例を編集または削除します
* サンプル発話を別の意図に再割り当てします
* フィルターとビュー: フィルター処理されたエンティティが含まれる発話のみを表示するか、オプションの詳細を表示します
* サンプル発話を検索します

## <a name="train-your-app-after-changing-model-with-intents"></a>意図を含むモデルの変更後にアプリをトレーニングする

意図を追加、編集、または削除したら、その変更がエンドポイントのクエリに反映されるように、アプリを[トレーニング](luis-how-to-train.md)して[発行](luis-how-to-publish-app.md)します。 1 つ変更するたびにトレーニングすることはしないでください。 ひとまとまりの変更後にトレーニングしてください。

## <a name="next-steps"></a>次のステップ

[発話の例](luis-how-to-add-example-utterances.md)にエンティティを追加する方法について学習する。
