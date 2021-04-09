---
title: Azure Relay ハイブリッド接続 - Node での HTTP 要求
description: Node での Azure Relay ハイブリッド接続 HTTP 要求用 Node.js コンソール アプリケーションを作成します。
ms.topic: conceptual
ms.date: 06/23/2020
ms.custom: devx-track-js
ms.openlocfilehash: 249b4fa231cd54a1a8054b32985ed0e48fcc16f1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91263761"
---
# <a name="get-started-with-relay-hybrid-connections-http-requests-in-node"></a>Node での Relay ハイブリッド接続 HTTP 要求の概要

[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

このクイック スタートでは、HTTP プロトコルを使用してメッセージを送受信する Node.js のセンダー アプリケーションとレシーバー アプリケーションを作成します。 これらのアプリケーションには、Azure Relay のハイブリッド接続機能が使用されます。 Azure Relay 全般については、[Azure Relay](relay-what-is-it.md) に関するページを参照してください。 

このクイック スタートでは、以下の手順を実行します。

1. Azure Portal を使用した Relay 名前空間の作成
2. Azure Portal を使用した、その名前空間内のハイブリッド接続の作成
3. メッセージを受信するサーバー (リスナー) コンソール アプリケーションの作成
4. メッセージを送信するクライアント (送信側) コンソール アプリケーションの作成
5. アプリケーションの実行

## <a name="prerequisites"></a>前提条件
- [Node.js](https://nodejs.org/en/)。
- Azure サブスクリプション。 お持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="create-a-namespace-using-the-azure-portal"></a>Azure Portal を使用した名前空間の作成
[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="create-a-hybrid-connection-using-the-azure-portal"></a>Azure Portal を使用したハイブリッド接続の作成
[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="create-a-server-application-listener"></a>サーバー アプリケーション (リスナー) の作成
Relay からのメッセージをリッスンおよび受信するために、Node.js コンソール アプリケーションを作成します。

[!INCLUDE [relay-hybrid-connections-node-get-started-server](../../includes/relay-hybrid-connections-http-requests-node-get-started-server.md)]

## <a name="create-a-client-application-sender"></a>クライアント アプリケーション (センダー) の作成

Relay にメッセージを送信するために、任意の HTTP クライアントを使用するか、Node.js コンソール アプリケーションを作成します。

[!INCLUDE [relay-hybrid-connections-node-get-started-client](../../includes/relay-hybrid-connections-http-requests-node-get-started-client.md)]

## <a name="run-the-applications"></a>アプリケーションの実行

1. Node.js コマンド プロンプトで「`node listener.js`」と入力して、サーバー アプリケーションを実行します。
2. Node.js コマンド プロンプトで「`node sender.js`」と入力してサーバー アプリケーションを実行し、何かテキストを入力します。
3. サーバー アプリケーション コンソールに、クライアント アプリケーションで入力したテキストが出力されることを確認します。

これで、Node.js を使用してエンドツーエンドのハイブリッド接続アプリケーションを作成できました。

## <a name="next-steps"></a>次のステップ
このクイック スタートでは、HTTP を使用してメッセージを送受信する Node.js のクライアント アプリケーションとサーバー アプリケーションを作成しました。 Azure Relay のハイブリッド接続機能は、WebSocket を使用したメッセージの送受信もサポートしています。 Azure Relay のハイブリッド接続で WebSocket を使用する方法については、[WebSocket のクイック スタート](relay-hybrid-connections-node-get-started.md)を参照してください。

このクイック スタートでは、Node.js を使用してクライアント アプリケーションとサーバー アプリケーションを作成しました。 .NET Framework を使用してクライアント アプリケーションとサーバー アプリケーションを作成する方法については、[.NET WebSocket のクイック スタート](relay-hybrid-connections-dotnet-get-started.md)または [.NET HTTP のクイック スタート](relay-hybrid-connections-http-requests-dotnet-get-started.md)を参照してください。
