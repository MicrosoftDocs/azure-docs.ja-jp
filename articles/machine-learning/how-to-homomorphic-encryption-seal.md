---
title: 暗号化された推論サービスをデプロイする (プレビュー)
titleSuffix: Azure Machine Learning
description: Microsoft SEAL を使用して、イメージ分類のための暗号化された予測サービスをデプロイする方法について説明します。
author: luisquintanilla
ms.author: luquinta
ms.date: 07/09/2020
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-python, deploy, responsible-ml
ms.openlocfilehash: a2c8b765f109408b8cb08f78a4b3ad2283f35eb9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98223586"
---
# <a name="how-to-deploy-an-encrypted-inferencing-web-service-preview"></a>暗号化された推論 Web サービスをデプロイする方法 (プレビュー)

[Azure Container Instances](../container-instances/index.yml) (ACI) で、イメージ分類モデルを暗号化された推論 Web サービスとしてデプロイする方法について説明します。 この Web サービスは、モデルとスコアリング ロジックを含む Docker コンテナー イメージです。

このガイドでは、Azure Machine Learning service を使って次の作業を行います。

> [!div class="checklist"]
> * 環境を構成する
> * 暗号化された推論 Web サービスをデプロイする
> * テスト データを準備する
> * 暗号化された予測を作成する
> * リソースをクリーンアップする

ACI は、モデル デプロイ ワークフローをテストして理解するための優れたソリューションです。 スケーラブルな運用環境のデプロイの場合は、Azure Kubernetes Service の使用を検討してください。 詳細については、[デプロイする方法と場所](./how-to-deploy-and-where.md)に関するページを参照してください。

このサンプルで使用する暗号化方法は、[準同型暗号](https://github.com/Microsoft/SEAL#homomorphic-encryption)です。 準同型暗号では、暗号化されたデータで計算を行う際に秘密 (復号化) キーを利用する必要がありません。 計算の結果は暗号化され、秘密キーの所有者だけに開示されます。 

## <a name="prerequisites"></a>前提条件

このガイドでは、Azure Machine Learning に登録されたイメージ分類モデルがあることを前提としています。 これがない場合は、[事前トレーニングされたモデル](https://github.com/Azure/MachineLearningNotebooks/raw/master/tutorials/image-classification-mnist-data/sklearn_mnist_model.pkl)を使用してモデルを登録するか、[Azure Machine Learning チュートリアルでイメージ分類モデルのトレーニング](tutorial-train-models-with-aml.md)を実行して独自のモデルを作成します。

## <a name="configure-local-environment"></a>ローカル環境を構成する

Jupyter Notebook で次の手順を実行します。

1. このサンプルに必要な Python パッケージをインポートします。

    ```python
    %matplotlib inline
    import numpy as np
    import matplotlib.pyplot as plt

    import azureml.core

    # display the core SDK version number
    print("Azure ML SDK Version: ", azureml.core.VERSION)
    ```

2. 確実な推論を行うために準同型暗号ライブラリをインストールします。

    > [!NOTE]
    > `encrypted-inference` パッケージは現在プレビュー段階にあります。

    [`encrypted-inference`](https://pypi.org/project/encrypted-inference) は、[Microsoft SEAL](https://github.com/Microsoft/SEAL) に基づく暗号化された推論のためのバインドを含んだライブラリです。

    ```python
    !pip install encrypted-inference==0.9
    ```

## <a name="configure-the-inferencing-environment"></a>推論環境を構成する

推論の環境を作成し、`encrypted-inference` パッケージを Conda 依存関係として追加します。

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

# to install required packages
env = Environment('tutorial-env')
cd = CondaDependencies.create(pip_packages=['azureml-dataprep[pandas,fuse]>=1.1.14', 'azureml-defaults', 'azure-storage-blob', 'encrypted-inference==0.9'], conda_packages = ['scikit-learn==0.22.1'])

env.python.conda_dependencies = cd

# Register environment to re-use later
env.register(workspace = ws)
```

## <a name="deploy-encrypted-inferencing-web-service"></a>暗号化された推論 Web サービスをデプロイする

ACI でホストされた Web サービスとしてモデルをデプロイします。

ACI に適した環境を構築するには、以下を用意します。

* モデルの使用方法を示すスコアリング スクリプト
* ACI を構築する構成ファイル
* トレーニング済みのモデル

### <a name="create-scoring-script"></a>スコアリング スクリプトを作成する

Web サービスで推論に使用されるスコアリング スクリプト `score.py` を作成します。

スコアリング スクリプトには、2 つの必要な関数を含める必要があります。

* `init()` 関数。通常、グローバル オブジェクトにモデルを読み込みます。 この関数は、Docker コンテナーを開始するときに 1 回だけ実行されます。
* `run(input_data)` 関数。モデルを使用して、入力データに基づく値を予測します。 実行に対する入力と出力は、通常、JSON を使用してシリアル化およびシリアル化解除が実行されますが、その他の形式もサポートされています。 この関数は、サービスの呼び出し元によってアップロードされた準同型暗号ベースの公開キーをフェッチします。

```python
%%writefile score.py
import json
import os
import pickle
import joblib
from azure.storage.blob import BlobServiceClient, BlobClient, ContainerClient, PublicAccess
from encrypted.inference.eiserver import EIServer

def init():
    global model
    # AZUREML_MODEL_DIR is an environment variable created during deployment.
    # It is the path to the model folder (./azureml-models/$MODEL_NAME/$VERSION)
    # For multiple models, it points to the folder containing all deployed models (./azureml-models)
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

    global server
    server = EIServer(model.coef_, model.intercept_, verbose=True)

def run(raw_data):

    json_properties = json.loads(raw_data)

    key_id = json_properties['key_id']
    conn_str = json_properties['conn_str']
    container = json_properties['container']
    data = json_properties['data']

    # download the public keys from blob storage
    blob_service_client = BlobServiceClient.from_connection_string(conn_str=conn_str)
    blob_client = blob_service_client.get_blob_client(container=container, blob=key_id)
    public_keys = blob_client.download_blob().readall()

    result = {}
    # make prediction
    result = server.predict(data, public_keys)

    # you can return any data type as long as it is JSON-serializable
    return result
```

### <a name="create-configuration-file"></a>構成ファイルの作成

デプロイの構成ファイルを作成し、ACI コンテナーに必要な CPU 数と RAM ギガバイト数を指定します。 実際のモデルにもよりますが、通常、多くのモデルには既定値の 1 コアと 1 ギガバイトの RAM で十分です。 後でもっと必要になった場合は、イメージを再作成し、サービスをデプロイし直す必要があります。

```python
from azureml.core.webservice import AciWebservice

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1, 
                                               memory_gb=1, 
                                               tags={"data": "MNIST",  "method" : "sklearn"}, 
                                               description='Encrypted Predict MNIST with sklearn + SEAL')
```

### <a name="deploy-to-azure-container-instances"></a>Azure Container Instances へのデプロイ

推定所要時間: **約 2-5 分**

イメージを構成してデプロイします。 以下のコードでは、次の手順が実行されます。

1. 環境ファイル (`myenv.yml`) を使用して、モデルに必要な依存関係を含んだ環境オブジェクトを作成します。
1. Web サービスとしてモデルをデプロイするために必要な推論構成を作成します。次の情報を使用します。
   * スコアリング ファイル (`score.py`)
   * 前の手順で作成した環境オブジェクト
1. ACI コンテナーにモデルをデプロイします。
1. Web サービス HTTP エンドポイントを取得します。

```python
%%time
from azureml.core.webservice import Webservice
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment
from azureml.core import Workspace
from azureml.core.model import Model

ws = Workspace.from_config()
model = Model(ws, 'sklearn_mnist')

myenv = Environment.get(workspace=ws, name="tutorial-env")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

service = Model.deploy(workspace=ws,
                       name='sklearn-encrypted-mnist-svc',
                       models=[model],
                       inference_config=inference_config,
                       deployment_config=aciconfig)

service.wait_for_deployment(show_output=True)
```

スコアリング Web サービスの HTTP エンドポイントを取得します。このエンドポイントで REST クライアントの呼び出しを受け取ります。 このエンドポイントは、Web サービスをテストしたい、またはアプリケーションに統合したい任意のユーザーと共有できます。

```python
print(service.scoring_uri)
```

## <a name="prepare-test-data"></a>テスト データを準備する

1. テスト データをダウンロードします。 ここでは、このデータを *data* というディレクトリに保存します。

    ```python
    import os
    from azureml.core import Dataset
    from azureml.opendatasets import MNIST
    
    data_folder = os.path.join(os.getcwd(), 'data')
    os.makedirs(data_folder, exist_ok=True)
    
    mnist_file_dataset = MNIST.get_file_dataset()
    mnist_file_dataset.download(data_folder, overwrite=True)
    ```

1. テスト データを *data* ディレクトリから読み込みます。

    ```python
    from utils import load_data
    import os
    import glob
    
    data_folder = os.path.join(os.getcwd(), 'data')
    # note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the neural network converge faster
    X_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-images-idx3-ubyte.gz"), recursive=True)[0], False) / 255.0
    y_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-labels-idx1-ubyte.gz"), recursive=True)[0], True).reshape(-1)
    ```

## <a name="make-encrypted-predictions"></a>暗号化された予測を作成する

モデルでテスト データセットを使って予測を取得します。

暗号化された予測を作成するには、次の手順を実行します。

1. 新しい `EILinearRegressionClient`、準同型暗号ベースのクライアント、および公開キーを作成します。

    ```python
    from encrypted.inference.eiclient import EILinearRegressionClient

    # Create a new Encrypted inference client and a new secret key.
    edp = EILinearRegressionClient(verbose=True)

    public_keys_blob, public_keys_data = edp.get_public_keys()
    ```

1. 準同型暗号で生成された公開キーを、ワークスペースの既定の BLOB ストアにアップロードします。 これにより、キーを推論サーバーと共有できるようになります。

    ```python
    import azureml.core
    from azureml.core import Workspace, Datastore
    import os

    ws = Workspace.from_config()

    datastore = ws.get_default_datastore()
    container_name=datastore.container_name

    # Create a local file and write the keys to it
    public_keys = open(public_keys_blob, "wb")
    public_keys.write(public_keys_data)
    public_keys.close()

    # Upload the file to blob store
    datastore.upload_files([public_keys_blob])

    # Delete the local file
    os.remove(public_keys_blob)
    ```

1. テスト データを暗号化します。

    ```python
    #choose any one sample from the test data 
    sample_index = 1

    #encrypt the data
    raw_data = edp.encrypt(X_test[sample_index])

    ```

1. SDK の `run` API を使用してサービスを呼び出し、テスト データセットをモデルに提供して予測を取得します。 公開キーがアップロードされた BLOB ストレージに接続文字列を送信する必要があります。

    ```python
    import json
    from azureml.core import Webservice

    service = Webservice(ws, 'sklearn-encrypted-mnist-svc')

    #pass the connection string for blob storage to give the server access to the uploaded public keys 
    conn_str_template = 'DefaultEndpointsProtocol={};AccountName={};AccountKey={};EndpointSuffix=core.windows.net'
    conn_str = conn_str_template.format(datastore.protocol, datastore.account_name, datastore.account_key)

    #build the json 
    data = json.dumps({"data": raw_data, "key_id" : public_keys_blob, "conn_str" : conn_str, "container" : container_name })
    data = bytes(data, encoding='ASCII')

    print ('Making an encrypted inference web service call ')
    eresult = service.run(input_data=data)

    print ('Received encrypted inference results')
    ```

1. クライアントを使用して結果を復号化します。

    ```python
    import numpy as np

    results = edp.decrypt(eresult)

    print ('Decrypted the results ', results)

    #Apply argmax to identify the prediction result
    prediction = np.argmax(results)

    print ( ' Prediction : ', prediction)
    print ( ' Actual Label : ', y_test[sample_index])
    ```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このサンプルで作成した Web サービスを削除します。

```python
service.delete()
```

作成した Azure リソースは、今後使用する予定がなければ削除します。 これにより、稼働したまま使用されていないリソースに課金されることを防止できます。 詳細については、[リソースをクリーンアップする](how-to-manage-workspace.md#clean-up-resources)方法に関するガイドを参照してください。