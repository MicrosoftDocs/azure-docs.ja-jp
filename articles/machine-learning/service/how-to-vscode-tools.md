---
title: 機械学習に Visual Studio Code を使用する
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning for Visual Studio Code をインストールし、Azure Machine Learning で簡単な実験を作成する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: shwinne
author: swinner95
ms.date: 7/12/2019
ms.custom: seodec18
ms.openlocfilehash: 3b49def1af7f13e38c3a9daea32d56bf3c633261
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67871763"
---
# <a name="get-started-with-azure-machine-learning-for-visual-studio-code"></a>Azure Machine Learning for Visual Studio Code の使用を開始する

この記事では、Azure Machine Learning for Visual Studio Code の拡張機能を使用して、機械学習モデルとディープラーニング モデルをトレーニングおよびデプロイする方法について説明します。

[Azure Machine Learning service](overview-what-is-azure-ml.md) は、ローカルとリモートのコンピューティング ターゲットで実行される実験をサポートしています。 どの実験についても、必要な頻度で複数の実行を追跡し、異なる手法やハイパーパラメーターなどを反復して試することができます。 Azure Machine Learning を使用してカスタム メトリックや実験の実行を追跡し、データ サイエンスの再現性と監査機能を実現できます。

テストや運用のニーズに合わせてこれらのモデルをデプロイすることもできます。

## <a name="prerequisites"></a>前提条件

+ Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning service](https://aka.ms/AMLFree) をお試しください。

+ Visual Studio Code をインストールする必要があります。 Visual Studio Code は、デスクトップ上で動作する軽量で強力なソース コード エディターです。 Python や他のプログラミング言語のサポートが組み込まれています。 まだ Visual Studio Code をインストールしていない場合は、[こちらの方法](https://code.visualstudio.com/docs/setup/setup-overview)を参照してください。

+ [Python 3.5 以降をインストールします](https://www.anaconda.com/download/)。


## <a name="install-the-extension-for-azure-machine-learning-for-visual-studio-code"></a>Azure Machine Learning for Visual Studio Code の拡張機能をインストールする

Azure Machine Learning 拡張機能をインストールすると、さらに 2 つの拡張機能が自動的にインストールされます (インターネットにアクセスできる場合)。 それは [Azure アカウント拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)と [Microsoft Python 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-python.python)です。

Azure Machine Learning を使用するには、Visual Studio Code を Python 統合開発環境 (IDE) に変更する必要があります。 [Visual Studio Code で Python](https://code.visualstudio.com/docs/languages/python) を使用するには、Microsoft Python 拡張機能が必要です。 この拡張機能は、Azure Machine Learning 拡張機能と共に自動的にインストールされます。 この拡張機能を利用すると、Visual Studio Code は優れた IDE になり、さまざまな Python インタープリターと共にあらゆるオペレーティング システムで動作します。 Microsoft Python 拡張機能は、Visual Studio Code のすべての機能を使用して、オートコンプリート、IntelliSense、リンティング、デバッグ、単体テストの機能を提供しています。 この拡張機能を使用すると、仮想環境と conda 環境を含め、Python 環境を簡単に切り替えることもできます。 Python コードの編集、実行、およびデバッグの詳細については、[Python hello-world チュートリアル](https://code.visualstudio.com/docs/python/python-tutorial)を参照してください。

Azure Machine Learning 拡張機能をインストールするには:

1. Visual Studio Code を開きます。

1. Web ブラウザーで、[Azure Machine Learning for Visual Studio Code 拡張機能 (プレビュー)](https://aka.ms/vscodetoolsforai) のページにアクセスします。

1. その Web ページで、 **[Install]\(インストール\)** を選択します。 

1. 拡張機能のタブで、 **[Install]\(インストール\)** を選択します。

1. 拡張機能のようこそタブが Visual Studio Code で開き、Azure 記号 (次のスクリーンショットでは赤で囲まれています) がアクティビティ バーに追加されます。

   ![Visual Studio Code アクティビティ バーの Azure アイコン](./media/vscode-tools-for-ai/azure-activity-bar.png)

1. ダイアログ ボックスで **[サインイン]** を選択し、プロンプトに従って Azure の認証を受けます。 
   
   Azure Machine Learning for Visual Studio Code 拡張機能と共にインストールされる Azure Account 拡張機能は、Azure アカウントでの認証に役立ちます。 コマンドの一覧については、[Azure Account 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)のページを参照してください。

> [!Tip] 
> [Visual Studio Code 用の IntelliCode 拡張機能 (プレビュー)](https://go.microsoft.com/fwlink/?linkid=2006060) を確認してください。 IntelliCode には、現在のコードのコンテキストに基づいて最も関連性の高いオートコンプリートを推測するなど、Python での IntelliSense に対応する一連の AI 支援機能があります。

## <a name="install-the-azure-machine-learning-sdk"></a>Azure Machine Learning SDK をインストールする

1. Python 3.5 以降がインストールされ、Visual Studio Code によって認識されていることを確認します。 今すぐインストールする場合は、Visual Studio Code を再起動して [Python インタープリターを選択します](https://code.visualstudio.com/docs/python/python-tutorial)。

1. 統合ターミナル ウィンドウで、使用する Python インタープリターを指定します。 または、既定の Python インタープリターを使用するには Enter キーを押します。

   ![インタープリターを選択する](./media/vscode-tools-for-ai/python.png)

1. ウィンドウの右下に、[Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) が自動的にインストールされていることを示す通知が表示されます。 新しく作成される Python 環境はローカルで非公開であり、Azure Machine Learning service を使用するための Visual Studio Code の前提条件があります。

   ![Azure Machine Learning SDK for Python をインストールする](./media/vscode-tools-for-ai/runtimedependencies.png)

## <a name="get-started-with-azure-machine-learning"></a>Azure Machine Learning の利用を開始

Visual Studio Code で機械学習モデルのトレーニングとデプロイを開始する前に、クラウドに [Azure Machine Learning service ワークスペース](concept-workspace.md)を作成する必要があります。 このワークスペースにはモデルとリソースが含まれます。 

ワークスペースを作成して最初の実験を追加するには:

1. Visual Studio Code アクティビティ バーで、Azure アイコンを選択します。 Azure Machine Learning のサイドバーが表示されます。

   [![ワークスペースを作成する](./media/vscode-tools-for-ai/CreateaWorkspace.gif)](./media/vscode-tools-for-ai/CreateaWorkspace.gif#lightbox)


1. Azure サブスクリプションを右クリックし、 **[ワークスペースの作成]** を選択します。 一覧が表示されます。 アニメーション画像の例では、サブスクリプション名は **Free Trial**、ワークスペースは **TeamWorkspace** です。 

1. 一覧からリソース グループを選択するか、コマンド パレットのウィザードを使用して新しいリソース グループを作成します。

1. フィールドに、新しいワークスペースの一意でわかりやすい名前を入力します。 画像の例では、ワークスペースは **TeamWorkspace** という名前です。

1. Enter キーを押して新しいワークスペースを作成します。 ツリーのサブスクリプション名の下に表示されます。

1. **[実験]** ノードを右クリックして、コンテキスト メニューから **[Create Experiment]\(実験の作成\)** を選択します。  実験は、Azure Machine Learning を使用した実行を記録します。

1. フィールドに実験の名前を入力します。 スクリーンショットの例では、実験の名前は **MNIST** です。
 
1. Enter キーを押して新しい実験を作成します。 実験は、ツリーのワークスペース名の下に表示されます。

1. ワークスペースで、実験を右クリックして**アクティブ**な実験として設定することができます。 **アクティブ**な実験とは、現在の実験です。 Visual Studio Code で開いているフォルダーは、クラウド内のこの実験にリンクされます。 このフォルダーには ローカルの Python スクリプトが含まれます。

これで、重要なメトリックが実験履歴内に保存されます。 同様に、トレーニングするモデルは Azure Machine Learning に自動的にアップロードされ、実験メトリックとログと共に保存されます。 

[![Visual Studio Code でフォルダーをアタッチする](./media/vscode-tools-for-ai/CreateAnExperiment.gif)](./media/vscode-tools-for-ai/CreateAnExperiment.gif#lightbox)


## <a name="create-and-manage-compute-targets"></a>コンピューティング ターゲットを作成して管理する

Azure Machine Learning for Visual Studio Code を使用すると、データを準備し、モデルをトレーニングし、それらをローカルとリモートの両方のコンピューティング先にデプロイできます。

この拡張機能は、Azure Machine Learning の場合にいくつかのリモート コンピューティング先をサポートします。 詳細については、[Azure Machine Learning でサポートされているコンピューティング先](how-to-set-up-training-targets.md)の全一覧を参照してください。

### <a name="create-compute-targets-for-azure-machine-learning-in-visual-studio-code"></a>Visual Studio Code で Azure Machine Learning のコンピューティング先を作成する

コンピューティング先を作成するには:

1. Visual Studio Code アクティビティ バーで、Azure アイコンを選択します。 Azure Machine Learning のサイドバーが表示されます。

2. ツリー ビューで、お使いの Azure サブスクリプションと Azure Machine Learning サービス ワークスペースを展開します。 次の画像の例では、サブスクリプション名は **Free Trial**、ワークスペースは **TeamWorkspace** です。 

3. ワークスペース ノードの下の **[コンピューティング]** ノードを右クリックし、 **[Create Compute]\(コンピューティングの作成)** を選択します。

4. 一覧からコンピューティング ターゲットの種類を選択します。 

5. コマンド パレットで、仮想マシンのサイズを選択します。

6. コマンド パレットのフィールドに、コンピューティング先の名前を入力します。 

7. 新しいタブに表示される JSON 構成ファイルに、詳細なプロパティを指定します。 最大ノード数などのプロパティを指定できます。

8. コンピューティング先の構成が完了したら、ウィンドウの右下にある **[送信]** を選択します。

Azure Machine Learning コンピューティング (AMLCompute) を作成する方法の例を次に示します。

[![Visual Studio Code で AML コンピューティングを作成する](./media/vscode-tools-for-ai/CreateARemoteCompute.gif)](./media/vscode-tools-for-ai/CreateARemoteCompute.gif#lightbox)

#### <a name="the-run-configuration-file"></a>実行構成ファイル

Visual Studio Code 拡張機能によって、ローカルのコンピューティング先と、ローカル コンピューター上のローカル環境と docker 環境の実行構成が自動的に作成されます。 実行構成ファイルは、関連するコンピューティング先ノード以下に表示されます。 

## <a name="train-and-tune-models"></a>モデルをトレーニングして調整する

Azure Machine Learning for Visual Studio Code (プレビュー) を使用して、コードをすばやく反復処理し、ステップ実行してデバッグし、ソリューションをソース コード管理に使用します。 

Azure Machine Learning を使用して実験をローカルで実行するには:

1. Visual Studio Code アクティビティ バーで、Azure アイコンを選択します。 Azure Machine Learning のサイドバーが表示されます。

1. ツリー ビューで、お使いの Azure サブスクリプションと Azure Machine Learning サービス ワークスペースを展開します。 

1. ワークスペース ノード以下の **[コンピューティング]** ノードを展開し、使用するコンピューティングの **[Run Config]\(実行構成\)** を右クリックします。 

1. **[Run Experiment]\(実験を実行する)** を選択します。

1. ファイル エクスプローラーから実行するスクリプトを選択します。 

1. 統合 Azure Machine Learning ポータルを表示し、実行を監視してトレーニング済みモデルを確認するには、 **[View Experiment Run]\(実験の実行の表示\)** を選択します。

実験をローカルで実行する方法の例を次に示します。

[![ローカルで実験を実行する](./media/vscode-tools-for-ai/RunExperimentLocally.gif)](./media/vscode-tools-for-ai/RunExperimentLocally.gif#lightbox)

### <a name="use-remote-computes-for-experiments-in-visual-studio-code"></a>Visual Studio Code で実験にリモート コンピューティングを使用する

リモート コンピューティング先をトレーニングに使用するには、実行構成ファイルを作成する必要があります。 このファイルは Azure Machine Learning に、実験を実行する場所だけでなく、環境を準備する方法も指示します。

#### <a name="the-conda-dependencies-file"></a>conda の依存関係ファイル

既定では、新しい conda 環境が自動的に作成され、インストールの依存関係が管理されます。 ただし、依存関係とそのバージョンを *aml_config/conda_dependencies.yml* ファイルに指定する必要があります。 

既定の *aml_config/conda_dependencies.yml* の次のスニペットでは、`tensorflow=1.12.0` を指定しています。 依存関係のバージョンを指定しない場合は、最新のバージョンが使用されます。 構成ファイルで他の依存関係を追加できます。

```yaml
# The dependencies defined in this file will be automatically provisioned for runs with userManagedDependencies=False.

name: project_environment
dependencies:
  # The python interpreter version.

  # Currently Azure ML only supports 3.5.2 and later.

- python=3.6.2
- tensorflow=1.12.0

- pip:
    # Required packages for AzureML execution, history, and data preparation.

  - --index-url https://azuremlsdktestpypi.azureedge.net/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1
  - --extra-index-url https://pypi.python.org/simple
  - azureml-defaults

```

リモート コンピューティング先で Azure Machine Learning を使用して実験を実行するには:

1. Visual Studio Code アクティビティ バーで、Azure アイコンを選択します。 Azure Machine Learning のサイドバーが表示されます。

1. ツリー ビューで、お使いの Azure サブスクリプションと Azure Machine Learning サービス ワークスペースを展開します。 

1. エディター ウィンドウで、Python スクリプトを右クリックして **[AML:Run as Experiment in Azure]\(AML: Azure で実験として実行\)** を選択します。 

1. コマンド パレットで、コンピューティング先を選択します。 

1. コマンド パレットのフィールドに、実行構成名を入力します。 

1. *conda_dependencies.yml* ファイルを編集して実験の実行時依存関係を指定します。 次に、ウィンドウの右下にある **[送信]** を選択します。 

1. 統合 Azure Machine Learning ポータルを表示し、実行を監視してトレーニング済みモデルを確認するには、 **[View Experiment Run]\(実験の実行の表示\)** を選択します。

リモート コンピューティング先で実験を実行する方法の例を次に示します。

[![リモート ターゲットで実験を実行する](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif)](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif#lightbox)


## <a name="deploy-and-manage-models"></a>モデルを展開して管理する
Azure Machine Learning では、機械学習モデルをクラウドとエッジにデプロイして管理できます。 

### <a name="register-your-model-to-azure-machine-learning-from-visual-studio-code"></a>モデルを Visual Studio Code から Azure Machine Learning に登録する

モデルのトレーニングが終わったので、次はそれをワークスペースに登録できます。 登録済みモデルを追跡してデプロイすることができます。

モデルを登録するには:

1. Visual Studio Code アクティビティ バーで、Azure アイコンを選択します。 Azure Machine Learning のサイドバーが表示されます。

1. ツリー ビューで、お使いの Azure サブスクリプションと Azure Machine Learning サービス ワークスペースを展開します。

1. ワークスペース ノードの下の **[モデル]** を右クリックし、 **[Register Model]\(モデルの登録)** を選択します。

1. コマンド パレットのフィールドに、モデル名を入力します。 

1. 一覧から**モデル ファイル** (単一モデルの場合) または**モデル フォルダー** (TensorFlow など、複数のファイルがあるモデルの場合) のどちらをアップロードするかを選択します。 

1. フォルダーまたはファイルを選択します。

1. モデルのプロパティの構成を完了したら、ウィンドウの右下にある **[送信]** を選択します。 

モデルを Azure Machine Learning に登録する方法の例を次に示します。

[![AML へのモデルの登録](./media/vscode-tools-for-ai/RegisteringAModel.gif)](./media/vscode-tools-for-ai/RegisteringAModel.gif#lightbox)


### <a name="deploy-your-service-from-visual-studio-code"></a>Visual Studio Code からサービスをデプロイする

Visual Studio Code では、Web サービスを以下にデプロイできます。
+ テスト用に Azure Container Instances (ACI)。
+ 運用環境用に Azure Kubernetes Service (AKS)。

ACI コンテナーはその場で作成されるので、テストするために ACI コンテナーを事前に作成する必要はありません。 ただし、AKS クラスターを事前に構成する必要があります。 詳細については、「[Azure Machine Learning service を使用してモデルをデプロイする](how-to-deploy-and-where.md)」を参照してください。

Web サービスをデプロイするには:

1. Visual Studio Code アクティビティ バーで、Azure アイコンを選択します。 Azure Machine Learning のサイドバーが表示されます。

1. ツリー ビューで、お使いの Azure サブスクリプションと Azure Machine Learning サービス ワークスペースを展開します。

1. ワークスペース ノードの下の **[モデル]** ノードを展開します。

1. デプロイするモデルを右クリックし、コンテキスト メニューから **[Deploy Service from Registered Model]\(登録モデルからのサービスのデプロイ\)** を選択します。

1. コマンド パレットで、デプロイ先にするコンピューティング先を選択します。 

1. コマンド パレットのフィールドに、このサービスの名前を入力します。  

1. コマンド パレットで、キーボードの Enter キーを押してスクリプト ファイルを参照して選択します。

1. コマンド パレットで、キーボードの Enter キーを押して conda 依存関係ファイルを参照して選択します。

1. サービスのプロパティの構成が完了したら、ウィンドウの右下にある **[送信]** を選択してデプロイします。 サービスのプロパティ ファイルには、ローカルの docker ファイルまたは schema.json ファイルを指定できます。

これで Web サービスがデプロイされました。

Web サービスをデプロイする方法の例を次に示します。

[![Web サービスをデプロイする](./media/vscode-tools-for-ai/CreatingAnImage.gif)](./media/vscode-tools-for-ai/CreatingAnImage.gif#lightbox)

### <a name="use-keyboard-shortcuts"></a>キーボード ショートカットの使用

キーボードを使用して、Visual Studio Code で Azure Machine Learning 機能にアクセスできます。 知っておくべき最も重要なキーボード ショートカットは、コマンド パレットを表示する Ctrl + Shift + P です。 コマンド パレットから、よく使用する操作のキーボード ショートカットを含め、Visual Studio Code のすべての機能にアクセスできます。

[![Azure Machine Learning for Visual Studio Code のキーボード ショートカット](./media/vscode-tools-for-ai/commands.gif)](./media/vscode-tools-for-ai/commands.gif#lightbox)

## <a name="next-steps"></a>次の手順

* Visual Studio Code 以外で Azure Machine Learning をトレーニングする方法のチュートリアルについては、[Azure Machine Learning を使用したモデルのトレーニングに関するチュートリアル](tutorial-train-models-with-aml.md)を参照してください。
* コードをローカルで編集、実行、およびデバッグする方法のチュートリアルについては、[Python hello-world チュートリアル](https://code.visualstudio.com/docs/python/python-tutorial)を参照してください。
