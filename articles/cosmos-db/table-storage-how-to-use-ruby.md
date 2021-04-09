---
title: Ruby から Azure Cosmos DB Table API および Azure Table Storage を使用する
description: Azure Table Storage または Azure Cosmos DB Table API を使用して、構造化データをクラウドに格納します。
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: ruby
ms.topic: sample
ms.date: 07/23/2020
author: sakash279
ms.author: akshanka
ms.reviewer: sngun
ms.openlocfilehash: 2d0c8433fff58854cb77a4e806058eae1937e71b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93101121"
---
# <a name="how-to-use-azure-table-storage-and-the-azure-cosmos-db-table-api-with-ruby"></a>Ruby から Azure Table Storage と Azure Cosmos DB Table API を使用する方法
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

この記事では、テーブルの作成、データの格納、データに対する CRUD 操作の実行を行う方法について説明します。 Azure Table service または Azure Cosmos DB Table API のいずれかを選択してください。 この記事で説明するサンプルは Ruby で記述され、[Ruby 用 Azure Storage Table クライアント ライブラリ](https://github.com/azure/azure-storage-ruby/tree/master/table)を使用しています。 紹介するシナリオは、テーブルの作成、テーブルの削除、エンティティの挿入、テーブルからのエンティティのクエリの実行などです。

## <a name="create-an-azure-service-account"></a>Azure サービス アカウントを作成する

[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

**Azure Storage アカウントを作成する**

[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

**Azure Cosmos DB アカウントの作成**

[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="add-access-to-azure-storage-or-azure-cosmos-db"></a>Azure Storage または Azure Cosmos DB へのアクセスを追加する

Azure Storage または Azure Cosmos DB を使用するには、Ruby Azure パッケージをダウンロードして使用する必要があります。このパッケージには、Table REST サービスと通信するための便利なライブラリのセットが含まれています。

### <a name="use-rubygems-to-obtain-the-package"></a>RubyGems を使用してパッケージを取得する

1. **PowerShell** (Windows)、**ターミナル** (Mac)、**Bash** (Unix) などのコマンド ライン インターフェイスを使用します。
2. コマンド ウィンドウに「**gem install azure-storage-table**」と入力して、gem と依存関係をインストールします。

### <a name="import-the-package"></a>パッケージをインポートする

任意のテキスト エディターを使用して、Storage を使用する Ruby ファイルの先頭に次のコードを追加します。

```ruby
require "azure/storage/table"
```

## <a name="add-your-connection-string"></a>接続文字列を追加する

Azure ストレージ アカウントまたは Azure Cosmos DB Table API アカウントのいずれかに接続できます。 使用しているアカウントの種類に基づいて接続文字列を取得します。

### <a name="add-an-azure-storage-connection"></a>Azure Storage 接続を追加する

Azure Storage モジュールは、Azure ストレージ アカウントに接続するために必要な情報として、環境変数 **AZURE_STORAGE_ACCOUNT** と **AZURE_STORAGE_ACCESS_KEY** を読み取ります。 これらの環境変数が設定されていない場合は、**Azure::Storage::Table::TableService** を使用する前に、次のコードを使用してアカウント情報を指定する必要があります。

```ruby
Azure.config.storage_account_name = "<your Azure Storage account>"
Azure.config.storage_access_key = "<your Azure Storage access key>"
```

Azure ポータルでクラシックまたは Resource Manager ストレージ アカウントからこれらの値を取得するには:

1. [Azure Portal](https://portal.azure.com) にログインします。
2. 使用するストレージ アカウントを表示します。
3. 右側の [設定] ブレードで、 **[アクセス キー]** をクリックします。
4. 表示される [アクセス キー] ブレードに、アクセス キー 1 とアクセス キー 2 が表示されます。 このいずれかを使用できます。
5. コピー アイコンをクリックしてキーをクリップボードにコピーします。

### <a name="add-an-azure-cosmos-db-connection"></a>Azure Cosmos DB の接続を追加する

Azure Cosmos DB に接続するには、Azure Portal からプライマリ接続文字列をコピーし、コピーした接続文字列を使って **Client** オブジェクトを作成します。 **TableService** オブジェクトの作成時に **Client** オブジェクトを渡すことができます。

```ruby
common_client = Azure::Storage::Common::Client.create(storage_account_name:'myaccount', storage_access_key:'mykey', storage_table_host:'mycosmosdb_endpoint')
table_client = Azure::Storage::Table::TableService.new(client: common_client)
```

## <a name="create-a-table"></a>テーブルを作成する

**Azure::Storage::Table::TableService** オブジェクトを使用して、テーブルとエンティティを操作できます。 テーブルを作成するには、**create_table()** メソッドを使用します。 次の例では、テーブルを作成し、既に存在している場合はエラーを出力します。

```ruby
azure_table_service = Azure::Storage::Table::TableService.new
begin
    azure_table_service.create_table("testtable")
rescue
    puts $!
end
```

## <a name="add-an-entity-to-a-table"></a>エンティティをテーブルに追加する

エンティティを追加するには、エンティティのプロパティを定義するハッシュ オブジェクトを最初に作成します。 すべてのエンティティについて、**PartitionKey** と **RowKey** を指定する必要があることに注意してください。 これらはエンティティの一意の識別子であり、他のエンティティのプロパティよりはるかに高速に照会できる値です。 Azure Storage では、テーブルのエンティティを多数のストレージ ノードに自動的に配布するために **PartitionKey** を使用します。 **PartitionKey** が同じエンティティは同じノードに格納されます。 **RowKey** は、エンティティが属するパーティション内のエンティティの一意の ID です。

```ruby
entity = { "content" => "test entity",
    :PartitionKey => "test-partition-key", :RowKey => "1" }
azure_table_service.insert_entity("testtable", entity)
```

## <a name="update-an-entity"></a>エンティティを更新する

既存のエンティティを更新するには、複数のメソッドがあります。

* **update_entity():** 既存のエンティティを置換することで更新します。
* **merge_entity():** 新しいプロパティ値を既存のエンティティにマージすることで既存のエンティティを更新します。
* **insert_or_merge_entity():** 既存のエンティティを置換することで更新します。 エンティティが存在しない場合は、新しいエンティティが挿入されます。
* **insert_or_replace_entity():** 新しいプロパティ値を既存のエンティティにマージすることで既存のエンティティを更新します。 エンティティが存在しない場合は、新しいエンティティが挿入されます。

次の例は、**update_entity()** を使用してエンティティを更新する方法を示しています。

```ruby
entity = { "content" => "test entity with updated content",
    :PartitionKey => "test-partition-key", :RowKey => "1" }
azure_table_service.update_entity("testtable", entity)
```

**update_entity()** と **merge_entity()** では、更新されるエンティティが存在しない場合、更新操作は失敗します。 したがって、既に存在しているかどうかに関係なくエンティティを格納するには、**insert_or_replace_entity()** または **insert_or_merge_entity()** を使用する必要があります。

## <a name="work-with-groups-of-entities"></a>エンティティのグループを操作する

状況によって、複数の操作をバッチとして送信し、サーバーによるアトミック処理を行うことが合理的である場合があります。 このためには、まず **Batch** オブジェクトを作成し、次に **TableService** の **execute_batch()** メソッドを使用します。 次の例では、RowKey が 2 および 3 である 2 つのエンティティをバッチで送信する方法を示します。 これは、同じ PartitionKey を持つエンティティでのみ機能することに注意してください。

```ruby
azure_table_service = Azure::TableService.new
batch = Azure::Storage::Table::Batch.new("testtable",
    "test-partition-key") do
    insert "2", { "content" => "new content 2" }
    insert "3", { "content" => "new content 3" }
end
results = azure_table_service.execute_batch(batch)
```

## <a name="query-for-an-entity"></a>エンティティを照会する

テーブル内のエンティティを照会するには、**get_entity()** メソッドを使用して、テーブル名、**PartitionKey**、および **RowKey** を渡します。

```ruby
result = azure_table_service.get_entity("testtable", "test-partition-key",
    "1")
```

## <a name="query-a-set-of-entities"></a>エンティティのセットを照会する

テーブル内のエンティティのセットを照会するには、クエリ ハッシュ オブジェクトを作成し、**query_entities()** メソッドを使用します。 次の例では、同じ **PartitionKey** を持つエンティティをすべて取得します。

```ruby
query = { :filter => "PartitionKey eq 'test-partition-key'" }
result, token = azure_table_service.query_entities("testtable", query)
```

> [!NOTE]
> 結果セットが大きすぎて単一のクエリで返すことができない場合は、継続トークンが返されます。この継続トークンを使用して、後続のページを取得できます。


## <a name="query-a-subset-of-entity-properties"></a>エンティティ プロパティのサブセットを照会する

テーブルに対するクエリでは、ごくわずかのプロパティだけをエンティティから取得できます。 プロジェクションと呼ばれるこの方法では、帯域幅の使用が削減され、クエリのパフォーマンスが向上します。特に、大量のエンティティがある場合に役立ちます。 select 句を使用して、クライアントに渡すプロパティの名前を指定します。

```ruby
query = { :filter => "PartitionKey eq 'test-partition-key'",
    :select => ["content"] }
result, token = azure_table_service.query_entities("testtable", query)
```

## <a name="delete-an-entity"></a>エンティティを削除する

エンティティを削除するには、**delete_entity()** メソッドを使用します。 目的のエンティティを含んでいるテーブルの名前、PartitionKey、および エンティティの RowKey を渡します。

```ruby
azure_table_service.delete_entity("testtable", "test-partition-key", "1")
```

## <a name="delete-a-table"></a>テーブルを削除する

テーブルを削除するには、**delete_table()** を使用して、削除するテーブルの名前を渡します。

```ruby
azure_table_service.delete_table("testtable")
```

## <a name="next-steps"></a>次のステップ

* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md)は、Windows、macOS、Linux で Azure Storage のデータを視覚的に操作できる Microsoft 製の無料のスタンドアロン アプリです。
* [Ruby デベロッパー センター](https://azure.microsoft.com/develop/ruby/)
* [Microsoft Azure Storage Table Client Library for Ruby](https://github.com/azure/azure-storage-ruby/tree/master/table)