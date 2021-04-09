---
title: プライベート エンドポイントを使用する
titleSuffix: Azure SignalR Service
description: 仮想ネットワークから Azure SignalR Service へのセキュアなアクセスのための、プライベート エンドポイントの概要。
services: signalr
author: ArchangelSDY
ms.service: signalr
ms.topic: article
ms.date: 05/06/2020
ms.author: dayshen
ms.openlocfilehash: 92e93c3746308d2d6c1a489efc6b5c866b0ad2d9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98682632"
---
# <a name="use-private-endpoints-for-azure-signalr-service"></a>Azure SignalR Service のプライベート エンドポイントを使用する

Azure SignalR Service の[プライベート エンドポイント](../private-link/private-endpoint-overview.md)を使用すると、仮想ネットワーク (VNet) 上のクライアントは[プライベート リンク](../private-link/private-link-overview.md)を介してデータに安全にアクセスできるようになります。 プライベート エンドポイントでは、Azure SignalR サービスの VNet アドレス空間からの IP アドレスが使用されます。 VNet 上のクライアントと Azure SignalR Service 間のネットワーク トラフィックが、Microsoft バックボーン ネットワーク上のプライベート リンク経由で送受信され、パブリック インターネットからの公開は行われなくなります。

Azure SignalR Service のプライベート エンドポイントを使用すると、次のことが可能になります。

- ネットワーク アクセス制御を使用して Azure SignalR サービスをセキュリティで保護し、Azure SignalR Service に対するすべての接続をパブリック エンドポイント上でブロックします。
- VNnet からのデータの流出をブロックできるようにすることで、仮想ネットワーク (VNet) のセキュリティを強化します。
- [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) または [ExpressRoutes](../expressroute/expressroute-locations.md) をプライベート ピアリングと共に使用して、VNet に接続するオンプレミス ネットワークから Azure SignalR Service に安全に接続します。

## <a name="conceptual-overview"></a>概念の概要

![Azure SignalR Service のプライベート エンドポイントの概要](media/howto-private-endpoints/private-endpoint-overview.png)

プライベート エンドポイントは、[仮想ネットワーク](../virtual-network/virtual-networks-overview.md) (VNet) 内の Azure サービス用の特別なネットワーク インターフェイスです。 Azure SignalR サービスのプライベート エンドポイントを作成すると、VNet 上のクライアントとサービス間の安全な接続が提供されます。 プライベート エンドポイントには、VNet の IP アドレス範囲から IP アドレスが割り当てられます。 プライベート エンドポイントと Azure SignalR Service 間の接続には、セキュリティで保護されたプライベート リンクが使用されます。

VNet 内のアプリケーションは、プライベート エンドポイント経由で Azure SignalR Service にシームレスに接続できます。その際、**使用される接続文字列と承認メカニズムは、それを経由しない場合と同じになります**。 プライベート エンドポイントは、Azure SignalR サービスでサポートされているすべてのプロトコル (REST API を含む) で使用できます。

お使いの VNet で Azure SignalR サービスのプライベート エンドポイントを作成すると、承認を得るために同意要求が Azure SignalR Service の所有者に送信されます。 プライベート エンドポイントの作成を要求しているユーザーが Azure SignalR サービスの所有者でもある場合、この同意要求は自動的に承認されます。

Azure SignalR Service の所有者は、[Azure portal](https://portal.azure.com) で Azure SignalR サービスの *[プライベート エンドポイント]* タブを使用して、同意要求とプライベート エンドポイントを管理できます。

> [!TIP]
> Azure SignalR サービスへのアクセスを、プライベート エンドポイント経由のみに制限する場合は、パブリック エンドポイント経由のアクセスを拒否または制御するように[ネットワーク アクセス制御を構成](howto-network-access-control.md#managing-network-access-control)します。

### <a name="connecting-to-private-endpoints"></a>プライベート エンドポイントへの接続

プライベート エンドポイントを使用する VNet 上のクライアントは、パブリック エンドポイントに接続するクライアントと同じ接続文字列を、Azure SignalR サービスに対して使用する必要があります。 プライベート リンク経由で VNet から Azure SignalR Service への接続を自動的にルーティングするために、DNS 解決に依存しています。

> [!IMPORTANT]
> プライベート エンドポイントを利用しない場合と同じ接続文字列を使用して、Azure SignalR Service に接続します。 `privatelink` サブドメイン URL を使用して、Azure SignalR Service に接続しないでください。

既定では、VNet に接続されている[プライベート DNS ゾーン](../dns/private-dns-overview.md)が作成され、プライベート エンドポイントに必要な更新も行われます。 ただし、独自の DNS サーバーを使用している場合は、DNS 構成に追加の変更が必要になることがあります。 以下の [DNS の変更](#dns-changes-for-private-endpoints)に関するセクションで、プライベート エンドポイントに必要な更新について説明しています。

## <a name="dns-changes-for-private-endpoints"></a>プライベート エンドポイントの DNS の変更

プライベート エンドポイントを作成すると、Azure SignalR サービスの DNS CNAME リソース レコードは、プレフィックス `privatelink` を含むサブドメイン内のエイリアスに更新されます。 既定で、`privatelink` サブドメインに対応する[プライベート DNS ゾーン](../dns/private-dns-overview.md)も作成されます。これには、プライベート エンドポイントの DNS A リソース レコードが含まれます。

プライベート エンドポイントを含む VNet の外部から Azure SignalR Service ドメイン名を解決すると、Azure SignalR サービスのパブリック エンドポイントに解決されます。 プライベート エンドポイントをホストしている VNet から解決されると、ドメイン名はプライベート エンドポイントの IP アドレスに解決されます。

上の図の例のように、プライベート エンドポイントをホストしている VNet の外部から解決されると、Azure SignalR Service の 'foobar' の DNS リソース レコードは次のようになります。

| 名前                                                  | Type  | 値                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``foobar.service.signalr.net``                        | CNAME | ``foobar.privatelink.service.signalr.net``            |
| ``foobar.privatelink.service.signalr.net``            | A     | \<Azure SignalR Service public IP address\>           |

既に説明したように、ネットワーク アクセス制御を使用して、VNet の外部のクライアントによるパブリック エンドポイント経由のアクセスを拒否または制御することができます。

'foobar' の DNS リソース レコードは、プライベート エンドポイントをホストしている VNet 内のクライアントによって解決されると、次のようになります。

| 名前                                                  | Type  | 値                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``foobar.service.signalr.net``                        | CNAME | ``foobar.privatelink.service.signalr.net``            |
| ``foobar.privatelink.service.signalr.net``            | A     | 10.1.1.5                                              |

この方法を使用すると、プライベート エンドポイントをホストしている VNet 上のクライアントと、VNet の外部のクライアントから **同じ接続文字列を使用して** Azure SignalR Service にアクセスできます。

ネットワーク上でカスタム DNS サーバーを使用している場合、クライアントでは、Azure SignalR Service エンドポイントの FQDN をプライベート エンドポイントの IP アドレスに解決できる必要があります。 プライベート リンク サブドメインを VNet のプライベート DNS ゾーンに委任するように DNS サーバーを構成するか、プライベート エンドポイントの IP アドレスを使用して `foobar.privatelink.service.signalr.net` の A レコードを構成する必要があります。

> [!TIP]
> カスタムまたはオンプレミスの DNS サーバーを使用している場合は、`privatelink` サブドメインの Azure SignalR Service 名をプライベート エンドポイントの IP アドレスに解決するように、DNS サーバーを構成する必要があります。 これを行うには、VNet のプライベート DNS ゾーンに `privatelink` サブドメインを委任するか、DNS サーバー上で DNS ゾーンを構成し、DNS A レコードを追加します。

Azure SignalR Service のプライベート エンドポイントに推奨される DNS ゾーン名は、`privatelink.service.signalr.net` です。

プライベート エンドポイントをサポートするように独自の DNS サーバーを構成する方法の詳細については、次の記事を参照してください。

- [Azure 仮想ネットワーク内のリソースの名前解決](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)
- [プライベート エンドポイントの DNS 構成](../private-link/private-endpoint-overview.md#dns-configuration)

## <a name="create-a-private-endpoint"></a>プライベート エンドポイントの作成

### <a name="create-a-private-endpoint-along-with-a-new-azure-signalr-service-in-the-azure-portal"></a>Azure portal 上で、新しい Azure SignalR サービスと共にプライベート エンドポイントを作成します

1. 新しい Azure SignalR サービスを作成する場合は、 **[ネットワーク]** タブを選択します。接続方法として **[プライベート エンドポイント]** を選択します。

    ![Azure SignalR Service の作成 - [ネットワーク] タブ](media/howto-private-endpoints/portal-create-blade-networking-tab.png)

1. **[追加]** をクリックします。 新しいプライベート エンドポイントのサブスクリプション、リソース グループ、場所、名前を入力します。 仮想ネットワークとサブネットを選択します。

    ![Azure SignalR Service の作成 - プライベート エンドポイントを追加する](media/howto-private-endpoints/portal-create-blade-add-private-endpoint.png)

1. **[Review + create]\(レビュー + 作成\)** をクリックします。

### <a name="create-a-private-endpoint-for-an-existing-azure-signalr-service-in-the-azure-portal"></a>Azure portal 上で、既存の Azure SignalR サービスのプライベート エンドポイントを作成します

1. Azure SignalR サービスに移動します。

1. **[プライベート エンドポイント接続]** という設定メニューをクリックします。

1. 上部にある **[+ プライベート エンドポイント]** ボタンをクリックします。

    ![[プライベート エンドポイント接続] ブレード](media/howto-private-endpoints/portal-private-endpoint-connections-blade.png)

1. 新しいプライベート エンドポイントのサブスクリプション、リソース グループ、リソース名、およびリージョンを入力します。
    
    ![プライベート エンドポイントの作成 - 基本](media/howto-private-endpoints/portal-create-private-endpoint-basics.png)

1. ターゲットの Azure SignalR Service リソースを選択します。

    ![プライベート エンドポイントの作成 - リソース](media/howto-private-endpoints/portal-create-private-endpoint-resource.png)

1. ターゲットの仮想ネットワークを選択します

    ![プライベート エンドポイントの作成 - 構成](media/howto-private-endpoints/portal-create-private-endpoint-configuration.png)

1. **[Review + create]\(レビュー + 作成\)** をクリックします。

### <a name="create-a-private-endpoint-using-azure-cli"></a>Azure CLI を使用してプライベート エンドポイントを作成する

1. Azure CLI にログインする
    ```azurecli
    az login
    ```
1. Azure サブスクリプションを選択する
    ```azurecli
    az account set --subscription {AZURE SUBSCRIPTION ID}
    ```
1. 新しいリソース グループを作成する
    ```azurecli
    az group create -n {RG} -l {AZURE REGION}
    ```
1. Microsoft.SignalRService をプロバイダーとして登録します
    ```azurecli
    az provider register -n Microsoft.SignalRService
    ```
1. 新しい Azure SignalR サービスを作成します
    ```azurecli
    az signalr create --name {NAME} --resource-group {RG} --location {AZURE REGION} --sku Standard_S1
    ```
1. 仮想ネットワークを作成します
    ```azurecli
    az network vnet create --resource-group {RG} --name {vNet NAME} --location {AZURE REGION}
    ```
1. サブネットの追加
    ```azurecli
    az network vnet subnet create --resource-group {RG} --vnet-name {vNet NAME} --name {subnet NAME} --address-prefixes {addressPrefix}
    ```
1. 仮想ネットワーク ポリシーを無効にする
    ```azurecli
    az network vnet subnet update --name {subnet NAME} --resource-group {RG} --vnet-name {vNet NAME} --disable-private-endpoint-network-policies true
    ```
1. プライベート DNS ゾーンを追加する
    ```azurecli
    az network private-dns zone create --resource-group {RG} --name privatelink.service.signalr.net
    ```
1. 仮想ネットワークにプライベート DNS ゾーンをリンクする
    ```azurecli
    az network private-dns link vnet create --resource-group {RG} --virtual-network {vNet NAME} --zone-name privatelink.service.signalr.net --name {dnsZoneLinkName} --registration-enabled true
    ```
1. プライベート エンドポイントを作成する (自動的に承認する)
    ```azurecli
    az network private-endpoint create --resource-group {RG} --vnet-name {vNet NAME} --subnet {subnet NAME} --name {Private Endpoint Name}  --private-connection-resource-id "/subscriptions/{AZURE SUBSCRIPTION ID}/resourceGroups/{RG}/providers/Microsoft.SignalRService/SignalR/{NAME}" --group-ids signalr --connection-name {Private Link Connection Name} --location {AZURE REGION}
    ```
1. プライベート エンドポイントを作成する (承認を手動で要求する)
    ```azurecli
    az network private-endpoint create --resource-group {RG} --vnet-name {vNet NAME} --subnet {subnet NAME} --name {Private Endpoint Name}  --private-connection-resource-id "/subscriptions/{AZURE SUBSCRIPTION ID}/resourceGroups/{RG}/providers/Microsoft.SignalRService/SignalR/{NAME}" --group-ids signalr --connection-name {Private Link Connection Name} --location {AZURE REGION} --manual-request
    ```
1. 接続状態を表示する
    ```azurecli
    az network private-endpoint show --resource-group {RG} --name {Private Endpoint Name}
    ```

## <a name="pricing"></a>価格

料金の詳細については、「[Azure Private Link の料金](https://azure.microsoft.com/pricing/details/private-link)」をご覧ください。

## <a name="known-issues"></a>既知の問題

Azure SignalR Service のプライベート エンドポイントに関する以下の既知の問題に注意してください

### <a name="free-tier"></a>Free レベル

Free レベルの Azure SignalR Service のプライベート エンドポイントを作成することはできません。

### <a name="access-constraints-for-clients-in-vnets-with-private-endpoints"></a>プライベート エンドポイントを含む VNet 内のクライアントに対するアクセス制約

既存のプライベート エンドポイントを含む VNet 内のクライアントには、プライベート エンドポイントを保持している他の Azure SignalR Service インスタンスにアクセスするときに、制約があります。 たとえば、VNet N1 に、Azure SignalR Service インスタンス S1 のプライベート エンドポイントがあるとします。 Azure SignalR Service S2 に VNet N2 でのプライベート エンドポイントがある場合、VNet N1 のクライアントでは、プライベート エンドポイントを使用して Azure SignalR Service S2 にもアクセスする必要があります。 Azure SignalR Service S2 にプライベート エンドポイントがない場合、VNet N1 のクライアントは、プライベート エンドポイントを使用せずにそのアカウントでの Azure SignalR Service にアクセスできます。

この制約は、Azure SignalR Service S2 によってプライベート エンドポイントが作成されるときに行われた DNS 変更の結果です。

### <a name="network-security-group-rules-for-subnets-with-private-endpoints"></a>プライベート エンドポイントがあるサブネットのネットワーク セキュリティ グループ規則

現在、プライベート エンドポイントの[ネットワーク セキュリティ グループ](../virtual-network/network-security-groups-overview.md) (NSG) 規則とユーザー定義ルートを構成することはできません。 プライベート エンドポイントをホストするサブネットに適用される NSG 規則は、プライベート エンドポイントに適用されます。 この問題の限定的な回避策として、ソース サブネットでプライベート エンドポイントのアクセス規則を実装できます。ただし、この方法では、管理オーバーヘッドが高くなる可能性があります。

## <a name="next-steps"></a>次のステップ

- [ネットワーク アクセス制御を構成する](howto-network-access-control.md)