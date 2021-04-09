---
title: Azure ネットワーク インターフェイスの IP アドレスの構成 | Microsoft Docs
description: ネットワーク インターフェイスのプライベート IP アドレスとパブリック IP アドレスを追加、変更、削除する方法について説明します。
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/22/2020
ms.author: allensu
ms.openlocfilehash: 1df132e558421d2ec6e26c3883c89457716dfc42
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103419016"
---
# <a name="add-change-or-remove-ip-addresses-for-an-azure-network-interface"></a>Azure ネットワーク インターフェイスの IP アドレスの追加、変更、削除

ネットワーク インターフェイスのパブリック IP アドレスとプライベート IP アドレスを追加、変更、削除する方法について説明します。 ネットワーク インターフェイスにプライベート IP アドレスを割り当てると、仮想マシンは、Azure 仮想ネットワーク内および接続されたネットワーク内の他のリソースと通信できます。 また、予測できない IP アドレスを使ってインターネットに通信を送信することもできます。 ネットワーク インターフェイスに[パブリック IP アドレス](virtual-network-public-ip-address.md)を割り当てると、インターネットから仮想マシンへの通信を受信できます。 また、予測できる IP アドレスを使って仮想マシンからインターネットに通信を送信することもできます。 詳細については、「[Azure の送信用接続の詳細](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)」を参照してください。

ネットワーク インターフェイスを作成、変更、または削除する必要がある場合は、「[ネットワーク インターフェイスの作成、変更、削除](virtual-network-network-interface.md)」を参照してください。 仮想マシンのネットワーク インターフェイスを追加または削除する必要がある場合は、「[仮想マシンに対するネットワーク インターフェイスの追加と削除](virtual-network-network-interface-vm.md)」を参照してください。

## <a name="before-you-begin"></a>開始する前に

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

この記事のセクションに記載された手順を始める前に、次のタスクを完了してください。

- まだ Azure アカウントを持っていない場合は、[無料試用版アカウント](https://azure.microsoft.com/free)にサインアップしてください。
- ポータルを使用する場合は、 https://portal.azure.com を開き、Azure アカウントでログインします。
- PowerShell コマンドを使用してこの記事のタスクを実行する場合は、[Azure Cloud Shell](https://shell.azure.com/powershell) でコマンドを実行するか、お使いのコンピューターから PowerShell を実行してください。 Azure Cloud Shell は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。 一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。 このチュートリアルには、Azure PowerShell モジュール バージョン 1.0.0 以降が必要です。 インストールされているバージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzAccount` を実行して Azure との接続を作成することも必要です。
- Azure コマンド ライン インターフェイス (CLI) コマンドを使用してこの記事のタスクを実行する場合は、[Azure Cloud Shell](https://shell.azure.com/bash) でコマンドを実行するか、お使いのコンピューターから CLI を実行してください。 このチュートリアルには、Azure CLI のバージョン 2.0.31 以降が必要です。 インストールされているバージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。 Azure CLI をローカルで実行している場合、`az login` を実行して Azure との接続を作成することも必要です。

Azure へのログインまたは接続に使用するアカウントは、[ネットワークの共同作業者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)ロール、または[ネットワーク インターフェイスのアクセス許可](virtual-network-network-interface.md#permissions)の一覧で示されている適切なアクセス許可を割り当てられた[カスタム ロール](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に、割り当てられている必要があります。

## <a name="add-ip-addresses"></a>IP アドレスを追加する

[Azure の制限](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)に関する記事で示されている制限内であれば、必要なだけいくつでも、[プライベート](#private)および[パブリック](#public)な [IPv4](#ipv4) アドレスをネットワーク インターフェイスに追加できます。 既存のネットワーク インターフェイスの 1 つの[セカンダリ IP 構成](#secondary)に、プライベート IPv6 アドレスを追加できます (既存のセカンダリ IP 構成がない場合のみ)。 各ネットワーク インターフェイスには、IPv6 プライベート アドレスを 1 つしか含めることができません。 必要に応じて、パブリック IPv6 アドレスを IPv6 ネットワーク インターフェイス構成に追加できます。 IPv6 アドレスの使用の詳細については、「[IPv6](#ipv6)」を参照してください。

1. Azure Portal 上部に "*リソースの検索*" というテキストが表示されたボックスがあります。そこに "*ネットワーク インターフェイス*"と入力します。 検索結果に **[ネットワーク インターフェイス]** が表示されたら、それを選択します。
2. IPv4 アドレスを追加するネットワーク インターフェイスを一覧から選択します。
3. **[設定]** で **[IP 構成]** を選択します。
4. **[IP 構成]** の **[+ 追加]** を選択します。
5. 次のように指定し、 **[OK]** を選択します。

   |設定|必須|詳細|
   |---|---|---|
   |名前|はい|ネットワーク インターフェイスについて一意である必要があります|
   |Type|はい|IP 構成を既存のネットワーク インターフェイスに追加しており、各ネットワーク インターフェイスには必ず [プライマリ](#primary) IP 構成があるため、選ぶことができるオプションは **セカンダリ** のみです。|
   |プライベート IP アドレスの割り当て方法|はい|[**動的**](#dynamic):Azure により、ネットワーク インターフェイスがデプロイされているサブネット アドレスの範囲で次に利用可能なアドレスが割り当てられます。 [**静的**](#static):ネットワーク インターフェイスがデプロイされているサブネット アドレスの範囲で未使用のアドレスを自分で割り当てます。|
   |パブリック IP アドレス|いいえ|**無効:** パブリック IP アドレス リソースは現在 IP 構成に関連付けられていません。 **有効:** 既存の IPv4 パブリック IP アドレスを選択するか、新しく作成します。 パブリック IP アドレスの作成方法については、「[Public IP addresses](virtual-network-public-ip-address.md#create-a-public-ip-address)」(パブリック IP アドレス) の記事をご覧ください。|
6. 「[VM オペレーティング システムに IP アドレスを追加する](virtual-network-multiple-ip-addresses-portal.md#os-config)」の手順に従って、セカンダリ プライベート IP アドレスを仮想マシンのオペレーティング システムに手動で追加します。 仮想マシンのオペレーティング システムに IP アドレスを手動で追加する前の特別な考慮事項については、「[プライベート](#private)」を参照してください。 仮想マシンのオペレーティング システムにパブリック IP アドレスは追加しないでください。

**コマンド**

|ツール|コマンド|
|---|---|
|CLI|[az network nic ip-config create](/cli/azure/network/nic/ip-config)|
|PowerShell|[Add-AzNetworkInterfaceIpConfig](/powershell/module/az.network/add-aznetworkinterfaceipconfig)|

## <a name="change-ip-address-settings"></a>IP アドレス設定を変更する

IPv4 アドレスの割り当て方法の変更、静的 IPv4 アドレスの変更、またはネットワーク インターフェイスに割り当てられたパブリック IP アドレスの変更が必要になることがあります。 仮想マシンのセカンダリ ネットワーク インターフェイスに関連付けられているセカンダリ IP 構成のプライベート IPv4 アドレスを変更する場合は (詳細については、[プライマリとセカンダリのネットワーク インターフェイス](virtual-network-network-interface-vm.md)に関する記事を参照)、仮想マシンを停止 (割り当て解除) 状態にしてから、次の手順を実行します。

1. Azure Portal 上部に "*リソースの検索*" というテキストが表示されたボックスがあります。そこに "*ネットワーク インターフェイス*"と入力します。 検索結果に **[ネットワーク インターフェイス]** が表示されたら、それを選択します。
2. IP アドレスの設定を表示または変更するネットワーク インターフェイスを一覧から選択します。
3. **[設定]** で **[IP 構成]** を選択します。
4. 変更する IP 構成を一覧から選択します。
5. 「[IP 構成の追加](#add-ip-addresses)」の手順 5 に記載された設定に関する情報を使用して、必要に応じて設定を変更します。
6. **[保存]** を選択します。

>[!NOTE]
>プライマリ ネットワーク インターフェイスに複数の IP 構成があるときに、プライマリ IP 構成のプライベート IP アドレスを変更すると、Windows でプライマリとセカンダリの IP アドレスをネットワーク インターフェイスに手動で再割り当てする必要があります (Linux では必要ありません)。 オペレーティング システム内で IP アドレスをネットワーク インターフェイスに手動で割り当てる方法については、[仮想マシンへの複数の IP アドレスの割り当て](virtual-network-multiple-ip-addresses-portal.md#os-config)に関するページをご覧ください。 仮想マシンのオペレーティング システムに IP アドレスを手動で追加する前の特別な考慮事項については、[プライベート](#private) IP アドレスをご覧ください。 仮想マシンのオペレーティング システムにパブリック IP アドレスは追加しないでください。

**コマンド**

|ツール|コマンド|
|---|---|
|CLI|[az network nic ip-config update](/cli/azure/network/nic/ip-config)|
|PowerShell|[Set-AzNetworkInterfaceIpConfig](/powershell/module/az.network/set-aznetworkinterfaceipconfig)|

## <a name="remove-ip-addresses"></a>IP アドレスを削除する

ネットワーク インターフェイスから[プライベート](#private) IP アドレスと[パブリック](#public) IP アドレスを削除することはできますが、ネットワーク インターフェイスには少なくとも 1 つのプライベート IPv4 アドレスが常に割り当てられている必要があります。

1. Azure Portal 上部に "*リソースの検索*" というテキストが表示されたボックスがあります。そこに "*ネットワーク インターフェイス*"と入力します。 検索結果に **[ネットワーク インターフェイス]** が表示されたら、それを選択します。
2. IP アドレスを削除するネットワーク インターフェイスを一覧から選択します。
3. **[設定]** で **[IP 構成]** を選択します。
4. 削除する [セカンダリ](#secondary) IP 構成を右クリックし ([プライマリ](#primary)構成を削除することはできません)、 **[削除]** 、 **[はい]** の順に選択して、削除を確定します。 構成にパブリック IP アドレスが関連付けられていた場合、リソースの関連付けが IP 構成から解除されますが、リソースは削除されません。

**コマンド**

|ツール|コマンド|
|---|---|
|CLI|[az network nic ip-config delete](/cli/azure/network/nic/ip-config)|
|PowerShell|[Remove-AzNetworkInterfaceIpConfig](/powershell/module/az.network/remove-aznetworkinterfaceipconfig)|

## <a name="ip-configurations"></a>IP 構成

ネットワーク インターフェイスに割り当てられている 1 つまたは複数の IP 構成に、[プライベート](#private) IP アドレスと (必要に応じて) [パブリック](#public) IP アドレスを割り当てます。 IP 構成には 2 つの種類があります。

### <a name="primary"></a>プライマリ

各ネットワーク インターフェイスには、1 つのプライマリ IP 構成が割り当てられます。 プライマリ IP 構成は次のようなものです。

- [プライベート](#private) [IPv4](#ipv4) アドレスが割り当てられます。 プライマリ IP 構成にプライベート [IPv6](#ipv6) アドレスを割り当てることはできません。
- [パブリック](#public) IPv4 アドレスを割り当てることもできます。 プライマリ (IPv4) IP 構成に、パブリック IPv6 アドレスを割り当てることはできません。 

### <a name="secondary"></a>セカンダリ

プライマリ IP 構成に加えて、0 個以上のセカンダリ IP 構成をネットワーク インターフェイスに割り当てることができます。 セカンダリ IP 構成は次のようなものです。

- プライベート IPv4 アドレスまたは IPv6 アドレスが割り当てられている必要があります。 アドレスが IPv6 の場合、ネットワーク インターフェイスに割り当てることができるセカンダリ IP 構成は 1 つだけです。 アドレスが IPv4 の場合、ネットワーク インターフェイスには複数のセカンダリ IP 構成を割り当てることができます。 ネットワーク インターフェイスに割り当てることができるプライベートとパブリックの IPv4 アドレスの数の詳細については、[Azure の制限](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)に関する記事をご覧ください。
- パブリック IPv4 または IPv6 アドレスを割り当てることもできます。 ネットワーク インターフェイスに複数の IPv4 アドレスを割り当てると、次のようなシナリオで役立ちます。
  - 異なる IP アドレスと TLS/SSL 証明書を持つ複数のウェブサイトやサービスを、1 つのサーバーでホストする。
  - 仮想マシンがファイアウォールやロード バランサーのような、ネットワーク仮想アプライアンスとして機能する。
  - 任意のネットワーク インターフェイスの任意のプライベート IPv4 アドレスを、Azure Load Balancer のバックエンド プールに追加できる。 以前は、プライマリ ネットワーク インターフェイスのプライマリ IPv4 アドレスしか、バックエンド プールに追加できませんでした。 複数の IPv4 構成の負荷分散方法の詳細については、「[Azure Portal を使用した複数の IP 構成での負荷分散](../load-balancer/load-balancer-multiple-ip.md?toc=%2fazure%2fvirtual-network%2ftoc.json)」を参照してください。 
  - ネットワーク インターフェイスに割り当てられている 1 つの IPv6 アドレスを負荷分散できる。 プライベート IPv6 アドレスに負荷分散する方法の詳細については、「[Azure Load Balancer の IPv6 の概要](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)」を参照してください。

## <a name="address-types"></a>アドレスの種類

[IP 構成](#ip-configurations)には、次の種類の IP アドレスを割り当てることができます。

### <a name="private"></a>プライベート

プライベート [IPv4](#ipv4) または IPv6 アドレスにより、仮想マシンは、仮想ネットワーク内または他の接続されたネットワーク内の他のリソースと通信できます。 

既定では、Azure DHCP サーバーにより、Azure ネットワーク インターフェイスの[プライマリ IP 構成](#primary)のプライベート IPv4 アドレスが、仮想マシンのオペレーティング システム内のネットワーク インターフェイスに割り当てられます。 必要がない限り、仮想マシンのオペレーティング システム内のネットワーク インターフェイスの IP アドレスを手動で設定しないでください。

> [!WARNING]
> 仮想マシンのオペレーティング システム内でネットワーク インターフェイスのプライマリ IP アドレスとして設定されている IPv4 アドレスが、Azure 内で仮想マシンにアタッチされているプライマリ ネットワーク インターフェイスのプライマリ IP 構成に割り当てられたプライベート IPv4 アドレスと異なる場合は、仮想マシンへの接続が失われます。

ただし、仮想マシンのオペレーティング システム内のネットワーク インターフェイスの IP アドレスを手動で設定することが必要な場合もあります。 たとえば、Azure の仮想マシンに複数の IP アドレスを追加するときは、Windows オペレーティング システムのプライマリとセカンダリの IP アドレスを、手動で設定する必要があります。 Linux 仮想マシンの場合、セカンダリ IP アドレスを手動で設定することだけが必要な場合があります。 詳細については、「[VM オペレーティング システムに IP アドレスを追加する](virtual-network-multiple-ip-addresses-portal.md#os-config)」を参照してください。 IP 構成に割り当てられているアドレスを変更する必要がある場合は、次のようにすることをお勧めします。

1. 仮想マシンが Azure の DHCP サーバーからアドレスを受け取っていることを確認してください。 確認できたら、IP アドレスの割り当てをオペレーティング システム内の DHCP に戻してから仮想マシンを再起動します。
2. 仮想マシンを停止します (割り当て解除)。
3. Azure 内で IP 構成の IP アドレスを変更します。
4. 仮想マシンを開始します。
5. オペレーティング システム内のセカンダリ IP アドレス (および Windows 内のプライマリ IP アドレス) を、Azure での設定と一致するように、[手動で構成](virtual-network-multiple-ip-addresses-portal.md#os-config)します。

この手順に従うと、Azure 内と、仮想マシンのオペレーティング システム内で、ネットワーク インターフェイスに割り当てられるプライベート IP アドレスは同じままです。 サブスクリプションのどの仮想マシンに、オペレーティング システム内で IP アドレスを手動設定したかを追跡するには、Azure の[タグ](../azure-resource-manager/management/tag-resources.md)を仮想マシンに追加することを検討します。 たとえば、"IP address assignment: Static" のようなタグを使います。 このようにすると、オペレーティング システムで IP アドレスを手動設定した仮想マシンを、サブスクリプション内で簡単に見つけることができます。

仮想マシンが同じ仮想ネットワーク内または接続された仮想ネットワーク内の他のリソースと通信できるようになるだけでなく、プライベート IP アドレスを使うと、仮想マシンはインターネットに通信を送信することもできます。 送信接続は、Azure によって予測できないパブリック IP アドレスに変換されたソース ネットワーク アドレスです。 Azure からインターネットへの送信接続の詳細については、[Azure からインターネットへの送信接続](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事を参照してください。 インターネットから仮想マシンのプライベート IP アドレスへの受信通信はできません。 予測可能なパブリック IP アドレスが送信接続に必要な場合は、ネットワーク インターフェイスへのパブリック IP アドレス リソースを関連付けます。

### <a name="public"></a>パブリック

パブリック IP アドレス リソースから割り当てられたパブリック IP アドレスにより、インターネットから仮想マシンへの受信接続が可能になります。 インターネットへの送信接続では、予測できる IP アドレスが使われます。 詳細については、「[Azure の送信用接続の詳細](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)」を参照してください。 IP 構成にパブリック IP アドレスを割り当てることができますが、必須ではありません。 パブリック IP アドレス リソースを関連付けて仮想マシンにパブリック IP アドレスを割り当てない場合でも、仮想マシンはインターネットに対して送信通信を実行できます。 その場合、プライベート IP アドレスは、Azure によって予測できないパブリック IP アドレスに変換されたソース ネットワーク アドレスになります。 パブリック IP アドレス リソースの詳細については、[パブリック IP アドレス リソース](virtual-network-public-ip-address.md)に関するページをご覧ください。

ネットワーク インターフェイスに割り当てることができるプライベート IP アドレスおよびパブリック IP アドレスの数には制限があります。 詳しくは、[Azure の制限](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)に関する記事をご覧ください。

> [!NOTE]
> Azure は、仮想マシンのプライベート IP アドレスをパブリック IP アドレスに変換します。 その結果、仮想マシンのオペレーティング システムは割り当てられているパブリック IP アドレスを認識しないので、オペレーティング システム内でパブリック IP アドレスを手動で割り当てる必要はありません。

## <a name="assignment-methods"></a>割り当て方法

パブリック IP アドレスとプライベート IP アドレスは、次の方法のうち 1 つを使って割り当てます。

### <a name="dynamic"></a>動的

既定では、動的なプライベート IPv4 および IPv6 (オプション) アドレスが割り当てられます。

- **パブリックのみ**:Azure により、各 Azure リージョンに固有の範囲からアドレスが割り当てられます。 各リージョンにどの範囲が割り当てられているかについて詳しくは、[Microsoft Azure データセンターの IP 範囲](https://www.microsoft.com/download/details.aspx?id=41653)に関するページをご覧ください。 仮想マシンがいったん停止した (割り当て解除された) 後に、再び始動すると、アドレスが変更になることがあります。 どちらの割り当て方法を使っても、パブリック IPv6 アドレスを IP 構成に割り当てることはできません。
- **プライベートのみ**:各サブネット アドレス範囲の先頭から 4 つのアドレスは Azure によって予約されており、それらのアドレスが割り当てられることはありません。 サブネット アドレス範囲でその次に利用可能なアドレスが Azure によってリソースに割り当てられます。 たとえば、サブネットのアドレス範囲が 10.0.0.0/16 であり、10.0.0.0.4 から 10.0.0.14 のアドレスが既に割り当て済みである場合 (.0 から .3 は予約済み)、Azure によってリソースに 10.0.0.15 が割り当てられます。 動的割り当てが既定の割り当て方法となります。 いったん割り当てられた動的 IP アドレスが解放されるのは、ネットワーク インターフェイスが削除されるか、同じ仮想ネットワーク内の別のサブネットに割り当てられた場合、または割り当て方法が "静的" に変更された際に異なる IP アドレスが指定された場合に限られます。 既定では、割り当て方法を "動的" から"静的" に変更すると、動的に割り当てられていた以前のアドレスが Azure によって静的アドレスとして割り当てられます。 

### <a name="static"></a>静的

必要に応じて、静的なパブリックまたはプライベート IPv4 または IPv6 アドレスを IP 構成に割り当てることができます。 Azure での静的パブリック IPv4 アドレスの割り当ての詳細については、「[パブリック IP アドレスの作成、変更、削除](virtual-network-public-ip-address.md)」を参照してください。

- **パブリックのみ**:Azure により、各 Azure リージョンに固有の範囲からアドレスが割り当てられます。 Azure [Public](https://www.microsoft.com/download/details.aspx?id=56519)、[US Government](https://www.microsoft.com/download/details.aspx?id=57063)、[China](https://www.microsoft.com/download/details.aspx?id=57062)、および [Germany](https://www.microsoft.com/download/details.aspx?id=57064) クラウドの範囲 (プレフィックス) の一覧をダウンロードできます。 このアドレスは、割り当てられたパブリック IP アドレス リソースが削除されるか、または割り当て方法が "動的" に変更されない限り、変更されません。 パブリック IP アドレス リソースが IP 構成に関連付けられている場合、その割り当て方法を変更するには、事前に IP 構成への関連付けを解除しておく必要があります。
- **プライベートのみ**:サブネットのアドレス範囲から自分でアドレスを選択して割り当てます。 割り当てるアドレスは、サブネット アドレス範囲内で、かつ現時点でそのサブネット内の他のどのリソースにも割り当てられていなければ、そのサブネット範囲の先頭 4 つのアドレスを除き、どれでもかまいません。 静的アドレスが解放されるのは、ネットワーク インターフェイスが削除された場合だけです。 割り当て方法を静的に変更した場合、サブネット アドレス範囲の先頭から空いている順に割り当てられるのではなく、それまで割り当てられていた動的 IP アドレスが静的アドレスとして Azure により動的に割り当てられます。 同じ仮想ネットワーク内の異なるサブネットにネットワーク インターフェイスを割り当てた場合にも、アドレスは変化します。ただし、ネットワーク インターフェイスを別のサブネットに割り当てるためには、最初に割り当て方法を "静的" から "動的" に変更しておく必要があります。 ネットワーク インターフェイスを別のサブネットに割り当てた後、割り当て方法を "静的" に変更し、新しいサブネットのアドレス範囲から IP アドレスを割り当てることができます。

## <a name="ip-address-versions"></a>IP アドレスのバージョン

アドレスを割り当てるときは、次のバージョンを指定できます。

### <a name="ipv4"></a>IPv4

各ネットワーク インターフェイスには、[プライベート](#private) [IPv4](#ipv4) アドレスが割り当てられた[プライマリ](#primary) IP 構成が 1 つ必要です。 1 つ以上の[セカンダリ](#secondary) IP 構成を追加することができ、各構成には IPv4 プライベート IP アドレスと、必要に応じて IPv4 [パブリック](#public) IP アドレスを割り当てます。

### <a name="ipv6"></a>IPv6

ネットワーク インターフェイスの 1 つのセカンダリ IP 構成には、プライベート [IPv6](#ipv6) アドレスを 1 つ割り当てることができます (割り当てなくてもかまいません)。 ネットワーク インターフェイスは、既存のセカンダリ IP 構成を使うことはできません。 各ネットワーク インターフェイスには、IPv6 プライベート アドレスを 1 つしか含めることができません。 必要に応じて、パブリック IPv6 アドレスを IPv6 ネットワーク インターフェイス構成に追加できます。

> [!NOTE]
> ポータルを使用して IPv6 アドレスを割り当てたネットワーク インターフェイスを作成することはできますが、ポータルを使用して既存のネットワーク インターフェイスを新規または既存の仮想マシンに追加することはできません。 プライベート IPv6 アドレスが割り当てられたネットワーク インターフェイスを作成し、仮想マシンを作成するときにそのネットワーク インターフェイスをアタッチするには、PowerShell または Azure CLI を使用します。 プライベート IPv6 アドレスが割り当てられたネットワーク インターフェイスを既存の仮想マシンにアタッチすることはできません。 どのツール (ポータル、CLI、PowerShell) を使っても、仮想マシンにアタッチされたネットワーク インターフェイスの IP 構成にプライベート IPv6 アドレスを追加することはできません。

## <a name="skus"></a>SKU

パブリック IP アドレスは基本または標準 SKU で作成されます。 SKU の違いについて詳しくは、[パブリック IP アドレスの管理](virtual-network-public-ip-address.md)に関するページをご覧ください。

> [!NOTE]
> 標準 SKU のパブリック IP アドレスを仮想マシンのネットワーク インターフェイスに割り当てるときは、[ネットワーク セキュリティ グループ](./network-security-groups-overview.md#network-security-groups)で、特定のトラフィックを明示的に許可する必要があります。 ネットワーク セキュリティ グループを作成して関連付け、目的のトラフィックを明示的に許可するまで、そのリソースとの通信は失敗します。

## <a name="next-steps"></a>次のステップ
異なる IP 構成で仮想マシンを作成する方法については、次の記事をご覧ください。

|タスク|ツール|
|---|---|
|複数のネットワーク インターフェイスを持つ VM を作成する|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)、[PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|複数の IPv4 アドレスが割り当てられた 1 つの NIC VM の作成|[CLI](virtual-network-multiple-ip-addresses-cli.md)、[PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|プライベート IPv6 アドレスが割り当てられた 1 つの NIC VM の作成 (Azure Load Balancer の背後)|[CLI](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json)、[PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)、[Azure Resource Manager テンプレート](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|