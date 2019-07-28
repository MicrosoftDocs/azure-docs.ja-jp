---
title: Azure ルート テーブルを作成、変更、削除する
titlesuffix: Azure Virtual Network
description: ルート テーブルの作成、変更、削除の方法について説明します。
services: virtual-network
documentationcenter: na
author: kumudd
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2018
ms.author: kumud
ms.openlocfilehash: a39d9f9c5a138ece5d40cc5afe1d1dcdd8e7a41a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "65849799"
---
# <a name="create-change-or-delete-a-route-table"></a>ルート テーブルの作成、変更、削除

Azure では、Azure のサブネット、仮想ネットワーク、およびオンプレミスのネットワーク間のトラフィックが自動的にルーティングされます。 Azure の既定のルーティングを変更する場合は、ルート テーブルを作成して変更します。 仮想ネットワークでのルーティングについて初心者の場合は、[ルーティングの概要](virtual-networks-udr-overview.md)に関するページを読むか、[チュートリアル](tutorial-create-route-table-portal.md)を行うことで、詳しく学習できます。

## <a name="before-you-begin"></a>開始する前に

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

この記事のセクションに記載された手順を始める前に、次のタスクを完了してください。

* まだ Azure アカウントを持っていない場合は、[無料試用版アカウント](https://azure.microsoft.com/free)にサインアップしてください。<br>
* ポータルを使用する場合は、 https://portal.azure.com を開き、Azure アカウントでサインインします。<br>
* PowerShell コマンドを使用してこの記事のタスクを実行する場合は、[Azure Cloud Shell](https://shell.azure.com/powershell) でコマンドを実行するか、お使いのコンピューターから PowerShell を実行してください。 Azure Cloud Shell は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。 アカウントで使用できるように、一般的な Azure ツールが事前にインストールおよび構成されています。 このチュートリアルには、Azure PowerShell モジュール バージョン 1.0.0 以降が必要です。 インストールされているバージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzAccount` を実行して Azure との接続を作成することも必要です。<br>
* Azure コマンド ライン インターフェイス (CLI) コマンドを使用してこの記事のタスクを実行する場合は、[Azure Cloud Shell](https://shell.azure.com/bash) でコマンドを実行するか、お使いのコンピューターから CLI を実行してください。 このチュートリアルには、Azure CLI のバージョン 2.0.31 以降が必要です。 インストールされているバージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。 Azure CLI をローカルで実行している場合、`az login` を実行して Azure との接続を作成することも必要です。

Azure へのサインインまたは接続に使用するアカウントは、[ネットワークの共同作業者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)ロール、または「[アクセス許可](#permissions)」の一覧に記載されている適切なアクションが割り当てられている[カスタム ロール](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に割り当てられている必要があります。

## <a name="create-a-route-table"></a>ルート テーブルの作成

Azure の場所およびサブスクリプションあたりの作成可能なルート テーブル数は、制限されています。 詳細については、[Azure の制限](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)に関する記事をご覧ください。

1. ポータルの左上隅にある **[+ リソースの作成]** を選択します。
1. **[ネットワーク]** 、 **[ルート テーブル]** の順に選択します。
1. ルート テーブルの**名前**を入力し、**サブスクリプション**を選択して、新しい**リソース グループ**を作成するか、既存のリソース グループを選択し、**場所**を選択してから、 **[作成]** を選択します。 **[仮想ネットワーク ゲートウェイのルート伝達]** オプションを [無効] にすると、ルート テーブルが関連付けられているサブネット内のネットワーク インターフェイスに、BGP 経由でオンプレミスのルートが伝達されなくなります。

### <a name="create-route-table---commands"></a>ルート テーブルの作成 - コマンド

* Azure CLI: [az network route-table create](/cli/azure/network/route-table/route)<br>
* PowerShell:[New-AzRouteTable](/powershell/module/az.network/new-azroutetable)

## <a name="view-route-tables"></a>ルート テーブルの表示

ポータルの上部にある検索ボックスに、「*ルート テーブル*」と入力します。 検索結果に **[ルート テーブル]** が表示されたら、それを選択します。 サブスクリプション内に存在するルート テーブルが一覧表示されます。

### <a name="view-route-table---commands"></a>ルート テーブルの表示 - コマンド

* Azure CLI: [az network route-table list](/cli/azure/network/route-table/route)<br>
* PowerShell:[Get-AzRouteTable](/powershell/module/az.network/get-azroutetable)

## <a name="view-details-of-a-route-table"></a>ルート テーブルの詳細の表示

1. ポータルの上部にある検索ボックスに、「*ルート テーブル*」と入力します。 検索結果に **[ルート テーブル]** が表示されたら、それを選択します。
1. 詳細を表示するルート テーブルを一覧から選択します。 **[設定]** では、ルート テーブル内の**ルート**と、ルート テーブルが関連付けられている**サブネット**を確認できます。
1. Azure の一般的な設定の詳細については、次の情報を参照してください。

    * [アクティビティ ログ](../azure-monitor/platform/activity-logs-overview.md)<br>
    * [アクセス制御 (IAM)](../role-based-access-control/overview.md)<br>
    * [タグ](../azure-resource-manager/resource-group-using-tags.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br>
    * [ロック](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br>
    * [Automation スクリプト](../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates)

### <a name="view-details-of-route-table---commands"></a>ルート テーブルの詳細の表示 - コマンド

* Azure CLI: [az network route-table show](/cli/azure/network/route-table/route)<br>
* PowerShell:[Get-AzRouteTable](/powershell/module/az.network/get-azroutetable)

## <a name="change-a-route-table"></a>ルート テーブルの変更

1. ポータルの上部にある検索ボックスに、「*ルート テーブル*」と入力します。 検索結果に **[ルート テーブル]** が表示されたら、それを選択します。
1. 変更するルート テーブルを選択します。 最も一般的な変更は、ルートの[追加](#create-a-route)または[削除](#delete-a-route)、ルート テーブルの[関連付け](#associate-a-route-table-to-a-subnet)、またはサブネットからのルート テーブルの[関連付けの解除](#dissociate-a-route-table-from-a-subnet)です。

### <a name="change-a-route-table---commands"></a>ルート テーブルの変更 - コマンド

* Azure CLI: [az network route-table update](/cli/azure/network/route-table/route)<br>
* PowerShell:[Set-AzRouteTable](/powershell/module/az.network/set-azroutetable)

## <a name="associate-a-route-table-to-a-subnet"></a>サブネットへのルート テーブルの関連付け

サブネットには、0 個または 1 個のルート テーブルを関連付けることができます。 ルート テーブルは、0 個または複数のサブネットに関連付けることができます。 ルート テーブルは仮想ネットワークに関連付けられていないので、ルート テーブルを関連付ける各サブネットにルート テーブルを関連付ける必要があります。 仮想ネットワークが Azure 仮想ネットワーク ゲートウェイに接続されている場合 (ExpressRoute または VPN)、サブネットを出るすべてのトラフィックは、ルート テーブル内に作成したルート、[既定のルート](virtual-networks-udr-overview.md#default)、およびオンプレミスのネットワークから伝達されたルートに基づいてルーティングされます。 ルート テーブルは、そのルート テーブルと同じ Azure の場所とサブスクリプションに存在する仮想ネットワーク内のサブネットにのみ関連付けることができます。

1. ポータルの上部にある検索ボックスに、「*仮想ネットワーク*」と入力します。 検索結果に **[仮想ネットワーク]** が表示されたら、それを選択します。
1. ルート テーブルを関連付けるサブネットが含まれる仮想ネットワークを一覧から選択します。
1. **[設定]** で、 **[サブネット]** を選択します。
1. ルート テーブルを関連付けるサブネットを選択します。
1. **[ルート テーブル]** を選択し、サブネットに関連付けるルート テーブルを選択してから、 **[保存]** を選択します。

仮想ネットワークが Azure VPN ゲートウェイに接続されている場合は、宛先が 0.0.0.0/0 であるルートを含む[ゲートウェイ サブネット](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub)にルート テーブルを関連付けないでください。 関連付けると、ゲートウェイが正しく機能しない可能性があります。 ルート上での 0.0.0.0/0 の使用について詳しくは、[仮想ネットワーク トラフィックのルーティング](virtual-networks-udr-overview.md#default-route)に関するページをご覧ください。

### <a name="associate-a-route-table---commands"></a>ルート テーブルの関連付け - コマンド

* Azure CLI: [az network vnet subnet update](/cli/azure/network/vnet/subnet?view=azure-cli-latest)<br>
* PowerShell:[Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig)

## <a name="dissociate-a-route-table-from-a-subnet"></a>サブネットからのルート テーブルの関連付けの解除

サブネットからルート テーブルの関連付けを解除すると、Azure では、[既定のルート](virtual-networks-udr-overview.md#default)に基づいてトラフィックがルーティングされます。

1. ポータルの上部にある検索ボックスに、「*仮想ネットワーク*」と入力します。 検索結果に **[仮想ネットワーク]** が表示されたら、それを選択します。
1. ルート テーブルの関連付けを解除するサブネットが含まれる仮想ネットワークを選択します。
1. **[設定]** で、 **[サブネット]** を選択します。
1. ルート テーブルの関連付けを解除するサブネットを選択します。
1. **[ルート テーブル]** 、 **[なし]** の順に選択してから、 **[保存]** を選択します。

### <a name="dissociate-a-route-table---commands"></a>ルート テーブルの関連付けの解除 - コマンド

* Azure CLI: [az network vnet subnet update](/cli/azure/network/vnet/subnet?view=azure-cli-latest)<br>
* PowerShell:[Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig)

## <a name="delete-a-route-table"></a>ルート テーブルの削除

ルート テーブルは、サブネットに関連付けられている場合、削除できません。 ルート テーブルは、削除する前に、すべてのサブネットから[関連付けを解除](#dissociate-a-route-table-from-a-subnet)してください。

1. ポータルの上部にある検索ボックスに、「*ルート テーブル*」と入力します。 検索結果に **[ルート テーブル]** が表示されたら、それを選択します。
1. 削除するルート テーブルの右側にある **[...]** を選択します。
1. **[削除]** を選択してから、 **[はい]** を選択します。

### <a name="delete-a-route-table---commands"></a>ルート テーブルの削除 - コマンド

* Azure CLI: [az network route-table delete](/cli/azure/network/route-table/route)<br>
* PowerShell:[Remove-AzRouteTable](/powershell/module/az.network/remove-azroutetable)

## <a name="create-a-route"></a>ルートの作成

Azure の場所およびサブスクリプションあたりの作成可能なルート テーブルごとのルート数は、制限されています。 詳細については、[Azure の制限](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)に関する記事をご覧ください。

1. ポータルの上部にある検索ボックスに、「*ルート テーブル*」と入力します。 検索結果に **[ルート テーブル]** が表示されたら、それを選択します。
1. ルートを追加するルート テーブルを一覧から選択します。
1. **[設定]** で、 **[ルート]** を選択します。
1. **[+ 追加]** を選択します。
1. ルート テーブル内のルートの一意の**名前**を入力します。
1. トラフィックをルーティングする**アドレス プレフィックス**を CIDR 表記で入力します。 ルート テーブル内の複数のルート上でプレフィックスを重複させることはできません。ただし、プレフィックスを別のプレフィックス内に指定することはできます。 たとえば、あるルートでプレフィックスとして 10.0.0.0/16 を定義した場合、アドレス プレフィックス 10.0.0.0/24 を持つ別のルートを定義することは可能です。 Azure では、最長プレフィックス一致に基づいてトラフィックのルートが選択されます。 Azure のルート選択方法の詳細については、[ルーティングの概要](virtual-networks-udr-overview.md#how-azure-selects-a-route)に関するページを参照してください。
1. **[次ホップの種類]** を選択します。 すべての次のホップの種類の詳細については、[ルーティングの概要](virtual-networks-udr-overview.md)に関するページを参照してください。
1. **次ホップ アドレス**の IP アドレスを入力します。 **[次ホップの種類]** に *[仮想アプライアンス]* を選択した場合は、アドレスのみを入力できます。
1. **[OK]** を選択します。

### <a name="create-a-route---commands"></a>ルートの作成 - コマンド

* Azure CLI: [az network route-table route create](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* PowerShell:[New-AzRouteConfig](/powershell/module/az.network/new-azrouteconfig)

## <a name="view-routes"></a>ルートの表示

ルート テーブルには、0 個または複数のルートが含まれます。 ルートを表示したときに一覧に表示される情報の詳細については、[ルーティングの概要](virtual-networks-udr-overview.md)に関するページを参照してください。

1. ポータルの上部にある検索ボックスに、「*ルート テーブル*」と入力します。 検索結果に **[ルート テーブル]** が表示されたら、それを選択します。
1. ルートを表示するルート テーブルを一覧から選択します。
1. **[設定]** で、 **[ルート]** を選択します。

### <a name="view-routes---commands"></a>ルートの表示 - コマンド

* Azure CLI: [az network route-table route list](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* PowerShell:[Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig)

## <a name="view-details-of-a-route"></a>ルートの詳細の表示

1. ポータルの上部にある検索ボックスに、「*ルート テーブル*」と入力します。 検索結果に **[ルート テーブル]** が表示されたら、それを選択します。
1. ルートの詳細を表示するルート テーブルを選択します。
1. **[ルート]** を選択します。
1. 詳細を表示するルートを選択します。

### <a name="view-details-of-a-route---commands"></a>ルートの詳細の表示 - コマンド

* Azure CLI: [az network route-table route show](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* PowerShell:[Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig)

## <a name="change-a-route"></a>ルートの変更

1. ポータルの上部にある検索ボックスに、「*ルート テーブル*」と入力します。 検索結果に **[ルート テーブル]** が表示されたら、それを選択します。
1. ルートを変更するルート テーブルを選択します。
1. **[ルート]** を選択します。
1. 変更するルートを選択します。
1. 既存の設定を新しい設定に変更し、 **[保存]** を選択します。

### <a name="change-a-route---commands"></a>ルートの変更 - コマンド

* Azure CLI: [az network route-table route update](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* PowerShell:[Set-AzRouteConfig](/powershell/module/az.network/set-azrouteconfig)

## <a name="delete-a-route"></a>ルートの削除

1. ポータルの上部にある検索ボックスに、「*ルート テーブル*」と入力します。 検索結果に **[ルート テーブル]** が表示されたら、それを選択します。
1. ルートを削除するルート テーブルを選択します。
1. **[ルート]** を選択します。
1. ルートの一覧から、削除するルートの右側にある **[...]** を選択します。
1. **[削除]** を選択してから、 **[はい]** を選択します。

### <a name="delete-a-route---commands"></a>ルートの削除 - コマンド

* Azure CLI: [az network route-table route delete](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* PowerShell:[Remove-AzRouteConfig](/powershell/module/az.network/remove-azrouteconfig)

## <a name="view-effective-routes"></a>有効なルートの表示

仮想マシンに接続されている各ネットワーク インターフェイスの有効なルートは、作成したルート テーブル、Azure の既定のルート、および Azure 仮想ネットワーク ゲートウェイを介して BGP 経由でオンプレミスのネットワークから伝達されるルートの組み合わせとなります。 ネットワーク インターフェイスの有効なルートを理解しておくと、ルーティングの問題をトラブルシューティングするときに役立ちます。 実行中の仮想マシンに接続されているネットワーク インターフェイスの有効なルートを表示できます。

1. ポータルの上部にある検索ボックスに、有効なルートを表示する仮想マシンの名前を入力します。 仮想マシンの名前がわからない場合は、検索ボックスに「*仮想マシン*」と入力します。 検索結果に **[仮想マシン]** が表示されたら、それを選択し、一覧から仮想マシンを選択します。
1. **[設定]** で、 **[ネットワーク]** を選択します。
1. ネットワーク インターフェイスの名前を選択します。
1. **[サポート + トラブルシューティング]** で、 **[有効なルート]** を選択します。
1. トラフィックのルーティング先への適切なルートが存在するかどうかを判断するために、有効なルートの一覧を確認します。 この一覧に表示される次のホップの種類の詳細については、[ルーティングの概要](virtual-networks-udr-overview.md)に関するページをご覧ください。

### <a name="view-effective-routes---commands"></a>有効なルートの表示 - コマンド

* Azure CLI: [az network nic show-effective-route-table](/cli/azure/network/nic?view=azure-cli-latest)<br>
* PowerShell:[Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable)

## <a name="validate-routing-between-two-endpoints"></a>2 つのエンドポイント間のルーティングの検証

仮想マシンと、他の Azure リソース、オンプレミスのリソース、またはインターネット上のリソースの IP アドレス間の次ホップの種類を確認できます。 Azure のルーティングを確認すると、ルーティングの問題をトラブルシューティングするときに役立ちます。 このタスクを完了するには、既存の Network Watcher が必要です。 既存の Network Watcher がない場合は、[Network Watcher のインスタンスの作成](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関するページの手順を実行して作成します。

1. ポータルの上部にある検索ボックスに、「*network watcher*」と入力します。 検索結果に **[Network Watcher]** が表示されたら、それを選択します。
1. **[ネットワーク診断ツール]** で、 **[次ホップ]** を選択します。
1. ご使用の **サブスクリプション**と、ルーティングを検証するソース仮想マシンの**リソース グループ**を選択します。
1. ルーティングを検証する **仮想マシン**、仮想マシンに接続されている**ネットワーク インターフェイス**、およびネットワーク インターフェイスに割り当てられている**送信元 IP アドレス**を選択します。
1. ルーティングを検証する**宛先 IP アドレス**を入力します。
1. **[次ホップ]** を選択します。
1. 少し待つと、次ホップの種類と、トラフィックをルーティングするルートの ID を示す情報が返されます。 返される次ホップの種類の詳細については、[ルーティングの概要](virtual-networks-udr-overview.md)に関するページをご覧ください。

### <a name="validate-routing-between-two-endpoints---commands"></a>2 つのエンドポイント間のルーティングの検証 - コマンド

* Azure CLI: [az network watcher show-next-hop](/cli/azure/network/watcher?view=azure-cli-latest)<br>
* PowerShell:[Get-AzNetworkWatcherNextHop](/powershell/module/az.network/get-aznetworkwatchernexthop)

## <a name="permissions"></a>アクセス許可

ルート テーブルとルートに関するタスクを実行するには、使用するアカウントが[ネットワークの共同作業者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)ロール、または次の表の適切なアクションが割り当てられた[カスタム](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ロールに、割り当てられている必要があります。

| Action                                                          |   Name                                                  |
|--------------------------------------------------------------   |   -------------------------------------------           |
| Microsoft.Network/routeTables/read                              |   ルート テーブルの読み取り                                    |
| Microsoft.Network/routeTables/write                             |   ルート テーブルの作成または更新                        |
| Microsoft.Network/routeTables/delete                            |   ルート テーブルの削除                                  |
| Microsoft.Network/routeTables/join/action                       |   サブネットへのルート テーブルの関連付け                   |
| Microsoft.Network/routeTables/routes/read                       |   ルートの読み取り                                          |
| Microsoft.Network/routeTables/routes/write                      |   ルートの作成または更新                              |
| Microsoft.Network/routeTables/routes/delete                     |   ルートの削除                                        |
| Microsoft.Network/networkInterfaces/effectiveRouteTable/action  |   ネットワーク インターフェイスに対する有効なルート テーブルの取得 |
| Microsoft.Network/networkWatchers/nextHop/action                |   VM から次ホップを取得する                           |

## <a name="next-steps"></a>次の手順

* [PowerShell](powershell-samples.md) または [Azure CLI](cli-samples.md) のサンプル スクリプトを使って、または Azure [Resource Manager テンプレート](template-samples.md)を使って、ルート テーブルを作成します<br>
* [Azure ポリシー](policy-samples.md)を作成して仮想ネットワークに適用します
