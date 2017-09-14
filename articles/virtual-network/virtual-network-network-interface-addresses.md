---
title: "Azure ネットワーク インターフェイスの IP アドレスの構成 | Microsoft Docs"
description: "ネットワーク インターフェイスのプライベート IP アドレスとパブリック IP アドレスを追加、変更、削除する方法について説明します。"
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
ms.translationtype: HT
ms.sourcegitcommit: b309108b4edaf5d1b198393aa44f55fc6aca231e
ms.openlocfilehash: 17ddb30c87d757176ce9428264135252c02bf713
ms.contentlocale: ja-jp
ms.lasthandoff: 08/15/2017

---

# <a name="add-change-or-remove-ip-addresses-for-an-azure-network-interface"></a>Azure ネットワーク インターフェイスの IP アドレスの追加、変更、削除

ネットワーク インターフェイスのパブリック IP アドレスとプライベート IP アドレスを追加、変更、削除する方法について説明します。 ネットワーク インターフェイスにプライベート IP アドレスを割り当てると、仮想マシンは、Azure 仮想ネットワーク内および接続されたネットワーク内の他のリソースと通信できます。 また、予測できない IP アドレスを使ってインターネットに通信を送信することもできます。 ネットワーク インターフェイスに[パブリック IP アドレス](virtual-network-public-ip-address.md)を割り当てると、インターネットから仮想マシンへの通信を受信できます。 また、予測できる IP アドレスを使って仮想マシンからインターネットに通信を送信することもできます。 詳細については、「[Azure の送信用接続の詳細](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)」を参照してください。 

ネットワーク インターフェイスを作成、変更、または削除する必要がある場合は、「[ネットワーク インターフェイスの作成、変更、削除](virtual-network-network-interface.md)」を参照してください。 仮想マシンのネットワーク インターフェイスを追加または削除する必要がある場合は、「[仮想マシンに対するネットワーク インターフェイスの追加と削除](virtual-network-network-interface-vm.md)」を参照してください。 


## <a name="before-you-begin"></a>開始する前に

この記事のセクションに記載された手順を始める前に、次のタスクを完了してください。

- [Azure の制限](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)に関する記事で、パブリック IP アドレスおよびプライベート IP アドレスの制限について確認します。
- Azure アカウントを使って、Azure [Portal](https://portal.azure.com)、Azure コマンド ライン インターフェイス (CLI)、または Azure PowerShell にログインします。 まだ Azure アカウントを持っていない場合は、[無料試用版アカウント](https://azure.microsoft.com/free)にサインアップしてください。
- PowerShell コマンドを使用してこの記事のタスクを実行する場合は、[Azure PowerShell をインストールして構成します](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json)。 最新バージョンの Azure PowerShell コマンドレットがインストールされていることを確認してください。 PowerShell コマンドのヘルプとサンプルを表示するには、「`get-help <command> -full`」と入力します。
- Azure コマンド ライン インターフェイス (CLI) のコマンドを使ってこの記事のタスクを実行する場合は、[Azure CLI をインストールして構成します](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)。 最新バージョンの Azure CLI がインストールされていることを確認してください。 CLI コマンドのヘルプを表示するには、「`az <command> --help`」と入力します。 CLI とその前提条件をインストールする代わりに、Azure Cloud Shell を使うことができます。 Azure Cloud Shell は、Azure Portal 内で直接実行できる無料の Bash シェルです。 Azure CLI が事前にインストールされており、アカウントで使用できるように構成されています。 Cloud Shell を使うには、[ポータル](https://portal.azure.com)の上部にある [Cloud Shell**> _**] ボタンをクリックします。

## <a name="add-ip-addresses"></a>IP アドレスを追加する

[Azure の制限](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)に関する記事で示されている制限内であれば、必要なだけいくつでも、[プライベート](#private)および[パブリック](#public)な [IPv4](#ipv4) アドレスをネットワーク インターフェイスに追加できます。 ポータルを使って IPv6 アドレスを既存のネットワーク インターフェイスに追加することはできません (ただし、ネットワーク インターフェイスを作成するときに、ポータルを使ってプライベート IPv6 アドレスを追加することはできます)。 PowerShell または CLI を使って、仮想マシンにアタッチされていない既存のネットワーク インターフェイスの 1 つの[セカンダリ IP 構成](#secondary) に、プライベート IPv6 アドレスを追加できます (既存のセカンダリ IP 構成がない場合のみ)。 どのツールを使っても、ネットワーク インターフェイスにパブリック IPv6 アドレスを追加することはできません。 IPv6 アドレスの使用の詳細については、「[IPv6](#ipv6)」を参照してください。 

1. ご利用のサブスクリプションのネットワーク作成協力者ロール (またはそれ以上) のアクセス許可が割り当てられているアカウントで [Azure Portal](https://portal.azure.com) にログインします。 アカウントへのロールとアクセス許可の割り当てについて詳しくは、「[Azure のロールベースのアクセス制御のための組み込みロール](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)」の記事をご覧ください。
2. Azure Portal 上部に "*リソースの検索*" というテキストが表示されたボックスがあります。そこに "*ネットワーク インターフェイス*" と入力します。 検索結果に **[ネットワーク インターフェイス]** が表示されたら、それをクリックします。
3. 表示される **[ネットワーク インターフェイス]** ブレードで、IPv4 アドレスを追加するネットワーク インターフェイスをクリックします。
4. 選んだネットワーク インターフェイスのブレードの **[設定]** セクションで、**[IP 構成]** をクリックします。
5. IP 構成に対して開くブレードで、**[+ 追加]** をクリックします。
6. 以下の項目を指定してから **[OK]** をクリックして、**[IP 構成の追加]** ブレードを閉じます。

    |設定|必須|詳細|
    |---|---|---|
    |名前|あり|ネットワーク インターフェイスについて一意である必要があります|
    |型|あり|IP 構成を既存のネットワーク インターフェイスに追加しており、各ネットワーク インターフェイスには必ず[プライマリ](#primary) IP 構成があるため、選ぶことができるオプションは **[セカンダリ]** のみです。|
    |プライベート IP アドレスの割り当て方法|あり|[**動的**](#dynamic)アドレスは、仮想マシンが停止 (割り当て解除) 状態になってから再起動した場合に変化することがあります。 ネットワーク インターフェイスが接続されているサブネットのアドレス空間から使用可能なアドレスが自動的に割り当てられます。 [**静的**](#static)アドレスは、ネットワーク インターフェイスが削除されるまで解放されません。 別の IP 構成で現在使用されていないサブネット アドレス空間の範囲から IP アドレスを指定します。|
    |パブリック IP アドレス|なし|**[無効]:** パブリック IP アドレス リソースは現在 IP 構成に関連付けられていません。 **[有効]:** 既存の IPv4 パブリック IP アドレスを選択するか、新しく作成します。 パブリック IP アドレスの作成方法については、「[Public IP addresses](virtual-network-public-ip-address.md#create-a-public-ip-address)」(パブリック IP アドレス) の記事をご覧ください。|
7. 「[VM オペレーティング システムに IP アドレスを追加する](virtual-network-multiple-ip-addresses-portal.md#os-config)」の手順に従って、セカンダリ プライベート IP アドレスを仮想マシンのオペレーティング システムに手動で追加します。 仮想マシンのオペレーティング システムに IP アドレスを手動で追加する前の特別な考慮事項については、「[プライベート](#private)」を参照してください。 仮想マシンのオペレーティング システムにパブリック IP アドレスは追加しないでください。

**コマンド**

|ツール|コマンド|
|---|---|
|CLI|[az network nic ip-config create](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[Add-AzureRmNetworkInterfaceIpConfig](/powershell/module/azurerm.network/add-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="change-ip-address-settings"></a>IP アドレス設定を変更する

IPv4 アドレスの割り当て方法の変更、静的 IPv4 アドレスの変更、またはネットワーク インターフェイスに割り当てられたパブリック IP アドレスの変更が必要になることがあります。 仮想マシンのセカンダリ ネットワーク インターフェイスに関連付けられているセカンダリ IP 構成のプライベート IPv4 アドレスを変更する場合は (詳細については、[プライマリとセカンダリのネットワーク インターフェイス](virtual-network-network-interface-vm.md#about)に関する記事を参照)、仮想マシンを停止 (割り当て解除) 状態にしてから、次の手順を実行します。 

1. ご利用のサブスクリプションのネットワーク作成協力者ロール (またはそれ以上) のアクセス許可が割り当てられているアカウントで [Azure Portal](https://portal.azure.com) にログインします。 アカウントへのロールとアクセス許可の割り当てについて詳しくは、「[Azure のロールベースのアクセス制御のための組み込みロール](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)」の記事をご覧ください。
2. Azure Portal 上部に "*リソースの検索*" というテキストが表示されたボックスがあります。そこに "*ネットワーク インターフェイス*" と入力します。 検索結果に **[ネットワーク インターフェイス]** が表示されたら、それをクリックします。
3. 表示される **[ネットワーク インターフェイス]** ブレードで、IP アドレス設定を表示または変更するネットワーク インターフェイスをクリックします。
4. 選んだネットワーク インターフェイスのブレードの **[設定]** セクションで、**[IP 構成]** をクリックします。
5. IP 構成に対して開いたブレードで、変更する IP 構成を一覧からクリックします。
6. この記事のセクション「[IP 構成の追加](#create-ip-config)」の手順 6. に記載された設定に関する情報を使用して、必要に応じて設定を変更します。 IP 構成を変更したら、**[保存]** をクリックしてブレードを閉じます。

>[!NOTE]
>プライマリ ネットワーク インターフェイスに複数の IP 構成があるときに、プライマリ IP 構成のプライベート IP アドレスを変更すると、Windows でプライマリとセカンダリの IP アドレスをネットワーク インターフェイスに手動で再割り当てする必要があります (Linux では必要ありません)。 オペレーティング システム内で IP アドレスをネットワーク インターフェイスに手動で割り当てるには、「[仮想マシンに複数の IP アドレスを割り当てる](virtual-network-multiple-ip-addresses-portal.md#os-config)」を参照してください。 仮想マシンのオペレーティング システムに IP アドレスを手動で追加する前の特別な考慮事項については、「[プライベート](#private)」を参照してください。 仮想マシンのオペレーティング システムにパブリック IP アドレスは追加しないでください。

**コマンド**

|ツール|コマンド|
|---|---|
|CLI|[az network nic ip-config update](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRMNetworkInterfaceIpConfig](/powershell/module/azurerm.network/set-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="remove-ip-addresses"></a>IP アドレスを削除する

ネットワーク インターフェイスから[プライベート](#private) IP アドレスと[パブリック](#public) IP アドレスを削除することはできますが、ネットワーク インターフェイスには少なくとも 1 つのプライベート IPv4 アドレスが常に割り当てられている必要があります。

1. ご利用のサブスクリプションのネットワーク作成協力者ロール (またはそれ以上) のアクセス許可が割り当てられているアカウントで [Azure Portal](https://portal.azure.com) にログインします。 アカウントへのロールとアクセス許可の割り当てについて詳しくは、「[Azure のロールベースのアクセス制御のための組み込みロール](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)」の記事をご覧ください。
2. Azure Portal 上部に "*リソースの検索*" というテキストが表示されたボックスがあります。そこに "*ネットワーク インターフェイス*" と入力します。 検索結果に **[ネットワーク インターフェイス]** が表示されたら、それをクリックします。
3. 表示される **[ネットワーク インターフェイス]** ブレードで、IP アドレスを削除するネットワーク インターフェイスをクリックします。
4. 選んだネットワーク インターフェイスのブレードの **[設定]** セクションで、**[IP 構成]** をクリックします。
5. 削除する[セカンダリ](#secondary) IP 構成を右クリックし ([プライマリ](#primary)構成を削除することはできません)、**[削除]**、**[はい]** の順にクリックして、削除を確定します。 構成にパブリック IP アドレスが関連付けられていた場合、リソースの関連付けが IP 構成から解除されますが、リソースは削除されません。
6. **[IP 構成]** ブレードを閉じます。

**コマンド**

|ツール|コマンド|
|---|---|
|CLI|[az network nic ip-config delete](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|PowerShell|[Remove-AzureRmNetworkInterfaceIpConfig](/powershell/module/azurerm.network/remove-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="ip-configurations"></a>IP 構成

ネットワーク インターフェイスに割り当てられている 1 つまたは複数の IP 構成に、[プライベート](#private) IP アドレスと (必要に応じて) [パブリック](#public) IP アドレスを割り当てます。 IP 構成には 2 つの種類があります。

### <a name="primary"></a>プライマリ

各ネットワーク インターフェイスには、1 つのプライマリ IP 構成が割り当てられます。 プライマリ IP 構成は次のようなものです。

- [プライベート](#private) [IPv4](#ipv4) アドレスが割り当てられます。 プライマリ IP 構成にプライベート [IPv6](#ipv6) アドレスを割り当てることはできません。
- [パブリック](#public) IPv4 アドレスを割り当てることもできます。 プライマリ IP 構成またはセカンダリ IP 構成に、パブリック IPv6 アドレスを割り当てることはできません。 ただし、Azure Load Balancer にパブリック IPv6 アドレスを割り当てて、仮想マシンのプライベート IPv6 アドレスにトラフィックを負荷分散することはできます。 詳細については、[IPv6 の詳細と制限](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#details-and-limitations)に関する記事をご覧ください。

### <a name="secondary"></a>セカンダリ

プライマリ IP 構成に加えて、0 個以上のセカンダリ IP 構成をネットワーク インターフェイスに割り当てることができます。 セカンダリ IP 構成は次のようなものです。

- プライベート IPv4 アドレスまたは IPv6 アドレスが割り当てられている必要があります。 アドレスが IPv6 の場合、ネットワーク インターフェイスに割り当てることができるセカンダリ IP 構成は 1 つだけです。 アドレスが IPv4 の場合、ネットワーク インターフェイスには複数のセカンダリ IP 構成を割り当てることができます。 ネットワーク インターフェイスに割り当てることができるプライベートとパブリックの IPv4 アドレスの数の詳細については、[Azure の制限](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)に関する記事をご覧ください。  
- プライベート IP アドレスが IPv4 の場合、パブリック IPv4 アドレスを割り当てることもできます。 プライベート IP アドレスが IPv6 の場合、パブリック IPv4 または IPv6 アドレスを IP 構成に割り当てることはできません。 ネットワーク インターフェイスに複数の IP アドレスを割り当てると、次のようなシナリオで役立ちます。
    - 異なる IP アドレスと SSL 証明書を持つ複数のウェブサイトやサービスを、1つのサーバーでホストする。
    - 仮想マシンがファイアウォールやロード バランサーのような、ネットワーク仮想アプライアンスとして機能する。
    - 任意のネットワーク インターフェイスの任意のプライベート IPv4 アドレスを、Azure Load Balancer のバックエンド プールに追加できる。 以前は、プライマリ ネットワーク インターフェイスのプライマリ IPv4 アドレスしか、バックエンド プールに追加できませんでした。 複数の IPv4 構成の負荷分散方法の詳細については、「[Azure Portal を使用した複数の IP 構成での負荷分散](../load-balancer/load-balancer-multiple-ip.md?toc=%2fazure%2fvirtual-network%2ftoc.json)」を参照してください。 
    - ネットワーク インターフェイスに割り当てられている 1 つの IPv6 アドレスを負荷分散できる。 プライベート IPv6 アドレスに負荷分散する方法の詳細については、「[Azure Load Balancer の IPv6 の概要](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)」を参照してください。


## <a name="address-types"></a>アドレスの種類

[IP 構成](#ip-configurations)には、次の種類の IP アドレスを割り当てることができます。

### <a name="private"></a>プライベート

プライベート [IPv4](#ipv4) アドレスにより、仮想マシンは、仮想ネットワーク内または他の接続されたネットワーク内の他のリソースと通信できます。 次の場合を除き、仮想マシンはプライベート [IPv6](#ipv6) アドレスで送信または受信の通信を行うことはできません。 仮想マシンは、IPv6 アドレスを使って Azure Load Balancer と通信することはできます。 詳細については、[IPv6 の詳細と制限](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#details-and-limitations)に関する記事をご覧ください。 

既定では、Azure DHCP サーバーは、ネットワーク インターフェイスの[プライマリ IP 構成](#primary)のプライベート IPv4 アドレスを、仮想マシンのオペレーティング システム内のネットワーク インターフェイスに割り当てます。 必要がない限り、仮想マシンのオペレーティング システム内のネットワーク インターフェイスの IP アドレスを手動で設定しないでください。 

> [!WARNING]
> 仮想マシンのオペレーティング システム内でネットワーク インターフェイスのプライマリ IP アドレスとして設定されている IPv4 アドレスが、Azure 内で仮想マシンにアタッチされているプライマリ ネットワーク インターフェイスのプライマリ IP 構成に割り当てられたプライベート IPv4 アドレスと異なる場合は、仮想マシンへの接続が失われます。

ただし、仮想マシンのオペレーティング システム内のネットワーク インターフェイスの IP アドレスを手動で設定することが必要な場合もあります。 たとえば、Azure の仮想マシンに複数の IP アドレスを追加するときは、Windows オペレーティング システムのプライマリとセカンダリの IP アドレスを、手動で設定する必要があります。 Linux 仮想マシンの場合、セカンダリ IP アドレスを手動で設定することだけが必要な場合があります。 詳細については、「[VM オペレーティング システムに IP アドレスを追加する](virtual-network-multiple-ip-addresses-portal.md#os-config)」を参照してください。 オペレーティング システム内の IP アドレスを手動で設定するときは、常に (動的ではなく) 静的な割り当て方法を使ってネットワーク インターフェイスの IP 構成にアドレスを割り当てることをお勧めします。 静的な方法を使ってアドレスを割り当てると、Azure 内でアドレスが変化しないことが保証されます。 IP 構成に割り当てられているアドレスを変更する必要がある場合は、次のようにすることをお勧めします。

1. 仮想マシンが Azure の DHCP サーバーからアドレスを受け取るようにするために、IP アドレスの割り当てをオペレーティング システム内の DHCP に戻してから仮想マシンを再起動します。
2. 仮想マシを停止します (割り当て解除)。
3. Azure 内で IP 構成の IP アドレスを変更します。
4. 仮想マシンを開始します。
5. オペレーティング システム内のセカンダリ IP アドレス (および Windows 内のプライマリ IP アドレス) を、Azure での設定と一致するように、[手動で構成](virtual-network-multiple-ip-addresses-portal.md#os-config)します。
 
この手順に従うと、Azure 内と、仮想マシンのオペレーティング システム内で、ネットワーク インターフェイスに割り当てられるプライベート IP アドレスは同じままです。 サブスクリプションのどの仮想マシンに、オペレーティング システム内で IP アドレスを手動設定したかを追跡するには、Azure の[タグ](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#tags)を仮想マシンに追加することを検討します。 たとえば、"IP address assignment: Static" のようなタグを使います。 このようにすると、オペレーティング システムで IP アドレスを手動設定した仮想マシンを、サブスクリプション内で簡単に見つけることができます。

仮想マシンが同じ仮想ネットワーク内または接続された仮想ネットワーク内の他のリソースと通信できるようになるだけでなく、プライベート IP アドレスを使うと、仮想マシンはインターネットに通信を送信することもできます。 送信接続は、Azure によって予測できないパブリック IP アドレスに変換されたソース ネットワーク アドレスです。 Azure からインターネットへの送信接続の詳細については、[Azure からインターネットへの送信接続](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事を参照してください。 インターネットから仮想マシンのプライベート IP アドレスへの受信通信はできません。

### <a name="public"></a>パブリック

パブリック IP アドレスにより、インターネットから仮想マシンへの受信接続が可能になります。 インターネットへの送信接続では、予測できる IP アドレスが使われます。 詳細については、「[Azure の送信用接続の詳細](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)」を参照してください。 IP 構成にパブリック IP アドレスを割り当てることができますが、必須ではありません。 仮想マシンにパブリック IP アドレスを割り当てない場合でも、プライベート IP アドレスを使ってインターネットに送信通信できます。 パブリック IP アドレスの詳細については、[パブリック IP アドレス](virtual-network-public-ip-address.md)に関する記事を参照してください。

ネットワーク インターフェイスに割り当てることができるプライベート IP アドレスおよびパブリック IP アドレスの数には制限があります。 詳しくは、[Azure の制限](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)に関する記事をご覧ください。

> [!NOTE]
> Azure は、仮想マシンのプライベート IP アドレスをパブリック IP アドレスに変換します。 その結果、オペレーティング システムは割り当てられているパブリック IP アドレスを認識しないので、オペレーティング システム内でパブリック IP アドレスを手動で割り当てる必要はありません。

## <a name="assignment-methods"></a>割り当て方法

パブリック IP アドレスとプライベート IP アドレスは、次の方法を使って割り当てます。

### <a name="dynamic"></a>動的

既定では、動的なプライベート IPv4 および IPv6 (オプション) アドレスが割り当てられます。 動的アドレスは、仮想マシンが停止 (割り当て解除) 状態になり、起動された場合、変更される可能性があります。 仮想マシンが存在する期間を通して IPv4 アドレスが変化しないようにするには、静的な方法を使ってアドレスを割り当てます。 プライベート IPv6 アドレスは、動的な割り当て方法を使うことによってのみ割り当てることができます。 どちらの方法を使っても、パブリック IPv6 アドレスを IP 構成に割り当てることはできません。

### <a name="static"></a>静的

静的な方法で割り当てられたアドレスでは、仮想マシンが削除されるまで変化しません。 ネットワーク インターフェイスが存在するサブネットのアドレス空間から、静的なプライベート IPv4 アドレスを IP 構成に手動で割り当てます。 必要に応じて、静的なパブリックまたはプライベート IPv4 アドレスを IP 構成に割り当てることができます。 静的なパブリックまたはプライベート IPv6 アドレスを IP 構成に割り当てることできません。 Azure での静的パブリック IPv4 アドレスの割り当ての詳細については、「[パブリック IP アドレスの作成、変更、削除](virtual-network-public-ip-address.md)」を参照してください。

## <a name="ip-address-versions"></a>IP アドレスのバージョン

アドレスを割り当てるときは、次のバージョンを指定できます。

### <a name="ipv4"></a>IPv4

各ネットワーク インターフェイスには、[プライベート](#private) [IPv4](#ipv4) アドレスが割り当てられた[プライマリ](#primary) IP 構成が 1 つ必要です。 1 つ以上の[セカンダリ](#secondary) IP 構成を追加することができ、各構成には IPv4 プライベート IP アドレスと、必要に応じて IPv4 [パブリック](#public) IP アドレスを割り当てます。

### <a name="ipv6"></a>IPv6

ネットワーク インターフェイスの 1 つのセカンダリ IP 構成には、プライベート [IPv6](#ipv6) アドレスを 1 つ割り当てることができます (割り当てなくてもかまいません)。 ネットワーク インターフェイスは、既存のセカンダリ IP 構成を使うことはできません。 ポータルを使って、IPv6 アドレスが割り当てられた IP 構成を追加することはできません。 PowerShell または CLI を使用して、プライベート IPv6 アドレスが割り当てられた IP 構成を既存のネットワーク インターフェイスに追加します。 ネットワーク インターフェイスを既存の VM にアタッチすることはできません。

> [!NOTE]
> ポータルを使用して IPv6 アドレスを割り当てたネットワーク インターフェイスを作成することはできますが、ポータルを使用して既存のネットワーク インターフェイスを新規または既存の仮想マシンに追加することはできません。 プライベート IPv6 アドレスが割り当てられたネットワーク インターフェイスを作成し、仮想マシンを作成するときにそのネットワーク インターフェイスをアタッチするには、PowerShell または Azure CLI 2.0 を使用します。 プライベート IPv6 アドレスが割り当てられたネットワーク インターフェイスを既存の仮想マシンにアタッチすることはできません。 どのツール (ポータル、CLI、PowerShell) を使っても、仮想マシンにアタッチされたネットワーク インターフェイスの IP 構成にプライベート IPv6 アドレスを追加することはできません。

プライマリ IP 構成またはセカンダリ IP 構成に、パブリック IPv6 アドレスを割り当てることはできません。

## <a name="next-steps"></a>次のステップ
異なる IP 構成で仮想マシンを作成する方法については、次の記事をご覧ください。

|タスク|ツール|
|---|---|
|複数 NIC を持つ VM の作成|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)、[PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|複数の IPv4 アドレスが割り当てられた 1 つの NIC VM の作成|[CLI](virtual-network-multiple-ip-addresses-cli.md)、[PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|プライベート IPv6 アドレスが割り当てられた 1 つの NIC VM の作成 (Azure Load Balancer の背後)|[CLI](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json)、[PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)、[Azure Resource Manager テンプレート](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|

