---
title: Language Understanding の事前構築済みモデル
titleSuffix: Azure Cognitive Services
description: LUIS には一連の事前構築済みモデルが含まれており、一般的な会話形式のユーザー シナリオを簡単に追加できます。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/17/2020
ms.author: diberry
ms.openlocfilehash: abf149d621d283744a71e08ea4ac2199a1994d57
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680957"
---
# <a name="add-prebuilt-models-for-common-usage-scenarios"></a>一般的な使用シナリオ向けに事前構築済みモデルを追加する

LUIS には一連の事前構築済みモデルが含まれており、一般的な会話形式のユーザー シナリオを簡単に追加できます。 これは会話型クライアント アプリケーションに機能を追加する迅速かつ簡単な方法であり、これらの機能のモデルを設計する必要はありません。

## <a name="add-a-prebuilt-domain"></a>事前構築済みのドメインの追加

1. [LUIS ポータル](https://www.luis.ai)にサインインし、自分の**サブスクリプション**と**作成リソース**を選択して、その作成リソースに割り当てられているアプリを表示します。
1. **[マイ アプリ]** ページで自分のアプリの名前を選択して、そのアプリを開きます。

1. 左側のツール バーから **[Prebuilt Domains]\(事前構築済みのドメイン\)** を選択します。

1. アプリに追加するドメインを見つけて、 **[ドメインの追加]** ボタンを選択します。

    > [!div class="mx-imgBorder"]
    > ![カレンダー事前構築済みドメインを追加する](./media/luis-prebuilt-domains/add-prebuilt-domain.png)

## <a name="add-a-prebuilt-intent"></a>事前構築済み意図を追加する

1. [LUIS ポータル](https://www.luis.ai)にサインインし、自分の**サブスクリプション**と**作成リソース**を選択して、その作成リソースに割り当てられているアプリを表示します。
1. **[マイ アプリ]** ページで自分のアプリの名前を選択して、そのアプリを開きます。

1. **[意図]** ページで、意図一覧の上にあるツール バーの **[事前構築済みドメインの意図の追加]** を選択します。

1. ポップアップ ダイアログから意図を選択します。

    > [!div class="mx-imgBorder"]
    > ![事前構築済み意図を追加する](./media/luis-prebuilt-domains/add-prebuilt-domain-intents.png)

1. **[完了]** ボタンを選択します。

## <a name="add-a-prebuilt-entity"></a>作成済みエンティティの追加
1. [LUIS ポータル](https://www.luis.ai)にサインインし、自分の**サブスクリプション**と**作成リソース**を選択して、その作成リソースに割り当てられているアプリを表示します。
1. **[マイ アプリ]** ページで自分のアプリの名前を選択して、そのアプリを開きます。
1. 左側にある **[エンティティ]** を選びます。

1. **[エンティティ]** ページで、 **[事前構築済みエンティティを追加する]** を選択します。

1. **[事前構築済みエンティティの追加]** ダイアログ ボックスで、事前構築済みエンティティを選択します。

    > [!div class="mx-imgBorder"]
    > ![[事前構築済みエンティティの追加] ダイアログ ボックス](./media/luis-prebuilt-domains/add-prebuilt-entity.png)

1. **[Done]** を選択します。 エンティティを追加した後、アプリをトレーニングする必要はありません。

## <a name="add-a-prebuilt-domain-entity"></a>事前構築済みのドメイン エンティティを追加する
1. [LUIS ポータル](https://www.luis.ai)にサインインし、自分の**サブスクリプション**と**作成リソース**を選択して、その作成リソースに割り当てられているアプリを表示します。
1. **[マイ アプリ]** ページで自分のアプリの名前を選択して、そのアプリを開きます。
1. 左側にある **[エンティティ]** を選びます。

1. **[エンティティ]** ページで、 **[Add prebuilt domain entity]\(事前構築済みドメイン エンティティの追\)** を選択します。

1. **[事前構築済みドメイン モデルの追加]** ダイアログ ボックスで、事前構築済みドメイン エンティティを選択します。

1. **[Done]** を選択します。 エンティティを追加した後、アプリをトレーニングする必要はありません。

## <a name="publish-to-view-prebuilt-model-from-prediction-endpoint"></a>発行して予測エンドポイントからあらかじめ構築されるモデルを表示する

事前構築済みモデルの値を見る最も簡単な方法は、公開されているエンドポイントに対してクエリを行うことです。

## <a name="entities-containing-a-prebuilt-entity-token"></a>事前構築済みエンティティのトークンを含むエンティティ

事前構築済みエンティティの必須の特徴を必要とする機械学習エンティティがある場合は、機械学習エンティティにサブエンティティを追加した後、事前構築済みエンティティの "_必須_" 特徴を追加します。

## <a name="next-steps"></a>次のステップ
> [!div class="nextstepaction"]
> [REST API を使用して .csv からモデルを構築する](./luis-tutorial-node-import-utterances-csv.md)
