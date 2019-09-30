---
title: カスタム Docker ベース イメージを使用してモデルをデプロイする
titleSuffix: Azure Machine Learning
description: Azure Machine Learning モデルをデプロイするときにカスタム Docker ベース イメージを使用する方法について説明します。 トレーニング済みのモデルをデプロイするとき、推論のモデルを実行するためのベース コンテナー イメージがデプロイされます。 Azure Machine Learning には既定のベース イメージが用意されていますが、独自のベース イメージを使用することもできます。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 08/22/2019
ms.openlocfilehash: b86c0d0de95581f7a47de428ffbf0b161353df83
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2019
ms.locfileid: "71260757"
---
# <a name="deploy-a-model-using-a-custom-docker-base-image"></a>カスタム Docker ベース イメージを使用してモデルをデプロイする

Azure Machine Learning を使用してトレーニング済みモデルをデプロイするときにカスタム Docker ベース イメージを使用する方法について説明します。

トレーニング済みのモデルを Web サービスまたは IoT Edge デバイスにデプロイするとき、受信要求を処理する Web サーバーを含んだパッケージが作成されます。

Azure Machine Learning には既定の Docker ベース イメージが用意されているので、自分で作成する必要はありません。 また、Azure Machine Learning __環境__を使用して、特定のベース イメージを選択することも、独自に指定したものを使用することもできます。

ベース イメージは、デプロイ用にイメージを作成するときの出発点として使用されます。 基となるオペレーティング システムとコンポーネントが用意されています。 デプロイ プロセスでは、デプロイ前に、モデル、conda 環境、その他のアセットなどのコンポーネントがイメージに追加されます。

通常、カスタム ベース イメージは、Docker を使用して依存関係を管理したい場合や、コンポーネントのバージョンをより厳密に制御したい場合、デプロイの時間を節約したい場合などに作成します。 たとえば、特定のバージョンの Python、Conda、またはその他のコンポーネントに基づいて標準化する場合があります。 モデルに必要なソフトウェアをインストールする場合もあります。この場合、インストール プロセスには長い時間がかかります。 基本イメージを作成するときにソフトウェアをインストールするということは、デプロイごとにインストールする必要がないことを意味します。

> [!IMPORTANT]
> モデルをデプロイするときに、Web サーバーや IoT Edge コンポーネントなどのコア コンポーネントをオーバーライドすることはできません。 これらのコンポーネントは、Microsoft によってテストおよびサポートされている既知の作業環境を提供します。

> [!WARNING]
> Microsoft では、カスタム イメージが原因で発生した問題をトラブルシューティングできない場合があります。 問題が発生した場合は、問題がイメージに固有のものであるかどうかを確認するために、既定のイメージ、または Microsoft が提供するイメージのいずれかを使用するように求められることがあります。

このドキュメントは次の 2 つのセクションに分かれています。

* カスタム ベース イメージを作成する: Azure CLI および Machine Learning CLI を使用してカスタム イメージを作成し、Azure Container Registry への認証を構成する方法について、管理者および DevOps に情報を提供します。
* カスタム ベース イメージを使用してモデルをデプロイする: Python SDK または ML CLI からトレーニング済みモデルをデプロイするときのカスタム イメージの使用に関する情報をデータ サイエンティストや DevOps エンジニア、ML エンジニアに提供します。

## <a name="prerequisites"></a>前提条件

* Azure Machine Learning ワークグループ。 詳細については、「[ワークスペースの作成](how-to-manage-workspace.md)を参照してください。
* [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。 
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。
* [Azure Machine Learning 用 CLI 拡張機能](reference-azure-machine-learning-cli.md)。
* インターネット上でアクセスできる [Azure Container Registry](/azure/container-registry) またはその他の Docker レジストリ。
* このドキュメントの手順は、モデルのデプロイの一部として __推論構成__ オブジェクトの作成と使用に慣れていることを前提としています。 詳細については、[デプロイ先と方法](how-to-deploy-and-where.md#prepare-to-deploy)に関するページの「デプロイを準備する」セクションを参照してください。

## <a name="create-a-custom-base-image"></a>カスタム ベース イメージを作成する

このセクションの情報は、Docker イメージを格納するために Azure Container Registry を使用していることを前提としています。 Azure Machine Learning 用にカスタム イメージを作成する予定の場合は、次のチェックリストを使用します。

* Azure Machine Learning ワークスペース用に作成された Azure Container Registry と、スタンドアロンの Azure Container Registry のどちらを使用しますか?

    __ワークスペースのコンテナー レジストリ__ に格納されているイメージを使用するときは、そのレジストリの認証を受ける必要はありません。 認証はワークスペースによって処理されます。

    > [!WARNING]
    > ワークスペースの Azure Container Registry は、ワークスペースを使用して __モデルを初めてトレーニングまたはデプロイするときに作成されます__。 新しいワークスペースを作成し、モデルのトレーニングも作成も行っていない場合、そのワークスペースの Azure Container Registry は存在しません。

    ワークスペースの Azure Container Registry の名前を取得する方法については、この記事の [コンテナー レジストリ名の取得](#getname)のセクションを参照してください。

    __スタンドアロン コンテナー レジストリ__ に格納されているイメージを使用する場合は、少なくとも読み取りアクセス権を持つサービス プリンシパルを構成する必要があります。 次に、レジストリのイメージを使用するユーザーにサービス プリンシパル ID (ユーザー名) とパスワードを提供します。 例外は、コンテナー レジストリを一般にアクセス可能にする場合です。

    プライベート Azure Container Registry の作成については、[プライベート コンテナー レジストリの作成](/azure/container-registry/container-registry-get-started-azure-cli)に関するページを参照してください。

    Azure Container Registry でサービス プリンシパルを使用する方法については、「[サービス プリンシパルによる Azure Container Registry 認証](/azure/container-registry/container-registry-auth-service-principal)」を参照してください。

* Azure Container Registry とイメージの情報:使用する必要があるユーザーに、イメージ名を提供します。 たとえば、`myregistry` という名前のレジストリに格納されている `myimage` という名前のイメージは、モデルのデプロイでそのイメージを使用するときに `myregistry.azurecr.io/myimage` として参照されます。

* イメージの要件:Azure Machine Learning では、次のソフトウェアを提供する Docker イメージのみがサポートされています。

    * Ubuntu 16.04 以上
    * Conda 4.5.# 以上
    * Python 3.5.# または 3.6.#

<a id="getname"></a>

### <a name="get-container-registry-information"></a>コンテナー レジストリ情報を取得する

このセクションでは、Azure Machine Learning ワークスペースの Azure Container Registry の名前を取得する方法について説明します。

> [!WARNING]
> ワークスペースの Azure Container Registry は、ワークスペースを使用して __モデルを初めてトレーニングまたはデプロイするときに作成されます__。 新しいワークスペースを作成し、モデルのトレーニングも作成も行っていない場合、そのワークスペースの Azure Container Registry は存在しません。

Azure Machine Learning を使用してモデルのトレーニングまたはデプロイを既に行っている場合は、ワークスペース用のコンテナー レジストリが作成されています。 このコンテナー レジストリの名前を確認するには、次の手順を実行します。

1. 新しいシェルまたはコマンドプロンプトを開き、次のコマンドを使用して Azure サブスクリプションの認証を受けます。

    ```azurecli-interactive
    az login
    ```

    プロンプトに従ってサブスクリプションの認証を受けます。

2. 次のコマンドを使用して、ワークスペースのコンテナー レジストリを一覧表示します。 `<myworkspace>` は、ご利用の Azure Machine Learning ワークスペース名に置き換えます。 `<resourcegroup>` は、ワークスペースが含まれている Azure リソース グループに置き換えます。

    ```azurecli-interactive
    az ml workspace show -w <myworkspace> -g <resourcegroup> --query containerRegistry
    ```

    [!INCLUDE [install extension](../../../includes/machine-learning-service-install-extension.md)]

    次のテキストのような情報が返されます。

    ```text
    /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.ContainerRegistry/registries/<registry_name>
    ```

    `<registry_name>` 値は、ワークスペースの Azure Container Registry の名前です。

### <a name="build-a-custom-base-image"></a>カスタム ベース イメージを作成する

このセクションの手順では、Azure Container Registry にカスタム Docker イメージを作成する方法について説明します。

1. `Dockerfile` という名前の新しいテキスト ファイルを作成し、内容として次のテキストを使用します。

    ```text
    FROM ubuntu:16.04

    ARG CONDA_VERSION=4.5.12
    ARG PYTHON_VERSION=3.6

    ENV LANG=C.UTF-8 LC_ALL=C.UTF-8
    ENV PATH /opt/miniconda/bin:$PATH

    RUN apt-get update --fix-missing && \
        apt-get install -y wget bzip2 && \
        apt-get clean && \
        rm -rf /var/lib/apt/lists/*

    RUN wget --quiet https://repo.anaconda.com/miniconda/Miniconda3-${CONDA_VERSION}-Linux-x86_64.sh -O ~/miniconda.sh && \
        /bin/bash ~/miniconda.sh -b -p /opt/miniconda && \
        rm ~/miniconda.sh && \
        /opt/miniconda/bin/conda clean -tipsy

    RUN conda install -y conda=${CONDA_VERSION} python=${PYTHON_VERSION} && \
        conda clean -aqy && \
        rm -rf /opt/miniconda/pkgs && \
        find / -type d -name __pycache__ -prune -exec rm -rf {} \;
    ```

2. シェルまたはコマンド プロンプトから、次のコマンドを使用して Azure Container Registry の認証を受けます。 `<registry_name>` を、イメージを格納するコンテナー レジストリの名前に置き換えます。

    ```azurecli-interactive
    az acr login --name <registry_name>
    ```

3. Dockerfile をアップロードしてビルドするには、次のコマンドを使用します。 `<registry_name>` を、イメージを格納するコンテナー レジストリの名前に置き換えます。

    ```azurecli-interactive
    az acr build --image myimage:v1 --registry <registry_name> --file Dockerfile .
    ```

    ビルド プロセス中に、情報はコマンド ラインに戻るようにストリーミングされます。 ビルドが成功すると、次のようなテキストのメッセージが表示されます。

    ```text
    Run ID: cda was successful after 2m56s
    ```

Azure Container Registry を使用してイメージをビルドする方法の詳細については、「[Azure Container Registry タスクを使用したコンテナー イメージのビルドと実行](https://docs.microsoft.com/azure/container-registry/container-registry-quickstart-task-cli)」を参照してください。

Azure Container Registry に既存のイメージをアップロードする詳細については、[プライベート Docker コンテナー レジストリに最初のイメージをプッシュする](/azure/container-registry/container-registry-get-started-docker-cli)方法に関するページを参照してください。

## <a name="use-a-custom-base-image"></a>カスタム ベース イメージを使用する

カスタム イメージを使用するには、次の情報が必要です。

* __イメージ名__。 たとえば、`mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` は Microsoft が提供する基本的な Docker イメージへのパスです。
* イメージが __プライベート リポジトリ__ 内にある場合は、次の情報が必要です。

    * レジストリの __アドレス__。 たとえば、「 `myregistry.azureecr.io` 」のように入力します。
    * レジストリへの読み取りアクセス権を持つサービス プリンシパルの __ユーザー名__ および __パスワード__。

    この情報がわからない場合は、イメージを含む Azure Container Registry の管理者に相談してください。

### <a name="publicly-available-base-images"></a>一般公開されているベース イメージ

Microsoft は、一般公開されているリポジトリにいくつかの docker イメージを提供しており、このセクションの手順で使用できます。

| Image | 説明 |
| ----- | ----- |
| `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` | Azure Machine Learning の基本イメージ |
| `mcr.microsoft.com/azureml/onnxruntime:v0.4.0` | ONNX ランタイムが含まれています。 |
| `mcr.microsoft.com/azureml/onnxruntime:v0.4.0-cuda10.0-cudnn7` | ONNX ランタイムと CUDA コンポーネントが含まれています。 |
| `mcr.microsoft.com/azureml/onnxruntime:v0.4.0-tensorrt19.03` | ONNX ランタイムと TensorRT が含まれています。 |

> [!TIP]
> これらのイメージは一般公開されているため、使用するときにアドレス、ユーザー名、またはパスワードを入力する必要はありません。

> [!IMPORTANT]
> CUDA または TensorRT を使用する Microsoft イメージを使用できるのは Microsoft Azure サービスのみです。

詳細については、[Azure Machine Learning コンテナー](https://github.com/Azure/AzureML-Containers)に関するページを参照してください。

> [!TIP]
>__モデルを Azure Machine Learning コンピューティングでトレーニングする場合__、__バージョン 1.0.22 以降__ の Azure Machine Learning SDK を使用して、トレーニング時にイメージが作成されます。 このイメージの名前を見つけるには、`run.properties["AzureML.DerivedImageName"]` を使用します。 次の例は、このイメージを使用する方法を示しています。
>
> ```python
> # Use an image built during training with SDK 1.0.22 or greater
> image_config.base_image = run.properties["AzureML.DerivedImageName"]
> ```

### <a name="use-an-image-with-the-azure-machine-learning-sdk"></a>Azure Machine Learning SDK でイメージを使用する

**ご自分のワークスペースの Azure Container Registry**、または**パブリックにアクセスできるコンテナー レジストリ**に格納されたイメージを使用するには、次の[環境](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)属性を設定します。

+ `docker.enabled=True`
+ `docker.base_image`:レジストリとイメージへのパスを設定します。

```python
from azureml.core import Environment
# Create the environment
myenv = Environment(name="myenv")
# Enable Docker and reference an image
myenv.docker.enabled = True
myenv.docker.base_image = "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda"
```

ご自分のワークスペース内にない__プライベート コンテナー レジストリ__のイメージを使用するには、`docker.base_image_registry` でリポジトリのアドレスとユーザー名とパスワードを指定する必要があります。

```python
# Set the container registry information
myenv.docker.base_image_registry.address = "myregistry.azurecr.io"
myenv.docker.base_image_registry.username = "username"
myenv.docker.base_image_registry.password = "password"
```

環境を定義したら、それを [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) オブジェクトと共に使用して、モデルと Web サービスを実行する推論環境を定義します。

```python
from azureml.core.model import InferenceConfig
# Use environment in InferenceConfig
inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
```

この時点で、デプロイを続行できます。 たとえば、次のコード スニペットでは、推論構成とカスタム イメージを使用して Web サービスがローカルにデプロイされます。

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

デプロイの詳細については、「[Azure Machine Learning を使用してモデルをデプロイする](how-to-deploy-and-where.md)」を参照してください。

### <a name="use-an-image-with-the-machine-learning-cli"></a>Machine Learning CLI でイメージを使用する

> [!IMPORTANT]
> 現在、Machine Learning CLI では、Azure Container Registry のイメージを、ご自分のワークスペースまたは一般公開されているリポジトリに使用できます。 スタンドアロンのプライベート レジストリからのイメージは使用できません。

Machine Learning CLI を使用してモデルをデプロイするときは、カスタム イメージを参照する推論構成ファイルを用意します。 次の JSON ドキュメントは、パブリック コンテナー レジストリ内のイメージを参照する方法を示しています。

```json
{
   "entryScript": "score.py",
   "runtime": "python",
   "condaFile": "infenv.yml",
   "extraDockerfileSteps": null,
   "sourceDirectory": null,
   "enableGpu": false,
   "baseImage": "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda",
   "baseImageRegistry": "mcr.microsoft.com"
}
```

このファイルは `az ml model deploy` コマンドで使用されます。 `--ic` パラメーターは、推論構成ファイルを指定するために使用されます。

```azurecli
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
```

ML CLI を使用したモデルのデプロイの詳細については、[Azure Machine Learning 用 CLI 拡張機能](reference-azure-machine-learning-cli.md#model-registration-profiling-deployment)に関する記事の「モデルの登録、プロファイル、デプロイ」セクションを参照してください。

## <a name="next-steps"></a>次の手順

* [デプロイ先とその方法](how-to-deploy-and-where.md)の詳細を学習します。
* [Azure Pipelines を使用して機械学習モデルをトレーニングおよびデプロイする](/azure/devops/pipelines/targets/azure-machine-learning?view=azure-devops)方法を学習します。
