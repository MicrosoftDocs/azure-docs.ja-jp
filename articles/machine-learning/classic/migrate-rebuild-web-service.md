---
title: 'ML スタジオ (クラシック): Azure Machine Learning に移行する - Web サービスの再構築'
description: スタジオ (クラシック) の Web サービスを Azure Machine Learning のパイプライン エンドポイントとして再構築する
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 03/08/2021
ms.openlocfilehash: 5e467d22cc3230bd9945fb276dc16cf1fa765bb6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103565655"
---
# <a name="rebuild-a-studio-classic-web-service-in-azure-machine-learning"></a>スタジオ (クラシック) の Web サービスを Azure Machine Learning で再構築する

この記事では、スタジオ (クラシック) の Web サービスを Azure Machine Learning の **エンドポイント** として再構築する方法について説明します。

Azure Machine Learning のパイプライン エンドポイントを使用して、予測の作成、モデルの再トレーニング、または任意の汎用パイプラインの実行を行います。 REST エンドポイントを使用すると、任意のプラットフォームからパイプラインを実行できます。 

この記事は、スタジオ (クラシック) から Azure Machine Learning への移行シリーズの一部です。 Azure Machine Learning への移行の詳細については、[移行の概要](migrate-overview.md)に関する記事を参照してください。

> [!NOTE]
> この移行シリーズでは、ドラッグ アンド ドロップ デザイナーに焦点を当てています。 プログラムによるモデルのデプロイの詳細については、「[機械学習モデルを Azure にデプロイする](../how-to-deploy-and-where.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- Azure Machine Learning ワークスペース。 [Azure Machine Learning ワークスペースを作成](../how-to-manage-workspace.md#create-a-workspace)します。
- Azure Machine Learning トレーニング パイプライン。 詳細については、「[Azure Machine Learning でスタジオ (クラシック) 実験を再構築する](migrate-rebuild-experiment.md)」を参照してください。

## <a name="real-time-endpoint-vs-pipeline-endpoint"></a>リアルタイム エンドポイントとパイプライン エンドポイント

スタジオ (クラシック) の Web サービスは、Azure Machine Learning の **エンドポイント** に置き換えられました。 次の表を使用して、使用するエンドポイントの種類を選択します。

|スタジオ (クラシック) の Web サービス| Azure Machine Learning (後継)
|---|---|
|要求/応答 Web サービス (リアルタイム予測)|リアルタイム エンドポイント|
|バッチ Web サービス (バッチ予測)|パイプライン エンドポイント|
|Web サービスの再トレーニング (再トレーニング)|パイプライン エンドポイント| 


## <a name="deploy-a-real-time-endpoint"></a>リアルタイム エンドポイントをデプロイする

スタジオ (クラシック) では、リアルタイム予測のモデルをデプロイするために、**要求/応答 Web サービス** を使用しました。 Azure Machine Learning では、**リアルタイム エンドポイント** を使用します。

Azure Machine Learning でモデルをデプロイするには、複数の方法があります。 最も簡単な方法の 1 つは、デザイナーを使用してデプロイ プロセスを自動化することです。 モデルをリアルタイム エンドポイントとしてデプロイするには、次の手順に従います。

1. 完了したトレーニング パイプラインを少なくとも 1 回実行します。
1. 実行が完了したら、キャンバスの上部で **[Create inference pipeline]\(推論パイプラインの作成\)**  >  **[Real-time inference pipeline]\(リアルタイム推論パイプライン\)** を選択します。

    ![リアルタイム推論パイプラインを作成する](./media/migrate-rebuild-web-service/create-inference-pipeline.png)
        
    トレーニング パイプラインは、デザイナーにより、リアルタイム推論パイプラインに変換されます。 同様の変換は、スタジオ (クラシック) でも行われます。

    デザイナーでは、この変換ステップで、[トレーニング済みのモデルが Azure Machine Learning ワークスペースに登録](../how-to-deploy-and-where.md#registermodel)されます。

1. **[送信]** を選択して、リアルタイム推論パイプラインを実行し、正常に実行されていることを確認します。

1. 推論パイプラインを確認したら、 **[デプロイ]** を選択します。

1. エンドポイントの名前とコンピューティングの種類を入力します。

    次の表は、デザイナーのデプロイ コンピューティング オプションを示しています。

    | コンピューティング ターゲット | 使用目的 | 説明 | 作成 |
    | ----- |  ----- | ----- | -----  |
    |[Azure Kubernetes Service (AKS)](../how-to-deploy-azure-kubernetes-service.md) |リアルタイムの推論|大規模な運用環境のデプロイ。 高速応答時間とサービスの自動スケール。| ユーザーが作成します。 詳細については、[コンピューティング先の作成](../how-to-create-attach-compute-studio.md#inference-clusters)に関するページを参照してください。 |
    |[Azure Container Instances](../how-to-deploy-azure-container-instance.md)|テストまたは開発 | 必要な RAM が 48 GB 未満の小規模な CPU ベース ワークロード。| Azure Machine Learning によって自動的に作成されます。

### <a name="test-the-real-time-endpoint"></a>リアルタイム エンドポイントをテストする

デプロイが完了したら、詳細を確認し、エンドポイントをテストできます。

1. **[エンドポイント]** タブに移動します。
1. ご自分のエンドポイントを選択します。
1. **[テスト]** タブを選びます。
    
    ![[エンドポイント] タブとエンドポイントの [テスト] ボタンを示すスクリーンショット](./media/migrate-rebuild-web-service/test-realtime-endpoint.png)

## <a name="publish-a-pipeline-endpoint-for-batch-prediction-or-retraining"></a>バッチの予測や再トレーニング用にパイプライン エンドポイントを発行する

トレーニング パイプラインを使用して、リアルタイム エンドポイントではなく、**パイプライン エンドポイント** を作成することもできます。 **パイプライン エンドポイント** を使用して、バッチ予測または再トレーニングを実行します。

パイプライン エンドポイントによって、スタジオ (クラシック) の **バッチ実行エンドポイント** と **Web サービスの再トレーニング** が置き換えられます。

### <a name="publish-a-pipeline-endpoint-for-batch-prediction"></a>バッチの予測用にパイプライン エンドポイントを発行する

バッチ予測エンドポイントの発行は、リアルタイムエンド ポイントと似ています。

バッチ予測のパイプライン エンドポイントを発行するには、次の手順に従います。

1. 完了したトレーニング パイプラインを少なくとも 1 回実行します。

1. 実行が完了したら、キャンバスの上部で **[Create inference pipeline]\(推論パイプラインの作成\)**  >  **[Batch inference pipeline]\(バッチ推論パイプライン\)** を選択します。

    ![トレーニング パイプラインの [Create inference pipeline]\(推論パイプラインの作成\) ボタンを示すスクリーンショット](./media/migrate-rebuild-web-service/create-inference-pipeline.png)
        
    トレーニング パイプラインは、デザイナーにより、バッチ推論パイプラインに変換されます。 同様の変換は、スタジオ (クラシック) でも行われます。

    デザイナーでは、このステップで、[トレーニング済みのモデルが Azure Machine Learning ワークスペースに登録](../how-to-deploy-and-where.md#registermodel)されます。

1. **[送信]** を選択して、バッチ推論パイプラインを実行し、正常に実行されていることを確認します。

1. 推論パイプラインを確認したら、 **[発行]** を選択します。
 
1. 新しいパイプライン エンドポイントを作成するか、既存のものを選択します。
    
    パイプライン エンドポイントを新規作成すると、パイプラインに新しい REST エンドポイントが作成されます。 

    既存のパイプライン エンドポイントを選択した場合、既存のパイプラインは上書きされません。 代わりに、エンドポイントの各パイプラインが Azure Machine Learning によりバージョン管理されます。 REST 呼び出しで実行するバージョンを指定できます。 REST 呼び出しでバージョンを指定しない場合は、既定のパイプラインも設定する必要があります。


 ### <a name="publish-a-pipeline-endpoint-for-retraining"></a>再トレーニング用にパイプライン エンドポイントを発行する

再トレーニング用にパイプライン エンドポイントを発行するには、モデルをトレーニングするパイプライン ドラフトが既に存在している必要があります。 トレーニング パイプラインの構築の詳細については、[スタジオ (クラシック) 実験の再構築](migrate-rebuild-experiment.md)に関するページを参照してください。

再トレーニング用にパイプライン エンドポイントを再利用するには、入力データセットの **パイプライン パラメーター** を作成する必要があります。 これにより、トレーニング データセットを動的に設定して、モデルを再トレーニングすることができます。

再トレーニングのパイプライン エンドポイントを発行するには、次の手順に従います。

1. トレーニング パイプラインを少なくとも 1 回実行します。
1. 実行が完了したら、データセット モジュールを選択します。
1. モジュールの詳細ペインで、 **[Set as pipeline parameter]\(パイプライン パラメーターとして設定\)** を選択します。
1. "InputDataset" のようなわかりやすい名前を指定します。    

    ![パイプライン パラメーターの作成方法を強調表示したスクリーンショット](./media/migrate-rebuild-web-service/create-pipeline-parameter.png)

    これにより、入力データセットのパイプライン パラメーターが作成されます。 トレーニングのためにパイプライン エンドポイントを呼び出すときに、新しいデータセットを指定してモデルを再トレーニングできます。

1. **[発行]** を選びます。

    ![トレーニング パイプラインの [発行] ボタンを強調表示したスクリーンショット](./media/migrate-rebuild-web-service/create-retraining-pipeline.png)


## <a name="call-your-pipeline-endpoint-from-the-studio"></a>スタジオからパイプライン エンドポイントを呼び出す

バッチ推論または再トレーニングのパイプライン エンドポイントを作成したら、ブラウザーからエンドポイントを直接呼び出すことができます。

1. **[パイプライン]** タブにアクセスし、 **[パイプライン エンドポイント]** を選択します。
1. 実行するパイプライン エンドポイントを選択します。
1. **[Submit]\(送信\)** をクリックします。

    **[送信]** を選択した後、任意のパイプライン パラメーターを指定できます。

## <a name="next-steps"></a>次のステップ

この記事では、スタジオ (クラシック) の Web サービスを Azure Machine Learning で再構築する方法について説明しました。 次の手順では、[Web サービスとクライアント アプリを統合](migrate-rebuild-integrate-with-client-app.md)します。


スタジオ (クラシック) 移行シリーズの他の記事を参照してください。

1. [移行の概要](migrate-overview.md)に関するドキュメントを参照してください。
1. [データセットを移行します](migrate-register-dataset.md)。
1. [スタジオ (クラシック) のトレーニング パイプラインを再構築します](migrate-rebuild-experiment.md)。
1. **スタジオ (クラシック) の Web サービスを再構築します**。
1. [Azure Machine Learning の Web サービスをクライアント アプリと統合します](migrate-rebuild-integrate-with-client-app.md)。
1. [R スクリプトの実行を移行します](migrate-execute-r-script.md)。
