---
title: パターンと精度
titleSuffix: Language Understanding - Azure Cognitive Services
description: Language Understanding (LUIS) アプリケーションで、パターン テンプレートを追加して予測精度を改善します。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: diberry
ms.openlocfilehash: 202b9632b7a7faaf955874a0300edbe5134b7fa1
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/12/2019
ms.locfileid: "59521256"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>パターンを追加して予測精度を改善する方法
LUIS アプリでエンドポイント発話を受信したら、単語の順序と選択を示す[パターン](luis-concept-patterns.md)を使用して、発話の予測精度を改善します。 パターンでは、特定の[構文](luis-concept-patterns.md#pattern-syntax)を使用して、[エンティティ](luis-concept-entity-types.md)、エンティティの[ロール](luis-concept-roles.md)、およびオプションのテキストの場所を示します。

## <a name="add-template-utterance-to-create-pattern"></a>パターンを作成するためのテンプレート発話の追加
1. **[マイ アプリ]** ページでアプリの名前を選択してアプリを開き、左側のパネルの **[アプリのパフォーマンス向上]** の下で **[パターン]** を選択します。

    ![パターン一覧のスクリーンショット](./media/luis-how-to-model-intent-pattern/patterns-1.png)

2. パターンの適切な意図を選択します。 

    ![意図の選択](./media/luis-how-to-model-intent-pattern/patterns-2.png)

3. テンプレート テキスト ボックスに、テンプレートの発話を入力し、Enter キーを押します。 エンティティ名を入力する場合は、正しいパターン エンティティ構文を使用します。 エンティティ構文は `{` で開始します。 エンティティの一覧が表示されます。 正しいエンティティを選択し、Enter キーを押します。 

    ![パターンのエンティティのスクリーンショット](./media/luis-how-to-model-intent-pattern/patterns-3.png)

    ご自身のエンティティに[ロール](luis-concept-roles.md)が含まれる場合、エンティティ名の後ろにコロンを 1 つ付けて`:`ロールを指定します (`{Location:Origin}` など)。 エンティティのロールの一覧が表示されます。 ロールを選択し、Enter キーを押します。 

    ![ロールを含むエンティティのスクリーンショット](./media/luis-how-to-model-intent-pattern/patterns-4.png)

    正しいエンティティを選択したら、パターンを入力して、Enter キーを押します。 パターンの入力が完了したら、ご自身のアプリを[トレーニング](luis-how-to-train.md)します。

    ![両方の型のエンティティを含む入力済みパターンのスクリーンショット](./media/luis-how-to-model-intent-pattern/patterns-5.png)

## <a name="train-your-app-after-changing-model-with-patterns"></a>パターンを含むモデルの変更後にアプリをトレーニング
パターンを追加、編集、削除、または再割り当てしたら、その変更について、エンドポイントのクエリに影響を与えるようにアプリを[トレーニング](luis-how-to-train.md)して[公開](luis-how-to-publish-app.md)します。 

<a name="search-patterns"></a>
<a name="edit-a-pattern"></a>
<a name="reassign-individual-pattern-to-different-intent"></a>
<a name="reassign-several-patterns-to-different-intent"></a>
<a name="delete-a-single-pattern"></a>
<a name="delete-several-patterns"></a>
<a name="filter-pattern-list-by-entity"></a>
<a name="filter-pattern-list-by-intent"></a>
<a name="remove-entity-or-intent-filter"></a>
<a name="add-pattern-from-existing-utterance-on-intent-or-entity-page"></a>

## <a name="use-contextual-toolbar"></a>コンテキスト ツールバーの使用

パターンの一覧の上部にあるコンテキスト ツールバーを使用して、以下を実行できます。

* パターンの検索
* パターンの編集
* 別の意図への 1 つのパターンの再割り当て
* 別の意図への複数のパターンの再割り当て
* 単一のパターンの削除
* 複数のパターンの削除
* エンティティでのパターンの一覧のフィルター処理
* 意図でのパターンの一覧のフィルター処理
* エンティティまたは意図フィルターの削除
* 意図またはエンティティ ページでの既存の発話からのパターンの追加

## <a name="next-steps"></a>次の手順

* チュートリアルに従って、pattern.any とロールを使用する[パターンをビルド](luis-tutorial-pattern.md)する方法を確認します。
* お使いのアプリを[トレーニング](luis-how-to-train.md)する方法を確認します。
