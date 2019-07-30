---
title: Azure Functions および Azure SignalR Service を使用してリアルタイム アプリを構築する
description: サーバーレス アプリケーションでの Azure SignalR Service の使用の概要。
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: c88e2b012086f61aad3143c8fe6b0bd25ec333e5
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2019
ms.locfileid: "67275098"
---
# <a name="build-real-time-apps-with-azure-functions-and-azure-signalr-service"></a>Azure Functions および Azure SignalR Service を使用してリアルタイム アプリを構築する

Azure SignalR サービスと Azure Functions はどちらもインフラストラクチャを管理するのではなく、アプリケーションの構築に専念できる非常にスケーラブルなフル マネージド サービスであるため、2 つのサービスを併用して[サーバーレス](https://azure.microsoft.com/solutions/serverless/)環境でリアルタイム通信を提供するのが一般的です。

> [!NOTE]
> 対話型のチュートリアル「[Azure Functions と SignalR Service を使って、Web アプリケーションの自動更新を有効にする](https://docs.microsoft.com/learn/modules/automatic-update-of-a-webapp-using-azure-functions-and-signalr)」で、SignalR と Azure Functions を一緒に使用する方法を学習します。

## <a name="integrate-real-time-communications-with-azure-services"></a>Azure サービスとのリアルタイム通信の統合

Azure Functions では、クラウド内でイベントが発生するたびにトリガーするコードを、JavaScript、C#、Java などの[いくつかの言語](../azure-functions/supported-languages.md)で記述できます。 これらのイベントの例は次のとおりです。

* HTTP と webhook の要求
* 定期的なタイマー
* 次のような Azure サービスからのイベント。
    - Event Grid
    - Event Hubs
    - Service Bus
    - Cosmos DB 変更フィード
    - ストレージ - BLOB およびキュー
    - Salesforce や SQL Server などの Logic Apps コネクタ

Azure Functions を使用してこれらのイベントを Azure SignalR サービスと統合することによって、イベントが発生したことを数千のクライアントに通知できます。

Azure Functions と Azure SignalR サービスで実装できるリアルタイム サーバーレス メッセージングの一般的なシナリオは、次のとおりです。

* IoT デバイスのテレメトリをリアルタイム ダッシュ ボードまたはマップ上で可視化します
* Cosmos DB のドキュメントを更新したときにアプリケーションのデータを更新します
* Salesforce で新しい注文が作成されたときにアプリ内通知を送信します

## <a name="signalr-service-bindings-for-azure-functions"></a>Azure Functions における SignalR サービスのバインド

Azure Functions における SignalR サービスのバインドによって、Azure Function アプリは SignalR サービスに接続されているクライアントにメッセージを発行できます。 クライアントは、.NET、JavaScript、および Java で利用可能な SignalR クライアント SDK を使用してサービスに接続できます (他の言語も近いうちに利用可能になる予定です)。

### <a name="an-example-scenario"></a>シナリオ例

SignalR サービスのバインドを使用する方法の例として、Azure Functions を使用して Azure Cosmos DB および SignalR サービスと統合し、Cosmos DB 変更フィードに新しいイベントが表示されたときにリアルタイム メッセージを送信することがあります。

![Cosmos DB、Azure Functions、SignalR サービス](media/signalr-concept-azure-functions/signalr-cosmosdb-functions.png)

1. Cosmos DB コレクションが変更されます
2. Cosmos DB 変更フィードに変更イベントが反映されます
3. Cosmos DB トリガーを使用する変更イベントによって Azure Functions がトリガーされます
4. SignalR サービスの出力バインドは、SignalR サービスにメッセージを発行します
5. SignalR サービスは、接続されているすべてのクライアントにメッセージを発行します

### <a name="authentication-and-users"></a>認証およびユーザー

SignalR サービスでは、すべてのクライアントや、単一のユーザーに属しているなどのクライアントのサブセットにのみメッセージをブロードキャストすることができます。 Azure Functions 向けの SignalR サービスのバインドは、App Service 認証と組み合わせることで、Azure Active Directory、Facebook、Twitter などのプロバイダーでユーザーを認証できます。 これらの認証されたユーザーにメッセージを直接送信できます。

## <a name="next-steps"></a>次の手順

この記事では、SignalR サービスと Azure Functions を使用して、さまざまなサーバーレス リアルタイム メッセージング シナリオを有効にする方法についての概要を把握しました。

Azure Functions とSignalR Service を一緒に使用する方法の完全な詳細については、以下のリソースにアクセスしてください。

* [SignalR Service を使用した Azure Functions の開発と構成](signalr-concept-serverless-development-config.md)
* [Azure Functions と SignalR Service を使って、Web アプリケーションの自動更新を有効にする](https://docs.microsoft.com/learn/modules/automatic-update-of-a-webapp-using-azure-functions-and-signalr)

詳細については、これらのいずれかのクイック スタートを参照してください。

* [Azure SignalR サービス サーバーレス クイック スタート - C#](signalr-quickstart-azure-functions-csharp.md)
* [Azure SignalR サービス サーバーレス クイック スタート - JavaScript](signalr-quickstart-azure-functions-javascript.md)