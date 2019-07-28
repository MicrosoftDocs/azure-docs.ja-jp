---
title: 初歩的なクエリのサンプル
description: Azure Resource Graph を使用して、リソースのカウント、リソースの並べ替え、特定のタグによるクエリなど、いくつかの初歩的なクエリを実行します。
author: dcthegeek
ms.author: dacoulte
ms.date: 04/23/2019
ms.topic: quickstart
ms.service: resource-graph
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 98b05f74f0d6f7d20b5aa7ed77047818f217f147
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2019
ms.locfileid: "64691188"
---
# <a name="starter-resource-graph-queries"></a>Resource Graph の初歩的なクエリ

Azure Resource Graph でクエリを理解する最初の手順は、[クエリ言語](../concepts/query-language.md)の基本を理解することです。 [Azure Data Explorer](../../../data-explorer/data-explorer-overview.md) にまだ精通していない場合、探しているリソースに対する要求を作成する方法を理解するための基礎を確認することをお勧めします。

次のスターター クエリを説明します。

> [!div class="checklist"]
> - [Azure リソースの数](#count-resources)
> - [名前で並べ替えられたリソースの一覧表示](#list-resources)
> - [降順の名前で順序付けられたすべての仮想マシンの表示](#show-vms)
> - [名前とその OS の種類による最初の 5 つの仮想マシンの表示](#show-sorted)
> - [仮想マシンの数 (OS の種類別)](#count-os)
> - [ストレージを含むリソースの表示](#show-storage)
> - [パブリック IP アドレスの一覧表示](#list-publicip)
> - [サブスクリプションで構成されている IP アドレスを持つリソースの数](#count-resources-by-ip)
> - [特定のタグ値が付いたリソースの一覧表示](#list-tag)
> - [特定のタグ値を持つすべてのストレージ アカウントの一覧表示](#list-specific-tag)
> - [仮想マシン リソースの別名の表示](#show-aliases)
> - [特定の別名の個別の値の表示](#distinct-alias-values)

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free) を作成してください。

[!INCLUDE [az-powershell-update](../../../../includes/updated-for-az.md)]

## <a name="language-support"></a>言語のサポート

Azure CLI (拡張経由) および Azure PowerShell (モジュール経由) は、Azure Resource Graph をサポートします。 次のクエリを実行する前に、環境が準備できていることを確認します。 選択するシェル環境をインストールし、検証する手順については、[Azure CLI](../first-query-azurecli.md#add-the-resource-graph-extension) および [Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module) を参照してください。

## <a name="a-namecount-resourcescount-azure-resources"></a><a name="count-resources"/>Azure リソースの数の取得

このクエリは、あなたがアクセスしたサブスクリプションに存在する Azure リソースの数を返します。 また、選択するシェルに適切な Azure Resource Graph コンポーネントがインストールされ、正常に動作していることを検証するために使用する場合にも有効なクエリです。

```kusto
summarize count()
```

```azurecli-interactive
az graph query -q "summarize count()"
```

```azurepowershell-interactive
Search-AzGraph -Query "summarize count()"
```

## <a name="a-namelist-resourceslist-resources-sorted-by-name"></a><a name="list-resources"/>名前で並べ替えられたリソースの一覧表示

このクエリは、あらゆる種類のリソースを返しますが、返されるプロパティは **name**、**type**、**location** に限られます。 `order by` を使用することにより、これらのプロパティが **name** プロパティの昇順 (`asc`) で並べ替えらえます。

```kusto
project name, type, location
| order by name asc
```

```azurecli-interactive
az graph query -q "project name, type, location | order by name asc"
```

```azurepowershell-interactive
Search-AzGraph -Query "project name, type, location | order by name asc"
```

## <a name="a-nameshow-vmsshow-all-virtual-machines-ordered-by-name-in-descending-order"></a><a name="show-vms"/>降順の名前で順序付けられたすべての仮想マシンの表示

仮想マシン (`Microsoft.Compute/virtualMachines`型) のみを一覧表示したければ、結果において **type** プロパティと突き合わせます。 前のクエリと同様に、`desc` は `order by` を降順に変更します。 `=~`の種類一致により、Resource Graph では大文字と小文字が区別されないことを示します。

```kusto
project name, location, type
| where type =~ 'Microsoft.Compute/virtualMachines'
| order by name desc
```

```azurecli-interactive
az graph query -q "project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

```azurepowershell-interactive
Search-AzGraph -Query "project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

## <a name="a-nameshow-sortedshow-first-five-virtual-machines-by-name-and-their-os-type"></a><a name="show-sorted"/>名前とその OS の種類による最初の 5 つの仮想マシンの表示

このクエリは、名前別に整理された 5 つの一致するレコードを取得するためのみに `limit` を使用します。 Azure リソースの種類は `Microsoft.Compute/virtualMachines` です。 `project` により、Azure Resource Graph に含めるプロパティを説明します。

```kusto
where type =~ 'Microsoft.Compute/virtualMachines'
| project name, properties.storageProfile.osDisk.osType
| top 5 by name desc
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

## <a name="a-namecount-oscount-virtual-machines-by-os-type"></a><a name="count-os"/>仮想マシンの数の取得 (OS の種類別)

前のクエリに基づき、Azure リソースの種類 `Microsoft.Compute/virtualMachines` によりまだ制限していますが、返されるレコード数を制限しなくなっています。
代わりに、プロパティで値をグループ化し、集計する方法を定義するために`summarize` および `count()` を使用しました。この例では、`properties.storageProfile.osDisk.osType`です。 この文字列が完全なオブジェクトにおいてどのように見えるかという例については、[ リソースの確認‐仮想マシンの検出 ](../concepts/explore-resources.md#virtual-machine-discovery)を参照してください。

```kusto
where type =~ 'Microsoft.Compute/virtualMachines'
| summarize count() by tostring(properties.storageProfile.osDisk.osType)
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

同じクエリを記述するさまざまな方法は、プロパティを `extend` し、クエリ内で使用するために一時的に名前を付けます。この場合は **os** です。 前の例に示すように、**os** が `summarize` および `count()` により使用されます。

```kusto
where type =~ 'Microsoft.Compute/virtualMachines'
| extend os = properties.storageProfile.osDisk.osType
| summarize count() by tostring(os)
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

> [!NOTE]
> `=~` によりプロパティの大文字と小文字を区別しないことが可能である一方、クエリにおけるプロパティの使用 (**properties.storageProfile.osDisk.osType** 等) には正しいケースを必要とすることにご注意ください。 プロパティが正しくない場合でも値を返せますが、グループ化または集計が不正確になる可能性があります。

## <a name="a-nameshow-storageshow-resources-that-contain-storage"></a><a name="show-storage"/>ストレージを含むリソースの表示

一致する種類を明示的に定義する代わりに、このクエリの例により`contains`単語**ストレージ**する任意の Azure リソースが見つかります。

```kusto
where type contains 'storage' | distinct type
```

```azurecli-interactive
az graph query -q "where type contains 'storage' | distinct type"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type contains 'storage' | distinct type"
```

## <a name="a-namelist-publiciplist-all-public-ip-addresses"></a><a name="list-publicip"/>すべてのパブリック IP アドレスの一覧表示

前のクエリと同様に、type に **publicIPAddresses** という語を含むものをすべて検索します。
このクエリは、そのパターンを拡張して、**properties.ipAddress**
`isnotempty` の場合にのみ結果を取得し、**properties.ipAddress** のみを返し、結果を上位 100 件に制限 (`limit`) します。
100. 選択したシェルによって引用符のエスケープが必要となる場合があります。

```kusto
where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress)
| project properties.ipAddress
| limit 100
```

```azurecli-interactive
az graph query -q "where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | project properties.ipAddress | limit 100"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | project properties.ipAddress | limit 100"
```

## <a name="a-namecount-resources-by-ipcount-resources-that-have-ip-addresses-configured-by-subscription"></a><a name="count-resources-by-ip"/>サブスクリプションで構成されている IP アドレスを持つリソースの数の取得

前例のクエリを使用し、`summarize` および `count()` を追加して、構成された IP アドレスをもつリソースのサブスクリプションにより一覧を取得できます。

```kusto
where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress)
| summarize count () by subscriptionId
```

```azurecli-interactive
az graph query -q "where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | summarize count () by subscriptionId"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | summarize count () by subscriptionId"
```

## <a name="a-namelist-taglist-resources-with-a-specific-tag-value"></a><a name="list-tag"/>特定のタグ値が付いたリソースの一覧表示

タグなどの Azure リソースの種類以外のプロパティによる結果を制限することができます。 この例では、**Internal** という値を持つ、**Environment** というタグ名の Azure リソースをフィルターで抽出しています。

```kusto
where tags.environment=~'internal'
| project name
```

```azurecli-interactive
az graph query -q "where tags.environment=~'internal' | project name"
```

```azurepowershell-interactive
Search-AzGraph -Query "where tags.environment=~'internal' | project name"
```

そのリソースが持っているタグとその値も得る必要がある場合は、`project` キーワードに **tags** プロパティを追加します。

```kusto
where tags.environment=~'internal'
| project name, tags
```

```azurecli-interactive
az graph query -q "where tags.environment=~'internal' | project name, tags"
```

```azurepowershell-interactive
Search-AzGraph -Query "where tags.environment=~'internal' | project name, tags"
```

## <a name="a-namelist-specific-taglist-all-storage-accounts-with-specific-tag-value"></a><a name="list-specific-tag"/>特定のタグ値を持つすべてのストレージ アカウントの一覧表示

前の例のフィルター機能を組み合わせて、**type** プロパティで Azure リソースの種類をフィルター処理してみましょう。 このクエリでは、特定の種類の Azure リソースを抽出する検索が、さらに特定のタグ名と値で制限されています。

```kusto
where type =~ 'Microsoft.Storage/storageAccounts'
| where tags['tag with a space']=='Custom value'
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

> [!NOTE]
> この例では`=~`の条件付きの代わりに、`==`をマッチングに使用しています。 `==` は大文字小文字が区別します。

## <a name="a-nameshow-aliasesshow-aliases-for-a-virtual-machine-resource"></a><a name="show-aliases"/>仮想マシン リソースの別名の表示

[Azure ポリシーの別名](../../policy/concepts/definition-structure.md#aliases)は、リソースのコンプライアンスを管理するために Azure Policy によって使用されます。 Azure Resource Graph は、リソースの種類の "_別名_" を返すことができます。 これらの値は、カスタム ポリシー定義を作成するときに別名の現在の値を比較するのに役立ちます。 _aliases_ 配列は、既定ではクエリの結果に提供されません。 明示的に結果に追加するには、`project aliases` を使用します。

```kusto
where type =~ 'Microsoft.Compute/virtualMachines'
| limit 1
| project aliases
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | limit 1 | project aliases"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | limit 1 | project aliases"
```

## <a name="a-namedistinct-alias-valuesshow-distinct-values-for-a-specific-alias"></a><a name="distinct-alias-values"/>特定の別名の個別の値の表示

単一のリソースの別名の値を確認することは有用ですが、それだけでは Azure Resource Graph を使用して複数のサブスクリプションにわたってクエリを実行することの真の価値は表されません。 この例では、特定の別名のすべての値を調べて、個別の値を返します。

```kusto
where type=~'Microsoft.Compute/virtualMachines'
| extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType']
| distinct tostring(alias)"
```

```azurecli-interactive
az graph query -q "where type=~'Microsoft.Compute/virtualMachines' | extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType'] | distinct tostring(alias)"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type=~'Microsoft.Compute/virtualMachines' | extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType'] | distinct tostring(alias)"
```

## <a name="next-steps"></a>次の手順

- [クエリ言語](../concepts/query-language.md)の詳細について学習します
- [その他のリソース](../concepts/explore-resources.md)
- [高度なクエリ](advanced.md)のサンプルを参照してください