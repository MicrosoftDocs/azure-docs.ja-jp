---
title: トレーニングとデプロイのコンピューティングを作成する (スタジオ)
titleSuffix: Azure Machine Learning
description: スタジオを使用して、機械学習用のトレーニングとデプロイのためのコンピューティング リソース (コンピューティング先) を作成します
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 08/06/2020
ms.topic: conceptual
ms.custom: how-to, contperf-fy21q1
ms.openlocfilehash: 4194c71823e1affde1dcae47fd7e64668b57c0cf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103149360"
---
# <a name="create-compute-targets-for-model-training-and-deployment-in-azure-machine-learning-studio"></a>Azure Machine Learning スタジオでモデルのトレーニングとデプロイのためのコンピューティング先を作成する

この記事では、Azure Machine スタジオでコンピューティング先を作成および管理する方法について説明します。  コンピューティング先の作成と管理は、次を利用して行うこともできます。

* Azure Machine Learning SDK または Azure Machine Learning 用の CLI 拡張機能
  * [コンピューティング インスタンス](how-to-create-manage-compute-instance.md)
  * [コンピューティング クラスター](how-to-create-attach-compute-cluster.md)
  * [Azure Kubernetes Service クラスター](how-to-create-attach-kubernetes.md)
  * [その他のコンピューティング リソース](how-to-attach-compute-targets.md)
* Azure Machine Learning 用 [VS Code 拡張機能](how-to-manage-resources-vscode.md#compute-clusters)


## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning](https://aka.ms/AMLFree) を試してください
* [Azure Machine Learning ワークスペース](how-to-manage-workspace.md)

## <a name="whats-a-compute-target"></a>コンピューティング先とは 

Azure Machine Learning では、さまざまなリソースまたは環境でご利用のモデルをトレーニングでき、それらを総称して [__コンピューティング先__](concept-azure-machine-learning-architecture.md#compute-targets)と呼びます。 コンピューティング先は、ローカル マシンでも、Azure Machine Learning コンピューティング、Azure HDInsight、リモート仮想マシンなどのクラウド リソースでもかまいません。  [モデルをデプロイする場所と方法](how-to-deploy-and-where.md)に関するページで説明されているように、モデルのデプロイ用のコンピューティング先を作成することもできます。

## <a name="view-compute-targets"></a><a id="portal-view"></a>コンピューティング先を表示する

ワークスペースのすべてのコンピューティング先を表示するには、次の手順を使用します。

1. [Azure Machine Learning Studio](https://ml.azure.com) に移動します。
 
1. __[管理]__ の下にある、 __[コンピューティング]__ を選択します。

1. 上部のタブを選択すると、コンピューティング先が種類ごとに表示されます。

    :::image type="content" source="media/how-to-create-attach-studio/view-compute-targets.png" alt-text="コンピューティング先の一覧を表示する":::

## <a name="create-compute-target"></a><a id="portal-create"></a>コンピューティング先を作成する

コンピューティング先の一覧を表示するには、前の手順に従います。 その後、次の手順を使用してコンピューティング先を作成します。

1. 作成するコンピューティングの種類に対応する上部のタブを選択します。

1. コンピューティング先がない場合は、ページの中央にある **[作成]** を選択します。
  
    :::image type="content" source="media/how-to-create-attach-studio/create-compute-target.png" alt-text="コンピューティング先を作成する":::

1. コンピューティング リソースの一覧が表示されたら、一覧の上にある **[+新規]** を選択します。

    :::image type="content" source="media/how-to-create-attach-studio/select-new.png" alt-text="[新規] の選択":::


1. コンピューティングの種類のフォームに入力します。

  * [コンピューティング インスタンス](#compute-instance)
  * [コンピューティング クラスター](#amlcompute)
  * [推論クラスター](#inference-clusters)
  * [接続型コンピューティング](#attached-compute)

1. __［作成］__ を選択します

1. 一覧からコンピューティング先を選択することによって、作成操作の状態を表示します。

    :::image type="content" source="media/how-to-create-attach-studio/view-list.png" alt-text="一覧からコンピューティングの状態を表示する":::


### <a name="compute-instance"></a>コンピューティング インスタンス

[上の手順](#portal-create)を使用して、コンピューティング インスタンスを作成します。  その後、次のようにフォームに入力します。

:::image type="content" source="media/concept-compute-instance/create-compute-instance.png" alt-text="新しいコンピューティング インスタンスの作成":::


|フィールド  |説明  |
|---------|---------|
|コンピューティング名     |  <li>名前は必須であり、3 文字から 24 文字の長さにする必要があります。</li><li>有効な文字は、大文字、小文字、数字、 **-** 文字です。</li><li>名前の先頭は文字である必要があります</li><li>名前は、Azure リージョン内のすべての既存のコンピューティングで一意である必要があります。 選択した名前が一意でない場合は、アラートが表示されます</li><li>名前に **-** 文字が使用されている場合は、その後に少なくとも 1 文字を続ける必要があります</li>     |
|仮想マシンのタイプ |  CPU または GPU を選択します。 このタイプは作成後に変更することはできません     |
|仮想マシンのサイズ     |  サポートされる仮想マシンのサイズがお客様のリージョンで制限されている可能性があります。 [利用可能な製品のリスト](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)に関するページを確認してください     |
|SSH アクセスの有効化/無効化     |   SSH アクセスは既定では無効になっています。  SSH アクセスは、 作成後に変更することはできません。 [VS Code Remote](how-to-set-up-vs-code-remote.md) を使用して対話形式でデバッグする場合は、アクセスを有効にする必要があります   |
|詳細設定     |  省略可能。 仮想ネットワークを構成する。 **[リソース グループ]** 、 **[仮想ネットワーク]** 、および **[サブネット]** を指定して、Azure Virtual Network (vnet) 内にコンピューティング インスタンスを作成します。 詳細については、vnet でのこれらの[ネットワーク要件](./how-to-secure-training-vnet.md)に関するページを参照してください。  |

### <a name="compute-clusters"></a><a name="amlcompute"></a> コンピューティング クラスター

トレーニング、バッチ推論、強化学習のワークロードのためのシングル ノードまたはマルチ ノードのコンピューティング クラスターを作成します。 [上の手順](#portal-create)を使用して、コンピューティング クラスターを作成します。  その後、次のようにフォームに入力します。


|フィールド  |説明  |
|---------|---------|
|コンピューティング名     |  <li>名前は必須であり、3 文字から 24 文字の長さにする必要があります。</li><li>有効な文字は、大文字、小文字、数字、 **-** 文字です。</li><li>名前の先頭は文字である必要があります</li><li>名前は、Azure リージョン内のすべての既存のコンピューティングで一意である必要があります。 選択した名前が一意でない場合は、アラートが表示されます</li><li>名前に **-** 文字が使用されている場合は、その後に少なくとも 1 文字を続ける必要があります</li>     |
|仮想マシンのタイプ |  CPU または GPU を選択します。 このタイプは作成後に変更することはできません     |
|仮想マシンの優先度 | **[専用]** または **[低優先度]** を選択します。  低優先度の仮想マシンは低コストですが、コンピューティング ノードが保証されません。 ジョブに対して割り込みが発生する場合があります。
|仮想マシンのサイズ     |  サポートされる仮想マシンのサイズがお客様のリージョンで制限されている可能性があります。 [利用可能な製品のリスト](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)に関するページを確認してください     |
|最小ノード数 | プロビジョニングするノードの最小数。 専用のノード数を指定する場合は、その数をここで設定します。 最小値を 0 に設定することにより、コストを節約できます。この場合、クラスターがアイドル状態であれば、ノードに対する課金は発生しません。 |
|ノードの最大数 | プロビジョニングするノードの最大数。 コンピューティングは、ジョブが送信されるときにこのノードの最大数まで自動スケーリングされます。 |
|詳細設定     |  省略可能。 仮想ネットワークを構成する。 **[リソース グループ]** 、 **[仮想ネットワーク]** 、および **[サブネット]** を指定して、Azure Virtual Network (vnet) 内にコンピューティング インスタンスを作成します。 詳細については、vnet でのこれらの[ネットワーク要件](./how-to-secure-training-vnet.md)に関するページを参照してください。   また、リソースへのアクセスを許可するには、[マネージド ID](#managed-identity) をアタッチします。     |

#### <a name="set-up-managed-identity"></a><a name="managed-identity"></a> マネージド ID を設定する

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-intro.md)]

クラスターの作成中、またはコンピューティング クラスターの詳細の編集時に、 **[詳細設定]** で **[マネージド ID を割り当てる]** を切り替えて、システム割り当て ID またはユーザー割り当て ID を指定します。

#### <a name="managed-identity-usage"></a>マネージド ID の使用

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-default.md)]

### <a name="inference-clusters"></a>推論クラスター

> [!IMPORTANT]
> Azure Machine Learning で Azure Kubernetes Service を使用するには、複数の構成オプションがあります。 ネットワークなど、一部のシナリオでは、追加の設定と構成が必要です。 Azure ML での AKS の使用に関する詳細については、「[Azure Kubernetes Service クラスターを作成してアタッチする](how-to-create-attach-kubernetes.md)」を参照してください。

大規模な推論の場合は、Azure Kubernetes Service (AKS) クラスターを作成またはアタッチします。 [上の手順](#portal-create)を使用して、AKS クラスターを作成します。  その後、次のようにフォームに入力します。


|フィールド  |説明  |
|---------|---------|
|コンピューティング名     |  <li>名前は必須です。 名前は 2 文字から 16 文字にする必要があります。 </li><li>有効な文字は、大文字、小文字、数字、 **-** 文字です。</li><li>名前の先頭は文字である必要があります</li><li>名前は、Azure リージョン内のすべての既存のコンピューティングで一意である必要があります。 選択した名前が一意でない場合は、アラートが表示されます</li><li>名前に **-** 文字が使用されている場合は、その後に少なくとも 1 文字を続ける必要があります</li>     |
|Kubernetes Service | **[新規作成]** を選択し、フォームの残りの部分を入力します。  または、 **[既存のものを使用]** を選択して、サブスクリプションから既存の AKS クラスターを選択します。
|リージョン |  クラスターが作成されるリージョンを選択します。 |
|仮想マシンのサイズ     |  サポートされる仮想マシンのサイズがお客様のリージョンで制限されている可能性があります。 [利用可能な製品のリスト](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)に関するページを確認してください     |
|クラスターの目的  | **[運用]** または **[開発テスト]** を選択します。 |
|ノードの数 | ノードの数に仮想マシンのコア数 (vCPU) を掛けた値は 12 以上である必要があります。 |
| ネットワーク構成 | **[詳細]** を選択して、既存の仮想ネットワーク内にコンピューティングを作成します。 仮想ネットワークでの AKS の詳細については、[プライベート エンドポイントと仮想ネットワークでのトレーニング中や推論中のネットワークの分離](./how-to-secure-inferencing-vnet.md)に関するページを参照してください。 |
| SSL 構成を有効にする | これを使用して、コンピューティングの SSL 証明書を構成します。 |

### <a name="attached-compute"></a>アタッチ型コンピューティング

Azure Machine Learning ワークスペースの外部に作成されたコンピューティング先を使用するには、それらをアタッチする必要があります。 コンピューティング ターゲットをアタッチすることで、ワークスペースで利用できるようにします。  **トレーニング** のためのコンピューティング先をアタッチするには、**アタッチ型コンピューティング** を使用します。  **推論** のための AKS クラスターをアタッチするには、**推論クラスター** を使用します。

[上の手順](#portal-create)を使用して、コンピューティングをアタッチします。  その後、次のようにフォームに入力します。

1. コンピューティング ターゲットの名前を入力します。 
1. アタッチするコンピューティングの種類を選択します。 Azure Machine Learning Studio から、すべてのコンピューティングの種類をアタッチできるわけではありません。 現在トレーニング用に接続できるコンピューティングの種類は次のとおりです。
    * Azure 仮想マシン (Data Science Virtual Machine をアタッチするため)
    * Azure Databricks (機械学習パイプラインで使用)
    * Azure Data Lake Analytics (機械学習パイプラインで使用)
    * Azure HDInsight

1. フォームに入力し、必要なプロパティの値を指定します。

    > [!NOTE]
    > パスワードよりも安全な SSH キーを使用することをお勧めします。 パスワードはブルート フォース攻撃に対して脆弱です。 SSH キーは暗号署名に依存します。 Azure Virtual Machines で使用するための SSH キーを作成する方法の詳細については、次のドキュメントを参照してください。
    >
    > * [Linux または macOS で SSH キーを作成して使用する](../virtual-machines/linux/mac-create-ssh-keys.md)
    > * [Windows で SSH キーを作成して使用する](../virtual-machines/linux/ssh-from-windows.md)

1. __[接続]__ を選択します。 


## <a name="next-steps"></a>次のステップ

コンピューティング先を作成してワークスペースにアタッチしたら、`ComputeTarget` オブジェクトを使用して[実行構成](how-to-set-up-training-targets.md)でそれを使用します。

```python
from azureml.core.compute import ComputeTarget
myvm = ComputeTarget(workspace=ws, name='my-vm-name')
```

* コンピューティング リソースを使用して、[トレーニング実行を送信](how-to-set-up-training-targets.md)します。
* [チュートリアル:モデルのトレーニング](tutorial-train-models-with-aml.md)に関する記事では、マネージド コンピューティング先を使用してモデルをトレーニングします。
* より優れたモデルを構築するために、[ハイパーパラメーター](how-to-tune-hyperparameters.md)を効率的に調整する方法を学習します。
* モデルのトレーニングが済んだら、[モデルをデプロイする方法と場所](how-to-deploy-and-where.md)を確認します。
* [Azure Machine Learning と Azure Virtual Network を使用する](./how-to-network-security-overview.md)