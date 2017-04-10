---
title: "Azure ネットワーク インターフェイス | Microsoft Docs"
description: "ネットワーク インターフェイス (NIC) を作成する方法および削除する方法、またパブリックおよびプライベート IP アドレスを NIC に割り当てる方法を説明します。 Azure Virtual Machines に対する NIC のアタッチとデタッチの方法を説明します。"
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
ms.date: 03/14/2017
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: f691f3886fce217ea784237f03a4f02ed58e12ee
ms.lasthandoff: 03/28/2017


---

# <a name="network-interfaces"></a>ネットワーク インターフェイス

ネットワーク インターフェイス (NIC) とその使用方法について説明します。 NIC とは、Azure Virtual Machine (VM) と基礎となるソフトウェア ネットワーク間の相互接続です。 次の図は、NIC で提供される機能を示しています。

![ネットワーク インターフェイス](./media/virtual-network-network-interface/nic.png)

この記事では、この図に示した概念を使用する方法について説明します。 次の概念のうちいずれかをクリックすると、記事の中でそのセクションに直接移動できます。

- [ネットワーク インターフェイス](#nics): NIC は Azure Virtual Network (VNet) 内の 1 つのサブネットに接続されています。 図では、**VM1** には 2 つの NIC がアタッチされ、**VM2** には 1 つの NIC がアタッチされています。 各 NIC は、同じ VNet に接続されていますがサブネットは異なります。 このセクションでは、既存の NIC の一覧表示、および NIC の作成、変更、削除を行う手順について説明します。
- [IP 構成](#ip-configs): 1 つ以上の IP 構成が各 NIC に関連付けられています。 各 IP 構成には、プライベート IP アドレスが割り当てられています。 IP 構成には、パブリック IP アドレスが割り当てられている場合があります。 図では、**NIC1** と **NIC3** には、いずれも 1 つの IP 構成が関連付けられ、**NIC2** には 2 つの IP 構成が関連付けられています。 NIC1 と NIC3 に割り当てられた IP 構成にはパブリック IP アドレスが割り当てられていますが、NIC2 に割り当てられた IP 構成はどちらにもパブリック IP アドレスが割り当てられていません。 このセクションでは、静的および動的な割り当て方法によってプライベート IP アドレスが割り当てられた IP 構成の作成、変更、削除を行う手順を説明します。 また、IP 構成に対するパブリック IP アドレスの関連付けや関連付け解除の手順も説明します。
- [ネットワーク セキュリティ グループ](#nsgs): ネットワーク セキュリティ グループ (NSG) には、1 つまたは複数の受信または送信セキュリティ規則が含まれています。 これらの規則は、ネットワーク インターフェイス、サブネット、または両方から送受信できるネットワーク トラフィックのタイプを制御します。 図では、**NIC1** と **NIC3** には NSG が関連付けられていますが、**NIC2** には関連付けられていません。 このセクションでは、NIC に適用された NSG の表示、NIC への NSG の追加、NIC からの NSG の削除を行う手順を説明します。
- [仮想マシン](#vms): VM には少なくとも 1 つの NIC がアタッチされます。VM のサイズによっては複数の NIC をアタッチできます。 各サイズの VM でサポートされる NIC の数を確認するには、[Windows](../virtual-machines/virtual-machines-windows-sizes.md) と [Linux](../virtual-machines/virtual-machines-linux-sizes.md) の VM のサイズに関する記事をご覧ください。 このセクションでは、単一または複数の NIC の VM を作成する手順、既存の VM に対して NIC のアタッチおよびデタッチする手順を説明します。

Azure で NIC と VM を初めて使用する場合は、この記事を読む前に、[初めての Azure Virtual Network の作成](virtual-network-get-started-vnet-subnet.md)に関する記事で演習を行うことをお勧めします。 この演習は VNet と VM を理解することに役立ちます。

この記事は、Azure Resource Manager デプロイメント モデルを通じて作成された VM と NIC を対象としています。 クラシック デプロイメント モデルではなく、Resource Manager デプロイメント モデルを使用してリソースを作成することをお勧めします。 2 つのモデルの違いについてご不明な点がある場合は、[Azure デプロイメント モデルについて](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)の記事をご覧ください。

以降、この記事の各セクションで、NIC に関連したさまざまなタスクの手順を紹介します。 各セクションの内容は次のとおりです。
- Azure Portal 内でタスクを実行する手順。 これらの手順を行うには、[Azure Portal](http://portal.azure.com) にログインする必要があります。 まだアカウントを持っていない場合は、[無料試用版アカウント](https://azure.microsoft.com/free)にサインアップしてください。
- Azure PowerShell を使用してタスクを実行するためのコマンドとそのコマンド リファレンスへのリンク。 [Azure PowerShell のインストールと構成方法](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事の手順に従い、PowerShell をインストールして構成してください。 PowerShell コマンドのヘルプとサンプルを表示するには、「`get-help <command> -full`」と入力します。
- Azure コマンド ライン インターフェイス (CLI) を使用してタスクを実行するためのコマンドとそのコマンド リファレンスへのリンク。 [Azure CLI 2.0 のインストールと構成の方法](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事の手順に従って、Azure CLI をインストールしてください。 CLI コマンドのヘルプを表示するには、「`az <command> -h`」と入力します。

## <a name="nics"></a>ネットワーク インターフェイス
この後のセクションの手順を実行して、ネットワーク インターフェイスと設定の作成、表示、変更、削除を行います。

### <a name="create-nic"></a>ネットワーク インターフェイスの作成

NIC は VM にアタッチされるか独自に存在します。 NIC を VM にアタッチする方法については、この記事の[仮想マシンへの NIC のアタッチ](#vm-attach-nic)に関するセクションをご覧ください。

NIC を作成するには、次の手順を実行します。

1. ご利用のサブスクリプションのネットワーク作成協力者ロール (またはそれ以上) のアクセス許可が割り当てられているアカウントで [Azure Portal](https://portal.azure.com) にログインします。 アカウントへのロールとアクセス許可の割り当てについて詳しくは、「[Azure のロールベースのアクセス制御のための組み込みロール](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)」の記事をご覧ください。
2. Azure Portal 上部に "*リソースの検索*" というテキストが表示されたボックスがあります。そこに "*ネットワーク インターフェイス*" と入力します。 検索結果に **[ネットワーク インターフェイス]** が表示されたら、それをクリックします。
3. **[ネットワーク インターフェイス]** ブレードが表示されたら、**[+ 追加]** をクリックします。
4. 表示された **[ネットワーク インターフェイスの作成]** ブレードで以下の設定の値を入力するか選択し、**[作成]** をクリックします。

    |**設定**|**必須**|**詳細**|
    |---|---|---|
    |**名前**|はい|名前は、NIC の作成後に変更することはできません。 選択したリソース グループ内で一意となる名前を使用してください。 名前の付け方については、[名前付け規則](/azure/architecture/best-practices/naming-conventions?toc=%2fazure%2fvirtual-network%2ftoc.json#naming-rules-and-restrictions)に関する記事をご覧ください。|
    |**Virtual Network**|はい|NIC を接続できるのは、NIC と同じサブスクリプションと場所に存在する VNet のみです。 NIC のアタッチ先の VM も、NIC と同じ場所とサブスクリプションに存在する必要があります。 VNet が表示されない場合は、1 つ作成する必要があります。 VNet を作成するには、[仮想ネットワーク](virtual-networks-create-vnet-arm-pportal.md)に関する記事の手順を完了します。 NIC が作成された後で、接続先の VNet を変更することはできません。|
    |**サブネット**|はい|選択した VNet 内のサブネットを選択します。 NIC の接続先のサブネットは、作成した後で変更できます。|
    |**プライベート IP アドレスの割り当て**|はい| プライベート IP アドレスは、NIC の作成時に Azure DHCP サーバーによって NIC に割り当てられます。 DHCP サーバーは、NIC の接続先のサブネットで定義されているサブネット アドレスの範囲から、使用可能なアドレスを割り当てます。 **動的:** NIC のアタッチ先の VM が停止 (割り当て解除) 状態から起動されたときに、Azure が異なるアドレスを NIC に割り当てることができます。 停止 (割り当て解除) 状態にならずに VM が再起動された場合、アドレスは変わりません。 **静的:** 静的アドレスは、ユーザーが変更するか NIC が削除されるまで変わりません。 NIC の作成後に、割り当て方法を変更することができます。|
    |**ネットワーク セキュリティ グループ**|いいえ|ネットワーク セキュリティ グループを使用して、NIC から送受信されるネットワーク トラフィック フローを制御できます。 NSG について詳しくは、[ネットワーク セキュリティ グループ](virtual-networks-nsg.md)に関する記事をご覧ください。 NIC に 0 個または 1 個のネットワーク セキュリティ グループ (NSG) を適用することができます。 0 個または 1 個の NSG は、その NIC の接続先の NIC にも適用できます。 NSG が NIC と接続先のサブネットに適用されるときに、予期しない結果が発生することがあります。 NIC に適用された NSG をトラブルシューティングするには、[NSG のトラブルシューティング](virtual-network-nsg-troubleshoot-portal.md#view-effective-security-rules-for-a-network-interface)に関する記事をご覧ください。|
    |**サブスクリプション**|はい| NIC のアタッチ先の VM と接続先の Vnet は、同じサブスクリプションに存在していることが必要です。|
    |**[リソース グループ]**|はい| NIC は、アタッチ先の VM や接続先の Vnet と、同じリソース グループに存在することも、違うリソース グループに存在することも可能です。|
    |**場所**|はい|NIC のアタッチ先の VM と接続先の Vnet は、同じ場所に存在していることが必要です。|

Azure Portal によって、動的プライベート IP アドレスを使った **ipconfig1** という名前のプライマリ IP 構成が作成され、ユーザーが作成した NIC に関連付けられます。 IP 構成について詳しくは、この記事の「[IP 構成](#ip-configs)」セクションをご覧ください。 NIC を作成するとき、ポータルによって作成される IP 構成の名前の指定、静的なプライベート IP アドレスの割り当て、またはパブリック IP アドレスの割り当てを行うことはできません。 PowerShell または CLI を使用して NIC を作成する場合は、IP 構成名の指定および静的 IP アドレスやパブリック IP アドレスの割り当てを行うことができます。 プライベート IP アドレスを割り当てる方法や、NIC の作成後にパブリック IP アドレスを NIC に関連付けるかどうかを変更することはできます。 NIC の作成後に設定を変更するには、この記事の「[IP 構成の変更](#change-ip-config)」セクションの手順を実行します。

>[!Note]
> Azure によって MAC アドレスが NIC に割り当てられるのは、NIC が VM にアタッチされ、VM が最初に起動した後です。 Azure が NIC に割り当てる MAC アドレスは指定できません。 MAC アドレスが NIC に割り当てられると、NIC が削除されるか、プライマリ NIC のプライマリ IP 構成に割り当てられたプライベート IP アドレスが変更されるまで、その状態は変わりません。 IP 構成について詳しくは、この記事の「[IP 構成](#ip-configs)」セクションをご覧ください。

|**ツール**|**コマンド**|
|:---|:---|
|**CLI**|[az network nic create](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|**PowerShell**|[New-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.4.0/new-azurermnetworkinterface/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|

### <a name="view-nics"></a>ネットワーク インターフェイスと設定の表示および変更

ネットワーク インターフェイスと設定を表示および変更するには、次の手順を実行します。

1. ご利用のサブスクリプションのネットワーク作成協力者ロール (またはそれ以上) のアクセス許可が割り当てられているアカウントで [Azure Portal](https://portal.azure.com) にログインします。 アカウントへのロールとアクセス許可の割り当てについて詳しくは、「[Azure のロールベースのアクセス制御のための組み込みロール](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)」の記事をご覧ください。
2. Azure Portal 上部に "*リソースの検索*" というテキストが表示されたボックスがあります。そこに "*ネットワーク インターフェイス*" と入力します。 検索結果に **[ネットワーク インターフェイス]** が表示されたら、それをクリックします。
3. 表示される **[ネットワーク インターフェイス]** ブレードで、設定を表示または変更する NIC をクリックします。
4. 次の設定が、選択した NIC に対して表示されるブレードに表示されます。
    - **概要:** 割り当てられている IP アドレス、NIC の接続先の VNet/サブネット、NIC のアタッチ先の VM (アタッチされている場合) など、NIC に関する情報を表示します。次の図は、**mywebserver256** という名前の NIC の概要設定です。  ![ネットワーク インターフェイスの概要](./media/virtual-network-network-interface/nic-overview.png)
    - **IP 構成:** NIC には少なくとも 1 つの IP 構成が割り当てられます。複数の IP 構成を割り当てることもできます。 NIC でサポートされる IP 構成の最大数については、[Azure の制限](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)に関する記事をご覧ください。 各 IP 構成には、1 つのプライベート IP アドレスが割り当てられます。また、1 つのパブリック IP アドレスを関連付けることができます。 表示される内容を変更するには、この記事の「[NIC へのセカンダリ IP 構成の追加](#create-ip-config)」セクション、「[IP 構成の変更](#change-ip-config)」セクション、または [IP 構成の削除](#delete-ip-config)に関するセクションの手順を実行します。
    - **DNS サーバー:** Azure DHCP サーバーが NIC に割り当てる DNS サーバーを指定できます。 Azure 内部の DNS サーバーかカスタム DNS サーバーを選択します。 表示される内容を変更するには、この記事の「[NIC の DNS 設定の変更](#dns)」セクションの手順を実行します。
    - **ネットワーク セキュリティ グループ (NSG):** NSG が NIC に関連付けられているかどうかが表示されます。 NSG が NIC に関連付けられている場合は、関連付けられている NSG の名前が表示されます。 表示される内容を変更するには、この記事の「[NSG とネットワーク インターフェイスの関連付けまたは関連付け解除](#associate-nsg)」セクションの手順を実行します。
    - **プロパティ:** NIC のキー設定が表示されます。MAC アドレスや NIC が存在するサブスクリプションを含めることができます。 NIC は、それに関連するすべてのリソースも移動できる場合のみ、別のリソース グループまたはサブスクリプションに移動できます。 たとえば、NIC が VM にアタッチされている場合は、VM と VM に関連するすべてのリソースも移動する必要があります。 NIC を移動するには、「[新しいリソース グループまたはサブスクリプションへのリソースの移動](../azure-resource-manager/resource-group-move-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json#use-portal)」の記事をご覧ください。 ここには、前提条件や、Azure Portal、PowerShell、および Azure CLI を使用してリソースを移動する方法が記載されています。
    - **有効なセキュリティ規則:** セキュリティ規則が一覧表示されるのは、NIC が実行中の VM にアタッチされ、NSG が NIC または NIC の接続先サブネット (あるいは両方) に関連付けられている場合です。 表示される内容について詳しくは、[ネットワーク セキュリティ グループのトラブルシューティング](virtual-network-nsg-troubleshoot-portal.md#view-effective-security-rules-for-a-network-interface)に関する記事をご覧ください。 NSG について詳しくは、[ネットワーク セキュリティ グループ](virtual-networks-nsg.md)に関する記事をご覧ください。
    - **有効なルート:** ルートが一覧表示されるのは、NIC が実行中の VM にアタッチされている場合です。 ルートは、Azure 既定ルート、すべてのユーザー定義ルート、NIC の接続先サブネットに存在する可能性がある BGP ルートが組み合わされたものです。 表示される内容について詳しくは、[ルートのトラブルシューティング](virtual-network-routes-troubleshoot-portal.md#view-effective-routes-for-a-network-interface)に関する記事をご覧ください。 ユーザー定義ルートについて詳しくは、[ユーザー定義のルート](virtual-networks-udr-overview.md)に関する記事をご覧ください。
    - **Azure Resource Manager の一般的な設定:** Azure Resource Manager の一般的な設定については、[アクティビティ ログ](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs)、[Access Control](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control)、[タグ](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#tags)[ロック](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)、および [Automation スクリプト](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group) に関する各記事をご覧ください。

|**ツール**|**コマンド**|
|---|---|
|**CLI**|[az network nic list](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#list) を使用して、サブスクリプションの NIC を表示します。[az network nic show](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#show) を使用して、NIC の設定を表示します。|
|**PowerShell**|[Get-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.4.0/get-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) を使用して、サブスクリプションの NIC または NIC の設定を表示します。|

### <a name="dns"></a>NIC の DNS 設定の変更

NIC の DNS 設定を変更するには、次の手順を実行します。 DNS サーバーは、Azure DHCP サーバーによって VM に割り当てられます。 NIC の名前解決について詳しくは、[VM の名前解決](virtual-networks-name-resolution-for-vms-and-role-instances.md)に関する記事をご覧ください。

1. 設定を変更する NIC について、この記事の「[ネットワーク インターフェイスと設定の表示および変更](#view-nics)」セクションの手順 1 - 3 を実行します。
2. 選択した NIC のブレードで **[DNS サーバー]** をクリックします。
3. 次のいずれかをクリックします。
    - **[仮想ネットワークから継承する] (既定)**: このオプションを選択すると、NIC の接続先仮想ネットワークで定義された DNS サーバー設定を継承します。 VNet レベルでは、カスタム DNS サーバーまたは Azure 提供の DNS サーバーが定義されます。 Azure 提供の DNS サーバーは、同じ VNet に接続しているリソースの名前を解決できますが、異なる VNet に接続しているリソースの名前は解決できません。
    - **[カスタム]**: 独自の DNS サーバーを構成して、複数の VNet で名前を解決できます。 DNS サーバーとして使用するサーバーの IP アドレスを入力します。 指定する DNS サーバー アドレスはこの NIC のみに割り当てられ、NIC が接続されている Vnet のすべての DNS 設定を上書きします。
4. [ **Save**] をクリックします。

|**ツール**|**コマンド**|
|---|---|
|**CLI**|[az network nic update](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|**PowerShell**|[Set-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

### <a name="ip-forwarding"></a>NIC の IP 転送の変更

IP 転送を使用すると、NIC がアタッチされている VM で次が可能になります。
- NIC に割り当てられた IP 構成のいずれかに割り当てられた IP アドレスの 1 つが宛先として指定されていなくても、ネットワーク トラフィックを受信できます。
- IP 構成のいずれかに割り当てられた IP アドレスとは異なる送信元 IP アドレスを使用してネットワーク トラフィックを送信できます。

トラフィックを受け取る VM と転送を行う必要がある VM にアタッチされているすべての NIC で、設定を有効にする必要があります。 VM は、アタッチされている NIC が 1 つでも複数でも、トラフィックを転送できます。 IP 転送は Azure の設定ですが、ファイアウォール、WAN 最適化、負荷分散アプリケーションなど、トラフィックを転送できるアプリケーションを VM が実行する必要があります。 ネットワーク アプリケーションを実行する VM は、しばしばネットワーク仮想アプライアンス (NVA) と呼ばれます。 すぐにデプロイできる NVA の一覧については、[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances) をご覧ください。 通常、IP 転送はユーザー定義ルートで使用されます。 ユーザー定義ルートについて詳しくは、[ユーザー定義のルート](virtual-networks-udr-overview.md)に関する記事をご覧ください。

NIC の IP 転送設定を変更するには、次の手順を実行します。

1. 変更する NIC について、この記事の「[ネットワーク インターフェイスと設定の表示および変更](#view-nics)」セクションの手順 1 - 3 を実行します。
2. 選択した NIC のブレードで [IP 構成] をクリックします。
3. **[有効]** または **[無効]** (既定の設定) をクリックして、設定を変更します。
4. **[Save]**をクリックします。

|**ツール**|**コマンド**|
|---|---|
|**CLI**|[az network nic update](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|**PowerShell**|[Set-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

### <a name="subnet"></a>NIC の接続先サブネットの変更

NIC の接続先のサブネットは変更できますが、Vnet は変更できません。 サブネットを変更する場合、NIC に関連付けられているすべての IP 構成に、動的なプライベート IP アドレスが割り当てられていることが必要です。 NIC が接続されているサブネットを変更するには、次の手順を実行します。

1. 別のサブネットに接続する NIC について、この記事の「[ネットワーク インターフェイスと設定の表示および変更](#view-nics)」セクションの手順 1 - 3 を実行します。
2. 選択した NIC のブレードで **[IP 構成]** をクリックします。 一覧表示された IP 構成のプライベート IP アドレスのいずれかが、静的な方法で割り当てられたプライベート IP アドレスである場合は、次の手順を実行して、動的な方法に変更する必要があります。 アドレスが動的な方法で割り当てられている場合は、手順 3 に進みます。
    - IP 構成の一覧で、変更しようとしている IP 構成の静的 IP アドレスをクリックします。
    - IP 構成に対して表示されるブレードで、**[割り当て]** 方法で **[動的]** をクリックします。
    - **[Save]**をクリックします。
3. NIC を接続するサブネットを、**[サブネット]** ドロップダウン リストで選択します。
4. **[Save]**をクリックします。 新しい動的アドレスが、新しいサブネット アドレスの範囲から割り当てられます。 選択する場合は、新しいサブネット アドレスの範囲から静的 IP アドレスを割り当てることができます。

|**ツール**|**コマンド**|
|---|---|
|**CLI**|[az network nic ip-config update](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|**PowerShell**|[Set-AzureRmNetworkInterfaceIpConfig](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|


### <a name="delete-nic"></a>ネットワーク インターフェイスの削除

NIC を削除できるのは VM にアタッチされていない場合です。 VM にアタッチされている場合は、削除する前にまず VM からデタッチする必要があります。 NIC を VM からデタッチするには、この記事の[仮想マシンからの NIC のデタッチ](#vm-detach-nic)に関するセクションの手順を実行します。

1. 削除する NIC について、この記事の「[ネットワーク インターフェイスと設定の表示および変更](#view-nics)」セクションの手順 1 - 2 を実行します。
2. 削除する NIC を右クリックし、**[削除]** をクリックします。
3. **[はい]** をクリックして、NIC の削除を確認します。

NIC を削除すると、割り当てられていた MAC アドレスまたは IP アドレスは解放されます。

|**ツール**|**コマンド**|
|---|---|
|**CLI**|[az network nic delete](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|**PowerShell**|[Remove-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.1.0/remove-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="ip-configs"></a>IP 構成
各 NIC には少なくとも 1 つの IP 構成があり、これは**プライマリ**構成と呼ばれます。 NIC に、1 つ以上の*セカンダリ* IP 構成を関連付けることもできます。 NIC に割り当てられる IP アドレスの数には制限があります。 詳しくは、[Azure の制限](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)に関する記事をご覧ください。 各 IP 構成の内容は次のとおりです。
- 静的または動的の割り当て方法を使用して、1 つのプライベート IP アドレスが割り当てられます。 動的 IP アドレスは、VM が停止 (割り当て解除) 状態になってから再起動した場合に変化することがあります。 静的 IP アドレスが NIC から解放されるのは、NIC が削除される場合のみです。
- 1 つのパブリック IP アドレスを関連付けられます。

Azure DHCP サーバーは、NIC のプライマリ IP 構成のプライベート IP アドレスを、VM オペレーティング システム内の NIC に割り当てます。

NIC に複数の IP アドレスを割り当てると、次のようなシナリオで役立ちます。
- 異なる IP アドレスと SSL 証明書を持つ複数のウェブサイトやサービスを、1つのサーバーでホストする。
- VM がファイアウォールやロード バランサーのような、ネットワーク仮想アプライアンスとして機能する。
- 複数の NIC いずれかの複数のプライベート IP アドレスいずれかを Azure Load Balancer のバックエンド プールに追加する。 以前は、プライマリ NIC のプライマリ IP アドレスのみをバックエンド プールに追加できました。 複数の IP 構成の負荷分散方法の詳細については、[複数の IP 構成の負荷分散](../load-balancer/load-balancer-multiple-ip.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事を参照してください。

サブスクリプション内で使用できるパブリック IP アドレスと、NIC に割り当てることができるプライベート IP アドレスの数は制限されています。 これらの制限について詳しくは、[Azure の制限](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)に関する記事をご覧ください。

### <a name="create-ip-config"></a>NIC へのセカンダリ IP 構成の追加

[Azure の制限](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)に関する記事に示されている制限内であれば、必要に応じて任意の数の IP 構成を NIC に追加できます。 NIC に IP 構成を追加するには、次の手順を実行します。

1. IP 構成を追加する NIC について、この記事の[ネットワーク インターフェイス設定の表示](#view-nics)に関するセクションの手順 1 - 3 を実行します。
2. 選択した NIC のブレードで **[IP 構成]** をクリックします。
3. IP 構成に対して開くブレードで、**[+ 追加]** をクリックします。
4. 以下の項目を指定してから **[OK]** をクリックして、**[IP 構成の追加]** ブレードを閉じます。

    |**設定**|**必須**|**詳細**|
    |---|---|---|
    |**名前**|はい|NIC に対して一意である必要があります。|
    |**型**|はい|IP 構成を既存の NIC に追加しており、各 NIC には必ずプライマリ IP 構成があるため、選択できるオプションは **[セカンダリ]** のみです。|
    |**[プライベート IP アドレスの割り当て]**|はい|**[動的]** アドレスは、VM が停止 (割り当て解除) 状態になってから再起動した場合に変化することがあります。 **[静的]** アドレスは、NIC が削除されるまで解放されません。 別の IP 構成で現在使用されていないサブネット アドレス空間の範囲から IP アドレスを指定します。|
    |**パブリック IP アドレス**|いいえ|**[無効]:** パブリック IP アドレス リソースは現在 IP 構成に関連付けられていません。 **[有効]:** 既存のパブリック IP アドレスを選択するか、新しいパブリック IP アドレスを作成します。 パブリック IP アドレスの作成方法については、「[Public IP addresses](virtual-network-public-ip-address.md#create)」(パブリック IP アドレス) の記事をご覧ください。|
5. セカンダリ プライベート IP アドレスを VM オペレーティング システムに手動で追加します。これは、[仮想マシンへの複数 IP アドレスの割り当て](virtual-network-multiple-ip-addresses-portal.md#os-config)に関する記事の手順を実行して行います。 VM オペレーティング システムにパブリック IP アドレスは追加しないでください。

|**ツール**|**コマンド**|
|---|---|
|**CLI**|[az network nic ip-config create](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|**PowerShell**|[Add-AzureRmNetworkInterfaceIpConfig](/powershell/resourcemanager/azurerm.network/v3.4.0/add-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

### <a name="change-ip-config"></a>IP 構成の変更

任意のプライマリまたはセカンダリ IP 構成でプライベート IP アドレスやパブリック IP アドレスの設定を変更するには、次の手順を実行します。

1. 変更する NIC について、この記事の[ネットワーク インターフェイス設定の表示](#view-nics)に関するセクションの手順 1 - 3 を実行します。
2. 選択した NIC のブレードで **[IP 構成]** をクリックします。
3. IP 構成に対して開いたブレードで、変更する IP 構成を一覧からクリックします。
4. この記事の [IP 構成の追加](#create-ip-config)に関するセクションの設定の情報を使用し、必要に応じて設定を変更します。その後、**[保存]** をクリックして、選択した IP 構成のブレードを閉じます。

>[!NOTE]
>プライマリ NIC に複数の IP 構成があるときに、プライマリ IP 構成のプライベート IP アドレスを変更すると、Windows ですべてのセカンダリ IP アドレスを NIC に手動で再割り当てする必要があります (Linux では必要ありません)。 オペレーティング システム内で IP アドレスを NIC に手動で割り当てるには、[仮想マシンへの複数 IP アドレスの割り当て](virtual-network-multiple-ip-addresses-portal.md#os-config)に関する記事をご覧ください。 VM オペレーティング システムにパブリック IP アドレスは追加しないでください。

|**ツール**|**コマンド**|
|---|---|
|**CLI**|[az network nic ip-config update](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|**PowerShell**|[Set-AzureRMNetworkInterfaceIpConfig](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

### <a name="delete-ip-config"></a>NIC からのセカンダリ IP 構成の削除

NIC からセカンダリ IP 構成を削除するには、次の手順を実行します。

1. 変更する NIC について、この記事の[ネットワーク インターフェイス設定の表示](#view-nics)に関するセクションの手順 1 - 3 を実行します。
2. 選択した NIC のブレードで **[IP 構成]** をクリックします。
3. 削除するセカンダリ IP 構成を右クリックし、**[削除]** をクリックします。 構成にパブリック IP アドレスが関連付けられていた場合、リソースの関連付けが IP 構成から解除されますが、リソースは削除されません。
4. **[IP 構成]** ブレードを閉じます。

|**ツール**|**コマンド**|
|---|---|
|**CLI**|[az network nic ip-config delete](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|**PowerShell**|[Remove-AzureRmNetworkInterfaceIpConfig](/powershell/resourcemanager/azurerm.network/v3.4.0/remove-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="nsgs"></a>ネットワーク セキュリティ グループ
ネットワーク セキュリティ グループ (NSG) には、NIC に対するネットワーク トラフィックを許可または拒否する受信と送信の規則が含まれます。 NIC や NIC が接続されているサブネットに、NSG を関連付ける必要はありません。 ただし、NSG は、NIC または NIC が接続されているサブネット (あるいは両方) に関連付けることができます。 サブネットに接続しているすべての NIC に適用できる規則を含む NSG は、通常はサブネットに関連付けられます。 きめ細かい規則が含まれる NSG は個々の NIC への適用が適しています。 ネットワーク セキュリティ グループについて詳しくは、[ネットワーク セキュリティ グループ](virtual-networks-nsg.md)に関する記事をご覧ください。

### <a name="associate-nsg"></a>NSG とネットワーク インターフェイスの関連付けまたは関連付け解除

NSG を NIC に関連付けたり、NSG と NIC の関連付けを解除するには、次の手順を実行します。

1. NSG との関連付けや関連付け解除を行う NIC について、この記事の「[ネットワーク インターフェイスと設定の表示および変更](#view-nics)」セクションの手順 1 - 3 を実行します。
2. 選択した NIC のブレードで **[ネットワーク セキュリティ グループ]** をクリックします。 **[編集]** が一番上にあるブレードが表示されます。 現在 NIC に関連付けられている NSG がない場合は、**[ネットワーク セキュリティ グループ]** に "*なし*" が表示されます。 現在 NSG が NIC に関連付けられている場合は、**[ネットワーク セキュリティ グループ]** に <*NSG-Name*> (NSG-Name は NIC に現在関連付けられている NSG の名前) が表示されます。
3. **[編集]**をクリックします。
4. **[ネットワーク セキュリティ グループ]** をクリックします。 ネットワーク セキュリティ グループがサブスクリプションに存在していないときは一覧表示されません。 NSG を作成するには、[ネットワーク セキュリティ グループ](virtual-networks-create-nsg-arm-pportal.md)に関連する記事の手順を実行します。
5. 表示される **[ネットワーク セキュリティ グループの選択]** ブレードで、一覧表示内の既存の NSG をクリックして、その NSG を NIC に関連付けます。または、**[なし]** をクリックして、NIC に現在関連付けられている NSG を解除します。
6. **[Save]**をクリックします。

|**ツール**|**コマンド**|
|---|---|
|**CLI**|[az network nic update](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|**PowerShell**|[Set-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vms"></a>仮想ネットワークへの NIC のアタッチとデタッチ

VM を作成して既存の NIC をその VM にアタッチできます。または、既存の NIC を既存の VM にアタッチできます。 既存の VM に 2 つ以上の NIC がアタッチされているときは、1 つの NIC をデタッチすることもできます。 ユーザーが VM を作成するとポータルによって NIC が作成されますが、このとき以下を行うことはできません。

- VM の作成時に、アタッチする既存の NIC を指定。
- 複数の NIC がアタッチされた VM を作成。
- NIC の名前を指定 (ポータルは既定の名前で NIC を作成します)。
- プライベート IP アドレスの割り当て方法を静的として指定。 ポータルでは自動的に動的プライベート IP アドレスが割り当てられますが、ポータルによる NIC の作成後に割り当て方法を変更できます。

PowerShell または CLI を使用すると、ポータルで対処できなかったすべての属性を設定して NIC または VM を作成できます。 この後のセクションのタスクを実行する前に、次の制約と動作について検討してください。

- VM のサイズによって、サポートされる NIC の数は異なります。 各サイズの VM でサポートされる NIC の数について詳しくは、[Linux](../virtual-machines/virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) と [Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) の VM のサイズに関する記事をご覧ください。 
- 既定では、VM にアタッチされる最初の NIC が "*プライマリ*" NIC と定義されます。 VM にアタッチされるそれ以外のすべての NIC は "*セカンダリ*" NIC です。
- 既定では、VM からのすべての送信トラフィックで、プライマリ NIC のプライマリ IP 構成に割り当てられたプライマリ IP アドレスが使用されます。 もちろん、VM のオペレーティング システム内で送信トラフィックに使用する IP アドレスを制御できます。
- かつては、同じ可用性セット内のすべての VM で、アタッチする NIC を 1 つにするか複数にするかを統一する必要がありました。 現在は、NIC の数に関係なく VM が同じ可用性セットに存在できます。 ただし、VM を可用性セットに追加できるのは、VM の作成時のみです。 可用性セットについて詳しくは、「[Azure での Windows 仮想マシンの可用性の管理](../virtual-machines/virtual-machines-windows-manage-availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy)」の記事をご覧ください。
- 同じ VM にアタッチされた NIC を VNet 内の別のサブネットに接続することはできますが、そのような NIC はすべて同じ VNet に接続する必要があります。
- 任意のプライマリまたはセカンダリ NIC の任意の IP 構成の任意の IP アドレスを Azure Load Balancer バックエンド プールに追加できます。 以前は、プライマリ NIC のプライマリ IP アドレスのみをバックエンド プールに追加できました。
- VM を削除しても VM にアタッチされた NIC は削除されません。 VM を削除すると、NIC は VM からデタッチされます。 この NIC を別の VM にアタッチしたり、削除することができます。

### <a name="vm-create"></a>仮想マシン作成時の 1 つ以上の NIC のアタッチ

既存の NIC の新しい VM へのアタッチ、または複数の NIC がアタッチされた VM の作成は、Azure Portal を使用して行うことはできません。 次の Azure CLI または PowerShell コマンドを使用すると、既存の 1 つ以上の NIC を VM の作成時にアタッチできます。

- **CLI:** [az vm create](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#create)
- **PowerShell:** [New-AzureRmVM](/powershell/resourcemanager/azurerm.compute/v2.5.0/new-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)

### <a name="vm-view-nic"></a> 仮想マシンにアタッチされている NIC の表示

1. ご利用のサブスクリプションの所有者、共同作成者、またはネットワーク共同作成者いずれかのロールが割り当てられているアカウントで、[Azure Portal](https://portal.azure.com) にログインします。 アカウントへのロールの割り当てについて詳しくは、「[Azure のロールベースのアクセス制御のための組み込みロール](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)」の記事をご覧ください。
2. Azure Portal 上部に "*リソースの検索*" というテキストが表示されたボックスがあります。そこに "*仮想マシン*" と入力します。 検索結果に **[仮想マシン]** が表示されたら、それをクリックします。
3. 表示される **[仮想マシン]** ブレードで、アタッチされているネットワーク インターフェイスを表示する VM の名前をクリックします。
4. 選択した VM に対して表示される **[仮想マシン]** ブレードで、**[ネットワーク インターフェイス]** をクリックします。

|**ツール**|**コマンド**|
|---|---|
|**CLI**|[az vm show](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#show)|
|**PowerShell**|[Get-AzureRmVM](/powershell/resourcemanager/azurerm.compute/v1.3.4/get-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

### <a name="vm-attach-nic"></a>既存仮想マシンへの NIC のアタッチ

NIC をアタッチしようとする VM は、複数の NIC をサポートし、停止 (割り当て解除) 状態になっている必要があります。 Azure Portal を使用して既存の VM に NIC をアタッチすることはできません。 次の Azure CLI または PowerShell コマンドを使用して、NIC を VM にアタッチできます。

- **CLI:** [az vm nic add](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#add)
- **PowerShell:** [Add-AzureRmVMNetworkInterface](/powershell/resourcemanager/azurerm.compute/v2.5.0/add-azurermvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)

### <a name="vm-detach-nic"></a>既存仮想マシンからの NIC のデタッチ

NIC をデタッチしようとする VM は、停止 (割り当て解除) 状態になっており、現時点で 2 つ以上の NIC がアタッチされている必要があります。 任意の NIC をデタッチできますが、VM には常に 1 つ以上の NIC がアタッチされている必要があります。 プライマリ NIC をデタッチした場合、残りの NIC の中で VM に最も長くアタッチされているものにプライマリ属性が割り当てられます。 自分で任意の NIC をプライマリに指定することもできます。 Azure Portal では NIC を VM からデタッチすることも、NIC のプライマリ属性を設定することもできませんが、どちらの操作も CLI または PowerShell を使用して行うことができます。 次の Azure CLI または PowerShell コマンドを使用して、NIC を VM からデタッチできます。

- **CLI:** [az vm nic remove](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#remove)
- **PowerShell:** [Remove-AzureRMVMNetworkInterface](/powershell/resourcemanager/azurerm.compute/v2.5.0/remove-azurermvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="next-steps"></a>次のステップ
スクリプトを使用して、複数の NIC または IP 構成を持つ VM を作成するには、次の記事をご覧ください。

|**タスク**|**ツール**|
|---|---|
|**複数 NIC を持つ VM の作成**|[CLI](virtual-network-deploy-multinic-arm-cli.md) および [PowerShell](virtual-network-deploy-multinic-arm-ps.md)|
|**複数の IP アドレスが割り当てられた 1 つの NIC VM の作成**|[CLI](virtual-network-multiple-ip-addresses-cli.md) および [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|

