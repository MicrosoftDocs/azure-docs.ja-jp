---
title: Azure 仮想ネットワーク サブネットの追加、変更、削除
titlesuffix: Azure Virtual Network
description: 仮想ネットワークに関する情報が見つかる場所と、Azure で仮想ネットワーク サブネットを追加、変更、削除する方法について説明します。
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2020
ms.author: kumud
ms.openlocfilehash: d591194f09e85c3e34ed4b904249df994a14bff5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100558580"
---
# <a name="add-change-or-delete-a-virtual-network-subnet"></a>仮想ネットワーク サブネットの追加、変更、削除

仮想ネットワーク サブネットを追加、変更、削除する方法について説明します。 仮想ネットワークにデプロイされているすべての Azure リソースは、仮想ネットワーク内のサブネットにデプロイされます。 仮想ネットワークが初めての方は、[仮想ネットワークの概要](virtual-networks-overview.md)に関するページで、あるいは[クイックスタート](quick-create-portal.md)を行うことで詳しく学習できます。 仮想ネットワークの管理の詳細については、「[仮想ネットワークの作成、変更、削除](manage-virtual-network.md)」をご覧ください。

## <a name="before-you-begin"></a>開始する前に

Azure アカウントをお持ちでない場合は、アクティブなサブスクリプションを使用してそれを設定します。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。 その後、この記事のセクションに記載された手順を始める前に、次のいずれかのタスクを完了してください。 

- **ポータル ユーザー**: Azure アカウントで [Azure Portal](https://portal.azure.com) にサインインします。

- **PowerShell ユーザー**:[Azure Cloud Shell](https://shell.azure.com/powershell) でコマンドを実行するか、お使いのコンピューターから PowerShell を実行します。 Azure Cloud Shell は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。 一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。 Azure Cloud Shell のブラウザー タブで、 **[環境の選択]** ドロップダウン リストを見つけ、 **[PowerShell]** を選択します (まだ選択されていない場合)。

    PowerShell をローカルで実行している場合は、Azure PowerShell モジュール バージョン 1.0.0 以降を使用してください。 インストールされているバージョンを確認するには、`Get-Module -ListAvailable Az.Network` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。 また、`Connect-AzAccount` を実行して、Azure との接続を作成します。

- **Azure コマンド ライン インターフェイス (CLI) のユーザー**: [Azure Cloud Shell](https://shell.azure.com/bash) でコマンドを実行するか、お使いのコンピューターから CLI を実行します。 Azure CLI をローカルに実行している場合は、Azure CLI バージョン 2.0.31 以降を使用してください。 インストールされているバージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。 また、`az login` を実行して、Azure との接続を作成します。

Azure へのサインインまたは接続に使用するアカウントは、[ネットワークの共同作成者ロール](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)、または「[アクセス許可](#permissions)」の一覧に記載されている適切なアクションが割り当てられている[カスタム ロール](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に割り当てられている必要があります。

## <a name="add-a-subnet"></a>サブネットの追加

1. [Azure portal](https://portal.azure.com) に移動して、仮想ネットワークを表示します。 **[仮想ネットワーク]** を検索して選択します。

2. サブネットを追加する仮想ネットワークの名前を選択します。

3. **[設定]** で、 **[サブネット]**  >  **[サブネット]** を選択します。

4. **[サブネットの追加]** ダイアログ ボックスで、次の設定の値を入力します。

    | 設定 | 説明 |
    | --- | --- |
    | **名前** | 仮想ネットワーク内で一意となる名前を使用してください。 他の Azure サービスとの最大限の互換性を維持するために、名前の最初の文字として英字を使用することをお勧めします。 たとえば、Azure Application Gateway は、数字で始まる名前を持つサブネットにはデプロイされません。 |
    | **アドレス範囲** | <p>範囲は仮想ネットワークのアドレス空間内で一意である必要があります。 この範囲は、仮想ネットワーク内の他のサブネット アドレス範囲と重複することはできません。 アドレス空間は、Classless Inter-Domain Routing (CIDR) 表記で指定する必要があります。</p><p>例えば、アドレス空間が *10.0.0.0/16* の仮想ネットワークでは、 *10.0.0.0/22* のサブネット アドレス空間を定義できます。 指定できる最小範囲は、 */29* です。この場合、サブネットに 8 つの IP アドレスを使用できます。 Azure では、サブネットごとに、最初と最後のアドレスがプロトコルに準拠するために予約されています。 そのほか、3 つのアドレスが Azure サービスの使用のために予約されています。 そのため、 */29* のアドレス範囲が設定されたサブネットを定義すると、そのサブネットで使用できる IP アドレスが 3 つになります。</p><p>仮想ネットワークを VPN ゲートウェイに接続する場合は、ゲートウェイ サブネットを作成する必要があります。 詳細については、[ゲートウェイ サブネットに指定するアドレス範囲の考慮事項](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub)に関する記事を参照してください。 特定の条件下でのみ、サブネットの追加後にアドレス範囲を変更できます。 サブネット アドレス範囲を変更する方法については、「[サブネット設定の変更](#change-subnet-settings)」をご覧ください。</p> |
    | **ネットワーク セキュリティ グループ** | サブネットの受信および送信ネットワーク トラフィックをフィルター処理するために、既存のネットワーク セキュリティ グループをサブネットに関連付けることができます。 ネットワーク セキュリティ グループは、仮想ネットワークと同じサブスクリプションと場所に存在する必要があります。 [ネットワーク セキュリティ グループ](./network-security-groups-overview.md)と[ネットワーク セキュリティ グループを作成する方法](tutorial-filter-network-traffic.md)に関するページを参照してください。 |
    | **ルート テーブル** | 他のネットワークへのネットワーク トラフィック ルーティングを制御するために、必要に応じて、既存のルート テーブルをサブネットに関連付けることができます。 ルート テーブルは、仮想ネットワークと同じサブスクリプションと場所に存在する必要があります。 [Azure のルーティング](virtual-networks-udr-overview.md)と[ルート テーブルを作成する方法](tutorial-create-route-table-portal.md)に関するページを参照してください。 |
    | **サービス エンドポイント** | <p>サブネットでは、必要に応じて、1 つまたは複数のサービス エンドポイントを有効にすることができます。 サービスのサービス エンドポイントを有効にするには、サービス エンドポイントを有効にするサービス (複数可) を **[サービス]** 一覧から選択します。 Azure では、エンドポイントの場所が自動的に構成されます。 Azure では、既定で、仮想ネットワークのリージョンに対してサービス エンドポイントが構成されます。 リージョン フェールオーバーのシナリオに対応するために、Azure では、Azure Storage に対して [Azure のペアになっているリージョン](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions)にエンドポイントを自動的に構成します。</p><p>サービス エンドポイントを削除するには、サービス エンドポイントを削除するサービスを選択解除します。 サービス エンドポイント､およびそれらを有効にできるサービスについては､「[仮想ネットワーク サービス エンドポイント](virtual-network-service-endpoints-overview.md)」を参照してください。 サービスのサービス エンドポイントを有効にしたら、そのサービスで作成されたリソースのサブネットへのネットワーク アクセスも有効にする必要があります。 たとえば、**Microsoft.Storage** のサービス エンドポイントを有効にする場合は、ネットワーク アクセスを許可するすべての Azure ストレージ アカウントへのネットワーク アクセスも有効にする必要があります。 サービス エンドポイントが有効になっているサブネットへのネットワーク アクセスを有効にするには、サービス エンドポイントを有効にした個々のサービスのドキュメントを参照してください。</p><p>サブネットに対してサービス エンドポイントが有効にされていることを検証するには､サブネット上の任意のネットワーク インターフェイスの[有効なルート](diagnose-network-routing-problem.md)を表示します｡ エンドポイントを構成するときに､そのサービスのアドレス プレフィックスと次ホップの種類 **VirtualNetworkServiceEndpoint** からなる "*既定*" のルートが表示されます。 ルーティングの詳細については、「[仮想ネットワーク トラフィックのルーティング](virtual-networks-udr-overview.md)」を参照してください。</p> |
    | **サブネットの委任** | サブネットでは、必要に応じて、1 つまたは複数の委任を有効にすることができます。 サブネット委任では、サービスのデプロイ時に一意の ID を利用してサブネットにサービス固有のリソースを作成するための明示的なアクセス許可がサービスに与えられます。 サービスに委任するには、 **[サービス]** 一覧から委任先となるサービスを選択します。 |

5. 選択した仮想ネットワークにサブネットを追加するには、 **[OK]** を選択します。

### <a name="commands"></a>コマンド

| ツール | コマンド |
| ---- | ------- |
| Azure CLI | [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) |
| PowerShell | [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) |

## <a name="change-subnet-settings"></a>サブネット設定の変更

1. [Azure portal](https://portal.azure.com) に移動して、仮想ネットワークを表示します。 **[仮想ネットワーク]** を検索して選択します。

2. 変更するサブネットを含む仮想ネットワークの名前を選択します。

3. **[設定]** で、 **[サブネット]** を選択します。

4. サブネットの一覧から、設定を変更するサブネットを選択します。

5. [サブネット] ページで、次のいずれかの設定を変更します。

    | 設定 | 説明 |
    | --- | --- |
    | **アドレス範囲** | サブネット内にリソースがデプロイされていない場合は、アドレス範囲を変更できます。 サブネット内にリソースが存在する場合は、まず、そのリソースを別のサブネットに移動するか、サブネットから削除する必要があります。 リソースを移動または削除する手順は、リソースによって異なります。 サブネット内のリソースを移動または削除する方法については、それらのリソースの種類に応じたドキュメントをご覧ください。 「[サブネットの追加](#add-a-subnet)」の手順 4 の **[アドレス範囲]** の制約について確認してください。 |
    | **ユーザー** | 組み込みロールまたは独自のカスタム ロールを使用して、サブネットへのアクセス権を制御できます。 サブネットにアクセスするロールとユーザーの割り当てについて詳しくは、[Azure ロールの割り当て](../role-based-access-control/role-assignments-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事をご覧ください。 |
    | **[ネットワーク セキュリティ グループ]** と **[ルート テーブル]** | 「[サブネットの追加](#add-a-subnet)」の手順 4 を参照してください。 |
    | **サービス エンドポイント** | <p>「[サブネットの追加](#add-a-subnet)」の手順 4 のサービス エンドポイントを参照してください。 既存のサブネットに対してサービス エンドポイントを有効にする場合は､サブネット上のリソース上で重要なタスクが実行されていないことを確認してください｡ サービス エンドポイントは、サブネット内のすべてのネットワーク インターフェイスでルートを切り替えます。 サービス エンドポイントは､*0.0.0.0/0* アドレス プレフィックスと次ホップの種類 *Internet* からなる既定のルートの使用から、サービスのアドレス プレフィックスと次ホップの種類 *VirtualNetworkServiceEndpoint* からなる新しいルートの使用まで処理を行います。</p><p>切り替えの間､開いている TCP 接続は終了されることがあります｡ サービス エンドポイントは､すべてのネットワーク インターフェイスについて､サービスに対するトラフィック フローが新しいルートで更新されるまで有効にされません。 ルーティングの詳細については、「[仮想ネットワーク トラフィックのルーティング](virtual-networks-udr-overview.md)」を参照してください。</p> |
    | **サブネットの委任** | 「[サブネットの追加](#add-a-subnet)」の手順 4 のサービス エンドポイントを参照してください。 サブネット委任は、0 または複数の委任を有効にするように変更できます。 あるサービスのあるリソースがサブネットで既にデプロイされている場合、サービスのすべてのリソースが削除されるまで、サブネット委任を追加したり削除したりすることはできません。 別のサービスに委任するには、 **[サービス]** 一覧から委任先となるサービスを選択します。 |

6. **[保存]** を選択します。

### <a name="commands"></a>コマンド

| ツール | コマンド |
| ---- | ------- |
| Azure CLI | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="delete-a-subnet"></a>サブネットの削除

サブネット内にリソースがない場合にのみ、サブネットを削除できます。 サブネット内にリソースがある場合は、サブネットを削除する前に、そのリソースを削除する必要があります。 リソースを削除する方法は、リソースによって異なります。 サブネット内のリソースを削除する方法については、それらのリソースの種類に応じたドキュメントをご覧ください。

1. [Azure portal](https://portal.azure.com) に移動して、仮想ネットワークを表示します。 **[仮想ネットワーク]** を検索して選択します。

2. 削除するサブネットを含む仮想ネットワークの名前を選択します。

3. **[設定]** で、 **[サブネット]** を選択します。

4. サブネットの一覧から、削除するサブネットを選択します。

5. **[削除]** を選択し、確認のダイアログ ボックスで **[はい]** を選択します。

### <a name="commands"></a>コマンド

| ツール | コマンド |
| ---- | ------- |
| Azure CLI | [az network vnet subnet delete](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-delete) |
| PowerShell | [Remove-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/remove-azvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json) |

## <a name="permissions"></a>アクセス許可

サブネットでタスクを実行するには、[ネットワークの共同作成者ロール](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)、または次の表に記載されている適切なアクションを割り当てられている[カスタム ロール](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)にアカウントが割り当てられている必要があります。

|アクション                                                                   |   名前                                       |
|-----------------------------------------------------------------------  |   -----------------------------------------  |
|Microsoft.Network/virtualNetworks/subnets/read                           |   仮想ネットワークのサブネットを読み取る              |
|Microsoft.Network/virtualNetworks/subnets/write                          |   仮想ネットワークのサブネットを作成または更新する  |
|Microsoft.Network/virtualNetworks/subnets/delete                         |   仮想ネットワークのサブネットを削除する            |
|Microsoft.Network/virtualNetworks/subnets/join/action                    |   仮想ネットワークの接続                     |
|Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action  |   サブネットに対してサービス エンドツーエンドを有効にする     |
|Microsoft.Network/virtualNetworks/subnets/virtualMachines/read           |   サブネット上の仮想マシンを取得する       |

## <a name="next-steps"></a>次のステップ

- [PowerShell](powershell-samples.md) または [Azure CLI](cli-samples.md) のサンプル スクリプト、または Azure [Resource Manager テンプレート](template-samples.md)を使って仮想ネットワークを作成する
- 仮想ネットワーク用に [Azure Policy 定義](./policy-reference.md)を作成して割り当てる