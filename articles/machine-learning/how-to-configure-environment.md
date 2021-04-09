---
title: Python 開発環境をセットアップする
titleSuffix: Azure Machine Learning
description: Jupyter Notebook、Visual Studio Code、Azure Databricks、Data Science Virtual Machine で、Azure Machine Learning の Python 開発環境をセットアップします。
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.date: 11/16/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperf-fy21q1, devx-track-azurecli
ms.openlocfilehash: 7e493404e7a36b60e8f9e62cd4ac2f2b32845bb9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102501619"
---
# <a name="set-up-a-python-development-environment-for-azure-machine-learning"></a>Azure Machine Learning のために Python 開発環境をセットアップする

Azure Machine Learning のために Python 開発環境を構成する方法について説明します。

次の表では、この記事で説明されている各開発環境と、その長所および短所を示します。

| 環境 | 長所 | 短所 |
| --- | --- | --- |
| [ローカル環境](#local) | 開発環境と依存関係を完全に制御できます。 任意のビルド ツール、環境、または IDE を使用できます。 | 始めるのに時間がかかります。 必要な SDK パッケージをインストールする必要があり、環境がまだない場合はそれもインストールする必要があります。 |
| [Data Science Virtual Machine (DSVM)](#dsvm) | クラウドベースのコンピューティング インスタンスに似ていますが (Python と SDK はプレインストールされています)、その他の一般的なデータ サイエンスおよび機械学習ツールがプレインストールされています。 簡単にスケーリングし、他のカスタム ツールやワークフローと組み合わせることができます。 | クラウドベースのコンピューティング インスタンスと比較して、作業の開始に時間がかかります。 |
| [Azure Machine Learning コンピューティング インスタンス](#compute-instance) | 最も簡単に始められる方法です。 SDK 全体は既にワークスペース VM にインストールされており、ノートブックのチュートリアルは事前に複製され、実行準備ができています。 | 開発環境と依存関係を制御することはできません。 Linux VM では追加コストが発生します (VM を使用していない場合は料金を回避するために停止できます)。 [価格の詳細](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)を参照してください。 |
| [Azure Databricks](how-to-configure-databricks-automl-environment.md) | スケーラブルな Apache Spark プラットフォームで大規模な集中型機械学習ワークフローを実行する場合に適しています。 | 実験用機械学習、または小規模な実験とワークフローでは過剰です。 Azure Databricks の追加コストが発生します。 [価格の詳細](https://azure.microsoft.com/pricing/details/databricks/)を参照してください。 |

この記事では、次のツールに関する追加の使用ヒントについても説明します。

* Jupyter Notebook: Jupyter Notebooks を既に使用している場合、SDK にはインストールすべきいくつかの追加機能があります。

* Visual Studio Code:Visual Studio Code を使用する場合、[Azure Machine Learning 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai)には、Python の幅広い言語サポートに加えて、Azure Machine Learning を使いやすく、生産性を高めるための機能が含まれています。

## <a name="prerequisites"></a>前提条件

* Azure Machine Learning ワークスペース。 Azure Machine Learning ワークスペースがない場合は、[Azure portal](how-to-manage-workspace.md)、[Azure CLI](how-to-manage-workspace-cli.md#create-a-workspace)、[Azure Resource Manager テンプレート](how-to-create-workspace-template.md)を使用して作成できます。

### <a name="local-and-dsvm-only-create-a-workspace-configuration-file"></a><a id="workspace"></a>ローカルおよび DSVM のみ:ワークスペース構成ファイルを作成する

ワークスペース構成ファイルは、Azure Machine Learning ワークスペースと通信する方法を SDK に示す JSON ファイルです。 ファイルの名前は *config.json* で、形式は次のとおりです。

```json
{
    "subscription_id": "<subscription-id>",
    "resource_group": "<resource-group>",
    "workspace_name": "<workspace-name>"
}
```

この JSON ファイルは、Python スクリプトまたは Jupyter Notebook を含むディレクトリ構造内にある必要があります。 それは、同じディレクトリ内、 *.azureml* という名前のサブディレクトリ内、または親ディレクトリ内に置くことができます。

コードからこのファイルを使用するには、[`Workspace.from_config`](/python/api/azureml-core/azureml.core.workspace.workspace#from-config-path-none--auth-none---logger-none---file-name-none-) メソッドを使用します。 このコードは、ファイルから情報を読み込み、ワークスペースに接続します。

次のいずれかの方法で、ワークスペース構成ファイルを作成します。

* Azure portal

    **ファイルをダウンロードする**:[Azure portal](https://ms.portal.azure.com) で、お使いのワークスペースの **[概要]** セクションから **[config.json をダウンロード]** を選択します。

    ![Azure portal](./media/how-to-configure-environment/configure.png)

* Azure Machine Learning Python SDK

    Azure Machine Learning ワークスペースに接続するためのスクリプトを作成し、[`write_config`](/python/api/azureml-core/azureml.core.workspace.workspace#write-config-path-none--file-name-none-) メソッドを使用してファイルを生成し、それを *.azureml/config.json* として保存します。 必ず `subscription_id`、`resource_group`、`workspace_name` を独自の値に置き換えてください。

    ```python
    from azureml.core import Workspace

    subscription_id = '<subscription-id>'
    resource_group  = '<resource-group>'
    workspace_name  = '<workspace-name>'

    try:
        ws = Workspace(subscription_id = subscription_id, resource_group = resource_group, workspace_name = workspace_name)
        ws.write_config()
        print('Library configuration succeeded')
    except:
        print('Workspace not found')
    ```

## <a name="local-computer-or-remote-vm-environment"></a><a id="local"></a>ローカル コンピューターまたはリモート VM 環境

ローカル コンピューターまたはリモート仮想マシン (Azure Machine Learning コンピューティング インスタンスや Data Science VM など) に環境をセットアップすることができます。 

ローカルの開発環境またはリモート VM を構成するには:

1. Python 仮想環境を作成します (virtualenv、conda)。

    > [!NOTE]
    > 必須ではありませんが、Python 仮想環境の管理とパッケージのインストールのためには、[Anaconda](https://www.anaconda.com/download/) または [Miniconda](https://www.anaconda.com/download/) を使用することが推奨されます。

    > [!IMPORTANT]
    > Linux または macOS 上で Bash 以外のシェル (例: zsh) を使用している場合、一部のコマンドを実行するとエラーが発生する可能性があります。 この問題を回避するには、`bash` コマンドを使用して新しい Bash シェルを開始し、そこでコマンドを実行します。

1. 新しく作成された Python 仮想環境をアクティブにします。
1. [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/install) をインストールします。
1. Azure Machine Learning ワークスペースを使用するようにローカル環境を構成するには、[ワークスペース構成ファイルを作成する](#workspace)か、既存のものを使用します。

ローカル環境をセットアップしたので、Azure Machine Learning の操作を開始する準備ができました。 使用を開始するには、[Azure Machine Learning Python のファースト ステップ ガイド](tutorial-1st-experiment-sdk-setup-local.md) を参照してください。

### <a name="jupyter-notebooks"></a><a id="jupyter"></a>Jupyter Notebooks

ローカル Jupyter Notebook サーバーを実行する場合は、Python 仮想環境用の IPython カーネルを作成することが推奨されます。 これは、予期されるカーネルおよびパッケージのインポート動作が確実に行われるようにするのに役立ちます。

1. 環境固有の IPython カーネルを有効にします

    ```bash
    conda install notebook ipykernel
    ```

1. Python 仮想環境用のカーネルを作成します。 `<myenv>` は必ず、Python 仮想環境の名前に置き換えてください。

    ```bash
    ipython kernel install --user --name <myenv> --display-name "Python (myenv)"
    ```

1. Jupyter Notebook を起動します

Azure Machine Learning と Jupyter Notebook の使用を開始するには、[Azure Machine Learning ノートブックのリポジトリ](https://github.com/Azure/MachineLearningNotebooks)を参照してください。

> [!NOTE]
> コミュニティ主導の例のリポジトリについては、 https://github.com/Azure/azureml-examples を参照してください。

### <a name="visual-studio-code"></a><a id="vscode"></a>Visual Studio Code

開発に Visual Studio Code を使用するには

1. [Visual Studio Code](https://code.visualstudio.com/Download) をインストールします。
1. [Visual Studio Code の Azure Machine Learning 拡張機能](tutorial-setup-vscode-extension.md) (プレビュー) をインストールします。

Visual Studio Code の拡張機能をインストールしたら、[Azure Machine Learning リソース](how-to-manage-resources-vscode.md)の管理、[実験の実行とデバッグ](how-to-debug-visual-studio-code.md)、[トレーニング済みモデルのデプロイ](tutorial-train-deploy-image-classification-model-vscode.md)を行うことができます。

## <a name="azure-machine-learning-compute-instance"></a><a id="compute-instance"></a>Azure Machine Learning コンピューティング インスタンス

Azure Machine Learning の[コンピューティング インスタンス](concept-compute-instance.md)は、セキュリティで保護された、クラウドベースの Azure ワークステーションであり、データ サイエンティストに、Jupyter Notebook サーバー、JupyterLab、フル マネージドの機械学習環境を提供します。

コンピューティング インスタンスでインストールまたは構成するものはありません。  

Azure Machine Learning ワークスペース内から、いつでも作成できます。 名前を指定し、Azure VM の種類を指定するだけです。 今すぐお試しいただくには、「[チュートリアル:環境とワークスペースの設定に関するチュートリアル](tutorial-1st-experiment-sdk-setup.md)を参照)。

パッケージのインストール方法など、コンピューティング インスタンスの詳細については、「[Azure Machine Learning コンピューティング インスタンスを作成して管理する](how-to-create-manage-compute-instance.md)」を参照してください。

> [!TIP]
> 未使用のコンピューティング インスタンスに対する課金が発生しないようにするには、[コンピューティング インスタンス](how-to-create-manage-compute-instance.md#manage)を停止します。

コンピューティング インスタンスは、Jupyter Notebook サーバーと JupyterLab に加えて、[Azure Machine Learning スタジオの内部に統合されたノートブック機能](how-to-run-jupyter-notebooks.md)で使用できます。

また、Visual Studio Code の Azure Machine Learning 拡張機能を使用して、[Azure Machine Learning コンピューティング インスタンスをリモート Jupyter Notebook サーバー](how-to-set-up-vs-code-remote.md#configure-compute-instance-as-remote-notebook-server)として構成することもできます。

## <a name="data-science-virtual-machine"></a><a id="dsvm"></a>Data Science Virtual Machine

Data Science VM は、開発環境として使用できる、カスタマイズされた仮想マシン (VM) イメージです。 これはデータ サイエンス作業向けに設計されていて、以下のようなツールとソフトウェアが事前に構成されています。

  - TensorFlow、PyTorch、Scikit-learn、XGBoost、Azure Machine Learning SDK などのパッケージ
  - Spark スタンドアロンや Drill などの一般的なデータ サイエンス ツール
  - Azure CLI、AzCopy、Storage Explorer などの Azure ツール
  - Visual Studio Code や PyCharm などの統合開発環境 (IDE)
  - Jupyter Notebook Server

ツールの包括的な一覧については、[Data Science VM ツール ガイド](data-science-virtual-machine/tools-included.md)に関する記事を参照してください。

> [!IMPORTANT]
> Data Science VM をトレーニングまたは推論ジョブのための[コンピューティング先](concept-compute-target.md)として使用する予定の場合は、Ubuntu のみがサポートされます。

Data Science VM を開発環境として使用するには:

1. 以下のいずれかの方法を使用して Data Science VM を作成します。

    * Azure portal を使用して、[Ubuntu](data-science-virtual-machine/dsvm-ubuntu-intro.md) または [Windows](data-science-virtual-machine/provision-vm.md) DSVM を作成します。
    * [ARM テンプレートを使用して Data Science VM を作成します](data-science-virtual-machine/dsvm-tutorial-resource-manager.md)。
    * Azure CLI の使用

        Ubuntu Data Science VM を作成するには、次のコマンドを使用します。

        ```azurecli-interactive
        # create a Ubuntu Data Science VM in your resource group
        # note you need to be at least a contributor to the resource group in order to execute this command successfully
        # If you need to create a new resource group use: "az group create --name YOUR-RESOURCE-GROUP-NAME --location YOUR-REGION (For example: westus2)"
        az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:linux-data-science-vm-ubuntu:linuxdsvmubuntu:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --generate-ssh-keys --authentication-type password
        ```

        Windows DSVM を作成するには、次のコマンドを使用します。

        ```azurecli-interactive
        # create a Windows Server 2016 DSVM in your resource group
        # note you need to be at least a contributor to the resource group in order to execute this command successfully
        az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:dsvm-windows:server-2016:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --authentication-type password
        ```

1. Azure Machine Learning SDK を含む conda 環境をアクティブにします。

    * Ubuntu Data Science VM の場合:

        ```bash
        conda activate py36
        ```

    * Windows Data Science VM の場合:

        ```bash
        conda activate AzureML
        ```

1. Azure Machine Learning ワークスペースを使用するように Data Science VM を構成するには、[ワークスペース構成ファイルを作成する](#workspace)か、既存のものを使用します。

ローカル環境と同様に、Visual Studio Code と、[Visual Studio Code の Azure Machine Learning 拡張機能](#vscode)を使用して、Azure Machine Learning を操作できます。

詳細については、「[Data Science Virtual Machines](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)」を参照してください。


## <a name="next-steps"></a>次のステップ

- MNIST データセットを使用して Azure Machine Learning で[モデルをトレーニング](tutorial-train-models-with-aml.md)します。
- [Azure Machine Learning SDK for Python のリファレンス](/python/api/overview/azure/ml/intro)に関するページを参照してください。 