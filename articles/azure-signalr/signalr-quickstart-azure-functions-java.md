---
title: Java を使用した Azure Functions と SignalR Service によるチャット ルームの作成
description: Java で Azure SignalR Service と Azure Functions を使ってチャット ルームを作成する方法について説明します。
author: sffamily
ms.service: signalr
ms.devlang: java
ms.topic: quickstart
ms.date: 03/04/2019
ms.author: zhshang
ms.custom: devx-track-java
ms.openlocfilehash: 62f31d8426e86cc20e957f3a47b30109dee0a634
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96185676"
---
# <a name="quickstart-use-java-to-create-a-chat-room-with-azure-functions-and-signalr-service"></a>クイック スタート:Java を使用した Azure Functions と SignalR Service によるチャット ルームの作成

Azure SignalR Service を使用すると、アプリケーションに対してリアルタイム機能を簡単に追加できます。Azure Functions は、インフラストラクチャを管理することなくコードを実行できるサーバーレス プラットフォームです。 このクイックスタートでは、Java で SignalR Service および Functions を使用して、サーバーレスかつリアルタイムのチャット アプリケーションを作成します。

## <a name="prerequisites"></a>前提条件

- コード エディター ([Visual Studio Code](https://code.visualstudio.com/) など)。
- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing)。 Azure 関数アプリをローカルに実行する際に使用します。

   > [!NOTE]
   > Java では、必須の SignalR Service バインディングが Azure Function Core Tools バージョン 2.4.419 (ホスト バージョン 2.0.12332) 以降でのみサポートされます。

   > [!NOTE]
   > Azure Functions Core Tools では、拡張機能をインストールするために、[.NET Core SDK](https://www.microsoft.com/net/download) がインストールされている必要があります。 ただし、JavaScript Azure Functions アプリの構築には、.NET の知識は必要ありません。

- [Java Developer Kit](https://www.azul.com/downloads/zulu/)、バージョン 8
- [Apache Maven](https://maven.apache.org)、バージョン 3.0 以降

> [!NOTE]
> このクイック スタートは、macOS、Windows、または Linux で実行できます。

問題がある場合は、 [トラブルシューティング ガイド](signalr-howto-troubleshoot-guide.md)をお試しになるか、[ご連絡ください](https://aka.ms/asrs/qsjava)。

## <a name="log-in-to-azure"></a>Azure にログインする

Azure アカウントで Azure Portal (<https://portal.azure.com/>) にサインインします。

問題がある場合は、 [トラブルシューティング ガイド](signalr-howto-troubleshoot-guide.md)をお試しになるか、[ご連絡ください](https://aka.ms/asrs/qsjava)。

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

問題がある場合は、 [トラブルシューティング ガイド](signalr-howto-troubleshoot-guide.md)をお試しになるか、[ご連絡ください](https://aka.ms/asrs/qsjava)。

[!INCLUDE [Clone application](includes/signalr-quickstart-clone-application.md)]

問題がある場合は、 [トラブルシューティング ガイド](signalr-howto-troubleshoot-guide.md)をお試しになるか、[ご連絡ください](https://aka.ms/asrs/qsjava)。

## <a name="configure-and-run-the-azure-function-app"></a>Azure Function アプリを構成して実行する

1. Azure portal を開いているブラウザーのポータルの上部にある検索ボックスから、以前にデプロイした SignalR Service インスタンスの名前で検索し、インスタンスが正常に作成されたことを確認します。 インスタンスを選択して開きます。

    ![SignalR Service インスタンスの検索](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

1. **[Key]\(キー\)** を選択し、SignalR Service インスタンスの接続文字列を表示します。

1. プライマリ接続文字列を選択してコピーします。

    ![SignalR Service の作成](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-keys.png)

1. コード エディターで、複製したリポジトリの *src/chat/java* フォルダーを開きます。

1. *local.settings.sample.json* を *local.settings.json* に変更します。

1. **local.settings.json** で、**AzureSignalRConnectionString** 設定に接続文字列の値を貼り付けます。 ファイルを保存します。

1. 関数を含むメイン ファイルは *src/chat/java/src/main/java/com/function/Functions.java* にあります。

    - **negotiate** - *SignalRConnectionInfo* 入力バインディングを使用し、有効な接続情報を生成して返却します。
    - **SendMessage** - 要求本文でチャット メッセージを受信し、*SignalR* 出力バインディングを使用して接続されているすべてのクライアント アプリケーションにメッセージをブロードキャストします。

1. ターミナルで、*src/chat/java* フォルダーにいることを確認します。 関数アプリを構築します。

    ```bash
    mvn clean package
    ```

1. 関数アプリをローカルで実行します。

    ```bash
    mvn azure-functions:run
    ```
    
問題がある場合は、 [トラブルシューティング ガイド](signalr-howto-troubleshoot-guide.md)をお試しになるか、[ご連絡ください](https://aka.ms/asrs/qsjava)。

[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]

問題がある場合は、 [トラブルシューティング ガイド](signalr-howto-troubleshoot-guide.md)をお試しになるか、[ご連絡ください](https://aka.ms/asrs/qsjava)。

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

問題がある場合は、 [トラブルシューティング ガイド](signalr-howto-troubleshoot-guide.md)をお試しになるか、[ご連絡ください](https://aka.ms/asrs/qsjava)。

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、Maven を使ってリアルタイムのサーバーレス アプリケーションを構築して実行しました。 次に、Java Azure Functions を一から作成する方法について説明します。

> [!div class="nextstepaction"]
> [Java と Maven を使用して初めての関数を作成する](../azure-functions/create-first-function-cli-csharp.md?pivots=programming-language-java%2cprogramming-language-java)