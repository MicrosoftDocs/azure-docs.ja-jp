---
title: Python 開発環境をセットアップする
titleSuffix: Azure Machine Learning
description: Azure Machine Learning のための開発環境を構成する方法を学びます。 Conda 環境を使用し、構成ファイルを作成し、独自のクラウドベースのノートブック サーバー、Jupyter Notebook、Azure Databricks、IDE、コード エディター、および Data Science Virtual Machine を構成します。
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.date: 12/27/2019
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 1753373b360a78918682b5f6102dcc896e2d90c3
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88652640"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>Azure Machine Learning のための開発環境を構成する
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

この記事では、Azure Machine Learning を操作する開発環境を構成する方法について説明します。 Azure Machine Learning は、プラットフォームに依存しません。 開発環境のハード要件は Python 3 だけです。 Anaconda や Virtualenv などの分離環境もお勧めします。

次の表では、この記事で説明されている各開発環境と、その長所および短所を示します。

| 環境 | 長所 | 短所 |
| --- | --- | --- |
| [クラウドベースの Azure Machine Learning コンピューティング インスタンスを使用する (プレビュー)](#compute-instance) | 最も簡単に始められる方法です。 SDK 全体は既にワークスペース VM にインストールされており、ノートブックのチュートリアルは事前に複製され、実行準備ができています。 | 開発環境と依存関係を制御することはできません。 Linux VM では追加コストが発生します (VM を使用していない場合は料金を回避するために停止できます)。 [価格の詳細](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)を参照してください。 |
| [ローカル環境](#local) | 開発環境と依存関係を完全に制御できます。 任意のビルド ツール、環境、または IDE を使用できます。 | 始めるのに時間がかかります。 必要な SDK パッケージをインストールする必要があり、環境がまだない場合はそれもインストールする必要があります。 |
| [Azure Databricks](#aml-databricks) | スケーラブルな Apache Spark プラットフォームで大規模な集中型機械学習ワークフローを実行する場合に適しています。 | 実験用機械学習、または小規模な実験とワークフローでは過剰です。 Azure Databricks の追加コストが発生します。 [価格の詳細](https://azure.microsoft.com/pricing/details/databricks/)を参照してください。 |
| [Data Science Virtual Machine (DSVM)](#dsvm) | クラウドベースのコンピューティング インスタンスに似ていますが (Python と SDK はプレインストールされています)、その他の一般的なデータ サイエンスおよび機械学習ツールがプレインストールされています。 簡単にスケーリングし、他のカスタム ツールやワークフローと組み合わせることができます。 | クラウドベースのコンピューティング インスタンスと比較して、作業の開始に時間がかかります。 |

この記事では、次のツールに関する追加の使用ヒントについても説明します。

* [Jupyter Notebook](#jupyter):Jupyter Notebook を既に使用している場合、SDK にはインストールする必要のある追加機能がいくつかあります。

* [Visual Studio Code](#vscode):Visual Studio Code を使用する場合、[Azure Machine Learning 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai)には、Python の幅広い言語サポートに加えて、Azure Machine Learning を使いやすく、生産性を高めるための機能が含まれています。

## <a name="prerequisites"></a>前提条件

Azure Machine Learning ワークスペース。 ワークスペースを作成するには、[Azure Machine Learning ワークスペース](how-to-manage-workspace.md)の作成に関するページを参照してください。 独自の[クラウドベースのノートブック サーバー](#compute-instance)、[DSVM](#dsvm)、または [Azure Databricks](#aml-databricks) を使い始めるために必要なものは、ワークスペースだけです。

[ローカル コンピューター](#local)、[Jupyter Notebook サーバー](#jupyter)、または [Visual Studio Code](#vscode) 用の SDK 環境をインストールするには、以下も必要です。

- [Anaconda](https://www.anaconda.com/download/) または [Miniconda](https://conda.io/miniconda.html) パッケージ マネージャーのいずれか。

- Linux または macOS 上では、Bash シェルが必要です。

    > [!TIP]
    > Linux または macOS 上で Bash 以外のシェル (例: zsh) を使用している場合、一部のコマンドを実行するとエラーが発生する可能性があります。 この問題を回避するには、`bash` コマンドを使用して新しい Bash シェルを開始し、そこでコマンドを実行します。

- Windows の場合、コマンド プロンプトまたは Anaconda プロンプト (Anaconda および Miniconda によってインストール済み) が必要です。

## <a name="your-own-cloud-based-compute-instance"></a><a id="compute-instance"></a>独自のクラウドベースのコンピューティング インスタンス

Azure Machine Learning の[コンピューティング インスタンス (プレビュー) ](concept-compute-instance.md)は、セキュリティで保護された、クラウドベースの Azure ワークステーションであり、データ サイエンティスト向けに Jupyter Notebook サーバー、JupyterLab、完全に準備された ML 環境を提供します。

コンピューティング インスタンスでインストールまたは構成するものはありません。  Azure Machine Learning ワークスペース内から、いつでも作成できます。 名前を指定し、Azure VM の種類を指定するだけです。 今すぐお試しいただくには、「[チュートリアル:環境とワークスペースの設定に関するチュートリアル](tutorial-1st-experiment-sdk-setup.md)を参照)。

パッケージのインストール方法など、コンピューティング インスタンスの詳細については、[コンピューティング インスタンス](concept-compute-instance.md)に関する記事を参照してください。

コンピューティング料金の発生を停止するには、[コンピューティング インスタンス](tutorial-1st-experiment-sdk-train.md#clean-up-resources)を停止します。

## <a name="data-science-virtual-machine"></a><a id="dsvm"></a>Data Science Virtual Machine

DSVM は、カスタマイズされた仮想マシン (VM) イメージです。 これは、以下の項目が事前に構成されているデータ サイエンスの作業向けに設計されています。

  - TensorFlow、PyTorch、Scikit-learn、XGBoost、Azure Machine Learning SDK などのパッケージ
  - Spark スタンドアロンや Drill などの一般的なデータ サイエンス ツール
  - Azure CLI、AzCopy、Storage Explorer などの Azure ツール
  - Visual Studio Code や PyCharm などの統合開発環境 (IDE)
  - Jupyter Notebook Server

Azure Machine Learning SDK は、DSVM の Ubuntu バージョンまたは Windows バージョンで動作します。 ただし、DSVM をコンピューティング先としても使用することを計画している場合は、Ubuntu のみがサポートされます。

DSVM を開発環境として使用するには

1. 次のいずれかの環境で、DSVM を作成します。

    * Microsoft Azure portal:

        * [Ubuntu Data Science Virtual Machine を作成する](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)

        * [Windows Data Science Virtual Machine を作成する](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/provision-vm)

    * Azure CLI:

        > [!IMPORTANT]
        > * Azure CLI を使用するときは、最初に `az login` コマンドを使用して Azure サブスクリプションにサインインする必要があります。
        >
        > * この手順でコマンドを使用するときは、リソース グループ名、VM の名前、ユーザー名、パスワードを指定する必要があります。

        * Ubuntu Data Science Virtual Machine を作成するには、次のいずれかのコマンドを使用します。

            ```azurecli-interactive
            # create a Ubuntu DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            # If you need to create a new resource group use: "az group create --name YOUR-RESOURCE-GROUP-NAME --location YOUR-REGION (For example: westus2)"
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:linux-data-science-vm-ubuntu:linuxdsvmubuntu:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --generate-ssh-keys --authentication-type password
            ```

        * Windows Data Science Virtual Machine を作成するには、次のいずれかのコマンドを使用します。

            ```azurecli-interactive
            # create a Windows Server 2016 DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:dsvm-windows:server-2016:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --authentication-type password
            ```

2. Azure Machine Learning SDK は DSVM に既にインストールされています。 SDK を含む Conda 環境を使用するには、次のいずれかのコマンドを使用します。

    * Ubuntu DSVM の場合:

        ```bash
        conda activate py36
        ```

    * Windows DSVM の場合:

        ```bash
        conda activate AzureML
        ```

1. SDK にアクセスして、バージョンをチェックできることを確認するには、次の Python コードを使用します。

    ```python
    import azureml.core
    print(azureml.core.VERSION)
    ```

1. Azure Machine Learning ワークスペースを使用するよう DSVM を構成するには、「[ワークスペース構成ファイルを作成する](#workspace)」セクションを参照してください。

詳細については、「[Data Science Virtual Machines](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)」を参照してください。

## <a name="local-computer"></a><a id="local"></a>ローカル コンピューター

ローカル コンピューター (リモート仮想マシンの場合もある) を使用している場合は、Anaconda 環境を作成し、SDK をインストールします。 次に例を示します。

1. まだお持ちでない場合は、[Anaconda](https://www.anaconda.com/distribution/#download-section) (Python 3.7 バージョン) をダウンロードしてインストールしてください。

1. Anaconda Prompt を開き、次のコマンドを使用して環境を作成します。

    次のコマンドを実行して環境を作成します。

    ```bash
    conda create -n myenv python=3.7.7
    ```

    次に、その環境をアクティブにします。

    ```bash
    conda activate myenv
    ```

    この例では Python 3.7.7 を使用して環境を作成していますが、任意の特定のサブバージョンも選択できます。 特定のメジャー バージョンでは、SDK との互換性が保証されません (3.5+ を推奨)。エラーが発生する場合は、現在の Anaconda 環境で異なるバージョンまたはサブバージョンを試してみることをお勧めします。 コンポーネントとパッケージがダウンロードされて環境が作成されるまでに数分かかります。

1. 新しく作成した環境で次のコマンドを実行し、環境固有の IPython カーネルを有効にします。 このようにしておくと、Anaconda 環境内で Jupyter Notebook を操作する際の、必要なカーネルとパッケージのインポート動作を確実にすることができます。

    ```bash
    conda install notebook ipykernel
    ```

    それから、次のコマンドを実行してカーネルを作成します。

    ```bash
    ipython kernel install --user --name myenv --display-name "Python (myenv)"
    ```

1. 次のコマンドを使用して、パッケージをインストールします。

    このコマンドで、ベース Azure Machine Learning SDK が、notebook extra および `automl` extra と一緒にインストールされます。 `automl` extra は大規模なインストールになるので、自動化された機械学習の実験を実行する予定がない場合はブラケットから削除できます。 `automl` extra には、既定で Azure Machine Learning Data Prep SDK も依存関係として含まれます。

    ```bash
    pip install azureml-sdk[notebooks,automl]
    ```

   > [!NOTE]
   > * PyYAML をアンインストールできないというメッセージが表示された場合は、代わりに次のコマンドを使用してください。
   >
   >   `pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML`
   >
   > * macOS Catalina 以降では、zsh (Z シェル) が既定のログイン シェルおよび対話型シェルです。 zsh で次のコマンドを使用します。ここでは角かっこを "\\" (バックスラッシュ) でエスケープしています。
   >
   >   `pip install --upgrade azureml-sdk\[notebooks,automl\]`

   SDK をインストールするには数分かかります。 インストール オプションの詳細については、[インストール ガイド](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)を参照してください。

1. 機械学習の実験に必要な他のパッケージをインストールします。

    次のコマンドのどちらかを使用します。その場合、 *\<new package>* の部分を、インストールするパッケージに置き換えてください。 `conda install` からパッケージをインストールするには、そのパッケージが現在のチャネルの一部である必要があります (Anaconda クラウドに新しいチャネルを追加できます)。

    ```bash
    conda install <new package>
    ```

    または、`pip` からパッケージをインストールすることもできます。

    ```bash
    pip install <new package>
    ```

### <a name="jupyter-notebooks"></a><a id="jupyter"></a>Jupyter Notebooks

Jupyter Notebook は、[Jupyter プロジェクト](https://jupyter.org/)の一部です。 これらは、ライブ コードと説明のテキストとグラフィックスが混在するドキュメントを作成する対話型のコーディング エクスペリエンスを提供します。 また、Jupyter Notebook は、ドキュメントにコード セクションの出力を保存できるので、結果を他のユーザーと共有する優れた方法です。 Jupyter Notebook は、さまざまなプラットフォームにインストールできます。

「[ローカル コンピューター](#local)」のセクションにある手順を実行すると、Anaconda 環境で Jupyter Notebook を実行するのに必要なコンポーネントがインストールされます。

Jupyter Notebook 環境内でこれらのコンポーネントを有効にするには

1. Anaconda プロンプトを開いて、環境をアクティブにします。

    ```bash
    conda activate myenv
    ```

1. サンプル ノートブックのセットに対して [GitHub リポジトリ](https://github.com/Azure/MachineLearningNotebooks)を複製します。

    ```bash
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. 次のコマンドを使用して、Jupyter Notebook サーバーを起動します。

    ```bash
    jupyter notebook
    ```

1. Jupyter Notebook で SDK を使用できることを確認するには、**新しい**ノートブックを作成し、カーネルとして **Python 3** を選択して、ノートブックのセルで次のコマンドを実行します。

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. モジュールのインポートで問題が発生して `ModuleNotFoundError` を受信する場合は、ノートブックのセルで次のコードを実行して、Jupyter カーネルが環境に適したパスに接続されていることを確認します。

    ```python
    import sys
    sys.path
    ```

1. Azure Machine Learning ワークスペースを使用するよう Jupyter Notebook を構成するには、「[ワークスペース構成ファイルを作成する](#workspace)」セクションを参照してください。

### <a name="visual-studio-code"></a><a id="vscode"></a>Visual Studio Code

Visual Studio Code は、[Visual Studio マーケットプレース](https://marketplace.visualstudio.com/vscode)で利用可能な拡張機能を通じて、幅広いプログラミング言語とツールのセットをサポートする、非常に一般的なクロス プラットフォームのコード エディターです。 [Azure Machine Learning 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai)では、すべての種類の Python 環境 (仮想、Anaconda など) でコーディングを行うために、[Python 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-python.python)がインストールされます。 さらに、これにより、Visual Studio Code を離れることなく、Azure Machine Learning リソースを操作し、Azure Machine Learning 実験をすべて実行するための便利な機能が提供されます。

開発に Visual Studio Code を使用するには

1. Visual Studio Code 用の Azure Machine Learning 拡張機能をインストールするには、「[Azure Machine Learning](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai)」を参照してください。

    詳細については、[Visual Studio Code 用の Azure Machine Learning の使用](tutorial-setup-vscode-extension.md)に関するページを参照してください。

1. あらゆる種類の Python 開発に Visual Studio Code を使用する方法については、「[VSCode での Python の使用](https://code.visualstudio.com/docs/python/python-tutorial)」を参照してください。

    - SDK を含む SDK Python 環境を選択するには、VS Code を開いてから、Ctrl+Shift+P キー (Linux と Windows) または Command+Shift+P キー (Mac) を選択します。
        - __コマンド パレット__ が開きます。

    - 「__Python:Select Interpreter__」と入力した後、適切な環境を選択します。

1. SDK を使用できることを確認するには、次のコードを含む新しい Python ファイル (.py) を作成します。

    ```python
    #%%
    import azureml.core
    azureml.core.VERSION
    ```
    このコードを実行するには、[セルの実行] CodeLens をクリックするか、単に Shift+Enter キーを押します。
<a name="aml-databricks"></a>

## <a name="azure-databricks"></a>Azure Databricks
Azure Databricks は、Azure クラウド内の Apache Spark ベースの環境です。 これは、CPU または GPU ベースのコンピューティング クラスターを備え、コラボレーションに適した Notebook ベースの環境を提供します。

Azure Databricks が Azure Machine Learning と連携する仕組み:
+ Spark MLlib を使用してモデルをトレーニングし、そのモデルを Azure Databricks 内から ACI/AKS にデプロイできます。
+ また、Azure Databricks を使用して、特別な Azure ML SDK で[自動化された機械学習](concept-automated-ml.md)機能を使用することもできます。
+ Azure Databricks は、[Azure Machine Learning パイプライン](concept-ml-pipelines.md)からのコンピューティング先として使用できます。

### <a name="set-up-your-databricks-cluster"></a>Databricks クラスターを設定する

[Databricks クラスター](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)を作成します。 一部の設定は、Databricks に自動化された機械学習用の SDK をインストールする場合にのみ適用されます。
**クラスターの作成には数分かかります。**

次の設定を使用します。

| 設定 |適用対象| 値 |
|----|---|---|
| クラスター名 |常時| yourclustername |
| Databricks ランタイム |常時|ML 以外のランタイム 6.5 (scala 2.11、spark 2.4.3) |
| Python バージョン |常時| 3 |
| ワーカー |常時| 2 以上 |
| ワーカー ノードの VM の種類 <br>(同時実行反復処理の最大数を決定) |自動化された ML<br>のみ| メモリ最適化 VM 優先 |
| 自動スケールの有効化 |自動化された ML<br>のみ| オフ |

クラスターが実行中になるまで待機してから、次に進んでください。

### <a name="install-the-correct-sdk-into-a-databricks-library"></a>Databricks ライブラリに適切な SDK をインストールする
クラスターが実行されたら、[ライブラリを作成](https://docs.databricks.com/user-guide/libraries.html#create-a-library)して、適切な Azure Machine Learning SDK パッケージをクラスターにアタッチします。

1. ライブラリを保存する現在のワークスペース フォルダーを右クリックします。 **[作成]**  >  **[ライブラリ]** の順に選択します。

1. オプションを **1 つだけ**選択します (他の SDK インストールはサポート対象外)。

   |SDK&nbsp;パッケージ&nbsp;extras|source|PyPi&nbsp;名&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
   |----|---|---|
   |Databricks 用| Python Egg または PyPI のアップロード | azureml-sdk[databricks]|
   |Databricks 用 (<br> 自動化された ML 機能付)| Python Egg または PyPI のアップロード | azureml-sdk[automl]|

   > [!Warning]
   > 他の SDK extras はインストールできません。 前述の [databricks] または [automl] オプションのどちらか一方のみを選択します。

   * **[すべてのクラスターに自動的にアタッチする]** は選択しないでください。
   * クラスター名の横にある **[アタッチ]** を選択します。

1. 状態が **[アタッチ済み]** に変わるまで、エラーを監視します。これには数分かかる場合があります。  このステップが失敗した場合は、次の操作を行います。

   次のようにして、クラスターを再起動してみます。
   1. 左側のウィンドウで、 **[クラスター]** を選択します。
   1. テーブルでクラスター名を選択します。
   1. **[ライブラリ]** タブで **[再起動]** を選択します。

   次の点も考慮します。
   + AutoML 構成では、Azure Databricks を使用するときに次のパラメーターを追加してください。
       1. ```max_concurrent_iterations``` は、ご利用のクラスター内のワーカー ノードの数に基づきます。
        2. ```spark_context=sc``` は、既定の Spark コンテキストに基づきます。
   + または、古いバージョンの SDK がある場合は、クラスターのインストール済みライブラリでその SDK の選択を解除し、ごみ箱に移動します。 新しいバージョンの SDK をインストールし、クラスターを再起動します。 再起動後に問題がある場合は、クラスターをデタッチし、再アタッチします。

インストールが成功した場合、インポートされたライブラリは次のどちらかのような外観になります。

自動化された機械学習機能を**_持たない_** Databricks 用 SDK ![Databricks 用 Azure Machine Learning SDK](./media/how-to-configure-environment/amlsdk-withoutautoml.jpg)

自動化された機械学習機能を**持つ** Databricks 用 SDK ![自動化された機械学習機能が Databricks にインストールされた SDK](./media/how-to-configure-environment/automlonadb.png)

### <a name="start-exploring"></a>実際に使ってみる

実際に試してみましょう。
+ サンプル ノートブックがたくさんありますが、**Azure Databricks で動作するのは[これらのサンプル ノートブック](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks)のみです。**

+ これらのサンプルは、ワークスペースから直接インポートしてください。 次を参照してください。![インポートの選択](./media/how-to-configure-environment/azure-db-screenshot.png)
![パネルのインポ―ト](./media/how-to-configure-environment/azure-db-import.png)

+ [トレーニング コンピューティングとして Databricks を使用してパイプラインを作成](how-to-create-your-first-pipeline.md)する方法を確認します。

## <a name="create-a-workspace-configuration-file"></a><a id="workspace"></a>ワークスペース構成ファイルを作成する

ワークスペース構成ファイルは、Azure Machine Learning ワークスペースと通信する方法を SDK に示す JSON ファイルです。 ファイルの名前は *config.json* で、形式は次のとおりです。

```json
{
    "subscription_id": "<subscription-id>",
    "resource_group": "<resource-group>",
    "workspace_name": "<workspace-name>"
}
```

この JSON ファイルは、Python スクリプトまたは Jupyter Notebook を含むディレクトリ構造内にある必要があります。 それは、同じディレクトリ内、 *.azureml* という名前のサブディレクトリ内、または親ディレクトリ内に置くことができます。

コードからこのファイルを使用するには、`ws=Workspace.from_config()` を使用します。 このコードは、ファイルから情報を読み込み、ワークスペースに接続します。

構成ファイルは 3 つの方法で作成できます。

* **[ws.write_config](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) を使用する**: *config.json* ファイルを描き込みます。 このファイルには、ワークスペースの構成情報が含まれています。 *config.json* を他の開発環境にダウンロードまたはコピーできます。

* **ファイルをダウンロードする**:[Azure portal](https://ms.portal.azure.com) で、お使いのワークスペースの **[概要]** セクションから **[config.json をダウンロード]** を選択します。

     ![Azure portal](./media/how-to-configure-environment/configure.png)

* **ファイルをプログラムで作成する**:次のコード スニペットでは、サブスクリプション ID、リソース グループ、ワークスペース名を指定して、ワークスペースに接続します。 その後、ワークスペースの構成をファイルに保存します。

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

    このコードでは、構成ファイルが *.azureml/config.json* ファイルに書き込まれます。

## <a name="next-steps"></a>次のステップ

- MNIST データセットを使用して Azure Machine Learning 上で[モデルをトレーニングする](tutorial-train-models-with-aml.md)
- [Azure Machine Learning SDK for Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) リファレンスを表示する
