---
title: "ExpressRoute 回線の作成および変更: PowerShell: Azure クラシック | Microsoft Docs"
description: "この記事では、ExpressRoute 回線の作成およびプロビジョニング手順について説明します。 この記事では回線の状態確認、更新、または削除およびプロビジョニング解除の方法も示します。"
documentationcenter: na
services: expressroute
author: ganesr
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 0134d242-6459-4dec-a2f1-4657c3bc8b23
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2017
ms.author: ganesr;cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: efdec32e565bf1d11b562d283e56bd8ed5d292b9
ms.lasthandoff: 03/24/2017


---
# <a name="create-and-modify-an-expressroute-circuit-using-powershell-classic"></a>PowerShell を使用した ExpressRoute 回線の作成と変更 (クラシック)
> [!div class="op_single_selector"]
> * [Resource Manager - Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [Resource Manager - PowerShell](expressroute-howto-circuit-arm.md)
> * [クラシック - PowerShell](expressroute-howto-circuit-classic.md)
> * [ビデオ - Azure Portal](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> 
>

この記事では、PowerShell コマンドレットとクラシック デプロイメント モデルを使用して、Azure ExpressRoute 回線を作成する手順について説明します。 この記事では、ExpressRoute 回線の状態確認、更新、または削除およびプロビジョニング解除の方法も示します。

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]


**Azure のデプロイ モデルについて**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-you-begin"></a>開始する前に
### <a name="step-1-review-the-prerequisites-and-workflow-articles"></a>手順 1. 前提条件とワークフローに関する記事を確認する
構成を開始する前に、必ず、[前提条件](expressroute-prerequisites.md)と[ワークフロー](expressroute-workflows.md)を確認してください。  

### <a name="step-2-install-the-latest-versions-of-the-azure-service-management-sm-powershell-modules"></a>手順 2. Azure サービス管理 (SM) PowerShell モジュールの最新版をインストールしてください
Azure PowerShell モジュールを使用するようにコンピューターを構成する方法の手順については、「[Azure PowerShell コマンドレットの概要](/powershell/azureps-cmdlets-docs)」の手順に従ってください。

### <a name="step-3-log-in-to-your-azure-account-and-select-a-subscription"></a>手順 3. Azure アカウントにログインしてサブスクリプションを選択する
1. 管理者特権で PowerShell コンソールを開き、アカウントに接続します。 接続については、次の例を参考にしてください。

        Login-AzureRmAccount

2. アカウントのサブスクリプションを確認します。

        Get-AzureRmSubscription

3. 複数のサブスクリプションがある場合は、使用するサブスクリプションを選択します。

        Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

4. 次に、次のコマンドレットを使用して、Azure サブスクリプションをクラシック デプロイ モデルの PowerShell に追加します。

        Add-AzureAccount

## <a name="create-and-provision-an-expressroute-circuit"></a>ExpressRoute 回線の作成とプロビジョニング
### <a name="step-1-import-the-powershell-modules-for-expressroute"></a>手順 1. ExpressRoute 用の PowerShell モジュールをインポートする
 ExpressRoute コマンドレットの使用を開始するには、PowerShell セッションに Azure および ExpressRoute のモジュールをインポートする必要があります (インポートがまだの場合)。 ローカル コンピューター上のインストールされた場所からモジュールをインポートします。 モジュールのインストールに使用した方法によっては、次の例で示す場所と異なる場合があります。 必要に応じて例を変更します。  

    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

### <a name="step-2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>手順 2. サポートされるプロバイダー、場所、帯域幅のリストを取得する
ExpressRoute 回線を作成する前に、サポートされている接続プロバイダー、場所、帯域幅オプションのリストが必要になります。

PowerShell コマンドレット `Get-AzureDedicatedCircuitServiceProvider` を実行すると、この情報が返されます。この情報は、後のステップで使用します。

    Get-AzureDedicatedCircuitServiceProvider

接続プロバイダーがそこにリストされているかどうかを確認します。 以下の項目は、後で回線を作成する際に必要になるため、書き留めておいてください。

* 名前
* PeeringLocations
* BandwidthsOffered

これで、ExpressRoute 回線を作成する準備が整いました。         

### <a name="step-3-create-an-expressroute-circuit"></a>手順 3. ExpressRoute 回線の作成
以下の例では、Silicon Valley の Equinix を通じて 200 Mbps の ExpressRoute 回線を作成する方法を示します。 別のプロバイダーと設定を使用する場合は、要求を実行するときにその情報に置き換えてください。

> [!IMPORTANT]
> ExpressRoute 回線の課金は、サービス キーが発行されたときから始まります。 接続プロバイダーが回線をプロビジョニングする準備ができたら、この操作を実行します。
> 
> 

以下に、新しいサービス キーの要求の例を示します。

    $Bandwidth = 200
    $CircuitName = "MyTestCircuit"
    $ServiceProvider = "Equinix"
    $Location = "Silicon Valley"

    New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location -sku Standard -BillingType MeteredData

または、Premium アドオンを使用して ExpressRoute 回線を作成する場合は、次の例を使用します。 Premium アドオンの詳細については、「 [ExpressRoute の FAQ](expressroute-faqs.md) 」を参照してください。

    New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location -sku Premium - BillingType MeteredData


応答にはサービス キーが含まれます。 以下を実行することで、すべてのパラメーターの詳細な説明を取得できます。

    get-help new-azurededicatedcircuit -detailed

### <a name="step-4-list-all-the-expressroute-circuits"></a>手順 4. すべての ExpressRoute 回線を一覧表示する
作成したすべての ExpressRoute 回線の一覧を取得するには、`Get-AzureDedicatedCircuit` コマンドを実行します。

    Get-AzureDedicatedCircuit

応答は次の例のようになります。

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : NotProvisioned
    Sku                              : Standard
    Status                           : Enabled

この情報は、 `Get-AzureDedicatedCircuit` コマンドレットを使用していつでも取得できます。 パラメーターを指定せずに呼び出しを実行すると、すべての回線が一覧表示されます。 サービス キーは *ServiceKey* フィールドに一覧表示されます。

    Get-AzureDedicatedCircuit

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : NotProvisioned
    Sku                              : Standard
    Status                           : Enabled

以下を実行することで、すべてのパラメーターの詳細な説明を取得できます。

    get-help get-azurededicatedcircuit -detailed

### <a name="step-5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>手順 5. プロビジョニングのためにサービス キーを接続プロバイダーに送信する
*ServiceProviderProvisioningState* は、サービス プロバイダー側でのプロビジョニングの現在の状態に関する情報を提供します。 *Status* は、Microsoft 側での状態を提供します。 回線のプロビジョニング状態に関する詳細については、 [ワークフロー](expressroute-workflows.md#expressroute-circuit-provisioning-states) に関する記事を参照してください。

新しい ExpressRoute 回線を作成する場合、この回線は次の状態になります。

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


回線は、接続プロバイダーが有効にしている間、次の状態になります。

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled

ExpressRoute 回線をユーザーが使用できるように、次の状態にする必要があります。

    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled


### <a name="step-6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>手順 6. 回線キーのステータスと状態を定期的に確認する
これにより、プロバイダーがいつ回線を有効にしたのかが分かります。 回線が構成されると、以下の例に示すように、*ServiceProviderProvisioningState* に *Provisioned* が表示されます。

    Get-AzureDedicatedCircuit

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

### <a name="step-7-create-your-routing-configuration"></a>手順 7. ルーティング構成を作成する
詳しい手順については、 [ExpressRoute 回線のルーティング構成 (回線ピアリングの作成と変更)](expressroute-howto-routing-classic.md) に関する記事を参照してください。

> [!IMPORTANT]
> 次の手順は、サービス プロバイダーが提供するレイヤー 2 接続サービスで作成された回線にのみ適用されます。 サービス プロバイダーが提供する管理対象レイヤー 3 サービス (MPLS など、通常は IP VPN) を使用する場合、接続プロバイダーがユーザーに代わってルーティングを構成および管理します。
> 
> 

### <a name="step-8-link-a-virtual-network-to-an-expressroute-circuit"></a>手順 8. ExpressRoute 回線への仮想ネットワークのリンク
次に、ExpressRoute 回線に仮想ネットワークをリンクします。 詳細な手順については、「 [ExpressRoute 回線への仮想ネットワークのリンク](expressroute-howto-linkvnet-classic.md) 」を参照してください。 ExpressRoute のクラシック デプロイメント モデルを使用して仮想ネットワークを作成する必要がある場合、手順については、「[クラシック ポータルでの ExpressRoute 用の Virtual Network の構成](expressroute-howto-vnet-portal-classic.md)」をご覧ください。

## <a name="getting-the-status-of-an-expressroute-circuit"></a>ExpressRoute 回線の状態の取得
この情報は、 `Get-AzureCircuit` コマンドレットを使用していつでも取得できます。 パラメーターを指定せずに呼び出しを実行すると、すべての回線が一覧表示されます。

    Get-AzureDedicatedCircuit

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

    Bandwidth                        : 1000
    CircuitName                      : MyAsiaCircuit
    Location                         : Singapore
    ServiceKey                       : #################################
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

呼び出しに対するパラメーターとしてサービス キーを渡すことで、特定の ExpressRoute 回線に関する情報を取得できます。

    Get-AzureDedicatedCircuit -ServiceKey "*********************************"

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled


次の例を実行することで、すべてのパラメーターの詳細な説明を取得できます。

    get-help get-azurededicatedcircuit -detailed

## <a name="modifying-an-expressroute-circuit"></a>ExpressRoute 回線の変更
ExpressRoute 回線の特定のプロパティは、接続に影響を与えることなく変更できます。

ダウンタイムなく、次を実行できます。

* ExpressRoute 回線の ExpressRoute Premium アドオンを有効または無効にします。
* ポートに使用可能な容量があれば、ExpressRoute 回線の帯域幅を増やします。 回線の帯域幅のダウングレードはサポートされていないことに注意してください。 
* 課金プランを従量制課金データから無制限データに変更します。 無制限データから従量制課金データへの課金プランの変更はサポートされていないことに注意してください。
* *従来の操作の許可*を有効または無効にできます。

制限および制約事項の詳細は、「 [ExpressRoute の FAQ](expressroute-faqs.md) 」を参照してください。

### <a name="to-enable-the-expressroute-premium-add-on"></a>ExpressRoute Premium アドオンを有効にするには
次の PowerShell コマンドレットを使用して、既存の回線の ExpressRoute Premium アドオンを有効にできます。

    Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Premium

    Bandwidth                        : 1000
    CircuitName                      : TestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Premium
    Status                           : Enabled

これで、回線の ExpressRoute Premium アドオン機能が有効になります。 このコマンドが正常に実行された時点で、Premium アドオン機能の課金が始まることに注意してください。

### <a name="to-disable-the-expressroute-premium-add-on"></a>ExpressRoute Premium アドオンを無効にするには
> [!IMPORTANT]
> 標準回線で許可されるリソースより多くのリソースを使用する場合、この操作は失敗することがあります。
> 
> 

#### <a name="considerations"></a>考慮事項

* Premium から標準にダウングレードする前に、回線にリンクされている仮想ネットワークの数が 10 未満であることを確認する必要があります。 そうしないと、更新要求は失敗し、Premium 料金が課金されます。
* 他の地理的リージョンではすべての仮想ネットワークのリンクを解除する必要があります。 そうしないと、更新要求は失敗し、Premium 料金が課金されます。
* プライベート ピアリングの場合、ルート テーブルのサイズを 4,000 ルート未満にする必要があります。 ルート テーブルのサイズが 4,000 ルートを超える場合、BGP セッションがドロップし、アドバタイズされたプレフィックスの数が 4,000 未満になるまで再度有効になりません。

#### <a name="disable-the-premium-add-on"></a>Premium アドオンの無効化
次の PowerShell コマンドレットを使用して、既存の回線の ExpressRoute Premium アドオンを無効にできます。

    Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Standard

    Bandwidth                        : 1000
    CircuitName                      : TestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled



### <a name="to-update-the-expressroute-circuit-bandwidth"></a>ExpressRoute 回線の帯域幅を更新するには
プロバイダーでサポートされている帯域幅のオプションについては、「 [ExpressRoute の FAQ](expressroute-faqs.md) 」を確認してください。 既存の回線のサイズを超えるサイズ (回線の作成先の物理ポートで許容されるだけのサイズ) を選択することができます。

> [!IMPORTANT]
> 既存のポートの容量が不十分な場合、ExpressRoute 回線の再作成が必要になる可能性があります。 その場所に使用可能な追加の容量がない場合、回路をアップグレードすることはできません。
>
> 中断せずに ExpressRoute 回線の帯域幅を減らすことはできません。 帯域幅をダウングレードするには、ExpressRoute 回線のプロビジョニングを解除してから、新しい ExpressRoute 回線を再度プロビジョニングする必要があります。
> 
> 

#### <a name="resize-a-circuit"></a>回線のサイズ変更

必要なサイズを決定した後、次のコマンドを使用して、回線のサイズを変更することができます。

    Set-AzureDedicatedCircuitProperties -ServiceKey ********************************* -Bandwidth 1000

    Bandwidth                        : 1000
    CircuitName                      : TestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

回線のサイズは、Microsoft 側で増やされます。 接続プロバイダーに連絡し、この変更に合わせて接続プロバイダー側の構成を更新するよう求める必要があります。 更新された帯域幅のオプションの課金は、この時点から開始されます。

回線の帯域幅を増やしたときに次のエラーが発生した場合は、既存の回線が作成されている物理ポートに十分な帯域幅がないことを意味します。 この回線を削除し、必要なサイズの新しい回線を作成する必要があります。 

    Set-AzureDedicatedCircuitProperties : InvalidOperation : Insufficient bandwidth available to perform this circuit
    update operation
    At line:1 char:1
    + Set-AzureDedicatedCircuitProperties -ServiceKey ********************* ...
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : CloseError: (:) [Set-AzureDedicatedCircuitProperties], CloudException
        + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ExpressRoute.SetAzureDedicatedCircuitPropertiesCommand


## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a>ExpressRoute 回線のプロビジョニング解除と削除

### <a name="considerations"></a>考慮事項

* この操作を正常に行うには、ExpressRoute 回線からすべての仮想ネットワークのリンクを解除する必要があります。 この操作が失敗した場合は、回線にリンクされている仮想ネットワークがないか確認してください。
* ExpressRoute 回線サービス プロバイダーのプロビジョニング状態が**プロビジョニング中**または**プロビジョニング済み**の場合、サービス プロバイダー側の回線のプロビジョニングを解除するには、サービス プロバイダーに連絡する必要があります。 Microsoft は、サービス プロバイダーが回線のプロビジョニング解除を完了し、通知するまで、リソースの予約と課金を続行します。
* サービス プロバイダーが回線のプロビジョニングを解除済み (サービス プロバイダーのプロビジョニング状態が **未プロビジョニング**に設定されている) の場合、回線を削除することができます。 回線の課金が停止されます。

#### <a name="delete-a-circuit"></a>回線の削除

ExpressRoute 回線は、次のコマンドを実行して削除できます。

    Remove-AzureDedicatedCircuit -ServiceKey "*********************************"



## <a name="next-steps"></a>次のステップ
回線を作成したら、次の操作を必ず実行します。

* [ExpressRoute 回線のルーティングの作成と変更を行う](expressroute-howto-routing-classic.md)
* [仮想ネットワークを ExpressRoute 回線にリンクする](expressroute-howto-linkvnet-classic.md)


