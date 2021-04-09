---
title: 大規模なデータ セットを処理する
description: Azure Resource Graph の操作中に大きなデータ セット内のレコードを取得、書式設定、ページング、およびスキップする方法について説明します。
ms.date: 01/27/2021
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 1eaabfdd78712966f3b21d869259a312db31b7bc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98917692"
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
az graph query -q "Resources | project name | order by name asc" --first 200 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name | order by name asc" -First 200
```

[REST API](/rest/api/azureresourcegraph/resourcegraph(2019-04-01)/resources/resources) でのコントロールは **$top** であり、**QueryRequestOptions** の一部です。

"_最も制限の厳しい_" コントロールが選択されます。 たとえば、クエリに含まれる **top** または **limit** 演算子で指定されているレコード数が **First** より多い場合、返される最大レコード数は **First** と等しくなります。 同様に、**top** または **limit** が **First** より小さい場合は、返されるレコード セットは **top** または **limit** で構成されている小さい方の値になります。

**First** の最大許容値は現在 _5000_ であり、これは、結果の _1000_ 件のレコードを一度に [ページング](#paging-results)することで実現されます。

> [!IMPORTANT]
> **First** が _1000_ 件を超えるレコード数になるように構成されている場合、改ページ位置の自動修正が機能するためには、**id** フィールドをクエリに **反映する** する必要があります。 これがクエリに含まれていない場合、応答は [改ページ](#paging-results)されず、結果は _1000_ レコードに制限されます。

## <a name="skipping-records"></a>レコードのスキップ

大規模なデータ セットを操作するための次のオプションは、**Skip** コントロールです。 このコントロールをクエリで使用すると、結果を返す前に定義されている数のレコードをスキップできます。 **Skip** は、結果セットの中間にあるレコードを取得することを目的として、意味のある方法で結果を並べ替えるクエリに便利です。 必要な結果が返されるデータ セットの末尾にある場合は、異なる並べ替え構成を使用し、代わりにデータ セットの先頭から結果を取得する方が効率的です。

> [!NOTE]
> **Skip** を使用するときは、`asc` または `desc` を使って、少なくとも 1 つの列を基に結果を並べ替えることをお勧めします。 並べ替えを行わないと、返される結果がランダムになり、反復可能ではなくなります。 クエリで `limit` または `take` が使用されている場合、**Skip** は無視されます。

次の例では、クエリ結果の最初の _10_ レコードをスキップして、代わりに 11 番目のレコードから結果セットを返し始める方法を示します。

```azurecli-interactive
az graph query -q "Resources | project name | order by name asc" --skip 10 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name | order by name asc" -Skip 10
```

[REST API](/rest/api/azureresourcegraph/resourcegraph(2019-04-01)/resources/resources) でのコントロールは **$skip** であり、**QueryRequestOptions** の一部です。

## <a name="paging-results"></a>ページングの結果

処理のために、または結果セットが返されるレコード数の最大許容値 _1000_ を超えるために、結果セットを小さなレコード セットに分割する必要がある場合は、ページングを使用します。 [REST API](/rest/api/azureresourcegraph/resourcegraph(2019-04-01)/resources/resources)
**QueryResponse** では、結果セットが分割されていることを示す値として、**resultTruncated** と **$skipToken** が提供されています。 **resultTruncated** はブール値で、応答で返されていない追加のレコードがあるかどうかをコンシューマーに通知します。 この状態は、**count** プロパティが **totalRecords** プロパティより小さい場合も示されます。 **totalRecords** では、クエリに一致したレコード数が定義されています。

 ページングが無効になっているか、`id` 列がないためにページングを使用できない場合、または使用可能なリソースがクエリで要求されている量に満たない場合、**resultTruncated** は **true** です。 **resultTruncated** が **true** のときは、 **$skipToken** プロパティは設定されません。

次の例では、Azure CLI および Azure PowerShell で、最初の 3000 個のレコードを **スキップ** し、これらのスキップされたレコードの後の **最初の** 1000 個のレコードを返す方法を示します。

```azurecli-interactive
az graph query -q "Resources | project id, name | order by id asc" --first 1000 --skip 3000
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project id, name | order by id asc" -First 1000 -Skip 3000
```

> [!IMPORTANT]
> 改ページ位置の自動修正が機能するためには、**id** フィールドをクエリに **反映する** 必要があります。 それがクエリにないと、応答には **$skipToken** が含まれません。

例については、REST API のドキュメントの「[Next page query (次のページのクエリ)](/rest/api/azureresourcegraph/resourcegraph(2019-04-01)/resources/resources#next-page-query)」をご覧ください。

## <a name="formatting-results"></a>結果の書式設定

Resource Graph クエリの結果は _Table_ と _ObjectArray_ の 2 つの形式で提供されます。 この形式は、要求オプションの一部として **resultFormat** パラメーターを使用して構成されます。 _Table_ 形式は **resultFormat** の既定値です。

既定では、Azure CLI の結果は JSON で提供されます。 Azure PowerShell の結果は、既定では **PSCustomObject** になりますが、`ConvertTo-Json` コマンドレットを使用してすぐに JSON に変換することができます。 他の SDK のために、_ObjectArray_ 形式を出力するようにクエリ結果を構成できます。

### <a name="format---table"></a>形式 - Table

既定の形式である _Table_ は、クエリによって返されるプロパティの列のデザインと行の値を強調表示するように設計された JSON 形式で結果を返します。 この形式は、構造化テーブルまたはスプレッドシートで定義されているデータによく似ており、最初に列が識別され、その後でデータを示す各行がこれらの列に合わせて表示されます。

_Table_ 形式でのクエリ結果の例を次に示します。

```json
{
    "totalRecords": 47,
    "count": 1,
    "data": {
        "columns": [{
                "name": "name",
                "type": "string"
            },
            {
                "name": "type",
                "type": "string"
            },
            {
                "name": "location",
                "type": "string"
            },
            {
                "name": "subscriptionId",
                "type": "string"
            }
        ],
        "rows": [
            [
                "veryscaryvm2-nsg",
                "microsoft.network/networksecuritygroups",
                "eastus",
                "11111111-1111-1111-1111-111111111111"
            ]
        ]
    },
    "facets": [],
    "resultTruncated": "true"
}
```

### <a name="format---objectarray"></a>形式 - ObjectArray

_ObjectArray_ 形式でも結果は JSON 形式で返されます。 ただし、このデザインは、列と行データが配列グループで一致する、JSON で共通するキーと値のペアの関係に対応しています。

_ObjectArray_ 形式でのクエリ結果の例を次に示します。

```json
{
    "totalRecords": 47,
    "count": 1,
    "data": [{
        "name": "veryscaryvm2-nsg",
        "type": "microsoft.network/networksecuritygroups",
        "location": "eastus",
        "subscriptionId": "11111111-1111-1111-1111-111111111111"
    }],
    "facets": [],
    "resultTruncated": "true"
}
```

_ObjectArray_ 形式を使用するように **resultFormat** を設定する例を以下に示します。

```csharp
var requestOptions = new QueryRequestOptions( resultFormat: ResultFormat.ObjectArray);
var request = new QueryRequest(subscriptions, "Resources | limit 1", options: requestOptions);
```

```python
request_options = QueryRequestOptions(
    result_format=ResultFormat.object_array
)
request = QueryRequest(query="Resources | limit 1", subscriptions=subs_list, options=request_options)
response = client.resources(request)
```

## <a name="next-steps"></a>次のステップ

- [初歩的なクエリ](../samples/starter.md)で使用されている言語を確認します。
- [高度なクエリ](../samples/advanced.md)で高度な使用方法を確認します。
- [リソースを探索する](explore-resources.md)方法について詳しく確認します。
