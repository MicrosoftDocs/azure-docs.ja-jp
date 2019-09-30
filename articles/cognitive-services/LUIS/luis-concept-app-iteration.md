---
title: 反復的アプリ設計 - LUIS
titleSuffix: Azure Cognitive Services
description: LUIS は、モデル変更、発話の例、公開、エンドポイント クエリからのデータ収集という反復サイクルの中で最も適切に学習します。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: diberry
ms.openlocfilehash: 4356d9e1cd3d6f1a924603f7405d612814d35859
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2019
ms.locfileid: "70256924"
---
# <a name="authoring-cycle-for-your-luis-app"></a>LUIS アプリの作成サイクル
LUIS は、モデル変更、発話の例、公開、エンドポイント クエリからのデータ収集という反復サイクルの中で最も適切に学習します。 

![作成サイクル](./media/luis-concept-app-iteration/iteration.png)

## <a name="building-a-luis-model"></a>LUIS モデルの構築
このモデルの目的は、ユーザーが求めていること (意図) と、回答の決定に役立つ詳細情報 (エンティティ) が質問のどの部分に示されているのかを明らかにすることです。 

関連性のある単語およびフレーズと一般的な語順を決定するためには、モデルがアプリのドメインに固有である必要があります。 

モデルには意図が必要で、エンティティが_ある必要_があります。 

## <a name="add-training-examples"></a>トレーニングするための例を追加する
LUIS には、意図内の発話例が必要です。 これらの例には、発話が意味する意図を判断できるくらい十分なバリエーションの単語の選択と語順が必要になります。 各発話例では、必要なあらゆるデータがエンティティとしてラベル付けされている必要があります。 

発話を **None** 意図に割り当てることによって、アプリのドメインに無関係な発話を無視するように LUIS に指示します。 発話から抽出する必要のない単語またはフレーズにはラベル付けをする必要がありません。 無視する単語またはフレーズにはラベルを付けません。 

## <a name="train-and-publish-the-app"></a>アプリをトレーニングして公開する
必要なエンティティがラベル付けされている 15 から 30 のさまざまな発話を意図ごとに用意したら、[トレーニング](luis-how-to-train.md)して[公開](luis-how-to-publish-app.md)する必要があります。 公開成功の通知から、リンクを使用してエンドポイントを取得します。 必ずアプリを作成して、必要な[エンドポイント領域](luis-reference-regions.md)で利用できるように公開します。 

## <a name="https-prediction-endpoint-testing"></a>HTTPS 予測エンドポイントのテスト
HTTPS 予測エンドポイントから LUIS アプリをテストできます。 予測エンドポイントからテストすると、LUIS では、信頼度が低い発話を[確認](luis-how-to-review-endpoint-utterances.md)用に選択できます。  

## <a name="recycle"></a>リサイクル

作成サイクルが完了したら、もう一度開始できます。 LUIS によって信頼度が低いとマークされた[予測エンドポイントの発話の確認](luis-how-to-review-endpoint-utterances.md)から開始します。 意図とエンティティの両方について、これらの発話をチェックします。 発話を確認したら、確認リストが空になります。  

現在のバージョンを新しいバージョンに[複製](luis-concept-version.md#clone-a-version)し、新しいバージョンで作成の変更を開始することを検討してください。 

## <a name="batch-testing"></a>バッチ テスト

[バッチ テスト](luis-concept-batch-test.md)は、LUIS によってどれだけの数の発話例がスコア付けされているかを確認する方法です。 これらの例は、LUIS にとって初めての内容である必要があり、LUIS に検出させる意図とエンティティに正しくラベル付けされている必要があります。 テスト結果は、その発話セットで LUIS がどの程度の能力を発揮できるか示します。 

## <a name="next-steps"></a>次の手順

[コラボレーション](luis-concept-keys.md)の概念を学習します。
