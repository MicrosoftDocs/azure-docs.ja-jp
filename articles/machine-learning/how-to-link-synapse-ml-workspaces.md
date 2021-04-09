---
title: Synapse と Azure Machine Learning のワークスペースを使用してリンク サービスを作成する (プレビュー)
titleSuffix: Azure Machine Learning
description: 統一されたデータ ラングリング エクスペリエンスを実現するために Azure Synapse と Azure Machine Learning のワークスペースをリンクする方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.reviewer: nibaccam
ms.date: 03/08/2021
ms.custom: how-to, devx-track-python, data4ml, synapse-azureml
ms.openlocfilehash: 9d2b416955234cae7a30ac65dff7d64dedad3b1d
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104889112"
---
# <a name="link-azure-synapse-analytics-and-azure-machine-learning-workspaces-preview"></a>Azure Synapse Analytics と Azure Machine Learning のワークスペースをリンクする (プレビュー)

この記事では、[Azure Synapse Analytics](/synapse-analytics/overview-what-is.md) ワークスペースと [Azure Machine Learning ワークスペース](concept-workspace.md)をリンクするリンク サービスを作成する方法について説明します。

Azure Machine Learning ワークスペースを Azure Synapse ワークスペースとリンクすることで、Apache Spark プールを大規模なデータ ラングリング専用コンピューティングとしてアタッチし、同じノートブックからモデル トレーニングを実施できます。

ML ワークスペースと Synapse ワークスペースのリンクは、[Python SDK](#link-sdk) または [Azure Machine Learning スタジオ](#link-studio)から行うことができます。

また、複数のワークスペースをリンクし、1 つの [Azure Resource Manager (ARM) テンプレート](https://github.com/Azure/azure-quickstart-templates/blob/master/101-machine-learning-linkedservice-create/azuredeploy.json)を使用して Synapse Spark プールをアタッチすることもできます。

>[!IMPORTANT]
> Azure Machine Learning と Azure Synapse の統合はパブリック プレビュー段階です。 `azureml-synapse` パッケージから提供される機能は[試験段階](/python/api/overview/azure/ml/#stable-vs-experimental)のプレビュー機能であり、随時変更される可能性があります。

## <a name="prerequisites"></a>前提条件

* [Azure Machine Learning ワークスペースを作成](how-to-manage-workspace.md?tabs=python)します。

* [Azure portal で Synapse ワークスペースを作成](/synapse-analytics/quickstart-create-workspace.md)します。

* [Azure portal、Web ツール、または Synapse Studio を使用して Apache Spark プールを作成](/synapse-analytics/quickstart-create-apache-spark-pool-portal.md)します。

* [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro) をインストールします。

* [Azure Machine Learning スタジオ](https://ml.azure.com/)にアクセスします。

<a name="link-sdk"></a>
## <a name="link-workspaces-with-the-python-sdk"></a>Python SDK にワークスペースをリンクさせる

> [!IMPORTANT]
> Synapse ワークスペースを正常にリンクさせるには、Synapse ワークスペースの **所有者** ロールが付与されている必要があります。 [Azure portal](https://ms.portal.azure.com/) でご自身のアクセス権を確認してください。
>
> Synapse ワークスペースの **所有者** ではなく、単に **共同作成者** である場合は、既存のリンク サービスのみを使用できます。 [既存のリンク サービスを取得して使用する](how-to-data-prep-synapse-spark-pool.md#get-an-existing-linked-service)方法を参照してください。

以下のコードでは、次の目的に [`LinkedService`](/python/api/azureml-core/azureml.core.linked_service.linkedservice) クラスと [`SynapseWorkspaceLinkedServiceConfiguration`](/python/api/azureml-core/azureml.core.linked_service.synapseworkspacelinkedserviceconfiguration) クラスを使用しています。

* Machine Learning ワークスペース (`ws`) を Azure Synapse ワークスペースにリンクさせる。
* リンクされたサービスとして Azure Machine Learning に Synapse ワークスペースを登録する。

``` python
import datetime  
from azureml.core import Workspace, LinkedService, SynapseWorkspaceLinkedServiceConfiguration

# Azure Machine Learning workspace
ws = Workspace.from_config()

#link configuration 
synapse_link_config = SynapseWorkspaceLinkedServiceConfiguration(
    subscription_id=ws.subscription_id,
    resource_group= 'your resource group',
    name='mySynapseWorkspaceName')

# Link workspaces and register Synapse workspace in Azure Machine Learning
linked_service = LinkedService.register(workspace = ws,              
                                            name = 'synapselink1',    
                                            linked_service_config = synapse_link_config)
```

> [!IMPORTANT] 
> リンクされたサービスごとにマネージド ID (`system_assigned_identity_principal_id`) が作成されます。 このマネージド ID には、Synapse セッションを開始する前に、Synapse ワークスペースの **Synapse Apache Spark 管理者** ロールが付与されている必要があります。 [Synapse Studio のマネージド ID に Synapse Apache Spark 管理者ロールを割り当てます](../synapse-analytics/security/how-to-manage-synapse-rbac-role-assignments.md)。
>
> 特定のリンクされたサービスの `system_assigned_identity_principal_id` を見つけるには、`LinkedService.get('<your-mlworkspace-name>', '<linked-service-name>')` を使用します。

### <a name="manage-linked-services"></a>リンクされたサービスの管理

Machine Learning ワークスペースに関連付けられているリンクされたサービスをすべて表示します。

```python
LinkedService.list(ws)
```

ワークスペースのリンクを解除するには、`unregister()` メソッドを使用します

``` python
linked_service.unregister()
```

<a name="link-studio"></a>
## <a name="link-workspaces-via-studio"></a>スタジオを使用してワークスペースをリンクさせる

Azure Machine Learning スタジオを使用して Machine Learning ワークスペースと Synapse ワークスペースをリンクさせるには、次の手順に従います。 

1. [Azure Machine Learning スタジオ](https://ml.azure.com/)にサインインします。
1. 左ペインの **[管理]** セクションにある **[リンクされたサービス]** を選択します。
1. **[Add integration]\(統合の追加\)** を選択します。
1. **[Link workspace]\(ワークスペースのリンク\)** フォームの各フィールドの内容を入力します。

    |フィールド| 説明    
    |---|---
    |名前| リンクされたサービスの名前を入力します。 この特定のリンクされたサービスを参照する際に、この名前を使用します。
    |サブスクリプション名 | Machine Learning ワークスペースに関連付けられているサブスクリプションの名前を選択します。 
    |Synapse ワークスペース | リンク先の Synapse ワークスペースを選択します。
    
1. **[次へ]** を選択して **[Select Spark pools (optional)]\(Spark プールの選択 (省略可)\)** フォームを開きます。 このフォームで、ワークスペースにアタッチする Synapse Spark プールを選択します。

1. **[次へ]** を選択して **[Review]\(レビュー\)** フォームを開き、選択内容を確認します。
1. **[作成]** を選択して、リンクされたサービスの作成プロセスを完了します。

## <a name="next-steps"></a>次のステップ

* [Azure Synapse を使用してデータ準備用に Synapse Spark プールをアタッチする (プレビュー)](how-to-data-prep-synapse-spark-pool.md)
* [機械学習パイプライン内の Apache Spark を Azure Synapse と一緒に使用する方法 (プレビュー)](how-to-use-synapsesparkstep.md)
* [モデルをトレーニングします](how-to-set-up-training-targets.md)。
