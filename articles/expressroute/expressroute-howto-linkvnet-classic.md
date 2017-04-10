---
title: "ExpressRoute 回線への仮想ネットワークのリンク: PowerShell: クラシック: Azure | Microsoft Docs"
description: "このドキュメントでは、クラシック デプロイメント モデルと PowerShell を使用して ExpressRoute 回線に仮想ネットワーク (VNet) をリンクする方法の概要について説明します。"
services: expressroute
documentationcenter: na
author: ganesr
manager: carmonm
editor: 
tags: azure-service-management
ms.assetid: 9b53fd72-9b6b-4844-80b9-4e1d54fd0c17
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/13/2016
ms.author: ganesr
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: 9601ff3152e62baf95794919fdc5a7fc43204d52
ms.lasthandoff: 03/24/2017


---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-powershell-classic"></a>PowerShell (クラシック) を使用して仮想ネットワークを ExpressRoute 回線に接続する
> [!div class="op_single_selector"]
> * [Resource Manager - Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [Resource Manager - PowerShell](expressroute-howto-linkvnet-arm.md)
> * [クラシック - PowerShell](expressroute-howto-linkvnet-classic.md)
> * [ビデオ - Azure Portal](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> 
> 

この記事では、クラシック デプロイメント モデルと PowerShell を使用して Azure ExpressRoute 回線に仮想ネットワーク (VNet) をリンクする方法について説明します。 仮想ネットワークは、同じサブスクリプションにあっても、別のサブスクリプションの一部であってもかいまいません。

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Azure のデプロイ モデルについて**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="configuration-prerequisites"></a>構成の前提条件
1. Azure PowerShell モジュールの最新バージョンが必要になります。 [Azure ダウンロード ページ](https://azure.microsoft.com/downloads/)の PowerShell セクションから、最新の PowerShell モジュールをダウンロードすることができます。 Azure PowerShell モジュールを使用するようにコンピューターを構成する方法の手順については、「 [Azure PowerShell のインストールと構成の方法](/powershell/azureps-cmdlets-docs) 」の手順に従ってください。
2. 構成を開始する前に、[前提条件](expressroute-prerequisites.md)、[ルーティングの要件](expressroute-routing.md)、[ワークフロー](expressroute-workflows.md)を確認する必要があります。
3. アクティブな ExpressRoute 回線が必要です。
   * 手順に従って、 [ExpressRoute 回線を作成](expressroute-howto-circuit-classic.md) し、接続プロバイダーに回線を有効にしてもらいます。
   * 回線用に Azure プライベート ピアリングが構成されていることを確認してください。 ルーティング手順については、 [ルーティングの構成](expressroute-howto-routing-classic.md) に関する記事を参照してください。
   * Azure プライベート ピアリングが構成されていることを確認します。また、エンド ツー エンド接続を有効にできるように、ネットワークと Microsoft の間の BGP ピアリングを起動しておく必要があります。
   * 仮想ネットワークと仮想ネットワーク ゲートウェイを作成して完全にプロビジョニングする必要があります。 指示に従い、 [ExpressRoute 用の仮想ネットワークを構成](expressroute-howto-vnet-portal-classic.md)します。

最大 10 個の仮想ネットワークを ExpressRoute 回線に接続できます。 仮想ネットワークはすべて同じ地理的リージョンにある必要があります。 ExpressRoute Premium アドオンを有効にした場合は、さらに多くの仮想ネットワークを ExpressRoute 回線にリンクしたり、他の地理的リージョンにある仮想ネットワークをリンクしたりできます。 Premium アドオンの詳細については、 [FAQ](expressroute-faqs.md) を確認してください。

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>同じサブスクリプション内の仮想ネットワークを回線に接続する
次のコマンドレットを使用して、ExpressRoute 回線に仮想ネットワークをリンクすることができます。 コマンドレットを実行する前に、仮想ネットワーク ゲートウェイが作成されており、リンクの準備ができていることを確認してください。

    New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
    Provisioned

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>別のサブスクリプション内の仮想ネットワークを回線に接続する
複数のサブスクリプションで ExpressRoute 回線を共有できます。 下図に、複数のサブスクリプションで ExpressRoute 回線を共有するしくみについて概略を示します。

大規模クラウド内のそれぞれの小規模クラウドは、組織内のさまざまな部門に属するサブスクリプションを表すために使用されています。 組織内の各部門はサービスのデプロイ用に固有のサブスクリプションを使用できますが、1 つの ExpressRoute 回線を共有することで、オンプレミス ネットワークに接続し直すことができます。 1 つの部門 (この例では IT) で ExpressRoute 回線を所有できます。 組織内の他のサブスクリプションも ExpressRoute 回線を使用できます。

> [!NOTE]
> 専用回線の接続と帯域幅の料金は、ExpressRoute 回線の所有者が負担することになります。 すべての仮想ネットワークが同じ帯域幅を共有します。
> 
> 

![サブスクリプション間接続](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration"></a>管理
*回線所有者* とは、ExpressRoute 回線が作成されたサブスクリプションの管理者または共同管理者です。 回線所有者は、他のサブスクリプションの管理者または共同管理者 ( *回線ユーザー*と呼ぶ) が所有する専用回線を使用することを承認できます。 組織の ExpressRoute 回線の使用を承認される回線ユーザーは、承認後、サブスクリプションの仮想ネットワークを ExpressRoute 回線にリンクできるようになります。

回線所有者は、承認をいつでも変更し、取り消す権限を持っています。 承認を取り消すと、アクセスが取り消されたサブスクリプションからすべてのリンクが削除されます。

### <a name="circuit-owner-operations"></a>回線所有者の操作

**承認の作成**

回線所有者は、その他のサブスクリプションの管理者が指定された回線を使用することを承認します。 次の例では、回線 (Contoso IT) の管理者は、別のサブスクリプション (Dev-Test) の管理者が最大 2 個の仮想ネットワークを回線にリンクできるようにします。 Contoso IT 管理者がこれを行うとき、Dev-Test Microsoft ID を指定します。 コマンドレットは、指定された Microsoft ID に電子メールを送信しません。 回線所有者が、承認が完了したことをその他のサブスクリプション所有者に明示的に通知する必要があります。

    New-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -Description "Dev-Test Links" -Limit 2 -MicrosoftIds 'devtest@contoso.com'

    Description         : Dev-Test Links
    Limit               : 2
    LinkAuthorizationId : **********************************
    MicrosoftIds        : devtest@contoso.com
    Used                : 0

**承認の確認**

回線所有者は、次のコマンドレットを実行し、特定の回線で発行されるすべての承認を確認できます。

    Get-AzureDedicatedCircuitLinkAuthorization -ServiceKey: "**************************"

    Description         : EngineeringTeam
    Limit               : 3
    LinkAuthorizationId : ####################################
    MicrosoftIds        : engadmin@contoso.com
    Used                : 1

    Description         : MarketingTeam
    Limit               : 1
    LinkAuthorizationId : @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
    MicrosoftIds        : marketingadmin@contoso.com
    Used                : 0

    Description         : Dev-Test Links
    Limit               : 2
    LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
    MicrosoftIds        : salesadmin@contoso.com
    Used                : 2


**承認の更新**

回線所有者は、次のコマンドレットを使用して承認を変更できます。

    Set-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -AuthorizationId "&&&&&&&&&&&&&&&&&&&&&&&&&&&&"-Limit 5

    Description         : Dev-Test Links
    Limit               : 5
    LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
    MicrosoftIds        : devtest@contoso.com
    Used                : 0


**承認の削除**

回線所有者は、次のコマンドレットを実行して、ユーザーに対する承認を取り消したり削除したりすることができます。

    Remove-AzureDedicatedCircuitLinkAuthorization -ServiceKey "*****************************" -AuthorizationId "###############################"


### <a name="circuit-user-operations"></a>回線ユーザーの操作

**承認の確認**

回線ユーザーは、次のコマンドレットを使用して承認を確認できます。

    Get-AzureAuthorizedDedicatedCircuit

    Bandwidth                        : 200
    CircuitName                      : ContosoIT
    Location                         : Washington DC
    MaximumAllowedLinks              : 2
    ServiceKey                       : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled
    UsedLinks                        : 0

**リンク承認の適用**

回線ユーザーは次のコマンドレットを実行し、リンク承認を利用できます。

    New-AzureDedicatedCircuitLink –servicekey "&&&&&&&&&&&&&&&&&&&&&&&&&&" –VnetName 'SalesVNET1'

    State VnetName
    ----- --------
    Provisioned SalesVNET1

## <a name="next-steps"></a>次のステップ
ExpressRoute の詳細については、「 [ExpressRoute のFAQ](expressroute-faqs.md)」をご覧ください。


