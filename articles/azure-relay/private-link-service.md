---
title: Azure Relay と Azure Private Link サービスの統合
description: Azure Relay を Azure Private Link サービスと統合する方法を説明します
ms.date: 09/24/2020
ms.topic: article
ms.openlocfilehash: 13644082160704ba9918e6bd6257fa314bb463a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98134383"
---
# <a name="integrate-azure-relay-with-azure-private-link"></a>Azure Relay と Azure Private Link の統合 
Azure **Private Link サービス** を使用すると、自分の仮想ネットワーク内のプライベート エンドポイント経由で、Azure サービス (Azure Relay、Azure Service Bus、Azure Event Hubs、Azure Storage、Azure Cosmos DB など) や、Azure でホストされている顧客またはパートナーのサービスにアクセスできます。 詳細については、「[Azure Private Link とは](../private-link/private-link-overview.md)」を参照してください。

**プライベート エンドポイント** は、仮想ネットワークで実行されているワークロードが、**プライベート リンク リソース** (Relay 名前空間など) を持つサービスにプライベートで安全に接続できるようにするネットワーク インターフェイスです。 プライベート エンドポイントは、ご自分の VNet からのプライベート IP アドレスを使用して、サービスを実質的に VNet に取り込みます。 サービスへのすべてのトラフィックをプライベート エンドポイントを介してルーティングできるため、ゲートウェイ、NAT デバイス、ExpressRoute、VPN 接続、パブリック IP アドレスは不要です。 仮想ネットワークとサービスの間のトラフィックは、Microsoft のバックボーン ネットワークを経由するので、パブリック インターネットから公開されることがなくなります。 特定の Azure Relay 名前空間への接続を許可することにより、アクセスをきめ細かく制御できます。 


## <a name="add-a-private-endpoint-using-azure-portal"></a>Azure portal を使用してプライベート エンドポイントを追加する

### <a name="prerequisites"></a>前提条件
Azure Relay 名前空間を Azure Private Link と統合するには、次のエンティティまたはアクセス許可が必要です。

- Azure Relay 名前空間。
- Azure 仮想ネットワーク。
- 仮想ネットワーク内のサブネット。
- 仮想ネットワークに対する所有者または共同作成者のアクセス許可。

プライベート エンドポイントと仮想ネットワークは、同じリージョンに存在する必要があります。 ポータルを使用してプライベート エンドポイントのリージョンを選択すると、自動的にフィルター処理が行われ、そのリージョン内にある仮想ネットワークのみが表示されます。 名前空間は、別のリージョンに配置することができます。

プライベート エンドポイントは、ご自分の仮想ネットワーク内のプライベート IP アドレスを使用します。

### <a name="steps"></a>手順
新しい Azure Relay 名前空間とその中のエンティティを作成する詳細な手順については、「[Azure portal を使用した Relay 名前空間の作成](relay-create-namespace-portal.md)」をご覧ください。

1. [Azure portal](https://portal.azure.com) にサインインします。 
2. 検索バーに「**Relays**」と入力します。
3. プライベート エンドポイントを追加する **名前空間** を一覧から選択します。
4. **[設定]** で **[ネットワーク]** タブを選択します。
5. ページの上部にある **[プライベート エンドポイント接続]** タブを選択します。
6. ページの上部にある **[+ プライベート エンドポイント]** ボタンを選択します。

    ![[プライベート エンドポイントの追加] ボタン](./media/private-link-service/add-private-endpoint-button.png)
7. **[基本]** ページで、次の手順を行います。 
    1. プライベート エンドポイントを作成する **Azure サブスクリプション** を選択します。 
    2. プライベート エンドポイント リソース用の **リソース グループ** を選択します。
    3. プライベート エンドポイントの **名前** を入力します。 
    5. プライベート エンドポイントの **リージョン** を選択します。 プライベート エンドポイントは仮想ネットワークと同じリージョンに存在する必要がありますが、接続しようとしている Azure Relay 名前空間とは異なるリージョンでも構いません。 
    6. **Next:次へ: リソース >** ボタンがページの下部にあるのでクリックします。

        ![[プライベート エンドポイントの作成 - 基本] ページ](./media/private-link-service/create-private-endpoint-basics-page.png)
8. **[リソース]** ページで、次の手順を行います。
    1. 接続方法として **[マイ ディレクトリ内の Azure リソースに接続します]** を選択し、名前空間に対する所有者または共同作成者のアクセス権があり、その名前空間がプライベート エンドポイントと同じディレクトリ内にある場合は、次の手順を行います。 
        1. **Azure Relay 名前空間** が存在する **Azure サブスクリプション** を選択します。 
        2. **リソースの種類** については、 **[リソースの種類]** で **[Microsoft.Relay/namespaces]** を選択します。
        3. **[リソース]** で、ドロップダウン リストから Relay 名前空間を選択します。 
        4. **[ターゲット サブリソース]** が **[名前空間]** に設定されていることを確認します。
        5. **Next:次へ: 構成 >** ボタンがページの下部にあるのでクリックします。 
        
            ![[プライベート エンドポイントの作成 - リソース] ページ](./media/private-link-service/create-private-endpoint-resource-page.png)    
    2. 名前空間がプライベート エンドポイントと同じディレクトリにないために **[リソース ID またはエイリアスを使って Azure リソースに接続します]** を選択した場合は、次の手順を行います。
        1. **リソース ID** または **別名** を入力します。 それは誰かが自分と共有しているリソース ID または別名とすることができます。 リソース ID を取得する最も簡単な方法は、Azure portal で Azure Relay 名前空間に移動し、`/subscriptions/` から始まる URI の部分をコピーすることです。 たとえば、`/subscriptions/000000000-0000-0000-0000-000000000000000/resourceGroups/myresourcegroup/providers/Microsoft.Relay/namespaces/myrelaynamespace.` をコピーします。 
        2. **[ターゲット サブリソース]** では、「**名前空間**」と入力します。 これは、ご自分のプライベート エンドポイントでアクセスできるサブリソースの種類です。
        3. (省略可能) **要求メッセージ** を入力します。 このメッセージは、プライベート エンドポイント接続の管理中にリソース所有者に表示されます。
        4. 次に、**Next:次へ: 構成 >** ボタンがページの下部にあるのでクリックします。

            ![プライベート エンドポイントの作成 - リソース ID を使用した接続](./media/private-link-service/connect-resource-id.png)
9. **[構成]** ページで、プライベート エンドポイントのデプロイ先とする仮想ネットワーク内のサブネットを選択します。 
    1. **[仮想ネットワーク]** を選択します。 ドロップダウン リストには、現在選択されているサブスクリプションおよび場所内の仮想ネットワークのみが一覧表示されます。 
    2. 選択した仮想ネットワーク内の **サブネット** を選択します。 
    3. プライベート エンドポイントをプライベート DNS ゾーンと統合する場合は、 **[プライベート DNS ゾーンとの統合]** を有効にします。 
    
        プライベート エンドポイントに非公開で接続するには、DNS レコードが必要です。 プライベート エンドポイントと **プライベート DNS ゾーン** を統合することをお勧めします。 また、独自の DNS サーバーを利用したり、仮想マシン上のホスト ファイルを使用して DNS レコードを作成したりすることもできます。 詳細については、「[Azure プライベート エンドポイントの DNS 構成](../private-link/private-endpoint-dns.md)」をご覧ください。 この例では、 **[プライベート DNS ゾーンと統合する]** オプションが選択されており、プライベート DNS ゾーンが自動的に作成されます。 
    3. **Next:次へ: タグ >** ボタンがページの下部にあるので選択します。 

        ![[プライベート エンドポイントの作成 - 構成] ページ](./media/private-link-service/create-private-endpoint-configuration-page.png)
10. **[タグ]** ページでは、プライベート エンドポイントおよびプライベート DNS ゾーン (オプションを有効にした場合) と関連付けるタグ (名前と値) を作成します。 次に、ページの下部にある **[確認と作成]** ボタンを選択します。 
11. **[確認と作成]** では、すべての設定を確認し、 **[作成]** を選択してプライベート エンドポイントを作成します。
    
    ![[プライベート エンドポイントの作成 - 確認と作成] ページ](./media/private-link-service/create-private-endpoint-review-create-page.png)
12. **[プライベート エンドポイント]** ページでは、プライベート エンドポイント接続の状態を確認できます。 Relay 名前空間の所有者であるか、Relay 名前空間に対する管理アクセス権を持っており、 **[接続方法]** として **[マイ ディレクトリ内の Azure リソースに接続します]** オプションを選択した場合は、エンドポイント接続が **自動承認** されるようにする必要があります。 それが **[保留中]** 状態にある場合は、「[Azure portal を使用してプライベート エンドポイントを管理する](#manage-private-endpoints-using-azure-portal)」セクションを参照してください。

    ![プライベート エンドポイント ページ](./media/private-link-service/private-endpoint-page.png)
13. **名前空間** の **[ネットワーク]** ページに戻り、 **[プライベート エンドポイント接続]** タブに切り替えます。作成したプライベート エンドポイントが表示されます。 

    ![作成されたプライベート エンドポイント](./media/private-link-service/private-endpoint-created.png)

## <a name="add-a-private-endpoint-using-powershell"></a>PowerShell を使用してプライベート エンドポイントを追加する
次の例では、Azure PowerShell を使用して、Azure Relay 名前空間へのプライベート エンドポイント接続を作成する方法を示します。

プライベート エンドポイントと仮想ネットワークは、同じリージョンに存在する必要があります。 Azure Relay 名前空間は、別のリージョンに配置することができます。 さらに、ご利用のプライベート エンドポイントでは、自分の仮想ネットワーク内のプライベート IP アドレスが使用されます。

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

# create a relay namespace
$namespaceResource = New-AzResource -Location $namespaceLocation -ResourceName $namespaceName -ResourceGroupName $rgName -Properties @{} -ResourceType "Microsoft.Relay/namespaces" 

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

プライベート エンドポイントを作成する際は、接続を承認する必要があります。 プライベート エンドポイントの作成対象のリソース (Relay 名前空間) が自分のディレクトリ内にある場合、Relay 名前空間に対する管理特権を持っていれば、接続要求を承認できます。 管理アクセス権を持っていない Relay 名前空間に接続する場合は、そのリソースの所有者が接続要求を承認するまで待つ必要があります。

プロビジョニングの状態には次の 4 つがあります。

| サービス アクション | サービス コンシューマーのプライベート エンドポイントの状態 | 説明 |
|--|--|--|
| なし | 保留中 | 接続は手動で作成されており、Azure Relay 名前空間の所有者による承認を待っています。 |
| 承認 | Approved | 接続が自動または手動で承認され、使用する準備が整っています。 |
| Reject | 拒否 | 接続は、Azure Relay 名前空間の所有者によって拒否されました。 |
| [削除] | [Disconnected]\(切断済み\) | 接続は、Azure Relay 名前空間の所有者によって削除されました。プライベート エンドポイントは情報を提供するだけになっており、クリーンアップのために削除する必要があります。 |
 
###  <a name="approve-reject-or-remove-a-private-endpoint-connection"></a>プライベート エンドポイント接続の承認、拒否、または削除

1. Azure portal にサインインします。
1. 検索バーに「**Relay**」と入力します。
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

### <a name="reject-a-private-endpoint-connection"></a>プライベート エンドポイント接続を拒否する

1. 拒否するプライベート エンドポイント接続がある場合は、保留中の要求か、前に承認されている既存の接続かにかかわらず、該当するエンドポイント接続を選択し、 **[拒否]** ボタンをクリックします。

    ![[拒否] ボタン](./media/private-link-service/private-endpoint-reject.png)
2. **[接続の拒否]** ページで、必要に応じてコメントを入力し、 **[はい]** を選択します。 **[いいえ]** を選択した場合は、何も起こりません。 

    ![[接続の拒否] ページ](./media/private-link-service/reject-connection-page.png)
3. リストに表示された接続の状態が **[拒否済み]** に変更されていることを確認します。


### <a name="remove-a-private-endpoint-connection"></a>プライベート エンドポイント接続を削除する

1. プライベート エンドポイント接続を削除するには、リストからそれを選択し、ツールバーの **[削除]** を選択します。 

    ![[削除] ボタン](./media/private-link-service/remove-endpoint.png)
2. **[接続の削除]** ページで **[はい]** を選択して、プライベート エンドポイントの削除を確定します。 **[いいえ]** を選択した場合は、何も起こりません。 

    ![[接続の削除] ページ](./media/private-link-service/delete-connection-page.png)
3. 状態が **[接続解除済み]** に変更されていることを確認します。 さらに、エンドポイントがリストから消えていることがわかります。 

## <a name="validate-that-the-private-link-connection-works"></a>プライベート リンク接続が機能することを検証する
プライベート エンドポイントの仮想ネットワーク内のリソースが、プライベート IP アドレス経由で Azure Relay 名前空間に接続していることを検証する必要があります。

このテストのために、「[Azure portal で Windows 仮想マシンを作成する](../virtual-machines/windows/quick-create-portal.md)」の手順に従って仮想マシンを作成します。

**[ネットワーク]** タブで、次の操作を行います。 

1. **[仮想ネットワーク]** と **[サブネット]** を指定します。 プライベート エンドポイントをデプロイした仮想ネットワークを選択する必要があります。
2. **[パブリック IP]** リソースを指定します。
3. **[NIC ネットワーク セキュリティ グループ]** で **[なし]** を選択します。
4. **[負荷分散]** で **[いいえ]** を選択します。

VM に接続してコマンド ラインを開き、次のコマンドを実行します。

```console
nslookup <your-relay-namespace-name>.servicebus.windows.net
```

結果は次のようになります。 

```console
Non-authoritative answer:
Name:    <namespace-name>.privatelink.servicebus.windows.net
Address:  10.0.0.4 (private IP address associated with the private endpoint)
Aliases:  <namespace-name>.servicebus.windows.net
```

## <a name="limitations-and-design-considerations"></a>制限事項と設計に関する考慮事項

### <a name="design-considerations"></a>設計上の考慮事項
- 価格情報については、[Azure Private Link の価格](https://azure.microsoft.com/pricing/details/private-link/)に関するページを参照してください。

### <a name="limitations"></a>制限事項 
- Azure Relay 名前空間あたりのプライベート エンドポイントの最大数: 64。
- プライベート エンドポイントがある Azure Relay 名前空間の、サブスクリプションあたりの最大数: 64。
- ネットワーク セキュリティ グループ (NSG) 規則とユーザー定義ルートは、プライベート エンドポイントには適用されません。 詳細については、[Azure Private Link サービスの制限事項](../private-link/private-link-service-overview.md#limitations)に関するセクションを参照してください。

## <a name="next-steps"></a>次の手順

- [Azure Private Link](../private-link/private-link-service-overview.md) の詳細
- [Azure Relay](relay-what-is-it.md) の詳細を確認する
