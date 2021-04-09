---
title: Visual Studio Code でコンピューティング インスタンスに接続する (プレビュー)
titleSuffix: Azure Machine Learning
description: Visual Studio Code で Azure Machine Learning コンピューティング インスタンスに接続して、対話型 Jupyter Notebook とリモート開発のワークロードを実行する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: luquinta
author: luisquintanilla
ms.date: 11/16/2020
ms.openlocfilehash: 3f3794ed4748eda44e863ff4627dedc0a34691af
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98070101"
---
# <a name="connect-to-an-azure-machine-learning-compute-instance-in-visual-studio-code-preview"></a>Visual Studio Code で Azure Machine Learning コンピューティング インスタンスに接続する (プレビュー)

この記事では、Visual Studio Code を使用して Azure Machine Learning コンピューティング インスタンスに接続する方法について説明します。

[Azure Machine Learning コンピューティング インスタンス](concept-compute-instance.md)は、データ サイエンティスト向けのクラウドベースのフルマネージド ワークステーションであり、IT 管理者用の管理およびエンタープライズ対応機能を備えています。

Visual Studio Code からコンピューティング インスタンスに接続するには、次の 2 つの方法があります。

* リモート Jupyter Notebook サーバー。 このオプションを使用すると、コンピューティング インスタンスをリモート Jupyter Notebook サーバーとして設定できます。
* [Visual Studio Code リモート開発](https://code.visualstudio.com/docs/remote/remote-overview) Visual Studio Code リモート開発を使用すると、コンテナー、リモート コンピューター、または Linux 用 Windows サブシステム (WSL) を完全な機能を備えた開発環境として使用できるようになります。

## <a name="configure-compute-instance-as-remote-notebook-server"></a>コンピューティング インスタンスをリモート ノートブック サーバーとして構成する

コンピューティング インスタンスをリモート Jupyter Notebook サーバーとして構成するには、いくつかの前提条件が必要です。

* Visual Studio Code の Azure Machine Learning 拡張機能 詳細については、[Visual Studio Code の Azure Machine Learning 拡張機能セットアップ ガイド](tutorial-setup-vscode-extension.md)を参照してください。
* Azure Machine Learning ワークスペース。 まだ用意していない場合は、[Visual Studio Code の Azure Machine Learning 拡張機能を使用して新しいワークスペースを作成](how-to-manage-resources-vscode.md#create-a-workspace)します。

コンピューティング インスタンスに接続するには、次のようにします。

1. Visual Studio Code で Jupyter Notebook を開きます。
1. 統合ノートブックのエクスペリエンスが読み込まれたら、**Jupyter Server** を選択します。

    > [!div class="mx-imgBorder"]
    > ![Azure Machine Learning のリモート Jupyter Notebook サーバー ドロップダウンの表示](media/how-to-set-up-vs-code-remote/launch-server-selection-dropdown.png)

    または、次のようにコマンド パレットを使用することもできます。

    1. メニュー バーから **[表示]、[コマンド パレット]** の順に選択して、コマンド パレットを開きます。
    1. テキストボックスに「`Azure ML: Connect to Compute instance Jupyter server`」と入力します。

1. Jupyter サーバー オプションの一覧から [`Azure ML Compute Instances`] を選択します。
1. サブスクリプションの一覧からご利用のサブスクリプションを選択します。 既定の Azure Machine Learning ワークスペースをあらかじめ構成済みである場合、この手順はスキップされます。
1. ワークスペースを選択します。
1. 一覧から自分のコンピューティング インスタンスを選択します。 それがない場合は、 **[Create new Azure ML Compute Instance]\(新しい Azure ML コンピューティング インスタンスを作成する\)** を選択し、プロンプトに従って作成します。
1. 変更を有効にするには、Visual Studio Code を再度読み込む必要があります。
1. Jupyter Notebook を開き、セルを実行します。

> [!IMPORTANT]
> 接続を確立するために、セルを実行する **必要があります**。

この時点では、ご利用の Jupyter Notebook でセルを実行し続けることができます。

> [!TIP]
> Jupyter のようなコード セルを含む Python スクリプト ファイル (.py) を操作することもできます。 詳細については、[Visual Studio Code Python Interactive のドキュメント](https://code.visualstudio.com/docs/python/jupyter-support-py)を参照してください。

## <a name="configure-compute-instance-remote-development"></a>コンピューティング インスタンス リモート開発を構成する

完全な機能を備えたリモート開発エクスペリエンスを実現するには、いくつかの前提条件が必要です。

* [Visual Studio Code リモート SSH 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh)。
* SSH 対応のコンピューティング インスタンス。 詳細については、[コンピューティング インスタンスの作成に関するガイド](how-to-create-manage-compute-instance.md)を参照してください。

> [!NOTE]
> Windows プラットフォーム上で、まだ存在していない場合に [OpenSSH と互換性のある SSH クライアントをインストール](https://code.visualstudio.com/docs/remote/troubleshooting#_installing-a-supported-ssh-client)する必要があります。 PuTTY は Windows ではサポートされていません。パスに ssh コマンドを含める必要があるからです。

### <a name="get-the-ip-and-ssh-port-for-your-compute-instance"></a>コンピューティング インスタンスの IP と SSH のポートを取得する

1. https://ml.azure.com/ で Azure Machine Learning Studio に移動します。
2. ご利用の[ワークスペース](concept-workspace.md)を選択します。
1. **[コンピューティング インスタンス]** タブをクリックします。
1. **[アプリケーション URI]** 列で、リモート コンピューティングとして使用するコンピューティング インスタンスの **SSH** リンクをクリックします。 
1. ダイアログで、IP アドレスと SSH ポートをメモします。 
1. ご利用の秘密キーをご自分のローカル コンピューター上の ~/.ssh/ ディレクトリに保存します。たとえば、新しいファイルのエディターを開き、次のようにキーを貼り付けます。 

   **Linux**:

   ```sh
   vi ~/.ssh/id_azmlcitest_rsa  
   ```

   **Windows**:

   ```cmd
   notepad C:\Users\<username>\.ssh\id_azmlcitest_rsa
   ```

   秘密キーは次のようになります。

   ```text
   -----BEGIN RSA PRIVATE KEY-----

   MIIEpAIBAAKCAQEAr99EPm0P4CaTPT2KtBt+kpN3rmsNNE5dS0vmGWxIXq4vAWXD
   ..... 
   ewMtLnDgXWYJo0IyQ91ynOdxbFoVOuuGNdDoBykUZPQfeHDONy2Raw==

   -----END RSA PRIVATE KEY-----
   ```

1. 自分だけがファイルを読み取ることができるように、そのファイルに対するアクセス許可を変更します。  

   ```sh
   chmod 600 ~/.ssh/id_azmlcitest_rsa
   ```

### <a name="add-instance-as-a-host"></a>インスタンスをホストとして追加する

ファイル `~/.ssh/config` (Linux) または `C:\Users<username>.ssh\config` (Windows) をエディターで開き、次のような内容の新しいエントリを追加します。

```
Host azmlci1 

    HostName 13.69.56.51 

    Port 50000 

    User azureuser 

    IdentityFile ~/.ssh/id_azmlcitest_rsa
```

ここでは、フィールドに関する詳細の一部を説明します。

|フィールド|説明|
|----|---------|
|Host|コンピューティング インスタンスの短縮形として何でも好きなものを使用できます |
|HostName|これはコンピューティング インスタンスの IP アドレスです。 |
|Port|これは、上の SSH ダイアログに表示されるポートです。 |
|User|これは、 `azureuser` とする必要があります。 |
|IdentityFile|秘密キーを保存したファイルを指す必要があります。 |

これで、前に使用した短縮形 `ssh azmlci1` を使用して、ご利用のコンピューティング インスタンスに ssh 接続できるようになったはずです。

### <a name="connect-vs-code-to-the-instance"></a>インスタンスに VS Code を接続する

1. Visual Studio Code アクティビティ バーの [リモート SSH] アイコンをクリックして、ご利用の SSH 構成を表示します。

1. 作成したばかりの SSH ホスト構成を右クリックします。

1. **[Connect to Host in Current Window]\(現在のウィンドウのホストに接続\)** を選択します。 

ここからは、完全にコンピューティング インスタンス上での作業となり、ご利用のローカル Visual Studio Code の場合と同様に、git を編集、デバッグ、および使用したり、拡張機能を使用したりできるようになります。

## <a name="next-steps"></a>次のステップ

Visual Studio Code Remote の設定が完了したので、Visual Studio Code からコンピューティング インスタンスをリモート コンピューティングとして使用して、[自分のコードを対話形式でデバッグ](how-to-debug-visual-studio-code.md)することができます。

[チュートリアル:最初の ML モデルをトレーニングする)](tutorial-1st-experiment-sdk-train.md)」では、統合ノートブックでコンピューティング インスタンスを使用する方法を示しています。
