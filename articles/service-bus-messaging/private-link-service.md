---
title: Azure Service Bus を Azure Private Link サービスと統合する
description: Azure Service Bus を Azure Private Link サービスと統合する方法を説明します
author: spelluru
ms.author: spelluru
ms.date: 10/07/2020
ms.topic: article
ms.openlocfilehash: 66de9a4ff65c73264257cb6f7f215fc15820c95f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "94427149"
---
# <a name="allow-access-to-azure-service-bus-namespaces-via-private-endpoints"></a>プライベート エンドポイント経由での Azure Service Bus 名前空間へのアクセスを許可する
Azure Private Link サービスを使用すると、自分の仮想ネットワーク内の **プライベート エンドポイント** 経由で、Azure サービス (Azure Service Bus、Azure Storage、Azure Cosmos DB など) と、Azure でホストされている顧客またはパートナー サービスにアクセスできます。

> [!IMPORTANT]
> この機能は、Azure Service Bus の **Premium** レベルでサポートされています。 Premium レベルの詳細については、「[Service Bus の Premium および Standard メッセージング レベル](service-bus-premium-messaging.md)」の記事を参照してください。

プライベート エンドポイントとは、Azure Private Link を使用するサービスにプライベートかつ安全に接続するネットワーク インターフェイスです。 プライベート エンドポイントは、ご自分の VNet からのプライベート IP アドレスを使用して、サービスを実質的に VNet に取り込みます。 サービスへのすべてのトラフィックをプライベート エンドポイント経由でルーティングできるため、ゲートウェイ、NAT デバイス、ExpressRoute または VPN 接続、パブリック IP アドレスは必要ありません。 仮想ネットワークとサービスの間のトラフィックは、Microsoft のバックボーン ネットワークを経由して、パブリック インターネットからの公開を排除します。 最高レベルの細分性でアクセスを制御しながら Azure リソースのインスタンスに接続できます。

詳細については、「[Azure Private Link とは](../private-link/private-link-overview.md)」を参照してください。

>[!WARNING]
> プライベート エンドポイントを実装すると、他の Azure サービスが Service Bus と対話するのを禁止できます。 例外として、プライベート エンドポイントが有効になっている場合でも、特定の信頼できるサービスからの Service Bus リソースへのアクセスを許可できます。 信頼できるサービスの一覧については、[信頼できるサービス](#trusted-microsoft-services)に関するセクションを参照してください。
>
> 仮想ネットワーク上には、次の Microsoft サービスが必要です
> - Azure App Service
> - Azure Functions



## <a name="add-a-private-endpoint-using-azure-portal"></a>Azure portal を使用してプライベート エンドポイントを追加する

### <a name="prerequisites"></a>前提条件

Service Bus 名前空間を Azure Private Link と統合するには、次のエンティティまたはアクセス許可が必要です。

- Service Bus 名前空間。
- Azure 仮想ネットワーク。
- 仮想ネットワーク内のサブネット。 **既定** のサブネットを使用できます。 
- Service Bus 名前空間と仮想ネットワークの両方に対する所有者または共同作成者のアクセス許可。

プライベート エンドポイントと仮想ネットワークは、同じリージョンに存在する必要があります。 ポータルを使用してプライベート エンドポイントのリージョンを選択すると、自動的にフィルター処理が行われ、そのリージョン内にある仮想ネットワークのみが表示されます。 ご利用の Service Bus 名前空間は、別のリージョンに配置することができます。 さらに、ご利用のプライベート エンドポイントでは、自分の仮想ネットワーク内のプライベート IP アドレスが使用されます。

### <a name="steps"></a>steps

既存の名前空間が既にある場合は、次の手順に従ってプライベート エンドポイントを作成できます。

1. [Azure portal](https://portal.azure.com) にサインインします。 
2. 検索バーで、「**Service Bus**」と入力します。
3. プライベート エンドポイントを追加する **名前空間** を一覧から選択します。
2. 左側のメニューで、 **[設定]** の下にある **[ネットワーク]** オプションを選択します。 

    > [!NOTE]
    > **[ネットワーク]** タブは **premium** 名前空間に対してのみ表示されます。  
    
    既定では、 **[選択されたネットワーク]** オプションが選択されています。 このページで 1 つ以上の IP ファイアウォール規則または仮想ネットワークを追加しない場合は、パブリック インターネット経由で (アクセス キーを使用して) 名前空間にアクセスできます。

    :::image type="content" source="./media/service-bus-ip-filtering/default-networking-page.png" alt-text="[ネットワーク] ページ - 既定" lightbox="./media/service-bus-ip-filtering/default-networking-page.png":::
    
    **[すべてのネットワーク]** オプションを選択した場合、Service Bus 名前空間はあらゆる IP アドレスからの (アクセス キーを使用した) 接続を受け入れます。 この既定の設定は、IP アドレス範囲 0.0.0.0/0 を受け入れる規則と同じです。 

    ![ファイアウォールで [すべてのネットワーク] のオプションが選択されている](./media/service-bus-ip-filtering/firewall-all-networks-selected.png)
5. プライベート エンドポイント経由での名前空間へのアクセスを許可するには、ページの上部にある **[プライベート エンドポイント接続]** タブを選択します
6. ページの上部にある **[+ プライベート エンドポイント]** ボタンを選択します。

    ![[プライベート エンドポイントの追加] ボタン](./media/private-link-service/private-link-service-3.png)
7. **[基本]** ページで、次の手順を行います。 
    1. プライベート エンドポイントを作成する **Azure サブスクリプション** を選択します。 
    2. プライベート エンドポイント リソース用の **リソース グループ** を選択します。
    3. プライベート エンドポイントの **名前** を入力します。 
    5. プライベート エンドポイントの **リージョン** を選択します。 プライベート エンドポイントが存在するリージョンは仮想ネットワークと同じでなければなりませんが、接続しようとしているプライベート リンク リソースのリージョンとは異なっていても構いません。 
    6. **Next:次へ: リソース >** ボタンがページの下部にあるのでクリックします。

        ![[プライベート エンドポイントの作成 - 基本] ページ](./media/private-link-service/create-private-endpoint-basics-page.png)
8. **[リソース]** ページで、次の手順を行います。
    1. 接続方法について、 **[マイ ディレクトリ内の Azure リソースに接続します]** を選択した場合は、次の手順に従います。   
        1. ご自分の **Service Bus 名前空間** が存在する **Azure サブスクリプション** を選択します。 
        2. **リソースの種類** については、 **[リソースの種類]** で **[Microsoft.ServiceBus/namespaces]** を選択します。
        3. **[リソース]** については、ドロップダウン リストから Service Bus 名前空間を選択します。 
        4. **[ターゲット サブリソース]** が **[名前空間]** に設定されていることを確認します。
        5. **Next:次へ: 構成 >** ボタンがページの下部にあるのでクリックします。 
        
            ![[プライベート エンドポイントの作成 - リソース] ページ](./media/private-link-service/create-private-endpoint-resource-page.png)
    2. **[リソース ID またはエイリアスを使って Azure リソースに接続します]** を選択した場合は、次の手順に従います。
        1. **リソース ID** または **別名** を入力します。 それは誰かが自分と共有しているリソース ID または別名とすることができます。 リソース ID を取得する最も簡単な方法は、Azure portal で Service Bus 名前空間に移動し、`/subscriptions/` から始まる URI の部分をコピーすることです。 例については、次の図を参照してください。 
        2. **[ターゲット サブリソース]** では、「**名前空間**」と入力します。 これは、ご自分のプライベート エンドポイントでアクセスできるサブリソースの種類です。 
        3. (省略可能) **要求メッセージ** を入力します。 このメッセージは、プライベート エンドポイント接続の管理中にリソース所有者に表示されます。 
        4. 次に、**Next:次へ: 構成 >** ボタンがページの下部にあるのでクリックします。 

            ![プライベート エンドポイントの作成 - リソース ID を使用した接続](./media/private-link-service/connect-resource-id.png)
9. **[構成]** ページで、プライベート エンドポイントのデプロイ先とする仮想ネットワーク内のサブネットを選択します。 
    1. **[仮想ネットワーク]** を選択します。 ドロップダウン リストには、現在選択されているサブスクリプションおよび場所内の仮想ネットワークのみが一覧表示されます。 
    2. 選択した仮想ネットワーク内の **サブネット** を選択します。 
    3. **Next:次へ: タグ >** ボタンがページの下部にあるので選択します。 

        ![[プライベート エンドポイントの作成 - 構成] ページ](./media/private-link-service/create-private-endpoint-configuration-page.png)
10. **[タグ]** ページでは、プライベート エンドポイント リソースに関連付ける任意のタグ (名前と値) を作成します。 次に、ページの下部にある **[確認と作成]** ボタンを選択します。 
11. **[確認と作成]** では、すべての設定を確認し、 **[作成]** を選択してプライベート エンドポイントを作成します。
    
    ![[プライベート エンドポイントの作成 - 確認と作成] ページ](./media/private-link-service/create-private-endpoint-review-create-page.png)
12. プライベート エンドポイントが作成されていることを確認します。 ご自分がリソースの所有者であり、 **[接続方法]** として **[マイ ディレクトリ内の Azure リソースに接続します]** を選択してある場合は、エンドポイント接続を **[自動承認]** にする必要があります。 それが **[保留中]** 状態にある場合は、「[Azure portal を使用してプライベート エンドポイントを管理する](#manage-private-endpoints-using-azure-portal)」セクションを参照してください。

    ![作成されたプライベート エンドポイント](./media/private-link-service/private-endpoint-created.png)

[!INCLUDE [service-bus-trusted-services](../../includes/service-bus-trusted-services.md)]

## <a name="add-a-private-endpoint-using-powershell"></a>PowerShell を使用してプライベート エンドポイントを追加する
次の例では、Azure PowerShell を使用して、Service Bus 名前空間へのプライベート エンドポイント接続を作成する方法を示します。

プライベート エンドポイントと仮想ネットワークは、同じリージョンに存在する必要があります。 ご利用の Service Bus 名前空間は、別のリージョンに配置することができます。 さらに、ご利用のプライベート エンドポイントでは、自分の仮想ネットワーク内のプライベート IP アドレスが使用されます。

```azurepowershell-interactive

$rgName = "<RESOURCE GROUP NAME>"
$vnetlocation = "<VNET LOCATION>"
$vnetName = "<VIRTUAL NETWORK NAME>"
$subnetName = "<SUBNET NAME>"
$namespaceLocation = "<NAMESPACE LOCATION>"
$namespaceName = "<NAMESPACE NAME>"
$peConnectionName = "<PRIVATE ENDPOINT CONNECTION NAME>"

# create resource group
az group create -l $vnetLocation -n $rgName

# create virtual network
$virtualNetwork = New-AzVirtualNetwork `
                    -ResourceGroupName $rgName `
                    -Location $vnetlocation `
                    -Name $vnetName `
                    -AddressPrefix 10.0.0.0/16

# create subnet with endpoint network policy disabled
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
                    -Name $subnetName `
                    -AddressPrefix 10.0.0.0/24 `
                    -PrivateEndpointNetworkPoliciesFlag "Disabled" `
                    -VirtualNetwork $virtualNetwork

# update virtual network
$virtualNetwork | Set-AzVirtualNetwork

# create premium service bus namespace
$namespaceResource = New-AzResource -Location $namespaceLocation -ResourceName $namespaceName -ResourceGroupName $rgName -Sku @{name = "Premium"; capacity = 1} -Properties @{} -ResourceType "Microsoft.ServiceBus/namespaces" -

# create a private link service connection
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
                                -Name $peConnectionName `
                                -PrivateLinkServiceId $namespaceResource.ResourceId `
                                -GroupId "namespace"

# get subnet object that you will use in the next step                                
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  $rgName -Name $vnetName
$subnet = $virtualNetwork | Select -ExpandProperty subnets `
                                | Where-Object  {$_.Name -eq $subnetName}  
   
# now, create private endpoint   
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $rgName  `
                                -Name $vnetName   `
                                -Location $vnetlocation `
                                -Subnet  $subnet   `
                                -PrivateLinkServiceConnection $privateEndpointConnection

(Get-AzResource -ResourceId $namespaceResource.ResourceId -ExpandProperties).Properties


```


## <a name="manage-private-endpoints-using-azure-portal"></a>Azure portal を使用してプライベート エンドポイントを管理する

プライベート エンドポイントを作成する際は、接続を承認する必要があります。 プライベート エンドポイントの作成対象のリソースが自分のディレクトリ内にある場合、十分なアクセス許可があれば、接続要求を承認することができます。 別のディレクトリ内の Azure リソースに接続している場合は、そのリソースの所有者が接続要求を承認するまで待機する必要があります。

プロビジョニングの状態には次の 4 つがあります。

| サービス アクション | サービス コンシューマーのプライベート エンドポイントの状態 | 説明 |
|--|--|--|
| なし | 保留中 | 接続が手動で作成されており、プライベート リンク リソースの所有者からの承認を待っています。 |
| 承認 | Approved | 接続が自動または手動で承認され、使用する準備が整っています。 |
| Reject | 拒否 | プライベート リンク リソースの所有者によって接続が拒否されました。 |
| [削除] | [Disconnected]\(切断済み\) | プライベート リンク リソースの所有者によって接続が削除されました。プライベート エンドポイントは情報が多くなり、クリーンアップのために削除する必要があります。 |
 
###  <a name="approve-reject-or-remove-a-private-endpoint-connection"></a>プライベート エンドポイント接続の承認、拒否、または削除

1. Azure portal にサインインします。
1. 検索バーで、「**Service Bus**」と入力します。
1. 管理する **名前空間** を選択します。
1. **[ネットワーク]** タブを選択します。
5. 必要としている操作 (承認、拒否、または削除) に応じて、以下の適切なセクションに進みます。 

### <a name="approve-a-private-endpoint-connection"></a>プライベート エンドポイント接続を承認する

1. 保留中の接続がある場合は、プロビジョニング状態に **[保留]** と表示されている接続がリストに表示されます。 
2. 承認する **プライベート エンドポイント** を選択します
3. **[承認]** ボタンを選択します。

    ![プライベート エンドポイントを承認する](./media/private-link-service/private-endpoint-approve.png)
4. **[接続の承認]** ページで、必要に応じて **コメント** を入力し、 **[はい]** を選択します。 **[いいえ]** を選択した場合は、何も起こりません。 

    ![[接続の承認] ページ](./media/private-link-service/approve-connection-page.png)
5. リストに表示された接続の状態が **[承認済み]** に変更されていることを確認します。 

    ![接続の状態 - 承認済み](./media/private-link-service/connection-status-approved.png)

### <a name="reject-a-private-endpoint-connection"></a>プライベート エンドポイント接続を拒否する

1. 拒否するプライベート エンドポイント接続がある場合は、保留中の要求か、前に承認されている既存の接続かにかかわらず、該当するエンドポイント接続を選択し、 **[拒否]** ボタンをクリックします。

    ![[拒否] ボタン](./media/private-link-service/private-endpoint-reject.png)
2. **[接続の拒否]** ページで、必要に応じてコメントを入力し、 **[はい]** を選択します。 **[いいえ]** を選択した場合は、何も起こりません。 

    ![[接続の拒否] ページ](./media/private-link-service/reject-connection-page.png)
3. リストに表示された接続の状態が **[拒否済み]** に変更されていることを確認します。 

    ![拒否されたエンドポイント](./media/private-link-service/endpoint-rejected.png)


### <a name="remove-a-private-endpoint-connection"></a>プライベート エンドポイント接続を削除する

1. プライベート エンドポイント接続を削除するには、リストからそれを選択し、ツールバーの **[削除]** を選択します。 

    ![[削除] ボタン](./media/private-link-service/remove-endpoint.png)
2. **[接続の削除]** ページで **[はい]** を選択して、プライベート エンドポイントの削除を確定します。 **[いいえ]** を選択した場合は、何も起こりません。 

    ![[接続の削除] ページ](./media/private-link-service/delete-connection-page.png)
3. 状態が **[接続解除済み]** に変更されていることを確認します。 さらに、エンドポイントがリストから消えていることがわかります。 

## <a name="validate-that-the-private-link-connection-works"></a>プライベート リンク接続が機能することを検証する

プライベート エンドポイントの仮想ネットワーク内にあるリソースが、プライベート IP アドレスを使用して自分の Service Bus 名前空間に接続していること、そしてそれらがプライベート DNS ゾーンに正しく統合されていることを検証する必要があります。

最初に、[Azure portal での Windows 仮想マシンの作成](../virtual-machines/windows/quick-create-portal.md)に関するページの手順に従って、仮想マシンを作成します。

**[ネットワーク]** タブで、次の操作を行います。 

1. **[仮想ネットワーク]** と **[サブネット]** を指定します。 プライベート エンドポイントをデプロイした仮想ネットワークを選択する必要があります。
2. **[パブリック IP]** リソースを指定します。
3. **[NIC ネットワーク セキュリティ グループ]** で **[なし]** を選択します。
4. **[負荷分散]** で **[いいえ]** を選択します。

VM に接続してコマンド ラインを開き、次のコマンドを実行します。

```console
nslookup <service-bus-namespace-name>.servicebus.windows.net
```

結果は次のようになります。 

```console
Non-authoritative answer:
Name:    <service-bus-namespace-name>.privatelink.servicebus.windows.net
Address:  10.0.0.4 (private IP address associated with the private endpoint)
Aliases:  <service-bus-namespace-name>.servicebus.windows.net
```

## <a name="limitations-and-design-considerations"></a>制限事項と設計に関する考慮事項

**価格**: 価格情報については、[Azure Private Link の価格](https://azure.microsoft.com/pricing/details/private-link/)に関するページを参照してください。

**制限事項**: この機能は、Azure のすべてのパブリック リージョンで使用できます。

**Service Bus 名前空間あたりのプライベート エンドポイントの最大数**: 120

詳細については、[Azure Private Link サービスの制限事項](../private-link/private-link-service-overview.md#limitations)に関するセクションを参照してください。

## <a name="next-steps"></a>次の手順

- [Azure Private Link](../private-link/private-link-service-overview.md) の詳細
- [Azure Service Bus](service-bus-messaging-overview.md) の詳細
