---
title: 事前構築済み意図
titleSuffix: Azure Cognitive Services
description: LUIS には一連の事前構築済み意図が含まれており、一般的な会話形式のユーザー シナリオを簡単に追加できます。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/24/2019
ms.author: diberry
ms.openlocfilehash: 8e11b6d5a9c9df10e789c8e0be3a90d94ecf3d23
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55874398"
---
# <a name="add-prebuilt-intents-for-common-intents"></a>一般的な意図の事前構築済み意図を追加する 

LUIS には事前構築済みドメインの一連の事前構築済み意図が含まれており、一般的な意図と発話を簡単に追加できます。 これは会話型クライアント アプリに機能を追加する迅速かつ簡単な方法であり、これらの機能のモデルを設計する必要はありません。 

## <a name="add-a-prebuilt-intent"></a>事前構築済み意図を追加する

1. **[マイ アプリ]** ページでアプリを選択します。 アプリの **[ビルド]** セクションが開きます。 

1. **[Intents]\(意図\)** ページで、意図一覧の上にあるツール バーの **[Add prebuilt intent]\(事前構築済み意図の追加\)** を選択します。 

1. ポップアップ ダイアログから **Utilities.Cancel** 意図を選択します。 

    ![事前構築済み意図を追加する](./media/luis-prebuilt-intents/prebuilt-intents-ddl.png)

1. **[完了]** ボタンを選択します。

## <a name="train-and-test"></a>トレーニングとテスト

1. 意図を追加した後、右上のツール バーにある **[トレーニング]** を選択することによってアプリをトレーニングします。 

1. 右側にあるツール バーの **[テスト]** を選択して、新しい意図をテストします。 

1. テキスト ボックスに、キャンセルの発話を入力します。

    |テスト発話|予測スコア|
    |--|:--|
    |フライトをキャンセルしたい。|0.67|
    |購入をキャンセルする。|0.52|
    |会議を取り消す。|0.56|

    ![事前構築済み意図をテストする](./media/luis-prebuilt-intents/test.png)

## <a name="next-steps"></a>次の手順
> [!div class="nextstepaction"]
> [事前構築済みのエンティティ](./luis-prebuilt-entities.md)
