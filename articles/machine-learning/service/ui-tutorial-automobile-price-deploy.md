---
title: チュートリアル:ビジュアル インターフェイスで機械学習モデルをデプロイする
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning service のビジュアル インターフェイスで予測分析ソリューションを構築する方法について説明します。 ドラッグ アンド ドロップ モジュールを使用して、機械学習モデルのトレーニング、スコア付け、およびデプロイを行います。 このチュートリアルは、線形回帰を使用した自動車価格の予測に関する 2 部構成のシリーズのパート 2 です。
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 07/11/2019
ms.openlocfilehash: dd28fb51a4fc3fbf3dfc893f2f5f159ccafdb4b3
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839303"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-visual-interface"></a>チュートリアル:ビジュアル インターフェイスで機械学習モデルをデプロイする

[チュートリアルのパート 1](ui-tutorial-automobile-price-train-score.md) で作成した予測モデルを他のユーザーが使用できるように、Azure Web サービスとしてデプロイできます。 これまでは、モデルのトレーニングを実験してきました。 ここでは、ユーザー入力に基づいて新しい予測を生成しましょう。 チュートリアルのこのパートでは、次のことを行います。

> [!div class="checklist"]
> * モデルをデプロイできるよう準備する
> * Web サービスのデプロイ
> * Web サービスをテストする
> * Web サービスを管理する
> * Web サービスを使用する

## <a name="prerequisites"></a>前提条件

[チュートリアルのパート 1](ui-tutorial-automobile-price-train-score.md) を完了して、ビジュアル インターフェイスで機械学習モデルをトレーニングしてスコア付けする方法を学習します。

## <a name="prepare-for-deployment"></a>デプロイの準備をする

実験を Web サービスとしてデプロイする前に、まず "*トレーニング実験*" を "*予測実験*" に変換する必要があります。

1. 実験キャンバスの下部にある **[Create Predictive Experiment]\(予測実験の作成\)** * を選択します。

    ![トレーニング実験から予測実験への自動変換を示すアニメーション GIF](./media/ui-tutorial-automobile-price-deploy/deploy-web-service.gif)

    **[Create Predictive Experiment]\(予測実験の作成\)** を選択すると、以下のようないくつかの処理が行われます。
    
    * トレーニング済みのモデルは、モジュール パレットに**トレーニング済みのモデル** モジュールとして格納されます。 このモジュールは、 **[Trained Models]\(トレーニング済みモデル\)** の下に表示されます。
    * トレーニングに使用したモジュールは削除されます。具体的には次のモジュールが削除されます。
      * モデルのトレーニング
      * データの分割
      * モデルの評価
    * 保存したトレーニング済みのモデルは実験に追加されます。
    * **Web サービスの入力**と **Web サービスの出力**モジュールが追加されます。 これらのモジュールでは、ユーザー データがモデルに入力される位置と、データが返される位置が特定されます。

    **トレーニング実験**は、実験キャンバスの上部にある新しいタブに保存されたままです。

1. **[実行]** します。

1. モデルがまだ機能していることを確認するには、**Score Model (モデルのスコア付け)** モジュールの出力を選択し、 **[結果の表示]** を選択します。 元のデータが、予測価格 ("Scored Labels" (スコア付けラベル)) と共に表示されます。

現在、実験は以下のようになっています。  

![デプロイできるよう準備した後の実験の予測される構成を示すスクリーンショット](./media/ui-tutorial-automobile-price-deploy/predictive-graph.png)

## <a name="deploy-the-web-service"></a>Web サービスをデプロイする

1. キャンバスの下にある **[Deploy Web Service]\(Web サービスのデプロイ\)** を選択します。

1. Web サービスを実行する **[Compute Target]\(コンピューティング ターゲット\)** を選択します。

    現時点では、ビジュアル インターフェイスでは Azure Kubernetes Service (AKS) のコンピューティング ターゲットへのデプロイのみがサポートされています。 機械学習サービス ワークスペース内の使用可能な AKS コンピューティング ターゲットから選択するか、または表示されるダイアログ ボックスの手順を使用して新しい AKS 環境を構成します。

    ![新しいコンピューティング ターゲットの可能な構成を示すスクリーンショット](./media/ui-tutorial-automobile-price-deploy/deploy-compute.png)

1. **[Deploy Web Service]\(Web サービスのデプロイ\)** を選択します。 デプロイが完了したら、次の通知が表示されます。 デプロイには数分かかる場合があります。

    ![デプロイが成功したことを確認するメッセージを示すスクリーンショット。](./media/ui-tutorial-automobile-price-deploy/deploy-succeed.png)

## <a name="test-the-web-service"></a>Web サービスをテストする

ビジュアル インターフェイス Web サービスをテストおよび管理するには、 **[Web サービス]** タブに移動します。

1. Web サービス セクションに移動します。 **Tutorial - Predict Automobile Price[Predictive Exp]** (チュートリアル - 自動車価格を予測する [予測実験]) という名前の、デプロイした Web サービスが表示されます。

     ![最近作成された Web サービスが強調表示された状態の Web サービス タブを示すスクリーンショット](./media/ui-tutorial-automobile-price-deploy/web-services.png)

1. 追加情報を表示する Web サービスの名前を選択します。

     ![Web サービス ビューに表示された追加情報を示すスクリーンショット](./media/ui-tutorial-automobile-price-deploy/web-service-details.png)

1. **[テスト]** を選択します。

    ![Web サービスのテスト ページを示すスクリーンショット](./media/ui-tutorial-automobile-price-deploy/web-service-test.png)

1. テスト データを入力するか、自動入力されたサンプル データを使用し、 **[テスト]** を選択します。

    テスト要求が Web サービスに送信され、ページに結果が表示されます。 価格値は入力データに対して生成されますが、予測値を生成するためには使用されません。

## <a name="consume-the-web-service"></a>Web サービスを使用する

ユーザーは、Azure Web サービスに API 要求を送信し、新しい自動車の価格を予測する結果を受け取ることができるようになりました。

**要求/応答** - ユーザーは、HTTP プロトコルを使用してサービスに 1 つまたは複数の行の自動車データを送信します。 サービスは、1 つまたは複数の結果セットを返します。

REST 呼び出しのサンプルは、Web サービスの詳細ページの **[Consume]\(使用\)** タブに表示されます。

   ![[Consume]\(使用\) タブに表示される REST 呼び出しのサンプルを示すスクリーンショット](./media/ui-tutorial-automobile-price-deploy/web-service-consume.png)

**[API Doc]\(API ドキュメント\)** タブに移動すると、API の追加情報が表示されます。

  ![[API Doc]\(API ドキュメント\) タブに表示される API の追加情報を示すスクリーンショット](./media/ui-tutorial-automobile-price-deploy/web-service-api.png)

## <a name="manage-models-and-deployments"></a>モデルとデプロイの管理

ビジュアル インターフェイスで作成したモデルおよび Web サービスのデプロイは、Azure Machine Learning service ワークスペースからも管理できます。

1. [Azure Portal](https://portal.azure.com/) でワークスペースを開きます。  

1. ワークスペースで、 **[モデル]** を選択します。 次に、作成した実験を選択します。

    ![Azure portal で実験に移動する方法を示すスクリーンショット](./media/ui-tutorial-automobile-price-deploy/portal-models.png)

    このページに、モデルに関する追加情報が表示されます。

    ![Azure portal での実験の統計情報の概要を示すスクリーンショット](./media/ui-tutorial-automobile-price-deploy/model-details.png)

1. **[デプロイ]** を選択すると、モデルを使用するすべての Web サービスが一覧表示されます。 Web サービスの名前を選択すると、Web サービスの詳細ページに移動します。 このページでは、Web サービスのより詳細な情報を取得できます。

    ![詳細な実行レポートを示すスクリーンショット](./media/ui-tutorial-automobile-price-deploy/deployment-details.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>次の手順

このチュートリアルでは、ビジュアル インターフェイスで機械学習モデルを作成、デプロイ、および使用する際の主な手順を学習しました。 ビジュアル インターフェイスを使用して他の種類の問題を解決する方法の詳細については、他のサンプル実験を参照してください。

> [!div class="nextstepaction"]
> [クレジット リスク分類のサンプル](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
