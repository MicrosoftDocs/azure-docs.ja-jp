---
title: "Azure 仮想ネットワーク サブネットの追加、変更、削除 | Microsoft Docs"
description: "Azure で仮想ネットワーク サブネットを追加、変更、削除する方法について説明します。"
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
ms.openlocfilehash: 85ba6ef3e51c339a77eb9b4198c4f87e2a64cf09
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="add-change-or-delete-a-virtual-network-subnet"></a>仮想ネットワーク サブネットの追加、変更、削除

仮想ネットワーク サブネットを追加、変更、削除する方法について説明します。 

仮想ネットワークを使い慣れていない場合は、サブネットを追加、変更、または削除する前に、[Azure Virtual Network の概要](virtual-networks-overview.md)に関するページと「[仮想ネットワークの作成、変更、削除](virtual-network-manage-network.md)」をお読みになることをお勧めします。 仮想ネットワークにデプロイされているすべての Azure リソースは、仮想ネットワーク内のサブネットにデプロイされます。 通常は次の目的のために、複数のサブネットを仮想ネットワーク内に作成します。
- **サブネット間のトラフィックのフィルター処理**。 サブネットにネットワーク セキュリティ グループを適用して、仮想ネットワーク内のすべてのリソース (仮想マシンなど) の受信および送信ネットワーク トラフィックをフィルター処理できます。 ネットワーク セキュリティ グループを作成する方法の詳細については、[ネットワーク セキュリティ グループの作成](virtual-networks-create-nsg-arm-pportal.md)に関するページをご覧ください。
- **サブネット間のルーティングの制御**。 Azure では、サブネット間のトラフィックが自動的にルーティングされるように既定のルートが作成されています。 ユーザー定義ルートを作成して、Azure の既定のルートを上書きできます。 ユーザー定義ルートの詳細については、[ユーザー定義ルートの作成](virtual-network-create-udr-arm-ps.md)に関するページをご覧ください。 

ここでは、Azure Resource Manager デプロイメント モデルを使用して作成された仮想ネットワークのサブネットを追加、変更、削除する方法について説明します。
 
## <a name="before"></a>開始する前に

この記事に記載されているタスクを開始する前に、次の前提条件を満たしてください。

- 仮想ネットワークを初めて使用する場合は、[最初の Azure 仮想ネットワークの作成](virtual-network-get-started-vnet-subnet.md)に関するページの演習を確認することをお勧めします。 この演習は、仮想ネットワークを理解するのに役立ちます。
- 仮想ネットワークの制限については、[Azure の制限](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)に関するページをご覧ください。
- Azure アカウントを使用して、Azure Portal、Azure コマンドライン ツール (Azure CLI)、または Azure PowerShell にサインインします。 Azure アカウントを持っていない場合は、[無料試用版アカウント](https://azure.microsoft.com/free)にサインアップしてください。
- PowerShell のコマンドを使用してこの記事に記載されたタスクを行う場合は、最初に [Azure PowerShell をインストールして構成する](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json)必要があります。 最新バージョンの Azure PowerShell コマンドレットがインストールされていることを確認してください。 サンプルの PowerShell コマンドのヘルプを表示するには、「`get-help <command> -full`」と入力します。
- Azure CLI のコマンドを使用してこの記事に記載されたタスクを行う場合は、次のいずれかを行う必要があります。
    - [Azure CLI をインストールして構成する](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)。 最新バージョンの Azure CLI がインストールされていることを確認してください。
    - Azure Cloud Shell を使用する。 CLI とその依存関係をインストールする代わりに、Azure Cloud Shell を使うことができます。 Azure Cloud Shell は、Azure Portal 内で直接実行できる無料の Bash シェルです。 Azure CLI が事前にインストールされており、アカウントで使用できるように構成されています。 Cloud Shell を使用するには、Azure Portal の上部にある Cloud Shell (**>_**) アイコンをクリックします。 

  Azure CLI コマンドのヘルプを表示するには、「`az <command> --help`」と入力します。

## <a name="create-subnet"></a>サブネットの追加

サブネットを追加するには、次の手順に従います。

1. ご利用のサブスクリプションのネットワーク共同作成者ロール (またはそれ以上) のアクセス許可が割り当てられているアカウントで[ポータル](https://portal.azure.com)にサインインします。 アカウントへのロールとアクセス許可の割り当ての詳細については、「[Azure のロールベースのアクセス制御のための組み込みロール](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)」をご覧ください。
2. ポータルの検索ボックスに、「**仮想ネットワーク**」と入力します。 検索結果で、**[仮想ネットワーク]** をクリックします。
3. **[仮想ネットワーク]** ブレードで、サブネットを追加する仮想ネットワークをクリックします。
4. 仮想ネットワーク ブレードで **[サブネット]** をクリックします。
5. **[+サブネット]** をクリックします。
6. **[サブネットの追加]** ブレードで、次のパラメーターの値を入力します。
    - **[名前]**: 名前は仮想ネットワーク内で一意である必要があります。
    - **[アドレス範囲]**: 範囲は仮想ネットワークのアドレス空間内で一意である必要があります。 仮想ネットワーク内の他のサブネット アドレス範囲と重複することはできません。 アドレス空間は、Classless Inter-Domain Routing (CIDR) 表記で指定する必要があります。 たとえば、アドレス空間が 10.0.0.0/16 の仮想ネットワークでは、10.0.0.0/24 のサブネット アドレス空間を定義できます。 指定できる最小範囲は、/29 です。これでサブネットに 8 つの IP アドレスを使用できます。 Azure では、サブネットごとに、最初と最後のアドレスがプロトコルに準拠するために予約されています。 そのほか、3 つのアドレスが Azure サービスの使用のために予約されています。 そのため、/29 のアドレス範囲が設定されたサブネットでは、使用できる IP アドレスが 3 つになります。 仮想ネットワークを VPN ゲートウェイに接続する場合は、ゲートウェイ サブネットを作成する必要があります。 詳細については、[ゲートウェイ サブネットに指定するアドレス範囲の考慮事項](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub)に関する記事を参照してください。 特定の条件下でのみ、サブネットの追加後にアドレス範囲を変更できます。 サブネット アドレス範囲を変更する方法については、この記事の「[サブネット設定の変更](#change-subnet)」をご覧ください。
    - **[ネットワーク セキュリティ グループ]**: 必要に応じて、既存のネットワーク セキュリティ グループをサブネットに関連付けることで、サブネットの受信および送信ネットワーク トラフィックのフィルター処理を制御できます。 ネットワーク セキュリティ グループは、仮想ネットワークと同じサブスクリプションと場所に存在する必要があります。 また、Resource Manager デプロイメント モデルを使用して作成する必要があります。 ネットワーク セキュリティ グループを作成する方法の詳細については、[ネットワーク セキュリティ グループ](virtual-networks-create-nsg-arm-pportal.md)に関するページをご覧ください。
    - **[ルート テーブル]**: 必要に応じて、既存のルート テーブルをサブネットに関連付けることで、他のネットワークへのネットワーク トラフィック ルーティングを制御できます。 ルート テーブルは、仮想ネットワークと同じサブスクリプションと場所に存在する必要があります。 また、Resource Manager デプロイメント モデルを使用して作成する必要があります。 ルート テーブルを作成する方法の詳細については、[ユーザー定義ルート](virtual-network-create-udr-arm-ps.md)に関するページをご覧ください。
    - **[ユーザー]**: 組み込みロールまたは独自のカスタム ロールを使用して、サブネットへのアクセス権を制御できます。 サブネットにアクセスするロールとユーザーの割り当ての詳細については、[ロールの割り当てを使用した Azure リソースへのアクセス権の管理](../active-directory/role-based-access-control-configure.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-access)に関するページをご覧ください。
7. 選択した仮想ネットワークにサブネットを追加するには、**[OK]** をクリックします。

**コマンド**

|ツール|コマンド|
|---|---|
|Azure CLI|[az network vnet subnet create](/cli/azure/network/vnet/subnet?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)、[Add-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="change-subnet"></a>サブネット設定の変更

サブネット内のリソースを管理することにより、ネットワーク セキュリティ グループ、ルート テーブル、サブネットへのユーザー アクセス権を変更できます。 これらの設定の詳細については、「[サブネットの追加](#create-subnet)」の手順 6 をご覧ください。 サブネットのアドレス範囲を変更するには、まずそのサブネット内のすべてのリソースを削除する必要があります。 リソースを削除する方法は、リソースによって異なります。 サブネット内のリソースを削除する方法については、削除するリソースの種類に応じたドキュメントをご覧ください。 サブネットのアドレス範囲を変更するには

1. ご利用のサブスクリプションのネットワーク共同作成者ロール (またはそれ以上) のアクセス許可が割り当てられているアカウントで[ポータル](https://portal.azure.com)にサインインします。 アカウントへのロールとアクセス許可の割り当ての詳細については、「[Azure のロールベースのアクセス制御のための組み込みロール](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)」をご覧ください。
2. ポータルの検索ボックスに、「**仮想ネットワーク**」と入力します。 検索結果で、**[仮想ネットワーク]** をクリックします。
3. **[仮想ネットワーク]** ブレードで、サブネットのアドレス範囲を変更する仮想ネットワークをクリックします。
4. アドレス範囲を変更するサブネットをクリックします。
5. サブネット ブレードの **[アドレス範囲]** ボックスに、新しいアドレス範囲を入力します。 範囲は仮想ネットワークのアドレス空間内で一意である必要があります。 仮想ネットワーク内の他のサブネット アドレス範囲と重複することはできません。 アドレス空間は、CIDR 表記で指定する必要があります。 たとえば、アドレス空間が 10.0.0.0/16 の仮想ネットワークでは、10.0.0.0/24 のサブネット アドレス空間を定義できます。 指定できる最小範囲は、/29 です。これでサブネットに 8 つの IP アドレスを使用できます。 Azure では、サブネットごとに、最初と最後のアドレスがプロトコルに準拠するために予約されています。 そのほか、3 つのアドレスが Azure サービスの使用のために予約されています。 そのため、/29 のアドレス範囲が設定されたサブネットで使用できる IP アドレスは 3 つです。 仮想ネットワークを VPN ゲートウェイに接続する場合は、ゲートウェイ サブネットを作成する必要があります。 詳細については、[ゲートウェイ サブネットに指定するアドレス範囲の考慮事項](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub)に関する記事を参照してください。 特定の条件下でのみ、サブネットの作成後にアドレス範囲を変更できます。 サブネット アドレス範囲を変更する方法については、この記事の「[サブネット設定の変更](#change-subnet)」をご覧ください。
6. [ **Save**] をクリックします。

**コマンド**

|ツール|コマンド|
|---|---|
|Azure CLI|[az network vnet subnet update](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="delete-subnet"></a>サブネットの削除

サブネット内にリソースがない場合にのみ、サブネットを削除できます。 サブネット内にリソースがある場合は、サブネットを削除する前に、そのサブネット内のリソースを削除する必要があります。 リソースを削除する方法は、リソースによって異なります。 サブネット内のリソースを削除する方法については、削除するリソースの種類に応じたドキュメントをご覧ください。 サブネットを削除するには

1. ご利用のサブスクリプションのネットワーク共同作成者ロール (またはそれ以上) のアクセス許可が割り当てられているアカウントで[ポータル](https://portal.azure.com)にサインインします。 アカウントへのロールとアクセス許可の割り当ての詳細については、「[Azure のロールベースのアクセス制御のための組み込みロール](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)」をご覧ください。
2. ポータルの検索ボックスに、「**仮想ネットワーク**」と入力します。 検索結果で、**[仮想ネットワーク]** をクリックします。
3. **[仮想ネットワーク]** ブレードで、サブネットを削除する仮想ネットワークをクリックします。
4. 仮想ネットワーク ブレードの **[設定]** で **[サブネット]** をクリックします。
5. サブネット ブレードに表示されたサブネットの一覧で、削除するサブネットを右クリックし、**[削除]** をクリックします。**[はい]** をクリックすると、サブネットが削除されます。

**コマンド**

|ツール|コマンド|
|---|---|
|Azure CLI|[az network vnet delete](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|PowerShell|[Remove-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/remove-azurermvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="next-steps"></a>次のステップ

サブネットに仮想マシンを作成する方法については、[仮想ネットワークの作成とサブネットへの VM のデプロイ](virtual-network-get-started-vnet-subnet.md#create-vms)に関するページをご覧ください。
