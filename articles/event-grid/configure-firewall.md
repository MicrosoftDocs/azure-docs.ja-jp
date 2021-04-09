---
title: Azure Event Grid のトピックまたはドメインに対して IP ファイアウォールを構成する
description: この記事では Event Grid のトピックまたはドメインに対してファイアウォール設定を構成する方法について説明します。
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 178b9d84ea8b2e0f764f7584526db8dbcf5284f3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102031839"
---
# <a name="configure-ip-firewall-for-azure-event-grid-topics-or-domains"></a>Azure Event Grid のトピックまたはドメインに対して IP ファイアウォールを構成する 
既定では、要求が有効な認証と承認を受けている限り、トピックおよびドメインにはインターネットからアクセスできます。 IP ファイアウォールを使用すると、[CIDR (クラスレス ドメイン間ルーティング)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) 表記の一連の IPv4 アドレスまたは IPv4 アドレス範囲のみにアクセスを制限できます。 他の IP アドレスから発信するパブリッシャーは拒否され、403 (禁止) の応答が返されます。 Event Grid でサポートされるネットワーク セキュリティ機能の詳細については、[Event Grid のネットワーク セキュリティ](network-security.md)に関する記事を参照してください。

この記事では Azure Event Grid のトピックまたはドメインに対して IP ファイアウォール設定を構成する方法について説明します。

## <a name="use-azure-portal"></a>Azure Portal の使用
このセクションでは、Azure portal を使用して受信 IP ファイアウォール規則を作成する方法について説明します。 このセクションで示す手順は、トピックに関するものです。 同様の手順を使用して、**ドメイン** の受信 IP 規則を作成できます。 

1. [Azure portal](https://portal.azure.com) 内で、イベント グリッドのトピックまたはドメインに移動し、 **[ネットワーク]** タブに切り替えます。
2. **[パブリック ネットワーク]** を選択して、インターネットを含むすべてのネットワークがリソースにアクセスできるようにします。 

    IP ベースのファイアウォール規則を使用して、トラフィックを制限できます。 クラスレス ドメイン間ルーティング (CIDR) 表記で、単一の IPv4 アドレスまたは IP アドレスの範囲を指定します。 

    ![[Public networks]\(パブリック ネットワーク\) が選択されている [Public network access]\(パブリック ネットワーク アクセス\) ページを示すスクリーンショット。](./media/configure-firewall/public-networks-page.png)
3. プライベート エンドポイント接続のみにこのリソースへのアクセスを許可するには、 **[Private endpoints only]\(プライベート エンドポイントのみ\)** を選択します。 このページの **[プライベート エンドポイント接続]** タブを使用して、接続を管理します。 

    ![[パブリック ネットワーク] ページ](./media/configure-firewall/private-endpoints-page.png)
4. ツールバーの **[保存]** を選択します。 



## <a name="use-azure-cli"></a>Azure CLI の使用
このセクションでは、Azure CLI コマンドを使用して受信 IP 規則を含むトピックを作成する方法について説明します。 このセクションで示す手順は、トピックに関するものです。 同様の手順を使用して、**ドメイン** の受信 IP 規則を作成できます。 


### <a name="enable-or-disable-public-network-access"></a>パブリック ネットワーク アクセスを有効または無効にする
既定では、トピックとドメインに対してパブリック ネットワーク アクセスが有効になっています。 また、明示的に有効にすることも、無効にすることもできます。 受信 IP ファイアウォール規則を構成することで、トラフィックを制限できます。 

#### <a name="enable-public-network-access-while-creating-a-topic"></a>トピックの作成中にパブリック ネットワーク アクセスを有効にする

```azurecli-interactive
az eventgrid topic create \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location \
    --public-network-access enabled
```


#### <a name="disable-public-network-access-while-creating-a-topic"></a>トピックの作成中にパブリック ネットワーク アクセスを無効にする

```azurecli-interactive
az eventgrid topic create \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location \
    --public-network-access disabled
```

> [!NOTE]
> トピックまたはドメインに対してパブリック ネットワーク アクセスが無効になっている場合、パブリック インターネット経由のトラフィックは許可されません。 プライベート エンドポイント接続のみが、これらのリソースへのアクセスを許可されます。 


#### <a name="enable-public-network-access-for-an-existing-topic"></a>既存のトピックに対してパブリック ネットワーク アクセスを有効にする

```azurecli-interactive
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access enabled 
```

#### <a name="disable-public-network-access-for-an-existing-topic"></a>既存のトピックに対してパブリック ネットワーク アクセスを無効にする 

```azurecli-interactive
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access disabled
```

### <a name="create-a-topic-with-single-inbound-ip-rule"></a>単一の受信 ip 規則を含むトピックを作成する
次の CLI コマンド例では、受信 IP 規則を含むイベント グリッド トピックを作成します。 

```azurecli-interactive
az eventgrid topic create \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location \
    --public-network-access enabled \
    --inbound-ip-rules <IP ADDR or CIDR MASK> allow 
```

### <a name="create-a-topic-with-multiple-inbound-ip-rules"></a>複数の受信 ip 規則を含むトピックを作成する

次の CLI コマンド例では、2 つの受信 IP 規則を含むイベント グリッド トピックを 1 ステップで作成します。 

```azurecli-interactive
az eventgrid topic create \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location \
    --public-network-access enabled \
    --inbound-ip-rules <IP ADDR 1 or CIDR MASK 1> allow \
    --inbound-ip-rules <IP ADDR 2 or CIDR MASK 2> allow
```

### <a name="update-an-existing-topic-to-add-inbound-ip-rules"></a>既存のトピックを更新して受信 IP 規則を追加する
この例では、まずイベント グリッド トピックを作成し、次に別のコマンドでそのトピックの受信 IP 規則を追加します。 また、2 番目のコマンドで設定された受信 IP 規則も更新されます。 

```azurecli-interactive

# create the event grid topic first
az eventgrid topic create \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location

# add inbound IP rules to an existing topic
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access enabled \
    --inbound-ip-rules <IP ADDR or CIDR MASK> allow

# later, update topic with additional ip rules
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access enabled \
    --inbound-ip-rules <IP ADDR 1 or CIDR MASK 1> allow \
    --inbound-ip-rules <IP ADDR 2 or CIDR MASK 2> allow
```

### <a name="remove-an-inbound-ip-rule"></a>受信 IP 規則を削除する
次のコマンドでは、設定を更新する際に 1 番目の規則のみを指定することで、前の手順で作成した 2 番目の規則を削除します。 

```azurecli-interactive
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access enabled \
    --inbound-ip-rules <IP ADDR 1 or CIDR MASK 1> allow
```


## <a name="use-powershell"></a>PowerShell の使用
このセクションでは、Azure PowerShell コマンドを使用して、受信 IP ファイアウォール規則を含む Azure Event Grid トピックを作成する方法について説明します。 このセクションで示す手順は、トピックに関するものです。 同様の手順を使用して、**ドメイン** の受信 IP 規則を作成できます。 

### <a name="prerequisites"></a>前提条件
「[方法:リソースにアクセスできる Azure AD アプリケーションとサービス プリンシパルをポータルで作成する](../active-directory/develop/howto-create-service-principal-portal.md)」の手順に従って、Azure Active Directory アプリケーションを作成し、以下の値を書き留めます。

- ディレクトリ (テナント) ID
- アプリケーション (クライアント) ID
- アプリケーション (クライアント) シークレット

### <a name="prepare-token-and-headers-for-rest-api-calls"></a>REST API 呼び出し用のトークンとヘッダーを準備する 
次の前提条件コマンドを実行して、REST API 呼び出しで使用する認証トークンを取得し、Authorization やその他のヘッダー情報を設定します。 

```azurepowershell-interactive
# replace <CLIENT ID> and <CLIENT SECRET>
$body = "grant_type=client_credentials&client_id=<CLIENT ID>&client_secret=<CLIENT SECRET>&resource=https://management.core.windows.net"

# get the authentication token. Replace <TENANT ID>
$Token = Invoke-RestMethod -Method Post `
    -Uri https://login.microsoftonline.com/<TENANT ID>/oauth2/token `
    -Body $body `
    -ContentType 'application/x-www-form-urlencoded'

# set authorization and content-type headers
$Headers = @{}
$Headers.Add("Authorization","$($Token.token_type) "+ " " + "$($Token.access_token)")
$Headers.Add("Content-Type","application/json")
```

### <a name="enable-public-network-access-for-an-existing-topic"></a>既存のトピックに対してパブリック ネットワーク アクセスを有効にする
既定では、トピックとドメインに対してパブリック ネットワーク アクセスが有効になっています。 受信 IP ファイアウォール規則を構成することで、トラフィックを制限できます。 

```azurepowershell-interactive
$body = @{"properties"=@{"publicNetworkAccess"="enabled"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="disable-public-network-access-for-an-existing-topic"></a>既存のトピックに対してパブリック ネットワーク アクセスを無効にする
トピックまたはドメインに対してパブリック ネットワーク アクセスが無効になっている場合、パブリック インターネット経由のトラフィックは許可されません。 プライベート エンドポイント接続のみが、これらのリソースへのアクセスを許可されます。 

```azurepowershell-interactive
$body = @{"properties"=@{"publicNetworkAccess"="disabled"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="create-an-event-grid-topic-with-inbound-rules-in-one-step"></a>受信規則を含むイベント グリッド トピックを 1 ステップで作成する

```azurepowershell-interactive

# prepare the body for the REST PUT method. Notice that inbound IP rules are included. 
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="basic"}; "properties"=@{"publicNetworkAccess"="enabled"; "inboundIpRules"=@(@{"ipmask"="<IP ADDR or CIDR MASK>";"action"="allow"})}} | ConvertTo-Json -Depth 5

# create the event grid topic with inbound IP rules
Invoke-RestMethod -Method 'Put' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01" `
    -Headers $Headers `
    -Body $body

# verify that the topic was created
Invoke-RestMethod -Method 'Get' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01" `
    -Headers $Headers `
    | ConvertTo-Json -Depth 5
```


### <a name="create-event-grid-topic-first-and-then-add-inbound-ip-rules"></a>まずイベント グリッド トピックを作成し、次に受信 IP 規則を追加する

```azurepowershell-interactive

# prepare the body for the REST PUT method. Notice that no inbound IP rules are specified. 
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="basic"}; "properties"=@{"publicNetworkAccess"="enabled";}} | ConvertTo-Json -Depth 5

# create the Event Grid topic
Invoke-RestMethod -Method 'Put' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01" `
    -Headers $Headers `
    -Body $body`

# verify that the topic was created
Invoke-RestMethod -Method 'Get' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01" `
    -Headers $Headers `
    | ConvertTo-Json -Depth 5

# prepare the body for REST PUT method. Notice that it includes inbound IP rules now. This feature is available in both basic and premium tiers.
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="basic"}; "properties"=@{"publicNetworkAccess"="enabled"; "inboundIpRules"=@(@{"ipmask"="<IP ADDR or CIDR MASK>";"action"="allow"}, @{"ipmask"="<IP ADDR or CIDR MASK>";"action"="allow"})}} | ConvertTo-Json -Depth 5

# update the topic with inbound IP rules
Invoke-RestMethod -Method 'Put' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01" `
    -Headers $Headers `
    -Body $body

# verify that the topic was updated
Invoke-RestMethod -Method 'Get' 
    -Uri "https://management.azure.com/subscriptions/<AzURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01" `
    -Headers $Headers `
    | ConvertTo-Json -Depth 5

```

## <a name="next-steps"></a>次のステップ

* イベント配信の監視について詳しくは、「[Event Grid メッセージ配信の監視](monitor-event-delivery.md)」をご覧ください。
* 認証キーについて詳しくは、「[Event Grid のセキュリティと認証](security-authentication.md)」をご覧ください。
* Azure Event Grid サブスクリプションの作成の詳細については、[Event Grid サブスクリプション スキーマ](subscription-creation-schema.md)に関する記事を参照してください。
* ネットワーク接続の問題をトラブルシューティングするには、[ネットワーク接続の問題のトラブルシューティング](troubleshoot-network-connectivity.md)に関するページを参照してください。
