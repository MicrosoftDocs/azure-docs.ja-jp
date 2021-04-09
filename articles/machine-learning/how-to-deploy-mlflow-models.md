---
title: MLflow モデルを Web サービスとしてデプロイする
titleSuffix: Azure Machine Learning
description: Azure Machine Learning で MLflow を設定して、ML モデルを Azure Web サービスとしてデプロイします。
services: machine-learning
author: shivp950
ms.author: shipatel
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.date: 12/23/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: c45b819f9fc02fae40c2bf7fc5c2247c8c0a6147
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102517482"
---
# <a name="deploy-mlflow-models-as-azure-web-services-preview"></a>MLflow モデルを Azure Web サービスとしてデプロイする (プレビュー)

この記事では、[MLflow](https://www.mlflow.org) モデルを Azure Web サービスとしてデプロイする方法について説明します。これにより、Azure Machine Learning のモデル管理とデータ ドリフト検出の機能を活用し、実稼働モデルに適用できるようになります。

Azure Machine Learning では、次のデプロイ構成が提供されます。
* Azure コンテナー インスタンス (ACI)。これは、迅速な開発/テスト デプロイに適した選択肢です。
* Azure Kubernetes Service (AKS)。これは、スケーラブルな運用環境デプロイに推奨されます。
> [!TIP]
> このドキュメントの情報は、MLflow モデルを Azure Machine Learning Web サービス エンドポイントにデプロイするデータ サイエンティストと開発者を主に対象としています。 Azure Machine Learning からリソース使用状況やイベント (クォータ、トレーニング実行の完了、モデル デプロイの完了など) を監視することに関心がある管理者の方は、「[Azure Machine Learning の監視](monitor-azure-machine-learning.md)」を参照してください。
## <a name="mlflow-with-azure-machine-learning-deployment"></a>Azure Machine Learning デプロイでの MLflow

MLflow は、機械学習の実験のライフ サイクルを管理するためのオープンソース ライブラリです。 Azure Machine Learning との統合により、モデルのトレーニングを超えて、実稼働モデルのデプロイ フェーズまでこの管理を拡張できます。

次の図は、MLflow デプロイ API と Azure Machine Learning を使用して、一般的なフレームワーク (PyTorch、Tensorflow、scikit-learn など) で作成されたモデルを Azure Web サービスとしてデプロイし、ワークスペース内で管理できることを示しています。 

![ Azure Machine Learning を使用して MLflow モデルをデプロイする](./media/how-to-deploy-mlflow-models/mlflow-diagram-deploy.png)


>[!NOTE]
> オープン ソース ライブラリである MLflow は頻繁に変更されます。 そのため、Azure Machine Learning と MLflow の統合によって利用できるようになる機能はプレビューとして見なす必要があり、Microsoft は完全にサポートしていません。

## <a name="prerequisites"></a>前提条件

* 機械学習モデル。 トレーニング済みのモデルがない場合は、[こちらのリポジトリ](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/using-mlflow)で実際のコンピューティング シナリオに最適なノートブックの例を見つけて、その手順に従います。 
* [Azure Machine Learning に接続するための MLflow 追跡 URI を設定します](how-to-use-mlflow.md#track-local-runs)。
* `azureml-mlflow` パッケージをインストールします。 
    * このパッケージからは自動的に、MLflow がワークスペースにアクセスするための接続を提供する、[Azure Machine Learning Python SDK](/python/api/overview/azure/ml/install) の `azureml-core` が持ち込まれます。
* [ワークスペースで MLflow 操作を実行するために必要なアクセス許可](how-to-assign-roles.md#mlflow-operations)を確認します。 

## <a name="deploy-to-azure-container-instance-aci"></a>Azure コンテナー インスタンス (ACI) にデプロイする

MLflow モデルを Azure Machine Learning Web サービスにデプロイするには、[Azure Machine Learning に接続するための MLflow 追跡 URI](how-to-use-mlflow.md) を使用してモデルを設定する必要があります。 

[deploy_configuration()](/python/api/azureml-core/azureml.core.webservice.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) メソッドを使用してデプロイ構成を設定します。 また、Web サービスの追跡に役立つタグや説明を追加することもできます。

```python
from azureml.core.webservice import AciWebservice, Webservice

# Set the model path to the model folder created by your run
model_path = "model"

# Configure 
aci_config = AciWebservice.deploy_configuration(cpu_cores=1, 
                                                memory_gb=1, 
                                                tags={'method' : 'sklearn'}, 
                                                description='Diabetes model',
                                                location='eastus2')
```

次に、Azure Machine Learning 用の MLflow の [deploy](https://www.mlflow.org/docs/latest/python_api/mlflow.azureml.html#mlflow.azureml.deploy) メソッドを使用して、1 つの手順でモデルを登録し、デプロイします。 

```python
(webservice,model) = mlflow.azureml.deploy( model_uri='runs:/{}/{}'.format(run.id, model_path),
                      workspace=ws,
                      model_name='sklearn-model', 
                      service_name='diabetes-model-1', 
                      deployment_config=aci_config, 
                      tags=None, mlflow_home=None, synchronous=True)

webservice.wait_for_deployment(show_output=True)
```

## <a name="deploy-to-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) へのデプロイ

MLflow モデルを Azure Machine Learning Web サービスにデプロイするには、[Azure Machine Learning に接続するための MLflow 追跡 URI](how-to-use-mlflow.md) を使用してモデルを設定する必要があります。 

AKS にデプロイするには、まず AKS クラスターを作成します。 [ComputeTarget.create()](/python/api/azureml-core/azureml.core.computetarget#create-workspace--name--provisioning-configuration-) メソッドを使用して、AKS クラスターを作成します。 新しいクラスターの作成には 20 分から 25 分かかる場合があります。

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (can also provide parameters to customize)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'aks-mlflow'

# Create the cluster
aks_target = ComputeTarget.create(workspace=ws, 
                                  name=aks_name, 
                                  provisioning_configuration=prov_config)

aks_target.wait_for_completion(show_output = True)

print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```
[deploy_configuration()](/python/api/azureml-core/azureml.core.webservice.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) メソッドを使用してデプロイ構成を設定します。 また、Web サービスの追跡に役立つタグや説明を追加することもできます。

```python
from azureml.core.webservice import Webservice, AksWebservice

# Set the web service configuration (using default here with app insights)
aks_config = AksWebservice.deploy_configuration(enable_app_insights=True, compute_target_name='aks-mlflow')

```

次に、Azure Machine Learning 用の MLflow の [deploy](https://www.mlflow.org/docs/latest/python_api/mlflow.azureml.html#mlflow.azureml.deploy) メソッドを使用して、1 つの手順でモデルを登録し、デプロイします。 

```python

# Webservice creation using single command
from azureml.core.webservice import AksWebservice, Webservice

# set the model path 
model_path = "model"

(webservice, model) = mlflow.azureml.deploy( model_uri='runs:/{}/{}'.format(run.id, model_path),
                      workspace=ws,
                      model_name='sklearn-model', 
                      service_name='my-aks', 
                      deployment_config=aks_config, 
                      tags=None, mlflow_home=None, synchronous=True)


webservice.wait_for_deployment()
```

サービスのデプロイには数分かかることがあります。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

デプロイした Web サービスを使用する予定がない場合は、`service.delete()` を使用してノートブックから削除します。  詳細については、[WebService.delete()](/python/api/azureml-core/azureml.core.webservice%28class%29#delete--) に関するドキュメントをご覧ください。

## <a name="example-notebooks"></a>サンプルの Notebook

[Azure Machine Learning ノートブックでの MLflow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/using-mlflow) は、この記事で提示した概念を示し、さらに詳しく説明します。

> [!NOTE]
> mlflow を使用したコミュニティ主導の例のリポジトリについては、 https://github.com/Azure/azureml-examples を参照してください。

## <a name="next-steps"></a>次のステップ

* [モデルを管理します](concept-model-management-and-deployment.md)。
* [データの誤差](./how-to-enable-data-collection.md)について実稼働モデルを監視します。
* [MLflow を使用して Azure Databricks 実行を追跡する](how-to-use-mlflow-azure-databricks.md)。

