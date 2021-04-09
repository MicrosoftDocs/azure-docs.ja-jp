---
title: インクルード ファイル
description: インクルード ファイル
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 10/15/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: d12df7197945a514ed8d3d0dca77271fb4bd0903
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96509315"
---
## <a name="prerequisites"></a>前提条件
[Azure サブスクリプション](../articles/guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)をお持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)を作成してください。

## <a name="create-a-service-bus-namespace"></a>Service Bus 名前空間を作成する
このチュートリアルの手順に従ってください。[クイック スタート: Azure portal を使用して Service Bus トピックとそのサブスクリプションを作成する](../articles/service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md)」で確認し、次のタスクを実行します:

- **Premium** Service Bus 名前空間を作成します。 
- 接続文字列を取得します。 
- Service Bus トピックを作成します。
- トピックへのサブスクリプションを作成します。 このチュートリアルで必要なサブスクリプションは 1 つだけです。そのため、サブスクリプション S2 および S3 を作成する必要はありません。 

## <a name="send-messages-to-the-service-bus-topic"></a>Service Bus トピックにメッセージを送信する
この手順では、サンプル アプリケーションを使用して、前の手順で作成した Service Bus トピックにメッセージを送信します。 

1. [GitHub の azure-service-bus リポジトリ](https://github.com/Azure/azure-service-bus/)を複製します。
2. Visual Studio で *\samples\DotNet\Azure.Messaging.ServiceBus\ServiceBusEventGridIntegration* フォルダーに移動し、*SBEventGridIntegration.sln* ファイルを開きます。
3. ソリューション エクスプローラー ウィンドウで、 **[MessageSender]** プロジェクトを展開し、 **[Program.cs]** を選択します。
4. `<SERVICE BUS NAMESPACE - CONNECTION STRING>` を Service Bus 名前空間への接続文字列で置き換え、`<TOPIC NAME>` をトピックの名前で置き換えます。 

    ```csharp
    const string ServiceBusConnectionString = "<SERVICE BUS NAMESPACE - CONNECTION STRING>";
    const string TopicName = "<TOPIC NAME>";
    ```
5. 5 つのテスト メッセージを Service Bus トピックに送信するプログラム (`const int numberOfMessages = 5;`) をビルドして実行します。 

    :::image type="content" source="./media/service-bus-event-grid-prerequisites/console-app-output.png" alt-text="コンソールのアプリ出力":::
