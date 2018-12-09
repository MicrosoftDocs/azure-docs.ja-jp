---
title: Azure Resource Graph の高度なクエリ
description: いくつかの高度なクエリを実行するには、Azure Resource Graph を使用します。
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/22/2018
ms.topic: quickstart
ms.service: resource-graph
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: 43cf9f5ec0f9c265efa0e59eadbf6c9bbe4f7c3f
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2018
ms.locfileid: "51682881"
---
# <a name="advanced-resource-graph-queries"></a>Resource Graph の高度なクエリ

Azure Resource Graph でクエリを理解する最初の手順は、[クエリ言語](../concepts/query-language.md)の基本を理解することです。 [Azure Data Explorer](../../../data-explorer/data-explorer-overview.md) にまだ精通していない場合、探しているリソースに対する要求を作成する方法を理解するための基礎を確認することをお勧めします。

次の高度なクエリを説明します。

> [!div class="checklist"]
> - [VMSS の容量とサイズを取得します](#vmss-capacity)
> - [すべてのタグ名を一覧表示します](#list-all-tags)
> - [ regexに一致する仮想マシン](#vm-regex)

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free) を作成してください。

## <a name="language-support"></a>言語のサポート

Azure CLI (拡張経由) および Azure PowerShell (モジュール経由) は、Azure Resource Graph をサポートします。 次のクエリを実行する前に、環境が準備できていることを確認します。 選択するシェル環境をインストールし、検証する手順については、[Azure CLI](../first-query-azurecli.md#add-the-resource-graph-extension) および [Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module) を参照してください。

## <a name="vmss-capacity"></a>仮想マシン スケール セットの容量とサイズを取得する

このクエリでは、仮想マシン スケール セットのリソースを検索し、仮想マシンのサイズ、スケール セットの容量などのさまざまな詳細情報を取得します。 このクエリは、`toint()` 関数を使用して、容量を分類できるよう数値にキャストしています。 最後に、列の名前をカスタムの名前付きプロパティに変更します。

```Query
where type=~ 'microsoft.compute/virtualmachinescalesets'
| where name contains 'contoso'
| project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name
| order by Capacity desc
```

```azurecli-interactive
az graph query -q "where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"
```

```azurepowershell-interactive
Search-AzureRmGraph -Query "where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"
```

## <a name="list-all-tags"></a>すべてのタグ名を一覧表示します

このクエリは、タグを使用して開始し、全ての独自のタグ名とその対応する種類を一覧表示する JSON オブジェクトを構築します。

```Query
project tags
| summarize buildschema(tags)
```

```azurecli-interactive
az graph query -q "project tags | summarize buildschema(tags)"
```

```azurepowershell-interactive
Search-AzureRmGraph -Query "project tags | summarize buildschema(tags)"
```

## <a name="vm-regex"></a> regexに一致する仮想マシン

このクエリは、[正規表現](/dotnet/standard/base-types/regular-expression-language-quick-reference) (_regex_ と呼ばれる) に一致する仮想マシンを検索します。
**matches regex @** では、一致させる regex を定義することができます。ここでは `^Contoso(.*)[0-9]+$` を指定しています。 その regex の定義は以下のように説明されています。

- `^` - 一致は、文字列の先頭から始まる必要があります。
- `Contoso` - 文字列。大文字と小文字は区別されます。
- `(.*)` - 部分式一致:
  - `.` - 任意の 1 文字との一致 (新しい行を除く)。
  - `*` - 「直前の要素 0 回以上」との一致。
- `[0-9]` - 文字グループは 0 ~ 9 の数字に一致。
- `+` - 「直前の要素 1 回以上」との一致。
- `$` - 直前の要素との一致は、文字列の最後に発生する必要があります。

名前で一致した後、クエリはプロジェクトの名前を提示し、名前の昇順で順序付けします。

```Query
where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$'
| project name
| order by name asc
```

```azurecli-interactive
az graph query -q "where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$' | project name | order by name asc"
```

```azurepowershell-interactive
Search-AzureRmGraph -Query "where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$' | project name | order by name asc"
```

## <a name="next-steps"></a>次の手順

- [Starter クエリ](starter.md)のサンプルを参照してください
- [クエリ言語](../concepts/query-language.md)の詳細について学習します
- [その他のリソース](../concepts/explore-resources.md)
