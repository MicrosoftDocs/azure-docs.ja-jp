---
title: ユーザーの発話を確認する - LUIS
titleSuffix: Azure Cognitive Services
description: アクティブ ラーニングでは、エンドポイント クエリがキャプチャされ、ユーザーの不確かなエンドポイントの発話が選択されます。 これらの発話をレビューして、意図を選択し、文字にされた発話に対応するエンティティをマークすることになります。 それらの変更を承認して発話の例に追加したら、トレーニングして公開します。 そうすることで、LUIS による発話の識別の精度が向上していきます。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: diberry
ms.openlocfilehash: c617e4aa62ce2ff468545bef0b2ebe2c4d0e4f03
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382355"
---
# <a name="how-to-review-endpoint-utterances-in-luis-portal-for-active-learning"></a>アクティブ ラーニングのためにエンドポイント発話を LUIS ポータルでレビューする方法

[アクティブ ラーニング](luis-concept-review-endpoint-utterances.md)では、エンドポイント クエリがキャプチャされ、ユーザーの不確かなエンドポイントの発話が選択されます。 これらの発話をレビューして、意図を選択し、文字にされた発話に対応するエンティティをマークすることになります。 それらの変更を承認して発話の例に追加したら、トレーニングして公開します。 そうすることで、LUIS による発話の識別の精度が向上していきます。


## <a name="enable-active-learning"></a>アクティブ ラーニングを有効にする

アクティブ ラーニングを有効にするには、ユーザーのクエリをログします。 そのためには、`log=true` という querystring パラメーターと値を使って、[エンドポイント クエリ](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint)を設定します。

## <a name="disable-active-learning"></a>アクティブ ラーニングを無効にする

アクティブ ラーニングを無効にするには、ユーザーのクエリがログされないようにします。 そのためには、`log=false` という querystring パラメーターと値を使って、[エンドポイント クエリ](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint)を設定します。

## <a name="filter-utterances"></a>発話をフィルター処理する

1. **[My Apps]\(マイ アプリ\)** ページでアプリ名を選択してアプリ (例: TravelAgent) を開き、上部のバーの **[Build]\(ビルド\)** を選択します。

1. **[Improve app performance]\(アプリのパフォーマンス向上\)** で **[Review endpoint utterances]\(エンドポイントの発話の確認\)** を選択します。

1. **[Review endpoint utterances]\(エンドポイントの発話の確認\)** ページで、 **[Filter list by intent or entity]\(意図またはエンティティでリストをフィルター処理する\)** テキスト ボックス内を選択します。 このドロップダウン リストでは、 **[INTENTS]\(意図\)** にすべての意図が含まれ、 **[ENTITIES]\(エンティティ\)** にすべてのエンティティが含まれています。

    ![発話フィルター](./media/label-suggested-utterances/filter.png)

1. ドロップダウン リストでカテゴリ (意図またはエンティティ) を選択し、発話を確認します。

    ![意図の発話](./media/label-suggested-utterances/intent-utterances.png)

## <a name="label-entities"></a>エンティティにラベルを付ける
LUIS では、エンティティ トークン (単語) が、青色で強調表示されたエンティティ名に置き換えられます。 発話にラベル付けされていないエンティティが含まれている場合は、発話内のそれらのエンティティにラベルを付けます。 

1. 発話内の単語を選択します。 

1. 一覧からエンティティを選択します。

    ![エンティティにラベルを付ける](./media/label-suggested-utterances/label-entity.png)

## <a name="align-single-utterance"></a>1 つの発話を配置する

各発話では、 **[Aligned intent]\(配置される意図\)** 列に意図の候補が表示されます。 

1. その候補に同意する場合は、チェック マークをオンにします。

    ![配置される意図を保持する](./media/label-suggested-utterances/align-intent-check.png)

1. 候補に同意しない場合は、配置される意図のドロップダウン リストから適切な意図を選択し、その配置される意図の右側にあるチェック マークをオンにします。 

    ![意図を配置する](./media/label-suggested-utterances/align-intent.png)

1. チェック マークをオンにすると、その発話はリストから削除されます。 

## <a name="align-several-utterances"></a>複数の発話を配置する

複数の発話を配置するには、発話の左側のチェック ボックスをオンにし、 **[Add selected]\(選択項目の追加\)** を選択します。 

![複数の発話を配置する](./media/label-suggested-utterances/add-selected.png)

## <a name="verify-aligned-intent"></a>配置された意図を確認する

発話に適切な意図が配置されていることを確認するには、 **[Intents]\(意図\)** ページに移動し、意図名を選択して、発話を確認します。 **[Review endpoint utterances]\(エンドポイントの発話の確認\)** の発話がリストに含まれています。

## <a name="delete-utterance"></a>発話を削除する

各発話は、レビュー リストから削除できます。 一度削除すると、リストに再び表示されることはなくなります。 ユーザーがエンドポイントから同じ発話を入力したとしても同様です。 

発話を削除する必要があるかどうかわからない場合は、その発話を意図 [None]\(なし\) に移動するか、"その他" などの新しい意図を作成し、その意図に発話を移動します。 

## <a name="delete-several-utterances"></a>複数の発話を削除する

複数の発話を削除するには、各項目を選択し、 **[Add selected]\(選択項目の追加\)** の右側にあるごみ箱を選択します。

![複数の発話を削除する](./media/label-suggested-utterances/delete-several.png)


## <a name="next-steps"></a>次の手順

推奨される発話にラベル付けした後にパフォーマンスがどのように向上するかをテストするには、上部のパネルの **[Test]\(テスト\)** を選択してテスト コンソールにアクセスします。 テスト コンソールを使用してアプリをテストする手順については、[アプリのトレーニングとテスト](luis-interactive-test.md)に関する記事をご覧ください。
