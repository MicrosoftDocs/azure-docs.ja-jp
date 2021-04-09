---
title: プライベート DNS ゾーンとレコードを保護する - Azure DNS
description: このラーニング パスでは、Microsoft Azure DNS でプライベート DNS ゾーンとレコード セットの保護を始めます。
services: dns
author: asudbring
ms.service: dns
ms.topic: how-to
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: 6e77f983f3600ae7c54d7d88f2ad1a006d7325fa
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102614192"
---
# <a name="how-to-protect-private-dns-zones-and-records"></a>プライベート DNS ゾーンとレコードを保護する方法

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

プライベート DNS ゾーンとレコードは、重要なリソースです。 DNS ゾーンまたは DNS レコードを削除すると、サービス全体が停止する可能性があります。 DNS ゾーンとレコードは、承認されていない変更や意図しない変更が加えられないように保護することが重要です。

この記事では、Azure DNS でプライベート DNS ゾーンとレコードをこのような変更から保護する方法について説明します。  Azure Resource Manager によって提供される 2 つの強力なセキュリティ機能である、[Azure ロールベースのアクセス制御 (Azure RBAC)](../role-based-access-control/overview.md) と[リソース ロック](../azure-resource-manager/management/lock-resources.md)を適用します。

## <a name="azure-role-based-access-control"></a>Azure ロールベースのアクセス制御

Azure のロールベースのアクセス制御 (Azure RBAC) では、Azure ユーザー、グループ、およびリソースのアクセス権を詳細に管理できます。 Azure RBAC を使用すると、ユーザーが必要とするアクセス レベルを付与することができます。 Azure RBAC を使用したアクセス管理の詳細については、「[Azure ロールベースのアクセス制御 (Azure RBAC) とは](../role-based-access-control/overview.md)」を参照してください。

### <a name="the-private-dns-zone-contributor-role"></a>プライベート DNS ゾーンの共同作成者ロール

プライベート DNS ゾーンの共同作成者ロールは、プライベート DNS リソースの管理用の組み込みロールです。 このロールをユーザーまたはグループに適用すると、ユーザーまたはグループはプライベート DNS リソースを管理できるようになります。

リソース グループ *myPrivateDNS* には、Contoso Corporation 用の 5 つのゾーンが含まれています。 このリソース グループに対するプライベート DNS ゾーンの共同作成者アクセス許可を DNS 管理者に付与すると、管理者はこれらの DNS ゾーンを完全に制御できるようになります。 これは、不要なアクセス許可の付与を防ぐことができます。 DNS 管理者は、仮想マシンを作成または停止することはできません。

Azure RBAC のアクセス許可は、[Azure portal から](../role-based-access-control/role-assignments-portal.md) 割り当てるのが最も簡単な方法です。  

リソース グループの **[アクセス制御 (IAM)]** を開き、 **[追加]** を選択して、 **[プライベート DNS ゾーンの共同作成者]** ロールを選択します。 アクセス許可を付与する必要があるユーザーまたはグループを選択します。

![Azure Portal を使用したリソース グループ レベルの Azure RBAC](./media/dns-protect-private-zones-recordsets/rbac1.png)

アクセス許可は、[Azure PowerShell を使用して付与する](../role-based-access-control/role-assignments-powershell.md)こともできます。

```azurepowershell-interactive
# Grant 'Private DNS Zone Contributor' permissions to all zones in a resource group

$rsg = "<resource group name>"
$usr = "<user email address>"
$rol = "Private DNS Zone Contributor"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -ResourceGroupName $rsg
```

また、同じ目的を果たせるコマンドを [Azure CLI から実行する](../role-based-access-control/role-assignments-cli.md)こともできます。

```azurecli-interactive
# Grant 'Private DNS Zone Contributor' permissions to all zones in a resource group

az role assignment create \
--assignee "<user email address>" \
--role "Private DNS Zone Contributor" \
--resource-group "<resource group name>"
```

### <a name="private-zone-level-azure-rbac"></a>プライベート ゾーン レベルの Azure RBAC

Azure RBAC のルールは、サブスクリプション、リソース グループ、または個々のリソースに適用できます。 このリソースは、個々の DNS ゾーンまたは個々のレコード セットのいずれかになります。

たとえば、リソース グループ *myPrivateDNS* にゾーン *private.contoso.com* とサブゾーン *customers.private.contoso.com* が含まれているとします。 顧客アカウントごとに CNAME レコードが作成されています。 これらの CNAME レコードの管理に使用する管理者アカウントに、*customers.private.contoso.com* ゾーンにレコードを作成できるアクセス許可を割り当てます。 このアカウントは、*customers.private.contoso.com* のみを管理できます。

ゾーン レベルの Azure RBAC アクセス許可は、Azure Portal から付与できます。  ゾーンの **[アクセス制御 (IAM)]** を開き、 **[追加]** を選択して、 **[プライベート DNS ゾーンの共同作成者]** ロールを選択します。 アクセス許可を付与する必要があるユーザーまたはグループを選択します。

![Azure Portal を使用した DNS ゾーン レベルの Azure RBAC](./media/dns-protect-private-zones-recordsets/rbac2.png)

アクセス許可は、[Azure PowerShell を使用して付与する](../role-based-access-control/role-assignments-powershell.md)こともできます。

```azurepowershell-interactive
# Grant 'Private DNS Zone Contributor' permissions to a specific zone

$rsg = "<resource group name>"
$usr = "<user email address>"
$zon = "<zone name>"
$rol = "Private DNS Zone Contributor"
$rsc = "Microsoft.Network/privateDnsZones"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -ResourceGroupName $rsg -ResourceName $zon -ResourceType $rsc
```

また、同じ目的を果たせるコマンドを [Azure CLI から実行する](../role-based-access-control/role-assignments-cli.md)こともできます。

```azurecli-interactive
# Grant 'Private DNS Zone Contributor' permissions to a specific zone

az role assignment create \
--assignee <user email address> \
--role "Private DNS Zone Contributor" \
--scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/privateDnsZones/<zone name>/"
```

### <a name="record-set-level-azure-rbac"></a>レコード セット レベルの Azure RBAC

アクセス許可は、レコード セット レベルで適用されます。  ユーザーには、必要とするエントリに対する制御が許可され、他の変更を行うことはできません。

レコード セット レベルの Azure RBAC アクセス許可は、Azure portal の [レコード セット] ページにある **[アクセス制御 (IAM)]** ボタンを使用して構成できます。

![[アクセス制御 (IAM)] ボタンを示すスクリーンショット。](./media/dns-protect-private-zones-recordsets/rbac3.png)

![[ロールの割り当ての追加] が選択されている [アクセス制御] を示すスクリーンショット。](./media/dns-protect-private-zones-recordsets/rbac4.png)

レコード セット レベルの Azure RBAC アクセス許可は、[Azure PowerShell を使用して付与する](../role-based-access-control/role-assignments-powershell.md)こともできます。

```azurepowershell-interactive
# Grant permissions to a specific record set

$usr = "<user email address>"
$rol = "Private DNS Zone Contributor"
$sco = 
"/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/privateDnsZones/<zone name>/<record type>/<record name>"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -Scope $sco
```

また、同じ目的を果たせるコマンドを [Azure CLI から実行する](../role-based-access-control/role-assignments-cli.md)こともできます。

```azurecli-interactive
# Grant permissions to a specific record set

az role assignment create \
--assignee "<user email address>" \
--role "Private DNS Zone Contributor" \
--scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/privateDnsZones/<zone name>/<record type>/<record name>"
```

### <a name="custom-roles"></a>カスタム ロール

組み込みのプライベート DNS ゾーン共同作成者ロールでも DNS リソースを完全に制御できます。 独自のカスタム Azure ロールを作成して、さらにきめ細かくリソースを制御することもできます。

CNAME の管理に使用するアカウントには、CNAME レコードのみを管理できるアクセス許可を付与します。 そのアカウントは、他の種類のレコードを変更することはできません。 また、ゾーンの削除などのゾーンレベルの操作を行うこともできません。

次の例は、CNAME レコードのみを管理するためのカスタム ロールの定義を示しています。

```json
{
    "Name": "Private DNS CNAME Contributor",
    "Id": "",
    "IsCustom": true,
    "Description": "Can manage DNS CNAME records only.",
    "Actions": [
        "Microsoft.Network/privateDnsZones/CNAME/*",
        "Microsoft.Network/privateDNSZones/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
    ],
    "NotActions": [
    ],
    "AssignableScopes": [
        "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
}
```

上記の Actions プロパティでは、次のような DNS に固有のアクセス許可を定義しています。

* `Microsoft.Network/privateDnsZones/CNAME/*` は CNAME レコードを完全に制御できるアクセス許可を付与しています。
* `Microsoft.Network/privateDNSZones/read` は DNS プライベート ゾーンを読み取ることができるが変更はできないアクセス許可を付与しています。これにより、CNAME が作成されているゾーンを表示することはできます。

> [!NOTE]
> レコード セットを削除できないようにしながらもレコード セットの更新は許可するような Azure カスタム ロールの使い方は、効果的な制御方法とは言えません。 レコード セットの削除は防止できますが、変更は自由にできてしまうためです。  レコード セットに対するレコードの追加や削除といった変更が許可されることになり、すべてのレコードを削除してレコード セットを空にすることもできてしまいます。 DNS 解決の視点から見ると、これはレコード セットが削除された場合と同等の影響があります。

現時点では、Azure Portal でカスタム ロールを定義することはできません。 カスタム ロールは、Azure PowerShell を使用して次のロール定義に基づいて作成できます。

```azurepowershell-interactive
# Create new role definition based on input file

New-AzRoleDefinition -InputFile <file path>
```

また、Azure CLI を使用して作成することもできます。

```azurecli-interactive
# Create new role definition based on input file

az role create -inputfile <file path>
```

作成したロールは、この記事で前述した組み込みロールの場合と同じ方法で割り当てることができます。

カスタム ロールの作成、管理、および割り当ての方法の詳細については、「[Azure カスタム ロール](../role-based-access-control/custom-roles.md)」を参照してください。

## <a name="resource-locks"></a>リソース ロック

Azure Resource Manager は、別のタイプのセキュリティ制御をサポートしています。それはリソースをロックする機能です。 リソース ロックはリソースに適用され、すべてのユーザーおよびロールが対象になります。 詳細については、[「Azure Resource Manager によるリソースのロック」](../azure-resource-manager/management/lock-resources.md)を参照してください。

リソースのロックは 2 種類あります｡**CanNotDelete** と **ReadOnly** です。 これらは、プライベート DNS ゾーンまたは個別のレコード セットのいずれかに適用できます。  以降のセクションでは、一般的なシナリオをいくつか紹介し、そのシナリオにリソース ロックを使用して対応する方法について説明します。

### <a name="protecting-against-all-changes"></a>あらゆる変更に対する保護

変更を防止するには、ゾーンに ReadOnly ロックを適用します。 このロックにより、レコード セットの新規作成と既存のレコード セットの変更または削除を防止できます。

ゾーン レベルのリソース ロックは、Azure Portal を使用して作成できます。  DNS ゾーン ページから **[ロック]** 、 **[+ 追加]** の順に選択します。

![Azure Portal を使用したゾーン レベルのリソース ロック](./media/dns-protect-private-zones-recordsets/locks1.png)

ゾーン レベルのリソース ロックは、[Azure PowerShell](/powershell/module/az.resources/new-azresourcelock) を使用して作成することもできます。

```azurepowershell-interactive
# Lock a DNS zone

$lvl = "<lock level>"
$lnm = "<lock name>"
$rsc = "<zone name>"
$rty = "Microsoft.Network/privateDnsZones"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rsc -ResourceType $rty -ResourceGroupName $rsg
```

また、同じ目的を果たせるコマンドを [Azure CLI から実行する](/cli/azure/lock#az-lock-create)こともできます。

```azurecli-interactive
# Lock a DNS zone

az lock create \
--lock-type "<lock level>" \
--name "<lock name>" \
--resource-name "<zone name>" \
--namespace "Microsoft.Network" \
--resource-type "privateDnsZones" \
--resource-group "<resource group name>"
```
### <a name="protecting-individual-records"></a>個々のレコードの保護

既存の DNS レコード セットが変更されないようにするには、レコード セットに ReadOnly ロックを適用します。

> [!NOTE]
> レコード セットへの CanNotDelete ロックの適用は、制御方法として効果的ではありません。 レコード セットの削除は防止できますが、変更は自由にできてしまうためです。  レコード セットに対するレコードの追加や削除といった変更が許可されることになり、すべてのレコードを削除してレコード セットを空にすることもできてしまいます。 DNS 解決の視点から見ると、これはレコード セットが削除された場合と同等の影響があります。

現時点では、レコード セット レベルのリソース ロックは、Azure PowerShell でのみ構成できます。  Azure Portal と Azure CLI ではサポートされていません。

Azure PowerShell

```azurepowershell-interactive
# Lock a DNS record set

$lvl = "<lock level>"
$lnm = "<lock name>"
$rnm = "<zone name>/<record set name>"
$rty = "Microsoft.Network/privateDnsZones"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rnm -ResourceType $rty -ResourceGroupName $rsg
```
### <a name="protecting-against-zone-deletion"></a>ゾーンの削除に対する保護

Azure DNS でゾーンを削除すると、ゾーン内のレコード セットもすべて削除されます。  この操作は元に戻すことができません。 誤って重要なゾーンを削除すると、ビジネスに多大な影響を与えるおそれがあります。  ゾーンの誤削除を防止することが重要です。

ゾーンに CanNotDelete ロックを適用することで、ゾーンが削除されるのを防止できます。 ロックは、子リソースによって継承されます。 ロックは、ゾーン内のレコード セットが削除されるのを防止します。 上記の注で説明したとおり、既存のレコード セットからレコードを削除できるため、効果的な制御方法とは言えません。

代わりに、ゾーン内のレコード セット (SOA レコード セットなど) に対して CanNotDelete ロックを適用してください。 レコード セットが削除されなければ、ゾーンは削除されません。 このロックは、ゾーンの削除を防止しますが、ゾーン内のレコード セットを自由に変更することができます。 ゾーンを削除しようとすると、削除は、Azure Resource Manager によって検出されます。 この削除により、SOA レコード セットも削除されます。SOA はロックされているため、Azure Resource Manager は、呼び出しをブロックします。  結果として、レコード セットは削除されません。

次の PowerShell コマンドを実行すると、指定されたゾーンの SOA レコードに対する CanNotDelete ロックが作成されます。

```azurepowershell-interactive
# Protect against zone delete with CanNotDelete lock on the record set

$lvl = "CanNotDelete"
$lnm = "<lock name>"
$rnm = "<zone name>/@"
$rty = "Microsoft.Network/privateDnsZones/SOA"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rnm -ResourceType $rty -ResourceGroupName $rsg
```
ゾーンの誤削除を防止するもう 1 つの方法は、カスタム ロールを使用することです。 このロールにより、ゾーンの管理に使用されるアカウントに、ゾーンの削除アクセス許可が付与されないようにします。 

ゾーンを削除する必要がある場合は、2 段階の削除操作を強制することができます。

 - まず、ゾーンの削除アクセス許可を付与します。
 - 次に、ゾーンを削除するアクセス許可を付与します。

カスタム ロールは、これらのアカウントでアクセスされるすべてのゾーンに有効です。 サブスクリプション所有者などのゾーンの削除アクセス許可を持つアカウントは、依然としてゾーンを誤って削除する可能性があります。

DNS ゾーン保護のための多層防御手法として、リソースのロックとカスタム ロールという 2 つの方法を同時に使用することもできます。

## <a name="next-steps"></a>次のステップ

* Azure RBAC の使用の詳細については、「[Azure ロールベースのアクセス制御 (Azure RBAC) とは](../role-based-access-control/overview.md)」を参照してください。
* リソース ロックの操作の詳細については、「[Azure Resource Manager によるリソースのロック](../azure-resource-manager/management/lock-resources.md)」を参照してください。
