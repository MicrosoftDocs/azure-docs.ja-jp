---
title: モデルのトレーニングとデプロイ - Azure IoT Edge での Machine Learning | Microsoft Docs
description: Azure Machine Learning を使用して機械学習モデルをトレーニングし、そのモデルを、Azure IoT Edge モジュールとしてデプロイできるコンテナー イメージにパッケージ化します。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/13/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: acf0b1984eb3e68858be6ed68731612448e672f4
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67432699"
---
# <a name="tutorial-train-and-deploy-an-azure-machine-learning-model"></a>チュートリアル:Azure Machine Learning モデルをトレーニングしてデプロイする

> [!NOTE]
> この記事は、IoT Edge 上での Azure Machine Learning の使用に関するチュートリアルのシリーズの一部です。 この記事に直接アクセスしている場合は、効果を最大限に高めるために、シリーズの[最初の記事](tutorial-machine-learning-edge-01-intro.md)から始めることをお勧めします。

この記事では、最初に Azure Notebooks で Azure Machine Learning を使用して機械学習モデルをトレーニングし、そのモデルを、Azure IoT Edge モジュールとしてデプロイできるコンテナー イメージにパッケージ化します。 Azure Notebooks では、機械学習モデルの実験、トレーニング、およびデプロイに使用される基本ブロックである Azure Machine Learning service ワークスペースを利用します。

チュートリアルのこの部分のアクティビティは、2 つのノートブックに分割されています。

* **01-turbofan\_regression.ipynb:** このノートブックでは、Azure Machine Learning を使用してモデルをトレーニングおよび公開する手順を実行します。 大まかに言って、関連する手順は次のとおりです。

  1. トレーニング データをダウンロード、準備、および探索する
  2. サービス ワークスペースを使用して、機械学習実験を作成および実行する
  3. 実験からのモデルの結果を評価する
  4. 最適なモデルをサービス ワークスペースに公開する

* **02-turbofan\_deploy\_model.ipynb:** このノートブックは、前のノートブックで作成したモデルを受け取り、それを使用して、Azure IoT Edge デバイスにデプロイできるコンテナー イメージを作成します。

  1. モデルのスコアリング スクリプトを作成する
  2. イメージを作成して公開する
  3. Azure コンテナー インスタンス上でイメージを Web サービスとしてデプロイする
  4. Web サービスを使用して、モデルとイメージが期待どおりに機能することを検証する

一般的に、この記事の手順は、データ サイエンティストによって実行される可能性があります。

## <a name="set-up-azure-notebooks"></a>Azure Notebooks を設定する

Azure Notebooks を使用して、2 つの Jupyter ノートブックとサポート ファイルをホストします。 ここでは、Azure Notebooks プロジェクトを作成して構成します。 Jupyter Notebook や Azure Notebooks を使用したことがない方のために、いくつかの入門用ドキュメントを次に示します。

* **クイックスタート:** [ノートブックの作成と共有](../notebooks/quickstart-create-share-jupyter-notebook.md)
* **チュートリアル:** [Python を使用して Jupyter ノートブックを作成し、実行する](../notebooks/tutorial-create-run-jupyter-notebook.md)

以前の開発者仮想マシンと同様に、Azure Notebooks を使用することで、演習のための一貫した環境を確保できます。

> [!NOTE]
> 設定すると、任意のマシンから Azure Notebooks サービスにアクセスできるようになります。 設定中は、必要なファイルが揃った開発用仮想マシンを使用する必要があります。

### <a name="create-an-azure-notebooks-account"></a>Azure Notebooks アカウントを作成する

Azure Notebook アカウントは、Azure サブスクリプションから独立しています。 Azure Notebooks を使用するには、アカウントを作成する必要があります。

1. [Azure Notebooks](https://notebooks.azure.com) に移動します。

2. ページの右上隅にある **[サインイン]** をクリックします。

3. 自分の職場または学校アカウント (Azure Active Directory) または個人用アカウント (Microsoft アカウント) でサインインします。

4. これまでに Azure Notebooks を使用したことがない場合は、Azure Notebooks アプリについてアクセスを許可するように求められます。

5. Azure Notebooks のユーザー ID を作成します。

### <a name="upload-jupyter-notebooks-files"></a>Jupyter ノートブック ファイルをアップロードする

この手順では、新しい Azure Notebooks プロジェクトを作成し、そこにファイルをアップロードします。 具体的には、次のファイルをアップロードします。

* **01-turbofan\_regression.ipynb**: この Jupyter ノートブック ファイルでは、デバイス ハーネスによって生成されたデータを Azure ストレージ アカウントからダウンロードする、分類子をトレーニングするためのデータを探索および準備する、モデルをトレーニングする、Test\_FD003.txt ファイルにあるテスト データセットを使用してデータをテストする、最後に分類子モデルを Machine Learning service ワークスペースに保存するというプロセスを実行します。

* **02-turbofan\_deploy\_model.ipynb:** この Jupyter ノートブックでは、Machine Learning service ワークスペースに保存されている分類子モデルを使用してコンテナー イメージを生成するプロセスを実行します。 イメージが作成されると、イメージを Web サービスとしてデプロイするプロセスがノートブックによって実行されるため、イメージが期待どおりに機能していることを検証できます。 検証済みのイメージは、このチュートリアルの「[カスタム IoT Edge モジュールの作成とデプロイ](tutorial-machine-learning-edge-06-custom-modules.md)」の部分で Edge デバイスにデプロイします。

* **Test\_FD003.txt:** このファイルには、トレーニング済み分類子を検証するときにテスト セットとして使用するデータが含まれています。 例を簡単にするために、元のコンテストで提供されたテスト データをテスト セットとして使用することにしました。

* **RUL\_FD003.txt:** このファイルには、Test\_FD003.txt ファイル内の各デバイスの最後のサイクルの RUL が含まれています。 データの詳細な説明については、C:\\source\\IoTEdgeAndMlSample\\data\\Turbofan 内の **readme.txt** および **Damage Propagation Modeling.pdf** ファイルを参照してください。

* **Utils.py:** データを操作するための Python ユーティリティ関数のセットが含まれています。 最初のノートブックに関数の詳細な説明が含まれています。

* **README.md:** ノートブックの用途を説明する Readme。

新しいプロジェクトを作成し、ノートブックにファイルをアップロードします。

1. 上部のメニュー バーから **[マイ プロジェクト]** を選択します。

1. **[+ 新しいプロジェクト]** を選択します。 名前と ID を指定します。 プロジェクトを公開する必要も、Readme を用意する必要もありません。

1. **[アップロード]** を選択し、 **[From Computer]\(コンピューターから\)** を選択します。

1. **[ファイルの選択]** を選択します。

1. **C:\source\IoTEdgeAndMlSample\AzureNotebooks** に移動します。 すべてのファイルを選択し、 **[開く]** をクリックします。

1. **[アップロード]** を選択してアップロードを開始します。プロセスが完了したら、 **[完了]** を選択します。

## <a name="run-azure-notebooks"></a>Azure Notebooks を実行する

プロジェクトが作成されたので、**01-turbofan\_regression.ipynb** ノートブックを実行します。

1. turbofan プロジェクト ページから、**01-turbofan\_regression.ipynb** を選択します。

    ![実行する最初のノートブックを選択する](media/tutorial-machine-learning-edge-04-train-model/select-turbofan-regression-notebook.png)

2. メッセージが表示されたら、ダイアログから Python 3.6 カーネルを選択し、 **[Set Kernel]\(カーネルの設定\)** を選択します。

3. ノートブックが "**信頼なし**" と表示される場合は、ノートブックの右上にある **[信頼なし]** ウィジェットをクリックします。 ダイアログが表示されたら、 **[信頼する]** を選択します。

4. ノートブックの指示に従います。

    * セルを実行するには、`Ctrl + Enter` キーを押します。
    * セルを実行し、次のセルに移動するには、`Shift + Enter` キーを押します。
    * セルが実行されているときは、 **[\*]** のように角かっこで囲まれたアスタリスクが表示されます。 セルが完了すると、アスタリスクが番号に置き換えられます。関連する出力が下に表示されることもあります。 セルは前のセルの作業に基づいて構築されることが多いため、一度に実行できるセルは 1 つだけです。

5. **01-turbofan\_regression.ipynb** ノートブックの実行が終了したら、プロジェクト ページに戻ります。

6. **02-turbofan\_deploy\_model.ipynb** を開き、このセクションの手順を繰り返して 2 番目のノートブックを実行します。

## <a name="next-steps"></a>次の手順

この記事では、Azure Notebooks で実行される 2 つの Jupyter ノートブックを使用して、ターボファン デバイスからのデータを使用した残存耐用時間数 (RUL) 分類子のトレーニング、モデルとしての分類子の保存、コンテナー イメージの作成、Web サービスとしてのイメージのデプロイおよびテストを行いました。

次の記事に進んで、IoT Edge デバイスを作成してください。

> [!div class="nextstepaction"]
> [IoT Edge デバイスを構成する](tutorial-machine-learning-edge-05-configure-edge-device.md)
