---
title: "Node での Azure Relay ハイブリッド接続の概要 | Microsoft Docs"
description: "Azure Relay ハイブリッド接続用の Node.js コンソール アプリケーションを作成します。"
services: service-bus-relay
documentationcenter: node
author: sethmanheim
manager: timlt
editor: 
ms.assetid: e44e4867-3cf3-46be-8f8a-7671e2013bc4
ms.service: service-bus-relay
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: node
ms.workload: na
ms.date: 07/07/2017
ms.author: sethm
ms.openlocfilehash: c3bfc45969f250059988129f532edd12dfe3dcfe
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-relay-hybrid-connections"></a>Relay ハイブリッド接続の概要

[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

このチュートリアルでは、[Azure Relay ハイブリッド接続](relay-what-is-it.md#hybrid-connections)の概要について説明し、対応するリスナー アプリケーションにメッセージを送信するクライアント アプリケーションを Node.js で作成する方法を紹介します。 

## <a name="what-will-be-accomplished"></a>作業内容

ハイブリッド接続ではクライアントとサーバーの 2 つのコンポーネントが必要になります。そのため、このチュートリアルでは 2 つのコンソール アプリケーションを作成します。 手順は次のようになります。

1. Azure Portal を使用した Relay 名前空間の作成
2. Azure Portal を使用したハイブリッド接続の作成
3. メッセージを受信するサーバー コンソール アプリケーションの作成
4. メッセージを送信するクライアント コンソール アプリケーションの作成

## <a name="prerequisites"></a>前提条件

1. [Node.js](https://nodejs.org/en/)。
2. Azure サブスクリプション。

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1.Azure ポータルを使用した名前空間の作成

Relay 名前空間を既に作成してある場合は、「[Azure Portal を使用したハイブリッド接続の作成](#2-create-a-hybrid-connection-using-the-azure-portal)」セクションに進んでください。

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="2-create-a-hybrid-connection-using-the-azure-portal"></a>2.Azure Portal を使用したハイブリッド接続の作成

ハイブリッド接続を既に作成してある場合は、「[サーバー アプリケーションの作成](#3-create-a-server-application-listener)」セクションに進んでください。

[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="3-create-a-server-application-listener"></a>3.サーバー アプリケーション (リスナー) の作成

Relay からのメッセージをリッスンおよび受信するために、Node.js コンソール アプリケーションを作成します。

[!INCLUDE [relay-hybrid-connections-node-get-started-server](../../includes/relay-hybrid-connections-node-get-started-server.md)]

## <a name="4-create-a-client-application-sender"></a>4.クライアント アプリケーション (センダー) の作成

Relay にメッセージを送信するために、Node.js コンソール アプリケーションを作成します。

[!INCLUDE [relay-hybrid-connections-node-get-started-client](../../includes/relay-hybrid-connections-node-get-started-client.md)]

## <a name="5-run-the-applications"></a>5.アプリケーションの実行

1. Node.js コマンド プロンプトで「`node listener.js`」と入力して、サーバー アプリケーションを実行します。
2. Node.js コマンド プロンプトで「`node sender.js`」と入力してサーバー アプリケーションを実行し、何かテキストを入力します。
3. サーバー アプリケーション コンソールに、クライアント アプリケーションで入力したテキストが出力されることを確認します。

![アプリケーションの実行](./media/relay-hybrid-connections-node-get-started/running-applications.png)

これで、Node.js を使用してエンドツーエンドのハイブリッド接続アプリケーションを作成できました。

## <a name="next-steps"></a>次のステップ:

* [Relay に関する FAQ](relay-faq.md)
* [名前空間を作成する](relay-create-namespace-portal.md)
* [.NET を使って作業を開始する](relay-hybrid-connections-dotnet-get-started.md)
* [Node を使って作業を開始する](relay-hybrid-connections-node-get-started.md)

