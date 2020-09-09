---
title: チュートリアル:Jupyter Notebook での実験 (Python)
titleSuffix: Machine Learning - Azure
description: このチュートリアルでは、Jupyter Notebook で実行されている Azure Machine Learning Python SDK を初めて使う場合について説明します。  パート 1 では、実験と ML モデルを管理するワークスペースを作成します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 02/10/2020
ms.custom: devx-track-python
ms.openlocfilehash: ff23a42d9b96b8411d8b2f82ab8303e2a8a69953
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87852720"
---
# <a name="tutorial-get-started-creating-your-first-ml-experiment-with-the-python-sdk"></a>チュートリアル:Python SDK で初めての ML 実験を作成する
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

このチュートリアルでは、Jupyter ノートブックで実行されている Azure Machine Learning Python SDK の使用を開始するためのエンドツーエンドの手順を完了します。 このチュートリアルは、**2 部構成のチュートリアル シリーズのパート 1** です。Python 環境の設定と構成のほか、実験と機械学習モデルを管理するためのワークスペースの作成について説明します。 [**パート 2**](tutorial-1st-experiment-sdk-train.md) では、これを基に、複数の機械学習モデルをトレーニングし、Azure Machine Learning Studio と SDK の両方を使用したモデル管理プロセスを紹介します。

このチュートリアルでは、次のことを行いました。

> [!div class="checklist"]
> * 次のチュートリアルで使用する [Azure Machine Learning ワークスペース](concept-workspace.md)を作成します。
> * チュートリアル ノートブックをワークスペース内のフォルダーに複製します。
> * Azure Machine Learning Python SDK がインストールされ、事前構成されたクラウドベースのコンピューティング インスタンスを作成します。


Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning](https://aka.ms/AMLFree) を今すぐお試しください。

## <a name="create-a-workspace"></a>ワークスペースの作成

Azure Machine Learning ワークスペースは、機械学習モデルを実験、トレーニング、およびデプロイするために使用する、クラウドでの基本的なリソースです。 ワークスペースは、Azure サブスクリプションとリソース グループを、サービス内の簡単に使用できるオブジェクトに結び付けます。 

ワークスペースを作成するには、Azure リソースを管理するための Web ベースのコンソールである Azure portal を使用します。 

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

>[!IMPORTANT] 
> お使いの**ワークスペース**と**サブスクリプション**をメモしておきます。 これらは、適切な場所に実験を作成するために必要になります。 

## <a name="run-notebook-in-your-workspace"></a><a name="azure"></a>ワークスペースでノートブックを実行する

このチュートリアルでは、インストール不要であらかじめ構成されているエクスペリエンスを実現するために、お使いのワークスペースでクラウド ノートブック サーバーを使用します。 お使いの環境、パッケージ、および依存関係を制御したい場合は、[独自の環境](how-to-configure-environment.md#local)を使用してください。

 次のビデオに従って、または後で示す詳細な手順を使用して、ワークスペースでチュートリアルを複製して実行します。 

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4mTUr]

### <a name="clone-a-notebook-folder"></a>ノートブック フォルダーを複製する

Azure Machine Learning Studio で、次の実験の設定を完了し、ステップを実行します。Azure Machine Learning Studio は、あらゆるスキル レベルのデータ サイエンス実務者がデータ サイエンス シナリオを実行するための機械学習ツールを含む統合インターフェイスです。

1. [Azure Machine Learning Studio](https://ml.azure.com/) にサインインします。

1. お使いのサブスクリプションと、作成したワークスペースを選択します。

1. 左側にある **[ノートブック]** を選択します。

1. 上部にある **[サンプル]** タブを選択します。

1. **Python** フォルダーを開きます。

1. バージョン番号が付いたフォルダーを開きます。  この番号は、Python SDK の現在のリリースを表します。

1. **tutorials** フォルダーの右側にある **[...]** を選択し、 **[Clone]\(複製\)** を選択します。

    :::image type="content" source="media/tutorial-1st-experiment-sdk-setup/clone-tutorials.png" alt-text="tutorials フォルダーを複製する":::

1. フォルダーの一覧には、ワークスペースにアクセスする各ユーザーが表示されます。  自分のフォルダーを選択して **tutorials** フォルダーをそこに複製します。

### <a name="open-the-cloned-notebook"></a><a name="open"></a>複製されたノートブックを開く

1. **[User Files]\(ユーザー ファイル\)** セクションに複製された **tutorials** フォルダーを開きます。

    > [!IMPORTANT]
    > **samples** フォルダー内のノートブックを表示できますが、そこからノートブックを実行することはできません。  ノートブックを実行するには、必ず **[User Files]\(ユーザー ファイル\)** セクションにあるノートブックの複製バージョンを開いてください。
    
1. **tutorials/create-first-ml-experiment** フォルダー内の **tutorial-1st-experiment-sdk-train.ipynb** ファイルを選択します。

    :::image type="content" source="media/tutorial-1st-experiment-sdk-setup/expand-user-folder.png" alt-text="tutorials フォルダーを開く":::


1. 上部のバーで、ノートブックの実行に使用するコンピューティング インスタンスを選択します。 これらの VM には、[Azure Machine Learning を実行するために必要なすべてのもの](concept-compute-instance.md#contents)があらかじめ構成されています。 

1. VM が見つからない場合は、 **[追加]** を選択してコンピューティング インスタンス VM を作成します。 

    1. VM を作成する場合は、以下のルールに従います。  
        + 名前は必須であり、空にすることはできません。
        + 名前は、ワークスペースまたはコンピューティング インスタンスの Azure リージョン内のすべての既存のコンピューティング インスタンス全体で一意である必要があります (大文字小文字は区別されません)。 選択した名前が一意でない場合は、アラートが表示されます。
        + 有効な文字は、大文字、小文字、数字 (0 から 9)、およびダッシュ (-) です。
        + 名前の長さは 3 から 24 文字の間でなければなりません。
        + 名前の先頭は文字である必要があります (数字やダッシュは使用できません)。
        + ダッシュが使用されている場合は、その後に少なくとも 1 文字を続ける必要があります。 例:Test-、test-0、test-01 は無効であり、test-a0、test-0a は有効な例です。

    1.  利用可能な選択肢から仮想マシンのサイズを選択します。 このチュートリアルでは、既定の VM を選択するのが適切です。

    1. **[作成]** を選択します。 VM の設定には約 5 分かかる可能性があります。

1. VM が使用可能になると、上部のツール バーに表示されます。  これで、ツール バーの **[Run all]\(すべて実行\)** を使用するか、ノートブックのコード セルで **Shift + Enter** キーを使用して、ノートブックを実行できるようになりました。

カスタム ウィジェットがある場合、または Jupyter/JupyterLab を使用する場合は、右端にある **[Jupyter]** ドロップダウンを選択し、 **[Jupyter]** または **[JupyterLab]** を選択します。 新しいブラウザー ウィンドウが開きます。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、次のタスクを完了しました。

* Azure Machine Learning ワークスペースを作成しました。
* お使いのワークスペース内にクラウド ノートブック サーバーを作成して構成しました。

チュートリアルの**パート 2** では、`tutorial-1st-experiment-sdk-train.ipynb` のコードを実行して機械学習モデルをトレーニングします。 

> [!div class="nextstepaction"]
> [チュートリアル:最初のモデルをトレーニングする](tutorial-1st-experiment-sdk-train.md)

> [!IMPORTANT]
> このチュートリアルのパート 2 や他のチュートリアルに取り組む予定がない場合は、不使用時のコストを抑えるために、[クラウド ノートブック サーバー VM を停止](tutorial-1st-experiment-sdk-train.md#clean-up-resources)してください。


