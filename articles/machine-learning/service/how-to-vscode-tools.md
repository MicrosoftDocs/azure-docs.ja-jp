---
title: Visual Studio Code での Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Azure Machine Learning for Visual Studio Code をインストールし、Azure Machine Learning で実験を作成する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jimgries
author: greazer
ms.date: 09/20/2019
ms.custom: seodec18
ms.openlocfilehash: 84b7f56ebcbb87a31fbfb85ea6d88c3388870872
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975994"
---
# <a name="get-started-with-azure-machine-learning-for-visual-studio-code"></a>Azure Machine Learning for Visual Studio Code の使用を開始する

この記事では、**Azure Machine Learning for Visual Studio Code** 拡張機能を使用して機械学習モデルのトレーニングとデプロイを行う方法について説明します。

[Azure Machine Learning](overview-what-is-azure-ml.md) は、機械学習モデルの構築、トレーニング、およびデプロイを合理化します。
+ トレーニングに関しては、ローカルまたはリモートでの実験の実行をサポートします。 すべての実験では、複数の実行のカスタム メトリックをログに記録し、ハイパーパラメーターを微調整できます。
+ さらに、Azure Machine Learning を使用すると、テストと運用のニーズに合わせて機械学習モデルを簡単にデプロイできます。

## <a name="prerequisites"></a>前提条件

+ Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning](https://aka.ms/AMLFree) をお試しください。

+ Windows、Mac、Linux で動作する軽量なコード エディターである [Visual Studio Code](https://code.visualstudio.com/docs/setup/setup-overview) をインストールします。

+ [Python 3.5 以降をインストールします](https://www.anaconda.com/download/)。


## <a name="install-the-extension"></a>拡張機能のインストール

Azure Machine Learning 拡張機能をインストールすると、さらに 2 つの拡張機能が自動的にインストールされます。 それは [Azure アカウント拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)と [Microsoft Python 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-Python.Python)です。 Python コードの編集、実行、デバッグに Python 拡張機能を使用する方法の詳細については、[Python hello-world チュートリアル](https://code.visualstudio.com/docs/Python/Python-tutorial)を参照してください。

Azure Machine Learning 拡張機能をインストールするには:

1. Visual Studio Code を開きます。

1. [拡張機能] タブに切り替えて、"Azure Machine Learning" を検索します。

1. 拡張機能のタブで、 **[Install]\(インストール\)** を選択します。

1. 拡張機能の最初のタブが Visual Studio Code で開き、Azure のシンボル (次のスクリーンショットでは赤で強調表示されています) がアクティビティ バーに追加されます。

   ![Visual Studio Code アクティビティ バーの Azure アイコン](./media/vscode-tools-for-ai/azure-activity-bar.png)

1. ダイアログ ボックスで **[サインイン]** を選択し、プロンプトに従って Azure の認証を受けます。

   Azure Machine Learning for Visual Studio Code 拡張機能と共にインストールされる Azure Account 拡張機能は、Azure アカウントでの認証に役立ちます。 コマンドの一覧については、[Azure Account 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)のページを参照してください。

> [!TIP]
> 拡張機能のインストーラーは、[Azure Machine Learning for Visual Studio Code 拡張機能 (プレビュー)](https://aka.ms/vscodetoolsforai) に関するページから直接ダウンロードすることもできます。

## <a name="quickstart-with-azure-machine-learning"></a>Azure Machine Learning を使用したクイックスタート
Azure Machine Learning を使用してトレーニング スクリプトを実行する方法は複数あります。 使い始めたばかりの場合は、まず、Azure で実行するためにトレーニング スクリプトをすばやく送信する方法を確認しましょう。

Azure Machine Learning の概念を既にある程度理解していて、拡張機能を使用してそれを管理および使用する方法についてより詳しく知りたい場合は、以下の「[VS Code を使用した Azure Machine Learning の詳細](./how-to-vscode-tools.md#azure-machine-learning-in-depth-with-vs-code)」を参照してください。

## <a name="run-an-existing-python-training-script-in-azure"></a>Azure で既存の Python トレーニング スクリプトを実行する
既存のトレーニング スクリプトがある場合、VS Code 用の Azure Machine Learning 拡張機能では、編集、デバッグ、ソース管理の優れたエクスペリエンスが提供されるだけでなく、お使いのスクリプトのメトリックを Azure で簡単に実行して格納することもできます。

それでは始めましょう。 準備が整っていれば独自のトレーニング スクリプトを使用することも、サンプルの [vscode-tools-for-ai repo](https://github.com/microsoft/vscode-tools-for-ai) を複製することもできます。 これは、この拡張機能に関する問題を提出するためのパブリック リポジトリです。 これには、この例に使用する、小さな **mnist** サンプル フォルダーも含まれています。

1. VS Code で **mnist** フォルダーを開きます。

1. お好みの仮想環境パッケージまたは Anaconda を使用して新しい Python 環境を作成し、TensorFlow および numpy パッケージをインストールします。

1. VS Code のステータス バーの左下隅で、作成した新しい環境を Python インタープリターとして選択します。

1. **train.py** を開いて実行します。そのためには、デバッガーを開いて実行ボタンを押します (または、F5 キーを押すだけです)。

   [![MNIST トレーニングを実行する](./media/vscode-tools-for-ai/run-mnist.gif)](./media/vscode-tools-for-ai/run-mnist.gif#lightbox)

すべてが正しくインストールされると、スクリプトが実行され、outputs フォルダーに TensorFlow モデルが作成されます。

[![TensorFlow モデルを表示する](./media/vscode-tools-for-ai/show-tensorflow-model.gif)](./media/vscode-tools-for-ai/show-tensorflow-model.gif#lightbox)

スクリプトが正常に実行されたことを確認したら、Azure でそれを実行しましょう。

これは、**train.py** をさらに変更しなくても簡単に実行できます。 ただし、簡単な変更をわずかに加えると、Azure Machine Learning を使用して、各トレーニング実行に関する任意の重要なメトリックを自動的に追跡できます。

### <a name="make-azure-aware-of-your-run-metrics"></a>実行メトリックを Azure に認識させる
実行における重要な情報を Azure が認識できるようにプロジェクトを変更するには:

1. **train.py** と同じフォルダー内に、**amlrun.py** という名前のファイルを作成します。

    ```Python
    import azureml
    from azureml.core import Run

    # Access the Azure ML run
    # Init run param to check if running within AML
    def get_AMLRun():
        try:
            run = Run.get_submitted_run()
            return run
        except Exception as e:
            print("Caught = {}".format(e.message))
            return None
    ```

2. **train.py** に amlrun ファイルをインポートします。

    ```Python
    ...
    from utils import prepare_data
    from amlrun import get_AMLRun
    ...
    ```
3. **train.py** で実行オブジェクトを初期化します。

    ```Python
    ...
    init = tf.global_variables_initializer()
    saver = tf.train.Saver()
    run = get_AMLRun()
    ...
    ```
4. run.log() 関数を使用して Azure にメトリックを記録します。

    ```Python
    ...
            acc_val = acc_op.eval(feed_dict = {X: X_test, y: y_test})

            # Log accuracies to AML logger if using AML
            if run != None:
                run.log('Validation Accuracy', np.float(acc_val))
                run.log('Training Accuracy', np.float(acc_train))

            print(epoch, '-- Training accuracy:', acc_train, '\b Validation
    ...
    ```
### <a name="run-the-script-in-azure"></a>Azure でスクリプトを実行する
これで完了です。 ここで、この拡張機能を使用してクラウド内でスクリプトを実行するだけです。 次のチュートリアル ビデオでは、新しい Azure Machine Learning ワークスペースおよびコンピューティングを作成するためにかかる時間や、トレーニング スクリプトを実行するためにかかる時間を大胆に短縮していることに注意してください。

   [![Azure ML の実験を開始する](./media/vscode-tools-for-ai/start-golden-path.gif)](./media/vscode-tools-for-ai/start-golden-path.gif#lightbox)

[Run Experiment]\(実験の実行\) ボタンをクリックした後、次のように画面の指示に対応します。

1. Azure サブスクリプションを選択します。
1. *新しい* Azure Machine Learning ワークスペースを作成することを選択します。
1. あらかじめ構成されている一連のテンプレートから選択し、実行用の Python 環境を初期化します。 テンプレートは出発点を提供するほか、次に関する設定が含まれています。
    1. **PyTorch**、**TensorFlow**、または **Scikit-learn**
    1. **単一**または**分散型**のコンピューティング トレーニング
    1. カスタム環境の **[全般]**
1. テンプレートに含まれていないパッケージを追加することで、お使いのスクリプトに合うように pip パッケージと conda パッケージのリストを完成させます。
1. 実験の実行の既定の名前と仕様を確認し、json ファイル内の **Submit Experiment** リンクをクリックします。 この json ファイルは、実験の設定を送信前に確認または変更するためだけに存在するため、保存されません。
1. 後は、拡張機能によってすべてが設定され、スクリプトが実行されるのを待つだけです。

    [![クラウドでトレーニングする](./media/vscode-tools-for-ai/run-golden-path.gif)](./media/vscode-tools-for-ai/run-golden-path.gif#lightbox)

数秒後に、実験が Azure に送信されたことが通知されます。その時点で、その進行状況を、VS Code 通知内の **[View Experiment Run]\(実験の実行を表示する\)** リンクをクリックして Azure Machine Learning Studio で確認することも、VS Code 内で [Azure] タブの更新ボタンをクリックして確認することもできます。

現在、実行メトリックの表示は Studio でのみサポートされています。 前述の **[View Experiment Run]\(実験の実行を表示する\)** リンクを使用すると、実行に移動し、記録したメトリックが表示されます。
[![ポータルでの実験の実行](./media/vscode-tools-for-ai/experiment-run-on-portal.PNG)](./media/vscode-tools-for-ai/experiment-run-on-portal.PNG#lightbox)

## <a name="azure-machine-learning-in-depth-with-vs-code"></a>VS Code を使用した Azure Machine Learning の詳細

上記のチュートリアルでは、最も簡単な方法で実験を送信しました。 お気付きかも知れませんが、拡張機能によって、実験を実行するために管理しなければならない手順が最小限に抑えられています。 このセクションでは、Azure Machine Learning のすべての概念を個別に管理して、最大限制御できるようにする方法について説明します。

Visual Studio Code で機械学習モデルのトレーニングとデプロイを開始する前に、クラウドに [Azure Machine Learning ワークスペース](concept-workspace.md)を作成する必要があります。 このワークスペースにはモデルとリソースが含まれます。

### <a name="create-a-workspace"></a>ワークスペースの作成

1. Visual Studio Code アクティビティ バーで、Azure アイコンを選択します。 Azure Machine Learning のサイドバーが表示されます。

    [![ワークスペースを作成する](./media/vscode-tools-for-ai/create-workspace.gif)](./media/vscode-tools-for-ai/create-workspace.gif#lightbox)


1. Azure サブスクリプションを右クリックし、 **[ワークスペースの作成]** を選択します。 既定では、作成日時を含む名前が生成されます。 その名前を「**TeamWorkspace**」に変更し、Enter キーを押します。

1. 選択するリソース グループがわかっている場合は一覧からそれを選択します。そうでなければ、新しく作成します。 新しく作成する場合は、お使いのモデルをデプロイする予定の場所に最も近い場所を選択します。 この例では、 **[米国西部 2]** を選択しました。

1. Enter キーを押すと、Azure Machine Learning がこの要求を受け取り、ワークスペースを作成します。 このプロセスは Visual Studio Code の通知領域で通知されます。

1. 新しく作成したワークスペースを検索するには、[サブスクリプション] ノードを展開します。

### <a name="create-an-experiment"></a>実験の作成
ワークスペースに 1 つ以上の実験を作成して、個々のモデルのトレーニング実行を追跡して分析することができます。 実行は、Azure クラウド内またはお使いのローカル コンピューター上で行うことができます。

1. **TeamWorkspace** ワークスペースを展開します。 **[実験]** ノードを右クリックして、コンテキスト メニューから **[Create Experiment]\(実験の作成\)** を選択します。

1. プロンプトで、実験の名前を入力します。 スクリーンショットの例では、実験の名前は **MNIST** です。

1. Enter キーを押して新しい実験を作成します。 この新しい実験は、 **[実験]** ノードの子としてツリーに表示されます。

1. ワークスペースで、実験を右クリックして**アクティブ**な実験として設定することができます。 この**アクティブ**な実験により、クラウド内のその実験が、Visual Studio Code で現在開いているフォルダーにリンクされます。 このフォルダーには ローカルの Python スクリプトが含まれます。 アクティブな実験を設定することで、すべてのトレーニング実行の主要なメトリックは、実行される場所に関係なく、実験内に格納されます。

    [![実験を作成する](./media/vscode-tools-for-ai/create-experiment.gif)](./media/vscode-tools-for-ai/create-experiment.gif#lightbox)


### <a name="create-and-manage-compute-targets"></a>コンピューティング ターゲットを作成して管理する

Azure Machine Learning for Visual Studio Code を使用すると、データを準備し、モデルをトレーニングし、それらをローカルとリモートの両方のコンピューティング先にデプロイできます。

この拡張機能は、Azure Machine Learning の場合にいくつかのリモート コンピューティング先をサポートします。 詳細については、[Azure Machine Learning でサポートされているコンピューティング先](how-to-set-up-training-targets.md)の全一覧を参照してください。

### <a name="create-compute-targets-for-azure-machine-learning-in-visual-studio-code"></a>Visual Studio Code で Azure Machine Learning のコンピューティング先を作成する

コンピューティング先を作成するには:

1. Visual Studio Code アクティビティ バーで、Azure アイコンを選択します。 Azure Machine Learning のサイドバーが表示されます。

1. ツリー ビューで、お使いの Azure サブスクリプションと Azure Machine Learning ワークスペースを展開します。

1. ワークスペース ノードの下の **[コンピューティング]** ノードを右クリックし、 **[Create Compute]\(コンピューティングの作成)** を選択します。

1. 一覧からコンピューティング ターゲットの種類を選択します。

1. コマンド パレットのプロンプトで、仮想マシンのサイズを選択します。 "gpu" などのテキストを使用して、コンピューティングをフィルター処理できます。

1. コマンド パレットのプロンプトで、コンピューティング先の名前を入力します。

1. 名前を入力すると、既定のパラメーターを使用してコンピューティングが作成されます。 パラメーターを変更するには、新しいコンピューティングを右クリックして、 **[Edit Compute]\(コンピューティングの編集\)** を選択します。

1. 表示された json で必要な変更を行い、CodeLens の [保存して続行] をクリックします (キーボードを使用する場合は **Ctrl + Shift + P** キーを押してコマンド パレットを呼び出し、 **[Azure ML: 保存して続行]** コマンドを実行します)。

Azure Machine Learning コンピューティング (AMLCompute) の作成および編集方法の例を次に示します。

[![Visual Studio Code で AML コンピューティングを作成する](./media/vscode-tools-for-ai/create-remote-compute.gif)](./media/vscode-tools-for-ai/create-remote-compute.gif#lightbox)

#### <a name="the-run-configuration-file"></a>実行構成ファイル

コンピューティング上で Azure Machine Learning の実験を実行するには、そのコンピューティングが適切に構成されている必要があります。 実行構成ファイルは、この環境を指定するためのメカニズムです。

上記で作成した AmlCompute 用の実行構成を作成する方法の例を次に示します。

[![コンピューティングの実行構成を作成する](./media/vscode-tools-for-ai/create-runconfig.gif)](./media/vscode-tools-for-ai/create-runconfig.gif#lightbox)

ローカル コンピューターで Azure ML の実験を実行するには、引き続き実行構成ファイルが必要になります。 ローカル実行構成の作成時に使用された Python 環境では、VS Code 内で設定したインタープリターへのパスが既定値になります。

### <a name="train-and-tune-models"></a>モデルをトレーニングして調整する

VS Code 用の Azure ML 拡張機能を使用した場合、実験内でトレーニング スクリプトを実行する方法は複数あります。

1. トレーニング スクリプトを右クリックして、 **[Azure ML: Run as Experiment in Azure]\(Azure ML: Azure で実験として実行\)** を選択します。
1. [Run Experiment]\(実験の実行\) ツール バー アイコンをクリックします。
1. 実行構成ノードを右クリックします。
1. VS Code のコマンド パレットを使用して、 **[Azure ML: Run Experiment]\(Azure ML: 実験の実行\)** を実行します。

Azure Machine Learning 実験を実行するには:

1. Visual Studio Code アクティビティ バーで、Azure アイコンを選択します。

1. ツリー ビューで、お使いの Azure サブスクリプションと Azure Machine Learning ワークスペースを展開します。

1. ワークスペース ノードの下の **[実験]** ノードを展開し、実行する実験を右クリックします。

1. **[Run Experiment]\(実験を実行する)** を選択します。

1. モデルのトレーニングのために実行する Python ファイルの名前を選択し、Enter キーを押して実行を送信します。 注:選択したファイルは、VS Code で現在開いているフォルダーに存在する必要があります。

1. 実行が送信されると、選択した実験の下に **[実行] ノード**が表示されます。 このノードを使用して、実行の状態を監視します。 注:最新の状態を確認するには、ウィンドウを定期的に更新することが必要になる場合があります。

前に作成したコンピューティング上で実験を実行する方法の例を次に示します。

[![ローカルで実験を実行する](./media/vscode-tools-for-ai/run-experiment.gif)](./media/vscode-tools-for-ai/run-experiment.gif#lightbox)

### <a name="deploy-and-manage-models"></a>モデルを展開して管理する
Azure Machine Learning では、機械学習モデルをクラウドとエッジにデプロイして管理できます。

#### <a name="register-your-model-to-azure-machine-learning-from-visual-studio-code"></a>モデルを Visual Studio Code から Azure Machine Learning に登録する

モデルのトレーニングが終わったので、次はそれをワークスペースに登録できます。 登録済みモデルを追跡してデプロイすることができます。

モデルを登録するには:

1. Visual Studio Code アクティビティ バーで、Azure アイコンを選択します。 Azure Machine Learning のサイドバーが表示されます。

1. ツリー ビューで、お使いの Azure サブスクリプションと Azure Machine Learning ワークスペースを展開します。

1. ワークスペース ノードの下の **[モデル]** を右クリックし、 **[Register Model]\(モデルの登録)** を選択します。

1. コマンド パレットのフィールドに、モデル名を入力します。

1. 一覧から**モデル ファイル** (単一モデルの場合) または**モデル フォルダー** (TensorFlow など、複数のファイルがあるモデルの場合) のどちらをアップロードするかを選択します。

1. フォルダーまたはファイルを選択します。

1. モデルのプロパティの構成を完了したら、ウィンドウの右下にある **[送信]** を選択します。

モデルを Azure Machine Learning に登録する方法の例を次に示します。

[![AML へのモデルの登録](./media/vscode-tools-for-ai/register-model.gif)](./media/vscode-tools-for-ai/register-model.gif#lightbox)


#### <a name="deploy-your-service-from-visual-studio-code"></a>Visual Studio Code からサービスをデプロイする

Visual Studio Code では、Web サービスを以下にデプロイできます。
+ テスト用に Azure Container Instances (ACI)。
+ 運用環境用に Azure Kubernetes Service (AKS)。

ACI コンテナーは必要に応じて作成されるので、テストするために ACI コンテナーを事前に作成する必要はありません。 ただし、AKS クラスターを事前に構成する必要があります。 詳細については、「[Azure Machine Learning を使用してモデルをデプロイする](how-to-deploy-and-where.md)」を参照してください。

Web サービスをデプロイするには:

1. Visual Studio Code アクティビティ バーで、Azure アイコンを選択します。 Azure Machine Learning のサイドバーが表示されます。

1. ツリー ビューで、お使いの Azure サブスクリプションと Azure Machine Learning ワークスペースを展開します。

1. ワークスペース ノードの下の **[モデル]** ノードを展開します。

1. デプロイするモデルを右クリックし、コンテキスト メニューから **[Deploy Service from Registered Model]\(登録モデルからのサービスのデプロイ\)** を選択します。

1. コマンド パレットで、デプロイ先にするコンピューティング先を選択します。

1. コマンド パレットのフィールドに、このサービスの名前を入力します。

1. コマンド パレットで、キーボードの Enter キーを押してスクリプト ファイルを参照して選択します。

1. コマンド パレットで、キーボードの Enter キーを押して conda 依存関係ファイルを参照して選択します。

1. サービスのプロパティの構成が完了したら、ウィンドウの右下にある **[送信]** を選択してデプロイします。 サービスのプロパティ ファイルには、ローカルの docker ファイルまたは schema.json ファイルを指定できます。

これで Web サービスがデプロイされました。

Web サービスをデプロイする方法の例を次に示します。

[![Web サービスをデプロイする](./media/vscode-tools-for-ai/create-image.gif)](./media/vscode-tools-for-ai/create-image.gif#lightbox)

### <a name="experiment-with-additional-features"></a>その他の機能を使用した実験

コマンド パレットを使用すると、Visual Studio Code で多くの Azure Machine Learning 機能にアクセスできます。 コマンド パレットを呼び出すには、Ctrl + Shift + P キーを押します。 ここから、拡張機能のその他の Azure ML 機能を検索できます。

[![Azure Machine Learning for Visual Studio Code のキーボード ショートカット](./media/vscode-tools-for-ai/commands.gif)](./media/vscode-tools-for-ai/commands.gif#lightbox)

## <a name="next-steps"></a>次の手順

* Visual Studio Code 以外で Azure Machine Learning をトレーニングする方法のチュートリアルについては、[Azure Machine Learning を使用したモデルのトレーニングに関するチュートリアル](tutorial-train-models-with-aml.md)を参照してください。
* コードをローカルで編集、実行、およびデバッグする方法のチュートリアルについては、[Python hello-world チュートリアル](https://code.visualstudio.com/docs/Python/Python-tutorial)を参照してください。
