---
title: 仮想マシン ネットワーク トラフィック フィルターの問題を診断する | Microsoft Docs
description: 仮想マシンの有効なセキュリティ規則を表示することによって、仮想マシンのネットワーク トラフィック フィルターに関する問題を診断する方法を説明します。
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: a54feccf-0123-4e49-a743-eb8d0bdd1ebc
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2018
ms.author: kumud
ms.openlocfilehash: 73562d8d32f265fa43ca80d2f8d4f84b1b631ec6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98223671"
---
# <a name="diagnose-a-virtual-machine-network-traffic-filter-problem"></a>仮想マシン ネットワーク トラフィック フィルターの問題を診断する

この記事では、仮想マシン (VM) に対して有効なネットワーク セキュリティ グループ (NSG) のセキュリティ規則を表示することによって、ネットワーク トラフィック フィルターに関する問題を診断する方法を学習します。

NSG を使うと、VM を出入りするトラフィックの種類を制御できます。 Azure 仮想ネットワーク内のサブネット、または VM に接続されたネットワーク インターフェイス、もしくはその両方に、NSG を関連付けることができます。 ネットワーク インターフェイスに適用される有効なセキュリティ規則は、ネットワーク インターフェイスに関連付けられている NSG およびネットワーク インターフェイスが含まれるサブネットに存在する規則をまとめたものです。 場合によっては、異なる NSG の規則が競合し、VM のネットワーク接続に影響を及ぼすことがあります。 VM のネットワーク インターフェイスに適用されるすべての有効なセキュリティ規則を NSG から表示できます。 仮想ネットワーク、ネットワーク インターフェイス、または NSG の概念について初めて学ぶ場合は、[仮想ネットワークの概要](virtual-networks-overview.md)、[ネットワーク インターフェイス](virtual-network-network-interface.md)、[ネットワーク セキュリティ グループ](./network-security-groups-overview.md)に関する記事をご覧ください。

## <a name="scenario"></a>シナリオ

インターネットからポート 80 経由で VM に接続しようとしますが、接続は失敗します。 インターネットからポート 80 にアクセスできない理由を特定するために、Azure [portal](#diagnose-using-azure-portal)、[PowerShell](#diagnose-using-powershell)、または [Azure CLI](#diagnose-using-azure-cli) を使って、ネットワーク インターフェイスの有効なセキュリティ規則を表示できます。

以下の手順では、有効なセキュリティ規則の表示対象となる既存の VM があるものと仮定します。 既存の VM がない場合は、最初に [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) または [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM を展開して、この記事のタスクを実行します。 この記事の例は、*myVMVMNic* というネットワーク インターフェイスを持つ *myVM* という VM に関するものです。 VM およびネットワーク インターフェイスは、*myResourceGroup* というリソース グループおよび "*米国東部*" リージョンにあります。 問題を診断する VM については、手順の値を適宜変更します。

## <a name="diagnose-using-azure-portal"></a>Azure portal を使用して診断する

1. [必要なアクセス許可](virtual-network-network-interface.md#permissions)を持つアカウントで Azure [portal](https://portal.azure.com) にログインします。
2. Azure portal の上部の検索ボックスに、VM の名前を入力します。 検索結果に VM の名前が表示されたら、それを選択します。
3. 次の図に示すように、 **[設定]** で **[ネットワーク]** を選択します。

   ![スクリーンショットには、my V M V M Nic の [ネットワークの設定] が表示された Azure portal が示されています。](./media/diagnose-network-traffic-filter-problem/view-security-rules.png)

   前の図に示されている規則は、**myVMVMNic** という名前のネットワーク インターフェイスのものです。 2 つの異なるネットワーク セキュリティ グループからのネットワーク インターフェイスに対する **受信ポートの規則** があることがわかります。
   
   - **mySubnetNSG**: ネットワーク インターフェイスが含まれるサブネットに関連付けられています。
   - **myVMNSG**: **myVMVMNic** という名前の VM 内のネットワーク インターフェイスに関連付けられています。

   **DenyAllInBound** という名前のルールが、「[シナリオ](#scenario)」で説明したように、ポート 80 経由でのインターネットから VM への受信通信を妨げています。 規則の **[ソース]** には *0.0.0.0/0* と表示されており、これにはインターネットが含まれます。 優先順位がそれより高くて (小さい値) ポート 80 での受信を許可する規則は他にありません。 インターネットから VM へのポート 80 での受信を許可する方法については、「[問題を解決する](#resolve-a-problem)」をご覧ください。 セキュリティ規則および Azure によるその適用方法について詳しくは、「[ネットワーク セキュリティ グループ](./network-security-groups-overview.md)」をご覧ください。

   図の下部には、 **[送信ポートの規則]** も示されています。 そこには、ネットワーク インターフェイスの送信ポート規則が表示されます。 図で各 NSG に表示されている受信規則は 4 つだけですが、もっと多くの規則がある場合もあります。 図では、**[ソース]** と **[ターゲット]** に **VirtualNetwork** と、また **[ソース]** に **AzureLoadBalancer** と表示されています。 **VirtualNetwork** と **AzureLoadBalancer** は [サービス タグ](./network-security-groups-overview.md#service-tags)です。 サービス タグは IP アドレス プレフィックスのグループを表し、セキュリティ規則の作成の複雑さを最小限に抑えるのに役立ちます。

4. VM が実行状態であることを確認した後、前の図のように **[有効なセキュリティ規則]** を選択して、次の図のように有効なセキュリティ規則を表示します。

   ![スクリーンショットには、[ダウンロード] が選択され、AllowAzureLoadBalancerInbound 受信規則が選択されている [有効なセキュリティ規則] ウィンドウが示されています。](./media/diagnose-network-traffic-filter-problem/view-effective-security-rules.png)

   表示されている規則はステップ 3 と同じですが、ネットワーク インターフェイスとサブネットに関連付けられている NSG に対する別のタブがあります。 図に示すように、最初の 50 規則のみが表示されています。 すべての規則を含む .csv ファイルをダウンロードするには、 **[ダウンロード]** を選択します。

   各サービス タグが表すプレフィックスを確認するには、**AllowAzureLoadBalancerInbound** のような規則を選択します。 次の図では、**AzureLoadBalancer** サービス タグのプレフィックスが示されています。

   ![スクリーンショットには、入力された AllowAzureLoadBalancerInbound のアドレス プレフィックスが示されています。](./media/diagnose-network-traffic-filter-problem/address-prefixes.png)

   **AzureLoadBalancer** サービス タグが表すプレフィックスは 1 つだけですが、他のサービス タグは複数のプレフィックスを表しています。

5. 前の手順では **myVMVMNic** という名前のネットワーク インターフェイスのセキュリティ規則を示しましたが、前のいくつかの図には **myVMVMNic2** という名前のネットワーク インターフェイスも表示されています。 この例の VM には 2 つのネットワーク インターフェイスが接続されています。 有効なセキュリティ規則は、ネットワーク インターフェイスごとに異なる場合があります。

   **myVMVMNic2** ネットワーク インターフェイスの規則を表示するには、それを選択します。 次の図のように、このネットワーク インターフェイスのサブネットには **myVMVMNic** ネットワーク インターフェイスと同じ規則が関連付けられています。これは、両方のネットワーク インターフェイスが同じサブネット内にあるためです。 サブネットに NSG を関連付けると、サブネット内のすべてのネットワーク インターフェイスにその規則が適用されます。

   ![スクリーンショットには、my V M V M Nic 2 の [ネットワークの設定] が表示された Azure portal が示されています。](./media/diagnose-network-traffic-filter-problem/view-security-rules2.png)

   **myVMVMNic** ネットワーク インターフェイスとは異なり、**myVMVMNic2** ネットワーク インターフェイスにはネットワーク セキュリティ グループが関連付けられていません。 各ネットワーク インターフェイスとサブネットには、1 つの NSG を関連付けることができ、NSG を関連付けなくてもかまいません。 各ネットワーク インターフェイスまたはサブネットに関連付ける NSG は、同じでも異なっていてもかまいません。 同じネットワーク セキュリティ グループを、任意の数のネットワーク インターフェイスとサブネットに関連付けることができます。

ここでは VM から有効なセキュリティ規則を表示しましたが、以下の個々の項目から有効なセキュリティ規則を表示することもできます。
- **ネットワーク インターフェイス**: [ネットワーク インターフェイスの表示](virtual-network-network-interface.md#view-network-interface-settings)方法をご確認ください。
- **NSG**: [NSG の表示](manage-network-security-group.md#view-details-of-a-network-security-group)方法をご確認ください。

## <a name="diagnose-using-powershell"></a>PowerShell を使用して診断する

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

以下のコマンドは、[Azure Cloud Shell](https://shell.azure.com/powershell) で、またはコンピューターから PowerShell を実行することで実行できます。 Azure Cloud Shell は無料の対話型シェルです。 一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。 お使いのコンピューターから PowerShell を実行する場合は、Azure PowerShell モジュール、バージョン 1.0.0 以降が必要です。 コンピューターで `Get-Module -ListAvailable Az` を実行して、インストールされているバージョンを確認してください。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、[必要なアクセス許可](virtual-network-network-interface.md#permissions)を持つアカウントで `Connect-AzAccount` を実行して Azure にログインする必要もあります。

ネットワーク インターフェイスの有効なセキュリティ規則を取得するには、[Get-AzEffectiveNetworkSecurityGroup](/powershell/module/az.network/get-azeffectivenetworksecuritygroup) を使います。 次の例では、*myResourceGroup* というリソース グループにある *myVMVMNic* という名前のネットワーク インターフェイスの有効なセキュリティ規則を取得します。

```azurepowershell-interactive
Get-AzEffectiveNetworkSecurityGroup `
  -NetworkInterfaceName myVMVMNic `
  -ResourceGroupName myResourceGroup
```

出力は JSON 形式で返されます。 出力を理解する方法については、「[コマンドの出力を解釈する](#interpret-command-output)」をご覧ください。
出力は、NSG がネットワーク インターフェイスまたはネットワーク インターフェイスが含まれるサブネットのどちらか一方または両方に関連付けられている場合にのみ返されます。 VM は実行状態になっている必要があります。 VM には、異なる NSG が適用された複数のネットワーク インターフェイスが存在することがあります。 トラブルシューティングを行うときは、それぞれのネットワーク インターフェイスに対してコマンドを実行します。

接続の問題が解決しない場合は、「[追加の診断](#additional-diagnosis)」と「[考慮事項](#considerations)」をご覧ください。

ネットワーク インターフェイスの名前がわからなくても、ネットワーク インターフェイスが接続されている VM の名前がわかっている場合は、次のコマンドで VM に接続されているすべてのネットワーク インターフェイスの ID が返されます。

```azurepowershell-interactive
$VM = Get-AzVM -Name myVM -ResourceGroupName myResourceGroup
$VM.NetworkProfile
```

次の例のような出力が返されます。

```output
NetworkInterfaces
-----------------
{/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic
```

前の出力では、ネットワーク インターフェイスの名前は *myVMVMNic* です。

## <a name="diagnose-using-azure-cli"></a>Azure CLI を使用して診断する

Azure コマンド ライン インターフェイス (CLI) コマンドを使用してこの記事のタスクを実行する場合は、[Azure Cloud Shell](https://shell.azure.com/bash) でコマンドを実行するか、お使いのコンピューターから CLI を実行してください。 この記事では、Azure CLI バージョン 2.0.32 以降が必要です。 インストールされているバージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。 Azure CLI をローカルで実行している場合、[必要なアクセス許可](virtual-network-network-interface.md#permissions)を持つアカウントで `az login` を実行して Azure にログインする必要もあります。

[az network nic list-effective-nsg](/cli/azure/network/nic#az-network-nic-list-effective-nsg) を使用して、ネットワーク インターフェイスに対して有効なセキュリティ規則を取得します。 次の例では、*myResourceGroup* というリソース グループにある *myVMVMNic* という名前のネットワーク インターフェイスの有効なセキュリティ規則を取得します。

```azurecli-interactive
az network nic list-effective-nsg \
  --name myVMVMNic \
  --resource-group myResourceGroup
```

出力は JSON 形式で返されます。 出力を理解する方法については、「[コマンドの出力を解釈する](#interpret-command-output)」をご覧ください。
出力は、NSG がネットワーク インターフェイスまたはネットワーク インターフェイスが含まれるサブネットのどちらか一方または両方に関連付けられている場合にのみ返されます。 VM は実行状態になっている必要があります。 VM には、異なる NSG が適用された複数のネットワーク インターフェイスが存在することがあります。 トラブルシューティングを行うときは、それぞれのネットワーク インターフェイスに対してコマンドを実行します。

接続の問題が解決しない場合は、「[追加の診断](#additional-diagnosis)」と「[考慮事項](#considerations)」をご覧ください。

ネットワーク インターフェイスの名前がわからなくても、ネットワーク インターフェイスが接続されている VM の名前がわかっている場合は、次のコマンドで VM に接続されているすべてのネットワーク インターフェイスの ID が返されます。

```azurecli-interactive
az vm show \
  --name myVM \
  --resource-group myResourceGroup
```

返される出力には次の例のような情報が含まれます。

```output
"networkProfile": {
    "additionalProperties": {},
    "networkInterfaces": [
      {
        "additionalProperties": {},
        "id": "/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
        "primary": true,
        "resourceGroup": "myResourceGroup"
      },
```

前の出力では、ネットワーク インターフェイスの名前は *myVMVMNic  interface* です。

## <a name="interpret-command-output"></a>コマンドの出力を解釈する

[PowerShell](#diagnose-using-powershell) または [Azure CLI](#diagnose-using-azure-cli) のどちらを使って問題を診断しても、次の情報が含まれる出力が表示されます。

- **NetworkSecurityGroup**: ネットワーク セキュリティ グループの ID です。
- **Association**: ネットワーク セキュリティ グループが *NetworkInterface* または *Subnet* のどちらに関連付けられているかを示します。 NSG が両方に関連付けられている場合、出力は各 NSG の **NetworkSecurityGroup**、**Association**、および **EffectiveSecurityRules** で返されます。 このコマンドを実行して有効なセキュリティ規則を表示する直前に、NSG リソースを関連付けたり、関連付けを解除したりした場合は、コマンドの出力にその変更が反映されるまで数秒間待たなければならないことがあります。
- **EffectiveSecurityRules**: 各プロパティの説明について詳しくは、「[セキュリティ規則を作成する](manage-network-security-group.md#create-a-security-rule)」をご覧ください。 名前の前に *defaultSecurityRules/* が付いている規則は、すべての NSG に存在する既定のセキュリティ規則です。 名前の前に *securityRules/* が付いている規則は、ユーザーが作成した規則です。 **destinationAddressPrefix** または **sourceAddressPrefix** プロパティに対して **Internet**、**VirtualNetwork**、**AzureLoadBalancer** などの [サービス タグ](./network-security-groups-overview.md#service-tags)が指定されている規則は、**expandedDestinationAddressPrefix** プロパティにも値があります。 **expandedDestinationAddressPrefix** プロパティには、サービス タグによって表されるすべてのアドレス プレフィックスが一覧表示されます。

出力に重複して規則が表示される場合は、NSG がネットワーク インターフェイスとサブネットの両方に関連付けられているためです。 既定の規則はどちらの NSG も同じであり、両方の NSG で同じ独自のルールが作成されている場合は、他にも重複する規則が存在することがあります。

**defaultSecurityRules/DenyAllInBound** という名前のルールが、「[シナリオ](#scenario)」で説明したように、ポート 80 経由でのインターネットから VM への受信通信を妨げています。 優先順位がそれより高くて (小さい値) ポート 80 でのインターネットからの受信を許可する規則は他にありません。

## <a name="resolve-a-problem"></a>問題を解決する

この記事の「[シナリオ](#scenario)」で示されている問題の診断に Azure [portal](#diagnose-using-azure-portal)、[PowerShell](#diagnose-using-powershell)、[Azure CLI](#diagnose-using-azure-cli) のどれを使用した場合でも、解決するには、以下のプロパティを含むネットワーク セキュリティ規則を作成します。

| プロパティ                | 値                                                                              |
|---------                |---------                                                                           |
| source                  | Any                                                                                |
| Source port ranges      | Any                                                                                |
| 宛先             | VM の IP アドレス、IP アドレスの範囲、またはサブネット内のすべてのアドレス。 |
| 宛先ポート範囲 | 80                                                                                 |
| Protocol                | TCP                                                                                |
| アクション                  | Allow                                                                              |
| Priority                | 100                                                                                |
| Name                    | Allow-HTTP-All                                                                     |

この規則を作成した後は、ポート 80 でインターネットからの受信が許可されるようになります。これは、トラフィックを拒否している *DenyAllInBound* という名前の既定のセキュリティ規則より、この規則の優先順位の方が高いためです。 方法については、「[セキュリティ規則を作成する](manage-network-security-group.md#create-a-security-rule)」をご覧ください。 ネットワーク インターフェイスとサブネットに異なる NSG が関連付けられている場合は、両方の NSG に同じ規則を作成する必要があります。

Azure は、受信トラフィックを処理するとき、最初にサブネットに関連付けられている NSG の規則を処理し (関連付けられた NSG がある場合)、次にネットワーク インターフェイスに関連付けられている NSG の規則を処理します。 ネットワーク インターフェイスとサブネットに関連付けられている NSG がある場合、トラフィックが VM に到達するためには、両方の NSG でポートを開く必要があります。 管理と通信の問題を軽減するため、個々のネットワーク インターフェイスではなくサブネットに NSG を関連付けることをお勧めします。 サブネット内の VM で異なるセキュリティ規則が必要な場合は、アプリケーション セキュリティ グループ (ASG) のネットワーク インターフェイス メンバーを作成し、セキュリティ規則のソースとターゲットとして ASG を指定できます。 詳しくは、「[アプリケーション セキュリティ グループ](./network-security-groups-overview.md#application-security-groups)」をご覧ください。

通信の問題が解決しない場合は、「[考慮事項](#considerations)」と「追加の診断」をご覧ください。

## <a name="considerations"></a>考慮事項

接続問題のトラブルシューティングを行う場合は、次の点を検討してください。

* 既定のセキュリティ規則は、インターネットからの受信アクセスをブロックし、仮想ネットワークからの受信トラフィックのみを許可します。 インターネットからの受信トラフィックを許可するには、既定の規則より優先順位の高いセキュリティ規則を追加します。 [既定のセキュリティ規則](./network-security-groups-overview.md#default-security-rules)または[セキュリティ規則を追加する](manage-network-security-group.md#create-a-security-rule)方法について学習します。
* 既定では、仮想ネットワークをピアリングした場合、**VIRTUAL_NETWORK** サービス タグは、ピアリングされている仮想ネットワークのプレフィックスを含めるように自動的に拡張されます。 仮想ネットワークのピアリングに関する問題をトラブルシューティングするには、**ExpandedAddressPrefix** の一覧でプレフィックスを表示できます。 [仮想ネットワーク ピアリング](virtual-network-peering-overview.md)および[サービス タグ](./network-security-groups-overview.md#service-tags)の詳細を学習してください。
* ネットワーク インターフェイスの有効なセキュリティ規則は、VM のネットワーク インターフェイスまたはサブネットに NSG が関連付けられていて、VM が実行中の状態である場合にのみ、表示されます。
* ネットワーク インターフェイスやサブネットに関連付けられている NSG がなく、VM に[パブリック IP アドレス](virtual-network-public-ip-address.md)が割り当てられている場合は、すべてのポートがすべての場所との間の受信/送信アクセス用に開かれます。 VM にパブリック IP アドレスがある場合は、ネットワーク インターフェイスが含まれるサブネットに NSG を適用することをお勧めします。

## <a name="additional-diagnosis"></a>追加の診断

* VM の送受信トラフィックが許可されているかどうかを確認するための簡単なテストを実行するには、Azure Network Watcher の [IP フロー検証](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md)機能を使用します。 IP フロー検証では、トラフィックが許可されるか拒否されるかが示されます。 拒否される場合、IP フロー検証はトラフィックを拒否しているセキュリティ規則を示します。
* VM のネットワーク接続の失敗を引き起こすセキュリティ規則がない場合は、他の原因が考えられます。
  * VM のオペレーティング システム内で実行されているファイアウォール ソフトウェア
  * 仮想アプライアンスまたはオンプレミスのトラフィック用に構成されたルート。 インターネット トラフィックは、[強制トンネリング](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)を使用してオンプレミスのネットワークにリダイレクトできます。 インターネット トラフィックを仮想アプライアンスまたはオンプレミスに強制トンネリングすると、インターネットから VM に接続できない場合があります。 VM からのトラフィックのフローを妨げる可能性があるルートの問題を診断する方法については、「[Diagnose a virtual machine routing problem](diagnose-network-routing-problem.md)」(仮想マシンのルーティングの問題を診断する) をご覧ください。

## <a name="next-steps"></a>次のステップ

- [ネットワーク セキュリティ グループ](manage-network-security-group.md#work-with-network-security-groups)と[セキュリティ規則](manage-network-security-group.md#work-with-security-rules)のすべてのタスク、プロパティ、および設定について学習します。
- VM の[既定のセキュリティ規則](./network-security-groups-overview.md#default-security-rules)、[サービス タグ](./network-security-groups-overview.md#service-tags)、および[受信および送信トラフィックのセキュリティ規則を Azure が処理する方法](./network-security-groups-overview.md#network-security-groups)について学習します。