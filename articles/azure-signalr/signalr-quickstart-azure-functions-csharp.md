---
title: Azure SignalR Service サーバーレス クイック スタート - C#
description: C# から Azure SignalR Service と Azure Functions を使ってチャット ルームを作成する方法について説明します。
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.date: 09/25/2020
ms.author: zhshang
ms.openlocfilehash: 1f28058a766144ada3326b3ee4ce09ca503d1896
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94873881"
---
# <a name="quickstart-create-a-chat-room-with-azure-functions-and-signalr-service-using-c"></a>クイック スタート:C\# を使用した Azure Functions と SignalR Service によるチャット ルームの作成

Azure SignalR Service を使用すると、アプリケーションにリアルタイム機能を簡単に追加できます。 Azure Functions は、インフラストラクチャを管理することなくコードを実行できるサーバーレス プラットフォームです。 このクイック スタートでは、SignalR Service および Functions を使用して、サーバーレスかつリアルタイムのチャット アプリケーションを構築する方法を説明します。

## <a name="prerequisites"></a>前提条件

まだ Visual Studio 2019 をインストールしていない場合は、**無料** の [Visual Studio 2019 Community エディション](https://www.visualstudio.com/downloads)をダウンロードして使用できます。 Visual Studio のセットアップ中に、必ず **[Azure の開発]** を有効にしてください。

[Azure Functions Core Tools (v2)](https://github.com/Azure/azure-functions-core-tools#installing)、[.NET Core SDK](https://dotnet.microsoft.com/download)、お好みのコード エディターを使用して、このチュートリアルをコマンド ライン (macOS、Windows、または Linux) で実行することもできます。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料でアカウントを 1 つ](https://azure.microsoft.com/free/dotnet)作成してください。

問題がある場合は、 [トラブルシューティング ガイド](signalr-howto-troubleshoot-guide.md)をお試しになるか、[ご連絡ください](https://aka.ms/asrs/qscsharp)。

## <a name="log-in-to-azure"></a>Azure にログインする

Azure アカウントで Azure Portal (<https://portal.azure.com/>) にサインインします。

問題がある場合は、 [トラブルシューティング ガイド](signalr-howto-troubleshoot-guide.md)をお試しになるか、[ご連絡ください](https://aka.ms/asrs/qscsharp)。

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

問題がある場合は、 [トラブルシューティング ガイド](signalr-howto-troubleshoot-guide.md)をお試しになるか、[ご連絡ください](https://aka.ms/asrs/qscsharp)。

[!INCLUDE [Clone application](includes/signalr-quickstart-clone-application.md)]

問題がある場合は、 [トラブルシューティング ガイド](signalr-howto-troubleshoot-guide.md)をお試しになるか、[ご連絡ください](https://aka.ms/asrs/qscsharp)。

## <a name="configure-and-run-the-azure-function-app"></a>Azure Function アプリを構成して実行する

1. Visual Studio (または他のコード エディター) を起動し、複製したリポジトリの *src/chat/csharp* フォルダーにあるソリューションを開きます。

1. Azure portal を開いているブラウザーのポータルの上部にある検索ボックスから、以前にデプロイした SignalR Service インスタンスの名前で検索し、インスタンスが正常に作成されたことを確認します。 インスタンスを選択して開きます。

    ![SignalR Service インスタンスの検索](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

1. **[Key]\(キー\)** を選択し、SignalR Service インスタンスの接続文字列を表示します。

1. プライマリ接続文字列を選択してコピーします。

1. Visual Studio の **ソリューション エクスプローラー** に戻り、*local.settings.sample.json* の名前を *local.settings.json* に変更します。

1. *local.settings.json* で、**AzureSignalRConnectionString** 設定に接続文字列の値を貼り付けます。 ファイルを保存します。

1. *Functions.cs* を開きます。 この関数アプリには、HTTP によってトリガーされる関数が 2 つあります。

    - **GetSignalRInfo** - `SignalRConnectionInfo` 入力バインディングを使用し、有効な接続情報を生成して返します。
    - **SendMessage** - 要求本文でチャット メッセージを受信し、*SignalR* 出力バインディングを使用して接続されているすべてのクライアント アプリケーションにメッセージをブロードキャストします。

1. Azure 関数アプリをローカルで起動するには、次のオプションのいずれかを使用します。

    - **Visual Studio**: *[デバッグ]* メニューの *[デバッグの開始]* を選択してアプリケーションを実行します。

        ![アプリケーションのデバッグ](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-debug-vs.png)

    - **コマンド ライン**:次のコマンドを実行して、関数ホストを開始します。

        ```bash
        func start
        ```
問題がある場合は、 [トラブルシューティング ガイド](signalr-howto-troubleshoot-guide.md)をお試しになるか、[ご連絡ください](https://aka.ms/asrs/qscsharp)。

[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]

問題がある場合は、 [トラブルシューティング ガイド](signalr-howto-troubleshoot-guide.md)をお試しになるか、[ご連絡ください](https://aka.ms/asrs/qscsharp)。

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

問題がある場合は、 [トラブルシューティング ガイド](signalr-howto-troubleshoot-guide.md)をお試しになるか、[ご連絡ください](https://aka.ms/asrs/qscsharp)

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、Visual Studio でリアルタイムのサーバーレス アプリケーションを構築して実行しました。 次に、Visual Studio で Azure Functions を開発およびデプロイする方法の詳細を学習してください。

> [!div class="nextstepaction"]
> [Visual Studio を使用する Azure Functions の開発](../azure-functions/functions-develop-vs.md)

