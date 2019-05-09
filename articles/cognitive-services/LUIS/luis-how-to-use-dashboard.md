---
title: アプリ ダッシュボード
titleSuffix: Language Understanding - Azure Cognitive Services
description: ご自身のアプリをひと目で監視できる視覚的なレポート作成ツール、アプリケーション ダッシュボードについて説明します。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/04/2019
ms.author: diberry
ms.openlocfilehash: c173152d0a59e391fe77ee855311a867a1b2b6c0
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2019
ms.locfileid: "57338435"
---
# <a name="model-and-usage-statistics-in-the-dashboard"></a>ダッシュボードにおけるモデルと使用状況の統計
アプリ ダッシュボードを使用すると、ご自身のアプリをひと目で監視することができます。 **ダッシュボード**は、**[マイ アプリ]** でアプリケーション名をクリックし、上部パネルから **[ダッシュボード]** を選択してアプリを開いたときに表示されます。 

> [!CAUTION]
> LUIS の最新のメトリックが必要な場合は、以下を実行する必要があります。
> * Azure で作成された LUIS [エンドポイント キー](luis-how-to-azure-subscription.md)を使用する
> * LUIS [API](https://aka.ms/luis-endpoint-apis)、ボットなど、すべてのエンドポイント要求に対して LUIS エンドポイント キーを使用する
> * LUIS アプリごとに異なるエンドポイント キーを使用する。 すべてのアプリに単一のエンドポイント キーを使用しないでください。 エンドポイント キーは、アプリ レベルではなくキー レベルで追跡されます。  

**[ダッシュボード]** ページでは、現在のモデルの状態などの LUIS アプリの概要と、時系列の[エンドポイント](luis-glossary.md#endpoint)使用量を確認できます。 
  
## <a name="app-status"></a>アプリの状態
ダッシュボードには、アプリの最終トレーニング日時、最終発行日時など、アプリケーションのトレーニングおよび発行状態が表示されます。  

![ダッシュボード - アプリの状態](./media/luis-how-to-use-dashboard/app-state.png)

## <a name="model-data-statistics"></a>モデル データの統計情報
ダッシュボードには、アプリに存在する意図、エンティティ、およびラベル付けされた発話の合計数が表示されます。 

![アプリ データの統計情報](./media/luis-how-to-use-dashboard/app-model-count.png)

## <a name="endpoint-hits"></a>エンドポイント ヒット
ダッシュボードには、LUIS アプリが受信したエンドポイント ヒットの合計数が表示されます。また、指定期間内のヒット数を表示することもできます。 表示されたヒットの合計数は、[エンドポイント キー](./luis-concept-keys.md#endpoint-key)を使用するエンドポイント ヒット数と、[作成](./luis-concept-keys.md#authoring-key)キーを使用するエンドポイント ヒット数の合計です。

![エンドポイント ヒット](./media/luis-how-to-use-dashboard/dashboard-endpointhits.png)

> [!NOTE] 
> 最新のエンドポイント ヒット カウントは、LUIS サービスの概要の Azure portal にあります。 
 
### <a name="total-endpoint-hits"></a>エンドポイント ヒットの合計
アプリが作成されてから現在の日付までに受信したエンドポイント ヒットの合計数です。

### <a name="endpoint-hits-per-period"></a>期間ごとのエンドポイント ヒット
過去の期間について、受信した 1 日あたりのヒット数が表示されます。 開始日と終了日の間のポイントは、この期間内の日を表します。 期間内の各ポイントにマウス ポインターを置くと、そのポイントが示す日のヒット カウントが表示されます。 

期間を選択してグラフ上に表示するには:
 
1. **[追加設定]** ![[設定追加] ボタン](./media/luis-how-to-use-dashboard/Dashboard-Settings-btn.png) をクリックして、期間の一覧にアクセスします。 1 週間から 1 年までの期間を選択できます。 

    ![期間ごとのエンドポイント ヒット](./media/luis-how-to-use-dashboard/timerange.png)

2. 一覧から期間を選択し、戻る矢印  ![戻る矢印 ](./media/luis-how-to-use-dashboard/Dashboard-backArrow.png) をクリックして、グラフを表示します。

### <a name="key-usage"></a>キー使用法
アプリケーションのエンドポイント キーからのヒットの数です。 エンドポイント キーの詳細については、「[LUIS でのキー](luis-concept-keys.md)」を参照してください。 
  
## <a name="intent-breakdown"></a>意図の内訳
**[Intent Breakdown]\(意図の内訳\)** には、ラベル付き発話またはエンドポイント ヒットに基づいて意図の内訳が表示されます。 この概要グラフは、アプリ内の各意図の重要度を相対的に示しています。 スライスにマウス ポインターを置くと、意図の名前と、ラベル付き発話/エンドポイント ヒットの合計に対する意図の内訳がパーセンテージで表示されます。 

![意図の内訳](./media/luis-how-to-use-dashboard/intent-breakdown.png)

## <a name="entity-breakdown"></a>エンティティの内訳
ダッシュボードには、ラベル付き発話またはエンドポイント ヒットに基づいてエンティティの内訳が表示されます。 この概要グラフは、アプリ内の各エンティティの重要度を相対的に示しています。 スライスにマウス ポインターを置くと、エンティティの名前と、ラベル付き発話/エンドポイント ヒットのエンティティの内訳がパーセンテージで表示されます。 

![エンティティの内訳](./media/luis-how-to-use-dashboard/entity-breakdown.png)

