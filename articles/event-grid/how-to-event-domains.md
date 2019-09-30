---
title: イベント ドメインを使用して Azure Event Grid で大規模なトピック セットを管理する
description: イベント ドメインを使用して、Azure Event Grid で大規模なトピック セットを管理し、それらにイベントを発行する方法を説明します。
services: event-grid
author: banisadr
ms.service: event-grid
ms.author: babanisa
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: 9d7cef35ef6d1138b037f7c520f21bee86567aa8
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/10/2019
ms.locfileid: "70842567"
---
# <a name="manage-topics-and-publish-events-using-event-domains"></a>イベント ドメインを使用してトピックを管理し、イベントを発行する

この記事では、次の方法について説明します。

* Event Grid ドメインを作成する
* Event Grid トピックをサブスクライブする
* キーのリスト
* ドメインにイベントを発行する

イベント ドメインについて学習するには、「[Event Grid トピックを管理するためのイベント ドメインについて](event-domains.md)」をご覧ください。

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

## <a name="install-preview-feature"></a>プレビュー機能のインストール

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="create-an-event-domain"></a>イベント ドメインを作成する

大規模なトピック セットを管理するには、イベント ドメインを作成します。

# <a name="azure-clitabazurecli"></a>[Azure CLI](#tab/azurecli)

```azurecli-interactive
# If you haven't already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid domain create \
  -g <my-resource-group> \
  --name <my-domain-name> \
  -l <location>
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
```azurepowershell-interactive
# If you have not already installed the module, do it now.
# This module is required for preview features.
Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery

New-AzureRmEventGridDomain `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain-name> `
  -Location <location>
```
---

正常に作成された場合、次の値が返されます。

```json
{
  "endpoint": "https://<my-domain-name>.westus2-1.eventgrid.azure.net/api/events",
  "id": "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>",
  "inputSchema": "EventGridSchema",
  "inputSchemaMapping": null,
  "location": "westus2",
  "name": "<my-domain-name>",
  "provisioningState": "Succeeded",
  "resourceGroup": "<my-resource-group>",
  "tags": null,
  "type": "Microsoft.EventGrid/domains"
}
```

ドメインを管理してイベントを発行するために必要なので、`endpoint` と `id` を書き留めておきます。

## <a name="manage-access-to-topics"></a>トピックへのアクセスを管理する

トピックへのアクセスの管理は、[ロールの割り当て](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)を使用して行われます。 ロールの割り当てではロールベースのアクセス制御を使用して、Azure リソースに対する操作を、特定のスコープの承認されたユーザーに制限します。

Event Grid には、ドメイン内のさまざまなトピックへのアクセスを特定のユーザーに割り当てるために使用できる 2 つの組み込みロールがあります。 これらのロールは、サブスクリプションの作成と削除を許可する `EventGrid EventSubscription Contributor (Preview)` と、イベント サブスクリプションのリストのみを許可する `EventGrid EventSubscription Reader (Preview)` です。

# <a name="azure-clitabazurecli"></a>[Azure CLI](#tab/azurecli)
次の Azure CLI コマンドでは、`alice@contoso.com` によるイベント サブスクリプションの作成と削除を、トピック `demotopic1` のみに制限します。

```azurecli-interactive
az role assignment create \
  --assignee alice@contoso.com \
  --role "EventGrid EventSubscription Contributor (Preview)" \
  --scope /subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
次の PowerShell コマンドでは、`alice@contoso.com` によるイベント サブスクリプションの作成と削除を、トピック `demotopic1` のみに制限します。

```azurepowershell-interactive
New-AzureRmRoleAssignment `
  -SignInName alice@contoso.com `
  -RoleDefinitionName "EventGrid EventSubscription Contributor (Preview)" `
  -Scope /subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1
```
---

Event Grid の操作に対するアクセスの管理について詳しくは、「[Event Grid のセキュリティと認証](./security-authentication.md)」をご覧ください。

## <a name="create-topics-and-subscriptions"></a>トピックとサブスクリプションを作成する

Event Grid サービスでは、ドメイン トピックのイベント サブスクリプションを作成するための呼び出しに基づいて、ドメインで対応するトピックが自動的に作成および管理されます。 ドメインでトピックを作成する別の手順はありません。 同様に、トピックの最後のイベント サブスクリプションが削除されたときに、トピックも削除されます。

ドメインのトピックをサブスクライブすることは、他のすべての Azure リソースをサブスクライブするのと同じです。 ソース リソース ID としては、前にドメインを作成するときに返されたイベント ドメイン ID を指定します。 サブスクライブするトピックを指定するには、ソース リソース ID の末尾に `/topics/<my-topic>` を追加します。 ドメイン内のすべてのイベントを受け取るドメイン スコープのイベント サブスクリプションを作成するには、トピックを指定せずに、イベント ドメイン ID を指定します。

通常、前のセクションでアクセスを許可されたユーザーが、サブスクリプションを作成します。 この記事では、簡単にするため自分でサブスクリプションを作成します。 

# <a name="azure-clitabazurecli"></a>[Azure CLI](#tab/azurecli)

```azurecli-interactive
az eventgrid event-subscription create \
  --name <event-subscription> \
  --source-resource-id "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1" \
  --endpoint https://contoso.azurewebsites.net/api/updates
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

```azurepowershell-interactive
New-AzureRmEventGridSubscription `
  -ResourceId "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1" `
  -EventSubscriptionName <event-subscription> `
  -Endpoint https://contoso.azurewebsites.net/api/updates
```

---

イベントをサブスクライブするテスト エンドポイントが必要な場合は、受信イベントを表示する[ビルド済みの Web アプリ](https://github.com/Azure-Samples/azure-event-grid-viewer)をいつでもデプロイすることができます。 `https://<your-site-name>.azurewebsites.net/api/updates` のテスト Web サイトにイベントを送信できます。

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

トピックに設定されているアクセス許可は Azure Active Directory で格納され、明示的に削除する必要があります。 ユーザーにトピックへの書き込みアクセス権限がある場合、イベント サブスクリプションを削除しても、イベント サブスクリプションを作成するためのアクセスは取り消されません。


## <a name="publish-events-to-an-event-grid-domain"></a>Event Grid ドメインにイベントを発行する

ドメインにイベントを発行することは、[カスタム トピックに発行する](./post-to-custom-topic.md)ことと同じです。 ただし、カスタム トピックに発行するのではなく、すべてのイベントをドメイン エンドポイントに発行します。 JSON イベント データでは、イベントの送信先のトピックを指定します。 次のイベントの配列では、`"id": "1111"` のイベントが `demotopic1` に送信され、`"id": "2222"` のイベントはトピック `demotopic2` に送信されます。

```json
[{
  "topic": "demotopic1",
  "id": "1111",
  "eventType": "maintenanceRequested",
  "subject": "myapp/vehicles/diggers",
  "eventTime": "2018-10-30T21:03:07+00:00",
  "data": {
    "make": "Contoso",
    "model": "Small Digger"
  },
  "dataVersion": "1.0"
},
{
  "topic": "demotopic2",
  "id": "2222",
  "eventType": "maintenanceCompleted",
  "subject": "myapp/vehicles/tractors",
  "eventTime": "2018-10-30T21:04:12+00:00",
  "data": {
    "make": "Contoso",
    "model": "Big Tractor"
  },
  "dataVersion": "1.0"
}]
```

# <a name="azure-clitabazurecli"></a>[Azure CLI](#tab/azurecli)
Azure CLI を使用してドメイン エンドポイントを取得するには、以下を使用します。

```azurecli-interactive
az eventgrid domain show \
  -g <my-resource-group> \
  -n <my-domain>
```

ドメインのキーを取得するには、以下を使用します。

```azurecli-interactive
az eventgrid domain key list \
  -g <my-resource-group> \
  -n <my-domain>
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
PowerShell を使用してドメイン エンドポイントを取得するには、以下を使用します。

```azurepowershell-interactive
Get-AzureRmEventGridDomain `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain>
```

ドメインのキーを取得するには、以下を使用します。

```azurepowershell-interactive
Get-AzureRmEventGridDomainKey `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain>
```
---

次に、HTTP POST を実行するための任意のメソッドを使用して、Event Grid ドメインにイベントを発行します。

## <a name="next-steps"></a>次の手順

* イベント ドメインの概念の概要と役立つ理由の詳細については、[イベント ドメインの概念の概要](event-domains.md)に関するページを参照してください。
