---
title: "Azure ネットワーク インターフェイスの作成、変更、削除 | Microsoft Docs"
description: "ネットワーク インターフェイスとは何かを説明し、その作成、設定変更、および削除を行う方法を示します。"
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
ms.date: 07/24/2017
ms.author: jdial
ms.openlocfilehash: 7dafb491cec908ffbb3683991919654f3d3eb452
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="create-change-or-delete-a-network-interface"></a>ネットワーク インターフェイスの作成、変更、削除

ネットワーク インターフェイスの作成、設定変更、削除の方法について説明します。 ネットワーク インターフェイスは、Azure 仮想マシンがインターネット、Azure、およびオンプレミスのリソースと通信できるようにします。 Azure Portal を使用して仮想マシンを作成すると、既定の設定でネットワーク インターフェイスが 1 つ自動的に作成されます。 その代わりに、カスタム設定でネットワーク インターフェイスを作成し、仮想マシンを作成するときに 1 つ以上のネットワーク インターフェイスを追加することができます。 既存のネットワーク インターフェイスについて、既定のネットワーク インターフェイス設定を変更することもできます。 この記事では、カスタム設定を使用してネットワーク インターフェイスを作成する方法、ネットワーク フィルター (ネットワーク セキュリティ グループ) の割り当て、サブネットの割り当て、DNS サーバーの設定、IP 転送などの既存の設定を変更する方法、およびネットワーク インターフェイスを削除する方法について説明します。

ネットワーク インターフェイスに対して IP アドレスの追加、変更、または削除が必要な場合は、[IP アドレスの管理](virtual-network-network-interface-addresses.md)についての記事を参照してください。 仮想マシンのネットワーク インターフェイスを追加または削除する必要がある場合は、[ネットワーク インターフェイスの追加または削除](virtual-network-network-interface-vm.md)についての記事を参照してください。


## <a name="before-you-begin"></a>開始する前に

この記事のセクションに記載された手順を始める前に、次のタスクを完了してください。

- ネットワーク インターフェイスの制限については、[Azure の制限](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)に関する記事を参照してください。
- Azure アカウントを使用して、Azure [Portal](https://portal.azure.com)、Azure コマンド ライン インターフェイス (CLI)、または Azure PowerShell にログインします。 まだ Azure アカウントを持っていない場合は、[無料試用版アカウント](https://azure.microsoft.com/free)にサインアップしてください。
- PowerShell コマンドを使用してこの記事のタスクを実行する場合は、[Azure PowerShell をインストールして構成します](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json)。 最新バージョンの Azure PowerShell コマンドレットがインストールされていることを確認してください。 PowerShell コマンドのヘルプとサンプルを表示するには、「`get-help <command> -full`」と入力します。
- Azure コマンド ライン インターフェイス (CLI) のコマンドを使ってこの記事のタスクを実行する場合は、[Azure CLI をインストールして構成します](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)。 最新バージョンの Azure CLI がインストールされていることを確認してください。 CLI コマンドのヘルプを表示するには、「`az <command> --help`」と入力します。 CLI とその前提条件をインストールする代わりに、Azure Cloud Shell を使うことができます。 Azure Cloud Shell は、Azure Portal 内で直接実行できる無料の Bash シェルです。 Azure CLI が事前にインストールされており、アカウントで使用できるように構成されています。 Cloud Shell を使うには、[ポータル](https://portal.azure.com)の上部にある [Cloud Shell**> _**] ボタンをクリックします。

## <a name="create-a-network-interface"></a>ネットワーク インターフェイスの作成

Azure Portal を使用して仮想マシンを作成すると、既定の設定で自動的にネットワーク インターフェイスが作成されます。 その代わりにすべてのネットワーク インターフェイス設定を指定する場合は、カスタム設定を使用してネットワーク インターフェイスを作成し、仮想マシンを作成するときにそのネットワーク インターフェイスを仮想マシンにアタッチすることができます (PowerShell または Azure CLI を使用)。 ネットワーク インターフェイスを作成して、それを既存の仮想マシンに追加することもできます (PowerShell または Azure CLI を使用)。 既存のネットワーク インターフェイスを使用して仮想マシンを作成する方法や、既存の仮想マシンに対してネットワーク インターフェイスを追加または削除する方法については、[ネットワーク インターフェイスの追加または削除](virtual-network-network-interface-vm.md)についての記事を参照してください。 ネットワーク インターフェイスを作成する前に、ネットワーク インターフェイスを作成するのと同じ場所およびサブスクリプションに[仮想ネットワーク](virtual-networks-create-vnet-arm-pportal.md)が作成されている必要があります。

1. ご利用のサブスクリプションのネットワーク作成協力者ロール (またはそれ以上) のアクセス許可が割り当てられているアカウントで [Azure Portal](https://portal.azure.com) にログインします。 アカウントへのロールとアクセス許可の割り当てについて詳しくは、「[Azure のロールベースのアクセス制御のための組み込みロール](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)」の記事をご覧ください。
2. Azure Portal 上部に "*リソースの検索*" というテキストが表示されたボックスがあります。そこに "*ネットワーク インターフェイス*" と入力します。 検索結果に **[ネットワーク インターフェイス]** が表示されたら、それをクリックします。
3. **[ネットワーク インターフェイス]** ブレードが表示されたら、**[+ 追加]** をクリックします。
4. 表示される **[ネットワーク インターフェイスの作成]** ブレードで以下の設定の値を入力するか選択し、**[作成]** をクリックします。

    |設定|必須|詳細|
    |---|---|---|
    |名前|あり|選択したリソース グループ内で一意となる名前を使用してください。 長い期間の間には、Azure サブスクリプションに複数のネットワーク インターフェイスを作成する可能性があります。 複数のネットワーク インターフェイスを管理しやすいように、名前付け規則を作成する際は、[名前付け規則](/azure/architecture/best-practices/naming-conventions?toc=%2fazure%2fvirtual-network%2ftoc.json#naming-rules-and-restrictions)に関する記事に記載された推奨事項をご覧ください。 名前は、ネットワーク インターフェイスの作成後に変更することはできません。|
    |Virtual Network|あり|ネットワーク インターフェイスの仮想ネットワークを選択します。 ネットワーク インターフェイスの割り当てが可能なのは、そのネットワーク インターフェイスと同じサブスクリプションおよび場所に存在する仮想ネットワークのみです。 いったんネットワーク インターフェイスが作成されると、それが割り当てられた仮想ネットワークを変更することはできません。 ネットワーク インターフェイスを追加する仮想マシンも、そのネットワーク インターフェイスと同じ場所およびサブスクリプション内に存在する必要があります。|
    |サブネット|あり|選択した仮想ネットワーク内のサブネットを選択します。 ネットワーク インターフェイスの割り当て先のサブネットは、作成後に変更できます。|
    |プライベート IP アドレスの割り当て|あり| この設定では、IPv4 アドレスの割り当て方法を選択します。 次の割り当て方法から選択します。**動的:** このオプションを選択すると、選択したサブネットのアドレス空間から使用可能なアドレスが自動的に割り当てられます。 ネットワーク インターフェイスがある仮想マシンが、停止 (割り当て解除) 状態になった後に起動された場合、そのネットワーク インターフェイスに異なるアドレスが割り当てられる可能性があります。 仮想マシンが、停止 (割り当て解除) 状態にならずに再起動された場合、アドレスは変わりません。 **静的:** このオプションを選択した場合は、選択したサブネットのアドレス空間内から使用可能な IP アドレスを手動で割り当てる必要があります。 静的アドレスは、ユーザーが変更するか、ネットワーク インターフェイスが削除されるまで変化しません。 ネットワーク インターフェイスの作成後に、割り当て方法を変更することができます。 このアドレスは Azure DHCP サーバーによって、仮想マシンのオペレーティング システム内のネットワーク インターフェイスに割り当てられます。|
    |ネットワーク セキュリティ グループ|いいえ| **[なし]** に設定されたままにするか、既存の[ネットワーク セキュリティ グループ](virtual-networks-nsg.md)を選択するか、[ネットワーク セキュリティ グループを作成する](virtual-networks-create-nsg-arm-pportal.md)ことができます。 ネットワーク セキュリティ グループによって、ネットワーク インターフェイスから送受信されるネットワーク トラフィックをフィルター処理できます。 ネットワーク インターフェイスには、0 個または 1 個のネットワーク セキュリティ グループを適用できます。 0 個または 1 つのネットワーク セキュリティ グループは、ネットワーク インターフェイスの割り当て先サブネットにも適用できます。 ネットワーク セキュリティ グループを、ネットワーク インターフェイスと、その割り当て先のサブネットに適用すると、予期しない結果が発生する場合があります。 ネットワーク インターフェイスとサブネットに適用したネットワーク セキュリティ グループのトラブルシューティングを行うには、「[ネットワーク セキュリティ グループのトラブルシューティング](virtual-network-nsg-troubleshoot-portal.md#nsg)」の記事を参照してください。|
    |[サブスクリプション]|あり|ご利用の Azure [サブスクリプション](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)を 1 つ選択します。 ネットワーク インターフェイスのアタッチ先の仮想マシンと、接続先の仮想ネットワークは、同じサブスクリプション内に存在する必要があります。|
    |[プライベート IP アドレス (IPv6)]|いいえ| このチェック ボックスをオンにすると、ネットワーク インターフェイスに割り当てられた IPv4 アドレスに加えて、IPv6 アドレスがネットワーク インターフェイスに割り当てられます。 ネットワーク インターフェイスでの IPv6 の使用に関する重要な情報については、この記事の [IPv6](#IPv6) についてのセクションを参照してください。 IPv6 アドレスの割り当て方法を選択することはできません。 IPv6 アドレスを割り当てることを選択した場合、動的な方法で割り当てが行われます。
    |[IPv6 名] \(**[プライベート IP アドレス (IPv6)]** チェック ボックスがオンの場合にのみ表示されます) |はい (**[プライベート IP アドレス (IPv6)]** チェック ボックスがオンの場合)。| この名前は、ネットワーク インターフェイスのセカンダリ IP 構成に割り当てられます。 IP 構成の詳細については、この記事の「[ネットワーク インターフェイス設定の表示](#view-network-interface-settings)」セクションで説明しています。|
    |リソース グループ|あり|既存の[リソース グループ](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group)を選択するか、新しく作成することができます。 ネットワーク インターフェイスは、アタッチ先の仮想マシンや接続先の仮想ネットワークと同じリソース グループに存在することも、違うリソース グループに存在することも可能です。|
    |場所|あり|ネットワーク インターフェイスのアタッチ先の仮想マシンと接続先の仮想ネットワークは、リージョンとも呼ばれる同じ[場所](https://azure.microsoft.com/regions)の中に存在する必要があります。|

ポータルには、ネットワーク インターフェイスを作成するときに、そこにパブリック IP アドレスを割り当てるオプションは用意されていません。しかし、ポータルを使用して仮想マシンを作成するときには、確かにポータルによってパブリック IP アドレスが作成され、それがネットワーク インターフェイスに割り当てられます。 ネットワーク インターフェイスの作成後にパブリック IP アドレスを追加する方法については、[IP アドレスの管理](virtual-network-network-interface-addresses.md)についての記事を参照してください。 パブリック IP アドレスを指定してネットワーク インターフェイスを作成する場合は、CLI または PowerShell を使用してネットワーク インターフェイスを作成する必要があります。

>[!Note]
> Azure では、ネットワーク インターフェイスの仮想マシンへのアタッチと、仮想マシンの初回起動の後にのみ、ネットワーク インターフェイスに MAC アドレスが割り当てられます。 Azure によってネットワーク インターフェイスに割り当てられる MAC アドレスは指定できません。 MAC アドレスがネットワーク インターフェイスに割り当てられると、そのネットワーク インターフェイスが削除されるか、プライマリ ネットワーク インターフェイスのプライマリ IP 構成に割り当てられたプライベート IP アドレスが変更されるまで、その状態は変わりません。 IP アドレスと IP 構成について詳しくは、[IP アドレスの管理](virtual-network-network-interface-addresses.md)についての記事を参照してください。

**コマンド**

|ツール|コマンド|
|---|---|
|CLI|[az network nic create](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|

## <a name="view-network-interface-settings"></a>ネットワーク インターフェイス設定の表示

ネットワーク インターフェイスが作成された後、そのほとんどの設定を表示して変更することができます。

1. ご利用のサブスクリプションのネットワーク作成協力者ロール (またはそれ以上) のアクセス許可が割り当てられているアカウントで [Azure Portal](https://portal.azure.com) にログインします。 アカウントへのロールとアクセス許可の割り当てについて詳しくは、「[Azure のロールベースのアクセス制御のための組み込みロール](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)」の記事をご覧ください。
2. Azure Portal 上部に "*リソースの検索*" というテキストが表示されたボックスがあります。そこに "*ネットワーク インターフェイス*" と入力します。 検索結果に **[ネットワーク インターフェイス]** が表示されたら、それをクリックします。
3. 表示される **[ネットワーク インターフェイス]** ブレードで、設定を表示または変更するネットワーク インターフェイスをクリックします。
4. 選択したネットワーク インターフェイスについて表示されるブレードには、以下の設定が一覧で表示されます。
    - **概要:** ネットワーク インターフェイスに割り当てられている IP アドレス、ネットワーク インターフェイスの割り当て先仮想ネットワーク/サブネット、ネットワーク インターフェイスのアタッチ先仮想マシン (1 つの仮想マシンにアタッチされている場合) など、ネットワーク インターフェイスに関する情報を提供します。 次の図は、**mywebserver256** という名前のネットワーク インターフェイスの概要設定を示しています。![ネットワーク インターフェイスの概要](./media/virtual-network-network-interface/nic-overview.png)ネットワーク インターフェイスを別のリソース グループまたはサブスクリプションに移動するには、**[リソース グループ]** または **[サブスクリプション名]** の横にある **[(変更)]** をクリックします。 ネットワーク インターフェイスを移動する場合は、そのネットワーク インターフェイスに関連するすべてのリソースを、インターフェイスと共に移動する必要があります。 たとえば、ネットワーク インターフェイスが仮想マシンにアタッチされている場合は、仮想マシンと、その他の仮想マシン関連リソースも移動する必要があります。 ネットワーク インターフェイスを移動するには、「[新しいリソース グループまたはサブスクリプションへのリソースの移動](../azure-resource-manager/resource-group-move-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json#use-portal)」の記事を参照してください。 この記事には、リソースを移動するための前提条件や、Azure Portal、PowerShell、および Azure CLI を使用して移動する方法が記載されています。
    - **IP 構成:** IP 構成に割り当てられている、パブリックおよびプライベートの IPv4 および IPv6 アドレスは、ここに一覧で示されています。 IP 構成に IPv6 アドレスが割り当てられている場合、アドレスは表示されません。 IP 構成と、IP アドレスの追加と削除の方法については、「[Azure ネットワーク インターフェイスの IP アドレスの構成](virtual-network-network-interface-addresses.md)」の記事を参照してください。 IP 転送とサブネットの割り当ても、このセクションで構成します。 これらの設定の詳細については、この記事のセクション「[IP 転送の有効化と無効化](#enable-or-disable-ip-forwarding)」と「[サブネットの割り当ての変更](#change-subnet-assignment)」を参照してください。
    - **DNS サーバー:** Azure DHCP サーバーがネットワーク インターフェイスに割り当てる DNS サーバーを指定できます。 ネットワーク インターフェイスは、ネットワーク インターフェイスの割り当て先の仮想ネットワークから設定を継承できます。または、割り当て先の仮想ネットワークの設定をオーバーライドするカスタム設定を持つことができます。 表示される内容を変更するには、この記事のセクション「[DNS サーバーの変更](#change-dns-servers)」の手順を実行します。
    - **ネットワーク セキュリティ グループ (NSG):** ネットワーク インターフェイスに関連付けられている NSG (ある場合) が表示されます。 NSG には、ネットワーク インターフェイスのネットワーク トラフィックをフィルター処理するための送受信の規則が含まれています。 NSG がネットワーク インターフェイスに関連付けられている場合は、関連付けられている NSG の名前が表示されます。 表示される内容を変更するには、[ネットワーク セキュリティ グループの関連付けの管理](virtual-network-manage-nsg-arm-portal.md#manage-associations)に関する記事の手順を完了します。
    - **プロパティ:** ネットワーク インターフェイスの MAC アドレスやネットワーク インターフェイスが存在するサブスクリプションなど、ネットワーク インターフェイスに関する重要な設定が表示されます (ネットワーク インターフェイスが仮想マシン接続されていない場合は空白です)。
    - **有効なセキュリティ規則:** セキュリティ規則が一覧表示されるのは、ネットワーク インターフェイスが実行中の仮想マシンにアタッチされ、NSG がネットワーク インターフェイスまたはその接続先サブネット (あるいは両方) に関連付けられている場合です。 表示される内容について詳しくは、[ネットワーク セキュリティ グループのトラブルシューティング](virtual-network-nsg-troubleshoot-portal.md#nsg)に関する記事をご覧ください。 NSG について詳しくは、[ネットワーク セキュリティ グループ](virtual-networks-nsg.md)に関する記事をご覧ください。
    - **有効なルート:** 実行中の仮想マシンにネットワーク インターフェイスがアタッチされている場合には、ルートが一覧表示されます。 ルートは、Azure の既定のルート、任意のユーザー定義ルート (UDR)、ネットワーク インターフェイスの割り当て先サブネットのために存在する場合がある任意の BGP ルートが組み合わされたものです。 表示される内容について詳しくは、[ルートのトラブルシューティング](virtual-network-routes-troubleshoot-portal.md#view-effective-routes-for-a-network-interface)に関する記事をご覧ください。 Azure 既定ルートと UDR について詳しくは、[ユーザー定義ルート](virtual-networks-udr-overview.md)に関する記事をご覧ください。
    - **Azure Resource Manager の一般的な設定:** Azure Resource Manager の一般的な設定については、[アクティビティ ログ](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs)、[Access Control](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control)、[タグ](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#tags)[ロック](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)、および [Automation スクリプト](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group) に関する各記事をご覧ください。

**コマンド**

IPv6 アドレスがネットワーク インターフェイスに割り当てられている場合、PowerShell の出力では、アドレスが割り当てられているという事実が返されますが、割り当てられたアドレスは返されません。 同様に、CLI では、アドレスが割り当てられているという事実が返されますが、そのアドレスの出力では *null* が返されます。

|ツール|コマンド|
|---|---|
|CLI|[az network nic list](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#list) ではサブスクリプションのネットワーク インターフェイスを表示します。[az network nic show](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#show) ではネットワーク インターフェイスの設定を表示します。|
|PowerShell|[Get-AzureRmNetworkInterface](/powershell/module/azurerm.network/get-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) では、サブスクリプションのネットワーク インターフェイスまたはネットワーク インターフェイスの設定を表示します。|

## <a name="change-dns-servers"></a>DNS サーバーの変更

DNS サーバーは Azure DHCP サーバーによって、仮想マシンのオペレーティング システム内のネットワーク インターフェイスに割り当てられます。 割り当てられる DNS サーバーは、ネットワーク インターフェイスの DNS サーバー設定に基づきます。 ネットワーク インターフェイスの名前解決の設定の詳細については、[仮想マシンの名前解決](virtual-networks-name-resolution-for-vms-and-role-instances.md)に関する記事を参照してください。 ネットワーク インターフェイスは、仮想ネットワークから設定を継承することも、仮想ネットワークの設定を上書きする独自の固有設定を使用することもできます。

1. ご利用のサブスクリプションのネットワーク作成協力者ロール (またはそれ以上) のアクセス許可が割り当てられているアカウントで [Azure Portal](https://portal.azure.com) にログインします。 アカウントへのロールとアクセス許可の割り当てについて詳しくは、「[Azure のロールベースのアクセス制御のための組み込みロール](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)」の記事をご覧ください。
2. Azure Portal 上部に "*リソースの検索*" というテキストが表示されたボックスがあります。そこに "*ネットワーク インターフェイス*" と入力します。 検索結果に **[ネットワーク インターフェイス]** が表示されたら、それをクリックします。
3. 表示される **[ネットワーク インターフェイス]** ブレードで、設定を表示または変更するネットワーク インターフェイスをクリックします。
4. 選択したネットワーク インターフェイスのブレードで、**[設定]** にある **[DNS サーバー]** をクリックします。
5. 次のいずれかをクリックします。
    - **[仮想ネットワークから継承する] (既定)**: このオプションを選択すると、ネットワーク インターフェイスの割り当て先の仮想ネットワークで定義されている DNS サーバー設定を継承します。 仮想ネットワーク レベルでは、カスタム DNS サーバーまたは Azure 提供の DNS サーバーのいずれかが定義されます。 Azure 提供の DNS サーバーは、同じ仮想ネットワークに割り当てられているリソースのホスト名を解決できます。 異なる仮想ネットワークに割り当てられているリソースの名前解決には、FQDN を使用する必要があります。
    - **[カスタム]**: 複数の仮想ネットワークにわたって名前を解決する独自の DNS サーバーを構成できます。 DNS サーバーとして使用するサーバーの IP アドレスを入力します。 指定した DNS サーバー アドレスは、このネットワーク インターフェイスにのみ割り当てられて、ネットワーク インターフェイス割り当て先の仮想ネットワークの DNS 設定をすべてオーバーライドします。
6. [ **Save**] をクリックします。

**コマンド**

|ツール|コマンド|
|---|---|
|CLI|[az network nic update](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmNetworkInterface](/powershell/module/azurerm.network/set-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="enable-or-disable-ip-forwarding"></a>IP 転送の有効化と無効化

IP 転送によって、ネットワーク インターフェイスのアタッチ先の仮想マシンを有効にします。
- これにより宛先が、ネットワーク インターフェイスに割り当てられたいずれかの IP 構成に割り当てられている IP アドレスの 1 つではないネットワーク トラフィックを受信します。
- ネットワーク トラフィックの送信は、ネットワーク インターフェイスの IP 構成のいずれかに割り当てられているものとは異なる送信元 IP アドレスを使用して行います。

転送する必要があるトラフィックを受信する仮想マシンにアタッチされているネットワーク インターフェイスすべてについて、設定を有効にする必要があります。 仮想マシンは、複数のネットワーク インターフェイスがあるか、それにアタッチされているのが 1 つのネットワーク インターフェイスであるかを問わず、トラフィックを転送できます。 IP 転送は Azure の設定ですが、ファイアウォール、WAN 最適化、負荷分散アプリケーションなど、仮想マシンでも、トラフィックを転送できるアプリケーションを実行する必要があります。 ネットワーク アプリケーションを実行する仮想マシンは、ネットワーク仮想アプライアンスと呼ばれることがよくあります。 すぐにデプロイできるネットワーク仮想アプライアンスの一覧については、[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances) で参照できます。 通常、IP 転送はユーザー定義ルートで使用されます。 ユーザー定義ルートについて詳しくは、[ユーザー定義のルート](virtual-networks-udr-overview.md)に関する記事をご覧ください。

1. ご利用のサブスクリプションのネットワーク作成協力者ロール (またはそれ以上) のアクセス許可が割り当てられているアカウントで [Azure Portal](https://portal.azure.com) にログインします。 アカウントへのロールとアクセス許可の割り当てについて詳しくは、「[Azure のロールベースのアクセス制御のための組み込みロール](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)」の記事をご覧ください。
2. Azure Portal 上部に "*リソースの検索*" というテキストが表示されたボックスがあります。そこに "*ネットワーク インターフェイス*" と入力します。 検索結果に **[ネットワーク インターフェイス]** が表示されたら、それをクリックします。
3. 表示される **[ネットワーク インターフェイス]** ブレードで、IP 転送を有効または無効にするネットワーク インターフェイスをクリックします。
4. 選択したネットワーク インターフェイスのブレードで、**[設定]** セクションの **[IP 構成]** をクリックします。
5. **[有効]** または **[無効]** (既定の設定) をクリックして、設定を変更します。
6. [ **Save**] をクリックします。

**コマンド**

|ツール|コマンド|
|---|---|
|CLI|[az network nic update](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmNetworkInterface](/powershell/module/azurerm.network/set-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="change-subnet-assignment"></a>サブネットの割り当ての変更

ネットワーク インターフェイスの割り当て先は、仮想ネットワークの場合は変更できませんが、サブネットであれば変更できます。

1. ご利用のサブスクリプションのネットワーク作成協力者ロール (またはそれ以上) のアクセス許可が割り当てられているアカウントで [Azure Portal](https://portal.azure.com) にログインします。 アカウントへのロールとアクセス許可の割り当てについて詳しくは、「[Azure のロールベースのアクセス制御のための組み込みロール](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)」の記事をご覧ください。
2. Azure Portal 上部に "*リソースの検索*" というテキストが表示されたボックスがあります。そこに "*ネットワーク インターフェイス*" と入力します。 検索結果に **[ネットワーク インターフェイス]** が表示されたら、それをクリックします。
3. 表示される **[ネットワーク インターフェイス]** ブレードで、設定を表示または変更するネットワーク インターフェイスをクリックします。
4. 選択したネットワーク インターフェイスのブレードの **[設定]** で、**[IP 構成]** をクリックします。 一覧表示された IP 構成のプライベート IP アドレスの中に、横に **[(静的)]** と表示されているものがある場合は、次の手順を実行して、IP アドレスの割り当て方法を "動的" に変更する必要があります。 ネットワーク インターフェイスのサブネット割り当てを変更するには、すべてのプライベート IP アドレスを動的な方法で割り当てる必要があります。 アドレスが動的な方法で割り当てられている場合は、手順 5. に進みます。 静的な割り当て方法で割り当てられている IPv4 アドレスがある場合は、次の手順を実行して、割り当て方法を "動的" な方法に変更します。
    - IP 構成の一覧で、IPv4 アドレスの割り当て方法を変更する IP 構成をクリックします。
    - IP 構成に対して表示されるブレードで、**[割り当て]** 方法で **[動的]** をクリックします。 静的な割り当て方法を使用して IPv6 アドレスを割り当てることはできません。
    - [ **Save**] をクリックします。
5. ネットワーク インターフェイスの接続先にするサブネットを、**[サブネット]** ドロップダウン リストから選択します。
6. [ **Save**] をクリックします。 新しい動的アドレスが、新しいサブネットのサブネット アドレス範囲から割り当てられます。 ネットワーク インターフェイスを新しいサブネットに割り当てたら、必要に応じて、新しいサブネット アドレス範囲から静的 IPv4 アドレスを割り当てることができます。 ネットワーク インターフェイスの IP アドレスの追加、変更、削除について詳しくは、[IP アドレスの管理](virtual-network-network-interface-addresses.md)に関する記事を参照してください。

**コマンド**

|ツール|コマンド|
|---|---|
|CLI|[az network nic ip-config update](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmNetworkInterfaceIpConfig](/powershell/module/azurerm.network/set-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="delete-a-network-interface"></a>ネットワーク インターフェイスの削除

ネットワーク インターフェイスは、仮想マシンにアタッチされていなければ削除できます。 仮想マシンにアタッチされている場合は、まず仮想マシンを停止 (割り当て解除) 状態にして、次に、仮想マシンからネットワーク インターフェイスをデタッチします。その後で、ネットワーク インターフェイスを削除できます。 仮想マシンからネットワーク インターフェイスをデタッチするには、「[ネットワーク インターフェイスの追加または削除](virtual-network-network-interface-vm.md)」の記事の、[仮想マシンからのネットワーク インターフェイスのデタッチ](virtual-network-network-interface-vm.md#vm-remove-nic)に関するセクションの手順を実行します。 仮想マシンを削除すると、それにアタッチされているすべてのネットワーク インターフェイスがデタッチされますが、ネットワーク インターフェイスは削除されません。

1. ご利用のサブスクリプションのネットワーク作成協力者ロール (またはそれ以上) のアクセス許可が割り当てられているアカウントで [Azure Portal](https://portal.azure.com) にログインします。 アカウントへのロールとアクセス許可の割り当てについて詳しくは、「[Azure のロールベースのアクセス制御のための組み込みロール](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)」の記事をご覧ください。
2. Azure Portal 上部に "*リソースの検索*" というテキストが表示されたボックスがあります。そこに "*ネットワーク インターフェイス*" と入力します。 検索結果に **[ネットワーク インターフェイス]** が表示されたら、それをクリックします。
3. 削除するネットワーク インターフェイスを右クリックし、**[削除]** をクリックします。
4. **[はい]** をクリックしてネットワーク インターフェイスの削除を確認します。

ネットワーク インターフェイスを削除すると、それに割り当てられていたすべての MAC アドレスや IP アドレスが解放されます。

**コマンド**

|ツール|コマンド|
|---|---|
|CLI|[az network nic delete](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|PowerShell|[Remove-AzureRmNetworkInterface](/powershell/module/azurerm.network/remove-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="next-steps"></a>次のステップ
複数のネットワーク インターフェイスまたは IP アドレスを持つ仮想マシンを作成するには、以下の記事を参照してください。

**コマンド**

|タスク|ツール|
|---|---|
|複数 NIC を持つ VM の作成|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)、[PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|複数の IPv4 アドレスが割り当てられた 1 つの NIC VM の作成|[CLI](virtual-network-multiple-ip-addresses-cli.md)、[PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|プライベート IPv6 アドレスが割り当てられた 1 つの NIC VM の作成 (Azure Load Balancer の背後)|[CLI](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json)、[PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)、[Azure Resource Manager テンプレート](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
