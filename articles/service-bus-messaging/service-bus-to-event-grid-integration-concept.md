---
title: Azure Service Bus と Event Grid の統合の概要 | Microsoft Docs
description: Service Bus メッセージングと Event Grid の統合の説明
services: service-bus-messaging
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: f99766cb-8f4b-4baf-b061-4b1e2ae570e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/15/2018
ms.author: aschhab
ms.openlocfilehash: 5d4ece6b631882200c6f98f6de5daa543fdf7ce4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67072134"
---
# <a name="azure-service-bus-to-event-grid-integration-overview"></a>Azure Service Bus と Event Grid の統合の概要

Azure Service Bus では、Azure Event Grid との新しい統合が利用可能になりました。 この機能の重要なシナリオは、メッセージが少量の Service Bus キューまたはサブスクリプションで、メッセージのレシーバー ポーリングを常時行う必要がなくなるというものです。 

Service Bus では、レシーバーがない場合にキューまたはサブスクリプションにメッセージがあるとき、Event Grid にイベントを発行できるようになりました。 Event Grid サブスクリプションを Service Bus 名前空間に作成してこれらのイベントをリッスンし、レシーバーの開始によってイベントに対応できます。 この機能により、Service Bus はリアクティブ プログラミング モデルで使用できます。

この機能を有効にするために必要な事柄を次に示します。

* 1 つ以上の Service Bus キューが含まれた Service Bus Premium 名前空間、または 1 つ以上のサブスクリプションがある Service Bus トピックが含まれた Service Bus Premium 名前空間。
* Service Bus 名前空間への共同作成者アクセス。
* さらに、Service Bus 名前空間の Event Grid サブスクリプションが必要です。 このサブスクリプションは、取得すべきメッセージがあるという通知を Event Grid から受け取ります。 典型的なサブスクライバーとしては、Web アプリに情報を渡す webhook、Azure Functions、Azure App Service の Logic Apps 機能が考えられます。 その後サブスクライバーによって、メッセージが処理されます。 

![19][]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="verify-that-you-have-contributor-access"></a>共同作成者アクセスがあることの確認
Service Bus 名前空間に移動して、 **[アクセス制御 (IAM)]** を選択し、 **[ロールの割り当て]** を選択します。名前空間への共同作成者アクセス権を持っていることを確認します。 

### <a name="events-and-event-schemas"></a>イベントとイベント スキーマ

現在の Service Bus では、2 つのシナリオでイベントが送信されます。

* [ActiveMessagesWithNoListenersAvailable](#active-messages-available-event)
* DeadletterMessagesAvailable

さらに、Service Bus では、標準の Event Grid セキュリティと[認証メカニズム](https://docs.microsoft.com/azure/event-grid/security-authentication)が使用されます。

詳細については、「[Azure Event Grid イベント スキーマ](https://docs.microsoft.com/azure/event-grid/event-schema)」を参照してください。

#### <a name="active-messages-available-event"></a>アクティブなメッセージが利用可能なイベント

このイベントは、キューまたはサブスクリプションにアクティブなメッセージがあり、リッスンしているレシーバーがない場合に生成されます。

このイベントのスキーマは、次のようになります。

```JSON
{
  "topic": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "eventType": "Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}
```

#### <a name="dead-letter-messages-available-event"></a>配信不能なメッセージが利用可能なイベント

配信不能キューごとに少なくとも 1 つのイベントを受信します。このキューには、メッセージはありますがアクティブなレシーバーはありません。

このイベントのスキーマは、次のようになります。

```JSON
[{
  "topic": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "eventType": "Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListener",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="how-many-events-are-emitted-and-how-often"></a>発行されるイベントの数とイベントが発行される頻度

名前空間に複数のキューおよびトピック/サブスクリプションがある場合、キューごとおよびサブスクリプションごとに少なくとも 1 つのイベントを受信します。 イベントは、Service Bus エンティティにメッセージがなく新しいメッセージが届いた場合にすぐに発行されます。 または、アクティブなレシーバーを Service Bus が検出するまで 2 分おきに発行されます。 メッセージの読み取りはイベントを中断しません。

既定の Service Bus では、名前空間内のすべてのエンティティについてイベントが発行されます。 特定のエンティティについてのみイベントを取得したい場合、次のセクションを参照してください。

### <a name="use-filters-to-limit-where-you-get-events-from"></a>イベントの取得元をフィルターを使って制限する

たとえば、名前空間内の 1 つのキューまたはサブスクリプションからのみイベントを取得したい場合、Event Grid によって提供される "*先頭*" フィルターまたは "*末尾*" フィルターを使用できます。 一部のインターフェイスでは、これらのフィルターは "*プレ*" フィルターおよび "*サフィックス*" フィルターと呼ばれます。 複数のキューとサブスクリプションについてイベントを取得したい場合、複数の Event Grid サブスクリプションを作成し、それぞれにフィルターを適用できます。

## <a name="create-event-grid-subscriptions-for-service-bus-namespaces"></a>Service Bus 名前空間の Event Grid サブスクリプションを作成する

Service Bus 名前空間の Event Grid サブスクリプションは、次の 3 とおりの方法で作成できます。

* Azure ポータルで次の操作を行います。
* [Azure CLI](#azure-cli-instructions)
* [PowerShell](#powershell-instructions)

## <a name="azure-portal-instructions"></a>Azure Portal での手順

新しい Event Grid サブスクリプションを作成するには、次の手順に従います。
1. Azure Portal で目的の名前空間に移動します。
2. 左側のウィンドウで **[Event Grid]** を選択します。 
3. **[イベント サブスクリプション]** を選びます。  

   次の画像には、Event Grid サブスクリプションがある名前空間が表示されています。

   ![Event Grid サブスクリプション](./media/service-bus-to-event-grid-integration-concept/sbtoeventgridportal.png)

   次の画像には、特定のフィルターなしで関数または webhook をサブスクライブする方法が示されています。

   ![21][]

## <a name="azure-cli-instructions"></a>Azure CLI の手順

最初に、Azure CLI バージョン 2.0 以降がインストールされていることを確認してください。 [インストーラーをダウンロード](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)します。 **Windows + X** キーを押し、管理者のアクセス許可で新しい PowerShell コンソールを開きます。 Azure Portal 内のコマンド シェルを使用してもかまいません。

次のコードを実行します。

 ```azurecli-interactive
az login

az account set -s "<Azure subscription name>"

namespaceid=$(az resource show --namespace Microsoft.ServiceBus --resource-type namespaces --name "<service bus namespace>" --resource-group "<resource group that contains the service bus namespace>" --query id --output tsv

az eventgrid event-subscription create --resource-id $namespaceid --name "<YOUR EVENT GRID SUBSCRIPTION NAME (CAN BE ANY NOT EXISTING)>" --endpoint "<your_function_url>" --subject-ends-with "<YOUR SERVICE BUS SUBSCRIPTION NAME>"
```

BASH を使用している場合 

## <a name="powershell-instructions"></a>PowerShell の手順

Azure PowerShell がインストールされていることを確認してください。 [インストーラーをダウンロード](https://docs.microsoft.com/powershell/azure/install-Az-ps)します。 **Windows + X** キーを押し、管理者のアクセス許可で新しい PowerShell コンソールを開きます。 Azure Portal 内のコマンド シェルを使用してもかまいません。

```powershell-interactive
Connect-AzAccount

Select-AzSubscription -SubscriptionName "<YOUR SUBSCRIPTION NAME>"

# This might be installed already
Install-Module Az.ServiceBus

$NSID = (Get-AzServiceBusNamespace -ResourceGroupName "<YOUR RESOURCE GROUP NAME>" -Na
mespaceName "<YOUR NAMESPACE NAME>").Id

New-AzEVentGridSubscription -EventSubscriptionName "<YOUR EVENT GRID SUBSCRIPTION NAME (CAN BE ANY NOT EXISTING)>" -ResourceId $NSID -Endpoint "<YOUR FUNCTION URL>” -SubjectEndsWith "<YOUR SERVICE BUS SUBSCRIPTION NAME>"
```

ここから、他のセットアップ オプションを調べたり、イベントが送信されていることをテストしたりできます。

## <a name="next-steps"></a>次の手順

* Service Bus と Event Grid の[例](service-bus-to-event-grid-integration-example.md)を確認します。
* [Event Grid](https://docs.microsoft.com/azure/event-grid/) の詳細を確認します。
* [Azure Functions](https://docs.microsoft.com/azure/azure-functions/) について学習します。
* [Logic Apps](https://docs.microsoft.com/azure/logic-apps/) の詳細を見る
* [Service Bus](https://docs.microsoft.com/azure/service-bus/) の詳細を確認します。

[1]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgrid1.png
[19]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgriddiagram.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[21]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png
