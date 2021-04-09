---
title: Azure Event Grid サブスクリプションのクエリを実行する
description: この記事では、Azure サブスクリプション内の Event Grid サブスクリプションの一覧を表示する方法を説明します。 サブスクリプションの種類に基づいて、異なるパラメーターを指定します。
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 3d700f543bc5e3c7add2a346c10acf975e1c2462
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86120451"
---
# <a name="query-event-grid-subscriptions"></a>Event Grid サブスクリプションのクエリを実行する 

この記事では、Azure サブスクリプション内の Event Grid サブスクリプションの一覧を表示する方法を説明します。 既存の Event Grid サブスクリプションのクエリを実行するときは、サブスクリプションの種類を理解しておく必要があります。 取得するサブスクリプションの種類に基づいて、異なるパラメーターを指定します。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="resource-groups-and-azure-subscriptions"></a>リソース グループと Azure サブスクリプション

Azure サブスクリプションとリソース グループは、Azure リソースではありません。 そのため、リソース グループまたは Azure サブスクリプションに対する Event Grid サブスクリプションには、Azure リソースに対する Event Grid サブスクリプションと同じプロパティはありません。 リソース グループまたは Azure サブスクリプションに対する Event Grid サブスクリプションは、グローバルと見なされます。

Azure サブスクリプションとそのリソース グループに対する Event Grid サブスクリプションを取得するには、どのようなパラメーターも指定する必要はありません。 クエリを実行する Azure サブスクリプションを選択したことを確認してください。 次の例では、カスタム トピックまたは Azure リソースに対する Event Grid サブスクリプションは取得しません。

Azure CLI では、次を使用します。

```azurecli-interactive
az account set -s "My Azure Subscription"
az eventgrid event-subscription list
```

PowerShell では、次を使用します。

```azurepowershell-interactive
Set-AzContext -Subscription "My Azure Subscription"
Get-AzEventGridSubscription
```

Azure サブスクリプションに対する Event Grid サブスクリプションを取得するには、トピックの種類 **Microsoft.Resources.Subscriptions** を指定します。

Azure CLI では、次を使用します。

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.Resources.Subscriptions" --location global
```

PowerShell では、次を使用します。

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.Resources.Subscriptions"
```

Azure サブスクリプション内のすべてのリソース グループに対する Event Grid サブスクリプションを取得するには、トピックの種類 **Microsoft.Resources.ResourceGroups** を指定します。

Azure CLI では、次を使用します。

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.Resources.ResourceGroups" --location global
```

PowerShell では、次を使用します。

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.Resources.ResourceGroups"
```

指定したリソース グループに対する Event Grid サブスクリプションを取得するには、パラメーターとしてリソース グループの名前を指定します。

Azure CLI では、次を使用します。

```azurecli-interactive
az eventgrid event-subscription list --resource-group myResourceGroup --location global
```

PowerShell では、次を使用します。

```azurepowershell-interactive
Get-AzEventGridSubscription -ResourceGroupName myResourceGroup
```

## <a name="custom-topics-and-azure-resources"></a>カスタム トピックと Azure リソース

Event Grid のカスタム トピックは Azure リソースです。 そのため、カスタム トピックと、Blob Storage アカウントなどの他のリソースでは、Event Grid サブスクリプションのクエリを実行する方法は同じです。 カスタム トピックに対する Event Grid サブスクリプションを取得するには、リソースまたはリソースの場所を示すパラメーターを指定する必要があります。 Azure サブスクリプション全体のリソースに対して Event Grid サブスクリプションのクエリをグローバルに実行することはできません。

1 つの場所のカスタム トピックと他のリソースに対する Event Grid サブスクリプションを取得するには、場所の名前を指定します。

Azure CLI では、次を使用します。

```azurecli-interactive
az eventgrid event-subscription list --location westus2
```

PowerShell では、次を使用します。

```azurepowershell-interactive
Get-AzEventGridSubscription -Location westus2
```

ある場所のカスタム トピックに対するサブスクリプションを取得するには、場所とトピックの種類 **Microsoft.EventGrid.Topics** を指定します。

Azure CLI では、次を使用します。

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.EventGrid.Topics" --location "westus2"
```

PowerShell では、次を使用します。

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.EventGrid.Topics" -Location westus2
```

ある場所のストレージ アカウントに対するサブスクリプションを取得するには、場所とトピックの種類 **Microsoft.Storage.StorageAccounts** を指定します。

Azure CLI では、次を使用します。

```azurecli-interactive
az eventgrid event-subscription list --topic-type "Microsoft.Storage.StorageAccounts" --location westus2
```

PowerShell では、次を使用します。

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.Storage.StorageAccounts" -Location westus2
```

カスタム トピックに対する Event Grid サブスクリプションを取得するには、カスタム トピックの名前とそのリソース グループの名前を指定します。

Azure CLI では、次を使用します。

```azurecli-interactive
az eventgrid event-subscription list --topic-name myCustomTopic --resource-group myResourceGroup
```

PowerShell では、次を使用します。

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicName myCustomTopic -ResourceGroupName myResourceGroup
```

特定のリソースの Event Grid サブスクリプションを取得するには、リソース ID を指定します。

Azure CLI では、次を使用します。

```azurecli-interactive
resourceid=$(az resource show -n mystorage -g myResourceGroup --resource-type "Microsoft.Storage/storageaccounts" --query id --output tsv)
az eventgrid event-subscription list --resource-id $resourceid
```

PowerShell では、次を使用します。

```azurepowershell-interactive
$resourceid = (Get-AzResource -Name mystorage -ResourceGroupName myResourceGroup).ResourceId
Get-AzEventGridSubscription -ResourceId $resourceid
```

## <a name="next-steps"></a>次のステップ

* イベント配信と再試行については、「[Event Grid によるメッセージ配信と再試行](delivery-and-retry.md)」をご覧ください。
* Event Grid の概要については、[Event Grid の紹介](overview.md)に関する記事を参照してください。
* Event Grid の使用をすぐに開始するには、[Azure Event Grid でのカスタム イベントの作成とルーティング](custom-event-quickstart.md)に関する記事を参照してください。
