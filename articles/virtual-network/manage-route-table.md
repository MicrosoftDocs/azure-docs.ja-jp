---
title: Azure ルート テーブルを作成、変更、削除する
titlesuffix: Azure Virtual Network
description: 仮想ネットワーク トラフィックのルーティングに関する情報の参照先、およびルートテーブルを作成、変更、削除する方法について説明します。
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/19/2020
ms.author: kumud
ms.openlocfilehash: 642a9a9f798492d85ee2a9784a1fe5ad4f854d58
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100574125"
---
# <a name="create-change-or-delete-a-route-table"></a>ルート テーブルの作成、変更、削除

Azure では、Azure のサブネット、仮想ネットワーク、およびオンプレミスのネットワーク間のトラフィックが自動的にルーティングされます。 Azure の既定のルーティングを変更する場合は、ルート テーブルを作成して変更します。 仮想ネットワークでのルーティングについて初心者の場合は、「[仮想ネットワーク トラフィックのルーティング](virtual-networks-udr-overview.md)」を読むか、[チュートリアル](tutorial-create-route-table-portal.md)を行うことで、詳しく学習できます。

## <a name="before-you-begin"></a>開始する前に

Azure アカウントをお持ちでない場合は、アクティブなサブスクリプションを使用してそれを設定します。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。 その後、この記事のセクションに記載された手順を始める前に、次のいずれかのタスクを完了してください。

- **ポータル ユーザー**: Azure アカウントで [Azure Portal](https://portal.azure.com) にサインインします。

- **PowerShell ユーザー**:[Azure Cloud Shell](https://shell.azure.com/powershell) でコマンドを実行するか、お使いのコンピューターから PowerShell を実行します。 Azure Cloud Shell は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。 一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。 Azure Cloud Shell のブラウザー タブで、 **[環境の選択]** ドロップダウン リストを見つけ、 **[PowerShell]** を選択します (まだ選択されていない場合)。

    PowerShell をローカルで実行している場合は、Azure PowerShell モジュール バージョン 1.0.0 以降を使用してください。 インストールされているバージョンを確認するには、`Get-Module -ListAvailable Az.Network` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。 また、`Connect-AzAccount` を実行して、Azure との接続を作成します。

- **Azure コマンド ライン インターフェイス (CLI) のユーザー**: [Azure Cloud Shell](https://shell.azure.com/bash) でコマンドを実行するか、お使いのコンピューターから CLI を実行します。 Azure CLI をローカルに実行している場合は、Azure CLI バージョン 2.0.31 以降を使用してください。 インストールされているバージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。 また、`az login` を実行して、Azure との接続を作成します。

Azure へのログインまたは接続に使用するアカウントは、[ネットワーク共同作成者ロール](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)または「[アクセス許可](#permissions)」の一覧の適切なアクションが割り当てられている[カスタム ロール](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に、割り当てられている必要があります。

## <a name="create-a-route-table"></a>ルート テーブルの作成

Azure の場所とサブスクリプションごとに作成できるルート テーブルの数には制限があります。 詳しくは、「[ネットワークの制限 - Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)」をご覧ください。

1. [[Azure portal]](https://portal.azure.com) メニュー上または **[ホーム]** ページから **[リソースの作成]** を選択します。

1. 検索ボックスに、「"*ルート テーブル*"」と入力します。 検索結果に **[ルート テーブル]** が表示されたら、それを選択します。

1. **[ルート テーブル]** ページで、 **[作成]** を選択します。

1. **[ルート テーブルの作成]** ダイアログ ボックスで次のようにします。

    1. ルート テーブルの **[名前]** を入力します。
    1. **サブスクリプション** を選択します。
    1. 既存の **[リソース グループ]** を選択するか、 **[新規作成]** を選択して新しいリソース グループを作成します。
    1. **[場所]** を選択します。
    1. VPN ゲートウェイを通してオンプレミスのネットワークに接続されている仮想ネットワーク内のサブネットにルート テーブルを関連付ける場合で、サブネット内のネットワーク インターフェイスにオンプレミスのルートを伝達したくないときは、 **[仮想ネットワーク ゲートウェイのルート伝達]** を **[無効]** に設定します。

1. **[作成]** を選択して、新しいルート テーブルを作成します。

### <a name="create-route-table---commands"></a>ルート テーブルの作成 - コマンド

| ツール | コマンド |
| ---- | ------- |
| Azure CLI | [az network route-table create](/cli/azure/network/route-table#az-network-route-table-create) |
| PowerShell | [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) |

## <a name="view-route-tables"></a>ルート テーブルの表示

[Azure portal](https://portal.azure.com) に移動して、仮想ネットワークを管理します。 **ルート テーブル** を検索して選択します。 サブスクリプション内に存在するルート テーブルが一覧表示されます。

### <a name="view-route-table---commands"></a>ルート テーブルの表示 - コマンド

| ツール | コマンド |
| ---- | ------- |
| Azure CLI | [az network route-table list](/cli/azure/network/route-table#az-network-route-table-list) |
| PowerShell | [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable) |

## <a name="view-details-of-a-route-table"></a>ルート テーブルの詳細の表示

1. [Azure portal](https://portal.azure.com) に移動して、仮想ネットワークを管理します。 **ルート テーブル** を検索して選択します。

1. ルート テーブルの一覧で、詳細を表示するルート テーブルを選択します。

1. ルート テーブルのページの **[設定]** に、ルート テーブル内の **[ルート]** 、またはルート テーブルが関連付けられている **[サブネット]** が表示されます。

Azure の一般的な設定の詳細については、次の情報を参照してください。

- [アクティビティ ログ](../azure-monitor/essentials/platform-logs-overview.md)
- [アクセス制御 (IAM)](../role-based-access-control/overview.md)
- [タグ](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Locks](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Automation スクリプト](../azure-resource-manager/templates/export-template-portal.md)

### <a name="view-details-of-route-table---commands"></a>ルート テーブルの詳細の表示 - コマンド

| ツール | コマンド |
| ---- | ------- |
| Azure CLI | [az network route-table show](/cli/azure/network/route-table#az-network-route-table-show) |
| PowerShell | [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable) |

## <a name="change-a-route-table"></a>ルート テーブルの変更

1. [Azure portal](https://portal.azure.com) に移動して、仮想ネットワークを管理します。 **ルート テーブル** を検索して選択します。

1. ルート テーブルの一覧で、変更するルート テーブルを選択します。

最も一般的な変更点は、ルートの[追加](#create-a-route)、ルートの[削除](#delete-a-route)、サブネットへのルート テーブルの[関連付け](#associate-a-route-table-to-a-subnet)、サブネットからのルート テーブルの[関連付け解除](#dissociate-a-route-table-from-a-subnet)です。

### <a name="change-a-route-table---commands"></a>ルート テーブルの変更 - コマンド

| ツール | コマンド |
| ---- | ------- |
| Azure CLI | [az network route-table update](/cli/azure/network/route-table#az-network-route-table-update) |
| PowerShell | [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) |

## <a name="associate-a-route-table-to-a-subnet"></a>サブネットへのルート テーブルの関連付け

必要に応じて、ルート テーブルをサブネットに関連付けることができます。 1 つのルート テーブルを、0 個以上のサブネットに関連付けることができます。 ルート テーブルは仮想ネットワークに関連付けられていないので、ルート テーブルを関連付ける各サブネットにルート テーブルを関連付ける必要があります。 仮想ネットワークが Azure 仮想ネットワーク ゲートウェイに接続されている場合 (ExpressRoute または VPN)、ルート テーブル内に作成したルート、[既定のルート](virtual-networks-udr-overview.md#default)、およびオンプレミスのネットワークから伝達されたルートに基づいて、サブネットを出るすべてのトラフィックが Azure によってルーティングされます。 ルート テーブルは、そのルート テーブルと同じ Azure の場所とサブスクリプションに存在する仮想ネットワーク内のサブネットにのみ関連付けることができます。

1. [Azure portal](https://portal.azure.com) に移動して、仮想ネットワークを管理します。 **仮想ネットワーク** を検索して選択します。

1. 仮想ネットワークの一覧で、ルート テーブルを関連付けるサブネットが含まれる仮想ネットワークを選択します。

1. 仮想ネットワークのメニュー バーで **[サブネット]** を選択します。

1. ルート テーブルを関連付けるサブネットを選択します。

1. **[ルート テーブル]** で、サブネットに関連付けるルート テーブルを選択します。

1. **[保存]** を選択します。

仮想ネットワークが Azure VPN ゲートウェイに接続されている場合は、宛先が *0.0.0.0/0* であるルートを含む [ゲートウェイ サブネット](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub)にルート テーブルを関連付けないでください。 関連付けると、ゲートウェイが正しく機能しない可能性があります。 ルート上での *0.0.0.0/0* の使用について詳しくは、「[仮想ネットワーク トラフィックのルーティング](virtual-networks-udr-overview.md#default-route)」をご覧ください。

### <a name="associate-a-route-table---commands"></a>ルート テーブルの関連付け - コマンド

| ツール | コマンド |
| ---- | ------- |
| Azure CLI | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="dissociate-a-route-table-from-a-subnet"></a>サブネットからのルート テーブルの関連付けの解除

サブネットからルート テーブルの関連付けを解除すると、Azure では、[既定のルート](virtual-networks-udr-overview.md#default)に基づいてトラフィックがルーティングされます。

1. [Azure portal](https://portal.azure.com) に移動して、仮想ネットワークを管理します。 **仮想ネットワーク** を検索して選択します。

1. 仮想ネットワークの一覧で、ルート テーブルの関連付けを解除するサブネットが含まれる仮想ネットワークを選択します。

1. 仮想ネットワークのメニュー バーで **[サブネット]** を選択します。

1. ルート テーブルの関連付けを解除するサブネットを選択します。

1. **[ルート テーブル]** で、 **[なし]** を選択します。

1. **[保存]** を選択します。

### <a name="dissociate-a-route-table---commands"></a>ルート テーブルの関連付けの解除 - コマンド

| ツール | command |
| ---- | ------- |
| Azure CLI | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="delete-a-route-table"></a>ルート テーブルの削除

いずれかのサブネットに関連付けられているルート テーブルを削除することはできません。 ルート テーブルは、削除する前に、すべてのサブネットから[関連付けを解除](#dissociate-a-route-table-from-a-subnet)してください。

1. [Azure portal](https://portal.azure.com) に移動して、ルート テーブルを管理します。 **ルート テーブル** を検索して選択します。

1. ルート テーブルの一覧で、削除するルート テーブルを選択します。

1. **[削除]** を選択し、確認のダイアログ ボックスで **[はい]** を選択します。

### <a name="delete-a-route-table---commands"></a>ルート テーブルの削除 - コマンド

| ツール | command |
| ---- | ------- |
| Azure CLI | [az network route-table delete](/cli/azure/network/route-table#az-network-route-table-delete) |
| PowerShell | [Remove-AzRouteTable](/powershell/module/az.network/remove-azroutetable) |

## <a name="create-a-route"></a>ルートの作成

Azure の場所およびサブスクリプションあたりの作成可能なルート テーブルごとのルート数は、制限されています。 詳しくは、「[ネットワークの制限 - Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)」をご覧ください。

1. [Azure portal](https://portal.azure.com) に移動して、ルート テーブルを管理します。 **ルート テーブル** を検索して選択します。

1. ルート テーブルの一覧で、ルートを追加するルート テーブルを選択します。

1. ルート テーブルのメニュー バーで、 **[ルート]**  >  **[追加]** を選択します。

1. ルート テーブル内のルートに対する一意の **[ルート名]** を入力します。

1. トラフィックのルーティング先の **[アドレス プレフィックス]** を Classless Inter-Domain Routing (CIDR) 表記で入力します。 ルート テーブル内の複数のルート上でプレフィックスを重複させることはできません。ただし、プレフィックスを別のプレフィックス内に指定することはできます。 たとえば、あるルートでプレフィックスとして *10.0.0.0/16* を定義した場合、別のルートをアドレス プレフィックス *10.0.0.0/22* で定義することは可能です。 Azure では、最長プレフィックス一致に基づいてトラフィックのルートが選択されます。 詳しくは、「[Azure がルートを選択するしくみ](virtual-networks-udr-overview.md#how-azure-selects-a-route)」をご覧ください。

1. **[次ホップの種類]** を選択します。 次ホップの種類について詳しくは、「[仮想ネットワーク トラフィックのルーティング](virtual-networks-udr-overview.md)」をご覧ください。

1. **[次ホップの種類]** で **[仮想アプライアンス]** を選択した場合は、 **[次ホップ アドレス]** に IP アドレスを入力します。

1. **[OK]** を選択します。

### <a name="create-a-route---commands"></a>ルートの作成 - コマンド

| ツール | command |
| ---- | ------- |
| Azure CLI | [az network route-table route create](/cli/azure/network/route-table/route#az-network-route-table-route-create) |
| PowerShell | [New-AzRouteConfig](/powershell/module/az.network/new-azrouteconfig) |

## <a name="view-routes"></a>ルートの表示

ルート テーブルには、0 個以上のルートが含まれます。 ルートを表示したときに一覧に表示される情報について詳しくは、「[仮想ネットワーク トラフィックのルーティング](virtual-networks-udr-overview.md)」をご覧ください。

1. [Azure portal](https://portal.azure.com) に移動して、ルート テーブルを管理します。 **ルート テーブル** を検索して選択します。

1. ルート テーブルの一覧で、ルートを表示するルート テーブルを選択します。

1. ルート テーブルのメニュー バーで、ルートの一覧を表示する **[ルート]** を選択します。

### <a name="view-routes---commands"></a>ルートの表示 - コマンド

| ツール | command |
| ---- | ------- |
| Azure CLI | [az network route-table route list](/cli/azure/network/route-table/route#az-network-route-table-route-list) |
| PowerShell | [Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig) |

## <a name="view-details-of-a-route"></a>ルートの詳細の表示

1. [Azure portal](https://portal.azure.com) に移動して、ルート テーブルを管理します。 **ルート テーブル** を検索して選択します。

1. ルート テーブルの一覧で、詳細を表示するルートが含まれるルート テーブルを選択します。

1. ルート テーブルのメニュー バーで、ルートの一覧を表示する **[ルート]** を選択します。

1. 詳細を表示するルートを選択します。

### <a name="view-details-of-a-route---commands"></a>ルートの詳細の表示 - コマンド

| ツール | command |
| ---- | ------- |
| Azure CLI | [az network route-table route show](/cli/azure/network/route-table/route#az-network-route-table-route-show) |
| PowerShell | [Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig) |

## <a name="change-a-route"></a>ルートの変更

1. [Azure portal](https://portal.azure.com) に移動して、ルート テーブルを管理します。 **ルート テーブル** を検索して選択します。

1. ルート テーブルの一覧で、変更するルートが含まれるルート テーブルを選択します。

1. ルート テーブルのメニュー バーで、ルートの一覧を表示する **[ルート]** を選択します。

1. 変更するルートを選択します。

1. 既存の設定を新しい設定に変更し、 **[保存]** を選択します。

### <a name="change-a-route---commands"></a>ルートの変更 - コマンド

| ツール | command |
| ---- | ------- |
| Azure CLI | [az network route-table route update](/cli/azure/network/route-table/route#az-network-route-table-route-update) |
| PowerShell | [Set-AzRouteConfig](/powershell/module/az.network/set-azrouteconfig) |

## <a name="delete-a-route"></a>ルートの削除

1. [Azure portal](https://portal.azure.com) に移動して、ルート テーブルを管理します。 **ルート テーブル** を検索して選択します。

1. ルート テーブルの一覧で、削除するルートが含まれるルート テーブルを選択します。

1. ルート テーブルのメニュー バーで、ルートの一覧を表示する **[ルート]** を選択します。

1. 削除するルートを選択します。

1. **[削除]** を選択し、確認のダイアログ ボックスで **[はい]** を選択します。

### <a name="delete-a-route---commands"></a>ルートの削除 - コマンド

| ツール | command |
| ---- | ------- |
| Azure CLI | [az network route-table route delete](/cli/azure/network/route-table/route#az-network-route-table-route-delete) |
| PowerShell | [Remove-AzRouteConfig](/powershell/module/az.network/remove-azrouteconfig) |

## <a name="view-effective-routes"></a>有効なルートの表示

VM に接続されている各ネットワーク インターフェイスの有効なルートは、作成したルート テーブル、Azure の既定のルート、および Azure 仮想ネットワーク ゲートウェイを介して Border Gateway Protocol (BGP) 経由でオンプレミスのネットワークから伝達されるルートの組み合わせとなります。 ネットワーク インターフェイスの有効なルートを理解しておくと、ルーティングの問題をトラブルシューティングするときに役立ちます。 実行中の VM に接続されているネットワーク インターフェイスの有効なルートを表示できます。

1. [Azure portal](https://portal.azure.com) に移動して、VM を管理します。 **[仮想マシン]** を検索して選択します。

1. 仮想マシンの一覧で、有効なルートを表示する VM を選択します。

1. VM のメニュー バーで、 **[ネットワーク]** を選択します。

1. ネットワーク インターフェイスの名前を選択します。

1. ネットワーク インターフェイスのメニュー バーで **[有効なルート]** を選択します。

1. 有効なルートの一覧で、トラフィックのルーティング先への適切なルートが存在するかどうかを確認します。 この一覧に表示される次のホップの種類の詳細については、「[仮想ネットワーク トラフィックのルーティング](virtual-networks-udr-overview.md)」をご覧ください。

### <a name="view-effective-routes---commands"></a>有効なルートの表示 - コマンド

| ツール | command |
| ---- | ------- |
| Azure CLI | [az network nic show-effective-route-table](/cli/azure/network/nic#az-network-nic-show-effective-route-table) |
| PowerShell | [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable) |

## <a name="validate-routing-between-two-endpoints"></a>2 つのエンドポイント間のルーティングの検証

仮想マシンと、他の Azure リソース、オンプレミスのリソース、またはインターネット上のリソースの IP アドレス間の次ホップの種類を確認できます。 Azure のルーティングを確認すると、ルーティングの問題をトラブルシューティングするときに役立ちます。 このタスクを完了するには、既存の Network Watcher が必要です。 既存の Network Watcher がない場合は、[Network Watcher のインスタンスの作成](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関するページの手順を実行して作成します。

1. [Azure portal](https://portal.azure.com) に移動して、Network Watcher を管理します。 **Network Watcher** を検索して選択します。

1. Network Watcher のメニュー バーで、 **[次ホップ]** を選択します。

1. **[Network Watcher | 次ホップ]** ページで、次のようにします。

    1. **[サブスクリプション]** と、ルーティングを検証するソース VM の **[リソース グループ]** を選択します。

    1. VM に接続されている **[仮想マシン]** と **[ネットワーク インターフェイス]** を選択します。
    
    1. ルーティングを検証するネットワーク インターフェイスに割り当てられている **[ソース IP アドレス]** を入力します。

    1. ルーティングを検証する **[接続先 IP アドレス]** を入力します。

1. **[次ホップ]** を選択します。

少し待つと、次ホップの種類と、トラフィックをルーティングするルートの ID が表示されます。 返される次ホップの種類の詳細については、「[仮想ネットワーク トラフィックのルーティング](virtual-networks-udr-overview.md)」をご覧ください。

### <a name="validate-routing-between-two-endpoints---commands"></a>2 つのエンドポイント間のルーティングの検証 - コマンド

| ツール | command |
| ---- | ------- |
| Azure CLI | [az network watcher show-next-hop](/cli/azure/network/watcher#az-network-watcher-show-next-hop) |
| PowerShell | [Get-AzNetworkWatcherNextHop](/powershell/module/az.network/get-aznetworkwatchernexthop) |

## <a name="permissions"></a>アクセス許可

ルート テーブルとルートに関するタスクを行うには、使用するアカウントが[ネットワーク共同作成者ロール](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)、または次の表の適切なアクションが割り当てられた[カスタム ロール](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に、割り当てられている必要があります。

| アクション                                                          |   名前                                                  |
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

## <a name="next-steps"></a>次のステップ

- [PowerShell](powershell-samples.md) または [Azure CLI](cli-samples.md) のサンプル スクリプトを使用して、または Azure [Resource Manager テンプレート](template-samples.md)を使用して、ルート テーブルを作成します
- 仮想ネットワーク用に [Azure Policy 定義](./policy-reference.md)を作成して割り当てる