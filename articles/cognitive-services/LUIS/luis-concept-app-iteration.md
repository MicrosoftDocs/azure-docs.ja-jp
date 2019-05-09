---
title: 反復的アプリ設計
titleSuffix: Language Understanding - Azure Cognitive Services
description: LUIS は、モデル変更、発話の例、公開、エンドポイント クエリからのデータ収集という反復サイクルの中で最も適切に学習します。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: diberry
ms.openlocfilehash: 67bcb33727bc808f5e5bea701daffc77dde736ff
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2019
ms.locfileid: "57337483"
---
# <a name="authoring-cycle-for-your-luis-app"></a>LUIS アプリの作成サイクル
LUIS は、モデル変更、発話の例、公開、エンドポイント クエリからのデータ収集という反復サイクルの中で最も適切に学習します。 

![作成サイクル](./media/luis-concept-app-iteration/iteration.png)

## <a name="building-a-luis-model"></a>LUIS モデルの構築
このモデルの目的は、ユーザーが求めていること (意図) と、回答の決定に役立つ詳細情報 (エンティティ) が質問のどの部分に示されているのかを明らかにすることです。 

関連性のある単語およびフレーズと一般的な語順を決定するためには、モデルがアプリのドメインに固有である必要があります。 

モデルには、意図とエンティティが含まれます。 

## <a name="add-training-examples"></a>トレーニングするための例を追加する
LUIS には、意図内の発話例が必要です。 これらの例には、発話が意味する意図を判断できるくらい十分なバリエーションの単語の選択と語順が必要になります。 各発話例では、必要なあらゆるデータがエンティティとしてラベル付けされている必要があります。 

発話を **None** 意図に割り当てることによって、アプリのドメインに無関係な発話を無視するように LUIS に指示します。 発話から抽出する必要のない単語またはフレーズにはラベル付けをする必要がありません。 無視する単語またはフレーズにはラベルを付けません。 

## <a name="train-and-publish-the-app"></a>アプリをトレーニングして公開する
必要なエンティティがラベル付けされている 10 から 15 のさまざまな発話を意図ごとに用意したら、トレーニングして公開します。 公開成功の通知から、リンクを使用してエンドポイントを取得します。 アプリを作成し、必要な[エンドポイント領域](luis-reference-regions.md)で利用できるように公開します。 

## <a name="https-endpoint-testing"></a>HTTPS エンドポイントのテスト
LUIS アプリは HTTPS エンドポイントからテストできます。 エンドポイントからテストすると、LUIS では、信頼度が低い発話を確認用に選択できます。  

## <a name="recycle"></a>リサイクル
作成サイクルが完了したら、もう一度開始できます。 LUIS によって信頼度が低いとマークされたエンドポイントの発話の見直しから開始します。 意図とエンティティの両方について、これらの発話をチェックします。 発話を確認したら、確認リストが空になります。  

## <a name="batch-testing"></a>バッチ テスト
バッチ テストは、LUIS によってどれだけの数の発話例がスコア付けされているかを確認する方法です。 これらの例は、LUIS にとって初めての内容である必要があり、LUIS に検出させる意図とエンティティに正しくラベル付けされている必要があります。 テスト結果は、その発話セットで LUIS がどの程度の能力を発揮できるか示します。 

## <a name="next-steps"></a>次の手順

[コラボレーション](luis-concept-collaborator.md)の概念を学習します。
