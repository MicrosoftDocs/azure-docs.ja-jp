---
title: Azure Service Bus と Event Grid の統合の概要 | Microsoft Docs
description: この記事では、Azure Service Bus メッセージングを Azure Event Grid と統合する方法について説明します。
documentationcenter: .net
author: spelluru
ms.topic: conceptual
ms.date: 02/11/2021
ms.author: spelluru
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 658107bb74396891c8e6e05a9e8074a9416a5f6f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100369664"
---
# <a name="azure-service-bus-to-event-grid-integration-overview"></a>Azure Service Bus と Event Grid の統合の概要
Service Bus では、レシーバーがない場合にキューまたはサブスクリプションにメッセージがあるとき、Event Grid にイベントを発行できるようになりました。 Event Grid サブスクリプションを Service Bus 名前空間に作成してこれらのイベントをリッスンし、レシーバーの開始によってイベントに対応できます。 この機能により、Service Bus はリアクティブ プログラミング モデルで使用できます。 この機能の重要なシナリオは、メッセージが少量の Service Bus キューまたはサブスクリプションで、メッセージのレシーバー ポーリングを常時行う必要がなくなるというものです。 

この機能を有効にするために必要な事柄を次に示します。

* 1 つ以上の Service Bus キューが含まれた Service Bus Premium 名前空間、または 1 つ以上のサブスクリプションがある Service Bus トピックが含まれた Service Bus Premium 名前空間。
* Service Bus 名前空間への共同作成者アクセス。 Azure portal で Service Bus 名前空間に移動して、 **[アクセス制御 (IAM)]** を選択し、 **[ロールの割り当て]** タブを選択します。名前空間への共同作成者アクセス権を持っていることを確認します。 
* さらに、Service Bus 名前空間の Event Grid サブスクリプションが必要です。 このサブスクリプションは、取得すべきメッセージがあるという通知を Event Grid から受け取ります。 典型的なサブスクライバーとしては、Web アプリに情報を渡す webhook、Azure Functions、Azure App Service の Logic Apps 機能が考えられます。 その後サブスクライバーによって、メッセージが処理されます。 

![19][]

[!INCLUDE [event-grid-service-bus.md](../../includes/event-grid-service-bus.md)]

## <a name="event-grid-subscriptions-for-service-bus-namespaces"></a>Service Bus 名前空間の Event Grid サブスクリプション
Service Bus 名前空間の Event Grid サブスクリプションは、次の 3 とおりの方法で作成できます。

- Azure portal。 次のチュートリアルを参照して、Azure portal を使用して、ハンドラーとして Azure Logic Apps と Azure Functions を使用して Service Bus イベントの Event Grid サブスクリプションを作成する方法を確認してください。 
    - [Azure Logic Apps](service-bus-to-event-grid-integration-example.md#receive-messages-by-using-logic-apps)
    - [Azure Functions](service-bus-to-event-grid-integration-function.md#connect-the-function-and-namespace-via-event-grid)
* Azure CLI。 次の CLI の例は、Service Bus 名前空間によって作成された[システム トピック](../event-grid/system-topics.md)の Azure Functions サブスクリプションを作成する方法を示しています。

     ```azurecli-interactive
    namespaceid=$(az resource show --namespace Microsoft.ServiceBus --resource-type namespaces --name "<service bus namespace>" --resource-group "<resource group that contains the service bus namespace>" --query id --output tsv
    
    az eventgrid event-subscription create --resource-id $namespaceid --name "<YOUR EVENT GRID SUBSCRIPTION NAME>" --endpoint "<your_endpoint_url>" --subject-ends-with "<YOUR SERVICE BUS SUBSCRIPTION NAME>"
    ```
- PowerShell。 次に例を示します。
    ```powershell-interactive
    $namespaceID = (Get-AzServiceBusNamespace -ResourceGroupName "<YOUR RESOURCE GROUP NAME>" -NamespaceName "<YOUR NAMESPACE NAME>").Id
    
    New-AzEVentGridSubscription -EventSubscriptionName "<YOUR EVENT GRID SUBSCRIPTION NAME>" -ResourceId $namespaceID -Endpoint "<YOUR ENDPOINT URL>” -SubjectEndsWith "<YOUR SERVICE BUS SUBSCRIPTION NAME>"
    ```
### <a name="how-many-events-are-emitted-and-how-often"></a>発行されるイベントの数とイベントが発行される頻度

名前空間に複数のキューおよびトピック/サブスクリプションがある場合、キューごとおよびサブスクリプションごとに少なくとも 1 つのイベントを受信します。 イベントは、Service Bus エンティティにメッセージがなく新しいメッセージが届いた場合にすぐに発行されます。 または、アクティブなレシーバーを Service Bus が検出するまで 2 分おきに発行されます。 メッセージの読み取りはイベントを中断しません。

既定の Service Bus では、名前空間内のすべてのエンティティについてイベントが発行されます。 特定のエンティティについてのみイベントを取得したい場合、次のセクションを参照してください。

### <a name="use-filters-to-limit-where-you-get-events-from"></a>イベントの取得元をフィルターを使って制限する

たとえば、名前空間内の 1 つのキューまたはサブスクリプションからのみイベントを取得したい場合、Event Grid によって提供される "*先頭*" フィルターまたは "*末尾*" フィルターを使用できます。 一部のインターフェイスでは、これらのフィルターは "*プレ*" フィルターおよび "*サフィックス*" フィルターと呼ばれます。 複数のキューとサブスクリプションについてイベントを取得したい場合、複数の Event Grid サブスクリプションを作成し、それぞれにフィルターを適用できます。

## <a name="next-steps"></a>次のステップ
次のチュートリアルを参照してください。 
- [Event Grid 経由で受信した Service Bus メッセージを処理する Azure Logic Apps](service-bus-to-event-grid-integration-example.md#receive-messages-by-using-logic-apps)
- [Event Grid 経由で受信した Service Bus メッセージを処理する Azure Functions](service-bus-to-event-grid-integration-function.md#connect-the-function-and-namespace-via-event-grid)

[1]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgrid1.png
[19]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgriddiagram.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[21]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png
