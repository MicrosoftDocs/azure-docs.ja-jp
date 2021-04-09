---
title: Azure 仮想マシンのルーティングに関する問題の診断 | Microsoft Docs
description: 仮想マシンの有効なルートを表示することによって仮想マシンのルーティングに関する問題を診断する方法を説明します。
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/30/2018
ms.author: kumud
ms.openlocfilehash: 1c23244707179e05c63ed44b5915e58eefd3f4a3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "84705051"
---
# <a name="diagnose-a-virtual-machine-routing-problem"></a>仮想マシンのルーティングに関する問題を診断する

この記事では、仮想マシン (VM) のネットワーク インターフェイスに対して有効なルートを表示することによってルーティングに関する問題を診断する方法を説明します。 Azure では、仮想ネットワークのサブネットごとに複数のルートが既定で作成されます。 ルート テーブルでルートを定義し、サブネットにルート テーブルを関連付けることによって、Azure の既定のルートをオーバーライドできます。 自分で作成したルート、Azure の既定のルート、およびボーダー ゲートウェイ プロトコル (BGP) を使用して Azure VPN ゲートウェイ経由でオンプレミスのネットワークから伝達されたルート (仮想ネットワークがオンプレミスのネットワークに接続されている場合) の組み合わせは、サブネット内のすべてのネットワーク インターフェイスに対して有効なルートです。 仮想ネットワーク、ネットワーク インターフェイス、またはルーティングの概念に関する知識がない場合は、[仮想ネットワークの概要](virtual-networks-overview.md)、[ネットワーク インターフェイス](virtual-network-network-interface.md)、[ルーティングの概要](virtual-networks-udr-overview.md)に関する記事をご覧ください。

## <a name="scenario"></a>シナリオ

VM に接続しようとしましたが、接続に失敗します。 VM に接続できない理由を特定するために、Azure [portal](#diagnose-using-azure-portal)、[PowerShell](#diagnose-using-powershell)、または [Azure CLI](#diagnose-using-azure-cli) を使用して、ネットワーク インターフェイスの有効なルートを表示できます。

以下の手順では、有効なルートの表示対象となる既存の VM があると仮定します。 既存の VM がない場合は、最初に [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) または [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM を展開して、この記事のタスクを実行します。 この記事の例は、*myVMNic1* というネットワーク インターフェイスを持つ *myVM* という VM に関するものです。 VM およびネットワーク インターフェイスは、*myResourceGroup* というリソース グループおよび "*米国東部*" リージョンにあります。 問題を診断する VM については、手順の値を適宜変更します。

## <a name="diagnose-using-azure-portal"></a>Azure portal を使用して診断する

1. [必要なアクセス許可](virtual-network-network-interface.md#permissions)を持つアカウントで Azure [portal](https://portal.azure.com) にログインします。
2. Azure portal の上部の検索ボックスに、実行中状態になっている VM の名前を入力します。 検索結果に VM の名前が表示されたら、それを選択します。
3. 左側の **[設定]** で、**[Networking]\(ネットワーク\)** を選択し、ネットワーク インターフェイス リソースの名前を選択してそのリソースに移動します。
     ![ネットワーク インターフェイスを表示する](./media/diagnose-network-routing-problem/view-nics.png)
4. 左側で、**[Effective routes]\(有効なルート\)** を選択します。 次の図に示すように、**myVMNic1** という名前のネットワーク インターフェイスに対して有効なルートが表示されます: ![有効なルートの表示](./media/diagnose-network-routing-problem/view-effective-routes.png)

    複数のネットワーク インターフェイスが VM に接続されている場合は、任意のネットワーク インターフェイスに対して有効なルートを選択して表示できます。 各ネットワーク インターフェイスは異なるサブネットに存在できるので、ネットワーク インターフェイスはそれぞれ異なる有効なルートを持つことができます。

    前の図に示した例では、一覧のルートは、Azure が各サブネットに対して作成する既定のルートです。 一覧には少なくともこれらのルートがありますが、別の仮想ネットワークとのピアリングや、Azure VPN ゲートウェイを通じたオンプレミスのネットワークへの接続など、仮想ネットワークに対して有効にした機能によっては追加のルートがある場合があります。 各ルート、およびネットワーク インターフェイスに対して表示される他のルートについて詳しくは、「[仮想ネットワーク トラフィックのルーティング](virtual-networks-udr-overview.md)」をご覧ください。 一覧に多数のルートがある場合は、**[ダウンロード]** を選択すると、ルートの一覧を .csv ファイルで簡単にダウンロードできます。

有効なルートは前の手順の VM を通じて表示されますが、以下を通じて有効なルートを表示することもできます。
- **個々のネットワーク インターフェイス**: [ネットワーク インターフェイス設定の表示](virtual-network-network-interface.md#view-network-interface-settings)方法をご確認ください。
- **個々のルート テーブル**: [ルート テーブルの表示](manage-route-table.md#view-details-of-a-route-table)方法をご確認ください。

## <a name="diagnose-using-powershell"></a>PowerShell を使用して診断する

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

以下のコマンドは、[Azure Cloud Shell](https://shell.azure.com/powershell) で、またはコンピューターから PowerShell を実行することで実行できます。 Azure Cloud Shell は無料の対話型シェルです。 一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。 お使いのコンピューターから PowerShell を実行する場合は、Azure PowerShell モジュール、バージョン 1.0.0 以降が必要です。 コンピューターで `Get-Module -ListAvailable Az` を実行して、インストールされているバージョンを確認してください。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-Az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、[必要なアクセス許可](virtual-network-network-interface.md#permissions)を持つアカウントで `Connect-AzAccount` を実行して Azure にログインする必要もあります。

[Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable) を使用してネットワーク インターフェイスの有効なルートを取得します。 次の例では、*myResourceGroup* というリソース グループにある *myVMNic1* というネットワーク インターフェイスの有効なルートを取得します。

```azurepowershell-interactive
Get-AzEffectiveRouteTable `
  -NetworkInterfaceName myVMNic1 `
  -ResourceGroupName myResourceGroup `
  | Format-Table
```

出力で返された情報を解釈するには、[ルーティングの概要](virtual-networks-udr-overview.md)に関する記事をご覧ください。 出力は、VM が実行中状態の場合にのみ返されます。 複数のネットワーク インターフェイスが VM に接続されている場合は、各ネットワーク インターフェイスに対して有効なルートを確認できます。 各ネットワーク インターフェイスは異なるサブネットに存在できるので、ネットワーク インターフェイスはそれぞれ異なる有効なルートを持つことができます。 通信の問題が解決しない場合は、「[追加の診断](#additional-diagnosis)」と「[考慮事項](#considerations)」をご覧ください。

ネットワーク インターフェイスの名前がわからなくても、ネットワーク インターフェイスが接続されている VM の名前がわかっている場合は、次のコマンドで VM に接続されているすべてのネットワーク インターフェイスの ID が返されます。

```azurepowershell-interactive
$VM = Get-AzVM -Name myVM `
  -ResourceGroupName myResourceGroup
$VM.NetworkProfile
```

次の例のような出力が返されます。

```powershell
NetworkInterfaces
-----------------
{/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMNic1
```

前の出力では、ネットワーク インターフェイスの名前は *myVMNic1* です。

## <a name="diagnose-using-azure-cli"></a>Azure CLI を使用して診断する

以下のコマンドは、[Azure Cloud Shell](https://shell.azure.com/bash) で、またはコンピューターから CLI を実行することで実行できます。 この記事では、Azure CLI バージョン 2.0.32 以降が必要です。 インストールされているバージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。 Azure CLI をローカルで実行している場合、[必要なアクセス許可](virtual-network-network-interface.md#permissions)を持つアカウントで `az login` を実行して Azure にログインする必要もあります。

[az network nic show-effective-route-table](/cli/azure/network/nic#az-network-nic-show-effective-route-table) を使用して、ネットワーク インターフェイスに対して有効なルートを取得します。 次の例では、*myResourceGroup* というリソース グループにある *myVMNic1* というネットワーク インターフェイスの有効なルートを取得します。

```azurecli-interactive
az network nic show-effective-route-table \
  --name myVMNic1 \
  --resource-group myResourceGroup
```

出力で返された情報を解釈するには、[ルーティングの概要](virtual-networks-udr-overview.md)に関する記事をご覧ください。 出力は、VM が実行中状態の場合にのみ返されます。 複数のネットワーク インターフェイスが VM に接続されている場合は、各ネットワーク インターフェイスに対して有効なルートを確認できます。 各ネットワーク インターフェイスは異なるサブネットに存在できるので、ネットワーク インターフェイスはそれぞれ異なる有効なルートを持つことができます。 通信の問題が解決しない場合は、「[追加の診断](#additional-diagnosis)」と「[考慮事項](#considerations)」をご覧ください。

ネットワーク インターフェイスの名前がわからなくても、ネットワーク インターフェイスが接続されている VM の名前がわかっている場合は、次のコマンドで VM に接続されているすべてのネットワーク インターフェイスの ID が返されます。

```azurecli-interactive
az vm show \
  --name myVM \
  --resource-group myResourceGroup
```

## <a name="resolve-a-problem"></a>問題を解決する

通常、ルーティングの問題の解決は以下から構成されます。

- カスタム ルートを追加して、Azure の既定のルートをオーバーライドします。 [カスタム ルートの追加](manage-route-table.md#create-a-route)方法をご確認ください。
- 望ましくない場所へのルーティングを引き起こす可能性があるカスタム ルートを変更または削除します。 カスタム ルートの[変更](manage-route-table.md#change-a-route)または[削除](manage-route-table.md#delete-a-route)の方法をご確認ください。
- 定義した任意のカスタム ルートを含むルート テーブルが、ネットワーク インターフェイスが存在するサブネットに関連付けられていることを確認します。 [サブネットへのルート テーブルの関連付け](manage-route-table.md#associate-a-route-table-to-a-subnet)の方法をご確認ください。
- デプロイした Azure VPN ゲートウェイやネットワーク仮想アプライアンスなどのデバイスが動作していることを確認します。 Network Watcher の [VPN 診断](../network-watcher/diagnose-communication-problem-between-networks.md?toc=%2fazure%2fvirtual-network%2ftoc.json)機能を使用して、Azure VPN ゲートウェイの問題を特定します。

通信の問題が解決しない場合は、「[考慮事項](#considerations)」と「追加の診断」をご覧ください。

## <a name="considerations"></a>考慮事項

通信の問題のトラブルシューティングを行う場合は、次の点を検討してください。

- ルーティングは、定義したルートの中の最長プレフィックス一致 (LPM)、ボーダー ゲートウェイ プロトコル (BGP)、システム ルートに基づきます。 同じ LPM マッチの複数のルートが存在する場合、ルートは[ルーティング概要](virtual-networks-udr-overview.md#how-azure-selects-a-route)のページに示されている順序でその起点に基づいて選択されます。 有効なルートを使用すると、使用可能なすべてのルートに対して LPM マッチの有効なルートのみが表示されます。 ネットワーク インターフェイスに対してルートが評価される方法を表示することによって、VM からの通信に影響を与える可能性のある特定のルートのトラブルシューティングをより簡単に行うことができます。
- "*仮想アプライアンス*" を次ホップの種類として使用して、ネットワーク仮想アプライアンス (NVA) へのカスタム ルートを定義した場合は、トラフィックを受信する NVA で IP 転送が有効であることを確認してください。有効になっていない場合、パケットが破棄されます。 [ネットワーク インターフェイスの IP 転送の有効化](virtual-network-network-interface.md#enable-or-disable-ip-forwarding)の詳細をご確認ください。 さらに、オペレーティング システム、または NVA 内のアプリケーションは、ネットワーク トラフィックを転送できる必要もあり、そのように構成されている必要があります。
- 0.0.0.0/0 へのルートを作成した場合、すべての送信インターネット トラフィックは、NVA や VPN ゲートウェイなど、指定した次ホップにルーティングされます。 このようなルートの作成は、多くの場合に強制トンネリングと呼ばれます。 次ホップがトラフィックを処理する方法によっては、RDP または SSH プロトコルを使用したインターネットから VM へのリモート接続がこのルートで動作しない場合があります。 強制トンネリングは、次の方法で有効にできます。
    - "*VPN ゲートウェイ*" の次ホップの種類でルートを作成して、サイト間 VPN を使用する場合。 [強制トンネリングの構成](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)の詳細をご確認ください。
    - サイト間 VPN または ExpressRoute 回線を使用しているときに、0.0.0.0/0 (既定のルート) が仮想ネットワーク ゲートウェイを介して BGP 経由でアドバタイズされる場合。 [サイト間 VPN](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) または [ExpressRoute](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#ip-addresses-used-for-azure-private-peering) で BGP を使用する方法の詳細をご確認ください。
- 仮想ネットワークのピアリング トラフィックが正常に機能するためには、次ホップの種類が "*VNet のピアリング*" であるシステム ルートが、ピアリングされる仮想ネットワークのプレフィックスの範囲に対して存在する必要があります。 そのようなルートが存在せず、仮想ネットワーク ピアリングのリンクが **接続済み** の場合:
    - 数秒待機してから再試行します。 新しく確立されたピアリング リンクの場合は、サブネット内のすべてのネットワーク インターフェイスにルートを反映させるのに時間がかかる場合がります。 仮想ネットワーク ピアリングについて詳しくは、[仮想ネットワーク ピアリングの概要](virtual-network-peering-overview.md)および[仮想ネットワーク ピアリングの管理](virtual-network-manage-peering.md)に関する記事をご覧ください。
    - ネットワーク セキュリティ グループの規則が通信に影響を与える可能性があります。 詳しくは、「[Diagnose a virtual machine network traffic filter problem](diagnose-network-traffic-filter-problem.md)」(仮想マシン ネットワーク トラフィック フィルターの問題を診断する) をご覧ください。
- Azure では、各 Azure ネットワーク インターフェイスに既定のルートが割り当てられますが、VM に複数のネットワーク インターフェイスが接続されている場合は、プライマリ ネットワーク インターフェイスだけに既定のルート (0.0.0.0/0) または VM のオペレーティング システム内のゲートウェイが割り当てられます。 [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) または [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) VM に接続されているセカンダリ ネットワーク インターフェイスの既定のルートを作成する方法をご確認ください。 [プライマリとセカンダリのネットワーク インターフェイス](virtual-network-network-interface-vm.md#constraints)の詳細をご確認ください。

## <a name="additional-diagnosis"></a>追加の診断

* 簡単なテストを実行して、ある場所を宛先とするトラフィックの次ホップの種類を確認するには、Azure Network Watcher の[次ホップ](../network-watcher/diagnose-vm-network-routing-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json)機能を使用します。 次ホップは、指定された場所を宛先とするトラフィックの次ホップの種類を示します。
* ルートがないことが原因で VM のネットワーク通信が失敗する場合、VM のオペレーティング システム内で実行されているファイアウォール ソフトウェアが問題を引き起こしている可能性があります
* VPN ゲートウェイまたは NVA を介してオンプレミスのデバイスへのトラフィックの[強制トンネリング](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)を行っている場合、デバイスのルーティングの構成方法によってはインターネットから VM に接続できない場合があります。 デバイス用に構成したルーティングが VM のパブリックまたはプライベート IP アドレスにトラフィックをルーティングすることを確認してください。
* Network Watcher の[接続のトラブルシューティング](../network-watcher/network-watcher-connectivity-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)機能を使用して、送信方向の通信に関する問題のルーティング、フィルタリング、および OS 内の原因を特定します。

## <a name="next-steps"></a>次のステップ

- タスク、プロパティ、および[ルート テーブルとルート](manage-route-table.md)の設定について理解を深める。
- [次ホップの種類、システム ルート、および Azure でのルートの選択方法](virtual-networks-udr-overview.md)について理解を深める。
