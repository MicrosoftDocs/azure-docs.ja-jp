---
title: "ネットワーク セキュリティ グループのトラブルシューティング - PowerShell | Microsoft Docs"
description: "Azure Resource Manager のデプロイメント モデルで、Azure PowerShell を使用してネットワーク セキュリティ グループをトラブルシューティングする方法について説明します。"
services: virtual-network
documentationcenter: na
author: AnithaAdusumilli
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: 4c732bb7-5cb1-40af-9e6d-a2a307c2a9c4
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/23/2016
ms.author: anithaa
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 13ffe90e03dbe99366fb4f6e2788ba7a3c968a30


---
# <a name="troubleshoot-network-security-groups-using-azure-powershell"></a>Azure PowerShell を使用したネットワーク セキュリティ グループのトラブルシューティング
> [!div class="op_single_selector"]
> * [Azure ポータル](virtual-network-nsg-troubleshoot-portal.md)
> * [PowerShell](virtual-network-nsg-troubleshoot-powershell.md)
> 
> 

この記事では、仮想マシン (VM) にネットワーク セキュリティ グループ (NSG) を構成している場合に、VM の接続の問題が発生したときのトラブルシューティングに役立つ NSG の診断機能の概要を説明します。

NSG では、仮想マシン (VM) を出入りするトラフィックのタイプを制御できます。 NSG は、Azure Virtual Network (VNet)、ネットワーク インターフェイス (NIC)、または両方のサブネットに適用できます。 NIC に効果的に規則を適用するには、NSG 内にある規則の集合体を NIC と接続しているサブネットに適用します。 場合によっては、これらのNSG 全体の規則が競合し、VM のネットワーク接続に影響を及ぼすことがあります。  

VM の NIC に適用されると、有効なセキュリティ規則を NSG からすべて表示できます。 この記事では、Azure Resource Manager デプロイメント モデルで、これらの規則を使用して VM の接続問題をトラブルシューティングする方法について説明します。 VNet と NSG の概念については、[仮想ネットワーク](virtual-networks-overview.md)と[ネットワーク セキュリティ グループ](virtual-networks-nsg.md)の概要に関する記事を参照してください。

## <a name="using-effective-security-rules-to-troubleshoot-vm-traffic-flow"></a>有効なセキュリティ規則を使用した VM トラフィック フローのトラブルシューティング
次のシナリオでは、一般的な接続の問題の例を示します。

*VM1* という名前の VM は、*WestUS-VNet1* という名前の VNet 内にある *Subnet1* という名前のサブネットの一部です。 TCP ポート 3389 経由で RDP を使用して、VM への接続に失敗しました。 NSG は、*VM1-NIC1* という NIC と *Subnet1* というサブネットの両方に適用されています。 TCP ポート 3389 へのトラフィックは、ネットワーク インターフェイス「 *VM1-NIC1*」に関連付けられている NSG で許可されていますが、VM1 のポート 3389 への ping が失敗します。

この例では TCP ポート 3389 を使用していますが、次の手順は任意のポート経由での受信/送信接続の失敗の確認に使用できます。

## <a name="detailed-troubleshooting-steps"></a>詳細なトラブルシューティングの手順
VM の NSG のトラブルシューティングを行うには、次の手順を実行します。

1. Azure PowerShell セッションを開始し、Azure にログインします。 Azure PowerShell の使用に慣れていない場合は、「 [Azure PowerShell のインストールおよび構成方法](/powershell/azureps-cmdlets-docs) 」の記事をご覧ください。
2. 次のコマンドを入力すると、リソース グループ *RG1* の *VM1-NIC1* という名前の NIC に適用されているすべての NSG 規則が返されます。
   
        Get-AzureRmEffectiveNetworkSecurityGroup -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1
   
   > [!TIP]
   > NIC の名前がわからない場合は、次のコマンドを入力して、リソース グループ内のすべての NIC の名前を取得します。 
   > 
   > `Get-AzureRmNetworkInterface -ResourceGroupName RG1 | Format-Table Name`
   > 
   > 
   
    次のテキストは、 *VM1 NIC1* NIC に返される、有効な規則の出力のサンプルです。
   
        NetworkSecurityGroup   : {
                                   "Id": "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/networkSecurityGroups/VM1-NIC1-NSG"
                                 }
        Association            : {
                                   "NetworkInterface": {
                                     "Id": "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/networkInterfaces/VM1-NIC1"
                                   }
                                 }
        EffectiveSecurityRules : [
                                 {
                                 "Name": "securityRules/allowRDP",
                                 "Protocol": "Tcp",
                                 "SourcePortRange": "0-65535",
                                 "DestinationPortRange": "3389-3389",
                                 "SourceAddressPrefix": "Internet",
                                 "DestinationAddressPrefix": "0.0.0.0/0",
                                 "ExpandedSourceAddressPrefix": [… ],
                                 "ExpandedDestinationAddressPrefix": [],
                                 "Access": "Allow",
                                 "Priority": 1000,
                                 "Direction": "Inbound"
                                 },
                                 {
                                 "Name": "defaultSecurityRules/AllowVnetInBound",
                                 "Protocol": "All",
                                 "SourcePortRange": "0-65535",
                                 "DestinationPortRange": "0-65535",
                                 "SourceAddressPrefix": "VirtualNetwork",
                                 "DestinationAddressPrefix": "VirtualNetwork",
                                 "ExpandedSourceAddressPrefix": [
                                  "10.9.0.0/16",
                                  "168.63.129.16/32",
                                  "10.0.0.0/16",
                                  "10.1.0.0/16"
                                  ],
                                 "ExpandedDestinationAddressPrefix": [
                                  "10.9.0.0/16",
                                  "168.63.129.16/32",
                                  "10.0.0.0/16",
                                  "10.1.0.0/16"
                                  ],
                                  "Access": "Allow",
                                  "Priority": 65000,
                                  "Direction": "Inbound"
                                  },…
                         ]
   
        NetworkSecurityGroup   : {
                                   "Id": 
                                 "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/networkSecurityGroups/Subnet1-NSG"
                                 }
        Association            : {
                                   "Subnet": {
                                     "Id": 
                                 "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/virtualNetworks/WestUS-VNet1/subnets/Subnet1"
                                 }
                                 }
        EffectiveSecurityRules : [
                                 {
                                "Name": "securityRules/denyRDP",
                                "Protocol": "Tcp",
                                "SourcePortRange": "0-65535",
                                "DestinationPortRange": "3389-3389",
                                "SourceAddressPrefix": "Internet",
                                "DestinationAddressPrefix": "0.0.0.0/0",
                                "ExpandedSourceAddressPrefix": [
                                   ... ],
                                "ExpandedDestinationAddressPrefix": [],
                                "Access": "Deny",
                                "Priority": 1000,
                                "Direction": "Inbound"
                                },
                                {
                                "Name": "defaultSecurityRules/AllowVnetInBound",
                                "Protocol": "All",
                                "SourcePortRange": "0-65535",
                                "DestinationPortRange": "0-65535",
                                "SourceAddressPrefix": "VirtualNetwork",
                                "DestinationAddressPrefix": "VirtualNetwork",
                                "ExpandedSourceAddressPrefix": [
                                "10.9.0.0/16",
                                "168.63.129.16/32",
                                "10.0.0.0/16",
                                "10.1.0.0/16"
                                ],
                                "ExpandedDestinationAddressPrefix": [
                                "10.9.0.0/16",
                                "168.63.129.16/32",
                                "10.0.0.0/16",
                                "10.1.0.0/16"
                                ],
                                "Access": "Allow",
                                "Priority": 65000,
                                "Direction": "Inbound"
                                },...
                                ]
   
    出力内の次の情報にご注意ください。
   
   * **NetworkSecurityGroup** セクションが 2 つあります。1 つは、サブネット (*Subnet1*) に関連付けられたもので、もう 1 つは NIC (*VM1-NIC1*) に関連付けられたものです。 この例では、それぞれに NSG が適用されています。
   * **Association** には、特定の NSG が関連付けられているリソース (サブネットまたは NIC) が表示されます。 このコマンドを実行する直前に NSG リソースが移動されたり、関連付けが解除されたりした場合は、コマンドの出力にその変更が反映されるまで数秒間待たなければならないことがあります。 
   * *defaultSecurityRules*から始まる規則名: NSG を作成すると、その規則内にいくつかの既定のセキュリティ規則が作成されます。 既定の規則は削除できませんが、優先順位の高い規則で上書きできます。
     NSG の既定の規則の詳細については、「 [NSG の概要](virtual-networks-nsg.md#default-rules) 」の記事をご覧ください。
   * **ExpandedAddressPrefix** は、NSG の既定のタグのアドレス プレフィックスを拡張します。 タグは、複数のアドレス プレフィックスを表します。 タグの拡張は、特定のアドレス プレフィックスとの VM 接続のトラブルシューティングを行う場合に便利です。 たとえば、VNET ピアリングを使用する場合、VIRTUAL_NETWORK タグを拡張すると、前の出力の際にピアリングされた VNet プレフィックスが表示されます。
     
     > [!NOTE]
     > コマンドでは、NSG がサブネットか NIC のいずれか、またはその両方に関連付けられている場合に、有効な規則のみが表示されます。 VM には、異なる NSG が適用された複数の NIC が存在することがあります。 トラブルシューティングを行うときは、それぞれの NIC に対してコマンドを実行します。
     > 
     > 
3. 多数の NSG 規則に対して簡単にフィルター処理を行うには次のコマンドを入力してさらにトラブルシューティングを行います。 
   
        $NSGs = Get-AzureRmEffectiveNetworkSecurityGroup -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1
        $NSGs.EffectiveSecurityRules | Sort-Object Direction, Access, Priority | Out-GridView
   
    RDP トラフィック (TCP ポート 3389) のフィルターは、次の図に示すように、グリッド ビューに適用されます。
   
    ![規則の一覧](./media/virtual-network-nsg-troubleshoot-powershell/rules.png)
4. グリッド ビューで示すように、RDP の許可規則と拒否規則の両方があります。 手順 2 の出力から、サブネットに適用されている NSG に *DenyRDP* 規則があることがわかります。 受信規則では、サブネットに適用される NSG が最初に処理されます。 一致するものが見つかると、ネットワーク インターフェイスに適用される NSG は処理されません。 この場合、サブネットの *DenyRDP* 規則によって VM (**VM1**) への RDP がブロックされます。
   
   > [!NOTE]
   > VM には複数の NIC 接続されており、 それぞれの接続先のサブネットが異なる場合があります。 前の手順のコマンドは NIC に対して実行するため、接続エラーが発生した NIC を指定する必要があります。 NIC がわからない場合は、VM に接続されている各 NIC に対してコマンドを実行できます。
   > 
   > 
5. VM1 に RDP を実行するには、**Subnet1-NSG** という NSG の *Deny RDP (3389)* の規則を *Allow RDP(3389)* に変更します。 VM への RDP 接続を開始するか、PsPing ツールを使用して、TCP ポート 3389 が開いていることを確認します。 PsPing の詳細については、 [PsPing のダウンロード ページ](https://technet.microsoft.com/sysinternals/psping.aspx)
   
    次のコマンドの出力にある情報を使用することによって、NSG から規則を削除できます。
   
        Get-Help *-AzureRmNetworkSecurityRuleConfig

## <a name="considerations"></a>考慮事項
接続問題のトラブルシューティングを行う場合は、次の点を検討してください。

* 既定の NSG 規則は、インターネットからの着信アクセスをブロックし、VNet の受信トラフィックのみを許可します。 必要に応じて、インターネットからの着信アクセスを許可する規則を追加します。
* VM のネットワーク接続の失敗を引き起こす NSG セキュリティ規則がない場合は、他の原因が考えられます。
  * VM のオペレーティング システム内で実行されているファイアウォール ソフトウェア
  * 仮想アプライアンスまたはオンプレミスのトラフィック用に構成されたルート。 インターネット トラフィックは、強制トンネリングを使用してオンプレミスにリダイレクトできます。 インターネットから VM への RDP/SSH 接続は、オンプレミスのネットワーク ハードウェアがこのトラフィックを処理する方法によっては、この設定では機能しない場合があります。 VM を出入りするトラフィックのフローを妨げているルートの問題を診断する方法については、「 [Troubleshooting Routes (ルートのトラブルシューティング)](virtual-network-routes-troubleshoot-powershell.md) 」の記事をご覧ください。 
* 既定では、VNet をピアリングした場合、VIRTUAL_NETWORK タグはピアリングされている VNet のプレフィックスを含めるように自動的に拡張されます。 これらのプレフィックスは、 **ExpandedAddressPrefix** の一覧で表示でき、VNet ピアリングの接続に関連する問題をトラブルシューティングする際に使用できます。 
* 有効なセキュリティの規則は、VM の NIC やサブネットに関連付けられている NSG がある場合のみ表示されます。 
* NIC やサブネットに関連付けられている NSG がなく、VM にパブリック IP アドレスを割り当てている場合は、着信/発信アクセス用にすべてのポートが開きます。 VM にパブリック IP アドレスがある場合は、NIC またはサブネットに NSG を適用することを強くお勧めします。  




<!--HONumber=Dec16_HO2-->


