---
title: Azure Machine Learning モデルを継続的にデプロイする
titleSuffix: Azure Machine Learning
description: Azure Machine Learning DevOps 拡張機能を使用してモデルを継続的にデプロイする方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 08/03/2020
ms.topic: conceptual
ms.custom: how-to, tracking-python
ms.openlocfilehash: c95e1012decf1e3ae4ee3fbab8aae1a4c1a71be9
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2020
ms.locfileid: "87544498"
---
# <a name="continuously-deploy-models"></a>モデルを継続的にデプロイする

この記事では、Azure DevOps の継続的デプロイを使用して、登録済みモデルの新しいバージョンを自動的に確認し、それらの新しいモデルを運用環境にプッシュする方法について説明します。

## <a name="prerequisites"></a>前提条件

この記事では、Azure Machine Learning ワークスペースにモデルが既に登録されていることを前提としています。 scikit-learn モデルのトレーニングと登録の例については、[こちらのチュートリアル](how-to-train-scikit-learn.md)を参照してください。

## <a name="continuously-deploy-models"></a>モデルを継続的にデプロイする

[Azure DevOps](https://azure.microsoft.com/services/devops/) 用の Machine Learning 拡張機能を使用して、モデルを継続的にデプロイできます。 Azure DevOps 用の Machine Learning 拡張機能を使用して、新しい機械学習モデルが Azure Machine Learning ワークスペースに登録されたときにデプロイ パイプラインをトリガーできます。

1. [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops) にサインアップします。そうすると、任意のプラットフォームや任意のクラウドへのご自分のアプリケーションの継続的インテグレーションとデリバリーを実現できます。 (Azure Pipelines は [Machine Learning パイプライン](concept-ml-pipelines.md#compare)とは異なることに注意してください)。

1. [Azure DevOps プロジェクトを作成します。](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

1. [Azure Pipelines 用の Machine Learning 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml&targetId=6756afbe-7032-4a36-9cb6-2771710cadc2&utm_source=vstsproduct&utm_medium=ExtHubManageList)をインストールします。

1. ご自分のすべての成果物にアクセスするために、サービス接続を使用して、ご自分の Azure Machine Learning ワークスペースへのサービス プリンシパル接続を設定します。 プロジェクト設定に移動し、 **[サービス接続]** を選択して、 **[Azure Resource Manager]** を選択します。

    [![Azure Resource Manager の選択](media/how-to-deploy-and-where/view-service-connection.png)](media/how-to-deploy-and-where/view-service-connection-expanded.png)

1. **[スコープ レベル]** の一覧で、 **[AzureMLWorkspace]** を選択し、残りの値を入力します。

    ![AzureMLWorkspace の選択](media/how-to-deploy-and-where/resource-manager-connection.png)

1. Azure Pipelines を使用してご自分の機械学習モデルを継続的にデプロイするために、パイプラインの下で **[リリース]** を選択します。 新しい成果物を追加し、 **[AzureML Model]** 成果物と前の手順で作成したサービス接続を選択します。 デプロイをトリガーするモデルとバージョンを選択します。

    [![AzureML モデルの選択](media/how-to-deploy-and-where/enable-modeltrigger-artifact.png)](media/how-to-deploy-and-where/enable-modeltrigger-artifact-expanded.png)

1. ご自分のモデル成果物に対してモデル トリガーを有効にします。 トリガーを有効にすると、そのモデルの指定したバージョン (最新バージョン) がワークスペースに登録されるたびに、Azure DevOps リリース パイプラインがトリガーされます。

    [![モデル トリガーを有効にする](media/how-to-deploy-and-where/set-modeltrigger.png)](media/how-to-deploy-and-where/set-modeltrigger-expanded.png)

## <a name="next-steps"></a>次のステップ

ML モデルの継続的なデプロイの例については、GitHub の以下のプロジェクトを参照してください。

* [Microsoft/MLOps](https://github.com/Microsoft/MLOps)
* [Microsoft/MLOpsPython](https://github.com/microsoft/MLOpsPython)