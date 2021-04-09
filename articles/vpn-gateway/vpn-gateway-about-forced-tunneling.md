---
title: 'Azure VPN Gateway:強制トンネリングを構成する - サイト間接続: クラシック'
description: クラシック デプロイ モデルを使用して作成された仮想ネットワークの強制トンネリングを構成する方法について説明します。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 10/15/2020
ms.author: cherylmc
ms.openlocfilehash: af4359efb48898c12bb8ee7ffb882448b5012d19
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92151354"
---
# <a name="configure-forced-tunneling-using-the-classic-deployment-model"></a>クラシック デプロイ モデルを使用した 強制トンネリングの構成

強制トンネリングを使用すると、検査および監査のために、サイト間の VPN トンネルを介して、インターネットへのすべてのトラフィックをオンプレミスの場所に戻すようにリダイレクトする (つまり、"強制する") ことができます。 これは、ほとんどの企業 IT ポリシーの重要なセキュリティ要件です。 強制トンネリングを使用しない場合、Azure の VM からインターネットへのトラフィックは、トラフィックを検査または監査できるオプションを使用せずに、常に Azure ネットワーク インフラストラクチャからインターネットへ直接トラバースします。 認証されていないインターネット アクセスは、情報の漏えいまたは他の種類のセキュリティ侵害を招く可能性があります。

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

この記事では、クラシック デプロイ モデルを使用して作成された仮想ネットワークの強制トンネリングを構成する手順について説明します。 強制トンネリングは、ポータルを経由せずに、PowerShell を使用して構成できます。 Resource Manager デプロイ モデル向けに強制トンネリングを構成する場合は、次のドロップダウン リストから Resource Manager の記事を選択します。

> [!div class="op_single_selector"]
> * [クラシック](vpn-gateway-about-forced-tunneling.md)
> * [Resource Manager](vpn-gateway-forced-tunneling-rm.md)
> 

## <a name="requirements-and-considerations"></a>要件と考慮事項

Azure では、強制トンネリングは仮想ネットワークのユーザー定義ルート (UDR) を使用して構成されます。 オンプレミス サイトへのトラフィックのリダイレクトは、Azure VPN Gateway への既定のルートとして表されます。 以下のセクションでは、Azure Virtual Network のルーティング テーブルおよびルートの現在の制限を一覧表示します。

* 各仮想ネットワーク サブネットには、システム ルーティング テーブルが組み込まれています。 システム ルーティング テーブルには、次の 3 つのグループがあります。

  * **ローカル VNet ルート:** 直接、同じ仮想ネットワーク内の宛先 VM へ。
  * **オンプレミス ルート:** Azure VPN ゲートウェイへ。
  * **既定のルート:** 直接、インターネットへ。 前の 2 つのルートが網羅していないプライベート IP アドレスへ送信されるパケットは削除されます。
* ユーザー定義ルートをリリースすることにより、既定のルートを追加するルーティング テーブルを作成し、そのルーティング テーブルを VNet サブネットに関連付け、それらのサブネットでの強制トンネリングを有効にすることができます。
* 仮想ネットワークに接続されたクロスプレミス ローカル サイト間で「既定のサイト」を設定する必要があります。
* 強制トンネリングは、動的ルーティング VPN ゲートウェイ (静的ゲートウェイではない) を持つ VNet に関連付ける必要があります。
* ExpressRoute の強制トンネリングは、このメカニズムを使用して構成されていませんが、代わりに ExpressRoute BGP ピアリング セッションを介して既定のルートを通知することで有効化されます。 詳細については、[ExpressRoute とは何か](../expressroute/expressroute-introduction.md)に関するページを参照してください。

## <a name="configuration-overview"></a>構成の概要

次の例では、フロントエンドのサブネットは、トンネリングを強制されません。 フロントエンドのサブネット内のワークロードは、直接、インターネットから顧客の要求を承認し応答し続けることができます。 Mid-tier および Backend のサブネットは、トンネリングを強制されます。 これら 2 つのサブネットからのインターネットへのオウトバウンド接続は、S2S VPN トンネルの 1 つを介して、オンプレミス サイトに ”強制” リダイレクトされます。

これにより、必要な多層サービス アーキテクチャが継続的に使用可能になっている間は、Azure 内の仮想マシンやクラウド サービスからのインターネット アクセスを制限および検査することができます。 また、仮想ネットワーク内に、インターネットに接続されたワークロードがない場合、仮想ネットワーク全体に強制トンネリングを適用することもできます。

![強制トンネリング](./media/vpn-gateway-about-forced-tunneling/forced-tunnel.png)

## <a name="prerequisites"></a>前提条件

構成を開始する前に、以下が揃っていることを確認します。

* Azure サブスクリプション。 Azure サブスクリプションをまだお持ちでない場合は、[MSDN サブスクライバーの特典](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)を有効にするか、[無料アカウント](https://azure.microsoft.com/pricing/free-trial/)にサインアップしてください。
* 構成済みの仮想ネットワーク。 
* [!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

## <a name="configure-forced-tunneling"></a>強制トンネリングについて

次の手順は、仮想ネットワークで強制トンネリングを指定するのに役立ちます。 構成手順は、VNet ネットワーク構成ファイルに対応します。  この例では、仮想ネットワークである "MultiTier-VNet" には、"Frontend"、"Midtier"、"Backend" という 3 つのサブネットがあり、"DefaultSiteHQ" と 3 つの Branch の計 4 つのクロス プレミス接続があります。


```xml
<VirtualNetworkSite name="MultiTier-VNet" Location="North Europe">
     <AddressSpace>
      <AddressPrefix>10.1.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Frontend">
            <AddressPrefix>10.1.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Midtier">
            <AddressPrefix>10.1.1.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Backend">
            <AddressPrefix>10.1.2.0/23</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.1.200.0/28</AddressPrefix>
          </Subnet>
        </Subnets>
        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="DefaultSiteHQ">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch1">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch2">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch3">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
        </Gateway>
      </VirtualNetworkSite>
    </VirtualNetworkSite>
```

次の手順では、"DefaultSiteHQ" を強制トンネリングの既定のサイト接続として設定し、強制トンネリングが使用されるように Midtier および Backend サブネットを構成します。

1. 管理者特権で PowerShell コンソールを開きます。 次の例を使用して、アカウントに接続します。

   ```powershell
   Add-AzureAccount
   ```

1. ルーティング テーブルを作成します。 ルート テーブルを作成するには、以下のコマンドレットを使用します。

   ```powershell
   New-AzureRouteTable –Name "MyRouteTable" –Label "Routing Table for Forced Tunneling" –Location "North Europe"
   ```

1. 既定のルートをルーティング テーブルに追加します。 

   次の例は、手順 1. で作成したルーティング テーブルに既定のルートを追加します。 サポートされている唯一のルートには、"VPNGateway" という次のホップへの宛先プレフィックスの「0.0.0.0/0」が付いています。

   ```powershell
   Get-AzureRouteTable -Name "MyRouteTable" | Set-AzureRoute –RouteTable "MyRouteTable" –RouteName "DefaultRoute" –AddressPrefix "0.0.0.0/0" –NextHopType VPNGateway
   ```

1. サブネットには、ルーティング テーブルを関連付けます。 

   ルーティング テーブルを作成し、ルートを追加した後は、次の例を使用して、ルート テーブルを VNet サブネットに追加または関連付けます。 このサンプルでは、ルート テーブル "MyRouteTable" を VNet MultiTier-VNet の Midtier および Backend サブネットに追加します。

   ```powershell
   Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Midtier" -RouteTableName "MyRouteTable"
   Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Backend" -RouteTableName "MyRouteTable"
   ```

1. 強制トンネリングに既定のサイトを割り当てます。 

   前の手順で、サンプルのコマンドレット スクリプトが、ルーティング テーブルを作成して、ルート テーブルを 2 つの VNet のサブネットに関連付けました。 残りの手順は、仮想ネットワークの複数サイト間接続でローカルのサイトを規定のサイトまたはトンネルとして選択します。

   ```powershell
   $DefaultSite = @("DefaultSiteHQ")
   Set-AzureVNetGatewayDefaultSite –VNetName "MultiTier-VNet" –DefaultSite "DefaultSiteHQ"
   ```

## <a name="additional-powershell-cmdlets"></a>その他の PowerShell コマンドレット

### <a name="to-delete-a-route-table"></a>ルート テーブルを削除するには

```powershell
Remove-AzureRouteTable -Name <routeTableName>
```
  
### <a name="to-list-a-route-table"></a>ルート テーブルを一覧表示するには

```powershell
Get-AzureRouteTable [-Name <routeTableName> [-DetailLevel <detailLevel>]]
```

### <a name="to-delete-a-route-from-a-route-table"></a>ルート テーブルからルートを削除するには

```powershell
Remove-AzureRouteTable –Name <routeTableName>
```

### <a name="to-remove-a-route-from-a-subnet"></a>サブネットからのルートを削除するには

```powershell
Remove-AzureSubnetRouteTable –VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>
```

### <a name="to-list-the-route-table-associated-with-a-subnet"></a>サブネットに関連付けられたルート テーブルの一覧表示するには

```powershell
Get-AzureSubnetRouteTable -VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>
```

### <a name="to-remove-a-default-site-from-a-vnet-vpn-gateway"></a>VNet VPN ゲートウェイから既定のサイトを削除するには

```powershell
Remove-AzureVnetGatewayDefaultSite -VNetName <virtualNetworkName>
```
