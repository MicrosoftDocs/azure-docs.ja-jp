---
title: 意図の追加
titleSuffix: Language Understanding - Azure Cognitive Services
description: 意図を LUIS アプリに追加して、同じ意図を持つ質問またはコマンドのグループを特定します。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.subservice: language-understanding
ms.topic: article
ms.date: 04/01/2019
ms.author: diberry
ms.service: cognitive-services
ms.openlocfilehash: ed180563ea6138b3b4bab6092b39eeacf9dbf840
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/12/2019
ms.locfileid: "59521744"
---
# <a name="add-intents-to-determine-user-intention-of-utterances"></a>ユーザーの発話意図を判断する意図を追加する

[意図](luis-concept-intent.md)を LUIS アプリに追加して、同じ意図を持つ質問またはコマンドのグループを特定します。 

インテントを管理するには、上部のナビゲーション バーの **[ビルド]** セクションに移動し、左側のパネルの **[Intents]\(意図\)** を選択します。 

## <a name="add-intent"></a>意図を追加する

1. **[Intents]\(意図\)** ページで、**[Create new intent]\(意図の新規作成\)** を選びます。

1. **[Create new intent]\(意図の新規作成\)** ダイアログ ボックスで、意図の名前 (`GetEmployeeInformation`) を入力し、**[完了]** をクリックします。

    ![意図の追加](./media/luis-how-to-add-intents/Addintent-dialogbox.png)

## <a name="add-an-example-utterance"></a>発話の例を追加する

発話の例とは、ユーザーの質問や命令のサンプル テキストです。 Language Understanding (LUIS) に学習させるには、意図に発話の例を追加する必要があります。

1. **[GetEmployeeInformation]** 意図詳細ページで、意図の名前の下にあるテキスト ボックスに、該当するユーザーから想定される関連する発話 (`Does John Smith work in Seattle?` など) を入力します。
 
    ![意図の詳細ページのスクリーンショット、発話が強調表示されています](./media/luis-how-to-add-intents/add-new-utterance-to-intent.png) 

    LUIS では、すべての発話が小文字に変換され、ハイフンなどのトークンの前後にはスペースが追加されます。

## <a name="intent-prediction-discrepancy-errors"></a>意図予測の不一致エラー 

意図の発話では、選択した意図と予測スコアの間に意図予測の不一致が存在する場合があります。 このような不一致がある場合、LUIS では、サンプル発話の行の**ラベル付き意図**の周囲に、赤いボックスが表示されます。 

![意図の詳細ページのスクリーンショット、発話予測の不一致エラー](./media/luis-how-to-add-intents/prediction-discrepancy-intent.png) 

上部のナビゲーションで、**[トレーニング]** を選択します。 これで、予測の不一致がなくなります。

> [!Note]
> サンプル発話内の単語またはフレーズの下に赤い線が表示されている場合は、[エンティティ予測エラー](luis-how-to-add-example-utterances.md#entity-status-predictions)が発生しています。 このエラーは修正する必要があります。 

## <a name="add-a-custom-entity"></a>カスタム エンティティの追加

意図に発話を追加したら、発話内からテキストを選択してカスタム エンティティを作成できます。 カスタム エンティティは、抽出するテキスト (および正しい意図) をタグ付けするためのものです。 

詳細については、[発話に対するエンティティの追加](luis-how-to-add-example-utterances.md)に関するページを参照してください。

## <a name="entity-prediction-discrepancy-errors"></a>エンティティ予測の不一致エラー 

エンティティに赤い下線が引かれ、[エンティティ予測不一致](luis-how-to-add-example-utterances.md#entity-status-predictions)があることが示されます。 これがエンティティの最初のオカレンスであるため、このテキストが正しいエンティティでタグ付けされていることを LUIS が自信を持って判断するための十分なサンプルがないことを意味しています。 この不一致は、アプリのトレーニングを実行すると解消されます。 

![意図の詳細ページのスクリーンショット (カスタム エンティティ名が青で強調表示されている様子)](./media/luis-how-to-add-intents/create-custom-entity-name-blue-highlight.png) 

テキストが青で強調表示され、それがエンティティであることが示されます。  

## <a name="add-a-prebuilt-entity"></a>作成済みエンティティの追加

詳しくは、「[事前構築済みのエンティティ](luis-how-to-add-entities.md#add-a-prebuilt-entity-to-your-app)」をご覧ください。

## <a name="using-the-contextual-toolbar"></a>コンテキスト ツールバーの使用

一覧で 1 つ以上のサンプル発話が選択されているときに発話の左側のチェック ボックスをオンにすると、発話一覧の上部にあるツール バーを使用して次のアクションを実行できます。

* 意図の再割り当て: 発話を別の意図に移動します
* 発話の削除
* エンティティ フィルター: フィルター処理されたエンティティを含んでいる発話だけを表示します
* すべて表示/エラーのみ: 予測エラーのある発話を表示するか、すべての発話を表示します
* エンティティ/トークン ビュー: エンティティ名付きのエンティティ ビューを表示するか、発話の未加工テキストを表示します
* 虫眼鏡: 特定のテキストを含んでいる発話を検索します

## <a name="working-with-an-individual-utterance"></a>個別の発話の操作

次の操作は、発話の右側にある省略記号のメニューから、個別の発話に対して実行できます。

* 編集: 発話のテキストを変更します
* 削除: 発話を意図から削除します。 発話がまだ必要な場合は、その発話を **None** の意図に移動するほうが合理的です。 
* パターンの追加: パターンを使用すると、共通の発話を使用して、置き換え可能なテキストと無視可能なテキストをマークし、意図内の発話の数を減らすことができます。 

**[Labeled intent]\(ラベル付き意図\)** 列では、発話の意図を変更できます。

## <a name="train-your-app-after-changing-model-with-intents"></a>意図を含むモデルの変更後にアプリをトレーニングする

意図を追加、編集、または削除したら、その変更がエンドポイントのクエリに反映されるように、アプリを[トレーニング](luis-how-to-train.md)して[発行](luis-how-to-publish-app.md)します。 

## <a name="next-steps"></a>次の手順

[発話の例](luis-how-to-add-example-utterances.md)にエンティティを追加する方法について学習する。 
