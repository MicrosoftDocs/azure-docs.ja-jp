---
title: Azure Firewall ポリシーでの IP グループ
description: IP グループを使用すると、Azure Firewall ポリシー規則の IP アドレスをグループ化して管理できます。
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: conceptual
ms.date: 07/30/2020
ms.author: victorh
ms.openlocfilehash: 54faa0bc7d414eb15a866b8e1e6d09e15b22b071
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102500854"
---
# <a name="ip-groups-in-azure-firewall-policy"></a>Azure Firewall ポリシーでの IP グループ

IP グループを使用すると、次の方法で Azure Firewall ポリシーの IP アドレスをグループ化して管理できます。

- DNAT 規則での送信元の種類として
- ネットワーク規則での送信元または送信先の種類として
- アプリケーション規則での送信元の種類として


IP グループは、1 つの IP アドレス、複数の IP アドレス、または 1 つ以上の IP アドレス範囲を持つことができます。

IP グループは、Azure のリージョンとサブスクリプションにまたがる複数のファイアウォールに対する Azure Firewall DNAT 規則、ネットワーク規則、アプリケーション規則で再利用できます。 グループ名は一意である必要があります。 IP グループの構成は、Azure portal、Azure CLI、または REST API で行うことができます。 使い始めるときに役に立つサンプル テンプレートが用意されています。

## <a name="sample-format"></a>サンプル形式

IP グループでは、次の例のような IPv4 アドレス形式を使用できます。

- 単一のアドレス: 10.0.0.0
- CIDR 表記: 10.1.0.0/32
- アドレス範囲: 10.2.0.0-10.2.0.31

## <a name="create-an-ip-group"></a>IP グループを作成する

IP グループは、Azure portal、Azure CLI、または REST API を使用して作成できます。 詳細については、「[IP グループを作成する](../firewall/create-ip-group.md)」を参照してください。

## <a name="browse-ip-groups"></a>IP グループを参照する
1. Azure portal の検索バーに「**IP グループ**」と入力して選択します。 IP グループの一覧を確認できます。または、 **[追加]** を選択して新しい IP グループを作成できます。
2. IP グループを選択して概要ページを開きます。 IP アドレスまたは IP グループを編集、追加、削除できます。

   ![IP グループの概要](media/ip-groups/overview.png)

## <a name="manage-an-ip-group"></a>IP グループを管理する

IP グループ内のすべての IP アドレスと、それに関連付けられている規則またはリソースを確認できます。 IP グループを削除するには、最初に、IP グループを使用しているリソースから IP グループの関連付けを解除する必要があります。

1. IP アドレスを表示または編集するには、左側のペインの **[設定]** で **[IP アドレス]** を選択します。
2. 1 つまたは複数の IP アドレスを追加するには、 **[IP アドレスの追加]** を追加します。 これにより、アップロードのための **[Drag or Browse]\(ドラッグまたは参照\)** ページが開きます。または、アドレスを手動で入力することもできます。
3.    IP アドレスを編集または削除するには、右側にある省略記号 **[...]** を選択します。 複数の IP アドレスを編集または削除するには、ボックスを選択し、上部にある **[編集]** または **[削除]** を選択します。
4. 最後に、CSV ファイル形式でファイルをエクスポートできます。

> [!NOTE]
> 規則でまだ使用されている間に IP グループ内のすべての IP アドレスを削除すると、その規則はスキップされます。


## <a name="use-an-ip-group"></a>IP グループを使用する

DNAT 規則、アプリケーション規則、またはネットワーク規則を備えたポリシーを作成するときに、IP アドレスの **[接続元の種類]** または **[接続先の種類]** として **[IP グループ]** を選択できます。

:::image type="content" source="media/ip-groups/firewall-ip-group.png" alt-text="Firewall での IP グループ":::

## <a name="ip-address-limits"></a>IP アドレスの制限

各 IP グループごとに最大 5000 個の個別の IP アドレスまたは IP プレフィックスを使用して、ファイアウォールあたり最大 100 個の IP グループを所有することができます。

## <a name="related-azure-powershell-cmdlets"></a>関連する Azure PowerShell コマンドレット

次の Azure PowerShell コマンドレットを使用して、IP グループを作成および管理できます。

- [New-AzIpGroup](/powershell/module/az.network/new-azipgroup)
- [Remove-AzIPGroup](/powershell/module/az.network/remove-azipgroup)
- [Get-AzIpGroup](/powershell/module/az.network/get-azipgroup)
- [Set-AzIpGroup](/powershell/module/az.network/set-azipgroup)
- [New-AzFirewallPolicyNetworkRule](/powershell/module/az.network/new-azfirewallpolicynetworkrule)
- [New-AzFirewallPolicyApplicationRule](/powershell/module/az.network/new-azfirewallpolicyapplicationrule)
- [New-AzFirewallPolicyNatRule](/powershell/module/az.network/new-azfirewallpolicynatrule)

## <a name="next-steps"></a>次のステップ

- [チュートリアル:Azure Firewall Manager を使用した仮想 WAN のセキュリティ保護](secure-cloud-network.md)