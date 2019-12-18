---
title: 'クラシック仮想ネットワークを Azure Resource Manager VNet に接続する: PowerShell | Microsoft Docs'
description: VPN Gateway と PowerShell を使用して、クラシック VNet と Resource Manager VNet の間に VPN 接続を作成します。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/17/2018
ms.author: cherylmc
ms.openlocfilehash: 2263996b84b17f7de9826c07eb28e4b7668cd915
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "62095594"
---
# <a name="connect-virtual-networks-from-different-deployment-models-using-powershell"></a>異なるデプロイ モデルの仮想ネットワークを PowerShell を使用して接続する

この記事は、クラシック VNet を Resource Manager VNet に接続し、異なるデプロイ モデルにあるリソースを相互に通信できるようにするのに役立ちます。 この記事の手順では PowerShell を使いますが、Azure Portal を使ってこの構成を作成することもできます。この場合は、次のリストから PowerShell に関する記事を選んでください。

> [!div class="op_single_selector"]
> * [ポータル](vpn-gateway-connect-different-deployment-models-portal.md)
> * [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

クラシック VNet から Resource Manager VNet への接続は、VNet をオンプレミス サイトの場所に接続することと似ています。 どちらの接続タイプでも、VPN ゲートウェイを使用して、IPsec/IKE を使った安全なトンネルが確保されます。 別のサブスクリプション、別のリージョンに存在する VNet 間で接続を作成することができます。 オンプレミスのネットワークに既に接続されている VNet を接続することもできます。ただし、Vnet が構成されているゲートウェイが動的またはルート ベースである場合に限ります。 VNet 間接続の詳細については、この記事の最後にある「[VNet 間接続に関してよく寄せられる質問](#faq)」を参照してください。 

仮想ネットワーク ゲートウェイがまだなく、新しく作成しない場合は、代わりに VNet ピアリングを使用して VNet を接続することを検討する可能性があります。 VNet ピアリングは、VPN ゲートウェイを使用しません。 詳細については、「 [VNet ピアリング](../virtual-network/virtual-network-peering-overview.md)」を参照してください。

## <a name="before"></a>開始する前に

次の手順では、各 VNet 用に動的またはルート ベースのゲートウェイを構成してゲートウェイ間の VPN 接続を作成する際に必要な設定について説明します。 この構成は、静的またはポリシー ベースのゲートウェイをサポートしていません。

### <a name="pre"></a>前提条件

* 両方の VNet が既に作成されている。 リソース マネージャーの仮想ネットワークを作成する必要がある場合は「[リソース グループと仮想ネットワークを作成する](../virtual-network/quick-create-powershell.md#create-a-resource-group-and-a-virtual-network)」を参照してください。 クラシックの仮想ネットワークを作成するには、[クラシック VNet の作成](https://docs.microsoft.com/azure/virtual-network/create-virtual-network-classic)に関するページをご覧ください。
* これらの VNet のアドレス範囲が互いに重複していない。また、ゲートウェイの接続先になる可能性のある他の接続の範囲と重複していない。
* 最新の PowerShell コマンドレットがインストール済みである。 詳細については、「 [Azure PowerShell のインストールおよび構成方法](/powershell/azure/overview) 」ご覧ください。 必ずサービス管理 (SM) と Resource Manager (RM) のコマンドレットの両方をインストールしてください。 

### <a name="exampleref"></a>設定例

この値を使って、テスト環境を作成できます。また、この値を参考にしながら、この記事の例を確認していくこともできます。

**クラシック VNet の設定**

VNet Name = ClassicVNet <br>
Location = West US <br>
Virtual Network Address Spaces = 10.0.0.0/24 <br>
Subnet-1 = 10.0.0.0/27 <br>
GatewaySubnet = 10.0.0.32/29 <br>
Local Network Name = RMVNetLocal <br>
GatewayType = DynamicRouting

**Resource Manager の VNet の設定**

VNet Name = RMVNet <br>
Resource Group = RG1 <br>
Virtual Network IP Address Spaces = 192.168.0.0/16 <br>
Subnet-1 = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
Location = East US <br>
Gateway public IP name = gwpip <br>
Local Network Gateway = ClassicVNetLocal <br>
Virtual Network Gateway name = RMGateway <br>
Gateway IP addressing configuration = gwipconfig

## <a name="createsmgw"></a>セクション 1 - クラシック VNet を構成する
### <a name="1-download-your-network-configuration-file"></a>1.ネットワーク構成ファイルをダウンロードする
1. 管理特権を使って PowerShell コンソールで Azure アカウントにログインします。 次のコマンドレットは、Azure アカウントのログイン資格情報をユーザーに求めます。 ログイン後にアカウント設定がダウンロードされ、Azure PowerShell で使用できるようになります。 このセクションでは、クラシック Service Management (SM) Azure PowerShell コマンドレットが使用されます。

   ```azurepowershell
   Add-AzureAccount
   ```

   Azure サブスクリプションを取得します。

   ```azurepowershell
   Get-AzureSubscription
   ```

   複数のサブスクリプションがある場合は、使用するサブスクリプションを選択します。

   ```azurepowershell
   Select-AzureSubscription -SubscriptionName "Name of subscription"
   ```
2. 次のコマンドを実行して、Azure のネットワーク構成ファイルをエクスポートします。 必要に応じて、ファイルの場所を変更して別の場所にエクスポートすることもできます。

   ```azurepowershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
3. ダウンロードした .xml ファイルを開き、編集します。 ネットワーク構成ファイルの例については、 [ネットワークの構成スキーマ](https://msdn.microsoft.com/library/jj157100.aspx)に関するページを参照してください。

### <a name="2-verify-the-gateway-subnet"></a>2.ゲートウェイ サブネットを確認する
**VirtualNetworkSites** 要素にゲートウェイ サブネットがまだ作成されていない場合は、VNet に追加します。 ネットワーク構成ファイルを使用する場合は、ゲートウェイ サブネットの名前が "GatewaySubnet" である必要があります。それ以外の場合は、Azure でこれを認識してゲートウェイ サブネットとして使用することができません。

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

**例:**

    <VirtualNetworkSites>
      <VirtualNetworkSite name="ClassicVNet" Location="West US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/24</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Subnet-1">
            <AddressPrefix>10.0.0.0/27</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.0.0.32/29</AddressPrefix>
          </Subnet>
        </Subnets>
      </VirtualNetworkSite>
    </VirtualNetworkSites>

### <a name="3-add-the-local-network-site"></a>3.ローカル ネットワーク サイトを追加する
追加するローカル ネットワーク サイトは、接続先の RM VNet を表します。 まだ存在していない場合は、**LocalNetworkSites** 要素をファイルに追加します。 構成のこの時点では、Resource Manager の VNet のゲートウェイをまだ作成していないため、VPNGatewayAddress は任意の有効なパブリック IP アドレスにすることができます。 ゲートウェイを作成したら、このプレースホルダー IP アドレスを、RM ゲートウェイに割り当てられている正しいパブリック IP アドレスに置き換えます。

    <LocalNetworkSites>
      <LocalNetworkSite name="RMVNetLocal">
        <AddressSpace>
          <AddressPrefix>192.168.0.0/16</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
      </LocalNetworkSite>
    </LocalNetworkSites>

### <a name="4-associate-the-vnet-with-the-local-network-site"></a>4.VNet をローカル ネットワーク サイトに関連付ける
このセクションでは、VNet の接続先のローカル ネットワーク サイトを指定します。 ここでは、先ほど参照した Resource Manager の VNet が該当します。 名前が一致することを確認します。 この手順では、ゲートウェイは作成しません。 ゲートウェイの接続先となるローカル ネットワークを指定します。

        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="RMVNetLocal">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

### <a name="5-save-the-file-and-upload"></a>5.ファイルを保存してアップロードする
ファイルを保存してから、次のコマンドを実行して Azure にインポートします。 必要に応じて、ファイル パスを環境に合わせて変更してください。

```azurepowershell
Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
```

インポートが成功したことを示す同様の結果が表示されます。

        OperationDescription        OperationId                      OperationStatus                                                
        --------------------        -----------                      ---------------                                                
        Set-AzureVNetConfig        e0ee6e66-9167-cfa7-a746-7casb9    Succeeded 

### <a name="6-create-the-gateway"></a>6.ゲートウェイを作成する

このサンプルを実行する前に、ダウンロードしたネットワーク構成ファイルで、Azure が期待する正確な名前を確認します。 ネットワーク構成ファイルには、クラシック仮想ネットワークの値が含まれています。 デプロイメント モデルでの違いのため、Azure Portal でクラシック VNet の設定を作成するときに、クラシック VNet の名前がネットワーク構成ファイルで変更される場合があります。 たとえば、Azure Portal を使って、"Classic VNet" という名前のクラシック VNet を、"ClassicRG" という名前のリソース グループに作成した場合、ネットワーク構成ファイルでは "Group ClassicRG Classic VNet" という名前に変換されます。 スペースを含む VNet の名前を指定するときは、引用符を使って値を囲みます。


次の例を使用して、動的ルーティング ゲートウェイを作成します。

```azurepowershell
New-AzureVNetGateway -VNetName ClassicVNet -GatewayType DynamicRouting
```

**Get-AzureVNetGateway** コマンドレットを使用すると、ゲートウェイの状態を確認できます。

## <a name="creatermgw"></a>セクション 2 - RM VNet ゲートウェイを構成する

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

前提条件では、既に RM VNet を作成済みであると仮定しています。 この手順では、RM VNet の VPN ゲートウェイを作成します。 クラシック VNet のゲートウェイのパブリック IP アドレスを取得するまで、手順を開始しないでください。 

1. PowerShell コンソールでお使いの Azure アカウントにサインインします。 次のコマンドレットは、Azure アカウントのログイン資格情報をユーザーに求めます。 サインイン後にアカウント設定がダウンロードされ、Azure PowerShell で使用できるようになります。 必要に応じて、"試してみる" 機能を使用して、ブラウザーで Azure Cloud Shell を起動できます。

   Azure Cloud Shell を使用している場合は、次のコマンドレットをスキップします。

   ```azurepowershell
   Connect-AzAccount
   ``` 
   適切なサブスクリプションを使用していることを確認するには、次のコマンドレットを実行します。  

   ```azurepowershell-interactive
   Get-AzSubscription
   ```
   
   複数のサブスクリプションがある場合は、使用するサブスクリプションを指定します。

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Name of subscription"
   ```
2. ローカル ネットワーク ゲートウェイを作成します。 仮想ネットワークでは、ローカル ネットワーク ゲートウェイは通常、オンプレミスの場所を指します。 ここでは、ローカル ネットワーク ゲートウェイはクラシック VNet を意味します。 Azure で参照できる名前を付けて、アドレス空間のプレフィックスも指定します。 指定した IP アドレス プレフィックスは、Azure がオンプレミスの場所に送信するトラフィックを特定するときに使用されます。 ここの情報を後で調整する必要がある場合は、ゲートウェイを作成する前に、値を変更してサンプルをもう一度実行することができます。
   
   **-Name** は、ローカル ネットワーク ゲートウェイを参照するために割り当てる名前です。<br>
   **-AddressPrefix** は、クラシック VNet のアドレス空間です。<br>
   **-GatewayIpAddress** は、クラシック VNet のゲートウェイのパブリック IP アドレスです。 適切な IP アドレスを反映するように、必ず次のサンプル テキストの "n.n.n.n" を変更してください。<br>

   ```azurepowershell-interactive
   New-AzLocalNetworkGateway -Name ClassicVNetLocal `
   -Location "West US" -AddressPrefix "10.0.0.0/24" `
   -GatewayIpAddress "n.n.n.n" -ResourceGroupName RG1
   ```
3. Resource Manager VNet の仮想ネットワーク ゲートウェイに割り当てるパブリック IP アドレスを要求します。 使用する IP アドレスを指定することはできません。 IP アドレスは仮想ネットワーク ゲートウェイに動的に割り当てられます。 ただし、これは IP アドレスが変更されるという意味ではありません。 仮想ネットワーク ゲートウェイの IP アドレスが変更されるのは、ゲートウェイが削除され、再度作成されたときのみです。 ゲートウェイのサイズ変更、リセット、その他の内部メンテナンス/アップグレードの際には変更されません。

   この手順では、後の手順で使用する変数も設定します。

   ```azurepowershell-interactive
   $ipaddress = New-AzPublicIpAddress -Name gwpip `
   -ResourceGroupName RG1 -Location 'EastUS' `
   -AllocationMethod Dynamic
   ```

4. 仮想ネットワークにゲートウェイ サブネットがあることを確認します。 ゲートウェイ サブネットが存在しない場合は追加します。 ゲートウェイ サブネットには、必ず *GatewaySubnet*という名前を付けてください。
5. 次のコマンドを実行して、ゲートウェイに使用されているサブネットを取得します。 この手順では、次の手順で使用する変数も設定します。
   
   **-Name** は、Resource Manager の VNet の名前です。<br>
   **-ResourceGroupName** は、VNet が関連付けられているリソース グループです。 ゲートウェイ サブネットが正常に動作するには、ゲートウェイ サブネットがこの VNet に対して既に存在し、 *GatewaySubnet* という名前が付けられている必要があります。<br>

   ```azurepowershell-interactive
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name GatewaySubnet `
   -VirtualNetwork (Get-AzVirtualNetwork -Name RMVNet -ResourceGroupName RG1)
   ``` 

6. ゲートウェイ IP アドレス指定の構成を作成します。 ゲートウェイの構成で、使用するサブネットとパブリック IP アドレスを定義します。 次のサンプルを使用して、ゲートウェイの構成を作成します。

   この手順では、 **-SubnetId** パラメーターと **-PublicIpAddressId** パラメーターはそれぞれ、サブネットの ID プロパティと IP アドレス オブジェクトの ID プロパティを渡さなければなりません。 単純な文字列を使用することはできません。 これらの変数は、パブリック IP を要求する手順とサブネットを取得する手順で設定されます。

   ```azurepowershell-interactive
   $gwipconfig = New-AzVirtualNetworkGatewayIpConfig `
   -Name gwipconfig -SubnetId $subnet.id `
   -PublicIpAddressId $ipaddress.id
   ```
7. Resource Manager の仮想ネットワーク ゲートウェイを作成します。 `-VpnType` は、 *RouteBased*である必要があります。 ゲートウェイの作成には 45 分以上かかる場合があります。

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1 `
   -Location "EastUS" -GatewaySKU Standard -GatewayType Vpn `
   -IpConfigurations $gwipconfig `
   -EnableBgp $false -VpnType RouteBased
   ```
8. VPN ゲートウェイを作成したら、パブリック IP アドレスをコピーします。 このアドレスは、クラシック VNet のローカル ネットワーク設定を構成するときに使用します。 次のコマンドレットを使用して、パブリック IP アドレスを取得できます。 パブリック IP アドレスは、 *IpAddress*として戻り値に表示されます。

   ```azurepowershell-interactive
   Get-AzPublicIpAddress -Name gwpip -ResourceGroupName RG1
   ```

## <a name="localsite"></a>セクション 3 - クラシック VNet ローカル サイトの設定を変更する

このセクションでは、クラシック VNet を使います。 Resource Manager VNet ゲートウェイに接続するために使われるローカル サイトの設定を指定するときに使ったプレースホルダー IP アドレスを置き換えます。 クラシック VNet を操作しているので、Azure Cloud Shell の試用機能ではなく、お使いのコンピューターにローカルにインストールされている PowerShell を使用してください。

1. ネットワーク構成ファイルをエクスポートします。

   ```azurepowershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
2. テキスト エディターを使って、VPNGatewayAddress の値を変更します。 プレースホルダー IP アドレスを Resource Manager ゲートウェイのパブリック IP アドレスに置き換えた後、変更を保存します。

   ```
   <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
   ```
3. 変更したネットワーク構成ファイルを Azure にインポートします。

   ```azurepowershell
   Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
   ```

## <a name="connect"></a>セクション 4 - ゲートウェイ間の接続を作成する
ゲートウェイ間の接続を作成するには PowerShell が必要です。 従来のバージョンの PowerShell コマンドレットを使って、Azure アカウントを追加する必要が生じる場合もあります。 それには、**Add-azureaccount** を使用します。

1. PowerShell コンソールで、共有キーを設定します。 コマンドレットを実行する前に、ダウンロードしたネットワーク構成ファイルで、Azure が期待する正確な名前を確認します。 スペースを含む VNet の名前を指定するときは、単一引用符を使って値を囲みます。<br><br>次の例では、 **-VNetName** はクラシック VNet の名前で、 **-LocalNetworkSiteName** はローカル ネットワーク サイトに対して指定した名前です。 **-SharedKey** は、生成して指定する値です。 この例では "abc123" を使いましたが、さらに複雑な値を生成して使うことができます。 重要なのは、ここで指定する値は、次の手順で接続を作成するときに指定するものと同じ値でなければならないということです。 戻り値が **Status: Successful** を示している必要があります。

   ```azurepowershell
   Set-AzureVNetGatewayKey -VNetName ClassicVNet `
   -LocalNetworkSiteName RMVNetLocal -SharedKey abc123
   ```
2. 次のコマンドを実行して、VPN 接続を作成します。
   
   変数を設定します。

   ```azurepowershell-interactive
   $vnet01gateway = Get-AzLocalNetworkGateway -Name ClassicVNetLocal -ResourceGroupName RG1
   $vnet02gateway = Get-AzVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1
   ```
   
   接続を作成します。 **-ConnectionType** が IPsec であり、Vnet2Vnet ではないことに注意してください。

   ```azurepowershell-interactive
   New-AzVirtualNetworkGatewayConnection -Name RM-Classic -ResourceGroupName RG1 `
   -Location "East US" -VirtualNetworkGateway1 `
   $vnet02gateway -LocalNetworkGateway2 `
   $vnet01gateway -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
   ```

## <a name="verify"></a>セクション 5 - 接続を確認する

### <a name="to-verify-the-connection-from-your-classic-vnet-to-your-resource-manager-vnet"></a>クラシック VNet から Resource Manager VNet への接続を確認するには

#### <a name="powershell"></a>PowerShell

[!INCLUDE [vpn-gateway-verify-connection-ps-classic](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

#### <a name="azure-portal"></a>Azure ポータル

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]


### <a name="to-verify-the-connection-from-your-resource-manager-vnet-to-your-classic-vnet"></a>Resource Manager VNet からクラシック VNet への接続を確認するには

#### <a name="powershell"></a>PowerShell

[!INCLUDE [vpn-gateway-verify-ps-rm](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

#### <a name="azure-portal"></a>Azure ポータル

[!INCLUDE [vpn-gateway-verify-connection-portal-rm](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="faq"></a>VNet 間接続に関してよく寄せられる質問

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]
