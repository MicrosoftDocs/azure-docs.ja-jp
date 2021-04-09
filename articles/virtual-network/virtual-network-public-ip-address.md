---
title: パブリック IP アドレスの管理 | Microsoft Docs
titleSuffix: Azure Virtual Network
description: パブリック IP アドレスを管理します。  パブリック IP アドレスは、どのように構成可能な独自の設定を備えたリソースであるのかについても説明します。
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
editor: ''
tags: azure-resource-manager
ms.assetid: bb71abaf-b2d9-4147-b607-38067a10caf6
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/06/2019
ms.author: kumud
ms.openlocfilehash: d52430c87d99f8837c78fcff89d8b214e45350ff
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98934933"
---
# <a name="manage-public-ip-addresses"></a>パブリック IP アドレスの管理

パブリック IP アドレスとその作成、変更、削除の方法について説明します。 パブリック IP アドレスは、変更可能な独自の設定を備えたリソースです。 パブリック IP アドレスをパブリック IP アドレスをサポートする Azure リソースに割り当てると、次のことが可能になります。
- インターネットから、Azure Virtual Machines (VM)、Azure Application Gateway、Azure Load Balancer、Azure VPN Gateway などのリソースへの受信。 パブリック IP アドレスが割り当てられていないリソース (VM など) でも、インターネットから一部のリソースと通信することができます。ただし、そのリソースがロード バランサーのバックエンド プールの一部であり、ロード バランサーにパブリック IP アドレスが割り当てられている必要があります。 特定の Azure サービスのリソースにパブリック IP アドレスを割り当てることができるかどうか、または別の Azure リソースのパブリック IP アドレスを使用して通信できるかどうかを判断するには、そのサービスのドキュメントを参照してください。
- 予測できる IP アドレスを使ったインターネットへの送信方向の接続。 たとえば、仮想マシンは、パブリック IP アドレスが割り当てられていなくてもインターネットに対して送信方向の通信を行うことができますが、既定でそのアドレスは Azure によって予測できないパブリック アドレスに変換されたネットワーク アドレスです。 パブリック IP アドレスをリソースに割り当てると、送信方向の接続に使われる IP アドレスがわかります。 アドレスは、予測することはできますが、選ばれている割り当て方法によっては変化する可能性があります。 詳細については、「[パブリック IP アドレスの作成](#create-a-public-ip-address)」を参照してください。 Azure リソースからの送信接続の詳細については、[送信用接続の詳細](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事を参照してください。

## <a name="before-you-begin"></a>開始する前に

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

この記事のセクションに記載された手順を始める前に、次のタスクを完了してください。

- まだ Azure アカウントを持っていない場合は、[無料試用版アカウント](https://azure.microsoft.com/free)にサインアップしてください。
- ポータルを使用する場合は、 https://portal.azure.com を開き、Azure アカウントでログインします。
- PowerShell コマンドを使用してこの記事のタスクを実行する場合は、[Azure Cloud Shell](https://shell.azure.com/powershell) でコマンドを実行するか、お使いのコンピューターから PowerShell を実行してください。 Azure Cloud Shell は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。 一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。 このチュートリアルには、Azure PowerShell モジュール バージョン 1.0.0 以降が必要です。 インストールされているバージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzAccount` を実行して Azure との接続を作成することも必要です。
- Azure コマンド ライン インターフェイス (CLI) コマンドを使用してこの記事のタスクを実行する場合は、[Azure Cloud Shell](https://shell.azure.com/bash) でコマンドを実行するか、お使いのコンピューターから CLI を実行してください。 このチュートリアルには、Azure CLI のバージョン 2.0.31 以降が必要です。 インストールされているバージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。 Azure CLI をローカルで実行している場合、`az login` を実行して Azure との接続を作成することも必要です。

Azure へのログインまたは接続に使用するアカウントは、[ネットワークの共同作業者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)ロール、または「[アクセス許可](#permissions)」の一覧に記載されている適切なアクションが割り当てられている[カスタム ロール](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に割り当てられている必要があります。

パブリック IP アドレスには、わずかですが料金が発生します。 料金については、「[IP アドレスの料金](https://azure.microsoft.com/pricing/details/ip-addresses)」ページを参照してください。

## <a name="create-a-public-ip-address"></a>パブリック IP アドレスの作成

ポータル、PowerShell、または CLI を使用してパブリック IP アドレスを作成する方法については、以下のページを参照してください。

 * [パブリック IP アドレスの作成 - ポータル](./create-public-ip-portal.md?tabs=option-create-public-ip-standard-zones)
 * [パブリック IP アドレスの作成 - PowerShell](./create-public-ip-powershell.md?tabs=option-create-public-ip-standard-zones)
 * [パブリック IP アドレスの作成 - Azure CLI](./create-public-ip-cli.md?tabs=option-create-public-ip-standard-zones)

>[!NOTE]
>ポータルによって 2 つのパブリック IP アドレス リソース (IPv4 と IPv6) を作成できますが、PowerShell および CLI のコマンドを使用すると、どちらか一方の IP バージョンのアドレスで 1 つのリソースが作成されます。 2 つのパブリック IP アドレス リソース (IP バージョンごとに 1 つ) を作成する場合は、コマンドを 2 回実行し、異なる名前と IP バージョンでパブリック IP アドレス リソースを指定する必要があります。

作成時のパブリック IP アドレスの特定の属性の詳細については、次の表を参照してください。

   |設定|必須|詳細|
   |---|---|---|
   |IP バージョン|はい| IPv4 または IPv6 またはその両方を選択します。 両方を選択すると、パブリック IP アドレスが 2 つ (IPv4 アドレスが 1 つと IPv6 アドレスが 1 つ) 作成されます。 [Azure VNET での IPv6](../virtual-network/ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) に関する詳細情報。|
   |SKU|はい|SKU の導入前に作成されたパブリック IP アドレスはすべて、**Basic** SKU のパブリック IP アドレスです。 パブリック IP アドレスが作成された後に SKU を変更することはできません。 スタンドアロンの仮想マシン、可用性セット内の仮想マシン、または仮想マシン スケール セットは、Basic または Standard SKU を使用できます。 可用性セットまたはスケール セットまたはスタンドアロン VM の仮想マシン間での SKU の混在は許可されません。 **Basic** SKU: 可用性ゾーンをサポートするリージョンにパブリック IP アドレスを作成している場合、既定では **[Availability zone] \(可用性ゾーン)** 設定が *[なし]* に設定されます。 基本パブリック IP では、可用性ゾーンはサポートされていません。 **Standard** SKU: Standard SKU のパブリック IP を仮想マシンまたはロード バランサーのフロント エンドに関連付けることができます。 可用性ゾーンをサポートするリージョンにパブリック IP アドレスを作成している場合、既定では **[Availability zone] \(可用性ゾーン)** 設定が *[Zone-redundant] \(ゾーン冗長)* に設定されます。 可用性ゾーンの詳細については、 **[Availability zone] \(可用性ゾーン)** 設定を参照してください。 アドレスを標準のロード バランサーに関連付ける場合は、Standard SKU が必要です。 標準のロード バランサーの詳細については、「[Azure Load Balancer の Standard SKU](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)」を参照してください。 標準 SKU のパブリック IP アドレスを仮想マシンのネットワーク インターフェイスに割り当てるときは、[ネットワーク セキュリティ グループ](./network-security-groups-overview.md#network-security-groups)で、特定のトラフィックを明示的に許可する必要があります。 ネットワーク セキュリティ グループを作成して関連付け、目的のトラフィックを明示的に許可するまで、そのリソースとの通信は失敗します。|
   |サービス レベル|はい|IP アドレスがリージョンに関連付けられているか ( **[Regional]\(リージョン\)** )、複数のリージョンの "エニーキャスト" であるか ( **[Global]\(グローバル\)** ) を示します。 *"グローバル レベル"の IP は Standard IP のプレビュー機能であり、現在はリージョン間のロード バランサーにのみ使用されていることに注意してください。*|
   |名前|はい|選択したリソース グループ内で一意となる名前を使用してください。|
   |IP アドレスの割り当て|はい|**[動的]:** 動的アドレスの割り当ては、パブリック IP アドレスが Azure リソースに関連付けられ、そのリソースが初めて起動した後に限られます。 動的アドレスが仮想マシンなどのリソースに割り当てられ、その仮想マシンが停止し (割り当てが解除される)、再起動した場合、動的アドレスが変わることがあります。 仮想マシンが再起動された場合や、停止された (ただし、割り当て解除されない) 場合、このアドレスは同じままです。 パブリック IP アドレス リソースが関連付けられているリソースから切り離されると、動的アドレスは解放されます。 **静的:** 静的アドレスは、パブリック IP アドレスの作成時に割り当てられます。 パブリック IP アドレス リソースが削除されるまで、静的アドレスは解放されません。 アドレスがリソースに関連付けられていない場合、アドレスの作成後に割り当て方法を変更できます。 アドレスがリソースに関連付けられている場合、割り当て方法を変更することはできません。 **[IP バージョン]** として *[IPv6]* を選択した場合、Basic SKU での割り当て方法は "*動的*" にする必要があります。  Standard SKU のアドレスは、IPv4 と IPv6 の両方に対して "*静的*" です。 |
   |アイドル タイムアウト (分)|いいえ|クライアントからキープアライブ メッセージを送信しなくても TCP 接続または HTTP 接続が開いたまま維持される時間 (分)。 **[IP バージョン]** で [IPv6] を選んだ場合、この値を変更することはできません。 |
   |DNS 名ラベル|いいえ|名前の作成先となる Azure の場所 (すべてのサブスクリプション、すべての顧客) で一意となるようにしてください。 指定した名前を使用してリソースに接続できるよう、DNS 内の名前と IP アドレスが Azure によって自動的に登録されます。 指定した名前に既定のサブネット (*location.cloudapp.azure.com* など、<location> は選択した場所) が付加されて、完全修飾 DNS 名が作成されます。 両方のアドレス バージョンの作成を選んだ場合は、IPv4 アドレスと IPv6 アドレスの両方に同じ DNS 名が割り当てられます。 Azure の既定の DNS には IPv4 A と IPv6 AAAA の両方の名前レコードが含まれており、DNS 名が参照されると両方のレコードで応答します。 どちらのアドレス (IPv4 または IPv6) と通信するかは、クライアントが選択します。 代わりに、既定のサフィックスを持つ DNS 名ラベルを使用することで、Azure DNS サービスを使用して、パブリック IP アドレスに解決されるカスタム サフィックスを持つ DNS 名を構成することもできます。 詳細については、[Azure パブリック IP アドレスを使用した Azure DNS の使用](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address)に関するページを参照してください。|
   |名前 ("**両方**" の IP バージョンを選択した場合のみ表示)|はい、"**両方**" の IP バージョンを選択した場合|この名前は、この表の最初の **[名前]** で入力した名前と異なっている必要があります。 IPv4 アドレスと IPv6 アドレス両方の作成を選ぶと、2 つのパブリック IP アドレス リソースが作成されて、それぞれに IP アドレスのバージョンが割り当てられます。|
   |IP アドレスの割り当て ("**両方**" の IP バージョンを選択した場合のみ表示)|はい、"**両方**" の IP バージョンを選択した場合|上記の IP アドレスの割り当てと同じ制限|
   |サブスクリプション|はい|パブリック IP を関連付けるリソースと同じ[サブスクリプション](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)内に存在する必要があります。|
   |Resource group|はい|パブリック IP を関連付けるリソースと同じであっても異なっていても、[リソース グループ](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group)に存在させることができます。|
   |場所|はい|パブリック IP を関連付けるリソースと同じ[場所](https://azure.microsoft.com/regions) (リージョンとも呼ばれる) に存在する必要があります。|
   |可用性ゾーン| いいえ | この設定は、サポートされている場所を選択した場合にのみ表示されます。 サポートされている場所の一覧については、「[Availability zones overview (可用性ゾーンの概要)](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)」を参照してください。 **Basic** SKU を選択した場合は、 *[なし]* が自動的に選択されます。 特定のゾーンを保証したい場合は、特定のゾーンを選択できます。 どちらの選択肢もゾーン冗長ではありません。 **Standard** SKU を選択した場合は、[ゾーン冗長] が自動的に選択され、データ パスのゾーン障害からの復元性が高くなります。 特定のゾーンを保証したい場合は (これは、ゾーン障害からの復元性が高くありません)、特定のゾーンを選択できます。

## <a name="view-modify-settings-for-or-delete-a-public-ip-address"></a>パブリック IP アドレスを表示、設定変更、削除する

   - **表示または一覧表示**: SKU、アドレス、該当する関連付け (仮想マシンの NIC、ロード バランサーのフロントエンドなど) を含むパブリック IP の設定を確認する場合。
   - **変更**: 「[パブリック IP アドレスを作成する](#create-a-public-ip-address)」の手順 4 にある情報を使用して、アイドル タイムアウト、DNS 名ラベル、割り当て方法などの設定を変更する場合。  (パブリック IP SKU を Basic から Standard にアップグレードする完全なプロセスについては、[Azure パブリック IP アドレスのアップグレード](./virtual-network-public-ip-address-upgrade.md)に関するページを参照してください。)
   >[!WARNING]
   >パブリック IP アドレスの割り当て方法を静的から動的に変更する場合はまず、該当する IP 構成からそのアドレスの関連付けを解除する必要があります ( **[削除]** セクションを参照してください)。  また、割り当て方法を静的から動的に変更すると、パブリック IP アドレスに割り当てられた IP アドレスが失われることにもご注意ください。 静的または動的アドレスと任意の DNS 名ラベル (定義している場合) の間のマッピングは Azure パブリック DNS サーバーによって保持されますが、仮想マシンが停止 (割り当て解除) された状態にあった後に起動されると、動的 IP アドレスが変化する場合があります。 アドレスが変化しないようにするには、静的 IP アドレスを割り当ててください。
   
|操作|Azure portal|Azure PowerShell|Azure CLI|
|---|---|---|---|
|表示 | パブリック IP の **[概要]** セクション内 |[Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) でパブリック IP アドレス オブジェクトを取得してその設定を表示| [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show) で設定を表示|
|List | **[パブリック IP アドレス]** カテゴリ |[Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) で 1 つ以上のパブリック IP アドレス オブジェクトを取得してその設定を表示|[az network public-ip list](/cli/azure/network/public-ip#az-network-public-ip-list) でパブリック IP アドレスを一覧表示|
|変更 | 関連付けが解除された IP の場合、 **[構成]** を選択してアイドル タイムアウト、DNS 名ラベルを変更するか、基本 IP の割り当てを静的から動的に変更  |[Set-AzPublicIpAddress](/powershell/module/az.network/set-azpublicipaddress) で設定を更新 |[az network public-ip update](/cli/azure/network/public-ip#az-network-public-ip-update) で更新 |

   - **[削除]** :パブリック IP を削除するには、パブリック IP オブジェクトが IP 構成または仮想マシンの NIC に関連付けられていない必要があります。 詳細については、以下の表を参照してください。

|リソース|Azure portal|Azure PowerShell|Azure CLI|
|---|---|---|---|
|[仮想マシン](./remove-public-ip-address-vm.md)|**[関連付けの解除]** を選択して NIC 構成から IP アドレスの関連付けを解除してから、 **[削除]** を選択します。|[Set-AzPublicIpAddress](/powershell/module/az.network/set-azpublicipaddress) で NIC 構成から IP アドレスの関連付けを解除。[Remove-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress) で削除|[az network public-ip update --remove](/cli/azure/network/public-ip#az-network-public-ip-update) で NIC 構成から IP アドレスの関連付けを解除。[az network public-ip delete](/cli/azure/network/public-ip#az-network-public-ip-delete) で削除 |
|ロード バランサーのフロントエンド | 使用されていないパブリック IP アドレスに移動し、 **[関連付け]** を選択し、関連するフロントエンド IP 構成を持つロード バランサーを選択して置き換え (その後、古い IP を VM の場合と同じ方法で削除できます)  | [Set-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/set-azloadbalancerfrontendipconfig) で新しいフロントエンド IP 構成をパブリック ロード バランサーに関連付け。[Remove-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress) で削除。[Remove-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/remove-azloadbalancerfrontendipconfig) を使用してフロントエンド IP 構成を削除することも可能 (複数ある場合) |[az network lb frontend-ip update](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_update) で新しいフロントエンド IP 構成をパブリック ロード バランサーに関連付け。[Remove-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress) で削除。[az network lb frontend-ip delete](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_delete) を使用してフロントエンド IP 構成を削除することも可能 (複数ある場合)|
|ファイアウォール|該当なし| [Deallocate()](../firewall/firewall-faq.yml#how-can-i-stop-and-start-azure-firewall) でファイアウォールの割り当てを解除し、すべての IP 構成を削除 | [az network firewall ip-config delete](/cli/azure/ext/azure-firewall/network/firewall/ip-config#ext_azure_firewall_az_network_firewall_ip_config_delete) で IP を削除 (しかし、最初に PowerShell を使用して割り当てを解除することが必要)|

## <a name="virtual-machine-scale-sets"></a>Virtual Machine Scale Sets

パブリック IP を持つ仮想マシン スケール セットを使用する場合、個々の仮想マシン インスタンスに関連付けられた個別のパブリック IP オブジェクトは存在しません。 ただし、パブリック IP プレフィックス オブジェクトを[使用してインスタンス IP を生成](https://azure.microsoft.com/resources/templates/101-vmms-with-public-ip-prefix/)することはできます。

仮想マシン スケール セットのパブリック IP アドレスを一覧表示するには、PowerShell ([Get-AzPublicIpAddress -VirtualMachineScaleSetName](/powershell/module/az.network/get-azpublicipaddress)) または CLI ([az vmss list-instance-public-ips](/cli/azure/vmss#az_vmss_list_instance_public_ips)) を使用できます。

詳細については、「[Azure 仮想マシン スケール セットのネットワーク](../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#public-ipv4-per-virtual-machine)」を参照してください。

## <a name="assign-a-public-ip-address"></a>パブリック IP アドレスを割り当てる

パブリック IP アドレスを割り当てる方法については、次のリソースを参照してください。

- [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) または [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 仮想マシン (作成時)、または[既存の仮想マシン](virtual-network-network-interface-addresses.md#add-ip-addresses)
- [パブリック ロード バランサー](../load-balancer/quickstart-load-balancer-standard-public-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Application Gateway](../application-gateway/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [VPN ゲートウェイを使用したサイト間接続](../vpn-gateway/tutorial-site-to-site-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Virtual Machine Scale Set](../virtual-machine-scale-sets/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="permissions"></a>アクセス許可

パブリック IP アドレスでタスクを実行するには、[ネットワークの共同作業者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)ロール、または次の表に記載されている適切なアクションが割り当てられている[カスタム](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ロールにアカウントが割り当てられている必要があります。

| アクション                                                             | 名前                                                           |
| ---------                                                          | -------------                                                  |
| Microsoft.Network/publicIPAddresses/read                           | パブリック IP アドレスの読み取り                                          |
| Microsoft.Network/publicIPAddresses/write                          | パブリック IP アドレスの作成または更新                           |
| Microsoft.Network/publicIPAddresses/delete                         | パブリック IP アドレスの削除                                     |
| Microsoft.Network/publicIPAddresses/join/action                    | リソースへのパブリック IP アドレスの関連付け                    |

## <a name="next-steps"></a>次のステップ

- [PowerShell](powershell-samples.md) か [Azure CLI](cli-samples.md) のサンプル スクリプト、または Azure [Resource Manager テンプレート](template-samples.md)を利用したパブリック IP アドレスの作成
- パブリック IP アドレス用に [Azure Policy 定義](./policy-reference.md)を作成して割り当てる
