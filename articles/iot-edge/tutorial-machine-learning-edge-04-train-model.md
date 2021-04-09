---
title: チュートリアル:モデルのトレーニングとデプロイ - Azure IoT Edge での Machine Learning
description: このチュートリアルでは、Azure Machine Learning を使用して機械学習モデルをトレーニングし、そのモデルを、Azure IoT Edge モジュールとしてデプロイできるコンテナー イメージとしてパッケージ化します。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 3/24/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 16d5fe90be71f39d448e4c1ce10c0373f6bfc86c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "103463104"
---
# <a name="tutorial-train-and-deploy-an-azure-machine-learning-model"></a>チュートリアル:Azure Machine Learning モデルをトレーニングしてデプロイする

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

この記事では、次のタスクを実行します。

* Azure Machine Learning スタジオを使用して機械学習モデルをトレーニングします。
* トレーニング済みのモデルをコンテナー イメージとしてパッケージ化する。
* コンテナー イメージを Azure IoT Edge モジュールとしてデプロイする。

Machine Learning スタジオは、機械学習モデルの実験、トレーニング、デプロイに使用される基礎ブロックです。

一般的に、この記事の手順は、データ サイエンティストによって実行される可能性があります。

チュートリアルのこのセクションで学習する内容は次のとおりです。

> [!div class="checklist"]
> * 機械学習モデルをトレーニングするための Jupyter ノートブックを Azure Machine Learning ワークスペースで作成する。
> * トレーニングされた機械学習モデルをコンテナー化する。
> * コンテナー化された機械学習モデルから IoT Edge モジュールを作成する。

## <a name="prerequisites"></a>前提条件

この記事は、IoT Edge 上で Machine Learning を使用するためのチュートリアルのシリーズの一部です。 シリーズの各記事は、前の記事の作業に基づいています。 この記事に直接アクセスしている場合は、シリーズの[最初の記事](tutorial-machine-learning-edge-01-intro.md)を参照してください。

## <a name="set-up-azure-machine-learning"></a>Azure Machine Learning を設定する

Machine Learning スタジオを使用して、2 つの Jupyter ノートブックとサポート ファイルをホストします。 ここでは、Machine Learning プロジェクトを作成して構成します。 Jupyter や Machine Learning スタジオを使用したことがない方のために、2 つの入門用ドキュメントをご紹介します。

* **Jupyter ノートブック:** [Visual Studio Code での Jupyter ノートブックの使用](https://code.visualstudio.com/docs/python/jupyter-support)
* **Azure Machine Learning:** [Jupyter ノートブックで Azure Machine Learning の作業を開始する](../machine-learning/tutorial-1st-experiment-sdk-setup.md)

> [!NOTE]
> サービスの設定後は、任意のマシンから Machine Learning にアクセスできるようになります。 設定中は、必要なファイルが揃った開発用 VM を使用する必要があります。

### <a name="install-azure-machine-learning-visual-studio-code-extension"></a>Visual Studio Code の Azure Machine Learning 拡張機能をインストールする

開発用 VM 上の Visual Studio Code には、この拡張機能がインストールされている必要があります。 別のインスタンスで実行している場合は、[Visual Studio Code 拡張機能の設定](../machine-learning/tutorial-setup-vscode-extension.md)に関する記事の説明に従って、拡張機能を再インストールします。

### <a name="create-an-azure-machine-learning-account"></a>Azure Machine Learning アカウントを作成する

Azure でリソースをプロビジョニングしてワークロードを実行するには、Azure アカウントの資格情報でサインインします。

1. Visual Studio Code のメニュー バーから、 **[表示]**  >  **[コマンド パレット]** を選択してコマンド パレットを開きます。

1. コマンド パレットに `Azure: Sign In` コマンドを入力して、サインイン プロセスを開始します。 指示に従って、サインインを完了します。

1. Machine Learning コンピューティング インスタンスを作成して、ワークロードを実行します。 コマンドパレットに、`Azure ML: Create Compute` コマンドを入力します。
1. Azure サブスクリプションを選択します。
1. **[+ Create new Azure ML Workspace]\(+ 新しい Azure ML ワークスペースを作成する\)** を選択し、名前として「**turbofandemo**」と入力します。
1. このデモに使用しているリソース グループを選択します。
1. Visual Studio Code ウィンドウの右下隅に、ワークスペース作成の進行状況 (**ワークスペースを作成しています: turobofandemo**) が表示されます。 この手順には 1 分から 2 分かかる場合があります。
1. ワークスペースが正常に作成されるまで待ちます。 "**Azure ML workspace turbofandemo created (Azure ML ワークスペース turbofandemo が作成されました)** " と表示されます。

### <a name="upload-jupyter-notebook-files"></a>Jupyter Notebook ファイルをアップロードする

サンプル ノートブック ファイルを新しい Machine Learning ワークスペースにアップロードします。

1. ml.azure.com に移動し、サインインします。
1. ご自分の Microsoft ディレクトリ、Azure サブスクリプション、および新しく作成した Machine Learning ワークスペースを選択します。

    :::image type="content" source="media/tutorial-machine-learning-edge-04-train-model/select-studio-workspace.png" alt-text="Azure Machine Learning ワークスペースの選択方法を示すスクリーンショット。" :::

1. Machine Learning ワークスペースにサインインしたら、左側のメニューを使用して **[Notebooks]** セクションに移動します。
1. **[My files]\(マイ ファイル\)** タブを選択します。

1. **[アップロード]** (上矢印アイコン) を選択します。

1. **C:\source\IoTEdgeAndMlSample\AzureNotebooks** に移動します。 一覧からすべてのファイルを選択し、 **[開く]** を選択します。

1. **[I trust the content of these files]\(これらのファイルの内容を信頼します\)** チェック ボックスをオンにします。

1. **[アップロード]** を選択してアップロードを開始します。 次に、プロセスの完了後に **[完了]** を選択します。

### <a name="jupyter-notebook-files"></a>Jupyter Notebook ファイル

Machine Learning ワークスペースにアップロードしたファイルを確認してみましょう。 このチュートリアル部分のアクティビティは 2 つのノートブック ファイルにまたがっており、そこでいくつかの関連ファイルが使用されています。

* **01-turbofan\_regression.ipynb**: このノートブックは、Machine Learning ワークスペースを使用して、機械学習実験を作成および実行します。 大まかに言うと、このノートブックは次のステップを実行するものです。

  1. デバイス ハーネスによって生成されたデータを Azure Storage アカウントからダウンロードします。
  1. データを確認および準備してから、そのデータを使用して分類子モデルをトレーニングします。
  1. テスト データセット (Test\_FD003.txt) を使用して、実験済みのモデルを評価します。
  1. 最良の分類子モデルを Machine Learning ワークスペースに発行します。

* **02-turbofan\_deploy\_model.ipynb**: このノートブックは、前のノートブックで作成したモデルを受け取り、それを使用して、IoT Edge デバイスにデプロイできるコンテナー イメージを作成します。 このノートブックは、次のステップを実行します。

  1. モデルのスコアリング スクリプトを作成します。
  1. Machine Learning ワークスペースに保存した分類子モデルを使用して、コンテナー イメージを生成します。
  1. イメージは、Azure Container Instances 上で Web サービスとしてデプロイします。
  1. Web サービスを使用して、モデルとイメージが期待どおりに機能することを検証します。 検証済みのイメージは、このチュートリアルの「[カスタム IoT Edge モジュールの作成とデプロイ](tutorial-machine-learning-edge-06-custom-modules.md)」の部分で IoT Edge デバイスにデプロイします。

* **Test\_FD003.txt**: このファイルには、トレーニング済みの分類子を検証するときにテスト セットとして使用するデータが含まれています。 簡潔にするために、元のコンテストで提供されたテスト データをテスト セットとして使用することにしました。
* **RUL\_FD003.txt**: このファイルには、Test\_FD003.txt ファイル内の各デバイスの最後のサイクルの残存耐用年数 (RUL) が含まれています。 データの詳細な説明については、C:\\source\\IoTEdgeAndMlSample\\data\\Turbofan 内の readme.txt および Damage Propagation Modeling.pdf ファイルを参照してください。
* **Utils.py**: このファイルには、データを操作するための Python ユーティリティ関数のセットが含まれています。 最初のノートブックに関数の詳細な説明が含まれています。
* **README.md**: この readme ファイルでは、ノートブックの使用方法が説明されています。

## <a name="run-the-jupyter-notebooks"></a>Jupyter ノートブックを実行する

ワークスペースが作成されたら、ノートブックを実行できます。

1. **[My files]\(マイ ファイル\)** ページから **[01-turbofan\_regression.ipynb]** を選択します。

    :::image type="content" source="media/tutorial-machine-learning-edge-04-train-model/select-turbofan-notebook.png" alt-text="最初に実行するノートブックの選択方法を示すスクリーンショット。":::

1. ノートブックが "**信頼なし**" と表示される場合は、ノートブックの右上隅にある **[信頼なし]** ウィジェットを選択します。 ダイアログ ボックスが表示されたら、 **[信頼する]** を選択します。

1. 最良の結果を得るためには、各セルのドキュメントを読み取って個別に実行します。 ツール バーの **[Run]\(実行\)** を選択します。 しばらくすると、複数のセルを実行するのが得策であることがわかります。 アップグレードと非推奨化の警告は無視してかまいません。

    セルが実行されているときは、角かっこで囲まれたアスタリスク ([\*]) が表示されます。 セルの処理が完了すると、アスタリスクが番号で置き換えられます。関連する出力が表示されることもあります。 ノートブックのセルは逐次的にビルドされ、実行できるセルは一度に 1 つだけです。

    また、 **[セル]** メニューの実行オプションを使用することもできます。 **Ctrl + Enter** キーを押すとセルが実行されます。**Shift + Enter** キーを押すと、セルが実行された後に次のセルに進みます。

    > [!TIP]
    > ブラウザーの複数のタブから同じノートブックを実行することは、セルの処理の一貫性を確保するため避けてください。

1. **[Set global properties]\(グローバル プロパティを設定する\)** の手順の後のセルで、ご利用の Azure サブスクリプション、設定、リソースの値を入力します。 そのうえで、セルを実行します。

    ![ノートブックでグローバル プロパティを設定する方法を示すスクリーンショット。](media/tutorial-machine-learning-edge-04-train-model/set-global-properties.png)

1. **[ワークスペースの詳細]** の前のセルで、その実行後に認証のためのサインインを指示するリンクを探します。

    ![デバイス認証のためのサインイン プロンプトを示すスクリーンショット。](media/tutorial-machine-learning-edge-04-train-model/sign-in-prompt.png)

    リンクを開き、指定されたコードを入力します。 このサインイン手順では、Microsoft Azure クロスプラットフォーム コマンド ライン インターフェイスを使用して、Jupyter ノートブックの認証を行って Azure リソースにアクセスします。

    ![デバイスでのアプリケーション認証の確認を示すスクリーンショット。](media/tutorial-machine-learning-edge-04-train-model/cross-platform-cli.png)

1. **[Explore the results]\(結果を調査する\)** の前のセルから実行 ID の値をコピーし、 **[Reconstitute a run]\(実行を再構成する\)** の後のセルの実行 ID に貼り付けます。

   ![セル間で実行 ID をコピーする方法を示すスクリーンショット。](media/tutorial-machine-learning-edge-04-train-model/automl-id.png)

1. ノートブックで残りのセルを実行します。

1. ノートブックを保存して、プロジェクト ページに戻ります。

1. **02-turbofan\_deploy\_model.ipynb** を開いて、各セルを実行します。 **[Configure workspace]\(ワークスペースを構成する\)** の後のセルで、サインインして認証を行う必要があります。

1. ノートブックを保存して、プロジェクト ページに戻ります。

### <a name="verify-success"></a>成功を確認する

ノートブックが正常に完了したことを確かめるために、いくつかの項目が作成されていることを確認します。

1. Machine Learning ノートブックの **[My files]\(マイ ファイル\)** タブで、 **[最新の情報に更新]** を選択します。

1. 次のファイルが作成されていることを確認します。

    | ファイル | 説明 |
    | --- | --- |
    | ./aml_config/.azureml/config.json | Machine Learning ワークスペースの作成に使用される構成ファイル。 |
    | ./aml_config/model_config.json | Azure の **turbofanDemo** Machine Learning ワークスペースにモデルをデプロイするために必要となる構成ファイル。 |
    | myenv.yml| デプロイされた機械学習モデルの依存関係に関する情報を提供します。|

1. 次の Azure リソースが作成されていることを確認します。 一部のリソース名には、ランダムな文字が追加されています。

    | Azure リソース | 名前 |
    | --- | --- |
    | Azure Machine Learning ワークスペース | turborfanDemo |
    | Azure Container Registry | turbofandemoxxxxxxxx |
    | Application Insights | turbofaninsightxxxxxxxx |
    | Azure Key Vault | turbofankeyvaultbxxxxxxxx |
    | Azure Storage | turbofanstoragexxxxxxxxx |

### <a name="debugging"></a>デバッグ

ノートブックには、値を表示するための `print()` コマンドなど、デバッグ用の Python ステートメントを挿入することができます。 定義されていない変数やオブジェクトが見つかった場合は、それらが最初に宣言またはインスタンス化されているセルを実行してください。

ノートブックを再実行する必要がある場合は、先ほど作成したファイルと Azure リソースは削除する必要があります。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このチュートリアルはセットの一部であり、各記事は前の記事の作業が行われたことが前提になっています。 最後のチュートリアルを完了するまで、リソースのクリーンアップはしないでください。

## <a name="next-steps"></a>次のステップ

この記事では、Machine Learning スタジオで実行される 2 つの Jupyter ノートブックを使用し、turbofan デバイスからのデータを使用して次を実行しました。

- RUL 分類子をトレーニングする。
- 分類子をモデルとして保存する。
- コンテナー イメージを作成する。
- イメージを Web サービスとしてデプロイしてテストする。

次の記事に進んで、IoT Edge デバイスを作成してください。

> [!div class="nextstepaction"]
> [IoT Edge デバイスを構成する](tutorial-machine-learning-edge-05-configure-edge-device.md)