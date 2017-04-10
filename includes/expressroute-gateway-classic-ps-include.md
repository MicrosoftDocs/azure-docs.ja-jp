以下の作業を行う前に、VNet とゲートウェイ サブネットを作成しておく必要があります。 詳細については、 [クラシック ポータルを使用した Virtual Network の構成](../articles/expressroute/expressroute-howto-vnet-portal-classic.md) に関する記事をご覧ください。   

## <a name="add-a-gateway"></a>ゲートウェイを追加する
ゲートウェイを作成するには、次のコマンドを使用します。 必ず、独自の値に置き換えてください。

    New-AzureVirtualNetworkGateway -VNetName "MyAzureVNET" -GatewayName "ERGateway" -GatewayType Dedicated -GatewaySKU  Standard

## <a name="verify-the-gateway-was-created"></a>ゲートウェイが作成されていることの確認
次のコマンドを使用して、ゲートウェイが作成されていることを確認します。 このコマンドは、他の操作に必要なゲートウェイ ID も取得します。

    Get-AzureVirtualNetworkGateway

## <a name="resize-a-gateway"></a>ゲートウェイのサイズを変更する
[ゲートウェイ SKU](../articles/expressroute/expressroute-about-virtual-network-gateways.md)には複数の種類があります。 次のコマンドを使用して、ゲートウェイ SKU をいつでも変更できます。

> [!IMPORTANT]
> このコマンドは UltraPerformance ゲートウェイでは使用できません。 ゲートウェイを UltraPerformance ゲートウェイに変更するには、既存の ExpressRoute ゲートウェイを削除してから、新しい UltraPerformance ゲートウェイを作成します。 ゲートウェイを UltraPerformance ゲートウェイからダウングレードするには、UltraPerformance ゲートウェイを削除してから、新しいゲートウェイを作成します。 
> 
> 

    Resize-AzureVirtualNetworkGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance

## <a name="remove-a-gateway"></a>ゲートウェイを削除する
ゲートウェイを削除するには、次のコマンドを使用します。

    Remove-AzureVirtualNetworkGateway -GatewayId <Gateway ID>

<!--HONumber=Nov16_HO3-->


