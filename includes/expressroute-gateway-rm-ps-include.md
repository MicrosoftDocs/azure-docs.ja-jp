このタスクの手順では、以下の値に基づいて VNet を使用します。 その他の設定と名前についても、一覧で概要を説明します。 ここでは、この一覧内の値に基づいて変数を追加しますが、どの手順でもこの一覧を直接使用しません。 参照として使用する一覧をコピーし、値を独自の値で置き換えることができます。

構成参照一覧:

* Virtual Network 名 = "TestVNet"
* Virtual Network のアドレス空間 = 192.168.0.0/16
* リソース グループ = "TestRG"
* Subnet1 名 = "FrontEnd" 
* Subnet1 アドレス空間 = "192.168.0.0/16"
* ゲートウェイ サブネット名: "GatewaySubnet"。ゲートウェイ サブネットには、常に *GatewaySubnet* という名前を付ける必要があります。
* ゲートウェイ サブネットのアドレス空間 = "192.168.200.0/26"
* リージョン = "米国東部"
* ゲートウェイ名 = "GW"
* ゲートウェイの IP 名 = "GWIP"
* ゲートウェイの IP 構成名 = "gwipconf"
* 種類 = "ExpressRoute"。ExpressRoute 構成には、この種類が必須です。
* ゲートウェイのパブリック IP 名 = "gwpip"

## <a name="add-a-gateway"></a>ゲートウェイを追加する
1. Azure サブスクリプションに接続します。 
   
        Login-AzureRmAccount
        Get-AzureRmSubscription 
        Select-AzureRmSubscription -SubscriptionName "Name of subscription"
2. この演習用に変数を宣言します。 この例では、以下のサンプルの編集を使用します。 サンプルを編集して、使用する設定が反映されることを確認します。 
   
        $RG = "TestRG"
        $Location = "East US"
        $GWName = "GW"
        $GWIPName = "GWIP"
        $GWIPconfName = "gwipconf"
        $VNetName = "TestVNet"
3. 仮想ネットワーク オブジェクトを変数として格納します。
   
        $vnet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
4. ゲートウェイ サブネットを Virtual Network に追加します。 ゲートウェイ サブネット名は、"GatewaySubnet " にする必要があります。 /27 以上 (/26、/25 など) のゲートウェイ サブネットを作成する必要があります。
   
        Add-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix 192.168.200.0/26
5. 構成を設定します。
   
            Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
6. 変数としてゲートウェイ サブネットを格納します。
   
        $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
7. パブリック IP アドレスを要求します。 IP アドレスは、ゲートウェイを作成する前に要求されます。 使用する IP アドレスは指定できません。IP アドレスは動的に割り当てられます。 この IP アドレスは、次の構成セクションで使用します。 AllocationMethod は動的である必要があります。
   
        $pip = New-AzureRmPublicIpAddress -Name $GWIPName  -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
8. ゲートウェイの構成を作成します。 ゲートウェイの構成で、使用するサブネットとパブリック IP アドレスを定義します。 この手順では、ケーブルの作成時に使用される構成を指定します。 この手順では、ゲートウェイ オブジェクトを実際に作成しません。 次のサンプルを使用して、ゲートウェイの構成を作成します。 
   
        $ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
9. ゲートウェイを作成します。 この手順では、 **-GatewayType** が特に重要です。 値 **ExpressRoute**を使用する必要があります。 これらのコマンドレットを実行してからゲートウェイが作成されるまでに 20 分以上かかる可能性があります。
   
        New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG -Location $Location -IpConfigurations $ipconf -GatewayType Expressroute -GatewaySku Standard

## <a name="verify-the-gateway-was-created"></a>ゲートウェイが作成されていることの確認
次のコマンドを使用して、ゲートウェイが作成されていることを確認します。

    Get-AzureRmVirtualNetworkGateway -ResourceGroupName $RG

## <a name="resize-a-gateway"></a>ゲートウェイのサイズを変更する
[ゲートウェイ SKU](../articles/expressroute/expressroute-about-virtual-network-gateways.md)には複数の種類があります。 次のコマンドを使用して、ゲートウェイ SKU をいつでも変更できます。

> [!IMPORTANT]
> このコマンドは UltraPerformance ゲートウェイでは使用できません。 ゲートウェイを UltraPerformance ゲートウェイに変更するには、既存の ExpressRoute ゲートウェイを削除してから、新しい UltraPerformance ゲートウェイを作成します。 ゲートウェイを UltraPerformance ゲートウェイからダウングレードするには、UltraPerformance ゲートウェイを削除してから、新しいゲートウェイを作成します。
> 
> 

    $gw = Get-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
    Resize-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance

## <a name="remove-a-gateway"></a>ゲートウェイを削除する
ゲートウェイを削除するには、次のコマンドを使用します。

    Remove-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG  
