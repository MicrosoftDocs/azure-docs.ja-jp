---
title: 高度なクエリのサンプル
description: Azure Resource Graph を使用して、VMSS の容量、使用されているすべてのタグの一覧、正規表現と一致する仮想マシンなど、高度なクエリを実行します。
author: DCtheGeek
ms.author: dacoulte
ms.date: 01/23/2019
ms.topic: quickstart
ms.service: resource-graph
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 9a243dd236a8c499602a9070a7dd61e69541d58d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59256823"
---
# <a name="advanced-resource-graph-queries"></a>Resource Graph の高度なクエリ

Azure Resource Graph でクエリを理解する最初の手順は、[クエリ言語](../concepts/query-language.md)の基本を理解することです。 [Azure Data Explorer](../../../data-explorer/data-explorer-overview.md) にまだ精通していない場合、探しているリソースに対する要求を作成する方法を理解するための基礎を確認することをお勧めします。

次の高度なクエリを説明します。

> [!div class="checklist"]
> - [VMSS の容量とサイズを取得します](#vmss-capacity)
> - [すべてのタグ名を一覧表示します](#list-all-tags)
> - [ regexに一致する仮想マシン](#vm-regex)

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free) を作成してください。

[!INCLUDE [az-powershell-update](../../../../includes/updated-for-az.md)]

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
Search-AzGraph -Query "where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"
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
Search-AzGraph -Query "project tags | summarize buildschema(tags)"
```

## <a name="vm-regex"></a> regexに一致する仮想マシン

このクエリは、[正規表現](/dotnet/standard/base-types/regular-expression-language-quick-reference) (_regex_ と呼ばれる) に一致する仮想マシンを検索します。
**matches regex \@** では、一致させる regex を定義することができます。ここでは `^Contoso(.*)[0-9]+$` を指定しています。 その regex の定義は以下のように説明されています。

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
Search-AzGraph -Query "where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$' | project name | order by name asc"
```

## <a name="next-steps"></a>次の手順

- [Starter クエリ](starter.md)のサンプルを参照してください
- [クエリ言語](../concepts/query-language.md)の詳細について学習します
- [その他のリソース](../concepts/explore-resources.md)