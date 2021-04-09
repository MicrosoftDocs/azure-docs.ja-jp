---
title: C++ で Azure Table Storage と Azure Cosmos DB Table API を使用する
description: C++ で Azure Table Storage または Azure Cosmos DB Table API を使用して、構造化データをクラウドに格納します。
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: cpp
ms.topic: sample
ms.date: 10/07/2019
author: sakash279
ms.author: akshanka
ms.openlocfilehash: 79b58b76954cf15289e85dbf763b7a399897635d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94489881"
---
# <a name="how-to-use-azure-table-storage-and-azure-cosmos-db-table-api-with-c"></a>C++ で Azure Table Storage と Azure Cosmos DB Table API を使用する方法
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

このガイドでは、Azure Table Storage サービスまたは Azure Cosmos DB Table API を使用する、一般的なシナリオを説明します。 サンプルは C++ で記述され、 [C++ 用 Azure ストレージ クライアント ライブラリ](https://github.com/Azure/azure-storage-cpp/blob/master/README.md)を利用しています。 この記事では、次のシナリオを取り上げます。

* テーブルの作成と削除
* テーブルのエンティティの操作

> [!NOTE]
> このガイドは、C++ 用 Azure ストレージ クライアント ライブラリ 1.0.0 以上のバージョンを対象としています。 推奨されるバージョンはストレージ クライアント ライブラリ 2.2.0 です。これは、[NuGet](https://www.nuget.org/packages/wastorage) または [GitHub](https://github.com/Azure/azure-storage-cpp/) を使用して入手できます。
>

## <a name="create-accounts"></a>アカウントの作成

### <a name="create-an-azure-service-account"></a>Azure サービス アカウントを作成する

[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>Azure のストレージ アカウントの作成

[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

### <a name="create-an-azure-cosmos-db-table-api-account"></a>Azure Cosmos DB Table API アカウントを作成する

[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="create-a-c-application"></a>C++ アプリケーションの作成

このガイドでは、 C++ アプリケーションのストレージ機能を使用します。 そのため、C++ 用 Azure Storage クライアント ライブラリをインストールします。

C++ 用 Azure ストレージ クライアント ライブラリをインストールする場合、次の方法を使用します。

* **Linux:** [C++ 用 Azure Storage クライアント ライブラリの README:Linux での開始](https://github.com/Azure/azure-storage-cpp#getting-started-on-linux)に関するページで示されている手順のようにします。
* **Windows:** Windows では、依存関係マネージャーとして [vcpkg](https://github.com/microsoft/vcpkg) を使用します。 [クイックスタート](https://github.com/microsoft/vcpkg#quick-start)に従って vcpkg を初期化してください。 そのうえで、次のコマンドを使用してライブラリをインストールします。

```powershell
.\vcpkg.exe install azure-storage-cpp
```

ソース コードをビルドして Nuget にエクスポートする方法については、[README](https://github.com/Azure/azure-storage-cpp#download--install) ファイルを参照してください。

### <a name="configure-access-to-the-table-client-library"></a>Table のクライアント ライブラリへのアクセスの構成

Azure Storage API を使用してテーブルにアクセスするには、次の `include` ステートメントを C++ ファイルの先頭に追加します。

```cpp
#include <was/storage_account.h>
#include <was/table.h>
```

Azure Storage クライアントまたは Cosmos DB クライアントでは、接続文字列を使用して、データ管理サービスにアクセスするためのエンドポイントや資格情報を保存します。 クライアント アプリケーションを実行する場合、ストレージ接続文字列または Azure Cosmos DB 接続文字列を、適切な形式で入力する必要があります。

### <a name="set-up-an-azure-storage-connection-string"></a>Azure Storage 接続文字列の設定

この例では、Azure Storage 接続文字列を保持する静的フィールドを宣言する方法が示されています。  

```cpp
// Define the Storage connection string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=<your_storage_account>;AccountKey=<your_storage_account_key>"));
```

`<your_storage_account>` にはストレージ アカウントの名前を使用します。 <your_storage_account_key> については、[Azure portal](https://portal.azure.com) に記載されているストレージアカウントのアクセス キーを使用します。 ストレージ アカウントとアクセス キーについて詳しくは、「[ストレージ アカウントを作成する](../storage/common/storage-account-create.md)」を参照してください。

### <a name="set-up-an-azure-cosmos-db-connection-string"></a>Azure Cosmos DB 接続文字列の設定

この例では、Azure Cosmos DB 接続文字列を保持する静的フィールドを宣言する方法が示されています。

```cpp
// Define the Azure Cosmos DB connection string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=<your_cosmos_db_account>;AccountKey=<your_cosmos_db_account_key>;TableEndpoint=<your_cosmos_db_endpoint>"));
```

`<your_cosmos_db_account>` には、Azure Cosmos DB アカウントの名前を使用します。 `<your_cosmos_db_account_key>` のプライマリ キーを入力してください。 `<your_cosmos_db_endpoint>` には、[Azure portal](https://portal.azure.com) に一覧表示されているエンドポイントを入力します。

ローカルの Windows ベースのコンピューターでアプリケーションをテストするには、[Azure SDK](https://azure.microsoft.com/downloads/) と共にインストールされた、Azure Storage Emulator を使用できます。 ストレージ エミュレーターは、ローカルの開発マシンで、Azure BLOB、Queue、および Table service をシミュレートするユーティリティです。 次の例では、ローカルのストレージ エミュレーターに接続文字列を保持する静的フィールドを宣言する方法を示しています。  

```cpp
// Define the connection string with Azure Storage Emulator.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

Windows デスクトップから Azure Storage Emulator を起動するには、 **[スタート]** ボタンをクリックするか、Windows キーを押します。 入力し、*Microsoft Azure Storage Emulator* を実行します。 詳細については、「[開発とテストのための Azure Storage Emulator 使用する](../storage/common/storage-use-emulator.md)」を参照してください。

### <a name="retrieve-your-connection-string"></a>接続文字列の取得

`cloud_storage_account` クラスを使用してストレージ アカウント情報を表すことができます。 ストレージ接続文字列からストレージ アカウント情報を取得するには、 `parse` メソッド使用します。

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

次に、`cloud_table_client` クラスへの参照を取得します。 このクラスにより、Table ストレージ サービス内に格納されているテーブルとエンティティの参照オブジェクトを取得できます。 次のコードは、前に取得したストレージ アカウント オブジェクトを使用して、`cloud_table_client` オブジェクトを作成します。  

```cpp
// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();
```

## <a name="create-and-add-entities-to-a-table"></a>エンティティを作成しテーブルに追加する

### <a name="create-a-table"></a>テーブルを作成する

`cloud_table_client` オブジェクトを使うと、テーブルとエンティティの参照オブジェクトを取得できます。 次のコードは、 `cloud_table_client` オブジェクトを作成し、これを使用して新しいテーブルを作成します。

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);  

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Retrieve a reference to a table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Create the table if it doesn't exist.
table.create_if_not_exists();  
```

### <a name="add-an-entity-to-a-table"></a>エンティティをテーブルに追加する

エンティティをテーブルに追加するには、新しい `table_entity` オブジェクトを作成し、`table_operation::insert_entity` に渡します。 次のコードは、ユーザーの名を行キーとして、姓をパーティション キーとしてそれぞれ使用します。 エンティティのパーティション キーと行キーの組み合わせで、テーブル内のエンティティを一意に識別します。 同じパーティション キーを持つエンティティは、異なるパーティション キーを持つエンティティよりも迅速に照会できます。 多様なパーティション キーを使用すると、並列操作のスケーラビリティを高めることができます。 詳しくは、「[Microsoft Azure Storage のパフォーマンスとスケーラビリティに対するチェック リスト](../storage/blobs/storage-performance-checklist.md)」をご覧ください。

次のコードでは、ユーザー データの格納用に `table_entity` の新しいインスタンスを作成しています。 次に、テーブルへのエンティティの挿入用に `table_operation` を作成するために `table_operation::insert_entity` を呼び出し、それに新しいテーブル エンティティを関連付けています。 最後に、`cloud_table` オブジェクトに対して `execute` メソッドを呼び出します。 新しい `table_operation` により、新しいユーザー エンティティを `people` テーブルに挿入する要求が Table service に送信されます。  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Retrieve a reference to a table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Create the table if it doesn't exist.
table.create_if_not_exists();

// Create a new customer entity.
azure::storage::table_entity customer1(U("Harp"), U("Walter"));

azure::storage::table_entity::properties_type& properties = customer1.properties();
properties.reserve(2);
properties[U("Email")] = azure::storage::entity_property(U("Walter@contoso.com"));

properties[U("Phone")] = azure::storage::entity_property(U("425-555-0101"));

// Create the table operation that inserts the customer entity.
azure::storage::table_operation insert_operation = azure::storage::table_operation::insert_entity(customer1);

// Execute the insert operation.
azure::storage::table_result insert_result = table.execute(insert_operation);
```

### <a name="insert-a-batch-of-entities"></a>エンティティのバッチを挿入する

1 回の書き込み操作でエンティティのバッチを Table service に挿入できます。 次のコードでは、`table_batch_operation` オブジェクトを作成し、3 つの挿入操作を追加しています。 追加する各挿入操作では、新しいエンティティ オブジェクトを作成してその値を設定してから、`table_batch_operation` オブジェクトの `insert` メソッドを呼び出して、エンティティを新しい挿入操作に関連付けています。 次に、`cloud_table.execute` を呼び出して操作を実行します。  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Define a batch operation.
azure::storage::table_batch_operation batch_operation;

// Create a customer entity and add it to the table.
azure::storage::table_entity customer1(U("Smith"), U("Jeff"));

azure::storage::table_entity::properties_type& properties1 = customer1.properties();
properties1.reserve(2);
properties1[U("Email")] = azure::storage::entity_property(U("Jeff@contoso.com"));
properties1[U("Phone")] = azure::storage::entity_property(U("425-555-0104"));

// Create another customer entity and add it to the table.
azure::storage::table_entity customer2(U("Smith"), U("Ben"));

azure::storage::table_entity::properties_type& properties2 = customer2.properties();
properties2.reserve(2);
properties2[U("Email")] = azure::storage::entity_property(U("Ben@contoso.com"));
properties2[U("Phone")] = azure::storage::entity_property(U("425-555-0102"));

// Create a third customer entity to add to the table.
azure::storage::table_entity customer3(U("Smith"), U("Denise"));

azure::storage::table_entity::properties_type& properties3 = customer3.properties();
properties3.reserve(2);
properties3[U("Email")] = azure::storage::entity_property(U("Denise@contoso.com"));
properties3[U("Phone")] = azure::storage::entity_property(U("425-555-0103"));

// Add customer entities to the batch insert operation.
batch_operation.insert_or_replace_entity(customer1);
batch_operation.insert_or_replace_entity(customer2);
batch_operation.insert_or_replace_entity(customer3);

// Execute the batch operation.
std::vector<azure::storage::table_result> results = table.execute_batch(batch_operation);
```

バッチ操作に関しては、次の事項にも留意してください。

* 1 つのバッチで、最大 100 の `insert`、`delete`、`merge`、`replace`、`insert-or-merge`、および `insert-or-replace` の操作を任意の組み合わせで実行できます。  
* 取得をバッチ操作として実行することもできますが、バッチ内の唯一の操作であることが必要です。  
* 1 つのバッチ操作に含まれるすべてのエンティティのパーティション キーが同じである必要があります。  
* バッチ操作のデータ ペイロードは 4 MB に制限されています。  

## <a name="query-and-modify-entities"></a>エンティティのクエリと変更

### <a name="retrieve-all-entities-in-a-partition"></a>パーティション内のすべてのエンティティを取得する

テーブルに対してパーティション内のすべてのエンティティを照会する場合は、`table_query` オブジェクトを使用します。 次のコード例は、`Smith` がパーティション キーであるエンティティに対してフィルターを指定します。 この例は、クエリ結果の各エンティティのフィールドをコンソールに出力します。  

> [!NOTE]
> 現在、これらの方法は Azure Cosmos DB の C++ 用にはサポートされていません。

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Construct the query operation for all customer entities where PartitionKey="Smith".
azure::storage::table_query query;

query.set_filter_string(azure::storage::table_query::generate_filter_condition(U("PartitionKey"), azure::storage::query_comparison_operator::equal, U("Smith")));

// Execute the query.
azure::storage::table_query_iterator it = table.execute_query(query);

// Print the fields for each customer.
azure::storage::table_query_iterator end_of_results;
for (; it != end_of_results; ++it)
{
    const azure::storage::table_entity::properties_type& properties = it->properties();

    std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key()
        << U(", Property1: ") << properties.at(U("Email")).string_value()
        << U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
}  
```

この例のクエリは、フィルター条件に一致するすべてのエンティティを返します。 テーブルの規模が大きく、そのテーブル エンティティを頻繁にダウンロードする必要がある場合は、Azure Storage BLOB にデータを保存することをお勧めします。

### <a name="retrieve-a-range-of-entities-in-a-partition"></a>パーティション内の一定範囲のエンティティを取得する

パーティション内の一部のエンティティのみ照会する場合は、範囲を指定できます。 パーティション キー フィルターと行キー フィルターを組み合わせます。 次のコード例は、2 つのフィルターを使用して、行キー (名) がアルファベットの `E` より前の文字で始まる、`Smith` というパーティション内のすべてのエンティティを取得し、クエリ結果を出力します。  

> [!NOTE]
> 現在、これらの方法は Azure Cosmos DB の C++ 用にはサポートされていません。

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Create the table query.
azure::storage::table_query query;

query.set_filter_string(azure::storage::table_query::combine_filter_conditions(
    azure::storage::table_query::generate_filter_condition(U("PartitionKey"),
    azure::storage::query_comparison_operator::equal, U("Smith")),
    azure::storage::query_logical_operator::op_and,
    azure::storage::table_query::generate_filter_condition(U("RowKey"), azure::storage::query_comparison_operator::less_than, U("E"))));

// Execute the query.
azure::storage::table_query_iterator it = table.execute_query(query);

// Loop through the results, displaying information about the entity.
azure::storage::table_query_iterator end_of_results;
for (; it != end_of_results; ++it)
{
    const azure::storage::table_entity::properties_type& properties = it->properties();

    std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key()
        << U(", Property1: ") << properties.at(U("Email")).string_value()
        << U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
}  
```

### <a name="retrieve-a-single-entity"></a>単一のエンティティを取得する

単一の特定のエンティティを取得するクエリを記述することができます。 次のコードは、`table_operation::retrieve_entity` を使用して、`Jeff Smith` というユーザーを指定します。 このメソッドで返されるのは、エンティティのコレクションではなく、単一のエンティティのみであり、`table_result` の戻り値です。 クエリでパーティション キーと行キーの両方を指定することが、Table service から単一のエンティティを取得するための最速の方法です。  

```cpp
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Retrieve the entity with partition key of "Smith" and row key of "Jeff".
azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

// Output the entity.
azure::storage::table_entity entity = retrieve_result.entity();
const azure::storage::table_entity::properties_type& properties = entity.properties();

std::wcout << U("PartitionKey: ") << entity.partition_key() << U(", RowKey: ") << entity.row_key()
    << U(", Property1: ") << properties.at(U("Email")).string_value()
    << U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
```

### <a name="replace-an-entity"></a>エンティティを置換する

エンティティを置換するには、そのエンティティを Table service から取得し、エンティティ オブジェクトを変更して、変更を Table service に戻して保存します。 次のコードは、既存のユーザーの電話番号と電子メール アドレスを変更します。 このコードでは、`table_operation::insert_entity` を呼び出す代わりに `table_operation::replace_entity` を使用します。 この方法により、エンティティを取得した後にサーバーでエンティティが変更された場合を除き、サーバーでエンティティが完全に置換されます。 変更されている場合、操作は失敗します。 このエラーは、取得と更新の間に別のコンポーネントによって行われた変更が上書きされるのを防ぎます。 このエラーを正しく処理するには、もう一度エンティティを取得し、変更を加えて (その変更がまだ有効な場合)、再び `table_operation::replace_entity` 操作を実行します。  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Replace an entity.
azure::storage::table_entity entity_to_replace(U("Smith"), U("Jeff"));
azure::storage::table_entity::properties_type& properties_to_replace = entity_to_replace.properties();
properties_to_replace.reserve(2);

// Specify a new phone number.
properties_to_replace[U("Phone")] = azure::storage::entity_property(U("425-555-0106"));

// Specify a new email address.
properties_to_replace[U("Email")] = azure::storage::entity_property(U("JeffS@contoso.com"));

// Create an operation to replace the entity.
azure::storage::table_operation replace_operation = azure::storage::table_operation::replace_entity(entity_to_replace);

// Submit the operation to the Table service.
azure::storage::table_result replace_result = table.execute(replace_operation);
```

### <a name="insert-or-replace-an-entity"></a>エンティティの挿入または置換を行う

`table_operation::replace_entity` 操作は、サーバーからエンティティを取得した後にエンティティが変更されていると失敗します。 さらに、`table_operation::replace_entity` が成功するためには、先にエンティティをサーバーから取得する必要があります。 場合によっては、サーバーにエンティティが存在するかどうかがわかりません。 更新によってすべてが上書きされるので、現在格納されている値は無関係です。 この結果を実現するには、`table_operation::insert_or_replace_entity` 操作を使用します。 この操作では、エンティティが存在しない場合は挿入します。 エンティティが存在する場合は、操作によって置き換えられます。 次のコード例では、先ほどと同じように `Jeff Smith` のユーザー エンティティを取得していますが、今度は `table_operation::insert_or_replace_entity` を使用してサーバーに保存しています。 そのため、取得操作と更新操作の間に行われたエンティティの更新は上書きされます。  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Insert or replace an entity.
azure::storage::table_entity entity_to_insert_or_replace(U("Smith"), U("Jeff"));
azure::storage::table_entity::properties_type& properties_to_insert_or_replace = entity_to_insert_or_replace.properties();

properties_to_insert_or_replace.reserve(2);

// Specify a phone number.
properties_to_insert_or_replace[U("Phone")] = azure::storage::entity_property(U("425-555-0107"));

// Specify an email address.
properties_to_insert_or_replace[U("Email")] = azure::storage::entity_property(U("Jeffsm@contoso.com"));

// Create an operation to insert or replace the entity.
azure::storage::table_operation insert_or_replace_operation = azure::storage::table_operation::insert_or_replace_entity(entity_to_insert_or_replace);

// Submit the operation to the Table service.
azure::storage::table_result insert_or_replace_result = table.execute(insert_or_replace_operation);
```

### <a name="query-a-subset-of-entity-properties"></a>エンティティ プロパティのサブセットを照会する

テーブルに対するクエリでは、ごくわずかのプロパティだけをエンティティから取得できます。 次のコードのクエリは、`table_query::set_select_columns` メソッドを使って、テーブル内のエンティティの電子メール アドレスだけを返します。  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Define the query, and select only the Email property.
azure::storage::table_query query;
std::vector<utility::string_t> columns;

columns.push_back(U("Email"));
query.set_select_columns(columns);

// Execute the query.
azure::storage::table_query_iterator it = table.execute_query(query);

// Display the results.
azure::storage::table_query_iterator end_of_results;
for (; it != end_of_results; ++it)
{
    std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key();

    const azure::storage::table_entity::properties_type& properties = it->properties();
    for (auto prop_it = properties.begin(); prop_it != properties.end(); ++prop_it)
    {
        std::wcout << ", " << prop_it->first << ": " << prop_it->second.str();
    }

    std::wcout << std::endl;
}
```

> [!NOTE]
> すべてのプロパティを取得するよりも、エンティティからいくつかのプロパティを照会する方が効率的です。
>

## <a name="delete-content"></a>コンテンツを削除する

### <a name="delete-an-entity"></a>エンティティを削除する

エンティティは、取得した後で削除できます。 エンティティを取得した後、削除するエンティティを含む `table_operation::delete_entity` を呼び出します。 次に `cloud_table.execute` メソッドを呼び出します。 次のコードは、 `Jeff` の行キーと `Smith` のパーティション キーを持つエンティティを取得して削除します。

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Create an operation to retrieve the entity with partition key of "Smith" and row key of "Jeff".
azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

// Create an operation to delete the entity.
azure::storage::table_operation delete_operation = azure::storage::table_operation::delete_entity(retrieve_result.entity());

// Submit the delete operation to the Table service.
azure::storage::table_result delete_result = table.execute(delete_operation);  
```

### <a name="delete-a-table"></a>テーブルを削除する

最後に、次のコード例は、ストレージ アカウントからテーブルを削除します。 削除されたテーブルは、削除後の一定期間は再作成できなくなります。  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Delete the table if it exists
if (table.delete_table_if_exists())
{
    std::cout << "Table deleted!";
}
else
{
    std::cout << "Table didn't exist";
}
```

## <a name="troubleshooting"></a>トラブルシューティング

Visual Studio Community エディションの場合、インクルード ファイル *storage_account.h* と *table.h* が原因でプロジェクトにビルド エラーが発生する場合は、 **/permissive-** コンパイラ スイッチを削除します。

1. **ソリューション エクスプローラー** で、プロジェクトを右クリックして **[プロパティ]** を選択します。
1. **[プロパティ ページ]** ダイアログ ボックスで、 **[構成プロパティ]** を展開し、 **[C/C++]** を展開し、 **[言語]** を選択します。
1. **[準拠モード]** を **[いいえ]** に設定します。

## <a name="next-steps"></a>次のステップ

[Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) は、Windows、macOS、Linux で Azure Storage のデータを視覚的に操作できる Microsoft 製の無料のスタンドアロン アプリです。

Azure Storage および Azure Cosmos DB の Table API について詳しくは、次のリンクを参照してください。

* [Table API の概要](table-introduction.md)
* [C++ での Azure Storage のリソース一覧の取得](../storage/common/storage-c-plus-plus-enumeration.md)
* [C++ 用ストレージ クライアント ライブラリ リファレンス](https://azure.github.io/azure-storage-cpp)
* [Azure Storage のドキュメント](https://azure.microsoft.com/documentation/services/storage/)
