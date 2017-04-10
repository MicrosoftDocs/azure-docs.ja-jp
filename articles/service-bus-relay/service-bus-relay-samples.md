---
title: "Azure Service Bus Relay サンプルの概要 | Microsoft Docs"
description: "Service Bus Relay サンプルを分類して説明し、各サンプルへのリンクが含まれています。"
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 9237a9a2-f126-4d3f-9f9b-604ee6b32153
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/13/2017
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 6027c973f0104fff9705a70f7812f62562165c1e
ms.openlocfilehash: d830770c49caeacf762382ce1c6113d9196a5994
ms.lasthandoff: 02/14/2017


---
# <a name="service-bus-relay-samples"></a>Service Bus Relay のサンプル
Service Bus Relay のサンプルでは、[Service Bus Relay](https://azure.microsoft.com/services/service-bus/) の主要な機能をデモンストレーションしています。 この記事では、使用可能なサンプルを分類して説明しています。また、各サンプルへのリンクも含まれています。

> [!NOTE]
> Service Bus のサンプルは Azure SDK と共にインストールされません。 サンプルを入手するには、 [Azure SDK のサンプル ページ](https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=5)にアクセスしてください。
> 
> さらに、更新された Service Bus Relay のサンプルのセットが[こちら](https://github.com/Azure-Samples/azure-servicebus-relay-samples)にあります。  
> 
> 

Service Bus メッセージングのサンプルについては、「[Service Bus メッセージングのサンプル](../service-bus-messaging/service-bus-samples.md)」をご覧ください。

## <a name="azure-service-bus-relay"></a>Azure Service Bus Relay
次のサンプルでは、Azure Relay サービスを使うアプリケーションの作成方法を説明します。

リレーのサンプルでは、Relay の名前空間にアクセスするための接続文字列が必要です。

### <a name="to-obtain-a-connection-string-for-azure-relay"></a>Azure Relay の接続文字列を取得するには
1. [Azure Portal](http://portal.azure.com) にログオンします。
2. **[すべてのリソース]** ウィンドウで、名前空間の一覧をクリックして展開します。
3. 一覧で、Relay 名前空間の名前をクリックします。
4. 名前空間ブレードで、**[共有アクセス ポリシー]** をクリックします。
5. **[共有アクセス ポリシー]** ブレードで、**[RootManageSharedAccessKey]** をクリックします。
6. 接続文字列をクリップボードにコピーします。

### <a name="to-obtain-a-connection-string-for-service-bus-for-windows-server"></a>Service Bus for Windows Server の接続文字列を取得するには
1. 次の PowerShell コマンドレットを実行します。
   
    ```powershell
    get-sbClientConfiguration
    ```
2. 接続文字列をサンプルの App.config ファイルに貼り付けます。

## <a name="azure-relay"></a>Azure Relay
Azure Relay を示すサンプルです。

### <a name="getting-started"></a>使用の開始
| サンプル名 | Description | SDK の最小バージョン | 可用性 |
| --- | --- | --- | --- |
| [WCF リレー型メッセージング: Azure](http://code.msdn.microsoft.com/Relayed-Messaging-Windows-0d2cede3) |Azure で Service Bus のクライアントとサービスを実行する方法について説明します。 このサンプルでは、プログラムによって Service Bus を構成します。 構成ファイルに格納されるのは、環境情報とセキュリティ情報のみです。 |1.8 |Microsoft Azure Service Bus |
| [WCF リレー型メッセージング認証: 共有シークレット](http://code.msdn.microsoft.com/Relayed-Messaging-92b04c02) |発行者名と発行者のシークレットを使用して、Service Bus で認証する方法について説明します。 |1.8 |Microsoft Azure Service Bus |
| [WCF リレー型メッセージング認証: WebNoAuth](http://code.msdn.microsoft.com/Relayed-Messaging-a4f0b831) |クライアント ユーザー認証を必要としない HTTP サービスの公開方法について説明します。 |1.8 |Microsoft Azure Service Bus |
| [WCF リレー型メッセージング バインド: WebHttp](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-a6477ba0) |Web プログラミング モデルを使用して、 **WebHttpRelayBinding** バインドでバイナリ データを返す方法について説明します。 |1.8 |Microsoft Azure Service Bus |
| [WCF リレー型メッセージング バインド: NetTcp のリレー](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-2dec7692) |**NetTcpRelayBinding** バインドの使用方法について説明します。 |1.8 |Microsoft Azure Service Bus |

### <a name="exploring-features"></a>機能の探索
Service Bus Relay のさまざまな機能を説明するサンプルです。

| サンプル名 | Description | SDK の最小バージョン | 可用性 |
| --- | --- | --- | --- |
| [WCF リレー型メッセージング認証: 単純な WebToken](http://code.msdn.microsoft.com/Relayed-Messaging-32c74392) |単純な Web トークンの資格情報を使用して、Service Bus で認証する方法について説明します。 このサンプルは Echo サンプルに似ていますが、いくつかの変更点があります。 具体的には、このサンプルでは、ServiceHost (サービス) アプリケーションと ChannelFactory (クライアント) アプリケーションに動作を追加します。 |1.8 |Microsoft Azure Service Bus |
| [WCF リレー型メッセージング: 負荷分散](http://code.msdn.microsoft.com/Relayed-Messaging-Load-bd76a9f8) |Microsoft Azure Service Bus を使用して複数の受信者にメッセージをルーティングする方法について説明します。 **NetTcpRelayBinding** バインドを介してクライアントと通信する簡単なサービスの複数のインスタンスを示します。 |1.8 |Microsoft Azure Service Bus |
| [WCF リレー型メッセージング バインド: Net イベント](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-c0176977) |Microsoft Azure Service Bus での **NetEventRelayBinding** バインドの使用について説明します。 |1.8 |Microsoft Azure Service Bus |
| [WCF リレー型メッセージング バインド: WS2007Http セッション](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-ef1f1fcb) |信頼できるセッションを有効にした **WS2007HttpRelayBinding** バインドの使用について説明します。 また、プログラムではなく構成ファイルで Service Bus 資格情報を指定する方法も示します。 |1.8 |Microsoft Azure Service Bus |
| [WCF リレー型メッセージング バインド: WS2007Http MsgSecCertificate](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-f29c9da5) |**WS2007HttpRelayBinding** バインドとメッセージ セキュリティを使用して、クライアントに Service Bus での認証を要求しながらエンド ツー エンドのメッセージをセキュリティで保護する方法について説明します。 |1.8 |Microsoft Azure Service Bus |
| [WCF リレー型メッセージング: メタデータ交換](http://code.msdn.microsoft.com/Relayed-Messaging-Metadata-f122312e) |リレー バインドを使用するメタデータ エンドポイントを公開する方法について説明します。 **MetadataExchange** は、**NetTcpRelayBinding**、**NetOnewayRelayBinding**、**BasicHttpRelayBinding**、**WS2007HttpRelayBinding** というリレー バインドでサポートされます。 |1.8 |Microsoft Azure Service Bus |
| [WCF リレー型メッセージング バインド: NetTcp Direct](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-ca039161) |**ハイブリッド**接続モードをサポートするように **NetTcpRelayBinding** バインドを構成する方法について説明します。このモードでは、最初にリレー型接続が確立され、可能な場合に、クライアントとサービス間の直接接続に自動的に切り替わります。 |1.8 |Microsoft Azure Service Bus |
| [WCF リレー型メッセージング バインド: NetTcp MsgSec UserName](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-30542392) |メッセージ セキュリティでの **NetTcpRelayBinding** バインドの使用方法について説明します。 |1.8 |Microsoft Azure Service Bus |
| [WCF リレー型メッセージング バインド: Net Oneway](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-bb5b813a) |**NetOnewayRelayBinding** バインドを使用したサービス エンドポイントの公開方法と使用方法について説明します。 |1.8 |Microsoft Azure Service Bus |
| [WCF リレー型メッセージング バインド: WS2007Http Simple](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-aa4b793a) |**WS2007HttpRelayBinding** バインドの使用方法について説明します。 セキュリティ オプションを使用せず、クライアントに認証を要求しない簡単なサービスを示します。 |1.8 |Microsoft Azure Service Bus |

## <a name="next-steps"></a>次のステップ
Service Bus の概念の要約については、次のトピックを参照してください。

* [Azure Relay の概要](relay-what-is-it.md)
* [Service Bus のアーキテクチャ](../service-bus-messaging/service-bus-architecture.md)
* [Service Bus の基礎](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)


