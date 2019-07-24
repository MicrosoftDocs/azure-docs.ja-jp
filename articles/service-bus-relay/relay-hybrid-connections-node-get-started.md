---
title: Node での Azure Relay ハイブリッド接続 Websocket の概要 | Microsoft Docs
description: Azure Relay ハイブリッド接続 Websocket 用の Node.js コンソール アプリケーションを作成します
services: service-bus-relay
documentationcenter: node
author: spelluru
manager: timlt
editor: ''
ms.assetid: e44e4867-3cf3-46be-8f8a-7671e2013bc4
ms.service: service-bus-relay
ms.devlang: tbd
ms.topic: conceptual
ms.tgt_pltfrm: node
ms.workload: na
ms.date: 11/01/2018
ms.author: spelluru
ms.openlocfilehash: b4864673e25ba4f5a1f2e8629e0889863051bc07
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2019
ms.locfileid: "57764865"
---
# <a name="get-started-with-relay-hybrid-connections-websockets-in-nodejs"></a>Node.js での Relay ハイブリッド接続 WebSocket の概要

[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

このクイック スタートでは、Azure Relay のハイブリッド接続 WebSocket を使用してメッセージを送受信する Node.js のセンダー アプリケーションとレシーバー アプリケーションを作成します。 Azure Relay 全般については、[Azure Relay](relay-what-is-it.md) に関するページを参照してください。 

このクイック スタートでは、以下の手順を実行します。 

1. Azure Portal を使用した Relay 名前空間の作成
2. Azure Portal を使用した、その名前空間内のハイブリッド接続の作成
3. メッセージを受信するサーバー (リスナー) コンソール アプリケーションの作成
4. メッセージを送信するクライアント (送信側) コンソール アプリケーションの作成
5. アプリケーションの実行 

## <a name="prerequisites"></a>前提条件

- [Node.js](https://nodejs.org/en/)。
- Azure サブスクリプション。 お持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="create-a-namespace"></a>名前空間の作成
[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="create-a-hybrid-connection"></a>ハイブリッド接続の追加
[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="create-a-server-application-listener"></a>サーバー アプリケーション (リスナー) の作成
Relay からのメッセージをリッスンおよび受信するために、Node.js コンソール アプリケーションを作成します。

[!INCLUDE [relay-hybrid-connections-node-get-started-server](../../includes/relay-hybrid-connections-node-get-started-server.md)]

## <a name="create-a-client-application-sender"></a>クライアント アプリケーション (センダー) の作成
Relay にメッセージを送信するために、Node.js コンソール アプリケーションを作成します。

[!INCLUDE [relay-hybrid-connections-node-get-started-client](../../includes/relay-hybrid-connections-node-get-started-client.md)]

## <a name="run-the-applications"></a>アプリケーションの実行

1. Node.js コマンド プロンプトで「`node listener.js`」と入力して、サーバー アプリケーションを実行します。
2. Node.js コマンド プロンプトで「`node sender.js`」と入力してサーバー アプリケーションを実行し、何かテキストを入力します。
3. サーバー アプリケーション コンソールに、クライアント アプリケーションで入力したテキストが出力されることを確認します。

    ![アプリケーションの実行](./media/relay-hybrid-connections-node-get-started/running-applications.png)

これで、Node.js を使用してエンドツーエンドのハイブリッド接続アプリケーションを作成できました。

## <a name="next-steps"></a>次の手順
このクイック スタートでは、WebSocket を使用してメッセージを送受信する Node.js のクライアント アプリケーションとサーバー アプリケーションを作成しました。 Azure Relay のハイブリッド接続機能は、HTTP を使用したメッセージの送受信もサポートしています。 Azure Relay のハイブリッド接続で HTTP を使用する方法については、[Node.js の HTTP のクイック スタート](relay-hybrid-connections-http-requests-node-get-started.md)を参照してください。

このクイック スタートでは、Node.js を使用してクライアント アプリケーションとサーバー アプリケーションを作成しました。 .NET Framework を使用してクライアント アプリケーションとサーバー アプリケーションを作成する方法については、[.NET WebSocket のクイック スタート](relay-hybrid-connections-dotnet-get-started.md)または [.NET HTTP のクイック スタート](relay-hybrid-connections-http-requests-dotnet-get-started.md)を参照してください。


