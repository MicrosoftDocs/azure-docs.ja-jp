---
title: ワークスペースの作成
titleSuffix: Azure Machine Learning service
description: Azure portal、SDK、テンプレートまたは CLI を使用し、Azure Machine Learning service ワークスペースを作成します。 このワークスペースでは、Azure Machine Learning service を使用するときに作成する、すべての成果物を操作するための一元的な場所が提供されます。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.author: sgilley
author: sdgilley
ms.date: 03/21/2019
ms.openlocfilehash: e2304f45cef2db720adf4430868205c00714034f
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2019
ms.locfileid: "59547950"
---
# <a name="create-an-azure-machine-learning-service-workspace"></a>Azure Machine Learning service ワークスペースを作成する

Azure Machine Learning service を使用するには、[**Azure Machine Learning service ワークスペース**](concept-azure-machine-learning-architecture.md#workspace)が必要です。  このワークスペースはサービスの最上位リソースであり、作成するすべての成果物を扱うための中心的な場所となります。 

この記事では、次のいずれかの方法でワークスペースを作成する方法について説明します。 
* [Azure portal](#portal) インターフェイス
* [Azure Machine Learning SDK for Python](#sdk)
* Azure Resource Manager テンプレート
* [Azure Machine Learning CLI](#cli)

ここで紹介する手順で作成したワークスペースは、他のチュートリアルやハウツー記事の前提条件として利用できます。 

ワークスペースを作成すると、次の Azure リソースが自動的に追加されます (リージョンで利用できる場合)。
 
- [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
- [Azure Storage](https://azure.microsoft.com/services/storage/)
- [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) 
- [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)

>[!Note]
>他の Azure サービスと同様に、特定の制限とクォータが Machine Learning に関連付けられています。 クォータの説明と、クォータの引き上げを要求する方法については、[こちら](how-to-manage-quotas.md)を参照してください。


## <a name="prerequisites"></a>前提条件
ワークスペースを作成するには、Azure サブスクリプションが必要です。 Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning service](https://aka.ms/AMLFree) を今日からお試しいただけます。

## <a name="portal"></a> Azure portal

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

作成方法に関係なく、[Azure portal](https://portal.azure.com/) で自分のワークスペースを表示できます。  詳細については「[ワークスペースの表示](how-to-manage-workspace.md#view)」を参照してください。

## <a name="sdk"></a> Python SDK

Python SDK を使用してワークスペースを作成します。 最初にこの SDK をインストールする必要があります。

> [!IMPORTANT]
> Azure Data Science Virtual Machine または Azure Databricks を使用している場合、この SDK のインストールをスキップします。
> * 2018 年 9 月 27 日より後に作成された Azure データ サイエンス仮想マシンには、Python SDK がプレインストールされています。 インストールをスキップし、「[Create a workspace with the SDK](#sdk-create)」(SDK でワークスペースを作成する) から始めてください。
> * Azure Databricks 環境では、代わりに [Databricks のインストール手順](how-to-configure-environment.md#azure-databricks)を使用します。

>[!NOTE]
> 指示に従ってローカル コンピューターに SDK をインストールして使用します。 リモートの仮想マシン上の Jupyter を使用するには、リモート デスクトップまたは X ターミナル セッションを設定します。

SDK をインストールする前に、分離された Python 環境を作成することをお勧めします。 この記事では [Miniconda](https://docs.conda.io/en/latest/miniconda.html) を使用していますが、[Anaconda](https://www.anaconda.com/) をフルインストールしたり、[Python virtualenv](https://virtualenv.pypa.io/en/stable/) を使用したりすることもできます。

この記事の手順では、クイック スタートとチュートリアルのノートブックを実行するために必要なすべてのパッケージがインストールされます。  他のサンプル ノートブックでは、さらに別のコンポーネントのインストールが必要になることがあります。  これらのコンポーネントの詳細については、「[Install the Azure Machine Learning SDK for Python (Azure Machine Learning SDK for Python をインストールする)](https://docs.microsoft.com/python/api/overview/azure/ml/install)」を参照してください。

### <a name="install-miniconda"></a>Miniconda のインストール

[Miniconda をダウンロードしてインストールします](https://docs.conda.io/en/latest/miniconda.html)。 Python 3.7 バージョンを選択してインストールします。 Python 2.x バージョンは選択しないでください。  

### <a name="create-an-isolated-python-environment"></a>分離された Python 環境の作成

1. コマンド ライン ウィンドウを開いて、*myenv* という名前の新しい conda 環境を作成し、Python 3.6.5 をインストールします。 Azure Machine Learning SDK は Python 3.5.2 以降で動作します。ただし、自動化された機械学習コンポーネントは、Python 3.7 で完全には機能しません。  コンポーネントとパッケージがダウンロードされて環境が作成されるまでに数分かかります。

    ```shell
    conda create -n myenv python=3.6.5
    ```

1. 環境をアクティブにします。

    ```shell
    conda activate myenv
    ```

1. 環境固有の ipython カーネルを有効にします。

    ```shell
    conda install notebook ipykernel
    ```

    その後、カーネルを作成します。

    ```shell
    ipython kernel install --user
    ```

### <a name="install-the-sdk"></a>SDK のインストール

1. アクティブ化された conda 環境で、Jupyter Notebook 機能を備えた Machine Learning SDK の主要なコンポーネントをインストールします。 お使いのマシンの構成によっては、インストールが完了するまでに数分かかります。

    ```shell
    pip install --upgrade azureml-sdk[notebooks]
    ```

1. Azure Machine Learning チュートリアルにこの環境を使用するために、これらのパッケージをインストールします。

    ```shell
    conda install -y cython matplotlib pandas
    ```

1. Azure Machine Learning チュートリアルにこの環境を使用するために、自動化された機械学習コンポーネントをインストールします。

    ```shell
    pip install --upgrade azureml-sdk[automl]
    ```

> [!IMPORTANT]
> 一部のコマンドライン ツールでは、次のように引用符を追加することが必要になる場合があります。
> *  'azureml-sdk[notebooks]'
> * 'azureml-sdk[automl]'
>

### <a name='sdk-create'></a> SDK でワークスペースを作成する

Python SDK を使用して Jupyter Notebook にワークスペースを作成します。

1. クイック スタートとチュートリアルに使用するディレクトリを作成します。または、cd を実行してそこに移動します。

1. Jupyter Notebook を起動するには、次のコマンドを入力します。

    ```shell
    jupyter notebook
    ```

1. ブラウザー ウィンドウで、既定の `Python 3` カーネルを使用して新しいノートブックを作成します。 

1. SDK のバージョンを表示するには、ノートブックのセルに次の Python コードを入力し、実行します。

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=import)]

1. [Azure portal のサブスクリプションの一覧](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)で、`<azure-subscription-id>` パラメーターの値を見つけます。 自分のロールが所有者または共同作成者になっている任意のサブスクリプションを使用します。 ロールの詳細については、「[Azure Machine Learning ワークスペースへのアクセスの管理](how-to-assign-roles.md)」の記事をご覧ください。

   ```python
   from azureml.core import Workspace
   ws = Workspace.create(name='myworkspace',
                         subscription_id='<azure-subscription-id>', 
                         resource_group='myresourcegroup',
                         create_resource_group=True,
                         location='eastus2' 
                        )
   ```

   コードを実行するときに、Azure アカウントへのサインインを求められることがあります。 サインインすると、認証トークンがローカルにキャッシュされます。

1. 関連付けられているストレージ、コンテナー レジストリ、キー コンテナーなど、ワークスペースの詳細を表示するには、次のコードを入力します。

    [!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=getDetails)]


### <a name="write-a-configuration-file"></a>構成ファイルの記述

構成ファイル内のワークスペースの詳細を現在のディレクトリに保存します。 このファイルは *.azureml/config.json* という名前です。  

このワークスペース構成ファイルを使用すると、後で同じワークスペースを簡単に読み込むことができます。 コード `ws=Workspace.from_config()` を使用し、同じディレクトリまたはサブディレクトリ内の他のノートブックやスクリプトと共に読み込むことができます。 

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=writeConfig)]

この `write_config()` API 呼び出しでは、構成ファイルが現在のディレクトリに作成されます。 *config.json* ファイルには、以下が含まれています。

```json
{
    "subscription_id": "<azure-subscription-id>",
    "resource_group": "myresourcegroup",
    "workspace_name": "myworkspace"
}
```

> [!TIP]
> 他のディレクトリにある Python スクリプトや Jupyter Notebooks でワークスペースを使用するには、このファイルをそのディレクトリにコピーします。 ファイルは、同じディレクトリ内、*.azureml* という名前のサブディレクトリ内、親ディレクトリ内に置くことができます。

## <a name="resource-manager-template"></a>Resource Manager テンプレート

テンプレートでワークスペースを作成するには、「[テンプレートを使用して Azure Machine Learning service ワークスペースを作成する](how-to-create-workspace-template.md)」を参照してください

<a name="cli"></a>
## <a name="command-line-interface"></a>コマンド ライン インターフェイス

CLI でワークスペースを作成するには、「[Azure Machine Learning service 用 CLI 拡張機能の使用](reference-azure-machine-learning-cli.md)」を参照してください。

## <a name="clean-up-resources"></a>リソースのクリーンアップ 

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>次の手順

* 作成方法に関係なく、[Azure portal](https://portal.azure.com/) で自分のワークスペースを表示できます。  詳細については「[ワークスペースの表示](how-to-manage-workspace.md#view)」を参照してください。

* 次のクイック スタートとチュートリアルでワークスペースを試してください。

    * クイック スタート:[クラウドで Jupyter Notebook を実行する](quickstart-run-cloud-notebook.md)。
    * クイック スタート:[独自のサーバーで Jupyter Notebook を実行する](quickstart-run-local-notebook.md)。
    * 2 つの部分からなるチュートリアル:画像分類モードを[トレーニング](tutorial-train-models-with-aml.md)し、[デプロイ](tutorial-deploy-models-with-aml.md)する。
    * 2 つの部分からなるチュートリアル:[データを準備](tutorial-data-prep.md)し、[自動化された機械学習を使用](tutorial-auto-train-models.md)して回帰モデルをビルドする。

* 開発環境の構成方法は[こちら](how-to-configure-environment.md)にあります。

* Azure Machine Learning SDK for Python の詳細は[こちら](https://aka.ms/aml-sdk)にあります。
