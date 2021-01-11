---
title: '対話型デバッグ: VS Code および ML のコンピューティング インスタンス (プレビュー)'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning で自分のコードを対話形式でデバッグできるように VS Code Remote を設定します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: jmartens
author: j-martens
ms.date: 08/06/2020
ms.openlocfilehash: 37d0ec0295d76f740b2e8bf70ae72f0c95e68d14
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87904481"
---
# <a name="debug-interactively-on-an-azure-machine-learning-compute-instance-with-vs-code-remote-preview"></a>VS Code Remote を使用して Azure Machine Learning コンピューティング インスタンス上で対話形式でデバッグする (プレビュー)

この記事では、VS Code から**自分のコードを対話形式でデバッグ**できるように、Azure Machine Learning コンピューティング インスタンスに対して Visual Studio Code Remote 拡張機能を設定する方法について説明します。

* [Azure Machine Learning コンピューティング インスタンス](concept-compute-instance.md)は、データ サイエンティスト向けのクラウドベースのフルマネージド ワークステーションであり、IT 管理者用の管理およびエンタープライズ対応機能を備えています。 

* [Visual Studio Code Remote](https://code.visualstudio.com/docs/remote/remote-overview) Development を使用すると、コンテナー、リモート コンピューター、または Linux 用 Windows サブシステム (WSL) を完全な機能を備えた開発環境として使用できるようになります。 

## <a name="prerequisite"></a>前提条件  

* SSH 対応のコンピューティング インスタンス。 詳細については、[コンピューティング インスタンスの作成ガイドを参照してください](https://docs.microsoft.com/azure/machine-learning/concept-compute-instance#create)。
* Windows プラットフォーム上で、まだ存在していない場合に [OpenSSH と互換性のある SSH クライアントをインストール](https://code.visualstudio.com/docs/remote/troubleshooting#_installing-a-supported-ssh-client)する必要があります。 

> [!Note]
> PuTTY は Windows ではサポートされていません。パスに ssh コマンドを含める必要があるからです。 

## <a name="get-the-ip-and-ssh-port-for-your-compute-instance"></a>コンピューティング インスタンスの IP と SSH のポートを取得する

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

## <a name="add-instance-as-a-host"></a>インスタンスをホストとして追加する

ファイル `~/.ssh/config` (Linux) または `C:\Users<username>.ssh\config` (Windows) をエディターで開き、次のような新しいエントリを追加します。

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

## <a name="connect-vs-code-to-the-instance"></a>インスタンスに VS Code を接続する

1. [Visual Studio Code をインストールする](https://code.visualstudio.com/)。

1. [リモート SSH 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh)をインストールします。

1. 左側の [リモート SSH] アイコンをクリックして、自分の SSH 構成を表示します。

1. 作成したばかりの SSH ホスト構成を右クリックします。

1. **[Connect to Host in Current Window]\(現在のウィンドウのホストに接続\)** を選択します。 

ここからは、完全にコンピューティング インスタンス上での作業となり、ご利用のローカル Visual Studio Code の場合と同様に、git を編集、デバッグ、および使用したり、拡張機能を使用したりできるようになります。

## <a name="next-steps"></a>次のステップ

Visual Studio Code Remote の設定が完了したので、Visual Studio Code からコンピューティング インスタンスをリモート コンピューティングとして使用して、[自分のコードを対話形式でデバッグ](how-to-debug-visual-studio-code.md)することができます。

[チュートリアル:最初の ML モデルをトレーニングする)](tutorial-1st-experiment-sdk-train.md)」では、統合ノートブックでコンピューティング インスタンスを使用する方法を示しています。