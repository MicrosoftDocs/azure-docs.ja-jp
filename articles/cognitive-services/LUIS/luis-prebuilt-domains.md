---
title: Language Understanding の事前構築済みドメイン
titleSuffix: Azure Cognitive Services
description: LUIS には一連の事前構築済みドメインが含まれており、一般的な会話形式のユーザー シナリオを簡単に追加できます。
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/24/2019
ms.author: diberry
ms.openlocfilehash: e1b99396c4739dc6f1b7a4da0164553d4c25ef3c
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55867105"
---
# <a name="add-prebuilt-domains-for-common-usage-scenarios"></a>一般的な使用シナリオの事前構築済みドメインを追加する 

LUIS には事前構築済みドメインの一連の事前構築済み意図が含まれており、一般的な意図と発話を簡単に追加できます。 これは会話型クライアント アプリに機能を追加する迅速かつ簡単な方法であり、これらの機能のモデルを設計する必要はありません。 

## <a name="add-a-prebuilt-domain"></a>事前構築済みのドメインの追加

1. **[マイ アプリ]** ページでアプリを選択します。 アプリの **[ビルド]** セクションが開きます。 

1. **[Intents]\(意図\)** ページで、左下のツール バーの **[Add prebuilt domains]\(事前構築済みドメインの追加\)** を選択します。 

1. **[カレンダー]** 意図を選択し、**[ドメインの追加]** ボタンを選択します。

    ![カレンダー事前構築済みドメインを追加する](./media/luis-prebuilt-domains/add-prebuilt-domain.png)

1. 左側のナビゲーションで **[意図]** を選択し、カレンダー意図を表示します。 このドメインの各意図には、`Calendar.` というプレフィックスが追加されます。 発話と共に、このドメインの 2 つのエンティティ `Calendar.Location` と `Calendar.Subject` がアプリに追加されます。 

## <a name="train-and-publish"></a>トレーニングして公開する

1. ドメインを追加した後、右上のツール バーにある **[トレーニング]** を選択することによってアプリをトレーニングします。 

1. 上部ツール バーで **[発行]** を選択します。 **[運用]** に発行します。 

1. 緑の成功通知が表示されたら、**[Refer to the list of endpoints]\(エンドポイントの一覧を参照する\)** リンクを選択して、エンドポイントを表示します。

1. エンドポイントを選択します。 新しいブラウザー タブでそのエンドポイントが開きます。 ブラウザー タブを開いたままにして、**[テスト]** セクションに移動します。

## <a name="test"></a>テスト

**q** パラメーターに値を追加して、エンドポイントで新しい意図をテストします。`Schedule a meeting with John Smith in Seattle next week`

LUIS から、正しい意図と会議の件名が返されます。

```json
{
  "query": "Schedule a meeting with John Smith in Seattle next week",
  "topScoringIntent": {
    "intent": "Calendar.Add",
    "score": 0.824783146
  },
  "entities": [
    {
      "entity": "a meeting with john smith",
      "type": "Calendar.Subject",
      "startIndex": 9,
      "endIndex": 33,
      "score": 0.484055847
    }
  ]
}
```

## <a name="next-steps"></a>次の手順
> [!div class="nextstepaction"]
> [事前構築済みドメイン リファレンス](./luis-reference-prebuilt-domains.md)
