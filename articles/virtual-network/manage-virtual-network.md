---
title: Azure 仮想ネットワークを作成、変更、削除する
titlesuffix: Azure Virtual Network
description: 仮想ネットワークを作成および削除し、既存の仮想ネットワークの DNS サーバーや IP アドレス空間などの設定を変更します。
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/10/2019
ms.author: kumud
ms.openlocfilehash: 1ec4e26e266a1777857fe0e890b093f60a3597f0
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102612526"
---
# <a name="create-change-or-delete-a-virtual-network"></a>仮想ネットワークの作成、変更、削除

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

仮想ネットワークを作成および削除する方法と、既存の仮想ネットワークの DNS サーバーや IP アドレス空間などの設定を変更する方法について説明します。 仮想ネットワークが初めての方は、「[Virtual Network の概要](virtual-networks-overview.md)」、あるいは[チュートリアル](quick-create-portal.md)を行うことで詳しく学習できます。 仮想ネットワークにはサブネットが含まれます。 サブネットを作成、変更、および削除する方法については、[サブネットの管理](virtual-network-manage-subnet.md)に関する記事をご覧ください。

## <a name="before-you-begin"></a>開始する前に

この記事のセクションに記載された手順を始める前に、次のタスクを完了してください。

- まだ Azure アカウントを持っていない場合は、[無料試用版アカウント](https://azure.microsoft.com/free)にサインアップしてください。
- ポータルを使用する場合は、 https://portal.azure.com を開き、Azure アカウントでログインします。
- PowerShell コマンドを使用してこの記事のタスクを実行する場合は、[Azure Cloud Shell](https://shell.azure.com/powershell) でコマンドを実行するか、お使いのコンピューターから PowerShell を実行してください。 Azure Cloud Shell は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。 一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。 このチュートリアルには、Azure PowerShell モジュール バージョン 1.0.0 以降が必要です。 インストールされているバージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzAccount` を実行して Azure との接続を作成することも必要です。
- Azure コマンド ライン インターフェイス (CLI) コマンドを使用してこの記事のタスクを実行する場合は、[Azure Cloud Shell](https://shell.azure.com/bash) でコマンドを実行するか、お使いのコンピューターから CLI を実行してください。 このチュートリアルには、Azure CLI のバージョン 2.0.31 以降が必要です。 インストールされているバージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。 Azure CLI をローカルで実行している場合、`az login` を実行して Azure との接続を作成することも必要です。
- Azure へのログインまたは接続に使用するアカウントは、[ネットワークの共同作業者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)ロール、または「[アクセス許可](#permissions)」の一覧に記載されている適切なアクションが割り当てられている[カスタム ロール](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に割り当てられている必要があります。

## <a name="create-a-virtual-network"></a>仮想ネットワークの作成

1. **+ リソースの作成** > **ネットワーク** > **仮想ネットワーク** の順に選択します。
2. 次の設定の値を入力または選択し、 **[作成]** を選択します。
   - **Name**:この名前は、仮想ネットワークの作成先として選択する [リソース グループ](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group)内で一意である必要があります。 仮想ネットワークの作成後は、この名前を変更できません。 長く使用する中で、仮想ネットワークを複数作成する可能性があります。 名前付けの推奨事項については、「[名前付け規則](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#naming-and-tagging-resources)」を参照してください。 名前付け規則に従うことで、複数の仮想ネットワークを管理しやすくなります。
   - **[アドレス空間]** : 仮想ネットワークのアドレス空間は、CIDR 表記で指定された、1 つ以上の重複しないアドレス範囲で構成されます。 定義するアドレス範囲は、パブリックとプライベート (RFC 1918 に準拠) のどちらでもかまいません。 パブリックとプライベートのどちらのアドレス範囲を定義する場合でも、そのアドレス範囲に到達できるのは、仮想ネットワーク内から、相互接続された仮想ネットワークから、および仮想ネットワークに接続したオンプレミス ネットワークからだけです。 次のアドレス範囲は追加できません。
     - 224.0.0.0/4 (マルチキャスト)
     - 255.255.255.255/32 (ブロードキャスト)
     - 127.0.0.0/8 (ループバック)
     - 169.254.0.0/16 (リンク ローカル)
     - 168.63.129.16/32 (内部 DNS、DHCP、および Azure Load Balancer の[正常性プローブ](../load-balancer/load-balancer-custom-probe-overview.md#probesource))

     ポータルで仮想ネットワークを作成する際に定義できるアドレス範囲は 1 つだけですが、仮想ネットワークの作成後にアドレス範囲をアドレス空間に追加することができます。 既存の仮想ネットワークにアドレス範囲を追加する方法については、「[アドレス範囲の追加または削除](#add-or-remove-an-address-range)」を参照してください。

     > [!WARNING]
     > 仮想ネットワークのアドレス範囲が他の仮想ネットワークやオンプレミス ネットワークと重複している場合、その 2 つのネットワークに接続することはできません。 アドレス範囲を定義する前に、将来その仮想ネットワークを他の仮想ネットワークやオンプレミス ネットワークに接続する可能性があるかどうかを検討してください。 Microsoft では、プライベート アドレス空間または組織が所有するパブリック アドレス空間を使用して、仮想ネットワークのアドレス範囲を構成することをお勧めします。
     >

     - **[サブネット名]** : 仮想ネットワーク内で一意となるサブネット名を使用してください。 サブネットの作成後は、サブネット名を変更できません。 ポータルでは、仮想ネットワークを作成する際にサブネットを 1 つ定義するよう求められますが、仮想ネットワークにサブネットを設定することは必須ではありません。 ポータルでは、仮想ネットワークの作成時に 1 つ以上のサブネットを定義できます。 仮想ネットワークを作成した後で、仮想ネットワークにサブネットを追加できます。 仮想ネットワークにサブネットを追加するには、[サブネットの管理](virtual-network-manage-subnet.md)に関するページを参照してください。 Azure CLI または PowerShell を使用して、複数のサブネットを持つ仮想ネットワークを作成できます。

       >[!TIP]
       >管理者が複数のサブネットを作成して、サブネット間でトラフィックのフィルター処理や制御を行うことはよくあります。 サブネットを定義する前に、サブネット間のトラフィックのルーティングやフィルター処理の方法について検討してください。 サブネット間のトラフィックをフィルター処理する方法の詳細については、[ネットワーク セキュリティ グループ](./network-security-groups-overview.md)に関する記事を参照してください。 Azure ではサブネット間のルーティングが自動的に行われますが、Azure の既定のルートをオーバーライドすることもできます。 Azure の既定のサブネット トラフィック ルーティングの詳細については、[ルーティングの概要](virtual-networks-udr-overview.md)に関するページを参照してください。
       >

     - **[サブネットのアドレス範囲]** : この範囲は、仮想ネットワーク用に入力したアドレス空間内にある必要があります。 指定できる最小範囲は、/29 です。これでサブネットに 8 つの IP アドレスを使用できます。 Azure では、サブネットごとに、最初と最後のアドレスがプロトコルに準拠するために予約されています。 そのほか、3 つのアドレスが Azure サービスの使用のために予約されています。 そのため、/29 のサブネット アドレス範囲が設定された仮想ネットワークで使用できる IP アドレスは 3 つのみです。 仮想ネットワークを VPN ゲートウェイに接続する場合は、ゲートウェイ サブネットを作成する必要があります。 詳細については、[ゲートウェイ サブネットに指定するアドレス範囲の考慮事項](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub)に関する記事を参照してください。 特定の条件下でのみ、サブネットの作成後にアドレス範囲を変更できます。 サブネット アドレス範囲を変更する方法については、[サブネットの管理](virtual-network-manage-subnet.md)に関するページを参照してください。
     - **サブスクリプション**:[サブスクリプション](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)を選択します。 1 つの仮想ネットワークが複数の Azure サブスクリプションにまたがることはできません。 ただし、1 つのサブスクリプションの仮想ネットワークを、[仮想ネットワーク ピアリング](virtual-network-peering-overview.md)を使用して、他のサブスクリプションの仮想ネットワークに接続することはできます。 仮想ネットワークに接続する Azure リソースは、仮想ネットワークと同じサブスクリプションに存在する必要があります。
     - **[リソース グループ]** :既存の [リソース グループ](../azure-resource-manager/management/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-groups)を選択するか、新しいものを作成します。 仮想ネットワークに接続する Azure リソースは、仮想ネットワークと同じリソース グループに存在していても別のリソース グループに存在していてもかまいません。
     - **[場所]** :Azure の [場所](https://azure.microsoft.com/regions/)を選択します。この場所はリージョンとも呼ばれます。 1 つの仮想ネットワークは、Azure の 1 つの場所にのみ存在できます。 ただし、VPN ゲートウェイを使用して、1 つの場所にある仮想ネットワークを別の場所の仮想ネットワークに接続することはできます。 仮想ネットワークに接続する Azure リソースは、仮想ネットワークと同じ場所に存在する必要があります。

**コマンド**

- Azure CLI: [az network vnet create](/cli/azure/network/vnet)
- PowerShell:[New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)

## <a name="view-virtual-networks-and-settings"></a>仮想ネットワークと設定の表示

1. ポータルの上部にある検索ボックスに、「*仮想ネットワーク*」と入力します。 検索結果に **[仮想ネットワーク]** が表示されたら、それを選択します。
2. 仮想ネットワークの一覧から、設定を表示する仮想ネットワークを選択します。
3. 選択した仮想ネットワークの、次の設定が表示されます。
   - **概要**:アドレス空間や DNS サーバーなど、仮想ネットワークに関する情報が表示されます。 次のスクリーンショットは、**MyVNet** という名前の仮想ネットワークの概要設定を示しています。

     ![ネットワーク インターフェイスの概要](./media/manage-virtual-network/vnet-overview.png)

     仮想ネットワークを別のサブスクリプションまたはリソース グループに移動するには、 **[リソース グループ]** または **[サブスクリプション名]** の横にある **[変更]** を選択します。 仮想ネットワーク を移動する方法については、[別のリソース グループまたはサブスクリプションへのリソースの移動](../azure-resource-manager/management/move-resource-group-and-subscription.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事を参照してください。 この記事には、Azure Portal、PowerShell、および Azure CLI を使用してリソースを移動するための前提条件と、移動の方法が記載されています。 仮想ネットワークに接続されているリソースもすべて、仮想ネットワークと共に移動する必要があります。
   - **[アドレス空間]** : 仮想ネットワークに割り当てられているアドレス空間が一覧表示されます。 アドレス空間へのアドレス範囲の追加と削除の方法については、「[アドレス範囲の追加または削除](#add-or-remove-an-address-range)」の手順を参照してください。
   - **接続されているデバイス**:仮想ネットワークに接続されているすべてのリソースが一覧表示されます。 前のスクリーンショットでは、3 つのネットワーク インターフェイスと 1 つのロード バランサーが仮想ネットワークに接続されています。 新しく作成して仮想ネットワークに接続したリソースはすべて一覧表示されます。 仮想ネットワークに接続されていたリソースを削除すると、一覧に表示されなくなります。
   - **サブネット**:仮想ネットワーク内に存在するサブネットの一覧が表示されます。 サブネットを追加または削除する方法については、[サブネットの管理](virtual-network-manage-subnet.md)に関するページを参照してください。
   - **DNS サーバー**:仮想ネットワークに接続されているデバイスの名前解決を Azure の内部 DNS サーバーとカスタム DNS サーバーのどちらで提供するかを指定できます。 Azure Portal を使用して仮想ネットワークを作成する場合は、既定で、仮想ネットワーク内の名前解決には Azure の DNS サーバーが使用されます。 DNS サーバーを変更するには、この記事の「[DNS サーバーの変更](#change-dns-servers)」の手順を実行してください。
   - **ピアリング**:サブスクリプションに既存のピアリングがある場合は、それらがここに表示されます。 既存のピアリング設定の表示や、ピアリングの作成、変更、削除を行うことができます。 ピアリングの詳細については、「[仮想ネットワーク ピアリング](virtual-network-peering-overview.md)」を参照してください。
   - **[プロパティ]** :仮想ネットワークのリソース ID や、仮想ネットワークが存在する Azure サブスクリプションなど、仮想ネットワークに関する設定が表示されます。
   - **ダイアグラム**:このダイアグラムには、仮想ネットワークに接続されているすべてのデバイスを視覚的に表現したものが表示されます。 ダイアグラムには、デバイスに関する重要な情報が含まれます。 このビューでダイアグラム内のデバイスを管理するには、デバイスを選択します。
   - **Azure の一般的な設定**:Azure の一般的な設定の詳細については、次の情報を参照してください。
     - [アクティビティ ログ](../azure-monitor/essentials/platform-logs-overview.md)
     - [アクセス制御 (IAM)](../role-based-access-control/overview.md)
     - [タグ](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
     - [Locks](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
     - [Automation スクリプト](../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates)

**コマンド**

- Azure CLI: [az network vnet show](/cli/azure/network/vnet)
- PowerShell:[Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork)

## <a name="add-or-remove-an-address-range"></a>アドレス範囲の追加または削除

仮想ネットワークのアドレス範囲を追加および削除することができます。 アドレス範囲は CIDR 表記で指定する必要があり、同じ仮想ネットワーク内で他のアドレス範囲と重複することはできません。 定義するアドレス範囲は、パブリックとプライベート (RFC 1918 に準拠) のどちらでもかまいません。 パブリックとプライベートのどちらのアドレス範囲を定義する場合でも、そのアドレス範囲に到達できるのは、仮想ネットワーク内から、相互接続された仮想ネットワークから、および仮想ネットワークに接続したオンプレミス ネットワークからだけです。 

仮想ネットワークのアドレス範囲は、関連付けられているサブネットの範囲がまだ含まれている範囲にまでは縮小できます。 さらに、/16 を /8 に変更するなど、アドレス範囲を拡張することができます。 

<!-- the above statement has been edited to reflect the most recent comments on the reopened issue: https://github.com/MicrosoftDocs/azure-docs/issues/20572 -->

次のアドレス範囲は追加できません。

- 224.0.0.0/4 (マルチキャスト)
- 255.255.255.255/32 (ブロードキャスト)
- 127.0.0.0/8 (ループバック)
- 169.254.0.0/16 (リンク ローカル)
- 168.63.129.16/32 (内部 DNS、DHCP、および Azure Load Balancer の[正常性プローブ](../load-balancer/load-balancer-custom-probe-overview.md#probesource))

アドレス範囲を追加または削除するには、次のようにします。

1. ポータルの上部にある検索ボックスに、「*仮想ネットワーク*」と入力します。 検索結果に **[仮想ネットワーク]** が表示されたら、それを選択します。
2. 仮想ネットワークの一覧から、アドレス範囲を追加または削除する仮想ネットワークを選択します。
3. **[設定]** の下の **[アドレス空間]** を選択します。
4. 次のいずれかのオプションを実行します。
    - **IP アドレス範囲の追加**:新しいアドレス範囲を入力します。 この仮想ネットワークに定義されている既存のアドレス範囲と重複するアドレス範囲を追加することはできません。
    - **アドレス範囲の削除**:削除するアドレス範囲の右側にある **[...]** を選択し、 **[削除]** を選択します。 アドレス範囲にサブネットが存在する場合は、アドレス範囲を削除することはできません。 アドレス範囲を削除するには、まず、まず、アドレス範囲内にあるすべてのサブネット (およびサブネット内のすべてのリソース) を削除する必要があります。
5. **[保存]** を選択します。

**コマンド**

- Azure CLI: [az network vnet update](/cli/azure/network/vnet)
- PowerShell:[Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork)

## <a name="change-dns-servers"></a>DNS サーバーの変更

仮想ネットワークに接続されている VM はすべて、その仮想ネットワークに指定した DNS サーバーに登録されます。 また、指定した DNS サーバーを名前解決に使用します。 VM 内のネットワーク インターフェイス (NIC) ごとに独自の DNS サーバー設定を構成できます。 NIC に独自の DNS サーバー設定を構成すると、仮想ネットワークの DNS サーバー設定がオーバーライドされます。 NIC DNS 設定の詳細については、[ネットワーク インターフェイスのタスクと設定](virtual-network-network-interface.md#change-dns-servers)に関する記事を参照してください。 Azure Cloud Services での VM とロール インスタンスの名前解決の詳細については、「[VM とロール インスタンスの名前解決](virtual-networks-name-resolution-for-vms-and-role-instances.md)」を参照してください。 DNS サーバーを追加、変更、削除するには、次のようにします。

1. ポータルの上部にある検索ボックスに、「*仮想ネットワーク*」と入力します。 検索結果に **[仮想ネットワーク]** が表示されたら、それを選択します。
2. 仮想ネットワークの一覧から、DNS サーバーを変更する仮想ネットワークを選択します。
3. **[設定]** で、 **[DNS サーバー]** を選択します。
4. 以下のオプションの 1 つを選択します。
   - **既定 (Azure 提供)** :すべてのリソースの名前とプライベート IP アドレスが Azure DNS サーバーに自動的に登録されます。 同じ仮想ネットワークに接続されているリソース間で名前を解決することができます。 このオプションでは、仮想ネットワーク間で名前を解決することはできません。 仮想ネットワーク間で名前を解決するには、カスタムの DNS サーバーを使用する必要があります。
   - **Custom**:1 つ以上のサーバーを追加できます。ただし、追加できる数は Azure で仮想ネットワークに設定されている上限までです。 DNS サーバー数の上限の詳細については、[Azure の制限](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-networking-limits-classic)に関する記事を参照してください。 次のオプションがあります。
   - **アドレスの追加**:サーバーを仮想ネットワークの DNS サーバー一覧に追加します。 また、その DNS サーバーを Azure に登録します。 既に DNS サーバーが Azure に登録されている場合は、その DNS サーバーを一覧で選択できます。
   - **アドレスの削除**:削除するサーバーの横にある **[...]** 、 **[削除]** の順に選択します。 サーバーを削除しても、この仮想ネットワークの一覧からサーバーが削除されるだけです。 DNS サーバーは、他の仮想ネットワークで使用するために Azure に登録されたままになります。
   - **DNS サーバーのアドレスの並べ替え**:環境に適した順序で DNS サーバーが一覧表示されていることを確認することが重要です。 DNS サーバーの一覧は指定された順序で使用されます。 ラウンド ロビン設定のようには機能しません。 一覧の先頭にある DNS サーバーに到達できる場合、クライアントはその DNS サーバーが正しく動作しているかどうかに関係なく、その DNS サーバーを使用します。 一覧表示されたすべての DNS サーバーを削除してから、希望する順序で再度追加します。
   - **アドレスの変更**:一覧で DNS サーバーを強調表示し、新しいアドレスを入力します。
5. **[保存]** を選択します。
6. 新しい DNS サーバー設定が割り当てられるように、仮想ネットワークに接続されている VM を再起動します。 再起動されるまで、VM は現在の DNS 設定を使用し続けます。

**コマンド**

- Azure CLI: [az network vnet update](/cli/azure/network/vnet)
- PowerShell:[Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork)

## <a name="delete-a-virtual-network"></a>仮想ネットワークの削除

接続されているリソースがない場合にのみ、仮想ネットワークを削除できます。 仮想ネットワーク内のいずれかのサブネットに接続されているリソースがある場合は、最初に、仮想ネットワーク内のすべてのサブネットに接続されているリソースを削除する必要があります。 リソースを削除する方法は、リソースによって異なります。 サブネットに接続されているリソースを削除する方法については、削除するリソースの種類に応じたドキュメントを参照してください。 仮想ネットワークを削除するには、次のようにします。

1. ポータルの上部にある検索ボックスに、「*仮想ネットワーク*」と入力します。 検索結果に **[仮想ネットワーク]** が表示されたら、それを選択します。
2. 仮想ネットワークの一覧から、削除する仮想ネットワークを選択します。
3. **[設定]** の **[接続されているデバイス]** を選択して、仮想ネットワークに接続されているデバイスがないことを確認します。 接続されているデバイスがある場合は、先にそのデバイスを削除してから仮想ネットワークを削除する必要があります。 接続されているデバイスがない場合は、 **[概要]** を選択します。
4. **[削除]** を選択します。
5. **[はい]** を選択して、仮想ネットワークの削除を確認します。

**コマンド**

- Azure CLI: [azure network vnet delete](/cli/azure/network/vnet)
- PowerShell:[Remove-AzVirtualNetwork](/powershell/module/az.network/remove-azvirtualnetwork)

## <a name="permissions"></a>アクセス許可

仮想ネットワークに関するタスクを実行するには、使用するアカウントが[ネットワークの共同作業者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)ロール、または次の表の適切なアクションが割り当てられた[カスタム](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ロールに、割り当てられている必要があります。

| アクション                                  |   名前                                |
|---------------------------------------- |   --------------------------------    |
|Microsoft.Network/virtualNetworks/read   |   仮想ネットワークの読み取り              |
|Microsoft.Network/virtualNetworks/write  |   仮想ネットワークの作成または更新  |
|Microsoft.Network/virtualNetworks/delete |   仮想ネットワークの削除            |

## <a name="next-steps"></a>次のステップ

- [PowerShell](powershell-samples.md) または [Azure CLI](cli-samples.md) のサンプル スクリプトを使って、または Azure [Resource Manager テンプレート](template-samples.md)を使って、仮想ネットワークを作成します
- 仮想ネットワーク用に [Azure Policy 定義](./policy-reference.md)を作成して割り当てる
