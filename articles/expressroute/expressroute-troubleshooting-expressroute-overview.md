---
title: 接続の検証 - ExpressRoute トラブルシューティング ガイド:Azure | Microsoft Docs
description: このページでは、ExpressRoute 回線のエンド ツー エンド接続のトラブルシューティングと検証の手順について説明します。
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 09/26/2017
ms.author: rambala
ms.custom: seodec18
ms.openlocfilehash: 888f4dedf2fda0f54297d42a5f813abf73ded748
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58121521"
---
# <a name="verifying-expressroute-connectivity"></a>ExpressRoute 接続の検証
この記事は、ExpressRoute 接続の検証とトラブルシューティングに役立ちます。 ExpressRoute は、接続プロバイダーが提供するプライベート接続を介してオンプレミスのネットワークを Microsoft クラウドに拡張するもので、次の 3 つの異なるネットワーク ゾーンが含まれています。

-   顧客のネットワーク
-   プロバイダーのネットワーク
-   Microsoft のデータセンター

このドキュメントの目的は、ユーザーが接続の問題が発生している場所 (発生しているかどうかも) と、その問題がどのゾーン内にあるかを特定し、その結果、その問題を解決するのに適切なチームに支援を依頼できるようにすることです。 問題の解決に Microsoft サポートが必要な場合は、[Microsoft サポート][Support]でサポート チケットを開いてください。

> [!IMPORTANT]
> このドキュメントは、単純な問題の診断と解決をサポートすることを目的としています。 Microsoft サポートに代わるものではありません。 記載されているガイダンスで問題を解決できない場合は、[Microsoft サポート][Support]でサポート チケットを開いてください。
>
>

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>概要
次の図は、ExpressRoute を使用した顧客のネットワークから Microsoft ネットワークへの論理的な接続を示します。
[![1]][1]

上の図の中の番号は、重要なネットワーク ポイントを示しています。 ネットワーク ポイントは、関連付けられた番号によってこの記事全体で頻繁に参照されています。

ExpressRoute 接続モデル (クラウド交換コロケーション、ポイント ツー ポイントのイーサネット接続、または任意の環境間 (IPVPN)) に応じて、ネットワーク ポイント 3 と 4 がスイッチになる場合があります (レイヤー 2 デバイス)。 図に示されている主なネットワーク ポイントは次のとおりです。

1.  顧客のコンピューティング デバイス (例: サーバーや PC)
2.  CE:顧客のエッジ ルーター 
3.  PE (CE に接続):顧客のエッジ ルーターに接続している、プロバイダーのエッジ ルーターまたはスイッチ。 このドキュメントでは PE-CE と呼びます。
4.  PE (MSEE に接続):MSEE に接続している、プロバイダーのエッジ ルーターまたはスイッチ。 このドキュメントでは PE-MSEE と呼びます。
5.  MSEE:Microsoft エンタープライズ エッジ (MSEE) ExpressRoute ルーター
6.  仮想ネットワーク (VNet) ゲートウェイ
7.  Azure VNet 上のコンピューティング デバイス

クラウド交換コロケーションまたはポイント ツー ポイントのイーサネット接続の接続モデルが使用されている場合は、顧客のエッジ ルーター (2) が MSEE (5) との BGP ピアリングを確立します。 ネットワーク ポイント 3 と 4 はまだ存在しますが、レイヤー 2 デバイスとして若干透過的になります。

任意の環境間 (IPVPN) 接続モデルが使用されている場合は、PE (MSEE に接続) (4) が MSEE (5) との BGP ピアリングを確立します。 その後、ルートは、IPVPN サービス プロバイダーのネットワークを介して顧客のネットワークに反映されます。

> [!NOTE]
>ExpressRoute の高可用性のために、Microsoft では、MSEE (5) と PE-MSEE (4) の間で BGP セッションの冗長ペアが必要となります。 また、顧客のネットワークと PE-CE の間にはネットワーク パスの冗長ペアも推奨されます。 ただし、任意の環境間 (IPVPN) 接続モデルでは、単一の CE デバイス (2) が 1 つ以上の PE (3) に接続される可能性があります。
>
>

ExpressRoute 回線を検証するために、次の手順を説明します (ネットワーク ポイントは、関連付けられた番号で示されています)。
1. [回線のプロビジョニングと状態を検証する (5)](#validate-circuit-provisioning-and-state)
2. [1 つ以上の ExpressRoute ピアリングが構成されていることを検証する (5)](#validate-peering-configuration)
3. [Microsoft とサービス プロバイダー間の ARP を検証する (4 と 5 の間のリンク)](#validate-arp-between-microsoft-and-the-service-provider)
4. [MSEE 上の BGP とルートを検証する (4 から 5 の間、VNet が接続されている場合は 5 から 6 の間の BGP)](#validate-bgp-and-routes-on-the-msee)
5. [トラフィックの統計情報を確認する (5 を通過するトラフィック)](#check-the-traffic-statistics)

将来的には、さらに多くの検証やチェックが追加される予定です。毎月確認してください。

## <a name="validate-circuit-provisioning-and-state"></a>回線のプロビジョニングと状態を検証する
接続モデルに関係なく、ExpressRoute 回線を作成し、それに従い、回線のプロビジョニング用にサービス キーを生成する必要があります。 ExpressRoute 回線をプロビジョニングすると、PE-MSEE (4) と MSEE (5) の間に冗長なレイヤー 2 接続が確立します。 ExpressRoute 回線の作成、変更、プロビジョニング、検証を行う方法の詳細については、「[ExpressRoute 回線の作成と変更][CreateCircuit]」を参照してください。

>[!TIP]
>サービス キーは ExpressRoute 回線を一意に識別します。 このキーは、このドキュメントで説明する PowerShell コマンドのほとんどに必要です。 また、ExpressRoute の問題のトラブルシューティングを行うために Microsoft または ExpressRoute パートナーによるサポートが必要な場合は、回線を簡単に特定できるようにサービス キーを提供してください。
>
>

### <a name="verification-via-the-azure-portal"></a>Azure Portal を使用した検証
Azure Portal で、ExpressRoute 回線の状態を確認するには、左側のバーにあるメニューで ![2][2] を選択し、ExpressRoute 回線を選択します。 [すべてのリソース] の下に表示されている ExpressRoute 回線を選択すると、[ExpressRoute circuit (ExpressRoute 回線)] ブレードが開きます。 このブレードの ![3][3] セクションで、次のスクリーンショットに示されているように、ExpressRoute の要点が表示されます。

![4][4]    

ExpressRoute の [要点] にある *[回線の状態]* は Microsoft 側の回線の状態を示します。 *[Provider status (プロバイダーの状態)]* は、サービス プロバイダー側で回線が "*プロビジョニング済み/未プロビジョニング*" かどうかを示します。 

ExpressRoute 回線を運用可能にするには、*[回線の状態]* が *[有効]*、*[Provider status (プロバイダーの状態)]* が *[プロビジョニング済み]* である必要があります。

> [!NOTE]
> *[回線の状態]* が有効になっていない場合は、[Microsoft サポート][Support]にお問い合わせください。 *[Provider status (プロバイダーの状態)]* が未プロビジョニングの場合は、サービス プロバイダーにお問い合わせください。
>
>

### <a name="verification-via-powershell"></a>PowerShell を使用した検証
リソース グループ内のすべての ExpressRoute 回線を一覧表示するには、次のコマンドを使用します。

    Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG"

>[!TIP]
>リソース グループ名は、Azure で取得できます。 このドキュメントの前のサブセクションを参照すると、リソース グループ名がサンプルのスクリーンショットに表示されていることがわかります。
>
>

リソース グループ内の特定の ExpressRoute 回線を選択するには、次のコマンドを使用します。

    Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"

応答のサンプルは次のとおりです。

    Name                             : Test-ER-Ckt
    ResourceGroupName                : Test-ER-RG
    Location                         : westus2
    Id                               : /subscriptions/***************************/resourceGroups/Test-ER-RG/providers/***********/expressRouteCircuits/Test-ER-Ckt
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                        "Name": "Standard_UnlimitedData",
                                        "Tier": "Standard",
                                        "Family": "UnlimitedData"
                                        }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned
    ServiceProviderNotes             : 
    ServiceProviderProperties        : {
                                        "ServiceProviderName": "****",
                                        "PeeringLocation": "******",
                                        "BandwidthInMbps": 100
                                        }
    ServiceKey                       : **************************************
    Peerings                         : []
    Authorizations                   : []

ExpressRoute 回線が運用可能かどうかを確認するには、特に次のフィールドに注目してください。

    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned

> [!NOTE]
> *CircuitProvisioningState* が有効になっていない場合は、[Microsoft サポート][Support]にお問い合わせください。 *ServiceProviderProvisioningState* がプロビジョニング済みになっていない場合は、サービス プロバイダーにお問い合わせください。
>
>

### <a name="verification-via-powershell-classic"></a>PowerShell (クラシック) を使用した検証
サブスクリプション内のすべての ExpressRoute 回線を一覧表示するには、次のコマンドを使用します。

    Get-AzureDedicatedCircuit

特定の ExpressRoute 回線を選択するには、次のコマンドを使用します。

    Get-AzureDedicatedCircuit -ServiceKey **************************************

応答のサンプルは次のとおりです。

    andwidth                         : 100
    BillingType                      : UnlimitedData
    CircuitName                      : Test-ER-Ckt
    Location                         : westus2
    ServiceKey                       : **************************************
    ServiceProviderName              : ****
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

ExpressRoute 回線が運用可能かどうかを確認するには、特に次のフィールドに注目してください。ServiceProviderProvisioningState:Provisioned Status                           :Enabled

> [!NOTE]
> *Status* が有効になっていない場合は、[Microsoft サポート][Support]にお問い合わせください。 *ServiceProviderProvisioningState* がプロビジョニング済みになっていない場合は、サービス プロバイダーにお問い合わせください。
>
>

## <a name="validate-peering-configuration"></a>ピアリング構成を検証する
サービス プロバイダーが ExpressRoute 回線のプロビジョニングを完了すると、MSEE-PR (4) と MSEE (5) の間の ExpressRoute 回線でルーティング構成を作成できます。 各 ExpressRoute 回線では、1 つ、2 つ、または 3 つのルーティング コンテキストを有効にしておくことができます。これらのルーティング コンテキストとは、Azure プライベート ピアリング (Azure 内のプライベート仮想ネットワークへのトラフィック)、Azure パブリック ピアリング (Azure 内のパブリック IP アドレスへのトラフィック)、Microsoft ピアリング (Office 365 と Dynamics 365 へのトラフィック) です。 ルーティング構成の作成と変更方法の詳細については、「[ExpressRoute 回線のルーティングの作成と変更を行う][CreatePeering]」を参照してください。

### <a name="verification-via-the-azure-portal"></a>Azure Portal を使用した検証

> [!NOTE]
> レイヤー 3 がサービス プロバイダーから提供され、ポータルのピアリングが空の場合、ポータルの更新ボタンを使用して回線の構成を更新します。 この操作で、回線に適切なルーティング構成が適用されます。 
>
>

Azure Portal で、ExpressRoute 回線の状態を確認するには、左側のバーにあるメニューで ![2][2] を選択し、ExpressRoute 回線を選択します。 [すべてのリソース] の下に表示されている ExpressRoute 回線を選択すると、[ExpressRoute circuit (ExpressRoute 回線)] ブレードが開きます。 このブレードの ![3][3] セクションで、次のスクリーンショットに示されているように、ExpressRoute の要点が表示されます。

![5][5]

上の例では、説明したように Azure プライベート ピアリングのルーティング コンテキストが有効になっていますが、Azure パブリック ピアリングと Microsoft ピアリングのルーティング コンテキストは有効になっていません。 正常に有効にされたピアリング コンテキストでは、(BGP に必要な) プライマリとセカンダリのポイント ツー ポイントのサブネットも表示されます。 /30 サブネットは、MSEE と PE-MSEE のインターフェイス IP アドレスに使用されます。 

> [!NOTE]
> ピアリングが有効になっていない場合は、割り当てられたプライマリ サブネットとセカンダリ サブネットが PE-MSEE 上の構成と一致するかどうかを確認してください。 一致しない場合、MSEE ルーター上の構成を変更するには、「[ExpressRoute 回線のルーティングの作成と変更を行う][CreatePeering]」を参照してください。
>
>

### <a name="verification-via-powershell"></a>PowerShell を使用した検証
Azure プライベート ピアリング構成の詳細を取得するには、次のコマンドを使用します。

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt

正常に構成されたプライベート ピアリングの応答のサンプルは次のとおりです。

    Name                       : AzurePrivatePeering
    Id                         : /subscriptions/***************************/resourceGroups/Test-ER-RG/providers/***********/expressRouteCircuits/Test-ER-Ckt/peerings/AzurePrivatePeering
    Etag                       : W/"################################"
    PeeringType                : AzurePrivatePeering
    AzureASN                   : 12076
    PeerASN                    : ####
    PrimaryPeerAddressPrefix   : 172.16.0.0/30
    SecondaryPeerAddressPrefix : 172.16.0.4/30
    PrimaryAzurePort           : 
    SecondaryAzurePort         : 
    SharedKey                  : 
    VlanId                     : 300
    MicrosoftPeeringConfig     : null
    ProvisioningState          : Succeeded

 正常に有効にされたピアリング コンテキストでは、プライマリとセカンダリのアドレス プレフィックスが表示されます。 /30 サブネットは、MSEE と PE-MSEE のインターフェイス IP アドレスに使用されます。

Azure パブリック ピアリング構成の詳細を取得するには、次のコマンドを使用します。

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt

Microsoft ピアリング構成の詳細を取得するには、次のコマンドを使用します。

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
     Get-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

ピアリングが構成されていない場合は、エラー メッセージが表示されます。 記述されているピアリング (この例では Azure パブリック ピアリング) が回線内で構成されていない場合の応答のサンプルは次のとおりです。

    Get-AzExpressRouteCircuitPeeringConfig : Sequence contains no matching element
    At line:1 char:1
        + Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering ...
        + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
            + CategoryInfo          : CloseError: (:) [Get-AzExpr...itPeeringConfig], InvalidOperationException
            + FullyQualifiedErrorId : Microsoft.Azure.Commands.Network.GetAzureExpressRouteCircuitPeeringConfigCommand


> [!NOTE]
> ピアリングが有効になっていない場合は、割り当てられたプライマリ サブネットとセカンダリ サブネットが、リンクされた PE-MSEE 上の構成と一致するかどうかを確認してください。 さらに、適切な *VlanId*、*AzureASN*、*PeerASN* が MSEE で使用されているかどうかと、これらの値がリンクされた PE-MSEE で使用されている値に対応しているかどうかも確認してください。 MD5 ハッシュが選択されている場合は、MSEE と PE-MSEE のペアで共有キーは同じものにする必要があります。 MSEE ルーター上の構成を変更するには、「[ExpressRoute 回線のルーティングの作成と変更を行う][CreatePeering]」を参照してください。  
>
>

### <a name="verification-via-powershell-classic"></a>PowerShell (クラシック) を使用した検証
Azure プライベート ピアリング構成の詳細を取得するには、次のコマンドを使用します。

    Get-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"

正常に構成されたプライベート ピアリングの応答のサンプルは次のとおりです。

    AdvertisedPublicPrefixes       : 
    AdvertisedPublicPrefixesState  : Configured
    AzureAsn                       : 12076
    CustomerAutonomousSystemNumber : 
    PeerAsn                        : ####
    PrimaryAzurePort               : 
    PrimaryPeerSubnet              : 10.0.0.0/30
    RoutingRegistryName            : 
    SecondaryAzurePort             : 
    SecondaryPeerSubnet            : 10.0.0.4/30
    State                          : Enabled
    VlanId                         : 100

正常に有効にされたピアリング コンテキストでは、プライマリ ピアとセカンダリ ピアのサブネットが表示されます。 /30 サブネットは、MSEE と PE-MSEE のインターフェイス IP アドレスに使用されます。

Azure パブリック ピアリング構成の詳細を取得するには、次のコマンドを使用します。

    Get-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"

Microsoft ピアリング構成の詳細を取得するには、次のコマンドを使用します。

    Get-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"

> [!IMPORTANT]
> レイヤー 3 のピアリングがサービス プロバイダーによって設定された場合、ポータルまたは PowerShell で ExpressRoute ピアリングを設定すると、サービス プロバイダーの設定が上書きされます。 プロバイダー側のピアリングの設定をリセットするには、サービス プロバイダーのサポートが必要です。 ExpressRoute ピアリングを変更するのは、サービス プロバイダーがレイヤー 2 サービスのみを提供していることが明白な場合のみです。
>
>

> [!NOTE]
> ピアリングが有効になっていない場合は、割り当てられたプライマリ ピアとセカンダリ ピアのサブネットが、リンクされた PE-MSEE 上の構成と一致するかどうかを確認してください。 さらに、適切な *VlanId*、*AzureASN*、*PeerASN* が MSEE で使用されているかどうかと、これらの値がリンクされた PE-MSEE で使用されている値に対応しているかどうかも確認してください。 MSEE ルーター上の構成を変更するには、「[ExpressRoute 回線のルーティングの作成と変更を行う][CreatePeering]」を参照してください。
>
>

## <a name="validate-arp-between-microsoft-and-the-service-provider"></a>Microsoft とサービス プロバイダーの間の ARP を検証する
このセクションでは、PowerShell (クラシック) コマンドを使用します。 PowerShell Azure Resource Manager コマンドを使用している場合は、サブスクリプションに対する管理者/共同管理者のアクセス権があることを確認してください。 Azure Resource Manager のコマンドを使用したトラブルシューティングについては、「[Resource Manager デプロイ モデルでの ARP テーブルの取得][ARP]」のドキュメントをご覧ください。

> [!NOTE]
>ARP を取得するには、Azure Portal と Azure Resource Manager PowerShell コマンドの両方を使用できます。 Azure Resource Manager PowerShell コマンドでエラーが発生した場合、クラシック PowerShell コマンドも Azure Resource Manager ExpressRoute 回線で動作するため、クラシック PowerShell コマンドが動作するはずです。
>
>

プライベート ピアリングのプライマリ MSEE ルーターから ARP テーブルを取得するには、次のコマンドを使用します。

    Get-AzureDedicatedCircuitPeeringArpInfo -AccessType Private -Path Primary -ServiceKey "*********************************"

成功した場合のこのコマンドの応答の例は次のとおりです。

    ARP Info:

                 Age           Interface           IpAddress          MacAddress
                 113             On-Prem       10.0.0.1           e8ed.f335.4ca9
                   0           Microsoft       10.0.0.2           7c0e.ce85.4fc9

同様に、*Private*/*Public*/*Microsoft* ピアリングについて、*Primary*/*Secondary* パスで MSEE から ARP テーブルを確認できます。

次の例は、ピアリングに対するコマンドの応答が存在しないことを示します。

    ARP Info:
       
> [!NOTE]
> ARP テーブルでインターフェイスの IP アドレスが MAC アドレスにマップされていない場合は、次の情報を確認してください。
>1. MSEE-PR と MSEE の間のリンクに割り当てられた /30 サブネットの最初の IP アドレスが MSEE-PR のインターフェイスで使用されている場合は、 Azure では、常に MSEE の 2 番目の IP アドレスが使用されます。
>2. MSEE-PR と MSEE のペアで顧客 (S-Tag) とサービス (S-Tag) VLAN タグの両方が一致することを確認します。
>
>

## <a name="validate-bgp-and-routes-on-the-msee"></a>MSEE 上の BGP とルートを検証する
このセクションでは、PowerShell (クラシック) コマンドを使用します。 PowerShell Azure Resource Manager コマンドを使用している場合は、サブスクリプションに対する管理者/共同管理者のアクセス権があることを確認してください。

> [!NOTE]
>BGP 情報を取得するには、Azure Portal と Azure Resource Manager PowerShell コマンドのどちらも使用できます。 Azure Resource Manager PowerShell コマンドでエラーが発生した場合、クラシック PowerShell コマンドも Azure Resource Manager ExpressRoute 回線で動作するため、クラシック PowerShell コマンドが動作するはずです。
>
>

特定のルーティング コンテキストのルーティング テーブル (BGP 近隣ノード) の概要を取得するには、次のコマンドを使用します。

    Get-AzureDedicatedCircuitPeeringRouteTableSummary -AccessType Private -Path Primary -ServiceKey "*********************************"

応答の例は次のとおりです。

    Route Table Summary:

            Neighbor                   V                  AS              UpDown         StatePfxRcd
            10.0.0.1                   4                ####                8w4d                  50

上の例に示すように、このコマンドは、ルーティング コンテキストが確立される時間について確認するのに役立ちます。 また、このコマンドは、ピアリング ルーターでアドバタイズされるルート プレフィックスの数を示します。

> [!NOTE]
> 状態がアクティブまたはアイドルの場合は、割り当てられたプライマリ ピアとセカンダリ ピアのサブネットが、リンクされた PE-MSEE 上の構成と一致するかどうかを確認してください。 さらに、適切な *VlanId*、*AzureASN*、*PeerASN* が MSEE で使用されているかどうかと、これらの値がリンクされた PE-MSEE で使用されている値に対応しているかどうかも確認してください。 MD5 ハッシュが選択されている場合は、MSEE と PE-MSEE のペアで共有キーは同じものにする必要があります。 MSEE ルーター上の構成を変更するには、「[ExpressRoute 回線のルーティングの作成と変更を行う][CreatePeering]」を参照してください。
>
>

> [!NOTE]
> 特定の宛先に特定のピアリングを介して到達できない場合は、特定のピアリング コンテキストに属している MSEE のルーティング テーブルを確認してください。 一致するプレフィックス (NAT された ID の場合があります) がルーティング テーブルに存在する場合は、パスにファイアウォール/NSG/ACL があるかどうかと、それらがトラフィックを許可しているかどうかを確認してください。
>
>

特定の *Private* ルーティング コンテキストについて、*Primary* パスで MSEE からルーティング テーブル全体を取得するには、次のコマンドを使用します。

    Get-AzureDedicatedCircuitPeeringRouteTableInfo -AccessType Private -Path Primary -ServiceKey "*********************************"

コマンドの成功結果の例を次に示します。

    Route Table Info:

             Network             NextHop              LocPrf              Weight                Path
         10.1.0.0/16            10.0.0.1                                       0    #### ##### #####     
         10.2.0.0/16            10.0.0.1                                       0    #### ##### #####
    ...

同様に、*Private*/*Public*/*Microsoft* ピアリング コンテキストについて、*Primary*/*Secondary* パスで MSEE からルーティング テーブルを確認できます。

次の例は、ピアリングに対するコマンドの応答が存在しないことを示します。

    Route Table Info:

## <a name="check-the-traffic-statistics"></a>トラフィックの統計情報を確認する
ピアリング コンテキストのプライマリ パスとセカンダリ パスを組み合わせたトラフィックの統計情報 (入出力バイト数) を取得するには、次のコマンドを使用します。

    Get-AzureDedicatedCircuitStats -ServiceKey 97f85950-01dd-4d30-a73c-bf683b3a6e5c -AccessType Private

コマンドの出力例は次のとおりです。

    PrimaryBytesIn PrimaryBytesOut SecondaryBytesIn SecondaryBytesOut
    -------------- --------------- ---------------- -----------------
         240780020       239863857        240565035         239628474

ピアリングが存在しない場合のコマンドの出力例は次のとおりです。

    Get-AzureDedicatedCircuitStats : ResourceNotFound: Can not find any subinterface for peering type 'Public' for circuit '97f85950-01dd-4d30-a73c-bf683b3a6e5c' .
    At line:1 char:1
    + Get-AzureDedicatedCircuitStats -ServiceKey 97f85950-01dd-4d30-a73c-bf ...
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : CloseError: (:) [Get-AzureDedicatedCircuitStats], CloudException
        + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ExpressRoute.GetAzureDedicatedCircuitPeeringStatsCommand

## <a name="next-steps"></a>次の手順
詳細やヘルプについては、次のリンク先を確認してください。

- [Microsoft サポート][Support]
- [ExpressRoute 回線の作成と変更][CreateCircuit]
- [ExpressRoute 回線のルーティングの作成と変更を行う][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-expressroute-overview/expressroute-logical-diagram.png "論理 Express Route 接続"
[2]: ./media/expressroute-troubleshooting-expressroute-overview/portal-all-resources.png "[すべてのリソース] アイコン"
[3]: ./media/expressroute-troubleshooting-expressroute-overview/portal-overview.png "[概要] アイコン"
[4]: ./media/expressroute-troubleshooting-expressroute-overview/portal-circuit-status.png "ExpressRoute Essentials サンプルのスクリーンショット"
[5]: ./media/expressroute-troubleshooting-expressroute-overview/portal-private-peering.png "ExpressRoute Essentials サンプルのスクリーンショット"

<!--Link References-->
[Support]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[CreateCircuit]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager 
[CreatePeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager
[ARP]: https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager






