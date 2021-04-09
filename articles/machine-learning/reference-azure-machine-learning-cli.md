---
title: Azure Machine Learning CLI のインストールと使用
description: ワークスペース、データストア、データセット、パイプライン、モデル、デプロイなどのリソースを作成して管理するための、ML 向け Azure CLI 拡張機能を使用する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: jordane
author: jpe316
ms.date: 06/22/2020
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 3e073310d62bfb772ea1120bd379cdc277137da0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102519114"
---
# <a name="install--use-the-cli-extension-for-azure-machine-learning"></a>Azure Machine Learning の CLI 拡張機能のインストールと使用


Azure Machine Learning CLI は [Azure CLI](/cli/azure/) への拡張機能であり、Azure プラットフォーム向けのクロスプラット フォームのコマンド ライン インターフェイスです。 この拡張機能では、Azure Machine Learning を操作するためのコマンドが提供されます。 それにより、機械学習のアクティビティを自動化することができます。 CLI 拡張機能を使用することで可能になるアクションの例を次に示します。

+ 実験を実行して機械学習モデルを作成します

+ 顧客が使用できるように機械学習モデルを登録します

+ 機械学習モデルのライフサイクルをパッケージ化、デプロイ、追跡します

CLI は、Azure Machine Learning SDK に取って代わるものではありません。 自動化に適合するために高度にパラメーター化されたタスクを処理するように最適化された補完的ツールです。

## <a name="prerequisites"></a>前提条件

* CLI を使用するには、Azure サブスクリプションが必要です。 Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning](https://aka.ms/AMLFree) を今すぐお試しください。

* ご使用の **ローカル環境** からこのドキュメントの CLI コマンドを使用するには、[Azure CLI](/cli/azure/install-azure-cli) が必要です。

    [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) を使用する場合は、ブラウザーを使用してクラウド内に存在する CLI にアクセスします。

## <a name="full-reference-docs"></a>詳細なリファレンス ドキュメント

[Azure CLI の azure-cli-ml 拡張機能に関する詳細なリファレンス ドキュメント](/cli/azure/ext/azure-cli-ml/)を参照してください。

## <a name="connect-the-cli-to-your-azure-subscription"></a>Azure サブスクリプションへの CLI の接続

> [!IMPORTANT]
> Azure Cloud Shell を使用している場合は、このセクションを省略できます。 Cloud Shell では、Azure サブスクリプションにログインするアカウントを使用して自動的に認証が行われます。

CLI から Azure サブスクリプションを認証するには、いくつかの方法があります。 最も基本的な方法は、ブラウザーを使用して対話形式で認証することです。 対話形式で認証するには、コマンド ラインまたはターミナルを開き、次のコマンドを使用します。

```azurecli-interactive
az login
```

CLI で既定のブラウザーを開くことができる場合、開いたブラウザにサインイン ページが読み込まれます。 それ以外の場合は、ブラウザーを開き、コマンド ラインの指示に従う必要があります。 この手順では、[https://aka.ms/devicelogin](https://aka.ms/devicelogin) にアクセスして認証コードを入力する必要があります。

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

その他の認証方法については、「[Azure CLI を使用してサインインする](/cli/azure/authenticate-azure-cli)」を参照してください。

## <a name="install-the-extension"></a>拡張機能をインストールする

Machine Learning CLI 拡張機能をインストールするには、次のコマンドを使用します。

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!TIP]
> 以下のコマンドで使用できるサンプル ファイルは、[こちら](https://aka.ms/azml-deploy-cloud)にあります。

メッセージが表示されたら、`y` を選択して拡張機能をインストールします。

拡張機能がインストールされたことを確認するには、次のコマンドを使用して、ML 固有のサブコマンドの一覧を表示します。

```azurecli-interactive
az ml -h
```

## <a name="update-the-extension"></a>拡張機能を更新する

Machine Learning CLI 拡張機能を更新するには、次のコマンドを使用します。

```azurecli-interactive
az extension update -n azure-cli-ml
```


## <a name="remove-the-extension"></a>拡張機能を削除する

CLI 拡張機能を削除するには、次のコマンドを使用します。

```azurecli-interactive
az extension remove -n azure-cli-ml
```

## <a name="resource-management"></a>リソース管理

次のコマンドは、CLI を使用して、Azure Machine Learning によって使用されるリソースを管理する方法を示したものです。

+ リソース グループがまだない場合は、次のように指定してリソース グループを作成します。

    ```azurecli-interactive
    az group create -n myresourcegroup -l westus2
    ```

+ Azure Machine Learning ワークスペースを作成します。

    ```azurecli-interactive
    az ml workspace create -w myworkspace -g myresourcegroup
    ```

    詳しくは、「[az ml workspace create](/cli/azure/ext/azure-cli-ml/ml/workspace#ext-azure-cli-ml-az-ml-workspace-create)」をご覧ください。

+ ワークスペース構成をフォルダーに接続して、CLI コンテキストの認識を有効にします。

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    このコマンドでは、サンプルの runconfig および conda 環境ファイルを含む `.azureml` サブディレクトリを作成します。 これには、Azure Machine Learning ワークスペースとの通信に使用される `config.json` ファイルも含まれています。

    詳しくは、「[az ml folder attach](/cli/azure/ext/azure-cli-ml/ml/folder#ext-azure-cli-ml-az-ml-folder-attach)」をご覧ください。

+ Azure BLOB コンテナーをデータストアとして接続します。

    ```azurecli-interactive
    az ml datastore attach-blob  -n datastorename -a accountname -c containername
    ```

    詳しくは、「[az ml datastore attach-blob](/cli/azure/ext/azure-cli-ml/ml/datastore#ext-azure-cli-ml-az-ml-datastore-attach-blob)」をご覧ください。

+ データストアにファイルをアップロードします。

    ```azurecli-interactive
    az ml datastore upload  -n datastorename -p sourcepath
    ```

    詳細については、[az ml datastore upload](/cli/azure/ext/azure-cli-ml/ml/datastore#ext-azure-cli-ml-az-ml-datastore-upload)に関するページを参照してください。

+ AKS クラスターをコンピューティング先として接続します。

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myresourcegroup -w myworkspace
    ```

    詳しくは、「[az ml computetarget attach aks](/cli/azure/ext/azure-cli-ml/ml/computetarget/attach#ext-azure-cli-ml-az-ml-computetarget-attach-aks)」をご覧ください。

### <a name="compute-clusters"></a>コンピューティング クラスター

+ 新しいマネージド コンピューティング クラスターを作成します。

    ```azurecli-interactive
    az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2
    ```



+ マネージド ID を使用して、新しいマネージド コンピューティング クラスターを作成する

  + ユーザー割り当てマネージド ID

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
    ```

  + システム割り当てマネージド ID

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '[system]'
    ```
+ 既存のクラスターにマネージド ID を追加します。

    + ユーザー割り当てマネージド ID
        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
        ```
    + システム割り当てマネージド ID

        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '[system]'
        ```

詳しくは、「[az ml computetarget create amlcompute](/cli/azure/ext/azure-cli-ml/ml/computetarget/create#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute)」をご覧ください。

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-note.md)]

<a id="computeinstance"></a>

### <a name="compute-instance"></a>コンピューティング インスタンス
コンピューティング インスタンスを管理します。  次のすべての例では、コンピューティング インスタンスの名前は **cpu** になります

+ 新しい computeinstance を作成します。

    ```azurecli-interactive
    az ml computetarget create computeinstance -n cpu -s "STANDARD_D3_V2" -v
    ```

    詳細については、「[az ml computetarget create computeinstance](/cli/azure/ext/azure-cli-ml/ml/computetarget/create#ext-azure-cli-ml-az-ml-computetarget-create-computeinstance)」をご覧ください。

+ computeinstance を停止します。

    ```azurecli-interactive
    az ml computetarget computeinstance stop -n cpu -v
    ```

    詳細については、「[az ml computetarget computeinstance stop](/cli/azure/ext/azure-cli-ml/ml/computetarget/computeinstance#ext-azure-cli-ml-az-ml-computetarget-computeinstance-stop)」を参照してください。

+ computeinstance を開始します。

    ```azurecli-interactive
    az ml computetarget computeinstance start -n cpu -v
    ```

    詳細については、「[az ml computetarget computeinstance start](/cli/azure/ext/azure-cli-ml/ml/computetarget/computeinstance#ext-azure-cli-ml-az-ml-computetarget-computeinstance-start)」を参照してください。

+ computeinstance を再起動します。

    ```azurecli-interactive
    az ml computetarget computeinstance restart -n cpu -v
    ```

    詳細については、「[az ml computetarget computeinstance restart](/cli/azure/ext/azure-cli-ml/ml/computetarget/computeinstance#ext-azure-cli-ml-az-ml-computetarget-computeinstance-restart)」を参照してください。

+ computeinstance を削除します。

    ```azurecli-interactive
    az ml computetarget delete -n cpu -v
    ```

    詳細については、[az ml computetarget の computeinstance の削除](/cli/azure/ext/azure-cli-ml/ml/computetarget#ext-azure-cli-ml-az-ml-computetarget-delete)に関する記事をご覧ください。


## <a name="run-experiments"></a><a id="experiments"></a>実験の実行

* 実験の実行を開始する。 このコマンドを使用する場合は、-c パラメーターに対して runconfig ファイルの名前 (ファイル システムが表示されている場合、\*.runconfig の前のテキスト) を指定します。

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > `az ml folder attach` コマンドでは、2 つのサンプル runconfig ファイルを含む `.azureml` サブディレクトリが作成されます。 
    >
    > プログラムで実行構成オブジェクトを作成する Python スクリプトがある場合は、[RunConfig.save()](/python/api/azureml-core/azureml.core.runconfiguration#save-path-none--name-none--separate-environment-yaml-false-) を使用してそれを runconfig ファイルとして保存できます。
    >
    > 完全な runconfig のスキーマについては、こちらの [JSON ファイル](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json)を参照してください。 スキーマは、各オブジェクトの `description` キーを使用して自己文書化されています。 また、有効な値の列挙と、末尾にテンプレートのスニペットがあります。

    詳しくは、「[az ml run submit-script](/cli/azure/ext/azure-cli-ml/ml/run#ext-azure-cli-ml-az-ml-run-submit-script)」をご覧ください。

* 次のように指定して、実験の一覧を表示します。

    ```azurecli-interactive
    az ml experiment list
    ```

    詳しくは、「[az ml experiment list](/cli/azure/ext/azure-cli-ml/ml/experiment#ext-azure-cli-ml-az-ml-experiment-list)」をご覧ください。

### <a name="hyperdrive-run"></a>HyperDrive の実行

Azure CLI で HyperDrive を使用すると、パラメーターの調整実行を行うことができます。 まず、次の形式で HyperDrive 構成ファイルを作成します。 ハイパーパラメーターのチューニング パラメーターの詳細については、[モデルのハイパーパラメーターのチューニング](how-to-tune-hyperparameters.md)に関する記事を参照してください。

```yml
# hdconfig.yml
sampling: 
    type: random # Supported options: Random, Grid, Bayesian
    parameter_space: # specify a name|expression|values tuple for each parameter.
    - name: --penalty # The name of a script parameter to generate values for.
      expression: choice # supported options: choice, randint, uniform, quniform, loguniform, qloguniform, normal, qnormal, lognormal, qlognormal
      values: [0.5, 1, 1.5] # The list of values, the number of values is dependent on the expression specified.
policy: 
    type: BanditPolicy # Supported options: BanditPolicy, MedianStoppingPolicy, TruncationSelectionPolicy, NoTerminationPolicy
    evaluation_interval: 1 # Policy properties are policy specific. See the above link for policy specific parameter details.
    slack_factor: 0.2
primary_metric_name: Accuracy # The metric used when evaluating the policy
primary_metric_goal: Maximize # Maximize|Minimize
max_total_runs: 8 # The maximum number of runs to generate
max_concurrent_runs: 2 # The number of runs that can run concurrently.
max_duration_minutes: 100 # The maximum length of time to run the experiment before cancelling.
```

実行構成ファイルと共にこのファイルを追加します。 その後、以下を使用して HyperDrive 実行を送信します。
```azurecli
az ml run submit-hyperdrive -e <experiment> -c <runconfig> --hyperdrive-configuration-name <hdconfig> my_train.py
```

runconfig の *arguments* セクションと HyperDrive 構成の *parameter space* に注意してください。これらには、トレーニング スクリプトに渡されるコマンドライン引数が含まれています。 runconfig の値は繰り返しごとに変わりませんが、HyperDrive 構成の範囲は反復処理されます。 両方のファイルで同じ引数を指定しないでください。

## <a name="dataset-management"></a>データセットの管理

次のコマンドは、Azure Machine Learning でデータセットを操作する方法を示しています。

+ データセットを登録する:

    ```azurecli-interactive
    az ml dataset register -f mydataset.json
    ```

    データセットを定義する場合に使用する JSON ファイルの形式の詳細については、`az ml dataset register --show-template` を使用してください。

    詳細については、「[az ml dataset register](/cli/azure/ext/azure-cli-ml/ml/dataset#ext-azure-cli-ml-az-ml-dataset-register)」を参照してください。

+ ワークスペース内のすべてのデータセットを一覧表示する:

    ```azurecli-interactive
    az ml dataset list
    ```

    詳細については、「[az ml dataset list](/cli/azure/ext/azure-cli-ml/ml/dataset#ext-azure-cli-ml-az-ml-dataset-list)」を参照してください。

+ データセットの詳細を取得する:

    ```azurecli-interactive
    az ml dataset show -n dataset-name
    ```

    詳細については、「[az ml dataset show](/cli/azure/ext/azure-cli-ml/ml/dataset#ext-azure-cli-ml-az-ml-dataset-show)」を参照してください。

+ データセットの登録を解除する:

    ```azurecli-interactive
    az ml dataset unregister -n dataset-name
    ```

    詳細については、「[az ml dataset unregister](/cli/azure/ext/azure-cli-ml/ml/dataset#ext-azure-cli-ml-az-ml-dataset-archive)」を参照してください。

## <a name="environment-management"></a>環境の管理

次のコマンドで、お使いのワークスペース用の Azure Machine Learning [環境](how-to-configure-environment.md)を作成、登録、および一覧表示する方法を示します。

+ 環境のスキャフォールディング ファイルを作成します。

    ```azurecli-interactive
    az ml environment scaffold -n myenv -d myenvdirectory
    ```

    詳細については、[az ml environment scaffold](/cli/azure/ext/azure-cli-ml/ml/environment#ext-azure-cli-ml-az-ml-environment-scaffold)に関するページを参照してください。

+ 環境を登録します。

    ```azurecli-interactive
    az ml environment register -d myenvdirectory
    ```

    詳細については、[az ml environment register](/cli/azure/ext/azure-cli-ml/ml/environment#ext-azure-cli-ml-az-ml-environment-register)に関するページを参照してください。

+ 登録されている環境を一覧表示します。

    ```azurecli-interactive
    az ml environment list
    ```

    詳細については、[az ml environment list](/cli/azure/ext/azure-cli-ml/ml/environment#ext-azure-cli-ml-az-ml-environment-list)に関するページを参照してください。

+ 登録されている環境をダウンロードします。

    ```azurecli-interactive
    az ml environment download -n myenv -d downloaddirectory
    ```

    詳細については、[az ml environment download](/cli/azure/ext/azure-cli-ml/ml/environment#ext-azure-cli-ml-az-ml-environment-download)に関するページを参照してください。

### <a name="environment-configuration-schema"></a>環境構成スキーマ

`az ml environment scaffold` コマンドを使用した場合、CLI でカスタム環境構成を作成するために変更および使用できるテンプレートの `azureml_environment.json` ファイルが生成されます。 最上位レベルのオブジェクトは、Python SDK の [`Environment`](/python/api/azureml-core/azureml.core.environment%28class%29) クラスに緩やかにマップされます。 

```json
{
    "name": "testenv",
    "version": null,
    "environmentVariables": {
        "EXAMPLE_ENV_VAR": "EXAMPLE_VALUE"
    },
    "python": {
        "userManagedDependencies": false,
        "interpreterPath": "python",
        "condaDependenciesFile": null,
        "baseCondaEnvironment": null
    },
    "docker": {
        "enabled": false,
        "baseImage": "mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04",
        "baseDockerfile": null,
        "sharedVolumes": true,
        "shmSize": "2g",
        "arguments": [],
        "baseImageRegistry": {
            "address": null,
            "username": null,
            "password": null
        }
    },
    "spark": {
        "repositories": [],
        "packages": [],
        "precachePackages": true
    },
    "databricks": {
        "mavenLibraries": [],
        "pypiLibraries": [],
        "rcranLibraries": [],
        "jarLibraries": [],
        "eggLibraries": []
    },
    "inferencingStackVersion": null
}
```

次の表は、JSON ファイルの各最上位レベルのフィールド、その種類、説明の詳細を示しています。 オブジェクトの種類が Python SDK のクラスにリンクされている場合、各 JSON フィールドと、Python クラスのパブリック変数名の間には緩い 1 対 1 の一致が存在します。 場合によっては、フィールドがクラス変数ではなく、コンストラクター引数にマップされることがあります。 たとえば、`environmentVariables` フィールドは [`Environment`](/python/api/azureml-core/azureml.core.environment%28class%29) クラスの `environment_variables` 変数にマップされます。

| JSON フィールド | Type | 説明 |
|---|---|---|
| `name` | `string` | 環境の名前。 名前を **Microsoft** や **AzureML** で開始しないでください。 |
| `version` | `string` | 環境のバージョン。 |
| `environmentVariables` | `{string: string}` | 環境変数の名前と値のハッシュ マップ。 |
| `python` | ターゲット コンピューティング リソースで使用する Python 環境とインタープリターを定義する [`PythonSection`](/python/api/azureml-core/azureml.core.environment.pythonsection)。 |
| `docker` | [`DockerSection`](/python/api/azureml-core/azureml.core.environment.dockersection) | 環境の仕様に合わせて構築された Docker イメージをカスタマイズするための設定を定義します。 |
| `spark` | [`SparkSection`](/python/api/azureml-core/azureml.core.environment.sparksection) | セクションで Spark 設定が構成されます。 これは、フレームワークが PySpark に設定されている場合にのみ使用されます。 |
| `databricks` | [`DatabricksSection`](/python/api/azureml-core/azureml.core.databricks.databrickssection) | Databricks ライブラリの依存関係を構成します。 |
| `inferencingStackVersion` | `string` | イメージに追加される推論スタックのバージョンを指定します。 推論スタックを追加しないようにするには、このフィールドを `null` のままにします。 有効な値: "latest"。 |

## <a name="ml-pipeline-management"></a>ML パイプラインの管理

次のコマンドは、機械学習パイプラインを操作する方法を示しています。

+ 機械学習パイプラインを作成する:

    ```azurecli-interactive
    az ml pipeline create -n mypipeline -y mypipeline.yml
    ```

    詳しくは、「[az ml pipeline create](/cli/azure/ext/azure-cli-ml/ml/pipeline#ext-azure-cli-ml-az-ml-pipeline-create)」をご覧ください。

    パイプラインの YAML ファイルについて詳しくは、「[YAML で機械学習パイプラインを定義する](reference-pipeline-yaml.md)」をご覧ください。

+ パイプラインの実行:

    ```azurecli-interactive
    az ml run submit-pipeline -n myexperiment -y mypipeline.yml
    ```

    詳しくは、「[az ml run submit-pipeline](/cli/azure/ext/azure-cli-ml/ml/run#ext-azure-cli-ml-az-ml-run-submit-pipeline)」をご覧ください。

    パイプラインの YAML ファイルについて詳しくは、「[YAML で機械学習パイプラインを定義する](reference-pipeline-yaml.md)」をご覧ください。

+ パイプラインのスケジュールを設定する:

    ```azurecli-interactive
    az ml pipeline create-schedule -n myschedule -e myexpereiment -i mypipelineid -y myschedule.yml
    ```

    詳しくは、「[az ml pipeline create-schedule](/cli/azure/ext/azure-cli-ml/ml/pipeline#ext-azure-cli-ml-az-ml-pipeline-create-schedule)」をご覧ください。

    パイプラインのスケジュールに関する YAML ファイルについて詳しくは、「[YAML で機械学習パイプラインを定義する](reference-pipeline-yaml.md#schedules)」をご覧ください。

## <a name="model-registration-profiling-deployment"></a>モデルの登録、プロファイル、デプロイ

次のコマンドは、トレーニング済みモデルを登録し、それを運用サービスとしてデプロイする方法を示したものです。

+ Azure Machine Learning にモデルを登録する:

    ```azurecli-interactive
    az ml model register -n mymodel -p sklearn_regression_model.pkl
    ```

    詳しくは、「[az ml model register](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-register)」をご覧ください。

+ **省略可能** モデルをプロファイルして、デプロイに最適な CPU とメモリの値を取得します。
    ```azurecli-interactive
    az ml model profile -n myprofile -m mymodel:1 --ic inferenceconfig.json -d "{\"data\": [[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}" -t myprofileresult.json
    ```

    詳しくは、「[az ml model profile](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-profile)」をご覧ください。

+ モデルを AKS にデプロイします｡
    ```azurecli-interactive
    az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
    ```
    
    推論構成ファイル スキーマの詳細については、「[推論構成スキーマ](#inferenceconfig)」を参照してください。
    
    デプロイ構成ファイル スキーマの詳細については、「[デプロイ構成スキーマ](#deploymentconfig)」を参照してください。

    詳しくは、「[az ml model deploy](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-deploy)」をご覧ください。

<a id="inferenceconfig"></a>

## <a name="inference-configuration-schema"></a>推論構成スキーマ

[!INCLUDE [inferenceconfig](../../includes/machine-learning-service-inference-config.md)]

<a id="deploymentconfig"></a>

## <a name="deployment-configuration-schema"></a>デプロイ構成スキーマ

### <a name="local-deployment-configuration-schema"></a>ローカル デプロイ構成スキーマ

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-local-deploy-config.md)]

### <a name="azure-container-instance-deployment-configuration-schema"></a>Azure コンテナー インスタンス デプロイ構成スキーマ 

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aci-deploy-config.md)]

### <a name="azure-kubernetes-service-deployment-configuration-schema"></a>Azure Kubernetes Service デプロイ構成スキーマ

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

## <a name="next-steps"></a>次のステップ

* [Machine Learning CLI 拡張機能のコマンド リファレンス](/cli/azure/ext/azure-cli-ml/ml)

* [Azure Pipelines を使用して機械学習モデルをトレーニングおよびデプロイする](/azure/devops/pipelines/targets/azure-machine-learning)
