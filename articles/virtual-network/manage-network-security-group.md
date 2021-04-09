---
title: Azure ネットワーク セキュリティ グループを作成、変更、削除する
titlesuffix: Azure Virtual Network
description: セキュリティ規則に関する情報の参照先、およびネットワーク セキュリティ グループを作成、変更、削除する方法について説明します。
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/13/2020
ms.author: kumud
ms.openlocfilehash: 5de909d0d57ae212fa562eb31551e2271d307d47
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101694259"
---
# <a name="create-change-or-delete-a-network-security-group"></a>ネットワーク セキュリティ グループの作成、変更、削除

ネットワーク セキュリティ グループのセキュリティ規則を使用して、仮想ネットワーク サブネットとネットワーク インターフェイスに出入りできるネットワーク トラフィックの種類をフィルター処理できます。 ネットワーク セキュリティ グループの詳細については、[ネットワーク セキュリティ グループの概要](./network-security-groups-overview.md)に関するページを参照してください。 次に、[ネットワーク トラフィックのフィルター処理](tutorial-filter-network-traffic.md)に関するチュートリアルを完了し、ネットワーク セキュリティ グループについて少し経験してみてください。

## <a name="before-you-begin"></a>開始する前に

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure アカウントをお持ちでない場合は、アクティブなサブスクリプションを使用してそれを設定します。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。 この記事の残りを始める前に、次のいずれかのタスクを完了します。

- **ポータル ユーザー**:Azure アカウントで [Azure Portal](https://portal.azure.com) にサインインします。

- **PowerShell ユーザー**:[Azure Cloud Shell](https://shell.azure.com/powershell) でコマンドを実行するか、お使いのコンピューターから PowerShell を実行します。 Azure Cloud Shell は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。 一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。 Azure Cloud Shell のブラウザー タブで、 **[環境の選択]** ドロップダウン リストを見つけ、(まだ選択されていない場合は) **[PowerShell]** を選択します。

    PowerShell をローカルで実行している場合、Azure PowerShell モジュール バージョン 1.0.0 以降を使用します。 インストールされているバージョンを確認するには、`Get-Module -ListAvailable Az.Network` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。 `Connect-AzAccount` を実行して、Azure との接続を作成します。

- **Azure のコマンド ライン インターフェイス (CLI) のユーザー**:[Azure Cloud Shell](https://shell.azure.com/bash) でコマンドを実行するか、コンピューターから CLI を実行します。 Azure CLI をローカルに実行している場合は、Azure CLI バージョン 2.0.28 以降を使用してください。 インストールされているバージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。 `az login` を実行して、Azure との接続を作成します。

Azure へのログインまたは接続に使用するアカウントは、[ネットワーク共同作成者ロール](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)または「[アクセス許可](#permissions)」の一覧の適切なアクションが割り当てられている[カスタム ロール](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に、割り当てられている必要があります。

## <a name="work-with-network-security-groups"></a>ネットワーク セキュリティ グループを操作する

ネットワーク セキュリティ グループは、作成、[すべて表示](#view-all-network-security-groups)、[詳細の表示](#view-details-of-a-network-security-group)、[変更](#change-a-network-security-group)、および[削除](#delete-a-network-security-group)できます。 ネットワーク インターフェイスまたはサブネットに対して、ネットワーク セキュリティ グループを[関連付けるか、関連付けを解除する](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface)こともできます。

### <a name="create-a-network-security-group"></a>ネットワーク セキュリティ グループの作成

Azure の場所およびサブスクリプションごとに作成できるネットワーク セキュリティ グループの数には制限があります。 詳しくは、「[Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)」をご覧ください。

1. [[Azure portal]](https://portal.azure.com) メニュー上または **[ホーム]** ページから **[リソースの作成]** を選択します。

2. **[ネットワーク]** を選択した後、 **[ネットワーク セキュリティ グループ]** を選択します。

3. **[ネットワーク セキュリティ グループの作成]** ページの **[基本]** タブで、次の設定の値を設定します。

    | 設定 | アクション |
    | --- | --- |
    | **サブスクリプション** | サブスクリプションを選択します。 |
    | **リソース グループ** | 既存のリソース グループを選択するか、 **[新規作成]** を選択して新しいリソース グループを作成します。 |
    | **名前** | リソース グループ内で一意のテキスト文字列を入力します。 |
    | **リージョン** | 目的の場所を選択します。 |

4. **[Review + create]\(レビュー + 作成\)** を選択します。

5. "**検証に成功しました**" というメッセージが表示されたら、 **[作成]** を選択します。

#### <a name="commands"></a>コマンド

| ツール | コマンド |
| ---- | ------- |
| Azure CLI | [az network nsg create](/cli/azure/network/nsg#az-network-nsg-create) |
| PowerShell | [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) |

### <a name="view-all-network-security-groups"></a>すべてのネットワーク セキュリティ グループを表示する

ネットワーク セキュリティ グループを表示するには、[Azure portal](https://portal.azure.com) に移動します。 「**ネットワーク セキュリティ グループ**」を検索して選択します。 サブスクリプションのネットワーク セキュリティ グループの一覧が表示されます。

#### <a name="commands"></a>コマンド

| ツール | コマンド |
| ---- | ------- |
| Azure CLI | [az network nsg list](/cli/azure/network/nsg#az-network-nsg-list) |
| PowerShell | [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) |

### <a name="view-details-of-a-network-security-group"></a>ネットワーク セキュリティ グループの詳細を表示する

1. ネットワーク セキュリティ グループを表示するには、[Azure portal](https://portal.azure.com) に移動します。 「**ネットワーク セキュリティ グループ**」を検索して選択します。

2. ネットワーク セキュリティ グループの名前を選択します。

ネットワーク セキュリティ グループのメニュー バーの **[設定]** では、ネットワーク セキュリティ グループが関連付けられている **[受信セキュリティ規則]** 、 **[送信セキュリティ規則]** 、 **[ネットワーク インターフェイス]** 、 **[サブネット]** を確認できます。

**[監視]** では、 **[診断設定]** を有効または無効にすることができます。 **[サポート + トラブルシューティング]** では、 **[有効なセキュリティ ルール]** を確認できます。 詳しくは、「[ネットワーク セキュリティ グループの診断ログ](virtual-network-nsg-manage-log.md)」および「[VM ネットワーク トラフィック フィルターの問題を診断する](diagnose-network-traffic-filter-problem.md)」をご覧ください。

Azure の一般的な設定の詳細については、次の記事を参照してください。

- [アクティビティ ログ](../azure-monitor/essentials/platform-logs-overview.md)
- [アクセス制御 (IAM)](../role-based-access-control/overview.md)
- [タグ](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Locks](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Automation スクリプト](../azure-resource-manager/templates/export-template-portal.md)

#### <a name="commands"></a>コマンド

| ツール | コマンド |
| ---- | ------- |
| Azure CLI | [az network nsg show](/cli/azure/network/nsg#az-network-nsg-show) |
| PowerShell | [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) |

### <a name="change-a-network-security-group"></a>ネットワーク セキュリティ グループを変更する

1. ネットワーク セキュリティ グループを表示するには、[Azure portal](https://portal.azure.com) に移動します。 「**ネットワーク セキュリティ グループ**」を検索して選択します。

2. 変更するネットワーク セキュリティ グループの名前を選択します。

最も一般的な変更は、[セキュリティ規則の追加](#create-a-security-rule)、[規則の削除](#delete-a-security-rule)、[サブネットまたはネットワーク インターフェイスに対するネットワーク セキュリティ グループの関連付けまたは関連付け解除](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface)です。

#### <a name="commands"></a>コマンド

| ツール | コマンド |
| ---- | ------- |
| Azure CLI | [az network nsg update](/cli/azure/network/nsg#az-network-nsg-update) |
| PowerShell | [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) |

### <a name="associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface"></a>ネットワーク インターフェイスまたはサブネットに対してネットワーク セキュリティ グループを関連付けるか関連付けを解除する

ネットワーク インターフェイスに対してネットワーク セキュリティ グループを関連付けるか関連付けを解除するには、「[ネットワーク セキュリティ グループの関連付けまたは関連付けの解除を行う](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group)」を参照してください。 サブネットに対してネットワーク セキュリティ グループを関連付けるか関連付けを解除するには、「[サブネットの設定を変更する](virtual-network-manage-subnet.md#change-subnet-settings)」を参照してください。

### <a name="delete-a-network-security-group"></a>ネットワーク セキュリティ グループを削除する

ネットワーク セキュリティ グループは、いずれかのサブネットまたはネットワーク インターフェイスに関連付けられている場合は削除できません。 ネットワーク セキュリティ グループを削除する前に、すべてのサブネットとネットワーク インターフェイスからネットワーク セキュリティ グループの関連付けを解除してください。

1. ネットワーク セキュリティ グループを表示するには、[Azure portal](https://portal.azure.com) に移動します。 「**ネットワーク セキュリティ グループ**」を検索して選択します。

2. 削除するネットワーク セキュリティ グループの名前を選択します。

3. ネットワーク セキュリティ グループのツール バーで、 **[削除]** を選択します。 次に、確認のダイアログ ボックスで **[はい]** を選択します。

#### <a name="commands"></a>コマンド

| ツール | コマンド |
| ---- | ------- |
| Azure CLI | [az network nsg delete](/cli/azure/network/nsg#az-network-nsg-delete) |
| PowerShell | [Remove-AzNetworkSecurityGroup](/powershell/module/az.network/remove-aznetworksecuritygroup) |

## <a name="work-with-security-rules"></a>セキュリティ規則を操作する

ネットワーク セキュリティ グループには、0 個または 1 つ以上のセキュリティ規則が含まれています。 セキュリティ規則は、作成、[すべて表示](#view-all-security-rules)、[詳細の表示](#view-details-of-a-security-rule)、[変更](#change-a-security-rule)、および[削除](#delete-a-security-rule)できます。

### <a name="create-a-security-rule"></a>セキュリティ規則を作成する

Azure の各場所およびサブスクリプションに対して作成できるネットワーク セキュリティ グループごとの規則の数には制限があります。 詳しくは、「[Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)」をご覧ください。

1. ネットワーク セキュリティ グループを表示するには、[Azure portal](https://portal.azure.com) に移動します。 「**ネットワーク セキュリティ グループ**」を検索して選択します。

2. セキュリティ規則を追加するネットワーク セキュリティ グループの名前を選択します。

3. ネットワーク セキュリティ グループのメニュー バーで、 **[受信セキュリティ規則]** または **[送信セキュリティ規則]** を選択します。

    既存の規則が一覧表示されます (追加していないものが含まれることもあります)。 ネットワーク セキュリティ グループを作成すると、既定のセキュリティ規則がいくつかその中に作成されます。 詳しくは、「[既定セキュリティ規則](./network-security-groups-overview.md#default-security-rules)」をご覧ください。  既定の規則は削除できませんが、優先順位の高い規則を使用してオーバーライドできます。

4. <a name="security-rule-settings"></a> **[追加]** を選択します。 次の設定の値を選択するか入力し、 **[OK]** を選択します。

    | 設定 | 値 | 詳細 |
    | ------- | ----- | ------- |
    | **ソース** | つぎのいずれかです。<ul><li>**[任意]**</li><li>**IP アドレス**</li><li>**サービス タグ** (受信セキュリティ規則) または **VirtualNetwork** (送信セキュリティ規則)</li><li>**アプリケーションのセキュリティ&nbsp;グループ**&nbsp;</li></ul> | <p>**[IP アドレス]** 選択した場合は、 **[発信元 IP アドレス/CIDR 範囲]** も指定する必要があります。</p><p>**[サービス タグ]** を選択した場合は、 **[ソース サービス タグ]** も選択できます。</p><p>**[アプリケーションのセキュリティ グループ]** を選択した場合は、既存のアプリケーション セキュリティ グループを選択する必要もあります。 **[ソース]** と **[宛先]** の両方に対して **[アプリケーションのセキュリティ グループ]** を選択した場合は、両方のアプリケーションのセキュリティ グループ内のネットワーク インターフェイスが同じ仮想ネットワークに存在する必要があります。</p> |
    | **ソース IP アドレス/CIDR 範囲** | IP アドレスおよびクラスレス ドメイン間ルーティング (CIDR) 範囲のコンマ区切りリスト | <p>**[ソース]** を **[IP アドレス]** に変更すると、この設定が表示されます。 単一の値またはコンマで区切った複数の値のリストを指定する必要があります。 複数の値の例は、`10.0.0.0/16, 192.188.1.1` です。 指定できる値の数には制限があります。 詳しくは、[Azure の制限事項](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) に関する記事をご覧ください。</p><p>指定した IP アドレスが Azure VM に割り当てられている場合は、そのパブリック IP アドレスではなく、プライベート IP アドレスを指定します。 Azure によってセキュリティ規則が処理されるのは、受信セキュリティ規則の場合はパブリック IP アドレスがプライベート IP アドレスに変換された後、送信規則の場合はプライベート IP アドレスがパブリック IP アドレスに変換される前です。 Azure のパブリック IP アドレスとプライベート IP アドレスの詳細については、[IP アドレスの種類](./public-ip-addresses.md)に関するページを参照してください。</p> |
    | **ソース サービス タグ** | ドロップダウン リストからのサービ スタグ | このオプションの設定は、受信セキュリティ規則の **[ソース]** を **[サービス タグ]** に設定した場合に表示されます。 サービス タグは、IP アドレスのカテゴリに対する定義済みの識別子です。 利用可能なサービス タグと各タグが表していることの詳細については、「[サービス タグ](./network-security-groups-overview.md#service-tags)」を参照してください。 |
    | **ソース アプリケーションのセキュリティ グループ** | 既存のアプリケーションのセキュリティ グループ | この設定は、 **[ソース]** を **[アプリケーションのセキュリティ グループ]** に設定した場合に表示されます。 ネットワーク インターフェイスと同じリージョンに存在するアプリケーションのセキュリティ グループを選択します。 [アプリケーション セキュリティ グループの作成](#create-an-application-security-group)方法を参照してください。 |
    | **ソース ポート範囲** | つぎのいずれかです。<ul><li>単一のポート (例: `80`)</li><li>ポートの範囲 (例: `1024-65535`)</li><li>単一つのポートまたはポート範囲 (またはその両方) のコンマ区切りのリスト (例: `80, 1024-65535`)</li><li>任意のポートでトラフィックを許可する場合はアスタリスク (`*`)</li></ul> | この設定では、規則でトラフィックを許可または拒否するポートを指定します。 指定できるポートの数には制限があります。 詳しくは、[Azure の制限事項](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) に関する記事をご覧ください。 |
    | **宛先** | つぎのいずれかです。<ul><li>**[任意]**</li><li>**[IP アドレス]**</li><li>**[サービス タグ]** (送信セキュリティ規則) または **[VirtualNetwork]** (受信セキュリティ規則)</li><li>**[アプリケーションのセキュリティ&nbsp;グループ]** &nbsp;</li></ul> | <p>**[IP アドレス]** 選択した場合は、 **[宛先 IP アドレス/CIDR 範囲]** も指定します。</p><p>**[VirtualNetwork]** を選択した場合、仮想ネットワークのアドレス空間内のすべての IP アドレスへのトラフィックが許可されます。 **VirtualNetwork** はサービス タグです。</p><p>**[アプリケーションのセキュリティ グループ]** を選択した場合は、既存のアプリケーションのセキュリティ グループを選択する必要があります。 [アプリケーション セキュリティ グループの作成](#create-an-application-security-group)方法を参照してください。</p> |
    | **宛先 IP アドレス/CIDR 範囲** | IP アドレスと CIDR 範囲のコンマ区切りのリスト | <p>**[宛先]** を **[IP アドレス]** に変更すると、この設定が表示されます。 **[ソース]** と **[送信元 IP アドレス/CIDR 範囲]** と同じように、単一のアドレス、複数のアドレス、または範囲を指定できます。 指定できる数には制限があります。 詳しくは、[Azure の制限事項](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) に関する記事をご覧ください。</p><p>指定した IP アドレスが Azure VM に割り当てられている場合は、そのパブリック IP アドレスではなく、プライベート IP アドレスを指定します。 Azure によってセキュリティ規則が処理されるのは、受信セキュリティ規則の場合はパブリック IP アドレスがプライベート IP アドレスに変換された後、送信規則の場合はプライベート IP アドレスがパブリック IP アドレスに変換される前です。 Azure のパブリック IP アドレスとプライベート IP アドレスの詳細については、[IP アドレスの種類](./public-ip-addresses.md)に関するページを参照してください。</p> |
    | **宛先サービス タグ** | ドロップダウン リストからのサービ スタグ | このオプションの設定は、送信セキュリティ規則の **[宛先]** を **[サービス タグ]** に変更した場合に表示されます。 サービス タグは、IP アドレスのカテゴリに対する定義済みの識別子です。 利用可能なサービス タグと各タグが表していることの詳細については、「[サービス タグ](./network-security-groups-overview.md#service-tags)」を参照してください。 |
    | **宛先アプリケーションのセキュリティ グループ** | 既存のアプリケーションのセキュリティ グループ | この設定は、 **[宛先]** を **[アプリケーションのセキュリティ グループ]** に設定した場合に表示されます。 ネットワーク インターフェイスと同じリージョンに存在するアプリケーションのセキュリティ グループを選択します。 [アプリケーション セキュリティ グループの作成](#create-an-application-security-group)方法を参照してください。 |
    | **宛先ポート範囲** | つぎのいずれかです。<ul><li>単一のポート (例: `80`)</li><li>ポートの範囲 (例: `1024-65535`)</li><li>単一つのポートまたはポート範囲 (またはその両方) のコンマ区切りのリスト (例: `80, 1024-65535`)</li><li>任意のポートでトラフィックを許可する場合はアスタリスク (`*`)</li></ul> | **[ソース ポート範囲]** と同様に、1 つまたは複数のポートおよび範囲を指定できます。 指定できる数には制限があります。 詳しくは、[Azure の制限事項](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) に関する記事をご覧ください。 |
    | **プロトコル** | **[任意]** 、 **[TCP]** 、 **[UDP]** 、または **[ICMP]** | 伝送制御プロトコル (TCP)、ユーザー データグラム プロトコル (UDP)、またはインターネット制御メッセージ プロトコル (ICMP) に規則を制限できます。 既定では、すべてのプロトコルに規則が適用されます。 |
    | **操作** | **[許可]** または **[拒否]** | この設定では、指定したソースと宛先の構成に対して、この規則でアクセスを許可するか拒否するかを指定します。 |
    | **優先順位** | ネットワーク セキュリティ グループ内のすべてのセキュリティ規則に対して一意である 100 から 4096 の範囲の値 | Azure では、優先順位の順序でセキュリティ規則が処理されます。 数値が小さいほど、優先度は高くなります。 規則を作成するときに、間を空けて優先順位を指定することをお勧めします (100、200、300 など)。 間を空けることにより、後で規則を簡単に追加し、既存の規則より高いまたは低い優先順位を指定できます。 |
    | **名前** | ネットワーク セキュリティ グループ内の一意の名前 | 名前の最大長は 80 文字です。 先頭は文字または数字、末尾は文字、数字、またはアンダースコアでなければなりません。 名前に使用できるのは、文字、数字、アンダースコア、ピリオド、およびハイフンのみです。 |
    | **説明** | テキストの説明 | 必要に応じて、セキュリティ規則の説明文を指定できます。 説明は 140 文字より長くすることはできません。 |

#### <a name="commands"></a>コマンド

| ツール | コマンド |
| ---- | ------- |
| Azure CLI | [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) |
| PowerShell | [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) |

### <a name="view-all-security-rules"></a>すべてのセキュリティ規則を表示する

ネットワーク セキュリティ グループには、0 個以上のセキュリティ規則が含まれます。 規則を表示したときに一覧に表示される情報の詳細については、[ネットワーク セキュリティ グループの概要](./network-security-groups-overview.md)に関する記事を参照してください。

1. ネットワーク セキュリティ グループの規則を表示するには、[Azure portal](https://portal.azure.com) に移動します。 「**ネットワーク セキュリティ グループ**」を検索して選択します。

2. 規則を表示するネットワーク セキュリティ グループの名前を選択します。

3. ネットワーク セキュリティ グループのメニュー バーで、 **[受信セキュリティ規則]** または **[送信セキュリティ規則]** を選択します。

一覧には、作成したすべての規則と、ネットワーク セキュリティ グループの[既定のセキュリティ規則](./network-security-groups-overview.md#default-security-rules)が含まれます。

#### <a name="commands"></a>コマンド

| ツール | コマンド |
| ---- | ------- |
| Azure CLI | [az network nsg rule list](/cli/azure/network/nsg/rule#az-network-nsg-rule-list) |
| PowerShell | [Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig) |

### <a name="view-details-of-a-security-rule"></a>セキュリティ規則の詳細を表示する

1. ネットワーク セキュリティ グループの規則を表示するには、[Azure portal](https://portal.azure.com) に移動します。 「**ネットワーク セキュリティ グループ**」を検索して選択します。

2. 規則の詳細を表示するネットワーク セキュリティ グループの名前を選択します。

3. ネットワーク セキュリティ グループのメニュー バーで、 **[受信セキュリティ規則]** または **[送信セキュリティ規則]** を選択します。

4. 詳細を表示する規則を選択します。 すべての設定の詳細については、[セキュリティ規則の設定](#security-rule-settings)に関する記事をご覧ください。

    > [!NOTE]
    > この手順は、カスタム セキュリティ規則にのみ適用されます。 既定のセキュリティ規則を選択した場合は機能しません。

#### <a name="commands"></a>コマンド

| ツール | コマンド |
| ---- | ------- |
| Azure CLI | [az network nsg rule show](/cli/azure/network/nsg/rule#az-network-nsg-rule-show) |
| PowerShell | [Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig) |

### <a name="change-a-security-rule"></a>セキュリティ規則を変更する

1. 「[セキュリティ規則の詳細を表示する](#view-details-of-a-security-rule)」の手順を完了します。

2. 必要に応じて設定を変更し、 **[保存]** を選択します。 すべての設定の詳細については、[セキュリティ規則の設定](#security-rule-settings)に関する記事をご覧ください。

    > [!NOTE]
    > この手順は、カスタム セキュリティ規則にのみ適用されます。 既定のセキュリティ規則を変更することは許可されていません。

#### <a name="commands"></a>コマンド

| ツール | コマンド |
| ---- | ------- |
| Azure CLI | [az network nsg rule update](/cli/azure/network/nsg/rule#az-network-nsg-rule-update) |
| PowerShell | [Set-AzNetworkSecurityRuleConfig](/powershell/module/az.network/set-aznetworksecurityruleconfig) |

### <a name="delete-a-security-rule"></a>セキュリティ規則を削除する

1. 「[セキュリティ規則の詳細を表示する](#view-details-of-a-security-rule)」の手順を完了します。

2. **[削除]** を選択してから、 **[はい]** を選択します。

    > [!NOTE]
    > この手順は、カスタム セキュリティ規則にのみ適用されます。 既定のセキュリティ規則を削除することは許可されていません。

#### <a name="commands"></a>コマンド

| ツール | コマンド |
| ---- | ------- |
| Azure CLI | [az network nsg rule delete](/cli/azure/network/nsg/rule#az-network-nsg-rule-delete) |
| PowerShell | [Remove-AzNetworkSecurityRuleConfig](/powershell/module/az.network/remove-aznetworksecurityruleconfig) |

## <a name="work-with-application-security-groups"></a>アプリケーション セキュリティ グループを操作する

アプリケーション セキュリティ グループには、0 個または複数個のネットワーク インターフェイスが含まれます。 詳細については、「[アプリケーション セキュリティ グループ](./network-security-groups-overview.md#application-security-groups)」を参照してください。 アプリケーション セキュリティ グループ内のすべてのネットワーク インターフェイスは、同じ仮想ネットワークに存在している必要があります。 アプリケーション セキュリティ グループにネットワーク インターフェイスを追加する方法については、「[アプリケーション セキュリティ グループにネットワーク インターフェイスを追加する](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups)」を参照してください。

### <a name="create-an-application-security-group"></a>アプリケーション セキュリティ グループを作成する

1. [[Azure portal]](https://portal.azure.com) メニュー上または **[ホーム]** ページから **[リソースの作成]** を選択します。

2. 検索ボックスに、「*Application security group*」と入力します。

3. **[Application security group]** ページで、 **[作成]** を選択します。

4. **[アプリケーションのセキュリティ グループの作成]** ページの **[基本]** タブで、次の設定の値を設定します。

    | 設定 | アクション |
    | --- | --- |
    | **サブスクリプション** | サブスクリプションを選択します。 |
    | **リソース グループ** | 既存のリソース グループを選択するか、 **[新規作成]** を選択して新しいリソース グループを作成します。 |
    | **名前** | リソース グループ内で一意のテキスト文字列を入力します。 |
    | **リージョン** | 目的の場所を選択します。 |

5. **[Review + create]\(レビュー + 作成\)** を選択します。

6. **[確認と作成]** タブに **[検証に成功しました]** というメッセージが表示されたら、 **[作成]** を選択します。

#### <a name="commands"></a>コマンド

| ツール | コマンド |
| ---- | ------- |
| Azure CLI | [az network asg create](/cli/azure/network/asg#az-network-asg-create) |
| PowerShell | [New-AzApplicationSecurityGroup](/powershell/module/az.network/new-azapplicationsecuritygroup) |

### <a name="view-all-application-security-groups"></a>すべてのアプリケーション セキュリティ グループを表示する

[Azure portal](https://portal.azure.com) に移動し、アプリケーションのセキュリティ グループを表示します。 **[アプリケーションのセキュリティ グループ]** を探して選択します。 Azure portal に、アプリケーションのセキュリティ グループの一覧が表示されます。

#### <a name="commands"></a>コマンド

| ツール | コマンド |
| ---- | ------- |
| Azure CLI | [az network asg list](/cli/azure/network/asg#az-network-asg-list) |
| PowerShell | [Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup) |

### <a name="view-details-of-a-specific-application-security-group"></a>特定のアプリケーション セキュリティ グループの詳細を表示する

1. アプリケーションのセキュリティ グループを表示するには、[Azure portal](https://portal.azure.com) に移動します。 **[アプリケーションのセキュリティ グループ]** を探して選択します。

2. 詳細を表示するアプリケーションのセキュリティ グループの名前を選択します。

#### <a name="commands"></a>コマンド

| ツール | コマンド |
| ---- | ------- |
| Azure CLI | [az network asg show](/cli/azure/network/asg#az-network-asg-show) |
| PowerShell | [Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup) |

### <a name="change-an-application-security-group"></a>アプリケーション セキュリティ グループを変更する

1. アプリケーションのセキュリティ グループを表示するには、[Azure portal](https://portal.azure.com) に移動します。 **[アプリケーションのセキュリティ グループ]** を探して選択します。

2. 変更するアプリケーションのセキュリティ グループの名前を選択します。

3. 変更する設定の横にある **[変更]** を選択します。 たとえば、 **[タグ]** を追加または削除したり、 **[リソース グループ]** または **[サブスクリプション]** を変更したりできます。

    > [!NOTE]
    > 場所を変更することはできません。

    メニュー バーでは、 **[アクセス制御 (IAM)]** を選択することもできます。 **[アクセス制御 (IAM)]** ページで、アプリケーションのセキュリティ グループに対するアクセス許可を割り当てたり削除したりできます。

#### <a name="commands"></a>コマンド

| ツール | コマンド |
| ---- | ------- |
| Azure CLI | [az network asg update](/cli/azure/network/asg#az-network-asg-update) |
| PowerShell | PowerShell コマンドレットはありません |

### <a name="delete-an-application-security-group"></a>アプリケーション セキュリティ グループを削除する

アプリケーションのセキュリティ グループにネットワーク インターフェイスが含まれる場合は、アプリケーションのセキュリティ グループを削除できません。 アプリケーションのセキュリティ グループからすべてのネットワーク インターフェイスを削除するには、ネットワーク インターフェイスの設定を変更するか、ネットワーク インターフェイスを削除します。 詳細については、「[アプリケーション セキュリティ グループに対して追加または削除を実行する](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups)」または「[ネットワーク インターフェイスの削除](virtual-network-network-interface.md#delete-a-network-interface)」を参照してください。

1. アプリケーションのセキュリティ グループを管理するには、[Azure portal](https://portal.azure.com) に移動します。 **[アプリケーションのセキュリティ グループ]** を探して選択します。

2. 削除するアプリケーションのセキュリティ グループの名前を選択します。

3. **[削除]** を選択し、 **[はい]** を選んでアプリケーション セキュリティ グループを削除します。

#### <a name="commands"></a>コマンド

| ツール | コマンド |
| ---- | ------- |
| Azure CLI | [az network asg delete](/cli/azure/network/asg#az-network-asg-delete) |
| PowerShell | [Remove-AzApplicationSecurityGroup](/powershell/module/az.network/remove-azapplicationsecuritygroup) |

## <a name="permissions"></a>アクセス許可

ネットワーク セキュリティ グループ、セキュリティ規則、およびアプリケーションのセキュリティ グループに関するタスクを行うには、使用するアカウントが[ネットワーク共同作成者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)ロール、または次の表に示す適切なアクセス許可が割り当てられた[カスタム ロール](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に、割り当てられている必要があります。

### <a name="network-security-group"></a>ネットワーク セキュリティ グループ

| アクション                                                        |   名前                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/read                  |   ネットワーク セキュリティ グループの取得                                          |
| Microsoft.Network/networkSecurityGroups/write                 |   ネットワーク セキュリティ グループの作成または更新                             |
| Microsoft.Network/networkSecurityGroups/delete                |   ネットワーク セキュリティ グループの削除                                       |
| Microsoft.Network/networkSecurityGroups/join/action           |   サブネットまたはネットワーク インターフェイスへのネットワーク セキュリティ グループの関連付け 


>[!NOTE]
> ネットワーク セキュリティ グループに対して `write` 操作を実行するには、`Microsoft.Network/networkSecurityGroups/write` アクセス許可に加えて、リソース グループに対する `read` 以上のアクセス許可がサブスクリプション アカウントに必要です。


### <a name="network-security-group-rule"></a>ネットワーク セキュリティ グループの規則

| アクション                                                        |   名前                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/securityRules/read            |   規則の取得                                                            |
| Microsoft.Network/networkSecurityGroups/securityRules/write           |   規則の作成または更新                                               |
| Microsoft.Network/networkSecurityGroups/securityRules/delete          |   規則の削除                                                         |

### <a name="application-security-group"></a>アプリケーション セキュリティ グループ

| アクション                                                                     | 名前                                                     |
| --------------------------------------------------------------             | -------------------------------------------              |
| Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action     | IP 構成をアプリケーション セキュリティ グループに結合する|
| Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | セキュリティ ルールをアプリケーション セキュリティ グループに結合する    |
| Microsoft.Network/applicationSecurityGroups/read                           | アプリケーション セキュリティ グループを取得する                        |
| Microsoft.Network/applicationSecurityGroups/write                          | アプリケーション セキュリティ グループを作成または更新する           |
| Microsoft.Network/applicationSecurityGroups/delete                         | アプリケーション セキュリティ グループを削除する                     |

## <a name="next-steps"></a>次のステップ

- [PowerShell](powershell-samples.md) または [Azure CLI](cli-samples.md) のサンプル スクリプトを使用して、または Azure [Resource Manager テンプレート](template-samples.md)を使用して、ネットワークまたはアプリケーションのセキュリティ グループを作成します
- 仮想ネットワーク用に [Azure Policy 定義](./policy-reference.md)を作成して割り当てる
