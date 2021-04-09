---
title: クイック スタート:PowerShell を使用して Web トラフィックを転送する
titleSuffix: Azure Application Gateway
description: このクイックスタートでは、Azure PowerShell を使用して、Web トラフィックをバックエンド プール内の仮想マシンに転送する Azure Application Gateway を作成する方法を説明します。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 01/19/2021
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 8073d1e18b08a6deb0175f8eaf18de382e93e299
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98601854"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway-using-azure-powershell"></a>クイック スタート:Azure PowerShell を使用した Azure Application Gateway による Web トラフィックの転送

このクイックスタートでは、Azure PowerShell を使用してアプリケーション ゲートウェイを作成します。 さらに、それをテストし、正しく動作することを確認します。 

アプリケーション ゲートウェイは、アプリケーション Web トラフィックをバックエンド プール内の特定のリソースに転送します。 リスナーをポートに割り当て、ルールを作成し、リソースをバックエンド プールに追加します。 わかりやすくするために、この記事では、パブリック フロントエンド IP アドレス、アプリケーション ゲートウェイで単一サイトをホストするための基本リスナー、基本要求ルーティング規則、およびバックエンド プール内の 2 つの仮想マシンを使用する簡単な設定を使用します。

また、[Azure CLI](quick-create-cli.md) または [Azure portal](quick-create-portal.md) を使用してこのクイックスタートを完了することもできます。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Azure PowerShell バージョン 1.0.0 以降](/powershell/azure/install-az-ps) (Azure PowerShell をローカルで実行する場合)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="connect-to-azure"></a>Azure に接続する

Azure に接続するには、`Connect-AzAccount` を実行します。

## <a name="create-a-resource-group"></a>リソース グループを作成する

Azure で、関連するリソースをリソース グループに割り当てます。 既存のリソース グループを使用することも、新しいリソース グループを作成することもできます。

新しいリソース グループを作成するには、`New-AzResourceGroup` コマンドレットを使用します。 

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```
## <a name="create-network-resources"></a>ネットワーク リソースを作成する

お客様が作成するリソースの間で Azure による通信が行われるには、仮想ネットワークが必要です。  アプリケーション ゲートウェイ サブネットには、アプリケーション ゲートウェイのみを含めることができます。 その他のリソースは許可されません。  Application Gateway 用に新しいサブネットを作成するか、既存のサブネットを使用することができます。 この例では 2 つのサブネットを作成します。1 つはアプリケーション ゲートウェイ用で、もう 1 つはバックエンド サーバー用です。 ユース ケースに従って、Application Gateway のフロントエンド IP アドレスを [パブリック] または [プライベート] に設定できます。 この例では、パブリック フロントエンド IP アドレスを選択します。

1. `New-AzVirtualNetworkSubnetConfig` を使用して、サブネット構成を作成します。
2. `New-AzVirtualNetwork` を使用して、サブネット構成を使用して仮想ネットワークを作成します。 
3. `New-AzPublicIpAddress` を使用して、パブリック IP アドレスを作成します。 
> [!NOTE]
> [仮想ネットワーク サービス エンドポイント ポリシー](../virtual-network/virtual-network-service-endpoint-policies-overview.md)は現在、Application Gateway のサブネットではサポートされません。

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
## <a name="create-an-application-gateway"></a>アプリケーション ゲートウェイの作成

### <a name="create-the-ip-configurations-and-frontend-port"></a>IP 構成とフロントエンド ポートの作成

1. `New-AzApplicationGatewayIPConfiguration` を使用して、お客様が作成したサブネットをアプリケーション ゲートウェイに関連付けるための構成を作成します。 
2. `New-AzApplicationGatewayFrontendIPConfig` を使用して、先ほど作成したパブリック IP アドレスをアプリケーション ゲートウェイに割り当てるための構成を作成します。 
3. `New-AzApplicationGatewayFrontendPort` を使用して、アプリケーション ゲートウェイにアクセスするためのポート 80 を割り当てます。

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

1. `New-AzApplicationGatewayBackendAddressPool` を使用して、アプリケーション ゲートウェイのバックエンド プールを作成します。 現時点では、バックエンド プールは空です。 次のセクションでバックエンド サーバーの NIC を作成するときに、それらをバックエンド プールに追加することになります。
2. `New-AzApplicationGatewayBackendHttpSetting` を使用して、バックエンド プールの設定を構成します。

```azurepowershell-interactive
$backendPool = New-AzApplicationGatewayBackendAddressPool `
  -Name myAGBackendPool
$poolSettings = New-AzApplicationGatewayBackendHttpSetting `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 30
```

### <a name="create-the-listener-and-add-a-rule"></a>リスナーの作成とルールの追加

Azure では、アプリケーション ゲートウェイを有効にしてトラフィックをバックエンド プールに適切にルーティングするためにリスナーが必要です。 また、受信トラフィックに使用するバックエンド プールをリスナーが判断するには、ルールが必要です。 

1. 先ほど作成したフロントエンド構成とフロントエンド ポートを指定して、`New-AzApplicationGatewayHttpListener` を使用して、リスナーを作成します。 
2. `New-AzApplicationGatewayRequestRoutingRule` を使用して、*rule1* という名前のルールを作成します。 

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

1. `New-AzApplicationGatewaySku` を使用して、アプリケーション ゲートウェイのパラメーターを指定します。
2. `New-AzApplicationGateway` を使用して、アプリケーション ゲートウェイを作成します。

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

### <a name="backend-servers"></a>バックエンド サーバー

Application Gateway を作成したら、Web サイトのホストとなるバックエンド仮想マシンを作成します。 バックエンドは、NIC、仮想マシンスケールセット、パブリック IP アドレス、内部 IP アドレス、完全修飾ドメイン名 (FQDN)、および Azure App Service のようなマルチテナント バックエンドで構成できます。 

この例では、アプリケーション ゲートウェイのバックエンド サーバーとして使用する 2 つの仮想マシンを作成します。 また、仮想マシンに IIS をインストールして、Azure によってアプリケーション ゲートウェイが正常に作成されたことを確認します。

#### <a name="create-two-virtual-machines"></a>2 つの仮想マシンの作成

1. 先ほど作成した Application Gateway バックエンド プールの構成を `Get-AzApplicationGatewayBackendAddressPool` を使用して取得します。
2. `New-AzNetworkInterface` を使用して、ネットワーク インターフェイスを作成します。
3. `New-AzVMConfig` を使用して、仮想マシンの構成を作成します。
4. `New-AzVM` を使用して、仮想マシンを作成します。

次のコード サンプルを実行して仮想マシンを作成するときに、Azure から資格情報の入力を求められます。 ユーザー名とパスワードを入力します。
    
```azurepowershell-interactive
$appgw = Get-AzApplicationGateway -ResourceGroupName myResourceGroupAG -Name myAppGateway
$backendPool = Get-AzApplicationGatewayBackendAddressPool -Name myAGBackendPool -ApplicationGateway $appgw
$vnet   = Get-AzVirtualNetwork -ResourceGroupName myResourceGroupAG -Name myVNet
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name myBackendSubnet
$cred = Get-Credential
for ($i=1; $i -le 2; $i++)
{
  $nic = New-AzNetworkInterface `
    -Name myNic$i `
    -ResourceGroupName myResourceGroupAG `
    -Location EastUS `
    -Subnet $subnet `
    -ApplicationGatewayBackendAddressPool $backendpool
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

## <a name="test-the-application-gateway"></a>アプリケーション ゲートウェイのテスト

IIS はアプリケーション ゲートウェイを作成するのに必要ではありませんが、このクイックスタートでは、Azure によってアプリケーション ゲートウェイが正常に作成されたかどうかを確認するためにインストールしました。

IIS を使用してアプリケーション ゲートウェイをテストします。

1. `Get-AzPublicIPAddress` を実行して、アプリケーション ゲートウェイのパブリック IP アドレスを取得します。 
2. そのパブリック IP アドレスをコピーし、ブラウザーのアドレス バーに貼り付けます。 ブラウザーを更新したら、仮想マシンの名前が表示されるはずです。 応答が有効であれば、アプリケーション ゲートウェイが正常に作成され、バックエンドと正常に接続できることが保証されます。

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![アプリケーション ゲートウェイのテスト](./media/quick-create-powershell/application-gateway-iistest.png)


## <a name="clean-up-resources"></a>リソースをクリーンアップする

アプリケーション ゲートウェイと共に作成したリソースが不要になったら、リソース グループを削除します。 リソース グループを削除すると、アプリケーション ゲートウェイとそのすべての関連リソースも削除されます。 

リソース グループを削除するには、`Remove-AzResourceGroup` コマンドレットを呼び出します。

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure PowerShell を使用して、アプリケーション ゲートウェイで Web トラフィックを管理する](./tutorial-manage-web-traffic-powershell.md)

