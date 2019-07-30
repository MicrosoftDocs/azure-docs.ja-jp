---
title: クイック スタート - Azure Application Gateway による Web トラフィックのルーティング - PowerShell | Microsoft Docs
description: Azure PowerShell を使用して、Web トラフィックをバックエンド プール内の仮想マシンにルーティングする Azure アプリケーション ゲートウェイを作成する方法を説明します。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 07/17/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: a55f602833cacd27cd82adafd888c67c544564c2
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359973"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-powershell"></a>クイック スタート:Azure Application Gateway による Web トラフィックの転送 - Azure PowerShell

このクイック スタートでは、Azure PowerShell を使用して、アプリケーション ゲートウェイをすばやく作成する方法を示します。  アプリケーション ゲートウェイを作成してから、それをテストして正しく動作していることを確認します。 Azure Application Gateway では、ポートにリスナーを割り当て、ルールを作成し、バックエンド プールにリソースを追加することによって、お客様のアプリケーション Web トラフィックを特定のリソースに転送します。 わかりやすくするために、この記事では、パブリック フロントエンド IP、このアプリケーション ゲートウェイで単一サイトをホストするための基本リスナー、バックエンド プールに使用される 2 つの仮想マシン、および基本要求ルーティング規則を使用する簡単な設定を使用します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>前提条件

### <a name="azure-powershell-module"></a>Azure PowerShell モジュール

Azure PowerShell をローカルにインストールして使用する場合、このチュートリアルでは Azure PowerShell モジュール バージョン 1.0.0 以降が必要になります。

1. バージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。 
2. Azure との接続を作成するには、`Login-AzAccount` を実行します。

### <a name="resource-group"></a>Resource group

Azure で、関連するリソースをリソース グループに割り当てます。 既存のリソース グループを使用することも、新しいリソース グループを作成することもできます。 この例では、次のように [New-AzResourceGroup](/powershell/module/Az.resources/new-Azresourcegroup) コマンドレットを使用して新しいリソース グループを作成します。 

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```

### <a name="required-network-resources"></a>必要なネットワーク リソース

お客様が作成するリソースの間で Azure による通信が行われるには、仮想ネットワークが必要です。  アプリケーション ゲートウェイ サブネットには、アプリケーション ゲートウェイのみを含めることができます。 その他のリソースは許可されません。  Application Gateway 用に新しいサブネットを作成するか、既存のサブネットを使用することができます。 2 つのサブネットを作成します。1 つはアプリケーション ゲートウェイ用で、もう 1 つはバックエンド サーバー用です。 ユース ケースに従って、Application Gateway のフロントエンド IP を [パブリック] または [プライベート] に設定できます。 この例では、パブリック フロントエンド IP を選択します。

1. [New-AzVirtualNetworkSubnetConfig](/powershell/module/Az.network/new-Azvirtualnetworksubnetconfig) を呼び出してサブネット構成を作成します。
2. [New-AzVirtualNetwork](/powershell/module/Az.network/new-Azvirtualnetwork) を呼び出して、サブネット構成と共に仮想ネットワークを作成します。 
3. [New-AzPublicIpAddress](/powershell/module/Az.network/new-Azpublicipaddress) を呼び出して、パブリック IP アドレスを作成します。 

```azurepowershell-interactive
$agSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.1.0/24
$backendSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.2.0/24
New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $agSubnetConfig, $backendSubnetConfig
New-AzPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myAGPublicIPAddress `
  -AllocationMethod Static `
  -Sku Standard
```
### <a name="backend-servers"></a>バックエンド サーバー

バックエンドは、NIC、仮想マシン スケール セット、パブリック IP、内部 IP、完全修飾ドメイン名 (FQDN)、および Azure App Service などのマルチテナント バックエンドで構成できます。 この例では、Azure によってアプリケーション ゲートウェイのバックエンド サーバーとして使用される 2 つの仮想マシンを作成します。 また、仮想マシンに IIS をインストールして、Azure によってアプリケーション ゲートウェイが正常に作成されたことを確認します。

#### <a name="create-two-virtual-machines"></a>2 つの仮想マシンの作成

1. [New-AzNetworkInterface](/powershell/module/Az.network/new-Aznetworkinterface) を使用してネットワーク インターフェイスを作成します。 
2. [New-AzVMConfig](/powershell/module/Az.compute/new-Azvmconfig) を使用して仮想マシンの構成を作成します。
3. [New-AzVM](/powershell/module/Az.compute/new-Azvm) を使用して、仮想マシンを作成します。

次のコード サンプルを実行して仮想マシンを作成するときに、Azure から資格情報の入力を求められます。 ユーザー名として「*azureuser*」を、パスワードとして「*Azure123456!* 」を 入力します。
    
```azurepowershell-interactive
$vnet   = Get-AzVirtualNetwork -ResourceGroupName myResourceGroupAG -Name myVNet
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name myBackendSubnet
$cred = Get-Credential
for ($i=1; $i -le 2; $i++)
{
  $nic = New-AzNetworkInterface `
    -Name myNic$i `
    -ResourceGroupName myResourceGroupAG `
    -Location EastUS `
    -SubnetId $subnet.Id
  $vm = New-AzVMConfig `
    -VMName myVM$i `
    -VMSize Standard_DS2_v2
  Set-AzVMOperatingSystem `
    -VM $vm `
    -Windows `
    -ComputerName myVM$i `
    -Credential $cred
  Set-AzVMSourceImage `
    -VM $vm `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest
  Add-AzVMNetworkInterface `
    -VM $vm `
    -Id $nic.Id
  Set-AzVMBootDiagnostic `
    -VM $vm `
    -Disable
  New-AzVM -ResourceGroupName myResourceGroupAG -Location EastUS -VM $vm
  Set-AzVMExtension `
    -ResourceGroupName myResourceGroupAG `
    -ExtensionName IIS `
    -VMName myVM$i `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
    -Location EastUS
}
```

## <a name="create-an-application-gateway"></a>アプリケーション ゲートウェイの作成

### <a name="create-the-ip-configurations-and-frontend-port"></a>IP 構成とフロントエンド ポートの作成

1. [New-AzApplicationGatewayIPConfiguration](/powershell/module/Az.network/new-Azapplicationgatewayipconfiguration) を使用して、お客様が作成したサブネットをアプリケーション ゲートウェイに関連付けるための構成を作成します。 
2. [New-AzApplicationGatewayFrontendIPConfig](/powershell/module/Az.network/new-Azapplicationgatewayfrontendipconfig) を使用して、お客様が先ほど作成したパブリック IP アドレスをアプリケーション ゲートウェイに割り当てるための構成を作成します。 
3. [New-AzApplicationGatewayFrontendPort](/powershell/module/Az.network/new-Azapplicationgatewayfrontendport) を使用して、アプリケーション ゲートウェイにアクセスするためのポート 80 を割り当てます。

```azurepowershell-interactive
$vnet   = Get-AzVirtualNetwork -ResourceGroupName myResourceGroupAG -Name myVNet
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name myAGSubnet
$pip    = Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress 
$gipconfig = New-AzApplicationGatewayIPConfiguration `
  -Name myAGIPConfig `
  -Subnet $subnet
$fipconfig = New-AzApplicationGatewayFrontendIPConfig `
  -Name myAGFrontendIPConfig `
  -PublicIPAddress $pip
$frontendport = New-AzApplicationGatewayFrontendPort `
  -Name myFrontendPort `
  -Port 80
```

### <a name="create-the-backend-pool"></a>バックエンド プールの作成

1. [New-AzApplicationGatewayBackendAddressPool](/powershell/module/Az.network/new-Azapplicationgatewaybackendaddresspool) を使用して、アプリケーション ゲートウェイのバックエンド プールを作成します。 
2. [New-AzApplicationGatewayBackendHttpSetting](/powershell/module/Az.network/new-Azapplicationgatewaybackendhttpsetting) を使用して、バックエンド プールの設定を構成します。

```azurepowershell-interactive
$address1 = Get-AzNetworkInterface -ResourceGroupName myResourceGroupAG -Name myNic1
$address2 = Get-AzNetworkInterface -ResourceGroupName myResourceGroupAG -Name myNic2
$backendPool = New-AzApplicationGatewayBackendAddressPool `
  -Name myAGBackendPool `
  -BackendIPAddresses $address1.ipconfigurations[0].privateipaddress, $address2.ipconfigurations[0].privateipaddress
$poolSettings = New-AzApplicationGatewayBackendHttpSetting `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-the-listener-and-add-a-rule"></a>リスナーの作成とルールの追加

Azure では、アプリケーション ゲートウェイを有効にしてトラフィックをバックエンド プールに適切にルーティングするためにリスナーが必要です。 また、受信トラフィックに使用するバックエンド プールをリスナーが判断するには、ルールが必要です。 

1. 先ほど作成したフロントエンド構成とフロントエンド ポートを指定して [New-AzApplicationGatewayHttpListener](/powershell/module/Az.network/new-Azapplicationgatewayhttplistener) を使用し、リスナーを作成します。 
2. [New-AzApplicationGatewayRequestRoutingRule](/powershell/module/Az.network/new-Azapplicationgatewayrequestroutingrule) を使用して、*rule1* という名前のルールを作成します。 

```azurepowershell-interactive
$defaultlistener = New-AzApplicationGatewayHttpListener `
  -Name myAGListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport
$frontendRule = New-AzApplicationGatewayRequestRoutingRule `
  -Name rule1 `
  -RuleType Basic `
  -HttpListener $defaultlistener `
  -BackendAddressPool $backendPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway"></a>アプリケーション ゲートウェイの作成

必要な関連リソースを作成したところで、アプリケーション ゲートウェイを作成します。

1. [New-AzApplicationGatewaySku](/powershell/module/Az.network/new-Azapplicationgatewaysku) を使用して、アプリケーション ゲートウェイのパラメーターを指定します。
2. [New-AzApplicationGateway](/powershell/module/Az.network/new-Azapplicationgateway) を使用して、アプリケーション ゲートウェイを作成します。

```azurepowershell-interactive
$sku = New-AzApplicationGatewaySku `
  -Name Standard_v2 `
  -Tier Standard_v2 `
  -Capacity 2
New-AzApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -BackendAddressPools $backendPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendport `
  -HttpListeners $defaultlistener `
  -RequestRoutingRules $frontendRule `
  -Sku $sku
```

## <a name="test-the-application-gateway"></a>アプリケーション ゲートウェイのテスト

IIS はアプリケーション ゲートウェイを作成するのに必要ではありませんが、このクイック スタートでは、Azure によってアプリケーション ゲートウェイが正常に作成されたかどうかを確認するためにインストールしました。 IIS を使用してアプリケーション ゲートウェイをテストします。

1. アプリケーション ゲートウェイのパブリック IP アドレスは、[Get-AzPublicIPAddress](/powershell/module/Az.network/get-Azpublicipaddress) を実行して取得します。 
2. そのパブリック IP アドレスをコピーし、ブラウザーのアドレス バーに貼り付けます。 ブラウザーを更新したら、仮想マシンの名前が表示されるはずです。 応答が有効であれば、アプリケーション ゲートウェイが正常に作成され、バックエンドと正常に接続できることが保証されます。

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![アプリケーション ゲートウェイのテスト](./media/quick-create-powershell/application-gateway-iistest.png)


## <a name="clean-up-resources"></a>リソースのクリーンアップ

アプリケーション ゲートウェイと共に作成したリソースが不要になったら、リソース グループを削除します。 リソース グループを削除することで、アプリケーション ゲートウェイとそのすべての関連リソースも削除します。 

リソース グループを削除するには、次のように [Remove-AzResourceGroup](/powershell/module/Az.resources/remove-Azresourcegroup) コマンドレットを呼び出します。

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure PowerShell を使用して、アプリケーション ゲートウェイで Web トラフィックを管理する](./tutorial-manage-web-traffic-powershell.md)

