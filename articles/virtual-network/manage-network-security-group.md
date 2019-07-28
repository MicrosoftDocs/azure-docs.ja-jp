---
title: Azure ネットワーク セキュリティ グループを作成、変更、削除する
titlesuffix: Azure Virtual Network
description: ネットワーク セキュリティ グループの作成、変更、削除の方法について説明します。
services: virtual-network
documentationcenter: na
author: kumudd
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/05/2018
ms.author: kumud
ms.openlocfilehash: f1353165954021cd949d6e46357d10514ee26b3c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "65560943"
---
# <a name="create-change-or-delete-a-network-security-group"></a>ネットワーク セキュリティ グループの作成、変更、削除

ネットワーク セキュリティ グループのセキュリティ規則を使用して、仮想ネットワーク サブネットとネットワーク インターフェイスに出入りできるネットワーク トラフィックの種類をフィルター処理できます。 ネットワーク セキュリティ グループに慣れていない場合は、[ネットワーク セキュリティ グループの概要](security-overview.md)に関する記事で詳細を確認し、[ネットワーク トラフィックをフィルター処理する](tutorial-filter-network-traffic.md)チュートリアルを完了してネットワークセキュリティ グループの機能を体験してください。

## <a name="before-you-begin"></a>開始する前に

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

この記事のセクションに記載された手順を始める前に、次のタスクを完了してください。

- まだ Azure アカウントを持っていない場合は、[無料試用版アカウント](https://azure.microsoft.com/free)にサインアップしてください。
- ポータルを使用する場合は、 https://portal.azure.com を開き、Azure アカウントでログインします。
- PowerShell コマンドを使用してこの記事のタスクを実行する場合は、[Azure Cloud Shell](https://shell.azure.com/powershell) でコマンドを実行するか、お使いのコンピューターから PowerShell を実行してください。 Azure Cloud Shell は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。 一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。 このチュートリアルには、Azure PowerShell モジュール バージョン 1.0.0 以降が必要です。 インストールされているバージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzAccount` を実行して Azure との接続を作成することも必要です。
- Azure コマンド ライン インターフェイス (CLI) コマンドを使用してこの記事のタスクを実行する場合は、[Azure Cloud Shell](https://shell.azure.com/bash) でコマンドを実行するか、お使いのコンピューターから CLI を実行してください。 このチュートリアルには、Azure CLI バージョン 2.0.28 以降が必要です。 インストールされているバージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。 Azure CLI をローカルで実行している場合、`az login` を実行して Azure との接続を作成することも必要です。

Azure へのログインまたは接続に使用するアカウントは、[ネットワークの共同作業者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)ロール、または「[アクセス許可](#permissions)」の一覧で示されている適切なアクセス許可を割り当てられた[カスタム ロール](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に、割り当てられている必要があります。

## <a name="work-with-network-security-groups"></a>ネットワーク セキュリティ グループを操作する

ネットワーク セキュリティ グループは、作成、[すべて表示](#view-all-network-security-groups)、[詳細の表示](#view-details-of-a-network-security-group)、[変更](#change-a-network-security-group)、および[削除](#delete-a-network-security-group)できます。 ネットワーク インターフェイスまたはサブネットに対して、ネットワーク セキュリティ グループを[関連付けるか、関連付けを解除する](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface)こともできます。

### <a name="create-a-network-security-group"></a>ネットワーク セキュリティ グループの作成

Azure の場所およびサブスクリプションごとに作成できるネットワーク セキュリティ グループの数には制限があります。 詳細については、[Azure の制限](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)に関する記事をご覧ください。

1. ポータルの左上隅にある **[+ リソースの作成]** を選択します。
2. **[ネットワーク]** を選択した後、 **[ネットワーク セキュリティ グループ]** を選択します。
3. ネットワーク セキュリティ グループの **[名前]** を入力し、**サブスクリプション**を選択します。新しい**リソース グループ**を作成するか、既存のリソース グループを選択し、**場所**を選択してから、 **[作成]** を選択します。

**コマンド**

- Azure CLI: [az network nsg create](/cli/azure/network/nsg#az-network-nsg-create)
- PowerShell:[New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup)

### <a name="view-all-network-security-groups"></a>すべてのネットワーク セキュリティ グループを表示する

ポータルの上部にある検索ボックスに、「*ネットワーク セキュリティ グループ*」と入力します。 検索結果に **[ネットワーク セキュリティ グループ]** が表示されたら、それを選択します。 サブスクリプション内に存在するネットワーク セキュリティ グループが一覧表示されます。

**コマンド**

- Azure CLI: [az network nsg list](/cli/azure/network/nsg#az-network-nsg-list)
- PowerShell:[Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup)

### <a name="view-details-of-a-network-security-group"></a>ネットワーク セキュリティ グループの詳細を表示する

1. ポータルの上部にある検索ボックスに、「*ネットワーク セキュリティ グループ*」と入力します。 検索結果に **[ネットワーク セキュリティ グループ]** が表示されたら、それを選択します。
2. 詳細を表示するネットワーク セキュリティ グループを一覧から選択します。 **[設定]** の下で、**受信セキュリティ規則**と**送信セキュリティ規則**、ネットワーク セキュリティ グループが関連付けられている**ネットワーク インターフェイス**と**サブネット**を確認できます。 **診断ログ**の有効化または無効化、**有効なセキュリティ規則**の表示を行うこともできます。 詳細については、[診断ログ](virtual-network-nsg-manage-log.md)に関する記事と[有効なセキュリティ規則の表示](diagnose-network-traffic-filter-problem.md)に関する記事を参照してください。
3. Azure の一般的な設定の詳細については、次の記事を参照してください。
    *   [アクティビティ ログ](../azure-monitor/platform/activity-logs-overview.md)
    *   [アクセス制御 (IAM)](../role-based-access-control/overview.md)
    *   [タグ](../azure-resource-manager/resource-group-using-tags.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    *   [ロック](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    *   [Automation スクリプト](../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates)

**コマンド**

- Azure CLI: [az network nsg show](/cli/azure/network/nsg#az-network-nsg-show)
- PowerShell:[Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup)

### <a name="change-a-network-security-group"></a>ネットワーク セキュリティ グループを変更する

1. ポータルの上部にある検索ボックスに、「*ネットワーク セキュリティ グループ*」と入力します。 検索結果に **[ネットワーク セキュリティ グループ]** が表示されたら、それを選択します。
2. 変更するネットワーク セキュリティ グループを選択します。 最も一般的な変更は、セキュリティ規則の[追加](#create-a-security-rule)または[削除](#delete-a-security-rule)、[サブネットまたはネットワーク インターフェイスに対するネットワーク セキュリティ グループの関連付けまたは関連付けの解除](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface)です。

**コマンド**

- Azure CLI: [az network nsg update](/cli/azure/network/nsg#az-network-nsg-update)
- PowerShell:[Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup)

### <a name="associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface"></a>ネットワーク インターフェイスまたはサブネットに対してネットワーク セキュリティ グループを関連付けるか関連付けを解除する

ネットワーク インターフェイスに対してネットワーク セキュリティ グループを関連付けるか関連付けを解除するには、「[ネットワーク セキュリティ グループの関連付けまたは関連付けの解除を行う](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group)」を参照してください。 サブネットに対してネットワーク セキュリティ グループを関連付けるか関連付けを解除するには、「[サブネットの設定を変更する](virtual-network-manage-subnet.md#change-subnet-settings)」を参照してください。

### <a name="delete-a-network-security-group"></a>ネットワーク セキュリティ グループを削除する

ネットワーク セキュリティ グループは、いずれかのサブネットまたはネットワーク インターフェイスに関連付けられている場合は削除できません。 ネットワーク セキュリティ グループを削除する前に、すべてのサブネットとネットワーク インターフェイスからネットワーク セキュリティ グループの関連付けを解除してください。

1. ポータルの上部にある検索ボックスに、「*ネットワーク セキュリティ グループ*」と入力します。 検索結果に **[ネットワーク セキュリティ グループ]** が表示されたら、それを選択します。
2. 削除するネットワーク セキュリティ グループを一覧から選択します。
3. **[削除]** を選択してから、 **[はい]** を選択します。

**コマンド**

- Azure CLI: [az network nsg delete](/cli/azure/network/nsg#az-network-nsg-delete)
- PowerShell:[Remove-AzNetworkSecurityGroup](/powershell/module/az.network/remove-aznetworksecuritygroup)

## <a name="work-with-security-rules"></a>セキュリティ規則を操作する

ネットワーク セキュリティ グループには、0 個または 1 つ以上のセキュリティ規則が含まれています。 セキュリティ規則は、作成、[すべて表示](#view-all-security-rules)、[詳細の表示](#view-details-of-a-security-rule)、[変更](#change-a-security-rule)、および[削除](#delete-a-security-rule)できます。

### <a name="create-a-security-rule"></a>セキュリティ規則を作成する

Azure の場所、サブスクリプション、ネットワーク セキュリティ グループごとに作成できるセキュリティ 規則の数には制限があります。 詳細については、[Azure の制限](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)に関する記事をご覧ください。

1. ポータルの上部にある検索ボックスに、「*ネットワーク セキュリティ グループ*」と入力します。 検索結果に **[ネットワーク セキュリティ グループ]** が表示されたら、それを選択します。
2. セキュリティ規則を追加するネットワーク セキュリティ グループを一覧から選択します。
3. **[設定]** で **[受信セキュリティ規則]** を選びます。 さまざまな既存のルールが一覧表示されます。 自分で追加していない規則が表示されることがあります。 ネットワーク セキュリティ グループが作成されると、既定のセキュリティ規則がいくつかその中に作成されます。 詳しくは、「[既定セキュリティ規則](security-overview.md#default-security-rules)」をご覧ください。  既定の規則は削除できませんが、優先順位の高い規則を使用してオーバーライドできます。
4. <a name = "security-rule-settings"></a> **[+ 追加]** を選択します。  次の設定の値を選択するか入力し、 **[OK]** を選択します。
    
    |Setting  |値  |詳細  |
    |---------|---------|---------|
    |source     | 受信セキュリティ規則として、 **[任意]** 、 **[アプリケーション セキュリティ グループ]** 、 **[IP アドレス]** 、または **[サービス タグ]** を選択します。 送信セキュリティ規則を作成する場合、オプションは **[宛先]** に対して一覧表示されるオプションと同じです。       | **[アプリケーション セキュリティ グループ]** を選択した場合は、ネットワーク インターフェイスと同じ領域に存在する既存のアプリケーション セキュリティ グループを 1 つ以上選択します。 [アプリケーション セキュリティ グループの作成](#create-an-application-security-group)方法を参照してください。 **[ソース]** と **[宛先]** の両方に対して **[アプリケーション セキュリティ グループ]** を選択した場合は、両方のアプリケーション セキュリティ グループ内のネットワーク インターフェイスが同じ仮想ネットワークにある必要があります。 **[IP アドレス]** 選択した場合は、 **[発信元 IP アドレス/CIDR 範囲]** を指定します。 単一の値またはコンマで区切った複数の値の一覧を指定できます。 複数の値の例は、10.0.0.0/16, 192.188.1.1 です。 指定できる値の数には制限があります。 詳細については、[Azure の制限](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)に関する記事をご覧ください。 **[サービス タグ]** を選択した場合は、1 つのサービス タグを選択します。 サービス タグは、IP アドレスのカテゴリに対する定義済みの識別子です。 利用可能なサービス タグと各タグが表していることの詳細については、「[サービス タグ](security-overview.md#service-tags)」を参照してください。 指定した IP アドレスが Azure 仮想マシンに割り当てられている場合は、仮想マシンに割り当てられているパブリック IP アドレスではなく、確実にプライベート IP アドレスを指定してください。 セキュリティ ルールが処理されるタイミングは、受信セキュリティ ルールの場合は Azure がパブリック IP アドレスをプライベート IP アドレスに変換した後、送信ルールの場合は Azure がプライベート IP アドレスをパブリック IP アドレスに変換する前です。 Azure のパブリック IP アドレスとプライベート IP アドレスの詳細については、[IP アドレスの種類](virtual-network-ip-addresses-overview-arm.md)に関するページを参照してください。        |
    |ソース ポート範囲     | 単一のポート (例: 80)、ポートの範囲 (例: 1024-65535)、コンマで区切った単一のポートとポートの範囲の一覧 (例: 80, 1024-65535) を指定します。 任意のポートでトラフィックを許可する場合は、アスタリスクを入力します。 | ポートと範囲は、規則によってトラフィックが許可または拒否されるポートを指定します。 指定できるポートの数には制限があります。 詳細については、[Azure の制限](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)に関する記事をご覧ください。  |
    |宛先     | 送信セキュリティ規則として、 **[任意]** 、 **[アプリケーション セキュリティ グループ]** 、 **[IP アドレス]** 、または **[仮想ネットワーク]** を選択します。 受信セキュリティ規則を作成する場合、オプションは **[ソース]** に一覧表示されるオプションと同じです。        | **[アプリケーション セキュリティ グループ]** を選択した場合は、ネットワーク インターフェイスと同じ領域に存在する既存のアプリケーション セキュリティ グループを 1 つ以上選択する必要があります。 [アプリケーション セキュリティ グループの作成](#create-an-application-security-group)方法を参照してください。 **[アプリケーション セキュリティ グループ]** を選択した場合は、ネットワーク インターフェイスと同じ領域に存在する既存のアプリケーション セキュリティ グループを 1 つ選択します。 **[IP アドレス]** 選択した場合は、 **[宛先 IP アドレス/CIDR 範囲]** を指定します。 **[ソース]** と **[送信元 IP アドレス/CIDR 範囲]** と同じように、単一のアドレス、複数のアドレス、または範囲を指定できます。指定できる数には制限があります。 サービス タグである **[仮想ネットワーク]** の選択は、トラフィックが仮想ネットワークのアドレス空間内のすべての IP アドレスで許可されることを意味します。 指定した IP アドレスが Azure 仮想マシンに割り当てられている場合は、仮想マシンに割り当てられているパブリック IP アドレスではなく、確実にプライベート IP アドレスを指定してください。 セキュリティ ルールが処理されるタイミングは、受信セキュリティ ルールの場合は Azure がパブリック IP アドレスをプライベート IP アドレスに変換した後、送信ルールの場合は Azure がプライベート IP アドレスをパブリック IP アドレスに変換する前です。 Azure のパブリック IP アドレスとプライベート IP アドレスの詳細については、[IP アドレスの種類](virtual-network-ip-addresses-overview-arm.md)に関するページを参照してください。        |
    |宛先ポート範囲     | 単一の値またはコンマで区切った値の一覧を指定します。 | **[ソース ポート範囲]** と同じように、単一のポート、複数のポート、または範囲を指定できます。指定できる数には制限があります。 |
    |Protocol     | **[任意]** 、 **[TCP]** 、または **[UDP]** を選択します。        |         |
    |アクション     | **[許可]** または **[拒否]** を選択します。        |         |
    |優先度     | ネットワーク セキュリティ グループ内のすべてのセキュリティ規則に対して一意である 100 ～ 4096 の範囲の値を入力します。 |規則は、優先度に従って処理されます。 数値が小さいほど、優先度は高くなります。 規則を作成するときに、間を空けて優先度を指定することをお勧めします (100、200、300 など)。 間を空けることで、既存の規則よりも優先度が高いまたは低い規則が必要になった場合に、そのような規則を簡単に追加できます。         |
    |名前     | ネットワーク セキュリティ グループ内の一意の名前。        |  名前の最大長は 80 文字です。 先頭にはアルファベットまたは数字、末尾にはアルファベット、数字、またはアンダースコアを使用する必要があります。また、使用できるのは、アルファベット、数字、アンダースコア、ピリオド、ハイフンのみです。       |
    |説明     | 任意の説明です。        |         |

**コマンド**

- Azure CLI: [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create)
- PowerShell:[New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig)

### <a name="view-all-security-rules"></a>すべてのセキュリティ規則を表示する

ネットワーク セキュリティ グループには、0 個または複数個のセキュリティ規則が含まれます。 規則を表示したときに一覧に表示される情報の詳細については、[ネットワーク セキュリティ グループの概要](security-overview.md)に関する記事を参照してください。

1. ポータルの上部にある検索ボックスに、「*ネットワーク セキュリティ グループ*」と入力します。 検索結果に **[ネットワーク セキュリティ グループ]** が表示されたら、それを選択します。
2. 規則を表示するネットワーク セキュリティ グループを一覧から選択します。
3. **[設定]** で **[受信セキュリティ規則]** または **[送信セキュリティ規則]** を選択します。

一覧には、作成したすべての規則と、ネットワーク セキュリティ グループの[既定のセキュリティ規則](security-overview.md#default-security-rules)が含まれます。

**コマンド**

- Azure CLI: [az network nsg rule list](/cli/azure/network/nsg/rule#az-network-nsg-rule-list)
- PowerShell:[Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig)

### <a name="view-details-of-a-security-rule"></a>セキュリティ規則の詳細を表示する

1. ポータルの上部にある検索ボックスに、「*ネットワーク セキュリティ グループ*」と入力します。 検索結果に **[ネットワーク セキュリティ グループ]** が表示されたら、それを選択します。
2. 規則の詳細を表示するネットワーク セキュリティ グループを一覧から選択します。
3. **[設定]** で **[受信セキュリティ規則]** または **[送信セキュリティ規則]** を選択します。
4. 詳細を表示する規則を選択します。 すべての設定の詳細については、[セキュリティ ルールの設定](#security-rule-settings)に関する説明を参照してください。

**コマンド**

- Azure CLI: [az network nsg rule show](/cli/azure/network/nsg/rule#az-network-nsg-rule-show)
- PowerShell:[Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig)

### <a name="change-a-security-rule"></a>セキュリティ規則を変更する

1. 「[セキュリティ規則の詳細を表示する](#view-details-of-a-security-rule)」の手順を完了します。
2. 必要に応じて設定を変更し、 **[保存]** を選択します。 すべての設定の詳細については、[セキュリティ ルールの設定](#security-rule-settings)に関する説明を参照してください。

**コマンド**

- Azure CLI: [az network nsg rule update](/cli/azure/network/nsg/rule#az-network-nsg-rule-update)
- PowerShell:[Set-AzNetworkSecurityRuleConfig](/powershell/module/az.network/set-aznetworksecurityruleconfig)

### <a name="delete-a-security-rule"></a>セキュリティ規則を削除する

1. 「[セキュリティ規則の詳細を表示する](#view-details-of-a-security-rule)」の手順を完了します。
2. **[削除]** を選択してから、 **[はい]** を選択します。

**コマンド**

- Azure CLI: [az network nsg rule delete](/cli/azure/network/nsg/rule#az-network-nsg-rule-delete)
- PowerShell:[Remove-AzNetworkSecurityRuleConfig](/powershell/module/az.network/remove-aznetworksecurityruleconfig)

## <a name="work-with-application-security-groups"></a>アプリケーション セキュリティ グループを操作する

アプリケーション セキュリティ グループには、0 個または複数個のネットワーク インターフェイスが含まれます。 詳細については、「[アプリケーション セキュリティ グループ](security-overview.md#application-security-groups)」を参照してください。 アプリケーション セキュリティ グループ内のすべてのネットワーク インターフェイスは、同じ仮想ネットワークに存在している必要があります。 アプリケーション セキュリティ グループにネットワーク インターフェイスを追加する方法については、「[アプリケーション セキュリティ グループにネットワーク インターフェイスを追加する](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups)」を参照してください。

### <a name="create-an-application-security-group"></a>アプリケーション セキュリティ グループを作成する

1. Azure Portal の左上隅にある **[+ リソースの作成]** を選択します。
2. **[Marketplace を検索]** ボックスに、「*アプリケーション セキュリティ グループ*」と入力します。 検索結果に**アプリケーション セキュリティ グループ**が表示されたら、それを選択し、 **[すべて]** の下の**アプリケーション セキュリティ グループ** をもう一度選択します。次に、 **[作成]** を選択します。
3. 以下の情報を入力するか選んだ後、 **[作成]** を選びます。

    | Setting        | 値                                                   |
    | ---            | ---                                                     |
    | 名前           | 名前はリソース グループ内で一意である必要があります。        |
    | サブスクリプション   | サブスクリプションを選択します。                               |
    | リソース グループ | 既存のリソース グループを選択するか、新しいものを作成します。 |
    | Location       | 場所を選択します。                                       |

**コマンド**

- Azure CLI: [az network asg create](/cli/azure/network/asg#az-network-asg-create)
- PowerShell:[New-AzApplicationSecurityGroup](/powershell/module/az.network/new-azapplicationsecuritygroup)

### <a name="view-all-application-security-groups"></a>すべてのアプリケーション セキュリティ グループを表示する

1. Azure Portal の左上隅にある **[すべてのサービス]** を選択します。
2. **[すべてのサービス] のフィルター** ボックスに「*アプリケーション セキュリティ グループ*」と入力し、検索結果に**アプリケーション セキュリティ グループ**が表示されたらそれを選択します。

**コマンド**

- Azure CLI: [az network asg list](/cli/azure/network/asg#az-network-asg-list)
- PowerShell:[Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup)

### <a name="view-details-of-a-specific-application-security-group"></a>特定のアプリケーション セキュリティ グループの詳細を表示する

1. Azure Portal の左上隅にある **[すべてのサービス]** を選択します。
2. **[すべてのサービス] のフィルター** ボックスに「*アプリケーション セキュリティ グループ*」と入力し、検索結果に**アプリケーション セキュリティ グループ**が表示されたらそれを選択します。
3. 詳細を表示するアプリケーション セキュリティ グループを選択します。

**コマンド**

- Azure CLI: [az network asg show](/cli/azure/network/asg#az-network-asg-show)
- PowerShell:[Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup)

### <a name="change-an-application-security-group"></a>アプリケーション セキュリティ グループを変更する

1. Azure Portal の左上隅にある **[すべてのサービス]** を選択します。
2. **[すべてのサービス] のフィルター** ボックスに「*アプリケーション セキュリティ グループ*」と入力し、検索結果に**アプリケーション セキュリティ グループ**が表示されたらそれを選択します。
3. 設定を変更するアプリケーション セキュリティ グループを選択します。 タグを追加したり削除したりすることができます。また、アプリケーション セキュリティ グループに対するアクセス許可を割り当てたり削除したりすることもできます。

- Azure CLI: [az network asg update](/cli/azure/network/asg#az-network-asg-update)
- PowerShell:PowerShell コマンドレットはありません。

### <a name="delete-an-application-security-group"></a>アプリケーション セキュリティ グループを削除する

アプリケーション セキュリティ グループ内にネットワーク インターフェイスがある場合、アプリケーション セキュリティ グループを削除することはできません。 ネットワーク インターフェイスの設定を変更するかネットワーク インターフェイスを削除することで、アプリケーション セキュリティ グループからすべてのネットワーク インターフェイスを削除します。 詳細については、[アプリケーション セキュリティ グループに対するネットワークインターフェイスの追加または削除](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups)に関する記事または「[ネットワーク インターフェイスの削除](virtual-network-network-interface.md#delete-a-network-interface)」を参照してください。

1. Azure Portal の左上隅にある **[すべてのサービス]** を選択します。
2. **[すべてのサービス] のフィルター** ボックスに「*アプリケーション セキュリティ グループ*」と入力し、検索結果に**アプリケーション セキュリティ グループ**が表示されたらそれを選択します。
3. 削除するアプリケーション セキュリティ グループを選択します。
4. **[削除]** を選択し、 **[はい]** を選んでアプリケーション セキュリティ グループを削除します。

**コマンド**

- Azure CLI: [az network asg delete](/cli/azure/network/asg#az-network-asg-delete)
- PowerShell:[Remove-AzApplicationSecurityGroup](/powershell/module/az.network/remove-azapplicationsecuritygroup)

## <a name="permissions"></a>アクセス許可

ネットワーク セキュリティ グループ、セキュリティ規則、およびアプリケーション セキュリティ グループに関するタスクを実行するには、使用するアカウントが[ネットワーク共同作成者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)ロール、または次の表に示す適切なアクセス許可が割り当てられた[カスタム ロール](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に、割り当てられている必要があります。

### <a name="network-security-group"></a>ネットワーク セキュリティ グループ

| アクションを表示します。                                                        |   名前                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/read                  |   ネットワーク セキュリティ グループの取得                                          |
| Microsoft.Network/networkSecurityGroups/write                 |   ネットワーク セキュリティ グループの作成または更新                             |
| Microsoft.Network/networkSecurityGroups/delete                |   ネットワーク セキュリティ グループの削除                                       |
| Microsoft.Network/networkSecurityGroups/join/action           |   サブネットまたはネットワーク インターフェイスへのネットワーク セキュリティ グループの関連付け 


### <a name="network-security-group-rule"></a>ネットワーク セキュリティ グループの規則

| アクションを表示します。                                                        |   名前                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/rules/read            |   規則の取得                                                            |
| Microsoft.Network/networkSecurityGroups/rules/write           |   規則の作成または更新                                               |
| Microsoft.Network/networkSecurityGroups/rules/delete          |   規則の削除                                                         |

### <a name="application-security-group"></a>アプリケーション セキュリティ グループ

| アクションを表示します。                                                                     | 名前                                                     |
| --------------------------------------------------------------             | -------------------------------------------              |
| Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action     | IP 構成をアプリケーション セキュリティ グループに結合する|
| Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | セキュリティ ルールをアプリケーション セキュリティ グループに結合する    |
| Microsoft.Network/applicationSecurityGroups/read                           | アプリケーション セキュリティ グループを取得する                        |
| Microsoft.Network/applicationSecurityGroups/write                          | アプリケーション セキュリティ グループを作成または更新する           |
| Microsoft.Network/applicationSecurityGroups/delete                         | アプリケーション セキュリティ グループを削除する                     |

## <a name="next-steps"></a>次の手順

- [PowerShell](powershell-samples.md) か [Azure CLI](cli-samples.md) のサンプル スクリプトを使って、または Azure [Resource Manager テンプレート](template-samples.md)を使って、ネットワークまたはアプリケーション セキュリティ グループを作成します
- [Azure ポリシー](policy-samples.md)を作成して仮想ネットワークに適用します
