---
title: チュートリアル:Azure PowerShell を使用してリージョン間ロード バランサーを作成する
titleSuffix: Azure Load Balancer
description: このチュートリアルでは、Azure PowerShell を使用してリージョン間 Azure ロード バランサーのデプロイを開始します。
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 02/10/2021
ms.openlocfilehash: 88e400cea764be84521c003a681aa74885dc29ce
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101721346"
---
# <a name="tutorial-create-a-cross-region-azure-load-balancer-using-azure-powershell"></a>チュートリアル:Azure PowerShell を使用してリージョン間 Azure ロード バランサーを作成する

リージョン間ロード バランサーを使うと、サービスが複数の Azure リージョンにわたってグローバルに利用可能になります。 1 つのリージョンで障害が発生した場合、トラフィックは次の最も近い正常なリージョンのロード バランサーにルーティングされます。  

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * リージョン間ロード バランサーを作成します。
> * ロード バランサー規則を作成します。
> * 2 つのリージョン ロード バランサーを含むバックエンド プールを作成します。
> * ロード バランサーをテストします。

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。
- 2 つの異なる Azure リージョンにデプロイされたバックエンド プールを備えた 2 つの **Standard** SKU Azure ロード バランサー。
    - バックエンド プール用にリージョンの Standard ロード バランサーと仮想マシンを作成する方法については、「[クイックスタート: Azure PowerShell を使用して VM の負荷を分散するパブリック ロード バランサーを作成する](quickstart-load-balancer-standard-public-powershell.md)」をご覧ください。
        - 各リージョンのロード バランサーと仮想マシンの名前の末尾に、 **-R1** と **-R2** を追加します。 
- ローカルにインストールされた Azure PowerShell または Azure Cloud Shell。


PowerShell をインストールしてローカルで使用する場合、この記事では Azure PowerShell モジュール バージョン 5.4.1 以降が必要になります。 インストールされているバージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-Az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzAccount` を実行して Azure との接続を作成することも必要です。

## <a name="create-cross-region-load-balancer"></a>リージョン間ロード バランサーを作成する


### <a name="create-a-resource-group"></a>リソース グループを作成する

Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) を使用して Azure リソース グループを作成します。


```azurepowershell-interactive
$rg = @{
    Name = 'MyResourceGroupLB-CR'
    Location = 'westus'
}
New-AzResourceGroup @rg

```

### <a name="create-cross-region-load-balancer-resources"></a>リージョン間ロード バランサーのリソースを作成する

このセクションでは、リージョン間ロード バランサーに必要なリソースを作成します。

グローバル Standard SKU パブリック IP は、リージョン間ロード バランサーのフロントエンドに使用されます。

* [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) を使用して、パブリック IP アドレスを作成します。

* [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) を使用して、フロントエンド IP 構成を作成します。

* [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) を使用して、バックエンド アドレス プールを作成します。

* [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig) を使用して、ロード バランサー規則を作成します。

* [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) を使用して、リージョン間ロード バランサーを作成します。

```azurepowershell-interactive
## Create global IP address for load balancer ##
$ip = @{
    Name = 'myPublicIP-CR'
    ResourceGroupName = 'MyResourceGroupLB-CR'
    Location = 'westus'
    Sku = 'Standard'
    Tier = 'Global'
    AllocationMethod = 'Static'
}
$publicIP = New-AzPublicIpAddress @ip

## Create frontend configuration ##
$fe = @{
    Name = 'myFrontEnd-CR'
    PublicIpAddress = $publicIP
}
$feip = New-AzLoadBalancerFrontendIpConfig @fe

## Create back-end address pool ##
$be = @{
    Name = 'myBackEndPool-CR'
}
$bepool = New-AzLoadBalancerBackendAddressPoolConfig @be

## Create the load balancer rule ##
$rul = @{
    Name = 'myHTTPRule-CR'
    Protocol = 'tcp'
    FrontendPort = '80'
    BackendPort = '80'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bepool
}
$rule = New-AzLoadBalancerRuleConfig @rul

## Create cross-region load balancer resource ##
$lbp = @{
    ResourceGroupName = 'myResourceGroupLB-CR'
    Name = 'myLoadBalancer-CR'
    Location = 'westus'
    Sku = 'Standard'
    Tier = 'Global'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bepool
    LoadBalancingRule = $rule
}
$lb = New-AzLoadBalancer @lbp
```

## <a name="configure-backend-pool"></a>バックエンド プールを構成する

このセクションでは、リージョン間ロード バランサーのバックエンド プールに 2 つのリージョン Standard ロード バランサーを追加します。

> [!IMPORTANT]
> これらの手順を完了するには、2 つのリージョン ロード バランサーとバックエンド プールを自分のサブスクリプションにデプロイしておく必要があります。  詳細については、「 **[クイックスタート: Azure PowerShell を使用して VM の負荷を分散するパブリック ロード バランサーを作成する](quickstart-load-balancer-standard-public-powershell.md)** 」をご覧ください。

* [Get-AzLoadBalancer](/powershell/module/az.network/get-azloadbalancer) と [Get-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/get-azloadbalancerfrontendipconfig) を使用して、リージョン ロード バランサーの情報を変数に格納します。

* [New-AzLoadBalancerBackendAddressConfig](/powershell/module/az.network/new-azloadbalancerbackendaddressconfig) を使用して、ロード バランサーのバックエンド アドレス プール構成を作成します。

* [Set-AzLoadBalancerBackendAddressPool](/powershell/module/az.network/new-azloadbalancerbackendaddresspool) を使用して、リージョン ロード バランサーのフロントエンドをリージョン間バックエンド プールに追加します。

```azurepowershell-interactive
## Place the region one load balancer configuration in a variable ##
$region1 = @{
    Name = 'myLoadBalancer-R1'
    ResourceGroupName = 'CreatePubLBQS-rg-r1'
}
$R1 = Get-AzLoadBalancer @region1

## Place the region two load balancer configuration in a variable ##
$region2 = @{
    Name = 'myLoadBalancer-R2'
    ResourceGroupName = 'CreatePubLBQS-rg-r2'
}
$R2 = Get-AzLoadBalancer @region2

## Place the region one load balancer front-end configuration in a variable ##
$region1fe = @{
    Name = 'MyFrontEnd-R1'
    LoadBalancer = $R1
}
$R1FE = Get-AzLoadBalancerFrontendIpConfig @region1fe

## Place the region two load balancer front-end configuration in a variable ##
$region2fe = @{
    Name = 'MyFrontEnd-R2'
    LoadBalancer = $R2
}
$R2FE = Get-AzLoadBalancerFrontendIpConfig @region2fe

## Create the cross-region backend address pool configuration for region 1 ##
$region1ap = @{
    Name = 'MyBackendPoolConfig-R1'
    LoadBalancerFrontendIPConfigurationId = $R1FE.Id
}
$beaddressconfigR1 = New-AzLoadBalancerBackendAddressConfig @region1ap

## Create the cross-region backend address pool configuration for region 2 ##
$region2ap = @{
    Name = 'MyBackendPoolConfig-R2'
    LoadBalancerFrontendIPConfigurationId = $R2FE.Id
}
$beaddressconfigR2 = New-AzLoadBalancerBackendAddressConfig @region2ap

## Apply the backend address pool configuration for the cross-region load balancer ##
$bepoolcr = @{
    ResourceGroupName = 'myResourceGroupLB-CR'
    LoadBalancerName = 'myLoadBalancer-CR'
    Name = 'myBackEndPool-CR'
    LoadBalancerBackendAddress = $beaddressconfigR1,$beaddressconfigR2
}
Set-AzLoadBalancerBackendAddressPool @bepoolcr

```

## <a name="test-the-load-balancer"></a>ロード バランサーをテストする

このセクションでは、リージョン間ロード バランサーをテストします。 Web ブラウザーで、パブリック IP アドレスに接続します。  いずれかのリージョン ロード バランサーのバックエンド プールにある仮想マシンを停止し、フェールオーバーを観察します。

1. [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) を使用して、ロード バランサーのパブリック IP アドレスを取得します。

```azurepowershell-interactive
$ip = @{
    Name = 'myPublicIP-CR'
    ResourceGroupName = 'myResourceGroupLB-CR'
}  
Get-AzPublicIPAddress @ip | select IpAddress

```
2. そのパブリック IP アドレスをコピーし、ブラウザーのアドレス バーに貼り付けます。 IIS Web サーバーの既定のページがブラウザーに表示されます。

3. いずれかのリージョン ロード バランサーのバックエンド プールにある仮想マシンを停止します。

4. Web ブラウザーを最新の状態に更新し、別のリージョン ロード バランサーへの接続のフェールオーバーを観察します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

必要がなくなれば、[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) コマンドを使用して、リソース グループやロード バランサー、その他のリソースを削除できます。

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'myResourceGroupLB-CR'
```

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、次の作業を行いました。

* グローバル IP アドレスを作成しました。
* リージョン間ロード バランサーを作成しました。
* 負荷分散規則を作成しました。
* リージョン間ロード バランサーのバックエンド プールにリージョン ロード バランサーを追加しました。
* ロード バランサーをテストしました。


次の記事に進み、以下の方法を学習してください。
> [!div class="nextstepaction"]
> [可用性ゾーン間のロード バランサー VM](tutorial-load-balancer-standard-public-zone-redundant-portal.md)