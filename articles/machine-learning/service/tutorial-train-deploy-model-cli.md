---
title: CLI からのモデルのトレーニングとデプロイ
titleSuffix: Azure Machine Learning service
description: Azure CLI 用の機械学習拡張機能を使用して、コマンド ラインからモデルをトレーニング、登録、デプロイする方法について説明します。
ms.author: larryfr
author: Blackmist
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 09/12/2019
ms.openlocfilehash: fb46aaf04535c1b44cdd80810fbb6382dc727a67
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350427"
---
# <a name="tutorial-train-and-deploy-a-model-from-the-cli"></a>チュートリアル:CLI からのモデルのトレーニングとデプロイ

このチュートリアルでは、Azure CLI 用の機械学習拡張機能を使用して、モデルのトレーニング、登録、デプロイを行います。

このチュートリアルの Python トレーニング スクリプトでは、基本的なモデルをトレーニングするために [scikit-learn](https://scikit-learn.org/) を使用します。 このチュートリアルのフォーカスは、スクリプトやモデルではなく、CLI を使用して Azure Machine Learning を操作するプロセスです。

次の操作の実行方法を確認してください。

> [!div class="checklist"]
> * 機械学習拡張機能をインストールする
> * Azure Machine Learning ワークスペースの作成
> * モデルのトレーニングに使用するコンピューティング リソースの作成
> * トレーニングの実行の開始
> * モデルの登録とダウンロード
> * モデルを Web サービスとして公開する
> * Web サービスを使用してデータをスコア付けする

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning](https://aka.ms/AMLFree) を今すぐお試しください。

* ご使用の**ローカル環境**からこのドキュメントの CLI コマンドを使用するには、[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) が必要です。

    [Azure Cloud Shell](https://azure.microsoft.com//features/cloud-shell/) を使用する場合は、ブラウザーを使用してクラウド内に存在する CLI にアクセスします。

## <a name="download-the-example-project"></a>プロジェクト例のダウンロード

このチュートリアルでは、[https://github.com/microsoft/MLOps](https://github.com/microsoft/MLOps) プロジェクトをダウンロードします。 このチュートリアルでは、`model-training` ディレクトリと `model-deployment` ディレクトリ内のファイルが使用されます。

ファイルのローカル コピーを取得するには、[.zip アーカイブをダウンロード](https://github.com/microsoft/MLOps/archive/master.zip)するか、次の Git コマンドを使用してリポジトリをクローンします。

```azurecli-interactive
git clone https://github.com/microsoft/MLOps.git
```

### <a name="training-files"></a>トレーニング ファイル

`model-training` ディレクトリには、モデルのトレーニング時に使用される以下のファイルが含まれています。

* `.azureml\sklearn.runconfig`:__実行構成__ファイル。 このファイルでは、モデルのトレーニングに必要なランタイム環境を定義します。
* `train-sklearn.py`:トレーニング スクリプト。 このファイルでは、モデルをトレーニングします。
* `mylib.py`:`train-sklearn.py` によって使用されるヘルパー モジュール。
* `training-env.yml`:トレーニング スクリプトの実行に必要なソフトウェアの依存関係を定義します。

トレーニング スクリプトでは、scikit-learn で提供されている糖尿病データセットを使用してモデルをトレーニングします。

### <a name="deployment-files"></a>デプロイ ファイル

`model-deployment` ディレクトリには、トレーニング済みのモデルを Web サービスとしてデプロイするために使用される以下のファイルが含まれています。

* `aciDeploymentConfig.yml`:__デプロイ構成__ファイル。 このファイルでは、モデルに必要なホスティング環境を定義します。
* `inferenceConfig.yml`:推論構成__ファイル。 このファイルでは、モデルを使用してデータをスコア付けするためにサービスによって使用されるソフトウェア環境を定義します。
* `score.py`:受信データを受け取り、モデルを使用してスコア付けした後、応答を返す python スクリプト。
* `scoring-env.yml`:モデルと `score.py` スクリプトの実行に必要な conda の依存関係。

## <a name="connect-to-your-azure-subscription"></a>Azure サブスクリプションへの接続

CLI から Azure サブスクリプションを認証するには、いくつかの方法があります。 最も基本的な方法は、ブラウザーを使用して対話形式で認証することです。 対話形式で認証するには、コマンド ラインまたはターミナルを開き、次のコマンドを使用します。

```azurecli-interactive
az login
```

CLI で既定のブラウザーを開くことができる場合、開いたブラウザにサインイン ページが読み込まれます。 それ以外の場合は、ブラウザーを開き、コマンド ラインの指示に従う必要があります。 この手順では、[https://aka.ms/devicelogin](https://aka.ms/devicelogin) にアクセスして認証コードを入力する必要があります。

## <a name="install-the-machine-learning-extension"></a>機械学習拡張機能をインストールする

機械学習拡張機能インストールするには、次のコマンドを使用します。

```azurecli-interactive
az extension add -n azure-cli-ml
```

拡張機能が既にインストールされていることを示すメッセージが表示される場合は、次のコマンドを使用して最新バージョンに更新します。

```azurecli-interactive
az extension update -n azure-cli-ml
```

## <a name="create-a-resource-group"></a>リソース グループの作成

リソース グループは、Azure プラットフォーム上のリソースの基本的なコンテナーです。 Azure Machine Learning service を使用する場合、リソース グループには Azure Machine Learning service ワークスペースが含まれます。 また、ワークスペースによって使用される他の Azure サービスも含まれます。 たとえば、クラウドベースのコンピューティング リソースを使用してモデルをトレーニングすると、そのリソースがリソース グループ内に作成されます。

__新しいリソース グループを作成__するには、次のコマンドを使用します。 `<resource-group-name>` をこのリソース グループに使用する名前に置き換えます。 `<location>` をこのリソース グループに使用する Azure リージョンに置き換えます。

> [!TIP]
> Azure Machine Learning service が使用可能なリージョンを選択する必要があります。 詳細については、「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service)」を参照してください。

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

ターミナルまたはコマンド プロンプトで、次のコマンドを使用して、`mlops` ディレクトリに移動してから、ワークスペースに接続します。

```azurecli-interactive
cd ~/mlops
az ml folder attach -w <workspace-name> -g <resource-group-name>
```

このコマンドの出力は、次の JSON のようになります。

```json
{
  "Experiment name": "model-training",
  "Project path": "/Code/MLOps/model-training",
  "Resource group": "<resource-group-name>",
  "Subscription id": "<subscription-id>",
  "Workspace name": "<workspace-name>"
}
```

このコマンドでは、ワークスペースに接続するために必要な情報を含む `.azureml/config.json` ファイルが作成されます。 このチュートリアルで使用する残りの `az ml` コマンドではこのファイルが使用されるため、ワークスペースとリソース グループをすべてのコマンドに追加する必要はありません。

## <a name="create-the-compute-target-for-training"></a>トレーニング用のコンピューティング先の作成

この例では、Azure Machine Learning コンピューティング インスタンスを使用してモデルをトレーニングします。 新しいコンピューティング インスタンスを作成するには、次のコマンドを使用します。

```azurecli-interactive
az ml computetarget create amlcompute -n cpu --max-nodes 4 --vm-size Standard_D2_V2
```

このコマンドの出力は、次の JSON のようになります。

```json
{
  "location": "<location>",
  "name": "cpu",
  "provisioningErrors": null,
  "provisioningState": "Succeeded"
}
```

このコマンドでは、最大 4 つのノードを使用して、`cpu` という名前の新しいコンピューティング先が作成されます。 選択した VM サイズによって、VM に GPU リソースが提供されます。 VM サイズについて詳しくは、[VM の種類とサイズ] を参照してください。

> [!IMPORTANT]
> コンピューティング先の名前 (この例では `cpu`) は重要です。これは、次のセクションで使用する `.azureml/sklearn.runconfig` ファイルによって参照されます。

## <a name="submit-the-training-run"></a>トレーニングの実行の送信

`cpu` コンピューティング先でトレーニングの実行を開始するには、`model-training` ディレクトリに移動し、次のコマンドを使用します。

```azurecli-interactive
cd ~/mlops/model-training
az ml run submit-script -e myexperiment -c sklearn -d training-env.yml -t runoutput.json train-sklearn.py
```

このコマンドでは、実験の名前 (`myexperiment`) が指定されます。 実験では、この実行に関する情報がワークスペースに格納されます。

`-c sklearn` パラメーターでは、`.azureml/sklearn.runconfig` ファイルを指定します。 前述のように、このファイルには、トレーニングの実行で使用される環境を構成するために使用される情報が含まれています。 このファイルを調べると、先ほど作成した `cpu` コンピューティング先が参照されていることがわかります。 また、トレーニング時に使用するノードの数 (`"nodeCount": "4"`) が一覧表示され、トレーニング スクリプトを実行するために必要な Python パッケージを一覧にした `"condaDependenciees"` セクションが含まれています。

実行構成ファイルについて詳しくは、[モデル トレーニング用のコンピューティング先の設定と使用](how-to-set-up-training-targets.md#create-run-configuration-and-submit-run-using-azure-machine-learning-cli)に関するページをご覧ください。

`-t` パラメーターでは、この実行への参照を JSON ファイルに格納します。このパラメーターは、次の手順でモデルを登録およびダウンロードするために使用されます。

トレーニングの実行プロセスでは、リモート コンピューティング リソース上のトレーニング セッションから情報をストリーム配信します。 情報の一部は次のテキストのようになります。

```text
alpha is 0.80, and mse is 3340.02
alpha is 0.85, and mse is 3349.36
alpha is 0.90, and mse is 3359.09
alpha is 0.95, and mse is 3369.13


The experiment completed successfully. Finalizing run...
Cleaning up all outstanding Run operations, waiting 300.0 seconds
```

このテキストは、トレーニング スクリプト (`train-sklearn.py`) からログに記録され、このモデルの 2 つのパフォーマンス メトリックを表示します。 この例では、アルファ値が最大のモデルが必要です。 パフォーマンス メトリックは、トレーニングするモデルに固有です。 他のモデルのパフォーマンス メトリックは異なります。

`train-sklearn.py` を調べると、トレーニング済みのモデルをファイルに保存するときにアルファ値も使用されていることがわかります。 この例では、複数のモデルがトレーニングされます。 アルファが最大のものが最適です。 上記の出力とコードを見ると、アルファが 0.95 のモデルが `./outputs/ridge_0.95.pkl` として保存されました

モデルは、トレーニングされたコンピューティング先にある `./outputs` ディレクトリに保存されました。 この例では、Azure クラウド内の Azure Machine Learning コンピューティング インスタンスです。 トレーニング プロセスでは、トレーニングが発生したコンピューティング先から Azure Machine Learning ワークスペースに `./outputs` ディレクトリの内容が自動的にアップロードされます。 これは、実験 (この例では `myexperiment`) の一部として保存されます。

## <a name="register-the-model"></a>モデルを登録する

実験で保存されているバージョンからモデルを直接登録するには、次のコマンドを使用します。

```azurecli-interactive
az ml model register -n mymodel -f runoutput.json --asset-path "outputs/ridge_0.95.pkl" -t registeredmodel.json
```

このコマンドでは、トレーニングの実行によって作成された `outputs/ridge_0.95.pkl` ファイルが `mymodel` という名前の新しいモデル登録として登録されます。 `--assets-path` では実験内のパスが参照されます。 この場合、実験と実行の情報は、トレーニング コマンドによって作成された `runoutput.json` ファイルから読み込まれます。 `-t registeredmodel.json` では、このコマンドによって作成された新しい登録済みモデルを参照する JSON ファイルが作成され、登録されているモデルと連携する他の CLI コマンドによって使用されます。

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
az ml model register -n mymodel -p "ridge_0.95.pkl"
```

最初のコマンドでは、登録されているモデルを現在のディレクトリにダウンロードします。 ファイル名は `ridge_0.95.pkl` です。これは、モデルを登録したときに参照されたファイルです。 2 番目のコマンドでは、前の登録 (`mymodel`) と同じ名前のローカル モデル (`-p "ridge_0.95.pkl"`) を登録します。 今回は、返される JSON データによってバージョンが 2 と表示されます。

## <a name="deploy-the-model"></a>モデルをデプロイする

モデルをデプロイするには、`model-deployment` ディレクトリに移動し、次のコマンドを使用します。

```azurecli-interactive
cd ~/mlops/model-deployment
az ml model deploy -n myservice -m "mymodel:1" --ic inferenceConfig.yml --dc aciDeploymentConfig.yml
```

「Docker クライアントの作成に失敗しました」というメッセージが表示される場合があります。 このメッセージは無視できます。 CLI では、Web サービスをローカルの Docker コンテナーにデプロイし、Docker を確認することができます。 この例では、ローカル デプロイを使用していません。

このコマンドでは、以前に登録したモデルのバージョン 1 を使用して、`myservice` という名前の新しいサービスをデプロイします。

`inferenceConfig.yml` ファイルでは、入力スクリプト (`score.py`) やソフトウェアの依存関係など、推論の実行方法に関する情報が提供されます。 このファイルの構造について詳しくは、「[推論構成スキーマ](reference-azure-machine-learning-cli.md#inference-configuration-schema)」を参照してください。 エントリ スクリプトの詳細については、「[Azure Machine Learning service を使用してモデルをデプロイする](how-to-deploy-and-where.md#prepare-to-deploy)」を参照してください。

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

エンドポイントを呼び出すクライアント アプリケーションを作成できますが、機械学習 CLI にはテスト クライアントとして機能するユーティリティが用意されています。 次のコマンドを使用して、テスト データをサービスに送信します。

```azurecli-interactive
az ml service run -n myservice -d '{"data":[[1,2,3,4,5,6,7,8,9,10]]}'
```

コマンドからの応答は `[4684.920839774082]` のようになります。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

> [!IMPORTANT]
> 作成したリソースは、Azure Machine Learning に関連したその他のチュートリアルおよびハウツー記事の前提条件として使用できます。

### <a name="delete-deployed-service"></a>デプロイしたサービスを削除する

Azure Machine Learning ワークスペースの使用を継続する予定で、デプロイされたサービスを削除してコストを削減する場合は、次のコマンドを使用します。

```azurecli-interactive
az ml service delete -n myservice
```

このコマンドでは、削除されたサービスの名前を含む JSON ドキュメントが返されます。 サービスが削除されるまで数分かかる場合があります。

### <a name="delete-the-training-compute"></a>トレーニング コンピューティングを削除する

Azure Machine Learning ワークスペースの使用を継続する予定で、トレーニング用に作成された `cpu` コンピューティング先を削除する場合は、次のコマンドを使用します。

```azurecli-interactive
az ml computetarget delete -n cpu
```

このコマンドでは、削除されたコンピューティング先の ID を含む JSON ドキュメントが返されます。 コンピューティング先が削除されるまで数分かかる場合があります。

### <a name="delete-everything"></a>すべてを削除する

作成したリソースを今後使用する予定がない場合は、追加の課金が発生しないように削除します。

リソース グループと、このドキュメントで作成されたすべての Azure リソースを削除するには、次のコマンドを使用します。 `<resource-group-name>` は、前に作成したリソース グループの名前に置き換えます。

```azurecli-interactive
az group delete -g <resource-group-name> -y
```

## <a name="next-steps"></a>次の手順

この Azure Machine Learning のチュートリアルでは、機械学習 CLI を使用して次の作業を行いました。

> [!div class="checklist"]
> * 機械学習拡張機能をインストールする
> * Azure Machine Learning ワークスペースの作成
> * モデルのトレーニングに使用するコンピューティング リソースの作成
> * トレーニングの実行の開始
> * モデルの登録とダウンロード
> * モデルを Web サービスとして公開する
> * Web サービスを使用してデータをスコア付けする

CLI の使用について詳しくは、[Azure Machine Learning service 用 CLI 拡張機能の使用](reference-azure-machine-learning-cli.md)に関する記事を参照してください。