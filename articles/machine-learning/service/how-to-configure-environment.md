---
title: Python 開発環境をセットアップする
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning service で作業する場合の、開発環境を構成する方法について説明します。 この記事では、Conda 環境の使用方法、構成ファイルの作成方法、独自のクラウドベースのノートブック サーバー、Jupyter Notebook、Azure Databricks、Azure Notebooks、IDE、コード エディター、および Data Science Virtual Machine の構成方法を説明します。
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.topic: conceptual
ms.date: 07/31/2019
ms.custom: seodec18
ms.openlocfilehash: d2f60b496594946e9175ecf5c1948b08c9065b1b
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2019
ms.locfileid: "68848194"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>Azure Machine Learning のための開発環境を構成する

この記事では、Azure Machine Learning service を操作する開発環境を構成する方法について説明します。 Azure Machine Learning service は、プラットフォームに依存しません。 開発環境のハード要件は Python 3 だけです。 Anaconda や Virtualenv などの分離環境もお勧めします。

次の表では、この記事で説明されている各開発環境と、その長所および短所を示します。

| 環境 | 長所 | 短所 |
| --- | --- | --- |
| [クラウドベースのノートブック VM](#notebookvm) | 最も簡単に始められる方法です。 SDK 全体は既にワークスペース VM にインストールされており、ノートブックのチュートリアルは事前に複製され、実行準備ができています。 | 開発環境と依存関係を制御することはできません。 Linux VM では追加コストが発生します (VM を使用していない場合は料金を回避するために停止できます)。 [価格の詳細](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)を参照してください。 |
| [ローカル環境](#local) | 開発環境と依存関係を完全に制御できます。 任意のビルド ツール、環境、または IDE を使用できます。 | 始めるのに時間がかかります。 必要な SDK パッケージをインストールする必要があり、環境がまだない場合はそれもインストールする必要があります。 |
| [Azure Databricks](#aml-databricks) | スケーラブルな Apache Spark プラットフォームで大規模な集中型機械学習ワークフローを実行する場合に適しています。 | 実験用機械学習、または小規模な実験とワークフローでは過剰です。 Azure Databricks の追加コストが発生します。 [価格の詳細](https://azure.microsoft.com/pricing/details/databricks/)を参照してください。 |
| [Data Science Virtual Machine (DSVM)](#dsvm) | クラウドベースのノートブック VM に似ていますが (Python と SDK はプレインストールされています)、その他の一般的なデータ サイエンスおよび機械学習ツールがプレインストールされています。 簡単にスケーリングし、他のカスタム ツールやワークフローと組み合わせることができます。 | クラウドベースのクラウドベース VM と比較して、作業の開始に時間がかかります。 |
| [Azure Notebooks](#aznotebooks) | Python と SDK がプレインストールされた、無料で軽量の使用開始エクスペリエンスです。 | クラウドベースのノートブック VM と比較して、使用可能な VM は非力です。 ワークスペースや他のリソースから分離されます。 |

この記事では、次のツールに関する追加の使用ヒントについても説明します。

* [Jupyter Notebook](#jupyter):Jupyter Notebook を既に使用している場合、SDK にはインストールする必要のある追加機能がいくつかあります。

* [Visual Studio Code](#vscode):Visual Studio Code を使用する場合は、いくつかの有用な拡張機能をインストールできます。

## <a name="prerequisites"></a>前提条件

Azure Machine Learning ワークスペース。 ワークスペースを作成するには、「[Create an Azure Machine Learning service workspace](how-to-manage-workspace.md)」 (Azure Machine Learning サービス ワークスペースの作成) を参照してください。 独自の[クラウドベースのノートブック サーバー](#notebookvm)、[DSVM](#dsvm)、[Azure Databricks](#aml-databricks)、または [Azure Notebooks](#aznotebooks) を使い始めるために必要なものは、ワークスペースだけです。

[ローカル コンピューター](#local)、[Jupyter Notebook サーバー](#jupyter)、または [Visual Studio Code](#vscode) 用の SDK 環境をインストールするには、以下も必要です。

- [Anaconda](https://www.anaconda.com/download/) または [Miniconda](https://conda.io/miniconda.html) パッケージ マネージャーのいずれか。

- Linux または macOS 上では、Bash シェルが必要です。

    > [!TIP]
    > Linux または macOS 上で Bash 以外のシェル (例: zsh) を使用している場合、一部のコマンドを実行するとエラーが発生する可能性があります。 この問題を回避するには、`bash` コマンドを使用して新しい Bash シェルを開始し、そこでコマンドを実行します。

- Windows の場合、コマンド プロンプトまたは Anaconda プロンプト (Anaconda および Miniconda によってインストール済み) が必要です。

## <a id="notebookvm"></a>独自のクラウドベースのノートブック VM

ノートブック仮想マシン (プレビュー) は、セキュリティで保護された、クラウドベースの Azure ワークステーションであり、データ サイエンティスト向けに Jupyter Notebook サーバー、JupyterLab、完全に準備された ML 環境を提供します。

ノートブック VM の特徴:

+ **セキュリティ保護**。 VM とノートブックのアクセスは既定で HTTPS と Azure Active Directory によってセキュリティで保護されているため、IT 技術者はシングル サインオンや多要素認証などの他のセキュリティ機能を簡単に実装できます。

+ **事前構成済み**。 必要なものが揃ったこの Python ML 環境は、人気のある IaaS Data Science VM に基づいており、次のものが含まれています。
  + Azure ML Python SDK (最新版)
  + ワークスペースで作業するための自動構成
  + Jupyter Notebook サーバー
  + JupyterLab ノートブック IDE
  + 事前構成済みの GPU ドライバー
  + 複数のディープ ラーニング フレームワーク


  コードを書く場合、VM にはチュートリアルやサンプルが含まれているため、Azure Machine Learning service を使用する方法について調べて学習することができます。 サンプル ノートブックはワークスペースの Azure Blob Storage アカウントに保存されており、VM 間で共有することができます。 実行されると、データ ストアと、ワークスペースのコンピューティング リソースにもアクセスできます。

+ **設定が簡単**:Azure Machine Learning ワークスペース内から、いつでも作成できます。 名前を指定し、Azure VM の種類を指定するだけです。 今すぐお試しいただくには、「[チュートリアル:環境とワークスペースを設定する](tutorial-1st-experiment-sdk-setup.md)」を完了することです。

+ **カスタマイズ可能**。 セキュリティで保護されたマネージド VM オファリングであるにも関わらず、ハードウェアの機能へのフル アクセスが維持されており、望みどおりにカスタマイズできます。 たとえば、最新の NVidia V100 を使用する VM をすばやく作成し、新しいニューラル ネットワーク アーキテクチャをステップバイステップでデバッグできます。

ノートブック VM の課金を停止するには、[ノートブック VM を停止します](tutorial-1st-experiment-sdk-setup.md#stop-the-notebook-vm)。 

## <a id="dsvm"></a>Data Science Virtual Machine

DSVM は、カスタマイズされた仮想マシン (VM) イメージです。 これは、以下の項目が事前に構成されているデータ サイエンスの作業向けに設計されています。

  - TensorFlow、PyTorch、Scikit-learn、XGBoost、Azure Machine Learning SDK などのパッケージ
  - Spark スタンドアロンや Drill などの一般的なデータ サイエンス ツール
  - Azure CLI、AzCopy、Storage Explorer などの Azure ツール
  - Visual Studio Code や PyCharm などの統合開発環境 (IDE)
  - Jupyter Notebook Server

Azure Machine Learning SDK は、DSVM の Ubuntu バージョンまたは Windows バージョンで動作します。 ただし、DSVM をコンピューティング先としても使用することを計画している場合は、Ubuntu のみがサポートされます。

DSVM を開発環境として使用するには、以下の手順を実行します。

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

            ```azurecli
            # create a Ubuntu DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            # If you need to create a new resource group use: "az group create --name YOUR-RESOURCE-GROUP-NAME --location YOUR-REGION (For example: westus2)"
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:linux-data-science-vm-ubuntu:linuxdsvmubuntu:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --generate-ssh-keys --authentication-type password
            ```

        * Windows Data Science Virtual Machine を作成するには、次のいずれかのコマンドを使用します。

            ```azurecli
            # create a Windows Server 2016 DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:dsvm-windows:server-2016:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --authentication-type password
            ```

2. Azure Machine Learning SDK は DSVM に既にインストールされています。 SDK を含む Conda 環境を使用するには、次のいずれかのコマンドを使用します。

    * Ubuntu DSVM の場合:

        ```shell
        conda activate py36
        ```

    * Windows DSVM の場合:

        ```shell
        conda activate AzureML
        ```

1. SDK にアクセスして、バージョンをチェックできることを確認するには、次の Python コードを使用します。

    ```python
    import azureml.core
    print(azureml.core.VERSION)
    ```

1. Azure Machine Learning service ワークスペースを使用するよう DSVM を構成するには、「[ワークスペース構成ファイルを作成する](#workspace)」セクションを参照してください。

詳細については、「[Data Science Virtual Machines](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)」を参照してください。

## <a id="local"></a>ローカル コンピューター

ローカル コンピューター (リモート仮想マシンの場合もある) を使用している場合は、以下の手順を実行して、Anaconda 環境を作成し、SDK をインストールします。

1. まだお持ちでない場合は、[Anaconda](https://www.anaconda.com/distribution/#download-section) (Python 3.7 バージョン) をダウンロードしてインストールしてください。

1. Anaconda Prompt を開き、次のコマンドを使用して環境を作成します。

    次のコマンドを実行して環境を作成します。

    ```shell
    conda create -n myenv python=3.6.5
    ```

    次に、その環境をアクティブにします。

    ```shell
    conda activate myenv
    ```

    この例では Python 3.6.5 を使用して環境を作成していますが、任意の特定のサブバージョンも選択できます。 特定のメジャー バージョンでは、SDK との互換性が保証されません (3.5+ を推奨)。エラーが発生する場合は、現在の Anaconda 環境で異なるバージョンまたはサブバージョンを試してみることをお勧めします。 コンポーネントとパッケージがダウンロードされて環境が作成されるまでに数分かかります。

1. 新しく作成した環境で次のコマンドを実行し、環境固有の ipython カーネルを有効にします。 このようにしておくと、Anaconda 環境内で Jupyter Notebook を操作する際の、必要なカーネルとパッケージのインポート動作を確実にすることができます。

    ```shell
    conda install notebook ipykernel
    ```

    それから、次のコマンドを実行してカーネルを作成します。

    ```shell
    ipython kernel install --user
    ```

1. 次のコマンドを使用して、パッケージをインストールします。

    このコマンドで、ベース Azure Machine Learning SDK が、notebook extra および automl extra と一緒にインストールされます。 `automl` extra は大規模なインストールになるので、自動化された機械学習の実験を実行する予定がない場合はブラケットから削除できます。 `automl` extra には、既定で Azure Machine Learning Data Prep SDK も依存関係として含まれます。

     ```shell
    pip install azureml-sdk[notebooks,automl]
    ```

   > [!NOTE]
   > PyYAML をアンインストールできないというメッセージが表示された場合は、代わりに次のコマンドを使用してください。
   >
   > `pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML`

   SDK をインストールするには数分かかります。 インストール オプションの詳細については、[インストール ガイド](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)を参照してください。

1. 機械学習の実験に必要な他のパッケージをインストールします。

    次のコマンドのどちらかを使用します。その際、 *\<new package>* の部分を、インストールするパッケージに置き換えてください。 `conda install` からパッケージをインストールするには、そのパッケージが現在のチャネルの一部である必要があります (Anaconda クラウドに新しいチャネルを追加できます)。

    ```shell
    conda install <new package>
    ```

    または、`pip` からパッケージをインストールすることもできます。

    ```shell
    pip install <new package>
    ```

### <a id="jupyter"></a>Jupyter Notebooks

Jupyter Notebook は、[Jupyter プロジェクト](https://jupyter.org/)の一部です。 これらは、ライブ コードと説明のテキストとグラフィックスが混在するドキュメントを作成する対話型のコーディング エクスペリエンスを提供します。 また、Jupyter Notebook は、ドキュメントにコード セクションの出力を保存できるので、結果を他のユーザーと共有する優れた方法です。 Jupyter Notebook は、さまざまなプラットフォームにインストールできます。

「[ローカル コンピューター](#local)」のセクションにある手順を実行すると、Anaconda 環境で Jupyter Notebook を実行するのに必要なコンポーネントがインストールされます。 Jupyter Notebook 環境内でこれらのコンポーネントを有効にするには、以下の手順を実行します。

1. Anaconda プロンプトを開いて、環境をアクティブにします。

    ```shell
    conda activate myenv
    ```
    
1. サンプル ノートブックのセットに対して [GitHub リポジトリ](https://aka.ms/aml-notebooks)を複製します。

    ```CLI
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. 次のコマンドを使用して、Jupyter Notebook サーバーを起動します。

    ```shell
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
    
1. Azure Machine Learning service ワークスペースを使用するよう Jupyter Notebook を構成するには、「[ワークスペース構成ファイルを作成する](#workspace)」セクションを参照してください。


### <a id="vscode"></a>Visual Studio Code

Visual Studio Code はクロス プラットフォーム コード エディターです。 Python サポートについてはローカルの Python 3 および Conda のインストールに依存していますが、AI を操作するための他のツールを提供します。 コード エディター内から Conda 環境を選択するためのサポートも提供します。

開発に Visual Studio Code を使用するには、以下の手順を実行します。

1. Python の開発に Visual Studio Code を使用する方法については、[VSCode での Python の使用](https://code.visualstudio.com/docs/python/python-tutorial)に関するページを参照してください。

1. Conda 環境を選択するには、VS Code を開いてから、Ctrl+Shift+P (Linux と Windows) または Command+Shift+P (Mac) を選択します。
    __Command Pallet__ が開きます。

1. 「__Python:Select Interpreter__」と入力した後、Conda 環境を選択します。

1. SDK を使用できることを確認するには、次のコードを含む新しい Python ファイル (.py) を作成してから実行します。

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. Visual Studio Code 用の Azure Machine Learning 拡張機能をインストールするには、[AI 用のツール](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai)に関するページを参照してください。

    詳細については、[Visual Studio Code 用の Azure Machine Learning の使用](how-to-vscode-tools.md)に関するページを参照してください。

<a name="aml-databricks"></a>

## <a name="azure-databricks"></a>Azure Databricks
Azure Databricks は、Azure クラウド内の Apache Spark ベースの環境です。 これは、CPU または GPU ベースのコンピューティング クラスターを備え、コラボレーションに適した Notebook ベースの環境を提供します。

Azure Databricks が Azure Machine Learning service と連携する仕組み:
+ Spark MLlib を使用してモデルをトレーニングし、そのモデルを Azure Databricks 内から ACI/AKS にデプロイできます。
+ また、Azure Databricks を使用して、特別な Azure ML SDK で[自動化された機械学習](concept-automated-ml.md)機能を使用することもできます。
+ Azure Databricks は、[Azure Machine Learning パイプライン](concept-ml-pipelines.md)からのコンピューティング先として使用できます。

### <a name="set-up-your-databricks-cluster"></a>Databricks クラスターを設定する

[Databricks クラスター](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)を作成します。 一部の設定は、Databricks に自動化された機械学習用の SDK をインストールする場合にのみ適用されます。
**クラスターの作成には数分かかります。**

次の設定を使用します。

| Setting |適用対象| 値 |
|----|---|---|
| クラスター名 |常時| yourclustername |
| Databricks ランタイム |常時| ML 以外の任意のランタイム (ML 4.x、5.x 以外) |
| Python バージョン |常時| 3 |
| ワーカー |常時| 2 以上 |
| ワーカー ノードの VM の種類 <br>(同時実行反復処理の最大数を決定) |自動化された ML<br>のみ| メモリ最適化 VM 優先 |
| 自動スケールの有効化 |自動化された ML<br>のみ| オフ |

クラスターが実行中になるまで待機してから、次に進んでください。

### <a name="install-the-correct-sdk-into-a-databricks-library"></a>Databricks ライブラリに適切な SDK をインストールする
クラスターが実行されたら、[ライブラリを作成](https://docs.databricks.com/user-guide/libraries.html#create-a-library)して、適切な Azure Machine Learning SDK パッケージをクラスターにアタッチします。

1. オプションを **1 つだけ**選択します (他の SDK インストールはサポート対象外)。

   |SDK&nbsp;パッケージ&nbsp;extras|source|PyPi&nbsp;名&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
   |----|---|---|
   |Databricks 用| Python Egg または PyPI のアップロード | azureml-sdk[databricks]|
   |Databricks 用 (<br> 自動化された ML 機能付)| Python Egg または PyPI のアップロード | azureml-sdk[automl_databricks]|

   > [!Warning]
   > 他の SDK extras はインストールできません。 前述の [databricks] または [automl_databricks] オプションから、どちらか一方のみを選択します。

   * **[すべてのクラスターに自動的にアタッチする]** は選択しないでください。
   * クラスター名の横にある **[アタッチ]** を選択します。

1. 状態が **[アタッチ済み]** に変わるまで、エラーを監視します。これには数分かかる場合があります。  この手順が失敗する場合は、次を確認します。

   次のようにして、クラスターを再起動してみます。
   1. 左側のウィンドウで、 **[クラスター]** を選択します。
   1. テーブルでクラスター名を選択します。
   1. **[ライブラリ]** タブで **[再起動]** を選択します。

   次の点も考慮します。
   + Automl 構成では、Azure Databricks を使用するときに次のパラメーターを追加してください。
       1. ```max_concurrent_iterations``` は、ご利用のクラスター内のワーカー ノードの数に基づきます。
        2. ```spark_context=sc``` は、既定の Spark コンテキストに基づきます。
   + または、古いバージョンの SDK がある場合は、クラスターのインストール済みライブラリでその SDK の選択を解除し、ごみ箱に移動します。 新しいバージョンの SDK をインストールし、クラスターを再起動します。 その後問題がある場合は、クラスターをデタッチし、再アタッチします。

インストールが成功した場合、インポートされたライブラリは次のどちらかのような外観になります。

自動化された機械学習機能を **_持たない_** Databricks 用 SDK ![Databricks 用 Azure Machine Learning SDK](./media/how-to-configure-environment/amlsdk-withoutautoml.jpg)

自動化された機械学習機能を**持つ** Databricks 用 SDK ![自動化された機械学習機能が Databricks にインストールされた SDK](./media/how-to-configure-environment/automlonadb.jpg)

### <a name="start-exploring"></a>実際に使ってみる

実際に試してみましょう。
+ Azure Databricks/Azure Machine Learning SDK 用の[ノートブック アーカイブ ファイル](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks/Databricks_AMLSDK_1-4_6.dbc)をダウンロードし、Databricks クラスターに[そのアーカイブ ファイルをインポート](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-an-archive)します。
  サンプル ノートブックがたくさんありますが、**Azure Databricks で動作するのは[これらのサンプル ノートブック](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks)のみです。**

+ [トレーニング コンピューティングとして Databricks を使用してパイプラインを作成](how-to-create-your-first-pipeline.md)する方法を確認します。

## <a id="aznotebooks"></a>Azure Notebooks

[Azure Notebooks](https://notebooks.azure.com) (プレビュー) は、Azure クラウドにおける対話型開発環境です。 これは、Azure Machine Learning の開発を開始するのに簡単な方法です。

* Azure Machine Learning SDK は既にインストールされています。
* Azure portal で Azure Machine Learning service ワークスペースを作成した後、ボタンをクリックすると、ワークスペースを操作する Azure Notebook 環境が自動的に構成されます。

Azure Notebooks を使い始めるには、[Azure portal](https://portal.azure.com) を使用します。  ワークスペースを開き、 **[概要]** セクションの **[Azure Notebooks で開始する]** を選択します。

既定では、Azure Notebooks は、4 GB のメモリと 1 GB のデータに制限されている無料のサービス層を使用します。 ただし、これらの制限は、Data Science Virtual Machine インスタンスを Azure Notebooks プロジェクトにアタッチすることで削除できます。 詳細については、[Azure Notebooks プロジェクトの管理と構成 - コンピューティング層](/azure/notebooks/configure-manage-azure-notebooks-projects#compute-tier)に関するページを参照してください。

## <a id="workspace"></a>ワークスペース構成ファイルを作成する

ワークスペース構成ファイルは、Azure Machine Learning service ワークスペースと通信する方法を SDK に示す JSON ファイルです。 ファイルの名前は *config.json* で、形式は次のとおりです。

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

     ![Azure ポータル](./media/how-to-configure-environment/configure.png)

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


## <a name="next-steps"></a>次の手順

- MNIST データセットを使用して Azure Machine Learning 上で[モデルをトレーニングする](tutorial-train-models-with-aml.md)
- [Azure Machine Learning SDK for Python](https://aka.ms/aml-sdk) リファレンスを表示する
- [Azure Machine Learning 用のデータ準備パッケージ](https://aka.ms/data-prep-sdk)について学習する
