---
title: Azure Virtual Machines に対するネットワーク インターフェイスの追加と削除 | Microsoft Docs
description: 仮想マシンにネットワーク インターフェイスを追加する、または仮想マシンからネットワーク インターフェイスを削除する方法について説明します。
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/15/2017
ms.author: jdial
ms.openlocfilehash: a6371746d156fb0be2d45ac94c898652a3147a6b
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/27/2019
ms.locfileid: "56887490"
---
# <a name="add-network-interfaces-to-or-remove-network-interfaces-from-virtual-machines"></a>仮想マシンのネットワーク インターフェイスの追加と削除

Azure 仮想マシン (VM) を作成する際に既存のネットワーク インターフェイスを追加する方法と、停止 (割り当て解除) 状態の既存の VM に対してネットワーク インターフェイスの追加または削除を行う方法について説明します。 ネットワーク インターフェイスは、Azure 仮想マシンがインターネット、Azure、およびオンプレミスのリソースと通信できるようにします。 VM には、1 つまたは複数のネットワーク インターフェイスを実装できます。 

ネットワーク インターフェイスに対して IP アドレスの追加、変更、または削除が必要な場合は、[ネットワーク インターフェイスの IP アドレスの管理](virtual-network-network-interface-addresses.md)に関するページを参照してください。 ネットワーク インターフェイスを作成、変更、または削除する必要がある場合は、[ネットワークインターフェイスの管理](virtual-network-network-interface.md)に関するページを参照してください。

## <a name="before-you-begin"></a>開始する前に

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

この記事のセクションに記載された手順を始める前に、次のタスクを完了してください。

- まだ Azure アカウントを持っていない場合は、[無料試用版アカウント](https://azure.microsoft.com/free)にサインアップしてください。
- ポータルを使用する場合は、 https://portal.azure.com を開き、Azure アカウントでログインします。
- PowerShell コマンドを使用してこの記事のタスクを実行する場合は、[Azure Cloud Shell](https://shell.azure.com/powershell) でコマンドを実行するか、お使いのコンピューターから PowerShell を実行してください。 Azure Cloud Shell は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。 一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。 このチュートリアルには、Azure PowerShell モジュール バージョン 1.0.0 以降が必要です。 インストールされているバージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzAccount` を実行して Azure との接続を作成することも必要です。
- Azure コマンド ライン インターフェイス (CLI) コマンドを使用してこの記事のタスクを実行する場合は、[Azure Cloud Shell](https://shell.azure.com/bash) でコマンドを実行するか、お使いのコンピューターから CLI を実行してください。 このチュートリアルには、Azure CLI バージョン 2.0.26 以降が必要です。 インストールされているバージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。 Azure CLI をローカルで実行している場合、`az login` を実行して Azure との接続を作成することも必要です。

## <a name="add-existing-network-interfaces-to-a-new-vm"></a>既存のネットワーク インターフェイスを新しい VM に追加する

ポータルで仮想マシンを作成すると、ネットワーク インターフェイスが既定の設定で自動的に作成され、VM に接続されます。 Azure Portal では、新しい VM への既存のネットワーク インターフェイスの追加も、複数のネットワーク インターフェイスが接続された VM の作成も、実行できません。 CLI または PowerShell を使用すると両方を実行できますが、必ず[制約](#constraints)を理解しておいてください。 複数のネットワーク インターフェイスを持つ VM を作成する場合は、VM を作成した後に、そのネットワーク インターフェイスが適切に使用されるようにオペレーティング システムを構成する必要もあります。 複数のネットワーク インターフェイスについては、[Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) または [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) の構成方法に関するページをご確認ください。

### <a name="commands"></a>command

VM を作成する前に、「[ネットワーク インターフェイスの作成](virtual-network-network-interface.md#create-a-network-interface)」の手順を使用して、ネットワーク インターフェイスを作成してください。

|ツール|command|
|---|---|
|CLI|[az vm create](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|PowerShell|[New-AzVM](/powershell/module/az.compute/new-azvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vm-add-nic"></a>ネットワーク インターフェイスを既存の VM に追加する

1. Azure ポータルにサインインします。
2. ポータルの上部にある検索ボックスに、ネットワーク インターフェイスを追加する VM の名前を入力するか、**[すべてのサービス]**、**[仮想マシン]** の順に選択して VM を参照します。 VM が見つかったら、選択します。 この VM は、追加するネットワーク インターフェイスの数をサポートしている必要があります。 各 VM サイズでサポートされるネットワーク インターフェイスの数を調べるには、「[Azure の Linux 仮想マシンのサイズ](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)」または「[Azure の Windows 仮想マシンのサイズ](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)」を参照してください。  
3. **[設定]** で、**[概要]** を選択します。 **[停止]** を選択し、VM の**状態**が **[停止済み (割り当て解除)]** に変わるまで待ちます。
4. **[設定]** で、**[ネットワーク]** を選択します。
5. **[ネットワーク インターフェイスの接続]** を選択します。 現在別の VM に接続されていないネットワーク インターフェイスの一覧で、接続するネットワーク インターフェイスを選択します。

   >[!NOTE]
   >選択したネットワーク インターフェイスで高速ネットワークを有効にしたり、IPv6 アドレスを割り当てたりすることはできません。また、このネットワーク インターフェイスの仮想ネットワークは、現在 VM に接続されているネットワーク インターフェイスが含まれる仮想ネットワークと同じである必要があります。

   既存のネットワーク インターフェイスがない場合は、最初に作成する必要があります。 これを行うには、**[ネットワーク インターフェイスの作成]** を選択します。 ネットワーク インターフェイスの作成方法の詳細については、「[ネットワーク インターフェイスの作成](virtual-network-network-interface.md#create-a-network-interface)」を参照してください。 ネットワーク インターフェイスを仮想マシンに追加するときの追加の制約に関する詳細については、「[制約](#constraints)」を参照してください。

6. **[OK]** を選択します。
7. **[設定]** で、**[概要]**、**[開始]** の順に選択し、仮想マシンを開始します。
8. 複数のネットワーク インターフェイスが適切に使用されるように VM オペレーティング システムを構成します。 複数のネットワーク インターフェイスについては、[Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) または [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) の構成方法に関するページをご確認ください。

### <a name="commands"></a>command
|ツール|command|
|---|---|
|CLI|[az vm nic add](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json) (参照) [または詳細な手順](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-a-vm)|
|PowerShell|[Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (参照) または[詳細な手順](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-an-existing-vm)|

## <a name="view-network-interfaces-for-a-vm"></a>VM のネットワーク インターフェイスを表示する

VM にアタッチされているネットワーク インターフェイスを表示して、各ネットワーク インターフェイスの構成の詳細や、各ネットワーク インターフェイスに割り当てられている IP アドレスを確認できます。 

1. ご利用のサブスクリプションの所有者、共同作成者、またはネットワーク共同作成者いずれかのロールが割り当てられているアカウントで、[Azure Portal](https://portal.azure.com) にサインインします。 アカウントへのロールの割り当て方法の詳細については、「[Azure ロールベースのアクセス制御の組み込みロール](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)」を参照してください。
2. Azure Portal 上部に "**リソースの検索**" というテキストが表示されたボックスがあります。そこに "**仮想マシン**" と入力します。 検索結果に **[仮想マシン]** が表示されたら、それを選択します。
3. ネットワーク インターフェイスを表示する VM の名前を選択します。
4. 選択した VM の **[設定]** セクションで、**[ネットワーク]** を選択します。 ネットワーク インターフェイスの設定とそれを変更する方法については、[ネットワーク インターフェイスの管理](virtual-network-network-interface.md)に関するページをご覧ください。 ネットワーク インターフェイスに割り当てる IP アドレスの追加、変更、または削除を行う方法については、[ネットワーク インターフェイスの IP アドレスの管理](virtual-network-network-interface-addresses.md)に関するページを参照してください。

### <a name="commands"></a>command

|ツール|command|
|---|---|
|CLI|[az vm show](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|PowerShell|[Get-AzVM](/powershell/module/az.compute/get-azvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="remove-a-network-interface-from-a-vm"></a>ネットワーク インターフェイスを VM から削除する

1. Azure ポータルにサインインします。
2. ポータルの上部にある検索ボックスで、ネットワーク インターフェイスを削除 (デタッチ) する VM の名前を検索するか、**[すべてのサービス]**、**[仮想マシン]** の順に選択して VM を参照します。 VM が見つかったら、選択します。
3. **[設定]** で **[概要]** を選択し、次に **[停止]** を選択します。 VM の**状態**が **[停止済み (割り当て解除)]** に変わるまで待ちます。
4. **[設定]** で、**[ネットワーク]** を選択します。
5. **[ネットワーク インターフェイスの切断]** を選択します。 現在仮想マシンに接続されているネットワーク インターフェイスの一覧で、デタッチするネットワーク インターフェイスを選択します。

   >[!NOTE]
   >1 つのネットワーク インターフェイスしか表示されていない場合、そのインターフェイスはデタッチできません。仮想マシンには、少なくとも 1 つのネットワーク インターフェイスが必ず接続されている必要があります。
6. **[OK]** を選択します。

### <a name="commands"></a>command

|ツール|command|
|---|---|
|CLI|[az vm nic remove](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json) (参照) [または詳細な手順](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-a-vm)|
|PowerShell|[Remove-AzVMNetworkInterface](/powershell/module/az.compute/remove-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (参照) または[詳細な手順](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-an-existing-vm)|

## <a name="constraints"></a>制約

- VM には少なくとも 1 つのネットワーク インターフェイスが接続されている必要があります。
- VM には、VM のサイズが対応できるだけの数のネットワーク インターフェイスしか接続できません。 各 VM サイズでサポートされるネットワーク インターフェイスの数の詳細については、「[Azure の Linux 仮想マシンのサイズ](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)」または「[Azure の Windows 仮想マシンのサイズ](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)」を参照してください。 すべてのサイズが、少なくとも 2 つのネットワーク インターフェイスに対応します。
- その時点で別の VM にアタッチされているネットワーク インターフェイスを VM に追加することはできません。 ネットワーク インターフェイスの作成方法の詳細については、「[ネットワーク インターフェイスの作成](virtual-network-network-interface.md#create-a-network-interface)」を参照してください。
- 以前は、複数のネットワーク インターフェイスをサポートしていて、少なくとも 2 つのネットワーク インターフェイスが作成された VM にのみ、ネットワーク インターフェイスを追加できました。 1 つのネットワーク インターフェイスが作成された VM は、VM のサイズが複数のネットワーク インターフェイスをサポートしている場合でも、ネットワーク インターフェイスを追加することはできませんでした。 逆に、ネットワーク インターフェイスを削除できるのは、少なくとも 3 つのネットワーク インターフェイスが存在する VM からのみでした。これは、少なくとも 2 つのネットワーク インターフェイスが作成された VM には、常に少なくとも 2 つのネットワーク インターフェイスが存在する必要があったためです。 これらの制約は、どちらも当てはまらなくなっています。 現在、任意の数 (VM のサイズでサポートされている最大数) のネットワーク インターフェイスで VM を作成できます。
- 既定では、VM に接続されている最初のネットワーク インターフェイスが、"*プライマリ*" ネットワーク インターフェイスとして定義されます。 VM 内の他のすべてのネットワーク インターフェイスは、*セカンダリ* ネットワーク インターフェイスになります。
- 送信トラフィックをどのネットワーク トラフィックに送信するかを制御することはできますが、既定では、VM からのすべての送信トラフィックが、プライマリ ネットワーク インターフェイスのプライマリ IP 構成に割り当てられた IP アドレスで送信されます。
- 以前は、同じ可用性セット内のすべての VM は、アタッチされるネットワーク インターフェイスを 1 つまたは複数に統一する必要がありました。 現在は、VM のサイズでサポートされている最大数までのネットワーク インターフェイスがアタッチされた VM が同じ可用性セットに存在できます。 VM を可用性セットに追加できるのは、VM の作成時のみです。 可用性セットの詳細については、[Azure での VM の可用性の管理](../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy)に関するページをご覧ください。
- 同じ VM のネットワーク インターフェイスは仮想ネットワーク内の異なるサブネットに接続できますが、これらのネットワーク インターフェイスはすべて同じ仮想ネットワークに接続されている必要があります。
- 任意のプライマリまたはセカンダリ ネットワーク インターフェイスの任意の IP 構成の任意の IP アドレスを Azure Load Balancer バックエンド プールに追加できます。 以前は、プライマリ ネットワーク インターフェイスのプライマリ IP アドレスのみをバックエンド プールに追加できました。 IP アドレスと IP 構成の詳細については、[IP アドレスの追加、変更、削除](virtual-network-network-interface-addresses.md)に関するページをご覧ください。
- VM を削除しても VM にアタッチされたネットワーク インターフェイスは削除されません。 VM を削除すると、ネットワーク インターフェイスは VM からデタッチされます。 それらのネットワーク インターフェイスは、別の VM に追加することも削除することもできます。
- ネットワーク インターフェイスにプライベート IPv6 アドレスが割り当てられている場合は、VM の作成時に VM に追加 (接続) する必要があります。 VM の作成後に、IPv6 アドレスが割り当てられているネットワーク インターフェイスを VM に追加することはできません。 仮想マシンの作成時に、プライベート IPv6 アドレスが割り当てられたネットワーク インターフェイスを追加する場合、VM サイトがサポートしているネットワーク インターフェイス数にかかわらず、その仮想マシンに追加できるのはそのネットワーク インターフェイスのみです。 IP アドレスをネットワーク インターフェイスに割り当てる方法については、[ネットワーク インターフェイスの IP アドレスの管理](virtual-network-network-interface-addresses.md)に関するページを参照してください。
- IPv6 と同様、VM の作成後、高速ネットワークが有効になっているネットワーク インターフェイスは、VM に接続できません。 また、高速ネットワークを利用するには、VM オペレーティング システムで手順を実行する必要もあります。 高速ネットワークの詳細、および使用時の他の制約については、[Windows](create-vm-accelerated-networking-powershell.md) または [Linux](create-vm-accelerated-networking-cli.md) 仮想マシンに関するページをご覧ください。

## <a name="next-steps"></a>次の手順
複数のネットワーク インターフェイスまたは IP アドレスを持つ VM を作成する方法については、次の記事を参照してください。

|タスク|ツール|
|---|---|
|複数 NIC を持つ VM の作成|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)、[PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|複数の IPv4 アドレスが割り当てられた 1 つの NIC VM の作成|[CLI](virtual-network-multiple-ip-addresses-cli.md)、[PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|プライベート IPv6 アドレスが割り当てられた 1 つの NIC VM の作成 (Azure Load Balancer の背後)|[CLI](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json)、[PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)、[Azure Resource Manager テンプレート](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
