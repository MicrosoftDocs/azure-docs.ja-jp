---
title: ワークスペースでコンピューティング インスタンスのターミナルにアクセスする方法
titleSuffix: Azure Machine Learning
description: Git 操作にコンピューティング インスタンス上のターミナルを使用して、パッケージをインストールし、カーネルを追加します。
services: machine-learning
author: abeomor
ms.author: osomorog
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 02/05/2021
ms.openlocfilehash: e42d6d53e4d06e5ec7a33ea4b8ca18233dcf1dd7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100101043"
---
# <a name="access-a-compute-instance-terminal-in-your-workspace"></a>ワークスペースでコンピューティング インスタンスのターミナルにアクセスする

自分のワークスペースでコンピューティング インスタンスのターミナルにアクセスして、次の操作を行います。

* Git のファイルを使用してファイルのバージョン管理を行う。 これらのファイルは、1 つのコンピューティング インスタンスに限定されるのではなく、自分のワークスペース ファイル システムに格納されます。
* コンピューティング インスタンスにパッケージをインストールする。
* コンピューティング インスタンスに追加のカーネルを作成する。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://aka.ms/AMLFree) を作成してください。
* Machine Learning ワークスペース。 [Azure Machine Learning ワークスペースを作成する](how-to-manage-workspace.md)方法に関するページを参照してください。

## <a name="access-a-terminal"></a>ターミナルにアクセスする

ターミナルにアクセスするには:

1. [Azure Machine Learning Studio](https://ml.azure.com) で、ワークスペースを開きます。
1. 左側にある **[Notebooks]\(ノートブック\)** を選択します。
1. **[Open terminal]\(ターミナルを開く\)** イメージを選択します。

    :::image type="content" source="media/how-to-use-terminal/open-terminal-window.png" alt-text="ターミナル ウィンドウを開く":::

1. コンピューティング インスタンスが実行されている場合、そのコンピューティング インスタンス用のターミナル ウィンドウが表示されます。
1. コンピューティング インスタンスが実行されていない場合は、右側の **[コンピューティング]** セクションを使用して、コンピューティング インスタンスを開始または作成します。
    :::image type="content" source="media/how-to-use-terminal/start-or-create-compute.png" alt-text="コンピューティング インスタンスの開始または作成":::

上記の手順に加えて、次のようにしてターミナルにアクセスすることもできます。

* RStudio: 左上にある **[Terminal]** を選択します。
* Jupyter Lab: [Launcher] タブの見出し **[Other]** の下にある **[Terminal]** タイルを選択します。
* Jupyter: [Files] タブの右上にある **[New] > [Terminal]** を選択します。
* コンピューティング インスタンスの作成時に SSH アクセスを有効にした場合は、マシンに SSH 接続します。

## <a name="copy-and-paste-in-the-terminal"></a>ターミナルでのコピーと貼り付け

> * Windows: コピーの場合は `Ctrl-Insert`、貼り付けの場合は `Ctrl-Shift-v` または `Shift-Insert` を使用します。
> * Mac OS: コピーの場合は `Cmd-c`、貼り付けの場合は `Cmd-v` を使用します。
> * Firefox や IE ではクリップボードのアクセス許可が正しくサポートされない場合があります。

## <a name="use-files-from-git-and-version-files"></a><a name=git></a> Git のファイルを使用してファイルのバージョン管理を行う

ターミナルからすべての Git 操作にアクセスします。 すべての Git ファイルとフォルダーは、ワークスペース ファイル システムに格納されます。 このストレージでは、自分のワークスペース内の任意のコンピューティング インスタンスからこれらのファイルを使用できます。

> [!NOTE]
> すべての Jupyter 環境で表示されるように、 **~/cloudfiles/code/Users** フォルダー以下の任意の場所にファイルとフォルダーを追加します。

詳細については、[Git リポジトリをワークスペース ファイル システムに複製する](concept-train-model-git-integration.md#clone-git-repositories-into-your-workspace-file-system)方法に関する記事を参照してください。

## <a name="install-packages"></a>パッケージをインストールする

 ターミナル ウィンドウからパッケージをインストールします。 Python パッケージを **Python 3.6 - AzureML** 環境にインストールします。  R パッケージを **R** 環境にインストールします。

または、パッケージを Jupyter Notebook または RStudio に直接インストールできます。

* RStudio: 右下にある **[Packages]** タブ、または左上にある **[Console]** タブを使用します。  
* Python: インストール コードを追加して、Jupyter Notebook のセルで実行します。

> [!NOTE]
> Notebook 内のパッケージ管理については、すべてのパッケージ (現在実行されているカーネルの外部のパッケージを含む) を参照する **!pip** または **!conda** ではなく、 **%pip** または **%conda** マジック関数を使用して、**現在実行中のカーネル** にパッケージを自動的にインストールします

## <a name="add-new-kernels"></a>新しいカーネルを追加する

> [!WARNING]
>  コンピューティング インスタンスをカスタマイズするときは、**azureml_py36** conda 環境も **Python 3.6 - AzureML** カーネルも決して削除しないようにしてください。 Jupyter/JupyterLab 機能に必要です

新しい Jupyter カーネルをコンピューティング インスタンスに追加するには、次のようにします。

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

## <a name="manage-terminal-sessions"></a>ターミナル セッションを管理する

 ターミナル ツール バーの **[アクティブなセッションの表示]** を選択して、アクティブなすべてのターミナル セッションの一覧を表示します。 アクティブなセッションがない場合、このタブは無効になります。

未使用のセッションをすべて終了して、コンピューティング インスタンスのリソースを保持します。