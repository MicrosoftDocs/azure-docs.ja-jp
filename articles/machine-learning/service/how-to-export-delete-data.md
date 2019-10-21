---
title: ワークスペース データのエクスポートまたは削除
titleSuffix: Azure Machine Learning
description: Azure portal、CLI、SDK、および認証済み REST API を使用してワークスペースをエクスポートまたは削除する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: ph-com
ms.author: pahusban
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: 18e2ab18dac214e73eaf6ad7dfcb9dbbab0b5cf5
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/15/2019
ms.locfileid: "71002838"
---
# <a name="export-or-delete-your-machine-learning-service-workspace-data"></a>Machine Learning service のワークスペース データをエクスポートまたは削除する 

Azure Machine Learning では、認証済み REST API を使用してワークスペース データをエクスポートまたは削除することができます。 この記事では、その方法について説明します。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="control-your-workspace-data"></a>ワークスペース データを制御する
Azure Machine Learning によって格納された製品内データは、Azure portal、CLI、SDK、および認証済み REST API を使用してエクスポートおよび削除することができます。 テレメトリ データには、Azure Privacy Portal を介してアクセスできます。 

Azure Machine Learning では、個人データは、実行履歴ドキュメント内のユーザー情報と、ユーザーのサービスとの対話のテレメトリ レコードで構成されます。

## <a name="delete-workspace-data-with-the-rest-api"></a>REST API を使用してワークスペース データを削除する 

データを削除するには、HTTP DELETE 動詞を使用して次の API 呼び出しを実行できます。 これらは、要求内に `Authorization: Bearer <arm-token>` ヘッダーがあることで承認されます (`<arm-token>` は `https://management.core.windows.net/` エンドポイントの AAD アクセス トークンです)。  

このトークンの取得と Azure エンドポイントの呼び出し方法については、[Azure REST API のドキュメント](https://docs.microsoft.com/rest/api/azure/)を参照してください。  

以下の例では、{} 内のテキストを、関連付けられているリソースを決定するインスタンス名に置き換えてください。

### <a name="delete-an-entire-workspace"></a>ワークスペース全体を削除する

ワークスペース全体を削除するには、次の呼び出しを使用します。  
> [!WARNING]
> すべての情報が削除され、ワークスペースは使用できなくなります。

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2018-03-01-preview

### <a name="delete-models"></a>モデルを削除する

モデルとその ID の一覧を取得するには、次の呼び出しを使用します。

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2018-03-01-preview

個々のモデルは、次の呼び出しで削除できます。

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2018-03-01-preview

### <a name="delete-assets"></a>アセットを削除する

アセットとその ID のリストを取得するには、次の呼び出しを使用します。

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2018-03-01-preview

個々のアセットは、次の呼び出しで削除できます。

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2018-03-01-preview

### <a name="delete-images"></a>イメージを削除する

イメージとその ID のリストを取得するには、次の呼び出しを使用します。

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2018-03-01-preview

個々のイメージは、次の呼び出しで削除できます。

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2018-03-01-preview

### <a name="delete-services"></a>サービスを削除する

サービスとその ID のリストを取得するには、次の呼び出しを使用します。

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2018-03-01-preview

個々のサービスは、次の呼び出しで削除できます。

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2018-03-01-preview

## <a name="export-service-data-with-the-rest-api"></a>REST API を使用してサービス データをエクスポートする

データをエクスポートするには、HTTP GET 動詞を使用して次の API 呼び出しを実行できます。 これらは、要求内に `Authorization: Bearer <arm-token>` ヘッダー があることで承認されます (`<arm-token>` は `https://management.core.windows.net/` エンドポイントの AAD アクセス トークンです)。  

このトークンの取得と Azure エンドポイントの呼び出し方法については、[Azure REST API のドキュメント](https://docs.microsoft.com/rest/api/azure/)を参照してください。   

以下の例では、{} 内のテキストを、関連付けられているリソースを決定するインスタンス名に置き換えてください。

### <a name="export-workspace-information"></a>ワークスペース情報をエクスポートする

すべてのワークスペースのリストを取得するには、次の呼び出しを使用します。

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces?api-version=2018-03-01-preview

個々のワークスペースの情報は、次の呼び出しで取得できます。

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2018-03-01-preview

### <a name="export-compute-information"></a>コンピューティング情報をエクスポートする

ワークロードに接続されているすべてのコンピューティング ターゲットは、次の呼び出しで取得できます。

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes?api-version=2018-03-01-preview

単一のコンピューティング ターゲットに関する情報は、次の呼び出しで取得できます。

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes/{computeName}?api-version=2018-03-01-preview

### <a name="export-run-history-data"></a>実行履歴データをエクスポートする
すべての実験とその情報のリストを取得するには、次の呼び出しを使用します。

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments 

特定の実験に関するすべての実行は、次の呼び出しで取得できます。

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/runs 

実行履歴項目は、次の呼び出しで取得できます。

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/runs/{runId} 

実験に関するすべての実行メトリックは、次の呼び出しで取得できます。

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/metrics 

単一の実行メトリックは、次の呼び出しで取得できます。

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/metrics/{metricId}
    
### <a name="export-artifacts"></a>成果物をエクスポートする

成果物とそのパスの一覧を取得するには、次の呼び出しを使用します。

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/artifacts/origins/ExperimentRun/containers/{runId}
    
### <a name="export-notifications"></a>通知をエクスポートする

格納されているタスクのリストを取得するには、次の呼び出しを使用します。     

    https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/tasks

単一タスクに関する通知は、次の呼び出しで取得できます。

    https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}tasks/{taskId}

### <a name="export-data-stores"></a>データ ストアをエクスポートする

データ ストアのリストを取得するには、次の呼び出しを使用します。

    https://{location}.experiments.azureml.net/datastore/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/datastores

個々のデータ ストアは、次の呼び出しで取得できます。

    https://{location}.experiments.azureml.net/datastore/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/datastores/{name}

### <a name="export-models"></a>モデルをエクスポートする

モデルとその ID の一覧を取得するには、次の呼び出しを使用します。

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2018-03-01-preview

個々のモデルは、次の呼び出しで取得できます。

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2018-03-01-preview

### <a name="export-assets"></a>アセットをエクスポートする

アセットとその ID のリストを取得するには、次の呼び出しを使用します。

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2018-03-01-preview

個々のアセットは、次の呼び出しで取得できます。

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2018-03-01-preview

### <a name="export-images"></a>イメージをエクスポートする

イメージとその ID のリストを取得するには、次の呼び出しを使用します。

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2018-03-01-preview

個々のイメージは、次の呼び出しで取得できます。

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2018-03-01-preview

### <a name="export-services"></a>サービスをエクスポートする

サービスとその ID のリストを取得するには、次の呼び出しを使用します。

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2018-03-01-preview

個々のサービスは、次の呼び出しで取得できます。

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2018-03-01-preview

### <a name="export-pipeline-experiments"></a>パイプライン実験をエクスポートする

個々の実験は、次の呼び出しで取得できます。

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Experiments/{experimentId}

### <a name="export-pipeline-graphs"></a>パイプライン グラフをエクスポートする

個々のグラフは、次の呼び出しで取得できます。

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Graphs/{graphId}

### <a name="export-pipeline-modules"></a>パイプライン モジュールをエクスポートする

モジュールは次の呼び出しで取得できます。

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Modules/{id}

### <a name="export-pipeline-templates"></a>パイプライン テンプレートをエクスポートする

テンプレートは次の呼び出しで取得できます。

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Templates/{templateId}

### <a name="export-pipeline-data-sources"></a>パイプライン データ ソースをエクスポートする

データ ソースは次の呼び出しで取得できます。

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/DataSources/{id}

## <a name="delete-visual-interface-assets"></a>ビジュアル インターフェイス アセットを削除する

実験で作成したビジュアル インターフェイスで、個々のアセットを削除します。

1. 左側で、削除するアセットの種類を選択します。

    ![アセットを削除する](media/how-to-export-delete-data.md/delete-experiment.png)

1. 一覧で、削除する個々の資産を選択します。

1. 下部にある **[削除]** を選択します。

## <a name="export-visual-interface-data"></a>ビジュアル インターフェイスをエクスポートする

実験で作成したビジュアル インターフェイスで、追加したデータをエクスポートします。

1. 左側で、 **[データ]** を選択します。

1. 上部にある **[My Datasets]\(マイ データセット\)** または **[サンプル]** を選択してエクスポートするデータを特定します。

    ![データをダウンロードする](media/how-to-export-delete-data.md/download-data.png)

1. 一覧で、エクスポートする個々のデータセットを選択します。

1. 下部にある **[ダウンロード]** を選択します。