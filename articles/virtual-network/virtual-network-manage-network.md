---
title: "Azure 仮想ネットワークの作成、変更、削除 | Microsoft Docs"
description: "Azure で仮想ネットワークを作成、変更、削除する方法について説明します。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/10/2017
ms.author: jdial
ms.openlocfilehash: 74aace2136136c25bc56327d38cfbab168265401
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="create-change-or-delete-a-virtual-network"></a>仮想ネットワークの作成、変更、削除

仮想ネットワークを作成および削除する方法と、既存の仮想ネットワークの DNS サーバーや IP アドレス空間などの設定を変更する方法について説明します。

仮想ネットワークとは、クラウド内のユーザー独自のネットワークを表したものです。 Azure サブスクリプション専用に Azure クラウドが論理的に分離されています。 作成する各仮想ネットワークでは、次のことを実行できます。
- 割り当てるアドレス空間を選択する。 アドレス空間は、10.0.0.0/16 のように Classless Inter-Domain Routing (CIDR) 表記を使用して定義された 1 つ以上のアドレス範囲で構成されます。
- Azure で提供される DNS サーバーを使用するか独自の DNS サーバーを使用するかを選択する。 仮想ネットワーク内で名前を解決するために、仮想ネットワークに接続されているすべてのリソースにこの DNS サーバーが割り当てられます。
- 仮想ネットワークをサブネットに分割し、仮想ネットワークのアドレス空間内で各サブネットに専用のアドレス範囲を設定する。 サブネットを作成、変更、および削除する方法については、[サブネットの追加、変更、削除](virtual-network-manage-subnet.md)に関する記事を参照してください。

この記事では、Azure Resource Manager デプロイメント モデルを使用して仮想ネットワークを作成する方法と、その仮想ネットワークを変更および削除する方法について説明します。

## <a name="before"></a>開始する前に

この記事に記載されているタスクを開始する前に、次の前提条件を満たしてください。

- 仮想ネットワークを初めて使用する場合は、[最初の Azure 仮想ネットワークの作成](virtual-network-get-started-vnet-subnet.md)に関するページの演習を確認することをお勧めします。 この演習は、仮想ネットワークを理解するのに役立ちます。
- 仮想ネットワークの制限については、[Azure の制限](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)に関するページをご覧ください。
- Azure アカウントを使用して、Azure Portal、Azure コマンドライン ツール (Azure CLI)、または Azure PowerShell にサインインします。 Azure アカウントを持っていない場合は、[無料試用版アカウント](https://azure.microsoft.com/free)にサインアップしてください。
- PowerShell のコマンドを使用してこの記事に記載されたタスクを行う場合は、最初に [Azure PowerShell をインストールして構成する](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json)必要があります。 最新バージョンの Azure PowerShell コマンドレットがインストールされていることを確認してください。 サンプルの PowerShell コマンドのヘルプを表示するには、「`get-help <command> -full`」と入力します。
- Azure CLI のコマンドを使用してこの記事に記載されたタスクを行う場合は、最初に [Azure CLI をインストールして構成する](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)必要があります。 最新バージョンの Azure CLI がインストールされていることを確認してください。 Azure CLI コマンドのヘルプを表示するには、「`az <command> --help`」と入力します。


## <a name="create-vnet"></a>仮想ネットワークの作成

仮想ネットワークを作成するには、次のようにします。

1. ご利用のサブスクリプションのネットワーク共同作成者ロール (またはそれ以上) のアクセス許可が割り当てられているアカウントで[ポータル](https://portal.azure.com)にサインインします。 アカウントへのロールとアクセス許可の割り当てについて詳しくは、「[Azure のロールベースのアクセス制御のための組み込みロール](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)」を参照してください。
2. **[新規]** > **[ネットワーキング]** > **[仮想ネットワーク]** をクリックします。
3. **[仮想ネットワーク]** ブレードの **[デプロイ モデルの選択]** ボックスで、**[Resource Manager]** が選択されていることを確認して、**[作成]** をクリックします。
4. **[仮想ネットワークの作成]** ブレードで、以下の設定の値を入力するか選択し、**[作成]** をクリックします。
    - **名前**: この名前は、仮想ネットワークの作成先として選択する[リソース グループ](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group)内で一意である必要があります。 仮想ネットワークの作成後は、この名前を変更できません。 長く使用する中で、仮想ネットワークを複数作成する可能性があります。 名前付けの推奨事項については、「[名前付け規則](/azure/architecture/best-practices/naming-conventions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#naming-rules-and-restrictions)」を参照してください。 名前付け規則に従うことで、複数の仮想ネットワークを管理しやすくなります。
    - **アドレス空間**: アドレス空間を CIDR 表記で指定します。 定義するアドレス空間は、パブリックとプライベート (RFC 1918 に準拠) のどちらでもかまいません。 パブリックとプライベートのどちらのアドレス空間を定義する場合でも、そのアドレス空間に到達できるのは、仮想ネットワーク内から、相互接続された仮想ネットワークから、および仮想ネットワークに接続したオンプレミス ネットワークからだけです。 次のアドレス空間は追加できません。
        - 224.0.0.0/4 (マルチキャスト)
        - 255.255.255.255/32 (ブロードキャスト)
        - 127.0.0.0/8 (ループバック)
        - 169.254.0.0/16 (リンク ローカル)
        - 168.63.129.16/32 (内部 DNS)

      仮想ネットワークを作成する際に定義できるアドレス空間は 1 つだけですが、仮想ネットワークの作成後にアドレス空間を追加することができます。 既存の仮想ネットワークにアドレス空間を追加する方法については、この記事の「[アドレス空間の追加または削除](#add-address-spaces)」を参照してください。

      >[!WARNING]
      >仮想ネットワークのアドレス空間が他の仮想ネットワークやオンプレミス ネットワークと重複している場合、そのネットワークに接続することはできません。 アドレス空間を定義する前に、将来その仮想ネットワークを他の仮想ネットワークやオンプレミス ネットワークに接続する可能性があるかどうかを検討してください。
      >
      >

    - **サブネット名**: サブネット名は仮想ネットワーク内で一意である必要があります。 サブネットの作成後は、サブネット名を変更できません。 ポータルでは、仮想ネットワークを作成する際にサブネットを 1 つ定義するよう求められますが、仮想ネットワークにサブネットを設定することは必須ではありません。 ポータルでは、仮想ネットワークの作成時に定義できるサブネットは 1 つのみです。 仮想ネットワークを作成した後で、仮想ネットワークにサブネットを追加できます。 仮想ネットワークにサブネットを追加する方法については、[サブネットの作成、変更、削除](virtual-network-manage-subnet.md)に関する記事の「[サブネットの作成](virtual-network-manage-subnet.md#create-subnet)」を参照してください。 Azure CLI または PowerShell を使用して、複数のサブネットを持つ仮想ネットワークを作成できます。

      >[!TIP]
      >管理者が複数のサブネットを作成して、サブネット間でトラフィックのフィルター処理や制御を行うことはよくあります。 サブネットを定義する前に、サブネット間のトラフィックのルーティングやフィルター処理の方法について検討してください。 サブネット間のトラフィックをフィルター処理する方法の詳細については、[ネットワーク セキュリティ グループ](virtual-networks-nsg.md)に関する記事を参照してください。 Azure ではサブネット間のルーティングが自動的に行われますが、Azure の既定のルートを上書きすることもできます。 Azure の既定のサブネット トラフィック ルーティングを上書きする方法については、[ユーザー定義のルート](virtual-networks-udr-overview.md)に関する記事を参照してください。
      >

    - **サブネット アドレス範囲**: この範囲は、仮想ネットワーク用に入力したアドレス空間内にある必要があります。 指定できる最小範囲は、/29 です。これでサブネットに 8 つの IP アドレスを使用できます。 Azure では、サブネットごとに、最初と最後のアドレスがプロトコルに準拠するために予約されています。 そのほか、3 つのアドレスが Azure サービスの使用のために予約されています。 そのため、/29 のサブネット アドレス範囲が設定された仮想ネットワークで使用できる IP アドレスは 3 つのみです。 仮想ネットワークを VPN ゲートウェイに接続する場合は、ゲートウェイ サブネットを作成する必要があります。 詳細については、[ゲートウェイ サブネットに指定するアドレス範囲の考慮事項](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub)に関する記事を参照してください。 特定の条件下でのみ、サブネットの作成後にアドレス範囲を変更できます。 サブネット アドレス範囲を変更する方法については、[サブネットの追加、変更、削除](virtual-network-manage-subnet.md)に関する記事の「[サブネット設定の変更](#change-subnet)」を参照してください。
    - **サブスクリプション**: [サブスクリプション](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)を選択します。 1 つの仮想ネットワークが複数の Azure サブスクリプションにまたがることはできません。 ただし、1 つのサブスクリプションの仮想ネットワークを他のサブスクリプションの仮想ネットワークに接続することはできます。 他のサブスクリプションの仮想ネットワークに接続するには、Azure VPN Gateway または仮想ネットワーク ピアリングを使用します。 仮想ネットワークに接続する Azure リソースは、仮想ネットワークと同じサブスクリプションに存在する必要があります。
    - **リソース グループ**: 既存の[リソース グループ](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-groups)を選択するか、新しいリソース グループを作成します。 仮想ネットワークに接続する Azure リソースは、仮想ネットワークと同じリソース グループに存在していても別のリソース グループに存在していてもかまいません。
    - **場所**: Azure の[場所](https://azure.microsoft.com/regions/)を選択します。この場所はリージョンとも呼ばれます。 1 つの仮想ネットワークは、Azure の 1 つの場所にのみ存在できます。 ただし、VPN ゲートウェイを使用して、1 つの場所にある仮想ネットワークを別の場所の仮想ネットワークに接続することはできます。 仮想ネットワークに接続する Azure リソースは、仮想ネットワークと同じ場所に存在する必要があります。

**コマンド**

|ツール|コマンド|
|---|---|
|Azure CLI|[az network vnet create](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name = "view-vnet"></a>仮想ネットワークと設定の表示

仮想ネットワークと設定を表示するには、次のようにします。

1. ご利用のサブスクリプションのネットワーク共同作成者ロール (またはそれ以上) のアクセス許可が割り当てられているアカウントで[ポータル](https://portal.azure.com)にサインインします。 アカウントへのロールとアクセス許可の割り当ての詳細については、「[Azure のロールベースのアクセス制御のための組み込みロール](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)」をご覧ください。
2. ポータルの検索ボックスに、「**仮想ネットワーク**」と入力します。 検索結果で、**[仮想ネットワーク]** をクリックします。
3. **[仮想ネットワーク]** ブレードで、設定を表示する仮想ネットワークをクリックします。
4. 選択した仮想ネットワークのブレードに、次の設定が表示されます。
    - **概要:** アドレス空間や DNS サーバーなど、仮想ネットワークに関する情報が表示されます。 次のスクリーンショットは、**MyVNet** という名前の仮想ネットワークの概要設定を示しています。

        ![ネットワーク インターフェイスの概要](./media/virtual-network-manage-network/vnet-overview.png)

      **[概要]** ブレードで、仮想ネットワークを別のサブスクリプションやリソース グループに移動することができます。 仮想ネットワーク を移動する方法については、[別のリソース グループまたはサブスクリプションへのリソースの移動](../azure-resource-manager/resource-group-move-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事を参照してください。 この記事には、Azure Portal、PowerShell、および Azure CLI を使用してリソースを移動するための前提条件と、移動の方法が記載されています。 仮想ネットワークに接続されているリソースもすべて、仮想ネットワークと共に移動する必要があります。
    - **アドレス空間**: 仮想ネットワークに割り当てられているアドレス空間が一覧表示されます。 アドレス空間の追加と削除の方法については、この記事の「[アドレス空間の追加または削除](#address-spaces)」に記載された手順を参照してください。
    - **接続されているデバイス**: 仮想ネットワークに接続されているすべてのリソースが一覧表示されます。 前のスクリーンショットでは、3 つのネットワーク インターフェイスと 1 つのロード バランサーが仮想ネットワークに接続されています。 新しく作成して仮想ネットワークに接続したリソースはすべて一覧表示されます。 仮想ネットワークに接続されていたリソースを削除すると、一覧に表示されなくなります。
    - **サブネット**: 仮想ネットワーク内に存在するサブネットの一覧が表示されます。 サブネットの追加と削除の方法については、[サブネットの追加、変更、削除](virtual-network-manage-subnet.md)に関する記事の「[サブネットの作成](virtual-network-manage-subnet.md#create-subnet)」と「[サブネットの削除](virtual-network-manage-subnet.md#delete-subnet)」を参照してください。
    - **DNS サーバー**: 仮想ネットワークに接続されているデバイスの名前解決を Azure の内部 DNS サーバーとカスタム DNS サーバーのどちらで提供するかを指定できます。 Azure Portal を使用して仮想ネットワークを作成する場合は、既定で、仮想ネットワーク内の名前解決には Azure の DNS サーバーが使用されます。 DNS サーバーを変更するには、この記事の「[DNS サーバーの追加、変更、削除](#dns-servers)」の手順を実行してください。
    - **ピアリング**: サブスクリプションに既存のピアリングがある場合は、ここに表示されます。 既存のピアリング設定の表示や、ピアリングの作成、変更、削除を行うことができます。 ピアリングの詳細については、「[仮想ネットワーク ピアリング](virtual-network-peering-overview.md)」を参照してください。
    - **プロパティ**: 仮想ネットワークのリソース ID や、仮想ネットワークが存在する Azure サブスクリプションなど、仮想ネットワークに関する設定が表示されます。
    - **ダイアグラム**: このダイアグラムには、仮想ネットワークに接続されているすべてのデバイスを視覚的に表現したものが表示されます。 ダイアグラムには、デバイスに関する重要な情報が含まれます。 このビューでダイアグラム内のデバイスを管理するには、デバイスをクリックします。
    - **Azure の一般的な設定**: Azure の一般的な設定の詳細については、次の情報を参照してください。
        *   [アクティビティ ログ](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs)
        *   [アクセス制御 (IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control)
        *   [タグ](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#tags)
        *   [ロック](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
        *   [Automation スクリプト](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group)

**コマンド**

|ツール|コマンド|
|---|---|
|Azure CLI|[az network vnet show](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#show)|
|PowerShell|[Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork/?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="add-address-spaces"></a>アドレス空間の追加または削除

仮想ネットワークのアドレス空間を追加および削除することができます。 アドレス空間は CIDR 表記で指定する必要があり、同じ仮想ネットワーク内で他のアドレス空間と重複することはできません。 定義するアドレス空間は、パブリックとプライベート (RFC 1918 に準拠) のどちらでもかまいません。 パブリックとプライベートのどちらのアドレス空間を定義する場合でも、そのアドレス空間に到達できるのは、仮想ネットワーク内から、相互接続された仮想ネットワークから、および仮想ネットワークに接続したオンプレミス ネットワークからだけです。 次のアドレス空間は追加できません。

- 224.0.0.0/4 (マルチキャスト)
- 255.255.255.255/32 (ブロードキャスト)
- 127.0.0.0/8 (ループバック)
- 169.254.0.0/16 (リンク ローカル)
- 168.63.129.16/32 (内部 DNS)

アドレス空間を追加または削除するには、次のようにします。

1. ご利用のサブスクリプションのネットワーク共同作成者ロール (またはそれ以上) のアクセス許可が割り当てられているアカウントで[ポータル](https://portal.azure.com)にサインインします。 アカウントへのロールとアクセス許可の割り当ての詳細については、「[Azure のロールベースのアクセス制御のための組み込みロール](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)」をご覧ください。
2. ポータルの検索ボックスに、「**仮想ネットワーク**」と入力します。 検索結果で、**[仮想ネットワーク]** を選択します。
3. **[仮想ネットワーク]** ブレードで、アドレス空間を追加または削除する仮想ネットワークをクリックします。
4. 仮想ネットワーク ブレードの **[設定]** で **[アドレス空間]** をクリックします。
5. アドレス空間のブレードで、次のオプションのいずれかを実行します。
    - **アドレス空間の追加**: 新しいアドレス空間を入力します。 この仮想ネットワークに定義されている既存のアドレス空間と重複するアドレス空間を追加することはできません。
    - **アドレス空間の削除**: アドレス空間を右クリックして、**[削除]** をクリックします。 アドレス空間にサブネットが存在する場合は、アドレス空間を削除することはできません。 アドレス空間を削除するには、まず、アドレス空間内にあるすべてのサブネット (およびサブネットに接続されているすべてのリソース) を削除する必要があります。
6. [ **Save**] をクリックします。

**コマンド**

|ツール|コマンド|
|---|---|
|Azure CLI|Resource Manager のみ|[az network vnet update](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="dns-servers"></a>DNS サーバーの追加、変更、削除

仮想ネットワークに接続されている VM はすべて、その仮想ネットワークに指定した DNS サーバーに登録されます。 また、指定した DNS サーバーを名前解決に使用します。 VM 内のネットワーク インターフェイス (NIC) ごとに独自の DNS サーバー設定を構成できます。 NIC に独自の DNS サーバー設定を構成すると、仮想ネットワークの DNS サーバー設定が上書きされます。 NIC DNS 設定の詳細については、[ネットワーク インターフェイスのタスクと設定](virtual-network-network-interface.md#change-dns-servers)に関する記事を参照してください。 Azure Cloud Services での VM とロール インスタンスの名前解決の詳細については、「[VM とロール インスタンスの名前解決](virtual-networks-name-resolution-for-vms-and-role-instances.md)」を参照してください。 DNS サーバーを追加、変更、削除するには、次のようにします。

1. ご利用のサブスクリプションのネットワーク共同作成者ロール (またはそれ以上) のアクセス許可が割り当てられているアカウントで[ポータル](https://portal.azure.com)にサインインします。 アカウントへのロールとアクセス許可の割り当てについて詳しくは、「[Azure のロールベースのアクセス制御のための組み込みロール](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)」を参照してください。
2. ポータルの検索ボックスに、「**仮想ネットワーク**」と入力します。 検索結果で、**[仮想ネットワーク]** を選択します。
3. **[仮想ネットワーク]** ブレードで、DNS 設定を変更する仮想ネットワークをクリックします。
4. 仮想ネットワーク ブレードの **[設定]** で **[DNS サーバー]** をクリックします。
5. DNS サーバーが一覧表示されたブレードで、次のオプションのいずれかを実行します。
    - **既定 (Azure 提供)**: すべてのリソースの名前とプライベート IP アドレスが Azure DNS サーバーに自動的に登録されます。 同じ仮想ネットワークに接続されているリソース間で名前を解決することができます。 このオプションでは、仮想ネットワーク間で名前を解決することはできません。 仮想ネットワーク間で名前を解決するには、カスタムの DNS サーバーを使用する必要があります。
    - **カスタム**: 1 つ以上のサーバーを追加できます。ただし、追加できる数は Azure で仮想ネットワークに設定されている上限までです。 DNS サーバー数の上限の詳細については、[Azure の制限](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-networking-limits-classic)に関する記事を参照してください。 次のオプションがあります。
        - **アドレスの追加**: サーバーを仮想ネットワークの DNS サーバー一覧に追加します。 また、その DNS サーバーを Azure に登録します。 既に DNS サーバーが Azure に登録されている場合は、その DNS サーバーを一覧で選択できます。
        - **アドレスの削除**: 削除するサーバーの横にある **[X]** をクリックします。サーバーを削除しても、この仮想ネットワークの一覧からサーバーが削除されるだけです。 DNS サーバーは、他の仮想ネットワークで使用するために Azure に登録されたままになります。
        - **DNS サーバーのアドレスの並べ替え**: 環境に適した順序で DNS サーバーが一覧表示されていることを確認することが重要です。 DNS サーバーの一覧は指定された順序で使用されます。 ラウンド ロビン設定のようには機能しません。 一覧の先頭にある DNS サーバーに到達できる場合、クライアントはその DNS サーバーが正しく動作しているかどうかに関係なく、その DNS サーバーを使用します。 一覧表示されたすべての DNS サーバーを削除してから、希望する順序で再度追加します。
        - **アドレスの変更**: 一覧で DNS サーバーを強調表示し、新しい名前を入力します。
6. [ **Save**] をクリックします。
7. 新しい DNS サーバー設定が割り当てられるように、仮想ネットワークに接続されている VM を再起動します。 再起動されるまで、VM は現在の DNS 設定を使用し続けます。

**コマンド**

|ツール|コマンド|
|---|---|
|Azure CLI|[az network vnet update](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="delete-vnet"></a>仮想ネットワークの削除

接続されているリソースがない場合にのみ、仮想ネットワークを削除できます。 仮想ネットワーク内のいずれかのサブネットに接続されているリソースがある場合は、最初に、仮想ネットワーク内のすべてのサブネットに接続されているリソースを削除する必要があります。 リソースを削除する方法は、リソースによって異なります。 サブネットに接続されているリソースを削除する方法については、削除するリソースの種類に応じたドキュメントを参照してください。 仮想ネットワークを削除するには、次のようにします。

1. ご利用のサブスクリプションのネットワーク共同作成者ロール (またはそれ以上) のアクセス許可が割り当てられているアカウントで[ポータル](https://portal.azure.com)にサインインします。 アカウントへのロールとアクセス許可の割り当てについて詳しくは、「[Azure のロールベースのアクセス制御のための組み込みロール](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)」を参照してください。
2. ポータルの検索ボックスに、「**仮想ネットワーク**」と入力します。 検索結果で、**[仮想ネットワーク]** をクリックします。
3. **[仮想ネットワーク]** ブレードで、削除する仮想ネットワークをクリックします。
4. [仮想ネットワーク] ブレードで、仮想ネットワークに接続されているデバイスがないことを確認するために、**[設定]** の **[接続されているデバイス]** をクリックします。 接続されているデバイスがある場合は、先にそのデバイスを削除してから仮想ネットワークを削除する必要があります。 接続されているデバイスがない場合は、**[概要]** をクリックします。
5. ブレードの上部にある **[削除]** アイコンをクリックします。
6. **[はい]** をクリックして、仮想ネットワークの削除を確認します。


**コマンド**

|ツール|コマンド|
|---|---|
|Azure CLI|[azure network vnet delete](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|PowerShell|[Remove-AzureRmVirtualNetwork](/powershell/module/azurerm.network/remove-azurermvirtualnetwork?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="next-steps"></a>次のステップ

- VM を作成して仮想ネットワークに接続する場合は、[仮想ネットワークの作成と VM の接続](virtual-network-get-started-vnet-subnet.md#create-vms)に関するページを参照してください。
- 仮想ネットワーク内のサブネット間のネットワーク トラフィックをフィルター処理する場合は、[ネットワーク セキュリティ グループの作成](virtual-networks-create-nsg-arm-pportal.md)に関する記事を参照してください。
- 仮想ネットワークを別の仮想ネットワークにピアリングする場合は、[仮想ネットワーク ピアリングの作成](virtual-network-create-peering.md#portal)に関する記事を参照してください。
- オンプレミスのネットワークに仮想ネットワークを接続するためのオプションの詳細については、[VPN Gateway の概要](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#diagrams)に関する記事を参照してください。
