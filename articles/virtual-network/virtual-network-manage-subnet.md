---
title: Azure 仮想ネットワーク サブネットの追加、変更、削除
titlesuffix: Azure Virtual Network
description: Azure で仮想ネットワーク サブネットを追加、変更、削除する方法について説明します。
services: virtual-network
documentationcenter: na
author: jimdial
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2018
ms.author: jdial
ms.openlocfilehash: b4fd3d7cd1d25415ba5938586aee448bdc512aa7
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58100464"
---
# <a name="add-change-or-delete-a-virtual-network-subnet"></a>仮想ネットワーク サブネットの追加、変更、削除

仮想ネットワーク サブネットを追加、変更、削除する方法について説明します。 仮想ネットワークにデプロイされているすべての Azure リソースは、仮想ネットワーク内のサブネットにデプロイされます。 仮想ネットワークが初めての方は、「[Virtual Network の概要](virtual-networks-overview.md)」、あるいは[チュートリアル](quick-create-portal.md)を行うことで詳しく学習できます。 仮想ネットワークの作成､変更､あるいは削除については､[仮想ネットワークの管理](manage-virtual-network.md)を参照してください｡

## <a name="before-you-begin"></a>開始する前に

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

この記事のセクションに記載された手順を始める前に、次のタスクを完了してください。

- まだ Azure アカウントを持っていない場合は、[無料試用版アカウント](https://azure.microsoft.com/free)にサインアップしてください。
- ポータルを使用する場合は、 https://portal.azure.com を開き、Azure アカウントでログインします。
- PowerShell コマンドを使用してこの記事のタスクを実行する場合は、[Azure Cloud Shell](https://shell.azure.com/powershell) でコマンドを実行するか、お使いのコンピューターから PowerShell を実行してください。 Azure Cloud Shell は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。 一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。 このチュートリアルには、Azure PowerShell モジュール バージョン 1.0.0 以降が必要です。 インストールされているバージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzAccount` を実行して Azure との接続を作成することも必要です。
- Azure コマンド ライン インターフェイス (CLI) コマンドを使用してこの記事のタスクを実行する場合は、[Azure Cloud Shell](https://shell.azure.com/bash) でコマンドを実行するか、お使いのコンピューターから CLI を実行してください。 このチュートリアルには、Azure CLI のバージョン 2.0.31 以降が必要です。 インストールされているバージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。 Azure CLI をローカルで実行している場合、`az login` を実行して Azure との接続を作成することも必要です。

Azure へのログインまたは接続に使用するアカウントは、[ネットワークの共同作業者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)ロール、または「[アクセス許可](#permissions)」の一覧に記載されている適切なアクションが割り当てられている[カスタム ロール](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に割り当てられている必要があります。

## <a name="add-a-subnet"></a>サブネットの追加

1. ポータルの上部にある検索ボックスに、「*仮想ネットワーク*」と入力します。 検索結果に **[仮想ネットワーク]** が表示されたら、それを選択します。
2. 仮想ネットワークの一覧から、サブネットを追加する仮想ネットワークを選択します。
3. **[設定]** で、**[サブネット]** を選択します。
4. **[+サブネット]** を選択します。
5. 次のパラメーターの値を入力します。
   - **[名前]**:仮想ネットワーク内で一意となる名前を使用してください。 他の Azure サービスとの最大限の互換性を維持するために、名前の最初の文字として英字を使用することをお勧めします。 たとえば、Azure Application Gateway は、数字で始まる名前を持つサブネットにはデプロイされません。
   - **アドレス範囲**:範囲は仮想ネットワークのアドレス空間内で一意である必要があります。 仮想ネットワーク内の他のサブネット アドレス範囲と重複することはできません。 アドレス空間は、Classless Inter-Domain Routing (CIDR) 表記で指定する必要があります。 たとえば、アドレス空間が 10.0.0.0/16 の仮想ネットワークでは、10.0.0.0/24 のサブネット アドレス空間を定義できます。 指定できる最小範囲は、/29 です。これでサブネットに 8 つの IP アドレスを使用できます。 Azure では、サブネットごとに、最初と最後のアドレスがプロトコルに準拠するために予約されています。 そのほか、3 つのアドレスが Azure サービスの使用のために予約されています。 そのため、/29 のアドレス範囲が設定されたサブネットでは、使用できる IP アドレスが 3 つになります。 仮想ネットワークを VPN ゲートウェイに接続する場合は、ゲートウェイ サブネットを作成する必要があります。 詳細については、[ゲートウェイ サブネットに指定するアドレス範囲の考慮事項](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub)に関する記事を参照してください。 特定の条件下でのみ、サブネットの追加後にアドレス範囲を変更できます。 サブネット アドレス範囲を変更する方法については、「[サブネット設定の変更](#change-subnet-settings)」をご覧ください。
   - **[ネットワーク セキュリティ グループ]**: 0 個か 1 個の既存のネットワーク セキュリティ グループをサブネットに関連付けることで、そのサブネットの受信および送信ネットワーク トラフィックをフィルター処理できます。 ネットワーク セキュリティ グループは、仮想ネットワークと同じサブスクリプションと場所に存在する必要があります。 [ネットワーク セキュリティ グループ](security-overview.md)と[ネットワーク セキュリティ グループを作成する方法](tutorial-filter-network-traffic.md)に関するページを参照してください。
   - **[ルート テーブル]**:0 個または 1 個の既存のルート テーブルをサブネットに関連付けることで、他のネットワークへのネットワーク トラフィック ルーティングを制御できます。 ルート テーブルは、仮想ネットワークと同じサブスクリプションと場所に存在する必要があります。 [Azure のルーティング](virtual-networks-udr-overview.md)と[ルート テーブルを作成する方法](tutorial-create-route-table-portal.md)に関するページを参照してください。
   - **[サービス エンドポイント]:** サブネットは、そのサブネットに対して 0 個または複数のサービス エンドポイントを有効にできます。 サービスのサービス エンドポイントを有効にするには、サービス エンドポイントを有効にするサービス (複数可) を **[サービス]** 一覧から選択します。 エンドポイントには､自動的に場所が構成されます。 既定では､仮想ネットワークのリージョンに対してサービス エンドポイントが構成されます｡ Azure Storage の場合、リージョン フェールオーバーのシナリオに対応するため、エンドポイントは [Azure のペアになっているリージョン](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions)に自動的に構成されます。
   - **[サブネットの委任]:** 1 つのサブスクリプションに対して 0 または複数の委任を有効にすることができます。 サブネット委任では、サービスのデプロイ時に一意の ID を利用してサブネットにサービス固有のリソースを作成するための明示的なアクセス許可がサービスに与えられます。 サービスに委任するには、**[サービス]** 一覧から委任先となるサービスを選択します。

       サービス エンドポイントを削除するには、サービス エンドポイントを削除するサービスを選択解除します。 サービス エンドポイントおよび､そうしたエンドポイントを有効にできるサービスについては､[仮想ネットワークのサービス エンドポイントの概要](virtual-network-service-endpoints-overview.md)を参照してください｡ サービスのサービス エンドポイントを有効にしたら、そのサービスで作成されたリソースのサブネットへのネットワーク アクセスも有効にする必要があります。 たとえば、*Microsoft.Storage* のサービス エンドポイントを有効にする場合は、ネットワーク アクセスを許可するすべての Azure ストレージ アカウントへのネットワーク アクセスも有効にする必要があります。 サービス エンドポイントが有効になっているサブネットへのネットワーク アクセスを有効にする方法の詳細については、サービス エンドポイントを有効にした個々のサービスのドキュメントを参照してください。

     サブネットに対してサービス エンドポイントが有効にされていることを検証するには､サブネット上の任意のネットワーク インターフェイスの[有効なルート](diagnose-network-routing-problem.md)を表示します｡ エンドポイントが構成されている場合は､そのサービスのアドレス プレフィックスと､**VirtualNetworkServiceEndpoint** の nextHopType からなる*既定* のルートが表示されます｡ ルーティングについては､[ルーティングの概要](virtual-networks-udr-overview.md)を参照してください｡
6. 選択した仮想ネットワークにサブネットを追加するには、**[OK]** を選択します。

**コマンド**

- Azure CLI: [az network vnet subnet create](/cli/azure/network/vnet/subnet)
- PowerShell:[Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig)

## <a name="change-subnet-settings"></a>サブネット設定の変更

1. ポータルの上部にある検索ボックスに、「*仮想ネットワーク*」と入力します。 検索結果に **[仮想ネットワーク]** が表示されたら、それを選択します。
2. 仮想ネットワークの一覧から、設定を変更するサブネットを含む仮想ネットワークを選択します。
3. **[設定]** で、**[サブネット]** を選択します。
4. サブネットの一覧から、設定を変更するサブネットを選択します。 次の設定を変更できます。

    - **[アドレス範囲]:** サブネット内にリソースがデプロイされていない場合は、アドレス範囲を変更できます。 サブネット内にリソースが存在する場合は、まず、そのリソースを別のサブネットに移動するか、サブネットから削除する必要があります。 リソースを移動または削除する手順は、リソースによって異なります。 サブネット内のリソースを移動または削除する方法については、移動または削除するリソースの種類に応じたドキュメントをご覧ください。 「[サブネットの追加](#add-a-subnet)」の手順 5. の **[アドレス範囲]** の制約について確認してください。
    - **[ユーザー]**:組み込みロールまたは独自のカスタム ロールを使用して、サブネットへのアクセス権を制御できます。 サブネットにアクセスするロールとユーザーの割り当ての詳細については、[ロールの割り当てを使用した Azure リソースへのアクセス権の管理](../role-based-access-control/role-assignments-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-role-assignment)に関するページをご覧ください。
    - **[ネットワーク セキュリティ グループ]** と **[ルート テーブル]**:「[サブネットの追加](#add-a-subnet)」の手順 5 を参照してください。
    - **[サービス エンドポイント]**:「[サブネットの追加](#add-a-subnet)」の手順 5 のサービス エンドポイントを参照してください。 既存のサブネットに対してサービス エンドポイントを有効にする場合は､サブネット上のリソース上で重要なタスクが実行されていないことを確認してください｡ サービス エンドポイントは､*0.0.0.0/0* アドレス プレフィックスとネクスト ホップ タイプが *Internet* からなる既定のルートから始まってサービスのアドレス プレフィックスとネクスト ホップ タイプが *VirtualNetworkServiceEndpoint* からなる新しいルートまで､サブネット上のあらゆるネットワーク インターフェイスでルートを切り替えます｡ 切り替えの間､開いている TCP 接続は終了されることがあります｡ サービス エンドポイントは､すべてのネットワーク インターフェイスについて､サービスに対するトラフィック フローが新しいルートで更新されるまで有効にされません｡ ルーティングについては､[ルーティングの概要](virtual-networks-udr-overview.md)を参照してください｡
    - **[サブネットの委任]:**「[サブネットの追加](#add-a-subnet)」の手順 5 のサービス エンドポイントを参照してください。 サブネット委任は、0 または複数の委任を有効にするように変更できます。 サブネットであるサービスのあるリソースが既にデプロイされている場合、サービスのすべてのリソースが削除されるまでサブネット委任は削除できません。 別のサービスに委任するには、**[サービス]** 一覧から委任先となるサービスを選択します。
5. **[保存]** を選択します。

**コマンド**

- Azure CLI: [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update)
- PowerShell:[Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig)

## <a name="delete-a-subnet"></a>サブネットの削除

サブネット内にリソースがない場合にのみ、サブネットを削除できます。 サブネット内にリソースがある場合は、サブネットを削除する前に、そのサブネット内のリソースを削除する必要があります。 リソースを削除する方法は、リソースによって異なります。 サブネット内のリソースを削除する方法については、削除するリソースの種類に応じたドキュメントをご覧ください。

1. ポータルの上部にある検索ボックスに、「*仮想ネットワーク*」と入力します。 検索結果に **[仮想ネットワーク]** が表示されたら、それを選択します。
2. 仮想ネットワークの一覧から、削除するサブネットを含む仮想ネットワークを選択します。
3. **[設定]** で、**[サブネット]** を選択します。
4. サブネットの一覧から、削除するサブネットの右側にある **[...]** を選択します。
5. **[削除]** を選択してから、**[はい]** を選択します。

**コマンド**

- Azure CLI: [az network vnet delete](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-delete)
- PowerShell:[Remove-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/remove-azvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="permissions"></a>アクセス許可

サブネットでタスクを実行するには、[ネットワークの共同作業者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)ロール、または次の表に記載されている適切なアクションを割り当てられている[カスタム](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ロールにアカウントが割り当てられている必要があります。

|Action                                                                   |   Name                                       |
|-----------------------------------------------------------------------  |   -----------------------------------------  |
|Microsoft.Network/virtualNetworks/subnets/read                           |   仮想ネットワークのサブネットを読み取る              |
|Microsoft.Network/virtualNetworks/subnets/write                          |   仮想ネットワークのサブネットを作成または更新する  |
|Microsoft.Network/virtualNetworks/subnets/delete                         |   仮想ネットワークのサブネットを削除する            |
|Microsoft.Network/virtualNetworks/subnets/join/action                    |   仮想ネットワークの接続                     |
|Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action  |   サブネットに対してサービス エンドツーエンドを有効にする     |
|Microsoft.Network/virtualNetworks/subnets/virtualMachines/read           |   サブネット上の仮想マシンを取得する       |

## <a name="next-steps"></a>次の手順

- [PowerShell](powershell-samples.md) または [Azure CLI](cli-samples.md) のサンプル スクリプト、または Azure [Resource Manager テンプレート](template-samples.md)を使って仮想ネットワークを作成する
- [Azure ポリシー](policy-samples.md)を作成して仮想ネットワークに適用する
