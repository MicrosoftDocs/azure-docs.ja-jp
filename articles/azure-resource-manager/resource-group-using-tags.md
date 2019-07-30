---
title: 論理的に整理するための Azure リソースのタグ付け | Microsoft Docs
description: タグを適用して、課金や管理のために Azure リソースを整理する方法を示します。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/11/2019
ms.author: tomfitz
ms.openlocfilehash: 77175738a0cae5c6185a8ac74e51e9b91b685235
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827929"
---
# <a name="use-tags-to-organize-your-azure-resources"></a>タグを使用した Azure リソースの整理

[!INCLUDE [resource-manager-governance-tags](../../includes/resource-manager-governance-tags.md)]

リソースにタグを適用するには、ユーザーがそのリソースの種類への書き込みアクセス権を保持している必要があります。 すべてのリソースの種類にタグを適用するには、[Contributor](../role-based-access-control/built-in-roles.md#contributor) ロールを使用します。 1 つのリソースの種類だけにタグを適用するには、そのリソースの共同作成者ロールを使用します。 たとえば、仮想マシンにタグを適用するには、[仮想マシン共同作成者](../role-based-access-control/built-in-roles.md#virtual-machine-contributor)を使用します。

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="policies"></a>ポリシー

[Azure Policy](../governance/policy/overview.md) を使用して、タグ付けのルールと規則を強制できます。 ポリシーを作成することで、組織の必要なタグに準拠しないサブスクリプションにリソースがデプロイされるというシナリオを回避します。 タグの適用や非準拠リソースの検索を手動で行う代わりに、デプロイ時に必要なタグを自動的に適用するポリシーを作成できます。 次のセクションでは、タグのポリシーの例を示します。

[!INCLUDE [Tag policies](../../includes/azure-policy-samples-general-tags.md)]

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

*リソース グループ*の既存のタグを表示するには、次のコマンドを使用します。

```azurepowershell-interactive
(Get-AzResourceGroup -Name examplegroup).Tags
```

このスクリプトは次の形式を返します。

```powershell
Name                           Value
----                           -----
Dept                           IT
Environment                    Test
```

*特定のリソース ID に該当するリソース*の既存のタグを表示するには、次のコマンドを使用します。

```azurepowershell-interactive
(Get-AzResource -ResourceId /subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>).Tags
```

*特定の名前とリソース グループに該当するリソース*の既存のタグを表示するには、次のコマンドを使用します。

```azurepowershell-interactive
(Get-AzResource -ResourceName examplevnet -ResourceGroupName examplegroup).Tags
```

*特定のタグが付いたリソース グループ*を取得するには、次のコマンドを使用します。

```azurepowershell-interactive
(Get-AzResourceGroup -Tag @{ Dept="Finance" }).ResourceGroupName
```

*特定のタグが付いたリソース*を取得するには、次のコマンドを使用します。

```azurepowershell-interactive
(Get-AzResource -Tag @{ Dept="Finance"}).Name
```

*特定のタグ名が付いたリソース*を取得するには、次のコマンドを使用します。

```azurepowershell-interactive
(Get-AzResource -TagName Dept).Name
```

リソースまたはリソース グループにタグを適用するたびに、そのリソースまたはリソース グループの既存のタグが上書きされます。 したがって、リソースまたはリソース グループに既存のタグがあるかどうかに基づいて、異なるアプローチを使用する必要があります。

*既存のタグのないリソース グループ*にタグを追加するには、次のコマンドを使用します。

```azurepowershell-interactive
Set-AzResourceGroup -Name examplegroup -Tag @{ Dept="IT"; Environment="Test" }
```

*既存のタグがあるリソース グループ*にタグを追加するには、既存のタグを取得して新しいタグを追加し、タグを適用し直します。

```azurepowershell-interactive
$tags = (Get-AzResourceGroup -Name examplegroup).Tags
$tags.Add("Status", "Approved")
Set-AzResourceGroup -Tag $tags -Name examplegroup
```

*既存のタグのないリソース*にタグを追加するには、次のコマンドを使用します。

```azurepowershell-interactive
$r = Get-AzResource -ResourceName examplevnet -ResourceGroupName examplegroup
Set-AzResource -Tag @{ Dept="IT"; Environment="Test" } -ResourceId $r.ResourceId -Force
```

*既存のタグがあるリソース*にタグを追加するには、次のコマンドを使用します。

```azurepowershell-interactive
$r = Get-AzResource -ResourceName examplevnet -ResourceGroupName examplegroup
$r.Tags.Add("Status", "Approved")
Set-AzResource -Tag $r.Tags -ResourceId $r.ResourceId -Force
```

"*リソース上にある既存のタグを保持せずに*"、リソース グループのすべてのタグをそのリソースに適用するには、次のスクリプトを使用します。

```azurepowershell-interactive
$groups = Get-AzResourceGroup
foreach ($g in $groups)
{
    Get-AzResource -ResourceGroupName $g.ResourceGroupName | ForEach-Object {Set-AzResource -ResourceId $_.ResourceId -Tag $g.Tags -Force }
}
```

"*リソース上にある重複しない既存のタグを保持したうえで*"、リソース グループのすべてのタグをそのリソースに適用するには、次のスクリプトを使用します。

```azurepowershell-interactive
$group = Get-AzResourceGroup "examplegroup"
if ($null -ne $group.Tags) {
    $resources = Get-AzResource -ResourceGroupName $group.ResourceGroupName
    foreach ($r in $resources)
    {
        $resourcetags = (Get-AzResource -ResourceId $r.ResourceId).Tags
        if ($resourcetags)
        {
            foreach ($key in $group.Tags.Keys)
            {
                if (-not($resourcetags.ContainsKey($key)))
                {
                    $resourcetags.Add($key, $group.Tags[$key])
                }
            }
            Set-AzResource -Tag $resourcetags -ResourceId $r.ResourceId -Force
        }
        else
        {
            Set-AzResource -Tag $group.Tags -ResourceId $r.ResourceId -Force
        }
    }
}
```

すべてのタグを削除するには、空のハッシュ テーブルを渡します。

```azurepowershell-interactive
Set-AzResourceGroup -Tag @{} -Name examplegroup
```

## <a name="azure-cli"></a>Azure CLI

*リソース グループ*の既存のタグを表示するには、次のコマンドを使用します。

```azurecli
az group show -n examplegroup --query tags
```

このスクリプトは次の形式を返します。

```json
{
  "Dept"        : "IT",
  "Environment" : "Test"
}
```

*特定の名前、型、リソース グループに該当するリソース*の既存のタグを表示するには、次のコマンドを使用します。

```azurecli
az resource show -n examplevnet -g examplegroup --resource-type "Microsoft.Network/virtualNetworks" --query tags
```

リソースのコレクション内をループ処理するときに、リソース ID ごとにリソースを表示したいことがあります。 完成した例については、この記事で後ほど紹介します。 *特定のリソース ID に該当するリソース*の既存のタグを表示するには、次のコマンドを使用します。

```azurecli
az resource show --id <resource-id> --query tags
```

特定のタグが付いたリソース グループを取得するには、`az group list` を使用します。

```azurecli
az group list --tag Dept=IT
```

特定のタグと値を持つすべてのリソースを取得するには、`az resource list` を使用します。

```azurecli
az resource list --tag Dept=Finance
```

リソースまたはリソース グループにタグを適用するたびに、そのリソースまたはリソース グループの既存のタグが上書きされます。 したがって、リソースまたはリソース グループに既存のタグがあるかどうかに基づいて、異なるアプローチを使用する必要があります。

*既存のタグのないリソース グループ*にタグを追加するには、次のコマンドを使用します。

```azurecli
az group update -n examplegroup --set tags.Environment=Test tags.Dept=IT
```

*既存のタグのないリソース*にタグを追加するには、次のコマンドを使用します。

```azurecli
az resource tag --tags Dept=IT Environment=Test -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

既にタグがあるリソースにタグを追加するには、既存のタグを取得してその値の形式を変更したうえで、既存のタグと新しいタグを再適用します。

```azurecli
jsonrtag=$(az resource show -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks" --query tags)
rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
az resource tag --tags $rt Project=Redesign -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

"*リソース上にある既存のタグを保持せずに*"、リソース グループのすべてのタグをそのリソースに適用するには、次のスクリプトを使用します。

```azurecli
groups=$(az group list --query [].name --output tsv)
for rg in $groups
do
  jsontag=$(az group show -n $rg --query tags)
  t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')
  r=$(az resource list -g $rg --query [].id --output tsv)
  for resid in $r
  do
    az resource tag --tags $t --id $resid
  done
done
```

"*リソースにある既存のタグを保持したうえで*"、リソース グループのすべてのタグをそのリソースに適用するには、次のスクリプトを使用します。

```azurecli
groups=$(az group list --query [].name --output tsv)
for rg in $groups
do
  jsontag=$(az group show -n $rg --query tags)
  t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')
  r=$(az resource list -g $rg --query [].id --output tsv)
  for resid in $r
  do
    jsonrtag=$(az resource show --id $resid --query tags)
    rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
    az resource tag --tags $t$rt --id $resid
  done
done
```

## <a name="templates"></a>テンプレート

[!INCLUDE [resource-manager-tags-in-templates](../../includes/resource-manager-tags-in-templates.md)]

## <a name="portal"></a>ポータル

[!INCLUDE [resource-manager-tag-resource](../../includes/resource-manager-tag-resources.md)]

## <a name="rest-api"></a>REST API

Azure Portal と PowerShell のどちらも、バックグラウンドで [リソース マネージャーの REST API](https://docs.microsoft.com/rest/api/resources/) を使用します。 別の環境にタグ付けを統合する必要がある場合、リソース ID に対する **GET** 操作でタグを取得し、**PATCH** 呼び出しでタグのセットを更新できます。

## <a name="tags-and-billing"></a>タグと課金

タグを使用して課金データをグループ化できます。 たとえば、異なる組織向けに複数の VM を実行している場合は、タグを使用して、コスト センターごとに使用状況をグループ化します。 また、タグを使用すると、運用環境で実行されている VM の課金データなどの、ランタイム環境ごとにコストを分類することもできます。

タグに関する情報は、 [Azure Resource Usage API と RateCard API](../billing/billing-usage-rate-card-overview.md) から、あるいはコンマ区切り値 (CSV) ファイルから取得できます。 使用状況ファイルは [Azure アカウント センター](https://account.azure.com/Subscriptions)または Azure portal からダウンロードします。 詳細については、「[Azure の請求書と毎日の使用状況データをダウンロードまたは表示する](../billing/billing-download-azure-invoice-daily-usage-date.md)」を参照してください。 Azure アカウント センターから使用状況ファイルをダウンロードする場合は、 **[バージョン 2]** を選択します。 課金のタグがサポートされているサービスの場合、タグは **[Tags]** 列に表示されます。

REST API の操作については、「 [Azure Billing REST API Reference (Azure Billing REST API リファレンス)](/rest/api/billing/)」を参照してください。

## <a name="next-steps"></a>次の手順

* すべてのリソースの種類で、タグがサポートされるわけではありません。 リソースの種類にタグを適用することができるかどうかを確認するには、[Azure リソースに対するタグのサポート](tag-support.md)に関する記事を参照してください。
* ポータルの使用方法の概要については、「[Azure ポータルを使用した Azure リソースの管理](manage-resource-groups-portal.md)」をご覧ください。  
