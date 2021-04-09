---
title: Azure 仮想ネットワーク ピアリングの作成、変更、削除 | Microsoft Docs
description: 仮想ネットワーク ピアリングを作成、変更、削除します。 仮想ネットワーク ピアリングを使用して、同じリージョン内およびリージョン間で仮想ネットワークを接続します。
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/01/2019
ms.author: altambaw
ms.openlocfilehash: dc8db3f1eccce2bb85f03d51fcfd1c4113823d49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98222668"
---
# <a name="create-change-or-delete-a-virtual-network-peering"></a>仮想ネットワーク ピアリングの作成、変更、削除

仮想ネットワーク ピアリングを作成、変更、削除する方法について説明します。 仮想ネットワーク ピアリングにより、同じリージョン内と異なるリージョン間 (グローバル VNet ピアリングとも呼ばれます) で仮想ネットワークを Azure バックボーン ネットワークを介して接続できます。 ピアリング後も、仮想ネットワークは個別のリソースとして管理されます。 仮想ネットワーク ピアリングが初めての方は、「[virtual network peering overview](virtual-network-peering-overview.md)」、または[チュートリアル](tutorial-connect-virtual-networks-portal.md)を行うことで詳しく学習できます。

## <a name="before-you-begin"></a>開始する前に

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

この記事のセクションに記載された手順を始める前に、次のタスクを完了してください。

- まだ Azure アカウントを持っていない場合は、[無料試用版アカウント](https://azure.microsoft.com/free)にサインアップしてください。
- ポータルを使う場合は、 https://portal.azure.com を開き、ピアリングの作業に[必要なアクセス許可](#permissions)を持つアカウントでログインします。
- PowerShell コマンドを使用してこの記事のタスクを実行する場合は、[Azure Cloud Shell](https://shell.azure.com/powershell) でコマンドを実行するか、お使いのコンピューターから PowerShell を実行してください。 Azure Cloud Shell は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。 一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。 このチュートリアルには、Azure PowerShell モジュール バージョン 1.0.0 以降が必要です。 インストールされているバージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、Azure との接続を作成するには、ピアリングの作業に[必要なアクセス許可](#permissions)を持つアカウントで `Connect-AzAccount` を実行する必要もあります。
- Azure コマンド ライン インターフェイス (CLI) コマンドを使用してこの記事のタスクを実行する場合は、[Azure Cloud Shell](https://shell.azure.com/bash) でコマンドを実行するか、お使いのコンピューターから CLI を実行してください。 このチュートリアルには、Azure CLI のバージョン 2.0.31 以降が必要です。 インストールされているバージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。 Azure CLI をローカルで実行している場合、Azure との接続を作成するには、ピアリングの作業に[必要なアクセス許可](#permissions)を持つアカウントで `az login` を実行する必要もあります。

Azure へのログインまたは接続に使用するアカウントは、[ネットワークの共同作業者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)ロール、または「[アクセス許可](#permissions)」の一覧に記載されている適切なアクションが割り当てられている[カスタム ロール](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に割り当てられている必要があります。

## <a name="create-a-peering"></a>ピアリングの作成

ピアリングを作成する前に、要件と制約、[必要なアクセス許可](#permissions)を十分に理解しておいてください。

1. Azure Portal の上部にある検索ボックスに、「*仮想ネットワーク*」と入力します。 検索結果に **[仮想ネットワーク]** が表示されたら、それを選択します。 一覧に **[仮想ネットワーク (クラシック)]** が表示されていても、これを選択しないでください。クラシック デプロイ モデルを使用してデプロイされた仮想ネットワークからピアリングを作成することはできません。
2. ピアリングを作成する仮想ネットワークを一覧から選択します。
3. **[設定]** で **[ピアリング]** を選択します。
4. **[+ 追加]** を選択します。 
5. <a name="add-peering"></a>次の設定の値を入力または選択します。
    - **[名前]:** ピアリングの名前は、仮想ネットワーク内で一意である必要があります。
    - **[仮想ネットワークのデプロイ モデル]:** ピアリングする仮想ネットワークのデプロイに使用されたデプロイ モデルを選択します。
    - **[リソース ID を知っている]:** ピアリングする仮想ネットワークへの読み取りアクセス権がある場合は、このチェック ボックスをオフのままにしておきます。 ピアリングする仮想ネットワークまたはサブスクリプションへの読み取りアクセス権がない場合は、このボックスをオンにします。 チェック ボックスをオンにしたときに表示される **[リソース ID]** ボックスに、ピアリングする仮想ネットワークの完全なリソース ID を入力します。 この仮想ネットワークと同じ Azure [リージョン](https://azure.microsoft.com/regions)、または[サポートされている異なる](#requirements-and-constraints) Azure リージョンに存在する、仮想ネットワークのリソース ID を入力する必要があります。 完全なリソース ID は `/subscriptions/<Id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>` のようになります。 仮想ネットワークのリソース ID を取得するには、仮想ネットワークのプロパティを表示します。 仮想ネットワークのプロパティを表示する方法については、「[仮想ネットワークと設定の表示](manage-virtual-network.md#view-virtual-networks-and-settings)」を参照してください。 サブスクリプションが、ピアリングを作成している仮想ネットワークを含むサブスクリプションと異なる Azure Active Directory テナントに関連付けられている場合、まず各テナントのユーザーを[ゲスト ユーザー](../active-directory/external-identities/add-users-administrator.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-guest-users-to-the-directory)として他方のテナントに追加します。
    - **サブスクリプション:** ピアリングする仮想ネットワークの [サブスクリプション](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)を選択します。 アカウントに読み取りアクセス権があるサブスクリプションの数に応じて、1 つ以上のサブスクリプションが表示されます。 **[リソース ID]** チェック ボックスをオンにした場合、この設定は使用できません。
    - **仮想ネットワーク:** ピアリングする仮想ネットワークを選択します。 いずれかの Azure デプロイ モデルで作成された仮想ネットワークを選択できます。 異なるリージョンの仮想ネットワークを選ぶ場合は、[サポートされているリージョン](#cross-region)の仮想ネットワークを選ぶ必要があります。 仮想ネットワークを一覧に表示するには、その仮想ネットワークへの読み取りアクセス権が必要です。 仮想ネットワークが一覧に表示されていても、淡色表示されている場合、仮想ネットワークのアドレス空間がこの仮想ネットワークのアドレス空間と重複している可能性があります。 仮想ネットワークのアドレス空間が重複している場合、それらの仮想ネットワークをピアリングをすることはできません。 **[リソース ID]** チェック ボックスをオンにした場合、この設定は使用できません。
    - **[仮想ネットワーク アクセスを許可する]** : 既定の `VirtualNetwork` フローを通過する 2 つの仮想ネットワーク間の通信を有効にする場合は、 **[有効]** (既定値) を選択します。 仮想ネットワーク間の通信を有効にすると、各仮想ネットワークに接続されているリソースは、同じ仮想ネットワークに接続されている場合と同様に、同じ帯域幅と待機時間で相互に通信できます。 2 つの仮想ネットワーク内のリソース間のすべての通信は、Azure プライベート ネットワーク経由で行われます。 ネットワーク セキュリティ グループの **VirtualNetwork** サービス タグには、この設定が **[有効]** になっているとき、仮想ネットワークとピアリングされた仮想ネットワークが含まれます。 (ネットワーク セキュリティ グループのサービス タグについて詳しくは、[ネットワーク セキュリティ グループの概要](./network-security-groups-overview.md#service-tags)に関する記事をご覧ください)ピアリングされた仮想ネットワークにトラフィックが既定で流れないようにする場合は、 **[無効]** を選択します。 仮想ネットワークを別の仮想ネットワークとピアリングしていても、2 つの仮想ネットワーク間の既定のトラフィック フローを無効にする必要がある場合は、 **[無効]** を選択できます。 ピアリングを削除し、再作成するよりも、有効化/無効化する方が便利な場合があります。 この設定が無効のとき、ピアリングされた仮想ネットワーク間では既定でトラフィックが流れません。ただし、適切な IP アドレスまたはアプリケーション セキュリティ グループが含まれる[ネットワーク セキュリティ グループ](./network-security-groups-overview.md) ルール経由で明示的に許可されている場合、トラフィックが流れることがあります。
      > [!WARNING]
      > **[仮想ネットワーク アクセスを許可する]** 設定を無効にすると、**VirtualNetwork** サービス タグの定義のみが変更されます。 この設定説明に明記されているように、ピア接続間でトラフィック フローが完全に阻止されることは "*ありません*"。    
    - **[転送されたトラフィックを許可する]:** このボックスをオンにすると、仮想ネットワーク内のネットワーク仮想アプライアンスによって *転送* されたトラフィック (仮想ネットワークから発生したものではないもの) を、ピアリングを通じてこの仮想ネットワークに流すことができます。 たとえば、Spoke1、Spoke2、およびハブという 3 つの仮想ネットワークがあるとします。 各スポーク仮想ネットワークとハブ仮想ネットワークの間にはピアリングが存在しますが、スポーク仮想ネットワーク間にはピアリングが存在しません。 ハブ仮想ネットワークにはネットワーク仮想アプライアンスがデプロイされており、ユーザー定義ルートは、ネットワーク仮想アプライアンスを通じてサブネット間のトラフィックをルーティングする、各スポーク仮想ネットワークに適用されます。 各スポーク仮想ネットワークとハブ仮想ネットワーク間のピアリングに対してこのチェック ボックスをオフにした場合、仮想ネットワーク間のトラフィックをハブが転送しないため、スポーク仮想ネットワーク間ではトラフィックは流れません。 この機能を有効にすると、ピアリングを介して転送されたトラフィックが許可されますが、ユーザー定義ルートやネットワーク仮想アプライアンスが作成されるわけではありません。 ユーザー定義ルートとネットワーク仮想アプライアンスは個別に作成されます。 ユーザー定義ルートについては、[こちら](virtual-networks-udr-overview.md#user-defined)をご覧ください。 Azure VPN Gateway を通じて仮想ネットワーク間でトラフィックが転送される場合は、この設定をオンにする必要はありません。
    - **[ゲートウェイ転送を許可する]:** この仮想ネットワークに仮想ネットワーク ゲートウェイが接続されており、ピアリングされた仮想ネットワークからのトラフィックがゲートウェイ経由で流れることを許可する場合は、このボックスをオンにします。 たとえば、仮想ネットワーク ゲートウェイを介して、この仮想ネットワークをオンプレミス ネットワークに接続できます。 ゲートウェイは、ExpressRoute または VPN ゲートウェイを指定できます。 このボックスをオンにすると、ピアリングされた仮想ネットワークからのトラフィックを、この仮想ネットワークに接続されたゲートウェイ経由でオンプレミス ネットワークに流すことができます。 このボックスをオンにしても、ピアリングされた仮想ネットワークでゲートウェイを構成することはできません。 もう一方の仮想ネットワークからこの仮想ネットワークへのピアリングを設定するときに、ピアリングされた仮想ネットワークで **[リモート ゲートウェイを使用する]** チェック ボックスをオンにしておく必要があります。 このボックスをオフ (既定値) のままにしても、ピアリングされた仮想ネットワークからのトラフィックはこの仮想ネットワークに流れますが、この仮想ネットワークに接続された仮想ネットワーク ゲートウェイ経由で流れることはできません。 ピアリングが仮想ネットワーク (Resource Manager) と仮想ネットワーク (クラシック) の間で行われる場合、ゲートウェイは仮想ネットワーク (Resource Manager) 内にある必要があります。

       VPN ゲートウェイでは、トラフィックをオンプレミス ネットワークに転送するだけでなく、自身が置かれている仮想ネットワークとピアリングされた仮想ネットワーク間のネットワーク トラフィックを、それらの仮想ネットワークを相互にピアリングすることなく転送できます。 VPN ゲートウェイを使用したトラフィックの転送は、ハブ内の VPN ゲートウェイを使用して、相互にピアリングされていないスポーク仮想ネットワーク間のトラフィックをルーティングする場合に便利です ( **[転送されたトラフィックを許可する]** について説明したハブとスポークの例をご覧ください)。 転送へのゲートウェイの使用許可について詳しくは、「[仮想ネットワーク ピアリングの VPN ゲートウェイ転送を構成する](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json)」をご覧ください。 このシナリオを実行するには、次ホップの種類として仮想ネットワーク ゲートウェイを指定したユーザー定義ルートを実装する必要があります。 ユーザー定義ルートについては、[こちら](virtual-networks-udr-overview.md#user-defined)をご覧ください。 ユーザー定義ルートで次ホップの種類として指定できるのは、VPN ゲートウェイだけです。ユーザー定義ルートで、次ホップの種類として ExpressRoute ゲートウェイを指定することはできません。

    - **[リモート ゲートウェイを使用する]:** この仮想ネットワークからのトラフィックが、ピアリングされた仮想ネットワークに接続された仮想ネットワーク ゲートウェイ経由で流れることを許可する場合は、このボックスをオンにします。 たとえば、ピアリングされた仮想ネットワークに、オンプレミス ネットワークとの通信を可能にする VPN Gateway が接続されているとします。  このボックスをオンにすると、この仮想ネットワークからのトラフィックを、ピアリングされた仮想ネットワークに接続された VPN Gateway 経由で流すことができます。 このボックスをオンにする場合、ピアリングされた仮想ネットワークに仮想ネットワーク ゲートウェイが接続されている必要があります。また、ピアリングされた仮想ネットワークで **[ゲートウェイ転送を許可する]** チェック ボックスがオンになっている必要があります。 このボックスをオフ (既定値) のままにしても、ピアリングされた仮想ネットワークからのトラフィックはこの仮想ネットワークに流れますが、この仮想ネットワークに接続された仮想ネットワーク ゲートウェイ経由で流れることはできません。
    
      この設定は、この仮想ネットワークの 1 つのピアリングに対してのみ有効にすることができます。

      仮想ネットワーク内にゲートウェイが既に構成されている場合、リモート ゲートウェイを使うことはできません。 転送へのゲートウェイの使用について詳しくは、「[仮想ネットワーク ピアリングの VPN ゲートウェイ転送を構成する](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json)」をご覧ください。
        
    > [!NOTE]
    > Virtual Network ゲートウェイを使用してオンプレミスのトラフィックをピアリングされた VNet に推移的に送信する場合は、オンプレミスの VPN デバイスのピアリングされた VNet IP の範囲を "対象" トラフィックに設定する必要があります。 そうしないと、オンプレミスのリソースがピアリングされた VNet 内のリソースと通信できなくなります。

6. **[OK]** を選んで、選んだ仮想ネットワークにサブネットを追加します。

異なるサブスクリプションおよびデプロイ モデルの仮想ネットワーク間にピアリングを実装する手順については、[次の手順](#next-steps)をご覧ください。

### <a name="commands"></a>コマンド

- **Azure CLI**: [az network vnet peering create](/cli/azure/network/vnet/peering)
- **PowerShell**:[Add-AzVirtualNetworkPeering](/powershell/module/az.network/add-azvirtualnetworkpeering)

## <a name="view-or-change-peering-settings"></a>ピアリング設定の表示または変更

ピアリングを変更する前に、要件と制約、[必要なアクセス許可](#permissions)を十分に理解しておいてください。

1. ポータルの上部にある検索ボックスに、「*仮想ネットワーク*」と入力します。 検索結果に **[仮想ネットワーク]** が表示されたら、それを選択します。 一覧に **[仮想ネットワーク (クラシック)]** が表示されていても、これを選択しないでください。クラシック デプロイ モデルを使用してデプロイされた仮想ネットワークからピアリングを作成することはできません。
2. ピアリング設定を変更する仮想ネットワークを一覧から選択します。
3. **[設定]** で **[ピアリング]** を選択します。
4. 設定を表示または変更するピアリングを選びます。
5. 該当する設定を変更します。 各設定のオプションについては、「ピアリングの作成」の[手順 5](#add-peering) を参照してください。
6. **[保存]** を選択します。

**コマンド**

- **Azure CLI**: [az network vnet peering list](/cli/azure/network/vnet/peering) (仮想ネットワークのピアリングを一覧表示する)、[az network vnet peering show](/cli/azure/network/vnet/peering) (特定のピアリングの設定を表示する)、[az network vnet peering update](/cli/azure/network/vnet/peering) (ピアリング設定を変更する)|
- **PowerShell**:[Get-AzureRmVirtualNetworkPeering](/powershell/module/az.network/get-azvirtualnetworkpeering) (ピアリング設定を取得する)、[Set-AzureRmVirtualNetworkPeering](/powershell/module/az.network/set-azvirtualnetworkpeering) (設定を変更する)

## <a name="delete-a-peering"></a>ピアリングの削除

ピアリングを削除する前に、[必要なアクセス許可](#permissions)がアカウントにあることを確認してください。

ピアリングを削除すると、仮想ネットワークからのトラフィックはピアリングされた仮想ネットワークに流れなくなります。 Resource Manager を使用してデプロイされた仮想ネットワークをピアリングしている場合、それぞれの仮想ネットワークにもう一方の仮想ネットワークとのピアリングが存在します。 一方の仮想ネットワークからピアリングを削除すると、仮想ネットワーク間の通信が無効になりますが、もう一方の仮想ネットワークからピアリングが削除されるわけではありません。 もう一方の仮想ネットワークに存在するピアリングの状態が "**切断**" になります。 1 つ目の仮想ネットワークのピアリングを再作成し、両方の仮想ネットワークのピアリングの状態が "*接続済み*" に変わるまで、ピアリングを再作成することはできません。

仮想ネットワーク間で通信することはあっても、常に通信するわけではない場合は、ピアリングを削除するのではなく、 **[仮想ネットワーク アクセスを許可する]** を **[無効]** に設定します。 方法については、この記事の「ピアリングの作成」の手順 6 を参照してください。 ピアリングを削除し、再作成するよりも、ネットワーク アクセスを無効化/有効化する方が簡単な場合があります。

1. ポータルの上部にある検索ボックスに、「*仮想ネットワーク*」と入力します。 検索結果に **[仮想ネットワーク]** が表示されたら、それを選択します。 一覧に **[仮想ネットワーク (クラシック)]** が表示されていても、これを選択しないでください。クラシック デプロイ モデルを使用してデプロイされた仮想ネットワークからピアリングを作成することはできません。
2. ピアリングを削除する仮想ネットワークを一覧から選択します。
3. **[設定]** で **[ピアリング]** を選択します。
4. 削除するピアリングの右側にある **[...]** を選択し、 **[削除]** 、 **[はい]** の順に選択して、最初の仮想ネットワークからピアリングを削除します。
5. 上記の手順を繰り返して、ピアリングのもう一方の仮想ネットワークからピアリングを削除します。

**コマンド**

- **Azure CLI**: [az network vnet peering delete](/cli/azure/network/vnet/peering)
- **PowerShell**:[Remove-AzVirtualNetworkPeering](/powershell/module/az.network/remove-azvirtualnetworkpeering)

## <a name="requirements-and-constraints"></a>要件と制約

- <a name="cross-region"></a>同じリージョンまたは異なるリージョンの仮想ネットワークをピアリングできます。 異なるリージョン内の仮想ネットワークのピアリングは "*グローバル VNet ピアリング*" とも呼ばれます。 
- グローバル ピアリングの作成では、ピアリングされた仮想ネットワークは Azure パブリック クラウドの任意のリージョン、または China クラウド リージョン、あるいは Government クラウド リージョンに存在できます。 クラウド間ではピアリングできません。 たとえば、Azure パブリック クラウドの VNet は Azure 中国クラウドの VNet にピアリングすることはできません。
- 仮想ネットワーク内のリソースは、グローバルにピアリングされた仮想ネットワークの Basic 内部ロード バランサーのフロントエンド IP アドレスと通信することはできません。 Basic Load Balancer のサポートは、同じリージョン内でのみ存在します。 Standard Load Balancer のサポートは VNet Peering と Global VNet Peering の両方に存在します。 Global VNet Peering 経由では機能しない Basic Load Balancer を使用するサービスについては、[こちら](virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)を参照してください。
- グローバルにピアリングされた仮想ネットワークおよびローカルにピアリングされた仮想ネットワークでは、リモート ゲートウェイを使用でき、ゲートウェイ転送を許可できます。
- 仮想ネットワークが属しているサブスクリプションは異なっていてもかまいません。 異なるサブスクリプションに属する仮想ネットワークをピアリングする場合、両方のサブスクリプションを同じまたは異なる Azure Active Directory テナントに関連付けることができます。 AD テナントをまだ持っていない場合は、[作成](../active-directory/develop/quickstart-create-new-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json-a-new-azure-ad-tenant)できます。
- ピアリングする仮想ネットワークの IP アドレス空間が重複していてはいけません。
- 仮想ネットワークを別の仮想ネットワークとピアリングした後に、仮想ネットワークのアドレス空間に対してアドレス範囲の追加または削除を実行することはできません。 アドレス範囲を追加または削除するには、ピアリングを削除し、アドレス範囲を追加または削除してからピアリングを再作成します。 仮想ネットワークに対してアドレス範囲を追加または削除するには、[仮想ネットワークの管理](manage-virtual-network.md)に関するページを参照してください。
- Resource Manager を使用してデプロイされた 2 つの仮想ネットワーク、または Resource Manager を使用してデプロイされた仮想ネットワークとクラシック デプロイ モデルを使用してデプロイされた仮想ネットワークをピアリングできます。 クラシック デプロイ モデルを使用して作成された 2 つの仮想ネットワークをピアリングすることはできません。 Azure デプロイ モデルの知識がない場合は、[Azure デプロイ モデルの概要](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事をご覧ください。 クラシック デプロイ モデルを使って作成された 2 つの仮想ネットワークは、[VPN Gateway](../vpn-gateway/design.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V) を使用して接続できます。
- Resource Manager を使用して作成された 2 つの仮想ネットワークをピアリングするときは、ピアリングする仮想ネットワークごとにピアリングを構成する必要があります。 ピアリングの状態の種類として次のいずれかが表示されます。 
  - *開始済み:* 1 つ目の仮想ネットワークから 2 つ目の仮想ネットワークへのピアリングを作成すると、ピアリングの状態が "*開始済み*" になります。 
  - *接続済み:* 2 つ目の仮想ネットワークから 1 つ目の仮想ネットワークへのピアリングを作成すると、ピアリングの状態が "*接続済み*" になります。 1 つ目の仮想ネットワークのピアリングの状態を確認すると、状態が "*開始済み*" から "*接続済み*" に変わっていることがわかります。 両方の仮想ネットワーク ピアリングの状態が "*接続済み*" になるまで、ピアリングは正常に確立されません。
- Resource Manager を使用して作成された仮想ネットワークを、クラシック デプロイ モデルを使用して作成された仮想ネットワークとピアリングするときは、Resource Manager を使用してデプロイされた仮想ネットワークのピアリングだけを構成します。 仮想ネットワーク (クラシック) のピアリングや、クラシック デプロイ モデルを使用してデプロイされた 2 つの仮想ネットワークのピアリングを構成することはできません。 仮想ネットワーク (Resource Manager) から仮想ネットワーク (クラシック) へのピアリングを作成すると、ピアリングの状態が "*更新中*" になった後、すぐに "*接続済み*" に変わります。
- ピアリングは 2 つの仮想ネットワーク間で確立されます。 ピアリング単独では推移的ではありません。 次の仮想ネットワーク間のピアリングを作成したとします。
  - VirtualNetwork1 と VirtualNetwork2   - VirtualNetwork1 と VirtualNetwork2
  - VirtualNetwork2 と VirtualNetwork3   - VirtualNetwork2 と VirtualNetwork3


  この場合、VirtualNetwork2 を介して、VirtualNetwork1 と VirtualNetwork3 のピアリングが確立されるわけではありません。 VirtualNetwork1 と VirtualNetwork3 の間に仮想ネットワーク ピアリングを作成する場合は、VirtualNetwork1 と VirtualNetwork3 の間にピアリングを作成する必要があります。 この場合、VirtualNetwork2 を介して、VirtualNetwork1 と VirtualNetwork3 のピアリングが確立されるわけではありません。 VirtualNetwork1 と VirtualNetwork3 が直接通信するようにしたい場合は、VirtualNetwork1 と VirtualNetwork3 の間に明示的なピアリングを作成するか、またはハブ ネットワークで NVA を実行する必要があります。  
- 既定の Azure 名前解決を使用して、ピアリングされた仮想ネットワークで名前を解決することはできません。 他の仮想ネットワークで名前を解決するには、[プライベート ドメインの Azure DNS](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) またはカスタム DNS サーバーを使う必要があります。 独自の DNS サーバーを設定する方法については、「[独自 DNS サーバー使用の名前解決](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)」をご覧ください。
- 同じリージョン内のピアリングされた仮想ネットワークのリソースは、同じ仮想ネットワークに存在する場合と同様に、同じ帯域幅と待機時間で相互に通信できます。 ただし、仮想マシンのサイズごとに独自の最大ネットワーク帯域幅が設定されています。 さまざまなサイズの仮想マシンの最大ネットワーク帯域幅の詳細については、[Windows](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) または [Linux](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) の VM サイズに関する記事を参照してください。
- 仮想ネットワークは、別の仮想ネットワークとピアリングすることができ、Azure 仮想ネットワーク ゲートウェイを使用して別の仮想ネットワークに接続することもできます。 ピアリングとゲートウェイの両方を使用して仮想ネットワークが接続されている場合、仮想ネットワーク間のトラフィックは、ゲートウェイではなく、ピアリング構成を介して流れます。
- 新しいルートをクライアントに確実にダウンロードするには、仮想ネットワーク ピアリングが正常に構成された後で、もう一度ポイント対サイト VPN クライアントをダウンロードする必要があります。
- 仮想ネットワーク ピアリングを利用するイグレス トラフィックとエグレス トラフィックには少額の料金が発生します。 詳細については、 [価格に関するページ](https://azure.microsoft.com/pricing/details/virtual-network)を参照してください。

## <a name="permissions"></a>アクセス許可

仮想ネットワークのピアリングを扱うために使用するアカウントは、次のロールに割り当てる必要があります。

- [ネットワーク共同作成者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor):Resource Manager を通じてデプロイされる仮想ネットワークの場合。
- [従来のネットワーク共同作成者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor):従来のデプロイ モデルを通じてデプロイされる仮想ネットワークの場合。

上記のどのロールにもアカウントが割り当てられていない場合、次の表から必要なアクションが割り当てられる[カスタム ロール](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に割り当てる必要があります。

| アクション                                                          | 名前 |
|---                                                              |---   |
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write  | 仮想ネットワーク A から仮想ネットワーク B へのピアリングを作成するために必要です。仮想ネットワーク A は仮想ネットワーク (Resource Manager) である必要があります          |
| Microsoft.Network/virtualNetworks/peer/action                   | 仮想ネットワーク B (Resource Manager) から仮想ネットワーク A へのピアリングを作成するために必要です                                                       |
| Microsoft.ClassicNetwork/virtualNetworks/peer/action                   | 仮想ネットワーク B (クラシック) から仮想ネットワーク A へのピアリングを作成するために必要です                                                                |
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/read   | 仮想ネットワーク ピアリングの読み取り   |
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/delete | 仮想ネットワーク ピアリングの削除 |

## <a name="next-steps"></a>次のステップ

- 仮想ネットワーク ピアリングは、同じまたは異なるサブスクリプションに存在する同じまたは異なるデプロイメント モデルを使って作成された仮想ネットワーク間に作成されます。 次のいずれかのシナリオのチュートリアルを完了します。

  |Azure デプロイメント モデル             | サブスクリプション  |
  |---------                          |---------|
  |両方が Resource Manager              |[同じ](tutorial-connect-virtual-networks-portal.md)|
  |                                   |[異なる](create-peering-different-subscriptions.md)|
  |一方が Resource Manager、もう一方がクラシック  |[同じ](create-peering-different-deployment-models.md)|
  |                                   |[異なる](create-peering-different-deployment-models-subscriptions.md)|

- [ハブおよびスポーク ネットワーク トポロジ](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json)を作成する方法を学習します
- [PowerShell](powershell-samples.md) または [Azure CLI](cli-samples.md) のサンプル スクリプトを使って、または Azure [Resource Manager テンプレート](template-samples.md)を使って、仮想ネットワーク ピアリングを作成します
- 仮想ネットワーク用に [Azure Policy 定義](./policy-reference.md)を作成して割り当てる