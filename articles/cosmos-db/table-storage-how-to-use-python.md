---
title: Python を使用して Azure Table Storage と Azure Cosmos DB Table API を使用する
description: Azure Table Storage または Azure Cosmos DB Table API を使用して、構造化データをクラウドに格納します。
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: python
ms.topic: sample
ms.date: 04/05/2018
author: wmengmsft
ms.author: wmeng
ms.reviewer: sngun
ms.openlocfilehash: 11b47483eaf39e7445ece8b9e38d81a6a2404cc6
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/06/2019
ms.locfileid: "55756600"
---
# <a name="get-started-with-azure-table-storage-and-the-azure-cosmos-db-table-api-using-python"></a>Python を使用して Azure Table Storage と Azure Cosmos DB Table API を使用する

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

Azure Table Storage と Azure Cosmos DB は、NoSQL の構造化データをクラウド内に格納するサービスです。スキーマレスのデザインでキー/属性ストアを実現します。 Table Storage と Azure Cosmos DB はスキーマがないため、アプリケーションの進化のニーズに合わせてデータを容易に修正できます。 Table Storage と Table API のデータへのアクセスは、多くの種類のアプリケーションにとって高速でコスト効率に優れ、また一般に、従来の SQL と比較して、同様の容量のデータを低コストで保存することができます。

Table Storage または Azure Cosmos DB を使用すると、Web アプリケーションのユーザー データ、アドレス帳、デバイス情報、サービスに必要なその他の種類のメタデータなど、柔軟なデータセットを格納できます。 ストレージ アカウントの容量の上限を超えない限り、テーブルには任意の数のエンティティを保存でき、ストレージ アカウントには任意の数のテーブルを含めることができます。

### <a name="about-this-sample"></a>このサンプルについて
このサンプルでは、Azure Table Storage の一般的な用途における [Azure Cosmos DB Table SDK for Python](https://pypi.python.org/pypi/azure-cosmosdb-table/) の使い方について説明します。 SDK の名前は、Azure Cosmos DB で使うことを示していますが、パッケージは Azure Cosmos DB と Azure Tables ストレージの両方に使うことができます。また、各サービスは一意のエンドポイントを持っています。 次の方法を示す Python の例を使って、これらのシナリオについて説明します。
* テーブルの作成と削除
* エンティティの挿入とクエリ
* エンティティの変更

このサンプルでシナリオに従って作業するときに、[Azure Cosmos DB SDK for Python API のリファレンス](https://docs.microsoft.com/python/api/overview/azure/cosmosdb?view=azure-python)を参照できます。

## <a name="prerequisites"></a>前提条件

このサンプルの作業を行うためには、次のものが必要になります。

- [Python](https://www.python.org/downloads/) 2.7、3.3、3.4、3.5、3.6
- [Azure Cosmos DB Table SDK for Python](https://pypi.python.org/pypi/azure-cosmosdb-table/) この SDK は、Azure Table Storage と Azure Cosmos DB Table API の両方に接続します。
- [Azure Storage アカウント](../storage/common/storage-quickstart-create-account.md)または [Azure Cosmos DB アカウント](https://azure.microsoft.com/try/cosmosdb/)

## <a name="create-an-azure-service-account"></a>Azure サービス アカウントを作成する
[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>Azure のストレージ アカウントの作成
[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

### <a name="create-an-azure-cosmos-db-table-api-account"></a>Azure Cosmos DB Table API アカウントを作成する
[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="install-the-azure-cosmos-db-table-sdk-for-python"></a>Azure Cosmos DB Table SDK for Python をインストールする

Storage アカウントを作成した後は、[Microsoft Azure Cosmos DB Table SDK for Python](https://pypi.python.org/pypi/azure-cosmosdb-table/) をインストールします。 SDK のインストールについて詳しくは、GitHub の Cosmos DB Table SDK for Python リポジトリ内の [README.rst](https://github.com/Azure/azure-cosmosdb-python/blob/master/azure-cosmosdb-table/README.rst) をご覧ください。

## <a name="import-the-tableservice-and-entity-classes"></a>TableService クラスと Entity クラスをインポートする

Python で Azure Table service のエンティティを扱うには、[TableService][py_TableService] クラスと [Entity][py_Entity] クラスを使います。 次のコードを Python ファイルの先頭付近に追加して、両方をインポートします。

```python
from azure.cosmosdb.table.tableservice import TableService
from azure.cosmosdb.table.models import Entity
```

## <a name="connect-to-azure-table-service"></a>Azure Table service に接続する

Azure Storage Table service に接続するには、[TableService][py_TableService] オブジェクトを作成し、お使いの Storage アカウントの名前とアカウント キーを渡します。 `myaccount` と `mykey` をアカウント名とキーに置き換えます。

```python
table_service = TableService(account_name='myaccount', account_key='mykey')
```

## <a name="connect-to-azure-cosmos-db"></a>Azure Cosmos DB への接続

Azure Cosmos DB に接続するには、Azure portal からプライマリ接続文字列をコピーし、コピーした接続文字列を使って [TableService][py_TableService] オブジェクトを作成します。

```python
table_service = TableService(connection_string='DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;TableEndpoint=myendpoint;)
```

## <a name="create-a-table"></a>テーブルを作成する

テーブルを作成するには、[create_table][py_create_table] を呼び出します。

```python
table_service.create_table('tasktable')
```

## <a name="add-an-entity-to-a-table"></a>エンティティをテーブルに追加する

エンティティを追加するには、最初にエンティティを表すオブジェクトを作成してから、そのオブジェクトを [TableService.insert_entity メソッド][py_TableService]に渡します。 エンティティ オブジェクトには、ディクショナリまたは [Entity][py_Entity] 型のオブジェクトが可能で、エンティティのプロパティの名前と値を定義します。 エンティティに定義するその他のプロパティの他に、すべてのエンティティに必須の [PartitionKey と RowKey](#partitionkey-and-rowkey) のプロパティを含める必要があります。

この例では、エンティティを表すディクショナリ オブジェクトを作成し、それを [insert_entity][py_insert_entity] メソッドに渡してテーブルに追加します。

```python
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001', 'description' : 'Take out the trash', 'priority' : 200}
table_service.insert_entity('tasktable', task)
```

この例では、[Entity][py_Entity] オブジェクトを作成し、それを [insert_entity][py_insert_entity] メソッドに渡してテーブルに追加します。

```python
task = Entity()
task.PartitionKey = 'tasksSeattle'
task.RowKey = '002'
task.description = 'Wash the car'
task.priority = 100
table_service.insert_entity('tasktable', task)
```

### <a name="partitionkey-and-rowkey"></a>PartitionKey と RowKey

すべてのエンティティに **PartitionKey** と **RowKey** を指定する必要があります。 これらは共にエンティティのプライマリ キーを形成するため、エンティティの一意の識別子です。 これらのプロパティのみがインデックス付けされているため、これらの値を使用すると、他のエンティティのプロパティでクエリを実行するよりもずっと高速にクエリを実行できます。

Table service は **PartitionKey** を使用してインテリジェントにテーブル エンティティをストレージ ノード全体に分散させます。 **PartitionKey** が同じエンティティは同じノードに格納されます。 **RowKey** は、エンティティが属するパーティション内のエンティティの一意の ID です。

## <a name="update-an-entity"></a>エンティティを更新する

エンティティのプロパティの値をすべて更新するには、[update_entity][py_update_entity] メソッドを呼び出します。 この例は、既存のエンティティを更新されたバージョンに置き換える方法を示しています。

```python
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001', 'description' : 'Take out the garbage', 'priority' : 250}
table_service.update_entity('tasktable', task)
```

更新されるエンティティが存在しない場合、更新操作は失敗します。 エンティティが存在するかどうかに関わらず、エンティティを格納する場合は、[insert_or_replace_entity][py_insert_or_replace_entity] を使用します。 次の例では、最初の呼び出しで既存のエンティティを置き換えます。 2 番目の呼び出しでは、指定された PartitionKey と RowKey を持つエンティティがテーブル内に存在しないため、新しいエンティティが挿入されます。

```python
# Replace the entity created earlier
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001', 'description' : 'Take out the garbage again', 'priority' : 250}
table_service.insert_or_replace_entity('tasktable', task)

# Insert a new entity
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '003', 'description' : 'Buy detergent', 'priority' : 300}
table_service.insert_or_replace_entity('tasktable', task)
```

> [!TIP]
> [update_entity][py_update_entity] メソッドは、既存のエンティティのすべてのプロパティと値を置き換えます。既存のエンティティからプロパティを削除するのにも使用できます。 [merge_entity][py_merge_entity] メソッドを使用すると、エンティティを完全に置き換えることなく、既存のエンティティを新しい、または変更されたプロパティ値で置き換えることができます。

## <a name="modify-multiple-entities"></a>複数のエンティティを変更する

Table service による要求のアトミック処理を保証するため、複数の操作をまとめてバッチで送信できます。 まず、[TableBatch][py_TableBatch] クラスを使用して、1 つのバッチに複数の操作を追加します。 次に、[TableService][py_TableService].[commit_batch][py_commit_batch] を呼び出して、操作をアトミック操作で送信します。 バッチで変更されるすべてのエンティティが同じパーティション内にある必要があります。

この例では、2 つのエンティティをバッチでまとめて追加します。

```python
from azure.cosmosdb.table.tablebatch import TableBatch
batch = TableBatch()
task004 = {'PartitionKey': 'tasksSeattle', 'RowKey': '004', 'description' : 'Go grocery shopping', 'priority' : 400}
task005 = {'PartitionKey': 'tasksSeattle', 'RowKey': '005', 'description' : 'Clean the bathroom', 'priority' : 100}
batch.insert_entity(task004)
batch.insert_entity(task005)
table_service.commit_batch('tasktable', batch)
```

バッチは、コンテキスト マネージャーの構文でも使用できます。

```python
task006 = {'PartitionKey': 'tasksSeattle', 'RowKey': '006', 'description' : 'Go grocery shopping', 'priority' : 400}
task007 = {'PartitionKey': 'tasksSeattle', 'RowKey': '007', 'description' : 'Clean the bathroom', 'priority' : 100}

with table_service.batch('tasktable') as batch:
    batch.insert_entity(task006)
    batch.insert_entity(task007)
```

## <a name="query-for-an-entity"></a>エンティティを照会する

テーブル内のエンティティに対してクエリを実行するには、その PartitionKey と RowKey を [TableService][py_TableService].[get_entity][py_get_entity] メソッドに渡します。

```python
task = table_service.get_entity('tasktable', 'tasksSeattle', '001')
print(task.description)
print(task.priority)
```

## <a name="query-a-set-of-entities"></a>エンティティのセットを照会する

**filter** パラメーターにフィルター文字列を指定することで、一連のエンティティに対してクエリを実行できます。 この例では、PartitionKey にフィルターを適用して、Seattle 内のすべてのタスクを検索します。

```python
tasks = table_service.query_entities('tasktable', filter="PartitionKey eq 'tasksSeattle'")
for task in tasks:
    print(task.description)
    print(task.priority)
```

## <a name="query-a-subset-of-entity-properties"></a>エンティティ プロパティのサブセットを照会する

クエリの各エンティティに返すプロパティを制限することもできます。 *プロジェクション*と呼ばれるこの方法では、帯域幅の使用が削減され、クエリのパフォーマンスが向上します。特に、大量のエンティティや結果セットがある場合に役立ちます。 **select** パラメーターを使用して、クライアントに返すプロパティの名前を渡します。

次のコードのクエリは、テーブル内のエンティティの説明だけを返します。

> [!NOTE]
> 次のスニペットは、Azure Storage に対してのみ機能します。 ストレージ エミュレーターではサポートされません。

```python
tasks = table_service.query_entities('tasktable', filter="PartitionKey eq 'tasksSeattle'", select='description')
for task in tasks:
    print(task.description)
```

## <a name="delete-an-entity"></a>エンティティを削除する

エンティティを削除するには、エンティティの **PartitionKey** と **RowKey** を [delete_entity][py_delete_entity] メソッドに渡します。

```python
table_service.delete_entity('tasktable', 'tasksSeattle', '001')
```

## <a name="delete-a-table"></a>テーブルを削除する

不要になったテーブルまたはテーブル内のエンティティがある場合、[delete_table][py_delete_table] メソッドを呼び出して、Azure Storage からテーブルを完全に削除します。

```python
table_service.delete_table('tasktable')
```

## <a name="next-steps"></a>次の手順

* [Azure Cosmos DB の FAQ - Table API を使った開発](https://docs.microsoft.com/azure/cosmos-db/faq)
* [Azure Cosmos DB SDK for Python API のリファレンス](https://docs.microsoft.com/python/api/overview/azure/cosmosdb?view=azure-python)
* [Python デベロッパー センター](https://azure.microsoft.com/develop/python/)
* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md): Windows、macOS、および Linux で Azure Storage のデータを視覚的に操作するための無料のクロス プラットフォーム アプリケーション。
* [Visual Studio での Python の使用 (Windows)](https://docs.microsoft.com/visualstudio/python/overview-of-python-tools-for-visual-studio)


[py_commit_batch]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_create_table]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_delete_entity]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_get_entity]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_insert_entity]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_insert_or_replace_entity]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_Entity]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.models.entity?view=azure-python
[py_merge_entity]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_update_entity]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_delete_table]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_TableService]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_TableBatch]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
