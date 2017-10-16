---
title: "Azure 仮想ネットワーク ピアリングの作成、変更、削除 | Microsoft Docs"
description: "仮想ネットワーク ピアリングを作成、変更、削除する方法について説明します。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: jdial;anavin
ms.openlocfilehash: 9fcfca3de6204581936a2bacfd86e84fd373190a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="create-change-or-delete-a-virtual-network-peering"></a>仮想ネットワーク ピアリングの作成、変更、削除

仮想ネットワーク ピアリングを作成、変更、削除する方法について説明します。 仮想ネットワーク ピアリングによって、仮想ネットワークを Azure のバックボーン ネットワークを介して接続できます。 ピアリング後も、仮想ネットワークは個別のリソースとして管理されます。 仮想ネットワーク ピアリングの知識がない場合は、この記事のタスクを実行する前に、「[仮想ネットワーク ピアリング](virtual-network-peering-overview.md)」を読み、[仮想ネットワーク ピアリングの作成方法のチュートリアル](virtual-network-create-peering.md)を完了することをお勧めします。

同一リージョン内の仮想ネットワーク ピアリングの機能は一般公開されています。 異なるリージョン間での仮想ネットワーク ピアリングは現在プレビュー段階にあり、米国中西部、カナダ中部、および米国西部 2 で提供されています。 [プレビュー版を利用するにはサブスクリプションを登録](virtual-network-create-peering.md)してください。

> [!WARNING]
> このシナリオで作成された仮想ネットワーク ピアリングでは、一般公開リリースのシナリオと同じレベルの可用性と信頼性が得られないことがあります。 また、一部の機能が制限されている場合があります。一部の Azure リージョンではご利用いただけない場合もあります。 この機能の可用性とステータスに関する最新の通知については、[Azure Virtual Network の更新情報](https://azure.microsoft.com/updates/?product=virtual-network)に関するページをご覧ください。
>

## <a name="before-you-begin"></a>開始する前に

この記事のセクションに記載された手順を始める前に、次のタスクを完了してください。

- [Azure の制限](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)に関する記事で、ピアリングの制限について確認します。
- Azure アカウントを使用して、Azure Portal、Azure コマンド ライン インターフェイス (CLI)、または Azure PowerShell にログインします。 まだ Azure アカウントを持っていない場合は、[無料試用版アカウント](https://azure.microsoft.com/free)にサインアップしてください。
- PowerShell コマンドを使用してこの記事のタスクを実行する場合は、[Azure PowerShell をインストールして構成します](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json)。 最新バージョンの Azure PowerShell コマンドレットがインストールされていることを確認してください。 PowerShell コマンドのヘルプとサンプルを表示するには、「`get-help <command> -full`」と入力します。
- Azure コマンド ライン インターフェイス (CLI) のコマンドを使用してこの記事のタスクを実行する場合は、[Azure CLI をインストールして構成します](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)。 最新バージョンの Azure CLI がインストールされていることを確認してください。 CLI コマンドのヘルプを表示するには、「`az <command> --help`」と入力します。 CLI とその前提条件をインストールする代わりに、Azure Cloud Shell を使うことができます。 Azure Cloud Shell は、Azure Portal 内で直接実行できる無料の Bash シェルです。 Cloud Shell には Azure CLI が事前にインストールされており、アカウントで使用できるように構成されています。 Cloud Shell を使うには、[ポータル](https://portal.azure.com)の上部にある [Cloud Shell**> _**] ボタンをクリックします。 

## <a name="create-a-peering"></a>ピアリングの作成

>[!NOTE]
>ピアリングを作成する前に、[要件と制約](#requirements-and-constraints)、[必要なアクセス許可](#permissions)を十分に理解しておいてください。
>

1. 必要な[ロールまたはアクセス許可](#permissions)が割り当てられているアカウントで、[ポータル](https://portal.azure.com)にログインします。
2. Azure Portal の上部にある "*リソースの検索*" というテキストが表示されたボックスに、「*仮想ネットワーク*」と入力します。 検索結果に **[仮想ネットワーク]** が表示されたら、それをクリックします。 一覧に **[仮想ネットワーク (クラシック)]** が表示されていても、これを選択しないでください。クラシック デプロイ モデルを使用してデプロイされた仮想ネットワークからピアリングを作成することはできません。
3. 表示される **[仮想ネットワーク]** ブレードで、ピアリングを作成する仮想ネットワークをクリックします。
4. 選択した仮想ネットワークに対して表示されたウィンドウで、**[設定]** セクションの **[ピアリング]** をクリックします。
5. **[+ 追加]** をクリックします。 
6. <a name="add-peering"></a>**[ピアリングの追加]** ブレードで、次の設定の値を入力または選択します。
    - **[名前]:** ピアリングの名前は、仮想ネットワーク内で一意である必要があります。
    - **[仮想ネットワークのデプロイ モデル]:** ピアリングする仮想ネットワークのデプロイに使用されたデプロイ モデルを選択します。
    - **[リソース ID を知っている]:** ピアリングする仮想ネットワークへの読み取りアクセス権がある場合は、このチェック ボックスをオフのままにしておきます。 ピアリングする仮想ネットワークまたはサブスクリプションへの読み取りアクセス権がない場合は、このボックスをオンにします。 チェック ボックスをオンにしたときに表示される **[リソース ID]** ボックスに、ピアリングする仮想ネットワークの完全なリソース ID を入力します。 この仮想ネットワークと同じ Azure [リージョン](https://azure.microsoft.com/regions)に存在する仮想ネットワークのリソース ID を入力する必要があります。 別のリージョンにある仮想ネットワークを選択する場合、サブスクリプション登録し、[プレビュー版](virtual-network-create-peering.md)をご利用ください。 完全なリソース ID は、/subscriptions/<Id>/resourceGroups/<リソース グループ名>/providers/Microsoft.Network/virtualNetworks/<仮想ネットワーク名> のようになります。 仮想ネットワークのリソース ID を取得するには、仮想ネットワークのプロパティを表示します。 仮想ネットワークのプロパティを表示する方法については、「[仮想ネットワークと設定の表示](virtual-network-manage-network.md#view-vnet)」を参照してください。
    - **[サブスクリプション]:** ピアリングする仮想ネットワークの[サブスクリプション](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)を選択します。 アカウントに読み取りアクセス権があるサブスクリプションの数に応じて、1 つ以上のサブスクリプションが表示されます。 **[リソース ID]** チェック ボックスをオンにした場合、この設定は使用できません。 どちらの仮想ネットワークも Resource Manager を使用して作成されていれば、異なるサブスクリプションの仮想ネットワークをピアリングできます。 プレビュー リリースでは、異なるデプロイ モデルを使用して作成されたサブスクリプション間でピアリングできます。 異なるサブスクリプションに存在する、異なるデプロイ モデルを使用してデプロイされた仮想ネットワークのピアリングを作成する前に、プレビューに登録してください。 プレビューに登録し、[異なるサブスクリプションに存在する、異なるデプロイ モデルを使用して作成された仮想ネットワークをピアリングする](create-peering-different-deployment-models-subscriptions.md)方法の詳細をご覧ください。
    - **[仮想ネットワーク]:** ピアリングする仮想ネットワークを選択します。 いずれかの Azure デプロイ モデルで作成された仮想ネットワークを選択できます。 別のリージョンにある仮想ネットワークを選択する場合、サブスクリプション登録し、[プレビュー版](virtual-network-create-peering.md)をご利用ください。 仮想ネットワークを一覧に表示するには、その仮想ネットワークへの読み取りアクセス権が必要です。 仮想ネットワークが一覧に表示されていても、淡色表示されている場合、仮想ネットワークのアドレス空間がこの仮想ネットワークのアドレス空間と重複している可能性があります。 仮想ネットワークのアドレス空間が重複している場合、それらの仮想ネットワークをピアリングをすることはできません。 **[リソース ID]** チェック ボックスをオンにした場合、この設定は使用できません。
    - **[仮想ネットワーク アクセスを許可する]:** 2 つの仮想ネットワーク間の通信を有効にする場合は、**[有効]** (既定値) を選択します。 仮想ネットワーク間の通信を有効にすると、各仮想ネットワークに接続されているリソースは、同じ仮想ネットワークに接続されている場合と同様に、同じ帯域幅と待機時間で相互に通信できます。 2 つの仮想ネットワーク内のリソース間のすべての通信は、Azure プライベート ネットワーク経由で行われます。 ネットワーク セキュリティ グループの既定の **VirtualNetwork** タグには、仮想ネットワークとピアリングされた仮想ネットワークが含まれます。 ネットワーク セキュリティ グループの既定のタグの詳細については、[ネットワーク セキュリティ グループの概要](virtual-networks-nsg.md#default-tags)に関する記事をご覧ください。  ピアリングされた仮想ネットワークにトラフィックが流れないようにする場合は、**[無効]** を選択します。 仮想ネットワークを別の仮想ネットワークとピアリングしていても、2 つの仮想ネットワーク間のトラフィック フローを無効にする必要がある場合は、**[無効]** を選択できます。 ピアリングを削除し、再作成するよりも、有効化/無効化する方が便利な場合があります。 この設定を無効にすると、ピアリングされた仮想ネットワーク間でトラフィックが流れなくなります。
    - **[転送されたトラフィックを許可する]:** このボックスをオンにすると、ピアリングされた仮想ネットワークに転送されたトラフィック (ピアリングされた仮想ネットワークで発生したものではないトラフィック) をこの仮想ネットワークに流すことができます。 ピアリング先の仮想ネットワークにネットワーク仮想アプライアンスをデプロイし、ネットワーク仮想アプライアンスを介してトラフィックを転送するためのユーザー定義ルートを作成した場合、一般にトラフィックの転送が行われます。 このボックスをオフ (既定値) のままにした場合、ピアリグされた仮想ネットワークの転送されたトラフィックは、この仮想ネットワークに流れることはできません。 この機能を有効にすると、ピアリングを介して転送されたトラフィックが許可されますが、ユーザー定義ルートやネットワーク仮想アプライアンスが作成されるわけではありません。 ユーザー定義ルートとネットワーク仮想アプライアンスは個別に作成されます。 ユーザー定義ルートについては、[こちら](virtual-networks-udr-overview.md)をご覧ください。
    - **[ゲートウェイ転送を許可する]:** この仮想ネットワークに仮想ネットワーク ゲートウェイが接続されており、ピアリングされた仮想ネットワークからのトラフィックがゲートウェイ経由で流れることを許可する場合は、このボックスをオンにします。 たとえば、仮想ネットワーク ゲートウェイを介して、この仮想ネットワークをオンプレミス ネットワークに接続できます。 このボックスをオンにすると、ピアリングされた仮想ネットワークからのトラフィックを、この仮想ネットワークに接続されたゲートウェイ経由で流すことができます。 このボックスをオンにしても、ピアリングされた仮想ネットワークでゲートウェイを構成することはできません。 もう一方の仮想ネットワークからこの仮想ネットワークへのピアリングを設定するときに、ピアリングされた仮想ネットワークで **[リモート ゲートウェイを使用する]** チェック ボックスをオンにしておく必要があります。 このボックスをオフ (既定値) のままにしても、ピアリングされた仮想ネットワークからのトラフィックはこの仮想ネットワークに流れますが、この仮想ネットワークに接続された仮想ネットワーク ゲートウェイ経由で流れることはできません。 仮想ネットワーク ゲートウェイの詳細については、[こちら](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti)をご覧ください。 
    
        仮想ネットワーク (Resource Manager) を仮想ネットワーク (クラシック) とピアリングしている場合、このオプションを有効にすることはできません。 トラフィックは 2 つの仮想ネットワーク間で流れますが、仮想ネットワーク (クラシック) のトラフィックは、仮想ネットワーク (Resource Manager) に接続されたネットワーク ゲートウェイ経由で流れることはできません。

    - **[リモート ゲートウェイを使用する]:** この仮想ネットワークからのトラフィックが、ピアリングされた仮想ネットワークに接続された仮想ネットワーク ゲートウェイ経由で流れることを許可する場合は、このボックスをオンにします。 たとえば、ピアリングされた仮想ネットワークに、オンプレミス ネットワークとの通信を可能にする VPN Gateway が接続されているとします。  このボックスをオンにすると、この仮想ネットワークからのトラフィックを、ピアリングされた仮想ネットワークに接続された VPN Gateway 経由で流すことができます。 このボックスをオンにする場合、ピアリングされた仮想ネットワークに仮想ネットワーク ゲートウェイが接続されている必要があります。また、ピアリングされた仮想ネットワークで **[ゲートウェイ転送を許可する]** チェック ボックスがオンになっている必要があります。 このボックスをオフ (既定値) のままにしても、ピアリングされた仮想ネットワークからのトラフィックはこの仮想ネットワークに流れますが、この仮想ネットワークに接続された仮想ネットワーク ゲートウェイ経由で流れることはできません。 
    
        仮想ネットワーク (Resource Manager) を仮想ネットワーク (クラシック) とピアリングしている場合、このオプションを有効にすることはできません。 トラフィックは 2 つの仮想ネットワーク間で流れますが、仮想ネットワーク (Resource Manager) のトラフィックは、仮想ネットワーク (クラシック) に接続されたネットワーク ゲートウェイ経由で流れることはできません。
7. **[OK]** をクリックすると、選択した仮想ネットワークにサブネットが追加されます。

### <a name="commands"></a>コマンド

|ツール|コマンド|
|---|---|
|CLI|[az network vnet peering create](/cli/azure/network/vnet/peering#create?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[Add-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/add-azurermvirtualnetworkpeering?toc=%2fazure%2fvirtual-network%2ftoc.json)|


### <a name="scenarios"></a>シナリオ

仮想ネットワーク ピアリングは、同じまたは異なるサブスクリプションに存在する同じまたは異なるデプロイメント モデルを使って作成された仮想ネットワーク間に作成されます。 次のいずれかのシナリオについて、手順を説明したチュートリアルを完了します。
 
|Azure デプロイメント モデル  | [サブスクリプション]  |
|---------|---------|
|両方が Resource Manager |[同じ](virtual-network-create-peering.md)|
| |[異なる](create-peering-different-subscriptions.md)|
|一方が Resource Manager、もう一方がクラシック     |[同じ](create-peering-different-deployment-models.md)|
| |[異なる](create-peering-different-deployment-models-subscriptions.md)|

## <a name="view-or-change-peering-settings"></a>ピアリング設定の表示または変更

1. 必要な[ロールまたはアクセス許可](#permissions)が割り当てられているアカウントで、[ポータル](https://portal.azure.com)にログインします。
2. Azure Portal の上部にある "*リソースの検索*" というテキストが表示されたボックスに、「*仮想ネットワーク*」と入力します。 検索結果に **[仮想ネットワーク]** が表示されたら、それをクリックします。
3. 表示される **[仮想ネットワーク]** ブレードで、ピアリングを作成する仮想ネットワークをクリックします。
4. 選択した仮想ネットワークに対して表示されたウィンドウで、**[設定]** セクションの **[ピアリング]** をクリックします。
5. 設定を表示または変更するピアリングをクリックします。
6. 該当する設定を変更します。 各設定のオプションについては、この記事の「ピアリングの作成」の[手順 6.](#add-peering) を参照してください。 

    >[!NOTE]
    >ピアリングを作成する前に、[要件と制約](#requirements-and-constraints)、[必要なアクセス許可](#permissions)を十分に理解しておいてください。
    >

7. [ **Save**] をクリックします。

**コマンド**

|ツール|コマンド|
|---|---|
|CLI|[az network vnet peering list](/cli/azure/network/vnet/peering?toc=%2fazure%2fvirtual-network%2ftoc.json#list) (仮想ネットワークのピアリングを一覧表示する)、[az network vnet peering show](/cli/azure/network/vnet/peering?toc=%2fazure%2fvirtual-network%2ftoc.json#show) (特定のピアリングの設定を表示する)、[az network vnet peering update](/cli/azure/network/vnet/peering?toc=%2fazure%2fvirtual-network%2ftoc.json#update) (ピアリング設定を変更する)|
|PowerShell|[Get-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/get-azurermvirtualnetworkpeering?toc=%2fazure%2fvirtual-network%2ftoc.json) (ピアリング設定を取得する)、[Set-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/set-azurermvirtualnetworkpeering?toc=%2fazure%2fvirtual-network%2ftoc.json) (設定を変更する)|

## <a name="delete-a-peering"></a>ピアリングの削除
ピアリングを削除すると、仮想ネットワークからのトラフィックはピアリングされた仮想ネットワークに流れなくなります。 Resource Manager を使用してデプロイされた仮想ネットワークをピアリングしている場合、それぞれの仮想ネットワークにもう一方の仮想ネットワークとのピアリングが存在します。 一方の仮想ネットワークからピアリングを削除すると、仮想ネットワーク間の通信が無効になりますが、もう一方の仮想ネットワークからピアリングが削除されるわけではありません。 もう一方の仮想ネットワークに存在するピアリングの状態が "**切断**" になります。 1 つ目の仮想ネットワークのピアリングを再作成し、両方の仮想ネットワークのピアリングの状態が "*接続済み*" に変わるまで、ピアリングを再作成することはできません。 

仮想ネットワーク間で通信することはあっても、常に通信するわけではない場合は、ピアリングを削除するのではなく、**[仮想ネットワーク アクセスを許可する]** を **[無効]** に設定します。 方法については、この記事の「[ピアリングの作成](#create-peering)」の手順 6. を参照してください。 ピアリングを削除し、再作成するよりも、ネットワーク アクセスを無効化/有効化する方が簡単な場合があります。

1. 必要な[ロールまたはアクセス許可](#permissions)が割り当てられているアカウントで、[ポータル](https://portal.azure.com)にログインします。
2. Azure Portal の上部にある "*リソースの検索*" というテキストが表示されたボックスに、「*仮想ネットワーク*」と入力します。 検索結果に **[仮想ネットワーク]** が表示されたら、それをクリックします。
3. 表示される **[仮想ネットワーク]** ブレードで、ピアリングを削除する仮想ネットワークをクリックします。
4. 選択した仮想ネットワークに対して表示されたブレードで、**[設定]** の **[ピアリング]** をクリックします。
5. ピアリング ブレードに表示されたピアリングの一覧で、削除するピアリングを右クリックして **[削除]** をクリックし、**[はい]** をクリックして 1 つ目の仮想ネットワークからピアリングを削除します。
6. 上記の手順を繰り返して、ピアリングのもう一方の仮想ネットワークからピアリングを削除します。

**コマンド**

|ツール|コマンド|
|---|---|
|CLI|[az network vnet peering delete](/cli/azure/network/vnet/peering?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|PowerShell|[Remove-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/remove-azurermvirtualnetworkpeering?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="requirements-and-constraints"></a>要件と制約 

- ピアリングする仮想ネットワークの IP アドレス空間が重複していてはいけません。
- 仮想ネットワークを別の仮想ネットワークとピアリングした後に、仮想ネットワークに対してアドレス空間の追加または削除を実行することはできません。 アドレス空間を追加または削除するには、ピアリングを削除し、アドレス空間を追加または削除してからピアリングを再作成します。 仮想ネットワークに対してアドレス空間の追加または削除を実行する方法については、「[仮想ネットワークの作成、変更、削除](virtual-network-manage-network.md#add-address-spaces)」をご覧ください。
- Resource Manager を使用してデプロイされた 2 つの仮想ネットワーク、または Resource Manager を使用してデプロイされた仮想ネットワークとクラシック デプロイ モデルを使用してデプロイされた仮想ネットワークをピアリングできます。 クラシック デプロイ モデルを使用して作成された 2 つの仮想ネットワークをピアリングすることはできません。 Azure デプロイ モデルの知識がない場合は、[Azure デプロイ モデルの概要](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事をご覧ください。 クラシック デプロイメント モデルを使って作成された 2 つの仮想ネットワークは、[VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V) を使用して接続できます。
- Resource Manager を使用して作成された 2 つの仮想ネットワークをピアリングするときは、ピアリングする仮想ネットワークごとにピアリングを構成する必要があります。 
    - *開始済み:* 1 つ目の仮想ネットワークから 2 つ目の仮想ネットワークへのピアリングを作成すると、ピアリングの状態が "*開始済み*" になります。 
    - *接続済み:* 2 つ目の仮想ネットワークから 1 つ目の仮想ネットワークへのピアリングを作成すると、ピアリングの状態が "*接続済み*" になります。 1 つ目の仮想ネットワークのピアリングの状態を確認すると、状態が "*開始済み*" から "*接続済み*" に変わっていることがわかります。 両方の仮想ネットワーク ピアリングの状態が "*接続済み*" になるまで、ピアリングは正常に確立されません。
- Resource Manager を使用して作成された仮想ネットワークを、クラシック デプロイ モデルを使用して作成された仮想ネットワークとピアリングするときは、Resource Manager を使用してデプロイされた仮想ネットワークのピアリングだけを構成します。 仮想ネットワーク (クラシック) のピアリングや、クラシック デプロイ モデルを使用してデプロイされた 2 つの仮想ネットワークのピアリングを構成することはできません。 仮想ネットワーク (Resource Manager) から仮想ネットワーク (クラシック) へのピアリングを作成すると、ピアリングの状態が "*更新中*" になった後、すぐに "*接続済み*" に変わります。
- ピアリングは 2 つの仮想ネットワーク間で確立されます。 ピアリングは推移的ではありません。 次の仮想ネットワーク間のピアリングを作成したとします。
    - VirtualNetwork1 と VirtualNetwork2
    - VirtualNetwork2 と VirtualNetwork3

  この場合、VirtualNetwork2 を介して、VirtualNetwork1 と VirtualNetwork3 のピアリングが確立されるわけではありません。 VirtualNetwork1 と VirtualNetwork3 の間に仮想ネットワーク ピアリングを作成する場合は、VirtualNetwork1 と VirtualNetwork3 の間にピアリングを作成する必要があります。
- 既定の Azure 名前解決を使用して、ピアリングされた仮想ネットワークで名前を解決することはできません。 他の仮想ネットワークで名前を解決するには、カスタム DNS サーバーを使用する必要があります。 独自の DNS サーバーを設定する方法については、「[独自 DNS サーバー使用の名前解決](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server)」をご覧ください。
- ピアリングされた 2 つの仮想ネットワークのリソースは、同じ仮想ネットワークに存在する場合と同様に、同じ帯域幅と待機時間で相互に通信できます。 ただし、仮想マシンのサイズごとに独自の最大ネットワーク帯域幅が設定されています。 さまざまなサイズの仮想マシンの最大ネットワーク帯域幅の詳細については、[Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) または [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) の VM サイズに関する記事を参照してください。
- 同じサブスクリプションまたは異なるサブスクリプションに存在する、Resource Manager を使用してデプロイされた仮想ネットワークをピアリングできます。
- 同一または異なるサブスクリプションに存在する、異なるデプロイメント モデルを使用してデプロイされた仮想ネットワークをピアリングできます (プレビュー)。 
- 各仮想ネットワークが存在するサブスクリプションは、同じ Azure Active Directory テナントに関連付けられている必要があります。 AD テナントをまだ持っていない場合は、簡単に[作成](../active-directory/develop/active-directory-howto-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json#start-from-scratch)できます。 別々の Active Directory テナントに関連付けられた異なるサブスクリプション内の 2 つの仮想ネットワークは、[VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V) を使って接続することができます。
- 仮想ネットワークは、別の仮想ネットワークとピアリングすることができ、Azure 仮想ネットワーク ゲートウェイを使用して別の仮想ネットワークに接続することもできます。 ピアリングとゲートウェイの両方を使用して仮想ネットワークが接続されている場合、仮想ネットワーク間のトラフィックは、ゲートウェイではなく、ピアリング構成を介して流れます。
- 仮想ネットワーク ピアリングを利用するイグレス トラフィックとエグレス トラフィックには少額の料金が発生します。 詳細については、 [価格に関するページ](https://azure.microsoft.com/pricing/details/virtual-network)を参照してください。


## <a name="permissions"></a>アクセス許可

仮想ネットワーク ピアリングの作成に使用するアカウントには、必要なロールまたはアクセス許可が割り当てられている必要があります。 たとえば、myVnetA と myVnetB という名前の 2 つの仮想ネットワークをピアリングする場合、各仮想ネットワークに対する次の最小限のロールまたはアクセス許可をアカウントに割り当てる必要があります。
    
|Virtual Network|デプロイメント モデル|役割|アクセス許可|
|---|---|---|---|
|myVnetA|リソース マネージャー|[ネットワークの共同作業者](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |クラシック|[従来のネットワークの共同作業者](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|該当なし|
|myVnetB|リソース マネージャー|[ネットワークの共同作業者](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||クラシック|[従来のネットワークの共同作業者](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

[組み込みロール](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)と、特定のアクセス許可を[カスタム ロール](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に割り当てる方法 (Resource Manager のみ) の詳細をご覧ください。

## <a name="next-steps"></a>次のステップ

[ハブおよびスポーク ネットワーク トポロジ](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering)を作成する方法を学習します 
