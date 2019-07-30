---
title: カスタム Docker イメージを使用してモデルをデプロイする方法
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning service モデルをデプロイするときにカスタム Docker イメージを使用する方法について説明します。 トレーニング済みモデルをデプロイすると、Docker イメージが作成され、サービスを実行するために必要なイメージ、Web サーバー、およびその他のコンポーネントがホストされます。 Azure Machine Learning service には既定のイメージが用意されていますが、独自のイメージを使用することもできます。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 07/11/2019
ms.openlocfilehash: b8591fe750d4bb1441cdc28c488b2c860eb0bccb
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840061"
---
# <a name="deploy-a-model-using-a-custom-docker-image"></a>カスタム Docker イメージを使用してモデルをデプロイする

Azure Machine Learning service を使用してトレーニング済みモデルをデプロイするときにカスタム Docker イメージを使用する方法について説明します。

トレーニング済みモデルを Web サービスまたは IoT Edge デバイスにデプロイすると、Docker イメージが作成されます。 このイメージには、モデル、conda 環境、およびモデルを使用するために必要な資産が含まれています。 また、Web サービスとしてデプロイされるときに受信要求を処理する Web サーバーと、Azure IoT Hub と連携するために必要なコンポーネントも含まれています。

Azure Machine Learning service には既定の Docker イメージが用意されているので、作成する必要はありません。 また、作成したカスタム イメージを "_ベース イメージ_" として使用することもできます。 ベース イメージは、デプロイ用にイメージを作成するときの出発点として使用されます。 基となるオペレーティング システムとコンポーネントが用意されています。 デプロイ プロセスでは、デプロイ前に、モデル、conda 環境、その他のアセットなどのコンポーネントがイメージに追加されます。

通常、コンポーネントのバージョンを制御する場合や、デプロイ時の時間を節約する場合は、カスタム イメージを作成します。 たとえば、特定のバージョンの Python、Conda、またはその他のコンポーネントに基づいて標準化する場合があります。 モデルに必要なソフトウェアをインストールする場合もあります。この場合、インストール プロセスには長い時間がかかります。 基本イメージを作成するときにソフトウェアをインストールするということは、デプロイごとにインストールする必要がないことを意味します。

> [!IMPORTANT]
> モデルをデプロイするときに、Web サーバーや IoT Edge コンポーネントなどのコア コンポーネントをオーバーライドすることはできません。 これらのコンポーネントは、Microsoft によってテストおよびサポートされている既知の作業環境を提供します。

> [!WARNING]
> Microsoft では、カスタム イメージが原因で発生した問題をトラブルシューティングできない場合があります。 問題が発生した場合は、問題がイメージに固有のものであるかどうかを確認するために、既定のイメージ、または Microsoft が提供するイメージのいずれかを使用するように求められることがあります。

このドキュメントは次の 2 つのセクションに分かれています。

* カスタム イメージを作成する:Azure CLI および Machine Learning CLI を使用してカスタム イメージを作成し、Azure Container Registry への認証を構成する方法について、管理者および DevOps に情報を提供します。
* カスタム イメージを使用する:Python SDK または ML CLI からトレーニング済みモデルをデプロイするときのカスタム イメージの使用に関する情報をデータ サイエンティストおよび DevOps/MLOps に提供します。

## <a name="prerequisites"></a>前提条件

* Azure Machine Learning service ワークグループ。 詳細については、[ワークスペースの作成](setup-create-workspace.md)に関する記事を参照してください。
* Azure Machine Learning SDK。 詳細については、「[ワークスペースの作成](setup-create-workspace.md#sdk)」の記事の Python SDK のセクションを参照してください。
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。
* [Azure Machine Learning 用 CLI 拡張機能](reference-azure-machine-learning-cli.md)。
* インターネット上でアクセスできる [Azure Container Registry](/azure/container-registry) またはその他の Docker レジストリ。
* このドキュメントの手順は、モデルのデプロイの一部として__推論構成__オブジェクトの作成と使用に慣れていることを前提としています。 詳細については、[デプロイ先と方法](how-to-deploy-and-where.md#prepare-to-deploy)に関するページの「デプロイを準備する」セクションを参照してください。

## <a name="create-a-custom-image"></a>カスタム イメージを作成する

このセクションの情報は、Docker イメージを格納するために Azure Container Registry を使用していることを前提としています。 Azure Machine Learning service 用にカスタム イメージを作成する予定の場合は、次のチェックリストを使用します。

* Azure Machine Learning service ワークスペース用に作成された Azure Container Registry と、スタンドアロンの Azure Container Registry のどちらを使用しますか。

    __ワークスペースのコンテナー レジストリ__に格納されているイメージを使用するときは、そのレジストリの認証を受ける必要はありません。 認証はワークスペースによって処理されます。

    > [!WARNING]
    > ワークスペースの Azure Container Registry は、ワークスペースを使用して__モデルを初めてトレーニングまたはデプロイするときに作成されます__。 新しいワークスペースを作成し、モデルのトレーニングも作成も行っていない場合、そのワークスペースの Azure Container Registry は存在しません。

    ワークスペースの Azure Container Registry の名前を取得する方法については、この記事の [コンテナー レジストリ名の取得](#getname)のセクションを参照してください。

    __スタンドアロン コンテナー レジストリ__に格納されているイメージを使用する場合は、少なくとも読み取りアクセス権を持つサービス プリンシパルを構成する必要があります。 次に、レジストリのイメージを使用するユーザーにサービス プリンシパル ID (ユーザー名) とパスワードを提供します。 例外は、コンテナー レジストリを一般にアクセス可能にする場合です。

    プライベート Azure Container Registry の作成については、[プライベート コンテナー レジストリの作成](/azure/container-registry/container-registry-get-started-azure-cli)に関するページを参照してください。

    Azure Container Registry でサービス プリンシパルを使用する方法については、「[サービス プリンシパルによる Azure Container Registry 認証](/azure/container-registry/container-registry-auth-service-principal)」を参照してください。

* Azure Container Registry とイメージの情報:使用する必要があるユーザーに、イメージ名を提供します。 たとえば、`myregistry` という名前のレジストリに格納されている `myimage` という名前のイメージは、モデルのデプロイでそのイメージを使用するときに `myregistry.azurecr.io/myimage` として参照されます。

* イメージの要件:Azure Machine Learning service は、次のソフトウェアを提供する Docker イメージのみをサポートしています。

    * Ubuntu 16.04 以上
    * Conda 4.5.# 以上
    * Python 3.5.# または 3.6.#

<a id="getname"></a>

### <a name="get-container-registry-information"></a>コンテナー レジストリ情報を取得する

このセクションでは、Azure Machine Learning service ワークスペースの Azure Container Registry の名前を取得する方法について説明します。

> [!WARNING]
> ワークスペースの Azure Container Registry は、ワークスペースを使用して__モデルを初めてトレーニングまたはデプロイするときに作成されます__。 新しいワークスペースを作成し、モデルのトレーニングも作成も行っていない場合、そのワークスペースの Azure Container Registry は存在しません。

Azure Machine Learning service を使用してモデルのトレーニングまたはデプロイを既に行っている場合は、ワークスペース用のコンテナー レジストリが作成されています。 このコンテナー レジストリの名前を確認するには、次の手順を実行します。

1. 新しいシェルまたはコマンドプロンプトを開き、次のコマンドを使用して Azure サブスクリプションの認証を受けます。

    ```azurecli-interactive
    az login
    ```

    プロンプトに従ってサブスクリプションの認証を受けます。

2. 次のコマンドを使用して、ワークスペースのコンテナー レジストリを一覧表示します。 `<myworkspace>` は、お使いの Azure Machine Learning service ワークスペース名に置き換えます。 `<resourcegroup>` は、ワークスペースが含まれている Azure リソース グループに置き換えます。

    ```azurecli-interactive
    az ml workspace show -w <myworkspace> -g <resourcegroup> --query containerRegistry
    ```

    次のテキストのような情報が返されます。

    ```text
    /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.ContainerRegistry/registries/<registry_name>
    ```

    `<registry_name>` 値は、ワークスペースの Azure Container Registry の名前です。

### <a name="build-a-custom-image"></a>カスタム イメージをビルドする

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

## <a name="use-a-custom-image"></a>カスタム イメージを使用する

カスタム イメージを使用するには、次の情報が必要です。

* __イメージ名__。 たとえば、`mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` は Microsoft が提供する基本的な Docker イメージへのパスです。
* イメージが__プライベート リポジトリ__内にある場合は、次の情報が必要です。

    * レジストリの__アドレス__。 たとえば、「 `myregistry.azureecr.io` 」のように入力します。
    * レジストリへの読み取りアクセス権を持つサービス プリンシパルの__ユーザー名__および__パスワード__。

    この情報がわからない場合は、イメージを含む Azure Container Registry の管理者に相談してください。

### <a name="publicly-available-images"></a>一般公開されているイメージ

Microsoft は、一般公開されているリポジトリにいくつかの docker イメージを提供しており、このセクションの手順で使用できます。

| Image | 説明 |
| ----- | ----- |
| `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` | Azure Machine Learning service の基本イメージ |
| `mcr.microsoft.com/azureml/onnxruntime:v0.4.0` | ONNX ランタイムが含まれています。 |
| `mcr.microsoft.com/azureml/onnxruntime:v0.4.0-cuda10.0-cudnn7` | ONNX ランタイムと CUDA コンポーネントが含まれています。 |
| `mcr.microsoft.com/azureml/onnxruntime:v0.4.0-tensorrt19.03` | ONNX ランタイムと TensorRT が含まれています。 |

> [!TIP]
> これらのイメージは一般公開されているため、使用するときにアドレス、ユーザー名、またはパスワードを入力する必要はありません。

> [!IMPORTANT]
> CUDA または TensorRT を使用する Microsoft イメージを使用できるのは Microsoft Azure サービスのみです。

> [!TIP]
>__モデルを Azure Machine Learning コンピューティングでトレーニングする場合__、__バージョン 1.0.22 以降__の Azure Machine Learning SDK を使用して、トレーニング時にイメージが作成されます。 このイメージの名前を見つけるには、`run.properties["AzureML.DerivedImageName"]` を使用します。 次の例は、このイメージを使用する方法を示しています。
>
> ```python
> # Use an image built during training with SDK 1.0.22 or greater
> image_config.base_image = run.properties["AzureML.DerivedImageName"]
> ```

### <a name="use-an-image-with-the-azure-machine-learning-sdk"></a>Azure Machine Learning SDK でイメージを使用する

カスタム イメージを使用するには、[推論構成オブジェクト](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)の `base_image` プロパティをイメージのアドレスに設定します。

```python
# use an image from a registry named 'myregistry'
inference_config.base_image = "myregistry.azurecr.io/myimage:v1"
```

この形式は、ワークスペース用の Azure Container Registry に格納されているイメージと、一般公開されているコンテナー レジストリの両方に利用できます。 たとえば、次のコードでは Microsoft が提供する既定のイメージを使用しています。

```python
# use an image available in public Container Registry without authentication
inference_config.base_image = "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda"
```

ワークスペース内にない__プライベート コンテナー レジストリ__のイメージを使用するには、リポジトリのアドレスとユーザー名とパスワードを指定する必要があります。

```python
# Use an image available in a private Container Registry
inference_config.base_image = "myregistry.azurecr.io/mycustomimage:1.0"
inference_config.base_image_registry.address = "myregistry.azurecr.io"
inference_config.base_image_registry.username = "username"
inference_config.base_image_registry.password = "password"
```

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

ML CLI を使用したモデルのデプロイの詳細については、[Azure Machine Learning service 用 CLI 拡張機能](reference-azure-machine-learning-cli.md#model-registration-profiling-deployment)の記事の「モデルの登録、プロファイル、デプロイ」セクションを参照してください。

## <a name="next-steps"></a>次の手順

* [デプロイ先とその方法](how-to-deploy-and-where.md)の詳細を学習します。
* [Azure Pipelines を使用して機械学習モデルをトレーニングおよびデプロイする](/azure/devops/pipelines/targets/azure-machine-learning?view=azure-devops)方法を学習します。
