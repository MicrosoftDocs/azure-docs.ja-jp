---
title: 特徴を使用して LUIS の単語リストを強化する
titleSuffix: Azure Cognitive Services
description: Language Understanding (LUIS) を使用して、カテゴリやパターンがある意図およびエンティティの検出または予測を改善できるアプリ フィーチャーを追加します
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/17/2020
ms.openlocfilehash: 33435d2367b92f312d41f0e32fdbb3a93d59a9fd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "95999928"
---
# <a name="use-features-to-boost-signal-of-word-list"></a>特徴を使用して単語リストのシグナルをブーストする

LUIS アプリにフィーチャーを追加して、その精度を向上させることができます。 フィーチャーは、特定の単語やフレーズがアプリ領域のボキャブラリの一部であるというヒントを提供することで、LUIS を助けます。

特徴を使用するタイミングと理由を理解するために、[概念](luis-concept-feature.md)を確認してください。

## <a name="add-phrase-list-as-a-feature"></a>フレーズ リストを特徴として追加する

1. [LUIS ポータル](https://www.luis.ai)にサインインし、自分の **サブスクリプション** と **作成リソース** を選択して、その作成リソースに割り当てられているアプリを表示します。
1. **[マイ アプリ]** ページで自分のアプリの名前を選択して、そのアプリを開きます。
1. **[ビルド]** を選択し、アプリの左側のパネルで **[特徴]** を選択します。

1. **[特徴]** ページで **[+ 作成]** を選択します。

1. **[Create new phrase list feature]\(新しいフレーズ リストの特徴の作成\)** ダイアログ ボックスで、`Cities` などの名前を入力します。 **[値]** ボックスに、`Seattle` などの市区町村の例を入力します。 値は一度に 1 つずつ入力することも、コンマで区切った値のセットを入力することもできます。入力したら **Enter** キーを押します。

    > [!div class="mx-imgBorder"]
    > ![特徴 (フレーズ リスト) の市区町村を追加するスクリーンショット](./media/luis-add-features/add-phrase-list-cities.png)

    LUIS に十分な値を入力すると、提案が表示されます。 提案されたすべての値を **[+ すべて追加]** で追加するか、個々の用語を選択することができます。

1. フレーズを交換して使用できる場合は、 **[これらの値はどれを使っても問題ありません]** をオンのままにします。

1. 語句リストは **グローバル** 設定を使用してアプリ全体に適用したり、特定のモデル (意図またはエンティティ) に適用したりできます。 フレーズ リストを作成する場合、意図またはエンティティの "_特徴_" として、トグルはグローバルに設定されません。 この場合、トグルの意味は、特徴がそのモデルに対してのみローカルであり、そのため、アプリケーションに対しては "_グローバルではない_" ということです。

1. **[Done]** を選択します。 新しい特徴は **[ML の特徴]** ページに追加されます。

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>


> [!Note]
> 語句一覧の削除や非アクティブ化は､ **[ML の特徴]** ページのコンテキスト ツールバーから行うことができます。

## <a name="global-phrase-list-applies-to-entire-app"></a>アプリ全体に適用されるグローバル フレーズ リスト

フレーズ リストは、目的の意図またはエンティティに適用する必要があります。ただし、フレーズ リストを **グローバル** な特徴としてアプリ全体に適用することが必要になる場合もあります。

[ML Features]\(ML の特徴\) ページでフレーズ リストを選択し、上部のコンテキスト ツール バーの **[Make global]\(グローバル化する\)** を選択します。

## <a name="model-as-a-feature"></a>特徴量としてのモデル

エンティティは、[意図またはエンティティの特徴](luis-concept-feature.md)にすることができます。

エンティティを特徴として意図に追加するには、[意図] ページから意図を選択し、コンテキスト ツール バーの上にある **[+ 特徴の追加]** を選択します。 一覧には、特徴として適用できるすべてのフレーズ リストとエンティティが含まれます。

エンティティを特徴として別のエンティティに追加するには、[エンティティ パレット](label-entity-example-utterance.md#adding-entity-as-a-feature-from-the-entity-palette)を使用して、意図の詳細ページで特徴を追加するか、エンティティの詳細ページで[特徴を追加](luis-how-to-add-entities.md#add-a-feature-to-a-machine-learned-entity)することができます。

## <a name="next-steps"></a>次のステップ

特徴を追加、編集、削除、または非アクティブ化した後、もう一度[アプリのトレーニングとテスト](luis-interactive-test.md)を行って、パフォーマンスが改善するかどうかを確認します。
