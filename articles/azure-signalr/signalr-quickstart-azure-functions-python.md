---
title: Azure SignalR Service サーバーレス クイックスタート - Python
description: Azure SignalR Service と Azure Functions を使ってチャット ルームを作成する方法について説明します。
author: anthonychu
ms.service: signalr
ms.devlang: python
ms.topic: quickstart
ms.date: 08/08/2019
ms.author: antchu
ms.openlocfilehash: fbae9d15ba8572a8ae2c493ec85eb0010c2664eb
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2019
ms.locfileid: "68886424"
---
# <a name="quickstart-create-a-chat-room-with-azure-functions-and-signalr-service-using-python"></a>クイック スタート:Python を使用した Azure Functions と SignalR Service によるチャット ルームの作成

Azure SignalR Service を使用すると、アプリケーションにリアルタイム機能を簡単に追加できます。 Azure Functions は、インフラストラクチャを管理することなくコードを実行できるサーバーレス プラットフォームです。 このクイック スタートでは、SignalR Service および Functions を使用して、サーバーレスかつリアルタイムのチャット アプリケーションを構築する方法を説明します。

## <a name="prerequisites"></a>前提条件

このクイック スタートは、macOS、Windows、または Linux で実行できます。

[Visual Studio Code](https://code.visualstudio.com/) などのコード エディターがインストールされていることを確認してください。

Python Azure Function アプリをローカルで実行するために、[Azure Functions Core Tools (v2)](https://github.com/Azure/azure-functions-core-tools#installing) (バージョン 2.7.1505 以降) をインストールします。

Azure Functions には [Python 3.6](https://www.python.org/downloads/) が必要です。

現在の Azure Functions Core Tools では、拡張機能をインストールするために [.NET Core SDK](https://www.microsoft.com/net/download) をインストールする必要があります。 ただし、Python Azure Functions アプリの構築には、.NET の知識は必要ありません。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-azure"></a>Azure にログインする

Azure アカウントで Azure Portal (<https://portal.azure.com/>) にサインインします。

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

[!INCLUDE [Clone application](includes/signalr-quickstart-clone-application.md)]

## <a name="configure-and-run-the-azure-function-app"></a>Azure Function アプリを構成して実行する

1. Azure portal を開いているブラウザーのポータルの上部にある検索ボックスから、以前にデプロイした SignalR Service インスタンスの名前で検索し、インスタンスが正常に作成されたことを確認します。 インスタンスを選択して開きます。

    ![SignalR Service インスタンスの検索](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

1. **[Key]\(キー\)** を選択し、SignalR Service インスタンスの接続文字列を表示します。

1. プライマリ接続文字列を選択してコピーします。

    ![SignalR Service の作成](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-keys.png)

1. コード エディターで、複製したリポジトリの *src/chat/python* フォルダーを開きます。

1. Python 関数をローカルで開発し、テストするには、Python 3.6 環境で作業する必要があります。 次のコマンドを実行して、`.venv` という名前の仮想環境を作成してアクティブにします。

    **Linux または macOS:**

    ```bash
    python3.6 -m venv .venv
    source .venv/bin/activate
    ```

    **Windows:**

    ```powershell
    py -3.6 -m venv .venv
    .venv\scripts\activate
    ```

1. *local.settings.sample.json* を *local.settings.json* に変更します。

1. **local.settings.json** で、**AzureSignalRConnectionString** 設定に接続文字列の値を貼り付けます。 ファイルを保存します。

1. フォルダーに Python 関数が編成されます。 各フォルダーには、関数で使用されるバインディングを定義する *function.json* と、関数の本体である *\_\_init\_\_.py* という 2 つのファイルがあります。 この関数アプリには、HTTP によってトリガーされる関数が 2 つあります。

    - **negotiate** - *SignalRConnectionInfo* 入力バインディングを使用し、有効な接続情報を生成して返却します。
    - **messages** - 要求本文でチャット メッセージを受信し、*SignalR* 出力バインディングを使用して接続されているすべてのクライアント アプリケーションにメッセージをブロードキャストします。

1. ターミナルで、*src/chat/python* フォルダーにいることを確認します。 Azure Functions Core Tools を使用して、アプリを実行するために必要な拡張機能をインストールします。

    ```bash
    func extensions install
    ```

1. 関数アプリを実行します。

    ```bash
    func start
    ```

    ![関数アプリを実行する](media/signalr-quickstart-azure-functions-python/signalr-quickstart-run-application.png)

[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

## <a name="next-steps"></a>次の手順

このクイック スタートでは、VS Code でリアルタイムのサーバーレス アプリケーションを構築して実行しました。 次は、VS Code から Azure Functions をデプロイする方法を詳しく学びます。

> [!div class="nextstepaction"]
> [VS Code で Azure Functions をデプロイする](https://code.visualstudio.com/tutorials/functions-extension/getting-started)