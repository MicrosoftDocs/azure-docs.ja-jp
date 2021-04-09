---
title: カスタム Docker イメージを使用してモデルをデプロイする
titleSuffix: Azure Machine Learning
description: カスタム Docker ベース イメージを使用して Azure Machine Learning モデルをデプロイする方法について説明します。 Azure Machine Learning には既定のベース イメージが用意されていますが、独自のベース イメージを使用することもできます。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sagopal
author: saachigopal
ms.reviewer: larryfr
ms.date: 11/16/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, deploy, devx-track-azurecli
ms.openlocfilehash: 440723167ded15933ba46a3a58f9a873496bd6a7
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/10/2021
ms.locfileid: "102609364"
---
# <a name="deploy-a-model-using-a-custom-docker-base-image"></a>カスタム Docker ベース イメージを使用してモデルをデプロイする

Azure Machine Learning を使用してトレーニング済みモデルをデプロイするときにカスタム Docker ベース イメージを使用する方法について説明します。

何も指定されていない場合、Azure Machine Learning では既定の基本 Docker イメージが使用されます。 使用されている特定の Docker イメージを見つけるには、`azureml.core.runconfig.DEFAULT_CPU_IMAGE` を使用します。 また、Azure Machine Learning __環境__ を使用して、特定のベース イメージを選択することも、独自に指定したものを使用することもできます。

ベース イメージは、デプロイ用にイメージを作成するときの出発点として使用されます。 基となるオペレーティング システムとコンポーネントが用意されています。 そして、デプロイ プロセスによって、その他のコンポーネント (モデル、conda 環境、その他のアセットなど) がイメージに追加されます。

通常、カスタムの基本イメージは、Docker を使用して依存関係を管理したい場合、コンポーネントのバージョンをより厳密に制御したい場合、またはデプロイ時に時間を節約したい場合に作成します。 モデルに必要なソフトウェアをインストールする場合もあります。この場合、インストール プロセスには長い時間がかかります。 基本イメージを作成するときにソフトウェアをインストールするということは、デプロイごとにインストールする必要がないことを意味します。

> [!IMPORTANT]
> モデルをデプロイするときに、Web サーバーや IoT Edge コンポーネントなどのコア コンポーネントをオーバーライドすることはできません。 これらのコンポーネントは、Microsoft によってテストおよびサポートされている既知の作業環境を提供します。

> [!WARNING]
> Microsoft では、カスタム イメージが原因で発生した問題をトラブルシューティングできない場合があります。 問題が発生した場合は、問題がイメージに固有のものであるかどうかを確認するために、既定のイメージ、または Microsoft が提供するイメージのいずれかを使用するように求められることがあります。

このドキュメントは次の 2 つのセクションに分かれています。

* カスタム ベース イメージを作成する: Azure CLI および Machine Learning CLI を使用してカスタム イメージを作成し、Azure Container Registry への認証を構成する方法について、管理者および DevOps に情報を提供します。
* カスタム ベース イメージを使用してモデルをデプロイする: Python SDK または ML CLI からトレーニング済みモデルをデプロイするときのカスタム イメージの使用に関する情報をデータ サイエンティストや DevOps エンジニア、ML エンジニアに提供します。

## <a name="prerequisites"></a>前提条件

* Azure Machine Learning ワークスペース。 詳細については、「[ワークスペースの作成](how-to-manage-workspace.md)を参照してください。
* [Azure Machine Learning SDK](/python/api/overview/azure/ml/install)。 
* [Azure CLI](/cli/azure/install-azure-cli)。
* [Azure Machine Learning 用 CLI 拡張機能](reference-azure-machine-learning-cli.md)。
* インターネット上でアクセスできる [Azure Container Registry](../container-registry/index.yml) またはその他の Docker レジストリ。
* このドキュメントの手順は、モデルのデプロイの一部として __推論構成__ オブジェクトの作成と使用に慣れていることを前提としています。 詳細については、[デプロイする場所と方法](how-to-deploy-and-where.md)に関する記事を参照してください。

## <a name="create-a-custom-base-image"></a>カスタム ベース イメージを作成する

このセクションの情報は、Docker イメージを格納するために Azure Container Registry を使用していることを前提としています。 Azure Machine Learning 用にカスタム イメージを作成する予定の場合は、次のチェックリストを使用します。

* Azure Machine Learning ワークスペース用に作成された Azure Container Registry と、スタンドアロンの Azure Container Registry のどちらを使用しますか?

    __ワークスペースのコンテナー レジストリ__ に格納されているイメージを使用するときは、そのレジストリの認証を受ける必要はありません。 認証はワークスペースによって処理されます。

    > [!WARNING]
    > ワークスペースの Azure Container Registry は、ワークスペースを使用して __モデルを初めてトレーニングまたはデプロイするときに作成されます__。 新しいワークスペースを作成し、モデルのトレーニングも作成も行っていない場合、そのワークスペースの Azure Container Registry は存在しません。

    __スタンドアロン コンテナー レジストリ__ に格納されているイメージを使用する場合は、少なくとも読み取りアクセス権を持つサービス プリンシパルを構成する必要があります。 次に、レジストリのイメージを使用するユーザーにサービス プリンシパル ID (ユーザー名) とパスワードを提供します。 例外は、コンテナー レジストリを一般にアクセス可能にする場合です。

    プライベート Azure Container Registry の作成については、[プライベート コンテナー レジストリの作成](../container-registry/container-registry-get-started-azure-cli.md)に関するページを参照してください。

    Azure Container Registry でサービス プリンシパルを使用する方法については、「[サービス プリンシパルによる Azure Container Registry 認証](../container-registry/container-registry-auth-service-principal.md)」を参照してください。

* Azure Container Registry とイメージの情報:使用する必要があるユーザーに、イメージ名を提供します。 たとえば、`myregistry` という名前のレジストリに格納されている `myimage` という名前のイメージは、モデルのデプロイでそのイメージを使用するときに `myregistry.azurecr.io/myimage` として参照されます。

### <a name="image-requirements"></a>イメージの要件

Azure Machine Learning では、次のソフトウェアを提供する Docker イメージのみがサポートされています。
* Ubuntu 16.04 以上
* Conda 4.5.# 以上
* Python 3.5 以降

データセットを使用するには、libfuse-dev パッケージをインストールしてください。 また、必要になる可能性のあるユーザー スペース パッケージも必ずインストールしてください。

Azure ML は、Microsoft Container Registry に公開された一連の CPU および GPU の基本イメージを保持しています。それらは、独自のカスタムイメージを作成する代わりに、必要に応じて利用 (または参照) することができます。 それらのイメージの Dockerfile を表示するには、[Azure/AzureML-Containers](https://github.com/Azure/AzureML-Containers) GitHub リポジトリを参照してください。

GPU イメージについては、Azure ML は現在、cuda9 と cuda10 の両方の基本イメージを提供しています。 これらの基本イメージにインストールされている主な依存関係は次のとおりです。

| 依存関係 | IntelMPI CPU | OpenMPI CPU | IntelMPI GPU | OpenMPI GPU |
| --- | --- | --- | --- | --- |
| miniconda | ==4.5.11 | ==4.5.11 | ==4.5.11 | ==4.5.11 |
| mpi | intelmpi==2018.3.222 |openmpi==3.1.2 |intelmpi==2018.3.222| openmpi==3.1.2 |
| cuda | - | - | 9.0/10.0 | 9.0/10.0/10.1 |
| cudnn | - | - | 7.4/7.5 | 7.4/7.5 |
| nccl | - | - | 2.4 | 2.4 |
| git | 2.7.4 | 2.7.4 | 2.7.4 | 2.7.4 |

CPU イメージは、ubuntu16.04 から構築されています。 cuda9 の GPU イメージは、nvidia/cuda:9.0-cudnn7-devel-ubuntu16.04 から構築されています。 cuda10 の GPU イメージは、nvidia/cuda:10.0-cudnn7-devel-ubuntu16.04 から構築されています。
<a id="getname"></a>

> [!IMPORTANT]
> カスタムの Docker イメージを使用する場合は、再現性を高めるためにパッケージのバージョンを固定することをお勧めします。

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

    [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

2. 次のコマンドを使用して、ワークスペースのコンテナー レジストリを一覧表示します。 `<myworkspace>` は、ご利用の Azure Machine Learning ワークスペース名に置き換えます。 `<resourcegroup>` は、ワークスペースが含まれている Azure リソース グループに置き換えます。

    ```azurecli-interactive
    az ml workspace show -w <myworkspace> -g <resourcegroup> --query containerRegistry
    ```

    [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

    次のテキストのような情報が返されます。

    ```text
    /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.ContainerRegistry/registries/<registry_name>
    ```

    `<registry_name>` 値は、ワークスペースの Azure Container Registry の名前です。

### <a name="build-a-custom-base-image"></a>カスタム ベース イメージを作成する

このセクションの手順では、Azure Container Registry にカスタム Docker イメージを作成する方法について説明します。 サンプル Dockerfile については、[Azure/AzureML-Containers](https://github.com/Azure/AzureML-Containers) GitHub リポジトリを参照してください。

1. `Dockerfile` という名前の新しいテキスト ファイルを作成し、内容として次のテキストを使用します。

    ```text
    FROM ubuntu:16.04

    ARG CONDA_VERSION=4.7.12
    ARG PYTHON_VERSION=3.7
    ARG AZUREML_SDK_VERSION=1.13.0
    ARG INFERENCE_SCHEMA_VERSION=1.1.0

    ENV LANG=C.UTF-8 LC_ALL=C.UTF-8
    ENV PATH /opt/miniconda/bin:$PATH
    ENV DEBIAN_FRONTEND=noninteractive

    RUN apt-get update --fix-missing && \
        apt-get install -y wget bzip2 && \
        apt-get install -y fuse && \
        apt-get clean -y && \
        rm -rf /var/lib/apt/lists/*

    RUN useradd --create-home dockeruser
    WORKDIR /home/dockeruser
    USER dockeruser

    RUN wget --quiet https://repo.anaconda.com/miniconda/Miniconda3-${CONDA_VERSION}-Linux-x86_64.sh -O ~/miniconda.sh && \
        /bin/bash ~/miniconda.sh -b -p ~/miniconda && \
        rm ~/miniconda.sh && \
        ~/miniconda/bin/conda clean -tipsy
    ENV PATH="/home/dockeruser/miniconda/bin/:${PATH}"

    RUN conda install -y conda=${CONDA_VERSION} python=${PYTHON_VERSION} && \
        pip install azureml-defaults==${AZUREML_SDK_VERSION} inference-schema==${INFERENCE_SCHEMA_VERSION} &&\
        conda clean -aqy && \
        rm -rf ~/miniconda/pkgs && \
        find ~/miniconda/ -type d -name __pycache__ -prune -exec rm -rf {} \;
    ```

2. シェルまたはコマンド プロンプトから、次のコマンドを使用して Azure Container Registry の認証を受けます。 `<registry_name>` を、イメージを格納するコンテナー レジストリの名前に置き換えます。

    ```azurecli-interactive
    az acr login --name <registry_name>
    ```

3. Dockerfile をアップロードしてビルドするには、次のコマンドを使用します。 `<registry_name>` を、イメージを格納するコンテナー レジストリの名前に置き換えます。

    ```azurecli-interactive
    az acr build --image myimage:v1 --registry <registry_name> --file Dockerfile .
    ```

    > [!TIP]
    > この例では、`:v1` のタグがイメージに適用されます。 タグが指定されない場合は、`:latest` のタグが適用されます。

    ビルド プロセス中に、情報はコマンド ラインに戻るようにストリーミングされます。 ビルドが成功すると、次のようなテキストのメッセージが表示されます。

    ```text
    Run ID: cda was successful after 2m56s
    ```

Azure Container Registry を使用してイメージをビルドする方法の詳細については、「[Azure Container Registry タスクを使用したコンテナー イメージのビルドと実行](../container-registry/container-registry-quickstart-task-cli.md)」を参照してください。

Azure Container Registry に既存のイメージをアップロードする詳細については、[プライベート Docker コンテナー レジストリに最初のイメージをプッシュする](../container-registry/container-registry-get-started-docker-cli.md)方法に関するページを参照してください。

## <a name="use-a-custom-base-image"></a>カスタム ベース イメージを使用する

カスタム イメージを使用するには、次の情報が必要です。

* __イメージ名__。 たとえば、`mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda:latest` は Microsoft が提供する単純な Docker イメージへのパスです。

    > [!IMPORTANT]
    > 作成したカスタム イメージに対しては、必ず、イメージに使用したタグを含めてください。 たとえば、`:v1`などの特定のタグを使ってイメージを作成した場合などです。 イメージの作成時に特定のタグを使用しなかった場合は、`:latest` のタグが適用済みになっています。

* イメージが __プライベート リポジトリ__ 内にある場合は、次の情報が必要です。

    * レジストリの __アドレス__。 たとえば、「 `myregistry.azureecr.io` 」のように入力します。
    * レジストリへの読み取りアクセス権を持つサービス プリンシパルの __ユーザー名__ および __パスワード__。

    この情報がわからない場合は、イメージを含む Azure Container Registry の管理者に相談してください。

### <a name="publicly-available-base-images"></a>一般公開されているベース イメージ

Microsoft は、一般公開されているリポジトリにいくつかの docker イメージを提供しており、このセクションの手順で使用できます。

| Image | 説明 |
| ----- | ----- |
| `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` | Azure Machine Learning のコア イメージ |
| `mcr.microsoft.com/azureml/onnxruntime:latest` | CPU 推論用の ONNX Runtime が含まれています |
| `mcr.microsoft.com/azureml/onnxruntime:latest-cuda` | GPU 用の ONNX Runtime と CUDA が含まれています |
| `mcr.microsoft.com/azureml/onnxruntime:latest-tensorrt` | GPU 用の ONNX Runtime と TensorRT が含まれています |
| `mcr.microsoft.com/azureml/onnxruntime:latest-openvino-vadm` | Movidius<sup>TM</sup> MyriadX VPU に基づく Intel<sup></sup> Vision Accelerator Design 用の ONNX Runtime と OpenVINO が含まれています |
| `mcr.microsoft.com/azureml/onnxruntime:latest-openvino-myriad` | Intel<sup></sup> Movidius<sup>TM</sup> USB スティック用の ONNX Runtime と OpenVINO が含まれています |

ONNX Runtime の基本イメージの詳細については、GitHub リポジトリの [ONNX Runtime dockerfile のセクション](https://github.com/microsoft/onnxruntime/blob/master/dockerfiles/README.md)を参照してください。

> [!TIP]
> これらのイメージは一般公開されているため、使用するときにアドレス、ユーザー名、またはパスワードを入力する必要はありません。

詳細については、GitHub 上の [Azure Machine Learning コンテナー](https://github.com/Azure/AzureML-Containers)のリポジトリを参照してください。

### <a name="use-an-image-with-the-azure-machine-learning-sdk"></a>Azure Machine Learning SDK でイメージを使用する

**ご自分のワークスペースの Azure Container Registry**、または **パブリックにアクセスできるコンテナー レジストリ** に格納されたイメージを使用するには、次の [環境](/python/api/azureml-core/azureml.core.environment.environment)属性を設定します。

+ `docker.enabled=True`
+ `docker.base_image`:レジストリとイメージへのパスを設定します。

```python
from azureml.core.environment import Environment
# Create the environment
myenv = Environment(name="myenv")
# Enable Docker and reference an image
myenv.docker.enabled = True
myenv.docker.base_image = "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda:latest"
```

ご自分のワークスペース内にない __プライベート コンテナー レジストリ__ のイメージを使用するには、`docker.base_image_registry` でリポジトリのアドレスとユーザー名とパスワードを指定する必要があります。

```python
# Set the container registry information
myenv.docker.base_image_registry.address = "myregistry.azurecr.io"
myenv.docker.base_image_registry.username = "username"
myenv.docker.base_image_registry.password = "password"

myenv.inferencing_stack_version = "latest"  # This will install the inference specific apt packages.

# Define the packages needed by the model and scripts
from azureml.core.conda_dependencies import CondaDependencies
conda_dep = CondaDependencies()
# you must list azureml-defaults as a pip dependency
conda_dep.add_pip_package("azureml-defaults")
myenv.python.conda_dependencies=conda_dep
```

バージョン 1.0.45 以上の azureml-defaults を pip 依存関係として追加する必要があります。 このパッケージには、Web サービスとしてモデルをホストするために必要な機能が含まれています。 また、環境の inferencing_stack_version プロパティを "latest" に設定する必要があります。これにより、Web サービスに必要な特定の apt パッケージがインストールされます。 

環境を定義したら、それを [InferenceConfig](/python/api/azureml-core/azureml.core.model.inferenceconfig) オブジェクトと共に使用して、モデルと Web サービスを実行する推論環境を定義します。

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

Python 環境のカスタマイズの詳細については、[トレーニングとデプロイのための環境の作成と管理](how-to-use-environments.md)に関するページを参照してください。 

### <a name="use-an-image-with-the-machine-learning-cli"></a>Machine Learning CLI でイメージを使用する

> [!IMPORTANT]
> 現在、Machine Learning CLI では、Azure Container Registry のイメージを、ご自分のワークスペースまたは一般公開されているリポジトリに使用できます。 スタンドアロンのプライベート レジストリからのイメージは使用できません。

Machine Learning CLI を使用してモデルをデプロイする前に、カスタム イメージを使用する[環境](/python/api/azureml-core/azureml.core.environment.environment)を作成します。 次に、環境を参照する推論構成ファイルを作成します。 推論構成ファイルで直接環境を定義することもできます。 次の JSON ドキュメントは、パブリック コンテナー レジストリ内のイメージを参照する方法を示しています。 この例では、環境をインラインで定義しています。

```json
{
    "entryScript": "score.py",
    "environment": {
        "docker": {
            "arguments": [],
            "baseDockerfile": null,
            "baseImage": "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda:latest",
            "enabled": false,
            "sharedVolumes": true,
            "shmSize": null
        },
        "environmentVariables": {
            "EXAMPLE_ENV_VAR": "EXAMPLE_VALUE"
        },
        "name": "my-deploy-env",
        "python": {
            "baseCondaEnvironment": null,
            "condaDependencies": {
                "channels": [
                    "conda-forge"
                ],
                "dependencies": [
                    "python=3.6.2",
                    {
                        "pip": [
                            "azureml-defaults",
                            "azureml-telemetry",
                            "scikit-learn",
                            "inference-schema[numpy-support]"
                        ]
                    }
                ],
                "name": "project_environment"
            },
            "condaDependenciesFile": null,
            "interpreterPath": "python",
            "userManagedDependencies": false
        },
        "version": "1"
    }
}
```

このファイルは `az ml model deploy` コマンドで使用されます。 `--ic` パラメーターは、推論構成ファイルを指定するために使用されます。

```azurecli
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
```

ML CLI を使用したモデルのデプロイの詳細については、[Azure Machine Learning 用 CLI 拡張機能](reference-azure-machine-learning-cli.md#model-registration-profiling-deployment)に関する記事の「モデルの登録、プロファイル、デプロイ」セクションを参照してください。

## <a name="next-steps"></a>次のステップ

* [デプロイ先とその方法](how-to-deploy-and-where.md)の詳細を学習します。
* [Azure Pipelines を使用して機械学習モデルをトレーニングおよびデプロイする](/azure/devops/pipelines/targets/azure-machine-learning)方法を学習します。