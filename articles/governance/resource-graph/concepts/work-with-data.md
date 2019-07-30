---
title: 大規模なデータ セットを処理する
description: Azure Resource Graph の使用時に大きなデータ セットを取得して制御する方法について説明します。
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/01/2019
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: d04f46dbc60a7242e44d76915e15281cc6248d20
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786545"
---
# <a name="working-with-large-azure-resource-data-sets"></a>大規模な Azure リソース データ セットの処理

Azure Resource Graph は、Azure 環境内にあるリソースを操作し、リソースに関する情報を取得することを目的に設計されています。 Resource Graph を使用すると、数千レコードのクエリを実行する場合でも、このデータの取得が高速になります。 Resource Graph には、これらの大きなデータ セットを操作するための複数のオプションがあります。

高頻度のクエリの操作に関するガイダンスについては、[スロットルされた要求に関するガイダンス](./guidance-for-throttled-requests.md)のページを参照してください。

## <a name="data-set-result-size"></a>データ セットの結果のサイズ

既定では、Resource Graph のクエリで返されるレコードは **100** 個だけに制限されています。 このコントロールにより、ユーザーとサービスの両方が、大規模なデータ セットになる意図しないクエリから保護されます。 このようなことが最もよく発生するのは、ユーザーが自分の特定のニーズに合った方法でリソースの検索とフィルター処理を行うクエリを実験しているときです。 このコントロールは、Azure Data Explorer 言語の [top](/azure/kusto/query/topoperator) または [limit](/azure/kusto/query/limitoperator) 演算子を使用して結果を制限する場合とは異なります。

> [!NOTE]
> **First** を使用するときは、`asc` または `desc` を使って、少なくとも 1 つの列を基に結果を並べ替えることをお勧めします。 並べ替えを行わないと、返される結果がランダムになり、反復可能ではなくなります。

既定の制限は、Resource Graph と対話するすべての方法でオーバーライドできます。 次の例では、データ セットのサイズ制限を _200_ に変更する方法を示します。

```azurecli-interactive
az graph query -q "project name | order by name asc" --first 200 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "project name | order by name asc" -First 200
```

[REST API](/rest/api/azureresourcegraph/resources/resources) でのコントロールは **$top** であり、**QueryRequestOptions** の一部です。

"_最も制限の厳しい_" コントロールが選択されます。 たとえば、クエリに含まれる **top** または **limit** 演算子で指定されているレコード数が **First** より多い場合、返される最大レコード数は **First** と等しくなります。 同様に、**top** または **limit** が **First** より小さい場合は、返されるレコード セットは **top** または **limit** で構成されている小さい方の値になります。

現在、**First** で許容される最大値は _5000_ です。

## <a name="skipping-records"></a>レコードのスキップ

大規模なデータ セットを操作するための次のオプションは、**Skip** コントロールです。 このコントロールをクエリで使用すると、結果を返す前に定義されている数のレコードをスキップできます。 **Skip** は、結果セットの中間にあるレコードを取得することを目的として、意味のある方法で結果を並べ替えるクエリに便利です。 必要な結果が返されるデータ セットの末尾にある場合は、異なる並べ替え構成を使用し、代わりにデータ セットの先頭から結果を取得する方が効率的です。

> [!NOTE]
> **Skip** を使用するときは、`asc` または `desc` を使って、少なくとも 1 つの列を基に結果を並べ替えることをお勧めします。 並べ替えを行わないと、返される結果がランダムになり、反復可能ではなくなります。

次の例では、クエリ結果の最初の _10_ レコードをスキップして、代わりに 11 番目のレコードから結果セットを返し始める方法を示します。

```azurecli-interactive
az graph query -q "project name | order by name asc" --skip 10 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "project name | order by name asc" -Skip 10
```

[REST API](/rest/api/azureresourcegraph/resources/resources) でのコントロールは **$skip** であり、**QueryRequestOptions** の一部です。

## <a name="paging-results"></a>ページングの結果

処理のために、または結果セットが返されるレコード数の最大許容値 _1000_ を超えるために、結果セットを小さなレコード セットに分割する必要がある場合は、ページングを使用します。 [REST API](/rest/api/azureresourcegraph/resources/resources) の **QueryResponse** では、結果セットが分割されていることを示す値として、**resultTruncated** と **$skipToken** が提供されています。
**resultTruncated** はブール値で、応答で返されていない追加のレコードがあるかどうかをコンシューマーに通知します。 この状態は、**count** プロパティが **totalRecords** プロパティより小さい場合も示されます。 **totalRecords** では、クエリに一致したレコード数が定義されています。

**resultTruncated** が **true** のときは、応答で **$skipToken** プロパティが設定されます。 この値は、クエリに一致した次のレコード セットを取得するために、同じクエリ値とサブスクリプション値で使用されます。

次の例では、Azure CLI および Azure PowerShell で、最初の 3000 個のレコードを**スキップ**し、これらのスキップされたレコードの後の**最初の** 1000 個のレコードを返す方法を示します。

```azurecli-interactive
az graph query -q "project id, name | order by id asc" --first 1000 --skip 3000
```

```azurepowershell-interactive
Search-AzGraph -Query "project id, name | order by id asc" -First 1000 -Skip 3000
```

> [!IMPORTANT]
> 改ページ位置の自動修正が機能するためには、**id** フィールドをクエリに**反映する**必要があります。 それがクエリにないと、応答には **$skipToken** が含まれません。

例については、REST API のドキュメントの「[Next page query (次のページのクエリ)](/rest/api/azureresourcegraph/resources/resources#next-page-query)」をご覧ください。

## <a name="next-steps"></a>次の手順

- [初歩的なクエリ](../samples/starter.md)で使用されている言語を確認します。
- [高度なクエリ](../samples/advanced.md)で高度な使用方法を確認します。
- [リソースの探索](explore-resources.md)について学習します。