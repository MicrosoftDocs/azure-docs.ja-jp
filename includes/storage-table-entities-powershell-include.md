---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/27/2019
ms.author: tamram
ms.openlocfilehash: 9a60c624b181a1efd2f6deebd349daa82214a8a4
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541385"
---
<!--created by Robin Shahan to go in the articles for table storage w/powershell.
    There is one for Azure Table Storage and one for Azure Cosmos DB Table API -->

## <a name="managing-table-entities"></a>テーブル エンティティの管理

テーブルが準備できたので、エンティティ、またはテーブル内の行を管理する方法を見てみましょう。 

エンティティには最大 255 個のプロパティを指定できます。たとえば、**PartitionKey**、**RowKey**、**Timestamp** のようなプロパティです。 **PartitionKey** と **RowKey** の値は、ご自身で挿入および更新する必要があります。 **Timestamp** の値はサーバーによって管理されます。この値は変更できません。 **PartitionKey** と **RowKey** が組み合わさって、テーブル内の各エンティティを一意に識別します。

* **PartitionKey**:エンティティが格納されるパーティションを決定します。
* **RowKey**: パーティション内のエンティティを一意に識別します。

1 個のエンティティに対して最大 252 個のカスタム プロパティを定義できます。 

### <a name="add-table-entities"></a>テーブル エンティティの追加

**Add-AzTableRow** を使用して、エンティティをテーブルに追加します。 これらの例では、`partition1` と `partition2` の値を持つパーティション キーおよび州コードと等しい行キーが使用されます。 各エンティティのプロパティは、`username` と `userid` です。 

```powershell
$partitionKey1 = "partition1"
$partitionKey2 = "partition2"

# add four rows 
Add-AzTableRow `
    -table $cloudTable `
    -partitionKey $partitionKey1 `
    -rowKey ("CA") -property @{"username"="Chris";"userid"=1}

Add-AzTableRow `
    -table $cloudTable `
    -partitionKey $partitionKey2 `
    -rowKey ("NM") -property @{"username"="Jessie";"userid"=2}

Add-AzTableRow `
    -table $cloudTable `
    -partitionKey $partitionKey1 `
    -rowKey ("WA") -property @{"username"="Christine";"userid"=3}

Add-AzTableRow `
    -table $cloudTable `
    -partitionKey $partitionKey2 `
    -rowKey ("TX") -property @{"username"="Steven";"userid"=4}
```

### <a name="query-the-table-entities"></a>テーブル エンティティのクエリ

テーブル内のエンティティにクエリを実行するには、**Get-AzTableRow** コマンドを使用します。

> [!NOTE]
> **Get-AzureStorageTableRowAll** コマンドレット、**Get-AzureStorageTableRowByPartitionKey** コマンドレット、**Get-AzureStorageTableRowByColumnName** コマンドレット、および **Get-AzureStorageTableRowByCustomFilter** コマンドレットは非推奨で、今後のバージョン更新で削除されます。

#### <a name="retrieve-all-entities"></a>すべてのエンティティの取得

```powershell
Get-AzTableRow -table $cloudTable | ft
```

このコマンドによって、次の表のような結果が生成されます。

| userid | username | partition | rowkey |
|----|---------|---------------|----|
| 1 | Chris | partition1 | CA |
| 3 | Christine | partition1 | WA |
| 2 | Jessie | partition2 | NM |
| 4 | Steven | partition2 | TX |

#### <a name="retrieve-entities-for-a-specific-partition"></a>特定のパーティションのエンティティの取得

```powershell
Get-AzTableRow -table $cloudTable -partitionKey $partitionKey1 | ft
```

結果は次の表のようになります。

| userid | username | partition | rowkey |
|----|---------|---------------|----|
| 1 | Chris | partition1 | CA |
| 3 | Christine | partition1 | WA |

#### <a name="retrieve-entities-for-a-specific-value-in-a-specific-column"></a>特定の列の特定の値のエンティティの取得

```powershell
Get-AzTableRow -table $cloudTable `
    -columnName "username" `
    -value "Chris" `
    -operator Equal
```

このクエリは、1 つのレコードを取得します。

|フィールド|value|
|----|----|
| userid | 1 |
| username | Chris |
| パーティション キー | partition1 |
| 行キー      | CA |

#### <a name="retrieve-entities-using-a-custom-filter"></a>カスタム フィルターを使用したエンティティの取得 

```powershell
Get-AzTableRow `
    -table $cloudTable `
    -customFilter "(userid eq 1)"
```

このクエリは、1 つのレコードを取得します。

|フィールド|value|
|----|----|
| userid | 1 |
| username | Chris |
| パーティション キー | partition1 |
| 行キー      | CA |

### <a name="updating-entities"></a>エンティティの更新 

エンティティを更新するには 3 つの手順があります。 まず、変更するエンティティを取得します。 次に、変更を加えます。 その後、**Update-AzTableRow** を使用して変更をコミットします。

username = 'Jessie' のエンティティを username = 'Jessie2' になるように更新します。 この例では、.NET 型を使用してカスタム フィルターを作成する別の方法も示しています。

```powershell
# Create a filter and get the entity to be updated.
[string]$filter = `
    [Microsoft.Azure.Cosmos.Table.TableQuery]::GenerateFilterCondition("username",`
    [Microsoft.Azure.Cosmos.Table.QueryComparisons]::Equal,"Jessie")
$user = Get-AzTableRow `
    -table $cloudTable `
    -customFilter $filter

# Change the entity.
$user.username = "Jessie2"

# To commit the change, pipe the updated record into the update cmdlet.
$user | Update-AzTableRow -table $cloudTable

# To see the new record, query the table.
Get-AzTableRow -table $cloudTable `
    -customFilter "(username eq 'Jessie2')"
```

結果には、Jessie2 レコードが表示されます。

|フィールド|value|
|----|----|
| userid | 2 |
| username | Jessie2 |
| パーティション キー | partition2 |
| 行キー      | NM |

### <a name="deleting-table-entities"></a>テーブル エンティティの削除

1 つのエンティティまたはテーブル内のすべてのエンティティを削除することができます。

#### <a name="deleting-one-entity"></a>1 つのエンティティの削除

1 つのエンティティを削除するには、そのエンティティへの参照を取得し、**Remove-AzTableRow** にパイプします。

```powershell
# Set filter.
[string]$filter = `
  [Microsoft.Azure.Cosmos.Table.TableQuery]::GenerateFilterCondition("username",`
  [Microsoft.Azure.Cosmos.Table.QueryComparisons]::Equal,"Jessie2")

# Retrieve entity to be deleted, then pipe it into the remove cmdlet.
$userToDelete = Get-AzTableRow `
    -table $cloudTable `
    -customFilter $filter
$userToDelete | Remove-AzTableRow -table $cloudTable

# Retrieve entities from table and see that Jessie2 has been deleted.
Get-AzTableRow -table $cloudTable | ft
```

#### <a name="delete-all-entities-in-the-table"></a>テーブル内のすべてのエンティティの削除

テーブル内のすべてのエンティティを削除するには、それらのエンティティを取得し、結果を削除コマンドレットにパイプします。 

```powershell
# Get all rows and pipe the result into the remove cmdlet.
Get-AzTableRow `
    -table $cloudTable | Remove-AzTableRow -table $cloudTable 

# List entities in the table (there won't be any).
Get-AzTableRow -table $cloudTable | ft
```
