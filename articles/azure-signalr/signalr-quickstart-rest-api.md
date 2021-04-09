---
title: クイック スタート - Azure SignalR Service REST API
description: Azure SignalR Service の REST API を使用する方法をサンプルを使って説明します。 REST API の仕様について詳しく取り上げています。
author: sffamily
ms.service: signalr
ms.topic: quickstart
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 2613f91e8c7f1ad3a05792a9a165f4560c09a637
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94874527"
---
# <a name="quickstart-broadcast-real-time-messages-from-console-app"></a>クイック スタート:コンソール アプリからのリアルタイム メッセージのブロードキャスト

Azure SignalR サービスは、ブロードキャストなどのサーバーとクライアントの間の通信シナリオをサポートする [REST API](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md) を提供します。 REST API 呼び出しが可能な任意のプログラミング言語を選択できます。 接続されているすべてのクライアント、名前を指定したクライアント、またはクライアントのグループにメッセージを投稿することができます。

このクイック スタートでは、コマンド ライン アプリから接続されたクライアント アプリに C# でメッセージを送信する方法について学習します。

## <a name="prerequisites"></a>前提条件

このクイック スタートは、macOS、Windows、または Linux で実行できます。

* [.NET Core SDK](https://www.microsoft.com/net/download/core)
* 任意のテキスト エディターまたはコード エディター。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

問題がある場合は、 [トラブルシューティング ガイド](signalr-howto-troubleshoot-guide.md)をお試しになるか、[ご連絡ください](https://aka.ms/asrs/qsapi)。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure アカウントで Azure Portal (<https://portal.azure.com/>) にサインインします。

問題がある場合は、 [トラブルシューティング ガイド](signalr-howto-troubleshoot-guide.md)をお試しになるか、[ご連絡ください](https://aka.ms/asrs/qsapi)。

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

問題がある場合は、 [トラブルシューティング ガイド](signalr-howto-troubleshoot-guide.md)をお試しになるか、[ご連絡ください](https://aka.ms/asrs/qsapi)。

## <a name="clone-the-sample-application"></a>サンプル アプリケーションの複製

サービスのデプロイ中、コードの作成に切り替えましょう。 [GitHub からサンプル アプリ](https://github.com/aspnet/AzureSignalR-samples.git)を複製し、SignalR Service 接続文字列を設定し、アプリケーションをローカルで実行します。

1. git ターミナル ウィンドウを開きます。 サンプル プロジェクトを複製するフォルダーに移動します。

1. 次のコマンドを実行して、サンプル レポジトリを複製します。 このコマンドは、コンピューター上にサンプル アプリのコピーを作成します。

    ```bash
    git clone https://github.com/aspnet/AzureSignalR-samples.git
    ```
問題がある場合は、 [トラブルシューティング ガイド](signalr-howto-troubleshoot-guide.md)をお試しになるか、[ご連絡ください](https://aka.ms/asrs/qsapi)。

## <a name="build-and-run-the-sample"></a>サンプルのビルドと実行

このサンプルは、Azure SignalR サービスの使用方法を示すコンソール アプリです。 これには次の 2 つのモードがあります。

- サーバー モード: 単純なコマンドを使用して、Azure SignalR サービスの REST API を呼び出します。
- クライアント モード: Azure SignalR サービスに接続し、サーバーからメッセージを受信します。

また、Azure SignalR サービスで認証するアクセス トークンを生成する方法も記載されています。

### <a name="build-the-executable-file"></a>実行可能ファイルの作成

例として MacOS の osx.10.13 x64 を使用します。 他のプラットフォームで作成する方法については、[こちらを参照](/dotnet/core/rid-catalog)してください。

```bash
cd AzureSignalR-samples/samples/Serverless/

dotnet publish -c Release -r osx.10.13-x64
```

### <a name="start-a-client"></a>クライアントを起動する

```bash
cd bin/Release/netcoreapp2.1/osx.10.13-x64/

Serverless client <ClientName> -c "<ConnectionString>" -h <HubName>
```

### <a name="start-a-server"></a>サーバーを起動する

```bash
cd bin/Release/netcoreapp2.1/osx.10.13-x64/

Serverless server -c "<ConnectionString>" -h <HubName>
```

問題がある場合は、 [トラブルシューティング ガイド](signalr-howto-troubleshoot-guide.md)をお試しになるか、[ご連絡ください](https://aka.ms/asrs/qsapi)。

## <a name="run-the-sample-without-publishing"></a>公開せずにサンプルを実行する

次のコマンドを実行して、サーバーまたはクライアントを開始することもできます

```bash
# Start a server
dotnet run -- server -c "<ConnectionString>" -h <HubName>

# Start a client
dotnet run -- client <ClientName> -c "<ConnectionString>" -h <HubName>
```

### <a name="use-user-secrets-to-specify-connection-string"></a>user-secrets を使用して接続文字列を指定する

サンプルのルート ディレクトリで`dotnet user-secrets set Azure:SignalR:ConnectionString "<ConnectionString>"`を実行することができます。 その後は、`-c "<ConnectionString>"` オプションが不要になります。

問題がある場合は、 [トラブルシューティング ガイド](signalr-howto-troubleshoot-guide.md)をお試しになるか、[ご連絡ください](https://aka.ms/asrs/qsapi)。

## <a name="usage"></a>使用法

サーバーが起動した後、コマンドを使用してメッセージを送信します。

```
send user <User Id>
send users <User List>
send group <Group Name>
send groups <Group List>
broadcast
```

別のクライアント名を持つ複数のクライアントを開始できます。

問題がある場合は、 [トラブルシューティング ガイド](signalr-howto-troubleshoot-guide.md)をお試しになるか、[ご連絡ください](https://aka.ms/asrs/qsapi)。

## <a name="integration-with-third-party-services"></a><a name="usage"> </a> サード パーティ サービスとの統合

Azure SignalR サービスを使用すると、サードパーティのサービスをシステムに統合することができます。

### <a name="definition-of-technical-specifications"></a>技術仕様の定義

以下の表は、現段階でサポートされている全バージョンの REST API を示したものです。 特定のバージョンごとの定義ファイルも記載しています。

Version | API 状態 | Door | 固有
--- | --- | --- | ---
`1.0-preview` | 利用可能 | 5002 | [Swagger](https://github.com/Azure/azure-signalr/tree/dev/docs/swagger/v1-preview.json)
`1.0` | 利用可能 | Standard | [Swagger](https://github.com/Azure/azure-signalr/tree/dev/docs/swagger/v1.json)

以下に示したのは、特定のバージョンごとに利用可能な API の一覧です。

API | 1.0-preview | 1.0
--- | --- | ---
[全員にブロードキャストする](#broadcast) | **&#x2713;** | **&#x2713;**
[グループにブロードキャストする](#broadcast-group) | **&#x2713;** | **&#x2713;**
一部のグループにブロードキャストする | **&#x2713;** (非推奨) | `N / A`
[ユーザーに送信する](#send-user) | **&#x2713;** | **&#x2713;**
一部のユーザーに送信する | **&#x2713;** (非推奨) | `N / A`
[ユーザーをグループに追加する](#add-user-to-group) | `N / A` | **&#x2713;**
[ユーザーをグループから削除する](#remove-user-from-group) | `N / A` | **&#x2713;**
[ユーザーの存在を確認する](#check-user-existence) | `N / A` | **&#x2713;**
[ユーザーをすべてのグループから削除する](#remove-user-from-all-groups) | `N / A` | **&#x2713;**
[接続に送信する](#send-connection) | `N / A` | **&#x2713;**
[グループに接続を追加する](#add-connection-to-group) | `N / A` | **&#x2713;**
[グループから接続を削除する](#remove-connection-from-group) | `N / A` | **&#x2713;**
[クライアント接続を閉じる](#close-connection) | `N / A` | **&#x2713;**
[サービス正常性](#service-health) | `N / A` | **&#x2713;**

<a name="broadcast"> </a>
### <a name="broadcast-to-everyone"></a>全員にブロードキャストする

Version | API HTTP メソッド | 要求 URL | 要求本文
--- | --- | --- | ---
`1.0-preview` | `POST` | `https://<instance-name>.service.signalr.net:5002/api/v1-preview/hub/<hub-name>` | `{"target": "<method-name>", "arguments": [...]}`
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>` | 同上

<a name="broadcast-group"> </a>
### <a name="broadcast-to-a-group"></a>グループにブロードキャストする

Version | API HTTP メソッド | 要求 URL | 要求本文
--- | --- | --- | ---
`1.0-preview` | `POST` | `https://<instance-name>.service.signalr.net:5002/api/v1-preview/hub/<hub-name>/group/<group-name>` | `{"target": "<method-name>", "arguments": [...]}`
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>` | 同上

<a name="send-user"> </a>
### <a name="sending-to-a-user"></a>ユーザーに送信する

Version | API HTTP メソッド | 要求 URL | 要求本文
--- | --- | --- | ---
`1.0-preview` | `POST` | `https://<instance-name>.service.signalr.net:5002/api/v1-preview/hub/<hub-name>/user/<user-id>` | `{"target": "<method-name>", "arguments": [...]}`
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/users/<user-id>` | 同上

<a name="add-user-to-group"> </a>
### <a name="adding-a-user-to-a-group"></a>ユーザーをグループに追加する

Version | API HTTP メソッド | 要求 URL
--- | --- | ---
`1.0` | `PUT` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/users/<user-id>`

<a name="remove-user-from-group"> </a>
### <a name="removing-a-user-from-a-group"></a>ユーザーをグループから削除する

Version | API HTTP メソッド | 要求 URL
--- | --- | ---
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/users/<user-id>`

<a name="check-user-existence"> </a>
### <a name="check-user-existence-in-a-group"></a>グループ内のユーザーの存在を確認する

API Version | API HTTP メソッド | 要求 URL
---|---|---
`1.0` | `GET` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/users/<user-id>/groups/<group-name>`
`1.0` | `GET` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/users/<user-id>` 

応答の状態コード | 説明
---|---
`200` | ユーザーが存在します
`404` | ユーザーが存在しません

<a name="remove-user-from-all-groups"> </a>
### <a name="remove-a-user-from-all-groups"></a>ユーザーをすべてのグループから削除する

API Version | API HTTP メソッド | 要求 URL
---|---|---
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/users/<user-id>/groups`

<a name="send-connection"> </a>
### <a name="send-message-to-a-connection"></a>接続にメッセージを送信する

API Version | API HTTP メソッド | 要求 URL | 要求本文
---|---|---|---
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/connections/<connection-id>` | `{ "target":"<method-name>", "arguments":[ ... ] }`

<a name="add-connection-to-group"> </a>
### <a name="add-a-connection-to-a-group"></a>グループに接続を追加する

API Version | API HTTP メソッド | 要求 URL
---|---|---
`1.0` | `PUT` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/connections/<connection-id>`
`1.0` | `PUT` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/connections/<connection-id>/groups/<group-name>`

<a name="remove-connection-from-group"> </a>
### <a name="remove-a-connection-from-a-group"></a>グループから接続を削除する

API Version | API HTTP メソッド | 要求 URL
---|---|---
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/connections/<connection-id>`
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/connections/<connection-id>/groups/<group-name>`

<a name="close-connection"> </a>
### <a name="close-a-client-connection"></a>クライアント接続を閉じる

API Version | API HTTP メソッド | 要求 URL
---|---|---
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/connections/<connection-id>`
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/connections/<connection-id>?reason=<close-reason>`

<a name="service-health"> </a>
### <a name="service-health"></a>サービス正常性

API Version | API HTTP メソッド | 要求 URL
---|---|---                             
`1.0` | `GET` | `https://<instance-name>.service.signalr.net/api/v1/health`

応答の状態コード | 説明
---|---
`200` | サービスは良好
`5xx` | サービス エラー

問題がある場合は、 [トラブルシューティング ガイド](signalr-howto-troubleshoot-guide.md)をお試しになるか、[ご連絡ください](https://aka.ms/asrs/qsapi)。

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

問題がある場合は、 [トラブルシューティング ガイド](signalr-howto-troubleshoot-guide.md)をお試しになるか、[ご連絡ください](https://aka.ms/asrs/qsapi)。

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、REST API を使用して SignalR Service からクライアントにリアルタイム メッセージをブロードキャストする方法を学習しました。 次は、REST API を基盤に構築されている SignalR Service バインディングを使用して Functions を開発およびデプロイする方法の詳細を学習してください。

> [!div class="nextstepaction"]
> [Azure SignalR Service バインディングを使用した Azure Functions の開発](signalr-quickstart-azure-functions-csharp.md)
