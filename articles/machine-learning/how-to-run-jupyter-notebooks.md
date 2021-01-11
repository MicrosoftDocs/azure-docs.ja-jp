---
title: ワークスペースで Jupyter Notebooks を実行する方法
titleSuffix: Azure Machine Learning
description: Azure Machine Learning Studio のワークスペースから離れずに Jupyter Notebooks を実行する方法について説明します。
services: machine-learning
author: abeomor
ms.author: osomorog
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 06/27/2020
ms.openlocfilehash: 861fcabbfca07cb342fda42ea2425fa290a1598e
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2020
ms.locfileid: "87386454"
---
# <a name="how-to-run-jupyter-notebooks-in-your-workspace"></a>ワークスペースで Jupyter Notebooks を実行する方法
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure Machine Learning Studio のワークスペースで Jupyter Notebooks を直接実行する方法について説明します。 [Jupyter](https://jupyter.org/) または [JupyterLab](https://jupyterlab.readthedocs.io) を起動できますが、ワークスペースから離れずにノートブックを編集して実行することもできます。

次のことを実行できます。

* ワークスペースで Jupyter Notebooks を作成する
* ノートブックから実験を実行する
* ノートブック環境を変更する
* ノートブックの実行に使用されるコンピューティング インスタンスの詳細を検索する

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://aka.ms/AMLFree) を作成してください。
* Machine Learning ワークスペース。 [Azure Machine Learning ワークスペースを作成する](how-to-manage-workspace.md)方法に関するページを参照してください。

## <a name="create-notebooks"></a><a name="create"></a> ノートブックを作成する

Azure Machine Learning ワークスペースで、新しい Jupyter Notebooks を作成して作業を開始します。 新しく作成されたノートブックは、既定のワークスペース ストレージに格納されます。 このノートブックは、ワークスペースにアクセスできるすべてのユーザーと共有できます。 

新しいノートブックを作成するには: 

1. [Azure Machine Learning Studio](https://ml.azure.com) で、ワークスペースを開きます。
1. 左側にある **[Notebooks]\(ノートブック\)** を選択します。 
1. **[My files]\(マイ ファイル\)** セクションの **[User files]\(ユーザー ファイル\)** リストの上にある **[新しいファイルの作成]** アイコンを選択します。

    :::image type="content" source="media/how-to-run-jupyter-notebooks/create-new-file.png" alt-text="[新しいファイルの作成]":::

1. ファイルに名前を付けます。 
1. Jupyter Notebook Files の場合は、ファイルの種類として **[Python Notebook]\(Python ノートブック\)** を選択します。
1. ファイル ディレクトリを選択します。
1. **［作成］** を選択します

テキスト ファイルを作成することもできます。  ファイルの種類として **[テキスト]** を選択し、拡張子を名前に追加します (たとえば、myfile.py、myfile.txt)。  

[Notebooks]\(ノートブック\) ページの上部にあるツールを使用して、ノートブックを含むフォルダーとファイルをアップロードすることもできます。  ノートブックとほとんどのテキスト ファイルの種類はプレビュー セクションに表示されます。  他のほとんどのファイルの種類ではプレビューを使用できません。

> [!IMPORTANT]
> ノートブックやスクリプトの内容によっては、Azure の組織なしでセッションからデータを読み取ったり、データにアクセスしたりできる可能性があります。  信頼できるソースのファイル以外は読み込まないでください。 詳細については、[ソース コードのベスト プラクティス](concept-secure-code-best-practice.md#azure-ml-studio-notebooks)に関する記事をご覧ください。

### <a name="clone-samples"></a>サンプルを複製する

ワークスペースには **Samples** フォルダーがあり、SDK を探索するために役立ち、独自の機械学習プロジェクトの例として利用できるように設計されたノートブックが格納されています。  これらのノートブックをワークスペース ストレージ コンテナーに作成したフォルダーに複製することができます。  

例については、「[Tutorial:初めての ML 実験を作成する](tutorial-1st-experiment-sdk-setup.md#azure)」を参照してください。

### <a name="use-files-from-git-and-version-my-files"></a><a name="terminal"></a> Git のファイルを使用し、ファイルのバージョン管理を行う

ターミナル ウィンドウを使用してすべての Git 操作にアクセスできます。 すべての Git ファイルとフォルダーは、ワークスペース ファイル システムに格納されます。

> [!NOTE]
> すべての Jupyter 環境で表示されるように、 **~/cloudfiles/code/Users** フォルダー以下の任意の場所にファイルとフォルダーを追加します。

ターミナルにアクセスするには:

1. [Azure Machine Learning Studio](https://ml.azure.com) で、ワークスペースを開きます。
1. 左側にある **[Notebooks]\(ノートブック\)** を選択します。
1. 左側の **[User files]\(ユーザー ファイル\)** セクションにある任意のノートブックを選択します。  ノートブックがない場合は、まず[ノートブックを作成](#create)します
1. **[コンピューティング]** ターゲットを選択するか、新しいターゲットを作成し、それが実行されるまで待ちます。
1. **[Open terminal]\(ターミナルを開く\)** アイコンを選択します。

    :::image type="content" source="media/how-to-run-jupyter-notebooks/open-terminal.png" alt-text="ターミナルを開きます":::

1. アイコンが表示されない場合は、コンピューティング ターゲットの右側にある **[...]** を選択し、 **[Open terminal]\(ターミナルを開く\)** を選択します。

    :::image type="content" source="media/how-to-run-jupyter-notebooks/alt-open-terminal.png" alt-text="[...] の [Open terminal]\(ターミナルを開く\)":::


詳細については、[Git リポジトリをワークスペース ファイル システムに複製する](concept-train-model-git-integration.md#clone-git-repositories-into-your-workspace-file-system)方法に関する記事を参照してください。


### <a name="share-notebooks-and-other-files"></a>ノートブックとその他のファイルを共有する

ノートブックまたはファイルを共有するには、URL をコピーして貼り付けます。  ワークスペースの他のユーザーのみがこの URL にアクセスできます。  詳細については、[ワークスペースへのアクセスの許可](how-to-assign-roles.md)に関する記事を参照してください。

## <a name="edit-a-notebook"></a>ノートブックを編集する

ノートブックを編集するには、ワークスペースの **[User files]\(ユーザー ファイル\)** セクションにあるノートブックを開きます。 編集するセルをクリックします。 

ノートブックは、コンピューティング インスタンスに接続することなく編集できます。  ノートブックのセルを実行する場合は、コンピューティング インスタンスを選択または作成します。  停止したコンピューティング インスタンスを選択した場合は、最初のセルを実行すると自動的に開始されます。

コンピューティング インスタンスの実行中に、任意の Python ノートブックで [Intellisense](https://code.visualstudio.com/docs/editor/intellisense) によるコード補完を使用することもできます。

Notebook ツールバーから Jupyter または JupyterLab を起動することもできます。  Azure Machine Learning では、Jupyter または JupyterLab からの更新プログラムの提供やバグの修正は行われません。これは、Microsoft サポートの範囲に含まれないオープン ソース製品であるためです。

### <a name="use-intellisense"></a>IntelliSense を使用する

[IntelliSense](https://code.visualstudio.com/docs/editor/intellisense) は、次のような多くの機能を備えたコード補完支援機能です: メンバーの一覧表示、パラメーター ヒント、クイック ヒント、入力候補。 これらの機能を使うと、数回のキーストロークだけで使用するコードの詳細を確認したり、入力するパラメーターを追跡したり、プロパティやメソッドの呼び出しを追加したりできます。  

コードを入力するときは、Ctrl + Space キーを使用して IntelliSense をトリガーします。

### <a name="save-and-checkpoint-a-notebook"></a>ノートブックを保存およびチェックポイントする

 *ipynb* ファイルを作成すると、Azure Machine Learning によってチェックポイント ファイルが作成されます。

ノートブック ツール バーでメニューを選択し、 **[ファイル] &gt; [Save and checkpoint]\(保存とチェックポイント\)** の順に選択して、手動でノートブックを保存します。そうすると、ノートブックに関連付けられたチェックポイント ファイルが追加されます。

:::image type="content" source="media/how-to-run-jupyter-notebooks/file-save.png" alt-text="ノートブック ツール バーの保存ツールのスクリーンショット":::

すべてのノートブックは 30 秒ごとに自動保存されます。 自動保存では、チェックポイント ファイルではなく、最初の *ipynb* ファイルのみが更新されます。
 
ノートブックのメニューで **[チェックポイント]** を選択して、名前付きチェックポイントを作成し、保存されているチェックポイントにノートブックを戻すことができます。


### <a name="useful-keyboard-shortcuts"></a>便利なキーボード ショートカット

|[キーボード]  |アクション  |
|---------|---------|
|Shift + Enter     |  セルを実行する       |
|Ctrl + Space | IntelliSense をアクティブにする |
|Ctrl + M (Windows)     |  ノートブックのタブ トラップを有効または無効にします。       |
|Ctrl + Shift + M (Mac と Linux)     |    ノートブックのタブ トラップを有効または無効にします。     |
|Tab (タブ トラップが有効な場合) | '\t' 文字 (インデント) を追加します
|Tab (タブ トラップが無効な場合) | フォーカスを次のフォーカス可能な項目に変更します (セルの削除ボタン、実行ボタンなど)

## <a name="delete-a-notebook"></a>ノートブックを削除する

**Samples** ノートブックを削除することは "*できません*"。  これらのノートブックは Studio の一部であり、新しい SDK が発行されるたびに更新されます。  

**ユーザー ファイル** ノートブックは、次のいずれかの方法で削除 "*できます*"。

* Studio で、フォルダーまたはファイルの末尾にある **[...]** を選択します。  必ずサポートされているブラウザー (Microsoft Edge、Chrome、または Firefox) を使用してください。
* 任意の Notebook ツールバーから [ **[Open terminal]\(ターミナルを開く\)** ](#terminal) を選択して、コンピューティング インスタンスのターミナル ウィンドウにアクセスします。
* Jupyter または JupyterLab で付属のツールを使用します。

## <a name="run-an-experiment"></a>実験を実行する

ノートブックから実験を実行するには、まず、実行中の[コンピューティング インスタンス](concept-compute-instance.md)に接続します。 コンピューティング インスタンスがない場合は、次の手順に従って作成します。 

1. Notebook ツールバーの **[+]** を選択します。 
2. コンピューティングに名前を付け、 **[仮想マシン サイズ]** を選択します。 
3. **［作成］** を選択します
4. コンピューティング インスタンスがノートブックに自動的に接続され、セルを実行できるようになります。

作成したコンピューティング インスタンスを表示および使用できるのは自分のみです。  **ユーザー ファイル**は VM とは別に格納され、ワークスペース内のすべてのコンピューティング インスタンス間で共有されます。

### <a name="view-logs-and-output"></a>ログと出力を表示する

[Notebook ウィジェット](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py)を使用して、実行の進行状況とログを表示します。 ウィジェットは非同期であり、トレーニングが終了するまで更新情報が表示されます。 Azure Machine Learning ウィジェットは、Jupyter および JupterLab でもサポートされています。

## <a name="change-the-notebook-environment"></a>ノートブック環境を変更する

Notebook ツールバーを使用すると、ノートブックを実行する環境を変更できます。  

このようなアクションを実行しても、ノートブックの状態またはノートブック内の変数の値は変更されません。

|アクション  |結果  |
|---------|---------| --------|
|カーネルを停止する     |  任意の実行中のセルを停止します。 セルを実行すると、カーネルが自動的に再起動されます。 |
|別のワークスペース セクションに移動する     |     実行中のセルは停止されます。 |

これらのアクションを実行すると、ノートブックの状態はリセットされ、ノートブック内のすべての変数がリセットされます。

|アクション  |結果  |
|---------|---------| --------|
| カーネルを変更する | Notebook に新しいカーネルが使用されます |
| コンピューティングを切り替える    |     Notebook には新しいコンピューティングが自動的に使用されます。 |
| コンピューティングをリセットする | セルを実行しようとすると再起動します |
| コンピューティングを停止する     |    セルは実行されません  |
| Jupyter または JupyterLab でノートブックを開く     |    新しいタブでノートブックが開きます。  |

### <a name="add-new-kernels"></a>新しいカーネルを追加する

Notebook によって、接続されたコンピューティング インスタンスにインストールされているすべての Jupyter カーネルが自動的に検出されます。  コンピューティング インスタンスにカーネルを追加するには:

1. Notebook ツールバーの [ **[Open terminal]\(ターミナルを開く\)** ](#terminal) を選択します。
1. ターミナル ウィンドウを使用して、新しい環境を作成します。  たとえば、次のコードでは `newenv` が作成されます。
    ```shell
    conda create --name newenv
    ```
1. 環境をアクティブにします。  `newenv` を作成した後の例を次に示します。

    ```shell
    conda activate newenv
    ```
1. pip と ipykernel パッケージを新しい環境にインストールし、その conda 環境用のカーネルを作成します

    ```shell
    conda install pip
    conda install ipykernel
    python -m ipykernel install --user --name newenv --display-name "Python (newenv)"
    ```

任意の[使用可能な Jupyter カーネル](https://github.com/jupyter/jupyter/wiki/Jupyter-kernels)をインストールできます。

### <a name="status-indicators"></a>状態インジケーター

**[コンピューティング]** ドロップダウンの横のインジケーターには、その状態が表示されます。  状態は、ドロップダウン リストにも表示されます。  

|Color |コンピューティング状態 |
|---------|---------| 
| [緑] | コンピューターは実行中 |
| [赤] |コンピューティングの失敗 | 
| Black | コンピューティングの停止 |
|  淡い青 |コンピューティングの作成、開始、再起動、設定 |
|  グレー |コンピューティングの削除、停止 |

**[カーネル]** ドロップダウンの横にあるインジケーターには、その状態が表示されます。

|Color |カーネルの状態 |
|---------|---------|
|  [緑] |カーネル接続済み、アイドル、ビジー|
|  グレー |カーネルが接続されていません |

## <a name="find-compute-details"></a>コンピューティングの詳細を確認する 

コンピューティング インスタンスの詳細については、[Studio](https://ml.azure.com) の**コンピューティング**に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

* [最初の実験を実行する](tutorial-1st-experiment-sdk-train.md)
* [スナップショットを使用してファイル ストレージをバックアップする](../storage/files/storage-snapshots-files.md)
