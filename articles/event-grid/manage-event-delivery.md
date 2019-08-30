---
title: Azure Event Grid サブスクリプションの配信不能と再試行に関する方針
description: Event Grid のイベント配信オプションをカスタマイズする方法について説明します。 配信不能メッセージの送信先を設定し、配信の再試行時間を指定します。
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/06/2019
ms.author: spelluru
ms.openlocfilehash: 63bae62ed89bd0bbc167a88274002d1fa1e9b86d
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2019
ms.locfileid: "68933374"
---
# <a name="dead-letter-and-retry-policies"></a>配信不能と再試行に関する方針

イベント サブスクリプションの作成時には、イベント配信の設定をカスタマイズできます。 この記事では、配信不能の場所の設定方法と、再試行の設定のカスタマイズ方法を示します。 これらの機能については、「[Event Grid によるメッセージの配信と再試行](delivery-and-retry.md)」をご覧ください。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="set-dead-letter-location"></a>配信不能の場所の設定

配信不能の場所を設定するには、エンドポイントに配信できなかったイベントを保持するためストレージ アカウントが必要です。 この例では、既存のストレージ アカウントのリソース ID を取得します。 ここでは、配信不能エンドポイント用にそのストレージ アカウント内のコンテナーを使用するイベント サブスクリプションを作成します。

> [!NOTE]
> - この記事のコマンドを実行する前に、ストレージ アカウントを作成し、ストレージに BLOB コンテナーを作成してください。
> - Event Grid サービスは、このコンテナーに BLOB を作成します。 BLOB の名前には、すべての文字を大文字にした Event Grid サブスクリプションの名前が付けられます。 たとえば、サブスクリプションの名前が My-Blob-Subscription の場合、配信不能 BLOB の名前は、MY-BLOB-SUBSCRIPTION (myblobcontainer/MY-BLOB-SUBSCRIPTION/2019/8/8/5/111111111-1111-1111-1111-111111111111.json) になります。 この動作は、Azure サービス間での大文字と小文字の処理の違いから保護するためのものです。


### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
containername=testcontainer

topicid=$(az eventgrid topic show --name demoTopic -g gridResourceGroup --query id --output tsv)
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --deadletter-endpoint $storageid/blobServices/default/containers/$containername
```

配信不能処理を無効にするには、コマンドを再実行してイベント サブスクリプションを作成しますが、`deadletter-endpoint` の値は指定しないでください。 イベント サブスクリプションを削除する必要はありません。

> [!NOTE]
> ローカル マシンで Azure CLI を使用する場合は、Azure CLI バージョン 2.0.56 以降を使用してください。 Azure CLI の最新バージョンをインストールする手順については、「[Azure CLI のインストール](/cli/azure/install-azure-cli)」を参照してください。

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
$containername = "testcontainer"

$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id
$storageid = (Get-AzStorageAccount -ResourceGroupName gridResourceGroup -Name demostorage).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -DeadLetterEndpoint "$storageid/blobServices/default/containers/$containername"
```

配信不能処理を無効にするには、コマンドを再実行してイベント サブスクリプションを作成しますが、`DeadLetterEndpoint` の値は指定しないでください。 イベント サブスクリプションを削除する必要はありません。

> [!NOTE]
> ローカル マシンで Azure PowerShell を使用する場合は、Azure PowerShell バージョン 1.1.0 以降を使用してください。 [Azure のダウンロード](https://azure.microsoft.com/downloads/) サイトから最新の Azure PowerShell をダウンロードしてインストールしてください。

## <a name="set-retry-policy"></a>再試行ポリシーの設定

Event Grid サブスクリプションの作成時には、Event Grid がイベント配信を試行する期間の値を設定できます。 既定では、Event Grid は、24 時間 (1440 分)、または 30 回の試行を行います。 Event Grid サブスクリプションには、これらの値のいずれかを設定できます。 イベントの有効期限の値は 1 から 1440 までの整数にする必要があります。 最大再試行回数は 1 から 30 までの整数にする必要があります。

[再試行のスケジュール](delivery-and-retry.md#retry-schedule-and-duration)を構成することはできません。

### <a name="azure-cli"></a>Azure CLI

イベントの Time to Live を 1440 分以外の値に設定するには、次のコマンドを使用します。

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --event-ttl 720
```

最大再試行回数を 30 以外の値に設定するには、次のコマンドを使用します。

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --max-delivery-attempts 18
```

`event-ttl` と `max-deliver-attempts` の両方を設定した場合、Event Grid は期限切れについて最初の設定を使用して、イベント配信を停止するタイミングを判別します。

### <a name="powershell"></a>PowerShell

イベントの Time to Live を 1440 分以外の値に設定するには、次のコマンドを使用します。

```azurepowershell-interactive
$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -EventTtl 720
```

最大再試行回数を 30 以外の値に設定するには、次のコマンドを使用します。

```azurepowershell-interactive
$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -MaxDeliveryAttempt 18
```

`EventTtl` と `MaxDeliveryAttempt` の両方を設定した場合、Event Grid は期限切れについて最初の設定を使用して、イベント配信を停止するタイミングを判別します。

## <a name="next-steps"></a>次の手順

* Azure 関数アプリを利用して配信不能イベントを処理するサンプル アプリケーションについては、「[Azure Event Grid Dead Letter Samples for .NET](https://azure.microsoft.com/resources/samples/event-grid-dotnet-handle-deadlettered-events/)」 (.NET 向け Azure Event Grid 配信不能サンプル) を参照してください。
* イベント配信と再試行については、「[Event Grid によるメッセージ配信と再試行](delivery-and-retry.md)」をご覧ください。
* Event Grid の概要については、[Event Grid の紹介](overview.md)に関する記事を参照してください。
* Event Grid の使用をすぐに開始するには、[Azure Event Grid でのカスタム イベントの作成とルーティング](custom-event-quickstart.md)に関する記事を参照してください。
