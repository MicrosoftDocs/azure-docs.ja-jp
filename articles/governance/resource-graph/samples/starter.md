---
title: 初歩的なクエリのサンプル
description: Azure Resource Graph を使用して、リソースのカウント、リソースの並べ替え、特定のタグによるクエリなど、いくつかの初歩的なクエリを実行します。
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/04/2019
ms.topic: quickstart
ms.service: resource-graph
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 2ba48e2a21bdee0c5698bdfa314dd3bf462c1c7e
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2019
ms.locfileid: "59267771"
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
> - [すべてのパブリック IP アドレスの一覧表示](#list-publicip)
> - [サブスクリプションで構成されている IP アドレスを持つリソースの数](#count-resources-by-ip)
> - [特定のタグ値が付いたリソースの一覧表示](#list-tag)
> - [特定のタグ値を持つすべてのストレージ アカウントの一覧表示](#list-specific-tag)

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free) を作成してください。

[!INCLUDE [az-powershell-update](../../../../includes/updated-for-az.md)]

## <a name="language-support"></a>言語のサポート

Azure CLI (拡張経由) および Azure PowerShell (モジュール経由) は、Azure Resource Graph をサポートします。 次のクエリを実行する前に、環境が準備できていることを確認します。 選択するシェル環境をインストールし、検証する手順については、[Azure CLI](../first-query-azurecli.md#add-the-resource-graph-extension) および [Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module) を参照してください。

## <a name="count-resources"></a>Azure リソースの数

このクエリは、あなたがアクセスしたサブスクリプションに存在する Azure リソースの数を返します。 また、選択するシェルに適切な Azure Resource Graph コンポーネントがインストールされ、正常に動作していることを検証するために使用する場合にも有効なクエリです。

```Query
summarize count()
```

```azurecli-interactive
az graph query -q "summarize count()"
```

```azurepowershell-interactive
Search-AzGraph -Query "summarize count()"
```

## <a name="list-resources"></a>名前で並べ替えられたリソースの一覧表示

このクエリは、あらゆる種類のリソースを返しますが、返されるプロパティは **name**、**type**、**location** に限られます。 `order by` を使用することにより、これらのプロパティが **name** プロパティの昇順 (`asc`) で並べ替えらえます。

```Query
project name, type, location
| order by name asc
```

```azurecli-interactive
az graph query -q "project name, type, location | order by name asc"
```

```azurepowershell-interactive
Search-AzGraph -Query "project name, type, location | order by name asc"
```

## <a name="show-vms"></a>降順の名前で順序付けられたすべての仮想マシンの表示

仮想マシン (`Microsoft.Compute/virtualMachines`型) のみを一覧表示したければ、結果において **type** プロパティと突き合わせます。 前のクエリと同様に、`desc` は `order by` を降順に変更します。 `=~`の種類一致により、Resource Graph では大文字と小文字が区別されないことを示します。

```Query
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

## <a name="show-sorted"></a>名前とその OS の種類による最初の 5 つの仮想マシンの表示

このクエリは、名前別に整理された 5 つの一致するレコードを取得するためのみに `limit` を使用します。 Azure リソースの種類は `Microsoft.Compute/virtualMachines` です。 `project` により、含めるプロパティを Azure Resource Graph に指示しています。

```Query
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

## <a name="count-os"></a>仮想マシンの数 (OS の種類別)

前のクエリに基づき、Azure リソースの種類 `Microsoft.Compute/virtualMachines` によりまだ制限していますが、返されるレコード数を制限しなくなっています。
代わりに、プロパティで値をグループ化し、集計する方法を定義するために`summarize` および `count()` を使用しました。この例では、`properties.storageProfile.osDisk.osType`です。 この文字列が完全なオブジェクトにおいてどのように見えるかという例については、[ リソースの確認‐仮想マシンの検出 ](../concepts/explore-resources.md#virtual-machine-discovery)を参照してください。

```Query
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

```Query
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

## <a name="show-storage"></a>ストレージを含むリソースの表示

一致する種類を明示的に定義する代わりに、このクエリの例により`contains`単語**ストレージ**する任意の Azure リソースが見つかります。

```Query
where type contains 'storage' | distinct type
```

```azurecli-interactive
az graph query -q "where type contains 'storage' | distinct type"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type contains 'storage' | distinct type"
```

## <a name="list-publicip"></a>パブリック IP アドレスの一覧表示

前のクエリと同様に、type に **publicIPAddresses** という語を含むものをすべて検索します。
このクエリは、そのパターンを拡張して、**properties.ipAddress**
`isnotempty` の場合にのみ結果を取得し、**properties.ipAddress** のみを、その結果を上位 100 件に制限 (`limit`) して返します
100. 選択したシェルによって引用符のエスケープが必要となる場合があります。

```Query
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

## <a name="count-resources-by-ip"></a>サブスクリプションで構成されている IP アドレスを持つリソースの数

前例のクエリを使用し、`summarize` および `count()` を追加して、構成された IP アドレスをもつリソースのサブスクリプションにより一覧を取得できます。

```Query
where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress)
| summarize count () by subscriptionId
```

```azurecli-interactive
az graph query -q "where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | summarize count () by subscriptionId"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | summarize count () by subscriptionId"
```

## <a name="list-tag"></a>特定のタグ値が付いたリソースの一覧表示

タグなどの Azure リソースの種類以外のプロパティによる結果を制限することができます。 この例では、**Internal** という値を持つ、**Environment** というタグ名の Azure リソースをフィルターで抽出しています。

```Query
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

```Query
where tags.environment=~'internal'
| project name, tags
```

```azurecli-interactive
az graph query -q "where tags.environment=~'internal' | project name, tags"
```

```azurepowershell-interactive
Search-AzGraph -Query "where tags.environment=~'internal' | project name, tags"
```

## <a name="list-specific-tag"></a>特定のタグ値を持つすべてのストレージ アカウントの一覧表示

前の例のフィルター機能を組み合わせて、**type** プロパティで Azure リソースの種類をフィルター処理してみましょう。 このクエリでは、特定の種類の Azure リソースを抽出する検索が、さらに特定のタグ名と値で制限されています。

```Query
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
> この例では`=~`の条件付きの代わりに、`==`をマッチングに使用しています。 `==` では、大文字と小文字が区別されます。

## <a name="next-steps"></a>次の手順

- [クエリ言語](../concepts/query-language.md)の詳細について学習します
- [その他のリソース](../concepts/explore-resources.md)
- [高度なクエリ](advanced.md)のサンプルを参照してください