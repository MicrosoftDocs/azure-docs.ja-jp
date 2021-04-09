---
title: 'Azure ExpressRoute: 接続の検証 - トラブルシューティング ガイド'
description: このページでは、ExpressRoute 回線のエンド ツー エンド接続のトラブルシューティングと検証の手順について説明します。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: troubleshooting
ms.date: 10/31/2019
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: af4ef156cccded6afe2db09628446a6ffe1ad53a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92204641"
---
# <a name="verifying-expressroute-connectivity"></a>ExpressRoute 接続の検証
この記事は、ExpressRoute 接続の検証とトラブルシューティングに役立ちます。 ExpressRoute は、接続プロバイダーが一般的に提供するプライベート接続を介して、オンプレミス ネットワークを Microsoft クラウドへと拡張します。 ExpressRoute 接続には従来、次の 3 つの異なるネットワーク ゾーンが含まれます。

-   顧客のネットワーク
-   プロバイダーのネットワーク
-   Microsoft のデータセンター

> [!NOTE]
> ExpressRoute の直接接続モデル (10/100 Gbps の帯域幅で提供) では、顧客は Microsoft Enterprise Edge (MSEE) ルーターのポートに直接接続できます。 そのため、直接接続モデルでは、顧客と Microsoft のネットワーク ゾーンのみが存在します。
>


このドキュメントの目的は、接続の問題があるかどうか、ある場合はその場所を、ユーザーが判断できるようにすることです。 そして、その問題を解決するのに適切なチームに支援を依頼できるようにすることです。 問題の解決に Microsoft サポートが必要な場合は、[Microsoft サポート][Support]でサポート チケットを開いてください。

> [!IMPORTANT]
> このドキュメントは、単純な問題の診断と解決をサポートすることを目的としています。 Microsoft サポートに代わるものではありません。 記載されているガイダンスで問題を解決できない場合は、[Microsoft サポート][Support]でサポート チケットを開いてください。
>
>

## <a name="overview"></a>概要
次の図は、ExpressRoute を使用した顧客のネットワークから Microsoft ネットワークへの論理的な接続を示します。
[![1]][1]

上の図の中の番号は、重要なネットワーク ポイントを示しています。 ネットワーク ポイントはこの記事全体で、関連付けられた番号によって時折参照されています。 ExpressRoute の接続モデル (クラウド交換コロケーション、ポイント ツー ポイントのイーサネット接続、または Any-to-Any (IPVPN)) に応じて、ネットワーク ポイント 3 と 4 がスイッチ (レイヤー 2 デバイス) またはルーター (レイヤー 3 デバイス) になる場合があります。 直接接続モデルでは、ネットワーク ポイント 3 と 4 は存在しません。代わりに、CE (2) はダーク ファイバー経由で MSEE に直接接続されます。 図に示されている主なネットワーク ポイントは次のとおりです。

1.  顧客のコンピューティング デバイス (例: サーバーや PC)
2.  CE:顧客のエッジ ルーター 
3.  PE (CE に接続):顧客のエッジ ルーターに接続している、プロバイダーのエッジ ルーターまたはスイッチ。 このドキュメントでは PE-CE と呼びます。
4.  PE (MSEE に接続):MSEE に接続している、プロバイダーのエッジ ルーターまたはスイッチ。 このドキュメントでは PE-MSEE と呼びます。
5.  MSEE:Microsoft エンタープライズ エッジ (MSEE) ExpressRoute ルーター
6.  仮想ネットワーク (VNet) ゲートウェイ
7.  Azure VNet 上のコンピューティング デバイス

クラウド交換コロケーション、ポイント ツー ポイントのイーサネット、または直接接続のモデルが使用されている場合は、CE (2) が MSEE (5) との BGP ピアリングを確立します。 

Any-to-Any (IPVPN) 接続モデルが使用されている場合は、PE-MSEE (4) が MSEE (5) との BGP ピアリングを確立します。 PE-MSEE により Microsoft から受信したルートが IPVPN サービス プロバイダーのネットワークを介して顧客のネットワークに反映されます。

> [!NOTE]
>高可用性を実現するために、Microsoft は MSEE (5) と PE-MSEE (4) のペアの間に完全に冗長なパラレル接続を確立します。 顧客のネットワークと PE-CE ペアの間にも、完全に冗長なパラレル ネットワーク パスが推奨されます。 高可用性に関する詳細については、記事「[ExpressRoute を使用した高可用性のための設計][HA]」を参照してください。
>
>

次に、ExpressRoute 回線のトラブルシューティングの論理的ステップを示します。

* [回線のプロビジョニングと状態を確認する](#verify-circuit-provisioning-and-state)
  
* [ピアリング構成を検証する](#validate-peering-configuration)
  
* [ARP を検証する](#validate-arp)
  
* [MSEE 上の BGP とルートを検証する](#validate-bgp-and-routes-on-the-msee)
  
* [トラフィック フローを確認する](#confirm-the-traffic-flow)


## <a name="verify-circuit-provisioning-and-state"></a>回線のプロビジョニングと状態を確認する
ExpressRoute 回線をプロビジョニングすると、CE/PE-MSEE (2)/(4) と MSEE (5) の間に冗長なレイヤー 2 接続が確立します。 ExpressRoute 回線の作成、変更、プロビジョニング、検証を行う方法の詳細については、「[ExpressRoute 回線の作成と変更][CreateCircuit]」を参照してください。

>[!TIP]
>サービス キーは ExpressRoute 回線を一意に識別します。 ExpressRoute の問題のトラブルシューティングを行うために Microsoft または ExpressRoute パートナーによるサポートが必要な場合は、回線を簡単に特定できるようにサービス キーを提供してください。
>
>

### <a name="verification-via-the-azure-portal"></a>Azure Portal を使用した検証
Azure portal で、[ExpressRoute 回線] ブレードを開きます。 このブレードの ![3][3] セクションで、次のスクリーンショットに示されているように、ExpressRoute の要点が表示されます。

![4][4]    

ExpressRoute の [要点] にある *[回線の状態]* は Microsoft 側の回線の状態を示します。 *[Provider status (プロバイダーの状態)]* は、サービス プロバイダー側で回線が "*プロビジョニング済み/未プロビジョニング*" かどうかを示します。 

ExpressRoute 回線を運用可能にするには、 *[回線の状態]* が *[有効]* 、 *[Provider status (プロバイダーの状態)]* が *[プロビジョニング済み]* である必要があります。

> [!NOTE]
> ExpressRoute 回線を構成した後、 *[回線の状態]* が有効の状態になっていない場合は、[Microsoft サポート][Support]にお問い合わせください。 一方、 *[プロバイダーの状態]* が未プロビジョニングの状態の場合は、サービス プロバイダーにお問い合わせください。
>
>

### <a name="verification-via-powershell"></a>PowerShell を使用した検証
リソース グループ内のすべての ExpressRoute 回線を一覧表示するには、次のコマンドを使用します。

```azurepowershell
Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG"
```

>[!TIP]
>リソース グループの名前を探している場合は、コマンド *Get-AzResourceGroup* を使用して、サブスクリプション内のすべてのリソース グループを一覧表示することで取得できます。
>


リソース グループ内の特定の ExpressRoute 回線を選択するには、次のコマンドを使用します。

```azurepowershell
Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
```

応答のサンプルは次のとおりです。

```output
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
```

ExpressRoute 回線が運用可能かどうかを確認するには、特に次のフィールドに注目してください。

```output
CircuitProvisioningState         : Enabled
ServiceProviderProvisioningState : Provisioned
```

> [!NOTE]
> ExpressRoute 回線を構成した後、 *[回線の状態]* が有効の状態になっていない場合は、[Microsoft サポート][Support]にお問い合わせください。 一方、 *[プロバイダーの状態]* が未プロビジョニングの状態の場合は、サービス プロバイダーにお問い合わせください。
>
>

## <a name="validate-peering-configuration"></a>ピアリング構成を検証する
サービス プロバイダーが ExpressRoute 回線のプロビジョニングを完了すると、CE/MSEE-PE (2)/(4) と MSEE (5) の間の ExpressRoute 回線で複数の eBGP ベースのルーティング構成を作成できます。 各 ExpressRoute 回線では、次を有効にすることができます: Azure プライベート ピアリング (Azure 内のプライベート仮想ネットワークへのトラフィック) と Microsoft ピアリング (PaaS と SaaS のパブリック エンドポイントへのトラフィック) です。 ルーティング構成の作成と変更方法の詳細については、「[ExpressRoute 回線のルーティングの作成と変更を行う][CreatePeering]」を参照してください。

### <a name="verification-via-the-azure-portal"></a>Azure Portal を使用した検証

> [!NOTE]
> IPVPN 接続モデルでは、サービス プロバイダーがピアリング (レイヤー 3 サービス) を構成する責任を担います。 このようなモデルでは、サービス プロバイダーがピアリングを構成した後、ポータルのピアリングが空の場合、ポータルの更新ボタンを使用して回線の構成を更新します。 この操作により、現在のルーティング構成が回線からプルされます。 
>

Azure portal では、ExpressRoute 回線のピアリング状態を [ExpressRoute 回線] ブレードで確認できます。 このブレードの ![3][3] セクションで、次のスクリーンショットに示されているように、ExpressRoute のピアリングが表示されます。

![5][5]

上の例では、表示されているとおり、Azure プライベート ピアリングはプロビジョニングされていますが、Azure パブリック ピアリングと Microsoft ピアリングはプロビジョニングされていません。 正常にプロビジョニングされたピアリング コンテキストでは、プライマリとセカンダリのポイント ツー ポイントのサブネットも表示されます。 /30 サブネットは、MSEE と CE/PE-MSEE のインターフェイス IP アドレスに使用されます。 この一覧では、プロビジョニングされているピアリングについて、最後に構成を変更したユーザーも表示されます。 

> [!NOTE]
> ピアリングの有効化が失敗する場合は、割り当てられたプライマリ サブネットとセカンダリ サブネットが、リンクされた CE/PE-MSEE 上の構成と一致するかどうかを確認してください。 さらに、適切な *VlanId*、*AzureASN*、*PeerASN* が MSEE で使用されているかどうかと、これらの値がリンクされた CE/PE-MSEE で使用されている値に対応しているかどうかも確認してください。 MD5 ハッシュが選択されている場合は、MSEE と PE-MSEE/CE のペアで共有キーは同じものにする必要があります。 以前に構成した共有キーは、セキュリティ上の理由で表示されません。 MSEE ルーター上のこれらの構成のいずれかを変更する必要がある場合は、「[ExpressRoute 回線のピアリングの作成と変更を行う][CreatePeering]」を参照してください。  
>

> [!NOTE]
> インターフェイスに割り当てられた /30 サブネットでは、サブネットの 2 番目の使用可能な IP アドレスが MSEE インターフェイス用に選択されます。 そのため、サブネットの最初の使用可能な IP アドレスが、ピアリングされた CE/PE-MSEE で確実に割り当てられているようにします。
>


### <a name="verification-via-powershell"></a>PowerShell を使用した検証
Azure プライベート ピアリング構成の詳細を取得するには、次のコマンドを使用します。

```azurepowershell
$ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt
```

正常に構成されたプライベート ピアリングの応答のサンプルは次のとおりです。

```output
Name                       : AzurePrivatePeering
Id                         : /subscriptions/***************************/resourceGroups/Test-ER-RG/providers/***********/expressRouteCircuits/Test-ER-Ckt/peerings/AzurePrivatePeering
Etag                       : W/"################################"
PeeringType                : AzurePrivatePeering
AzureASN                   : 12076
PeerASN                    : 123##
PrimaryPeerAddressPrefix   : 172.16.0.0/30
SecondaryPeerAddressPrefix : 172.16.0.4/30
PrimaryAzurePort           : 
SecondaryAzurePort         : 
SharedKey                  : 
VlanId                     : 200
MicrosoftPeeringConfig     : null
ProvisioningState          : Succeeded
```

 正常に有効にされたピアリング コンテキストでは、プライマリとセカンダリのアドレス プレフィックスが表示されます。 /30 サブネットは、MSEE と CE/PE-MSEE のインターフェイス IP アドレスに使用されます。

Azure パブリック ピアリング構成の詳細を取得するには、次のコマンドを使用します。

```azurepowershell
$ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt
```

Microsoft ピアリング構成の詳細を取得するには、次のコマンドを使用します。

```azurepowershell
$ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
Get-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt
```

ピアリングが構成されていない場合は、エラー メッセージが表示されます。 記述されているピアリング (この例では Azure パブリック ピアリング) が回線内で構成されていない場合の応答のサンプルは次のとおりです。

```azurepowershell
Get-AzExpressRouteCircuitPeeringConfig : Sequence contains no matching element
At line:1 char:1
    + Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering ...
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : CloseError: (:) [Get-AzExpr...itPeeringConfig], InvalidOperationException
        + FullyQualifiedErrorId : Microsoft.Azure.Commands.Network.GetAzureExpressRouteCircuitPeeringConfigCommand
```

> [!NOTE]
> ピアリングの有効化が失敗する場合は、割り当てられたプライマリ サブネットとセカンダリ サブネットが、リンクされた CE/PE-MSEE 上の構成と一致するかどうかを確認してください。 さらに、適切な *VlanId*、*AzureASN*、*PeerASN* が MSEE で使用されているかどうかと、これらの値がリンクされた CE/PE-MSEE で使用されている値に対応しているかどうかも確認してください。 MD5 ハッシュが選択されている場合は、MSEE と PE-MSEE/CE のペアで共有キーは同じものにする必要があります。 以前に構成した共有キーは、セキュリティ上の理由で表示されません。 MSEE ルーター上のこれらの構成のいずれかを変更する必要がある場合は、「[ExpressRoute 回線のピアリングの作成と変更を行う][CreatePeering]」を参照してください。  
>
>

> [!NOTE]
> インターフェイスに割り当てられた /30 サブネットでは、サブネットの 2 番目の使用可能な IP アドレスが MSEE インターフェイス用に選択されます。 そのため、サブネットの最初の使用可能な IP アドレスが、ピアリングされた CE/PE-MSEE で確実に割り当てられているようにします。
>

## <a name="validate-arp"></a>ARP を検証する

ARP テーブルから、特定のピアリングに関する IP アドレスと MAC アドレスのマッピングを得ることができます。 ExpressRoute 回線のピアリングで使用される ARP テーブルは、プライマリ インターフェイスとセカンダリ インターフェイスのそれぞれに関して次の情報を提供します。
* オンプレミス ルーター インターフェイスの IP アドレスから MAC アドレスへのマッピング
* ExpressRoute ルーター インターフェイスの IP アドレスから MAC アドレスへのマッピング
* レイヤー 2 の構成を検証したり、レイヤー 2 の基本的な接続の問題をトラブルシューティングしたりする際に、ARP テーブルのマッピングの経過時間を役立てることができます。


ExpressRoute ピアリングの ARP テーブルの表示方法、およびこの情報を使用してレイヤー 2 の接続に関する問題のトラブルシューティングを行う方法については、「[Resource Manager デプロイ モデルでの ARP テーブルの取得][ARP]」ドキュメントを参照してください。


## <a name="validate-bgp-and-routes-on-the-msee"></a>MSEE 上の BGP とルートを検証する

*Private* ルーティング コンテキストについて、*Primary* パスで MSEE からルーティング テーブルを取得するには、次のコマンドを使用します。

```azurepowershell
Get-AzExpressRouteCircuitRouteTable -DevicePath Primary -ExpressRouteCircuitName ******* -PeeringType AzurePrivatePeering -ResourceGroupName ****
```

応答の例は次のとおりです。

```output
Network : 10.1.0.0/16
NextHop : 10.17.17.141
LocPrf  : 
Weight  : 0
Path    : 65515

Network : 10.1.0.0/16
NextHop : 10.17.17.140*
LocPrf  : 
Weight  : 0
Path    : 65515

Network : 10.2.20.0/25
NextHop : 172.16.0.1
LocPrf  : 
Weight  : 0
Path    : 123##
```

> [!NOTE]
> MSEE と CE/PE-MSEE の間の eBGP ピアリングの状態がアクティブまたはアイドルの場合は、割り当てられたプライマリ ピアとセカンダリ ピアのサブネットが、リンクされた CE/PE-MSEE 上の構成と一致するかどうかを確認してください。 さらに、適切な *VlanId*、*AzureASN*、*PeerASN* が MSEE で使用されているかどうかと、これらの値がリンクされた PE-MSEE/CE で使用されている値に対応しているかどうかも確認してください。 MD5 ハッシュが選択されている場合は、MSEE と CE/PE-MSEE のペアで共有キーは同じものにする必要があります。 MSEE ルーター上のこれらの構成のいずれかを変更する必要がある場合は、「[ExpressRoute 回線のピアリングの作成と変更を行う][CreatePeering]」を参照してください。
>


> [!NOTE]
> 特定の宛先にピアリングを介して到達できない場合は、対応するピアリング コンテキストの MSEE のルーティング テーブルを確認してください。 一致するプレフィックス (NAT された IP の場合があります) がルーティング テーブルに存在する場合は、トラフィックをブロックしているファイアウォール/NSG/ACL がパスにあるかどうかを確認してください。
>


次の例は、ピアリングに対するコマンドの応答が存在しないことを示します。

```azurepowershell
Get-AzExpressRouteCircuitRouteTable : The BGP Peering AzurePublicPeering with Service Key ********************* is not found.
StatusCode: 400
```

## <a name="confirm-the-traffic-flow"></a>トラフィック フローを確認する
ピアリング コンテキストのプライマリ パスとセカンダリ パスを組み合わせたトラフィックの統計情報 (入出力バイト数) を取得するには、次のコマンドを使用します。

```azurepowershell
Get-AzExpressRouteCircuitStats -ResourceGroupName $RG -ExpressRouteCircuitName $CircuitName -PeeringType 'AzurePrivatePeering'
```

コマンドの出力例は次のとおりです。

```output
PrimaryBytesIn PrimaryBytesOut SecondaryBytesIn SecondaryBytesOut
-------------- --------------- ---------------- -----------------
     240780020       239863857        240565035         239628474
```

ピアリングが存在しない場合のコマンドの出力例は次のとおりです。

```azurepowershell
Get-AzExpressRouteCircuitRouteTable : The BGP Peering AzurePublicPeering with Service Key ********************* is not found.
StatusCode: 400
```

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
[CreateCircuit]: ./expressroute-howto-circuit-portal-resource-manager.md
[CreatePeering]: ./expressroute-howto-routing-portal-resource-manager.md
[ARP]: ./expressroute-troubleshooting-arp-resource-manager.md
[HA]: ./designing-for-high-availability-with-expressroute.md
[DR-Pvt]: ./designing-for-disaster-recovery-with-expressroute-privatepeering.md