---
title: Azure Databricks ML 実験用の MLflow Tracking
titleSuffix: Azure Machine Learning
description: Azure Databricks Ml 実験のメトリックと成果物をログに記録するために、Azure Machine Learning で MLflow を設定します。
services: machine-learning
author: nibaccam
ms.author: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.date: 09/22/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: a894a46ce7c78b65dde80c52f9516fcbe4d27bcb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102521001"
---
# <a name="track-azure-databricks-ml-experiments-with-mlflow-and-azure-machine-learning-preview"></a>MLflow と Azure Machine Learning を使用した Azure Databricks ML 実験の追跡 (プレビュー)

この記事では、MLflow の追跡 URI とログ API (まとめて [MLflow Tracking](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api) と呼ばれる) を有効にして、Azure Databricks (ADB) 実験、MLflow、および Azure Machine Learning を接続する方法について説明します。

[MLflow](https://www.mlflow.org) は、機械学習の実験のライフ サイクルを管理するためのオープンソース ライブラリです。 MLflow Tracking は、トレーニング実行のメトリックとモデル成果物のログ記録と追跡を行う、MLflow のコンポーネントです。 [Azure Databricks と MLflow](/azure/databricks/applications/mlflow/)の詳細を参照してください。 

MLflow と Azure Machine Learning のその他の機能統合については、[MLflow と Azure Machine Learning を使用した実験実行の追跡](how-to-use-mlflow.md)に関する記事をご覧ください。

>[!NOTE]
> オープン ソース ライブラリである MLflow は頻繁に変更されます。 そのため、Azure Machine Learning と MLflow の統合によって利用できるようになる機能はプレビューとして見なす必要があり、Microsoft は完全にサポートしていません。

> [!TIP]
> このドキュメントの情報は主に、モデルのトレーニング プロセスを監視したいデータ サイエンティストや開発者を対象としています。 Azure Machine Learning からリソース使用状況やイベント (クォータ、トレーニング実行の完了、モデル デプロイの完了など) を監視することに関心がある管理者の方は、「[Azure Machine Learning の監視](monitor-azure-machine-learning.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

* `azureml-mlflow` パッケージをインストールします。 
    * このパッケージからは自動的に、MLflow がワークスペースにアクセスするための接続を提供する、[Azure Machine Learning Python SDK](/python/api/overview/azure/ml/install) の `azureml-core` が持ち込まれます。
* [Azure Databricks ワークスペースおよびクラスター](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal)。
* [Azure Machine Learning ワークスペースを作成](how-to-manage-workspace.md)します。
    * [ワークスペースで MLflow 操作を実行するために必要なアクセス許可](how-to-assign-roles.md#mlflow-operations)を確認します。

## <a name="track-azure-databricks-runs"></a>Azure Databricks の実行を追跡する

Azure Machine Learning で MLflow Tracking を使用すると、Azure Databricks の実行からログに記録されたメトリックと成果物を以下に格納できます。 

* Azure Databricks ワークスペース
* Azure Machine Learning ワークスペース

Azure Databricks ワークスペースとクラスターを作成した後、 

1. PyPi から *azureml-mlflow* ライブラリをインストールして、クラスターから必要な関数とクラスに確実にアクセスできるようにします。

1. 実験ノートブックを設定します。

1. Azure Databricks ワークスペースと Azure Machine Learning ワークスペースを接続します。

これらの手順の詳細については、以下のセクションをご覧ください。Azure Databricks で MLflow 実験を正しく実行できるようになります。 

## <a name="install-libraries"></a>ライブラリのインストール

クラスターにライブラリをインストールするには、 **[ライブラリ]** タブに移動して、 **[新規インストール]** を選択します。

 ![Azure Databricks での MLflow](./media/how-to-use-mlflow-azure-databricks/azure-databricks-cluster-libraries.png)

**[パッケージ]** フィールドに「azureml-mlflow」と入力し、次に [インストール] を選択します。 必要に応じてこの手順を繰り返して、実験のための他の追加パッケージをクラスターにインストールします。

 ![Azure DB install mlflow ライブラリ](./media/how-to-use-mlflow-azure-databricks/install-libraries.png)

## <a name="set-up-your-notebook"></a>ノートブックを設定する 

ADB クラスターが設定されたら、 
1. 左側のナビゲーション ウィンドウで、 **[ワークスペース]** を選択します。 
1. [ワークスペース] ドロップダウン メニューを展開し、 **[インポート]** を選択します。
1. 実験ノートブックをドラッグ アンド ドロップするか参照して見つけ、ADB ワークスペースをインポートします。
1. **[インポート]** を選択します。 実験ノートブックが自動的に開きます。
1. 左上にあるノートブックのタイトルの下で、実験ノートブックに接続するクラスターを選択します。 

## <a name="connect-your-azure-databricks-and-azure-machine-learning-workspaces"></a>Azure Databricks と Azure Machine Learning ワークスペースに接続する

ADB ワークスペースを Azure Machine Learning ワークスペースにリンクすると、Azure Machine Learning ワークスペースで実験データを追跡できるようになります。

ADB ワークスペースを新規または既存の Azure Machine Learning ワークスペースにリンクするには、 
1. [Azure ポータル](https://ms.portal.azure.com)にサインインします。
1. ADB ワークスペースの **[概要]** ページに移動します。
1. 右下の **[Link Azure Machine Learning workspace]\(Azure Machine Learning ワークスペースをリンク\)** ボタンを選択します。 

 ![Azure DB と Azure Machine Learning のワークスペースをリンクする](./media/how-to-use-mlflow-azure-databricks/link-workspaces.png)

## <a name="mlflow-tracking-in-your-workspaces"></a>ワークスペースでの MLflow Tracking

ワークスペースのインスタンスを作成すると、MLflow Tracking が、次のすべての場所で追跡されるように自動的に設定されます。

* リンクされた Azure Machine Learning ワークスペース。
* 元の ADB ワークスペース。 

実験はすべて、マネージド Azure Machine Learning 追跡サービスに配置されます。

次のコードを実験ノートブックに配置して、リンクされた Azure Machine Learning ワークスペースを取得する必要があります。 

このコードによって以下が行われます。 

*  Azure Machine Learning ワークスペースをインスタンス化するための Azure サブスクリプションの詳細を取得します。 

* 既存のリソース グループと Azure Machine Learning ワークスペースがあることを前提とします。そうでない場合は、[それらを作成](how-to-manage-workspace.md)できます。 

* 実験の名前を設定します。 ここで `user_name` は、Azure Databricks ワークスペースに関連付けられている `user_name` と一致します。

```python
import mlflow
import mlflow.azureml
import azureml.mlflow
import azureml.core

from azureml.core import Workspace

subscription_id = 'subscription_id'

# Azure Machine Learning resource group NOT the managed resource group
resource_group = 'resource_group_name' 

#Azure Machine Learning workspace name, NOT Azure Databricks workspace
workspace_name = 'workspace_name'  

# Instantiate Azure Machine Learning workspace
ws = Workspace.get(name=workspace_name,
                   subscription_id=subscription_id,
                   resource_group=resource_group)

#Set MLflow experiment. 
experimentName = "/Users/{user_name}/{experiment_folder}/{experiment_name}" 
mlflow.set_experiment(experimentName) 

```

### <a name="set-mlflow-tracking-to-only-track-in-your-azure-machine-learning-workspace"></a>ご利用の Azure Machine Learning ワークスペースでのみ追跡するように MLflow Tracking を設定する

追跡対象の実験を 1 か所で管理する場合は、Azure Machine Learning ワークスペースで **のみ** 追跡するように MLflow 追跡を設定できます。 

次のコードをスクリプトに含めます。

```python
uri = ws.get_mlflow_tracking_uri()
mlflow.set_tracking_uri(uri)
```

トレーニング スクリプトで、`mlflow` をインポートして MLflow ログ API を使用し、実行のメトリックのログ記録を開始します。 次の例では、エポック損失メトリックをログに記録します。 

```python
import mlflow 
mlflow.log_metric('epoch_loss', loss.item()) 
```

## <a name="register-models-with-mlflow"></a>MLflow にモデルを登録する

モデルがトレーニングされたら、`mlflow.<model_flavor>.log_model()` メソッドを使用して、モデルをログに記録し、バックエンド追跡サーバーに登録できます。 `<model_flavor>` は、モデルに関連付けられているフレームワークを参照します。 [サポートされているモデルの種類については、こちらを参照してください](https://mlflow.org/docs/latest/models.html#model-api)。 

バックエンド追跡サーバーは、既定では Azure Databricks ワークスペースです。ただし、[Azure Machine Learning ワークスペースでのみ追跡するように MLflow Tracking を設定](#set-mlflow-tracking-to-only-track-in-your-azure-machine-learning-workspace)することを選択した場合は除外されます。その場合、バックエンド追跡サーバーは Azure Machine Learning ワークスペースになります。   

* **名前付きの登録済みモデルが存在しない場合**、メソッドにより新しいモデルが登録され、バージョン 1 が作成され、MLflow オブジェクト ModelVersion が返されます。 

* **名前付きの登録済みモデルが既にある場合** は、メソッドにより新しいモデル バージョンが作成され、バージョン オブジェクトが返されます。 

```python
mlflow.spark.log_model(model, artifact_path = "model", 
                       registered_model_name = 'model_name')  

mlflow.sklearn.log_model(model, artifact_path = "model", 
                         registered_model_name = 'model_name') 
```

## <a name="create-endpoints-for-mlflow-models"></a>MLflow モデルのエンドポイントを作成する

ML モデルのエンドポイントを作成する準備ができたら、 以下としてデプロイできます。 

* 対話的なスコアリングのための Azure Machine Learning 要求 - 応答 Web サービス。 このデプロイを使用すると、Azure Machine Learning のモデル管理およびデータ誤差検出の機能を活用して、運用モデルに適用することができます。 

* MLflow モデル オブジェクト。Azure Databricks ワークスペースでのストリーミングまたはバッチ パイプラインで Python 関数または Pandas UDF として使用できます。

### <a name="deploy-models-to-azure-machine-learning-endpoints"></a>Azure Machine Learning エンドポイントにモデルをデプロイする 
[mlflow.azureml.deploy](https://www.mlflow.org/docs/latest/python_api/mlflow.azureml.html#mlflow.azureml.deploy) API を利用して、Azure Machine Learning ワークスペースにモデルをデプロイできます。 「[MLflow にモデルを登録する](#register-models-with-mlflow)」セクションで説明したように、モデルを Azure Databricks ワークスペースだけに登録した場合は、`model_name` パラメーターを指定してモデルを Azure Machine Learning ワークスペースに登録します。 

Azure Databricks の実行は、以下のエンドポイントにデプロイできます。 
* [Azure Container Instance](how-to-deploy-mlflow-models.md#deploy-to-azure-container-instance-aci)
* [Azure Kubernetes Service](how-to-deploy-mlflow-models.md#deploy-to-azure-kubernetes-service-aks)

### <a name="deploy-models-to-adb-endpoints-for-batch-scoring"></a>バッチ スコアリングのためにモデルを ADB エンドポイントにデプロイする 

バッチ スコアリング用に Azure Databricks クラスターを選択できます。 MLflow モデルが読み込まれ、新しいデータをスコアリングする Spark Pandas UDF として使用されます。 

```python
from pyspark.sql.types import ArrayType, FloatType 

model_uri = "runs:/"+last_run_id+ {model_path} 

#Create a Spark UDF for the MLFlow model 

pyfunc_udf = mlflow.pyfunc.spark_udf(spark, model_uri) 

#Load Scoring Data into Spark Dataframe 

scoreDf = spark.table({table_name}).where({required_conditions}) 


#Make Prediction 

preds = (scoreDf 

           .withColumn('target_column_name', pyfunc_udf('Input_column1', 'Input_column2', ' Input_column3', …)) 

        ) 

display(preds) 
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

ログに記録されたメトリックと成果物をワークスペースで使用する予定がない場合、現時点では、それらを個別に削除する機能は提供されていません。 代わりに、ストレージ アカウントとワークスペースを含むリソース グループを削除すれば、課金は発生しません。

1. Azure Portal で、左端にある **[リソース グループ]** を選択します。

   ![Azure portal で削除する](./media/how-to-use-mlflow-azure-databricks/delete-resources.png)

1. 作成したリソース グループを一覧から選択します。

1. **[リソース グループの削除]** を選択します。

1. リソース グループ名を入力します。 次に、 **[削除]** を選択します。

## <a name="example-notebooks"></a>サンプルの Notebook

[Azure Machine Learning ノートブックでの MLflow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/track-and-monitor-experiments/using-mlflow) は、この記事で提示した概念を示し、さらに詳しく説明します。

## <a name="next-steps"></a>次のステップ
* [MLflow モデルを Azure Web サービスとしてデプロイします](how-to-deploy-mlflow-models.md)。 
* [モデルを管理します](concept-model-management-and-deployment.md)。
* [MLflow と Azure Machine Learning を使用して実験実行を追跡します](how-to-use-mlflow.md)。 
* [Azure Databricks と MLflow](/azure/databricks/applications/mlflow/)の詳細を参照してください。