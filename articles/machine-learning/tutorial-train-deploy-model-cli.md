---
title: CLI からのモデルのトレーニングとデプロイ
titleSuffix: Azure Machine Learning
description: Azure CLI 用の機械学習拡張機能を使用して、コマンド ラインからモデルをトレーニング、登録、デプロイする方法について説明します。
ms.author: larryfr
author: Blackmist
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 03/26/2020
ms.openlocfilehash: 4028d6732220dba4848c88306a501ce22af7487a
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2020
ms.locfileid: "89144025"
---
# <a name="tutorial-train-and-deploy-a-model-from-the-cli"></a>チュートリアル:CLI からのモデルのトレーニングとデプロイ
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

このチュートリアルでは、Azure CLI 用の機械学習拡張機能を使用して、モデルのトレーニング、登録、デプロイを行います。

このチュートリアルの Python トレーニング スクリプトでは、基本的なモデルをトレーニングするために [scikit-learn](https://scikit-learn.org/) を使用します。 このチュートリアルのフォーカスは、スクリプトやモデルではなく、CLI を使用して Azure Machine Learning を操作するプロセスです。

次の操作の実行方法を確認してください。

> [!div class="checklist"]
> * 機械学習拡張機能をインストールする
> * Azure Machine Learning ワークスペースの作成
> * モデルのトレーニングに使用するコンピューティング リソースの作成
> * モデルのトレーニングに使用するデータセットを定義して登録する
> * トレーニングの実行の開始
> * モデルの登録とダウンロード
> * モデルを Web サービスとして公開する
> * Web サービスを使用してデータをスコア付けする

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning](https://aka.ms/AMLFree) を今すぐお試しください。

* ご使用の**ローカル環境**からこのドキュメントの CLI コマンドを使用するには、[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) が必要です。

    [Azure Cloud Shell](https://azure.microsoft.com//features/cloud-shell/) を使用する場合は、ブラウザーを使用してクラウド内に存在する CLI にアクセスします。

## <a name="download-the-example-project"></a>プロジェクト例のダウンロード

このチュートリアルでは、[https://github.com/microsoft/MLOps](https://github.com/microsoft/MLOps) プロジェクトをダウンロードします。 このチュートリアルの各手順では、`examples/cli-train-deploy` ディレクトリ内のファイルが使用されます。

ファイルのローカル コピーを取得するには、[.zip アーカイブをダウンロード](https://github.com/microsoft/MLOps/archive/master.zip)するか、次の Git コマンドを使用してリポジトリをクローンします。

```azurecli-interactive
git clone https://github.com/microsoft/MLOps.git
```

### <a name="training-files"></a>トレーニング ファイル

このプロジェクトの `examples/cli-train-deploy` ディレクトリには、モデルのトレーニング時に使用される以下のファイルが含まれています。

* `.azureml\mnist.runconfig`:__実行構成__ ファイル。 このファイルでは、モデルのトレーニングに必要なランタイム環境を定義します。 この例では、トレーニング環境へのモデルのトレーニングに使用されるデータもマウントします。
* `scripts\train.py`:トレーニング スクリプト。 このファイルでは、モデルをトレーニングします。
* `scripts\utils.py`:トレーニング スクリプトによって使用されるヘルパー ファイル。
* `.azureml\conda_dependencies.yml`:トレーニング スクリプトの実行に必要なソフトウェアの依存関係を定義します。
* `dataset.json`:データセットの定義。 Azure Machine Learning ワークスペースに MNIST データセットを登録するために使用します。

### <a name="deployment-files"></a>デプロイ ファイル

リポジトリには、トレーニング済みのモデルを Web サービスとしてデプロイするために使用される以下のファイルが含まれています。

* `aciDeploymentConfig.yml`:__デプロイ構成__ ファイル。 このファイルでは、モデルに必要なホスティング環境を定義します。
* `inferenceConfig.json`:__推論構成__ ファイル。 このファイルでは、モデルを使用してデータをスコア付けするためにサービスによって使用されるソフトウェア環境を定義します。
* `score.py`:受信データを受け取り、モデルを使用してスコア付けした後、応答を返す python スクリプト。
* `scoring-env.yml`:モデルと `score.py` スクリプトの実行に必要な conda の依存関係。
* `testdata.json`:デプロイ済み Web サービスのテストに使用できるデータ ファイル。

## <a name="connect-to-your-azure-subscription"></a>Azure サブスクリプションへの接続

CLI から Azure サブスクリプションを認証するには、いくつかの方法があります。 最も基本的な方法は、ブラウザーを使用して対話形式で認証することです。 対話形式で認証するには、コマンド ラインまたはターミナルを開き、次のコマンドを使用します。

```azurecli-interactive
az login
```

CLI で既定のブラウザーを開くことができる場合、開いたブラウザにサインイン ページが読み込まれます。 それ以外の場合は、ブラウザーを開き、コマンド ラインの指示に従う必要があります。 この手順では、[https://aka.ms/devicelogin](https://aka.ms/devicelogin) にアクセスして認証コードを入力する必要があります。

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

## <a name="install-the-machine-learning-extension"></a>機械学習拡張機能をインストールする

機械学習拡張機能インストールするには、次のコマンドを使用します。

```azurecli-interactive
az extension add -n azure-cli-ml
```

拡張機能が既にインストールされていることを示すメッセージが表示される場合は、次のコマンドを使用して最新バージョンに更新します。

```azurecli-interactive
az extension update -n azure-cli-ml
```

## <a name="create-a-resource-group"></a>リソース グループを作成する

リソース グループは、Azure プラットフォーム上のリソースの基本的なコンテナーです。 Azure Machine Learning を使用する場合、リソース グループには Azure Machine Learning ワークスペースが含まれます。 また、ワークスペースによって使用される他の Azure サービスも含まれます。 たとえば、クラウドベースのコンピューティング リソースを使用してモデルをトレーニングすると、そのリソースがリソース グループ内に作成されます。

__新しいリソース グループを作成__ するには、次のコマンドを使用します。 `<resource-group-name>` をこのリソース グループに使用する名前に置き換えます。 `<location>` をこのリソース グループに使用する Azure リージョンに置き換えます。

> [!TIP]
> Azure Machine Learning が使用可能なリージョンを選択する必要があります。 詳細については、「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service)」を参照してください。

```azurecli-interactive
az group create --name <resource-group-name> --location <location>
```

このコマンドからの応答は、次の JSON のようになります。

```json
{
  "id": "/subscriptions/<subscription-GUID>/resourceGroups/<resourcegroupname>",
  "location": "<location>",
  "managedBy": null,
  "name": "<resource-group-name>",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

リソース グループの操作の詳細については、「[az group](https://docs.microsoft.com//cli/azure/group?view=azure-cli-latest)」を参照してください。

## <a name="create-a-workspace"></a>ワークスペースの作成

新しいワークスペースを作成するには、次のコマンドを使用します。 `<workspace-name>` をこのワークスペースに使用する名前に置き換えます。 `<resource-group-name>` をリソース グループの名前に置き換えます。

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name>
```

このコマンドの出力は、次の JSON のようになります。

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>",
  "containerRegistry": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.containerregistry/registries/<acr-name>",
  "creationTime": "2019-08-30T20:24:19.6984254+00:00",
  "description": "",
  "friendlyName": "<workspace-name>",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

## <a name="connect-local-project-to-workspace"></a>ローカル プロジェクトをワークスペースに接続する

ターミナルまたはコマンド プロンプトで、次のコマンドを使用して、`cli-train-deploy` ディレクトリに移動してから、ワークスペースに接続します。

```azurecli-interactive
cd ~/MLOps/examples/cli-train-deploy
az ml folder attach -w <workspace-name> -g <resource-group-name>
```

このコマンドの出力は、次の JSON のようになります。

```json
{
  "Experiment name": "model-training",
  "Project path": "/home/user/MLOps/examples/cli-train-deploy",
  "Resource group": "<resource-group-name>",
  "Subscription id": "<subscription-id>",
  "Workspace name": "<workspace-name>"
}
```

このコマンドでは、ワークスペースに接続するために必要な情報を含む `.azureml/config.json` ファイルが作成されます。 このチュートリアルで使用する残りの `az ml` コマンドではこのファイルが使用されるため、ワークスペースとリソース グループをすべてのコマンドに追加する必要はありません。

## <a name="create-the-compute-target-for-training"></a>トレーニング用のコンピューティング先の作成

この例では、Azure Machine Learning コンピューティング クラスターを使用してモデルをトレーニングします。 新しいコンピューティング クラスターを作成するには、次のコマンドを使用します。

```azurecli-interactive
az ml computetarget create amlcompute -n cpu-cluster --max-nodes 4 --vm-size Standard_D2_V2
```

このコマンドの出力は、次の JSON のようになります。

```json
{
  "location": "<location>",
  "name": "cpu-cluster",
  "provisioningErrors": null,
  "provisioningState": "Succeeded"
}
```

このコマンドでは、最大 4 つのノードを使用して、`cpu-cluster` という名前の新しいコンピューティング先が作成されます。 選択した VM サイズによって、VM に GPU リソースが提供されます。 VM サイズについて詳しくは、[VM の種類とサイズ] を参照してください。

> [!IMPORTANT]
> コンピューティング先の名前 (この例では `cpu-cluster`) は重要です。これは、次のセクションで使用する `.azureml/mnist.runconfig` ファイルによって参照されます。

## <a name="define-the-dataset"></a>データセットを定義する

モデルをトレーニングする場合、データセットを使用してトレーニング データを指定できます。 CLI からデータセットを作成するには、データセット定義ファイルを指定する必要があります。 リポジトリで提供される `dataset.json` ファイルは、MNIST データを使用して新しいデータセットを作成します。 作成されるデータセットの名前は `mnist-dataset`になります。

`dataset.json` ファイルを使用してデータセットを登録するには、次のコマンドを使用します。

```azurecli-interactive
az ml dataset register -f dataset.json --skip-validation
```

このコマンドの出力は、次の JSON のようになります。

```json
{
  "definition": [
    "GetFiles"
  ],
  "registration": {
    "description": "mnist dataset",
    "id": "a13a4034-02d1-40bd-8107-b5d591a464b7",
    "name": "mnist-dataset",
    "tags": {
      "sample-tag": "mnist"
    },
    "version": 1,
    "workspace": "Workspace.create(name='myworkspace', subscription_id='mysubscriptionid', resource_group='myresourcegroup')"
  },
  "source": [
    "http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz",
    "http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz",
    "http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz",
    "http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz"
  ]
}
```

> [!IMPORTANT]
> `id` エントリの値をコピーします。これは次のセクションで使用します。

データセット用のより包括的なテンプレートを確認するには、次のコマンドを使用します。

```azurecli-interactive
az ml dataset register --show-template
```

## <a name="reference-the-dataset"></a>データセットを参照する

データセットをトレーニング環境で使用できるようにするには、runconfig ファイルからそのデータセットを参照する必要があります。 `.azureml/mnist.runconfig` ファイルには、次の YAML エントリが含まれています。

```yaml
# The arguments to the script file.
arguments:
- --data-folder
- DatasetConsumptionConfig:mnist

.....

# The configuration details for data.
data:
  mnist:
# Data Location
    dataLocation:
# the Dataset used for this run.
      dataset:
# Id of the dataset.
        id: a13a4034-02d1-40bd-8107-b5d591a464b7
# the DataPath used for this run.
      datapath:
# Whether to create new folder.
    createOutputDirectories: false
# The mode to handle
    mechanism: mount
# Point where the data is download or mount or upload.
    environmentVariableName: mnist
# relative path where the data is download or mount or upload.
    pathOnCompute:
# Whether to overwrite the data if existing.
    overwrite: false
```

`id` エントリの値を、データセットを登録したときに返された値と一致するように変更します。 この値は、トレーニング中にデータをコンピューティング先に読み込むために使用されます。

この YAML では、トレーニング中に次のアクションが行われます。

* トレーニング環境で (データセットの ID に基づいて) データセットをマウントし、マウント ポイントへのパスを `mnist` 環境変数に格納します。
* `--data-folder` 引数を使用して、トレーニング環境内のデータの場所 (マウント ポイント) をスクリプトに渡します。

runconfig ファイルには、トレーニングの実行で使用される環境を構成するために使用される情報も含まれています。 このファイルを調べると、先ほど作成した `cpu-compute` コンピューティング先が参照されていることがわかります。 また、トレーニング時に使用するノードの数 (`"nodeCount": "4"`) が一覧表示され、トレーニング スクリプトを実行するために必要な Python パッケージを一覧にした `"condaDependencies"` セクションが含まれています。

> [!TIP]
> runconfig ファイルは手動で作成することもできますが、この例のファイルは、リポジトリに含まれている `generate-runconfig.py` ファイルを使用して作成されたものです。 このファイルは、登録済みデータセットへの参照を取得し、プログラムによって実行構成を作成して、ファイルに保存します。

実行構成ファイルについて詳しくは、[モデル トレーニング用のコンピューティング ターゲットの使用](how-to-set-up-training-targets.md#whats-a-run-configuration)に関するページを参照してください。 完全な JSON リファレンスについては、[runconfigschema.json](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json) を参照してください。

## <a name="submit-the-training-run"></a>トレーニングの実行の送信

`cpu-cluster` コンピューティング先でトレーニングの実行を開始するには、次のコマンドを使用します。

```azurecli-interactive
az ml run submit-script -c mnist -e myexperiment --source-directory scripts -t runoutput.json
```

このコマンドでは、実験の名前 (`myexperiment`) が指定されます。 実験では、この実行に関する情報がワークスペースに格納されます。

`-c mnist` パラメーターでは、`.azureml/mnist.runconfig` ファイルを指定します。

`-t` パラメーターでは、この実行への参照を JSON ファイルに格納します。このパラメーターは、次の手順でモデルを登録およびダウンロードするために使用されます。

トレーニングの実行プロセスでは、リモート コンピューティング リソース上のトレーニング セッションから情報をストリーム配信します。 情報の一部は次のテキストのようになります。

```output
Predict the test set
Accuracy is 0.9185
```

このテキストは、トレーニング スクリプトからログに記録され、モデルの精度を表示します。 他のモデルのパフォーマンス メトリックは異なります。

トレーニング スクリプトを調べると、トレーニング済みのモデルを `outputs/sklearn_mnist_model.pkl` に保存するときにアルファ値も使用されていることがわかります。

モデルは、トレーニングされたコンピューティング先にある `./outputs` ディレクトリに保存されました。 この例では、Azure クラウド内の Azure Machine Learning コンピューティング インスタンスです。 トレーニング プロセスでは、トレーニングが発生したコンピューティング先から Azure Machine Learning ワークスペースに `./outputs` ディレクトリの内容が自動的にアップロードされます。 これは、実験 (この例では `myexperiment`) の一部として保存されます。

## <a name="register-the-model"></a>モデルを登録する

実験で保存されているバージョンからモデルを直接登録するには、次のコマンドを使用します。

```azurecli-interactive
az ml model register -n mymodel -f runoutput.json --asset-path "outputs/sklearn_mnist_model.pkl" -t registeredmodel.json
```

このコマンドでは、トレーニングの実行によって作成された `outputs/sklearn_mnist_model.pkl` ファイルが `mymodel` という名前の新しいモデル登録として登録されます。 `--assets-path` では実験内のパスが参照されます。 この場合、実験と実行の情報は、トレーニング コマンドによって作成された `runoutput.json` ファイルから読み込まれます。 `-t registeredmodel.json` では、このコマンドによって作成された新しい登録済みモデルを参照する JSON ファイルが作成され、登録されているモデルと連携する他の CLI コマンドによって使用されます。

このコマンドの出力は、次の JSON のようになります。

```json
{
  "createdTime": "2019-09-19T15:25:32.411572+00:00",
  "description": "",
  "experimentName": "myexperiment",
  "framework": "Custom",
  "frameworkVersion": null,
  "id": "mymodel:1",
  "name": "mymodel",
  "properties": "",
  "runId": "myexperiment_1568906070_5874522d",
  "tags": "",
  "version": 1
}
```

### <a name="model-versioning"></a>モデルのバージョン管理

モデルに対して返されたバージョン番号をメモしておきます。 バージョンは、この名前を使用して新しいモデルを登録するたびに増やされます。 たとえば、次のコマンドを使用して、モデルをダウンロードし、ローカル ファイルから登録できます。

```azurecli-interactive
az ml model download -i "mymodel:1" -t .
az ml model register -n mymodel -p "sklearn_mnist_model.pkl"
```

最初のコマンドでは、登録されているモデルを現在のディレクトリにダウンロードします。 ファイル名は `sklearn_mnist_model.pkl` です。これは、モデルを登録したときに参照されたファイルです。 2 番目のコマンドでは、前の登録 (`mymodel`) と同じ名前のローカル モデル (`-p "sklearn_mnist_model.pkl"`) を登録します。 今回は、返される JSON データによってバージョンが 2 と表示されます。

## <a name="deploy-the-model"></a>モデルをデプロイする

モデルをデプロイするには、次のコマンドを使用します。

```azurecli-interactive
az ml model deploy -n myservice -m "mymodel:1" --ic inferenceConfig.json --dc aciDeploymentConfig.yml
```

> [!NOTE]
> 「LocalWebservice の存在を確認できませんでした」または「Docker クライアントの作成に失敗しました」という警告が表示される場合があります。 ローカル Web サービスをデプロイしていないので、この問題は無視してかまいません。

このコマンドでは、以前に登録したモデルのバージョン 1 を使用して、`myservice` という名前の新しいサービスをデプロイします。

`inferenceConfig.yml` ファイルでは、推論にモデルを使用する方法に関する情報が提供されます。 たとえば、これはエントリ スクリプト (`score.py`) とソフトウェアの依存関係を参照します。

このファイルの構造について詳しくは、「[推論構成スキーマ](reference-azure-machine-learning-cli.md#inference-configuration-schema)」を参照してください。 エントリ スクリプトの詳細については、「[Azure Machine Learning を使用してモデルをデプロイする](how-to-deploy-and-where.md)」を参照してください。

`aciDeploymentConfig.yml` は、サービスをホストするために使用されるデプロイ環境を示します。 デプロイ構成は、デプロイに使用するコンピューティング先に固有です。 この例では、Azure Container インスタンスが使用されます。 詳しくは、「[デプロイ構成スキーマ](reference-azure-machine-learning-cli.md#deployment-configuration-schema)」を参照してください。

デプロイ プロセスが完了するまで数分かかります。

> [!TIP]
> この例では、Azure Container Instances が使用されます。 ACI へのデプロイによって、必要な ACI リソースが自動的に作成されます。 代わりに Azure Kubernetes Service にデプロイする場合は、AKS クラスターを事前に作成し、`az ml model deploy` コマンドの一部として指定する必要があります。 AKS へのデプロイの例については、「[Azure Kubernetes Service クラスターにモデルをデプロイする](how-to-deploy-azure-kubernetes-service.md)」を参照してください。

数分後に、次の JSON のような情報が返されます。

```json
ACI service creation operation finished, operation "Succeeded"
{
  "computeType": "ACI",
  {...ommitted for space...}
  "runtimeType": null,
  "scoringUri": "http://6c061467-4e44-4f05-9db5-9f9a22ef7a5d.eastus2.azurecontainer.io/score",
  "state": "Healthy",
  "tags": "",
  "updatedAt": "2019-09-19T18:22:32.227401+00:00"
}
```

### <a name="the-scoring-uri"></a>スコアリング URI

デプロイから返される `scoringUri` は、Web サービスとしてデプロイされるモデルの REST エンドポイントです。 この URI は、次のコマンドを使用して取得できます。

```azurecli-interactive
az ml service show -n myservice
```

このコマンドでは、`scoringUri` を含む同じ JSON ドキュメントが返されます。

REST エンドポイントを使用して、サービスにデータを送信できます。 サービスにデータを送信するクライアント アプリケーションの作成について詳しくは、「[Web サービスとしてデプロイされた Azure Machine Learning モデルを使用する](how-to-consume-web-service.md)」を参照してください

### <a name="send-data-to-the-service"></a>データをサービスに送信する

エンドポイントを呼び出すクライアント アプリケーションを作成できますが、機械学習 CLI にはテスト クライアントとして機能するユーティリティが用意されています。 次のコマンドを使用して、`testdata.json` ファイルのデータをサービスに送信します。

```azurecli-interactive
az ml service run -n myservice -d @testdata.json
```

> [!TIP]
> PowerShell を使用している場合は、代わりに次のコマンドを使用します。
>
> ```azurecli-interactive
> az ml service run -n myservice -d `@testdata.json
> ```

コマンドからの応答は `[ 3 ]` のようになります。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

> [!IMPORTANT]
> 作成したリソースは、Azure Machine Learning に関連したその他のチュートリアルおよびハウツー記事の前提条件として使用できます。

### <a name="delete-deployed-service"></a>デプロイしたサービスを削除する

Azure Machine Learning ワークスペースの使用を継続する予定で、デプロイされたサービスを削除してコストを削減する場合は、次のコマンドを使用します。

```azurecli-interactive
az ml service delete -n myservice
```

このコマンドでは、削除されたサービスの名前を含む JSON ドキュメントが返されます。 サービスが削除されるまで数分かかる場合があります。

### <a name="delete-the-training-compute"></a>トレーニング コンピューティングを削除する

Azure Machine Learning ワークスペースの使用を継続する予定で、トレーニング用に作成された `cpu-cluster` コンピューティング先を削除する場合は、次のコマンドを使用します。

```azurecli-interactive
az ml computetarget delete -n cpu-cluster
```

このコマンドでは、削除されたコンピューティング先の ID を含む JSON ドキュメントが返されます。 コンピューティング先が削除されるまで数分かかる場合があります。

### <a name="delete-everything"></a>すべてを削除する

作成したリソースを今後使用する予定がない場合は、追加の課金が発生しないように削除します。

リソース グループと、このドキュメントで作成されたすべての Azure リソースを削除するには、次のコマンドを使用します。 `<resource-group-name>` は、前に作成したリソース グループの名前に置き換えます。

```azurecli-interactive
az group delete -g <resource-group-name> -y
```

## <a name="next-steps"></a>次のステップ

この Azure Machine Learning のチュートリアルでは、機械学習 CLI を使用して次の作業を行いました。

> [!div class="checklist"]
> * 機械学習拡張機能をインストールする
> * Azure Machine Learning ワークスペースの作成
> * モデルのトレーニングに使用するコンピューティング リソースの作成
> * モデルのトレーニングに使用するデータセットを定義して登録する
> * トレーニングの実行の開始
> * モデルの登録とダウンロード
> * モデルを Web サービスとして公開する
> * Web サービスを使用してデータをスコア付けする

CLI の使用について詳しくは、「[Azure Machine Learning 用の CLI 拡張機能を使用する](reference-azure-machine-learning-cli.md)」を参照してください。
