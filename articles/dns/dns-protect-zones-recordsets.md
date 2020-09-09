---
title: DNS ゾーンとレコードを保護する - Azure DNS
description: このラーニング パスでは、Microsoft Azure DNS で DNS ゾーンとレコード セットの保護を始めます。
services: dns
author: asudbring
ms.service: dns
ms.topic: how-to
ms.date: 2/20/2020
ms.author: allensu
ms.openlocfilehash: b06ae396ae15c8572cf8160ce576651f47001add
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2020
ms.locfileid: "87920502"
---
# <a name="how-to-protect-dns-zones-and-records"></a>DNS ゾーンとレコードを保護する方法

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

DNS ゾーンと DNS レコードは、重要なリソースです。 DNS ゾーンまたは DNS レコードを削除すると、サービス全体が停止する可能性があります。 DNS ゾーンとレコードは、承認されていない変更や意図しない変更が加えられないように保護することが重要です。

この記事では、Azure DNS でプライベート DNS ゾーンとレコードをこのような変更から保護する方法について説明します。  Azure Resource Manager によって提供される 2 つの強力なセキュリティ機能である、[Azure ロールベースのアクセス制御 (Azure RBAC)](../role-based-access-control/overview.md) と[リソース ロック](../azure-resource-manager/management/lock-resources.md)を適用します。

## <a name="role-based-access-control"></a>ロールベースのアクセス制御

Azure のロールベースのアクセス制御 (Azure RBAC) では、Azure ユーザー、グループ、およびリソースのアクセス権を詳細に管理できます。 RBAC を使用すると、ユーザーが必要とするアクセス レベルを付与することができます。 RBAC を使用したアクセス管理の詳細については、[Azure ロールベースのアクセス制御の概要 (Azure RBAC)](../role-based-access-control/overview.md) に関するページを参照してください。

### <a name="the-dns-zone-contributor-role"></a>DNS ゾーン共同作成者ロール

DNS ゾーンの共同作成者ロールは、プライベート DNS リソースの管理用の組み込みロールです。 このロールをユーザーまたはグループに適用すると、ユーザーまたはグループは DNS リソースを管理できるようになります。

リソース グループ *myResourceGroup* には、Contoso Corporation の 5 つのゾーンが含まれています。 このリソース グループに対する DNS ゾーン共同作成者アクセス許可を DNS 管理者に付与すると、管理者はこれらの DNS ゾーンを完全に制御できるようになります。 これは、不要なアクセス許可の付与を防ぐことができます。 DNS 管理者は、仮想マシンを作成または停止することはできません。

RBAC アクセス許可の割り当ては、[Azure Portal](../role-based-access-control/role-assignments-portal.md) を使用すると最も簡単に行うことができます。  

リソース グループの **[アクセス制御 (IAM)]** を開き、 **[追加]** を選択して、 **[DNS Zone Contributor\(DNS ゾーン共同作成者\)]** ロールを選択します。 アクセス許可を付与する必要があるユーザーまたはグループを選択します。

![Azure Portal を使用したリソース グループ レベルの RBAC](./media/dns-protect-zones-recordsets/rbac1.png)

アクセス許可は、[Azure PowerShell を使用して付与する](../role-based-access-control/role-assignments-powershell.md)こともできます。

```azurepowershell
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group

$usr = "<user email address>"
$rol = "DNS Zone Contributor"
$rsg = "<resource group name>"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -ResourceGroupName $rsg
```

また、同じ目的を果たせるコマンドを [Azure CLI から実行する](../role-based-access-control/role-assignments-cli.md)こともできます。

```azurecli
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group

az role assignment create \
--assignee "<user email address>" \
--role "DNS Zone Contributor" \
--resource-group "<resource group name>"
```

### <a name="zone-level-rbac"></a>ゾーン レベルの RBAC

Azure RBAC のルールは、サブスクリプション、リソース グループ、または個々のリソースに適用できます。 このリソースは、個々の DNS ゾーンまたは個々のレコード セットのいずれかになります。

たとえば、リソース グループ *myResourceGroup* には、ゾーン *contoso.com* とサブゾーン *customers.contoso.com* が含まれています。 顧客アカウントごとに CNAME レコードが作成されています。 これらの CNAME レコードの管理に使用する管理者アカウントに、*customers.contoso.com* ゾーンにレコードを作成できるアクセス許可を割り当てます。 このアカウントでは *customers.contoso.com* のみを管理できます。

ゾーン レベルの RBAC アクセス許可は、Azure Portal から付与できます。  ゾーンの **[アクセス制御 (IAM)]** を開き、 **[追加]** 、 **[DNS Zone Contributor\(DNS ゾーン共同作成者\)]** ロールの順に選択し、アクセス許可の付与が必要なユーザーまたはグループを選択します。

![Azure Portal を使用した DNS ゾーン レベルの RBAC](./media/dns-protect-zones-recordsets/rbac2.png)

アクセス許可は、[Azure PowerShell を使用して付与する](../role-based-access-control/role-assignments-powershell.md)こともできます。

```azurepowershell
# Grant 'DNS Zone Contributor' permissions to a specific zone

$usr = "<user email address>"
$rol = "DNS Zone Contributor"
$rsg = "<resource group name>"
$zon = "<zone name>"
$typ = "Microsoft.Network/DNSZones"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -ResourceGroupName $rsg -ResourceName $zon -ResourceType $typ
```

また、同じ目的を果たせるコマンドを [Azure CLI から実行する](../role-based-access-control/role-assignments-cli.md)こともできます。

```azurecli
# Grant 'DNS Zone Contributor' permissions to a specific zone

az role assignment create \
--assignee <user email address> \
--role "DNS Zone Contributor" \
--scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/DnsZones/<zone name>/"
```

### <a name="record-set-level-rbac"></a>レコード セット レベルの RBAC

アクセス許可は、レコード セット レベルで適用されます。  ユーザーには、必要とするエントリに対する制御が許可され、他の変更を行うことはできません。

レコード セット レベルの RBAC アクセス許可は、Azure portal の [レコード セット] ページにある **[アクセス制御 (IAM)]** ボタンを使用して構成できます。

![Azure Portal を使用したレコード セット レベルの RBAC](./media/dns-protect-zones-recordsets/rbac3.png)

レコード セット レベルの RBAC アクセス許可は、[Azure PowerShell を使用して付与する](../role-based-access-control/role-assignments-powershell.md)こともできます。

```azurepowershell
# Grant permissions to a specific record set

$usr = "<user email address>"
$rol = "DNS Zone Contributor"
$sco = 
"/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -Scope $sco
```

また、同じ目的を果たせるコマンドを [Azure CLI から実行する](../role-based-access-control/role-assignments-cli.md)こともできます。

```azurecli
# Grant permissions to a specific record set

az role assignment create \
--assignee "<user email address>" \
--role "DNS Zone Contributor" \
--scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"
```

### <a name="custom-roles"></a>カスタム ロール

組み込みの DNS ゾーン共同作成者ロールでも DNS リソースを完全に制御できます。 独自のカスタム Azure ロールを作成して、さらにきめ細かくリソースを制御することもできます。

CNAME の管理に使用するアカウントには、CNAME レコードのみを管理できるアクセス許可を付与します。 そのアカウントは、他の種類のレコードを変更することはできません。 また、ゾーンの削除などのゾーンレベルの操作を行うこともできません。

次の例は、CNAME レコードのみを管理するためのカスタム ロールの定義を示しています。

```json
{
    "Name": "DNS CNAME Contributor",
    "Id": "",
    "IsCustom": true,
    "Description": "Can manage DNS CNAME records only.",
    "Actions": [
        "Microsoft.Network/dnsZones/CNAME/*",
        "Microsoft.Network/dnsZones/read",
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

* `Microsoft.Network/dnsZones/CNAME/*` は CNAME レコードを完全に制御できるアクセス許可を付与しています。
* `Microsoft.Network/dnsZones/read` は DNS ゾーンを読み取ることができるが変更はできないアクセス許可を付与しています。これにより、CNAME が作成されているゾーンを表示できます。

残りの Actions プロパティは、[DNS ゾーン共同作成者組み込みロール](../role-based-access-control/built-in-roles.md#dns-zone-contributor)からコピーしたものです。

> [!NOTE]
> レコード セットを削除できないようにしながらもレコード セットの更新は許可するような Azure カスタム ロールの使い方は、効果的な制御方法とは言えません。 レコード セットの削除は防止できますが、変更は自由にできてしまうためです。  レコード セットに対するレコードの追加や削除といった変更が許可されることになり、すべてのレコードを削除してレコード セットを空にすることもできてしまいます。 DNS 解決の視点から見ると、これはレコード セットが削除された場合と同等の影響があります。

現時点では、Azure Portal でカスタム ロールを定義することはできません。 カスタム ロールは、Azure PowerShell を使用して次のロール定義に基づいて作成できます。

```azurepowershell
# Create new role definition based on input file
New-AzRoleDefinition -InputFile <file path>
```

また、Azure CLI を使用して作成することもできます。

```azurecli
# Create new role definition based on input file
az role create -inputfile <file path>
```

作成したロールは、この記事で前述した組み込みロールの場合と同じ方法で割り当てることができます。

カスタム ロールの作成、管理、および割り当ての方法の詳細については、「[Azure RBAC のカスタム ロール](../role-based-access-control/custom-roles.md)」を参照してください。

## <a name="resource-locks"></a>リソース ロック

Azure Resource Manager は、別のタイプのセキュリティ制御をサポートしています。それはリソースをロックする機能です。 リソース ロックはリソースに適用され、すべてのユーザーおよびロールが対象になります。 詳細については、[「Azure Resource Manager によるリソースのロック」](../azure-resource-manager/management/lock-resources.md)を参照してください。

リソースのロックは 2 種類あります｡**CanNotDelete** と **ReadOnly** です。 これらは、プライベート DNS ゾーンまたは個別のレコード セットのいずれかに適用できます。 以降のセクションでは、一般的なシナリオをいくつか紹介し、そのシナリオにリソース ロックを使用して対応する方法について説明します。

### <a name="protecting-against-all-changes"></a>あらゆる変更に対する保護

変更を防止するには、ゾーンに ReadOnly ロックを適用します。 このロックにより、レコード セットの新規作成と既存のレコード セットの変更または削除を防止できます。

ゾーン レベルのリソース ロックは、Azure Portal を使用して作成できます。  DNS ゾーン ページから **[ロック]** 、 **[+ 追加]** の順に選択します。

![Azure Portal を使用したゾーン レベルのリソース ロック](./media/dns-protect-zones-recordsets/locks1.png)

ゾーン レベルのリソース ロックは、[Azure PowerShell](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcelock?view=latest) を使用して作成することもできます。

```azurepowershell
# Lock a DNS zone

$lvl = "<lock level>"
$lnm = "<lock name>"
$rsc = "<zone name>"
$rty = "Microsoft.Network/DNSZones"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rsc -ResourceType $rty -ResourceGroupName $rsg
```

また、同じ目的を果たせるコマンドを [Azure CLI から実行する](https://docs.microsoft.com/cli/azure/lock?view=azure-cli-latest#az-lock-create)こともできます。

```azurecli
# Lock a DNS zone

az lock create \
--lock-type "<lock level>" \
--name "<lock name>" \
--resource-name "<zone name>" \
--namespace "Microsoft.Network" \
--resource-type "DnsZones" \
--resource-group "<resource group name>"
```

### <a name="protecting-individual-records"></a>個々のレコードの保護

既存の DNS レコード セットが変更されないようにするには、レコード セットに ReadOnly ロックを適用します。

> [!NOTE]
> レコード セットへの CanNotDelete ロックの適用は、制御方法として効果的ではありません。 レコード セットの削除は防止できますが、変更は自由にできてしまうためです。  レコード セットに対するレコードの追加や削除といった変更が許可されることになり、すべてのレコードを削除してレコード セットを空にすることもできてしまいます。 DNS 解決の視点から見ると、これはレコード セットが削除された場合と同等の影響があります。

現時点では、レコード セット レベルのリソース ロックは、Azure PowerShell でのみ構成できます。  Azure Portal と Azure CLI ではサポートされていません。

```azurepowershell
# Lock a DNS record set

$lvl = "<lock level>"
$lnm = "<lock name>"
$rsc = "<zone name>/<record set name>"
$rty = "Microsoft.Network/DNSZones/<record type>"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rsc -ResourceType $rty -ResourceGroupName $rsg
```

### <a name="protecting-against-zone-deletion"></a>ゾーンの削除に対する保護

Azure DNS でゾーンを削除すると、ゾーン内のレコード セットもすべて削除されます。  この操作は元に戻すことができません。 誤って重要なゾーンを削除すると、ビジネスに多大な影響を与えるおそれがあります。  ゾーンの誤削除を防止することが重要です。

ゾーンに CanNotDelete ロックを適用することで、ゾーンが削除されるのを防止できます。 ロックは、子リソースによって継承されます。 ロックは、ゾーン内のレコード セットが削除されるのを防止します。 上記の注で説明したとおり、既存のレコード セットからレコードを削除できるため、効果的な制御方法とは言えません。

代わりに、ゾーン内のレコード セット (SOA レコード セットなど) に対して CanNotDelete ロックを適用してください。 レコード セットが削除されなければ、ゾーンは削除されません。 このロックは、ゾーンの削除を防止しますが、ゾーン内のレコード セットを自由に変更することができます。 ゾーンを削除しようとすると、削除は、Azure Resource Manager によって検出されます。 この削除により、SOA レコード セットも削除されます。SOA はロックされているため、Azure Resource Manager は、呼び出しをブロックします。  結果として、レコード セットは削除されません。

次の PowerShell コマンドを実行すると、指定されたゾーンの SOA レコードに対する CanNotDelete ロックが作成されます。

```azurepowershell
# Protect against zone delete with CanNotDelete lock on the record set

$lvl = "CanNotDelete"
$lnm = "<lock name>"
$rsc = "<zone name>/@"
$rty = "Microsoft.Network/DNSZones/SOA"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rsc -ResourceType $rty -ResourceGroupName $rsg
```

ゾーンの誤削除を防止するもう 1 つの方法は、カスタム ロールを使用することです。 このロールにより、ゾーンの管理に使用されるアカウントに、ゾーンの削除アクセス許可が付与されないようにします。 

ゾーンを削除する必要がある場合は、2 段階の削除操作を強制することができます。

 - まず、ゾーンの削除アクセス許可を付与します。
 - 次に、ゾーンを削除するアクセス許可を付与します。

カスタム ロールは、これらのアカウントでアクセスされるすべてのゾーンに有効です。 サブスクリプション所有者などのゾーンの削除アクセス許可を持つアカウントは、依然としてゾーンを誤って削除する可能性があります。

DNS ゾーン保護のための多層防御手法として、リソースのロックとカスタム ロールという 2 つの方法を同時に使用することもできます。

## <a name="next-steps"></a>次のステップ

* RBAC の操作の詳細については、「[Azure Portal でのアクセス管理の概要](../role-based-access-control/overview.md)」を参照してください。
* リソース ロックの操作の詳細については、「[Azure Resource Manager によるリソースのロック](../azure-resource-manager/management/lock-resources.md)」を参照してください。
