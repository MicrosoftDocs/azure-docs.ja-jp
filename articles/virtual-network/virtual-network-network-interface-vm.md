---
title: Azure VM のネットワーク インターフェイスを追加または削除する
description: 仮想マシンにネットワーク インターフェイスを追加する、または仮想マシンからネットワーク インターフェイスを削除する方法について説明します。
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/13/2020
ms.author: kumud
ms.openlocfilehash: 43d3da49688a7daeb3ea4e0c1c3dba505dcd3b59
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102508335"
---
# <a name="add-network-interfaces-to-or-remove-network-interfaces-from-virtual-machines"></a>仮想マシンのネットワーク インターフェイスの追加と削除

Azure 仮想マシン (VM) を作成するときに、既存のネットワーク インターフェイスを追加する方法について説明します。 また、既存の VM から停止 (割り当て解除) 状態でネットワーク インターフェイスを追加または削除する方法について説明します。 Azure VM は、ネットワーク インターフェイスを使用してインターネット、Azure、およびオンプレミスのリソースと通信できます。 1 つの VM には、1 つ以上のネットワーク インターフェイスがあります。 

ネットワーク インターフェイスに対して IP アドレスの追加、変更、または削除が必要な場合は、[ネットワーク インターフェイスの IP アドレスの管理](virtual-network-network-interface-addresses.md)に関するページを参照してください。 ネットワーク インターフェイスを作成、変更、または削除する場合は、[ネットワークインターフェイスの管理](virtual-network-network-interface.md)に関するページを参照してください。

## <a name="before-you-begin"></a>開始する前に

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure アカウントをお持ちでない場合は、アクティブなサブスクリプションを使用してそれを設定します。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。 この記事の残りを始める前に、次のいずれかのタスクを完了します。

- **ポータル ユーザー**:Azure アカウントで [Azure Portal](https://portal.azure.com) にサインインします。

- **PowerShell ユーザー**:[Azure Cloud Shell](https://shell.azure.com/powershell) でコマンドを実行するか、お使いのコンピューターから PowerShell を実行します。 Azure Cloud Shell は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。 一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。 Azure Cloud Shell のブラウザー タブで、 **[環境の選択]** ドロップダウン リストを見つけ、(まだ選択されていない場合は) **[PowerShell]** を選択します。

    PowerShell をローカルで実行している場合、Azure PowerShell モジュール バージョン 1.0.0 以降を使用します。 インストールされているバージョンを確認するには、`Get-Module -ListAvailable Az.Network` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。 `Connect-AzAccount` を実行して、Azure との接続を作成します。

- **Azure のコマンド ライン インターフェイス (CLI) のユーザー**:[Azure Cloud Shell](https://shell.azure.com/bash) でコマンドを実行するか、お使いのコンピューターから CLI を実行します。 ローカルで Azure CLI を実行する場合は、Azure CLI バージョン 2.0.26 以降を使用してください。 インストールされているバージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。 `az login` を実行して、Azure との接続を作成します。

## <a name="add-existing-network-interfaces-to-a-new-vm"></a>既存のネットワーク インターフェイスを新しい VM に追加する

ポータルで仮想マシンを作成すると、ポータルがネットワーク インターフェイスを既定の設定で作成し、ユーザーの代わりにネットワーク インターフェイスを VM に接続します。 ポータルでは、新しい VM に既存のネットワーク インターフェイスを追加したり、複数のネットワーク インターフェイスが接続された VM を作成したりすることはできません。 この 2 つは CLI または PowerShell を使用して行います。 これの[制約](#constraints)は理解しておいてください。 複数のネットワーク インターフェイスを持つ VM を作成する場合は、VM を作成した後に、そのネットワーク インターフェイスが適切に使用されるようにオペレーティング システムを構成する必要もあります。 複数のネットワーク インターフェイスについては、[Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) または [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) の構成方法に関するページをご確認ください。

### <a name="commands"></a>コマンド

VM を作成する前に、[ネットワーク インターフェイス](virtual-network-network-interface.md#create-a-network-interface)を作成します。

|ツール|コマンド|
|---|---|
|CLI|[az network nic create](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-network-nic-create)|
|PowerShell|[New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="add-a-network-interface-to-an-existing-vm"></a>ネットワーク インターフェイスを既存の VM に追加する

ネットワーク インターフェイスをお使いの仮想マシンに追加するには、次を実行します。

1. [Azure portal](https://portal.azure.com) に移動し、既存の仮想マシンを検索します。 **[仮想マシン]** を検索して選択します。

2. 使用する VM の名前を選択します。 この VM は、追加するネットワーク インターフェイスの数をサポートしている必要があります。 各 VM サイズでサポートされているネットワーク インターフェイスの数については、[Linux VM](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) または [Windows VM](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) の Azure におけるサイズをご確認ください。

3. VM のコマン ドバーで **[停止]** を選択し、確認のダイアログ ボックスで **[OK]** を選択します。 そして VM の **状態** が **[停止済み (割り当て解除)]** に変わるまで待ちます。

4. VM のメニュー バーで、 **[ネットワーク]**  >  **[ネットワーク インターフェイスの接続]** を順に選択します。 次に、 **[Attach existing network interface]** \(既存のネットワーク インターフェイスの接続\) で、 **[OK]** を選択します。

    >[!NOTE]
    >選択したネットワーク インターフェイスで高速ネットワークを有効にしたり、IPv6 アドレスを割り当てたりすることはできません。また、これは現在 VM に接続されているネットワーク インターフェイスと同じ仮想ネットワーク内にある必要があります。

    既存のネットワーク インターフェイスがない場合は、最初に作成する必要があります。 これを行うには、 **[ネットワーク インターフェイスの作成]** を選択します。 ネットワーク インターフェイスの作成方法の詳細については、「[ネットワーク インターフェイスの作成](virtual-network-network-interface.md#create-a-network-interface)」を参照してください。 ネットワーク インターフェイスを仮想マシンに追加するときの追加の制約に関する詳細については、「[制約](#constraints)」を参照してください。

5. VM のメニュー バーで、 **[概要]**  >  **[開始]** を選択し、仮想マシンを再起動します。

これで複数のネットワーク インターフェイスを正しく使用できるように VM オペレーティング システムを構成できます。 複数のネットワーク インターフェイスについては、[Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) または [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) の構成方法に関するページをご確認ください。

### <a name="commands"></a>コマンド

|ツール|コマンド|
|---|---|
|CLI|[az vm nic add](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-vm-nic-add) (参照)[手順詳細](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-a-vm)|
|PowerShell|[Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (参照)、[手順詳細](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-an-existing-vm)|

## <a name="view-network-interfaces-for-a-vm"></a>VM のネットワーク インターフェイスを表示する

VM にアタッチされているネットワーク インターフェイスを表示して、各ネットワーク インターフェイスの構成の詳細や、各ネットワーク インターフェイスに割り当てられている IP アドレスを確認できます。 

1. [Azure portal](https://portal.azure.com) に移動し、既存の仮想マシンを検索します。 **[仮想マシン]** を検索して選択します。

    >[!NOTE]
    >お使いのサブスクリプションで所有者、共同作成者、またはネットワーク共同作成者いずれかのロールが割り当てられているアカウントでサインインします。 アカウントへのロールの割り当て方法の詳細については、「[Azure ロールベースのアクセス制御の組み込みロール](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)」を参照してください。

2. 接続されているネットワーク インターフェイスを表示する VM の名前を選択します。

3. VM のメニュー バーで、 **[ネットワーク]** を選択します。

ネットワーク インターフェイスの設定とそれを変更する方法については、[ネットワーク インターフェイスの管理](virtual-network-network-interface.md)に関するページをご覧ください。 ネットワーク インターフェイスに割り当てる IP アドレスの追加、変更、または削除を行う方法については、[ネットワーク インターフェイスの IP アドレスの管理](virtual-network-network-interface-addresses.md)に関するページを参照してください。

### <a name="commands"></a>コマンド

|ツール|コマンド|
|---|---|
|CLI|[az vm nic list](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-vm-nic-list)|
|PowerShell|[Get-AzVM](/powershell/module/az.compute/get-azvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="remove-a-network-interface-from-a-vm"></a>ネットワーク インターフェイスを VM から削除する

1. [Azure portal](https://portal.azure.com) に移動し、既存の仮想マシンを検索します。 **[仮想マシン]** を検索して選択します。

2. 接続されているネットワーク インターフェイスを表示する VM の名前を選択します。

3. VM ツールバーで、 **[停止]** を選択します。

4. VM の **状態** が **[停止済み (割り当て解除)]** に変わるまで待ちます。

5. VM のメニュー バーで、 **[ネットワーク]**  >  **[ネットワーク インターフェイスの切断]** を順に選択します。

6. **[ネットワーク インターフェイスの切断]** ダイアログボックスで、切断するネットワーク インターフェイスを選択します。 **[OK]** をクリックします。

    >[!NOTE]
    >仮想マシンには常に少なくとも 1 つのネットワーク インターフェイスが接続されている必要があるため、ネットワーク インターフェイスが 1 つしか表示されていない場合、そのインターフェイスは切断できません。

### <a name="commands"></a>コマンド

|ツール|コマンド|
|---|---|
|CLI|[az vm nic remove](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-vm-nic-remove) (参照)、[手順詳細](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-a-vm)|
|PowerShell|[Remove-AzVMNetworkInterface](/powershell/module/az.compute/remove-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (参照)、[手順詳細](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-an-existing-vm)|

## <a name="constraints"></a>制約

- VM には少なくとも 1 つのネットワーク インターフェイスが接続されている必要があります。

- VM には、VM のサイズが対応できるだけの数のネットワーク インターフェイスしか接続できません。 各 VM サイズでサポートされているネットワーク インターフェイスの数の詳細については、[Linux VM](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) または [Windows VM](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) の Azure におけるサイズをご覧ください。 すべてのサイズが、少なくとも 2 つのネットワーク インターフェイスに対応します。

- 現在 VM に接続しているネットワーク インターフェイスは、別の VM には追加できません。 ネットワーク インターフェイスの作成方法の詳細については、「[ネットワーク インターフェイスの作成](virtual-network-network-interface.md#create-a-network-interface)」を参照してください。

- 以前は、複数のネットワーク インターフェイスをサポートする、少なくとも 2 つのネットワーク インターフェイスで作成された VM にのみ、ネットワーク インターフェイスを追加できました。 1 つのネットワーク インターフェイスで作成された VM は、VM のサイズで複数のネットワーク インターフェイスがサポートされている場合でも、ネットワーク インターフェイスを追加できませんでした。 逆に、ネットワーク インターフェイスを削除できるのは、少なくとも 3 つのネットワーク インターフェイスが存在する VM からのみでした。これは、少なくとも 2 つのネットワーク インターフェイスが作成された VM には、常に少なくとも 2 つのネットワーク インターフェイスが存在する必要があったためです。 これらの制約はなくなりました。 現在、任意の数 (VM のサイズでサポートされている最大数) のネットワーク インターフェイスで VM を作成できます。

- 既定で VM に接続された最初のネットワーク インターフェイスが、"*プライマリ*" ネットワーク インターフェイスです。 VM 内の他のすべてのネットワーク インターフェイスは、*セカンダリ* ネットワーク インターフェイスになります。

- どのネットワーク インターフェイスから送信トラフィックを送信するかを制御できます。 ただし、VM では既定で、プライマリ ネットワーク インターフェイスのプライマリ IP 構成に割り当てられた IP アドレスからすべての送信トラフィックを送信します。

- 以前は、同じ可用性セット内のすべての VM は、アタッチされるネットワーク インターフェイスを 1 つまたは複数に統一する必要がありました。 現在は、VM のサイズでサポートされている最大数までのネットワーク インターフェイスがアタッチされた VM が同じ可用性セットに存在できます。 VM を可用性セットに追加できるのは、VM の作成時のみです。 可用性セットの詳細については、[Azure での VM の可用性の管理](../virtual-machines/availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関するページをご覧ください。

- 同じ VM 内のネットワーク インターフェイスを、1 つの仮想ネットワーク内の別のサブネットに接続できます。 ただし、そのネットワーク インターフェイスはすべて同じ仮想ネットワークに接続されている必要があります。

- 任意のプライマリまたはセカンダリ ネットワーク インターフェイスの任意の IP 構成の任意の IP アドレスを Azure Load Balancer バックエンド プールに追加できます。 以前は、プライマリ ネットワーク インターフェイスのプライマリ IP アドレスのみをバックエンド プールに追加できました。 IP アドレスと IP 構成の詳細については、[IP アドレスの追加、変更、削除](virtual-network-network-interface-addresses.md)に関するページをご覧ください。

- VM を削除してもそれに接続されたネットワーク インターフェイスは削除されません。 VM を削除すると、ネットワーク インターフェイスは VM からデタッチされます。 それらのネットワーク インターフェイスは、別の VM に追加することも削除することもできます。

- ドキュメントに記載されている最適なパフォーマンスを実現するには、高速ネットワークが必要です。 場合によっては、[Windows](create-vm-accelerated-networking-powershell.md) または [Linux](create-vm-accelerated-networking-cli.md) 仮想マシンの高速ネットワークを明示的に有効にする必要があります。

## <a name="next-steps"></a>次のステップ

複数のネットワーク インターフェイスまたは IP アドレスを持つ VM を作成する方法については、次の項目を参照してください。

|タスク|ツール|
|---|---|
|複数 NIC を持つ VM の作成|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)、[PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|複数の IPv4 アドレスが割り当てられた 1 つの NIC VM の作成|[CLI](virtual-network-multiple-ip-addresses-cli.md)、[PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|プライベート IPv6 アドレスが割り当てられた 1 つの NIC VM の作成 (Azure Load Balancer の背後)|[CLI](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json)、[PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)、[Azure Resource Manager テンプレート](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|