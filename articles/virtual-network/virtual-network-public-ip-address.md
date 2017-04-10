---
title: "Azure パブリック IP アドレス | Microsoft Docs"
description: "パブリック IP アドレスの作成、変更、削除の方法について説明します。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: bb71abaf-b2d9-4147-b607-38067a10caf6
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/14/2017
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 2c9877f84873c825f96b62b492f49d1733e6c64e
ms.openlocfilehash: 851d806a7e92dafaa5325c032fd3e0a71ce726c2
ms.lasthandoff: 03/15/2017


---

# <a name="public-ip-addresses"></a>パブリック IP アドレス

パブリック IP アドレスとその作成、変更、削除の方法について説明します。 パブリック IP アドレスは、変更可能な独自の設定を備えたリソースです。 パブリック IP アドレスを他の Azure リソースに割り当てることで次のことが可能となります。
- Azure Virtual Machines (VM)、Azure Virtual Machine Scale Sets、Azure VPN Gateway、インターネット接続 Azure Load Balancer などの各種リソースに対する受信インターネット接続。
- ネットワーク アドレス変換 (NAT) されていないインターネットへの送信接続。 たとえば VM は、それ自体のパブリック IP アドレスを取得せずにそのアドレスを Azure によって NAT 変換することで、インターネットに対して送信方向の通信を行うことができます。 Azure リソースからの送信接続の詳細については、[送信用接続の詳細](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事を参照してください。

この記事では、パブリック IP アドレスの使用方法について説明します。 この記事は、Azure Resource Manager デプロイメント モデルを通じてデプロイされたリソースについて記載しています。 クラシック デプロイメント モデルでデプロイされたリソースにパブリック IP アドレスを割り当てることもできますが、Resource Manager を使用した場合とはアドレスの適用方法が異なります。 2 つのモデルの違いについてご不明な点がある場合は、[Azure デプロイメント モデルの概要](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事を参照してください。

以降、この記事の各セクションで、パブリック IP アドレスに関連したすべてのタスクの手順を紹介します。 各セクションの内容は次のとおりです。
- Azure Portal 内でタスクを実行する手順。 これらの手順を行うには、[Azure Portal](http://portal.azure.com) にログインする必要があります。 まだアカウントを持っていない場合は、[無料試用版アカウント](https://azure.microsoft.com/free)にサインアップしてください。
- Azure PowerShell を使用してタスクを実行するためのコマンドとそのコマンド リファレンスへのリンク。 [Azure PowerShell のインストールと構成方法](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事の手順に従い、PowerShell をインストールして構成してください。 PowerShell コマンドのヘルプとサンプルを表示するには、「`get-help <command> -full`」と入力します。
- Azure コマンド ライン インターフェイス (CLI) を使用してタスクを実行するためのコマンドとそのコマンド リファレンスへのリンク。 [Azure CLI 2.0 のインストールと構成の方法](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事の手順に従って、Azure CLI をインストールしてください。 CLI コマンドのヘルプを表示するには、「`az <command> -h`」と入力します。

パブリック IP アドレスには、わずかですが料金が発生します。 料金については、「[IP アドレスの料金](https://azure.microsoft.com/pricing/details/ip-addresses)」ページを参照してください。 サブスクリプション内で使用できるパブリック IP アドレスの数には制限があります。 これらの制限については、[Azure の制限](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)に関する記事を参照してください。 

パブリック IP アドレス リソースを作成、変更、削除するには、以降の各セクションの手順に従ってください。

## <a name="create"></a>パブリック IP アドレスの作成

パブリック IP アドレスを作成するには、次の手順を実行します。
1. ご利用のサブスクリプションのネットワーク作成協力者ロール (またはそれ以上) のアクセス許可が割り当てられているアカウントで [Azure Portal](https://portal.azure.com) にログインします。 アカウントへのロールとアクセス許可の割り当てについて詳しくは、「[Azure のロールベースのアクセス制御のための組み込みロール](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)」の記事を参照してください。
2. Azure Portal 上部に *[リソースの検索]* というテキストが表示されたボックスがあります。そこに「*パブリック IP アドレス*」と入力します。 検索結果に **[パブリック IP アドレス]** が表示されたら、それをクリックします。
3. 表示された **[パブリック IP アドレス]** ブレードの **[+ 追加]** をクリックします。
4. 表示された **[パブリック IP アドレスの作成]** ブレードで以下の設定の値を入力するか選択し、**[作成]** をクリックします。

    |**設定**|必須|**詳細**|
    |---|---|---|
    |**名前**|はい|選択したリソース グループ内で一意となる名前を使用してください。|
    |**IP アドレスの割り当て**|はい|**[動的]:** 動的アドレスの割り当ては、VM の NIC にパブリック IP アドレスが関連付けられた後、VM の初回起動後に行われます。 NIC の接続先 VM が停止 (割り当て解除) された場合、動的アドレスは変化する場合があります。 VM が再起動された場合や、停止されても割り当て解除されなければ、アドレスは変化しません。 **[静的]:** 静的アドレスは、パブリック IP アドレスの作成時に割り当てられます。 VM が停止 (割り当て解除) 状態になっても静的アドレスは変化しません。 アドレスが解放されるのは、NIC が削除されたときだけです。 NIC の作成後に、割り当て方法を変更することができます。|
    |**アイドル タイムアウト (分)**|いいえ|クライアントからキープアライブ メッセージを送信しなくても TCP 接続または HTTP 接続が開いたまま維持される時間 (分)。|
    |**DNS 名ラベル**|なし|名前の作成先となる Azure の場所 (すべてのサブスクリプション、すべての顧客) で一意となるようにしてください。 指定した名前のリソースに接続できるよう、その名前と IP アドレスが Azure のパブリック DNS サービスによって自動的に登録されます。 指定した名前に *location.cloudapp.azure.com* (<location> は選択した場所) が自動的に付加されて完全修飾 DNS 名が作成されます。 |
    |**サブスクリプション**|はい|パブリック IP アドレスを関連付けるリソースと同じサブスクリプション内に存在する必要があります。|
    |**[リソース グループ]**|はい|所属するリソース グループは、パブリック IP アドレスを関連付けるリソースと同じであっても異なっていてもかまいません。|
    |**場所**|はい|パブリック IP アドレスを関連付けるリソースと同じ場所に存在する必要があります。|

|**ツール**|**コマンド**|
|---|---|
|**CLI**|[az network public-ip-create](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|**PowerShell**|[New-AzureRmPublicIpAddress](/powershell/resourcemanager/azurerm.network/v3.4.0/new-azurermpublicipaddress?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="change"></a>パブリック IP アドレスの設定変更または削除

パブリック IP アドレスを変更したり削除したりするには、次の手順を実行します。

1. ご利用のサブスクリプションのネットワーク作成協力者ロール (またはそれ以上) のアクセス許可が割り当てられているアカウントで [Azure Portal](https://portal.azure.com) にログインします。 アカウントへのロールとアクセス許可の割り当てについて詳しくは、「[Azure のロールベースのアクセス制御のための組み込みロール](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)」の記事を参照してください。
2. Azure Portal 上部に *[リソースの検索]* というテキストが表示されたボックスがあります。そこに「*パブリック IP アドレス*」と入力します。 検索結果に **[パブリック IP アドレス]** が表示されたら、それをクリックします。
3. 表示された **[パブリック IP アドレス]** ブレードで、設定変更または削除の対象となるパブリック IP アドレスの名前をクリックします。
4. 表示されたパブリック IP アドレスのブレードで、実行する操作 (削除または変更) に応じて次のいずれかを実行します。
    - **削除:** パブリック IP アドレスを削除するには、ブレードの **[概要]** セクションの **[削除]** をクリックします。 現在 IP 構成に関連付けられているアドレスは削除できません。 アドレスが構成に関連付けられている場合は、**[関連付け解除]** をクリックすると、IP 構成からアドレスの関連付けが解除されます。
    - **変更:** **[構成]** をクリックします。 この記事の「[パブリック IP アドレスの作成](#create)」セクションにある手順 4. の情報を参考にして設定を変更します。 割り当て方法を静的から動的に変更する場合はまず、IP 構成からパブリック IP アドレスの関連付けを解除する必要があります。 そのうえで、動的な割り当て方法に変更し、**[関連付け]** をクリックして、同じ IP 構成または異なる IP 構成に IP アドレスを関連付けるか、関連付けを解除したままの状態にすることができます。 パブリック IP アドレスの関連付けを解除するには、**[概要]** セクションの **[関連付け解除]** をクリックします。

>[!WARNING]
>割り当て方法を静的から動的に変更すると、パブリック IP アドレスに割り当てられた IP アドレスが失われます。 DNS 名ラベル (定義した場合) に対する静的アドレスまたは動的アドレスのマッピングは Azure パブリック DNS サーバーによって管理されますが、動的 IP アドレスは、VM が停止 (割り当て解除) 状態になった後、起動されたときに変化する可能性があります。 アドレスが変化しないようにするには、静的 IP アドレスを割り当ててください。

|**ツール**|**コマンド**|
|---|---|
|**CLI**|[az network public-ip update](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#update) (更新)、[az network public-ip delete](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#delete) (削除)|
|**PowerShell**|[Set-AzureRmPublicIpAddress](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermpublicipaddress?toc=%2fazure%2fvirtual-network%2ftoc.json) (更新)、[Remove-AzureRmPublicIpAddress](/powershell/resourcemanager/azurerm.network/v3.4.0/remove-azurermpublicipaddress?toc=%2fazure%2fvirtual-network%2ftoc.json) (削除)|

## <a name="next-steps"></a>次のステップ
次の Azure リソースの作成時にパブリック IP アドレスを割り当てます。

- [Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 仮想マシンまたは [Linux](../virtual-machines/virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 仮想マシン
- [インターネットに接続する Azure Load Balancer](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure Application Gateway](../application-gateway/application-gateway-create-gateway-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure VPN Gateway を使用したサイト間接続](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure Virtual Machine Scale Sets](../virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
