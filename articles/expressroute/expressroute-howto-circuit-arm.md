---
title: ExpressRoute 回線を作成および変更する - PowerShell:Azure | Microsoft Docs
description: ExpressRoute 回線を作成、プロビジョニング、確認、更新、削除、プロビジョニング解除します。
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 02/20/2019
ms.author: cherylmc
ms.reviewer: ganesr
ms.custom: seodec18
ms.openlocfilehash: 2ff22a3c0087ff7e24517f792ba9abacbae76e3a
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2019
ms.locfileid: "67846638"
---
# <a name="create-and-modify-an-expressroute-circuit-using-powershell"></a>PowerShell を使用した ExpressRoute 回線の作成と変更
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Azure Resource Manager テンプレート](expressroute-howto-circuit-resource-manager-template.md)
> * [ビデオ - Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (クラシック)](expressroute-howto-circuit-classic.md)
>

この記事は、PowerShell コマンドレットと Azure Resource Manager デプロイ モデルを使用して、ExpressRoute 回線を作成するときに役立ちます。 状態の確認、および回線の更新、削除、プロビジョニング解除を行うこともできます。

## <a name="before-you-begin"></a>開始する前に

構成を開始する前に、[前提条件](expressroute-prerequisites.md)と[ワークフロー](expressroute-workflows.md)を確認してください。

### <a name="working-with-azure-powershell"></a>Azure PowerShell を使用する

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="create"></a>ExpressRoute 回線の作成とプロビジョニング
### <a name="1-sign-in-to-your-azure-account-and-select-your-subscription"></a>1.Azure アカウントにサインインしてサブスクリプションを選択する

[!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

### <a name="2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>2.サポートされるプロバイダー、場所、帯域幅のリストを取得する
ExpressRoute 回線を作成する前に、サポートされている接続プロバイダー、場所、帯域幅オプションのリストが必要になります。

PowerShell コマンドレット **Get-AzExpressRouteServiceProvider** を実行すると、この情報が返されます。この情報は、後の手順で使用します。

```azurepowershell-interactive
Get-AzExpressRouteServiceProvider
```

接続プロバイダーがそこにリストされているかどうかを確認します。 以下の項目は、回線を作成する際に後で必要になるため、書き留めておいてください。

* EnableAdfsAuthentication
* PeeringLocations
* BandwidthsOffered

これで、ExpressRoute 回線を作成する準備が整いました。

### <a name="3-create-an-expressroute-circuit"></a>手順 3.ExpressRoute 回線の作成
リソース グループがまだない場合は、ExpressRoute 回線を作成する前に、作成しておく必要があります。 リソース グループを作成するには、次のコマンドを実行します。

```azurepowershell-interactive
New-AzResourceGroup -Name "ExpressRouteResourceGroup" -Location "West US"
```

以下の例では、Silicon Valley の Equinix を通じて 200 Mbps の ExpressRoute 回線を作成する方法を示します。 別のプロバイダーと設定を使用する場合は、要求を実行するときにその情報に置き換えてください。 新しいサービス キーを要求する場合は、次の例を参考にしてください。

```azurepowershell-interactive
New-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup" -Location "West US" -SkuTier Standard -SkuFamily MeteredData -ServiceProviderName "Equinix" -PeeringLocation "Silicon Valley" -BandwidthInMbps 200
```

必ず、適切な SKU レベルと SKU ファミリを指定してください。

* SKU レベルによって、ExpressRoute の Standard と Premium のどちらのアドオンが有効になるかが決まります。 標準 SKU を取得する場合は *[Standard]* を、プレミアム アドオンの場合は *[Premium]* を指定できます。
* SKU ファミリによって、課金の種類が決まります。 従量制課金データ プランの場合は *Metereddata*、無制限データ プランの場合は *Unlimiteddata* を指定できます。 課金の種類は *Metereddata* から *Unlimiteddata* に変更できますが、*Unlimiteddata* から *Metereddata* に変更することはできません。

> [!IMPORTANT]
> ExpressRoute 回線の課金は、サービス キーが発行されたときから始まります。 接続プロバイダーが回線をプロビジョニングする準備ができたら、この操作を実行します。
>
>

応答にはサービス キーが含まれます。 次のコマンドを実行することで、すべてのパラメーターの詳細な説明を取得できます。

```azurepowershell-interactive
get-help New-AzExpressRouteCircuit -detailed
```


### <a name="4-list-all-expressroute-circuits"></a>4.すべての ExpressRoute 回線を一覧表示する
作成したすべての ExpressRoute 回線の一覧を取得するには、**Get-AzExpressRouteCircuit** コマンドを実行します。

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

応答は次の例のようになります。

    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : westus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                         "Tier": "Standard",
                                         "Family": "MeteredData"
                                          }
    CircuitProvisioningState          : Enabled
    ServiceProviderProvisioningState  : NotProvisioned
    ServiceProviderNotes              :
    ServiceProviderProperties         : {
                                          "ServiceProviderName": "Equinix",
                                          "PeeringLocation": "Silicon Valley",
                                          "BandwidthInMbps": 200
                                        }
    ServiceKey                        : **************************************
    Peerings                          : []

この情報は、 `Get-AzExpressRouteCircuit` コマンドレットを使用していつでも取得できます。 パラメーターを指定せずに呼び出しを実行すると、すべての回線が一覧表示されます。 サービス キーは *ServiceKey* フィールドに一覧表示されます。

```azurepowershell-interactive
Get-AzExpressRouteCircuit
```


応答は次の例のようになります。

    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : westus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                         "Tier": "Standard",
                                         "Family": "MeteredData"
                                          }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : NotProvisioned
    ServiceProviderNotes             :
    ServiceProviderProperties        : {
                                         "ServiceProviderName": "Equinix",
                                         "PeeringLocation": "Silicon Valley",
                                         "BandwidthInMbps": 200
                                          }
    ServiceKey                       : **************************************
    Peerings                         : []


### <a name="5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>5.プロビジョニングのためにサービス キーを接続プロバイダーに送信する
*ServiceProviderProvisioningState* は、サービス プロバイダー側でのプロビジョニングの現在の状態に関する情報を提供します。 Status は、Microsoft 側での状態を提供します。 回線のプロビジョニング状態に関する詳細については、[ワークフロー](expressroute-workflows.md#expressroute-circuit-provisioning-states)に関するページを参照してください。

新しい ExpressRoute 回線を作成する場合、この回線は次の状態になります。

    ServiceProviderProvisioningState : NotProvisioned
    CircuitProvisioningState         : Enabled



回線は、接続プロバイダーが有効にしている間、次の状態に変化します。

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled

ExpressRoute 回線をユーザーが使用できるように、次の状態にする必要があります。

    ServiceProviderProvisioningState : Provisioned
    CircuitProvisioningState         : Enabled

### <a name="6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>6.回線キーのステータスと状態を定期的に確認する
回線キーのステータスと状態をチェックすると、いつプロバイダーによって回線が有効にされたかがわかります。 回線が構成されると、以下の例に示すように、*ServiceProviderProvisioningState* が *Provisioned* と表示されます。

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```


応答は次の例のようになります。

    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : westus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                         "Tier": "Standard",
                                         "Family": "MeteredData"
                                       }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned
    ServiceProviderNotes             :
    ServiceProviderProperties        : {
                                         "ServiceProviderName": "Equinix",
                                         "PeeringLocation": "Silicon Valley",
                                         "BandwidthInMbps": 200
                                       }
    ServiceKey                       : **************************************
    Peerings                         : []

### <a name="7-create-your-routing-configuration"></a>7.ルーティング構成を作成する
回線ピアリングの作成と変更の詳しい手順については、「 [PowerShell を使用した ExpressRoute 回線のルーティングの作成と変更](expressroute-howto-routing-arm.md) 」を参照してください。

> [!IMPORTANT]
> 次の手順は、サービス プロバイダーが提供するレイヤー 2 接続サービスで作成された回線にのみ適用されます。 サービス プロバイダーが提供する管理対象レイヤー 3 サービス (MPLS など、通常は IP VPN) を使用する場合、接続プロバイダーがユーザーに代わってルーティングを構成して管理します。
>
>

### <a name="8-link-a-virtual-network-to-an-expressroute-circuit"></a>8.ExpressRoute 回線への仮想ネットワークのリンク
次に、ExpressRoute 回線に仮想ネットワークをリンクします。 Resource Manager デプロイ モデルを使用するときは、「[ExpressRoute 回線への仮想ネットワークのリンク](expressroute-howto-linkvnet-arm.md)」を参照してください。

## <a name="getting-the-status-of-an-expressroute-circuit"></a>ExpressRoute 回線の状態の取得
この情報は、**Get-AzExpressRouteCircuit** コマンドレットを使用していつでも取得できます。 パラメーターを指定せずに呼び出しを実行すると、すべての回線が一覧表示されます。

```azurepowershell-interactive
Get-AzExpressRouteCircuit
```


応答は次の例のようになります。

    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : westus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                         "Tier": "Standard",
                                         "Family": "MeteredData"
                                       }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned
    ServiceProviderNotes             :
    ServiceProviderProperties        : {
                                            "ServiceProviderName": "Equinix",
                                            "PeeringLocation": "Silicon Valley",
                                            "BandwidthInMbps": 200
                                          }
    ServiceKey                       : **************************************
    Peerings                         : []


呼び出しに対するパラメーターとしてリソース グループ名と回線名を渡すことで、特定の ExpressRoute 回線に関する情報を取得できます。

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```


応答は次の例のようになります。

    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : westus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                            "Tier": "Standard",
                                            "Family": "MeteredData"
                                          }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned
    ServiceProviderNotes             :
    ServiceProviderProperties        : {
                                         "ServiceProviderName": "Equinix",
                                         "PeeringLocation": "Silicon Valley",
                                         "BandwidthInMbps": 200
                                          }
    ServiceKey                       : **************************************
    Peerings                         : []


次のコマンドを実行することで、すべてのパラメーターの詳細な説明を取得できます。

```azurepowershell-interactive
get-help get-azurededicatedcircuit -detailed
```

## <a name="modify"></a>ExpressRoute 回線の変更
ExpressRoute 回線の特定のプロパティは、接続に影響を与えることなく変更できます。

ダウンタイムなく、次のタスクを実行できます。

* ExpressRoute 回線の ExpressRoute Premium アドオンを有効または無効にします。
* ポートに使用可能な容量があれば、ExpressRoute 回線の帯域幅を増やします。 回線の帯域幅のダウングレードはサポートされていません。
* 課金プランを従量制課金データから無制限データに変更します。 無制限データから従量制課金データへの課金プランの変更はサポートされていません。
* *従来の操作の許可*を有効または無効にできます。

制限と制約事項の詳細は、「[ExpressRoute の FAQ](expressroute-faqs.md)」を参照してください。

### <a name="to-enable-the-expressroute-premium-add-on"></a>ExpressRoute Premium アドオンを有効にするには
次の PowerShell スニペットを使用し、既存の回線の ExpressRoute Premium アドオンを有効にすることができます。

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Premium"
$ckt.sku.Name = "Premium_MeteredData"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

これで、回線の ExpressRoute Premium アドオン機能が有効になりました。 このコマンドが正常に実行された時点で、Premium アドオン機能の課金が始まります。

### <a name="to-disable-the-expressroute-premium-add-on"></a>ExpressRoute Premium アドオンを無効にするには
> [!IMPORTANT]
> 標準回線で許可されるリソースより多くのリソースを使用する場合、この操作は失敗することがあります。
>
>

次の情報をメモしておきます。

* Premium から Standard にダウングレードする前に、回線にリンクされている仮想ネットワークの数が 10 未満であることを確認する必要があります。 そうでない場合、更新要求が失敗し、Premium 料金で課金されます。
* 他の地理的リージョンではすべての仮想ネットワークのリンクを解除する必要があります。 これを行わない場合、更新要求が失敗し、Premium 料金で課金されます。
* プライベート ピアリングの場合、ルート テーブルのサイズを 4,000 ルート未満にする必要があります。 ルート テーブルのサイズが 4,000 ルートを超える場合、BGP セッションがドロップし、アドバタイズされたプレフィックスの数が 4,000 未満になるまで再度有効になりません。

次の PowerShell コマンドレットを使用し、既存の回線の ExpressRoute Premium アドオンを無効にできます。

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Standard"
$ckt.sku.Name = "Standard_MeteredData"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-update-the-expressroute-circuit-bandwidth"></a>ExpressRoute 回線の帯域幅を更新するには
プロバイダーでサポートされている帯域幅のオプションについては、「 [ExpressRoute の FAQ](expressroute-faqs.md)」を確認してください。 既存の回線のサイズを超えるサイズを選択することができます。

> [!IMPORTANT]
> 既存のポートの容量が不十分な場合、ExpressRoute 回線の再作成が必要になる可能性があります。 その場所に使用可能な追加の容量がない場合、回路をアップグレードすることはできません。
>
> 中断せずに ExpressRoute 回線の帯域幅を減らすことはできません。 帯域幅をダウングレードするには、ExpressRoute 回線のプロビジョニングを解除してから、新しい ExpressRoute 回線を再度プロビジョニングする必要があります。
>

必要なサイズを決定した後、次のコマンドを使用して、回線のサイズを変更できます。

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.ServiceProviderProperties.BandwidthInMbps = 1000

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```


回線のサイズは Microsoft 側で増やされます。 次に、接続プロバイダーに連絡し、この変更に合わせて接続プロバイダー側の構成を更新するよう依頼する必要があります。 その通知が完了すると、更新された帯域幅オプションでの課金が開始されます。

### <a name="to-move-the-sku-from-metered-to-unlimited"></a>SKU を従量制から無制限に変更するには
ExpressRoute 回線の SKU を変更するには、次の PowerShell スニペットを使用します。

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Family = "UnlimitedData"
$ckt.sku.Name = "Premium_UnlimitedData"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>クラシック環境および Resource Manager 環境へのアクセスを制御するには
「[クラシック デプロイ モデルから Resource Manager デプロイ モデルへの ExpressRoute 回線の移行](expressroute-howto-move-arm.md)」の説明を参照してください。

## <a name="delete"></a>ExpressRoute 回線のプロビジョニング解除と削除
次の情報をメモしておきます。

* ExpressRoute 回線からすべての仮想ネットワークのリンクを解除する必要があります。 この操作が失敗した場合は、回線にリンクされている仮想ネットワークがないか確認してください。
* ExpressRoute 回線サービス プロバイダーのプロビジョニング状態が**プロビジョニング中**または**プロビジョニング済み**の場合、サービス プロバイダー側の回線のプロビジョニングを解除するには、サービス プロバイダーに連絡する必要があります。 Microsoft は、サービス プロバイダーが回線のプロビジョニング解除を完了し、通知するまで、リソースの予約と課金を続行します。
* サービス プロバイダーが回線のプロビジョニングを解除済み (サービス プロバイダーのプロビジョニング状態が**未プロビジョニング**に設定されている) の場合、回線を削除することができます。 これによって回線の課金が停止されます。

ExpressRoute 回線は、次のコマンドを実行して削除できます。

```azurepowershell-interactive
Remove-AzExpressRouteCircuit -ResourceGroupName "ExpressRouteResourceGroup" -Name "ExpressRouteARMCircuit"
```

## <a name="next-steps"></a>次の手順

回線を作成したら、次の手順を必ず実行します。

* [ExpressRoute 回線のルーティングの作成と変更を行う](expressroute-howto-routing-arm.md)
* [仮想ネットワークを ExpressRoute 回線にリンクする](expressroute-howto-linkvnet-arm.md)
