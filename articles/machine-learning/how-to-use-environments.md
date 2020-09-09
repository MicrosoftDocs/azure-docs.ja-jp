---
title: ソフトウェア環境を使用する
titleSuffix: Azure Machine Learning
description: モデルのトレーニングとデプロイのための環境を作成し、管理します。 環境の Python パッケージとその他の設定を管理します。
services: machine-learning
author: saachigopal
ms.author: sagopal
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.date: 07/23/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 3368a42248e084476eb27318abbcd1ca9fbfdacf
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88927546"
---
# <a name="create--use-software-environments-in-azure-machine-learning"></a>Azure Machine Learning でソフトウェア環境を作成して使用する
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

この記事では、Azure Machine Learning の[環境](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)を作成および管理する方法を学習します。 環境を使用して、プロジェクトのソフトウェアの発展する依存関係を追跡および再現します。

ソフトウェアの依存関係の管理は、開発者にとって一般的なタスクです。 広範な手動によるソフトウェア構成を行わずに、ビルドを再現できることを保証する必要があります。 Azure Machine Learning の `Environment` クラスには、pip や Conda などのローカル開発ソリューションや、Docker 機能を通じた分散クラウド開発を指定します。

この記事の例は、以下の方法を示します。

* 環境を作成して、パッケージの依存関係を指定する。
* 環境を取得および更新する。
* トレーニングに環境を使用する。
* Web サービスのデプロイに環境を使用する。

Azure Machine Learning の環境のしくみの概要については、[ML 環境の概要](concept-environments.md)に関するページを参照してください。 開発環境の構成については、[こちら](how-to-configure-environment.md)を参照してください。

## <a name="prerequisites"></a>前提条件

* [Azure Machine Learning SDK for Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* [Azure Machine Learning ワークスペース](how-to-manage-workspace.md)

## <a name="create-an-environment"></a>環境の作成

以下のセクションでは、実験用の環境を作成できる複数の方法について説明します。

### <a name="use-a-curated-environment"></a>選別された環境を使用する

キュレーションされた環境には、既定でお使いのワークスペースで使用できる Python パッケージのコレクションが含まれています。 これらの環境は、キャッシュされた Docker イメージでバックアップされ、実行の準備コストを下げます。 まずは、次などの一般的なキュレーション環境を 1 つ選択するとよいでしょう。 

* _AzureML-Minimal_ 環境には、実行追跡とアセット アップロードを可能にする最小限のパッケージ セットが含まれています。 それを独自の環境の開始点として利用できます。

* _AzureML-Tutorial_ 環境には、一般的なデータ サイエンス パッケージが含まれています。 これらのパッケージには、Scikit-Learn、Pandas、Matplotlib、およびより大きな azureml-sdk パッケージのセットが含まれます。

キュレーションされた環境の一覧については、[キュレーションされた環境](resource-curated-environments.md)に関する記事を参照してください。

`Environment.get` メソッドを使用して、選別された環境のいずれかを選択します。

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
env = Environment.get(workspace=ws, name="AzureML-Minimal")
```

次のコードを使用して、選別された環境とそのパッケージを一覧表示できます。

```python
envs = Environment.list(workspace=ws)

for env in envs:
    if env.startswith("AzureML"):
        print("Name",env)
        print("packages", envs[env].python.conda_dependencies.serialize_to_string())
```

> [!WARNING]
>  独自の環境名の先頭には、_AzureML_ プレフィックスを付加しないでください。 このプレフィックスは、選別された環境用に予約されています。


### <a name="instantiate-an-environment-object"></a>環境オブジェクトのインスタンス化

環境を手動で作成するには、SDK から `Environment` クラスをインポートします。 次に、以下のコードを使用して、環境オブジェクトをインスタンス化します。

```python
from azureml.core.environment import Environment
Environment(name="myenv")
```

自分独自の環境を定義する場合、pip の依存関係として `azureml-defaults` にバージョン 1.0.45 以降を指定する必要があります。 このパッケージには、Web サービスとしてモデルをホストするために必要な機能が含まれています。

### <a name="use-conda-and-pip-specification-files"></a>Conda と pip の仕様ファイルを使用する

環境は、Conda の仕様または pip 要件ファイルから作成することができます。 [`from_conda_specification()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-conda-specification-name--file-path-) メソッド、または [`from_pip_requirements()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-pip-requirements-name--file-path-) メソッドを使用します。 メソッドの引数に、ご利用の環境の名前と、必要なファイルのファイル パスを含めます。 

```python
# From a Conda specification file
myenv = Environment.from_conda_specification(name = "myenv",
                                             file_path = "path-to-conda-specification-file")

# From a pip requirements file
myenv = Environment.from_pip_requirements(name = "myenv"
                                          file_path = "path-to-pip-requirements-file")                                          
```

### <a name="use-existing-environments"></a>既存の環境を使用する

ローカル コンピューター上に既存の Conda 環境がある場合は、環境オブジェクトを作成するためのサービスを使用できます。 この戦略を使用することで、ローカルの対話型環境をリモート実行で再利用できます。

次のコードでは、既存の Conda 環境 `mycondaenv` から環境オブジェクトを作成します。 [`from_existing_conda_environment()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-existing-conda-environment-name--conda-environment-name-) メソッドが使用されます。

``` python
myenv = Environment.from_existing_conda_environment(name = "myenv",
                                                    conda_environment_name = "mycondaenv")
```

環境定義は、[`save_to_directory()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#save-to-directory-path--overwrite-false-) メソッドを使用して、簡単に編集できる形式でディレクトリに保存できます。 変更が完了すると、そのディレクトリからファイルを読み込んで、新しい環境をインスタンス化できます。

```python
myenv = Environment.save_to_directory(path = "path-to-destination-directory", overwrite = False)
# modify the environment definition
newenv = Environment.load_from_directory(path = "path-to-source-directory")
```

### <a name="create-environments-automatically"></a>環境を自動的に作成する

トレーニング実行を送信することで、環境を自動的に作成します。 `submit()` メソッドを使用して、実行を送信します。 トレーニング実行を送信するときに、新しい環境のビルドに数分かかる場合があります。 ビルド期間は、必要な依存関係のサイズによって異なります。 

実行を送信する前に実行構成で環境を指定しなかった場合は、自動的に既定の環境が作成されます。

```python
from azureml.core import ScriptRunConfig, Experiment, Environment
# Create experiment 
myexp = Experiment(workspace=ws, name = "environment-example")

# Attach training script and compute target to run config
runconfig = ScriptRunConfig(source_directory=".", script="example.py")
runconfig.run_config.target = "local"

# Submit the run
run = myexp.submit(config=runconfig)

# Show each step of run 
run.wait_for_completion(show_output=True)
```

同様に、[`Estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) オブジェクトをトレーニングに使用する場合は、環境を指定せずに、推定インスタンスを実行として直接送信できます。 `Estimator` オブジェクトでは既に、環境とコンピューティング ターゲットがカプセル化されています。

## <a name="add-packages-to-an-environment"></a>環境にパッケージを追加する

Conda、pip、またはプライベート wheel ファイルを使用して、パッケージを環境に追加します。 [`CondaDependency`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) クラスを使用して、各パッケージの依存関係を指定します。 それを環境の `PythonSection` に追加します。

### <a name="conda-and-pip-packages"></a>Conda パッケージと pip パッケージ

パッケージが Conda パッケージ リポジトリで使用可能な場合は、pip インストールではなく、Conda インストールを使用することをお勧めします。 Conda パッケージには通常、インストールの信頼性を高めるビルド済みのバイナリが付属しています。

次の例では環境 `myenv` に追加します。 バージョン 1.17.0 の `numpy` が追加されます。 また、`pillow` パッケージも追加されます。 この例では、[`add_conda_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-conda-package-conda-package-) メソッドと [`add_pip_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-pip-package-pip-package-) メソッドがそれぞれ使用されています。

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

myenv = Environment(name="myenv")
conda_dep = CondaDependencies()

# Installs numpy version 1.17.0 conda package
conda_dep.add_conda_package("numpy==1.17.0")

# Installs pillow package
conda_dep.add_pip_package("pillow")

# Adds dependencies to PythonSection of myenv
myenv.python.conda_dependencies=conda_dep
```

自分の環境に環境変数を追加することもできます。 そうすると、これらは、トレーニング スクリプトで os.environ.get を使用して利用できるようになります。

```python
myenv.environment_variables = {"MESSAGE":"Hello from Azure Machine Learning"}
```

>[!IMPORTANT]
> 別の実行に同じ環境定義を使用する場合、Azure Machine Learning service では、自分の環境にキャッシュされているイメージが再利用されます。 ```numpy``` など、固定されていないパッケージの依存関係を持つ環境を作成すると、その環境では、"_環境の作成時_" にインストールされたパッケージのバージョンが引き続き使用されます。 また、一致した定義を持つ将来の環境では、古いバージョンが使用され続けます。 詳細については、「[環境のビルド、キャッシュ、再利用](https://docs.microsoft.com/azure/machine-learning/concept-environments#environment-building-caching-and-reuse)」を参照してください。

### <a name="private-python-packages"></a>非公開の Python パッケージ

パブリック インターネットに公開せずに、非公開で安全に Python パッケージを使用する方法については、[非公開の Python パッケージの使用](how-to-use-private-python-packages.md)に関する記事を参照してください。

## <a name="manage-environments"></a>環境を管理する

コンピューティング ターゲット全体で、またワークスペースの他のユーザーと共に環境を更新、追跡、再利用できるように環境を管理します。

### <a name="register-environments"></a>環境を登録する

実行を送信するか、Web サービスをデプロイすると、環境が自動的にワークスペースに登録されます。 [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#register-workspace-) メソッドを使用して、環境を手動で登録することもできます。 この操作により、環境は、クラウドで追跡およびバージョン管理されるエンティティに変わります。 エンティティは、ワークスペース ユーザー間で共有できます。

次のコードでは、`myenv` 環境を `ws` ワークスペースに登録します。

```python
myenv.register(workspace=ws)
```

トレーニングまたはデプロイで初めて環境を使用するときに、ワークスペースに登録されます。 その後、コンピューティング ターゲットにビルドおよびデプロイされます。 環境はサービスによってキャッシュされます。 キャッシュされた環境を再利用すると、新しいサービスや更新されたサービスを使用するよりもずっと短時間で済みます。

### <a name="get-existing-environments"></a>既存の環境を取得する

`Environment` クラスでは、ワークスペース内の既存の環境を取得できるメソッドが提供されます。 環境は、名前、一覧、または特定のトレーニング実行によって取得できます。 この情報は、トラブルシューティングや監査に、また再現性を確保するのに役立ちます。

#### <a name="view-a-list-of-environments"></a>環境の一覧を表示する

[`Environment.list(workspace="workspace_name")`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#list-workspace-) クラスを使用して、ワークスペース内の環境を表示します。 次に、再利用する環境を選択します。

#### <a name="get-an-environment-by-name"></a>名前を指定して環境を取得する

名前とバージョンを指定して特定の環境を取得することもできます。 次のコードでは、[`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#get-workspace--name--version-none-) メソッドを使用して、`ws` ワークスペース上のバージョン `1` の `myenv` 環境を取得します。

```python
restored_environment = Environment.get(workspace=ws,name="myenv",version="1")
```

#### <a name="train-a-run-specific-environment"></a>実行固有の環境をトレーニングする

トレーニングが完了した後に特定の実行に使用された環境を取得するには、`Run` クラスの [`get_environment()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-environment--) メソッドを使用します。

```python
from azureml.core import Run
Run.get_environment()
```

### <a name="update-an-existing-environment"></a>既存の環境を更新する

たとえば、Python パッケージを追加するなどして、既存の環境を変更したとします。 新しいバージョンの環境は、実行の送信、モデルのデプロイ、または環境の手動登録を行うときに作成ドされるので、ビルドには時間がかかります。 バージョン管理を使用すると、時間の経過に伴う環境の変化を確認できます。 

既存の環境の Python パッケージ バージョンを更新するには、そのパッケージのバージョン番号を指定します。 正確なバージョン番号を使用しない場合、Azure Machine Learning では、元のパッケージ バージョンで既存の環境が再利用されます。

### <a name="debug-the-image-build"></a>イメージ ビルドをデバッグする

次の例では、[`build()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#build-workspace--image-build-compute-none-) メソッドを使用して、環境を Docker イメージとして手動で作成します。 [`wait_for_completion()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image(class)?view=azure-ml-py#wait-for-creation-show-output-false-) を使用して、イメージ ビルドからの出力ログを監視します。 その後、ビルドされたイメージは、ワークスペースの Azure Container Registry インスタンスに表示されます。 この情報はデバッグに役立ちます。

```python
from azureml.core import Image
build = env.build(workspace=ws)
build.wait_for_completion(show_output=True)
```

まずは、[`build_local()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#build-local-workspace--platform-none----kwargs-) メソッドを使用して、ローカルでイメージをビルドするのが便利です。 Docker イメージをビルドするには、オプションのパラメーター `useDocker=True` を設定します。 生成されたイメージを AzureML ワークスペース コンテナー レジストリにプッシュするには、`pushImageToWorkspaceAcr=True` を設定します。

```python
build = env.build_local(workspace=ws, useDocker=True, pushImageToWorkspaceAcr=True)
```

> [!WARNING]
>  環境内の依存関係またはチャネルの順序を変更すると、新しい環境になり、新しいイメージのビルドが必要になります。 また、既存のイメージの `build()` メソッドを呼び出すと、新しいバージョンが存在する場合にその依存関係も更新されます。 

## <a name="enable-docker"></a>Docker を有効にする

Docker コンテナーにより、依存関係を効率的にカプセル化する方法が提供されます。 Docker を有効にすると、Azure ML によって Docker イメージがビルドされ、指定に従って、そのコンテナー内に Python 環境が作成されます。 Docker イメージはキャッシュされて再利用されます。通常、新しい環境での最初の実行時には、イメージがビルドされるため時間がかかります。

Azure Machine Learning `Environment` クラスの [`DockerSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockersection?view=azure-ml-py) では、トレーニングを実行するゲスト オペレーティング システムを細かくカスタマイズおよび制御できます。 `arguments` 変数を使用すると、Docker の実行コマンドに渡す追加の引数を指定できます。

```python
# Creates the environment inside a Docker container.
myenv.docker.enabled = True
```

既定では、新しくビルドされた Docker イメージは、ワークスペースに関連付けられているコンテナー レジストリに表示されます。  リポジトリ名の形式は *azureml/azureml_\<uuid\>* です。 名前の一意識別子 (*uuid*) の部分は、環境構成から計算されたハッシュに対応します。 この対応により、サービスでは、特定の環境のイメージが既に存在していて再利用できるかどうかを判断できます。

### <a name="use-a-custom-docker-image-or-dockerfile"></a>カスタム Docker イメージまたは Dockerfile を使用する 
サービスでは、Ubuntu Linux ベースの[基本イメージ](https://github.com/Azure/AzureML-Containers)のいずれかが自動的に使用されます。 指定された Python パッケージがインストールされます。 基本イメージには、CPU バージョンと GPU バージョンがあります。 Azure Machine Learning では、使用するバージョンが自動的に検出されます。 [カスタム Docker 基本イメージ](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-custom-docker-image#create-a-custom-base-image)を使用することも可能です。

```python
# Specify custom Docker base image and registry, if you don't want to use the defaults
myenv.docker.base_image="your_base-image"
myenv.docker.base_image_registry="your_registry_location"
```

>[!IMPORTANT]
> Azure Machine Learning では、次のソフトウェアを提供する Docker イメージのみがサポートされています。
> * Ubuntu 16.04 以上
> * Conda 4.5.# 以上
> * Python 3.5.#、3.6.#、または 3.7.#

カスタム Dockerfile を指定することもできます。 Docker ```FROM``` コマンドを使用していずれかの Azure Machine Learning 基本イメージから開始し、独自のカスタム ステップを追加する方法が最も簡単です。 Python 以外のパッケージを依存関係としてインストールする必要がある場合は、この方法を使用します。 基本イメージは必ず [なし] に設定してください。

```python
# Specify docker steps as a string. 
dockerfile = r"""
FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04
RUN echo "Hello from custom container!"
"""

# Set base image to None, because the image is defined by dockerfile.
myenv.docker.base_image = None
myenv.docker.base_dockerfile = dockerfile

# Alternatively, load the string from a file.
myenv.docker.base_image = None
myenv.docker.base_dockerfile = "./Dockerfile"
```

### <a name="specify-your-own-python-interpreter"></a>独自の Python インタープリターを指定する

場合によっては、使用するパッケージを含む Python 環境がカスタム基本イメージに含まれていることがあります。

自分独自でインストールしたパッケージを使用し、Conda を無効にするには、パラメーターを `Environment.python.user_managed_dependencies = True` に設定します。 基本イメージに Python インタープリターが含まれており、トレーニング スクリプトに必要なパッケージがあることを確実にします。

たとえば、NumPy パッケージがインストールされている基本の Miniconda 環境で実行するには、まず、パッケージをインストールするステップを含む Dockerfile を指定します。 ユーザーによって管理される依存関係を `True` に設定します。 

また、`Environment.python.interpreter_path` 変数を設定することにより、イメージ内の特定の Python インタープリターへのパスを指定することもできます。

```python
dockerfile = """
FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04
RUN conda install numpy
"""

myenv.docker.base_image = None
myenv.docker.base_dockerfile = dockerfile
myenv.python.user_managed_dependencies=True
myenv.python.interpreter_path = "/opt/miniconda/bin/python"
```

> [!WARNING]
> Python の一部の依存関係を Docker イメージにインストールし、user_managed_dependencies = True の設定を忘れた場合、それらのパッケージは実行環境に存在しないため、実行時エラーが発生します。 既定では、指定した依存関係を含む Conda 環境が Azure ML で構築され、その環境で実行が行われます。基本イメージにインストールされている Python ライブラリは使用されません。

### <a name="retrieve-image-details"></a>イメージの詳細を取得する

登録済みの環境では、次のコードを使用してイメージの詳細を取得できます。`details` は [DockerImageDetails](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockerimagedetails?view=azure-ml-py) (AzureML Python SDK >= 1.11) のインスタンスであり、dockerfile、レジストリー、イメージ名などの環境イメージに関するすべての情報を提供します。

```python
details = environment.get_image_details(workspace=ws)
```

## <a name="use-environments-for-training"></a>トレーニングに環境を使用する

トレーニング実行を送信するには、環境、[コンピューティング ターゲット](concept-compute-target.md)、トレーニング Python スクリプトを実行構成に結合する必要があります。 この構成は、実行の送信に使用されるラッパー オブジェクトです。

トレーニング実行を送信するときに、新しい環境のビルドに数分かかる場合があります。 その期間は、必要な依存関係のサイズによって異なります。 環境はサービスによってキャッシュされます。 そのため、環境定義が変更されていない限り、完全なセットアップの時間が発生するのは一度だけです。

次のローカル スクリプトの実行例では、[`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) をラッパー オブジェクトとして使用する場所を示しています。

```python
from azureml.core import ScriptRunConfig, Experiment
from azureml.core.environment import Environment

exp = Experiment(name="myexp", workspace = ws)
# Instantiate environment
myenv = Environment(name="myenv")

# Add training script to run config
runconfig = ScriptRunConfig(source_directory=".", script="train.py")

# Attach compute target to run config
runconfig.run_config.target = "local"

# Attach environment to run config
runconfig.run_config.environment = myenv

# Submit run 
run = exp.submit(runconfig)
```

> [!NOTE]
> 実行履歴または実行スナップショットを無効にするには、`ScriptRunConfig.run_config.history` の下の設定を使用します。

実行構成で環境を指定しない場合、実行を送信するときにサービスによって既定の環境が作成されます。

### <a name="use-an-estimator-for-training"></a>トレーニングに見積もりツールを使用する

トレーニングに[見積りツール](how-to-train-ml-models.md)を使用する場合は、推定インスタンスを直接送信できます。 環境とコンピューティング ターゲットは既にカプセル化されています。

次のコードでは、単一ノードのトレーニング実行で見積もりツールを使用します。 これは、`scikit-learn` モデルのリモート コンピューティングで実行されます。 以前にコンピューティング ターゲット オブジェクト `compute_target`、およびデータストア オブジェクト `ds` を作成したことを前提とします。

```python
from azureml.train.estimator import Estimator

script_params = {
    '--data-folder': ds.as_mount(),
    '--regularization': 0.8
}

sk_est = Estimator(source_directory='./my-sklearn-proj',
                   script_params=script_params,
                   compute_target=compute_target,
                   entry_script='train.py',
                   conda_packages=['scikit-learn'])

# Submit the run 
run = experiment.submit(sk_est)
```

## <a name="use-environments-for-web-service-deployment"></a>Web サービスのデプロイに環境を使用する

モデルを Web サービスとしてデプロイするときに、環境を使用できます。 この機能により、再現可能な、接続されたワークフローが有効になります。 このワークフローでは、トレーニング コンピューティングと推論計算の両方で同じライブラリを使用して、モデルのトレーニング、テスト、およびデプロイを行うことができます。

Web サービスをデプロイするには、環境、推論計算、スコアリング スクリプト、および登録済みモデルをデプロイ オブジェクト [`deploy()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) に結合します。 詳細については、「[モデルをデプロイする方法と場所](how-to-deploy-and-where.md)」を参照してください。

この例では、トレーニングの実行が完了していることを前提とします。 次に、そのモデルを Azure Container Instances にデプロイします。 Web サービスをビルドすると、モデルとスコアリング ファイルがイメージにマウントされ、Azure Machine Learning 推論スタックがイメージに追加されます。

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import AciWebservice, Webservice

# Register the model to deploy
model = run.register_model(model_name = "mymodel", model_path = "outputs/model.pkl")

# Combine scoring script & environment in Inference configuration
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

# Set deployment configuration
deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)

# Define the model, inference, & deployment configuration and web service name and location to deploy
service = Model.deploy(
    workspace = ws,
    name = "my_web_service",
    models = [model],
    inference_config = inference_config,
    deployment_config = deployment_config)
```

## <a name="notebooks"></a>ノートブック

こちらの[記事](https://docs.microsoft.com/azure/machine-learning/how-to-run-jupyter-notebooks#add-new-kernels)では、Conda 環境をカーネルとしてノートブックにインストールする方法についての情報が提供されています。

[カスタム Docker イメージを使用するモデルのデプロイ方法](how-to-deploy-custom-docker-image.md)に関するページでは、カスタム Docker ベース イメージを使用してモデルをデプロイする方法を示します。

この[サンプルの Notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/spark) では、Spark モデルを Web サービスとしてデプロイする方法が示されます。

## <a name="create-and-manage-environments-with-the-cli"></a>CLI を使用して環境を作成および管理する

[Azure Machine Learning CLI](reference-azure-machine-learning-cli.md) では、Python SDK のほとんどの機能をミラーリングします。 それを使用して、環境を作成および管理することができます。 このセクションで説明するコマンドでは、基本的な機能が示されています。

以下のコマンドでは、指定されたディレクトリ内の既定の環境定義のファイルをスキャフォールディングします。 これらのファイルは JSON ファイルです。 これらは、SDK の対応するクラスと同じように機能します。 これらのファイルを使用すると、カスタム設定を含む新しい環境を作成できます。 

```azurecli-interactive
az ml environment scaffold -n myenv -d myenvdir
```

次のコマンドを実行して、指定されたディレクトリから環境を登録します。

```azurecli-interactive
az ml environment register -d myenvdir
```

次のコマンドを実行して、登録されたすべての環境を一覧表示します。

```azurecli-interactive
az ml environment list
```

次のコマンドを使用して、登録された環境をダウンロードします。

```azurecli-interactive
az ml environment download -n myenv -d downloaddir
```

## <a name="next-steps"></a>次のステップ

* マネージド コンピューティング ターゲットを使用してモデルをトレーニングする場合は、[チュートリアル: モデルのトレーニング](tutorial-train-models-with-aml.md)に関するページを参照してください。
* モデルのトレーニングが完了した後、[モデルをデプロイする方法と場所](how-to-deploy-and-where.md)について学習します。
* [`Environment` クラスの SDK リファレンス](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py)を参照します。
