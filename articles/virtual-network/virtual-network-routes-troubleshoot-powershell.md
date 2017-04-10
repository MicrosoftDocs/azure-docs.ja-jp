---
title: "ルートのトラブルシューティング - PowerShell | Microsoft Docs"
description: "Azure Resource Manager のデプロイメント モデルで、Azure PowerShell を使用してルートをトラブルシューティングする方法について説明します。"
services: virtual-network
documentationcenter: na
author: AnithaAdusumilli
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: bf7dc5e7-9399-460e-8e0d-8992dbed98a6
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/23/2016
ms.author: anithaa
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: b568a9bea9679a9edeb708a5f7fcc6d68854574f


---
# <a name="troubleshoot-routes-using-azure-powershell"></a>Azure PowerShell を使用してルートのトラブルシューティングを行う
> [!div class="op_single_selector"]
> * [Azure ポータル](virtual-network-routes-troubleshoot-portal.md)
> * [PowerShell](virtual-network-routes-troubleshoot-powershell.md)
> 
> 

Azure 仮想マシン (VM) とのネットワーク接続に問題が発生した場合、VM トラフィック フローがルートの影響を受けている場合があります。 この記事では、詳細なトラブルシューティングを行うためのルートの診断機能の概要を説明します。

ルート テーブルはサブネットに関連付けられ、そのサブネットのすべてのネットワーク インターフェイス (NIC) に対して有効になります。 各ネットワーク インターフェイスには、次の種類のルートを適用できます。

* **システム ルート:** 既定では、Azure Virtual Network (VNet) で作成されたすべてのサブネットが、ローカル VNet トラフィック、VPN ゲートウェイ経由のオンプレミスのトラフィック、インターネット トラフィックを利用できるシステム ルート テーブルを持ちます。 ピアリングされる VNet にもシステム ルートが存在します。
* **BGP ルート:** ExpressRoute またはサイト間 VPN 接続経由のネットワーク インターフェイスに反映されます。 BGP ルーティングの詳細については、「[Azure VPN ゲートウェイを使用した BGP の概要](../vpn-gateway/vpn-gateway-bgp-overview.md)」と「[ExpressRoute の技術概要](../expressroute/expressroute-introduction.md)」の記事を参照してください。
* **ユーザー定義のルート (UDR):** ネットワーク仮想アプライアンスまたは強制トンネリングを使用して、オンプレミス ネットワークにサイト間 VPN 経由でトラフィックをルートしている場合は、サブネット ルート テーブルにユーザー定義のルート (UDR) が関連付けられていることがあります。 UDR に慣れていない場合は、「 [ユーザー定義のルート](virtual-networks-udr-overview.md#user-defined-routes) 」の記事をご覧ください。

ネットワーク インターフェイスに適用できるさまざまなルートを使用する場合、有効な集約ルートの特定が難しくなることがあります。 VM ネットワーク接続をトラブルシューティングするため、Azure Resource Manager デプロイメント モデルのネットワーク インターフェイスのすべての有効なルートを表示できます。

## <a name="using-effective-routes-to-troubleshoot-vm-traffic-flow"></a>有効なルートを使用した VM トラフィック フローのトラブルシューティング
この記事では、例として次のシナリオを使用し、ネットワーク インターフェイスの有効な規則のトラブルシューティング方法を説明します。

VNet (*VNet1*、プレフィックス: 10.9.0.0/16) に接続する VM (*VM1*) が、新たにピアリングされた VNet (*VNet3*、プレフィックス 10.10.0.0/16) の VM (VM3) に接続できません。 VM に接続する VM1-NIC1 ネットワーク インターフェイスには UDR や BGP ルートが適用されておらず、システム ルートのみが適用されています。

この記事では、Azure Resource Management デプロイ モデルの有効なルート機能を使用した、接続エラーの原因の特定方法を説明します。
この例ではシステム ルートのみを使用していますが、次の手順は任意の種類のルート経由での受信/送信接続の失敗の確認に使用できます。

> [!NOTE]
> VM に複数の NIC が接続されているときは、各 NIC の有効なルートを確認して、VM とのネットワーク接続の問題を診断します。
> 
> 

### <a name="view-effective-routes-for-a-virtual-machine"></a>仮想マシンの有効なルートを表示する
VM に適用されている集約ルートを表示するには、次の手順を実行します。

### <a name="view-effective-routes-for-a-network-interface"></a>ネットワーク インターフェイスの有効なルートを表示する
ネットワーク インターフェイスに適用されている集約ルートを表示するには、次の手順を実行します。

1. Azure PowerShell セッションを開始し、Azure にログインします。 Azure PowerShell の使用に慣れていない場合は、「 [Azure PowerShell のインストールおよび構成方法](/powershell/azureps-cmdlets-docs) 」の記事をご覧ください。
2. 次のコマンドを使用すると、リソース グループ *RG1* の *VM1-NIC1* という名前のネットワーク インターフェイスに適用されているすべてのルートが返されます。
   
       Get-AzureRmEffectiveRouteTable -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1
   
   > [!TIP]
   > ネットワーク インターフェイスの名前がわからない場合は、次のコマンドを入力して、リソース グループ内のすべてのネットワーク インターフェイスの名前を取得します。*
   > 
   > 
   
       Get-AzureRmNetworkInterface -ResourceGroupName RG1 | Format-Table Name
   
   次の出力は、NIC が接続されているサブネットに適用されている各ルートの出力に似ています。
   
       Name :
       State : Active
       AddressPrefix : {10.9.0.0/16}
       NextHopType : VNetLocal
       NextHopIpAddress : {}
   
       Name :
       State : Active
       AddressPrefix : {0.0.0.0/16}
       NextHopType : Internet
       NextHopIpAddress : {}
   
   出力内の次の情報にご注意ください。
   
   * **Name**: 明示的に指定しない場合、ユーザー定義のルートの有効なルートの名前が空になることがあります。 
   * **State**: 有効なルートの状態を示します。 「Active」か「Invalid」のいずれかの値になります。
   * **AddressPrefixes**: CIDR 表記で有効なルートのアドレス プレフィックスを指定します。 
   * **nextHopType**: 特定のルートの次のホップを示します。 *VirtualAppliance*、*Internet*、*VNetLocal*、*VNetPeering*、*Null* のいずれかの値になります。 UDR の *nextHopType* の値が **Null** の場合、ルートが無効であることを表します。 たとえば、 **nextHopType** が *VirtualAppliance* で、ネットワーク仮想アプライアンス VM がプロビジョニング/実行中の状態でない場合です。 **nextHopType** が *VPNGateway* で、特定の VNet のゲートウェイがプロビジョニング/実行中の状態でない場合も、ルートが無効になることがあります。
   * **NextHopIpAddress**: 有効なルートの次のホップの IP アドレスを指定します。
   
   次のコマンドは、よりわかりやすいテーブルでルートを返します。
   
       Get-AzureRmEffectiveRouteTable -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1 | Format-Table
   
   次の出力は、前述のシナリオで受信した出力の一部です。
   
       Name State AddressPrefix NextHopType NextHopIpAddress
       ---- ----- ------------- ----------- ----------------
       Active {10.9.0.0/16} VnetLocal {}
       Active {0.0.0.0/0} Internet {}
3. 前の手順の出力には、*WestUS-VNet3* VNet (プレフィックス 10.9.0.0/16)** から *WestUS-VNet1* (プレフィックス 10.10.0.0/16) へのルートはリストされていません。 次の図に示すように、*WestUS-VNet3* VNet を使用した VNet ピアリング リンクは *Disconnected* 状態です。
   
    ![](./media/virtual-network-routes-troubleshoot-portal/image4.png)
   
    ピアリングの双方向リンクが切断されています。VM1 が *WestUS-VNet3* VNet の VM3 に接続できないのはそのためです。 *WestUS-VNet1* と *WestUS-VNet3* VNet に双方向の VNet のピアリング リンクをもう一度設定します。 VNet のピアリング リンクが正常に確立されると、次のような出力が返されます。
   
        Name State AddressPrefix NextHopType NextHopIpAddress
        ---- ----- ------------- ----------- ----------------
        Active {10.9.0.0/16} VnetLocal {}
        Active {10.10.0.0/16} VNetPeering {}
        Active {0.0.0.0/0} Internet {}
   
    問題が特定できたら、ルートやルート テーブルを追加、削除、変更できます。 この操作のためのコマンドの一覧を表示するには、次のコマンドを入力します。
   
        Get-Help *-AzureRmRouteConfig

## <a name="considerations"></a>考慮事項
返されたルートの一覧を確認する際は、次の点にご注意ください。

* UDR、BGP、システム ルートにおいては、ルーティングは最長プレフィックス一致 (LPM) に基づいて行われます。 同じ LPM マッチの複数のルートが存在する場合は、そのルートが検出された経緯に応じて次の順序でルートが選択されます。
  
  * ユーザー定義のルート
  * BGP のルート
  * システム (既定) のルート
    
    有効なルートを使用すると、使用可能なすべてのルートに対して LPM マッチの有効なルートのみが表示されます。 特定の NIC に対してルートが実際に評価される方法を表示することによって、VM との接続に影響を与える可能性のある特定のルートのトラブルシューティングをより簡単に行うことができます。
* UDR があり、**nextHopType** の *VirtualAppliance* を使用して、ネットワーク仮想アプライアンス (NVA) にトラフィックを送信している場合は、トラフィックを受信する NVA で IP 転送が有効であることを確認してください。有効になっていない場合、パケットが破棄されます。 
* 強制トンネリングが有効になっている場合、発信されるインターネット トラフィックはオンプレミスにルーティングされます。 オンプレミスによるトラフィックの処理方法によっては、インターネットから VM への RDP/SSH は、この設定では機能しない場合があります。 
  強制トンネリングは、次の方法で有効にできます。
  * VPN ゲートウェイとして nextHopType を使用するユーザー定義のルート (UDR) を設定して、サイト間 VPN を使用する場合
  * 既定のルートが BGP 経由で提供される場合
* VNet のピアリング トラフィックが正常に機能するためには、ピアリングされる VNet のプレフィックスの範囲に **nextHopType** *VNetPeering* を使用するシステム ルートを追加する必要があります。 そのようなルートが存在せず、VNet ピアリングのリンクに問題がないと考えられる場合:
  * 新しく確立されたピアリングのリンクの場合は、数秒待ってから再度実行してください。 サブネット内のすべてのネットワーク インターフェイスにルートを反映させるのに時間がかかる場合がります。
  * ネットワーク セキュリティ グループ (NSG) の規則が、トラフィック フローに影響を与える可能性があります。 詳細については、「 [ネットワーク セキュリティ グループのトラブルシューティング](virtual-network-nsg-troubleshoot-powershell.md) 」をご覧ください。




<!--HONumber=Dec16_HO2-->


