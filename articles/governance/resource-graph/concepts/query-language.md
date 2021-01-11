---
title: クエリ言語を理解する
description: Resource Graph テーブルと、Azure Resource Graph で使用可能な Kusto データ型、演算子、関数について説明します。
ms.date: 08/24/2020
ms.topic: conceptual
ms.openlocfilehash: 4d7ca949e9eef075adb130bb84b2617749950bec
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88798552"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Azure Resource Graph クエリ言語の概要

Azure Resource Graph のクエリ言語では、さまざまな演算子と関数がサポートされています。 それぞれは、[Kusto Query Language (KQL)](/azure/kusto/query/index) に基づいて動作します。 Resource Graph で使用されるクエリ言語の詳細については、[KQL のチュートリアル](/azure/kusto/query/tutorial)を参照してください。

この記事では、Resource Graph でサポートされる言語コンポーネントについて説明します。

- [Resource Graph テーブル](#resource-graph-tables)
- [Resource Graph のカスタム言語要素](#resource-graph-custom-language-elements)
- [サポートされる KQL 言語要素](#supported-kql-language-elements)
- [クエリのスコープ](#query-scope)
- [エスケープ文字](#escape-characters)

## <a name="resource-graph-tables"></a>Resource Graph テーブル

Resource Graph には、Azure Resource Manager のリソースの種類とそのプロパティに関するデータ用のテーブルがいくつか用意されています。 これらのテーブルを `join` または `union` 演算子と共に使用して、関連するリソースの種類からプロパティを取得できます。 Resource Graph で使用できるテーブルの一覧を次に示します。

|Resource Graph テーブル |説明 |
|---|---|
|リソース |クエリ内で何も定義されていない場合の既定のテーブル。 Resource Manager のリソースの種類とプロパティのほとんどはここにあります。 |
|ResourceContainers |サブスクリプション (プレビュー中 -- `Microsoft.Resources/subscriptions`) とリソース グループ (`Microsoft.Resources/subscriptions/resourcegroups`) のリソースの種類とデータが含まれています。 |
|AdvisorResources |`Microsoft.Advisor` に "_関連する_" リソースが含まれています。 |
|AlertsManagementResources |`Microsoft.AlertsManagement` に "_関連する_" リソースが含まれています。 |
|HealthResources |`Microsoft.ResourceHealth` に "_関連する_" リソースが含まれています。 |
|MaintenanceResources |`Microsoft.Maintenance` に "_関連する_" リソースが含まれています。 |
|SecurityResources |`Microsoft.Security` に "_関連する_" リソースが含まれています。 |

リソースの種類を含む、完全な一覧については、[リファレンス: サポートされているテーブルとリソースの種類](../reference/supported-tables-resources.md)に関するページを参照してください。

> [!NOTE]
> _Resources_ が既定のテーブルです。 _Resources_  テーブルに対してクエリを実行する場合、`join` または `union` を使用しない限り、テーブル名を指定する必要はありません。 ただし、最初のテーブルを常にクエリに含めることをお勧めします。

ポータル内の Resource Graph Explorer を使用して、各テーブルで使用できるリソースの種類を確認します。 別の方法として、`<tableName> | distinct type` などのクエリを使用して、指定した Resource Graph テーブルでサポートされている、環境内に存在するリソースの種類の一覧を取得することもできます。

次のクエリは、単純な `join` を示しています。 クエリ結果では、列が結合され、結合されたテーブルの重複する列名 (この例では _ResourceContainers_) が **1** と共に追加されます。 _ResourceContainers_ テーブルには、サブスクリプションとリソース グループの両方の種類があります。どちらの種類も、_resources_  テーブルからリソースへの結合に使用できます。

```kusto
Resources
| join ResourceContainers on subscriptionId
| limit 1
```

次のクエリは、`join` のより複雑な使用方法を示しています。 このクエリは、結合対象のテーブルをサブスクリプション リソースに制限し、さらに、`project` を使用して、元のフィールドである _subscriptionId_ と、_SubName_ という名前に変更された _name_ フィールドのみが含まれるように制限しています。 フィールドは _Resources_  に既に存在するため、フィールド名を変更すると、`join` によって _name1_  として追加されなくなります。 元のテーブルは `where` でフィルター処理され、次の `project` には両方のテーブルの列が含まれます。 クエリの結果として、キー コンテナーが 1 つ返されると共に、そのタイプと名前、そしてキー コンテナーが存在するサブスクリプションの名前が表示されます。

```kusto
Resources
| where type == 'microsoft.keyvault/vaults'
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| project type, name, SubName
| limit 1
```

> [!NOTE]
> `project` を使用して `join` の結果を制限する場合は、2 つのテーブルを関連付けるために `join` によって使用されるプロパティ (上記の例の _subscriptionId_) が `project` に含まれている必要があります。

## <a name="extended-properties-preview"></a><a name="extended-properties"></a>拡張プロパティ (プレビュー)

_プレビュー_機能として、Resource Graph の一部のリソースの種類には、Azure Resource Manager によって提供されるプロパティ以外に、クエリで使用できる追加の種類関連のプロパティがあります。 この一連の値は_拡張プロパティ_と呼ばれ、`properties.extended` でサポートされているリソースの種類に存在します。 _拡張プロパティ_があるリソースの種類を確認するには、次のクエリを使用します。

```kusto
Resources
| where isnotnull(properties.extended)
| distinct type
| order by type asc
```

例:`instanceView.powerState.code` によって、仮想マシンの数を取得します。

```kusto
Resources
| where type == 'microsoft.compute/virtualmachines'
| summarize count() by tostring(properties.extended.instanceView.powerState.code)
```

## <a name="resource-graph-custom-language-elements"></a>Resource Graph のカスタム言語要素

### <a name="shared-query-syntax-preview"></a><a name="shared-query-syntax"></a>共有クエリ構文 (プレビュー)

プレビュー機能として、[共有クエリ](../tutorials/create-share-query.md)には、Resource Graph クエリで直接アクセスできます。 このシナリオにより、標準クエリを共有クエリとして作成し、再利用することができます。 Resource Graph クエリ内で共有クエリを呼び出すには、`{{shared-query-uri}}` 構文を使用します。 共有クエリの URI は、そのクエリの **[設定]** ページにある共有クエリの _[リソース ID]_ です。 この例では、共有クエリ URI は `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SharedQueries/providers/Microsoft.ResourceGraph/queries/Count VMs by OS` です。
この URI は、サブスクリプション、リソース グループ、および別のクエリで参照する共有クエリの完全な名前を指します。 このクエリは、[チュートリアル:クエリの作成と共有](../tutorials/create-share-query.md)で作成したものと同じです。

> [!NOTE]
> 共有クエリを参照するクエリを共有クエリとして保存することはできません。

例 1:共有クエリのみを使用する

この Resource Graph クエリの結果は、共有クエリに格納されているクエリと同じです。

```kusto
{{/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SharedQueries/providers/Microsoft.ResourceGraph/queries/Count VMs by OS}}
```

例 2:より大きいクエリの一部として共有クエリを含める

このクエリでは、最初に共有クエリを使用し、次に `limit` を使用して結果をさらに制限します。

```kusto
{{/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SharedQueries/providers/Microsoft.ResourceGraph/queries/Count VMs by OS}}
| where properties_storageProfile_osDisk_osType =~ 'Windows'
```

## <a name="supported-kql-language-elements"></a>サポートされる KQL 言語要素

Resource Graph では、KQL [データ型](/azure/kusto/query/scalar-data-types/)、[スカラー関数](/azure/kusto/query/scalarfunctions)、[スカラー演算子](/azure/kusto/query/binoperators)、[集計関数](/azure/kusto/query/any-aggfunction)のサブセットがサポートされています。 Resource Graph では、特定の[テーブル演算子](/azure/kusto/query/queries)がサポートされます。その中には、動作が異なるものもあります。

### <a name="supported-tabulartop-level-operators"></a>サポートされているテーブル演算子/上位レベルの演算子

次に示すのは、Resource Graph でサポートされる KQL のテーブル演算子の一覧と具体的なサンプルです。

|KQL |Resource Graph のサンプル クエリ |Notes |
|---|---|---|
|[count](/azure/kusto/query/countoperator) |[カウント キー コンテナー](../samples/starter.md#count-keyvaults) | |
|[distinct](/azure/kusto/query/distinctoperator) |[特定の別名の個別の値の表示](../samples/starter.md#distinct-alias-values) | |
|[extend](/azure/kusto/query/extendoperator) |[仮想マシンの数 (OS の種類別)](../samples/starter.md#count-os) | |
|[join](/azure/kusto/query/joinoperator) |[サブスクリプション名を含むキー コンテナー](../samples/advanced.md#join) |サポートされる結合フレーバー: [innerunique ](/azure/kusto/query/joinoperator#default-join-flavor)、[inner ](/azure/kusto/query/joinoperator#inner-join)、[leftouter ](/azure/kusto/query/joinoperator#left-outer-join)。 1 つのクエリでは `join` が 3 つに制限されます。 ブロードキャスト結合などのカスタム結合方法は使用できません。 1 つのテーブル内、または _Resources_ テーブルと _ResourceContainers_ テーブルの間で使用できます。 |
|[limit](/azure/kusto/query/limitoperator) |[パブリック IP アドレスの一覧表示](../samples/starter.md#list-publicip) |`take` のシノニム |
|[mvexpand](/azure/kusto/query/mvexpandoperator) | | レガシ演算子では、代わりに `mv-expand` を使用します。 _RowLimit_ の最大は 400 です。 既定値は 128 です。 |
|[mv-expand](/azure/kusto/query/mvexpandoperator) |[特定の書き込み場所を含む Cosmos DB を一覧表示する](../samples/advanced.md#mvexpand-cosmosdb) |_RowLimit_ の最大は 400 です。 既定値は 128 です。 |
|[order](/azure/kusto/query/orderoperator) |[名前で並べ替えられたリソースの一覧表示](../samples/starter.md#list-resources) |`sort` のシノニム |
|[project](/azure/kusto/query/projectoperator) |[名前で並べ替えられたリソースの一覧表示](../samples/starter.md#list-resources) | |
|[project-away](/azure/kusto/query/projectawayoperator) |[結果から列を除外する](../samples/advanced.md#remove-column) | |
|[sort](/azure/kusto/query/sortoperator) |[名前で並べ替えられたリソースの一覧表示](../samples/starter.md#list-resources) |`order` のシノニム |
|[summarize](/azure/kusto/query/summarizeoperator) |[Azure リソースの数](../samples/starter.md#count-resources) |簡略化された最初のページのみ |
|[take](/azure/kusto/query/takeoperator) |[パブリック IP アドレスの一覧表示](../samples/starter.md#list-publicip) |`limit` のシノニム |
|[top](/azure/kusto/query/topoperator) |[名前とその OS の種類による最初の 5 つの仮想マシンの表示](../samples/starter.md#show-sorted) | |
|[union](/azure/kusto/query/unionoperator) |[2 つのクエリの結果を結合して 1 つの結果にする](../samples/advanced.md#unionresults) |1 つのテーブルを使用できます:_T_ `| union` \[`kind=` `inner`\|`outer`\] \[`withsource=`_ColumnName_\] _Table_. 1 つのクエリでは `union` 分岐が 3 つに制限されます。 `union` 分岐テーブルのあいまい解決は許可されていません。 1 つのテーブル内、または _Resources_ テーブルと _ResourceContainers_ テーブルの間で使用できます。 |
|[where](/azure/kusto/query/whereoperator) |[ストレージを含むリソースの表示](../samples/starter.md#show-storage) | |

## <a name="query-scope"></a>クエリ スコープ

クエリによってリソースが返されるサブスクリプションのスコープは、Resource Graph にアクセスする方法によって異なります。 Azure CLI および Azure PowerShell は、承認されたユーザーのコンテキストに基づいて、要求に含めるサブスクリプションの一覧を設定します。 サブスクリプションの一覧は、それぞれ **subscriptions** と **Subscription** パラメーターを使用して、それぞれに対して手動で定義できます。
REST API およびその他のすべての SDK では、リソースを含めるサブスクリプションの一覧は、要求の一部として明示的に定義する必要があります。

**プレビュー**として、REST API バージョン `2020-04-01-preview` では、クエリのスコープを[管理グループ](../../management-groups/overview.md)に設定するプロパティが追加されます。 また、このプレビュー API では、サブスクリプション プロパティは省略可能になります。 管理グループまたはサブスクリプションの一覧が定義されていない場合は、認証されたユーザーがアクセスできるすべてのリソースがクエリ スコープになります。 新しい `managementGroupId` プロパティは管理グループ ID を取ります。これは、管理グループの名前とは異なります。 `managementGroupId` を指定すると、指定した管理グループ階層内、またはその下にある最初の5000 件のサブスクリプションのリソースが含まれます。 `managementGroupId` を `subscriptions` と同時に使用することはできません。

例:ID "myMG" を持つ "My Management Group" という名前の管理グループの階層内のすべてのリソースに対してクエリを実行します。

- REST API URI

  ```http
  POST https://management.azure.com/providers/Microsoft.ResourceGraph/resources?api-version=2020-04-01-preview
  ```

- 要求本文

  ```json
  {
      "query": "Resources | summarize count()",
      "managementGroupId": "myMG"
  }
  ```

## <a name="escape-characters"></a>エスケープ文字

`.` や `$` を含むものなど、一部のプロパティ名はクエリ内でラップまたはエスケープする必要があります。そうしないと、プロパティ名が正しく解釈されず、期待する結果が得られません。

- `.` - プロパティ名を `['propertyname.withaperiod']` のようにラップします。
  
  プロパティ _odata.type_ をラップするクエリ例:

  ```kusto
  where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.['odata.type']
  ```

- `$` - プロパティ名の文字をエスケープします。 使用されるエスケープ文字は、Resource Graph が実行されるシェルによって異なります。

  - **bash** - `\`

    bash でプロパティ _\$type_ をエスケープするクエリ例:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.\$type
    ```

  - **cmd** - `$` 文字をエスケープしないでください。

  - **PowerShell** - ``` ` ```

    PowerShell でプロパティ _\$type_ をエスケープするクエリ例:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.`$type
    ```

## <a name="next-steps"></a>次のステップ

- [初歩的なクエリ](../samples/starter.md)で使用されている言語を確認します。
- [高度なクエリ](../samples/advanced.md)で高度な使用方法を確認します。
- [リソースを探索する](explore-resources.md)方法について詳しく確認します。