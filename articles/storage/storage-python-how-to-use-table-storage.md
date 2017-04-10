---
title: "Python から Table Storage を使用する方法 | Microsoft Docs"
description: "NoSQL データ ストアである Azure Table Storage を使用して構造化データをクラウドに格納します。"
services: storage
documentationcenter: python
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: 7ddb9f3e-4e6d-4103-96e6-f0351d69a17b
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 12/08/2016
ms.author: marsma
translationtype: Human Translation
ms.sourcegitcommit: 931503f56b32ce9d1b11283dff7224d7e2f015ae
ms.openlocfilehash: 98b02e8faa21e6d0e04d2f2c70bee6b8b018c010
ms.lasthandoff: 12/09/2016


---
# <a name="how-to-use-table-storage-from-python"></a>Python から Table ストレージを使用する方法
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]

[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>概要
このガイドでは、Azure Table ストレージ サービスを使用して一般的なシナリオを実行する方法について説明します。 サンプルは Python で作成され、 [Microsoft Azure Storage SDK for Python]を使用しています。 紹介するシナリオは、テーブルのエンティティの挿入とクエリ実行のほか、テーブルの作成と削除などがあります。

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-table"></a>テーブルを作成する
**TableService** オブジェクトを使用して、テーブル サービスを操作できます。 次のコードでは、 **TableService** オブジェクトを作成します。 プログラムを使用して Azure Storage にアクセスするすべての Python ファイルの先頭付近に、コードを追加します。

```python
from azure.storage.table import TableService, Entity
```

次のコードでは、ストレージ アカウント名とアカウント キーを使用して **TableService** オブジェクトを作成します。  'myaccount' と 'mykey' は、実際のアカウント名とキーに置き換えてください。

```python
table_service = TableService(account_name='myaccount', account_key='mykey')

table_service.create_table('tasktable')
```

## <a name="add-an-entity-to-a-table"></a>エンティティをテーブルに追加する
エンティティを追加するには、最初に、エンティティのプロパティ名と値を定義するディクショナリまたはエンティティを作成します。 すべてのエンティティについて、**PartitionKey** と **RowKey** を指定する必要があることにご注意ください。 これらは、エンティティの一意の識別子になります。 これらの値を使用してクエリすると、他のプロパティをクエリするよりもはるかに高速に実行されます。 システムでは **PartitionKey** が使用されて多くのストレージ ノードにテーブル エンティティが自動的に配布されます。
**PartitionKey** が同じエンティティは同じノードに格納されます。 **RowKey** は、エンティティが属するパーティション内のエンティティの一意の ID です。

エンティティをテーブルに追加するには、ディクショナリ オブジェクトを **insert\_entity** メソッドに渡します。

```python
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '1', 'description' : 'Take out the trash', 'priority' : 200}
table_service.insert_entity('tasktable', task)
```

**Entity** クラスのインスタンスを **insert\_entity** メソッドに渡すこともできます。

```python
task = Entity()
task.PartitionKey = 'tasksSeattle'
task.RowKey = '2'
task.description = 'Wash the car'
task.priority = 100
table_service.insert_entity('tasktable', task)
```

## <a name="update-an-entity"></a>エンティティを更新する
次のコードは、既存のエンティティの以前のバージョンを更新されたバージョンに置き換える方法を示しています。

```python
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '1', 'description' : 'Take out the garbage', 'priority' : 250}
table_service.update_entity('tasktable', 'tasksSeattle', '1', task, content_type='application/atom+xml')
```

更新されるエンティティが存在しない場合、更新操作は失敗します。 存在しているかどうかに関係なくエンティティを格納するには、**insert\_or\_replace_entity** を使用します。
次の例では、最初の呼び出しで既存のエンティティを置き換えます。 2 番目の呼び出しでは、指定された **PartitionKey** と **RowKey** を持つエンティティがテーブル内に存在しないため、新しいエンティティが挿入されます。

```python
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '1', 'description' : 'Take out the garbage again', 'priority' : 250}
table_service.insert_or_replace_entity('tasktable', 'tasksSeattle', '1', task, content_type='application/atom+xml')

task = {'PartitionKey': 'tasksSeattle', 'RowKey': '3', 'description' : 'Buy detergent', 'priority' : 300}
table_service.insert_or_replace_entity('tasktable', 'tasksSeattle', '1', task, content_type='application/atom+xml')
```

## <a name="change-a-group-of-entities"></a>エンティティのグループを変更する
状況によって、複数の操作をバッチとして送信し、サーバーによるアトミック処理を行うことが合理的である場合があります。 このような処理を実行するには、 **TableBatch** クラスを使用します。 バッチを送信するときは、**commit\_batch** を呼び出します。 バッチでエンティティを変更するには、すべてのエンティティが同じパーティションに含まれている必要があります。 次の例では、2 つのエンティティをバッチでまとめて追加します。

```python
from azure.storage.table import TableBatch
batch = TableBatch()
task10 = {'PartitionKey': 'tasksSeattle', 'RowKey': '10', 'description' : 'Go grocery shopping', 'priority' : 400}
task11 = {'PartitionKey': 'tasksSeattle', 'RowKey': '11', 'description' : 'Clean the bathroom', 'priority' : 100}
batch.insert_entity(task10)
batch.insert_entity(task11)
table_service.commit_batch('tasktable', batch)
```

バッチは、コンテキスト マネージャーの構文でも使用できます。

```python
task12 = {'PartitionKey': 'tasksSeattle', 'RowKey': '12', 'description' : 'Go grocery shopping', 'priority' : 400}
task13 = {'PartitionKey': 'tasksSeattle', 'RowKey': '13', 'description' : 'Clean the bathroom', 'priority' : 100}

with table_service.batch('tasktable') as batch:
    batch.insert_entity(task12)
    batch.insert_entity(task13)
```

## <a name="query-for-an-entity"></a>エンティティを照会する
テーブル内のエンティティを照会するには、**get\_entity** メソッドを使用して、**PartitionKey** と **RowKey** を渡します。

```python
task = table_service.get_entity('tasktable', 'tasksSeattle', '1')
print(task.description)
print(task.priority)
```

## <a name="query-a-set-of-entities"></a>エンティティのセットを照会する
次の例では、 **PartitionKey**に基づいて、Seattle 内のすべてのタスクを検索します。

```python
tasks = table_service.query_entities('tasktable', filter="PartitionKey eq 'tasksSeattle'")
for task in tasks:
    print(task.description)
    print(task.priority)
```

## <a name="query-a-subset-of-entity-properties"></a>エンティティ プロパティのサブセットを照会する
テーブルに対するクエリでは、ごくわずかのプロパティだけをエンティティから取得できます。
*プロジェクション*と呼ばれるこの方法では、帯域幅の使用が削減され、クエリのパフォーマンスが向上します。 **select** パラメーターを使用して、クライアントに提供するプロパティの名前を渡します。

次のコードのクエリは、テーブル内のエンティティの説明だけを返します。

> [!NOTE]
> 次のスニペットは、Azure Storage に対してのみ機能します。 ストレージ エミュレーターではサポートされません。
>
>

```python
tasks = table_service.query_entities('tasktable', filter="PartitionKey eq 'tasksSeattle'", select='description')
for task in tasks:
    print(task.description)
```

## <a name="delete-an-entity"></a>エンティティを削除する
パーティション キーと行キーを使用してエンティティを削除できます。

```python
table_service.delete_entity('tasktable', 'tasksSeattle', '1')
```

## <a name="delete-a-table"></a>テーブルを削除する
次のコードは、ストレージ アカウントからテーブルを削除します。

```python
table_service.delete_table('tasktable')
```

## <a name="next-steps"></a>次のステップ
これで、Table Storage の基本を学習できました。さらに詳細な情報が必要な場合は、次のリンク先を参照してください。

* [Python デベロッパー センター](/develop/python/)
* [Azure Storage Services REST API (Azure Storage サービスの REST API)](http://msdn.microsoft.com/library/azure/dd179355)
* [Azure Storage チーム ブログ]
* [Microsoft Azure Storage SDK for Python]

[Azure Storage チーム ブログ]: http://blogs.msdn.com/b/windowsazurestorage/
[Microsoft Azure Storage SDK for Python]: https://github.com/Azure/azure-storage-python

