---
title: Event Grid リソースのアクセス キーを取得する
description: この記事では、Event Grid トピックまたはドメインのアクセス キーを取得する方法を説明します
ms.topic: how-to
ms.date: 07/07/2020
ms.openlocfilehash: a642affbac79766684dc75a37dae0373450d20e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98632531"
---
# <a name="get-access-keys-for-event-grid-resources-topics-or-domains"></a>Event Grid リソース (トピックまたはドメイン) のアクセス キーを取得する
アクセス キーは、Azure Event Grid リソース (トピックおよびドメイン) に対してイベントを発行するアプリケーションを認証するために使用されます。 キーを定期的に再生成し、安全に格納することをお勧めします。 アクセス キーは 2 つ提供されるため、1 つのキーを使用して接続を保ったまま、もう 1 つのキーを再生成できます。

この記事では、Azure portal、PowerShell、または CLI を使用して、Event Grid リソース (トピックまたはドメイン) のアクセス キーを取得する方法を説明します。 

## <a name="azure-portal"></a>Azure portal
Azure portal で、自分のトピックまたはドメインの **[Event Grid トピック]** または **[Event Grid ドメイン]** ページの **[アクセス キー]** タブに切り替えます。  

:::image type="content" source="./media/get-access-keys/azure-portal.png" alt-text="[アクセス キー] ページ":::

## <a name="azure-powershell"></a>Azure PowerShell
[Get-AzEventGridTopicKey](/powershell/module/az.eventgrid/get-azeventgridtopickey) コマンドを使用して、トピックのアクセス キーを取得します。 

```azurepowershell-interactive
Get-AzEventGridTopicKey -ResourceGroup <RESOURCE GROUP NAME> -Name <TOPIC NAME>
```

[Get-AzEventGridDomainKey](/powershell/module/az.eventgrid/get-azeventgriddomainkey) コマンドを使用して、ドメインのアクセス キーを取得します。 

```azurepowershell-interactive
Get-AzEventGridDomainKey -ResourceGroup <RESOURCE GROUP NAME> -Name <DOMAIN NAME>
```

## <a name="azure-cli"></a>Azure CLI
[az eventgrid topic key list](/cli/azure/eventgrid/topic/key#az-eventgrid-topic-key-list) を使用して、トピックのアクセス キーを取得します。 

```azurecli-interactive
az eventgrid topic key list --resource-group <RESOURCE GROUP NAME> --name <TOPIC NAME>
```

[az eventgrid domain key list](/cli/azure/eventgrid/domain/key#az-eventgrid-domain-key-list) を使用して、ドメインのアクセス キーを取得します。 

```azurecli-interactive
az eventgrid domain key list --resource-group <RESOURCE GROUP NAME> --name <DOMAIN NAME>
```

## <a name="next-steps"></a>次のステップ
次の記事を参照してください: [発行クライアントの認証](security-authenticate-publishing-clients.md)。 
