---
title: "Service Bus メッセージングのサンプルの概要 | Microsoft Docs"
description: "Service Bus メッセージングのサンプルを分類して説明し、各サンプルへのリンクが含まれています。"
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 0b420343-2d2a-4c65-98f1-ee0e39ef55c8
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/07/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 719403ca2ef90cdcf5f76fe15f7a33095e71a74c


---
# <a name="service-bus-messaging-samples"></a>Service Bus メッセージングのサンプル
Service Bus メッセージングのサンプルでは、[Service Bus メッセージング](https://azure.microsoft.com/services/service-bus/) (クラウド サービス) と [Service Bus for Windows Server](https://msdn.microsoft.com/library/dn282144.aspx) の主な機能を紹介します。 この記事では、使用可能なサンプルを分類して説明しています。また、各サンプルへのリンクも含まれています。

> [!NOTE]
> Service Bus のサンプルは SDK と共にインストールされません。 サンプルを入手するには、 [Azure SDK のサンプル ページ](https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=5)にアクセスしてください。
> 
> さらに、 [ここ](https://github.com/Azure-Samples/azure-servicebus-messaging-samples) には、Service Bus メッセージングのサンプルの更新されたセットがあります (この記事の執筆時点では、この記事に記述されていません)。  
> 
> 

リレーのサンプルについては、[「Service Bus relay samples」](../service-bus-relay/service-bus-relay-samples.md) (Service Bus Relay のサンプル) を参照してください。

## <a name="service-bus-messaging"></a>Service Bus メッセージング
次のサンプルでは、Service Bus メッセージングを使用するアプリケーションの作成方法を説明しています。

メッセージングのサンプルでは、ご使用の Service Bus の名前空間にアクセスするための接続文字列が必要です。

### <a name="to-obtain-a-connection-string-for-azure-service-bus"></a>Azure Service Bus の接続文字列を取得するには
1. [Azure Portal](http://portal.azure.com) にログオンします。
2. 左側の列で、 **[Service Bus]**をクリックします。
3. 一覧から、ご使用の名前空間の名前をクリックします。
4. 名前空間ブレードで、**[共有アクセス ポリシー]** をクリックします。
5. **[共有アクセス ポリシー]** ブレードで、**[RootManageSharedAccessKey]** をクリックします。
6. 接続文字列をクリップボードにコピーします。

### <a name="to-obtain-a-connection-string-for-service-bus-for-windows-server"></a>Service Bus for Windows Server の接続文字列を取得するには
1. 次の PowerShell コマンドレットを実行します。
   
    ```
    get-sbClientConfiguration
    ```
2. 接続文字列をサンプルの App.config ファイルに貼り付けます。

### <a name="getting-started-samples"></a>入門サンプル
次のサンプルでは、基本的なメッセージング機能について説明します。

| サンプル名 | Description | SDK の最小バージョン | 可用性 |
| --- | --- | --- | --- |
| [はじめに: キューによるメッセージング](http://code.msdn.microsoft.com/Getting-Started-Brokered-aa7a0ac3) |Microsoft Azure Service Bus を使用して、キューからメッセージを送受信する方法を示します。 |1.8 |Microsoft Azure Service Bus; Service Bus for Windows Server |
| [はじめに: トピックによるメッセージング](http://code.msdn.microsoft.com/Getting-Started-Brokered-614d42e5) |Microsoft Azure Service Bus を使用して、複数のサブスクリプションを持つトピックからメッセージを送受信する方法を示します。 |1.8 |Microsoft Azure Service Bus; Service Bus for Windows Server |

### <a name="exploring-features"></a>機能の探索
次のサンプルでは、Service Bus のさまざまな機能について説明します。

| サンプル名 | Description | SDK の最小バージョン | 可用性 |
| --- | --- | --- | --- |
| [HTTP トークン プロバイダー](http://code.msdn.microsoft.com/Service-Bus-HTTP-Token-38f2cfc5) |Service Bus を使用して HTTP/REST クライアントを認証する際のさまざまな方法について説明します。 |2.1 |Microsoft Azure Service Bus; Service Bus for Windows Server |
| [Service Bus HTTP クライアント](http://code.msdn.microsoft.com/Service-Bus-HTTP-client-fe7da74a) |Service Bus から HTTP/REST 経由でメッセージを送受信する方法について説明します。 |2.3 |Microsoft Azure Service Bus; Service Bus for Windows Server |
| [Service Bus の自動転送](http://code.msdn.microsoft.com/Service-Bus-Autoforwarding-b9df470b) |キュー、サブスクリプション、配信不能キューからのメッセージを別のキューまたはトピックに自動的に転送する方法について説明します。 また、転送キュー経由でキューまたはトピックにメッセージを送信する方法についても説明します。 |2.3 |Microsoft Azure Service Bus; Service Bus for Windows Server |
| [ブローカー メッセージング: WCF チャネル セッションのサンプル](http://code.msdn.microsoft.com/Brokered-Messaging-WCF-0a526451) |Windows Communication Foundation (WCF) チャネルを使用して、Microsoft Azure Service Bus を使用する方法について説明します。 このサンプルでは、WCF チャネルを使用した Service Bus キュー経由のメッセージの送受信を示します。 このサンプルは、Service Bus でのセッションの通信とセッション以外の通信を示します。 |1.8 |Microsoft Azure Service Bus; Service Bus for Windows Server |
| [ブローカー メッセージング: トランザクション](http://code.msdn.microsoft.com/Brokered-Messaging-8cd41d1e) |メッセージング操作のバッチがアトミックにコミットされるように、トランザクション スコープ内の Microsoft Azure Service Bus のメッセージング機能を使用する方法について説明します。 |1.8 |Microsoft Azure Service Bus; Service Bus for Windows Server |
| [ブローカー メッセージング: REST を使用した管理操作](http://code.msdn.microsoft.com/Brokered-Messaging-569cff88) |REST を使用して Service Bus の管理操作を実行する方法について説明します。 |1.8 |Microsoft Azure Service Bus; Service Bus for Windows Server |
| [リソース プロバイダー REST API](http://code.msdn.microsoft.com/Service-Bus-Resource-5d887203) |新しい Service Bus RDFE REST API を使用して、名前空間とメッセージング エンティティを管理する方法について説明します。 |1.8 |Microsoft Azure Service Bus; Service Bus for Windows Server |
| [ブローカー メッセージング: WCF サービス セッションのサンプル](http://code.msdn.microsoft.com/Brokered-Messaging-WCF-db4262c2) |WCF サービス モデルを使用して、Microsoft Azure Service Bus を使用する方法について説明します。 このサンプルでは、WCF サービス モデルを使用した Service Bus キュー経由のセッションベースの通信を示します。 |1.8 |Microsoft Azure Service Bus; Service Bus for Windows Server |
| [Brokered Messaging: Request Response (ブローカー メッセージング: 要求応答)](http://code.msdn.microsoft.com/Brokered-Messaging-Request-2b4ff5d8) |Microsoft Azure Service Bus と要求/応答機能の使用方法について説明します。 このサンプルでは、Service Bus キューを介した単純なクライアントとサーバーの通信を示します。 |1.8 |Microsoft Azure Service Bus; Service Bus for Windows Server |
| [ブローカー メッセージング: 配信不能キュー](http://code.msdn.microsoft.com/Brokered-Messaging-Dead-22536dd8) |Microsoft Azure Service Bus とメッセージングの "配信不能キュー" 機能を使用する方法を示します。 このサンプルでは、Service Bus キューを介した送信者と受信者の単純な通信を示します。 |1.8 |Microsoft Azure Service Bus; Service Bus for Windows Server |
| [ブローカー メッセージング: 遅延メッセージ](http://code.msdn.microsoft.com/Brokered-Messaging-ccc4f879) |Microsoft Azure Service Bus のメッセージ遅延機能を使用する方法について説明します。 このサンプルでは、Service Bus キューを介した送信者と受信者の単純な通信を示します。 |1.8 |Microsoft Azure Service Bus; Service Bus for Windows Server |
| [ブローカー メッセージング: セッション メッセージ](http://code.msdn.microsoft.com/Brokered-Messaging-Session-41c43fb4) |Microsoft Azure Service Bus とメッセージング セッション機能を使用する方法について説明します。 このサンプルでは、Service Bus キューを介した送信者と受信者の単純な通信を示します。 |1.8 |Microsoft Azure Service Bus; Service Bus for Windows Server |
| [ブローカー メッセージング: 要求応答トピック](http://code.msdn.microsoft.com/Brokered-Messaging-Request-6759a36e) |Microsoft Azure Service Bus のトピックとサブスクリプションを使用して要求/応答パターンを実装する方法について説明します。 このサンプルでは、Service Bus トピックを介したクライアントとサーバーの単純な通信を示します。 |1.8 |Microsoft Azure Service Bus; Service Bus for Windows Server |
| [ブローカー メッセージング: 要求応答キュー](http://code.msdn.microsoft.com/Brokered-Messaging-Request-0ce8fcaf) |Microsoft Azure Service Bus と要求/応答機能の使用方法について説明します。 このサンプルでは、2 つの Service Bus キューを介したクライアントとサーバーの単純な通信を示します。 |1.8 |Microsoft Azure Service Bus; Service Bus for Windows Server |
| [ブローカー メッセージング: 重複データ検出](http://code.msdn.microsoft.com/Brokered-Messaging-c0acea25) |キューと共に Microsoft Azure Service Bus の重複メッセージ検出を使用する方法について説明します。 このサンプルでは 2 つのキューを作成します。1 つは重複データ検出を有効にし、もう 1 つは重複検出を使用しません。 |1.8 |Microsoft Azure Service Bus; Service Bus for Windows Server |
| [ブローカー メッセージング: 非同期メッセージング](http://code.msdn.microsoft.com/Brokered-Messaging-Async-211c1e74) |Microsoft Azure Service Bus を使用して、キューから非同期的にメッセージを送受信する方法について説明します。 このキューは、1 人の送信者と任意の数の受信者 (ここでは 1 人) の間で分離された非同期通信を提供します。 |1.8 |Microsoft Azure Service Bus; Service Bus for Windows Server |
| [ブローカー メッセージング: 高度なフィルター](http://code.msdn.microsoft.com/Brokered-Messaging-6b0d2749) |Microsoft Azure Service Bus のパブリッシュ/サブスクライブの高度なフィルターを使用する方法について説明します。 このサンプルでは、フィルター定義の異なる 1 つのトピックと 3 つのサブスクリプションを作成し、そのトピックにメッセージを送信して、サブスクリプションからすべてのメッセージを受信します。 |1.8 |Microsoft Azure Service Bus; Service Bus for Windows Server |
| [ブローカー メッセージング: メッセージのプリフェッチ](http://code.msdn.microsoft.com/Brokered-Messaging-be2dac1d) |Microsoft Azure Service Bus のメッセージのプリフェッチ機能を使用する方法について説明します。 ここでは、受信時にメッセージのプリフェッチ機能を使用する方法を示します。 |1.8 |Microsoft Azure Service Bus; Service Bus for Windows Server |

## <a name="service-bus-reference-tools"></a>Service Bus リファレンス ツール
次のサンプルでは、サービスのその他さまざまな機能について説明します。

| サンプル名 | Description | SDK の最小バージョン | 可用性 |
| --- | --- | --- | --- |
| [Service Bus エクスプローラー](http://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a) |Service Bus エクスプローラーを使用すると、ユーザーは Service Bus サービスの名前空間に接続し、簡単にメッセージング エンティティを管理できます。 このツールには、インポート/エクスポート機能、メッセージング エンティティとリレー サービスをテストする機能など、高度な機能が用意されています。 |1.8 |Microsoft Azure Service Bus; Service Bus for Windows Server |
| [承認: SBAzTool](http://code.msdn.microsoft.com/Authorization-SBAzTool-6fd76d93) |このサンプルでは、Service Bus で使用するための Microsoft Azure Active Directory Access Control (Access Control Service または ACS とも呼ばれます) のサービス ID を作成、管理する方法について説明します。 |該当なし |Microsoft Azure Service Bus |

## <a name="next-steps"></a>次のステップ
Service Bus の概念の要約については、次のトピックを参照してください。

* [Service Bus メッセージングの概要](service-bus-messaging-overview.md)
* [Service Bus のアーキテクチャ](service-bus-architecture.md)
* [Service Bus の基礎](service-bus-fundamentals-hybrid-solutions.md)




<!--HONumber=Nov16_HO3-->


