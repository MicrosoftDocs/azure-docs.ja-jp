---
title: Azure パブリック IP アドレス プレフィックスを作成、変更、または削除する
titlesuffix: Azure Virtual Network
description: パブリック IP アドレス プレフィックスとその作成、変更、削除の方法について説明します。 追加情報が見つかる場所を確認してください。
services: virtual-network
documentationcenter: na
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/13/2019
ms.author: allensu
ms.openlocfilehash: 2e32faad698fbf316d51123cc8b7845a3b262c7f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98938649"
---
# <a name="create-change-or-delete-a-public-ip-address-prefix"></a>パブリック IP アドレス プレフィックスの作成、変更、削除

パブリック IP アドレス プレフィックスとその作成、変更、削除の方法について説明します。 パブリック IP アドレス プレフィックスは、指定したパブリック IP アドレス数に基づく連続したアドレス範囲です。 このようなアドレスはサブスクリプションに割り当てられます。 パブリック IP アドレス リソースを作成するときに、プレフィックスから静的なパブリック IP アドレスを割り当てて、そのアドレスを仮想マシン、ロード バランサーなどのリソースに関連付けてインターネット接続を有効にすることができます。 パブリック IP アドレス プレフィックスをよく理解していない場合は、[パブリック IP アドレス プレフィックスの概要](public-ip-address-prefix.md)に関するページを参照してください。

## <a name="before-you-begin"></a>開始する前に

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

この記事のセクションに記載された手順を始める前に、次のタスクを完了してください。

- まだ Azure アカウントを持っていない場合は、[無料試用版アカウント](https://azure.microsoft.com/free)にサインアップしてください。
- ポータルを使用する場合は、 https://portal.azure.com を開き、Azure アカウントでログインします。
- PowerShell コマンドを使用してこの記事のタスクを実行する場合は、[Azure Cloud Shell](https://shell.azure.com/powershell) でコマンドを実行するか、お使いのコンピューターから PowerShell を実行してください。 Azure Cloud Shell は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。 一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。 このチュートリアルには、Azure PowerShell モジュール バージョン 1.0.0 以降が必要です。 インストールされているバージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzAccount` を実行して Azure との接続を作成することも必要です。
- Azure コマンド ライン インターフェイス (CLI) コマンドを使用してこの記事のタスクを実行する場合は、[Azure Cloud Shell](https://shell.azure.com/bash) でコマンドを実行するか、お使いのコンピューターから CLI を実行してください。 このチュートリアルには、Azure CLI バージョン 2.0.41 以降が必要です。 インストールされているバージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール](/cli/azure/install-azure-cli)」を参照してください。 Azure CLI をローカルで実行している場合、`az login` を実行して Azure との接続を作成することも必要です。

Azure へのログインまたは接続に使用するアカウントは、[ネットワークの共同作業者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)ロール、または「[アクセス許可](#permissions)」の一覧に記載されている適切なアクションが割り当てられている[カスタム ロール](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に割り当てられている必要があります。

パブリック IP アドレス プレフィックスは有料です。 詳細については、[価格](https://azure.microsoft.com/pricing/details/ip-addresses)のページを参照してください。

## <a name="create-a-public-ip-address-prefix"></a>パブリック IP アドレス プレフィックスの作成

1. ポータルの左上隅にある **[+ リソースの作成]** を選択します。
2. *[マーケットプレースを検索]* ボックスに「*パブリック IP プレフィックス*」と入力します。 検索結果に **[パブリック IP アドレス プレフィックス]** が表示されたら、それをクリックします。
3. **[パブリック IP アドレス プレフィックス]** の **[作成]** を選択します。
4. **[Create public IP address prefix]\(パブリック IP アドレス プレフィックス\)** にある以下の設定に対して値を入力または選択して、 **[作成]** を選択します。

   |設定|必須|詳細|
   |---|---|---|
   |サブスクリプション|はい|パブリック IP アドレスを関連付けるリソースと同じ[サブスクリプション](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)内に存在する必要があります。|
   |Resource group|はい|所属する[リソース グループ](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group)は、パブリック IP アドレスを関連付けるリソースと同じであっても異なっていてもかまいません。|
   |名前|はい|選択したリソース グループ内で一意となる名前を使用してください。|
   |リージョン|はい|範囲からアドレスを割り当てるパブリック IP アドレスと同じ[リージョン](https://azure.microsoft.com/regions)に存在する必要があります。|
   |プレフィックス サイズ|はい| 必要なプレフィックスのサイズ。 /28 または 16 の IP アドレスが既定値です。

**コマンド**

|ツール|コマンド|
|---|---|
|CLI|[az network public-ip prefix create](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-create)|
|PowerShell|[New-AzPublicIpPrefix](/powershell/module/az.network/new-azpublicipprefix)|

>[!NOTE]
>可用性ゾーンがあるリージョンでは、PowerShell または CLI コマンドを使用して、パブリック IP アドレス プレフィックスを、非ゾーンとして、特定のゾーンに関連付けて、またはゾーン冗長を使用するように作成できます。  API バージョン 2020-08-01 以降では、ゾーン パラメーターが指定されていない場合、非ゾーンのパブリック IP アドレス プレフィックスが作成されます。 2020-08-01 より前のバージョンの API では、ゾーン冗長パブリック IP アドレス プレフィックスが作成されます。 

## <a name="create-a-static-public-ip-address-from-a-prefix"></a>プレフィックスから静的パブリック IP アドレスを作成する
プレフィックスを作成したら、プレフィックスから静的 IP アドレスを作成する必要があります。 これを行うには、次の手順を実行します。

1. Azure portal の上部に *[リソースの検索]* というテキストが表示されたボックスがあります。そこに「*パブリック IP アドレス プレフィックス*」と入力します。 検索結果に **[パブリック IP アドレス プレフィックス]** が表示されたら、それをクリックします。
2. パブリック IP の作成に使用するプレフィックスを選択します。
3. 検索結果に表示されたら選択し、[概要] セクションの **[+ IP アドレスの追加]** をクリックします。
4. **[パブリック IP アドレスの作成]** で次の設定の値を入力または選択します。 プレフィックスは Standard SKU、IPv4、かつ静的なので、次の情報を入力する必要があります。

   |設定|必須|詳細|
   |---|---|---|
   |名前|はい|パブリック IP アドレスには、選択したリソース グループ内で一意の名前を付ける必要があります。|
   |アイドル タイムアウト (分)|いいえ|クライアントからキープアライブ メッセージを送信しなくても TCP 接続または HTTP 接続が開いたまま維持される時間 (分)。 |
   |DNS 名ラベル|いいえ|作成する Azure リージョン (すべてのサブスクリプション、すべての顧客) で一意の名前を付けます。 指定した名前を使用してリソースに接続できるよう、DNS 内の名前と IP アドレスが Azure によって自動的に登録されます。 指定した名前に既定のサブネット (*location.cloudapp.azure.com* など、"location" は選択した場所) が付加されて、完全修飾 DNS 名が作成されます。詳細については、[Azure パブリック IP アドレスで Azure DNS を使用する方法](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address)に関するページを参照してください。|

または、パブリック IP アドレス リソースを作成するには、--public-ip-prefix (CLI) および -PublicIpPrefix (PS) パラメーターを指定して以下の CLI および PS コマンドを使用することができます。 

|ツール|コマンド|
|---|---|
|CLI|[az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create)|
|PowerShell|[New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)|

## <a name="view-or-delete-a-prefix"></a>プレフィックスの表示または削除

1. Azure portal の上部に *[リソースの検索]* というテキストが表示されたボックスがあります。そこに「*パブリック IP アドレス プレフィックス*」と入力します。 検索結果に **[パブリック IP アドレス プレフィックス]** が表示されたら、それをクリックします。
2. 表示か設定の変更、または一覧から削除するパブリック IP アドレス プレフィックスの名前を選択します。
3. パブリック IP アドレス プレフィックスの表示か設定の変更、または一覧からの削除のどの操作を行うかに従って以下のいずれかの設定を行います。
   - **表示**: **[概要]** セクションには、プレフィックスなど、パブリック IP アドレス プレフィックスの主要な設定が表示されます。
   - **[削除]** :パブリック IP アドレス プレフィックスを削除するには、 **[概要]** セクションの **[削除]** を選択します。 プレフィックス内のアドレスがパブリック IP アドレス リソースに関連付けられている場合は、まずパブリック IP アドレス リソースを削除する必要があります。 [パブリック IP アドレスの削除](virtual-network-public-ip-address.md#view-modify-settings-for-or-delete-a-public-ip-address)に関するページを参照してください。

**コマンド**

|ツール|コマンド|
|---|---|
|CLI|[az network public-ip prefix list](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-list) (パブリック IP アドレスの一覧表示)、[az network public-ip prefix show](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-show) (設定の表示)、[az network public-ip prefix update](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-update) (更新)、[az network public-ip prefix delete](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-delete) (削除)|
|PowerShell|[Get-AzPublicIpPrefix](/powershell/module/az.network/get-azpublicipprefix) (パブリック IP アドレス オブジェクトの取得とその設定の表示)、[Set-AzPublicIpPrefix](/powershell/module/az.network/set-azpublicipprefix) (設定の更新)、[Remove-AzPublicIpPrefix](/powershell/module/az.network/remove-azpublicipprefix) (削除)|

## <a name="permissions"></a>アクセス許可

パブリック IP アドレス プレフィックスでタスクを実行するには、[ネットワークの共同作成者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)ロール、または次の表に記載されている適切なアクションが割り当てられている[カスタム](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ロールにアカウントが割り当てられている必要があります。

| アクション                                                            | 名前                                                           |
| ---------                                                         | -------------                                                  |
| Microsoft.Network/publicIPPrefixes/read                           | パブリック IP アドレス プレフィックスの読み取り                                |
| Microsoft.Network/publicIPPrefixes/write                          | パブリック IP アドレス プレフィックスの作成または更新                    |
| Microsoft.Network/publicIPPrefixes/delete                         | パブリック IP アドレス プレフィックスの削除                              |
|Microsoft.Network/publicIPPrefixes/join/action                     | プレフィックスからのパブリック IP アドレスの作成 |

## <a name="next-steps"></a>次のステップ

- [パブリック IP プレフィックス](public-ip-address-prefix.md)を使用するシナリオと利点について説明します
