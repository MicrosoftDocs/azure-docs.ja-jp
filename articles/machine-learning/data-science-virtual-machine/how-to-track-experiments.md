---
title: 実験の追跡とモデルのデプロイ
titleSuffix: Azure Data Science Virtual Machine
description: Azure Machine Learning や MLFlow を使用して Data Science Virtual Machine から実験を追跡し、ログに記録する方法について説明します。
services: machine-learning
ms.service: data-science-vm
author: samkemp
ms.author: samkemp
ms.topic: conceptual
ms.date: 07/17/2020
ms.openlocfilehash: 3795d531c5c4c543587ab817c05cd1cfeea6be06
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "100518540"
---
# <a name="track-experiments-and-deploy-models-in-azure-machine-learning"></a>Azure Machine Learning で実験を追跡してモデルをデプロイする

実験を追跡し、実行のメトリックを監視することでモデルの作成プロセスを強化します。 この記事では、Azure Machine Learning で [MLflow](https://mlflow.org/) API を使用してトレーニング スクリプトにログ記録コードを追加し、実験を追跡する方法について説明します。

次の図は、MLflow Tracking を使用して、実験の実行メトリックを追跡し、Azure Machine Learning ワークスペース内にモデル成果物を保存する例を示しています。

![実験を追跡する](./media/how-to-track-experiments/mlflow-diagram-track.png)

## <a name="prerequisites"></a>前提条件

* [Azure Machine Learning ワークスペースをプロビジョニングする](../how-to-manage-workspace.md#create-a-workspace)必要があります

## <a name="create-a-new-notebook"></a>新しいノートブックを作成する

Azure Machine Learning および MLFlow SDK は Data Science VM にプレインストールされており、**azureml_py36_\*** conda 環境でアクセスできます。 JupyterLab でランチャーをクリックし、次のカーネルを選択します。

![カーネルの選択](./media/how-to-track-experiments/experiment-tracking-1.png)

## <a name="set-up-the-workspace"></a>ワークスペースを設定する

[Azure portal](https://portal.azure.com) にアクセスして、前提条件の一部としてプロビジョニングしたワークスペースを選択します。 __[config.json をダウンロードする]__ が表示されます (下記参照)。構成ファイルをダウンロードし、DSVM 上の作業ディレクトリに保存されていることを確認します。

![構成ファイルを取得する](./media/how-to-track-experiments/experiment-tracking-2.png)

構成ファイルには、ワークスペース名やサブスクリプションなどの情報が含まれています。これは、これらのパラメーターをハードコーディングする必要がないことを意味します。

## <a name="track-dsvm-runs"></a>DSVM の実行を追跡する

次のコードをノートブック (またはスクリプト) に追加して、AzureML ワークスペース オブジェクトを設定します。

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
追跡 URI の有効期間は最大 1 時間以内です。 一定のアイドル時間の経過後にスクリプトを再起動した場合は、get_mlflow_tracking_uri API を使用して新しい URI を取得してください。

### <a name="load-the-data"></a>データを読み込む

この例では、糖尿病のデータセットを使用します。scikit-learn に付属する、よく知られた小規模のデータセットです。 このセルでは、データセットを読み込み、ランダムのトレーニング セットとテスト セットに分割します。

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
from sklearn.externals import joblib

X, y = load_diabetes(return_X_y = True)
columns = ['age', 'gender', 'bmi', 'bp', 's1', 's2', 's3', 's4', 's5', 's6']
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=0)
data = {
    "train":{"X": X_train, "y": y_train},
    "test":{"X": X_test, "y": y_test}
}

print ("Data contains", len(data['train']['X']), "training samples and",len(data['test']['X']), "test samples")
```

### <a name="add-tracking"></a>追跡の追加

Azure Machine Learning SDK を使用して実験の追跡を追加し、永続化されたモデルを実験の実行レコードにアップロードします。 次のコードでは、ログを記録して、実験の実行にモデル ファイルをアップロードします。 モデルは、Azure Machine Learning モデル レジストリにも登録されます。

```python
# Get an experiment object from Azure Machine Learning
from azureml.mlflow import register_model
experiment_name = 'experiment_with_mlflow'
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    # Log the algorithm parameter alpha to the run
    mlflow.log_param('alpha', 0.03)

    # Create, fit, and test the scikit-learn Ridge regression model
    regression_model = Ridge(alpha=0.03)
    regression_model.fit(data['train']['X'], data['train']['y'])
    preds = regression_model.predict(data['test']['X'])

    # Output the Mean Squared Error to the notebook and to the run
    print('Mean Squared Error is', mean_squared_error(data['test']['y'], preds))
    mlflow.log_metric('mse', mean_squared_error(data['test']['y'], preds))

    # Save the model
    model_file_name = 'model.pkl'
    joblib.dump(value = regression_model, filename = model_file_name)

    # upload the model file explicitly into artifacts
    mlflow.log_artifact(model_file_name)
    # register the model
    register_model(mlflow.active_run(), 'diabetes_model', 'model.pkl', model_framework="ScikitLearn")
```

### <a name="view-runs-in-azure-machine-learning"></a>Azure Machine Learning で実行を表示する

[Azure Machine Learning Studio](https://ml.azure.com) で実験の実行を表示できます。 左側のメニューの __[実験]__ をクリックし、[experiment_with_mlflow] を選択します (または、上記のスニペットで実験に異なる名前を指定した場合は、使用した名前をクリックします)。

![実験を選択する](./media/how-to-track-experiments/mlflow-experiments.png)

ログに記録された平均二乗誤差 (MSE) が表示されます。

![MSE](./media/how-to-track-experiments/mlflow-experiments-2.png)

実行をクリックすると、その他の詳細が表示されます。また、 __[出力とログ]__ にも pickle から出力されたモデルが表示されます。

## <a name="deploy-model-in-azure-machine-learning"></a>Azure Machine Learning にモデルをデプロイする

このセクションでは、DSVM でトレーニングされたモデルを Azure Machine Learning にデプロイする方法について説明します。

### <a name="step-1-create-inference-compute"></a>手順 1:推論コンピューティングの作成

[AzureML Studio](https://ml.azure.com) の左側のメニューで __[コンピューティング]__ 、 __[Inference clusters]\(推論クラスター\)__ タブの順にクリックします。次に、以下で説明するように __[+ 新規]__ をクリックします。

![推論コンピューティングの作成](./media/how-to-track-experiments/mlflow-experiments-6.png)

__[新しい推論クラスター]__ ウィンドウで、次の項目の詳細を入力します。

* コンピューティング名
* Kubernetes Service - [新規作成] を選択します
* リージョンを選択します
* VM サイズを選択します (このチュートリアルでは、既定の Standard_D3_v2 で十分です)
* クラスターの目的 - __[開発テスト]__ を選択します
* ノード数は __1__ にする必要があります
* ネットワーク構成 - 基本

次に、 __[作成]__ をクリックします。

![コンピューティングの詳細](./media/how-to-track-experiments/mlflow-experiments-7.png)

### <a name="step-2-deploy-no-code-inference-service"></a>手順 2:コードなしの推論サービスのデプロイ

`register_model` を使用してコードにモデルを登録したときに、sklearn としてフレームワークを指定しました。 Azure Machine Learning では、次のフレームワークに対して、コードなしのデプロイがサポートされています。

* scikit-learn
* Tensorflow SaveModel 形式
* ONNX モデル形式

コードなしのデプロイとは、特定のスコアリング スクリプトを指定しなくても、モデル成果物から直接デプロイできることを意味します。

糖尿病モデルをデプロイするには、[Azure Machine Learning Studio](https://ml.azure.com) の左側のメニューに移動し、 __[モデル]__ を選択します。 次に、登録されている diabetes_model をクリックします。

![モデルの選択](./media/how-to-track-experiments/mlflow-experiments-3.png)

次に、モデル詳細ウィンドウの __[デプロイ]__ ボタンをクリックします。

![配置](./media/how-to-track-experiments/mlflow-experiments-4.png)

手順 1 で作成した推論クラスター (Azure Kubernetes Service) にモデルをデプロイします。 サービスの名前と、手順 1 で作成した AKS コンピューティング クラスターの名前を指定して、以下の詳細を入力します。 また、 __[CPU 予約容量]__ を (0.1 から) 1 に、 __[メモリ予約容量]__ を (0.5 から) 1 に増やすことをお勧めします。この増加操作を行うには、 __[詳細設定]__ をクリックして詳細を入力します。 次に、 __[デプロイ]__ をクリックします。

![デプロイの詳細](./media/how-to-track-experiments/mlflow-experiments-5.png)

### <a name="step-3-consume"></a>手順 3:使用

モデルが正常にデプロイされると、次の情報が表示されます (このページにアクセスするには、左側のメニューの [エンドポイント]、デプロイされたサービスの名前の順にクリックします)。

![モデルの使用](./media/how-to-track-experiments/mlflow-experiments-8.png)

デプロイの状態は __[移行中]__ から __[正常]__ に変化します。 さらに、この詳細セクションには、アプリケーション開発者が ML モデルをアプリに統合するために使用できる REST エンドポイントと Swagger URL が記載されています。

[Postman](https://www.postman.com/) を使用してエンドポイントをテストできます。または、AzureML SDK を使用することもできます。

```python
from azureml.core import Webservice
import json

# if you called your service differently then change the name below
service = Webservice(ws, name="diabetes-service")

input_payload = json.dumps({
    'data': X_test[0:2].tolist(),
    'method': 'predict'  # If you have a classification model, you can get probabilities by changing this to 'predict_proba'.
})

output = service.run(input_payload)

print(output)
```

### <a name="step-4-clean-up"></a>手順 4:クリーンアップ

継続的なコンピューティング料金が発生しないように、手順 1 で作成した推論コンピューティングを削除します。 Azure Machine Learning Studio の左側のメニューで、[コンピューティング]、[推論クラスター]、[コンピューティングの選択]、[削除] の順にクリックします。

## <a name="next-steps"></a>次の手順

* [AzureML でのモデルのデプロイ](../how-to-deploy-and-where.md)の詳細を確認する
