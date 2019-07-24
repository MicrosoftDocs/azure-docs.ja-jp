---
title: Azure DNS ゾーンとレコードを保護する
description: Microsoft Azure DNS で DNS ゾーンとレコード セットを保護する方法
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 12/4/2018
ms.author: victorh
ms.openlocfilehash: 9340a43eb88b4be03c0f0ccc0d07a32f22a9001c
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/11/2019
ms.locfileid: "55997381"
---
# <a name="how-to-protect-dns-zones-and-records"></a>DNS ゾーンとレコードを保護する方法

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

DNS ゾーンと DNS レコードは、重要なリソースです。 DNS ゾーンはもちろんのこと、DNS レコードが 1 つ削除されただけで、サービス全体が停止する可能性があります。  そのため、DNS ゾーンと DNS レコードは、承認されていない変更や意図しない変更が加えられないように保護する必要があります。

この記事では、Azure DNS でこのような変更から DNS ゾーンおよび DNS レコードを保護する方法について説明します。  Azure Resource Manager によって提供される 2 つの強力なセキュリティ機能、[ロールベースのアクセス制御](../role-based-access-control/overview.md)と[リソース ロック](../azure-resource-manager/resource-group-lock-resources.md)を使用します。

## <a name="role-based-access-control"></a>ロールベースのアクセス制御

Azure のロールベースのアクセス制御 (RBAC) では、Azure ユーザー、グループ、およびリソースのアクセス権を詳細に管理できます。 RBAC を使用すると、職務に必要な範囲のアクセス権をユーザーに的確に付与することができます。 RBAC を使用したアクセス管理の詳細については、[ロールベースのアクセス制御の概要](../role-based-access-control/overview.md)に関するページを参照してください。

### <a name="the-dns-zone-contributor-role"></a>DNS ゾーン共同作成者ロール

DNS ゾーン共同作成者ロールは、Azure に用意されている DNS リソース管理用の組み込みロールです。  DNS ゾーン共同作成者アクセス許可を割り当てられたユーザーまたはグループは、DNS リソースを管理することができます。他のタイプのリソースは管理できません。

たとえば、リソース グループ *myzones* に Contoso Corporation の 5 つのゾーンが含まれているとします。 このリソース グループに対する DNS ゾーン共同作成者アクセス許可を DNS 管理者に付与すると、管理者はこれらの DNS ゾーンを完全に制御できるようになります。 DNS 管理者が仮想マシンの作成や停止などを実行できないように、必要のないアクセス許可は付与しないようにしてください。

RBAC アクセス許可の割り当ては、[Azure Portal](../role-based-access-control/role-assignments-portal.md) を使用すると最も簡単に行うことができます。  リソース グループの **[アクセス制御 (IAM)]** を開き、**[追加]**、**[DNS Zone Contributor (DNS ゾーン共同作成者)]** ロールの順に選択し、アクセス許可の付与が必要なユーザーまたはグループを選択します。

![Azure Portal を使用したリソース グループ レベルの RBAC](./media/dns-protect-zones-recordsets/rbac1.png)

アクセス許可は、[Azure PowerShell を使用して付与する](../role-based-access-control/role-assignments-powershell.md)こともできます。

```azurepowershell
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group
New-AzRoleAssignment -SignInName "<user email address>" -RoleDefinitionName "DNS Zone Contributor" -ResourceGroupName "<resource group name>"
```

また、同じ目的を果たせるコマンドを [Azure CLI から実行する](../role-based-access-control/role-assignments-cli.md)こともできます。

```azurecli
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group
azure role assignment create --signInName "<user email address>" --roleName "DNS Zone Contributor" --resourceGroup "<resource group name>"
```

### <a name="zone-level-rbac"></a>ゾーン レベルの RBAC

Azure RBAC のルールは、サブスクリプション、リソース グループ、または個々のリソースに適用できます。 Azure DNS の場合、このリソースとは、個々の DNS ゾーンか個々のレコード セットを指します。

たとえば、リソース グループ *myzones* にゾーン *contoso.com* とサブゾーン *customers.contoso.com* が含まれていて、サブゾーンには顧客アカウントごとに CNAME レコードが作成されているとします。  これらの CNAME レコードの管理に使用するアカウントに、*customers.contoso.com* ゾーンにのみレコードを作成できるアクセス許可を割り当て、他のゾーンにはアクセスできないようにするような状況が該当します。

ゾーン レベルの RBAC アクセス許可は、Azure Portal から付与できます。  ゾーンの **[アクセス制御 (IAM)]** を開き、**[追加]**、**[DNS Zone Contributor (DNS ゾーン共同作成者)]** ロールの順に選択し、アクセス許可の付与が必要なユーザーまたはグループを選択します。

![Azure Portal を使用した DNS ゾーン レベルの RBAC](./media/dns-protect-zones-recordsets/rbac2.png)

アクセス許可は、[Azure PowerShell を使用して付与する](../role-based-access-control/role-assignments-powershell.md)こともできます。

```azurepowershell
# Grant 'DNS Zone Contributor' permissions to a specific zone
New-AzRoleAssignment -SignInName "<user email address>" -RoleDefinitionName "DNS Zone Contributor" -ResourceGroupName "<resource group name>" -ResourceName "<zone name>" -ResourceType Microsoft.Network/DNSZones
```

また、同じ目的を果たせるコマンドを [Azure CLI から実行する](../role-based-access-control/role-assignments-cli.md)こともできます。

```azurecli
# Grant 'DNS Zone Contributor' permissions to a specific zone
azure role assignment create --signInName <user email address> --roleName "DNS Zone Contributor" --resource-name <zone name> --resource-type Microsoft.Network/DNSZones --resource-group <resource group name>
```

### <a name="record-set-level-rbac"></a>レコード セット レベルの RBAC

RBAC のレベルにはもう 1 つあります。 Contoso Corporation のメール管理者を考えてみましょう。このメール管理者は、"contoso.com" ゾーンの頂点にある MX レコードと TXT レコードへのアクセスを必要としています。  他の MX レコードや TXT レコード、および他のタイプのレコードにはアクセスする必要がありません。  Azure DNS を使用すると、メール管理者がアクセスする必要のあるレコードのみを対象に、レコード セット レベルでアクセス許可を割り当てることができます。  メール管理者は必要な権限のみが付与され、他の変更を加えることはできません。

レコード セット レベルの RBAC アクセス許可は、Azure portal の [レコード セット] ページにある **[ユーザー]** ボタンを使用して構成できます。

![Azure Portal を使用したレコード セット レベルの RBAC](./media/dns-protect-zones-recordsets/rbac3.png)

レコード セット レベルの RBAC アクセス許可は、[Azure PowerShell を使用して付与する](../role-based-access-control/role-assignments-powershell.md)こともできます。

```azurepowershell
# Grant permissions to a specific record set
New-AzRoleAssignment -SignInName "<user email address>" -RoleDefinitionName "DNS Zone Contributor" -Scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"
```

また、同じ目的を果たせるコマンドを [Azure CLI から実行する](../role-based-access-control/role-assignments-cli.md)こともできます。

```azurecli
# Grant permissions to a specific record set
azure role assignment create --signInName "<user email address>" --roleName "DNS Zone Contributor" --scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"
```

### <a name="custom-roles"></a>カスタム ロール

組み込みの DNS ゾーン共同作成者ロールでも DNS リソースを完全に制御できます。 カスタム Azure ロールを自分で作成して、さらにきめ細かくリソースを制御することもできます。

Contoso Corporation が顧客アカウントごとの CNAME レコードをゾーン *customers.contoso.com* に作成している例をもう一度考えてみましょう。  これらの CNAME の管理に使用するアカウントには、CNAME レコードのみを管理できるアクセス許可を付与する必要があります。  MX レコードなどの他のタイプのレコードの変更や、ゾーンの削除などのゾーン レベルの操作は実行できないようにします。

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
        "/subscriptions/ c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
}
```

上記の Actions プロパティでは、次のような DNS に固有のアクセス許可を定義しています。

* `Microsoft.Network/dnsZones/CNAME/*` は CNAME レコードを完全に制御できるアクセス許可を付与しています。
* `Microsoft.Network/dnsZones/read` は DNS ゾーンを読み取ることができるが変更はできないアクセス許可を付与しています。これにより、CNAME が作成されているゾーンを表示できます。

残りの Actions プロパティは、[DNS ゾーン共同作成者組み込みロール](../role-based-access-control/built-in-roles.md#dns-zone-contributor)からコピーしたものです。

> [!NOTE]
> レコード セットを削除できないようにしながらもレコード セットの更新は許可するようなカスタム RBAC ロールの作成は、効果的な制御方法とは言えません。 レコード セットの削除は防止できますが、変更は自由にできてしまうためです。  レコード セットに対するレコードの追加や削除といった変更が許可されることになり、すべてのレコードを削除してレコード セットを空にすることもできてしまいます。 DNS 解決の視点から見ると、これはレコード セットが削除された場合と同等の影響があります。

現時点では、Azure Portal でカスタム ロールを定義することはできません。 カスタム ロールは、Azure PowerShell を使用して次のロール定義に基づいて作成できます。

```azurepowershell
# Create new role definition based on input file
New-AzRoleDefinition -InputFile <file path>
```

また、Azure CLI を使用して作成することもできます。

```azurecli
# Create new role definition based on input file
azure role create -inputfile <file path>
```

作成したロールは、この記事で前述した組み込みロールの場合と同じ方法で割り当てることができます。

カスタム ロールの作成、管理、および割り当ての方法の詳細については、「[Azure RBAC のカスタム ロール](../role-based-access-control/custom-roles.md)」を参照してください。

## <a name="resource-locks"></a>リソース ロック

Azure Resource Manager は、RBAC に加えて、別のタイプのセキュリティ制御をサポートしています。それはリソースをロックする機能です。 RBAC ルールでは特定のユーザーやグループが行う操作を制御できますが、リソース ロックはリソースに適用され、すべてのユーザーとロールが対象になります。 詳細については、[「Azure Resource Manager によるリソースのロック」](../azure-resource-manager/resource-group-lock-resources.md)を参照してください。

リソースのロックは 2 種類あります｡**CanNotDelete** と **ReadOnly** です。 これらは、DNS ゾーンと個別のレコード セットのいずれかに適用できます。  以降のセクションでは、一般的なシナリオをいくつか紹介し、そのシナリオにリソース ロックを使用して対応する方法について説明します。

### <a name="protecting-against-all-changes"></a>あらゆる変更に対する保護

一切の変更を防止するには、ゾーンに対して ReadOnly ロックを適用します。  これにより、レコード セットの新規作成と既存のレコード セットに対する変更や削除を防止できます。

ゾーン レベルのリソース ロックは、Azure Portal を使用して作成できます。  DNS ゾーン ページから **[ロック]**、**[+ 追加]** の順に選択します。

![Azure Portal を使用したゾーン レベルのリソース ロック](./media/dns-protect-zones-recordsets/locks1.png)

ゾーン レベルのリソース ロックは、Azure PowerShell を使用して作成することもできます。

```azurepowershell
# Lock a DNS zone
New-AzResourceLock -LockLevel <lock level> -LockName <lock name> -ResourceName <zone name> -ResourceType Microsoft.Network/DNSZones -ResourceGroupName <resource group name>
```

現時点では、Azure CLI では Azure リソース ロックの構成はサポートされていません。

### <a name="protecting-individual-records"></a>個々のレコードの保護

既存の DNS レコード セットが変更されないようにするには、レコード セットに ReadOnly ロックを適用します。

> [!NOTE]
> レコード セットへの CanNotDelete ロックの適用は、制御方法として効果的ではありません。 レコード セットの削除は防止できますが、変更は自由にできてしまうためです。  レコード セットに対するレコードの追加や削除といった変更が許可されることになり、すべてのレコードを削除してレコード セットを空にすることもできてしまいます。 DNS 解決の視点から見ると、これはレコード セットが削除された場合と同等の影響があります。

現時点では、レコード セット レベルのリソース ロックは、Azure PowerShell でのみ構成できます。  Azure Portal と Azure CLI ではサポートされていません。

```azurepowershell
# Lock a DNS record set
New-AzResourceLock -LockLevel <lock level> -LockName "<lock name>" -ResourceName "<zone name>/<record set name>" -ResourceType "Microsoft.Network/DNSZones/<record type>" -ResourceGroupName "<resource group name>"
```

### <a name="protecting-against-zone-deletion"></a>ゾーンの削除に対する保護

Azure DNS ゾーンを削除すると、ゾーン内のレコード セットもすべて削除されます。  削除操作は元に戻すことができません。  誤って重要なゾーンを削除すると、ビジネスに多大な影響を与えるおそれがあります。  そのため、意図せずゾーンが削除されるのを防ぐことが重要です。

ゾーンに CanNotDelete ロックを適用することで、ゾーンが削除されるのを防止できます。  ただし、ロックは子リソースに継承されるため、ゾーン内のすべてのレコード セットが削除できないようになりますが、この措置が適切でない場合もあります。  また、上記の注で説明したとおり、既存のレコード セットからレコードを削除できるため、効果的な制御方法とは言えません。

代わりに、ゾーン内の、SOA レコード セットなどのレコード セットに CanNotDelete ロックを適用することを検討してください。  レコード セットを削除せずにゾーンを削除することはできないため、この措置によってゾーンの削除は防止される一方、ゾーン内のレコード セットは自由に変更できます。 ゾーンを削除しようとすると、この操作によって SOA レコード セットも削除されることが Azure Resource Manager によって検出され、SOA がロックされているため呼び出しがブロックされます。  結果として、レコード セットは削除されません。

次の PowerShell コマンドを実行すると、指定されたゾーンの SOA レコードに対する CanNotDelete ロックが作成されます。

```azurepowershell
# Protect against zone delete with CanNotDelete lock on the record set
New-AzResourceLock -LockLevel CanNotDelete -LockName "<lock name>" -ResourceName "<zone name>/@" -ResourceType" Microsoft.Network/DNSZones/SOA" -ResourceGroupName "<resource group name>"
```

意図せずゾーンが削除されることを防ぐもう 1 つの方法として、カスタム ロールを使用し、ゾーンの管理に使われるオペレーター アカウントとサービス アカウントにゾーンの削除アクセス許可が付与されないようにするという方法があります。 ゾーンの削除が必要な場合は、まずゾーンの削除アクセス許可を付与 (誤ったゾーンを削除しないように、ゾーン スコープでのアクセス許可を付与) し、次にゾーンを削除するという、2 段階の削除操作を強制することができます。

この 2 つ目の方法には、前述のアカウントがアクセスするすべてのゾーンに有効であり、ロックを作成することを覚えておく必要がないという利点があります。 しかし、この方法でも、サブスクリプション所有者のようなゾーンの削除アクセス許可を持つアカウントは、重要なゾーンを誤って削除する可能性があります。

DNS ゾーン保護のための多層防御手法としては、リソースのロックとカスタム ロールという 2 つの方法を同時に使用することも可能です。

## <a name="next-steps"></a>次の手順

* RBAC の操作の詳細については、「[Azure Portal でのアクセス管理の概要](../role-based-access-control/overview.md)」を参照してください。
* リソース ロックの操作の詳細については、「[Azure Resource Manager によるリソースのロック](../azure-resource-manager/resource-group-lock-resources.md)」を参照してください。
