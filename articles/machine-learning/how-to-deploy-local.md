---
title: ローカル環境で実行および展開する方法
titleSuffix: Azure Machine Learning
description: この記事では、Azure Machine Learning で作成されたモデルのトレーニング、デバッグ、またはデプロイのターゲットとしてローカル コンピューターを使用する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 11/20/2020
ms.topic: conceptual
ms.custom: how-to, deploy
ms.openlocfilehash: a7d1212d1106f0883d05a860b498b90e4e5f8e00
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102517516"
---
# <a name="deploy-models-trained-with-azure-machine-learning-on-your-local-machines"></a>Azure Machine Learning でトレーニングされたモデルをローカル コンピューターにデプロイする 

この記事では、Azure Machine Learning で作成されたモデルのトレーニングまたはデプロイのターゲットとしてローカル コンピューターを使用する方法について説明します。 Azure Machine Learning は柔軟性が高いため、ほとんどの Python 機械学習フレームワークで動作することができます。 機械学習ソリューションには、通常、複製が困難な場合がある複雑な依存関係があります。 この記事では、全体的な制御と使いやすさのバランスを取る方法について説明します。

ローカル デプロイには次のようなシナリオがあります。

* プロジェクトの早い段階での、データ、スクリプト、モデルのすばやいイテレーション。
* 後のステージでのデバッグとトラブルシューティング。
* ユーザーが管理するハードウェアへの最終的なデプロイ。

## <a name="prerequisites"></a>前提条件

- Azure Machine Learning ワークスペース。 詳細については、[Azure Machine Learning ワークスペースの作成](how-to-manage-workspace.md)に関するページをご覧ください。
- モデルと環境。 トレーニング済みのモデルがない場合、[こちらのチュートリアル](tutorial-train-models-with-aml.md)で提供されているモデルと依存関係のファイルを使用できます。
- [Azure Machine Learning SDK for Python](/python/api/overview/azure/ml/intro)。
- Azure Machine Learning パッケージの依存関係をミラー化する場合は、Anaconda や Miniconda などの Conda マネージャー。
- Azure Machine Learning 環境のコンテナー化されたバージョンを使用する場合は、Docker。

## <a name="prepare-your-local-machine"></a>ローカル コンピューターを準備する

Azure Machine Learning モデルをローカル環境で実行する最も信頼性の高い方法は、Docker イメージを使用することです。 Docker イメージを使用すると、ハードウェアの問題を除いた Azure 実行環境が複製される、分離されてコンテナー化されたエクスペリエンスが提供されます。 開発シナリオ用に Docker をインストールして構成する方法の詳細については、「[Windows での Docker リモート開発の概要](/windows/dev-environment/docker/overview)」を参照してください。

Docker で実行されているプロセスにデバッガーをアタッチできます (「[実行中のコンテナーにアタッチする](https://code.visualstudio.com/docs/remote/attach-container)」を参照)。ただし、Docker を使用せずに、Python コードのデバッグと反復処理を行うこともできます。 このシナリオでは、Azure Machine Learning で実験を実行するときに使用するのと同じライブラリをローカル コンピューターでも使用することが重要です。 Python の依存関係の管理に、Azure では [Conda](https://docs.conda.io/) を使用します。 他のパッケージ マネージャーを使用して環境を作り直すこともできますが、ローカル コンピューターに Conda をインストールして構成するのが最も簡単な同期方法です。 

## <a name="prepare-your-entry-script"></a>エントリ スクリプトを準備する

Docker を使用してモデルと依存関係を管理する場合でも、Python のスコア付けスクリプトはローカル環境に用意する必要があります。 このスクリプトには 2 つのメソッドが必要です。

- 引数を受け取らず何も返さない `init()` メソッド 
- JSON 形式の文字列を受け取り、JSON でシリアル化できるオブジェクトを返す `run()` メソッド

`run()` メソッドへの引数は次のような形式です。 

```json
{
    "data": <model-specific-data-structure>
}
```

`run()` メソッドから返すオブジェクトで、`toJSON() -> string` を実装する必要があります。

次の例では、登録された Scikit-learn モデルを読み込み、NumPy データを使用してそれをスコア付けする方法を示します。 この例は、[このチュートリアル](tutorial-train-models-with-aml.md)のモデルと依存関係に基づいています。

```python
import json
import numpy as np
import os
import pickle
import joblib

def init():
    global model
    # AZUREML_MODEL_DIR is an environment variable created during deployment.
    # It's the path to the model folder (./azureml-models/$MODEL_NAME/$VERSION).
    # For multiple models, it points to the folder containing all deployed models (./azureml-models).
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # Make prediction.
    y_hat = model.predict(data)
    # You can return any data type as long as it's JSON-serializable.
    return y_hat.tolist()
```

Swagger スキーマの自動生成とバイナリ データ (画像など) のスコア付けを含む、より高度な例については、「[高度なエントリ スクリプトの作成](how-to-deploy-advanced-entry-script.md)」を参照してください。 

## <a name="deploy-as-a-local-web-service-by-using-docker"></a>Docker を使用してローカル Web サービスとしてデプロイする

Azure Machine Learning によって使用される環境を複製する最も簡単な方法は、Docker を使用して Web サービスをデプロイすることです。 ローカル コンピューターで Docker を実行し、次のようにします。

1. モデルが登録されている Azure Machine Learning ワークスペースに接続します。
1. モデルを表す `Model` オブジェクトを作成します。
1. 依存関係が含まれ、コードを実行するソフトウェア環境が定義されている、`Environment` オブジェクトを作成します。
1. エントリ スクリプトと `Environment` を関連付ける `InferenceConfig` オブジェクトを作成します。
1. サブクラス `LocalWebserviceDeploymentConfiguration` の `DeploymentConfiguration` オブジェクトを作成します。
1. `Model.deploy()` を使用して、`Webservice` オブジェクトを作成します。 このメソッドにより、Docker イメージがダウンロードされて、`Model`、`InferenceConfig`、`DeploymentConfiguration` と関連付けられます。
1. `Webservice.wait_for_deployment()` を使用して `Webservice` をアクティブにします。

これらの手順を示すコードは次のようになります。

```python
from azureml.core.webservice import Webservice
from azure.core.model import InferenceConfig
from azureml.core.environment import Environment
from azureml.core import Workspace
from azureml.core.model import Model

ws = Workspace.from_config()
model = Model(ws, 'sklearn_mnist')


myenv = Environment.get(workspace=ws, name="tutorial-env", version="1")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

deployment_config = LocalWebservice.deploy_configuration(port=6789)

local_service = Model.deploy(workspace=ws, 
                       name='sklearn-mnist-local', 
                       models=[model], 
                       inference_config=inference_config, 
                       deployment_config = deployment_config)

local_service.wait_for_deployment(show_output=True)
print(f"Scoring URI is : {local_service.scoring_uri}")
```

`Model.deploy()` の呼び出しには数分かかることがあります。 最初に Web サービスをデプロイすると、その後の `update()` メソッドは、ゼロから始めるより効率的に使用できるようになります。 「[デプロイされた Web サービスを更新する](how-to-deploy-update-web-service.md)」を参照してください。

### <a name="test-your-local-deployment"></a>ローカルな展開をテストする

前のデプロイ スクリプトを実行すると、スコア付けのためにデータを POST できる URI が出力されます (例: `http://localhost:6789/score`)。 次のサンプルでは、ローカルにデプロイされたモデル `"sklearn-mnist-local"` を使用して、サンプル データのスコア付けを行うスクリプトを示します。 モデルが適切にトレーニングされていれば、`normalized_pixel_values` は "2" と解釈される必要があることが推測されます。 

```python
import requests

normalized_pixel_values = "[\
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 0.5, 0.7, 1.0, 1.0, 0.6, 0.4, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.7, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.9, 0.1, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.7, 1.0, 1.0, 1.0, 0.8, 0.6, 0.7, 1.0, 1.0, 0.5, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.2, 1.0, 1.0, 0.8, 0.1, 0.0, 0.0, 0.0, 0.8, 1.0, 0.5, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.3, 1.0, 0.8, 0.1, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, 0.3, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.1, 0.1, 0.0, 0.0, 0.0, 0.0, 0.8, 1.0, 1.0, 0.3, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, 0.8, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.3, 1.0, 1.0, 0.9, 0.2, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, 0.6, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.7, 1.0, 1.0, 0.6, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.1, 0.9, 1.0, 0.9, 0.1, \
0.1, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.8, 1.0, 1.0, 0.6, \
0.6, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.3, 1.0, 1.0, 0.7, \
0.7, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.1, 0.8, 1.0, 1.0, \
1.0, 0.6, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, \
1.0, 0.7, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 1.0, 1.0, \
1.0, 1.0, 0.1, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 1.0, \
1.0, 1.0, 1.0, 0.2, 0.1, 0.1, 0.1, 0.1, 0.0, 0.0, 0.0, 0.1, 0.1, 0.1, 0.6, 0.6, 0.6, 0.6, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.7, 0.6, 0.7, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.5, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.7, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.7, 0.5, 0.5, 0.2, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.5, 0.5, 0.5, 0.5, 0.7, 1.0, 1.0, 1.0, 0.6, 0.5, 0.5, 0.2, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0]"

input_data = "{\"data\": [" + normalized_pixel_values + "]}"

headers = {'Content-Type': 'application/json'}

scoring_uri = "http://localhost:6789/score"
resp = requests.post(scoring_uri, input_data, headers=headers)

print("Should be predicted as '2'")
print("prediction:", resp.text)
```

## <a name="download-and-run-your-model-directly"></a>モデルを直接ダウンロードして実行する

Docker を使用してモデルを Web サービスとしてデプロイすることは、最も一般的なオプションです。 ただし、ローカルの Python スクリプトを使用してコードを直接実行することもできます。 次の 2 つの重要なコンポーネントが必要です。 

- モデル自体
- モデルが依存している依存関係 

モデルは次の方法でダウンロードできます。  

- ポータルで **[モデル]** タブを選択し、目的のモデルを選択して、 **[詳細]** ページで **[ダウンロード]** を選択する。
- コマンドラインで `az ml model download` を使用する ([model download](/cli/azure/ext/azure-cli-ml/ml/model#ext_azure_cli_ml_az_ml_model_download) に関するセクションを参照)。
- Python SDK `Model.download()` メソッドを使用する (「[Model クラス](/python/api/azureml-core/azureml.core.model.model#download-target-dir------exist-ok-false--exists-ok-none-)」を参照)。

Azure モデルは、1 つまたは複数のシリアル化された Python オブジェクトであり、Python pickle ファイル ( .pkl 拡張子) としてパッケージ化されています。 pickle ファイルの内容は、モデルのトレーニングに使用された機械学習ライブラリまたは技法によって異なります。 たとえば、チュートリアルのモデルを使用している場合は、次のようにしてモデルを読み込むことができます。

```python
import pickle

with open('sklearn_mnist_model.pkl', 'rb') as f : 
    logistic_model = pickle.load(f, encoding='latin1')
```

依存関係を正しく把握することは常に難しく、特に機械学習では、特定のバージョンを必要とするわかりにくい複雑な関係になることがよくあります。 ローカル コンピューター上の Azure Machine Learning 環境は、完全な Conda 環境として、または `Environment` クラスの `build_local()` メソッドを使用することにより Docker イメージとして、作り直すことができます。 

```python
ws = Workspace.from_config()
myenv = Environment.get(workspace=ws, name="tutorial-env", version="1")
myenv.build_local(workspace=ws, useDocker=False) #Creates conda environment.
```

`build_local()` `useDocker` 引数を `True` に設定すると、Conda 環境ではなく Docker イメージが作成されます。 より詳細な制御が必要な場合は、`Environment` の `save_to_directory()` メソッドを使用できます。これにより、定義ファイル conda_dependencies.yml と azureml_environment.json が作成されるので、それを微調整し、拡張機能のベースとして使用できます。 

`Environment` クラスには、コンピューティング ハードウェア、Azure ワークスペース、Docker イメージの間で環境を同期するためのメソッドが他にもいくつかあります。 詳細については、「[Environment クラス](/python/api/azureml-core/azureml.core.environment(class))」を参照してください。

モデルをダウンロードし、その依存関係を解決した後は、スコア付けの実行、モデルの微調整、転移学習の使用などの方法に関して、Azure で定義されている制限はありません。 

## <a name="upload-a-retrained-model-to-azure-machine-learning"></a>再トレーニングされたモデルを Azure Machine Learning にアップロードする

ローカル環境でトレーニングまたは再トレーニングしたモデルがある場合は、Azure に登録できます。 登録が完了したら、Azure Compute を使用してそれを引き続きチューニングするか、[Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md) や [Triton Inference Server (プレビュー)](how-to-deploy-with-triton.md) などの Azure の機能を使用してデプロイすることができます。

Azure Machine Learning の Python SDK で使用するには、モデルをシリアル化された Python オブジェクトとして pickle 形式 (pkl ファイル) で格納する必要があります。 また、JSON でシリアル化可能なオブジェクトを返す `predict(data)` メソッドを実装する必要もあります。 たとえば、ローカル環境でトレーニングした Scikit-learn 糖尿病モデルを次の方法で保存できます。 

```python
import joblib

from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge

dataset_x, dataset_y = load_diabetes(return_X_y=True)

sk_model = Ridge().fit(dataset_x, dataset_y)

joblib.dump(sk_model, "sklearn_regression_model.pkl")
```

モデルを Azure で使用できるようにするには、`Model` クラスの `register()` メソッドを使用します。

```python
from azureml.core.model import Model

model = Model.register(model_path="sklearn_regression_model.pkl",
                       model_name="sklearn_regression_model",
                       tags={'area': "diabetes", 'type': "regression"},
                       description="Ridge regression model to predict diabetes",
                       workspace=ws)
```

新しく登録したモデルは、Azure Machine Learning の **[モデル]** タブで検索できます。

:::image type="content" source="media/how-to-deploy-local/registered-model.png" alt-text="アップロードしたモデルが示されている Azure Machine Learning の [モデル] タブのスクリーンショット。":::

モデルと環境のアップロードと更新の詳細については、「[高度な使用方法でモデルを登録してローカル環境にデプロイする](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/deploy-to-local/register-model-deploy-local-advanced.ipynb)」を参照してください。

## <a name="next-steps"></a>次のステップ

- 環境の管理の詳細については、「[Azure Machine Learning でソフトウェア環境を作成して使用する](how-to-use-environments.md)」を参照してください。
- データストアからデータへのアクセスの詳細については、「[Azure のストレージ サービスに接続する](how-to-access-data.md)」を参照してください。
